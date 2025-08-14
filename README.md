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

| Pin # | Name | FPGA pin # || Pin # | Name | FPGA pin # |
|-------|------|-----------|-|-------|------|------------|
| 1  | +VS    |       || 28 | +VS   |  |
| 2  | LVDD   |       || 27 | IN    |  |
| 3  | D0 LSB |   23  || 26 | CM    |  |
| 4  | D1     |   22  || 25 | LnBy  |  |
| 5  | D2     |   21  || 24 | REFB  |  |
| 6  | D3     |   20  || 23 | NC    |  |
| 7  | D4     |   19  || 22 | REFT  |  |
| 8  | D5     |   18  || 21 | LpBy  |  |
| 9  | D6     |   17  || 20 | GND   |  |
| 10 | D7     |   16  || 19 | GND   |  |
| 11 | D8     |   15  || 18 | +VS   |  |
| 12 | D9 MSB |   14  || 17 | Pwrdn |  |
| 13 | GND    |       || 16 | _OE_  |  |
| 14 | GND    |       || 15 | CLK   | 13 |

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
