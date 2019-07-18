---
title: 새 Azure Application Insights 리소스 만들기 | Microsoft Docs
description: 새 라이브 애플리케이션에 대한 Application Insights 모니터링을 수동으로 설정합니다.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 878b007e-161c-4e36-8ab2-3d7047d8a92d
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 06/10/2019
ms.author: mbullwin
ms.openlocfilehash: 9da52e5a9dfa3b55431d66ed3162172226f71a40
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67073283"
---
# <a name="create-an-application-insights-resource"></a>Application Insights 리소스 만들기

Azure Application Insights는 Microsoft Azure *리소스*에 애플리케이션에 대한 데이터를 표시합니다. 따라서 새 리소스 만들기는 [새 애플리케이션을 모니터링하도록 Application Insights를 설정][start]하는 과정에 포함됩니다. 새 리소스를 만든 후에 계측 키를 가져옵니다 하 고 Application Insights SDK 구성에 사용할 수 있습니다. 계측 키를 리소스에 원격 분석을 연결합니다.

## <a name="sign-in-to-microsoft-azure"></a>Microsoft Azure에 로그인

Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.

## <a name="create-an-application-insights-resource"></a>Application Insights 리소스 만들기

에 로그인 합니다 [Azure portal](https://portal.azure.com), Application Insights 리소스를 만듭니다.

![왼쪽된 위 모퉁이에 '+' 기호를 클릭 합니다. 뒤에 Application Insights는 개발자 도구를 선택 합니다.](./media/create-new-resource/new-app-insights.png)

   | 설정        |  값           | 설명  |
   | ------------- |:-------------|:-----|
   | **Name**      | 전역적으로 고유한 값 | 모니터링 하는 앱을 식별 하는 이름입니다. |
   | **리소스 그룹**     | myResourceGroup      | App Insights 데이터를 호스트 하는 새 또는 기존 리소스 그룹 이름입니다. |
   | **Location**: | 미국 동부 | 가까운 위치를 선택 하거나 앱 호스팅되는 거의 합니다. |

필수 필드에 적절 한 값을 입력 하 고 선택한 **검토 + 만들기**합니다.

![필수 필드에 값을 입력 하 고 "검토 + 만들기"를 선택 합니다.](./media/create-new-resource/review-create.png)

앱을 만든 경우 새 창이 열립니다. 이 창은 응용 프로그램 모니터링된에 대 한 성능 및 사용 현황 데이터 참조 되는 위치입니다. 

## <a name="copy-the-instrumentation-key"></a>계측 키 복사

계측 키를 사용 하 여 원격 분석 데이터를 연결 하려는 리소스를 식별 합니다. 응용 프로그램의 코드를 계측 키를 추가 하는 복사를 해야 합니다.

![클릭 하 고 계측 키 복사](./media/create-new-resource/instrumentation-key.png)

## <a name="install-the-sdk-in-your-app"></a>응용 프로그램에 SDK를 설치합니다.

Application Insights SDK를 애플리케이션에 설치합니다. 이 단계는 애플리케이션의 형식에 따라 크게 달라 집니다.

계측 키를 사용하여 [애플리케이션에 설치한 SDK][start]를 구성합니다.

SDK에는 추가 코드를 작성 하지 않고도 원격 분석을 전송 하는 표준 모듈이 포함 됩니다. 사용자 작업을 추적하거나 문제를 보다 세부적으로 진단하려면 [API를 사용][api]하여 사용자 고유의 원격 분석을 보내도록 합니다.

## <a name="creating-a-resource-automatically"></a>자동으로 리소스 만들기
리소스를 자동으로 만드는 [PowerShell 스크립트](../../azure-monitor/app/powershell.md) 를 작성할 수 있습니다.

## <a name="next-steps"></a>다음 단계
* [진단 검색](../../azure-monitor/app/diagnostic-search.md)
* [메트릭 탐색](../../azure-monitor/app/metrics-explorer.md)
* [분석 쿼리 작성](../../azure-monitor/app/analytics.md)

<!--Link references-->

[api]: ../../azure-monitor/app/api-custom-events-metrics.md
[diagnostic]: ../../azure-monitor/app/diagnostic-search.md
[metrics]: ../../azure-monitor/app/metrics-explorer.md
[start]: ../../azure-monitor/app/app-insights-overview.md