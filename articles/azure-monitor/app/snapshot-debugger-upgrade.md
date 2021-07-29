---
title: Azure Application Insights 스냅샷 디버거 업그레이드
description: 최신 버전의 Azure App Services 또는 Nuget 패키지를 통해 .NET 앱용 스냅샷 디버거를 업그레이드하는 방법
ms.topic: conceptual
author: pharring
ms.author: pharring
ms.date: 03/28/2019
ms.reviewer: mbullwin
ms.openlocfilehash: 6991db6055ca8556adff67f194bac0f4aa04cb06
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "77671395"
---
# <a name="upgrading-the-snapshot-debugger"></a>스냅샷 디버거 업그레이드

Microsoft는 데이터에 가능한 가장 적합한 보안을 제공하기 위해 확인된 공격자에 대한 취약성을 보인 TLS 1.0과 TLS 1.1에서 벗어나고 있습니다. 이전 버전의 사이트 확장을 사용하는 경우 계속 작업하려면 업그레이드가 필요합니다. 이 문서에서는 스냅샷 디버거를 최신 버전으로 업그레이드하는 데 필요한 단계에 대해 간략하게 설명합니다. 사이트 확장을 사용하여 스냅샷 디버거를 사용하도록 설정했는지 또는 애플리케이션에 추가된 SDK/Nuget을 사용했는지에 따라 두 가지 주 업그레이드 경로가 있습니다. 두 업그레이드 경로의 설명은 다음과 같습니다. 

## <a name="upgrading-the-site-extension"></a>사이트 확장 업그레이드

> [!IMPORTANT]
> 이전 버전의 Application Insights에서는 ‘Azure App Service용 Application Insights 확장’이라는 프라이빗 사이트 확장을 사용했습니다. 현재 Application Insights 환경은 미리 설치된 사이트 확장을 강조 표시하도록 앱 설정을 지정하여 사용하도록 설정됩니다.
> 사이트의 작동이 중지될 수 있는 충돌을 방지하기 위해 먼저 프라이빗 사이트 확장을 삭제하는 것이 중요합니다. 아래 4단계를 참조하세요.

사이트 확장을 사용하여 스냅샷 디버거를 사용하도록 설정한 경우 다음 절차를 사용하여 업그레이드할 수 있습니다.

1. Azure Portal에 로그인합니다.
2. Application Insights 및 스냅샷 디버거를 사용하는 리소스로 이동합니다. 예를 들어, 웹앱의 경우 App Service 리소스로 이동합니다.

   ![DiagService01이라는 개별 App Service 리소스의 스크린샷](./media/snapshot-debugger-upgrade/app-service-resource.png)

3. 리소스로 이동한 후 확장 블레이드를 클릭하고 확장 목록이 채워질 때까지 기다립니다.

   ![설치된 Azure App Service용 Application Insights 확장을 보여 주는 App Service 확장의 스크린샷](./media/snapshot-debugger-upgrade/application-insights-site-extension-to-be-deleted.png)

4. ‘Azure App Service용 Application Insights 확장’ 버전이 설치되어 있는 경우 이를 선택하고 삭제를 클릭합니다. **예** 를 확인하여 확장을 삭제하고 다음 단계로 이동하기 전에 삭제가 완료될 때까지 기다립니다.

   ![삭제 단추가 강조 표시된 Azure App Service용 Application Insights 확장을 보여 주는 App Service 확장의 스크린샷](./media/snapshot-debugger-upgrade/application-insights-site-extension-delete.png)

5. 리소스의 개요 블레이드로 이동하고 Application Insights를 클릭합니다.

   ![세 가지 단추의 스크린샷 이름이 Application Insights인 가운데 단추가 선택됨](./media/snapshot-debugger-upgrade/application-insights-button.png)

6. 이 App Service의 Application Insights 블레이드를 처음 본 경우 Application Insights를 설정하라는 메시지가 표시됩니다. **Application Insights 켜기** 를 선택합니다.
 
   ![Application Insights 켜기 단추가 강조 표시된 Application Insights 블레이드를 처음 본 경우 스크린샷](./media/snapshot-debugger-upgrade/turn-on-application-insights.png)

7. 현재 Application Insights 설정이 표시됩니다. 설정을 변경하지 않으려는 경우에는 있는 그대로 유지할 수 있습니다. 블레이드 아래쪽에 있는 **적용** 단추는 기본적으로 사용하도록 설정되지 않으므로 단추를 활성화하려면 설정 중 하나를 토글해야 합니다. 실제 설정을 변경할 필요가 없으며, 오히려 설정을 변경한 후 즉시 다시 변경할 수 있습니다. 프로파일러 설정을 토글한 다음, **적용** 을 선택하는 것이 좋습니다.

   ![적용 단추가 빨간색으로 강조 표시된 Application Insights App Service 구성 페이지의 스크린샷](./media/snapshot-debugger-upgrade/view-application-insights-data.png)

8. **적용** 을 클릭하면 변경 내용을 확인하라는 메시지가 표시됩니다.

    > [!NOTE]
    > 사이트는 업그레이드 프로세스의 일부로 다시 시작됩니다.

   ![App Service 모니터링 프롬프트 적용의 스크린샷 텍스트 상자에 “이제 애플리케이션 설정에 변경 내용을 적용하고 Application Insights 리소스를 웹앱에 연결하는 도구를 설치합니다.”라는 메시지가 표시됩니다. 디바이스가 다시 시작됩니다. 계속하시겠습니까?”](./media/snapshot-debugger-upgrade/apply-monitoring-settings.png)

9. **예** 를 클릭하여 변경 내용을 적용하고 프로세스가 완료될 때까지 기다립니다.

이제 사이트가 업그레이드되었고 사용할 준비가 되었습니다.

## <a name="upgrading-snapshot-debugger-using-sdknuget"></a>SDK/Nuget을 사용하여 스냅샷 디버거 업그레이드

애플리케이션에서 버전 1.3.1 이전의 `Microsoft.ApplicationInsights.SnapshotCollector` 버전을 사용하는 경우 작업을 계속하려면 [최신 버전](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector)으로 업그레이드해야 합니다.
