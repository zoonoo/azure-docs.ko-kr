---
title: .NET 앱에 대 한 Azure 애플리케이션 Insights 스냅숏 디버거 업그레이드 | Microsoft Docs
description: Azure 앱 Services 또는 Nuget 패키지를 통해 스냅숏 디버거를 최신 버전으로 업그레이드 하는 방법
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: MarioHewardt
ms.author: marioh
ms.date: 03/28/2019
ms.reviewer: mbullwin
ms.openlocfilehash: e2b21b7cbb6b04da0c93e73c0cacb8a05c338bde
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/24/2019
ms.locfileid: "72899843"
---
# <a name="upgrading-the-snapshot-debugger"></a>스냅숏 디버거 업그레이드

Microsoft는 사용자의 데이터에 가장 적합 한 보안을 제공 하기 위해 TLS 1.0 및 TLS 1.1에서 멀리 이동 하 고 있으며,이는 결정 된 공격자에 게 취약 하다 고 표시 되었습니다. 이전 버전의 사이트 확장을 사용 하는 경우 계속 작업 하려면 업그레이드가 필요 합니다. 이 문서에서는 스냅숏 디버거를 최신 버전으로 업그레이드 하는 데 필요한 단계에 대해 간략하게 설명 합니다. 사이트 확장을 사용 하 여 스냅숏 디버거를 사용 하도록 설정 했는지 여부 또는 응용 프로그램에 추가 된 SDK/Nuget을 사용한 경우에 따라 두 가지 기본 업그레이드 경로가 있습니다. 두 업그레이드 경로에 대 한 설명은 다음과 같습니다. 

## <a name="upgrading-the-site-extension"></a>사이트 확장 업그레이드

사이트 확장을 사용 하 여 스냅숏 디버거를 사용 하도록 설정한 경우 다음 절차를 사용 하 여 쉽게 업그레이드할 수 있습니다.

1. Azure 포털에 로그인합니다.
2. Application Insights 및 스냅숏 디버거를 사용 하는 리소스로 이동 합니다. 예를 들어 웹 앱의 경우 App Service 리소스로 이동 합니다.

   ![DiagService01 이라는 개별 App Service 리소스의 스크린샷](./media/snapshot-debugger-upgrade/app-service-resource.png)

3. 리소스를 탐색 한 후 개요 블레이드에서 Application Insights를 클릭 합니다.

   ![세 단추의 스크린샷 이름이 Application Insights 가운데 단추를 선택 합니다.](./media/snapshot-debugger-upgrade/application-insights-button.png)

4. 현재 설정을 사용 하 여 새 블레이드가 열립니다. 설정을 변경할 수 없는 경우에는 그대로 둘 수 있습니다. 블레이드의 아래쪽에 있는 **적용** 단추는 기본적으로 사용 하도록 설정 되어 있지 않으며 단추를 활성화 하려면 설정 중 하나를 전환 해야 합니다. 실제 설정을 변경할 필요 없이 설정을 변경한 후 즉시 다시 변경할 수 있습니다. 프로파일러 설정을 전환 하 고 **적용**을 선택 하는 것이 좋습니다.

   ![빨간색으로 강조 표시 된 적용 단추가 있는 Application Insights App Service 구성 페이지의 스크린샷](./media/snapshot-debugger-upgrade/view-application-insights-data.png)

5. **적용**을 클릭 하면 변경 내용을 확인 하 라는 메시지가 표시 됩니다.

    > [!NOTE]
    > 업그레이드 프로세스의 일부로 사이트가 다시 시작 됩니다.

   ![App Service 모니터링 프롬프트 적용의 스크린샷 텍스트 상자에 "응용 프로그램 설정에 변경 내용을 적용 하 고 Application Insights 리소스를 웹 앱에 연결 하는 도구를 설치 합니다. 메시지가 표시 됩니다. 그러면 사이트가 다시 시작 됩니다. "활성 컨트롤러를 다시 시작하면 장치는 수동 컨트롤러에 장애 조치합니다.](./media/snapshot-debugger-upgrade/apply-monitoring-settings.png)

6. **예** 를 클릭 하 여 변경 내용을 적용 합니다. 프로세스 중에 변경 내용이 적용 되는 것을 보여 주는 알림이 표시 됩니다.

   ![오른쪽 위 모서리에 표시 되는 변경 내용 적용-확장 업데이트 메시지의 스크린샷](./media/snapshot-debugger-upgrade/updating-extensions.png)

완료 되 면 **"변경 내용 적용"** 알림이 표시 됩니다.

   ![변경 내용이 적용 되는 메시지의 스크린샷](./media/snapshot-debugger-upgrade/changes-are-applied.png)

이제 사이트가 업그레이드 되었고 사용할 준비가 되었습니다.

## <a name="upgrading-snapshot-debugger-using-sdknuget"></a>SDK/Nuget을 사용 하 여 스냅숏 디버거 업그레이드

응용 프로그램에서 1.3.1 버전의 `Microsoft.ApplicationInsights.SnapshotCollector` 버전을 사용 하는 경우 작업을 계속 하려면 [최신 버전](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) 으로 업그레이드 해야 합니다.
