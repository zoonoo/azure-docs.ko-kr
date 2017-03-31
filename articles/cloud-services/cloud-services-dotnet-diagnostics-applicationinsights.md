---
title: "Application Insights를 사용하여 클라우드 서비스 문제 해결 | Microsoft Docs"
description: "Application Insights를 통해 Azure 진단의 데이터를 처리하여 클라우드 서비스 문제를 해결하는 방법에 대해 알아봅니다."
services: cloud-services
documentationcenter: .net
author: sbtron
manager: timlt
editor: tysonn
ms.assetid: e93f387b-ef29-4731-ae41-0676722accb6
ms.service: cloud-services
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/15/2015
ms.author: saurabh
translationtype: Human Translation
ms.sourcegitcommit: 9553c9ed02fa198d210fcb64f4657f84ef3df801
ms.openlocfilehash: 78d8908a144dadb5fe9d4c48491abf153defe118
ms.lasthandoff: 03/23/2017


---
# <a name="troubleshoot-cloud-services-using-application-insights"></a>Application Insights를 사용하여 클라우드 서비스 문제 해결
[Azure SDK 2.8](https://azure.microsoft.com/downloads/) 및 Azure 진단 확장 1.5를 사용하면 클라우드 서비스에 대한 Azure 진단 데이터를 Application Insights로 직접 보낼 수 있습니다. 응용 프로그램 로그, Windows 이벤트 로그, ETW 로그 및 성능 카운터를 포함하여 Azure 진단에서 수집한 다양한 형식의 로그를 Application Insights로 보내 Application Insights 포털 UI에서 시각화할 수 있습니다. Application Insights SDK와 함께 사용할 경우 응용 프로그램의 메트릭 및 로그뿐만 아니라 Azure 진단의 시스템 및 인프라 수준 데이터를 이해할 수 있습니다.

## <a name="configure-azure-diagnostics-to-send-data-to-application-insights"></a>Application Insights에 데이터를 보내도록 Azure 진단 구성
Application Insights로 Azure 진단 데이터를 보내도록 클라우드 서비스 프로젝트를 설정하려면 다음 단계를 수행합니다.

1) Visual Studio 솔루션 탐색기에서 역할을 마우스 오른쪽 단추로 클릭하고 **속성**을 선택하여 역할 디자이너를 엽니다.

![솔루션 탐색기 역할 속성][1]

2) 역할 디자이너의 진단 섹션에서 **진단 데이터를 Application Insights로 보내기** 확인란을 선택합니다.

![역할 디자이너에서 진단 데이터를 Application Insights로 보내기][2]

3) 팝업으로 나타난 대화 상자에서 Azure 진단 데이터를 전송할 Application Insights 리소스를 선택합니다. 이 대화 상자를 사용하면 구독에서 기존 Application Insights 리소스를 선택하거나 Application Insights 리소스에 대한 계측 키를 수동으로 지정할 수 있습니다. 기존 Application Insights 리소스가 없는 경우 **새 리소스 만들기** 링크를 클릭하여 Application Insights 리소스를 만들 수 있는 Azure 클래식 포털에 대한 브라우저 창을 엽니다. Application Insights 리소스 만들기에 대한 자세한 내용은 [새 Application Insights 리소스 만들기](../application-insights/app-insights-create-new-resource.md)

![Application Insights 리소스 선택][3]

4) Application Insights 리소스를 추가하고 나면 해당 리소스에 대한 계측 키가 **APPINSIGHTS_INSTRUMENTATIONKEY**라는 이름의 서비스 구성 설정으로 저장됩니다. 서비스 구성 드롭다운에서 다른 구성을 선택하고 해당 구성에 대한 새 계측 키를 지정하여 각 서비스 구성 또는 환경에 대한 이 구성 설정을 변경할 수 있습니다.

![서비스 구성 선택][4]

**APPINSIGHTS_INSTRUMENTATIONKEY** 구성 설정은 게시 중 Visual Studio에서 적절한 Application Insights 리소스 정보로 진단 확장을 구성하는 데 사용됩니다. 구성 설정을 사용하면 각 서비스 구성에 대해 다른 계측 키를 편리하게 정의할 수 있습니다. Visual Studio는 해당 설정을 변환하여 게시할 때 진단 확장 공용 구성에 삽입합니다. PowerShell을 사용한 진단 확장 구성 프로세스를 단순화하기 위해 Visual Studio의 패키지 출력에도 적절한 Application Insights 계측 키와 함께 공용 구성 XML이 포함됩니다. 공용 config 파일이 Extensions 폴더에서 생성되고 PaaSDiagnostics<RoleName>.PubConfig.xml 패턴을 따릅니다. 모든 PowerShell 기반 배포에서 이 패턴을 사용하여 각 구성을 역할에 매핑합니다.

