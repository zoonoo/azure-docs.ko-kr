---
title: C# - Azure 타임시리즈 인사이트를 사용하여 미리 보기 환경에서 데이터를 쿼리 | 마이크로 소프트 문서
description: C#으로 작성된 앱을 사용하여 Azure Time Series Insights 환경에서 데이터를 쿼리하는 방법을 알아봅니다.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 04/14/2020
ms.custom: seodec18
ms.openlocfilehash: fbc2cbc29cb23a21e7d3713091fc22f01bb1b15a
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/14/2020
ms.locfileid: "81379825"
---
# <a name="query-data-from-the-azure-time-series-insights-preview-environment-using-c"></a>C를 사용하여 Azure Time Series 인사이트 미리 보기 환경의 쿼리 데이터 #

이 C# 예제에서는 Azure Time Series Insights 미리 보기 환경에서 [미리 보기 데이터 액세스 API에서](https://docs.microsoft.com/rest/api/time-series-insights/preview) 데이터를 쿼리하는 방법을 보여 줍니다.

> [!TIP]
> 에서 미리 보기 C# [https://github.com/Azure-Samples/Azure-Time-Series-Insights](https://github.com/Azure-Samples/Azure-Time-Series-Insights/tree/master/csharp-tsi-preview-sample)코드 샘플

## <a name="summary"></a>요약

아래 샘플 코드는 다음과 같은 기능을 보여 줍니다.

* [Azure AutoRest에서](https://github.com/Azure/AutoRest)SDK 자동 생성 지원.
* [Microsoft.IdentityModel.Client.ActiveDirectory를](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/)사용하여 Azure Active Directory를 통해 액세스 토큰을 획득하는 방법.
* 후속 데이터 액세스 API 요청의 헤더에서 획득한 액세스 토큰을 `Authorization` 전달하는 방법. 
* 샘플은 HTTP 요청이 다음에 어떻게 이루어지는지 보여주는 콘솔 인터페이스를 제공합니다.

    * [환경 미리 보기 API](https://docs.microsoft.com/rest/api/time-series-insights/preview#preview-environments-apis)
        * [환경 가용성 API 및](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/getavailability) [이벤트 스키마 API 받기](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/geteventschema)
    * [쿼리 API 미리 보기](https://docs.microsoft.com/rest/api/time-series-insights/preview#query-apis)
        * [이벤트 API 가져오기,](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/execute#getevents) [시리즈 API 가져오기](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/execute#getseries)및 집계 시리즈 API [가져오기](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/execute#aggregateseries)
    * [타임시리즈 모델 API](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/execute#aggregateseries)
        * [계층 API](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/timeserieshierarchies/get) 및 [계층 배치 API](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/timeserieshierarchies/executebatch) 받기
        * [형식 API](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/timeseriestypes/get) 및 [형식 일괄 처리 API](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/timeseriestypes/executebatch) 받기
        * [인스턴스 API](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/timeseriesinstances/get) 및 [인스턴스 배치 API](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/timeseriesinstances/executebatch) 받기
* 고급 [검색](https://docs.microsoft.com/rest/api/time-series-insights/preview#search-features) 및 [TSX](https://docs.microsoft.com/rest/api/time-series-insights/preview#time-series-expression-and-syntax) 기능.

## <a name="prerequisites-and-setup"></a>전제 조건 및 설정

샘플 코드를 컴파일 및 실행하기 전에 다음 단계를 완료합니다.

1. [미리 보기 Azure 서열 인사이트 환경을 프로비전합니다.](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-how-to-manage#create-the-environment)
1. [인증 및 권한 부여에](time-series-insights-authentication-and-authorization.md)설명된 대로 Azure Active Directory에 대한 Azure Time Series Insights 환경을 구성합니다. 
1. [Readme.md](https://github.com/Azure-Samples/Azure-Time-Series-Insights/blob/master/csharp-tsi-preview-sample/DataPlaneClient/Readme.md) 지정된 대로 [GenerateCode.bat를](https://github.com/Azure-Samples/Azure-Time-Series-Insights/blob/master/csharp-tsi-preview-sample/DataPlaneClient/GenerateCode.bat) 실행하여 타임시리즈 인사이트 미리 보기 클라이언트 종속성을 생성합니다.
1. `TSIPreviewDataPlaneclient.sln` 솔루션을 열고 Visual `DataPlaneClientSampleApp` Studio에서 기본 프로젝트로 설정합니다.
1. [아래에](#project-dependencies) 설명된 단계를 사용하여 필요한 프로젝트 종속성을 설치하고 예제를 실행 `.exe` 파일로 컴파일합니다.
1. `.exe` 파일을 두 번 클릭하여 실행합니다.

## <a name="project-dependencies"></a>프로젝트 종속성

최신 버전의 Visual Studio를 사용하는 것이 좋습니다.

* [비주얼 스튜디오 2019](https://visualstudio.microsoft.com/vs/) - 버전 16.4.2+

샘플 코드에는 [packages.config](https://github.com/Azure-Samples/Azure-Time-Series-Insights/blob/master/csharp-tsi-preview-sample/DataPlaneClientSampleApp/packages.config) 파일에서 볼 수 있는 몇 가지 필수 종속성이 있습니다.

**Build** > **빌드 솔루션** 옵션을 선택하여 Visual Studio 2019에서 패키지를 다운로드합니다. 

또는 [NuGet 2.12+](https://www.nuget.org/)를 사용하여 각 패키지를 추가합니다. 다음은 그 예입니다.

* `dotnet add package Microsoft.IdentityModel.Clients.ActiveDirectory --version 4.5.1`

## <a name="c-sample-code"></a>C# 샘플 코드

[!code-csharp[csharpquery-example](~/samples-tsi/csharp-tsi-preview-sample/DataPlaneClientSampleApp/Program.cs)]

> [!NOTE]
> * 코드 샘플은 기본 환경 변수를 변경하지 않고 실행할 수 있습니다.
> * 코드 샘플은 .NET 실행 콘솔 앱으로 컴파일됩니다.

## <a name="next-steps"></a>다음 단계

- 쿼리에 대해 자세히 알아보려면 [쿼리 API 참조를](https://docs.microsoft.com/rest/api/time-series-insights/preview-query)참조하십시오.

- [클라이언트 SDK를 사용하여 자바스크립트 앱을](https://github.com/microsoft/tsiclient) 타임시리즈 인사이트에 연결하는 방법을 읽어보세요.
