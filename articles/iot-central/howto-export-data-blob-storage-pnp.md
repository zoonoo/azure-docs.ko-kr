---
title: Azure Blob Storage에 데이터 내보내기 | Microsoft Docs
description: Azure IoT Central 애플리케이션에서 Azure Blob Storage에 데이터를 내보내는 방법
services: iot-central
author: viv-liu
ms.author: viviali
ms.date: 09/26/2019
ms.topic: conceptual
ms.service: iot-central
manager: corywink
ms.openlocfilehash: 9ac650273a53da715b89e3233b30cf50710cfcfd
ms.sourcegitcommit: c2e7595a2966e84dc10afb9a22b74400c4b500ed
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/05/2019
ms.locfileid: "71973251"
---
# <a name="export-your-data-to-azure-blob-storage-preview-features"></a>데이터를 Azure Blob Storage로 내보내기 (미리 보기 기능)

[!INCLUDE [iot-central-pnp-original](../../includes/iot-central-pnp-original-note.md)]

*이 항목의 내용은 관리자에게 적용됩니다.*

이 문서에서는 Azure IoT Central의 연속 데이터 내보내기 기능을 사용 하 여 주기적으로 **Azure Blob storage 계정** 또는 **Azure Data Lake Storage Gen2 storage 계정**으로 데이터를 내보내는 방법을 설명 합니다. **원격 분석**, **장치**및 **장치 템플릿을** JSON 형식의 파일로 내보낼 수 있습니다. 내보낸 데이터는 Azure Machine Learning의 학습 모델 또는 Microsoft Power BI의 장기 추세 분석과 같은 콜드 경로 분석에 사용할 수 있습니다.

> [!Note]
> 연속 데이터 내보내기를 켜면 그 시점 이후의 데이터만 얻게 됩니다. 현재는 연속 데이터 내보내기가 꺼져 있는 시간의 데이터를 검색할 수 없습니다. 더 많은 기록 데이터를 유지하려면 연속 데이터 내보내기를 일찍 켜세요.


## <a name="prerequisites"></a>사전 요구 사항

- IoT Central 애플리케이션에서 관리자여야 함

## <a name="create-storage-account"></a>스토리지 계정 만들기
내보낼 기존 저장소가 없는 경우 다음 단계를 수행 합니다.

