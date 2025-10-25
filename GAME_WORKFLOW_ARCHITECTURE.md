# Phân Tích Code & Kiến Trúc Script Game Multiplayer Unity

## 🎮 Tổng Quan Code
Dự án **Game_Multi_DauTien** sử dụng Photon Fusion framework với kiến trúc script tập trung vào quản lý gameplay multiplayer.

## 🏗️ Kiến Trúc Script Chính

### 1. Lớp Gameplay.cs - Controller Trung Tâm

```csharp
public class Gameplay : ContextBehaviour
{
    [Networked, Capacity(200)]
    public NetworkDictionary<PlayerRef, Player> Players { get; }
    
    private SpawnPoint[] _spawnPoints;
    private int _lastSpawnPoint = -1;
    private List<SpawnRequest> _spawnRequests = new();
}
```

#### Phân Tích Thuộc Tính:
- **NetworkDictionary<PlayerRef, Player> Players**: Lưu trữ danh sách người chơi với capacity 200
- **_spawnPoints**: Mảng các điểm spawn được cache
- **_lastSpawnPoint**: Index của spawn point cuối được sử dụng
- **_spawnRequests**: Queue các yêu cầu spawn được delay

### 2. Kiến Trúc Networking (Photon Fusion)

#### Các Component Cốt Lõi:
- **NetworkRunner**: Quản lý phiên mạng và mô phỏng tick
- **NetworkObject**: Đối tượng có thể đồng bộ qua mạng
- **NetworkBehaviour**: Lớp base cho các component networking
- **NetworkDictionary**: Lưu trữ dữ liệu theo PlayerRef

#### Cấu Trúc Gameplay:
```
Gameplay (NetworkBehaviour)
├── Players (NetworkDictionary<PlayerRef, Player>)
├── Quản lý SpawnPoints
├── Logic Join/Leave Player
└── Sinh/Hủy Agent
```

## 🔄 Quy Trình Xử Lý Code

### 1. Phương Thức Join Player

```csharp
public void Join(Player player)
{
    if (HasStateAuthority == false) return;
    
    var playerRef = player.Object.InputAuthority;
    if (Players.ContainsKey(playerRef) == true) {
        Debug.LogError($"Player {playerRef} already joined");
        return;
    }
    
    Players.Add(playerRef, player);
    OnPlayerJoined(player);
}
```

#### Phân Tích Logic:
1. **Kiểm tra quyền**: Chỉ máy chủ mới được thêm player
2. **Lấy PlayerRef**: Từ InputAuthority của NetworkObject
3. **Kiểm tra trùng lặp**: Tránh thêm player đã tồn tại
4. **Thêm vào Dictionary**: Lưu trữ player theo PlayerRef
5. **Gọi callback**: OnPlayerJoined để xử lý logic spawn

### 2. Phương Thức Leave Player

```csharp
public void Leave(Player player)
{
    if (HasStateAuthority == false) return;
    if (Players.ContainsKey(player.Object.InputAuthority) == false) return;
    
    Players.Remove(player.Object.InputAuthority);
    OnPlayerLeft(player);
}
```

#### Phân Tích Logic:
1. **Kiểm tra quyền**: Chỉ máy chủ xử lý
2. **Kiểm tra tồn tại**: Player phải có trong dictionary
3. **Xóa khỏi Dictionary**: Remove theo PlayerRef
4. **Gọi callback**: OnPlayerLeft để dọn dẹp

### 3. Vòng Đời NetworkBehaviour

```csharp
public override void Spawned()
{
    Context.Gameplay = this;
}

public override void Despawned(NetworkRunner runner, bool hasState)
{
    Context.Gameplay = null;
}
```

#### Phân Tích Vòng Đời:
- **Spawned()**: Đăng ký với Context khi object được tạo
- **Despawned()**: Xóa khỏi Context khi object bị hủy
- **Context Pattern**: Sử dụng để truy cập global từ các script khác

### 4. FixedUpdateNetwork - Tick Processing

```csharp
public override void FixedUpdateNetwork()
{
    if (HasStateAuthority == false) return;
    
    int currentTick = Runner.Tick;
    for (int i = _spawnRequests.Count - 1; i >= 0; i--) {
        var request = _spawnRequests[i];
        if (request.Tick > currentTick) continue;
        
        _spawnRequests.RemoveAt(i);
        // Xử lý spawn request...
    }
}
```

#### Phân Tích Tick System:
1. **Chỉ máy chủ xử lý**: HasStateAuthority check
2. **Lặp ngược**: Tránh lỗi index khi remove item
3. **So sánh Tick**: Chỉ xử lý request đã đến thời gian
4. **Remove và Process**: Xóa request và thực hiện spawn

## 🎯 Luồng Xử Lý Gameplay

### 1. Hệ Thống Spawn Agent

```csharp
protected void SpawnPlayerAgent(Player player)
{
    DespawnPlayerAgent(player);
    
    var agent = SpawnAgent(player.Object.InputAuthority, player.AgentPrefab) as PlayerAgent;
    player.AssignAgent(agent);
    
    agent.Health.FatalHitTaken += OnFatalHitTaken;
    OnPlayerAgentSpawned(agent);
}
```

