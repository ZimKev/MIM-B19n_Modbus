# MIM-B19n_Modbus
Samsung Heat pump register read/write via Modbus (MIM-B19N) Module

The Samsung MIM-B19N module can be used to communicate with Climate systems by Samsung. In this example, a Samsung HT Quiet Heat pump AE080BXYDEG/EU (outdoor unit) + MIM-E03CN/EU (Control Box) + MWR-WW10N (Touch Controller) was used.

Module can be installed in outdoor unit, see MIM-b19n.jpg

Installation, see Installation Guide: https://www.samsung.com/uk/support/model/MIM-B19N/

How to access with Home Assistant:
1) Create a link in your configuration.yaml and create new file "modbus.yaml"
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
With this configuration some standard registers, describten in the MIM-b19n, are added.

-----Experimental Features!-----

Disclaimer - I can not guarantee that by doing things wrong, the Indoor or Outdoor unit is damaged. Please be careful. This page will be updated frequently to share more information.

In the Installation manual I got with my MIM-19n (could not find this version at the internet yet) are the following registers described:
- Register 1 to 4 - Indoor Unit functions
- Register 5 to 50 - not describted
- Register 51 - 73 - Outdoor Unit functions
- Register 83 - 99? - not describted

When reading the description, there is the information that by writing a MassageSet ID to registers starting at 6000 or 7000 you can add new functions to the Modbus.
This "MassageSet IDs" can be found in NASA.ptc file. (I am still exporting, I found them in the Installation folder from SNET-PRO and not all are relevant for all Devices.

When e.g. the MassageSet ID 0x8238 is written to adress 6000, then the register 5 shows the Current operating frequency of the compressor 
```
<Message ProtocolID ="VAR_out_control_cfreq_comp1" Index="8238">
```

To write the MassageSet IDs to the Interface via Home Assistant, the following Service can be used. (Just an example of tested registers)
If there are multiple functions to add, they must be written all at once using command 16 (Write multiple holding registers)

Add indoor unit funktions:
```
service: modbus.write_register
data:
  hub: samsung
  address: 7000
  value: 
  - 0x4067 #82 3 way valve position
  - 0x406C #83 Backup_Heater read-only
  - 0x4087 #84 Booster
  - 0x40C4 #85 PWM Water Pump
  - 0x411E #86 Zone_2 ON/OFF
  - 0x4203 #87 Controller Room Temp Sensor
  - 0x4239 #88 Heater Sensor Temp
  - 0x4247 #89 VAR_IN_TEMP_WATER_OUTLET_TARGET_F
  - 0x4248 #90 VAR_IN_TEMP_WATER_LAW_TARGET_F
  - 0x427F #91 VAR_IN_TEMP_WATER_LAW_F 
  - 0x428C #92 Mixing Valve Temp
  - 0x42CC #93 VAR_IN_MODULATING_FAN
  - 0x42D6 #94 Zone_2 Set Room temp
  - 0x42D7 #95 Zone_2 Set Water temp
  - 0x42D8 #96 VAR_IN_TEMP_WATER_OUTLET_ZONE1_F
  - 0x42D9 #97 VAR_IN_TEMP_WATER_OUTLET_ZONE2_F
  - 0x42E9 #98 Water_flow
  - 0x42F1 #99 FR controll
  slave: 2
```
Add outdoor unit Funktions:
```
service: modbus.write_register
data:
  hub: samsung
  address: 6000
  value:
  - 0x8001 #4 Test
  - 0x8001 #5 Operation Mode	
  - 0x8010 #6 Compressor 1 Status5
  - 0x8017 #7 Hot Gas 1 Status
  - 0x8019 #8 Liquid Valuve Status
  - 0x8021 #9 EVI Bypass
  - 0x801A #10 4-way Valve Status
  - 0x80AF #11 Base Heater
  - 0x80D7 #12 PHE Heater
  - 0x8204 #13 Outdoor temperature
  - 0x8206 #14 High Pressure
  - 0x8208 #15 Low Pressure
  - 0x820A #16 Discharge 1 Temperature
  - 0x8217 #17 Compressor 1 Current
  - 0x8218 #18 Heat exchanger outlet temperature (condout)
  - 0x821A #19 Suction Temperature
  - 0x821E #20 EVI In Temperature
  - 0x8220 #21 EVI Out Temperature
  - 0x8226 #22 fanstep1
  - 0x8229 #23 Main EEV1
  - 0x8235 #24 Error Code
  - 0x8236 #25 Compressor 1 Order Frequency
  - 0x8237 #26 Compressor 1 Target Frequency
  - 0x8238 #27 Compressor 1 Current Frequency
  - 0x823B #28 DC Link 1 Voltage
  - 0x823D #29 Outdoor Fan1 RPM
  - 0x82DE #30 EVA In Temperature
  - 0x8254 #31 IPM1 Temperature
  - 0x8278 #32 Compressor OCT1
  - 0x8280 #33 Compressor 1 Top Temperature
  - 0x829F #34 High pressure saturation temperature
  - 0x82A0 #35 Low pressure saturation temperature
  slave: 2
```

That`s for now, the page will be updated from time to time.
Maybe Youtube video following
Should not be only work at Home assistant.
The MIM-19N(T) Installation Guid has more information inside, if I can not find the version I have in printed format in the www I will scan it and upload it here.

