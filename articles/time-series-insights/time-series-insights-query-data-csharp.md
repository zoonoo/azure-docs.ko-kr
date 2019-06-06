---
title: 사용 하 여 Azure Time Series Insights GA 환경에서 데이터를 쿼리 C# 코드 | Microsoft Docs
description: 이 문서에서는 C#(c-sharp) .NET 언어로 작성된 사용자 지정 앱을 코딩하여 Azure Time Series Insights 환경에서 데이터를 쿼리하는 방법을 설명합니다.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: dpalled
manager: cshankar
reviewer: jasonwhowell, kfile, tsidocs
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 06/05/2019
ms.custom: seodec18
ms.openlocfilehash: 250dd691c3ef3146d6768123de52bf0628b10e42
ms.sourcegitcommit: 1aefdf876c95bf6c07b12eb8c5fab98e92948000
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/06/2019
ms.locfileid: "66728960"
---
# <a name="query-data-from-the-azure-time-series-insights-ga-environment-using-c"></a>사용 하 여 Azure Time Series Insights GA 환경에서 데이터 쿼리C#

이 C# 예제에서는 Azure Time Series Insights GA 환경에서에서 데이터를 쿼리 하는 방법을 보여 줍니다.

이 샘플은 쿼리 API 사용의 몇 가지 기본 예제를 보여 줍니다.

1. 준비 단계로, Azure Active Directory API를 통해 액세스 토큰을 가져옵니다. 이 토큰을 모든 쿼리 API 요청의 `Authorization` 헤더에 전달합니다. 비대화형 애플리케이션을 설정하려면 [인증 및 권한 부여](time-series-insights-authentication-and-authorization.md)를 참조하세요. 또한 샘플의 시작 부분에 정의된 모든 상수를 올바르게 설정합니다.
1. 사용자가 액세스 권한을 가진 환경 목록을 가져옵니다. 환경 중 하나를 관심 환경으로 선택하고 이 환경에 대해 추가 데이터를 쿼리합니다.
1. HTTPS 요청의 예로, 관심 환경에 대해 가용성 데이터를 요청합니다.
1. 웹 소켓 요청의 예로, 관심 환경에 대해 이벤트 집계 데이터를 요청합니다. 전체 가용성 시간 범위에 대한 데이터가 요청됩니다.

> [!NOTE]
> 예제 코드에서 제공 됩니다 [ https://github.com/Azure-Samples/Azure-Time-Series-Insights ](https://github.com/Azure-Samples/Azure-Time-Series-Insights/tree/master/csharp-tsi-ga-sample)합니다.

## <a name="project-dependencies"></a>프로젝트 종속성

NuGet 패키지 추가 `Microsoft.IdentityModel.Clients.ActiveDirectory` 고 `Newtonsoft.Json`입니다.

## <a name="c-example"></a>C# 예제

[!code-csharp[csharpquery-example](~/samples-tsi/csharp-tsi-ga-sample/Program.cs)]

## <a name="next-steps"></a>다음 단계

- 쿼리에 대 한 자세한 내용은 참조는 [쿼리 API 참조](/rest/api/time-series-insights/ga-query-api)합니다.

- 읽기 방법 하 [JavaScript 단일 페이지 앱 연결](tutorial-create-tsi-sample-spa.md) Time Series Insights에 있습니다.