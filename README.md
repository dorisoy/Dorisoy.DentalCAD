# Dorisoy.DentalCAD — 口腔种植设计软件

基于 Blender 5.0.1 开源内核打造的专业口腔种植规划与设计软件。

---

## 项目概述

Dorisoy.DentalCAD 是一款面向口腔种植临床场景的三维设计工具。项目以 Blender 5.0.1 为底层 3D 引擎，通过深度裁剪与定制，移除了动画、渲染、合成等非医疗模块，保留高性能三维编辑内核，并规划集成 VTK、ITK、DCMTK、Open3D 等医学计算库，为口腔种植体放置、骨量分析及手术导板设计提供一体化解决方案。

## 系统架构

```
DentalCAD
├── 界面定制系统
│   ├── 顶部菜单栏（文件 / 编辑 / 种植设计 / 窗口 / 帮助）
│   ├── 工作区标签（布局 / 建模 / 雕刻 / 脚本）
│   └── 属性面板（工具 / 场景 / 集合 / 对象 / 修改器 / 约束 / 数据）
├── 3D 编辑内核
│   ├── 3D 视口与导航
│   ├── 对象操作（变换 / 选择 / 捕捉）
│   ├── 网格编辑（顶点 / 边 / 面编辑）
│   ├── 修改器系统（布尔 / 镜像 / 细分 等）
│   ├── 雕刻模式
│   └── 测量工具
├── 医学影像分析（规划中）
│   ├── DCMTK — DICOM 影像解析
│   ├── VTK — 医学三维可视化
│   ├── ITK — 图像分割与处理
│   └── Open3D — 点云与三维重建
├── 口腔种植模块（规划中）
│   ├── 种植体放置
│   ├── 基台选配
│   ├── 骨量密度分析
│   └── 手术导板设计
└── 品牌与交付
    ├── 自定义启动画面
    ├── 窗口标题 
    ├── 版权信息
    └── 默认简体中文界面
```

## 构建环境

| 项目 | 版本 / 配置 |
|------|-------------|
| **操作系统** | Windows 10/11 (x64) |
| **编译器** | Visual Studio 2022 (MSVC) |
| **CMake** | 3.20+ |
| **Python** | 3.11（内嵌） |
| **Blender 内核** | 5.0.1 |
| **源码目录** | `E:\Sinol\Blender\blender-5.0.1` |
| **构建目录** | `E:\Sinol\Blender\build_dentalcad` |
| **输出文件** | `dentalcad.exe` / `dentalcad-launcher.exe` |

## 快速开始

### 1. 前置依赖

- 安装 Visual Studio 2022（含 "C++ 桌面开发" 工作负载）
- 安装 CMake 3.20+
- Blender 预编译依赖库：`E:\Blender\blender-5.0.1\lib\windows_x64`

### 2. CMake 配置

```powershell
cmake -S E:\Blender\blender-5.0.1 `
      -B E:\Blender\build_dentalcad `
      -DWITH_INTERNATIONAL=ON
```

### 3. 编译

```powershell
# Release 编译（推荐）
cmake --build E:\Blender\build_dentalcad --config Release --target blender -- /m:1

# 安装（复制脚本、数据文件到输出目录）
cmake --build E:\Blender\build_dentalcad --config Release --target install -- /m:1
```

> **注意**：使用 `/m:1`（单线程链接）可避免 PDB 文件锁冲突导致的 LNK1318 错误。

### 4. 运行

```powershell
# 使用出厂默认配置启动
E:\Blender\build_dentalcad\bin\Release\dentalcad.exe --factory-startup

# 批处理模式（无窗口，用于测试）
E:\Blender\build_dentalcad\bin\Release\dentalcad.exe --factory-startup -b --python-expr "import bpy; print('OK')"
```

### 5. Visual Studio 开发

1. 用 VS 2022 打开 `E:\Blender\build_dentalcad\DentalCAD.sln`
2. 将 **blender** 项目设为启动项目
3. 选择 **Release x64** 配置编译运行

## 已裁剪的 Blender 模块

以下模块已从 UI 和构建中移除，以精简软件体积并聚焦牙科设计场景：

| 类别 | 已移除的模块 |
|------|-------------|
| **UV 编辑** | space_image, properties_texture |
| **纹理绘制** | properties_texture |
| **着色** | properties_material, properties_world, properties_data_light, properties_data_lightprobe, node_add_menu_shader, space_node |
| **动画** | anim, properties_animviz, space_dopesheet, space_graph, space_nla, space_time |
| **渲染** | properties_render, properties_output, properties_view_layer, Cycles 渲染引擎 |
| **合成** | node_add_menu (compositor) |
| **几何节点** | node_add_menu_geometry |
| **物理** | properties_physics_*, Bullet 物理引擎 |
| **粒子** | properties_particle |
| **视频编辑** | space_sequencer, FFmpeg 编解码 |
| **蜡笔** | properties_data_grease_pencil, properties_grease_pencil_common |
| **骨骼/动作** | properties_data_armature, properties_data_bone |

### CMake 层面关闭的功能

