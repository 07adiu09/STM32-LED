# STM32 LED Point Light (GPIO 输出实验)

基于 **STM32F103C8T6** 主流开发板 + **Keil MDK** + **STM32F10x 标准外设库** 的点灯实验，
用于学习 **GPIO 的用法与原理**。

## 硬件

| 项目 | 说明 |
|---|---|
| 开发板 | STM32F103C8T6 最小系统板（江协科技入门套件） |
| 下载器 | ST-Link V2 |
| LED | 板载 LED，连接至 **PA0** |
| 点亮方式 | **低电平点亮**（3.3V → 限流电阻 → LED → PA0） |

## GPIO 原理说明

### 1. 为什么要先开时钟
STM32 的外设默认是**关闭时钟**的，不使能时钟直接写寄存器不会有任何效果。
```c
RCC_APB2PeriphClockCmd(RCC_APB2Periph_GPIOA, ENABLE);
```

### 2. 为什么选推挽输出
`GPIO_Mode_Out_PP`（推挽输出）可以**主动输出高电平（3.3V）和低电平（0V）**两种确定电平，
驱动 LED 这类小电流负载正合适。

```c
GPIO_InitTypeDef GPIO_Initstructure;
GPIO_Initstructure.GPIO_Mode  = GPIO_Mode_Out_PP;   // 推挽输出
GPIO_Initstructure.GPIO_Pin   = GPIO_Pin_0;         // 选中 PA0
GPIO_Initstructure.GPIO_Speed = GPIO_Speed_50MHz;   // 输出速度
GPIO_Init(GPIOA, &GPIO_Initstructure);
```

### 3. 为什么低电平点亮
LED 的**正极接 3.3V**，负极经限流电阻接到 PA0。
只有 PA0 输出**低电平（0V）**时，LED 两端才有压差、形成电流回路，灯才会亮。

因此：
- `GPIO_ResetBits(GPIOA, GPIO_Pin_0);` → 输出低电平 → **灯亮**
- `GPIO_SetBits(GPIOA, GPIO_Pin_0);`   → 输出高电平 → **灯灭**

## 目录结构

```
STM32-LED/
├── 1.uvprojx          Keil 工程文件
├── user/
│   └── main.c         主程序
├── Library/           STM32F10x 标准外设库
└── Startup/           启动文件与内核文件
```

## 使用方式

1. 用 **Keil MDK5** 打开 `1.uvprojx`
2. Rebuild（重新编译），确认 `0 Error(s)`
3. 连接 ST-Link，点击 **Download** 下载程序
4. 板载 LED（PA0）点亮
