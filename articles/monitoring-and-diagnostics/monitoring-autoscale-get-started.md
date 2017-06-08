---
title: "Azure에서 자동 크기 조정 시작 | Microsoft Docs"
description: "Azure에서 리소스 크기를 조정하는 방법에 대해 알아봅니다."
author: rajram
manager: rboucher
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: d37d3fda-8ef1-477c-a360-a855b418de84
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/07/2017
ms.author: rajram
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: a0a420adc57a0bcbb3779183324ab7bfafcb5bf1
ms.contentlocale: ko-kr
ms.lasthandoff: 05/10/2017


---
# <a name="get-started-with-auto-scale-in-azure"></a>Azure에서 자동 크기 조정 시작
이 문서에서는 Azure Portal에서 리소스에 대한 자동 크기 조정을 설정하는 방법에 대해 설명합니다.

Azure Monitor 자동 크기 조정은 VMSS(Virtual Machine Scale Sets), 클라우드 서비스 및 앱 서비스 계획 및 앱 서비스 환경에만 적용됩니다. 

# <a name="lets-get-started"></a>시작

## <a name="discover-the-auto-scale-settings-in-your-subscriptions"></a>구독 정보에서 자동 크기 조정 설정 검색
Azure Monitor에서 자동 크기 조정을 적용할 수 있는 리소스를 모두 검색할 수 있습니다. 단계별 연습을 위해 아래에 나열된 단계를 수행합니다.

- [Azure Portal][1]을 엽니다.
- 왼쪽 탐색 창에서 Azure Monitor 아이콘을 클릭합니다.
  ![Azure Monitor 시작][2]
- 자동 크기 조정 설정을 클릭하여 현재 자동 크기 조정 상태와 함께 자동 크기 조정을 적용할 수 있는 모든 리소스를 확인합니다. ![Azure Monitor에서 자동 크기 조정 검색][3]

위쪽의 필터 창을 사용하여 특정 리소스 그룹의 리소스를 선택할 수 있도록 목록의 범위를 좁히고, 특정 리소스 종류를 선택하거나 특정 리소스를 선택할 수 있습니다.

각 리소스에 대해 현재 인스턴스 수와 자동 크기 조정 상태를 확인합니다. 자동 크기 조정 상태는 다음과 같을 수 있습니다.

- 구성되지 않음: 이 리소스에 대해 자동 크기 조정 설정을 사용하도록 아직 설정하지 않았습니다.
- 사용: 이 리소스에 대해 자동 크기 조정 설정을 사용하도록 설정했습니다.
- 사용 안 함: 이 리소스에 대해 자동 크기 조정 설정을 사용 하지 않도록 설정했습니다.

## <a name="create-your-first-auto-scale-setting"></a>첫 번째 자동 크기 조정 설정 만들기

이제 간단한 단계별 연습을 통해 첫 번째 자동 크기 조정 설정을 만들어 보겠습니다.

- Azure Monitor에서 '자동 크기 조정' 블레이드를 열고 크기를 조정할 리소스를 선택합니다. (아래 단계에서는 웹앱과 연결된 앱 서비스 계획을 사용합니다. [Azure에서 5분 내에 첫 번째 ASP.NET 웹앱을 만들 수 있습니다.][4])
- 리소스에 대한 크기 조정 설정 블레이드에서 현재 인스턴스 수가 하나임을 알려줍니다. '자동 크기 조정 사용'을 클릭합니다.
  ![새 웹앱에 대한 크기 조정 설정][5]
- 크기 조정 설정의 이름을 제공하고 "규칙 추가"를 클릭합니다. 오른쪽에 열리는 컨텍스트 창에서 크기 조정 규칙 옵션이 표시됩니다. 기본적으로 리소스의 CPU 사용률이 70%를 초과하면 인스턴스 수를 하나씩 늘리는 옵션이 설정되어 있습니다. 기본값은 그대로 두고 [추가]를 클릭합니다.
  ![웹앱에 대한 크기 조정 설정 만들기][6]
- 이제 첫 번째 크기 조정 규칙을 만들었습니다. UX에서 모범 사례를 권장하고 '규칙에 하나 이상의 크기 조정을 사용하는 좋습니다.'라고 알려줍니다. 이렇게 하려면 '규칙 추가'를 클릭하고 '연산자'를 '미만'으로, '임계값'을 '20'으로, '작업'을 '다음을 기준으로 개수 줄이기'로 설정합니다. 이제 CPU 사용량을 기준으로 확장/축소하는 크기 조정 설정이 있어야 합니다.
  ![CPU 기준 크기 조정][8]
- '저장'을 클릭합니다.

축하합니다. 이제 CPU 사용량을 기준으로 웹앱의 크기를 자동으로 조정하는 첫 번째 크기 조정 설정을 성공적으로 만들었습니다.

