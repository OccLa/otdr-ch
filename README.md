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

| #| Name | Con | | #| Name    | Con    | | #| Name | Con  | | #| Name | Con   |
|--|-----|------|-|--|---------|--------|-|--|-----|-------|-|--|-----|--------|
| 1| Pwr |      | |13| adc_clk | ADC-15 | |25| Pwr |       | |37| Pwr |        |
| 2| Gnd |      | |14| adc_d[9]| ADC-12 | |26| Gnd |       | |38|     | MCU-67 |
| 3| TCK | J9-1 | |15| adc_d[8]| ADC-11 | |27| ld1 |   LD1 | |39|     | MCU-64 |
| 4| TMS | J9-2 | |16| adc_d[7]| ADC-10 | |28|     | MCU-96| |40|     |        |
| 5| TDI | J9-3 | |17| adc_d[6]|  ADC-9 | |29|     | MCU-95| |41|     |        |
| 6|     |      | |18| adc_d[5]|  ADC-8 | |30|     | MCU-93| |42|     |        |
| 7| TDO | J9-4 | |19| adc_d[4]|  ADC-7 | |31|     | MCU-92| |43|     |        |
| 8|     |      | |20| adc_d[3]|  ADC-6 | |32|     | MCU-91| |44|     |        |
| 9|     |      | |21| adc_d[2]|  ADC-5 | |33|     |       | |45|     |        |
|10|     |      | |22| adc_d[1]|  ADC-4 | |34|     | MCU-90| |46|     |        |
|11|     |      | |23| adc_d[0]|  ADC-3 | |35|     | MCU-89| |47|     |        |
|12| Pwr |      | |24|     ld2 |    LD2 | |36| Pwr |       | |48|     |        |

</details>

**J9** - FPGA JTAG

| #    | 1   |    2|    3|    4|   5|    6|  7|
|-----:|-----|-----|-----|-----|----|-----|---|
| Name | TCK | TMS | TDI | TDO | +V | GND | - |


### MCU
Looks like an STM32 clone from GigaDevice.

<details>
<summary>MCU pinout</summary>

| #| Con        |      | | #| Con  |      | | #| Con     |        | | #| Con  |        | 
|--|------------|------|-|--|------|------|-|--|---------|--------|-|--|------|--------|
| 1|            |  PE2 | |26|Reboot|  PA3 | |51|         |   PB12 | |76| J4-5 |  SWCLK/PA14 |
| 2|            |  PE3 | |27| GND  |  VSS | |52|         |   PB13 | |77|         | PA15 |
| 3|            |  PE4 | |28|      |  VDD | |53|         |   PB14 | |78|         | PC10 |
| 4|            |  PE5 | |29|      |  PA4 | |54|         |   PB15 | |79|         | PC11 |
| 5|            |  PE6 | |30|      |  PA5 | |55|         |    PD8 | |80|         | PC12 |
| 6|            | VBAT | |31|      |  PA6 | |56|         |    PD9 | |81|         | PD0 |
| 7|            | PC13 | |32|      |  PA7 | |57|         |   PD10 | |82|         | PD1 |
| 8| 32KHz  | OSC32_IN | |33|      |  PC4 | |58|         |   PD11 | |83|         | PD2 |
| 9| 32KHz | OSC32_OUT | |34|      |  PC5 | |59|         |   PD12 | |84|         | PD3 |
|10| GND        |  VSS | |35|      |  PB0 | |60|         |   PD13 | |85|         | PD4 |
|11|            |  VDD | |36|      |  PB1 | |61|         |   PD14 | |86|         | PD5 |
|12| 25MHz    | OSC_IN | |37|      | BOOT1| |62|         |   PD15 | |87|         | PD6 |
|13| 25MHz   | OSC_OUT | |38|      |  PE7 | |63|         |    PC6 | |88|         | PD7 |
|14| R70,C36,S1 | NRST | |39|      |  PE8 | |64| FPGA-39 |    PC7 | |89| FPGA-35 | PB3 |
|15|            |  PC0 | |40|      |  PE9 | |65|         |    PC8 | |90| FPGA-34 | PB4 |
|16|            |  PC1 | |41|      | PE10 | |66|         |    PC9 | |91| FPGA-32 | PB5 |
|17|            |  PC2 | |42|      | PE11 | |67| FPGA-38 |    PA8 | |92| FPGA-31 | PB6 |
|18|            |  PC3 | |43|      | PE12 | |68| J4-3    |  UA1_TX| |93| FPGA-30 | PB7 |
|19|            |  VDD | |44|      | PE13 | |69| J4-2    |  UA1_RX| |94|         | BOOT0 |
|20|            | VSSA | |45|      | PE14 | |70|         |   PA11 | |95| FPGA-29 | PB8 |
|21|            | VREF+| |46|      | PE15 | |71|         |   PA12 | |96| FPGA-28 | PB9 |
|22|            | VDDA | |47|      | PB10 | |72| J4-6 | SWDIO/PA13| |97|         | PE0 |
|23|          |PA0/WKUP| |48|      | PB11 | |73|         | VCAP_2 | |98|         | PE1 |
|24|            |  PA1 | |49|      |VCAP_1| |74|         |    VSS | |99|         | VSS |
|25|            |  PA2 | |50|      |  VDD | |75| +V      |    VDD | |00| +V      | VDD |

</details>

**J4** - MCU Config

|   # |    1 |        2 |          3|    4|      5|      6|7|
|:---:|:----:|:--------:|:---------:|:---:|:-----:|:-----:|-|
| Con |      |       69 |        68 | GND |    76 |    72 | |
| todo|      |USART1_RX | USART1_TX |     | SWCLK | SWDIO | |

