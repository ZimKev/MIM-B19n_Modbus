# MIM-B19n_Modbus
Samsung Heatpump register read/write via Modbus (MIM-B19N) Module

The Samsung MIM-B19N module can be used to communicate with Climate systems by Samsung. In this expample a Samsung HT Quiet Heatpump AE080BXYDEG/EU (outdoor unit) + MIM-E03CN (Controll Box) + MWR-WW10N (Touch Controller) was used.

Module can be installed in outdoor unit, see MIM-b19n.jpg

Installation, see Installation Guide: https://www.samsung.com/uk/support/model/MIM-B19N/

How to access with Homeassistant:
1) Create a link in your configuration.yaml and crate new file "modbus.yaml"
```
modbus: !include modbus.yaml
```
2) Configuration of the RS485 interface in modbus.yaml
```
  - name: "samsung"
    type: serial
    port: /dev/ttyUSB0
    baudrate: 9600
    bytesize: 8
    method: rtu
    parity: E
    stopbits: 1
    close_comm_on_error: true
    delay: 0
    message_wait_milliseconds: 30
    retries: 3
    retry_on_empty: false
    timeout: 5
```
3) Set up the Communication elements, example therefore see modbus.yaml or https://www.home-assistant.io/integrations/modbus/
With this configuration standart registers describten in the MIM-b19n are added.

-----Experimental Features!-----

Disclaimer - I can not guarantee that by doing things wrong the content of storage in Indoor or Outdoor unit is damaged. Please be carefull. This page will be updated frequently to share more informations.

In the Installation manual I got with my MIM-19n (could not find this version in the internet yet) are the following registers decribted:
- Register 1 to 4 - Indoor Unit Funktions
- Register 5 to 50 - not describted
- Register 51 - 73 - Outdoor Unit Funktions
- Register 83 - 99? - not describted

When reading the discription, than there is the information that by writing a MassageSet ID to registers starting at 6000 or 7000 you can add new funktions to the Modbus.
This "MassageSet IDs" can be found in NASA.ptc file. (I am still exporting, I found them in the Installation folder from SNET-PRO and not all are relevant for all Devices.

When e.g. the MassageSet ID 0x8238 is written to adress 6000, than the register 5 shows the Current operating frequency of the compressor 
```
<Message ProtocolID ="VAR_out_control_cfreq_comp1" Index="8238">
```

To write the MassageSet IDs to the Interface via Home Assistant the following Service can be used. (Just an example of tested registers)
If there are multiple functions to add, they must be written all at once using command 16 (Write multiple holding registers)

Add indoor unit funktions:
```
service: modbus.write_register
data:
  hub: samsung
  address: 7000
  value: [0x411E, 0x42D7, 0x42D6, 0x4087, 0x406c, 0x42E9, 0x42F1, 0x4067, 0x428C]
  slave: 2
```
Add outdoor unit Funktions:
```
service: modbus.write_register
data:
  hub: samsung
  address: 6000
  value: [0x8238, 0x8204, 0x823D]
  slave: 2
```

Thats for now, page will be updated from time to time.
Maybe Youtube video following
Should not be only work at Home assistant.
MIM-19N(T) Installation guid has more information inside, if I can not find the version I have in printed format in the www I will scan it and upload it here.

