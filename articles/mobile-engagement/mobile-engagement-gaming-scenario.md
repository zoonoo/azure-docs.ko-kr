---
title: 게임 앱에 대한 Azure Mobile Engagement 구현
description: Azure Mobile Engagement 구현을 위한 게임 앱 시나리오
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: ''
ms.assetid: 2cafc044-4902-4058-8037-49399bf6bf7f
ms.service: mobile-engagement
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: mobile-multiple
ms.workload: mobile
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: 96e827b57e804cc91798859bc906e49046decede
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2018
---
# <a name="implement-mobile-engagement-with-gaming-app"></a>게임 앱에서 Mobile Engagement 구현
## <a name="overview"></a>개요
> [!IMPORTANT]
> Azure Mobile Engagement는 2018/3/31에 사용이 중지됩니다. 이 페이지는 이후에 삭제됩니다.
> 

새 낚시 기반 롤플레이/전략 게임 앱을 출시했습니다. 이 게임이 시작되고 6개월 동안 실행되었습니다. 이 게임은 커다란 성공을 거두어 수백만 회의 다운로드를 기록했고 재방문 주기도 새로 시작한 다른 게임 앱에 비해 매우 높습니다. 이에 관련자들은 분기별 검토 회의에서 ARPU(사용자당 평균 수익)를 올리기로 합의했습니다. 프리미엄 게임 내 패키지가 특별 제공으로 나와 있습니다. 이러한 게임 팩을 통해 사용자는 게임의 낚싯줄 및 미끼와 낚시 도구의 모양 및 성능을 업그레이드할 수 있습니다. 그러나 패키지 판매는 매우 낮은 실정입니다. 그래서 분석 도구를 사용하여 사용자 환경을 먼저 분석한 다음 Engagement 프로그램을 개발하여 고급 구분을 통해 판매를 늘리기로 결정했습니다.

[Azure Mobile Engagement - 모범 사례가 포함된 시작 가이드](mobile-engagement-getting-started-best-practices.md) 에 따라 Engagement 전략을 구축했습니다.

## <a name="objectives-and-kpis"></a>목표 및 KPI
주요 게임의 관련자들의 관심사가 충족되어야 합니다. 프리미엄 패키지 매출의 15% 증가라는 한 가지 주요 목표에 모두 동의합니다. 이 목표를 측정하고 추진하기 위해 비즈니스 KPI(핵심 성과 지표)를 만듭니다.

* 어느 게임 레벨에서 이러한 패키지를 구입하나요?
* 사용자별, 세션별, 주간 및 월간 수익은 어떻게 되나요?
* 즐겨찾는 구매 유형은 무엇인가요?

[시작 가이드](mobile-engagement-getting-started-best-practices.md) 1부에서는 목표 및 KPI를 정의하는 방법에 대해 설명합니다. 

지금 정의한 비즈니스 KPI를 사용하여 모바일 제품 관리자가 새로운 사용자 추세 및 재방문 주기를 확인하기 위한 Engagement KPI를 만듭니다.

* 매일, 2일 간격, 매주, 매월, 3개월 간격을 사용해서 재방문 주기를 모니터링합니다.
* 활성 사용자 수
* 스토어의 앱 등급

IT 팀의 권고에 따라 다음 질문에 답하기 위해 다음과 같은 기술 KPI가 추가되었습니다.

* 사용자 경로(방문한 페이지, 사용자가 이곳에서 사용한 시간)
* 세션별 발생한 중단 또는 버그 수
* 사용자가 실행하는 OS 버전
* 사용자의 평균 화면 크기
* 사용자가 사용하는 인터넷 연결 종류

각 KPI에 대해 모바일 제품 관리자가 필요한 데이터와 이 데이터의 플레이북 위치를 지정합니다.

## <a name="engagement-program-and-integration"></a>Engagement 프로그램 및 통합
고급 Engagement 프로그램을 구축하기 전에 프로젝트를 담당하는 모바일 프로젝트 책임자는 사용자들이 언제 어떻게 제품을 사용하는지에 대해 잘 알고 있어야 합니다.

3개월 후 모바일 프로젝트 책임자가 앱 내 푸시 알림 판매를 개선하기 위한 충분한 데이터를 수집했습니다. 알아낸 사실은 다음과 같습니다.

* 일반적으로 레벨 14에서 첫 번째 구매가 발생합니다. 이런 사례의 90%가 3달러의 전설적인 새 무기를 구매했습니다.
* 이런 사례의 80%에서 사용자가 구매를 한 후 제품을 계속 사용할 뿐만 아니라 추가로 더 구매합니다.
* 레벨 20을 통과한 사용자는 1주일에 10달러를 넘게 쓰기 시작합니다.
* 사용자들은 레벨 16, 24, 32에서 프리미엄 패키지를 구입하는 경향이 있습니다.

이 분석을 이용하여 모바일 프로젝트 책임자는 앱 내 판매를 늘리기 위해 특정 푸시 알림 순서를 만들기로 결정했습니다. 세 가지 푸시 순서, 즉 시작 프로그램, 판매 프로그램 및 비활성 프로그램을 만들고 이대로 호출합니다. 자세한 내용은 [플레이 북](https://github.com/Azure/azure-mobile-engagement-samples/tree/master/Playbooks)![][1]을 참조하세요.

<!--Image references-->

[1]: ./media/mobile-engagement-game-scenario/notification-scenario.png

<!--Link references-->
