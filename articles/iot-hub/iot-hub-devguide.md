<properties
 pageTitle="IoT Hub에 대한 개발자 가이드 항목 | Microsoft Azure"
 description="IoT Hub 끝점, 보안, 장치 ID 레지스트리 및 메시징을 포함하는 Azure IoT Hub 개발자 가이드"
 services="iot-hub"
 documentationCenter=".net"
 authors="dominicbetts"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="multiple"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="02/03/2016"
 ms.author="dobett"/>

# Azure IoT Hub 개발자 가이드

Azure IoT Hub는 수백만의 IoT 장치와 응용 프로그램 백 엔드 간에서 안정적이고 안전한 양방향 통신이 가능하도록 완전히 관리되는 서비스입니다.

Azure IoT Hub는 다음을 활성화합니다.

* 장치 단위 보안 자격 증명 및 액세스 제어를 사용하여 통신을 보호하고
* 신뢰할 수 있는 장치-클라우드 및 클라우드-장치 하이퍼 눈금 메시징을 제공합니다.
* 가장 인기 있는 언어 및 플랫폼에 대한 장치 라이브러리로 쉽게 장치에 연결합니다.

이 문서는 다음 항목을 설명합니다.

- [끝점](#endpoints). 이 섹션에서는 각 IoT Hub가 런타임 및 관리 작업에 노출하는 다양한 끝점을 설명합니다.
- [장치 ID 레지스트리](#device-identity-registry). 이 섹션에서는 각 IoT Hub 장치 ID 레지스트리가 어떤 정보를 저장하고 어떻게 액세스 및 수정할 수 있는지 설명합니다.
- [보안](#security). 이 섹션에서는 장치 및 클라우드 구성 요소 모두에 대한 IoT Hub 기능에 액세스 권한을 부여하는 데 사용하는 보안 모델을 설명합니다.
- [메시징](#messaging). 이 섹션에서는 IoT Hub이 노출하는 메시징 기능(장치-클라우드 및 클라우드-장치)을 설명합니다.
- [할당량 및 제한](#throttling). 이 섹션에서는 IoT Hub의 사용에 적용되는 할당량을 요약합니다.

## 끝점 <a id="endpoints"></a>

Azure IoT Hub는 다중 테넌트 서비스이며 다양한 행위자에게 기능을 노출합니다. 아래 다이어그램에서는 IoT Hub가 노출하는 다양한 끝점을 보여줍니다.

![IoT Hub 끝점][img-endpoints]

끝점에 대한 설명은 다음과 같습니다.

* **리소스 공급자**: IoT Hub 리소스 공급자는 Azure 구독 소유자가 IoT Hub를 만들고 IoT Hub 속성을 업데이트하며 IoT Hub를 삭제하도록 하는 [Azure 리소스 관리자][lnk-arm] 인터페이스를 노출합니다. 장치 수준 액세스 제어와 반대로 IoT Hub 속성은 허브 수준 보안 정책(아래 [액세스 제어](#accesscontrol) 참조) 및 클라우드-장치 및 장치-클라우드 메시징에 대한 기능 옵션을 제어합니다. 또한 리소스 공급자는 [장치 ID를 내보내도록](#importexport) 합니다.
* **장치 ID 관리**: 각 IoT Hub는 HTTP REST 끝점 집합을 노출하여 장치 ID를 관리합니다(만들기, 검색, 업데이트 및 삭제). 장치 ID는 장치 인증 및 액세스 제어에 사용됩니다. 자세한 내용은 [장치 ID 레지스트리](#device-identity-registry)를 참조하세요.
* **장치 끝점**: 장치 ID 레지스트리에 프로비전된 각 장치의 경우 IoT Hub는 해당 장치가 메시지를 받고 보내는 데 사용하는 끝점의 집합을 노출합니다.
    - *장치-클라우드 메시지 보내기*. 이 끝점을 사용하여 장치-클라우드로 메시지를 보냅니다. 자세한 내용은 [장치-클라우드 메시징](#d2c)을 참조하세요.
    - *클라우드-장치 메시지 받기*. 장치는 이 끝점을 사용하여 대상인 클라우드-장치 메시지를 받습니다. 자세한 내용은 [클라우드-장치 메시징](#c2d)을 참조하세요.

    이들 끝점은 HTTP, [MQTT][lnk-mqtt] 및 [AMQP][lnk-amqp] 프로토콜을 사용하여 노출됩니다. AMQP는 포트 443의 [Websockets][lnk-websockets]를 통해 사용할 수도 있습니다.
* **서비스 끝점**: 각 IoT Hub는 응용 프로그램 백 엔드에서 장치와 통신하는 데 사용할 수 있는 끝점의 집합을 노출합니다. 이러한 끝점은 현재 [AMQP][lnk-amqp] 프로토콜만을 사용하여 노출됩니다.
    - *장치-클라우드 메시지 받기*. 이 끝점은 [Azure 이벤트 허브][lnk-event-hubs]와 호환 가능하고 백 엔드 서비스는 장치에서 보낸 모든 장치-클라우드 메시지를 읽는 데 사용할 수 있습니다. 자세한 내용은 [장치-클라우드 메시징](#d2c)을 참조하세요.
    - *클라우드-장치 메시지를 보내고 배달 승인 받기*. 이러한 끝점을 사용하면 응용 프로그램 백 엔드가 안정적인 클라우드-장치 메시지를 보내고 해당하는 전달 또는 만료 승인을 수신하도록 합니다. 자세한 내용은 [클라우드-장치 메시징](#c2d)을 참조하세요.

[IoT Hub API 및 SDK][lnk-apis-sdks] 문서는 이러한 끝점에 액세스할 수 있는 다양한 방법을 설명합니다.

마지막으로 모든 IoT Hub 끝점이 [TLS][lnk-tls] 프로토콜을 사용하고 어떤 끝점도 암호화되지 않은/보안되지 않은 채널에 노출되지 않는다는 점에 유의합니다.

### 이벤트 허브와 호환 가능한 끝점에서 읽는 방법. <a id="eventhubcompatible"></a>

[.NET에 Azure 서비스 버스 SDK](https://www.nuget.org/packages/WindowsAzure.ServiceBus) 또는 [이벤트 허브 - 이벤트 프로세서 호스트][]를 사용하는 경우 적절한 권한이 있는 모든 IoT Hub 연결 문자열을 사용한 다음 **메시지/이벤트**를 이벤트 허브 이름으로 사용할 수 있습니다.

IoT Hub를 인식하지 않는 SDK(또는 제품 통합)를 사용하는 경우 [Azure 포털][]의 IoT Hub 설정에서 이벤트 허브와 호환 가능한 끝점 및 이벤트 허브 이름을 검색해야 합니다.

1. IoT Hub 블레이드에서 **설정** 및 **메시징**을 차례로 클릭합니다.
2. **장치-클라우드 설정** 섹션에서 **이벤트 허브와 호환 가능한 끝점**, **이벤트 허브와 호환 가능한 이름** 및 **파티션** 값을 찾습니다.

    ![][img-eventhubcompatible]

> [AZURE.NOTE] 경우에 따라 SDK는 **Hostname** 또는 **네임스페이스** 값을 필요로 합니다. 이 경우 **이벤트 허브 호환 끝점**에서 구성표를 제거합니다. 예를 들어 이벤트 허브와 호환 가능한 끝점이 **sb://iothub-ns-myiothub-1234.servicebus.windows.net/**인 경우 **호스트 이름**은 **iothub-ns-myiothub-1234.servicebus.windows.net**이고 **네임스페이스**는 **iothub-ns-myiothub-1234**입니다.

지정된 이벤트 허브에 연결할 수 있는 **ServiceConnect** 권한이 있는 공유 액세스 보안 정책을 사용할 수 있습니다.

이전의 정보를 사용하여 이벤트 허브 연결 문자열을 작성해야 하는 경우 다음과 같은 패턴을 사용합니다.

```
Endpoint={Event Hub-compatible endpoint};SharedAccessKeyName={iot hub policy name};SharedAccessKey={iot hub policy key}
```

다음은 IoT Hub를 노출하는 이벤트 허브와 호환 가능한 끝점으로 사용할 수 있는 SDK 및 통합의 목록입니다.

* [Java 이벤트 허브 클라이언트](https://github.com/hdinsight/eventhubs-client)
* [Apache Storm spout](../hdinsight/hdinsight-storm-develop-csharp-event-hub-topology.md). GitHub의 [spout 원본](https://github.com/apache/storm/tree/master/external/storm-eventhubs)을 볼 수 있습니다.
* [Apache Spark 통합](../hdinsight/hdinsight-apache-spark-csharp-apache-zeppelin-eventhub-streaming.md)

## 장치 ID 레지스트리

각 IoT Hub에는 진행 중인 클라우드-장치 메시지를 포함 하는 큐와 같은 서비스에 장치 단위 리소스를 만드는 데 사용하는 장치 ID 레지스트리가 있습니다. 또한 [액세스 제어](#accesscontrol) 섹션에서 설명한 대로 장치 지향 끝점에 대한 액세스를 허용합니다.

높은 수준에서 장치 ID 레지스트리는 장치 ID 리소스의 REST를 지원하는 컬렉션입니다. 다음 섹션에서는 레지스트리가 ID에 허용하는 장치 ID 리소스 속성 및 작업을 자세히 설명합니다.

> [AZURE.NOTE] 장치 ID 레지스트리와 상호작용 하는 데 사용할 수 있는 HTTP 프로토콜 및 SDK에 대한 자세한 내용은 [IoT Hub API 및 SDK][lnk-apis-sdks]를 참조하세요.

### 장치 ID 속성 <a id="deviceproperties"></a>

장치 ID는 다음 속성을 사용하여 JSON 문서로 표시됩니다.

| 속성 | 옵션 | 설명 |
| -------- | ------- | ----------- |
| deviceId | 필요한 경우 업데이트에서 읽기 전용입니다. | ASCII 7 비트 영숫자 문자 + `{'-', ':', '.', '+', '%', '_', '#', '*', '?', '!', '(', ')', ',', '=', '@', ';', '$', '''}`의 대/소문자 구분 문자열(최대 128자 길이)입니다. |
| generationId | 필요한 경우 읽기 전용 | 허브에서 생성된 대/소문자 구분 문자열은 최대 128자입니다. 삭제되고 다시 만들 때와 동일한 **deviceId**로 장치를 구분하는 데 사용됩니다. |
| etag | 필요한 경우 읽기 전용 | [RFC7232][lnk-rfc7232] 당 장치 ID에 대해 약한 etag를 나타내는 문자열입니다.|
| auth | 선택 사항 | 인증 정보 및 보안 자료를 포함하는 복합 개체입니다. |
| auth.symkey | 선택 사항 | base64 형식으로 저장된 기본 및 보조 키를 포함하는 복합 개체입니다. |
| status | 필수 | **사용** 또는 **사용 안 함**으로 설정할 수 있습니다. **사용**이면 장치를 연결할 수 있습니다. **사용 안 함**이면 이 장치는 장치 연결 끝점에 액세스할 수 없습니다. |
| statusReason | 선택 사항 | 장치 ID 상태의 원인을 저장하는 128자 길이의 문자열입니다. UTF-8 문자를 모두 허용합니다. |
| statusUpdateTime | 읽기 전용 | 마지막 상태 업데이트된 날짜 및 시간입니다. |
| connectionState | 읽기 전용 | **연결됨** 또는 **연결 끊김**은 장치 연결 상태의 IoT Hub 뷰를 나타냅니다. **중요**: 이 필드는 개발/디버깅 용도로만 사용해야 합니다. 연결 상태는 AMQP 또는 MQTT를 사용하여 장치에 대해서만 업데이트됩니다. 또한 이는 프로토콜 수준의 ping(MQTT ping 또는 AMQP ping)을 기반으로 하고 있으며 최대 5분간의 지연이 있을 수 있습니다. 이러한 이유로, 연결되었지만 실제로는 연결이 끊긴 것으로 보고된 장치와 같이 거짓 긍정이 있을 수 있습니다. |
| connectionStateUpdatedTime | 읽기 전용 | 연결 상태가 마지막으로 업데이트된 날짜 및 시간입니다. |
| lastActivityTime | 읽기 전용 | 장치 연결이 마지막으로 연결되거나 메시지를 받거나 보낸 날짜 및 시간입니다. |

> [AZURE.NOTE] 연결 상태는 연결 상태의 IoT Hub 뷰만을 나타낼 수 있습니다. 이 상태에 대한 업데이트는 네트워크 상태 및 구성에 따라 지연될 수도 있습니다.

### 장치 ID 작업

IoT Hub 장치 ID 레지스트리는 다음과 같은 작업을 노출합니다.

* 장치 ID 만들기
* 장치 ID 업데이트
* ID로 장치 ID 검색
* 장치 ID 삭제
* 최대 1000개의 ID 목록
* 모든 ID를 Blob 저장소로 내보내기
* Blob 저장소에서 모든 ID를 가져오기

모든 작업은 [RFC7232][lnk-rfc7232]에 지정된 낙관적 동시성의 사용을 허용합니다.

> [AZURE.IMPORTANT] 허브의 ID 레지스트리에서 모든 ID를 검색하는 유일한 방법은 [내보내기](#importexport) 기능을 사용하는 것입니다.

IoT Hub 장치 ID 레지스트리:

- 응용 프로그램 메타 데이터가 없습니다.
- 키로서 **deviceId**를 사용하여 사전처럼 액세스할 수 있습니다.
- 표현 쿼리를 지원하지 않습니다.

IoT 솔루션에는 일반적으로 응용 프로그램 관련 메타데이터가 포함된 별도의 솔루션 관련 저장소가 있습니다. 예를 들어 스마트 건물 솔루션의 솔루션 관련 저장소는 온도 센서가 배포된 방을 기록합니다.

> [AZURE.IMPORTANT] 장치 관리 및 프로비전 작업에 대해서는 장치 ID 레지스트리만 사용해야 합니다. 런타임 시 처리량이 높은 작업은 장치 ID 레지스트리에서 수행하는 작업에 따라 달라지지 합니다. 예를 들어 명령을 보내기 전에 장치의 연결 상태를 검사하는 것은 지원되는 패턴이 아닙니다. 장치 ID 레지스트리에 대한 [제한율](#throttling)과 [장치 하트비트][lnk-guidance-heartbeat] 패턴을 확인해야 합니다.

### 장치 사용 안 함

레지스트리에서 ID의 **상태** 속성을 업데이트하여 장치를 사용하지 않을 수 있습니다. 일반적으로 두 가지 시나리오에 사용합니다.

- 오케스트레이션 과정을 프로비전하는 동안입니다. 자세한 내용은 [솔루션 설계 - 장치 프로비전][lnk-guidance-provisioning]을 참조하세요.
- 어떤 이유로든 손상되거나 권한이 없는 장치를 사용하도록 고려합니다.

### 장치 ID 내보내기 <a id="importexport"></a>

내보내기는 고객이 제공한 Blob 컨테이너를 사용하여 ID 레지스터에서 읽은 장치 ID 데이터를 저장하는 장기 실행 작업입니다.

[IoT Hub 리소스 공급자 끝점](#endpoints)에서 비동기 작업을 사용하여 IoT Hub의 ID 레지스트리에서 장치 ID를 대량으로 내보낼 수 있습니다.

다음은 내보내기 작업에 사용할 수 있는 작업입니다.

* 내보내기 작업 만들기
* 실행 중인 작업의 상태 검색
* 실행 중인 작업 취소

> [AZURE.NOTE] 각 허브는 지정된 시간에 실행되는 단일 작업만을 가질 수 있습니다.

API를 가져오고 내보내는 작업에 대한 자세한 정보는 [Azure IoT Hub - 리소스 공급자 API][lnk-resource-provider-apis]를 참조하세요.

가져오기 및 내보내기 작업 실행에 대한 자세한 내용은 [IoT Hub 장치 ID의 대량 관리][lnk-bulk-identity]를 참조하세요.

### 작업 내보내기

모든 내보내기 작업에는 다음과 같은 속성이 있습니다.

| 속성 | 옵션 | 설명 |
| -------- | ------- | ----------- |
| jobId | 시스템에서 생성된, 생성 시 무시된 | |
| creationTime | 시스템에서 생성된, 생성 시 무시된 | |
| endOfProcessingTime | 시스템에서 생성된, 생성 시 무시된 | |
| type | 읽기 전용 | **ExportDevices** |
| status | 시스템에서 생성된, 생성 시 무시된 | **큐에 넣음**, **시작**, **완료**, **실패** |
| 진행률 | 시스템에서 생성된, 생성 시 무시된 | 완료율의 정수 값 |
| outputBlobContainerURI | 모든 작업에 필요함 | Blob 컨테이너에 쓰기 액세스 권한이 있는 Blob 공유 액세스 서명 URI입니다([Blob 서비스로 SAS 만들기 및 사용][lnk-createuse-sas]을 참조). 이 작업은 작업 및 결과의 상태를 출력하는 데 사용됩니다. |
| excludeKeysInExport | 선택 사항 | **false**인 경우 키가 내보내기 출력에 포함되지 않고 그렇지 않으면 키는 **null**로 내보내 집니다. 기본값은 **false**입니다. |
| failureReason | 시스템에서 생성된, 생성 시 무시된 | 상태가 **실패**인 경우 원인을 포함하는 문자열입니다. |

작업을 내보내면 Blob 공유 액세스 서명 URI를 매개 변수로 인식합니다. 결과를 출력하는 작업을 사용하도록 Blob 컨테이너에 대한 쓰기 권한을 부여합니다.

작업은 출력 결과를 **devices.txt**라는 파일에 지정된 Blob 컨테이너에 기록합니다. 이 파일은 [장치 id 속성](#deviceproperties)에 지정된 JSON으로 직렬화된 장치 ID를 포함합니다. **excludeKeysInExport** 매개 변수가 **true**로 설정된 경우 **devices.txt** 파일에 있는 각 장치에 대한 인증 값은 **null**로 설정됩니다.

**예제**:

```
{"id":"devA","eTag":"MQ==","status":"enabled","authentication":{"symmetricKey":{"primaryKey":"123","secondaryKey":"123"}}}
{"id":"devB","eTag":"MQ==","status":"enabled","authentication":{"symmetricKey":{"primaryKey":"123","secondaryKey":"123"}}}
{"id":"devC","eTag":"MQ==","status":"enabled","authentication":{"symmetricKey":{"primaryKey":"123","secondaryKey":"123"}}}
```

### 장치 ID 가져오기

가져오기는 고객이 제공한 Blob 컨테이너의 데이터를 사용하여 장치 ID 데이터를 장치 ID 레지스터에 쓰는 장기 실행 작업입니다.

[IoT Hub 리소스 공급자 끝점](#endpoints)에서 비동기 작업을 사용하여 IoT Hub의 ID 레지스트리로 장치 ID를 대량으로 가져올 수 있습니다.

다음은 가져오기 작업에 사용할 수 있는 작업입니다.

* 가져오기 작업 만들기
* 실행 중인 작업의 상태 검색
* 실행 중인 작업 취소

> [AZURE.NOTE] 각 허브는 지정된 시간에 실행되는 단일 작업만을 가질 수 있습니다.

API를 가져오고 내보내는 작업에 대한 자세한 정보는 [Azure IoT Hub - 리소스 공급자 API][lnk-resource-provider-apis]를 참조하세요.

가져오기 및 내보내기 작업 실행에 대한 자세한 내용은 [IoT Hub 장치 ID의 대량 관리][lnk-bulk-identity]를 참조하세요.

### 가져오기 작업

모든 가져오기 작업에는 다음과 같은 속성이 있습니다.

| 속성 | 옵션 | 설명 |
| -------- | ------- | ----------- |
| jobId | 시스템에서 생성된, 생성 시 무시된 | |
| creationTime | 시스템에서 생성된, 생성 시 무시된 | |
| endOfProcessingTime | 시스템에서 생성된, 생성 시 무시된 | |
| type | 읽기 전용 | **ImportDevices** |
| status | 시스템에서 생성된, 생성 시 무시된 | **큐에 넣음**, **시작**, **완료**, **실패** |
| 진행률 | 시스템에서 생성된, 생성 시 무시된 | 완료율의 정수 값 |
| outputBlobContainerURI | 모든 작업에 필요함 | Blob 컨테이너에 쓰기 액세스 권한이 있는 Blob 공유 액세스 서명 URI입니다([Blob 서비스로 SAS 만들기 및 사용][lnk-createuse-sas]을 참조). 이 작업은 작업의 상태를 출력하는 데 사용됩니다. |
| inputBlobContainerURI | 필수 | Blob 컨테이너에 읽기 액세스 권한이 있는 Blob 공유 액세스 서명 URI입니다([Blob 서비스로 SAS 만들기 및 사용][lnk-createuse-sas]을 참조). 작업이 장치 정보를 읽고 이 Blob를 가져옵니다. |
| failureReason | 시스템에서 생성된, 생성 시 무시된 | 상태가 **실패**인 경우 원인을 포함하는 문자열입니다. |

작업을 가져오면 두 개의 Blob 공유 액세스 서명 URI를 매개 변수로 인식합니다. 하나는 Blob 컨테이너에 대한 읽기 액세스 권한을 부여하여 작업 상태를 출력할 수 있으며, 다른 하나는 Blob 컨테이너에 대한 읽기 액세스 권한을 부여하여 작업 입력 데이터를 읽을 수 있습니다.

작업은 **devices.txt**라는 파일에 지정된 Blob 컨테이너로부터 입력 데이터를 읽어옵니다. 이 파일은 [장치 id 속성](#deviceproperties)에 지정된 JSON으로 직렬화된 장치 ID를 포함합니다. **importMode** 속성을 추가하여 각 장치에 대한 기본 가져오기 동작을 재정의할 수 있습니다. 이 속성은 다음 값 중 하나를 사용할 수 있습니다.

| importMode | 설명 |
| -------- | ----------- |
| **createOrUpdate** | 지정된 **ID**를 가진 장치가 존재하지 않는 경우, 새로 등록됩니다. <br/>장치가 이미 존재하는 경우 **ETag** 값과 관계 없이 제공된 입력 데이터가 기존 정보를 덮어씁니다. |
| **create** | 지정된 **ID**를 가진 장치가 존재하지 않는 경우, 새로 등록됩니다. <br/>장치에 이미 존재하는 경우 오류가 로그 파일에 기록됩니다. |
| **update** | 지정된 **ID**를 가진 장치가 이미 존재하는 경우 **ETag** 값과 관계 없이 제공된 입력 데이터가 기존 정보를 덮어씁니다. <br/>장치가 존재하지 않는 경우 오류가 로그 파일에 기록됩니다. |
| **updateIfMatchETag** | 지정된 **ID**를 가진 장치가 이미 존재하는 경우 **ETag**가 일치하는 경우에만 제공된 입력 데이터가 기존 정보를 덮어씁니다. <br/>장치가 존재하지 않는 경우 오류가 로그 파일에 기록됩니다. <br/>**ETag**가 일치하지 않는 경우 오류가 로그 파일에 기록됩니다. |
| **createOrUpdateIfMatchETag** | 지정된 **ID**를 가진 장치가 존재하지 않는 경우, 새로 등록됩니다. <br/>장치가 이미 존재하는 경우 **ETag**가 일치하는 경우에만 제공된 입력 데이터가 기존 정보를 덮어씁니다. <br/>**ETag**가 일치하지 않는 경우 오류가 로그 파일에 기록됩니다. |
| **delete** | 지정된 **ID**를 가진 장치가 이미 존재하는 경우, **ETag** 값과 관계 없이 삭제됩니다. <br/>장치가 존재하지 않는 경우 오류가 로그 파일에 기록됩니다. |
| **deleteIfMatchETag** | 지정된 **ID**를 가진 장치가 이미 존재하는 경우, **ETag**가 일치하는 경우에만 삭제됩니다. 장치가 존재하지 않는 경우 오류가 로그 파일에 기록됩니다. <br/>ETag가 일치하지 않는 경우 불일치 오류가 로그 파일에 기록됩니다. |

**예제**:

```
{"id":"devA","eTag":"MQ==","status":"enabled","authentication":{"symmetricKey":{"primaryKey":"123","secondaryKey":"123"}}, "importMode":"delete"}
{"id":"devB","eTag":"MQ==","status":"enabled","authentication":{"symmetricKey":{"primaryKey":"123","secondaryKey":"123"}}, "importMode":"createOrUpdate"}
{"id":"devC","eTag":"MQ==","status":"enabled","authentication":{"symmetricKey":{"primaryKey":"123","secondaryKey":"123"}}, "importMode":"create"}
```

## 보안 <a id="security"></a>

이 섹션에서는 Azure IoT Hub를 보호하기 위한 옵션을 설명합니다.

### 액세스 제어<a id="accesscontrol"></a>

IoT Hub는 다음 *권한* 집합을 사용하여 각 IoT Hub의 끝점에 대한 액세스 권한을 부여합니다. 사용 권한은 기능에 따라 IoT Hub에 대한 액세스를 제한합니다.

* **RegistryRead**. 장치 ID 레지스트리에 대한 읽기 액세스 권한을 부여합니다. 자세한 내용은 [장치 ID 레지스트리](#device-identity-registry)를 참조하세요.
* **RegistryReadWrite**. 장치 ID 레지스트리에 대한 읽기 및 쓰기 액세스 권한을 부여합니다. 자세한 내용은 [장치 ID 레지스트리](#device-identity-registry)를 참조하세요.
* **ServiceConnect**. 클라우드 서비스 지향 통신 및 모니터링 중인 끝점에 대한 액세스를 부여합니다. 예를 들어 장치-클라우드 메시지를 받고 클라우드-장치 메시지를 보내며 해당 전달 승인을 검색할 수 있는 권한을 백 엔드 클라우드 서비스에 부여합니다.
* **DeviceConnect**. 장치 지향 통신 끝점에 대한 액세스를 부여합니다. 예를 들어 장치-클라우드 메시지를 보내고 클라우드-장치 메시지를 받을 수 있는 권한을 클라우드에 부여합니다. 이 권한은 장치에서 사용됩니다.

다음과 같은 방법으로 컨테이너 권한을 부여할 수 있습니다.

* **허브 수준 공유 액세스 정책**. 공유 액세스 정책은 이전의 섹션에 나열된 사용 권한의 조합에 권한을 부여할 수 있습니다. [Azure 포털][lnk-management-portal] 또는 프로그래밍 방식으로 [Azure IoT Hub 리소스 공급자 API][lnk-resource-provider-apis]를 사용하여 정책을 정의할 수 있습니다. 새로 만든 IoT Hub에는 다음과 같은 기본 정책이 있습니다.

    - *iothubowner*: 모든 권한이 있는 정책입니다.
    - *서비스*: **ServiceConnect** 사용 권한이 있는 정책입니다.
    - *장치*: **DeviceConnect** 사용 권한이 있는 정책입니다.
    - *registryRead*: **RegistryRead** 사용 권한이 있는 정책입니다.
    - *registryReadWrite*: **RegistryRead** 및 **RegistryWrite** 사용 권한이 있는 정책입니다.

* **장치 단위 보안 자격 증명**. 각 IoT Hub는 [장치 ID 레지스트리](#device-identity-registry)를 포함합니다. 이 레지스트리의 각 장치의 경우 해당 장치 끝점으로 범위가 지정된 **DeviceConnect** 사용 권한을 부여하는 보안 자격 증명을 구성할 수 있습니다.

**예제**. 일반적인 IoT 솔루션:
- 장치 관리 구성 요소가 *registryReadWrite* 정책을 사용합니다.
- 이벤트 프로세서 구성 요소는 *서비스* 정책을 사용합니다.
- 런타임 장치 비즈니스 논리 구성 요소는 *서비스* 정책을 사용합니다.
- 개별 장치는 IoT Hub의 ID 레지스트리에 저장된 자격 증명을 사용하여 연결합니다.

IoT Hub 보안 토픽에 대한 지침은 [솔루션 설계][lnk-guidance-security]에서 보안 섹션을 참조하세요.

### 인증

Azure IoT Hub는 공유 액세스 정책 및 장치 ID 레지스트리 보안 자격 증명에 대한 토큰을 확인하여 끝점에 대한 액세스를 부여합니다.

대칭 키와 같은 보안 자격 증명은 통신 중에 전송되지 않습니다.

> [AZURE.NOTE] Azure IoT Hub 리소스 공급자는 [Azure 리소스 관리자][lnk-azure-resource-manager]의 모든 공급자처럼 Azure 구독을 통해 보호됩니다.

보안 토큰을 생성 및 사용하는 방법에 대한 자세한 내용은 [IoT Hub 보안 토큰][lnk-sas-tokens] 문서를 참조하세요.

#### 프로토콜 세부 사항

지원되는 각 프로토콜(예: AMQP, MQTT 및 HTTP)은 다양한 방식으로 토큰을 전송합니다.

HTTP는 **권한 부여** 요청 헤더에서 유효한 토큰을 포함하여 인증을 구현합니다. 또한 **권한 부여**라는 쿼리 매개 변수는 토큰을 전송할 수 있습니다.

[AMQP][lnk-amqp]을 사용하는 경우 IoT Hub는 [SASL PLAIN][lnk-sasl-plain] 및 [AMQP 클레임 기반-보안][lnk-cbs]을 지원합니다.

AMQP 클레임-기반-보안의 경우 표준은 해당 토큰을 전송하는 방법을 지정합니다.

SASL PLAIN의 경우 **사용자 이름**은 다음이 될 수 있습니다.

* `{policyName}@sas.root.{iothubName}` 허브 수준 토큰의 경우입니다.
* `{deviceId}` 장치 범위 토큰의 경우입니다.

두 가지 경우 모두 암호 필드는 [IoT Hub 보안 토큰][lnk-sas-tokens] 문서에 설명된 토큰을 포함합니다.

MQTT를 사용하는 경우 CONNECT 패킷에는 사용자 이름 필드의 ClientId, {iothubhostname}/{deviceId}에 deviceId, 암호 필드에 SAS 토큰이 있습니다. {iothubhostname}은 IoT Hub의 전체 CName이어야 합니다(예: contoso.azure devices.net).

##### 예제: #####

사용자 이름(DeviceId는 대/소문자 구분): `iothubname.azure-devices.net/DeviceId`

암호(장치 탐색기를 통해 SAS 생성): `SharedAccessSignature sr=iothubname.azure-devices.net%2fdevices%2fDeviceId&sig=kPszxZZZZZZZZZZZZZZZZZAhLT%2bV7o%3d&se=1487709501`

> [AZURE.NOTE] [Azure IoT Hub SDK][lnk-apis-sdks]는 서비스에 연결할 때 토큰을 자동으로 생성합니다. 일부 경우에 SDK는 모든 프로토콜 또는 모든 인증 방법을 지원하지 않습니다.

#### CBS에 비해 SASL PLAIN

SASL PLAIN을 사용할 때 IoT Hub에 연결한 클라이언트는 각 TCP 연결에 단일 토큰을 사용할 수 있습니다. 토큰이 만료될 때 TCP 연결은 서비스에서 연결을 끊고 다시 연결을 트리거합니다. 이 동작은 응용 프로그램 백 엔드 구성 요소에 문제를 발생시키지 않는 반면 다음과 같은 이유로 장치 쪽 응용 프로그램에 막대한 피해를 줍니다.

*  게이트웨이가 일반적으로 많은 장치를 대신하여 연결됩니다. SASL PLAIN을 사용할 때 IoT Hub에 연결한 각 장치에 고유한 TCP 연결을 만들어야 합니다. 전원 및 네트워킹 리소스의 소비를 상당히 증가시키고 각 장치 연결의 대기 시간을 늘립니다.
* 각 토큰이 만료 후에 다시 연결하기 위해 리소스의 사용이 증가하여 리소스가 제한된 장치에 부정적인 영향을 미칩니다.

### 허브 수준 자격 증명의 범위 지정

제한된 리소스 URI로 토큰을 만들어 허브 수준 보안 정책의 범위를 지정할 수 있습니다. 예를 들어 장치에서 장치-클라우드 메시지를 보낼 끝점은 **/devices/{deviceId}/messages/events**입니다. 또한 **DeviceConnect** 사용 권한으로 허브 수준 공유 액세스 정책을 사용하여 resourceURI가 **/devices/{deviceId}**인 토큰에 서명하며 이는 장치 **deviceId**를 대신하여 장치를 보내는 데 사용 가능한 토큰을 만듭니다.

이 메커니즘은 [이벤트 허브 게시자 정책][lnk-event-hubs-publisher-policy]과 유사하며 [솔루션 설계][lnk-guidance-security]의 보안 섹션에서 설명한 것처럼 사용자 지정 인증 메서드를 구현하도록 합니다.

## 메시징

IoT Hub는 다음 통신을 위한 메시징 기본 요소를 제공합니다.
- [클라우드-장치](#c2d): 응용 프로그램 백 엔드(*서비스* 또는 *클라우드*)에서
- [장치-클라우드](#d2c): 장치에서 응용 프로그램 백 엔드로

IoT Hub 메시징 기능의 핵심 속성은 메시지의 안정성 및 내구성입니다. 이를 사용하여 장치 측면에서 일시적인 연결 및 클라우드 측면에서 이벤트 처리에 급증한 부하를 복원합니다. IoT Hub는 장치-클라우드 및 클라우드-장치 메시징 모두에 대해 *한 번 이상* 전달 보증을 구현합니다.

IoT Hub는 여러 장치 연결 프로토콜을 지원합니다(예: AMQP 및 HTTP/1). 프로토콜을 통해 원활한 상호 운용성을 지원하기 위해 IoT Hub는 모든 장치 지향 프로토콜에서 지원하는 일반적인 메시지 형식을 정의합니다.

### 메시지 형식 <a id="messageformat"></a>

IoT Hub 메시지는 다음을 구성합니다.

* *시스템 속성* 집합. IoT Hub를 해석하거나 설정하는 속성입니다. 이 집합은 미리 결정됩니다.
* *응용 프로그램 속성* 집합. 메시지 본문을 역직렬화할 필요 없이 응용 프로그램이 정의하고 액세스할 수 있는 문자열 속성의 사전입니다. IoT Hub는 절대로 이러한 속성을 수정하지 않습니다.
* 불투명한 이진 본문.

다른 프로토콜에서 메시지를 인코딩하는 방법에 대한 자세한 내용은 [IoT Hub API 및 SDK][lnk-apis-sdks]를 참조하세요.

IoT Hub 메시지에서 시스템 속성의 집합입니다.

| 속성 | 설명 |
| -------- | ----------- |
| MessageId | 사용자가 설정할 수 있는 메시지에 대한 식별자는 요청-회신 패턴에 일반적으로 사용됩니다. 형식: ASCII 7 비트 영숫자 문자 + `{'-', ':',’.', '+', '%', '_', '#', '*', '?', '!', '(', ')', ',', '=', '@', ';', '$', '''}`의 대/소문자 구분 문자열(최대 128자 길이)입니다. |
| 시퀀스 번호 | 숫자(장치 큐 별로 고유함)는 IoT Hub에서 각 클라우드-장치 메시지에 할당됩니다. |
| 받는 사람   | 대상을 지정하는 [클라우드-장치](#c2d) 메시지에 사용됩니다. |
| ExpiryTimeUtc | 메시지 만료 날짜 및 시간입니다. |
| EnqueuedTime | IoT Hub에서 메시지를 수신한 날짜 및 시간입니다. |
| CorrelationId | 일반적으로 요청-응답 패턴으로 요청의 MessageId가 포함된 응답 메시지의 String 속성입니다. |
| UserId | 메시지의 원본을 지정하는 데 사용합니다. 메시지가 IoT Hub에서 생성되면 `{iot hub name}`로 설정합니다. |
| Ack | 장치에서 소비하는 메시지의 결과로 피드백 메시지를 생성하는 IoT Hub를 요청하려면 클라우드-장치 메시지에서 사용합니다. 가능한 값: **없음** (기본값): 피드백 메시지가 생성되지 않습니다. **양수**: 메시지가 완료된 경우 피드백 메시지를 받습니다. **음수**: 장치에서 완료되지 않고 메시지가 만료(또는 최대 전달 횟수에 도달)하면 피드백 메시지를 받습니다 **전체**: 양수 및 음수 모두입니다. 자세한 내용은 [메시지 피드백](#feedback)을 참조하세요. |
| ConnectionDeviceId | IoT Hub에서 장치-클라우드 메시지에 설정됩니다. 메시지를 보낸 장치의 **deviceId**를 포함합니다. |
| ConnectionDeviceGenerationId | IoT Hub에서 장치-클라우드 메시지에 설정됩니다. 메시지를 보낸 장치의 **generationId**를 포함합니다.([장치 ID 속성](#deviceproperties) 당) |
| ConnectionAuthMethod | IoT Hub에서 장치-클라우드 메시지에 설정됩니다. 메시지를 보내는 장치를 인증하는 데 사용되는 인증 방법에 대한 정보입니다. 자세한 내용은 [장치-클라우드 스푸핑 방지](#antispoofing)를 참조하세요.|

### 통신 프로토콜을 선택합니다. <a id="amqpvshttp"></a>

IoT Hub는 장치 측 통신을 위해 [AMQP][lnk-amqp], WebSocket을 통한 AMQP, MQTT 및 HTTP/1 프로토콜을 지원합니다. 사용을 고려한 고려 사항 목록은 다음과 같습니다.

* **클라우드-장치 패턴**. HTTP/1에는 서버 푸시를 구현하는 효율적인 방법이 없습니다. 이와 같이 HTTP/1을 사용하는 경우 장치는 클라우드-장치 메시지에 IoT Hub를 폴링합니다. 장치와 IoT Hub 모두에 매우 비효율적입니다. HTTP/1을 사용하는 경우 현재 지침은 25분 이상마다 각 장치가 폴링하도록 해야 합니다. 반면에 AMQP 및 MQTT는 클라우드-장치 메시지를 받을 때 서버 푸시를 지원하고 IoT Hub에서 장치에 메시지를 즉각적으로 푸시할 수 있도록 합니다. 전달 대기 시간이 중요한 경우 AMQP 또는 MQTT는 가장 사용하기 적합한 프로토콜입니다. 반면에 거의 연결되지 않은 장치의 경우 HTTP/1도 작동합니다.
* **현장 게이트웨이**. HTTP/1 및 MQTT를 사용하는 경우 동일한 TLS 연결을 사용하여 여러 장치(각각 장치별 자격 증명 보유)를 연결할 수 없습니다. 필드 게이트웨이에 연결된 각 장치에 대해 필드 게이트웨이와 IoT Hub 간에 하나의 TLS 연결이 필요하므로 [필드 게이트웨이 시나리오][lnk-azure-gateway-guidance]를 구현할 때 이러한 프로토콜을 따르는 것은 좋은 방법이 아닙니다.
* **낮은 리소스 장치**. MQTT 및 HTTP/1 라이브러리는 AMQP 라이브러리보다 적은 공간을 차지합니다. 이와 같이 장치에 리소스가 적으면(예: 1Mb RAM 보다 작음) 이러한 프로토콜은 사용할 수 있는 유일한 프로토콜 구현일 수도 있습니다.
* **네트워크 통과**. MQTT 표준은 포트 8883에서 수신 대기합니다. 비 HTTP 프로토콜에 접근할 수 있는 네트워크에서 문제를 일으킬 수 있습니다. HTTP와 AMQP(Websockets를 통해) 모두 이 시나리오에서 사용할 수 있습니다.
* **페이로드 크기**. AMQP 및 MQTT는 HTTP/1보다 더욱 압축된 바이너리 프로토콜입니다.

높은 수준에서는 사용 가능할 때마다 AMQP(또는 Websockets을 통한 AMQP)를 사용해야 하며, 리소스 제약 조건이 AMQP 사용을 차단하는 경우 MQTT만 사용해야 합니다. HTTP/1은 네트워크 통과 및 네트워크 구성 모두가 MQTT 및 AMQP 사용을 차단하는 경우에만 사용해야 합니다. 또한 HTTP/1을 사용하는 경우 각 장치는 25분 이상마다 클라우드-장치에 대해 폴링합니다.

> [AZURE.NOTE] 개발하는 동안에는 25분 보다 좀 더 자주 폴링을 하도록 할 수도 있습니다.

#### MQTT 지원에 대한 참고 사항
IoT Hub는 다음과 같은 제한 사항과 특정 동작으로 MQTT v3.1.1 프로토콜을 구현합니다.

  * **QoS 2는 지원되지 않습니다**: 장치 클라이언트가 **QoS 2**의 메시지를 게시하는 경우, IoT Hub는 네트워크 연결을 닫습니다. 장치 클라이언트가 **QoS 2**의 토픽을 구독하는 경우, IoT Hub는 **SUBACK** 패킷에서 최대 QoS level 1을 부여합니다.
  * **보존**: 장치 클라이언트가 RETAIN 플래그가 1로 설정된 메시지를 게시하는 경우, IoT Hub는 **x-opt-retain** 응용 프로그램 속성을 메시지에 추가합니다. 즉, IoT Hub는 보관 메시지를 유지하지 않고 백 엔드 응용 프로그램에 전달합니다.

최종 고려 사항으로 IoT Hub와 직접 인터페이스하는 고성능 사용자 지정 프로토콜 게이트웨이를 배포할 수 있도록 [Azure IoT 프로토콜 게이트웨이][lnk-azure-protocol-gateway]를 검토해야 합니다. Azure IoT 프로토콜 게이트웨이를 사용하면 brownfield MQTT 배포를 수용하는 장치 프로토콜 또는 기타 사용자 지정 프로토콜을 사용자 지정할 수 있습니다. 이 방법의 단점은 프로토콜 게이트웨이를 자체 호스팅하고 운영해야 한다는 점입니다.

### 장치-클라우드 <a id="d2c"></a>

[끝점](#endpoints) 섹션에 설명된 대로 장치-클라우드 메시지는 장치 지향 끝점(**/devices/{deviceId}/messages/events**)을 통해 보내지고 서비스 지향 끝점(**/messages/events**)을 통해 수신합니다. 즉, [이벤트 허브][lnk-event-hubs]와 호환 가능합니다. 따라서 표준 이벤트 허브 통합 및 SDK를 사용하여 장치-클라우드 메시지를 받을 수 있습니다.

IoT Hub는 IoT Hub의 장치-클라우드 메시지가 [서비스 버스][lnk-servicebus] *메시지* 보다 이벤트 허브의 *이벤트*와 비슷하기에 [이벤트 허브][lnk-event-hubs]와 비슷한 방식으로 장치-클라우드 메시징을 구현합니다.

이 구현에는 다음과 같은 의미가 있습니다.

* 이벤트 허브 *이벤트*와 유사하게 장치-클라우드 메시지는 최대 7일 동안 IoT Hub에서 지속되고 보관됩니다.([장치-클라우드 구성 옵션](#d2cconfiguration) 참조)
* 장치-클라우드 메시지는 생성 시간에 설정된 고정된 파티션 집합에서 분할됩니다([장치-클라우드 구성 옵션](#d2cconfiguration) 참조).
* 이벤트 허브와 유사하게 장치-클라우드 메시지를 읽는 클라이언트는 분할 및 검사점을 처리해야 합니다. [이벤트 허브 - 이벤트 사용][lnk-event-hubs-consuming-events]을 참조하세요.
* 이벤트 허브 이벤트와 마찬가지로 장치-클라우드 메시지는 최대 크기 256KB이며 최적화하기 위해 일괄 처리로 그룹화할 수 있습니다. 일괄 처리는 최대 256KB이며 최대 500개의 메시지일 수 있습니다.

그러나 IoT Hub 장치-클라우드 및 이벤트 허브 간에 몇 가지 중요한 차이가 있습니다.

* [보안](#security) 섹션에서 설명한 것처럼 IoT Hub는 장치 단위 인증 및 액세스 제어가 가능합니다.
* 이벤트 허브가 네임스페이스 당 5000개의 AMQP 연결로 제한되는 반면 IoT Hub는 동시에 연결된 장치를 수 백만 대까지 사용할 수 있습니다.([할당량 및 제한](#throttling) 참조)
* IoT Hub를 사용하면 **PartitionKey**를 사용하는 임의의 분할을 할 수 없습니다. 장치-클라우드 메시지는 **deviceId**의 시작에 따라 분할됩니다.
* IoT Hub를 확장하는 것은 이벤트 허브와 약간 다릅니다. 자세한 내용은 [IoT Hub 확장][lnk-guidance-scale]을 참조하십시오.

이말이 모든 시나리오에서 IoT Hub가 이벤트 허브를 대체한다는 의미는 아닙니다. 예를 들어 이벤트 처리 계산 일부에서 데이터 스트림을 분석하기 전에 다른 속성 또는 필드와 관련하여 이벤트를 다시 분할할 필요가 있습니다. 이 경우 이벤트 허브를 사용하여 스트림 처리 파이프라인의 두 부분을 분리할 수 있습니다. 자세한 내용은 [Azure 이벤트 허브 개요][lnk-eventhub-partitions]의 *파티션*을 참조하세요.

장치-클라우드 메시징을 사용하는 방법에 대한 세부 정보는 [IoT Hub API 및 SDK][lnk-apis-sdks]를 참조하세요.

> [AZURE.NOTE] HTTP를 사용하여 장치-클라우드 메시지를 보낼 때 다음 문자열은 ASCII 문자만 포함할 수 있습니다. 시스템 속성 값 및 응용 프로그램 속성 이름 및 값.

#### 비-원격 분석 트래픽

대부분의 경우 원격 분석 데이터 요소 외에도 장치는 응용 프로그램 비즈니스 논리 계층에서 실행 및 처리를 필요로 하는 메시지 및 요청도 보냅니다. 예를 들어 백 엔드에서 특정 동작을 트리거해야 하는 중요한 알림 또는 백 엔드에서 전송된 명령에 대한 장치 응답입니다.

이러한 종류의 메시지를 처리하는 가장 좋은 방법에 대한 자세한 내용은 [장치-클라우드 처리][lnk-guidance-d2c-processing]를 참조하세요.

#### 장치-클라우드 구성 옵션 <a id="d2cconfiguration"></a>

IoT Hub는 다음 속성을 노출하여 장치-클라우드 메시징을 제어합니다.

* **분할 개수**. 이 속성은 생성 시 설정하여 장치-클라우드 이벤트 수집에 대한 파티션 수를 정의합니다.
* **보존 시간**. 이 속성은 장치-클라우드 메시지에 대한 보존 시간을 지정합니다. 기본값은 1일이지만 7일로 늘릴 수 있습니다.

또한 이벤트 허브와 유사하게 IoT Hub를 사용하면 장치-클라우드에서 소비자 그룹의 관리가 끝점을 받을 수 있습니다.

[Azure 포털][lnk-management-portal]을 사용하거나 [Azure IoT Hub - 리소스 공급자 API][lnk-resource-provider-apis]를 통해 프로그래밍 방식으로 해당 속성을 모두 수정할 수 있습니다.

#### 스푸핑 방지 속성 <a id="antispoofing"></a>

장치-클라우드 메시지에서 스푸핑된 장치를 피하려면 IoT Hub는 다음 속성을 사용하여 모든 메시지를 보여줍니다.

* **ConnectionDeviceId**
* **ConnectionDeviceGenerationId**
* **ConnectionAuthMethod**

처음 두 가지는 [장치 ID 속성](#deviceproperties) ekd 원래 장치의 **deviceId** 및 **generationId**를 포함합니다.

**ConnectionAuthMethod** 속성은 다음 속성을 가진 JSON으로 직렬화된 개체를 포함합니다.

```
{
  "scope": "{ hub | device}",
  "type": "{ symkey | sas}",
  "issuer": "iothub"
}
```

### 클라우드-장치 <a id="c2d"></a>

[끝점](#endpoints) 섹션에 설명한 대로 서비스 끝점(**/messages/devicebound**)을 통해 클라우드-장치 메시지를 보낼 수 있고 장치는 장치 특정 끝점(**/devices/{deviceId}/messages/devicebound**)을 통해 해당 메시지를 받을 수 있습니다.

각 클라우드-장치 메시지는 단일 장치를 대상으로 설정되며 **to** 속성을 **/devices/{deviceId}/messages/devicebound**로 설정합니다.

**중요**: 각 장치 큐는 최대 50개의 클라우드-장치 메시지를 보유할 수 있습니다. 동일한 장치에 더 많은 메시지를 보내려고 하면 오류가 발생합니다.

> [AZURE.NOTE] 클라우드-장치 메시지를 보낼 때 다음 문자열은 ASCII 문자만 포함할 수 있습니다. 시스템 속성 값 및 응용 프로그램 속성 이름 및 값.

#### 메시지 수명 주기 <a id="message lifecycle"></a>

*한번 이상* 전달 보장을 구현하기 위해 클라우드-장치 메시지는 장치 단위 큐에 지속되고 장치는 IoT Hub가 큐에서 해당 장치를 제거하기 위해 작업을 명시적으로 *완료*했다고 인정해야 합니다. 연결 및 장치 오류로부터 복구를 보장합니다.

다음 다이어그램은 클라우드-장치 메시지에 대한 수명 주기 상태 Graph를 보여줍니다.

![클라우드-장치 메시지 수명 주기][img-lifecycle]

서비스가 메시지를 보낼 때 *큐에 대기*로 간주합니다. 장치가 메시지를 *수신*하려면 IoT Hub는 동일한 장치에 있는 다른 스레드가 다른 메시지를 받기 시작하도록 하기 위해 메시지를 *잠급니다*(상태는 **보이지 않음**으로 설정). 장치 스레드가 메시지의 처리를 완료하면 IoT Hub에 메시지를 *완료*했다고 알립니다.

또한 장치는 다음을 수행할 수 있습니다.
- 메시지를 *거부*하며 이로 인해 IoT Hub는 메시지를 **효력 상실** 상태로 설정합니다.
- 메시지를 *중단*하며 이로 인해 IoT Hub는 상태가 **큐에 넣음**으로 설정된 메시지를 큐에 다시 넣습니다.

스레드는 IoT Hub에 알리지 않고 메시지를 처리하는 데 실패할 수 있습니다. 이 경우에 *표시(또는 잠금) 시간 초과*의 기본값인 1분 후에 메시지는 **보이지 않음** 상태에서 **큐에 대기** 상태로 자동으로 전환됩니다. 메시지는 IoT Hub의 *최대 배달 횟수* 속성에 지정된 최대 지정된 횟수만큼 **큐에 대기**와 **보이지 않음** 상태 간에 전환될 수 있습니다. 해당 전환 횟수 후에 IoT Hub는 메시지의 상태를 **효력 상실**로 설정합니다. 마찬가지로 해당 만료 시간 후에 IoT Hub는 메시지의 상태를 **효력 상실**로 설정합니다([TTL(Time to Live)](#ttl) 참조).

클라우드-장치 메시지에 대한 자습서는 [Azure IoT Hub 클라우드-장치 메시지 시작][lnk-getstarted-c2d-tutorial]를 참조하세요. 다른 API 및 SDK가 클라우드-장치 기능을 어떻게 노출하는지에 대한 참조 항목은 [IoT Hub API 및 SDK][lnk-apis-sdks]를 참조하세요.

> [AZURE.NOTE] 일반적으로 클라우드-장치 메시지는 메시지의 손실이 응용 프로그램 논리에 영향을 줄 때마다 완료합니다. 다양한 시나리오에서 발생할 수 있습니다. 예를 들어 메시지 콘텐츠가 로컬 저장소에 성공적으로 지속되거나 작업이 성공적으로 실행되거나 메시지가 손실해도 응용 프로그램의 기능에 영향을 주지 않는 임시 정보를 전달합니다. 장기 실행 작업의 경우 로컬 저장소에서 작업을 지속적으로 설명한 후에 클라우드-장치 메시지를 완료한 다음 작업의 진행 상황의 여러 단계에서 하나 이상의 장치-클라우드 메시지로 응용 프로그램 백 엔드를 알릴 수 있습니다.

#### TTL(Time to live) <a id="ttl"></a>

모든 클라우드-장치 메시지에는 만료 시간이 있습니다. 서비스에서 명시적으로 설정할 수 있거나(**ExpiryTimeUtc** 속성에) 또는 IoT Hub 속성처럼 지정된 기본 *TTL(Time To Live)*을 사용하여 IoT Hub에서 설정됩니다. [클라우드-장치 구성 옵션](#c2dconfiguration)을 참조하세요.

> [AZURE.NOTE] 메시지 만료를 활용하는 일반적인 방법은 연결되지 않은 장치에 메시지를 전송하지 않도록 값이 유효한 짧은 시간을 설정하는 것입니다. 이렇게 하면 훨씬 더 효율적으로 유지하면서 장치 연결 상태를 유지 관리하는 것과 동일한 결과를 얻을 수 있습니다. 또한 메시지 승인을 요청하여 장치가 메시지를 받을 수 있거나 온라인 상태가 아니거나 실패한 IoT Hub의 알림을 받는 것도 가능합니다.

#### 메시지 피드백 <a id="feedback"></a>

클라우드-장치 메시지를 보낼 때 서비스는 해당 메시지의 최종 상태에 대한 메시지 단위 피드백을 전달하도록 요청할 수 있습니다.

- **Ack** 속성을 **양수**로 설정하면 클라우드-장치 메시지가 **완료** 상태에 도달하는 경우에만 IoT Hub가 피드백 메시지를 생성합니다.
- **Ack** 속성을 **음수**로 설정하면 클라우드-장치 메시지가 **효력 상실** 상태에 도달하는 경우에만 IoT Hub가 피드백 메시지를 생성합니다.
- **Ack** 속성을 **전체**로 설정하면 IoT Hub가 피드백 메시지를 생성합니다.

> [AZURE.NOTE] **Ack**가 **전체**인데 피드백 메시지가 수신되지 않으면 피드백 메시지가 만료되어 원본 메시지에서 변경된 내용을 서비스에서 알 수 없다는 것을 의미합니다. 실제로 서비스는 만료되기 전에 피드백을 처리할 수 있는지 확인해야 합니다. 최대 만료 시간은 2일이므로 오류가 발생한 경우 서비스를 가동하고 실행할 시간이 충분해야 합니다.

[끝점](#endpoints)에 설명된 대로 IoT Hub는 서비스 끝점(**/messages/servicebound/feedback**)을 통해 사용자 의견을 메시지로 전달합니다. 피드백에 대한 수신 의미 체계는 클라우드-장치 메시지의 경우와 동일하며 동일한 [메시지 수명 주기](#메시지 수명 주기)를 갖습니다. 가능한 경우 메시지 피드백은 다음 형식으로 단일 메시지에서 일괄 처리됩니다.

장치가 피드백 끝점에서 검색한 각 메시지에는 다음과 같은 속성이 있습니다.

| 속성 | 설명 |
| -------- | ----------- |
| EnqueuedTime | 메시지를 만든 시간을 나타내는 타임스탬프입니다. |
| UserId | `{iot hub name}` |
| ContentType | `application/vnd.microsoft.iothub.feedback.json` |

본문은 각각 다음과 같은 속성이 있는 레코드의 JSON으로 직렬화된 배열입니다.

| 속성 | 설명 |
| -------- | ----------- |
| EnqueuedTimeUtc | 메시지의 결과가 발생하는 경우를 나타내는 타임스탬프입니다. 예를 들어 완료된 장치 또는 만료된 메시지입니다. |
| OriginalMessageId | 이 피드백 정보가 관련된 클라우드-장치 메시지의 **MessageId**입니다. |
| StatusCode | 필수 정수입니다. IoT Hub에 의해 생성된 피드백 메시지에서 사용됩니다. <br/> 0 = 성공 <br/> 1 = 메시지 만료 <br/> 2 = 최대 배달 횟수 초과<br/> 3 = 메시지 거부 |
| 설명 | **StatusCode**에 대한 문자열 값입니다. |
| DeviceId | 피드백의 해당 부분이 관련된 클라우드-장치 메시지에서 대상 장치의 **DeviceId**입니다. |
| DeviceGenerationId | 피드백의 해당 부분이 관련된 클라우드-장치 메시지에서 대상 장치의 **DeviceGenerationId**입니다. |


**중요**. 서비스는 원본 메시지와 해당 피드백을 상호 연결하기 위해 클라우드-장치 메시지에 대한 **MessageId**를 지정해야 합니다.

**예제**. 피드백 메시지의 예제 본문입니다.

```
[
  {
    "OriginalMessageId": "0987654321",
    "EnqueuedTimeUtc": "2015-07-28T16:24:48.789Z",
    "StatusCode": 0
    "Description": "Success",
    "DeviceId": "123",
    "DeviceGenerationId": "abcdefghijklmnopqrstuvwxyz"
  },
  {
    ...
  },
  ...
]
```

#### 클라우드-장치 구성 옵션 <a id="c2dconfiguration"></a>

각 IoT Hub는 클라우드-장치 메시징에 다음 구성 옵션을 노출합니다.

| 속성 | 설명 | 범위 및 기본값 |
| -------- | ----------- | ----------------- |
| defaultTtlAsIso8601 | 클라우드-장치 메시지에 대한 기본 TTL | 최대 2D(최소 1 분)까지 ISO\_8601 간격입니다. 기본값은 1시간입니다. |
| maxDeliveryCount | 클라우드-장치 장치 단위 큐에 대한 최대 전달 수입니다. | 1에서 100까지입니다. 기본값은 10입니다. |
| feedback.ttlAsIso8601 | 서비스 바인딩 피드백 메시지에 대한 보존 기간입니다. | 최대 2D(최소 1 분)까지 ISO\_8601 간격입니다. 기본값은 1시간입니다. |
| feedback.maxDeliveryCount | 피드백 큐에 대한 최대 전달 수입니다. | 1에서 100까지입니다. 기본값은 100입니다. |

자세한 내용은 [IoT Hub 관리][lnk-manage]를 참조하세요.

## 할당량 및 제한 <a id="throttling"></a>

각 Azure 구독에는 최대 10 IoT Hub가 있을 수 있습니다.

각 IoT Hub는 특정 SKU에서 특정한 단위 수로 프로비전됩니다. 자세한 내용은 [Azure IoT Hub 가격 책정][lnk-pricing]을 참조하세요. SKU와 단위 수는 보낼 수 있는 메시지의 최대 일일 할당량을 결정합니다.

또한 SKU는 IoT Hub가 모든 작업에 강제로 적용하는 조정 제한을 결정합니다.

### 작업 제한

작업 제한은 분 범위에 적용하고 남용을 방지하고자 하는 속도 제한입니다. IoT Hub는 가능한 경우 오류를 반환 하지 않으려 하지만 제한이 너무 오랫동안 위반된 경우 예외를 반환하기 시작합니다.

강제로 적용된 제한 목록은 다음과 같습니다. 값은 개별 허브라고 합니다.

| 제한 | 허브 단위 값 |
| -------- | ------------- |
| ID 레지스트리 작업(만들기, 검색, 나열, 업데이트 및 삭제) | 100/분/단위, 최대 5000/분까지 |
| 장치 연결 | 120/초/단위(S2의 경우), 12/초/단위(S1의 경우) <br/>100/초의 최소값. <br/> 예를 들어 2개의 S1 단위는 2*12 = 24/초이지만 단위 전체에 대해서는 최소 100/초입니다. S1 단위가 9개인 경우 단위 전체에 대한 값은 108/초(9*12)입니다. |
| 장치->클라우드 보내기 | 120/초/단위(S2의 경우), 12/초/단위(S1의 경우) <br/>100/초의 최소값. <br/> 예를 들어 2개의 S1 단위는 2*12 = 24/초이지만 단위 전체에 대해서는 최소 100/초입니다. S1 단위가 9개인 경우 단위 전체에 대한 값은 108/초(9*12)입니다. |
| 클라우드-장치 보내기 | 100/분/단위 |
| 클라우드-장치 받기 | 1000/분/단위 |

**참고**. 지정된 시간에 IoT Hub에 프로비전된 단위 수를 늘려 할당량이나 조정 제한을 증가시킬 수 있습니다.

**중요**: ID 레지스트리 작업은 장치 관리 및 프로비전 시나리오에서 런타임 사용을 위한 것입니다. 많은 수의 장치 ID 읽기 또는 업데이트는 [가져오기/내보내기 작업](#importexport)을 통해 지원됩니다.

## 다음 단계

IoT Hub 개발의 개요를 살펴보았습니다. 자세한 내용을 보려면 다음 링크를 따라가십시오.

- [IoT Hub 시작(자습서)][lnk-get-started]
- [OS 플랫폼 및 하드웨어 호환성][lnk-compatibility]
- [Azure IoT 개발자 센터][lnk-iotdev]
- [솔루션 디자인][lnk-guidance]

[이벤트 허브 - 이벤트 프로세서 호스트]: http://blogs.msdn.com/b/servicebus/archive/2015/01/16/event-processor-host-best-practices-part-1.aspx

[Azure 포털]: https://portal.azure.com

[img-endpoints]: ./media/iot-hub-devguide/endpoints.png
[img-lifecycle]: ./media/iot-hub-devguide/lifecycle.png
[img-eventhubcompatible]: ./media/iot-hub-devguide/eventhubcompatible.png

[lnk-compatibility]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/tested_configurations.md
[lnk-apis-sdks]: https://github.com/Azure/azure-iot-sdks/blob/master/readme.md
[lnk-pricing]: https://azure.microsoft.com/pricing/details/iot-hub
[lnk-resource-provider-apis]: https://msdn.microsoft.com/library/mt548492.aspx

[lnk-sas-tokens]: iot-hub-sas-tokens.md
[lnk-azure-gateway-guidance]: iot-hub-guidance.md#field-gateways
[lnk-guidance-provisioning]: iot-hub-guidance.md#provisioning
[lnk-guidance-scale]: iot-hub-scaling.md
[lnk-guidance-security]: iot-hub-guidance.md#customauth
[lnk-guidance-heartbeat]: iot-hub-guidance.md#heartbeat

[lnk-azure-protocol-gateway]: iot-hub-protocol-gateway.md
[lnk-get-started]: iot-hub-csharp-csharp-getstarted.md
[lnk-guidance]: iot-hub-guidance.md
[lnk-getstarted-c2d-tutorial]: iot-hub-csharp-csharp-c2d.md

[lnk-amqp]: https://www.amqp.org/
[lnk-mqtt]: http://mqtt.org/
[lnk-websockets]: https://tools.ietf.org/html/rfc6455
[lnk-arm]: ../resource-group-overview.md
[lnk-azure-resource-manager]: https://azure.microsoft.com/documentation/articles/resource-group-overview/
[lnk-cbs]: https://www.oasis-open.org/committees/download.php/50506/amqp-cbs-v1%200-wd02%202013-08-12.doc
[lnk-createuse-sas]: ../storage-dotnet-shared-access-signature-part-2/
[lnk-event-hubs-publisher-policy]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-99ce67ab
[lnk-event-hubs]: http://azure.microsoft.com/documentation/services/event-hubs/
[lnk-event-hubs-consuming-events]: ../event-hubs/event-hubs-programming-guide.md#event-consumers
[lnk-guidance-d2c-processing]: iot-hub-csharp-csharp-process-d2c.md
[lnk-management-portal]: https://portal.azure.com
[lnk-rfc7232]: https://tools.ietf.org/html/rfc7232
[lnk-sasl-plain]: http://tools.ietf.org/html/rfc4616
[lnk-servicebus]: http://azure.microsoft.com/documentation/services/service-bus/
[lnk-tls]: https://tools.ietf.org/html/rfc5246
[lnk-iotdev]: https://azure.microsoft.com/develop/iot/
[lnk-bulk-identity]: iot-hub-bulk-identity-mgmt.md
[lnk-eventhub-partitions]: ../event-hubs/event-hubs-overview.md#partitions
[lnk-manage]: iot-hub-manage-through-portal.md

<!---HONumber=AcomDC_0323_2016-->