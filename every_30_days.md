### Event Sequence - 30 Days

This config is for 30 days @ 1500 Hours
```
cloud_update_config = "0 15 */30 * *"
```


```mermaid
sequenceDiagram
    Note left of Device: Day 1
    Device ->> Cloud: d2c_update(Ingestion lambda)
    Note right of Device: 1500 Hrs
    Note left of Redis DB: Current Time = 1502 Hrs<br/>Write next connection expiry time NCE<br/>Next check-in time(epoch) NCT<br/>NCT is cloud_update_config from device profile<br/>Delta 72 hours<br/>NCT is 29 days 23 Hrs 58 Min from now<br/>NCE = NCT + 72<br/>NCE = Day 33@1500 Hrs<br/>~33 Days till expiry
    Cloud ->> Redis DB: Write NCE


    Note left of Device: Day 2
    Cloud ->> MC-Lambda: Init
    Note left of Cloud: 0000 Hrs
    Note left of MC-Lambda: 2400 - 1500 Hrs = 9 Hrs<br/>33 Days - 9 Hrs till expiry<br/>32 Days 15 Hrs till expiry<br/>Check NCE less than current time<br>Day 33@1500 Hrs < Day 2@0001 ?
    Note right of MC-Lambda: False
    MC-Lambda ->> MC-Lambda: Continue
    Device --x Cloud: d2c_update
    Note right of Device: Device does<br>not connect on<br>Day 2
    Cloud--xRedis DB: No info written<br>for this device


    Note left of Device: Day 3
    Cloud ->> MC-Lambda: Init
    Note left of Cloud: 0000 Hrs
    Note left of MC-Lambda: 32 Days 15 Hrs - 24 Hrs till expiry<br/>31 Days 15 Hrs till expiry<br/>Check NCE less than current time<br>Day 33@1500 Hrs < Day 3@0002 ?
    Note right of MC-Lambda: False
    MC-Lambda ->> MC-Lambda: Continue


    Note left of Device: Day 4
    Cloud ->> MC-Lambda: Init
    Note left of Cloud: 0000 Hrs
    Note left of MC-Lambda: 31 Days 15 Hrs - 24 Hrs till expiry<br/>30 Days 15 Hrs till expiry<br/>Check NCE less than current time<br>Day 33@1500 Hrs < Day 4@0000 ?
    Note right of MC-Lambda: False
    MC-Lambda ->> MC-Lambda: Continue

    Note left of Device: Day 5
    Cloud ->> MC-Lambda: Init
    Note left of Cloud: 0000 Hrs
    Note left of MC-Lambda: 30 Days 15 Hrs - 24 Hrs till expiry<br/>29 Days 15 Hrs till expiry<br/>Check NCE less than current time<br>Day 33@1500 Hrs < Day 5@0000 ?
    Note right of MC-Lambda: False
    MC-Lambda ->> MC-Lambda: Continue

    Note left of Device: Device configured to connect once a month<br>device does not connect on days 31, 32 and 33 as well
    Note left of Device: Day 34
    Cloud ->> MC-Lambda: Init
    Note left of Cloud: 0000 Hrs
    Note left of MC-Lambda: 15 Hrs - 24 Hrs till expiry<br/>9 Hrs past expiry<br/>Check NCE less than current time<br>Day 33@1500 Hrs < Day 34@0000 ?
    Note right of MC-Lambda: True

    MC-Lambda ->> Cloud: Trigger missed check-in alarm for this device