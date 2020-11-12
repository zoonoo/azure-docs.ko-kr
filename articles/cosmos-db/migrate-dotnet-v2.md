---
title: Azure Cosmos DB .NET SDK 2.0를 사용 하도록 응용 프로그램 마이그레이션 (Cosmos)
description: 기존 .NET 응용 프로그램을 v1 SDK에서 .NET SDK v2 for Core (SQL) API로 업그레이드 하는 방법에 대해 알아봅니다.
author: stefArroyo
ms.author: esarroyo
ms.service: cosmos-db
ms.topic: how-to
ms.date: 10/15/2020
ms.openlocfilehash: 88c40452da72ed4ab43d2d7613636136a5b78cfe
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/12/2020
ms.locfileid: "94550086"
---
# <a name="migrate-your-application-to-use-the-azure-cosmos-db-net-sdk-v2"></a>Azure Cosmos DB .NET SDK v2를 사용 하도록 응용 프로그램 마이그레이션

> [!IMPORTANT]
> .NET SDK의 v3은 현재 사용할 수 있으며 v 2에서 v3로의 마이그레이션 계획은 [여기](migrate-dotnet-v3.md)에서 사용할 수 있습니다. Azure Cosmos DB .NET SDK v2에 대해 알아보려면 [릴리스 정보](sql-api-sdk-dotnet.md), [.net GitHub 리포지토리](https://github.com/Azure/azure-cosmos-dotnet-v2), .Net SDK V2 [성능 팁](performance-tips.md)및 [문제 해결 가이드](troubleshoot-dot-net-sdk.md)를 참조 하세요.
>

이 문서에서는 기존 v1 .NET 응용 프로그램을 Azure Cosmos DB .NET SDK v2 for Core (SQL) API로 업그레이드 하는 몇 가지 고려 사항을 중점적으로 설명 합니다. Azure Cosmos DB .NET SDK v2는 `Microsoft.Azure.DocumentDB` 네임 스페이스에 해당 합니다. 다음 Azure Cosmos DB .NET 플랫폼에서 v2 SDK를 사용 하도록 응용 프로그램을 마이그레이션하는 경우이 문서에 제공 된 정보를 사용할 수 있습니다 `Microsoft.Azure.Cosmos` .

* SQL API 용 Azure Cosmos DB .NET Framework v1 SDK
* SQL API 용 Azure Cosmos DB .NET Core SDK v1

## <a name="whats-available-in-the-net-v2-sdk"></a>.NET v2 SDK에서 제공 되는 기능

V2 SDK에는 다음과 같은 다양 한 유용성 및 성능 향상이 포함 되어 있습니다.

* 비 Windows 클라이언트에 대 한 TCP 직접 모드 지원
* 다중 지역 쓰기 지원
* 쿼리 성능 향상
* 지리 공간적/geometry 컬렉션 및 인덱싱 지원
* 직접/TCP 전송 진단 기능이 향상 되었습니다.
* 설정 된 연결 수를 줄이기 위해 직접 TCP 전송 스택을 업데이트 합니다.
* RequestTimeout의 대기 시간 감소의 향상 된 기능

대부분의 다시 시도 논리와 하위 수준의 SDK는 대체로 변경 되지 않습니다.

## <a name="why-migrate-to-the-net-v2-sdk"></a>.NET v2 SDK로 마이그레이션하는 이유

다양 한 성능 향상 외에도 최신 SDK에서 수행 되는 새로운 기능 투자가 이전 버전으로 다시 이식할 수 없습니다.

또한 이전 Sdk는 최신 버전으로 대체 되 고 v1 SDK는 [유지 관리 모드로](sql-api-sdk-dotnet.md)전환 됩니다. 최상의 개발 환경을 위해 응용 프로그램을 최신 버전의 SDK로 마이그레이션하는 것이 좋습니다.

## <a name="major-changes-from-v1-sdk-to-v2-sdk"></a>V1 SDK에서 v2 SDK로의 주요 변경 내용

### <a name="direct-mode--tcp"></a>직접 모드 + TCP

이제 .NET v2 SDK는 직접 및 게이트웨이 모드를 모두 지원 합니다. 직접 모드는 TCP 프로토콜을 통한 연결을 지원 하 고 더 많은 네트워크 홉을 사용 하 여 백 엔드 복제본에 직접 연결할 때 더 나은 성능을 제공 합니다.

자세한 내용은 [AZURE COSMOS DB SQL SDK 연결 모드 가이드](sql-sdk-connection-modes.md)를 참조 하세요.

### <a name="session-token-formatting"></a>세션 토큰 형식 지정

V2 SDK는 더 이상 v1에서 사용 되는 *단순* 세션 토큰 형식을 사용 하지 않습니다. 대신 SDK에서 *벡터* 서식 지정을 사용 합니다. 형식은 서로 다른 버전을 사용 하는 클라이언트 응용 프로그램에 전달할 때 변환 해야 합니다.

자세한 내용은 [.NET SDK에서 세션 토큰 형식 변환](how-to-convert-session-token.md)을 참조 하세요.

### <a name="using-the-net-change-feed-processor-sdk"></a>.NET 변경 피드 프로세서 SDK 사용

.NET 변경 피드 프로세서 라이브러리 2.1. x에는 `Microsoft.Azure.DocumentDB` 2.0 이상이 필요 합니다.

2.1. x 라이브러리에는 다음과 같은 주요 변경 사항이 있습니다.

* 안정성 및 진단 가능 향상
* 오류 및 예외 처리 개선
* 분할 된 임대 컬렉션에 대 한 추가 지원
* `ChangeFeedDocument`추가 오류 처리 및 추적을 위한 인터페이스 및 클래스를 구현 하기 위한 고급 확장
* 사용자 지정 저장소를 사용 하 여 파티션당 연속 토큰을 유지 하기 위한 지원 추가

자세한 내용은 변경 피드 프로세서 라이브러리 [릴리스 정보](sql-api-sdk-dotnet-changefeed.md)를 참조 하세요.

### <a name="using-the-bulk-executor-library"></a>대량 실행자 라이브러리 사용

V2 bulk executor 라이브러리는 현재 Azure Cosmos DB .NET SDK 2.5.1 이상에 종속 되어 있습니다.

자세한 내용은 [Azure Cosmos DB 대량 실행자 라이브러리 개요](bulk-executor-overview.md) 및 .net 대량 실행자 라이브러리 [릴리스 정보](sql-api-sdk-bulk-executor-dot-net.md)를 참조 하세요.

## <a name="next-steps"></a>다음 단계

* 최대 성능을 얻기 위해 응용 프로그램을 최적화 하기 위해 SQL API v 2 용 Azure Cosmos DB를 사용 하 여 [추가 성능 팁](sql-api-get-started.md) 에 대해 알아봅니다.
* [V2 SDK로 수행할 수 있는 작업](sql-api-dotnet-samples.md) 에 대 한 자세한 정보