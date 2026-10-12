### System Diargram 
┌─ Mac ───────────────────────────┐             ┌─ Raspberry Pi 4B ───────────────┐
│                                 │             │                                 │
│ +-------------+ +-------------+ │             │ +-------------+ +-------------+ │
│ |  PlatformIO | |     ssh     | │             │ |     sshd    | |  visca_cli  | │
│ |Debug, Upload| |    client   | │             │ |   TCP :22   | |  C program  | │
│ +-------------+ +-------------+ │             │ +-------------+ +-------------+ │
│        ^               ^        │             │        ^    syscall    ^        │
│        v               v        │             │        v               v        │
│ +-------------+ +-------------+ │             │ +-------------+ +-------------+ │
│ |  USB driver | |     en0     | │             │ |    wlan0    | | /dev/ttyAMA0| │
│ | macOS IOKit | |net interface| │             │ |net interface| |  tty device | │
│ +-------------+ +-------------+ │             │ +-------------+ +-------------+ │
│        ^               ^        │             │        ^               ^        │
│        |               |        │             │        v               v        │
│        |               |        │ +.........+ │ +-------------+ +-------------+ │ +.........+
│        |               |        │ : Wi-Fi AP: │ |   brcmfmac  | |  amba-pl011 | │ :  Dupont :
│        |               |        │ :L2 bridge: │ | Wi-Fi driver| | UART driver | │ :TX/RX/GND:
│        |               |        │ +.........+ │ +-------------+ +-------------+ │ +.........+ ┌─ STM32F103 ─────┐
│        |               |        │      ^      │        ^   MMIO/IRQ    ^        │      ^      │                 │
│        v               v        │      |      │        v               v        │      |      │                 │
│ +=============+ +=============+ │      |      │ +=============+ +=============+ │      |      │ +=============+ │
│ |USB host ctrl| |  Wi-Fi ctrl | │      |      │ |  Wi-Fi ctrl | |  UART ctrl  | │      |      │ |  USART ctrl | │
│ |   built-in  | |   built-in  |<-------+------->|   CYW43455  | |    PL011    |<-------+------->|    USART1   | │
│ |             | |             | │Wi-Fi   Wi-Fi│ |     SDIO    | |  GPIO14/15  | │ UART   UART │ |   PA9/PA10  | │
│ +=============+ +=============+ │             │ +=============+ +=============+ │             │ +=============+ │
│        ^                        │             │                                 │             │                 │
└────────|────────────────────────┘             └─────────────────────────────────┘             │                 │
         |                                +.......................+                             │ +=============+ │
         |                                :       ST-LINK V2      :                             │ |  Debug port | │
         +------------ USB -------------->:Flash Debug Power Reset:<------------ SWD ------------>|     SWD     | │
                                          +.......................+                             │ |  PA13/PA14  | │
                                                                                                │ +=============+ │
                                                                                                │                 │
                                                                                                └─────────────────┘
+---+ software    +===+ hardware    +...+ link / medium


### System settings
Primary UART interface: /dev/serial0 -> /dev/ttyAMA0 
Secondary UART interface: N/A

### Controller & Device nodes
UART: 1. PL011:
         - UART0 -> /dev/ttyAMA0
         - UART2～5 -> N/A
      2. miniUART:
         - UART1 -> /dev/ttyS0


### Picture   
pass

### Docs
1. Definetion of Primary and Secondary UART Interface: 
https://www.raspberrypi.com/documentation/computers/configuration.html#primary-and-secondary-uart

2. Relationship between UART controller and UART interfaces:
https://www.raspberrypi.com/documentation/computers/configuration.html#prerequisite-information  