```
WITH_CYCLES=OFF          # Cycles 渲染引擎
WITH_BULLET=OFF          # Bullet 物理引擎
WITH_AUDASPACE=OFF       # 音频引擎
WITH_CODEC_FFMPEG=OFF    # FFmpeg 编解码
WITH_FREESTYLE=OFF       # Freestyle 线条渲染
WITH_ALEMBIC=OFF         # Alembic 缓存格式
WITH_USD=OFF             # Universal Scene Description
WITH_IO_FBX=OFF          # FBX 导入导出
```

### 保留的 I/O 格式

```
WITH_IO_STL=ON           # STL（牙科模型标准格式）
WITH_IO_PLY=ON           # PLY（点云与扫描数据）
WITH_IO_WAVEFRONT_OBJ=ON # OBJ（通用网格交换）
```

## 保留的核心功能

- **3D 视口**：完整的三维导航、视角切换、显示模式
- **对象操作**：移动 / 旋转 / 缩放 / 捕捉 / 对齐
- **网格编辑**：顶点 / 边 / 面编辑、挤出、倒角、切割、布尔运算
- **修改器**：布尔 / 镜像 / 细分 / 阵列 / 实体化 等
- **雕刻模式**：多分辨率雕刻、动态拓扑
- **约束系统**：对象约束
- **场景管理**：大纲视图 / 集合 / 层级
- **文件 I/O**：STL / PLY / OBJ 导入导出
- **Python 脚本**：完整的 Python API 与脚本编辑器
- **国际化**：默认简体中文界面

## 医学库集成（规划中）

项目已在 CMake 中预留医学库开关，当前均为 OFF，待后续开发启用：

| 开关 | 库 | 用途 |
|------|-----|------|
| `WITH_DENTAL_VTK` | VTK | 医学三维可视化、体绘制 |
| `WITH_DENTAL_ITK` | ITK | 医学图像分割与配准 |
| `WITH_DENTAL_DCMTK` | DCMTK | DICOM 影像文件解析 |
| `WITH_DENTAL_OPEN3D` | Open3D | 点云处理与三维重建 |

## 项目目录结构

```
E:\Blender\
├── blender-5.0.1\                  # Blender 源码（含 DentalCAD 修改）
│   ├── scripts\startup\bl_ui\      # Python UI 模块（核心裁剪点）
│   │   ├── __init__.py             # 模块加载控制、工作区管理、语言设置
│   │   ├── space_topbar.py         # 顶部菜单栏（种植设计菜单）
│   │   ├── space_properties.py     # 属性面板标签页裁剪
│   │   └── space_view3d.py         # 3D 视口菜单适配
│   ├── source\blender\             # C/C++ 源码
│   │   └── dental\                 # DentalCAD 医学模块（规划中）
│   └── release\windows\icons\
│       └── winblender.rc           # 版本信息（sinol / ©2026）
├── build_dentalcad\                # CMake 构建目录
│   ├── DentalCAD.sln               # VS 解决方案
│   └── bin\Release\
│       ├── dentalcad.exe           # 主程序
│       ├── dentalcad-launcher.exe  # 启动器
│       └── 5.1\                    # 运行时数据
│           ├── scripts\            # Python 脚本
│           ├── datafiles\icons\    # 工具栏图标 (.dat)
│           └── datafiles\locale\   # 语言包
└── blender-release-extracted\      # Blender 官方发行版（LFS 资源来源）
```

## 关键修改文件清单

| 文件 | 修改内容 |
|------|----------|
| `scripts/startup/bl_ui/__init__.py` | 模块加载裁剪、工作区清理与中文重命名、时间轴移除、默认中文设置 |
| `scripts/startup/bl_ui/space_topbar.py` | 移除 Render 菜单、添加"种植设计"菜单、帮助菜单中文化 |
| `scripts/startup/bl_ui/space_properties.py` | 移除渲染/输出/视图层/世界/材质/纹理标签页 |
| `scripts/startup/bl_ui/space_view3d.py` | 替换 IMAGE_MT_uvs_unwrap 引用 |
| `scripts/addons_core/io_mesh_uv_layout/__init__.py` | hasattr 安全检查（UV 编辑器已移除） |
| `scripts/addons_core/io_scene_gltf2/blender/com/gltf2_blender_ui.py` | hasattr 安全检查（着色节点已移除） |
| `release/windows/icons/winblender.rc` | 品牌信息：sinol / DentalCAD / ©2026 |
| `CMakeLists.txt` | 输出名 dentalcad、医学库开关、模块裁剪 |

## 已知问题

- **Splash Screen**：启动画面图片为 LFS 指针，需替换为自定义启动画面
- **Debug 构建**：Debug 配置可能出现 SxS 运行时错误，建议使用 Release 配置
- **LFS 资源**：源码中部分二进制文件（.dat、.blend）需从官方发行版复制

## 版权信息

- **软件名称**：DentalCAD
- **公司名称**：Dorisoy
- **版权声明**：©2026 Dorisoy
- **内核基础**：Blender 5.0.1（GPL-2.0-or-later）

> 本项目基于 Blender 开源内核开发，遵循 GNU General Public License v2.0 或更高版本。

