---
title: Java v4 SDK를 사용 하 여 HTTP 408 Azure Cosmos DB 또는 요청 시간 제한 문제 해결
description: Java v4 SDK를 사용 하 여 Java SDK 요청 시간 제한 예외를 진단 하 고 해결 하는 방법을 알아봅니다.
author: kushagrathapar
ms.service: cosmos-db
ms.date: 10/28/2020
ms.author: kuthapar
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: 45452109582be40f007ae57a00c2a151f216bdb8
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93103012"
---
# <a name="diagnose-and-troubleshoot-azure-cosmos-db-java-v4-sdk-request-timeout-exceptions"></a>Java v4 SDK 요청 시간 제한 예외 Azure Cosmos DB 진단 및 문제 해결
408 시간 제한이 발생 하기 전에 SDK에서 요청을 완료할 수 없는 경우 HTTP 오류가 발생 합니다.

## <a name="troubleshooting-steps"></a>문제 해결 단계
다음 목록에는 요청 시간 제한 예외에 대 한 알려진 원인과 해결 방법이 나와 있습니다.

### <a name="high-cpu-utilization"></a>높은 CPU 사용률
높은 CPU 사용률은 가장 일반적인 경우입니다. 최적 대기 시간을 위해 CPU 사용량은 약 40%가 되어야 합니다. 최대 (평균) CPU 사용률을 모니터링 하려면 10 초 간격으로 사용 합니다. CPU 급증은 단일 쿼리에 대해 여러 연결을 수행할 수 있는 파티션 간 쿼리를 사용 하는 것이 더 일반적입니다.

#### <a name="solution"></a>해결 방법:
SDK를 사용 하는 클라이언트 응용 프로그램을 확장 하거나 축소 해야 합니다.

### <a name="connection-throttling"></a>연결 제한
연결 제한은 호스트 컴퓨터의 연결 제한 또는 Azure SNAT(PAT) 포트 고갈 중 하나로 인해 발생할 수 있습니다.

### <a name="connection-limit-on-a-host-machine"></a>호스트 컴퓨터의 연결 제한
일부 Linux 시스템(예: 'Red Hat')에는 열려 있는 파일의 총 수에 상한이 있습니다. Linux의 소켓은 파일로 구현되므로 이 숫자는 총 연결 수도 제한합니다. 다음 명령을 실행합니다.

```bash
ulimit -a
```

#### <a name="solution"></a>해결 방법:
"Nofile"으로 식별 되는 열려 있는 최대 파일 수는 1만 이상 이어야 합니다. 자세한 내용은 Azure Cosmos DB Java SDK v4 [성능 팁](performance-tips-java-sdk-v4-sql.md)을 참조하세요.

### <a name="socket-or-port-availability-might-be-low"></a>소켓 또는 포트 가용성이 낮을 수 있습니다.
Azure에서 실행 하는 경우 Java SDK를 사용 하는 클라이언트는 Azure SNAT (PAT) 포트 고갈에 도달할 수 있습니다.

