---
title: 사용 하 여 Azure Time Series Insights 미리 보기 환경에서 데이터를 쿼리 C# 코드 | Microsoft Docs
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
ms.date: 05/09/2019
ms.custom: seodec18
ms.openlocfilehash: fc5f35aedd52e206433afb0f556bc1cde8296232
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/27/2019
ms.locfileid: "66237469"
---
# <a name="query-data-from-the-azure-time-series-insights-preview-environment-using-c"></a>사용 하 여 Azure Time Series Insights 미리 보기 환경에서 데이터 쿼리C#

이 C# 예제에서는 Azure Time Series Insights 미리 보기 환경에서에서 데이터를 쿼리 하는 방법을 보여 줍니다.

이 샘플은 쿼리 API 사용의 몇 가지 기본 예제를 보여 줍니다.

1. 준비 단계로, Azure Active Directory API를 통해 액세스 토큰을 가져옵니다. 이 토큰을 모든 쿼리 API 요청의 `Authorization` 헤더에 전달합니다. 비대화형 애플리케이션을 설정하려면 [인증 및 권한 부여](time-series-insights-authentication-and-authorization.md)를 참조하세요. 또한 샘플의 시작 부분에 정의된 모든 상수를 올바르게 설정합니다.
1. 사용자가 액세스 권한을 가진 환경 목록을 가져옵니다. 환경 중 하나를 관심 환경으로 선택하고 이 환경에 대해 추가 데이터를 쿼리합니다.
1. HTTPS 요청의 예로, 관심 환경에 대해 가용성 데이터를 요청합니다.
1. 웹 소켓 요청의 예로, 관심 환경에 대해 이벤트 집계 데이터를 요청합니다. 전체 가용성 시간 범위에 대한 데이터가 요청됩니다.

> [!NOTE]
> 이 예제 코드를 다운로드 [ https://github.com/Azure-Samples/Azure-Time-Series-Insights ](https://github.com/Azure-Samples/Azure-Time-Series-Insights/tree/master/csharp-tsi-preview-sample)합니다.

## <a name="c-example"></a>C# 예제

[!code-csharp[csharpquery-example](~/samples-tsi/csharp-tsi-preview-sample/DataPlaneClientSampleApp/Program.cs)]

> [!NOTE]
> 위의 코드 샘플은 기본 환경 값을 변경 하지 않고 실행할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- 쿼리에 대 한 자세한 내용은 참조는 [쿼리 API 참조](/rest/api/time-series-insights/preview-query)합니다.

- 읽기 방법 하 [JavaScript 단일 페이지 앱 연결](tutorial-create-tsi-sample-spa.md) Time Series Insights에 있습니다.