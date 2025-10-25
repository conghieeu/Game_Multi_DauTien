# 🎮 Game_Multi_DauTien

Unity multiplayer game project sử dụng Photon Fusion networking framework.

## 📋 Mô Tả Dự Án

**Game_Multi_DauTien** là một game multiplayer được phát triển trên nền tảng Unity 3D, sử dụng Photon Fusion để xử lý networking. Dự án này bao gồm các tính năng multiplayer cơ bản như player management, spawn system, và real-time synchronization.

## ⚡ Tính Năng Chính

- ✅ **Multiplayer Networking**: Photon Fusion integration
- ✅ **Player Management**: Join/Leave system với NetworkDictionary
- ✅ **Spawn System**: Automatic player spawning với spawn points
- ✅ **Health System**: Network-synchronized health và respawn
- ✅ **Input Prediction**: Lag compensation và smooth gameplay
- ✅ **Cross Platform**: PC support với potential mobile expansion

## 🛠️ Công Nghệ Sử Dụng

- **Engine**: Unity 2022.3 LTS
- **Networking**: Photon Fusion
- **Rendering**: Universal Render Pipeline (URP)
- **Input**: Unity Input System
- **Platform**: Windows PC (Primary)

## 📁 Cấu Trúc Dự Án

```
Assets/
├── Scripts/              # Core game logic
│   └── Game/
│       └── Gameplay.cs   # Main gameplay controller
├── Prefabs/             # GameObject prefabs
├── Scenes/              # Game scenes
├── Materials/           # Materials và shaders
├── Animations/          # Animation assets
├── Audio/              # Sound effects và music
├── Photon/             # Photon Fusion assets
└── Settings/           # Project configurations
```

## 🚀 Cài Đặt & Setup

### Yêu Cầu Hệ Thống
- **Unity**: 2022.3 LTS hoặc mới hơn
- **OS**: Windows 10/11
- **RAM**: 8GB minimum, 16GB recommended
- **Storage**: 2GB free space

### Bước Cài Đặt

1. **Clone Repository**
   ```bash
   git clone https://github.com/conghieeu/Game_Multi_DauTien.git
   cd Game_Multi_DauTien
   ```

2. **Mở Dự Án trong Unity**
   - Mở Unity Hub
   - Click "Open" và chọn thư mục dự án
   - Đợi Unity import assets

3. **Setup Photon Fusion**
   - Tạo Photon account tại [Photon Engine](https://www.photonengine.com/)
   - Lấy App ID từ Photon Dashboard
   - Paste App ID vào Photon settings trong Unity

4. **Build Settings**
   - File → Build Settings
   - Chọn platform (PC, Mac & Linux Standalone)
   - Click "Build and Run"

## 🎮 Cách Chơi

### Single Player Testing
1. Build game từ Unity
2. Chạy executable
3. Chọn "Host" để tạo room
4. Test gameplay mechanics

### Multiplayer Testing  
1. Build game với Development Build enabled
2. Chạy nhiều instances:
   - Instance 1: Chọn "Host" 
   - Instance 2+: Chọn "Join" và nhập room name
3. Test networking features

## 🏗️ Kiến Trúc Code

### Core Classes

#### `Gameplay.cs`
- **Purpose**: Main gameplay controller
- **Responsibilities**:
  - Player join/leave management
  - Agent spawning/despawning
  - Respawn system
  - Network state synchronization

#### Network Architecture
```
NetworkRunner
└── Gameplay (NetworkBehaviour)
    ├── Players (NetworkDictionary<PlayerRef, Player>)
    ├── SpawnPoints Management
    └── Agent Lifecycle Management
```

### Key Systems

1. **Player Management**
   - NetworkDictionary lưu trữ players by PlayerRef
   - Automatic cleanup khi player disconnect

2. **Spawn System**
   - Round-robin spawn point selection
   - Respawn delay: 3 seconds
   - Spawn protection: 3 seconds immortality

3. **Health System**
   - Network-synchronized health values
   - FatalHitTaken event handling
   - Automatic respawn trigger

## 🔧 Development Workflow

### Git Branching Strategy
- `main`: Production code
- `develop`: Integration branch  
- `feature/*`: New features
- `bugfix/*`: Bug fixes

### Testing Process
1. **Local Testing**: Single player functionality
2. **Network Testing**: Multiple clients trên cùng máy
3. **LAN Testing**: Multiple devices
4. **Performance Testing**: Profiling với Unity Profiler

## 📈 Performance Metrics

- **Tick Rate**: 60 Hz
- **Max Players**: 200 concurrent
- **Network RTT**: < 50ms optimal
- **Frame Rate**: 60 FPS target

## 🐛 Troubleshooting

### Common Issues

1. **Connection Failed**
   - Kiểm tra Photon App ID
   - Verify internet connection
   - Check firewall settings

2. **Sync Issues**
   - Restart Unity
   - Clear Library folder
   - Reimport Photon assets

3. **Build Errors**
   - Update Unity to latest LTS
   - Clear Temp folder
   - Check platform settings

### Debug Tools
- **Fusion Statistics**: Real-time network stats
- **Unity Console**: Error logging
- **Photon Dashboard**: Connection monitoring

## 🤝 Contributing

### Development Guidelines
1. Follow C# coding standards
2. Write clear commit messages
3. Test trước khi push
4. Update documentation

### Pull Request Process
1. Create feature branch từ `develop`
2. Implement changes với tests
3. Submit PR với description
4. Code review và approval
5. Merge vào `develop`

## 📞 Contact & Support

- **Developer**: conghieeu
- **Repository**: [Game_Multi_DauTien](https://github.com/conghieeu/Game_Multi_DauTien)
- **Issues**: GitHub Issues tab
- **Documentation**: [Game Workflow & Architecture](GAME_WORKFLOW_ARCHITECTURE.md)

## 📄 License

Dự án này được phát triển cho mục đích học tập và nghiên cứu.

## 🔄 Version History

- **v1.0.0**: Initial multiplayer setup
- **v0.9.0**: Photon Fusion integration
- **v0.8.0**: Basic networking framework

---

*Last Updated: October 2025*