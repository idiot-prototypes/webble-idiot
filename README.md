# webble-idiot

webble-idiot is a [Web Bluetooth] experiment hosted on [GitHub Pages] and based
on the [Google Chrome Samples].

## WI-FI PROVISIONING OVER BLE

This Web Application intends to implement the *Wi-Fi Scan and Connect Services*
as defined in the [Atmel ATWINC3400 User Guide].

By "intends", it means the document suffers from a lack of specifications.

_Note:_ [webble-idiot] is tested against the *Wi-Fi Scan and Connect Services*
implemented in the sample [peripheral_wifi] from the Zephyr's module
[esperimentative-idiot].

### MISSING UUID

The document says the following for the services:

> 3 Scan Service Declaration
>
> The Wi-Fi Scan Service shall be instantiated as a «Primary Service»
>
> The service UUID shall be set to «Wi-Fi Scan Service». The UUID value
> assigned to «Wi-Fi Scan Service» is ‘fb8c0001-d224-11e4-85a1-0002a5d5c51b’

And:

> 5 Connect Service Declaration
>
> The Wi-Fi Connect Service shall be instantiated as a «Primary Service».
> 
> The service UUID shall be set to «Wi-Fi Connect Service». The UUID value
> assigned to «Wi-Fi Connect Service» is ‘77880001-d229-11e4-8689-0002a5d5c51b’.

But, the document does not mention the *UUID* values for their corresponding
characteristics.

It was assumed the characteristics *UUIDs* follows their services *UUIDs*, by
incrementing by one the second 16 bits of the *UUIDs* (i.e. the Bytes 2 and 3).

- Service: `xxxx0001-xxxx-xxxx-xxxx-xxxxxxxxxxxx`
- Characteristic 1: `xxxx0002-xxxx-xxxx-xxxx-xxxxxxxxxxxx`
- Characteristic 2: `xxxx0003-xxxx-xxxx-xxxx-xxxxxxxxxxxx`
- ...

As a consequence, the tables at section 4 and 6 are amended to include the
characteristic's *UUID*.
 
| Characteristics name | Requirement | Mandatory properties | Optional properties | Security       | Characteristics UUID                 |
| -------------------- | ----------- | -------------------- | ------------------- | -------------- | ------------------------------------ |
| Scanning Mode        | Mandatory   | Read, Write, Notify  |                     | None           | fb8c0002-d224-11e4-85a1-0002a5d5c51b |
| AP Count             | Mandatory   | Read                 |                     | None           | fb8c0003-d224-11e4-85a1-0002a5d5c51b |
| AP Details           | Mandatory   | Read                 |                     | None           | fb8c0004-d224-11e4-85a1-0002a5d5c51b |

| Characteristics name | Requirement | Mandatory properties | Optional properties | Security       | Characteristics UUID                 |
| -------------------- | ----------- | -------------------- | ------------------- | -------------- | ------------------------------------ |
| Connection state     | Mandatory   | Read, Write, Notify  |                     | None           | 77880002-d229-11e4-8689-0002a5d5c51b |
| AP parameters        | Mandatory   | Read                 |                     | Encrypted link | 77880003-d229-11e4-8689-0002a5d5c51b |

### AP DETAILS CHARACTERISTICS

Additionally, the document says:

> 4.2.1 Characteristic Behavior
>
> The AP Count characteristic consists of a single value in the range 0-15,
> which is the number of consecutive entries in the AP Details characteristic
> (starting from entry 0) that are currently valid and populated.

And defines the *AP Details* as follow:

> 4.3.1 Characteristic Behavior
>
> The AP Details characteristic consist of a list of information, which
> describes the scan result for a single Wi-Fi access point. The following
> table describes the data structure used and the byte order transmitted.

| Field       | Length   | Meaning                                                                                 | Byte order and alignment                         |
| ----------- | -------- | --------------------------------------------------------------------------------------- | ------------------------------------------------ |
| State       | 1        | Current state of AP details entry: 0 = No details 1 = Stale details 2 = Current details | n/a                                              |
| Channel     | 1        | Wi-Fi channel number                                                                    | n/a                                              |
| Freq. band  | 1        | Frequency band: 0 = 2.4GHz 1 = 5GHz                                                     | n/a                                              |
| RSSI        | 1        | Received signal strength indication in dB                                               | n/a                                              |
| SSID Length | 1        | Number of UINT8 elements in SSID field                                                  | n/a                                              |
| SSID        | Up to 32 | The SSID of the AP detected                                                             | The first UINT8 of the SSID is transmitted first |

The document does not say much if the *AP Details* characteristic sends the
whole data structure for a single entry (i.e. 37 Bytes) or if it sends the very
bare minimum if the *SSID* is less than 32 Bytes long (i.e. 5 Bytes + length of
*SSID*).

However, the total size of the full list of *AP Details* (16 * 37 Bytes = 592
Bytes) exeeds the limit of 512 Bytes of a characteristic value.

Therefore, the *AP Count* characteristic is limited to range 0..13, and the *AP
Details* characteristic list is limited to 13 entries.

As a consequence, the section 4.2.1 is amended to reflect the range for the AP
Count characteristic's *UUID*.

> 4.2.1 Characteristic Behavior
>
> The AP Count characteristic consists of a single value in the range 0-13,
> which is the number of consecutive entries in the AP Details characteristic
> (starting from entry 0) that are currently valid and populated.

## TODO

This is the *TODO* list for the current *WIP*:

- [ ] Wi-Fi Scan Service
  - [x] Read Scanning Mode
  - [x] Write Scanning Mode
  - [ ] Notify Scanning Mode
  - [ ] Read AP Count
  - [ ] Read AP Details
- [ ] Wi-Fi Connect service
  - [ ] Read Connection State
  - [ ] Write Connection State
  - [ ] Notify Connection State
  - [ ] Read AP Parameters
  - [ ] Write AP Parameters
- [ ] Make a very simple form for the user
- [ ] Split index.html into HTML and JavaScript pieces

[Web Bluetooth]: https://web.dev/bluetooth/
[GitHub Pages]: https://pages.github.com/
[Google Chrome Samples]: https://github.com/GoogleChrome/samples/tree/gh-pages/web-bluetooth
[Atmel ATWINC3400 User Guide]: http://ww1.microchip.com/downloads/en/DeviceDoc/Atmel-42683-ATWINC3400-BLE-WiFi-Scan-and-Connect-Services-Guide_UserGuide.pdf
[webble-idiot]: https://idiot-prototypes.github.io/webble-idiot/
[esperimentative-idiot]: https://idiot-prototypes.github.io/esperimentative-idiot/
[peripheral_wifi]: https://github.com/idiot-prototypes/esperimentative-idiot/blob/master/samples/bluetooth/peripheral_wifi/
