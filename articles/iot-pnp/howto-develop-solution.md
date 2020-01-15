---
title: Azure IoT 솔루션에서 IoT 플러그 앤 플레이 미리 보기 디바이스와 상호 작용 | Microsoft Docs
description: 솔루션 개발자로서 서비스 SDK를 사용하여 IoT 플러그 앤 플레이 디바이스와 상호 작용하는 방법을 알아봅니다.
author: Philmea
ms.author: philmea
ms.date: 12/26/2019
ms.topic: tutorial
ms.custom: mvc
ms.service: iot-pnp
services: iot-pnp
manager: philmea
ms.openlocfilehash: 485b17ff236de32eab5388629c1bb6044ba19197
ms.sourcegitcommit: ce4a99b493f8cf2d2fd4e29d9ba92f5f942a754c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/28/2019
ms.locfileid: "75531346"
---
# <a name="connect-to-and-interact-with-an-iot-plug-and-play-preview-device"></a>IoT 플러그 앤 플레이 미리 보기 디바이스에 연결하고 상호 작용

이 방법 가이드에서는 IoT 솔루션이 IoT 플러그 앤 플레이 미리 보기 디바이스와 상호 작용하는 방법을 알려주는 Node 서비스 SDK의 샘플을 보여줍니다.

아직 [솔루션에 IoT 플러그 앤 플레이 디바이스 연결](quickstart-connect-pnp-device-solution-node.md) 빠른 시작을 완료하지 않은 경우 지금 완료해야 합니다. 이 빠른 시작에서는 SDK를 다운로드하여 설치하고 샘플을 실행하는 방법을 보여줍니다.

서비스 샘플을 실행하기 전에, 새 터미널을 열고, 복제된 리포지토리의 루트 폴더로 이동하고, **digitaltwins/quickstarts/service** 폴더로 이동한 다음, 다음 명령을 실행하여 종속 요소를 설치합니다.

```cmd/sh
npm install
```

## <a name="run-the-service-samples"></a>서비스 샘플 실행

다음 샘플을 사용하여 Node.js 서비스 SDK의 기능을 살펴봅니다. 사용하는 셸에서 `IOTHUB_CONNECTION_STRING` 환경 변수를 설정합니다.

### <a name="retrieve-a-digital-twin-and-list-the-interfaces"></a>디지털 쌍을 검색하고 인터페이스 나열

**get_digital_twin.js**는 디바이스와 연결된 디지털 쌍을 가져와서 명령줄에 해당 구성 요소를 출력합니다. 실행 중인 디바이스가 없어도 작업이 성공합니다.

**get_digital_twin_interface_instance.js**는 디바이스와 연결된 디지털 쌍의 단일 인터페이스 인스턴스를 가져와서 명령줄에 출력합니다. 디바이스 샘플을 실행할 필요는 없습니다.

### <a name="get-and-set-properties-using-the-node-service-sdk"></a>Node 서비스 SDK를 사용하여 속성을 가져와서 설정

**update_digital_twin.js**는 전체 패치를 사용하여 디바이스 디지털 쌍의 쓰기 가능한 속성을 업데이트합니다. 원한다면 여러 인터페이스에서 여러 속성을 업데이트할 수 있습니다. 이 작업이 성공하려면 디바이스 샘플을 동시에 실행해야 합니다. 디바이스 샘플이 속성 업데이트에 대한 정보를 출력하고 서비스 샘플이 업데이트된 디지털 쌍을 터미널에 출력하면 성공입니다.

### <a name="send-a-command-and-retrieve-the-response-using-the-node-service-sdk"></a>Node 서비스 SDK를 사용하여 명령을 보내고 응답 검색

**invoke_command.js**는 디바이스 디지털 쌍에서 동기 명령을 호출합니다. 이 작업이 성공하려면 디바이스 샘플을 동시에 실행해야 합니다. 디바이스 샘플이 명령 승인에 대한 정보를 출력하고 서비스 클라이언트가 명령 결과를 터미널에 출력하면 성공입니다.

### <a name="connect-to-the-public-repository-and-retrieve-a-model-definition-using-the-node-service-sdk"></a>노드 서비스 SDK를 사용하여 퍼블릭 리포지토리에 연결하고 모델 정의 검색

