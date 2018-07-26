---
title: Azure IoT Central에서 데이터 내보내기 | Microsoft Docs
description: Azure IoT Central 응용 프로그램에서 데이터를 내보내는 방법
services: iot-central
author: viv-liu
ms.author: viviali
ms.date: 07/3/2018
ms.topic: article
ms.prod: azure-iot-central
manager: peterpr
ms.openlocfilehash: 6d35e3cfefcefef0b4ff40364cbdab92d486b769
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/13/2018
ms.locfileid: "39011461"
---
# <a name="export-your-data-in-azure-iot-central"></a>Azure IoT Central에서 데이터 내보내기

연속 데이터 내보내기를 사용하여 Azure Blob Storage 계정에 주기적으로 데이터를 내보냅니다. [Apache AVRO](https://avro.apache.org/docs/current/index.html) 형식의 파일에서 **측정값**, **장치** 및 **장치 템플릿**을 내보내도록 선택합니다. Azure Machine Learning의 학습 모델 또는 Power BI의 장기 추세 분석과 같은 콜드 경로 분석에 내보낸 데이터를 사용합니다.

> [!Note]
> 연속 데이터 내보내기를 켜면 그 순간부터 제공되는 데이터만을 가져옵니다. 현재 연속 데이터 내보내기가 꺼진 경우 데이터를 검색하는 방법이 없습니다. 더 많은 기록 데이터를 유지하려면 연속 데이터 내보내기를 일찍 켭니다!

## <a name="prerequisites"></a>필수 조건

- 확장된 30일 평가판 앱 또는 유료 앱
- Azure 구독이 있는 Azure 계정
- 동일한 Azure 계정이 IoT Central 앱에서 관리자임
- 동일한 Azure 계정에 저장소 계정을 만들거나 동일한 Azure 구독에서 기존 저장소 계정에 액세스하는 권한이 있음

## <a name="types-of-data-to-export"></a>내보낼 데이터 형식

### <a name="measurements"></a>측정값

장치에서 전송하는 측정값이 저장소 계정으로 내보내집니다. 해당 기간 내에 모든 장치의 IoT Central에서 받은 모든 메시지를 포함하는 측정값 데이터가 약 1분에 한 번 내보내집니다. 내보낸 AVRO 파일은 [IoT Hub 메시지 라우팅](https://docs.microsoft.com/en-us/azure/iot-hub/iot-hub-csharp-csharp-process-d2c)에서 Blob 저장소로 내보낸 메시지 파일과 동일한 형식입니다.

> [!NOTE]
> 측정값을 전송한 장치는 장치 ID로 표시됩니다(아래 참조). 장치 이름을 가져오려면 장치 스냅숏도 내보내야 합니다. 장치 ID와 일치하는 connectionDeviceId를 사용하여 각 메시지 레코드를 상호 연결할 수 있습니다.

디코딩된 AVRO 파일의 레코드의 예제입니다.

```json
{
    "EnqueuedTimeUtc": "2018-06-11T00:00:08.2250000Z",
    "Properties": {},
    "SystemProperties": {
        "connectionDeviceId": "2383d8ba-c98c-403a-b4d5-8963859643bb",
        "connectionAuthMethod": "{\"scope\":\"hub\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}",
        "connectionDeviceGenerationId": "636614021491644195",
        "enqueuedTime": "2018-06-11T00:00:08.2250000Z"
    },
    "Body": "{\"humidity\":80.59100954598546,\"magnetometerX\":0.29451796907056726,\"magnetometerY\":0.5550332126050068,\"magnetometerZ\":-0.04116681874733441,\"connectivity\":\"connected\",\"opened\":\"triggered\"}"
}
```

### <a name="devices"></a>장치

연속 데이터 내보내기를 처음 켜면 모든 장치를 포함하는 단일 스냅숏이 내보내집니다. 다음 내용이 포함됩니다.
- 장치 ID
- 장치 이름
- 장치 템플릿 ID
- 속성 값
- 설정 값

약 1분에 한 번 다음을 포함하는 새 스냅숏이 작성됩니다.

- 마지막 스냅숏 이후에 추가된 새 장치
- 마지막 스냅숏 이후에 속성 및 설정 값이 변경된 장치

> [!NOTE]
> 마지막 스냅숏이 내보내지지 않은 이후에 삭제된 장치 이 때 삭제된 장치에 대한 스냅숏에 표시기가 없습니다.

> [!NOTE]
> 각 장치가 속하는 장치 템플릿은 장치 템플릿 ID로 표시됩니다. 장치 템플릿의 이름을 가져오려면 장치 템플릿 스냅숏도 내보내야 합니다.

디코딩된 AVRO 파일의 각 레코드는 다음과 같습니다.

```json
{
    "id": "2383d8ba-c98c-403a-b4d5-8963859643bb",
    "name": "Refrigerator 2",
    "simulated": true,
    "deviceTemplate": {
        "id": "c318d580-39fc-4aca-b995-843719821049",
        "version": "1.0.0"
    },
    "properties": {
        "cloud": {
            "location": "New York",
            "maintCon": true,
            "tempThresh": 20
        },
        "device": {
            "lastReboot": "2018-02-09T22:22:47.156Z"
        }
    },
    "settings": {
        "device": {
            "fanSpeed": 0
        }
    }
}
```

### <a name="device-templates"></a>장치 템플릿

연속 데이터 내보내기를 처음 켜면 모든 장치 템플릿을 포함하는 단일 스냅숏이 내보내집니다. 다음 내용이 포함됩니다. 
- 장치 템플릿 ID
- 측정 데이터 형식 및 최솟/최댓값
- 속성 데이터 형식 및 기본값
- 설정 데이터 형식 및 기본값

약 1분에 한 번 다음을 포함하는 새 스냅숏이 작성됩니다.

- 마지막 스냅숏 이후에 추가된 새 장치 템플릿
- 마지막 스냅숏 이후에 측정값, 속성 및 설정 정의가 변경된 장치 템플릿

> [!NOTE]
> 마지막 스냅숏이 내보내지지 않은 이후에 삭제된 장치 템플릿 이 때 삭제된 장치 템플릿에 대한 스냅숏에 표시기가 없습니다.

디코딩된 AVRO 파일의 각 레코드는 다음과 같습니다.

```json
{
    "id": "c318d580-39fc-4aca-b995-843719821049",
    "name": "Refrigerated Vending Machine",
    "version": "1.0.0",
    "measurements": {
        "telemetry": {
            "humidity": {
                "dataType": "double",
                "name": "Humidity"
            },
            "magnetometerX": {
                "dataType": "double",
                "name": "Magnetometer X"
            },
            "magnetometerY": {
                "dataType": "double",
                "name": "Magnetometer Y"
            },
            "magnetometerZ": {
                "dataType": "double",
                "name": "Magnetometer Z"
            }
        },
        "states": {
            "connectivity": {
                "dataType": "enum",
                "name": "Connectivity"
            }
        },
        "events": {
            "opened": {
                "name": "Door Opened",
                "category": "informational"
            }
        }
    },
    "settings": {
        "device": {
            "fanSpeed": {
                "dataType": "double",
                "name": "Fan Speed",
                "initialValue": 0
            }
        }
    },
    "properties": {
        "cloud": {
            "location": {
                "dataType": "string",
                "name": "Location",
                "initialValue": "Seattle"
            },
            "maintCon": {
                "dataType": "boolean",
                "name": "Maintenance Contract",
                "initialValue": true
            },
            "tempThresh": {
                "dataType": "double",
                "name": "Temperature Alert Threshold",
                "initialValue": 30
            }
        },
        "device": {
            "lastReboot": {
                "dataType": "dateTime",
                "name": "Last Reboot"
            }
        }
    }
}
```

## <a name="how-to-set-up-data-export"></a>데이터 내보내기를 설정하는 방법

1. 계정이 아직 없는 경우 **앱이 있는 Azure 구독에서** Azure Storage 계정을 만듭니다. [여기를 클릭](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM)하여 Azure Portal로 이동하여 새 Azure Storage 계정을 만듭니다.

    - *범용* 또는 *Blob 저장소* 계정 종류를 선택합니다.
    - IoT Central 앱이 있는 구독을 선택합니다. 구독이 보이지 않으면 다른 Azure 계정에 로그인하거나 구독에 대한 액세스를 요청해야 합니다.
    - 기존 리소스 그룹을 선택하거나 새 리소스 그룹을 만들 수 있습니다. [새 저장소 계정을 만드는 방법](https://aka.ms/blobdocscreatestorageaccount)에 대해 알아봅니다.

2. IoT Central 데이터를 내보낼 저장소 계정에서 컨테이너를 만듭니다. 저장소 계정 -> Blob 찾아보기로 이동하고, 새 컨테이너를 만듭니다. ![컨테이너 이미지 만들기](media/howto-export-data/createcontainer.png)

3. 동일한 Azure 계정을 사용하여 IoT Central 응용 프로그램에 로그인합니다.
1. 관리 -> 연속 데이터 내보내기로 이동합니다.
![IoT Central CDE](media/howto-export-data/continuousdataexport.PNG)
1. 드롭다운을 사용하여 저장소 계정 및 컨테이너를 선택합니다. 그런 다음, 토글을 사용하여 다양한 유형의 내보낼 데이터를 켜거나 끕니다.
1. 마지막으로 토글을 사용하여 연속 데이터 내보내기를 켜고 "저장"을 누릅니다.
1. 몇 분 정도 기다리면 저장소 계정에 데이터가 표시됩니다. 저장소 계정으로 이동하고, Blob 찾아보기를 선택하고, 컨테이너를 선택하면 세 개의 폴더가 표시됩니다. 다양한 유형의 데이터를 포함하는 AVRO 파일에 대한 기본 경로는 다음과 같습니다.
- 메시지: **{container}/measurements/{hubname}/{YYYY}/{MM}/{dd}/{hh}/{mm}/00.avro**
- 장치: **{container}/devices/{hubname}/{YYYY}/{MM}/{dd}/{hh}/{mm}/00.avro**
- 장치 템플릿: **{container}/deviceTemplates/{hubname}/{YYYY}/{MM}/{dd}/{hh}/{mm}/00.avro**

## <a name="how-to-read-exported-avro-files"></a>내보낸 AVRO 파일을 읽는 방법

AVRO는 이진 형식이므로 해당 원시 상태에서 파일을 읽을 수 없습니다. JSON 형식으로 디코딩될 수 있습니다. 다음 예제에서는 위의 예제를 사용하여 측정값, 장치 및 장치 템플릿 AVRO 파일을 구문 분석하는 방법을 보여줍니다.

## <a name="python"></a>파이썬

### <a name="install-pandas-and-the-pandaavro-package"></a>Pandas 및 PandaAvro 패키지를 설치합니다.

```python
pip install pandas
pip install pandavro
```
### <a name="parse-measurements-avro-file"></a>측정값 AVRO 파일 구문 분석

```python
import json
import pandavro as pdx
import pandas as pd

def parse(filePath):
    # Pandavro loads the avro file into a pandas DataFrame where each record is
    # a single row
    measurements = pdx.from_avro(filePath)

    # In this example, we create a new DataFrame and load a series for each
    # column we map into a column in our new DataFrame.
    transformed = pd.DataFrame()

    # The SystemProperties column contains a dictionary with the device id
    # located under the "connectionDeviceId" key.
    transformed["device_id"] = measurements["SystemProperties"].apply(lambda x: x["connectionDeviceId"])

    # The Body column is a series of utf-8 bytes that is stringified and parsed
    # as json. In this example, we pull the "humidity" property off of each one
    # to get the humidity field.
    transformed["humidity"] = measurements["Body"].apply(lambda x: json.loads(bytes(x).decode('utf-8'))["humidity"])

    # Finally, we print the new DataFrame with our device ids and humidities
    print(transformed)

```
### <a name="parse-devices-avro-file"></a>장치 AVRO 파일 구문 분석

```python
import json
import pandavro as pdx
import pandas as pd

def parse(filePath):
    # Pandavro loads the avro file into a pandas DataFrame where each record is
    # a single row
    devices = pdx.from_avro(filePath)

    # In this example, we create a new DataFrame and load a series for each
    # column we map into a column in our new DataFrame.
    transformed = pd.DataFrame()

    # The device id is available directly in the "id" column
    transformed["device_id"] = devices["id"]

    # The template id and version are present in a dictionary under the
    # deviceTemplate column
    transformed["template_id"] = devices["deviceTemplate"].apply(lambda x: x["id"])
    transformed["template_version"] = devices["deviceTemplate"].apply(lambda x: x["version"])

    # The fanSpeed setting value is located in a nested dictionary under the
    # settings column
    transformed["fan_speed"] = devices["settings"].apply(lambda x: x["device"]["fanSpeed"])

    # Finally, we print the new DataFrame with our device and template
    # information, along with the value of the fan speed
    print(transformed)

```

### <a name="parse-device-templates-avro-file"></a>장치 템플릿 AVRO 파일 구문 분석

```python
import json
import pandavro as pdx
import pandas as pd

def parse(filePath):
    # Pandavro loads the avro file into a pandas DataFrame where each record is
    # a single row
    templates = pdx.from_avro(filePath)

    # In this example, we create a new DataFrame and load a series for each
    # column we map into a column in our new DataFrame.
    transformed = pd.DataFrame()

    # The template and version are available directly in the "id" and "version"
    # columns, respectively
    transformed["template_id"] = templates["id"]
    transformed["template_version"] = templates["version"]

    # The fanSpeed setting value is located in a nested dictionary under the
    # settings column
    transformed["fan_speed"] = templates["settings"].apply(lambda x: x["device"]["fanSpeed"])

    # Finally, we print the new DataFrame with our device and template
    # information, along with the value of the fan speed
    print(transformed)
```

## <a name="c"></a>C#

### <a name="install-microsofthadoopavro"></a>Microsoft.Hadoop.Avro 설치

```csharp
Install-Package Microsoft.Hadoop.Avro -Version 1.5.6
```

### <a name="parse-measurements-avro-file"></a>측정값 AVRO 파일 구문 분석

```csharp
using Microsoft.Hadoop.Avro;
using Microsoft.Hadoop.Avro.Container;
using Newtonsoft.Json;

public static async Task Run(string filePath)
{
    using (var fileStream = File.OpenRead(filePath))
    {
        using (var reader = AvroContainer.CreateGenericReader(fileStream))
        {
            // For one Avro Container, it may contain multiple blocks
            // Loop through each block within the container
            while (reader.MoveNext())
            {
                // Loop through Avro record inside the block and extract the fields
                foreach (AvroRecord record in reader.Current.Objects)
                {
                    var systemProperties = record.GetField<IDictionary<string, object>>("SystemProperties");
                    var deviceId = systemProperties["connectionDeviceId"] as string;
                    Console.WriteLine("Device ID: {0}", deviceId);

                    using (var stream = new MemoryStream(record.GetField<byte[]>("Body")))
                    {
                        using (var streamReader = new StreamReader(stream, Encoding.UTF8))
                        {
                            var body = JsonSerializer.Create().Deserialize(streamReader, typeof(IDictionary<string, dynamic>)) as IDictionary<string, dynamic>;
                            var humidity = body["humidity"];
                            Console.WriteLine("Humidity: {0}", humidity);
                        }
                    }
                }
            }
        }
    }
}
```

### <a name="parse-devices-avro-file"></a>장치 AVRO 파일 구문 분석

```csharp
using Microsoft.Hadoop.Avro;
using Microsoft.Hadoop.Avro.Container;

public static async Task Run(string filePath)
{
    using (var fileStream = File.OpenRead(filePath))
    {
        using (var reader = AvroContainer.CreateGenericReader(fileStream))
        {
            // For one Avro Container, it may contains multiple blocks
            // Loop through each block within the container
            while (reader.MoveNext())
            {
                // Loop through Avro record inside the block and extract the
                // fields from it
                foreach (AvroRecord record in reader.Current.Objects)
                {
                    // Get the field value directly. You can also yield return
                    // record and make the function IEnumerable<AvroRecord>
                    var deviceId = record.GetField<string>("id");

                    // The device template information is stored in a sub-record
                    // under the "deviceTemplate" field.
                    var deviceTemplateRecord = record.GetField<AvroRecord>("deviceTemplate");
                    var templateId = deviceTemplateRecord.GetField<string>("id");
                    var templateVersion = deviceTemplateRecord.GetField<string>("version");

                    // The settings and properties are nested two levels deep,
                    // with the first level indicating settings or properties
                    // and the second indicating the kind of setting/property
                    var settingsRecord = record.GetField<AvroRecord>("settings");
                    var deviceSettingsRecord = settingsRecord.GetField<IDictionary<string, dynamic>>("device");
                    var fanSpeed = deviceSettingsRecord["fanSpeed"];
                    
                    Console.WriteLine(
                        "ID: {0}, Template ID: {1}, Template Version: {2}, Fan Speed: {3}",
                        deviceId,
                        templateId,
                        templateVersion,
                        fanSpeed
                    );
                }
            }
        }
    }
}

```
### <a name="parse-device-templates-avro-file"></a>장치 템플릿 AVRO 파일 구문 분석

```csharp
using Microsoft.Hadoop.Avro;
using Microsoft.Hadoop.Avro.Container;

public static async Task Run(string filePath)
{
    using (var fileStream = File.OpenRead(filePath))
    {
        using (var reader = AvroContainer.CreateGenericReader(fileStream))
        {
            // For one Avro Container, it may contains multiple blocks
            // Loop through each block within the container
            while (reader.MoveNext())
            {
                // Loop through Avro record inside the block and extract the
                // fields from it
                foreach (AvroRecord record in reader.Current.Objects)
                {
                    // Get the field value directly. You can also yield return
                    // record and make the function IEnumerable<AvroRecord>
                    var id = record.GetField<string>("id");
                    var version = record.GetField<string>("version");

                    // The settings and properties are nested two levels deep,
                    // with the first level indicating settings or properties
                    // and the second indicating the kind of setting/property
                    var settingsRecord = record.GetField<AvroRecord>("settings");
                    var deviceSettingsRecord = settingsRecord.GetField<IDictionary<string, dynamic>>("device");
                    var fanSpeed = deviceSettingsRecord["fanSpeed"];
                    
                    Console.WriteLine(
                        "ID: {1}, Version: {2}, Fan Speed: {3}",
                        id,
                        version,
                        fanSpeed
                    );
                }
            }
        }
    }
}
```

## <a name="javascript"></a>JavaScript

### <a name="install-avsc"></a>avsc 설치

```javascript
npm install avsc
```

### <a name="parse-measurements-avro-file"></a>측정값 AVRO 파일 구문 분석

```javascript
const avro = require('avsc');

// Read the avro file and parse the device id and humidity from each record
async function parse(filePath) {
    const records = await load(filePath);
    for (const record of records) {
        // Fetch the device id from the system properties
        const deviceId = record.SystemProperties.connectionDeviceId;

        // Convert the Body from a Buffer to a string and parse it
        const body = JSON.parse(record.Body.toString());

        // Get the humidty property off of the body
        const humidity = body.humidity;

        // Log the device id and humidity we found
        console.log(`Device ID: ${deviceId}`);
        console.log(`Humidity: ${humidity}`);
    }
}

function load(filePath) {
    return new Promise((resolve, reject) => {
        // The file decoder emits each record as a data event on a stream. We
        // collect them up into an array and return them when we get to the end.
        const records = [];
        avro.createFileDecoder(filePath)
            .on('data', record => { records.push(record); })
            .on('end', () => resolve(records))
            .on('error', reject);
    });
}
```

### <a name="parse-devices-avro-file"></a>장치 AVRO 파일 구문 분석

```javascript
const avro = require('avsc');

// Read the avro file and parse the device and template identification info as
// well as the fanSpeed setting for each device record.
async function parse(filePath) {
    const records = await load(filePath);
    for (const record of records) {
        // Fetch the device id from the id property
        const deviceId = record.id;

        // Fetch the tempalte id and version from the deviceTemplate property
        const deviceTemplateId = record.deviceTemplate.id;
        const deviceTemplateVersion = record.deviceTemplate.version;

        // Get the fanSpeed off the nested device settings property
        const fanSpeed = record.settings.device.fanSpeed;

        // Log the device id and humidity we found
        console.log(`ID: ${deviceId}, Template ID: ${deviceTemplateId}, Template Version: ${deviceTemplateVersion}, Fan Speed: ${fanSpeed}`);
    }
}

function load(filePath) {
    return new Promise((resolve, reject) => {
        // The file decoder emits each record as a data event on a stream. We
        // collect them up into an array and return them when we get to the end.
        const records = [];
        avro.createFileDecoder(filePath)
            .on('data', record => { records.push(record); })
            .on('end', () => resolve(records))
            .on('error', reject);
    });
}
```

### <a name="parse-device-templates-avro-file"></a>장치 템플릿 AVRO 파일 구문 분석

```javascript
const avro = require('avsc');

// Read the avro file and parse the device and template identification info as
// well as the fanSpeed setting for each device record.
async function parse(filePath) {
    const records = await load(filePath);
    for (const record of records) {
        // Fetch the template id and version from the id and verison properties
        const templateId = record.id;
        const templateVersion = record.version;

        // Get the fanSpeed off the nested device settings property
        const fanSpeed = record.settings.device.fanSpeed;

        // Log the device id and humidity we found
        console.log(`Template ID: ${templateId}, Template Version: ${templateVersion}, Fan Speed: ${fanSpeed}`);
    }
}

function load(filePath) {
    return new Promise((resolve, reject) => {
        // The file decoder emits each record as a data event on a stream. We
        // collect them up into an array and return them when we get to the end.
        const records = [];
        avro.createFileDecoder(filePath)
            .on('data', record => { records.push(record); })
            .on('end', () => resolve(records))
            .on('error', reject);
    });
}
```

## <a name="next-steps"></a>다음 단계

장치 탐색기에서 [장치를 관리하는](howto-manage-devices.md) 방법을 알아봅니다. 
