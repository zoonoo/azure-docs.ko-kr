---
title: C# 코드를 사용하여 Gen1 환경에서 데이터 쿼리 - Azure Time Series Insights Gen1 | Microsoft Docs
description: C#으로 작성한 사용자 지정 앱을 사용하여 Azure Time Series Insights Gen1 환경에서 데이터를 쿼리하는 방법을 알아봅니다.
ms.service: time-series-insights
services: time-series-insights
author: shreyasharmamsft
ms.author: shresha
manager: cnovak
ms.reviewer: orspodek
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 09/30/2020
ms.custom: seodec18
ms.openlocfilehash: ccc49221514f7846148ff2958e9fe8954fba074e
ms.sourcegitcommit: 7f59e3b79a12395d37d569c250285a15df7a1077
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/02/2021
ms.locfileid: "110790889"
---
# <a name="query-data-from-the-azure-time-series-insights-gen1-environment-using-c-sharp"></a>C#을 사용하여 Azure Time Series Insights Gen1 환경에서 데이터 쿼리

> [!CAUTION]
> 이는 Gen1 문서입니다.

이 C# 예는 [Gen1 쿼리 API](/rest/api/time-series-insights/gen1-query)를 사용하여 Azure Time Series Insights Gen1 환경에서 데이터를 쿼리하는 방법을 보여 줍니다.

> [!TIP]
> [https://github.com/Azure-Samples/Azure-Time-Series-Insights](https://github.com/Azure-Samples/Azure-Time-Series-Insights/tree/master/gen1-sample/csharp-tsi-gen1-sample)에서 Gen1 C# 코드 샘플을 확인합니다.

## <a name="summary"></a>요약

아래 샘플 코드에서는 다음과 같은 기능을 보여 줍니다.

* [Microsoft.IdentityModel.Clients.ActiveDirectory](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/)를 사용하여 Azure Active Directory를 통해 액세스 토큰을 획득하는 방법

* 후속 쿼리 API 요청의 `Authorization` 헤더에서 획득한 액세스 토큰을 전달하는 방법

* 샘플은 HTTP 요청이 다음 항목에 어떻게 이루어지는지 보여 주는 각 Gen1 쿼리 API를 호출합니다.
  * [Get Environments API](/rest/api/time-series-insights/gen1-query-api#get-environments-api) - 사용자가 액세스할 수 있는 환경 반환
  * [Get Environment Availability API](/rest/api/time-series-insights/gen1-query-api#get-environment-availability-api)
  * [Get Environment Metadata API](/rest/api/time-series-insights/gen1-query-api#get-environment-metadata-api) - 환경 메타데이터 검색
  * [Get Environments Events API](/rest/api/time-series-insights/gen1-query-api#get-environment-events-api)
  * [Get Environment Aggregates API](/rest/api/time-series-insights/gen1-query-api#get-environment-aggregates-api)

* WSS를 사용하여 다음 API에 메시지를 보내는 Gen1 쿼리 API와 상호 작용하는 방법:

  * [Get Environment Events Streamed API](/rest/api/time-series-insights/gen1-query-api#get-environment-events-streamed-api)
  * [Get Environment Aggregates Streamed API](/rest/api/time-series-insights/gen1-query-api#get-environment-aggregates-streamed-api)

## <a name="prerequisites-and-setup"></a>필수 조건 및 설정

샘플 코드를 컴파일 및 실행하기 전에 다음 단계를 완료합니다.

1. [Gen1 Azure Time Series Insights](./time-series-insights-get-started.md) 환경을 프로비저닝합니다.
1. [인증 및 권한 부여](time-series-insights-authentication-and-authorization.md)에 설명된 대로 Azure Active Directory에 대한 Azure Time Series Insights 환경을 구성합니다.
1. 필요한 프로젝트 종속성을 설치합니다.
1. 각 **#DUMMY#** 를 적절 한 환경 식별자로 바꿔서 아래 샘플 코드를 편집합니다.
1. Visual Studio 내에서 코드를 실행합니다.

## <a name="project-dependencies"></a>프로젝트 종속성

최신 버전의 Visual Studio를 사용하는 것이 좋습니다.

* [Visual Studio 2019](https://visualstudio.microsoft.com/vs/) - 버전 16.4.2+

샘플 코드에는 다음과 같은 두 가지 필수 종속성이 있습니다.

* [Microsoft.IdentityModel.Clients.ActiveDirectory](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/) - 3.13.9 패키지.
* [Newtonsoft.Json](https://www.nuget.org/packages/Newtonsoft.Json) - 9.0.1 패키지

**빌드** > **솔루션 빌드** 옵션을 선택하여 Visual Studio 2019에서 패키지를 다운로드합니다.

또는 [NuGet 2.12+](https://www.nuget.org/)를 사용하여 패키지를 추가합니다.

* `dotnet add package Newtonsoft.Json --version 9.0.1`
* `dotnet add package Microsoft.IdentityModel.Clients.ActiveDirectory --version 3.13.9`

## <a name="c-sample-code"></a>C# 샘플 코드

C# 샘플 코드에 액세스하려면 [Azure Time Series Insights](https://github.com/Azure-Samples/Azure-Time-Series-Insights/blob/master/gen1-sample/csharp-tsi-gen1-sample/Program.cs)] 리포지토리를 참조하세요.

## <a name="next-steps"></a>다음 단계

* 쿼리에 대해 자세히 알아보려면 [Query API 참조](/rest/api/time-series-insights/gen1-query-api)를 참조하세요.

* [클라이언트 SDK를 사용하여 JavaScript 앱을](https://github.com/microsoft/tsiclient) Time Series Insights에 연결하는 방법을 읽어보세요.
Azure-Samples/Azure-Time-Series-Insights/gen1-sample/csharp-tsi-gen1-sample/Program.cs