## Reverse engineering the cheapest OTDRs from China (Tynish TN1700).

Available on Aliexpress for around $100.<br>
https://www.google.com/search?q=TN1700+site%3Aaliexpress.com

![PCB](img/TN1700_pcb_2.png "PCB TN1700")

#### APD(avalanche photodiode) power supply
https://www.sg-micro.com/product/SGM41282C<br>
https://www.sg-micro.com/rect/assets/7a6f5550-29b0-4f92-9198-7deb495df53e/SGM41282C.pdf<br>

#### TIA(transimpedance amplifier) & ADC amplifier
https://www.ti.com/lit/ds/symlink/opa657.pdf

#### ADC
Looks like TI ADS901 or THS1031.

#### FPGA
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

#### MCU
Looks like an STM32 clone from GigaDevice.
