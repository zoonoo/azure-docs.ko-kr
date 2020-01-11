---
title: -Azure Time Series Insights을 사용 하 여 C# 미리 보기 환경에서 데이터 쿼리 Microsoft Docs
description: 에서 C#작성 한 앱을 사용 하 여 Azure Time Series Insights 환경에서 데이터를 쿼리 하는 방법에 대해 알아봅니다.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 12/05/2019
ms.custom: seodec18
ms.openlocfilehash: a1d32bf7ea296ed2c4ed9351fcefe400c03effa5
ms.sourcegitcommit: 12a26f6682bfd1e264268b5d866547358728cd9a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/10/2020
ms.locfileid: "75861441"
---
# <a name="query-data-from-the-azure-time-series-insights-preview-environment-using-c"></a>을 사용 하 여 Azure Time Series Insights 미리 보기 환경에서 데이터 쿼리C#

이 C# 예제에서는 Azure Time Series Insights 미리 보기 환경에서 데이터를 쿼리 하는 방법을 보여 줍니다.

이 샘플은 쿼리 API 사용의 몇 가지 기본 예제를 보여 줍니다.

1. 준비 단계로, Azure Active Directory API를 통해 액세스 토큰을 가져옵니다. 이 토큰을 모든 쿼리 API 요청의 `Authorization` 헤더에 전달합니다. 비 대화형 응용 프로그램을 설정 하려면 [인증 및 권한 부여](time-series-insights-authentication-and-authorization.md)를 참조 하세요. 또한 샘플의 시작 부분에 정의된 모든 상수를 올바르게 설정합니다.
1. 사용자가 액세스 권한을 가진 환경 목록을 가져옵니다. 환경 중 하나를 관심 환경으로 선택하고 이 환경에 대해 추가 데이터를 쿼리합니다.
1. HTTPS 요청의 예로, 관심 환경에 대해 가용성 데이터를 요청합니다.
1. [Azure AutoRest](https://github.com/Azure/AutoRest)의 SDK 자동 생성 지원에 대 한 예제를 제공 합니다.

> [!NOTE]
> 코드를 컴파일하고 실행 하는 단계 및 예제 코드는 [https://github.com/Azure-Samples/Azure-Time-Series-Insights](https://github.com/Azure-Samples/Azure-Time-Series-Insights/tree/master/csharp-tsi-preview-sample)에서 사용할 수 있습니다.

## <a name="c-example"></a>C# 예제

[!code-csharp[csharpquery-example](~/samples-tsi/csharp-tsi-preview-sample/DataPlaneClientSampleApp/Program.cs)]

> [!NOTE]
> 위의 코드 샘플은 기본 환경 값을 변경 하지 않고 실행할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- 쿼리에 대 한 자세한 내용은 [쿼리 API 참조를 참조](https://docs.microsoft.com/rest/api/time-series-insights/preview-query)하세요.

- [클라이언트 SDK를 사용 하 여 JavaScript 앱을 연결 하 여](https://github.com/microsoft/tsiclient) Time Series Insights 하는 방법을 참조 하세요.
