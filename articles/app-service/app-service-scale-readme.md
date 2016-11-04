---
title: 'Azure 앱 서비스: 앱 서비스 응용 프로그램 크기 조정'
description: 앱 서비스에서 응용 프로그램 크기 조정의 다양한 측면을 알아봅니다.
keywords: 앱 서비스, Azure 앱 서비스, 규모, 확장성, 앱 서비스 계획, 앱 서비스 비용
services: app-service
documentationcenter: ''
author: btardif
manager: wpickett
editor: ''

ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/07/2016
ms.author: byvinyal

---
# <a name="azure-app-service:-scaling-app-service-applications"></a>Azure 앱 서비스: 앱 서비스 응용 프로그램 크기 조정
Azure 앱 서비스에서 호스팅되는 응용 프로그램은 [대규모로 확장](https://azure.microsoft.com/blog/canadian-broadcasting-corporation-radio-canada-leverage-azure-for-smooth-election-coverage/)할 수 있습니다.
하지만 응용 프로그램의 크기를 조정하는 작업은 "보편적으로 적용되는" 솔루션이 없는 복잡한 문제입니다. 응용 프로그램의 크기를 올바르게 조정하기 위해 응용 프로그램 성공에 기여하는 3가지 주요 영역이 있습니다.

1. 응용 프로그램 아키텍처와 약점 이해
   * 응용 프로그램이 상태 저장인가요? 상태 비저장인가요?
   * 응용 프로그램의 모든 구성 요소는 무엇인가요?
     * 응용 프로그램의 병목 지점은 어디인가요?
   * 응용 프로그램에 부하가 걸리면 무엇부터 중단되나요?
2. 예상된 부하 및 성능 요구 사항 이해
   * 응용 프로그램이 천 명의 사용자를 위한 것인가요? 아니면 백만 명의 사용자를 위한 것인가요?
   * 트래픽이 단일 지리적 위치에서 생기나요? 아니면 전역적으로 생기나요?
   * 계절적 변동이 있나요? 트래픽 정점이 있나요?
   * 앱 응답 속도가 얼마나 빠른가요? 1초? 1밀리초인가요?
3. 응용 프로그램 호스팅 플랫폼에 대한 이해 및 올바른 활용
   * 내 목표 규모를 달성하기 위해 무슨 기능을 활용해야 하나요?

이 섹션에서는 모든 요소를 이해하고 장치에서 확장성 목표를 달성하는 데 필요한 App Service 기능을 활용하는 전략을 세울 수 있습니다.

[!INCLUDE [app-service-blueprint-scaling-app-service-applications](../../includes/app-service-blueprint-scaling-app-service-applications.md)]

<!--HONumber=Oct16_HO2-->


