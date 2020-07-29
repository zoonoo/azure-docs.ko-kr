---
title: Azure Application Insights를 최대한 활용하기 | Microsoft Docs
description: Application Insights를 시작한 경우 다음 기능을 사용할 수 있습니다.
ms.topic: conceptual
ms.date: 02/03/2017
ms.openlocfilehash: c0be377636159dbb2bdb628af6a0bc9077942397
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87321346"
---
# <a name="more-telemetry-from-application-insights"></a>Application Insights의 추가 원격 분석
[ASP.NET 코드에 Application Insights를 추가](./asp-net.md)한 후 추가 원격 분석을 위해 수행할 수 있는 몇 가지 작업이 있습니다. 

| 작업 | 수신 항목|
|---|---|
|(IIS 서버) 각 서버 컴퓨터에 [상태 모니터를 설치](https://go.microsoft.com/fwlink/?LinkId=506648)합니다.<br/>(Azure 웹앱) 웹앱의 Azure 제어판에서 Application Insights 블레이드를 엽니다.| [**성능 카운터**](./performance-counters.md)<br/>[**예외**](asp-net-exceptions.md) - 자세한 스택 추적<br/>[**종속성**](./asp-net-dependencies.md)|
|[웹 페이지에 JavaScript 코드 조각 추가](./javascript.md)|[성능 페이지](./usage-overview.md), 브라우저 예외, AJAX 성능. 클라이언트쪽 사용자 지정 원격 분석.|
|[가용성 웹 테스트 만들기](./monitor-web-app-availability.md)|사이트를 사용할 수 없게 되면 알림 수신|
|MSBuild에서 [Ensure buildinfo.config](/visualstudio/debugger/diagnose-problems-after-deployment?view=vs-2015) 생성|[메트릭 차트의 빌드 주석](./annotations.md)
|[사용자 지정 이벤트 및 메트릭 작성](./api-custom-events-metrics.md)|비즈니스 이벤트 및 메트릭의 수를 계산하고, 자세한 사용 현황을 추적하는 등의 작업을 수행합니다.|
|[라이브 사이트 프로파일링](https://aka.ms/AIProfilerPreview)|라이브 웹앱의 자세한 함수 타이밍|

