# stm_bluetooth
STM32F103C8T6操控BT05蓝牙模块给手机发送信息

## Attention

蓝牙模块的串口波特率和单片机与电脑的串口波特率均为**115200**。

蓝牙模块默认波特率为9600，其`AT+BAUD`档位默认为4，需手动调成第8档，调节方法：以9600波特率打开串口调试器，输入`AT+BAUD8`命令（AT指令必须以`\r\n`结尾）并发送。串口调试器返回OK后，以115200波特率重启串口调试器，蓝牙模块即可以115200波特率正常工作。

## Features

自己封装了一个`usart.h`和`usart.c`小库，定义了串口缓冲区结构体以及中断响应函数的模板。

## Main

接线方法：

| STM32     | BT05  |
| --------- | ----- |
| 3.3V      | VCC   |
| GND       | GND   |
| USART2_Tx | Rx    |
| USART2_Rx | Tx    |
| PB1       | STATE |

引脚配置：PA13 PA14用作SWD，启动USART1(PA9 PA10)用于与电脑通讯，启动USART2(PA2 PA3)用来控制蓝牙模块，引脚PB1为GPIO_Input连蓝牙模块的STATE引脚用来判断蓝牙连接状态。除此之外，SPI1与其配套的三个GPIO(CSN CE IRQ)均被启动但是程序中不做任何处理（在硬件上连着无线模块，在配置中只是把相应的引脚空出来）。

运行效果：每2秒检测一次蓝牙状态：若蓝牙模块连接着手机，则向手机发送一段指定的信息并高速闪烁LED；如果没连手机则只低频闪烁LED。同时向电脑串口打印蓝牙状态信息。