## Reverse engineering the cheapest OTDRs from China (Tynish TN1700).

Available on Aliexpress for around $100.<br>
https://www.google.com/search?q=TN1700+site%3Aaliexpress.com

![PCB](img/TN1700_pcb_2.png "PCB TN1700")

### APD(avalanche photodiode) power supply
https://www.sg-micro.com/product/SGM41282C<br>
https://www.sg-micro.com/rect/assets/7a6f5550-29b0-4f92-9198-7deb495df53e/SGM41282C.pdf<br>

### TIA(transimpedance amplifier) & ADC amplifier
https://www.ti.com/lit/ds/symlink/opa657.pdf

### ADC
Looks like TI ADS901 or THS1031.

<details>
<summary>ADC pinout</summary>

| Pin # | ADS901 | THS1031 | FPGA pin # || Pin # | ADS901 | THS1031 | FPGA pin # |
|-------|------|-----------|-----------|-|-------|------|------|------------|
| 1  | +VS    | AGND |   GND || 28 | +VS   | AVDD    | +V |
| 2  | LVDD   | DVDD |   +V  || 27 | IN    | AIN     | R19->TIA |
| 3  | D0 LSB | I/O0 |   23  || 26 | CM    | VREF    |  |
| 4  | D1     | I/O1 |   22  || 25 | LnBy  | REFBS   | GND |
| 5  | D2     | I/O2 |   21  || 24 | REFB  | REFBF   | C7->GND; C5->... |
| 6  | D3     | I/O3 |   20  || 23 | NC    | MODE    | R23->GND; R22->... |
| 7  | D4     | I/O4 |   19  || 22 | REFT  | REFTF   | C3->GND |
| 8  | D5     | I/O5 |   18  || 21 | LpBy  | REFTS   | R11 DNI |
| 9  | D6     | I/O6 |   17  || 20 | GND   | CLAMPIN | GND |
| 10 | D7     | I/O7 |   16  || 19 | GND   | CLAMP   | GND |
| 11 | D8     | I/O8 |   15  || 18 | +VS   | REFSENSE| GND |
| 12 | D9 MSB | I/O9 |   14  || 17 | Pwrdn | WR      | R5->GND |
| 13 | GND    |  OVR |       || 16 | _OE_  | _OE_    | R4->GND |
| 14 | GND    | DGND |   GND || 15 | CLK   | CLK     | 13 |

</details>

### FPGA
Looks like Gowin GW1NZ series.

<details>
<summary>FPGA pinout</summary>

| #| Name | to Dev | to Pin |
|--|------|--------|--------|
| 1| Power |  |  |
| 2| Ground |  |  |
| 3| TCK | J9 | 1 |
| 4| TMS | J9 | 2 |
| 5| TDI | J9 | 3 |
| 6|  |  |  |
| 7| TDO | J9 | 4 |
| 8|  |  |  |
| 9|  |  |  |
|10|  |  |  |
|11|  |  |  |
|12| Power |  |  |
|13| ADC_CLK | ADC | 15 |
|14|  | ADC | 12 |
|15|  | ADC | 11 |
|16|  | ADC | 10 |
|17|  | ADC | 9 |
|18|  | ADC | 8 |
|19|  | ADC | 7 |
|20|  | ADC | 6 |
|21|  | ADC | 5 |
|22|  | ADC | 4 |
|23|  | ADC | 3 |
|24|  |  |  |
|25| Power |  |  |
|26| Ground |  |  |
|27|  |  |  |
|28|  |  |  |
|29|  |  |  |
|30|  |  |  |
|31|  |  |  |
|32|  |  |  |
|33|  |  |  |
|34|  |  |  |
|35|  |  |  |
|36| Power |  |  |
|37| Power |  |  |
|38|  |  |  |
|39|  |  |  |
|40|  |  |  |
|41|  |  |  |
|42|  |  |  |
|43|  |  |  |
|44|  |  |  |
|45|  |  |  |
|46|  |  |  |
|47|  |  |  |
|48|  |  |  |

</details>

**J9** - FPGA JTAG

| #| Name |
|--|------|
| 1| TCK |
| 2| TMS |
| 3| TDI |
| 4| TDO |
| 5| +V |
| 6| GND |
| 7| - |

### MCU
Looks like an STM32 clone from GigaDevice.

<details>
<summary>MCU pinout</summary>

| #| Con  |      | | #| Con  |      | | #| Con  |      | | #| Con  |      | 
|--|------|------|-|--|------|------|-|--|------|------|-|--|------|------|
| 1|      |      | |26|Reboot|      | |51|      |      | |76| J4-5 |      |
| 2|      |      | |27| GND  |      | |52|      |      | |77|      |      |
| 3|      |      | |28|      |      | |53|      |      | |78|      |      |
| 4|      |      | |29|      |      | |54|      |      | |79|      |      |
| 5|      |      | |30|      |      | |55|      |      | |80|      |      |
| 6|      |      | |31|      |      | |56|      |      | |81|      |      |
| 7|      |      | |32|      |      | |57|      |      | |82|      |      |
| 8| 32KHz|      | |33|      |      | |58|      |      | |83|      |      |
| 9| 32KHz|      | |34|      |      | |59|      |      | |84|      |      |
|10| GND  |      | |35|      |      | |60|      |      | |85|      |      |
|11|      |      | |36|      |      | |61|      |      | |86|      |      |
|12| 25MHz|      | |37|      |      | |62|      |      | |87|      |      |
|13| 25MHz|      | |38|      |      | |63|      |      | |88|      |      |
|14|      |      | |39|      |      | |64|      |      | |89|      |      |
|15|      |      | |40|      |      | |65|      |      | |90|      |      |
|16|      |      | |41|      |      | |66|      |      | |91|      |      |
|17|      |      | |42|      |      | |67|      |      | |92|      |      |
|18|      |      | |43|      |      | |68| J4-3 |      | |93|      |      |
|19|      |      | |44|      |      | |69| J4-2 |      | |94|      |      |
|20|      |      | |45|      |      | |70|      |      | |95|      |      |
|21|      |      | |46|      |      | |71|      |      | |96|      |      |
|22|      |      | |47|      |      | |72| J4-6 |      | |97|      |      |
|23|      |      | |48|      |      | |73|      |      | |98|      |      |
|24|      |      | |49|      |      | |74|      |      | |99|      |      |
|25|      |      | |50|      |      | |75| +V   |      | |00| +V   |      |

</details>

**J4** - MCU Config

| #| Con  | todo |
|--|------|------|
| 1|  ||
| 2| 69  | USART1_RX |
| 3| 68  | USART1_TX |
| 4| GND ||
| 5| 76  | SWCLK |
| 6| 72  | SWDIO |
| 7|  ||
