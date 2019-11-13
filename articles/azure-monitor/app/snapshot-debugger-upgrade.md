---
title: .NET 앱에 대 한 Azure 애플리케이션 Insights 스냅숏 디버거 업그레이드 | Microsoft Docs
description: Azure 앱 Services 또는 Nuget 패키지를 통해 스냅숏 디버거를 최신 버전으로 업그레이드 하는 방법
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: pharring
ms.author: pharring
ms.date: 03/28/2019
ms.reviewer: mbullwin
ms.openlocfilehash: 51642dde3de16f2bed3ca247e573237effb30917
ms.sourcegitcommit: 44c2a964fb8521f9961928f6f7457ae3ed362694
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/12/2019
ms.locfileid: "73935979"
---
# <a name="upgrading-the-snapshot-debugger"></a>스냅숏 디버거 업그레이드

Microsoft는 사용자의 데이터에 가장 적합 한 보안을 제공 하기 위해 TLS 1.0 및 TLS 1.1에서 멀리 이동 하 고 있으며,이는 결정 된 공격자에 게 취약 하다 고 표시 되었습니다. 이전 버전의 사이트 확장을 사용 하는 경우 계속 작업 하려면 업그레이드가 필요 합니다. 이 문서에서는 스냅숏 디버거를 최신 버전으로 업그레이드 하는 데 필요한 단계에 대해 간략하게 설명 합니다. 사이트 확장을 사용 하 여 스냅숏 디버거를 사용 하도록 설정 했는지 여부 또는 응용 프로그램에 추가 된 SDK/Nuget을 사용한 경우에 따라 두 가지 기본 업그레이드 경로가 있습니다. 두 업그레이드 경로에 대 한 설명은 다음과 같습니다. 

## <a name="upgrading-the-site-extension"></a>사이트 확장 업그레이드

> [!IMPORTANT]
> 이전 버전의 Application Insights에서는 _Azure App Service에 대 한 Application Insights 확장_이라는 개인 사이트 확장을 사용 했습니다. 현재 Application Insights 환경은 미리 설치 된 사이트 확장을 밝게 하기 위해 앱 설정을 설정 하 여 사용할 수 있습니다.
> 충돌을 방지 하기 위해 사이트의 작동이 중지 될 수 있으므로 먼저 개인 사이트 확장을 삭제 하는 것이 중요 합니다. 아래의 4 단계를 참조 하세요.

사이트 확장을 사용 하 여 스냅숏 디버거를 사용 하도록 설정한 경우 다음 절차를 사용 하 여 업그레이드할 수 있습니다.

1. Azure 포털에 로그인합니다.
2. Application Insights 및 스냅숏 디버거를 사용 하는 리소스로 이동 합니다. 예를 들어 웹 앱의 경우 App Service 리소스로 이동 합니다.

   ![DiagService01 이라는 개별 App Service 리소스의 스크린샷](./media/snapshot-debugger-upgrade/app-service-resource.png)

3. 리소스를 탐색 했으면 확장 블레이드를 클릭 하 고 확장 목록이 채워질 때까지 기다립니다.

   ![Azure App Service 설치 Application Insights 확장을 보여 주는 App Service 확장의 스크린샷](./media/snapshot-debugger-upgrade/application-insights-site-extension-to-be-deleted.png)

4. _Azure App Service에 대 한 Application Insights 확장_ 버전이 설치 되어 있는 경우이를 선택 하 고 삭제를 클릭 합니다. 확장을 삭제 하 고 다음 단계로 이동 하기 전에 삭제 작업이 완료 될 때까지 기다리려면 **예** 를 확인 합니다.

   ![삭제 단추가 강조 표시 된 Azure App Service에 대 한 Application Insights 확장을 보여 주는 App Service 확장의 스크린샷](./media/snapshot-debugger-upgrade/application-insights-site-extension-delete.png)

5. 리소스의 개요 블레이드로 이동 하 고 Application Insights를 클릭 합니다.

   ![세 단추의 스크린샷 이름이 Application Insights 가운데 단추를 선택 합니다.](./media/snapshot-debugger-upgrade/application-insights-button.png)

6. 이 App Service에 대 한 Application Insights 블레이드를 처음 보는 경우 Application Insights를 설정 하 라는 메시지가 표시 됩니다. **Application Insights 켜기를**선택 합니다.
 
   ![Application Insights 켜기 단추가 강조 표시 된 Application Insights 블레이드의 첫 번째 환경의 스크린샷](./media/snapshot-debugger-upgrade/turn-on-application-insights.png)

7. 현재 Application Insights 설정이 표시 됩니다. 설정을 변경할 수 없는 경우에는 그대로 둘 수 있습니다. 블레이드의 아래쪽에 있는 **적용** 단추는 기본적으로 사용 하도록 설정 되어 있지 않으며 단추를 활성화 하려면 설정 중 하나를 전환 해야 합니다. 실제 설정을 변경할 필요 없이 설정을 변경한 후 즉시 다시 변경할 수 있습니다. 프로파일러 설정을 전환 하 고 **적용**을 선택 하는 것이 좋습니다.

   ![빨간색으로 강조 표시 된 적용 단추가 있는 Application Insights App Service 구성 페이지의 스크린샷](./media/snapshot-debugger-upgrade/view-application-insights-data.png)

8. **적용**을 클릭 하면 변경 내용을 확인 하 라는 메시지가 표시 됩니다.

    > [!NOTE]
    > 업그레이드 프로세스의 일부로 사이트가 다시 시작 됩니다.

   ![App Service 모니터링 프롬프트 적용의 스크린샷 텍스트 상자에 "응용 프로그램 설정에 변경 내용을 적용 하 고 Application Insights 리소스를 웹 앱에 연결 하는 도구를 설치 합니다. 메시지가 표시 됩니다. 그러면 사이트가 다시 시작 됩니다. "활성 컨트롤러를 다시 시작하면 장치는 수동 컨트롤러에 장애 조치합니다.](./media/snapshot-debugger-upgrade/apply-monitoring-settings.png)

9. **예** 를 클릭 하 여 변경 내용을 적용 하 고 프로세스가 완료 될 때까지 기다립니다.

이제 사이트가 업그레이드 되었고 사용할 준비가 되었습니다.

## <a name="upgrading-snapshot-debugger-using-sdknuget"></a>SDK/Nuget을 사용 하 여 스냅숏 디버거 업그레이드

응용 프로그램에서 1.3.1 버전의 `Microsoft.ApplicationInsights.SnapshotCollector` 버전을 사용 하는 경우 작업을 계속 하려면 [최신 버전](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) 으로 업그레이드 해야 합니다.
