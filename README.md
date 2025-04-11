# TSC_E-BookReader

## Diagrama bloc:
![diagram](./Images/BlockDiagram.png)

## Pasi de implementare:

- Am creat **schema electrica** pe baza celei atasate in cerinta, folosind biblioteca `DeskAssistant`
- Am creat apoi **PCB**-ul:
    - am modificat dimensiunile PCB-ului pentru a corespunde cu cele descrise in cerinta
    -  am plasat componentele in functie de schema electrica, grupandu-le pe baza 'functionalitatii'
    -  am decupat PCB-ul conform fisierului atasat in enunt
    -  am modificat denumirile *test-pad-urilor* - *TP* in functie de semnalele pe care le poarta
    -  am facut autorutare cu dimensiunile traseelor de alimentare precizate in enunt (`VBUS`, `VUSB`, `VBAT`, `3V3`, `EPD_3V3` si `EPD_3V3_C` cu *CopperWidth* de minimum **0.3 mm**, iar restul cu *CopperWidth* de minimum **0.15 mm**)
    -  am trasat planurile de masa (*top* si *bottom*)
- Am introdus modelele **3D**:
    - am descarcat modelele 3D corespunzatoare de pe [Component Search Engine](https://componentsearchengine.com/)
    - am editat fiecare componenta, adaugandu-i modelul 3D pe care l-am pozitionat conform *pinilor* / *footprint-ului*
- Am desenat modelele **3D** pentru *baterie*, *ecran* si *test-pad-uri* tinand cont de dimensiuni
- Am adaugat toate piesele (*placuta*, *ecranul*, *bateria* si *carcasa*) obtinand produsul final



## Descriere functionalitate hardware:

### 1. Alimentare și Protecție (USB-C & ESD)
- **Conector USB-C**: permite alimentarea plăcii și comunicarea de date.
- **PFMF.050.1**, **TVS diode (D1)**: oferă protecție la supratensiuni și descărcări electrostatice.
- **IC1**: convertește tensiunea de intrare (5V) la 3.3V, necesară pentru alimentarea circuitelor logice (ESP32, senzori, etc.).

### 2. Control Alimentare Baterie
- **MCP73831** este un controler de încărcare pentru baterii Li-Po.
- **C1, C2** filtrează zgomotele pe linia de alimentare.
- Permite alimentarea sistemului din baterie reîncărcabilă și oferă protecție la supraîncărcare/descărcare.

### 3. Nivel Încărcare Baterie
- **MAX17048** măsoară nivelul de încărcare al bateriei.
- Comunicarea se face prin magistrala I2C (SCL/SDA), comună cu alți senzori.
- Permite afișarea nivelului bateriei pe ecran sau transmiterea sa prin rețea.

### 4. Afișaj E-Paper
- **Header E-Paper** conectează un afișaj de tip E-Ink.
- **Driverul de semnal (MBR0530, L1, Q3)** controlează alimentarea și semnalele de comutare pentru afișaj.
- Selectarea tipului de ecran se face cu jumperul **S1**.
- Afișajul e-paper este ideal pentru consum redus de energie.

### 5. Modul RTC (Ceas în timp real)
- **DS3231SN** menține ora exactă, chiar și când placa este oprită.
- Funcționează pe I2C și are o baterie de rezervă.

### 6. Senzor Ambiental (BME688)
- Măsoară temperatură, umiditate, presiune și calitatea aerului.
- Se conectează la ESP32 prin I2C.

### 7. Memorie NOR Flash externă (64MB)
- **W25Q512JVEIQ** este o memorie SPI folosită pentru stocare extinsă (ex: cărți, imagini, date).
- Conectată la ESP32 pe interfață SPI (IO4–IO7).
- Permite stocarea persistentă a fișierelor.

### 8. Cititor Card SD
- Slot SD conectat prin SPI (IO8–IO11).
- Permite acces la fișiere externe, jurnalizare sau încărcare de date din card.

### 9. ESP32-C6 - Microcontroller Principal
- Conectează toate perifericele și controlează logica sistemului.
- Suportă Wi-Fi 6, BLE 5.2 și Zigbee/Thread.
- Are GPIO configurate pentru SPI, I2C, UART și control E-Paper.
- Este nucleul care rulează software-ul aplicației.

### 10. Protecții ESD pe SPI & Qwiic
- Diodă **TVS** și rezistențe pe liniile SPI/Qwiic pentru protecție la ESD.
- Asigură durabilitate mai mare în aplicații reale.

### 11. Butoane - Reset & Boot
- Buton **RESET**: Repornește ESP32.
- Buton **BOOT**: Permite intrarea în mod de boot pentru programare.
- Ambele conectate la pini critici (EN, IO0).

### 12. Interfață Qwiic / Stemma QT
- Conector I2C standard (JST-SH 4 pini).
- Permite adăugarea rapidă de senzori sau module adiționale compatibile.

### 13. Test Pads
- Paduri expuse pentru GPIO-uri esențiale.
- Permit depanarea, măsurători și extinderea funcționalității.

---

## Descrierea pinilor ESP32-C6:

| Pin ESP32-C6 | Nume Semnal     | Componentă Asociată                   | Scop / Funcție                                           |
|--------------|------------------|----------------------------------------|-----------------------------------------------------------|
| GND          | GND              | Toate componentele                     | Masă comună pentru alimentare și semnal.                  |
| EN           | Enable           | Buton Reset                            | Resetează microcontrollerul la apăsare.                   |
| IO0          | GPIO0            | Buton BOOT                             | Intrare în modul de boot la apăsare.                      |
| IO1          | GPIO1            | Test Pad TP11                          | Test/debug sau I/O general.                               |
| IO2          | GPIO2            | Test Pad TP12                          | Test/debug sau I/O general.                               |
| IO3          | GPIO3            | Test Pad TP13                          | Test/debug sau I/O general.                               |
| IO4          | GPIO4            | SPI Flash (CS)                         | Chip Select pentru memoria NOR Flash externă.             |
| IO5          | GPIO5            | SPI Flash (CLK)                        | Clock SPI pentru memoria NOR Flash externă.               |
| IO6          | GPIO6            | SPI Flash (MISO)                       | Data Out din NOR Flash spre ESP32.                        |
| IO7          | GPIO7            | SPI Flash (MOSI)                       | Data In către NOR Flash de la ESP32.                      |
| IO8          | GPIO8            | SD Card (CS)                           | Chip Select pentru card SD.                               |
| IO9          | GPIO9            | SD Card (CLK)                          | Clock SPI pentru card SD.                                 |
| IO10         | GPIO10           | SD Card (MISO)                         | Date de pe card SD către ESP32.                           |
| IO11         | GPIO11           | SD Card (MOSI)                         | Date de la ESP32 către card SD.                           |
| IO12         | GPIO12           | E-Paper Header                         | Semnal pentru ecran E-Paper (ex: BUSY, RST etc.).         |
| IO13         | GPIO13           | E-Paper Header                         | Semnal pentru ecran E-Paper.                              |
| IO14         | GPIO14           | E-Paper Header                         | Semnal pentru ecran E-Paper.                              |
| IO15         | GPIO15           | E-Paper Header                         | Semnal pentru ecran E-Paper.                              |
| IO16         | GPIO16           | SCL (RTC & BME688)                     | I2C Clock comun pentru RTC și senzor de mediu.            |
| IO17         | GPIO17           | SDA (RTC & BME688)                     | I2C Data comun pentru RTC și senzor de mediu.             |
| IO18         | GPIO18           | UART_RX (Qwiic/Debug)                  | Recepție serială (debug sau Qwiic/StemMA).                |
| IO19         | GPIO19           | UART_TX (Qwiic/Debug)                  | Transmisie serială (debug sau Qwiic/StemMA).              |

## Bill of Materials (BOM):

| Componenta |        Link      |               Datasheet                |
|------------|------------------|----------------------------------------|
| ESP32-C6-WROOM-1-N8 | [Link](https://ro.mouser.com/ProductDetail/Espressif-Systems/ESP32-C6-WROOM-1-N8?qs=8Wlm6%252BaMh8ST02Gmwp74cw%3D%3D) | [Datasheet](https://ro.mouser.com/datasheet/2/891/Espressif_ESP32_C6_WROOM_1__Datasheet_V0_1_PRELIMI-3239987.pdf) |
| 112A-TAAR-R03_ATTEND | [Link](https://www.digikey.ro/ro/products/detail/attend-technology/112A-TAAR-R03/17633923) | [Datasheet](https://www.attend.com.tw/data/download/file/112A-TAAR-R03_Spec.pdf) |
| ADAFRUIT_LEDCHIP-LED0603 | [Link](https://www.snapeda.com/parts/KP-1608SURCK/Kingbright/view-part/?ref=search&t=LED%200603) | [Datasheet](https://www.snapeda.com/parts/KP-1608SURCK/Kingbright/datasheet/) |
| ESP32_WROVER_EAGLE-LTSPICE_RR0402 | [Link](https://www.snapeda.com/parts/RC0402FR-07226RL/Yageo/view-part/) | [Datasheet](https://www.snapeda.com/parts/RC0402FR-07226RL/Yageo/datasheet/) |
| ESP32_WROVER_EAGLE-LTSPICE_CC0402 | [Link](https://componentsearchengine.com/part-view/CC0402MRX5R5BB106/YAGEO) | [Datasheet](https://componentsearchengine.com/Datasheets/2/CC0402MRX5R5BB106.pdf) |
| RCL_CPOL-EUCT3528 | [Link](https://ro.mouser.com/ProductDetail/Vishay-Sprague/TR3B106K025C1300?qs=jCGqFXxTmLdffnuDkXzk1g%3D%3D) | [Datasheet](https://www.vishay.com/docs/40080/tr3.pdf) |
| ESP32_WROVER_SPARKFUN-DISCRETESEMI_MOSFET_PCH-DMG2305UX-7 | [Link](https://ro.mouser.com/ProductDetail/Diodes-Incorporated/DMG2305UX-7?qs=L1DZKBg7t5F%2FNBHrjfxC%252Bg%3D%3D) | [Datasheet](https://www.diodes.com/assets/Datasheets/DMG2305UX.pdf) |
| 744043680IND_4828-WE-TPC_WRE | [Link](https://www.digikey.sg/en/products/detail/w%C3%BCrth-elektronik/744043680/1638515) | [Datasheet](https://www.we-online.com/components/products/datasheet/744043680.pdf) |
| BD5229G-TR | [Link](https://ro.mouser.com/ProductDetail/ROHM-Semiconductor/BD5229G-TR?qs=4kLU8WoGk0vvnhrrYwdszw%3D%3D) | [Datasheet](https://fscdn.rohm.com/en/products/databook/datasheet/ic/power/voltage_detector/bd52xxg-e.pdf) |
| ESP32_WROVER_BME680_BME680 | [Link](https://ro.mouser.com/ProductDetail/Bosch-Sensortec/BME680?qs=v271MhAjFHjo0yA%2FC4OnDQ%3D%3D) | [Datasheet](https://ro.mouser.com/datasheet/2/783/BST_BME680_DS001-1509608.pdf) |
| BUTTON_CUSYOMV1 | [Link](https://ro.mouser.com/ProductDetail/Panasonic/EVQ-P7M01P?qs=rJ%252BziJWpysz0sJAzuCybtw%3D%3D) | [Datasheet](https://industry.panasonic.com/global/en/downloads/?file_cd=402906&tab=catalog&series_cd=3457) |
| CPH3225A | [Link](https://ro.mouser.com/ProductDetail/Seiko-Semiconductors/CPH3225A?qs=3etwrb1wR%252BhUOph6lAO7eg%3D%3D) | [Datasheet](https://ro.mouser.com/datasheet/2/360/Seiko_Instruments_MicroBattery_E_20230330_2024Jan_-3561061.pdf) |
| DS3231SN# | [Link](https://ro.mouser.com/ProductDetail/Analog-Devices-Maxim-Integrated/DS3231SN?qs=1eQvB6Dk1vhUlr8%2FOrV0Fw%3D%3D) | [Datasheet](https://ro.mouser.com/datasheet/2/609/DS3231-3421123.pdf) |
| ESP32C6_VARISTORCN1812 | [Link](https://eu.mouser.com/ProductDetail/KEMET/VC1812L501R008?qs=jj7GbYRQuOa1j1%2F3gPpdvQ%3D%3D) | [Datasheet](https://eu.mouser.com/datasheet/2/447/KEM_V0002_VC-3316862.pdf) |
| ESP32_WROVER_AVX---SD0805S020S1R0_AVX_SD0805S020S1R0_0_0AVX_SD0805S020S1R0_0_0 | [Link](https://eu.mouser.com/ProductDetail/KYOCERA-AVX/SD0805S020S1R0?qs=jCA%252BPfw4LHbpkAoSnwrdjw%3D%3D&utm_id=6470900573&utm_source=google&utm_medium=cpc&utm_marketing_tactic=emeacorp&gad_source=1&gbraid=0AAAAADn_wf2CZaBvMXoqDoLB8a6wI1EC_&gclid=CjwKCAjw--K_BhB5EiwAuwYoyhyp4YPMQJxEs9O7Um1pbb_qrwD9UbwWFHYqYH_KvU5-nXs4CTfFVRoCY7kQAvD_BwE) | [Datasheet](https://eu.mouser.com/datasheet/2/40/schottky-3165252.pdf) |
| FH34SRJ-24S-0.5SH_99_ | [Link](https://eu.mouser.com/ProductDetail/Hirose-Connector/FH34SRJ-24S-05SH99?qs=vcbW%252B4%252BSTIpKBl5ap9J8Fw%3D%3D&utm_id=6470900573&utm_source=google&utm_medium=cpc&utm_marketing_tactic=emeacorp&gad_source=1&gbraid=0AAAAADn_wf2CZaBvMXoqDoLB8a6wI1EC_&gclid=CjwKCAjw--K_BhB5EiwAuwYoykKExbMK_Nf7-m2eYAFcWwWt_BaAKmDd5HmJ3DzOwus-MUIZVeL9ExoCTtAQAvD_BwE) | [Datasheet](https://eu.mouser.com/datasheet/2/185/FH34SRJ_24S_0_5SH_99__CL0580_1255_6_99_2DDrawing_0-1615044.pdf) |
| MAX17048G+T10 | [Link](https://eu.mouser.com/ProductDetail/Analog-Devices-Maxim-Integrated/MAX17048G+T10?qs=D7PJwyCwLAoGnnn8jEPRBQ%3D%3D&utm_id=20109199409&utm_source=google&utm_medium=cpc&utm_marketing_tactic=emeacorp&gad_source=1&gbraid=0AAAAADn_wf23I7VSL8Khkvz-kjEhjYqPz&gclid=CjwKCAjw--K_BhB5EiwAuwYoytVFz576awyE1ug3GUFRWVeGavv1g_gzDMEDxxjVwp1JIOS60JmKnhoC-zIQAvD_BwE) | [Datasheet](https://eu.mouser.com/datasheet/2/609/MAX17048_MAX17049-3469099.pdf) |
| MBR0530 | [Link](https://eu.mouser.com/ProductDetail/Micro-Commercial-Components-MCC/MBR0530-TP?qs=KFo7JewZbUECRHkxGanrdg%3D%3D&utm_id=6470900573&utm_source=google&utm_medium=cpc&utm_marketing_tactic=emeacorp&gad_source=1&gbraid=0AAAAADn_wf2CZaBvMXoqDoLB8a6wI1EC_&gclid=CjwKCAjw--K_BhB5EiwAuwYoysNC_AmHFOx6dFFFiTr86y8jRKC674aUhl0na6xIgHM9UWT_XyhG0RoCYwIQAvD_BwE) | [Datasheet](https://eu.mouser.com/datasheet/2/258/MBR0520_MBR0580_SOD123_-2492194.pdf) |
| ESP32_WROVER_SPARKFUN-IC-POWER_MCP73831 | [Link](https://ro.mouser.com/ProductDetail/Microchip-Technology/MCP73831-2DCI-MC?qs=hH%252BOa0VZEiBQ%2FrptDRXKdg%3D%3D) | [Datasheet](https://ro.mouser.com/datasheet/2/268/MCP73831_Family_Data_Sheet_DS20001984H-3441711.pdf) |
| PGB1010603MR | [Link](https://ro.mouser.com/ProductDetail/Littelfuse/PGB1010603MR?qs=gu7KAQ731URLg4GSnNNN7Q%3D%3D) | [Datasheet](https://www.littelfuse.com/assetdocs/pulseguard-esd-suppressors-pgb1-datasheet?assetguid=8a337998-d54d-466b-be4e-dc5bcd1f9321) |
| QWIIC_CONNECTORJS-1MM | [Link](https://www.sparkfun.com/qwiic-jst-connector-smd-4-pin-vertical.html) | [Datasheet](https://www.jst-mfg.com/product/pdf/eng/eSH.pdf?5ec58813d214e) |
| SAMACSYS_PARTS_USB4110-GF-A | [Link](https://eu.mouser.com/ProductDetail/GCT/USB4110-GF-A?qs=KUoIvG%2F9IlYiZvIXQjyJeA%3D%3D&utm_id=6470900573&utm_source=google&utm_medium=cpc&utm_marketing_tactic=emeacorp&gad_source=1&gbraid=0AAAAADn_wf2CZaBvMXoqDoLB8a6wI1EC_&gclid=CjwKCAjw--K_BhB5EiwAuwYoyqoFYku-1KEnaVMBZNhraqam0ThMUfxlrF7A4GQ8Pqj9d6zLqr7juBoCrOEQAvD_BwE) | [Datasheet](https://eu.mouser.com/datasheet/2/837/GCT_USB4110_Product_Drawing___20k_cycles-3455479.pdf) |
| SI1308EDL-T1-GE3 | [Link](https://eu.mouser.com/ProductDetail/Vishay-Semiconductors/SI1308EDL-T1-GE3?qs=bX1%252BNvsK%2FBramh9tgpOaEw%3D%3D&utm_id=20109199409&utm_source=google&utm_medium=cpc&utm_marketing_tactic=emeacorp&gad_source=1&gbraid=0AAAAADn_wf23I7VSL8Khkvz-kjEhjYqPz&gclid=CjwKCAjw--K_BhB5EiwAuwYoyllRgbebqlW1QDUb9SQpEls061aMn76tZxer07jsniKXNOsDESHnSBoCkL8QAvD_BwE) | [Datasheet](https://www.vishay.com/docs/63399/si1308edl.pdf) |
| USBLC6-2SC6Y | [Link](https://eu.mouser.com/ProductDetail/STMicroelectronics/USBLC6-2SC6Y?qs=gNDSiZmRJS%2FOgDexvXkdow%3D%3D&srsltid=AfmBOoqlX0fgvKlHMd5oyRC9Sr6hBsf51Zi0RKmaIGejNRz6zsEP_LgY) | [Datasheet](https://eu.mouser.com/datasheet/2/389/usblc6_2sc6y-1852505.pdf) |
| SJ | [Link](https://grabcad.com/library/solder-jumpers-1) | [Datasheet]() |
| W25Q512JVEIQ | [Link](https://eu.mouser.com/ProductDetail/Winbond/W25Q512JVEIQ?qs=l7cgNqFNU1jw6svr3at6tA%3D%3D&srsltid=AfmBOopwp-NQhQ6JM3w-WMYdzldr5QNIm_7CdLwVaaaS66y9swf4PMgu) | [Datasheet](https://eu.mouser.com/datasheet/2/949/Winbond_W25Q512JV_Datasheet-3240039.pdf) |
| XC6220A331MR-G | [Link](https://eu.mouser.com/ProductDetail/Torex-Semiconductor/XC6220A331MR-G?qs=AsjdqWjXhJ8ZSWznL1J0gg%3D%3D&srsltid=AfmBOopNlEzK7MdHRFabPSVSf32rb8GhsgKYTsuSlo8EjEXWvzOc5IIo) | [Datasheet](https://eu.mouser.com/datasheet/2/760/xc6220-3371556.pdf) |

## Probleme:

- Am aprobat 2 erori tip *SMD-Hole* la rutarea *PCB-ului*
- Am aprobat erorile de tip *part X has no value* sau *pin X connected to Y* ce apareau dupa rularea *ERC-ului* in schematic
- Pentru *Test-pad-uri* am creat eu un model 3D cilindric, deoarece nu am gasit model pe internet