#### <a name="solution-1"></a>해결 방법 1:
Azure Vm에서 실행 하는 경우 [SNAT 포트 소모 가이드](troubleshoot-java-sdk-v4-sql.md#snat)를 따르세요.

#### <a name="solution-2"></a>해결 방법 2:
Azure App Service에서 실행 하는 경우 [연결 오류 문제 해결 가이드](../app-service/troubleshoot-intermittent-outbound-connection-errors.md#cause) 를 따르고 [App Service 진단을 사용](https://azure.github.io/AppService/2018/03/01/Deep-Dive-into-TCP-Connections-in-App-Service-Diagnostics.html)합니다.

#### <a name="solution-3"></a>해결 방법 3:
Azure Functions에서 실행 하는 경우 모든 관련 서비스 (Azure Cosmos DB 포함)에 대해 singleton 또는 정적 클라이언트를 유지 관리 하는 [Azure Functions 권장 사항을](../azure-functions/manage-connections.md#static-clients) 준수 하는지 확인 합니다. 호스팅 함수 앱의 형식 및 크기에 따라 [서비스 제한을](../azure-functions/functions-scale.md#service-limits) 확인 합니다.

#### <a name="solution-4"></a>해결 방법 4:
HTTP 프록시를 사용하는 경우 SDK `GatewayConnectionConfig`에서 구성된 연결 수를 지원할 수 있는지 확인합니다. 그렇지 않으면 연결 문제가 발생 합니다.

### <a name="create-multiple-client-instances"></a>여러 클라이언트 인스턴스 만들기
여러 클라이언트 인스턴스를 만들면 연결 경합 및 시간 제한 문제가 발생할 수 있습니다.

#### <a name="solution-1"></a>해결 방법 1:
[성능 팁](performance-tips-java-sdk-v4-sql.md#sdk-usage)을 따르고 전체 응용 프로그램에서 단일 CosmosClient 인스턴스를 사용 합니다.

#### <a name="solution-2"></a>해결 방법 2:
Singleton CosmosClient이 응용 프로그램에 있을 수 없는 경우 CosmosClient에서이 API를 통해 여러 Cosmos 클라이언트에서 연결 공유를 사용 하는 것이 좋습니다 `connectionSharingAcrossClientsEnabled(true)` . 여러 Cosmos 계정과 상호 작용 하는 동일한 JVM에 Cosmos Client 인스턴스가 여러 개 있는 경우이를 사용 하도록 설정 하면 Cosmos Client의 인스턴스 간에 가능 하면 직접 모드에서 연결을 공유할 수 있습니다. 이 옵션을 설정 하면 첫 번째 인스턴스화된 클라이언트의 연결 구성 (예: 소켓 시간 제한 구성, 유휴 시간 제한 구성)이 다른 모든 클라이언트 인스턴스에 사용 됩니다.

### <a name="hot-partition-key"></a>핫 파티션 키
Azure Cosmos DB는 전체 프로 비전 된 처리량을 실제 파티션에 균등 하 게 분산 합니다. 핫 파티션이 있으면 실제 파티션에 있는 하나 이상의 논리 파티션 키가 모든 물리적 파티션의 요청 단위 (/초)를 사용 하는 것입니다. 동시에 다른 실제 파티션에 대 한 o s/s가 사용 되지 않습니다. 증상으로, 사용 된 총 r u/초는 데이터베이스 또는 컨테이너에서 전체 프로 비전 된 r u/초 보다 작으므로 핫 논리 파티션 키에 대 한 요청에 대 한 제한 (429s)이 계속 표시 됩니다. 정규화 된 작업 부하 [메트릭을](monitor-normalized-request-units.md) 사용 하 여 작업에 핫 파티션이 발생 하는지 확인 합니다. 

#### <a name="solution"></a>해결 방법:
요청 볼륨과 저장소를 균등 하 게 분산 하는 좋은 파티션 키를 선택 합니다. [파티션 키를 변경](https://devblogs.microsoft.com/cosmosdb/how-to-change-your-partition-key/)하는 방법에 대해 알아봅니다.

### <a name="high-degree-of-concurrency"></a>높은 동시성 수준
응용 프로그램에서 동시성 수준이 높으면 채널 경합이 발생할 수 있습니다.

#### <a name="solution"></a>해결 방법:
SDK를 사용 하는 클라이언트 응용 프로그램을 확장 하거나 축소 해야 합니다.

### <a name="large-requests-or-responses"></a>대량 요청 또는 응답
대량 요청이 나 응답은 비교적 낮은 동시성을 사용 하는 경우에도 채널 및 악화 경합의 줄이 차단 될 수 있습니다.

#### <a name="solution"></a>해결 방법:
SDK를 사용 하는 클라이언트 응용 프로그램을 확장 하거나 축소 해야 합니다.

### <a name="failure-rate-is-within-the-azure-cosmos-db-sla"></a>실패율이 Azure Cosmos DB SLA 내에 있습니다.
응용 프로그램은 일시적인 오류를 처리 하 고 필요한 경우 다시 시도할 수 있습니다. 경로 만들기에서 서비스가 항목을 만들었는지 여부를 알 수 없기 때문에 408 예외는 다시 시도 되지 않습니다. 생성을 위해 동일한 항목을 다시 보내면 충돌 예외가 발생 합니다. 사용자 응용 프로그램 비즈니스 논리에는 충돌을 처리 하는 사용자 지정 논리가 있을 수 있습니다 .이는 기존 항목의 모호성 및 create retry의 충돌을 중단 합니다.

### <a name="failure-rate-violates-the-azure-cosmos-db-sla"></a>실패율이 Azure Cosmos DB SLA를 위반 합니다.
[Azure 지원](https://aka.ms/azure-support)에 문의 하세요.

## <a name="next-steps"></a>다음 단계
* Azure Cosmos DB Java v4 SDK를 사용 하는 경우 문제를 [진단 하 고 해결](troubleshoot-java-sdk-v4-sql.md) 합니다.
* [Java v4](performance-tips-java-sdk-v4-sql.md)에 대 한 성능 지침을 알아보세요.
