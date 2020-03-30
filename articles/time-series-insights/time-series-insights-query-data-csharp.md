---
title: C# 코드를 사용하여 GA 환경에서 데이터를 쿼리 - Azure 타임시리즈 인사이트 | 마이크로 소프트 문서
description: C#으로 작성된 사용자 지정 앱을 사용하여 Azure Time Series Insights 환경에서 데이터를 쿼리하는 방법을 알아봅니다.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 02/03/2020
ms.custom: seodec18
ms.openlocfilehash: 9f7819974e3548baf5e10f0bf9a2d656d9412beb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76987974"
---
# <a name="query-data-from-the-azure-time-series-insights-ga-environment-using-c"></a>C를 사용하여 Azure 타임시리즈 인사이트 GA 환경의 데이터 쿼리 #

이 C# 예제에서는 GA [쿼리 API를](https://docs.microsoft.com/rest/api/time-series-insights/ga-query) 사용하여 Azure Time Series Insights GA 환경에서 데이터를 쿼리하는 방법을 보여 줍니다.

> [!TIP]
> 에서 GA C# 코드 [https://github.com/Azure-Samples/Azure-Time-Series-Insights](https://github.com/Azure-Samples/Azure-Time-Series-Insights/tree/master/csharp-tsi-ga-sample)샘플 보기

## <a name="summary"></a>요약

아래 샘플 코드는 다음과 같은 기능을 보여 줍니다.

* [Microsoft.IdentityModel.Client.ActiveDirectory를](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/)사용하여 Azure Active Directory를 통해 액세스 토큰을 획득하는 방법.

* 후속 쿼리 API 요청의 `Authorization` 헤더에서 획득한 액세스 토큰을 전달하는 방법. 

* 샘플은 HTTP 요청이 다음에 어떻게 이루어지는지 보여 주는 각 GA 쿼리 API를 호출합니다.
    * [환경 API를 사용하여](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api#get-environments-api) 사용자가 액세스할 수 있는 환경을 반환합니다.
    * [환경 가용성 API 받기](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api#get-environment-availability-api)
    * [환경 메타데이터 API를 가져와](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api#get-environment-metadata-api) 환경 메타데이터 검색
    * [환경 이벤트 API 받기](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api#get-environment-events-api)
    * [환경 집계 API 가져오기](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api#get-environment-aggregates-api)
    
* WSS를 사용하여 GA 쿼리 API와 상호 작용하는 방법 :

   * [환경 이벤트 스트리밍 API 받기](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api#get-environment-events-streamed-api)
   * [환경 집계 스트리밍 API 가져오기](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api#get-environment-aggregates-streamed-api)

## <a name="prerequisites-and-setup"></a>전제 조건 및 설정

샘플 코드를 컴파일 및 실행하기 전에 다음 단계를 완료합니다.

1. [GA Azure 서계 정보 환경 프로비전합니다.](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-get-started)
1. [인증 및 권한 부여에](time-series-insights-authentication-and-authorization.md)설명된 대로 Azure Active Directory에 대한 Azure Time Series Insights 환경을 구성합니다. 
1. 필요한 프로젝트 종속성을 설치합니다.
1. 각 **#DUMMY#을** 적절한 환경 식별자로 대체하여 아래 샘플 코드를 편집합니다.
1. Visual Studio 내에서 코드를 실행합니다.

## <a name="project-dependencies"></a>프로젝트 종속성

최신 버전의 Visual Studio를 사용하는 것이 좋습니다.

* [비주얼 스튜디오 2019](https://visualstudio.microsoft.com/vs/) - 버전 16.4.2+

샘플 코드에는 두 가지 필수 종속성이 있습니다.

* [마이크로소프트.아이덴티티모델.클라이언트.액티브 디렉토리](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/) - 3.13.9 패키지.
* [뉴턴소프트.Json](https://www.nuget.org/packages/Newtonsoft.Json) - 9.0.1 패키지.

**Build** > **빌드 솔루션** 옵션을 선택하여 Visual Studio 2019에서 패키지를 다운로드합니다.

또는 [NuGet 2.12+를](https://www.nuget.org/)사용하여 패키지를 추가합니다.

* `dotnet add package Newtonsoft.Json --version 9.0.1`
* `dotnet add package Microsoft.IdentityModel.Clients.ActiveDirectory --version 3.13.9`

## <a name="c-sample-code"></a>C# 샘플 코드

[!code-csharp[csharpquery-example](~/samples-tsi/csharp-tsi-ga-sample/Program.cs)]

## <a name="next-steps"></a>다음 단계

- 쿼리에 대해 자세히 알아보려면 [쿼리 API 참조를](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api)참조하십시오.

- [클라이언트 SDK를 사용하여 자바스크립트 앱을](https://github.com/microsoft/tsiclient) 타임시리즈 인사이트에 연결하는 방법을 읽어보세요.
