

# 增加FM收音机 RSSI显示功能

LCD屏幕相关:
分辨率128*64
一共可以显示8行, 小字体占用一行, 大字体/字符占用两行

uint8_t gStatusLine[128];
第一行固定显示状态栏
uint8_t gFrameBuffer[7][128];
显示缓存定义了7组, 实际应该是从第二行开始显示

大字符 16*8, 占2行, 调用接口: UI_PrintString
小数字 7*8, 占1行, 调用接口: UI_DisplaySmallDigits
大数字 26*8, 占2行, 调用接口: UI_DisplayFrequency
新增:
大字符 7*8, 占1行, 调用接口: UI_DisplaySmall

以上这些都是将显示数据填充到gFrameBuffer中, 
接着需要调用ST7565_BlitFullScreen()将gFrameBuffer数据推到驱动芯片上, 流程太长

使用ST7565_DrawLine可以只修改局部数据, 更新画面流程最短

显示代码调用流程如下:
APP_TimeSlice10ms() 轮询, 周期10ms
	UI_DisplayStatus();
	GUI_DisplayScreen() 10ms判断一次是否要运行, 如果gUpdateDisplay标志在其他地方置位, 立即刷新屏幕
		UI_DisplayMain()
		UI_DisplayFM() 刷新一次FM界面所有数据
		UI_DisplayMenu()
		UI_DisplayScanner()
		UI_DisplayAircopy()

整个屏幕刷新, 会导致噪音

使用ST7565_DrawLine局部刷新, 实测依然有噪音(好像稍微好一点)



# Open reimplementation of the Quan Sheng UV K5 v2.1.27 firmware

This repository is a preservation project of the UV K5 v2.1.27 firmware.
It is dedicated to understanding how the radio works and help developers making their own customisations/fixes/etc.
It is by no means fully understood or has all variables/functions properly named, as this is best effort only.
As a result, this repository will not include any customisations or improvements over the original firmware.

You can find an alternate branch called "fixes" that contains fixes for real bugs present in the original firmware.
This branch will also accumulate fixes/improvements from newer releases by QS (for example v2.01.31).

For improved/better firmware and new features, you can find the following repositories by other collaborators:

* https://github.com/fagci/uv-k5-firmware-fagci-mod
* https://github.com/OneOfEleven/uv-k5-firmware-cust11om
* https://github.com/Tunas1337/uv-k5-firmware (Check the branches)
* https://github.com/rebezhir/openquack for Russian users

# Compiler

arm-none-eabi GCC version 10.3.1 is recommended, which is the current version on Ubuntu 22.04.03 LTS.
Other versions may generate a flash file that is too big.
You can get an appropriate version from: https://developer.arm.com/downloads/-/gnu-rm

# Building

To build the firmware, you need to fetch the submodules and then run make:
```
git submodule update --init --recursive --depth=1
make
```

# Flashing with the official updater

* Use the firmware.packed.bin file

# Flashing with [k5prog](https://github.com/piotr022/k5prog)

* ./k5prog -F -YYY -b firmware.bin

# Flashing with SWD

* If you own a JLink or compatible device and want to use the Segger software, you can find a flash loader [here](https://github.com/DualTachyon/dp32g030-flash-loader)
* If you want to use OpenOCD instead, you can use run "make flash" off this repo.
* The DP32G030 has flash masking to move the bootloader out of the way. Do not try to flash your own way outside of the above methods or risk losing your bootloader.

# Support

* If you like my work, you can support me through https://ko-fi.com/DualTachyon

# Credits

Many thanks to various people on Telegram for putting up with me during this effort and helping:

* [Mikhail](https://github.com/fagci/)
* [Andrej](https://github.com/Tunas1337)
* [Manuel](https://github.com/manujedi)
* @wagner
* @Lohtse Shar
* [@Matoz](https://github.com/spm81)
* @Davide
* @Ismo OH2FTG
* [OneOfEleven](https://github.com/OneOfEleven)
* and others I forget

# License

Copyright 2023 Dual Tachyon
https://github.com/DualTachyon

Licensed under the Apache License, Version 2.0 (the "License");
you may not use this file except in compliance with the License.
You may obtain a copy of the License at

    http://www.apache.org/licenses/LICENSE-2.0
    
    Unless required by applicable law or agreed to in writing, software
    distributed under the License is distributed on an "AS IS" BASIS,
    WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
    See the License for the specific language governing permissions and
    limitations under the License.

