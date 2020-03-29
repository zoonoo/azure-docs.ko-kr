---
title: AZURE 블록체인 서비스 모니터링(ABS)
description: Azure 모니터를 통한 Azure 블록 체인 서비스 모니터링
ms.date: 01/08/2020
ms.topic: article
ms.reviewer: v-umha
ms.openlocfilehash: 6f2a91a8ffce67d3c4008a7587f2787f6446c341
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76293252"
---
# <a name="monitor-azure-blockchain-service-through-azure-monitor"></a>Azure 모니터를 통해 Azure 블록 체인 서비스 모니터링  

고객이 AZURE 블록 체인 서비스 (ABS)에서 프로덕션 등급 블록 체인 시나리오를 실행함에 따라 가용성, 성능 및 작업에 대한 리소스를 모니터링하는 것이 중요합니다. 이 문서에서는 Azure Blockchain Service에서 생성된 모니터링 데이터와 Azure Monitor의 다양한 기능 및 통합을 사용하여 분석하고 경고하여 프로덕션 등급 환경을 관리하는 방법에 대해 설명합니다.  

## <a name="what-is-azure-monitor"></a>Azure Monitor란?

Azure 블록 체인 서비스는 Azure 리소스를 모니터링하는 전체 기능 집합을 제공하는 Azure의 전체 스택 모니터링 서비스인 Azure Monitor를 사용하여 모니터링 데이터를 만듭니다. Azure 모니터에 대한 자세한 내용은 [Azure 모니터를 통해 Azure 리소스 모니터링을](https://docs.microsoft.com/azure/azure-monitor/insights/monitor-azure-resource)참조하십시오.
 

다음 섹션에서는 Azure 블록 체인 서비스에서 수집 한 특정 데이터를 설명하고 Azure 도구를 사용하여 데이터 수집을 구성하고이 데이터를 분석하는 예제를 제공하여이 문서를 작성합니다.

## <a name="monitor-data-collected-from-azure-blockchain-service"></a>Azure 블록 체인 서비스에서 수집 된 데이터 모니터링  

Azure 블록 체인 서비스는 Azure 리소스의 모니터링 데이터에 설명 된 다른 Azure 리소스와 동일한 종류의 [모니터링 데이터를](https://docs.microsoft.com/azure/azure-monitor/insights/monitor-azure-resource#monitoring-data) 수집합니다. Azure 블록 체인 서비스에서 만든 로그 및 메트릭에 대한 자세한 참조는 [Azure 블록 체인 서비스 데이터 참조 를 모니터링하십시오.](#monitor-azure-blockchain-service-data-reference)

각 Azure 블록 체인 서비스 구성원 리소스에 대한 Azure 포털의 개요 페이지에는 처리된 요청 및 처리된 블록을 포함한 트랜잭션에 대한 간략한 보기가 포함됩니다. 이 데이터 중 일부는 자동으로 수집되며 Azure Blockchain Service 구성원 리소스를 만든 후 분석에 사용할 수 있으며 추가 구성을 사용하여 추가 데이터 수집을 활성화할 수 있습니다.

## <a name="diagnostic-settings"></a>진단 설정  

플랫폼 메트릭 및 활동 로그는 자동으로 수집되지만 리소스 로그를 수집하거나 Azure Monitor 외부에서 전달하려면 진단 설정을 만들어야 합니다. Azure 포털, CLI 또는 PowerShell을 사용하여 진단 설정을 만들기 위한 자세한 프로세스에 대한 [Azure의 플랫폼 로그 및 메트릭을 수집하는](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings) 진단 설정 만들기를 참조하십시오.

진단 설정을 만들 때 수집할 로그 범주를 지정합니다. Azure 블록 체인 서비스에 대한 범주는 다음과 같습니다.

**블록 체인 프록시 로그** - NGNIX 프록시 로그를 모니터링하려면 범주를 선택합니다. 모든 고객 트랜잭션 세부 정보는 감사 및 디버그 목적으로 사용할 수 있습니다.  

**블록 체인 응용 프로그램 로그** - 관리 되는 서비스에 의해 호스팅 되는 블록 체인 응용 프로그램의 로그를 얻을 수 있는 범주를 선택 합니다. 예를 들어 ABS-쿼럼 멤버의 경우 이러한 로그는 쿼럼 자체의 로그가 됩니다.  

**메트릭 요청**: Azure Cosmos DB에서 Azure 메트릭에서 Azure 메트릭에서 자동으로 수집되는 진단 설정의 대상에 대한 메트릭 데이터를 수집하는 옵션을 선택합니다. 리소스 로그를 사용하여 메트릭 데이터를 수집하여 두 종류의 데이터를 함께 분석하고 Azure Monitor 외부로 메트릭 데이터를 보냅니다.

## <a name="analyze-metric-data"></a>메트릭 데이터 분석  

메트릭 탐색기를 사용하여 Azure 블록 체인 서비스에 대한 메트릭을 분석하고 ABS 리소스 블레이드의 모니터링 섹션 아래의 메트릭 탭으로 이동할 수 있습니다. 도구 사용에 대한 자세한 내용은 [Azure 메트릭 탐색기 시작을](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-getting-started) 참조하십시오. Azure 블록 체인 서비스에 대한 전체 메트릭은 네임 스페이스 Azure 블록 체인 서비스 표준 메트릭에 있습니다.

필터를 추가하거나 메트릭을 분할할 때 **노드** 차원을 사용할 수 있으며, 기본적으로 트랜잭션 노드당 메트릭 값과 ABS 멤버의 유효성 검사기 노드를 제공합니다.

## <a name="analyze-log-data"></a>로그 데이터 분석

다음은 Azure 블록 체인 서비스 멤버를 모니터링하는 데 도움이되는 로그 검색 모음에 입력 할 수있는 몇 가지 쿼리입니다. 이러한 쿼리는 [새 언어](https://docs.microsoft.com/azure/azure-monitor/log-query/log-query-overview)에서 작동합니다.

Blockchain 응용 프로그램 로그의 오류 조건을 쿼리하려면 아래 쿼리를 사용하십시오.

```
BlockchainApplicationLog | where BlockchainMessage contains "ERROR" or BlockchainMessage contains "fatal"

```

Blockchain 프록시 로그의 오류 조건을 쿼리하려면 아래 쿼리를 사용하십시오.  


```
BlockchainProxyLog
| filter Code != 200
| limit 500

```
Azure 로그에서 사용할 수 있는 시간 필터를 사용하여 특정 시간 범위에 대한 쿼리를 필터링할 수 있습니다.

## <a name="monitor-azure-blockchain-service-data-reference"></a>Azure 블록 체인 서비스 데이터 참조 모니터링  

이 문서에서는 Azure 블록 체인 서비스의 성능 및 가용성을 분석하기 위해 수집 된 로그 및 메트릭 데이터에 대한 참조를 제공합니다.  

### <a name="resource-logs"></a>리소스 로그

모든 리소스 로그는 블록 체인 서비스와 관련된 몇 가지 고유 속성을 가진 최상위 공통 스키마를 공유합니다. 기사를 참조할 수 있습니다 [최상위 리소스 로그 스키마,](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-logs-schema#top-level-resource-logs-schema)Azure Blockchain 서비스 특정 속성의 세부 정보는 아래에서 다룹니다.  

다음 표에는 Azure 모니터 로그 또는 Azure 저장소에서 수집될 때 Azure Blockchain 프록시 로그에 대한 속성이 나열됩니다.  


| 속성 이름  | 설명 |
|:---|:---|
| time | 작업이 발생한 날짜 및 시간(UTC)입니다. |
| Resourceid  | 로그가 활성화된 Azure 블록 체인 서비스 리소스입니다.  |
| category  |Azure 블록 체인 서비스의 경우 가능한 값은 **프록시 로그** 및 **응용 프로그램 로그입니다.** |
| operationName  | 이 이벤트가 나타내는 작업의 이름입니다.   |
| 로그 수준  | 기본적으로 Azure 블록 체인 서비스는 **정보** 로그 수준을 활성화합니다.   |
| 노드 위치  | 블록 체인 멤버가 배포되는 Azure 지역입니다.  |
| 블록체인노드네임  | 작업이 수행되는 Azure 블록 체인 서비스 멤버의 노드 이름입니다.   |
| 이스메라코  | 기본 블록 체인 프로토콜에 의해 호출되는 이 방법은 쿼럼에서 eth_sendTransactions, eth_getBlockByNumber 등이 될 수 있습니다.  |
| 에이전트  | 웹 브라우저 모질라, 에지 등과 같은 사용자를 대신하여 행동하는 사용자 에이전트 값의 예는 다음과 같습니다 : "모질라 / 5.0 (리눅스 x64) node.js/ 8.16.0 v8 / 6.2.414.77"  |
| 코드   | HTTP 오류 코드. 일반적으로 4XX 및 5XX는 오류 조건입니다.  |
| 노드 호스트  | 노드의 DNS 이름입니다.   |
| 요청 메서드 이름 | HTTP 메서드라는 HTTP 메서드는 여기에서 가능한 값은 멤버 만들기, 기존 멤버의 세부 정보를 얻기 위한 GET, 삭제 멤버에 대한 DELETE, 멤버 업데이트용 PATCH입니다.   |
| 블록체인회원이름  | 사용자가 제공하는 Azure 블록 체인 서비스 회원 이름입니다.  |
| 컨소시엄 | 사용자가 제공한 컨소시엄 이름입니다.   |
| 원격  | 요청이 오는 클라이언트의 IP입니다.  |
| RequestSize  | 바이트로 이루어진 요청의 크기입니다.  |
| 요청 시간  | 요청 의 기간(밀리초 단위)입니다.|




다음 표에는 Azure Blockchain 응용 프로그램 로그에 대한 속성이 나열되어 있습니다.


| 속성 이름  | 설명 |
|:---|:---|
| time | 작업이 발생한 날짜 및 시간(UTC)입니다. |
| Resourceid  | 로그가 활성화된 Azure 블록 체인 서비스 리소스입니다.|
| category  |Azure 블록 체인 서비스의 경우 가능한 값은 **프록시 로그** 및 **응용 프로그램 로그입니다.**  |
| operationName  | 이 이벤트가 나타내는 작업의 이름입니다.   |
| 로그 수준  | 기본적으로 Azure 블록 체인 서비스는 **정보** 로그 수준을 활성화합니다.   |
| 노드 위치  | 블록 체인 멤버가 배포되는 Azure 지역입니다.  |
| 블록체인노드네임  | 작업이 수행되는 Azure 블록 체인 서비스 멤버의 노드 이름입니다.   |
| 블록체인메시지    | 이 필드에는 데이터 일반 로그인 블록체인 응용 프로그램 로그가 포함됩니다. ABS-쿼럼의 경우 정원회 로그가 있습니다. 그것은 정보, 오류, 경고 및 실행 된 작업에 대 한 자세한 정보를 제공 하는 문자열은 로그 항목의 종류에 대 한 정보를 가지고 있습니다.   |
| TenantID    | Azure 블록 체인 서비스의 지역별 테넌트입니다. 이 필드의 형식은 https://westlake-rp-prod. <region>.cloudapp.azure.com 지역이 배포된 구성원의 Azure 영역을 지정합니다.       |
| SourceSystem   | 시스템은 로그를 채우고, 이 경우 **Azure**.    |



### <a name="metrics"></a>메트릭

다음 표에는 Azure 블록 체인 서비스에 대해 수집된 플랫폼 메트릭이 나열되어 있습니다. 모든 메트릭은 네임스페이스 **Azure 블록체인 서비스** 표준 메트릭에 저장됩니다.

Azure 모니터가 지원되는 모든 메트릭 목록(Azure 블록체인 서비스 포함)은 [Azure 모니터 지원 메트릭을](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported)참조하십시오.

### <a name="blockchain-metrics"></a>블록체인 지표

다음 표는 Azure 블록체인 서비스 구성원 리소스에 대해 수집되는 블록체인 메트릭 목록을 지정합니다.


| 메트릭 이름 | 단위  |  집계 유형| 설명   |
|---|---|---|---|
| 보류 중인 트랜잭션   | 개수  |  평균 | 채굴대기 중인 트랜잭션 수입니다.   |
| 가공블록   | 개수  | 합계  |  각 시간 간격으로 처리되는 블록 수입니다. 현재 블록 크기는 5초이므로 각 노드는 5분 내에 12개의 블록과 60개의 블록을 처리합니다.   |
|처리된 트랜잭션    | 개수  | 합계  | 블록에서 처리된 트랜잭션 수입니다.    |
|큐에 대기된 트랜잭션    |  개수 | 평균  | 즉시 채굴할 수 없는 트랜잭션 수입니다. 그들은 순서에 도착하고 미래의 하나가 이전 트랜잭션이 도착하기를 기다리고 있기 때문일 수 있습니다. 또는 두 개의 트랜잭션이 한 번만 사용되는 수(nonce)와 동일한 가스 값을 가지므로 두 번째 트랜잭션은 채굴할 수 없습니다.   |

### <a name="connection-metrics"></a>연결 메트릭  

다음 표에는 Azure 블록 체인 서비스 구성원 리소스에 대해 수집되는 다양한 연결 메트릭이 나열되어 있습니다. NGINX 프록시 메트릭입니다.


| 메트릭 이름 | 단위  |  집계 유형| 설명 |
|---|---|---|---|
| 허용된 연결   | 개수  |  합계 | 허용되는 클라이언트 연결의 총 수입니다.   |
| 활성 연결 수  | 개수  | 평균  |  대기 연결을 포함한 현재 활성 클라이언트 연결 수입니다.    |
|처리된 연결    | 개수  | 합계  | 처리된 총 연결 수입니다. 일반적으로 일부 리소스 제한에 도달하지 않는 한 매개 변수 값은 허용된 연결과 동일합니다.     |
|처리된 요청     |  개수 | 합계  | 클라이언트 요청의 총 수입니다.  |


### <a name="performance-metrics"></a>성능 메트릭

다음 표에는 Azure Blockchain 구성원 리소스의 각 노드에 대해 수집되는 성능 메트릭이 나열되어 있습니다.  


| 메트릭 이름 | 단위  |  집계 유형| 설명   |
|---|---|---|---|
| CPU 사용량 비율   | 백분율  |  최대값 | CPU 사용량의 백분율입니다.     |
| IO 읽기 바이트   | 킬로바이트   | 합계  |  블록 체인 멤버 리소스의 모든 노드에서 IO 읽기 바이트의 합계입니다.      |
|IO 쓰기 바이트     | 킬로바이트   | 합계  | IO의 합계는 블록 체인 멤버 리소스의 모든 노드에서 바이트를 씁니다.     |
|메모리 제한       |  기가바이트   | 평균    | 노드당 블록 체인 프로세스에 사용할 수있는 최대 메모리입니다. |
|메모리 사용량     | 기가바이트  |  평균 | 모든 노드에서 평균으로 사용되는 메모리 양입니다.  |
| 메모리 사용량 백분율     | 백분율   | 평균  |  모든 노드에서 평균으로 사용된 메모리의 백분율입니다.       |
|스토리지 사용      | 기가바이트   | 평균  | 사용된 스토리지의 GB는 모든 노드에서 평균으로 사용됩니다.       |


## <a name="next-steps"></a>다음 단계

[블록체인 데이터 관리자에](https://docs.microsoft.com/azure/blockchain/service/data-manager) 대해 자세히 알아보고 블록 체인 데이터를 캡처하고 Azure 이벤트 그리드로 변환합니다.
