---
title: Azure Cosmos DB 서비스를 사용할 수 없는 예외 문제 해결
description: Cosmos DB 서비스를 사용할 수 없는 예외를 진단 하 고 해결 하는 방법
author: j82w
ms.service: cosmos-db
ms.date: 07/13/2020
ms.author: jawilley
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: 12ecec7cf8f406ed53fb5e054fc304bf672cbbb0
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87294264"
---
# <a name="diagnose-and-troubleshoot-azure-cosmos-db-service-unavailable"></a>서비스를 사용할 수 Azure Cosmos DB 진단 및 문제 해결
SDK가 Azure Cosmos DB 서비스에 연결할 수 없습니다.

## <a name="troubleshooting-steps"></a>문제 해결 단계
다음 목록에는 서비스를 사용할 수 없는 예외에 대 한 알려진 원인과 해결 방법이 나와 있습니다.

### <a name="1-the-required-ports-are-not-enabled"></a>1. 필요한 포트를 사용할 수 없습니다.
[필요한 포트](performance-tips-dotnet-sdk-v3-sql.md#networking) 를 모두 사용 하도록 설정 했는지 확인 합니다.

## <a name="if-an-existing-application-or-service-started-getting-503"></a>기존 응용 프로그램 또는 서비스에서 503 가져오기 시작

### <a name="1-there-is-an-outage"></a>1. 작동이 중단 됩니다.
[Azure 상태](https://status.azure.com/status) 를 확인 하 여 진행 중인 문제가 있는지 확인 합니다.

### <a name="2-snat-port-exhaustion"></a>2. SNAT 포트 소모
[SNAT 포트 소모 가이드](troubleshoot-dot-net-sdk.md#snat)를 따르세요.

### <a name="3-the-required-ports-are-being-blocked"></a>3. 필요한 포트가 차단 되 고 있습니다.
[필요한 포트](performance-tips-dotnet-sdk-v3-sql.md#networking) 를 모두 사용 하도록 설정 했는지 확인 합니다.

## <a name="next-steps"></a>다음 단계
* Azure Cosmos DB .NET SDK를 사용 하는 경우 문제 [진단 및 해결](troubleshoot-dot-net-sdk.md)
* [.Net V3](performance-tips-dotnet-sdk-v3-sql.md) 및 [.net V2](performance-tips.md) 에 대 한 성능 지침 알아보기