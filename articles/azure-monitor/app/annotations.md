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
ms.date: 05/31/2019
ms.author: mbullwin
ms.openlocfilehash: 6567d7f2ebaab5bd7b5bc8fb7b5a62970f169161
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/04/2019
ms.locfileid: "66476164"
---
# <a name="annotations-on-metric-charts-in-application-insights"></a>Application Insights의 메트릭 차트에 대한 주석

[메트릭 탐색기](../../azure-monitor/app/metrics-explorer.md) 차트의 주석은 새 빌드를 배포한 위치 또는 다른 중요한 이벤트를 표시합니다. 릴리스 주석으로 변경 내용이 애플리케이션의 성능에 영향을 주는지 여부를 쉽게 확인할 수 있습니다. 릴리스 주석은 [Azure DevOps Services 빌드 시스템](https://docs.microsoft.com/azure/devops/pipelines/tasks/)에서 자동으로 만들 수 있습니다. PowerShell에서 주석을 만들어 원하는 이벤트에 대한 플래그를 지정하는 주석을 만들 수도 있습니다.

> [!NOTE]
> 이 문서에서는 사용하지 않는 **클래식 메트릭 환경**을 반영합니다. 주석은 현재 클래식 환경 및 **[workbook](../../azure-monitor/app/usage-workbooks.md)** 에서만 사용할 수 있습니다. 현재 메트릭 환경에 대해 자세히 알아보려면 [이 문서](../../azure-monitor/platform/metrics-charts.md)를 참조하세요.

![주석 예제](./media/annotations/0-example.png)

## <a name="release-annotations-with-azure-devops-services-build"></a>Azure DevOps Services 빌드를 사용한 릴리스 주석

릴리스 주석은 Azure DevOps Services의 클라우드 기반 Azure Pipelines 서비스 기능입니다.

### <a name="install-the-annotations-extension-one-time"></a>주석 확장 설치(한 번)
릴리스 주석을 만들려면 Visual Studio Marketplace에서 사용 가능한 여러 Azure DevOps Services 확장 중 하나를 설치해야 합니다.

1. [Azure DevOps Services](https://azure.microsoft.com/services/devops/) 프로젝트에 로그인합니다.
2. Visual Studio Marketplace의 [릴리스 주석 확장을 가져와서](https://marketplace.visualstudio.com/items/ms-appinsights.appinsightsreleaseannotations) Azure DevOps Services 조직에 추가합니다.

![Azure DevOps 조직을 선택 하 고 설치 합니다.](./media/annotations/1-install.png)

Azure DevOps Services 조직에 대해 이 작업을 한 번만 수행하면 됩니다. 이제 조직의 모든 프로젝트에 대해 릴리스 주석을 구성할 수 있습니다.

### <a name="configure-release-annotations"></a>릴리스 주석 구성

각 Azure DevOps Services 릴리스 템플릿에 대한 별도의 API 키를 가져와야 합니다.

1. 에 로그인 합니다 [Microsoft Azure 포털](https://portal.azure.com) 응용 프로그램을 모니터링 하는 Application Insights 리소스를 엽니다. (또는 아직 만들지 않은 경우 [지금 만듭니다](../../azure-monitor/app/app-insights-overview.md).)
2. 엽니다는 **API 액세스** 탭 및 복사 합니다 **Application Insights ID**합니다.
   
    ![portal.azure.com에서 Application Insights 리소스를 열고 설정을 선택합니다. API 액세스를 엽니다. 애플리케이션 ID를 복사합니다.](./media/annotations/2-app-id.png)

4. 별도의 브라우저 창에서, Azure DevOps Services에서 배포를 관리하는 릴리스 템플릿을 열거나 만듭니다.
   
    작업을 추가하고 메뉴에서 Application Insights 릴리스 주석 작업을 선택합니다.

   ![추가 작업에 더하기 기호를 클릭 하 고 Application Insights 릴리스 주석을 선택 합니다. Application Insights ID를 붙여 넣습니다.](./media/annotations/3-add-task.png)

    붙여넣기 합니다 **응용 프로그램 ID** API 액세스 탭에서 복사 합니다.
   
    ![Application Insights ID를 붙여 넣습니다.](./media/annotations/4-paste-app-id.png)

5. Azure 창으로 돌아가 새 API 키를 만들어 복사합니다.
   
    ![Azure 창의 API 액세스 탭에서 API 키 만들기를 클릭 합니다.](./media/annotations/5-create-api-key.png)

    ![API 만들기 키 탭에서 메모를 제공할 주석 쓰기를 확인 하 고 키 생성을 클릭 합니다. 새 키를 복사합니다.](./media/annotations/6-create-api-key.png)

6. 릴리스 템플릿의 구성 탭을 엽니다.
   
    `ApiKey`에 대한 변수 정의를 만듭니다.
   
    ApiKey 변수 정의에 API 키를 붙여넣습니다.
   
    ![Azure DevOps 서비스 창에서 변수 탭을 선택 하 고 추가 클릭 합니다. ApiKey에 대 한 값으로 이름을 설정 하면 생성 된 키를 붙여 넣고 잠금 아이콘을 클릭.](./media/annotations/7-paste-api-key.png)
1. 마지막으로 릴리스 파이프라인을 **저장**합니다.


## <a name="view-annotations"></a>주석 보기
이제 릴리스 템플릿을 사용하여 새 릴리스를 배포할 때마다 주석이 Application Insights로 전송됩니다. 주석은 메트릭 탐색기의 차트에 표시됩니다.

파이프라인, 환경 및 릴리스를 요청자, 소스 제어 분기를 릴리스 하는 방법에 대 한 주석 마커 (밝은 회색 화살표) 세부 정보를 열려면 클릭 합니다.

![릴리스 주석 마커를 클릭합니다.](./media/annotations/8-release.png)

## <a name="create-custom-annotations-from-powershell"></a>PowerShell에서 사용자 지정 주석 만들기
Azure DevOps Services를 사용하지 않고 원하는 모든 프로세스에서 주석을 만들 수도 있습니다. 


1. [GitHub에서 Powershell 스크립트](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/API/CreateReleaseAnnotation.ps1)의 로컬 복사본을 만듭니다.

2. 응용 프로그램 ID를 가져오고 API 액세스 탭에서 API 키를 만듭니다.

3. 다음과 같은 스크립트를 호출합니다.

```PS

     .\CreateReleaseAnnotation.ps1 `
      -applicationId "<applicationId>" `
      -apiKey "<apiKey>" `
      -releaseName "<myReleaseName>" `
      -releaseProperties @{
          "ReleaseDescription"="a description";
          "TriggerBy"="My Name" }
```

과거에 대한 주석을 만드는 등 이 스크립트를 쉽게 수정할 수 있습니다.

## <a name="next-steps"></a>다음 단계

* [작업 항목 만들기](../../azure-monitor/app/diagnostic-search.md#create-work-item)
* [PowerShell을 사용한 Automation](../../azure-monitor/app/powershell.md)
