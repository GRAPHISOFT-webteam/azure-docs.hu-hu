---
title: "Csatlakoztassa a Node.js használatával |} Microsoft Docs"
description: "Eszköz csatlakoztatása az Azure IoT Suite előre konfigurált távoli figyelési megoldást igényelnek olyan alkalmazással Node.js nyelven írt ismerteti."
services: 
suite: iot-suite
documentationcenter: na
author: dominicbetts
manager: timlt
editor: 
ms.assetid: fc50a33f-9fb9-42d7-b1b8-eb5cff19335e
ms.service: iot-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/02/2017
ms.author: dobett
ms.openlocfilehash: 87a2e97638508eef1d90a219cfb38d1fcac81d55
ms.sourcegitcommit: 295ec94e3332d3e0a8704c1b848913672f7467c8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/06/2017
---
# <a name="connect-your-device-to-the-remote-monitoring-preconfigured-solution-nodejs"></a>Csatlakoztassa az eszközt a távoli felügyeleti előkonfigurált megoldás (Node.js)
[!INCLUDE [iot-suite-v1-selector-connecting](../../includes/iot-suite-v1-selector-connecting.md)]

## <a name="create-a-nodejs-sample-solution"></a>Node.js sample megoldás létrehozása

Győződjön meg arról, hogy Node.js verziót 0.11.5, vagy később a fejlesztői gépen telepítve. Futtathat `node --version` verziójának a parancssorból.

1. Hozzon létre egy nevű **RemoteMonitoring** a fejlesztési számítógépén. Keresse meg a mappát a parancssori környezetben.

1. Futtassa az alábbi parancsokat a végre kell hajtania a mintaalkalmazás letöltése és telepítése a csomagok:

    ```
    npm init
    npm install azure-iot-device azure-iot-device-mqtt --save
    ```

1. Az a **RemoteMonitoring** mappa, hozzon létre egy nevű fájlt **remote_monitoring.js**. Nyissa meg ezt a fájlt egy szövegszerkesztőben.

1. Az a **remote_monitoring.js** fájlt, adja hozzá a következő `require` utasításokat:

    ```nodejs
    'use strict';

    var Protocol = require('azure-iot-device-mqtt').Mqtt;
    var Client = require('azure-iot-device').Client;
    var ConnectionString = require('azure-iot-device').ConnectionString;
    var Message = require('azure-iot-device').Message;
    ```

1. Adja hozzá a következő változódeklarációkat az `require` utasítások után. Cserélje le a [Device Id] és a [Device Key] helyőrzőértékeket az eszközhöz tartozó értékekre a távoli figyelési megoldás irányítópultja alapján. Cserélje le az [IoTHub Name] értéket a megoldás irányítópultján található IoT Hub gazdanévre. Ha például az IoT Hub gazdaneve **contoso.azure-devices.net**, cserélje le az [IoTHub Name] helyőrzőt a **contoso** értékre:

    ```nodejs
    var connectionString = 'HostName=[IoTHub Name].azure-devices.net;DeviceId=[Device Id];SharedAccessKey=[Device Key]';
    var deviceId = ConnectionString.parse(connectionString).DeviceId;
    ```

1. Adja hozzá a következő változók néhány alapvető telemetriai adatok meghatározásához:

    ```nodejs
    var temperature = 50;
    var humidity = 50;
    var externalTemperature = 55;
    ```

1. Adja hozzá a következő segítő függvény nyomtatni a művelet eredménye:

    ```nodejs
    function printErrorFor(op) {
        return function printError(err) {
            if (err) console.log(op + ' error: ' + err.toString());
        };
    }
    ```

1. Adja hozzá a következő segítő függvény használatával a telemetriai adatok értékek ügyfélfuttatási:

    ```nodejs
    function generateRandomIncrement() {
        return ((Math.random() * 2) - 1);
    }
    ```

1. Adja hozzá a következő definícióját a **deviceinfo információja** objektum indítási küld az eszköz:

    ```nodejs
    var deviceMetaData = {
        'ObjectType': 'DeviceInfo',
        'IsSimulatedDevice': 0,
        'Version': '1.0',
        'DeviceProperties': {
            'DeviceID': deviceId,
            'HubEnabledState': 1
        }
    };
    ```

