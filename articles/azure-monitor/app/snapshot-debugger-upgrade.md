---
title: Azure 응용 프로그램 인사이트 스냅숏 디버거 업그레이드
description: .NET 앱의 스냅샷 디버거를 Azure 앱 서비스의 최신 버전으로 업그레이드하거나 Nuget 패키지를 통해 업그레이드하는 방법
ms.topic: conceptual
author: pharring
ms.author: pharring
ms.date: 03/28/2019
ms.reviewer: mbullwin
ms.openlocfilehash: 6991db6055ca8556adff67f194bac0f4aa04cb06
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77671395"
---
# <a name="upgrading-the-snapshot-debugger"></a>스냅샷 디버거 업그레이드

데이터에 대한 최상의 보안을 제공하기 위해 Microsoft는 결정된 공격자에 취약한 것으로 나타난 TLS 1.0 및 TLS 1.1에서 벗어나고 있습니다. 이전 버전의 사이트 확장을 사용하는 경우 작업을 계속하려면 업그레이드가 필요합니다. 이 문서에서는 스냅숏 디버거를 최신 버전으로 업그레이드하는 데 필요한 단계를 간략하게 설명합니다. 사이트 확장을 사용하여 스냅숏 디버거를 사용하도록 설정했는지 또는 응용 프로그램에 추가된 SDK/Nuget을 사용했는지 여부에 따라 두 가지 기본 업그레이드 경로가 있습니다. 두 업그레이드 경로는 아래에서 설명합니다. 

## <a name="upgrading-the-site-extension"></a>사이트 확장 업그레이드

> [!IMPORTANT]
> 이전 버전의 응용 프로그램 인사이트는 _Azure 앱 서비스에 대한 응용 프로그램 인사이트 확장이라는_개인 사이트 확장을 사용했습니다. 앱 설정을 설정하여 사전 설치된 사이트 확장을 표시하여 현재 응용 프로그램 인사이트 환경을 사용할 수 있습니다.
> 사이트가 작동을 중지할 수 있는 충돌을 방지하려면 먼저 비공개 사이트 확장을 삭제하는 것이 중요합니다. 아래 4단계를 참조하십시오.

사이트 확장을 사용하여 스냅숏 디버거를 사용하도록 설정한 경우 다음 절차를 사용하여 업그레이드할 수 있습니다.

1. Azure Portal에 로그인합니다.
2. 응용 프로그램 인사이트 및 스냅숏 디버거를 사용하도록 설정한 리소스로 이동합니다. 예를 들어 웹 앱의 경우 앱 서비스 리소스로 이동합니다.

   ![DiagService01이라는 이름의 개별 앱 서비스 리소스의 스크린샷](./media/snapshot-debugger-upgrade/app-service-resource.png)

3. 리소스로 이동한 후 확장 블레이드를 클릭하고 확장 목록이 채워집니다.

   ![설치된 Azure 앱 서비스에 대한 응용 프로그램 인사이트 확장을 보여주는 앱 서비스 확장 의 스크린샷](./media/snapshot-debugger-upgrade/application-insights-site-extension-to-be-deleted.png)

4. Azure 앱 _서비스에 대한 응용 프로그램 인사이트 확장_ 버전이 설치된 경우 해당 확장을 선택하고 삭제를 클릭합니다. **예를** 확인하여 확장을 삭제하고 다음 단계로 이동하기 전에 삭제가 완료될 때까지 기다립니다.

   ![삭제 버튼이 강조 표시된 Azure 앱 서비스에 대한 응용 프로그램 인사이트 확장을 보여주는 앱 서비스 확장 스크린샷](./media/snapshot-debugger-upgrade/application-insights-site-extension-delete.png)

5. 리소스의 개요 블레이드로 이동하여 응용 프로그램 인사이트를 클릭합니다.

   ![세 개의 버튼의 스크린 샷. 이름 응용 프로그램 인사이트가 있는 가운데 버튼이 선택됩니다.](./media/snapshot-debugger-upgrade/application-insights-button.png)

6. 이 앱 서비스에 대한 응용 프로그램 인사이트 블레이드를 처음 본 경우 응용 프로그램 인사이트를 사용 설정하라는 메시지가 표시됩니다. **응용 프로그램 인사이트 설정**선택 합니다.
 
   ![응용 프로그램 인사이트 켜기 버튼이 강조 표시된 애플리케이션 인사이트 블레이드의 첫 경험 스크린샷](./media/snapshot-debugger-upgrade/turn-on-application-insights.png)

7. 현재 응용 프로그램 인사이트 설정이 표시됩니다. 설정을 변경할 기회를 원하지 않는 한 그대로 둘 수 있습니다. 블레이드 하단의 **적용** 버튼은 기본적으로 활성화되어 있지 않으며 버튼을 활성화하려면 설정 중 하나를 전환해야 합니다. 실제 설정을 변경할 필요가 없으며 설정을 변경한 다음 즉시 다시 변경할 수 있습니다. 프로파일러 설정을 전환한 다음 **적용을**선택하는 것이 좋습니다.

   ![응용 프로그램 인사이트 앱 서비스 구성 페이지의 스크린샷 적용 버튼이 빨간색으로 강조 표시되어 있습니다.](./media/snapshot-debugger-upgrade/view-application-insights-data.png)

8. **적용을**클릭하면 변경 사항을 확인하라는 메시지가 표시됩니다.

    > [!NOTE]
    > 업그레이드 프로세스의 일부로 사이트가 다시 시작됩니다.

   ![앱 서비스의 적용 모니터링 프롬프트의 스크린샷입니다. 텍스트 상자에는 "이제 앱 설정에 변경 사항을 적용하고 응용 프로그램 인사이트 리소스를 웹 앱에 연결하는 도구를 설치합니다. 그러면 사이트가 다시 시작됩니다. 계속하시겠습니까?”](./media/snapshot-debugger-upgrade/apply-monitoring-settings.png)

9. **예를** 클릭하여 변경 내용을 적용하고 프로세스가 완료될 때까지 기다립니다.

이제 사이트가 업그레이드되었으며 사용할 준비가 되었습니다.

## <a name="upgrading-snapshot-debugger-using-sdknuget"></a>SDK/Nuget을 사용하여 스냅샷 디버거 업그레이드

응용 프로그램이 `Microsoft.ApplicationInsights.SnapshotCollector` 아래 버전 1.3.1 버전을 사용하는 경우 작업을 계속하려면 [최신 버전으로](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) 업그레이드해야 합니다.
