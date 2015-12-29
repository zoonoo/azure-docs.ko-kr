<properties
 pageTitle="IoT Hub에 대한 개발자 가이드 항목 | Microsoft Azure"
 description="IoT Hub 끝점, 보안, 장치 ID 레지스트리 및 메시징을 포함하는 Azure IoT Hub 개발자 가이드"
 services="iot-hub"
 documentationCenter=".net"
 authors="fsautomata"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="multiple"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="09/29/2015"
 ms.author="elioda"/>

# Azure IoT Hub 개발자 가이드

Azure IoT Hub는 수백만의 IoT 장치와 응용 프로그램 백 엔드 간에서 안정적이고 안전한 양방향 통신이 가능하도록 완전히 관리되는 서비스입니다.

Azure IoT Hub는 다음을 활성화합니다.

* 장치 단위 보안 자격 증명 및 액세스 제어를 사용하여 통신을 보호하고
* 신뢰할 수 있는 장치-클라우드 및 클라우드-장치 하이퍼 눈금 메시징을 제공하며
* 가장 인기 있는 언어 및 플랫폼에 대한 장치 라이브러리로 쉽게 장치에 연결합니다.

다음은 이 문서에서 설명하는 영역입니다.

- [끝점](#endpoints). 이 섹션에서는 각 IoT Hub가 런타임 및 관리 작업에 노출하는 다양한 끝점을 설명합니다.
- [장치 ID 레지스트리](#device-identity-registry). 이 섹션에서는 어떤 정보가 각 IoT Hub 장치 ID 레지스트리에 저장되고 어떻게 액세스 및 수정될 수 있는지 설명합니다.
- [보안](#security). 이 섹션에서는 IoT Hub 기능에 액세스 권한을 부여하는 데 사용하는 장치 및 클라우드 구성 요소 모두에 대한 보안 모델을 설명합니다.
- [메시징](#messaging). 이 섹션에서는 IoT Hub에서 노출되는 메시징 기능(장치-클라우드 및 클라우드-장치)을 설명합니다.
- [할당량 및 제한](#throttling). 이 섹션에서는 IoT Hub의 사용에 적용되는 할당량을 요약합니다.

## 끝점 <a id="endpoints"></a>

Azure IoT Hub는 다중 테넌트 서비스이며 다양한 행위자에게 기능을 노출합니다. 아래 다이어그램에서는 IoT Hub에서 노출되는 다양한 끝점을 보여줍니다.

![IoT Hub 끝점][img-endpoints]

끝점에 대한 설명은 다음과 같습니다.

* **리소스 공급자**: IoT Hub 리소스 공급자는 Azure 구독 소유자가 IoT Hub를 만들고 IoT Hub 속성을 업데이트하며 IoT Hub를 삭제하도록 하는 [Azure 리소스 관리자][lnk-arm] 인터페이스를 노출합니다. IoT Hub 속성은 허브 수준 보안 정책을 제어합니다(반대로 장치 수준 액세스 제어). [액세스 제어](#accesscontrol)) 및 클라우드-장치 및 장치-클라우드 메시징에 대한 기능 옵션을 참조하세요. 또한 리소스 공급자는 [장치 ID를 내보내도록](#importexport) 합니다.
* **장치 ID 관리**: 각 IoT Hub는 HTTP REST 끝점 집합을 노출하여 장치 ID를 관리합니다.(만들기, 검색, 업데이트 및 삭제) 장치 ID는 장치 인증 및 액세스 제어에 사용됩니다. 자세한 내용은 [장치 ID 레지스트리](#device-identity-registry)를 참조하세요.
* **장치 끝점**: 장치 ID 레지스트리에 프로비전된 각 장치의 경우 IoT Hub는 해당 장치 간의 통신에 사용되는 끝점의 집합을 노출합니다. 이러한 끝점은 HTTP 및 [AMQP][lnk-amqp] 모두에서 현재 노출됩니다.
    - *장치-클라우드 메시지 보내기*. 이 끝점이 장치-클라우드로 메시지를 보내는데 사용됩니다. 자세한 내용은 [장치-클라우드 메시징](#d2c)을 참조하세요.
    - *클라우드-장치 메시지 받기*. 이 끝점은 장치에서 사용되어 대상으로 한 클라우드-장치 메시지를 받습니다. 자세한 내용은 [클라우드-장치 메시징](#c2d)을 참조하세요.
* **서비스 끝점**: 각 IoT Hub는 응용 프로그램 백 엔드(*서비스*)에서 사용하는 끝점의 집합을 노출하여 장치와 통신합니다. 이러한 끝점은 현재 [AMQP][lnk-amqp] 프로토콜만을 사용하여 노출됩니다.
    - *장치-클라우드 메시지 받기*. 이 끝점은 [Azure 이벤트 허브][lnk-event-hubs]와 호환 가능하고 장치에서 보낸 모든 장치-클라우드 메시지를 읽는 데 사용할 수 있습니다. 자세한 내용은 [장치-클라우드 메시징](#d2c)을 참조하세요.
    - *클라우드-장치 메시지를 보내고 배달 승인 받기*. 이러한 끝점은 응용 프로그램 백 엔드가 안정적인 클라우드-장치 메시지를 보내고 해당 전달 또는 만료 승인을 수신하도록 합니다. 자세한 내용은 [클라우드-장치 메시징](#c2d)을 참조하세요.

[IoT Hub API 및 SDK][lnk-apis-sdks] 문서는 이러한 끝점에 액세스할 수 있는 다양한 방법을 설명합니다.

마지막으로 모든 IoT Hub 끝점이 [TLS][lnk-tls]를 통해 노출되고 어떤 끝점도 암호화되지 않은/보안되지 않은 채널에 노출되지 않는다는 점에 유의합니다.

### 이벤트 허브와 호환 가능한 끝점에서 읽는 방법. <a id="eventhubcompatible"></a>

[.NET에 Azure 서비스 버스 SDK](https://www.nuget.org/packages/WindowsAzure.ServiceBus) 또는 [이벤트 허브 - 이벤트 프로세서 호스트][]를 사용하는 경우 적절한 권한이 있는 모든 IoT Hub 연결 문자열을 사용한 다음 `messages/events`을 이벤트 허브 이름으로 사용할 수 있습니다.

IoT Hub를 인식하지 않는 SDK(또는 제품 통합)를 사용하는 경우 [Azure 포털][]의 IoT Hub 설정에서 이벤트 허브와 호환 가능한 끝점 및 이벤트 허브 이름을 검색해야 합니다.

1. IoT Hub 블레이드에서 **설정** 및 **메시징**을 차례로 클릭합니다.
2. **장치-클라우드 설정** 섹션에서 **이벤트 허브와 호환 가능한 끝점**, **이벤트 허브와 호환 가능한 이름** 및 **파티션** 상자를 찾습니다.

    ![][img-eventhubcompatible]

> [AZURE.NOTE] 경우에 따라 SDK는 **Hostname** 또는 **네임스페이스** 값을 필요로 합니다. 이 경우 **이벤트 허브 호환 끝점**에서 구성표를 제거합니다. 예를 들어 이벤트 허브와 호환 가능한 끝점이 `sb://iothub-ns-myiothub-1234.servicebus.windows.net/`인 경우 **호스트 이름**은 `iothub-ns-myiothub-1234.servicebus.windows.net`이고 **네임스페이스**는 `iothub-ns-myiothub-1234`입니다.

지정된 이벤트 허브에 연결할 수 있는 **ServiceConnect** 권한이 있는 공유 액세스 보안 정책을 사용할 수 있습니다.

이전의 정보를 사용하여 이벤트 허브 연결 문자열을 작성해야 하는 경우 다음과 같은 패턴을 사용할 수 있습니다.

        Endpoint={Event Hub-compatible endpoint};SharedAccessKeyName={iot hub policy name};SharedAccessKey={iot hub policy key}


SDK 및 IoT Hub와 사용할 수 있는 통합 목록은 다음과 같습니다.

* [Java 이벤트 허브 클라이언트](https://github.com/hdinsight/eventhubs-client)
* [Apache Storm spout](../hdinsight/hdinsight-storm-develop-csharp-event-hub-topology.md). GitHub의 [spout 원본](https://github.com/apache/storm/tree/master/external/storm-eventhubs)을 볼 수 있습니다.
* [Apache Spark 통합](../hdinsight/hdinsight-apache-spark-csharp-apache-zeppelin-eventhub-streaming.md)

## 장치 ID 레지스트리

각 IoT Hub에는 진행 중인 클라우드-장치 메시지를 포함 하는 큐와 같은 서비스에 장치 단위 리소스를 만들는 데 사용하는 장치 ID 레지스트리가 있습니다. 또한 [액세스 제어](#accesscontrol) 섹션에서 설명한 대로 장치 지향 끝점에 대한 액세스를 허용합니다.

높은 수준에서 장치 ID 레지스트리는 장치 ID 리소스의 REST를 지원하는 컬렉션입니다. 다음 섹션에서는 레지스트리가 ID에 허용하는 장치 ID 리소스 속성 및 작업을 자세히 설명합니다.

> [AZURE.NOTE] 장치 ID 레지스트리와 상호작용 하는 데 사용할 수 있는 HTTP 프로토콜 및 SDK에 대한 자세한 내용은 [IoT Hub API 및 SDK][lnk-apis-sdks]를 참조하세요.

### 장치 ID 속성 <a id="deviceproperties"></a>

장치 ID는 다음 속성을 사용하여 JSON 문서로 표시됩니다.

| 속성 | 옵션 | 설명 |
| -------- | ------- | ----------- |
| deviceId | 필요한 경우 업데이트에서 읽기 전용입니다. | ASCII 7 비트 영숫자 문자 + `{'-', ':', '.', '+', '&percnt;', '_', '&num;', '&ast;', '?', '!', '(', ')', ',', '=', '&commat;', ';', '&dollar;', '''}`의 대/소문자 구분 문자열(최대 128자 길이)입니다. |
| generationId | 필요한 경우 읽기 전용 | 허브에서 생성된 대/소문자 구분 문자열은 최대 128자입니다. 삭제되고 다시 만들 때와 동일한 **deviceId**로 장치를 구분하는 데 사용됩니다. |
| etag | 필요한 경우 읽기 전용 | [RFC7232][lnk-rfc7232] 당 장치 ID에 대해 약한 etag를 나타내는 문자열입니다.|
| auth | 선택 사항 | 인증 정보 및 보안 자료를 포함하는 복합 개체입니다. |
| auth.symkey | 선택 사항 | base64 형식으로 저장된 기본 및 보조 키를 포함하는 복합 개체입니다. |
| status | 필수 | **사용** 또는 **사용 안 함**으로 설정할 수 있습니다. **사용**이면 장치를 연결할 수 있습니다. **사용 안 함**이면 이 장치는 장치 연결 끝점에 액세스할 수 없습니다. |
| statusReason | 선택 사항 | 장치 ID 상태의 원인을 저장하는 128자 길이의 문자열입니다. UTF-8 문자를 모두 허용합니다. |
| statusUpdateTime | 읽기 전용 | 마지막 상태 업데이트된 날짜 및 시간입니다. |
| connectionState | 읽기 전용 | **연결됨** 또는 **연결 끊김**은 장치 연결 상태의 IoT Hub 뷰를 나타냅니다. |
| connectionStateUpdatedTime | 읽기 전용 | 연결 상태가 마지막으로 업데이트된 날짜 및 시간입니다. |
| lastActivityTime | 읽기 전용 | 장치 연결이 마지막으로 연결되거나 메시지를 받거나 보낸 날짜 및 시간입니다. |

> [AZURE.NOTE] 연결 상태는 연결 상태의 IoT Hub 뷰만을 나타낼 수 있습니다. 이 상태는 네트워크 상태 및 구성에 따라 지연될 수 있습니다.

### 장치 ID 작업

IoT Hub 장치 ID 레지스트리는 다음과 같은 작업을 노출합니다.

* 장치 ID 만들기
* 장치 ID 업데이트
* ID로 장치 ID 검색
* 장치 ID 삭제
* 최대 1000개의 ID 목록

모든 작업은 [RFC7232][lnk-rfc7232]에 지정된 낙관적 동시성의 사용을 허용합니다.

> [AZURE.IMPORTANT] 허브의 ID 레지스트리에서 모든 ID를 검색하는 유일한 방법은 [내보내기](#importexport) 기능을 사용하는 것입니다.

허브의 장치 ID 레지스트리는 응용 프로그램 메타데이터를 포함하지 않고 **deviceId**를 키로 사용하여 사전처럼 액세스할 수 있으며 표현 쿼리를 지원하지 않습니다. 모든 IoT 솔루션은 스마트 빌딩 솔루션에서 온도 센서가 배포된 공간과 같은 응용 프로그램 특정 메타데이터를 포함하는 솔루션 특정 저장소를 갖습니다.

### 장치 사용 안 함

레지스트리에서 ID의 **상태** 속성을 업데이트하여 장치를 사용하지 않을 수 있습니다. 일반적으로이 두 가지 시나리오에 사용됩니다.

1. 오케스트레이션 과정을 프로비전하는 동안입니다. 자세한 내용은 [Azure IoT Hub 지침 - 프로비전][lnk-guidance-provisioning]을 참조하세요.
2. 어떤 이유로든 손상되거나 일시적으로 권한이 없는 장치를 사용하도록 고려합니다.

### 장치 ID 내보내기 <a id="importexport"></a>

내보내기는 고객이 제공한 Blob 컨테이너를 사용하여 장치 ID 데이터를 읽고 쓰는 장기 실행 작업입니다.

IoT Hub 리소스 공급자 끝점에서 비동기 작업을 사용하여 IoT Hub의 ID 레지스트리에서 장치 ID를 대량으로 내보낼 수 있습니다.([끝점](#endpoints))

다음은 내보내기 작업에 사용할 수 있는 작업입니다.

* 내보내기 작업 만들기
* 실행 중인 작업의 상태 검색
* 실행 중인 작업 취소

> [AZURE.NOTE] 각 허브는 지정된 시간에 실행되는 단일 작업만을 가질 수 있습니다.

API를 가져오고 내보내는 작업에 대한 자세한 정보는 [Azure IoT Hub - 리소스 공급자 API][lnk-resource-provider-apis]를 참조하세요.

#### 작업

모든 내보내기 작업에는 다음과 같은 속성이 있습니다.

| 속성 | 옵션 | 설명 |
| -------- | ------- | ----------- |
| jobId | 시스템에서 생성된, 생성 시 무시된 | |
| creationTime | 시스템에서 생성된, 생성 시 무시된 | |
| endOfProcessingTime | 시스템에서 생성된, 생성 시 무시된 | |
| type | 읽기 전용 | **내보내기** |
| status | 시스템에서 생성된, 생성 시 무시된 | **큐에 넣음**, **시작**, **완료**, **실패** |
| 진행률 | 시스템에서 생성된, 생성 시 무시된 | 완료율의 정수 값 |
| outputBlobContainerURI | 모든 작업에 필요함 | Blob 컨테이너에 쓰기 액세스 권한이 있는 Blob 공유 액세스 서명 URI입니다.([Blob 서비스로 SAS 만들기 및 사용][lnk-createuse-sas]과 [Java에서 공유 액세스 서명 만들기][lnk-sas-java]를 참조) 이 작업은 작업 및 결과의 상태를 출력하는 데 사용됩니다. |
| includeKeysInExport | 선택 사항 | **true**인 경우 키가 내보내기 출력에 포함되지 않고 그렇지 않으면 키는 **null**로 내보내 집니다. 기본값은 **false**입니다. |
| failureReason | 시스템에서 생성된, 생성 시 무시된 | 상태가 **실패**인 경우 원인을 포함하는 문자열입니다. |

#### 작업 내보내기

작업을 내보내면 Blob 공유 액세스 서명 URI를 매개 변수로 인식합니다. Blob 컨테이너에 쓰기 액세스 권한을 부여하고 작업의 결과를 출력하는 데 사용됩니다.

출력 결과는 `job_{job_id}_devices.txt`라는 파일에 지정된 Blob 컨테이너에 기록됩니다. 이 파일은 [장치 id 속성](#deviceproperties)에 지정된 JSON으로 직렬화된 장치 ID를 포함합니다. **includeKeysInExport**이 **false**로 설정된 경우 보안 자료를 **null**로 설정합니다.

**예제**:

    {"deviceId":"devA","auth":{"symKey":{"primaryKey":"123"}},"status":"enabled"}
    {"deviceId":"devB","auth":{"symKey":{"primaryKey":"234"}},"status":"enabled"}
    {"deviceId":"devC","auth":{"symKey":{"primaryKey":"345"}},"status":"enabled"}
    {"deviceId":"devD","auth":{"symKey":{"primaryKey":"456"}},"status":"enabled"}


## 보안 <a id="security"></a>

이 섹션에서는 Azure IoT Hub를 보호하기 위한 옵션을 설명합니다.

### 액세스 제어<a id="accesscontrol"></a>

IoT Hub는 다음 *권한* 집합을 사용하여 각 IoT Hub의 끝점에 대한 액세스 권한을 부여합니다. 사용 권한은 기능에 따라 IoT Hub에 대한 액세스를 제한합니다.

* **RegistryRead**. 이 사용 권한은 장치 ID 레지스트리에 대한 읽기 액세스 권한을 부여합니다. 자세한 내용은 [장치 ID 레지스트리](#device-identity-registry)를 참조하세요.
* **RegistryReadWrite**. 장치 ID 레지스트리에 대한 읽기 및 쓰기 액세스 권한을 부여합니다. 자세한 내용은 [장치 ID 레지스트리](#device-identity-registry)를 참조하세요.
* **ServiceConnect**. 클라우드 서비스 지향 통신 및 모니터링 중인 끝점에 대한 액세스를 부여합니다. 예를 들어 장치-클라우드 메시지를 받고 클라우드-장치 메시지를 보내며 해당 전달 승인을를 검색할 수 있는 권한을 클라우드 서비스에 부여합니다.
* **DeviceConnect**. 장치 지향 통신 끝점에 대한 액세스를 부여합니다. 예를 들어 장치-클라우드 메시지를 보내고 클라우드-장치 메시지를 받을 수 있는 권한을 클라우드에 부여합니다. 이 권한은 주로 장치에서 사용됩니다.

다음과 같은 방법으로 권한이 부여됩니다.

* **허브 수준 공유 액세스 정책**. *공유 액세스 정책*은 이전의 섹션에 나열된 사용 권한의 조합에 권한을 부여할 수 있습니다. [Azure 포털][lnk-management-portal] 또는 프로그래밍 방식으로 [Azure IoT Hub 리소스 공급자 API][lnk-resource-provider-apis]를 사용하여 정책을 정의할 수 있습니다. 새로 만든 IoT Hub에는 다음과 같은 기본 정책이 있습니다.

    - *iothubowner*: 모든 권한이 있는 정책입니다.
    - *서비스*: **ServiceConnect** 사용 권한이 있는 정책입니다.
    - *장치*: **DeviceConnect** 사용 권한이 있는 정책입니다.
    - *registryRead*: **RegistryRead** 사용 권한이 있는 정책입니다.
    - *registryReadWrite*: **RegistryRead** 및 **RegistryWrite** 사용 권한이 있는 정책입니다.

* **장치 단위 보안 자격 증명**. 각 IoT Hub는 [장치 ID 레지스트리](#device-identity-registry)를 포함합니다. 이 레지스트리의 각 장치의 경우 해당 장치 끝점으로 범위가 지정된 **DeviceConnect** 사용 권한을 부여하는 보안 자격 증명을 구성할 수 있습니다.

**예제**. IoT 솔루션에서는 일반적으로 *registryReadWrite* 정책을 사용하는 장치 관리 구성 요소와 *서비스* 정책을 사용하는 이벤트 프로세서 구성 요소 및 런타임 장치 비즈니스 논리 구성 요소가 있습니다. 개별 장치는 IoT Hub의 ID 레지스트리에 저장된 자격 증명을 사용하여 연결합니다.

IoT Hub 보안 항목에 대한 지침은 [Azure IoT Hub 지침][lnk-guidance-security]에서 보안 섹션을 참조하세요.

### 인증

Azure IoT Hub는 공유 액세스 정책 및 장치 ID 레지스트리 보안 자격 증명에 대한 토큰을 확인하여 끝점에 대한 액세스를 부여합니다.

대칭 키와 같은 보안 자격 증명은 통신 중에 전송되지 않습니다.

> [AZURE.NOTE] Azure IoT Hub 리소스 공급자는 [Azure 리소스 관리자][lnk-azure-resource-manager]의 모든 공급자처럼 Azure 구독을 통해 보호됩니다.

#### 보안 토큰 형식 <a id="tokenformat"></a>

보안 토큰의 형식은 다음과 같습니다.

	SharedAccessSignature sig={signature-string}&se={expiry}&skn={policyName}&sr={URL-encoded-resourceURI}

다음은 필요한 값입니다.

| 값 | 설명 |
| ----- | ----------- |
| {signature} | 형식의 HMAC-SHA256 서명 문자열은 `{URL-encoded-resourceURI} + "\n" + expiry`입니다. |
| {resourceURI} | 이 토큰으로 액세스할 수 있는 끝점의 URI 접두사(세그먼트 별)입니다. 위치(예:`/events` |
| {expiry} | 1970년 1월 1일 epoch 0시 UTC 이후의 초 수에 대한 UTF8 문자열입니다. |
| {URL-encoded-resourceURI} | URL로 인코딩된 리소스 URI(소문자) |
| {policyName} | 이 토큰을 참조하는 공유 액세스 정책의 이름입니다. 장치 레지스트리 자격 증명을 참조하는 토큰의 경우가 없습니다. |

**접두사에 대한 참고**: 문자가 아니라에 세그먼트에 의해 계산된 URI 접두사입니다. 예를 들어 `/a/b`는 `/a/b/c`에 대한 접두사이지만 `/a/bc`에 대한 접두사는 아닙니다.

#### 프로토콜 세부 사항

지원되는 각 프로토콜(예: AMQP 및 HTTP)은 다양한 방식으로 토큰을 전송합니다.

HTTP 인증은 **권한 부여** 요청 헤더에서 유효한 토큰을 포함하여 구현됩니다. 또한 **권한 부여**라는 쿼리 매개 변수는 토큰을 전송할 수 있습니다.

[AMQP][lnk-amqp]을 사용하는 경우 IoT Hub는 [SASL PLAIN][lnk-sasl-plain] 및 [AMQP 클레임 기반-보안][lnk-cbs]을 지원합니다.

AMQP 클레임-기반-보안의 경우 표준은 해당 토큰을 전송하는 방법을 지정합니다.

SASL PLAIN의 경우 **사용자 이름**은 다음이 될 수 있습니다.

* `{policyName}&commat;sas.root.{iothubName}` 허브 수준 토큰의 경우입니다.
* `{deviceId}` 장치 범위 토큰의 경우입니다.

두 가지 경우 모두 암호 필드는 [토큰 형식](#tokenformat) 섹션에서 설명된 토큰을 포함합니다.

> [AZURE.NOTE] [Azure IoT Hub SDK][lnk-apis-sdks]는 서비스에 연결할 때 토큰을 자동으로 생성합니다. 경우에 따라 지원하는 프로토콜 또는 사용할 수 있는 인증 방법에서 SDK가 제한됩니다. 자세한 내용은 [Azure IoT Hub SDK][lnk-apis-sdks] 문서를 참조하세요.

#### CBS에 비해 SASL PLAIN

SASL PLAIN을 사용할 때 IoT Hub에 연결한 클라이언트는 각 TCP 연결에 단일 토큰을 사용할 수 있습니다. 토큰이 만료될 때 TCP 연결은 서비스에서 연결이 끊어지고 다시 연결을 트리거합니다. 이 동작은 응용 프로그램 백 엔드 구성 요소에 문제를 발생시키지 않는 반면 다음과 같은 이유로 장치 쪽 응용 프로그램에 막대한 피해를 줍니다.

*  게이트웨이가 일반적으로 많은 장치를 대신하여 연결됩니다. SASL PLAIN을 사용할 때 IoT Hub에 연결한 각 장치에 고유한 TCP 연결을 만들어야 합니다. 전원 및 네트워킹 리소스의 소비를 상당히 증가시키고 각 장치 연결의 대기 시간을 늘립니다.
* 각 토큰이 만료 후에 다시 연결하기 위해 리소스의 사용이 증가하여 주로 리소스가 제한된 장치에 영향을 미칩니다.

### 허브 수준 자격 증명의 범위 지정

제한된 리소스 URI로 토큰을 만들어 허브 수준 보안 정책의 범위를 지정할 수 있습니다. 예를 들어 장치에서 장치-클라우드 메시지를 보낼 끝점은 `/devices/{deviceId}/events`입니다. 또한 **DeviceConnect** 사용 권한으로 허브 수준 공유 액세스 정책을 사용하여 resourceURI가 `/devices/{deviceId}`인 토큰에 서명하며 이는 장치 **deviceId**를 대신하여 장치를 보내는 데 사용 가능한 토큰을 만듭니다.

이 메커니즘은 [이벤트 허브 게시자 정책][lnk-event-hubs-publisher-policy]과 유사하며 [Azure IoT Hub 지침][lnk-guidance-security]의 보안 섹션에서 설명한 것처럼 사용자 지정 인증 메서드 구현을 활성화합니다.

## 메시징

IoT Hub는 메시징 기본 요소를 제공하여 응용 프로그램 백 엔드(*서비스* 또는 *클라우드*)에서 장치로, 또는 반대로 통신합니다. 이러한 기능을 [장치-클라우드](#d2c) 및 [클라우드-장치](#c2d)라고 합니다.

IoT Hub 메시징 기능의 핵심 속성은 메시지의 안정성 및 내구성입니다. 이를 사용하여 장치 측면에서 일시적인 연결 및 클라우드 측면에서 이벤트 처리에 급증한 부하를 복원합니다. IoT Hub는 D2C 및 C2D 메시징 모두에 대해 *한 번 이상* 전달 보증을 구현합니다.

IoT Hub는 여러 장치 연결 프로토콜을 지원합니다.(예: AMQP 및 HTTP/1) 프로토콜을 통해 원활한 상호 운용성을 지원하기 위해 IoT Hub는 모든 장치 지향 프로토콜에서 지원되는 일반적인 메시지 형식을 정의합니다.

### 메시지 형식 <a id="messageformat"></a>

IoT Hub 메시지는 다음을 구성합니다.

* *시스템 속성* 집합. IoT Hub에서 해석되거나 설정된 속성입니다. 이 집합은 미리 결정됩니다.
* *응용 프로그램 속성* 집합. 본문을 역직렬화할 필요 없이 응용 프로그램이 정의하고 액세스할 수 있는 문자열 속성의 사전입니다. 이러한 속성이 IoT Hub에서 수정되지 않습니다.
* 불투명한 이진 본문.

다른 프로토콜에서 메시지를 인코딩하는 방법에 대한 자세한 내용은 [IoT Hub API 및 SDK][lnk-apis-sdks]를 참조하세요.

IoT Hub 메시지에서 시스템 속성의 집합입니다.

| 속성 | 설명 |
| -------- | ----------- |
| MessageId | 사용자가 설정할 수 있는 메시지에 대한 식별자는 요청-회신 패턴에 일반적으로 사용됩니다. 형식: ASCII 7 비트 영숫자 문자 + `{'-', ':',’.', '+', '%', '_', '#', '*', '?', '!', '(', ')', ',', '=', '@', ';', '$', '''}`의 대/소문자 구분 문자열(최대 128자 길이)입니다. |
| 시퀀스 번호 | 숫자(장치 큐 별로 고유함)는 IoT Hub에서 각 C2D 메시지에 할당됩니다. |
| 받는 사람 | 메시지의 대상에 대한 [클라우드-장치](#c2d)에 사용됩니다.|
| ExpiryTimeUtc | 메시지 만료 날짜 및 시간입니다. |
| EnqueuedTime | IoT Hub에서 메시지가 수신된 시간입니다. |
| CorrelationId | 일반적으로 요청-회신 패턴에서 요청의 메시지 ID를 포함하는 문자열 속성입니다. |
| UserId | 메시지의 원본을 지정하는 데 사용합니다. 메시지가 IoT Hub에서 생성되면 `{iot hub name}`로 설정합니다. |
| Ack | 장치에서 소비하는 메시지의 결과로 피드백 메시지를 생성하는 IoT Hub를 요청하려면 C2D 메시지에서 사용합니다. 가능한 값: **없음** (기본값): 피드백 메시지가 생성되지 않습니다. **양수**: 메시지가 완료된 경우 피드백 메시지를 받습니다. **음수**: 장치에서 완료되지 않고 메시지가 만료(또는 최대 전달 횟수에 도달)하면 피드백 메시지를 받습니다 **전체**: 양수 및 음수 모두입니다. 자세한 내용은 [메시지 피드백](#feedback)을 참조하세요. |
| ConnectionDeviceId | IoT Hub가 D2C 메시지에 설정합니다. 메시지를 보낸 장치의 **deviceId**를 포함합니다. |
| ConnectionDeviceGenerationId | IoT Hub가 D2C 메시지에 설정합니다. 메시지를 보낸 장치의 **generationId**를 포함합니다.([장치 ID 속성](#deviceproperties) 당) |
| ConnectionAuthMethod | IoT Hub가 D2C 메시지에 설정합니다. 메시지를 보내는 장치를 인증하는 데 사용되는 인증 방법에 대한 정보입니다. 자세한 내용은 [D2C 스푸핑 방지](#antispoofing)를 참조하세요.|

### 통신 프로토콜을 선택합니다. <a id="amqpvshttp"></a>

Iot Hub는 장치 측면 통신에 [AMQP][lnk-amqp] 및 HTTP/1 프로토콜을 모두 지원합니다. 사용을 고려한 고려 사항 목록은 다음과 같습니다.

* **클라우드-장치 패턴**. HTTP/1에는 서버 푸시를 구현하는 효율적인 방법이 없습니다. 이와 같이 HTTP/1을 사용하는 경우 장치는 클라우드-장치 메시지에 IoT Hub를 폴링합니다. 장치와 IoT Hub 모두에 매우 비효율적입니다. HTTP/1을 사용하는 경우 현재 지침은 각 장치에 25분 마다 한번 보다 작은 폴링 간격을 설정하는 것입니다. 반면에 AMQP는 클라우드-장치 메시지를 받을 때 서버 푸시를 지원하고 IoT Hub에서 장치에 메시지를 즉각적으로 푸쉬할 수 있도록 합니다. 전달 대기 시간이 중요한 경우 AMQP는 가장 사용하기 적합한 프로토콜입니다. 반면에 거의 연결되지 않은 장치의 경우 HTTP/1도 작동합니다.
* **현장 게이트웨이**. 서버 푸시와 관련하여 HTTP/1 제한 사항을 고려하면 [필드 게이트웨이 시나리오][lnk-azure-gateway-guidance]에서 사용하기에 적합하지 않습니다.
* **낮은 리소스 장치**. HTTP/1 라이브러리는 AMQP 라이브러리 보다 매우 작습니다. 이와 같이 장치에 리소스가 적으면(예: 1Mb RAM 보다 작음) HTTP/1은 사용할 수 있는 유일한 프로토콜 구현일 수도 있습니다.
* **네트워크 통과**. AMQP 표준은 포트 5672에서 수신 대기합니다. 비 HTTP 프로토콜에 접근할 수 있는 네트워크에서 문제를 일으킬 수 있습니다.
* **페이로드 크기**. AMQP는 이진 프로토콜이며 HTTP/1 보다 훨씬 압축적입니다.

높은 수준에서 가능한 경우 항상 AMQP를 사용하고 장치 리소스 또는 네트워크 구성이 AMQP를 허용하지 않는 경우 HTTP/1을 사용하는 것이 좋습니다. 또한 HTTP/1을 사용하는 경우 폴링 빈도는 각 장치에 25분 마다 한 번 보다 작은 값으로 설정해야 합니다. 명확하게 개발하는 동안 폴링 빈도가 더 자주이더라도 문제가 없습니다.

최종 고려 사항으로 [Azure IoT 프로토콜 게이트웨이][lnk-azure-protocol-gateway]를 참조하는 것이 중요하며 이는 IoT Hub와 직접 인터페이스하는 고성능 MQTT 게이트웨이를 배포할 수 있습니다. MQTT는 서버 푸시를 지원하며(C2D 메시지 전달을 장치에 즉시 활성화) 매우 낮은 리소스 장치에 사용할 수 있습니다. 이 방법의 중요한 단점은 프로토콜 게이트웨이를 자체 호스팅하고 관리하는 요구 사항입니다.

### 장치-클라우드 <a id="d2c"></a>

[끝점](#endpoints) 섹션에 설명된 대로 장치-클라우드 메시지는 장치 지향 끝점을 통해 보내지고(특히 `/devices/{deviceId}/messages/events`) 서비스 지향 끝점을 통해 수신합니다.(`/messages/events`) 즉, [이벤트 허브][lnk-event-hubs]와 호환 가능합니다. 따라서 표준 이벤트 허브 통합 및 SDK를 사용하여 메시지를 받을 수 있습니다.

IoT Hub는 IoT Hub의 장치-클라우드 메시지가 [서비스 버스][lnk-servicebus] *메시지* 보다 이벤트 허브의 *이벤트*와 비슷하기에 [이벤트 허브][lnk-event-hubs]와 비슷한 방식으로 장치-클라우드 메시징을 구현합니다.

이 구현에는 다음과 같은 의미가 있습니다.

* 이벤트 허브 *이벤트*와 유사하게 장치-클라우드 메시지는 최대 7일 동안 IoT Hub에서 지속되고 보관됩니다.([장치-클라우드 구성 옵션](#d2cconfiguration) 참조)
* 장치-클라우드 메시지는 생성 시간에 설정된 고정된 파티션 집합에서 분할됩니다.([장치-클라우드 구성 옵션](#d2cconfiguration) 참조)
* 이벤트 허브와 유사하게 장치-클라우드 메시지를 읽는 클라이언트는 분할 및 검사점을 처리해야 합니다. [이벤트 허브 - 이벤트 사용][lnk-event-hubs-consuming-events]을 참조하세요.
* 이벤트 허브 이벤트와 마찬가지로 장치-클라우드 메시지는 최대 크기 256KB이며 최적화하기 위해 일괄 처리로 그룹화할 수 있습니다. 일괄 처리는 최대 256KB이며 최대 500개의 메시지일 수 있습니다.

그러나 IoT Hub 장치-클라우드 및 이벤트 허브 간에 몇가지 중요한 차이가 있습니다.

* [보안](#security) 섹션에서 설명한 것처럼 IoT Hub는 장치 단위 인증 및 액세스 제어가 가능합니다.
* 이벤트 허브가 네임스페이스 당 5000개의 AMQP 연결로 제한되는 반면 IoT Hub는 동시에 연결된 장치를 수 백만 대까지 사용할 수 있습니다.([할당량 및 제한](#throttling) 참조)
* IoT Hub는 **PartitionKey**를 사용하는 임의의 분할을 허용하지 않습니다. 장치-클라우드 메시지는 원래 **deviceId**와 관련하여 분할됩니다.
* IoT Hub를 확장하는 것은 이벤트 허브와 약간 다릅니다. 자세한 내용은 [IoT Hub 확장][lnk-guidance-scale]을 참조하십시오.

이말이 모든 시나리오에서 IoT Hub가 이벤트 허브를 대체한다는 의미는 아닙니다 예를 들어 이벤트 처리 계산 일부에서 데이터 스트림을 분석하기 전에 다른 속성 또는 필드와 관련하여 이벤트를 다시 분할할 필요가 있습니다. 이 경우 이벤트 허브를 사용하여 스트림 처리 파이프라인의 두 부분을 분리할 수 있습니다.

장치-클라우드 메시징을 사용하는 방법에 대한 세부 정보는 [IoT Hub API 및 SDK][lnk-apis-sdks]를 참조하세요.

#### 비-원격 분석 트래픽

대부분의 경우 장치는 원격 분석 데이터 요소를 응용 프로그램 백 엔드로 보낼 뿐만 아니라 응용 프로그램 비즈니스 논리 계층에서 실행 및 처리를 필요로 하는 *대화형* 메시지 및 요청도 보냅니다. 좋은 예는 백 엔드에서 특정 동작을 트리거해야 하거나 장치가 명령에 응답하는 중요한 경고입니다.

이러한 종류의 메시지를 처리하는 가장 좋은 방법은 IoT Hub 지침의 [장치-클라우드 처리][lnk-guidance-d2c-processing] 섹션을 참조하세요.

#### 장치-클라우드 구성 옵션 <a id="d2cconfiguration"></a>

IoT Hub는 다음 속성을 노출하여 D2C 메시징을 제어합니다.

* **분할 개수**. 이 속성은 생성 시 설정하여 D2C 이벤트 수집에 대한 파티션 수를 정의합니다.
* **보존 시간**. 이 속성은 장치-클라우드 메시지에 대한 보존 시간을 지정합니다. 기본값은 하루이지만 최대 7일로 늘릴 수 있습니다.

또한 이벤트 허브와 유사하게 IoT Hub는 장치-클라우드에서 소비자 그룹의 관리가 끝점을 받도록 합니다.

[Azure 포털][lnk-management-portal]을 사용하거나 [Azure IoT Hub - 리소스 공급자 API][lnk-resource-provider-apis]를 통해 프로그래밍 방식으로 해당 속성을 모두 수정할 수 있습니다.

#### 스푸핑 방지 속성 <a id="antispoofing"></a>

장치-클라우드 메시지에서 스푸핑된 장치를 피하려면 IoT Hub는 다음 속성을 사용하여 모든 메시지를 보여줍니다.

* **ConnectionDeviceId**
* **ConnectionDeviceGenerationId**
* **ConnectionAuthMethod**

처음 두 가지는 [장치 ID 속성](#deviceproperties) ekd 원래 장치의 **deviceId** 및 **generationId**를 포함합니다.

**ConnectionAuthMethod** 속성은 다음 속성을 가진 JSON으로 직렬화된 개체를 포함합니다.

    {
        "scope": "{ hub | device}",
        "type": "{ symkey | sas}",
        "issuer": "iothub"
    }

### 클라우드-장치 <a id="c2d"></a>

이미 [끝점](#endpoints) 세션에서 자세히 설명한 대로 클라우드-장치 메시지는 서비스 지향 끝점을 통해 보내고(`/messages/devicebound`) 장치 지향 끝점을 통해 수신합니다.(`/devices/{deviceId}/messages/devicebound`)

각 클라우드-장치 메시지는 단일 장치를 대상으로 설정하며 **to** 속성을 `/devices/{deviceId}/messages/devicebound`로 설정합니다.

**중요**: 각 장치 큐는 최대 50개의 클라우드-장치 메시지를 보유할 수 있습니다. 동일한 장치에 더 많은 메시지를 보내려고 하면 오류가 발생합니다.

#### 메시지 수명 주기 <a id="message lifecycle"></a>

*한번 이상* 전달 보장을 구현하기 위해 클라우드-장치 메시지는 장치 단위 큐에 지속되고 장치는 IoT Hub가 큐에서 해당 장치를 제거하기 위해 작업을 명시적으로 *완료*했다고 인정해야 합니다. 연결 및 장치 오류로부터 복구를 보장합니다.

다음 그림은 클라우드-장치 메시지에 대한 수명 주기 상태 Graph를 보여줍니다.

![클라우드-장치 메시지 수명 주기][img-lifecycle]

서비스에서 메시지를 보낼 때 *큐에 넣음*으로 간주합니다. 장치가 메시지를 *수신*하려면 IoT Hub는 동일한 장치에 있는 다른 스레드가 다른 메시지를 받기 시작하도록 하기 위해 메시지를 *잠급니다*.(상태는**보이지 않음**으로 설정) 장치 스레드가 장치의 처리를 완료하면 IoT Hub에 *완료* 메시지를 알립니다. 또한 메시지를 *거부*하기로 결정할 수도 있으며 메시지를 **효력 상실** 상태 또는 다시 큐에 메시지를 저장하는 *중단*으로 보냅니다.(**큐에 대기** 상태)

스레드는 IoT Hub에 알리지 않고 메시지를 처리하는 데 실패할 수 있으므로 *표시(또는 잠금) 제한 시간* (기본값: 1 분) 후에 메시지가 **보이지 않음**에서 **큐에 대기**로 자동으로 전환됩니다. 메시지는 IoT Hub의 *최대 배달 횟수* 속성에 지정된 최대 지정된 횟수 만큼 **큐에 대기**와 **보이지 않는** 상태 간에 전환될 수 있습니다. 해당 전환 횟수 후에 메시지는 자동으로 효력을 상실합니다. 마찬가지로 해당 만료 시간 후에 메시지가 자동으로 효력을 상실합니다.([지속 시간](#ttl) 참조)

클라우드-장치 메시지에 대한 자습서는 [Azure IoT Hub 클라우드-장치 메시지 시작][lnk-getstarted-c2d-tutorial]를 참조하세요. 다른 API 및 SDK가 클라우드-장치 기능을 어떻게 노출하는지에 대한 참조 항목은 [IoT Hub API 및 SDK][lnk-apis-sdks]를 참조하세요.

> [AZURE.NOTE] 일반적으로 클라우드-장치 메시지는 메시지의 손실이 응용 프로그램 논리에 영향을 줄 때마다 완료되어야 합니다. 다양한 시나리오에서 발생할 수 있습니다. 예를 들어 메시지 콘텐츠가 로컬 저장소에 성공적으로 지속되거나 작업이 성공적으로 실행되거나 메시지가 손실해도 응용 프로그램의 기능에 영향을 주지 않는 임시 정보를 전달합니다. 장기 실행 작업의 경우 로컬 저장소에서 작업을 지속적으로 설명한 후에 클라우드-장치 메시지를 완료한 다음 작업의 진행 상황의 여러 단계에서 하나 이상의 장치-클라우드 메시지로 응용 프로그램 백 엔드를 알리는 것이 일반적입니다.

#### TTL(Time to live) <a id="ttl"></a>

모든 클라우드-장치 메시지에는 만료 시간이 있습니다. 서비스에서 명시적으로 설정할 수 있거나(**ExpiryTimeUtc** 속성에) 또는 IoT Hub 속성처럼 지정된 기본 *TTL(Time To Live)*을 사용하여 IoT Hub에서 설정됩니다. [클라우드-장치 구성 옵션](#c2dconfiguration)을 참조하세요.

#### 메시지 피드백 <a id="feedback"></a>

클라우드-장치 메시지를 보낼 때 서비스는 해당 메시지의 최종 상태에 대한 메시지 단위 피드백을 전달하도록 요청할 수 있습니다. **Ack** 속성을 **양수**로 설정하는 경우 클라우드-장치 메시지가 **완료** 상태에 도달하는 경우에만 IoT Hub가 피드백 메시지를 생성합니다. **Ack** 속성을 **음수**로 설정하여 클라우드-장치 메시지가 **효력 상실** 상태에 도달하는 경우에만 IoT Hub가 피드백 메시지를 생성합니다. **Ack** 속성을 **전체**로 설정하여 두 경우에도 IoT Hub가 피드백 메시지를 생성합니다.

[끝점](#endpoints)에 설명된 대로 피드백은 서비스 지향 끝점(`/messages/servicebound/feedback`)을 통해 메시지로 전달됩니다. 피드백에 대한 수신 의미 체계는 동일한 [메시지 수명 주기](#message lifecycle)를 가진 클라우드-장치 메시지의 경우와 동일합니다. 가능한 경우 메시지 피드백은 다음 형식으로 단일 메시지에서 일괄 처리됩니다.

피드백 끝점에서 검색한 각 메시지에는 다음과 같은 속성이 있습니다.

| 속성 | 설명 |
| -------- | ----------- |
| EnqueuedTime | 배치를 만들 경우를 나타내는 타임스탬프입니다. |
| UserId | `{iot hub name}` |
| ContentType | `application/vnd.microsoft.iothub.feedback.json` |

본문은 각각 다음과 같은 속성이 있는 레코드의 JSON으로 직렬화된 배열입니다.

| 속성 | 설명 |
| -------- | ----------- |
| EnqueuedTimeUtc | 메시지의 결과가 발생하는 경우를 나타내는 타임스탬프입니다. 예를 들어 완료된 장치 또는 만료된 메시지입니다. |
| OriginalMessageId | 이 피드백 정보가 관련된 클라우드-장치 메시지의 **MessageId**입니다. |
| 설명 | 이전의 결과에 대한 문자열 값입니다. |
| DeviceId | 피드백의 해당 부분이 관련된 클라우드-장치 메시지에서 대상 장치의 **DeviceId**입니다. |
| DeviceGenerationId | 피드백의 해당 부분이 관련된 클라우드-장치 메시지에서 대상 장치의 **DeviceGenerationId**입니다. |

**중요**. 서비스는 원본 메시지와 해당 피드백을 상호 연결하기 위해 클라우드-장치 메시지에 대한 **MessageId**를 지정해야 합니다.

**예제**. 피드백 메시지의 예제 본문입니다.

    [
        {
            "OriginalMessageId": "0987654321",
            "EnqueuedTimeUtc": "2015-07-28T16:24:48.789Z",
            "Description": "Success",
            "DeviceId": "123",
            "DeviceGenerationId": "abcdefghijklmnopqrstuvwxyz"
        },
        {
            ...
        },
        ...
    ]

#### 클라우드-장치 구성 옵션 <a id="c2dconfiguration"></a>

각 IoT Hub는 클라우드-장치 메시징에 다음 구성 옵션을 노출합니다.

| 속성 | 설명 | 범위 및 기본값 |
| -------- | ----------- | ----------------- |
| defaultTtlAsIso8601 | 클라우드-장치 메시지에 대한 기본 TTL | 최대 2D(최소 1 분)까지 ISO\_8601 간격입니다. 기본값은 1시간입니다. |
| maxDeliveryCount | 클라우드-장치 장치 단위 큐에 대한 최대 전달 수입니다. | 1에서 100까지입니다. 기본값은 10입니다. |
| feedback.ttlAsIso8601 | 서비스 바인딩 피드백 메시지에 대한 보존 기간입니다. | 최대 2D(최소 1 분)까지 ISO\_8601 간격입니다. 기본값은 1시간입니다. |
| feedback.maxDeliveryCount | 피드백 큐에 대한 최대 전달 수입니다. | 1에서 100까지입니다. 기본값은 100입니다. |

## 할당량 및 제한 <a id="throttling"></a>

각 Azure 구독에는 최대 10 IoT Hub가 있을 수 있습니다.

각 IoT Hub는 특정 SKU에서 특정한 단위 수로 프로비전됩니다. 자세한 내용은 [Azure IoT Hub 가격 책정][lnk-pricing]을 참조하세요. SKU 및 단위 수는 전송할 수 있는 메시지의 최대 일일 할당량 및 ID 레지스트리에서 최대 장치 ID 수를 결정합니다. 동시에 연결된 장치 수는 레지스트리에서 ID 번호로 제한됩니다.

또한 IoT Hub가 작업에 강제로 적용하는 조정 제한을 결정합니다.

### 장치 ID 레지스트리 할당량

IoT Hub는 하루에 단위당(SKU 관계 없이) 최대 1100개의 장치 업데이트를 허용합니다.(만들기, 업데이트 및 삭제)

### 작업 제한

작업 제한은 분 범위에 적용하고 남용을 방지하고자 하는 속도 제한입니다. IoT Hub는 가능한 경우 오류를 반환 하지 않으려 하지만 제한이 너무 오랫동안 위반된 경우 예외를 반환하기 시작합니다.

강제로 적용된 제한 목록은 다음과 같습니다. 값은 개별 허브라고 합니다.

| 제한 | 허브 단위 값 |
| -------- | ------------- |
| ID 레지스트리 작업(만들기, 검색, 나열, 업데이트 및 삭제), 개별 또는 대량 가져오기/내보내기 | 100/분/단위, 최대 5000/분까지 |
| 장치 연결 | 100/초/단위 |
| D2C 보내기 | 2000/분/단위(S2의 경우), 60/분/단위(S1의 경우). 100/초의 최소값. |
| C2D 작업(송신, 수신, 피드백) | 100/분/단위 |

**참고**. 지정된 시간에 IoT Hub에 프로비전된 단위 수를 늘려 할당량이나 조정 제한을 증가시킬 수 있습니다.

## 다음 단계

IoT Hub 개발의 개요를 살펴보았습니다. 자세한 내용을 보려면 다음 링크를 따라가십시오.

- [IoT Hub 시작(자습서)][lnk-get-started]
- [OS 플랫폼 및 하드웨어 호환성][lnk-compatibility]
- [Azure IoT 개발자 센터][lnk-iotdev]
- [IoT 구현 계획][lnk-guidance]

[이벤트 허브 - 이벤트 프로세서 호스트]: http://blogs.msdn.com/b/servicebus/archive/2015/01/16/event-processor-host-best-practices-part-1.aspx

[Azure 포털]: https://portal.azure.com

[img-summary]: ./media/iot-hub-devguide/summary.png
[img-endpoints]: ./media/iot-hub-devguide/endpoints.png
[img-lifecycle]: ./media/iot-hub-devguide/lifecycle.png
[img-eventhubcompatible]: ./media/iot-hub-devguide/eventhubcompatible.png

[lnk-compatibility]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/tested_configurations.md
[lnk-apis-sdks]: https://github.com/Azure/azure-iot-sdks/blob/master/readme.md
[lnk-azure-hub-sdks]: https://msdn.microsoft.com/library/mt488521.aspx
[lnk-pricing]: https://azure.microsoft.com/pricing/details/iot-hub
[lnk-resource-provider-apis]: https://msdn.microsoft.com/library/mt548492.aspx
[lnk-reference-architecture]: iot-hub-what-is-azure-iot.md

[lnk-azure-gateway-guidance]: iot-hub-guidance.md#fieldgateways
[lnk-guidance-provisioning]: iot-hub-guidance.md#provisioning
[lnk-guidance-scale]: iot-hub-scaling.md
[lnk-guidance-security]: iot-hub-guidance.md#customauth

[lnk-azure-protocol-gateway]: iot-hub-protocol-gateway.md
[lnk-get-started]: iot-hub-csharp-csharp-getstarted.md
[lnk-guidance]: iot-hub-guidance.md
[lnk-getstarted-c2d-tutorial]: iot-hub-csharp-csharp-c2d.md

[lnk-amqp]: https://www.amqp.org/
[lnk-arm]: ../resource-group-overview.md
[lnk-azure-resource-manager]: https://azure.microsoft.com/documentation/articles/resource-group-overview/
[lnk-cbs]: https://www.oasis-open.org/committees/download.php/50506/amqp-cbs-v1%200-wd02%202013-08-12.doc
[lnk-createuse-sas]: ../storage/storage-dotnet-shared-access-signature-part-2/
[lnk-event-hubs-publisher-policy]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-99ce67ab
[lnk-event-hubs]: http://azure.microsoft.com/services/event-hubs/
[lnk-event-hubs-consuming-events]: ../event-hubs/event-hubs-programming-guide.md#event-consumers
[lnk-guidance-d2c-processing]: iot-hub-csharp-csharp-process-d2c.md
[lnk-management-portal]: https://portal.azure.com
[lnk-rfc7232]: https://tools.ietf.org/html/rfc7232
[lnk-sas-java]: https://msdn.microsoft.com/library/azure/Hh875756.aspx
[lnk-sasl-plain]: http://tools.ietf.org/html/rfc4616
[lnk-servicebus]: http://azure.microsoft.com/services/service-bus/
[lnk-tls]: https://tools.ietf.org/html/rfc5246
[lnk-iotdev]: https://azure.microsoft.com/develop/iot/

<!-----HONumber=AcomDC_1217_2015-->