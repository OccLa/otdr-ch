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

| #| Name  | Con  | | #| Name       | Con    | | #| Name  | Con   | | #| Name | Con   |
|--|-------|------|-|--|------------|--------|-|--|-------|-------|-|--|-----|--------|
| 1|   Pwr |      | |13| **adc_clk**| ADC-15 | |25|   Pwr |       | |37| Pwr |        |
| 2|   Gnd |      | |14|**adc_d[9]**| ADC-12 | |26|   Gnd |       | |38|     | MCU-67 |
| 3|   TCK | J9-1 | |15|**adc_d[8]**| ADC-11 | |27|**ld1**|   LD1 | |39|     | MCU-64 |
| 4|   TMS | J9-2 | |16|**adc_d[7]**| ADC-10 | |28|**PB9**| MCU-96| |40|     |        |
| 5|   TDI | J9-3 | |17|**adc_d[6]**|  ADC-9 | |29|**PB8**| MCU-95| |41|     |        |
| 6|       |      | |18|**adc_d[5]**|  ADC-8 | |30|**PB7**| MCU-93| |42|     |        |
| 7|   TDO | J9-4 | |19|**adc_d[4]**|  ADC-7 | |31|**PB6**| MCU-92| |43|     |        |
| 8|**PB0**|MCU-35| |20|**adc_d[3]**|  ADC-6 | |32|**PB5**| MCU-91| |44|     |        |
| 9|**PB1**|MCU-36| |21|**adc_d[2]**|  ADC-5 | |33|       |       | |45|     |        |
|10|**PB2**|MCU-37| |22|**adc_d[1]**|  ADC-4 | |34|**PB4**| MCU-90| |46|     |        |
|11|       |      | |23|**adc_d[0]**|  ADC-3 | |35|**PB3**| MCU-89| |47|     |        |
|12|   Pwr |      | |24|    **ld2** |    LD2 | |36|   Pwr |       | |48|     |        |

</details>

**J9** - FPGA JTAG

| #    | 1   |    2|    3|    4|   5|    6|  7|
|-----:|-----|-----|-----|-----|----|-----|---|
| Name | TCK | TMS | TDI | TDO | +V | GND | - |


### MCU
Looks like an STM32 clone from GigaDevice.

<details>
<summary>MCU pinout</summary>

| #| Con        |------------|
|--|------------|------------|
| 1|            |  PE2 | 
| 2|            |  PE3 | 
| 3|            |  PE4 | 
| 4|            |  PE5 | 
| 5|            |  PE6 | 
| 6|            | VBAT | 
| 7|            | PC13 | 
| 8| 32KHz      | OSC32_IN | 
| 9| 32KHz      | OSC32_OUT | 
|10| GND        |  VSS | 
|11|            |  VDD | 
|12| 25MHz      | OSC_IN | 
|13| 25MHz      | OSC_OUT | 
|14| R70,C36,S1 | NRST | 
|15|            |  PC0 | 
|16|            |  PC1 | 
|17|            |  PC2 | 
|18|            |  PC3 | 
|19|            |  VDD | 
|20|            | VSSA | 
|21|            | VREF+ | 
|22|            | VDDA | 
|23|            | PA0/WKUP | 
|24|            |  PA1 | 
|25|            |  PA2 | 
|**#**|**Con**|**------------**|
|26| Reboot |  PA3 |
|27|  GND   |  VSS |
|28|        |  VDD |
|29|        |  PA4 |
|30|        |  PA5 |
|31|        |  PA6 |
|32|        |  PA7 |
|33|        |  PC4 |
|34|        |  PC5 |
|35| FPGA-8 |  PB0 |
|36| FPGA-9 |  PB1 |
|37| FPGA-10| BOOT1/PB2 |
|38|        |  PE7 |
|39|        |  PE8 |
|40|        |  PE9 |
|41|        | PE10 |
|42|        | PE11 |
|43|        | PE12 |
|44|        | PE13 |
|45|        | PE14 |
|46|        | PE15 |
|47|        | PB10 |
|48|        | PB11 |
|49|        |VCAP_1|
|50|        |  VDD |
|**#**|**Con**|**------------**|
|51|  CS_U10   |   PB12 |
|52| U9,10 CLK |SPI2_SCK|
|53| U9,10 DO  |SPI2_MISO|
|54| U9,10 DI  |SPI2_MOSI|
|55|           |    PD8 |
|56|           |    PD9 |
|57|           |   PD10 |
|58|           |   PD11 |
|59|           |   PD12 |
|60|     CS_U9 |   PD13 |
|61|           |   PD14 |
|62|           |   PD15 |
|63|           |    PC6 |
|64|   FPGA-39 |    PC7 |
|65|           |    PC8 |
|66|           |    PC9 |
|67|   FPGA-38 |    PA8 |
|68|   J4-3    |  UA1_TX |
|69|   J4-2    |  UA1_RX |
|70|   Type-C  | OTG_FS_DM |
|71|   Type-C  | OTG_FS_DP |
|72|   J4-6    | SWDIO/PA13 |
|73|           | VCAP_2 |
|74|           |    VSS |
|75|   +V      |    VDD |
|**#**|**Con**|**------------**|
|76| J4-5    |  SWCLK/PA14 |
|77|         | PA15 |
|78|         | PC10 |
|79|         | PC11 |
|80|         | PC12 |
|81|         | PD0 |
|82|         | PD1 |
|83|         | PD2 |
|84|         | PD3 |
|85|         | PD4 |
|86|         | PD5 |
|87|         | PD6 |
|88|         | PD7 |
|89| FPGA-35 | PB3 |
|90| FPGA-34 | PB4 |
|91| FPGA-32 | PB5 |
|92| FPGA-31 | PB6 |
|93| FPGA-30 | PB7 |
|94|         | BOOT0 |
|95| FPGA-29 | PB8 |
|96| FPGA-28 | PB9 |
|97|         | PE0 |
|98|         | PE1 |
|99|         | VSS |
|00| +V      | VDD |


</details>

**J4** - MCU Config

|   # |    1 |        2 |          3|    4|      5|      6|7|
|:---:|:----:|:--------:|:---------:|:---:|:-----:|:-----:|-|
| Con |      |       69 |        68 | GND |    76 |    72 | |
| todo|      |USART1_RX | USART1_TX |     | SWCLK | SWDIO | |

