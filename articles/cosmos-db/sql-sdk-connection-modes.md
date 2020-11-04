---
title: Azure Cosmos DB SQL SDK 연결 모드
description: Azure Cosmos DB SQL Sdk에서 제공 되는 다양 한 연결 모드에 대해 알아봅니다.
author: ealsur
ms.service: cosmos-db
ms.topic: how-to
ms.date: 10/14/2020
ms.author: maquaran
ms.custom: devx-track-dotnet, contperfq2
ms.openlocfilehash: 3eb10452dda0227bcada3a576d28fb55089397cb
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2020
ms.locfileid: "93305114"
---
# <a name="azure-cosmos-db-sql-sdk-connectivity-modes"></a>Azure Cosmos DB SQL SDK 연결 모드
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

클라이언트에서 Azure Cosmos DB에 연결 하는 방법은 특히 관찰 되는 클라이언트 쪽 대기 시간에 대해 중요 한 성능에 영향을 미칩니다. Azure Cosmos DB는 HTTPS를 통한 간단한 오픈 RESTful 프로그래밍 모델을 제공 합니다. 또한 효율적인 TCP 프로토콜을 제공 합니다 .이 프로토콜은 통신 모델에서 RESTful TLS를 사용 하 여 초기 인증 및 암호화 트래픽에 사용 됩니다.

## <a name="available-connectivity-modes"></a>사용 가능한 연결 모드

사용 가능한 두 가지 연결 모드는 다음과 같습니다.

  * 게이트웨이 모드
      
    게이트웨이 모드는 모든 SDK 플랫폼에서 지원 됩니다. 응용 프로그램이 엄격한 방화벽 제한을 사용 하는 회사 네트워크 내에서 실행 되는 경우 표준 HTTPS 포트 및 단일 DNS 끝점을 사용 하기 때문에 게이트웨이 모드를 선택 하는 것이 가장 좋습니다. 그러나 성능 단점은 데이터를 Azure Cosmos DB에서 읽거나 쓸 때마다 게이트웨이 모드에 추가 네트워크 홉이 포함 된다는 것입니다. 또한 소켓 연결 수가 제한 된 환경에서 응용 프로그램을 실행할 때 게이트웨이 연결 모드를 권장 합니다.

    특히 [소비 계획](../azure-functions/functions-scale.md#consumption-plan)에서 Azure Functions SDK를 사용 하는 경우 [연결에 대 한 현재 제한](../azure-functions/manage-connections.md)사항을 알고 있어야 합니다.

  * 직접 모드

    직접 모드는 TCP 프로토콜을 통한 연결을 지원 하며 네트워크 홉이 적기 때문에 더 나은 성능을 제공 합니다. 응용 프로그램은 백 엔드 복제본에 직접 연결 됩니다. 직접 모드는 현재 .NET 및 Java SDK 플랫폼 에서만 지원 됩니다.
     
:::image type="content" source="./media/performance-tips/connection-policy.png" alt-text="Azure Cosmos DB 연결 모드" border="false":::

이러한 연결 모드는 기본적으로 데이터 평면 요청에서 문서 읽기 및 쓰기를 수행 하는 경로를 클라이언트 컴퓨터에서 Azure Cosmos DB 백 엔드의 파티션으로 가져오는 조건입니다. 직접 모드는 최상의 성능을 위해 기본 설정 된 옵션입니다 .이 옵션을 사용 하면 클라이언트가 백 엔드를 사용 하지 않고 *직접* Azure Cosmos DB 백 엔드에 있는 파티션에 대 한 TCP 연결을 직접 열고 요청을 보낼 수 있습니다. 이와 대조적으로, 게이트웨이 모드에서 클라이언트에 의해 수행 된 요청은 Azure Cosmos DB 프런트 엔드에 있는 "게이트웨이" 서버로 라우팅됩니다. 그러면 Azure Cosmos DB 백 엔드에서 적절 한 파티션으로 요청을 팬 합니다.

## <a name="service-port-ranges"></a>서비스 포트 범위

직접 모드를 사용 하는 경우 게이트웨이 포트 외에도 Azure Cosmos DB에서 동적 TCP 포트를 사용 하므로 1만과 2만 사이의 포트 범위가 열려 있는지 확인 해야 합니다. [전용 끝점](./how-to-configure-private-endpoints.md)에서 직접 모드를 사용 하는 경우 TCP 포트의 전체 범위 (0 ~ 65535)가 열려 있어야 합니다. 이러한 포트가 열려 있지 않은 상태에서 TCP 프로토콜을 사용 하려고 하면 503 서비스를 사용할 수 없음 오류가 발생할 수 있습니다.

다음 표에서는 각 API에 사용 되는 다양 한 Api 및 서비스 포트에 사용할 수 있는 연결 모드의 요약을 보여 줍니다.

|연결 모드  |지원되는 프로토콜  |지원되는 SDK  |API/서비스 포트  |
|---------|---------|---------|---------|
|게이트웨이  |   HTTPS    |  모든 Sdk    |   SQL (443), MongoDB (10250, 10255, 10256), 테이블 (443), Cassandra (10350), 그래프 (443) <br> 10250 포트는 지역에서 복제를 사용 하지 않고 MongoDB 인스턴스에 대 한 기본 Azure Cosmos DB API에 매핑됩니다. 반면에 포트 10255 및 10256은 지역에서 복제를 포함 하는 인스턴스에 매핑됩니다.   |
|직접    |     TCP    |  .NET SDK    | 공개/서비스 끝점을 사용 하는 경우: 1만 ~ 2만 범위의 포트<br>개인 끝점을 사용 하는 경우: 0-65535 범위의 포트 |

## <a name="next-steps"></a>다음 단계

특정 SDK 플랫폼 성능 최적화:

* [.NET V2 SDK 성능 팁](performance-tips.md)

* [.NET V3 SDK 성능 팁](performance-tips-dotnet-sdk-v3-sql.md)
 
* [Java V4 SDK 성능 팁](performance-tips-java-sdk-v4-sql.md)