> 참고: VMSS 또는 클라우드 서비스 역할을 시작하는 데에도 동일한 단계를 적용할 수 있습니다.

# <a name="other-considerations"></a>기타 고려 사항
## <a name="scale-based-on-a-schedule"></a>일정을 기준으로 크기 조정
CPU 기준 크기 조정 외에도 특정 요일에 대한 크기 조정을 다르게 설정할 수 있습니다.

- '크기 조정 조건 추가'를 클릭합니다.
- 크기 조정 모드 및 규칙 설정은 기본 조건과 동일합니다.
- 일정에 대한 '특정 요일 반복'을 선택합니다.
- 요일 및 선택한 요일에 대해 크기 조정 조건을 적용해야 하는 시작/종료 시간을 선택합니다.

![일정 기준 크기 조정 조건][9]
## <a name="scale-differently-on-specific-dates"></a>특정 날짜에 대해 다르게 크기 조정
CPU 기준 크기 조정 외에도 특정 날짜에 대한 크기 조정을 다르게 설정할 수 있습니다.

- '크기 조정 조건 추가'를 클릭합니다.
- 크기 조정 모드 및 규칙 설정은 기본 조건과 동일합니다.
- 일정에 대한 '시작/종료 날짜 지정'을 선택합니다.
- 시작/종료 날짜뿐만 아니라 선택한 날짜에 대해 크기 조정 조건을 적용해야 하는 시작/종료 시간도 선택합니다.

![날짜 크기 조정 조건][10]

## <a name="view-the-scale-history-of-your-resource"></a>리소스의 크기 조정 기록 보기
리소스의 크기가 확장/축소될 때마다 해당 이벤트가 활동 로그에 기록됩니다. '실행 기록' 탭으로 전환하여 지난 24시간 동안의 리소스의 크기 조정 기록을 볼 수 있습니다.

![실행 기록][11]

전체 크기 조정 기록(최대 90일 동안)을 보려면 '자세한 내용을 보려면 여기를 클릭하세요'를 클릭하면 됩니다. 그러면 리소스와 범주가 '자동 크기 조정'으로 미리 선택된 활동 로그가 시작됩니다.

## <a name="view-the-scale-definition-of-the-resource"></a>리소스 크기 조정 정의 보기
자동 크기 조정 설정은 ARM 리소스입니다. 'JSON' 탭으로 전환하여 JSON의 크기 조정 정의를 볼 수 있습니다.

![크기 조정 정의][12]

필요한 경우 JSON에서 직접 변경할 수 있습니다. 이러한 변경 내용은 저장함으로써 반영됩니다.

## <a name="disable-autoscale-and-manually-scale-your-instances"></a>자동 크기 조정 사용 안 함 설정 및 인스턴스 수동 크기 조정
현재 크기 조정 설정을 사용하지 않도록 설정하고 수동으로 리소스의 크기를 조정하려는 경우가 있을 수 있습니다.

위쪽의 '자동 크기 조정 사용 안 함' 단추를 클릭합니다.
![자동 크기 조정 사용 안 함][13]

이 옵션을 사용하면 구성을 사용하지 않도록 설정하며, 자동 크기 조정을 다시 사용하도록 설정하면 되돌릴 수 있습니다. 이제 크기 조정하려는 인스턴스의 수를 수동으로 설정할 수 있습니다.

![수동 크기 조정 설정][14]

'자동 크기 조정 사용'을 클릭하고 '저장'을 클릭하면 언제든지 자동 크기 조정으로 돌아갈 수 있습니다.

<!--Reference-->
[1]:https://portal.azure.com
[2]: ./media/monitoring-autoscale-get-started/azure-monitor-launch.png
[3]: ./media/monitoring-autoscale-get-started/discover-autoscale-azure-monitor.png
[4]: https://docs.microsoft.com/en-us/azure/app-service-web/app-service-web-get-started-dotnet
[5]: ./media/monitoring-autoscale-get-started/scale-setting-new-web-app.png
[6]: ./media/monitoring-autoscale-get-started/create-scale-setting-web-app.png
[7]: ./media/monitoring-autoscale-get-started/scale-in-recommendation.png
[8]: ./media/monitoring-autoscale-get-started/scale-based-on-cpu.png
[9]: ./media/monitoring-autoscale-get-started/scale-condition-schedule.png
[10]: ./media/monitoring-autoscale-get-started/scale-condition-dates.png
[11]: ./media/monitoring-autoscale-get-started/scale-history.png
[12]: ./media/monitoring-autoscale-get-started/scale-definition-json.png
[13]: ./media/monitoring-autoscale-get-started/disable-autoscale.png
[14]: ./media/monitoring-autoscale-get-started/set-manualscale.png
