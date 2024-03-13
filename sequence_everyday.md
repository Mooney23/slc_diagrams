### Event Sequence - Everyday

This config is for everyday @ 1500 Hours
```
cloud_update_config = "0 15 * * *"
```


```
sequenceDiagram
Note left of Device: Day 1
Device ->> Cloud: d2c_update
Note right of Device: 1500 Hrs
Cloud->>DB: Write current time as<br/> last connected time<br/>LCT = Day 1 @ 1509 Hrs
Cloud->>DB: Write expected<br/> next connection time<br/>ENCT = LCT + 72 hours<br/>ENCT = Day 4 @ 1509 Hrs


Note left of Device: Day 2
Cloud ->> MC-Lambda: Init
Note left of Cloud: 0000 Hrs
MC-Lambda ->> MC-Lambda: Check ENCT less than current time<br>Day 4 - 1509 Hrs < Day 2 - 0000 ?
Note right of MC-Lambda: False
MC-Lambda ->> MC-Lambda: Continue
Device --x Cloud: d2c_update
Note right of Device: Device does<br>not connect on<br>Day 2
Cloud--xDB: No info written<br>for this device


Note left of Device: Device does not connect <br>on day 3 and 4 as well
Note left of Device: Day 3
Cloud ->> MC-Lambda: Init
Note left of Cloud: 0000 Hrs
MC-Lambda ->> MC-Lambda: Check ENCT less than current time<br>Day 4 - 1509 Hrs < Day 3 - 0000 ?


Note left of Device: Day 4
Cloud ->> MC-Lambda: Init
Note left of Cloud: 0000 Hrs
MC-Lambda ->> MC-Lambda: Check ENCT less than current time<br>Day 4 - 1509 Hrs < Day 4 - 0000 ?


Note left of Device: Day 5
Cloud ->> MC-Lambda: Init
Note left of Cloud: 0000 Hrs
MC-Lambda ->> MC-Lambda: Check ENCT less than current time<br>Day 4 - 1509 Hrs < Day 5 - 0000 ?
Note left of MC-Lambda: True
MC-Lambda ->> Cloud: Trigger missed check-in alarm for this device
```