1. [Azure Portal에서 새 스토리지 계정](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM)을 만듭니다. 새 [Azure Blob Storage 계정](https://aka.ms/blobdocscreatestorageaccount) 만들기 또는 [v2 저장소 계정 Azure Data Lake Storage](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-quickstart-create-account)에 대해 자세히 알아볼 수 있습니다.

    > [!Note] 
    > ADLS v2 storage 계정으로 데이터를 내보내도록 선택 하는 경우 **계정 종류** 를 **blobstorage**로 선택 해야 합니다. 

    > [!Note] 
    > 종 량 제 IoT Central 응용 프로그램에 대 한 것과 다른 구독에서 저장소 계정으로 데이터를 내보낼 수 있습니다. 이 경우 연결 문자열을 사용하여 연결합니다.

2. 스토리지 계정에 컨테이너를 만듭니다. 스토리지 계정으로 이동합니다. **Blob 서비스**에서 **Blob 찾아보기**를 선택합니다. 맨 위에서 **+ 컨테이너**를 선택하여 새 컨테이너를 만듭니다.


## <a name="set-up-continuous-data-export"></a>연속 데이터 내보내기 설정

이제 데이터를 내보낼 저장소 대상이 있으므로 다음 단계에 따라 연속 데이터 내보내기를 설정 합니다. 

1. IoT Central 애플리케이션에 로그인합니다.

2. 왼쪽 메뉴에서 **데이터 내보내기**를 선택 합니다.

    > [!Note]
    > 왼쪽 메뉴에 데이터 내보내기가 표시 되지 않는 경우 앱의 관리자가 아닙니다. 관리자에게 데이터 내보내기를 설정하도록 요청합니다.

3. 오른쪽 위에 있는 **+ 새로 만들기** 단추를 선택 합니다. 내보내기의 대상으로 **Azure Blob Storage** 를 선택 합니다. 

    > [!NOTE] 
    > 앱당 최대 내보내기 수는 5개입니다. 

    ![새 연속 데이터 내보내기 만들기](media/howto-export-data-pnp/export-new2.png)

4. 드롭다운 목록 상자에서 **저장소 계정 네임 스페이스**를 선택 합니다. **연결 문자열 입력**인 목록의 마지막 옵션을 선택할 수도 있습니다. 

    > [!NOTE] 
    > 저장소 계정 네임 스페이스는 **IoT Central 앱과 동일한 구독**에만 표시 됩니다. 이 구독 외부의 대상으로 내보내려는 경우 **연결 문자열 입력**을 선택하고 5단계를 참조합니다.

    > [!NOTE] 
    > 7일 평가판 앱의 경우 연결 문자열을 통해서만 연속 데이터 내보내기를 구성할 수 있습니다. 7일 평가판 앱에는 연결된 Azure 구독이 없기 때문입니다.

    ![Blob에 대 한 새 내보내기 만들기](media/howto-export-data-pnp/export-create-blob2.png)

5. (선택 사항) **연결 문자열 입력**을 선택한 경우 연결 문자열을 붙여넣을 수 있는 새 상자가 나타납니다. 저장소 계정에 대 한 연결 문자열을 가져오려면 Azure Portal의 저장소 계정으로 이동 합니다.
    - **설정**아래에서 **액세스 키** 를 선택 합니다.
    - key1 연결 문자열 또는 key2 연결 문자열 중 하나를 복사합니다.
 
6. 드롭다운 목록 상자에서 컨테이너를 선택 합니다. 컨테이너가 없는 경우 Azure Portal의 저장소 계정으로 이동 합니다.
    - **Blob service**에서 **blob**을 선택 합니다. **+ 컨테이너** 를 클릭 하 고 컨테이너에 이름을 지정 합니다. 데이터에 대 한 공용 액세스 수준을 선택 합니다 (모든 데이터는 연속 데이터 내보내기와 함께 사용 됨). [Azure Storage 문서](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal#create-a-container)에서 자세히 알아보세요.

7.  **데이터 내보내기** 아래에서 형식을 **켜기**로 설정하여 내보낼 각 데이터 형식을 지정합니다.
   
    > [!NOTE] 
    > 데이터는 JSON 형식으로 내보내집니다.

8. 연속 데이터 내보내기를 설정 하려면 **데이터 내보내기** 토글이 on으로 설정 되어 있는지 확인 합니다. **저장**을 선택합니다.

   ![연속 데이터 내보내기 구성](media/howto-export-data-pnp/export-list-blob2.png)

9. 몇 분 후에 데이터는 저장소 계정에 표시 됩니다.


## <a name="path-structure"></a>경로 구조

원격 분석, 장치 및 장치 템플릿 데이터는 분당 한 번씩 저장소 계정으로 내보내집니다. 각 파일은 마지막으로 내보낸 파일 이후의 변경 내용 일괄 처리를 포함 합니다. 내보낸 데이터는 JSON 형식으로 3 개의 폴더에 배치 됩니다. 스토리지 계정의 기본 경로는 다음과 같습니다.
- 원격 분석: {container}/{app-id}/telemetry/{YYYY}/{MM}/{dd}/{hh}/{mm}/{filename}
- 장치: {container}/{app-id}/devices/{YYYY}/{MM}/{dd}/{hh}/{mm}/{filename}
- 장치 템플릿: {container}/{app-id}/deviceTemplates/{YYYY}/{MM}/{dd}/{hh}/{mm}/{filename}

파일로 이동 하 고 **Blob 편집** 탭을 선택 하 여 Azure Portal에서 내보낸 파일을 찾아볼 수 있습니다.

## <a name="data-format"></a>데이터 서식
### <a name="telemetry"></a>원격 분석

내보낸 원격 분석 데이터에는 해당 시간 동안 모든 장치에서 IoT Central 하 여 받은 모든 새 메시지가 포함 됩니다. 내보낸 파일은 [IoT Hub 메시지 라우팅](https://docs.microsoft.com/azure/iot-hub/iot-hub-csharp-csharp-process-d2c)에서 Blob 스토리지로 내보낸 메시지 파일과 동일한 형식을 사용합니다.

> [!NOTE]
> 장치가 `contentType: application/JSON`과 `contentEncoding:utf-8` (또는 `utf-16`, `utf-32`) 인 메시지를 보내고 있는지 확인 합니다. 예는 [IoT Hub 설명서](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-routing-query-syntax#message-routing-query-based-on-message-body) 를 참조 하세요.

> [!NOTE]
> 원격 분석을 보내는 장치는 장치 Id로 표시 됩니다 (다음 섹션 참조). 디바이스 이름을 가져오려면 디바이스 스냅샷을 내보냅니다. 디바이스 레코드의 **deviceId**와 일치하는 **connectionDeviceId**를 사용하여 각 메시지 레코드의 상관 관계를 지정합니다.

다음 예제에서는 JSON 형식의 레코드를 보여 줍니다.

```json
{ 
  "EnqueuedTimeUtc":"2019-09-26T17:46:09.8870000Z",
  "Properties":{ 

  },
  "SystemProperties":{ 
    "connectionDeviceId":"<deviceid>",
    "connectionAuthMethod":"{\"scope\":\"device\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}",
    "connectionDeviceGenerationId":"637051167384630591",
    "contentType":"application/json",
    "contentEncoding":"utf-8",
    "enqueuedTime":"2019-09-26T17:46:09.8870000Z"
  },
  "Body":{ 
    "temp":49.91322758395974,
    "humid":49.61214852573155,
    "pm25":25.87332214661367
  }
}
```

### <a name="devices"></a>장치

연속 데이터 내보내기를 처음으로 켜면 모든 디바이스가 포함된 단일 스냅샷이 내보내집니다. 각 디바이스에는 다음 항목이 포함됩니다.
- IoT Central의 디바이스 `@id`
- 디바이스의 `name`
- [Device Provisioning Service](https://aka.ms/iotcentraldocsdps)의 `deviceId`
- 디바이스 템플릿 정보
- 속성 값

새 스냅샷이 1분에 한 번씩 기록됩니다. 스냅샷에는 다음이 포함됩니다.

- 마지막 스냅샷 이후에 추가된 새 디바이스.
- 마지막 스냅숏 이후 변경 된 속성 값이 있는 장치입니다.

> [!NOTE]
> 마지막 스냅샷 이후에 삭제된 디바이스는 내보내지지 않습니다. 현재는 삭제된 디바이스를 나타내는 표시기가 스냅샷에 없습니다.
>
> 각 장치가 속하는 장치 템플릿은 `instanceOf` 필드로 표시 됩니다. 디바이스 템플릿 이름을 가져오려면 디바이스 템플릿 스냅샷을 내보내야 합니다.

내보낸 파일은 레코드 당 한 줄을 포함 합니다. 다음 예제에서는 JSON 형식의 레코드를 보여 줍니다.

```json
{ 
  "@id":"<id-value>",
  "@type":"Device",
  "displayName":"Airbox",
  "data":{ 
    "$cloudProperties":{ 
        "Color":"blue"
    },
    "EnvironmentalSensor":{ 
      "thsensormodel":{ 
        "reported":{ 
          "value":"Neque quia et voluptatem veritatis assumenda consequuntur quod.",
          "$lastUpdatedTimestamp":"2019-09-30T20:35:43.8478978Z"
        }
      },
      "pm25sensormodel":{ 
        "reported":{ 
          "value":"Aut alias odio.",
          "$lastUpdatedTimestamp":"2019-09-30T20:35:43.8478978Z"
        }
      }
    },
    "urn_azureiot_DeviceManagement_DeviceInformation":{ 
      "totalStorage":{ 
        "reported":{ 
          "value":27900.9730905171,
          "$lastUpdatedTimestamp":"2019-09-30T20:35:43.8478978Z"
        }
      },
      "totalMemory":{ 
        "reported":{ 
          "value":4667.82916715811,
          "$lastUpdatedTimestamp":"2019-09-30T20:35:43.8478978Z"
        }
      }
    }
  },
  "instanceOf":"<template-id>",
  "deviceId":"<device-id>",
  "simulated":true
}
```

### <a name="device-templates"></a>디바이스 템플릿

연속 데이터 내보내기를 처음으로 켜면 모든 디바이스 템플릿이 포함된 단일 스냅샷이 내보내집니다. 각 디바이스 템플릿에는 다음 항목이 포함됩니다.
- 디바이스 템플릿의 `@id`
- 디바이스 템플릿의 `name`
- 디바이스 템플릿의 `version`
- 장치 @no__t `interfaces`, 원격 분석, 속성 및 명령 정의를 포함 하 여 0입니다.
- `cloudProperties` 정의
- 및 초기 값을 재정의 하 고 `capabilityModel`으로 인라인

새 스냅샷이 1분에 한 번씩 기록됩니다. 스냅샷에는 다음이 포함됩니다.

- 마지막 스냅샷 이후에 추가된 새 디바이스 템플릿. 여기에는 새 버전의 장치 템플릿이 포함 됩니다.
- 마지막 스냅숏 이후 변경 된 재정의, 초기 값 및 클라우드 속성 정의가 있는 장치 템플릿입니다.

> [!NOTE]
> 마지막 스냅샷 이후에 삭제된 디바이스 템플릿은 내보내지지 않습니다. 현재는 삭제된 디바이스 템플릿을 나타내는 표시기가 스냅샷에 없습니다.

내보낸 파일은 레코드 당 한 줄을 포함 합니다. 다음 예제에서는 JSON 형식의 레코드를 보여 줍니다.

```json
{ 
  "@id":"<template-id>",
  "@type":"DeviceModelDefinition",
  "displayName":"Airbox",
  "capabilityModel":{ 
    "@id":"<id>",
    "@type":"CapabilityModel",
    "implements":[ 
      { 
        "@id":"<id>",
        "@type":"InterfaceInstance",
        "name":"EnvironmentalSensor",
        "schema":{ 
          "@id":"<id>",
          "@type":"Interface",
          "comment":"Requires temperature and humidity sensors.",
          "description":"Provides functionality to report temperature, humidity. Provides telemetry, commands and read-write properties",
          "displayName":"Environmental Sensor",
          "contents":[ 
            { 
              "@id":"<id>",
              "@type":"Telemetry",
              "description":"Current temperature on the device",
              "displayName":"Temperature",
              "name":"temp",
              "schema":"double",
              "unit":"Units/Temperature/celsius",
              "valueDetail":{ 
                "@id":"<id>",
                "@type":"ValueDetail/NumberValueDetail",
                "minValue":{ 
                  "@value":"50"
                }
              },
              "visualizationDetail":{ 
                "@id":"<id>",
                "@type":"VisualizationDetail"
              }
            },
            { 
              "@id":"<id>",
              "@type":"Telemetry",
              "description":"Current humidity on the device",
              "displayName":"Humidity",
              "name":"humid",
              "schema":"integer"
            },
            { 
              "@id":"<id>",
              "@type":"Telemetry",
              "description":"Current PM2.5 on the device",
              "displayName":"PM2.5",
              "name":"pm25",
              "schema":"integer"
            },
            { 
              "@id":"<id>",
              "@type":"Property",
              "description":"T&H Sensor Model Name",
              "displayName":"T&H Sensor Model",
              "name":"thsensormodel",
              "schema":"string"
            },
            { 
              "@id":"<id>",
              "@type":"Property",
              "description":"PM2.5 Sensor Model Name",
              "displayName":"PM2.5 Sensor Model",
              "name":"pm25sensormodel",
              "schema":"string"
            }
          ]
        }
      },
      { 
        "@id":"<id>",
        "@type":"InterfaceInstance",
        "name":"urn_azureiot_DeviceManagement_DeviceInformation",
        "schema":{ 
          "@id":"<id>",
          "@type":"Interface",
          "displayName":"Device information",
          "contents":[ 
            { 
              "@id":"<id>",
              "@type":"Property",
              "comment":"Total available storage on the device in kilobytes. Ex. 20480000 kilobytes.",
              "displayName":"Total storage",
              "name":"totalStorage",
              "displayUnit":"kilobytes",
              "schema":"long"
            },
            { 
              "@id":"<id>",
              "@type":"Property",
              "comment":"Total available memory on the device in kilobytes. Ex. 256000 kilobytes.",
              "displayName":"Total memory",
              "name":"totalMemory",
              "displayUnit":"kilobytes",
              "schema":"long"
            }
          ]
        }
      }
    ],
    "displayName":"AAEONAirbox52"
  },
  "solutionModel":{ 
    "@id":"<id>",
    "@type":"SolutionModel",
    "cloudProperties":[ 
      { 
        "@id":"<id>",
        "@type":"CloudProperty",
        "displayName":"Color",
        "name":"Color",
        "schema":"string",
        "valueDetail":{ 
          "@id":"<id>",
          "@type":"ValueDetail/StringValueDetail"
        },
        "visualizationDetail":{ 
          "@id":"<id>",
          "@type":"VisualizationDetail"
        }
      }
    ]
  }
}
```

## <a name="next-steps"></a>다음 단계

데이터를 내보내는 방법을 알아보았으므로 다음 단계를 계속 진행하세요.

> [!div class="nextstepaction"]
> [IoT Central 장치 브리지를 사용 하 여 다른 IoT 클라우드를 연결 하는 방법](howto-build-iotc-device-bridge.md)