#### Phân Tích Spawn Logic:
1. **Dọn dẹp trước**: DespawnPlayerAgent để tránh duplicate
2. **Spawn Agent**: Tạo PlayerAgent với InputAuthority
3. **Gán Agent**: Liên kết Player với Agent
4. **Đăng ký Event**: Subscribe FatalHitTaken để xử lý chết
5. **Post-Spawn**: Gọi callback xử lý bổ sung

### 2. Hệ Thống Respawn

```csharp
protected virtual void OnPlayerDeath(Player player)
{
    AddSpawnRequest(player, 3f);
}

protected void AddSpawnRequest(Player player, float spawnDelay)
{
    int delayTicks = Mathf.RoundToInt(Runner.TickRate * spawnDelay);
    _spawnRequests.Add(new SpawnRequest() {
        Player = player,
        Tick = Runner.Tick + delayTicks,
    });
}
```

#### Phân Tích Respawn System:
- **Delay 3 giây**: Chuyển đổi float sang tick
- **Tick-based**: Sử dụng Runner.Tick để tính thời gian
- **Queue System**: Lưu request vào list để xử lý sau

### 3. Xử Lý Event Chết

```csharp
private void OnFatalHitTaken(HitData hitData)
{
    var health = hitData.Target as Health;
    if (health == null) return;
    
    if (Players.TryGet(health.Object.InputAuthority, out Player player) == true) {
        OnPlayerDeath(player);
    }
}
```

#### Phân Tích Event Handler:
1. **Cast Target**: Chuyển đổi sang Health component
2. **Null Check**: Kiểm tra tính hợp lệ
3. **Tìm Player**: Dùng InputAuthority để tìm trong Dictionary
4. **Trigger Death**: Gọi OnPlayerDeath để bắt đầu respawn

### 4. Quản Lý Spawn Point

```csharp
private PlayerAgent SpawnAgent(PlayerRef inputAuthority, PlayerAgent agentPrefab)
{
    if (_spawnPoints == null) {
        _spawnPoints = Runner.SimulationUnityScene.FindObjectsOfTypeInOrder<SpawnPoint>(false);
    }
    
    _lastSpawnPoint = (_lastSpawnPoint + 1) % _spawnPoints.Length;
    var spawnPoint = _spawnPoints[_lastSpawnPoint].transform;
    
    var agent = Runner.Spawn(agentPrefab, spawnPoint.position, spawnPoint.rotation, inputAuthority);
    return agent;
}
```

#### Phân Tích Spawn Point Logic:
1. **Lazy Loading**: Chỉ load spawn points khi cần
2. **Round-Robin**: Luân phiên các spawn point
3. **Modulo Operation**: Quay vòng khi hết spawn point
4. **Network Spawn**: Tạo object qua Runner với authority

## 📊 Tối Ưu Hóa & Hiệu Năng

### Cấu Trúc Data
- **NetworkDictionary**: Capacity 200 để giới hạn memory
- **List<SpawnRequest>**: Dynamic array cho queue
- **Array SpawnPoint**: Cache để tránh FindObjects liên tục

### Network Optimization
- **HasStateAuthority**: Chỉ server xử lý logic quan trọng
- **Tick-based Timing**: Sử dụng tick thay vì Time.deltaTime
- **Event-driven**: Subscribe/Unsubscribe events khi cần

## � Pattern & Best Practice Trong Code

### 1. Context Pattern
```csharp
public override void Spawned() {
    Context.Gameplay = this;
}
```
- **Singleton Access**: Cho phép truy cập global
- **Dependency Injection**: Tránh hard coupling
- **Lifecycle Management**: Tự động cleanup

### 2. Event-Driven Architecture
```csharp
agent.Health.FatalHitTaken += OnFatalHitTaken;
// Cleanup khi despawn
player.ActiveAgent.Health.FatalHitTaken -= OnFatalHitTaken;
```
- **Loose Coupling**: Component không phụ thuộc trực tiếp
- **Memory Safety**: Subscribe/Unsubscribe đúng cách
- **Responsive**: Xử lý ngay khi có event

### 3. Authority Pattern
```csharp
if (HasStateAuthority == false) return;
```
- **Server Authority**: Chỉ server thay đổi state
- **Client Prediction**: Client có thể predict
- **Conflict Resolution**: Server có quyền quyết định cuối

### 4. Object Pooling Pattern (Implicit)
```csharp
// Despawn thay vì Destroy
Runner.Despawn(agent.Object);
```
- **Memory Efficiency**: Tái sử dụng object
- **Performance**: Tránh GC allocation
- **Network Optimization**: Fusion tự động handle pooling

## 🧠 Kiến Trúc Tư Duy Code

### Separation of Concerns
- **Gameplay**: Quản lý logic game
- **Player**: Thông tin người chơi
- **PlayerAgent**: Thực thể trong game
- **Health**: Component riêng biệt

### Data Flow
```
NetworkRunner → Gameplay → Player → PlayerAgent → Health
```

### Error Handling
```csharp
if (player == null || player.Object == null) continue;
if (Players.ContainsKey(request.Player.Object.InputAuthority) == false) continue;
```
- **Null Checks**: Kiểm tra tính hợp lệ
- **Early Return**: Thoát sớm khi có lỗi
- **Graceful Degradation**: Tiếp tục xử lý các case khác