5) **진단 데이터를 Application Insights로 보내기**를 사용하면 Azure 진단 에이전트에 의해 수집된 모든 성능 카운터 및 오류 수준 로그를 Application Insights로 보내도록 Azure 진단이 자동으로 구성됩니다. Application Insights로 보낼 데이터를 추가로 구성하려는 경우 각 역할에 대한 *diagnostics.wadcfgx* 파일을 수동으로 편집해야 합니다. 구성을 수동으로 업데이트하는 방법에 대한 자세한 내용은 [Application Insights에 데이터를 보내도록 Azure 진단 구성](#configure-azure-diagnostics-to-send-data-to-application-insights) 을 참조하세요.

Azure 진단 데이터를 Application Insights로 보내도록 클라우드 서비스를 구성한 후에는 일반적인 방법으로 Azure에 배포하여 Azure 진단 확장이 사용되는지 확인할 수 있습니다. [Visual Studio를 사용하여 클라우드 서비스 게시](../vs-azure-tools-publishing-a-cloud-service.md)를 참조하세요.  

## <a name="viewing-azure-diagnostics-data-in-application-insights"></a>Application Insights에서 Azure 진단 데이터 보기
Azure 진단 원격 분석이 해당 클라우드 서비스에 대해 구성된 Application Insights 리소스에 표시됩니다.

다음은 다양한 Azure 진단 로그 형식이 Application Insights 개념에 어떻게 매핑되는지 나타낸 것입니다.  

* 성능 카운터는 Application Insights에서 사용자 지정 메트릭으로 표시됩니다.
* Windows 이벤트 로그는 Application Insights에서 추적 및 사용자 지정 이벤트로 표시됩니다.
* 응용 프로그램 로그, ETW 로그 및 진단 인프라 로그는 Application Insights에서 추적으로 표시됩니다.

Application Insights에서 Azure 진단 데이터를 보려면 다음을 수행합니다.

* [메트릭 탐색기](../application-insights/app-insights-metrics-explorer.md) 를 사용하여 사용자 지정 성능 카운터 또는 다양한 형식의 Windows 이벤트 로그 이벤트 수를 시각화합니다.

![메트릭 탐색기의 사용자 지정 메트릭][5]

* [검색](../application-insights/app-insights-diagnostic-search.md)을 사용하여 Azure 진단에서 보낸 다양한 추적 로그를 검색합니다. 예를 들어 역할에 처리되지 않은 예외가 있어 역할이 충돌 및 재활용되는 경우 해당 정보가 *Windows 이벤트 로그*의 *응용 프로그램* 채널에 표시됩니다. 검색 기능을 사용하여 Windows 이벤트 로그 오류를 확인하고 예외에 대한 전체 스택 추적을 가져와서 문제의 근본 원인을 찾을 수 있습니다.

![추적 검색][6]

## <a name="next-steps"></a>다음 단계
* [Application Insights SDK를 클라우드 서비스에 추가](../application-insights/app-insights-cloudservices.md) 하여 응용 프로그램에서 요청, 예외, 종속성 및 모든 사용자 지정 원격 분석에 대한 데이터를 보냅니다. Azure 진단 데이터와 함께 사용하여 동일한 Application Insight 리소스에 있는 모든 응용 프로그램 및 시스템을 전체적으로 확인할 수 있습니다.  

<!--Image references-->
[1]: ./media/cloud-services-dotnet-diagnostics-applicationinsights/solution-explorer-properties.png
[2]: ./media/cloud-services-dotnet-diagnostics-applicationinsights/role-designer-sendtoappinsights.png
[3]: ./media/cloud-services-dotnet-diagnostics-applicationinsights/select-appinsights-resource.png
[4]: ./media/cloud-services-dotnet-diagnostics-applicationinsights/role-designer-appinsights-serviceconfig.png
[5]: ./media/cloud-services-dotnet-diagnostics-applicationinsights/metrics-explorer-custom-metrics.png
[6]: ./media/cloud-services-dotnet-diagnostics-applicationinsights/search-windowseventlog-error.png

