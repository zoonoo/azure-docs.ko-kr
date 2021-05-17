---
title: Azure Blockchain Service(ABS) 모니터링
description: Azure Monitor를 통해 Azure Blockchain Service 모니터링
ms.date: 01/08/2020
ms.topic: how-to
ms.reviewer: v-umha
ms.openlocfilehash: 05147f48c4cde4cc97bf6cc9cae5c8220a389ebd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100594922"
---
# <a name="monitor-azure-blockchain-service-through-azure-monitor"></a>Azure Monitor를 통해 Azure Blockchain Service 모니터링  

고객이 ABS(Azure Blockchain Service)에서 프로덕션 등급 블록체인 시나리오를 실행할 때는 가용성, 성능, 작업을 위한 리소스를 모니터링하는 것이 중요합니다. 이 문서에서는 Azure Blockchain Service에서 생성하는 모니터링 데이터와 Azure Monitor의 다양한 기능 및 통합을 사용하여 프로덕션 등급 환경을 분석, 경고, 관리하는 방법을 설명합니다.  

## <a name="what-is-azure-monitor"></a>Azure Monitor란?

Azure Blockchain Service는 Azure 리소스를 모니터링할 수 있는 완전한 기능 집합을 제공하는 Azure의 전체 스택 모니터링 서비스인 Azure Monitor를 사용하여 모니터링 데이터를 만듭니다. Azure Monitor에 대한 자세한 내용은 [Azure Monitor를 사용하여 Azure 리소스 모니터링](../../azure-monitor/essentials/monitor-azure-resource.md)을 참조하세요.
 

다음 섹션에서는 Azure Blockchain Service에서 수집한 특정 데이터를 설명하고, Azure 도구를 사용하여 데이터 컬렉션을 구성하고, 이 데이터를 분석하기 위한 예제를 제공합니다.

## <a name="monitor-data-collected-from-azure-blockchain-service"></a>Azure Blockchain Service에서 수집된 데이터 모니터링  

