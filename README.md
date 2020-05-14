# OpenCovidTrace – Fully Private Open Source Contact Tracing

OpenCovidTrace is an open-source platform integrating all popular BLE  (Bluetooth Low Energy) contact tracing protocols ( [https://github.com/DP-3T DP-3T], Google & Apple, [https://github.com/opentrace-community BlueTrace], etc.) with an additional set of features for iOS and Android platforms.


Our vision is to provide a trustworthy contact tracing tool with universal interoperability, that empowers people and communities to fight Coronavirus.


Our mission is to develop an open-source implementation for proprietary protocols (such as Apple & Google and BlueTrace backends) which addresses privacy concerns in terms of preservation of individual privacy, and integrates with all popular open-source protocols (such as DP-3T).

## Idea
The main idea behind this effort is to decelerate the spread of COVID-19 by notifying potentially infected individuals in advance.

After one person infects several others, then, by the time the COVID-19 diagnosis of the first person is confirmed (as its incubation period is about 14 days), most of the recently infected people will most likely not yet feel any of its symptoms. Instead they will be infecting others.

If we can manage to inform such symptom-free carriers at the moment of diagnosis of the person who had infected them, we can prevent a chain reaction. Thus drastically reducing spread of infection.

This approach can help to at least partially remove quarantine restrictions. Implementing this simple idea might save economies in many countries.

## How it works

An app is installed on a smartphone with iOS or Android launches contact tracing via 

* Every day a new random 16-byte daily Exposure Key is generated. It is used for generation of an encryption key for temporary identifiers:
```RPIKey = HKDF(ExpKey, NULL, UTF8(“EN-RPIK”),16)```
where
- `HKDF(Key, Salt, Info, OutputLength)` designates the HKDF function as defined by IETF RFC 5869, using the SHA-256 hash function.

- `NULL stands for salt that is not used.`

- `UTF8(“EN-RPIK”)` is a byte array that corresponds with the EN-RPIK string in UTF8.

* Every 10 miutes a new temporary Rolling Proximity ID (RPID) is generated.
```
RPID = AES128(RPIKey, UTF8(“EN-RPI”) || 0x000000000000 || Ti)
```
where
`AES128(Key, Data)` is for AES crypto function with 128-bit key.

- `Key` is RPIKey or Rolling Proximity Key generated at first step.

- `Data` is concatenation (`||` -symbol designates concatenation) of the following:

- `UTF8(“EN-RPI”)` is a 6-byte array corresponding with the EN-RPI line encoded in UTF8.

- `0x000000000000` is six zero bytes (to get one 128-bit block in total).

- `Ti` — 4-byte number of 10-minute temporary interval, calculated as `unix_timestamp div (60 * 10)`, whereas div stands for integer division.

This batch is broadcast via Bluetooth Low Energy. Devices that read it and store it locally afterwards.

* Once someone is infected, their last 14 daily keys are published, and all other devices, after downloading these 14 daily keys, generate Rolling Proximity IDs for all the 14 days and compare them to locally stored Rolling Proximity IDs.

## OpenCovidTrace App for iOS
OpenCovidTrace App for iOS sources available in this repository: [https://github.com/OpenCovidTrace/octrace-ios]

Testflight public beta will be started soon.


## OpenCovidTrace App for Android
OpenCovidTrace App for iOS Android available in this repository: [https://github.com/OpenCovidTrace/octrace-android]

Latest build can be found by link: [https://opencovidtrace.org/octrace-android.apk]


## OpenCovidTrace Backend
Contact storage backend source available in this repository: [https://github.com/OpenCovidTrace/oct-storage]

## OpenCovidTrace Website
More information about the project available on our website [https://opencovidtrace.org]
