---
title: Application Insights에 대한 릴리스 주석 | Microsoft Docs
description: Application Insights에서 배포 또는 빌드 표식을 메트릭 탐색기 차트에 추가합니다.
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: 23173e33-d4f2-4528-a730-913a8fd5f02e
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 07/01/2019
ms.author: mbullwin
ms.openlocfilehash: e3ec202ba6126b150fb78c76591682f163018661
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/05/2019
ms.locfileid: "67604554"
---
# <a name="annotations-on-metric-charts-in-application-insights"></a>Application Insights의 메트릭 차트에 대한 주석

에 대 한 주석 [메트릭 탐색기](../../azure-monitor/app/metrics-explorer.md) 차트 배포한 새 빌드가 또는 기타 중요 한 이벤트를 표시 합니다. 주석을 사용 하면 쉽게 변경 내용을 응용 프로그램의 성능에 영향을 주지가 있는지 여부를 볼 수 있습니다. 자동으로 생성 합니다 [Azure 파이프라인](https://docs.microsoft.com/azure/devops/pipelines/tasks/) 빌드 시스템입니다. PowerShell에서 주석을 만들어 원하는 이벤트에 대한 플래그를 지정하는 주석을 만들 수도 있습니다.

> [!NOTE]
> 이 문서에서는 사용하지 않는 **클래식 메트릭 환경**을 반영합니다. 주석은 현재 클래식 환경 및 **[workbook](../../azure-monitor/app/usage-workbooks.md)** 에서만 사용할 수 있습니다. 현재 메트릭 경험에 대 한 자세한 내용은 참조 하세요 [Azure 메트릭 탐색기의 고급 기능](../../azure-monitor/platform/metrics-charts.md)합니다.

![주석 예제](./media/annotations/0-example.png)

## <a name="release-annotations-with-azure-pipelines-build"></a>Azure 파이프라인 빌드를 사용한 릴리스 주석

릴리스 주석은 Azure DevOps의 클라우드 기반 Azure 파이프라인 서비스의 기능입니다.

### <a name="install-the-annotations-extension-one-time"></a>주석 확장 설치(한 번)
릴리스 주석을 만들려면 수, Visual Studio Marketplace에서 사용할 수 있는 여러 Azure DevOps 확장 기능 중 하나를 설치 해야 합니다.

1. 에 로그인 하 여 [Azure DevOps](https://azure.microsoft.com/services/devops/) 프로젝트입니다.
   
1. Visual Studio Marketplace에서 [릴리스 주석 확장](https://marketplace.visualstudio.com/items/ms-appinsights.appinsightsreleaseannotations) 페이지, Azure DevOps 조직을 선택 하 고, 선택한 **설치** Azure DevOps 조직에 확장을 추가 합니다.
   
   ![Azure DevOps 조직을 선택 하 고 설치를 선택 합니다.](./media/annotations/1-install.png)
   
Azure DevOps 조직에 한 번씩 확장을 설치 해야 합니다. 이제 조직에서 모든 프로젝트에 대 한 릴리스 주석을 구성할 수 있습니다.

### <a name="configure-release-annotations"></a>릴리스 주석 구성

Azure 파이프라인 릴리스 템플릿의 마다 별도 API 키를 만듭니다.

1. 에 로그인 합니다 [Azure portal](https://portal.azure.com) 응용 프로그램을 모니터링 하는 Application Insights 리소스를 엽니다. 계정이 없는 경우 또는 [새 Application Insights 리소스 만들기](../../azure-monitor/app/app-insights-overview.md)합니다.
   
1. 엽니다는 **API 액세스** 탭 및 복사 합니다 **Application Insights ID**합니다.
   
   ![API 액세스에서 id입니다. 응용 프로그램을 복사](./media/annotations/2-app-id.png)

1. 별도 브라우저 창에서 열거나 Azure 파이프라인 배포를 관리 하는 릴리스 템플릿을 만듭니다.
   
1. 선택 **추가 작업**를 선택한 후는 **Application Insights 릴리스 주석을** 메뉴에서 작업 합니다.
   
   ![작업 추가 선택 하 고 Application Insights 릴리스 주석을 선택 합니다.](./media/annotations/3-add-task.png)
   
1. 아래 **응용 프로그램 ID**에서 복사한 Application Insights ID를 붙여 넣습니다 합니다 **API 액세스** 탭 합니다.
   
   ![Application Insights ID를 붙여 넣습니다.](./media/annotations/4-paste-app-id.png)
   
1. Application Insights 년대에서 **API 액세스** 창에서 **API 키 만들기**합니다. 
   
   ![API 액세스 탭에서 API 키 만들기를 선택 합니다.](./media/annotations/5-create-api-key.png)
   
1. 에 **API 키 만들기** 창에서 설명 입력 선택 **주석을 작성**를 선택한 후 **키 생성**. 새 키를 복사합니다.
   
   ![API 만들기 키 창에서 설명을 입력 주석 쓰기 선택한 다음 키 생성을 선택 합니다.](./media/annotations/6-create-api-key.png)
   
1. 릴리스 템플릿 창에서에 **변수** 탭을 선택 **추가** 새 API 키에 대 한 변수 정의 만듭니다.

1. 아래 **이름**, 입력 `ApiKey`, 아래에서 **값**에서 복사한 API 키를 붙여넣습니다 합니다 **API 액세스** 탭 합니다.
   
   ![Azure DevOps 변수 탭에서 추가, ApiKey 변수 이름을 선택 하 고 값에서 API 키를 붙여넣습니다.](./media/annotations/7-paste-api-key.png)
   
1. 선택 **저장할** 템플릿을 저장 하 여 주 릴리스 템플릿 창에서.

## <a name="view-annotations"></a>주석 보기
이제 릴리스 템플릿을 사용 하 여 새 릴리스를 배포할 때마다 주석이 Application Insights로 전달 됩니다. 차트에 표시 된 주석을 **메트릭 탐색기**합니다.

주석 마커 (밝은 회색 화살표)을 요청자, 소스 제어 분기, 릴리스 파이프라인 및 환경 등 릴리스에 대 한 세부 정보를 열려면 선택 합니다.

![릴리스 주석 마커를 선택 합니다.](./media/annotations/8-release.png)

## <a name="create-custom-annotations-from-powershell"></a>PowerShell에서 사용자 지정 주석 만들기
사용할 수는 [CreateReleaseAnnotation](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/API/CreateReleaseAnnotation.ps1) Azure DevOps를 사용 하지 않고 원하는 모든 프로세스에서 주석을 작성 하는 GitHub에서 PowerShell 스크립트입니다. 

1. 로컬 복사본 [CreateReleaseAnnotation.ps1](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/API/CreateReleaseAnnotation.ps1)합니다.
   
1. 이전 절차에서 단계를 사용 하 여 Application Insights에서 API 키를 만들고 Application Insights ID를 가져옵니다 **API 액세스** 탭 합니다.
   
1. 다음 코드에서는 값을 사용 하 여 꺾쇠 괄호가 자리 표시자를 대체를 사용 하 여 PowerShell 스크립트를 호출 합니다. `-releaseProperties` 는 선택 사항입니다. 
   
   ```powershell
   
        .\CreateReleaseAnnotation.ps1 `
         -applicationId "<applicationId>" `
         -apiKey "<apiKey>" `
         -releaseName "<releaseName>" `
         -releaseProperties @{
             "ReleaseDescription"="<a description>";
             "TriggerBy"="<Your name>" }
   ```

예를 들어 이전에 대 한 주석을 만드는 스크립트를 수정할 수 있습니다.

## <a name="next-steps"></a>다음 단계

* [작업 항목 만들기](../../azure-monitor/app/diagnostic-search.md#create-work-item)
* [PowerShell을 사용한 Automation](../../azure-monitor/app/powershell.md)
