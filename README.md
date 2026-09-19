# 桌宠 Pet

这是一个可直接双击运行的 Windows x64 透明桌宠。最终程序位于 `publish\Pet.exe`，根目录也有一份 `Pet.exe`。

## 目录说明

- `src\`：Win32/C++ 源代码、资源脚本和 DPI manifest。
- `assets\pet\pet_red.png`：红衣站立形态，透明 PNG。
- `assets\pet\pet_sitting.png`：黑灰衣坐姿形态，透明 PNG。
- `assets\pet.ico`：程序图标，已嵌入 Pet.exe。
- `tools\process_images.py`：可重复执行的人物裁切、透明蒙版、统一画布和图标生成脚本。
- `tools\build.ps1`：Release x64 构建脚本。
- `publish\`：最终发布目录；包含 Pet.exe 和运行所需 PNG。
- `config.json`：程序首次退出时写入 Pet.exe 所在目录，保存位置、形态和置顶状态；损坏时自动回退默认设置。
- `_reference.jpg`：原始参考图副本，处理脚本不会修改它。

## 启动与操作

1. 进入 `E:\pet\publish\`，双击 `Pet.exe`。
2. 左键按住移动超过 7 像素：拖动桌宠；拖动不会产生音符。
3. 左键单击：在头顶生成随机 ♪ / ♫ / ♬，约 0.9–1.35 秒上浮并淡出。
4. 左键双击：在红衣站立与黑灰坐姿之间切换；双击不会重复生成音符。
5. 右键：可“切换形态”“置于最前”“重置位置”或“退出”。
6. 正常退出会保存当前位置、形态和置顶状态；下次启动恢复。

## 重新编译

本机未安装 .NET 8 SDK，且当前受限网络无法取得 SDK，因此最终实现使用原生 Win32 + GDI+（C++17），没有 .NET 运行时依赖。发布结果是静态链接的 x64 单文件 EXE；系统只使用 Windows 自带的 GDI+。

已验证编译器：`D:\mingw64\bin\g++.exe`（MinGW-w64 8.1）。在 PowerShell 中运行：

```powershell
powershell -ExecutionPolicy Bypass -File E:\pet\tools\build.ps1
```

Release 构建使用 `-O2 -s -static -static-libgcc -static-libstdc++`。因此“使用的 .NET 版本”为：不适用（原生 Win32 版本）。

## 主要代码

- `src\main.cpp`：DPI 感知、GDI+ 初始化和消息循环。
- `src\PetWindow.cpp`：透明分层窗口、拖动阈值、单双击仲裁、右键菜单、定位与渲染。
- `src\PetStateManager.cpp`：缓存两张 PNG 并切换状态。
- `src\MusicNoteAnimation.cpp`：按需启动的音符上浮和渐隐动画。
- `src\ConfigManager.cpp`：容错读取和保存 JSON 配置。

## 更换人物图片

直接替换 `assets\pet\pet_red.png` 与 `assets\pet\pet_sitting.png`，保持 PNG 透明背景。建议两张图都使用相同画布和底部锚点（当前为 520×720），然后重新运行 `tools\build.ps1`；也可同时替换 `publish\assets\pet\` 中的两张图片。程序启动时缓存资源，不会每帧读取磁盘。
