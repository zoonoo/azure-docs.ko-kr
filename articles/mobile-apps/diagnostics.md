---
title: 응용 프로그램을 모니터링 하 여 Visual Studio App Center의 실시간 충돌 및 오류 발생
description: 작동 중단 및 오류에 대 한 모바일 응용 프로그램을 모니터링 하는 서비스로 App Center에 대해 알아봅니다.
author: elamalani
ms.assetid: 12a8a079-9b3c-4faf-8588-ccff02097224
ms.service: vs-appcenter
ms.topic: article
ms.date: 10/22/2019
ms.author: emalani
ms.openlocfilehash: ad9f8414274b16c9d805864385dc554f824e57cf
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/23/2019
ms.locfileid: "72795511"
---
# <a name="monitor-crashes-and-errors-in-real-time-for-your-mobile-application"></a>모바일 응용 프로그램에 대 한 실시간으로 충돌 및 오류 모니터링
수천 명의 사용자가 응용 프로그램을 사용 하 고 있는 경우 버그가 있을 수 있으며 사용자는 예기치 않은 응용 프로그램 작동이 중단 될 수 있습니다. 사용자 관점에서 볼 때 모든 것이 작동 하 고 안정적인 응용 프로그램입니다. 버그가 있는 및 crash 응용 프로그램은 좋은 사용자 환경을 제공 하지 않습니다. 실망 사용자는 앱을 제거 하 고, 잘못 된 리뷰를 남겨 두거나, 경험을 공개적으로 criticize 하 고 경험 합니다.

따라서 모바일 응용 프로그램의 경우 실제 충돌 동작을 이해 하는 것이 필수적입니다. 사용자가 앱 스토어를 출시 하는 즉시 작업은 완료 되지 않습니다. 앱 안정성을 보장 하 고 사용자 만족도를 유지 하기 위해 지속적으로 성능을 모니터링 하 고 측정 하는 것이 중요 합니다. 사용자의 요구 사항에 따라 응용 프로그램을 모니터링 하 고 문제가 발생 한 문제를 파악 하 여 비즈니스 요구 사항에 중요 한 문제를 해결 하 고 문제를 해결할 수 있습니다.

## <a name="importance-of-crashes-monitoring"></a>충돌 모니터링의 중요도
개발자는 다음을 수행 해야 합니다.
- **응용 프로그램의 상태** 에 대 한 세부 정보 및 **최종 사용자를 위해 수행**하는 방법에 대해 알아봅니다.
- 사용자의 장치에서 충돌이 발생 한 원인과 원인을 **파악** 합니다.
- 응용 프로그램을 안정적으로 만들기 위해 **문제를 심사 하 고 충돌을 해결** 해야 합니다.
- 사용자가 응용 프로그램을 다시 사용할 수 있도록 뛰어난 사용자 환경을 제공 하기 위해 더 나은 응용 프로그램을 빌드하고 **더 빠르게 반복** 하세요.

아래 서비스를 활용 하 여 응용 프로그램의 작동 중단을 모니터링 하 고, 문제를 진단 하 고, 신속 하 고 우수한 모바일 응용 프로그램 환경을 제공 하기 위해 신속 하 게 해결 하세요.

## <a name="visual-studio-app-center"></a>Visual Studio App Center
[App Center 진단](/appcenter/diagnostics/) 서비스를 사용 하면 응용 프로그램의 상태를 모니터링 하 고, 발생 한 작업과 응용 프로그램이 충돌 하는 이유를 이해 하 고, 충돌 보고서의 우선 순위를 지정 하 고 관리할 수 있습니다.

**주요 기능**
- 가장 심각한 문제 및 필요한 관련 정보를 강조 표시 하는 실시간, 자동 **충돌 및 오류**현명 그룹화 되었습니다.
- 근본 원인을 식별 하는 데 도움이 되는 컨텍스트 및 명확성을 제공 하는 **스택 추적 및 장치** 데이터를 완료 합니다.
- 가장 중요 한 문제를 찾는 데 도움이 되는 **강력한 검색 기능** 입니다.
- 분석 통합을 통해 사용자의 행동을 이해 하 고 이벤트를 충돌 하는 것으로 볼 수 있습니다.
- 사용자 지정 속성, 사용자 ID 및 첨부 파일-문제를 진단 하는 데 도움이 되는 추가 컨텍스트를 추가할 수 있습니다.
- **경고, 알림 및 버그 추적기 통합** 을 통해 사용자에 게 영향을 주는 새로운 및 예기치 않은 충돌을 발생 시킬 수 있습니다.
- 몇 분 내에이 서비스 사용을 시작 하기 위한 **단일 LINER SDK 통합** .
- **플랫폼 지원** -IOS, Android, Macos, TvOS, Xamarin, 네이티브, Unity, CORDOVA, WPF, WinForms.

**참조**
- [App Center 등록](https://appcenter.ms/signup?utm_source=Mobile%20Development%20Docs&utm_medium=Azure&utm_campaign=New%20azure%20docs)
- [App Center 진단 시작](/appcenter/diagnostics/)