## Reverse engineering the cheapest OTDRs from China (Tynish TN1700).

Available on Aliexpress for around $100.<br>
https://www.google.com/search?q=TN1700+site%3Aaliexpress.com

![PCB](img/TN1700_pcb_2.png "PCB TN1700")

### APD(avalanche photodiode) power supply
https://www.sg-micro.com/product/SGM41282C<br>
https://www.sg-micro.com/rect/assets/7a6f5550-29b0-4f92-9198-7deb495df53e/SGM41282C.pdf<br>

- **EN** - SGM41282C pin #2 -> (R34(1K) & R35(100K)->GND) -> FPGA pin #6
- **VS** - SGM41282C pin #4 -> (R33(8K) & R32(20K)->GND) -> MCU pin #29(DAC_OUT1)
- **SVMON** - SGM41282C pin #5 -> T4
- **TH** - SGM41282C pin #5 -> T5
- **THXOR** - SGM41282C pin #5 -> T3

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

|**#**|**Name**   |**Con** |
|--|-------------:|--------|
| 1|        VCCIO0|    Pwr |
| 2|          VSS |    Gnd |
| 3|          TCK |   J9-1 |
| 4|          TMS |   J9-2 |
| 5|          TDI |   J9-3 |
| 6|   IOT9A/SCLK |APD-PWR-EN|
| 7|          TDO |   J9-4 |
| 8|      **PB0** | MCU-35 |
| 9|      **PB1** | MCU-36 |
|10|      **PB2** | MCU-37 |
|11|              | MCU-45 |
|12|          VCC |    Pwr |
|**#**|**---------------**|**--------**|
|13|  **adc_clk** | ADC-15 |
|14| **adc_d[9]** | ADC-12 |
|15| **adc_d[8]** | ADC-11 |
|16| **adc_d[7]** | ADC-10 |
|17| **adc_d[6]** |  ADC-9 |
|18| **adc_d[5]** |  ADC-8 |
|19| **adc_d[4]** |  ADC-7 |
|20| **adc_d[3]** |  ADC-6 |
|21| **adc_d[2]** |  ADC-5 |
|22| **adc_d[1]** |  ADC-4 |
|23| **adc_d[0]** |  ADC-3 |
|24|      **ld2** |    LD2 |
|**#**|**---------------**|**--------**|
|25|       VCCIO1 |    Pwr |
|26|          VSS |    Gnd |
|27|      **ld1** |    LD1 |
|28|      **PB9** | MCU-96 |
|29|      **PB8** | MCU-95 |
|30|      **PB7** | MCU-93 |
|31|      **PB6** | MCU-92 |
|32|      **PB5** | MCU-91 |
|33| GCLKT_2/RPLL_T_in | 100Mhz |
|34|      **PB4** | MCU-90 |
|35|      **PB3** | MCU-89 |
|36|         VCCX |    Pwr |
|**#**|**---------------**|**--------**|
|37|          VCC |             Pwr |
|38|              |          MCU-67 |
|39|              |          MCU-64 |
|40|              |          MCU-59 |
|41|              |          MCU-46 |
|42|        MODE1 |       R149->GND |
|43|        MODE0 |       R146->GND |
|44|    JTAGSEL_N |       R153->GND |
|45|        READY |        R166->+V |
|46|         DONE | R142,R143 to D1 |
|47|      GCLKT_0 |                 |
|48|   RECONFIG_N |        R151->+V |

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
| 1|     OPM S1 |  PE2 | 
| 2|     OPM S2 |  PE3 | 
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
|16|   OPM R101 |  PC1 | 
|17|OPM R135 U23|  PC2 | 
|18| OPM R84 Q9 |  PC3 | 
|19|            |  VDD | 
|20|            | VSSA | 
|21|            | VREF+ | 
|22|            | VDDA | 
|23| OPM Q9 inA | PA0/WKUP | 
|24|            |  PA1 | 
|25|            |  PA2 | 
|**#**|**Con**|**------------**|
|26| Reboot |  PA3 |
|27|  GND   |  VSS |
|28|        |  VDD |
|29|APD-PWR-VS|  PA4/DAC_OUT1 |
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
|45| FPGA-11| PE14 |
|46| FPGA-41| PE15 |
|47| Touch-SCL | PB10 |
|48| Touch-SDA | PB11 |
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
|58| Touch-INT |   PD11 |
|59|   FPGA-40 |   PD12 |
|60|     CS_U9 |   PD13 |
|61|           |   PD14 |
|62|           |   PD15 |
|63| Touch-RST |    PC6 |
|64|   FPGA-39 |    PC7 |
|65|      SD-7 | PC8/SDIO_D0 |
|66|      SD-8 | PC9/SDIO_D1 |
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
|78|    SD-1 | PC10/SDIO_D2 |
|79|    SD-2 | PC11/SDIO_D3 |
|80|    SD-5 | PC12/SDIO_CK |
|81|         | PD0 |
|82|         | PD1 |
|83|    SD-3 | PD2/SDIO_CMD |
|84|    SD-9 | PD3 |
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
|97| LightLED| PE0 |
|98|  OPM S0 | PE1 |
|99|         | VSS |
|00| +V      | VDD |


</details>

**J4** - MCU Config

|   # |    1 |        2 |          3|    4|      5|      6|7|
|:---:|:----:|:--------:|:---------:|:---:|:-----:|:-----:|-|
| Con |      |       69 |        68 | GND |    76 |    72 | |
| todo|      |USART1_RX | USART1_TX |     | SWCLK | SWDIO | |


**J2** - micro SD slot
|         # |       1 |       2 |         3|   4|        5|    6|        7|        8|    9|
|:---------:|:-------:|:-------:|:--------:|:--:|:-------:|:---:|:-------:|:-------:|:---:|
|      Name |      D2 |      D3 |      CMD | +V |     CLK | GND |      D0 |      D1 | Det |
| MCU pin # |      78 |      79 |       83 |    |      80 |     |      65 |      66 |  84 |
|  MCU Name | SDIO_D2 | SDIO_D3 | SDIO_CMD |    | SDIO_CK |     | SDIO_D0 | SDIO_D1 | PD3 |


**J3** - GT911 5-Point Capacitive Touch Controller
|         # |        1 |        2 |    3|        4|        5|    6|
|:---------:|:--------:|:--------:|:---:|:-------:|:-------:|:---:|
|      Name |      SCL |      SDA | GND |     RST |     INT | VDD |
| MCU pin # |       47 |       48 |     |      63 |      58 |     |
|  MCU Name | I2C2_SCL | I2C2_SDA |     |     PC6 |    PD11 |     |
