---
author: diberry
ms.author: diberry
ms.service: cognitive-services
ms.topic: include
ms.date: 01/02/2019
ms.openlocfilehash: 2f5b03dd0170da9a9869183d7a412688509525ef
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67182471"
---
`ApplicationInsights` 설정으로 [Azure Application Insights](https://docs.microsoft.com/azure/application-insights) 원격 분석 지원을 컨테이너에 추가할 수 있습니다. Application Insights는 컨테이너의 심층 모니터링을 제공합니다. 컨테이너의 가용성, 성능 및 사용량을 쉽게 모니터링할 수 있습니다. 또한 컨테이너의 오류를 빠르게 식별하고 진단할 수 있습니다.

다음 표에서는 `ApplicationInsights` 섹션에서 지원되는 구성 설정을 설명합니다.

|필수| 이름 | 데이터 형식 | 설명 |
|--|------|-----------|-------------|
|아니요| `InstrumentationKey` | 문자열 | 컨테이너에 대한 원격 분석 데이터가 전송되는 Application Insights 인스턴스의 계측 키입니다. 자세한 내용은 [ASP.NET Core용 Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-asp-net-core)를 참조하세요. <br><br>예:<br>`InstrumentationKey=123456789`|