1. Adja hozzá a következő definícióját az eszköz iker jelentett értékek. Ez a definíció tartalmazza az eszköz támogatja a közvetlen módszerek:

    ```nodejs
    var reportedProperties = {
        "Device": {
            "DeviceState": "normal",
            "Location": {
                "Latitude": 47.642877,
                "Longitude": -122.125497
            }
        },
        "Config": {
            "TemperatureMeanValue": 56.7,
            "TelemetryInterval": 45
        },
        "System": {
            "Manufacturer": "Contoso Inc.",
            "FirmwareVersion": "2.22",
            "InstalledRAM": "8 MB",
            "ModelNumber": "DB-14",
            "Platform": "Plat 9.75",
            "Processor": "i3-9",
            "SerialNumber": "SER99"
        },
        "Location": {
            "Latitude": 47.642877,
            "Longitude": -122.125497
        },
        "SupportedMethods": {
            "Reboot": "Reboot the device",
            "InitiateFirmwareUpdate--FwPackageURI-string": "Updates device Firmware. Use parameter FwPackageURI to specifiy the URI of the firmware file"
        },
    }
    ```

1. Adja hozzá a következő függvény kezelni a **újraindítás** közvetlen metódus hívása:

    ```nodejs
    function onReboot(request, response) {
        // Implement actual logic here.
        console.log('Simulated reboot...');

        // Complete the response
        response.send(200, "Rebooting device", function(err) {
            if(!!err) {
                console.error('An error occurred when sending a method response:\n' + err.toString());
            } else {
                console.log('Response to method \'' + request.methodName + '\' sent successfully.' );
            }
        });
    }
    ```

1. Adja hozzá a következő függvény kezelni a **InitiateFirmwareUpdate** közvetlen metódus hívása. Ez a közvetlen módszer egy paraméter használatával adja meg a belső vezérlőprogram kép helyét, és kezdeményezi a belső vezérlőprogram frissítése az eszközön aszinkron módon:

    ```nodejs
    function onInitiateFirmwareUpdate(request, response) {
        console.log('Simulated firmware update initiated, using: ' + request.payload.FwPackageURI);

        // Complete the response
        response.send(200, "Firmware update initiated", function(err) {
            if(!!err) {
                console.error('An error occurred when sending a method response:\n' + err.toString());
            } else {
                console.log('Response to method \'' + request.methodName + '\' sent successfully.' );
            }
        });

        // Add logic here to perform the firmware update asynchronously
    }
    ```

1. Adja hozzá az ügyfél-példány létrehozása a következő kódot:

    ```nodejs
    var client = Client.fromConnectionString(connectionString, Protocol);
    ```

1. Adja hozzá a következő kódot:

    * Nyissa meg a kapcsolatot.
    * Küldjön a **deviceinfo információja** objektum.
    * A kezelő kívánt tulajdonságok beállítása.
    * Jelentett tulajdonságok küldése.
    * A közvetlen módszer kezelők regisztrálni.
    * Indítsa el a telemetriai adatok küldését.

    ```nodejs
    client.open(function (err) {
        if (err) {
            printErrorFor('open')(err);
        } else {
            console.log('Sending device metadata:\n' + JSON.stringify(deviceMetaData));
            client.sendEvent(new Message(JSON.stringify(deviceMetaData)), printErrorFor('send metadata'));

            // Create device twin
            client.getTwin(function(err, twin) {
                if (err) {
                    console.error('Could not get device twin');
                } else {
                    console.log('Device twin created');

                    twin.on('properties.desired', function(delta) {
                        console.log('Received new desired properties:');
                        console.log(JSON.stringify(delta));
                    });

                    // Send reported properties
                    twin.properties.reported.update(reportedProperties, function(err) {
                        if (err) throw err;
                        console.log('twin state reported');
                    });

                    // Register handlers for direct methods
                    client.onDeviceMethod('Reboot', onReboot);
                    client.onDeviceMethod('InitiateFirmwareUpdate', onInitiateFirmwareUpdate);
                }
            });

            // Start sending telemetry
            var sendInterval = setInterval(function () {
                temperature += generateRandomIncrement();
                externalTemperature += generateRandomIncrement();
                humidity += generateRandomIncrement();

                var data = JSON.stringify({
                    'DeviceID': deviceId,
                    'Temperature': temperature,
                    'Humidity': humidity,
                    'ExternalTemperature': externalTemperature
                });

                console.log('Sending device event data:\n' + data);
                client.sendEvent(new Message(data), printErrorFor('send event'));
            }, 5000);

            client.on('error', function (err) {
                printErrorFor('client')(err);
                if (sendInterval) clearInterval(sendInterval);
                client.close(printErrorFor('client.close'));
            });
        }
    });
    ```

1. A módosítások mentése a **remote_monitoring.js** fájlt.

1. A következő parancsot egy parancssorból indítsa el a mintaalkalmazást:
   
    ```
    node remote_monitoring.js
    ```

[!INCLUDE [iot-suite-v1-visualize-connecting](../../includes/iot-suite-v1-visualize-connecting.md)]

[lnk-github-repo]: https://github.com/azure/azure-iot-sdk-node
[lnk-github-prepare]: https://github.com/Azure/azure-iot-sdk-node/blob/master/doc/node-devbox-setup.md