Azure Blockchain Service는 다른 Azure 리소스와 동일한 종류의 모니터링 데이터를 수집합니다. 이에 대한 내용은 Azure 리소스의 [모니터링 데이터](../../azure-monitor/essentials/monitor-azure-resource.md#monitoring-data)에 설명되어 있습니다. Azure Blockchain Service에서 만든 로그 및 메트릭에 대한 자세한 내용은 [Azure Blockchain Service 데이터 모니터링 참조](#monitor-azure-blockchain-service-data-reference)를 확인하세요.

각 Azure Blockchain Service 멤버 리소스에 대한 Azure Portal의 개요 페이지에는 처리된 요청 및 처리된 블록을 포함하여 트랜잭션의 간략한 보기가 포함되어 있습니다. Azure Blockchain Service 멤버 리소스를 만들면 이 데이터 중 일부를 자동으로 수집하여 분석에 사용할 수 있으며, 추가 구성을 사용하여 추가 데이터 수집을 사용하도록 설정할 수 있습니다.

## <a name="diagnostic-settings"></a>진단 설정  

플랫폼 메트릭과 활동 로그는 자동으로 수집되지만, 리소스 로그를 수집하거나 Azure Monitor 외부로 전달하려면 진단 설정을 만들어야 합니다. Azure Portal, CLI 또는 PowerShell을 사용한 진단 설정 만들기의 자세한 프로세스는 [Azure에서 플랫폼 로그 및 메트릭을 수집하는 진단 설정 만들기](../../azure-monitor/essentials/diagnostic-settings.md)를 참조하세요.

진단 설정을 만들 때 수집할 로그 범주를 지정합니다. Azure Blockchain Service의 범주는 아래 나열되어 있습니다.

**블록체인 프록시 로그** – NGNIX 프록시 로그를 모니터링하려는 경우 이 범주를 선택합니다. 모든 고객 트랜잭션 세부 정보는 감사 및 디버그 목적으로 사용할 수 있습니다.  

**블록체인 애플리케이션 로그** – 관리되는 서비스에서 호스트되는 블록체인 애플리케이션의 로그를 가져오려면 이 범주를 선택합니다. 예를 들어 ABS-Quorum 멤버의 경우 이러한 로그는 Quorum 자체의 로그가 됩니다.  

**메트릭 요청**: Azure Cosmos DB에서 진단 설정의 대상으로 메트릭 데이터를 수집하려면 이 옵션을 선택합니다. 메트릭 데이터는 Azure Metrics에서 자동으로 수집됩니다. 두 종류의 데이터를 함께 분석하고 메트릭 데이터를 Azure Monitor 외부로 보내려면 리소스 로그와 함께 메트릭 데이터를 수집합니다.

## <a name="analyze-metric-data"></a>메트릭 데이터 분석  

메트릭 탐색기를 사용하여 Azure Blockchain Service에 대한 메트릭을 분석하고 ABS 리소스 블레이드의 모니터링 섹션 아래에 있는 메트릭 탭으로 이동할 수 있습니다. 이 도구 사용에 대한 자세한 내용은 [Azure 메트릭 탐색기 시작](../../azure-monitor/essentials/metrics-getting-started.md)을 참조하세요. Azure Blockchain Service에 대한 전체 메트릭은 Azure Blockchain Service 표준 메트릭 네임스페이스에 있습니다.

필터를 추가하거나 메트릭을 분할할 때 기본적으로 ABS 멤버의 트랜잭션 노드 및 유효성 검사기 노드별 메트릭 값을 제공하는 **노드** 차원을 사용할 수 있습니다.

## <a name="analyze-log-data"></a>로그 데이터 분석

다음 쿼리를 로그 검색 창에 입력하면 Azure Blockchain Service 멤버를 모니터링하는 데 도움이 됩니다. 이러한 쿼리는 [새 언어](../../azure-monitor/logs/log-query-overview.md)에서 작동합니다.

블록체인 애플리케이션 로그의 오류 조건을 쿼리하려면 아래 쿼리를 사용하세요.

```
BlockchainApplicationLog | where BlockchainMessage contains "ERROR" or BlockchainMessage contains "fatal"

```

블록체인 프록시 로그의 오류 조건을 쿼리하려면 아래 쿼리를 사용하세요.  


```
BlockchainProxyLog
| filter Code != 200
| limit 500

```
Azure 로그에서 사용 가능한 시간 필터를 사용하면 특정 시간 범위의 쿼리를 필터링할 수 있습니다.

## <a name="monitor-azure-blockchain-service-data-reference"></a>Azure Blockchain Service 데이터 모니터링 참조  

이 문서에서는 Azure Blockchain Service의 성능 및 가용성을 분석하기 위해 수집된 로그 및 메트릭 데이터에 대한 참조를 제공합니다.  

### <a name="resource-logs"></a>리소스 로그

모든 리소스 로그는 블록체인 서비스와 관련된 몇 가지 고유한 속성을 포함하는 최상위 수준의 공통 스키마를 공유합니다. [최상위 리소스 로그 스키마](../../azure-monitor/essentials/resource-logs-schema.md#top-level-common-schema) 문서를 참조할 수 있습니다. Azure Blockchain Service 관련 속성의 세부 정보는 아래에서 설명합니다.  

다음 표에는 Azure Monitor 로그 또는 Azure Storage에서 수집되는 Azure Blockchain 프록시 로그의 속성이 나와 있습니다.  


| 속성 이름  | 설명 |
|:---|:---|
| time | 작업이 발생한 날짜 및 시간(UTC)입니다. |
| resourceID  | 로그를 사용하도록 설정된 Azure Blockchain Service 리소스입니다.  |
| category  |Azure Blockchain Service의 경우 가능한 값은 **Proxylogs** 및 **Applicationlogs** 입니다. |
| operationName  | 이 이벤트가 나타내는 작업의 이름입니다.   |
| 로그 수준  | 기본적으로 Azure Blockchain Service는 **정보** 로그 수준을 사용하도록 설정합니다.   |
| NodeLocation  | 블록체인 멤버가 배포되는 Azure 지역입니다.  |
| BlockchainNodeName  | 작업이 수행되는 Azure Blockchain Service 멤버의 노드 이름입니다.   |
| EthMethod  | 기본 블록체인 프로토콜에 의해 호출되는 메서드로, Quorum에서는 eth_sendTransactions, eth_getBlockByNumber 등이 될 수 있습니다.  |
| 에이전트  | 웹 브라우저 Mozilla, Edge 등 사용자를 대신하여 동작을 수행하는 사용자 에이전트입니다. 값의 예는 ‘Mozilla/5.0 (Linux x64) node.js/8.16.0 v8/6.2.414.77’입니다.  |
| 코드   | HTTP 오류 코드입니다. 일반적으로 4XX 및 5XX는 오류 조건입니다.  |
| NodeHost  | 노드의 DNS 이름입니다.   |
| RequestMethodName | 호출된 HTTP 메서드로, 여기서 가능한 값은 멤버를 만들기 위한 PUT, 기존 멤버의 세부 정보를 가져오기 위한 GET, 멤버를 삭제하기 위한 DELETE, 멤버를 업데이트하기 위한 PATCH입니다.   |
| BlockchainMemberName  | 사용자가 제공한 Azure Blockchain Service 멤버 이름입니다.  |
| 컨소시엄 | 사용자가 제공한 컨소시엄 이름입니다.   |
| 원격  | 요청을 보내는 클라이언트의 IP입니다.  |
| RequestSize  | 수행된 요청의 크기(바이트)입니다.  |
| RequestTime  | 요청의 기간(밀리초)입니다.|




다음 표에는 Azure Blockchain 애플리케이션 로그의 속성이 나와 있습니다.


| 속성 이름  | 설명 |
|:---|:---|
| time | 작업이 발생한 날짜 및 시간(UTC)입니다. |
| resourceID  | 로그를 사용하도록 설정된 Azure Blockchain Service 리소스입니다.|
| category  |Azure Blockchain Service의 경우 가능한 값은 **Proxylogs** 및 **Applicationlogs** 입니다.  |
| operationName  | 이 이벤트가 나타내는 작업의 이름입니다.   |
| 로그 수준  | 기본적으로 Azure Blockchain Service는 **정보** 로그 수준을 사용하도록 설정합니다.   |
| NodeLocation  | 블록체인 멤버가 배포되는 Azure 지역입니다.  |
| BlockchainNodeName  | 작업이 수행되는 Azure Blockchain Service 멤버의 노드 이름입니다.   |
| BlockchainMessage    | 이 필드에는 데이터 일반 로그인 블록체인 애플리케이션 로그가 포함됩니다. ABS-Quorum의 경우 이 필드에 Quorum 로그가 있습니다. 이 필드에는 로그 항목의 유형(정보, 오류, 경고)에 관한 정보와 실행된 작업에 대한 자세한 정보를 제공하는 문자열이 있습니다.   |
| TenantID    | Azure Blockchain Service의 지역별 테넌트입니다. 이 필드의 형식은 https://westlake-rp-prod.<region>.cloudapp.azure.com입니다. 여기서 region은 배포된 멤버의 Azure 지역을 지정합니다.       |
| SourceSystem   | 시스템이 로그를 채우며, 이 경우에는 **Azure** 입니다.    |



### <a name="metrics"></a>메트릭

다음 표에는 Azure Blockchain Service를 위해 수집되는 플랫폼 메트릭이 나와 있습니다. 모든 메트릭은 **Azure Blockchain Service** 표준 메트릭 네임스페이스에 저장됩니다.

Azure Blockchain Service를 포함하여 모든 Azure Monitor 지원 메트릭의 목록은 [Azure Monitor에서 지원하는 메트릭](../../azure-monitor/essentials/metrics-supported.md)을 참조하세요.

### <a name="blockchain-metrics"></a>블록체인 메트릭

다음 표는 Azure Blockchain Service 멤버 리소스를 위해 수집되는 블록체인 메트릭 목록을 지정합니다.


| 메트릭 이름 | 단위  |  집계 유형| 설명   |
|---|---|---|---|
| 보류 중인 트랜잭션   | 개수  |  평균 | 마이닝되기를 기다리고 있는 트랜잭션 수입니다.   |
| 처리된 블록   | 개수  | 합계  |  각 시간 간격에서 처리된 블록 수입니다. 현재 블록 크기는 5초이므로 각 노드는 1분 안에 12개의 블록, 5분 안에 60개의 블록을 처리합니다.   |
|처리된 트랜잭션    | 개수  | 합계  | 블록에서 처리된 트랜잭션 수입니다.    |
|지연 트랜잭션    |  개수 | 평균  | 즉시 마이닝할 수 없는 트랜잭션 수입니다. 트랜잭션이 잘못된 순서로 도착해 장래 트랜잭션이 이전 트랜잭션의 도착을 기다리고 있기 때문일 수 있습니다. 또는 두 트랜잭션에 동일한 nonce(number only used once)와 동일한 가스 값이 있기 때문에 두 번째 트랜잭션을 마이닝할 수 없을 수도 있습니다.   |

### <a name="connection-metrics"></a>연결 메트릭  

다음 표에는 Azure Blockchain Service 멤버 리소스를 위해 수집되는 다양한 연결 메트릭이 나와 있습니다. 이러한 메트릭은 NGINX 프록시 메트릭입니다.


| 메트릭 이름 | 단위  |  집계 유형| 설명 |
|---|---|---|---|
| 수락된 연결   | 개수  |  합계 | 허용되는 총 클라이언트 연결 수입니다.   |
| 활성 연결 수  | 개수  | 평균  |  대기 중인 연결을 포함하여 활성 클라이언트 연결의 현재 수입니다.    |
|처리된 연결    | 개수  | 합계  | 처리된 총 연결 수입니다. 일반적으로 일정한 리소스 한도에 도달하지 않는 한 매개 변수 값은 허용된 연결과 동일합니다.     |
|처리된 요청     |  개수 | 합계  | 총 클라이언트 요청 수입니다.  |


### <a name="performance-metrics"></a>성능 메트릭

다음 표에는 Azure Blockchain 멤버 리소스의 각 노드를 위해 수집되는 성능 메트릭이 나와 있습니다.  


| 메트릭 이름 | 단위  |  집계 유형| 설명   |
|---|---|---|---|
| CPU 사용 백분율   | 백분율  |  최대 | CPU 사용 백분율입니다.     |
| IO 읽기 바이트   | 킬로바이트   | 합계  |  블록체인 멤버 리소스의 모든 노드에서의 IO 읽기 바이트의 합계입니다.      |
|IO 쓰기 바이트     | 킬로바이트   | 합계  | 블록체인 멤버 리소스의 모든 노드에서의 IO 쓰기 바이트의 합계입니다.     |
|메모리 제한       |  기가바이트   | 평균    | 블록체인 프로세스에 사용할 수 있는 노드당 최대 메모리입니다. |
|메모리 사용량     | 기가바이트  |  평균 | 모든 노드에서 사용된 메모리 양의 평균입니다.  |
| 메모리 사용 백분율     | 백분율   | 평균  |  모든 노드에서 사용된 메모리의 평균 백분율입니다.       |
|스토리지 사용량      | 기가바이트   | 평균  | 모든 노드에서 사용된 스토리지 용량의 평균(GB)입니다.       |


## <a name="next-steps"></a>다음 단계

블록체인 데이터를 캡처하고 Azure Event Grid로 변환하기 위해 [Blockchain Data Manager](./data-manager.md)에 대해 자세히 알아봅니다.
