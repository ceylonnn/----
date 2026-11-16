# GPIO — STM32F103C8T6 + VS Code 开发环境

CubeMX 6.18 生成（Toolchain = **CMake**），用 CMake + Ninja + arm-none-eabi-gcc 编译，
Cortex-Debug + OpenOCD + ST-LINK 烧录调试。

## 环境

| 组件 | 版本 | 路径 |
|---|---|---|
| Arm GNU Toolchain | 13.3.1 | `C:\STM32\gcc\bin` |
| OpenOCD | 0.12.0 | `C:\STM32\openocd\bin` |
| CMake | 3.31.6 | `C:\STM32\cmake\bin` |
| Ninja | 1.12.1 | `C:\STM32\ninja` |
| STM32CubeProgrammer | 2.23.0 | `C:\Program Files\STMicroelectronics\...\bin` |
| ST-LINK | V2J37S7, SN 37FF71064E5734368B8E1243 | USB HUB |

以上目录均已加入用户 PATH。

## 编译

```powershell
cmake --preset Debug
cmake --build --preset Debug
```

或直接 `Ctrl+Shift+B`（默认任务 `Build (Debug)`，会自动先 configure）。

产物：`build/Debug/GPIO.elf` / `.map` / `compile_commands.json`。

## 烧录 / 调试

| 操作 | 方法 |
|---|---|
| 编译 + 烧录 + 进调试 | **F5**（配置 `Debug (OpenOCD + ST-LINK)`） |
| 只烧录 | `Tasks: Run Task` → **Flash (OpenOCD / ST-LINK)** |
| 全片擦除 | `Tasks: Run Task` → **Erase chip (OpenOCD / ST-LINK)** |
| 图形界面烧录 | STM32CubeProgrammer 打开 elf → 下载 |

> ⚠️ **CubeProgrammer 与 OpenOCD 不能同时占用 ST-LINK**。
> F5 之前请在 CubeProgrammer 里点"断开连接"，否则 OpenOCD 报
> `libusb_open() failed with LIBUSB_ERROR_ACCESS`。

## 踩过的坑（重要）

1. **`Error: init mode failed` / `Unable to get core ID`**
   = ST-LINK 与 MCU 之间的 SWD 没通。
   本项目实际原因是**排线接到了 UART 那组 4 针排针**。
   蓝丸类板子有两组 4 针：SWD 是丝印 `3.3 / DIO / CLK / GND`（复位键那一端），
   串口是 `3V3 / RXD / TXD / GND`。接错时探针一切正常、只是目标永远不响应。
2. **`No ST-Link detected`** = USB 线是充电线（无 D+/D-）或插到了只供电的口。
3. **OpenOCD 无法打开含中文的路径**
   所以 Flash 任务用相对路径 `build/Debug/${workspaceFolderBasename}.elf`，
   不要改成绝对路径。或者把工程放到纯 ASCII 路径下。
4. **ST-LINK 驱动**：CubeProgrammer 安装后绑定的是 WinUSB，
   OpenOCD 和 CubeProgrammer 都能用，不要再用 Zadig 换驱动。

## 调试能力

- 断点 / 单步 / 变量 / 调用栈：Run and Debug 面板
- 外设寄存器：`STM32F103.svd` 已加载，Peripherals 里看 GPIO/RCC/USART
- 内存 / 反汇编：`Ctrl+Shift+P` → `Cortex-Debug: View Memory / View Disassembly`

## 改代码与重新生成

- 用户代码写在 `USER CODE BEGIN/END` 之间，CubeMX 重新生成不会覆盖
- 新增源文件 / include / 宏 → 改**根目录 `CMakeLists.txt`**（该文件只生成一次）
- **不要改** `cmake/stm32cubemx/CMakeLists.txt`，那是 CubeMX 每次生成都会重写的
- `EWARM/` 是切换工具链前的残留，没有 IAR 打不开，可以删