서비스 및 디바이스 샘플과 동일한 지침을 사용하여 다음 환경 변수를 설정해야 합니다.

* `AZURE_IOT_MODEL_REPOSITORY_CONNECTION_STRING`

이 연결 문자열은 **회사 리포지토리**의 **연결문자열** 탭에 있는 [IoT용 Azure Certified 포털](https://preview.catalog.azureiotsolutions.com)에서 찾을 수 있습니다.

연결 문자열은 다음 예제와 비슷합니다.

```text
HostName={repo host name};RepositoryId={repo ID};SharedAccessKeyName={repo key ID};SharedAccessKey={repo key secret}
```

이러한 네 가지 환경 변수를 설정한 후에는 다른 샘플을 실행한 것과 동일한 방법으로 샘플을 실행합니다.

```cmd/sh
node model_repo.js
```

이 샘플은 **ModelDiscovery** 인터페이스를 다운로드하고 이 모델을 터미널에 출력합니다.

### <a name="run-queries-in-iot-hub-based-on-capability-models-and-interfaces"></a>기능 모델 및 인터페이스에 따라 IoT Hub에서 쿼리 실행

IoT Hub 쿼리 언어는 다음 예제와 같이 `HAS_INTERFACE` 및 `HAS_CAPABILITYMODEL`을 지원합니다.

```sql
select * from devices where HAS_INTERFACE('id without version', version)
```

```sql
select * from devices where HAS_CAPABILITYMODEL('id without version', version)
```

### <a name="creating-digital-twin-routes"></a>디지털 쌍 경로 만들기

솔루션에서 디지털 쌍 변경 이벤트에 대한 알림을 받을 수 있습니다. 이러한 알림을 구독하려면 [IoT Hub 라우팅 기능](../iot-hub/iot-hub-devguide-endpoints.md)을 사용하여 Blob 스토리지, Event Hubs, Service Bus 큐 등의 엔드포인트로 알림을 보냅니다.

디지털 쌍 경로를 만들려면 다음을 수행합니다.

1. Azure Portal에서 IoT Hub 리소스로 이동합니다.
1. **메시지 라우팅**을 선택합니다.
1. **경로** 탭에서 **추가**를 선택합니다.
1. **이름** 필드에 값을 입력하고 **엔드포인트**를 선택합니다. 아직 엔드포인트를 구성하지 않은 경우 **엔드포인트 추가**를 선택합니다.
1. **데이터 원본** 드롭다운에서 **디지털 쌍 변경 이벤트**를 선택합니다.
1. **저장**을 선택합니다.

다음 JSON은 디지털 쌍 변경 이벤트의 예를 보여줍니다.

```json
{
  "interfaces": {
    "urn_azureiot_ModelDiscovery_DigitalTwin": {
      "name": "urn_azureiot_ModelDiscovery_DigitalTwin",
      "properties": {
        "modelInformation": {
          "reported": {
            "value": {
              "modelId": "urn:domain:capabilitymodel:TestCapability:1",
              "interfaces": {
                "MyInterfaceFoo": "urn:domain:interfaces:FooInterface:1",
                "urn_azureiot_ModelDiscovery_DigitalTwin": "urn:azureiot:ModelDiscovery:DigitalTwin:1"
              }
            }
          }
        }
      }
    },
    "MyInterfaceFoo": {
      "name": "MyInterfaceFoo",
      "properties": {
        "property_1": { "desired": { "value": "value_1" } },
        "property_2": {
          "desired": { "value": 20 },
          "reported": {
            "value": 10,
            "desiredState": {
              "code": 200,
              "version": 22,
              "subCode": 400,
              "description": ""
            }
          }
        },
        "property_3": { "reported": { "value": "value_3" } }
      }
    }
  },
  "version": 4
}
```

## <a name="next-steps"></a>다음 단계

IoT 플러그 앤 플레이 디바이스와 상호 작용하는 서비스 솔루션에 대해 알아보았으므로, 그 다음 단계로 [모델 검색](concepts-model-discovery.md)에 대해 알아보세요.
