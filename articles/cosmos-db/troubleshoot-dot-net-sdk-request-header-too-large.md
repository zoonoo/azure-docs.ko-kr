---
title: Azure Cosmos DB 요청 헤더 문제 해결이 너무 크거나 400 잘못 된 요청입니다.
description: 요청 헤더를 진단 하 고 수정 하는 방법 너무 큼 예외
author: j82w
ms.service: cosmos-db
ms.date: 07/13/2020
ms.author: jawilley
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: 942af52c43a1d4526a42f20df40abf3f510e0ee6
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87294430"
---
# <a name="diagnose-and-troubleshoot-azure-cosmos-db-request-header-too-large-message"></a>Azure Cosmos DB 요청 헤더의 진단 및 문제 해결 메시지 너무 큼 메시지
HTTP 오류 코드 400를 사용 하 여 요청 헤더 너무 큼 메시지가 throw 됩니다. 이 오류는 요청 헤더의 크기가 너무 커지고 최대 허용 크기를 초과 하는 경우에 발생 합니다. 최신 버전의 SDK를 사용 하는 것이 좋습니다. 이러한 버전이 예외 메시지에 헤더 크기 추적을 추가 하기 때문에 버전 3(sp3) 또는 2.x 이상을 사용 해야 합니다.

## <a name="troubleshooting-steps"></a>문제 해결 단계
세션 또는 연속 토큰이 너무 클 경우 요청 헤더 너무 큼 메시지는 발생 합니다. 다음 섹션에서는 각 범주의 문제에 대 한 원인과 해결 방법을 설명 합니다.

### <a name="1-session-token-too-large"></a>1. 세션 토큰이 너무 큼

#### <a name="cause"></a>원인:
400 잘못 된 요청은 세션 토큰이 너무 크므로 발생 가능성이 높습니다. 다음 문이 true 이면 세션 토큰이 너무 큰지 확인 합니다.

* 이 오류는 만들기, 읽기, 업데이트 등의 시점 작업과 연속 토큰이 없는 경우에 발생 합니다.
* 응용 프로그램을 변경 하지 않고 예외를 시작 했습니다. 세션 토큰은 컨테이너의 파티션 수가 증가 함에 따라 증가 합니다. 파티션 수는 데이터의 양이 증가 하거나 처리량이 증가 함에 따라 증가 합니다.

#### <a name="temporary-mitigation"></a>임시 완화: 
클라이언트 응용 프로그램을 다시 시작 하 여 모든 세션 토큰을 다시 설정 합니다. 결국 세션 토큰은 문제를 일으킨 이전 크기로 다시 증가 하 게 됩니다. 따라서이 문제를 완전히 방지 하려면 다음 섹션의 솔루션을 사용 하세요.

#### <a name="solution"></a>해결책:
1. [.Net V3](performance-tips-dotnet-sdk-v3-sql.md) 또는 [.net V2](performance-tips.md) 성능 팁 문서의 지침을 따르고 TCP 프로토콜을 사용 하 여 직접 연결 모드를 사용 하도록 응용 프로그램을 변환 합니다. TCP 프로토콜을 사용 하는 직접 모드는 HTTP 프로토콜과 같은 헤더 크기 제한을 갖지 않으므로이 문제를 방지 합니다. 서비스 interop를 사용할 수 없는 경우 쿼리 작업에 대 한 수정이 포함 된 최신 버전의 SDK를 사용 해야 합니다.
2. TCP 프로토콜을 사용 하는 직접 연결 모드를 작업에 사용할 수 없는 경우 [클라이언트 일관성 수준을](how-to-manage-consistency.md)변경 하 여이를 완화 합니다. 세션 토큰은 Azure Cosmos DB의 기본 일관성 수준인 세션 일관성에만 사용 됩니다. 다른 일관성 수준은 세션 토큰을 사용 하지 않습니다.

### <a name="2-continuation-token-too-large"></a>2. 연속 토큰이 너무 큼

#### <a name="cause"></a>원인:
연속 토큰이 사용 되는 쿼리 작업에서 400 잘못 된 요청이 발생 합니다. 연속 토큰이 너무 크게 증가 했거나 쿼리에 다른 연속 토큰 크기가 있는 경우
    
#### <a name="solution"></a>해결책:
1. [.Net V3](performance-tips-dotnet-sdk-v3-sql.md) 또는 [.net V2](performance-tips.md) 성능 팁 문서의 지침을 따르고 TCP 프로토콜을 사용 하 여 직접 연결 모드를 사용 하도록 응용 프로그램을 변환 합니다. TCP 프로토콜을 사용 하는 직접 모드는 HTTP 프로토콜과 같은 헤더 크기 제한을 갖지 않으므로이 문제를 방지 합니다. 
3. TCP 프로토콜을 사용 하는 직접 연결 모드가 워크 로드에 대 한 옵션이 아닌 경우 옵션을 설정 해 보십시오 `ResponseContinuationTokenLimitInKb` . V2 또는 v3의에서이 옵션을 찾을 수 있습니다 `FeedOptions` `QueryRequestOptions` .

## <a name="next-steps"></a>다음 단계
* Azure Cosmos DB .NET SDK를 사용 하는 경우 문제 [진단 및 해결](troubleshoot-dot-net-sdk.md)
* [.Net V3](performance-tips-dotnet-sdk-v3-sql.md) 및 [.net V2](performance-tips.md) 에 대 한 성능 지침 알아보기
