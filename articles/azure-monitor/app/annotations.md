---
title: Application Insights에 대한 릴리스 주석 | Microsoft Docs
description: Application Insights를 사용하여 배포 또는 기타 중요한 이벤트를 추적하기 위한 주석을 만드는 방법을 알아봅니다.
ms.topic: conceptual
ms.date: 05/27/2021
ms.openlocfilehash: cfd1c9b28a79d68983e49ef5d6dfd70dd357ab47
ms.sourcegitcommit: 8651d19fca8c5f709cbb22bfcbe2fd4a1c8e429f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/14/2021
ms.locfileid: "112071032"
---
# <a name="release-annotations-for-application-insights"></a>Application Insights의 릴리스 주석

주석은 새 빌드를 배포한 위치나 다른 중요한 이벤트를 표시합니다. 주석으로 변경 내용이 애플리케이션의 성능에 영향을 주었는지 여부를 쉽게 확인할 수 있습니다. 주석은 [Azure Pipelines](/azure/devops/pipelines/tasks/) 빌드 시스템에서 자동으로 만들 수 있습니다. PowerShell에서 주석을 만들어 원하는 이벤트에 대한 플래그를 지정하는 주석을 만들 수도 있습니다.

## <a name="release-annotations-with-azure-pipelines-build"></a>Azure Pipelines 빌드를 사용한 릴리스 주석

릴리스 주석은 Azure DevOps의 클라우드 기반 Azure Pipelines 서비스 기능입니다.

다음 조건이 모두 충족되면 배포 작업에서 릴리스 주석을 자동으로 만듭니다.

- 배포할 리소스는 `APPINSIGHTS_INSTRUMENTATIONKEY` 앱 설정을 통해 Application Insights에 연결됩니다.
- Application Insights 리소스는 배포할 리소스와 동일한 구독에 있습니다.
- 다음 Azure DevOps 파이프라인 작업 중 하나를 사용하고 있습니다.

    | 작업 코드                 | 작업 이름                     | 버전     |
    |---------------------------|-------------------------------|--------------|
    | AzureAppServiceSettings   | Azure App Service 설정    | 임의          |
    | AzureRmWebAppDeployment   | Azure App Service 배포      | V3 이상 |
    | AzureFunctionApp          | Azure Functions               | 임의          |
    | AzureFunctionAppContainer | 컨테이너용 Azure Functions | 임의          |
    | AzureWebAppContainer      | Azure Web App for Containers  | 임의          |
    | AzureWebApp               | Azure 웹앱                 | 임의          |

> [!NOTE]
> Application Insights 주석 배포 작업을 계속 사용 중인 경우 해당 작업을 삭제해야 합니다.

### <a name="configure-release-annotations"></a>릴리스 주석 구성

이전 섹션에서 배포 작업 중 하나를 사용할 수 없는 경우에는 배포 파이프라인에 인라인 스크립트 작업을 추가해야 합니다.

1. 새 파이프라인 또는 기존 파이프라인으로 이동하여 작업을 선택합니다.
    :::image type="content" source="./media/annotations/task.png" alt-text="선택한 단계의 작업 스크린샷." lightbox="./media/annotations/task.png":::
1. 새 작업을 추가하고 **Azure CLI** 를 선택합니다.
    :::image type="content" source="./media/annotations/add-azure-cli.png" alt-text="새 작업을 추가하고 Azure CLI를 선택하는 스크린샷." lightbox="./media/annotations/add-azure-cli.png":::
1. 관련 Azure 구독을 지정합니다.  **스크립트 유형** 을 *PowerShell* 로 변경하고 **스크립트 위치** 를 *인라인* 으로 변경합니다.
1. [다음 섹션의 2단계에서 PowerShell 스크립트](#create-release-annotations-with-azure-cli)를 **인라인 스크립트** 에 추가합니다.
1. 아래 인수를 추가하여 꺾쇠 괄호 자리 표시자를 **스크립트 인수** 에 대한 값으로 바꿉니다. -releaseProperties는 선택 사항입니다.

    ```powershell
        -aiResourceId "<aiResourceId>" `
        -releaseName "<releaseName>" `
        -releaseProperties @{"ReleaseDescription"="<a description>";
             "TriggerBy"="<Your name>" }
    ```

    :::image type="content" source="./media/annotations/inline-script.png" alt-text="스크립트 유형, 스크립트 위치, 인라인 스크립트 및 스크립트 인수가 강조 표시된 Azure CLI 작업 설정의 스크린샷." lightbox="./media/annotations/inline-script.png":::

1. 저장합니다.

## <a name="create-release-annotations-with-azure-cli"></a>Azure CLI를 사용하여 릴리스 주석 만들기

CreateReleaseAnnotation PowerShell 스크립트를 사용하여 Azure DevOps를 사용하지 않고도 원하는 모든 프로세스에서 주석을 만들 수 있습니다.

1. [Azure CLI](/cli/azure/authenticate-azure-cli)에 로그인합니다.

2. 아래 스크립트의 로컬 복사본을 만들고 CreateReleaseAnnotation.ps1을 호출합니다.

    ```powershell
    param(
        [parameter(Mandatory = $true)][string]$aiResourceId,
        [parameter(Mandatory = $true)][string]$releaseName,
        [parameter(Mandatory = $false)]$releaseProperties = @()
    )
    
    $annotation = @{
        Id = [GUID]::NewGuid();
        AnnotationName = $releaseName;
        EventTime = (Get-Date).ToUniversalTime().GetDateTimeFormats("s")[0];
        Category = "Deployment";
        Properties = ConvertTo-Json $releaseProperties -Compress
    }
    
    $body = (ConvertTo-Json $annotation -Compress) -replace '(\\+)"', '$1$1"' -replace "`"", "`"`""

    az rest --method put --uri "$($aiResourceId)/Annotations?api-version=2015-05-01" --body "$($body) "
    ```

3. 다음 코드로 PowerShell 스크립트를 호출하여 꺽쇠 괄호로 묶인 자리 표시자를 고유한 값으로 바꿉니다. -releaseProperties는 선택 사항입니다.

    ```powershell
         .\CreateReleaseAnnotation.ps1 `
          -aiResourceId "<aiResourceId>" `
          -releaseName "<releaseName>" `
          -releaseProperties @{"ReleaseDescription"="<a description>";
              "TriggerBy"="<Your name>" }
    ```

|인수 | 정의 | 참고|
|--------------|-----------------------|--------------------|
|aiResourceId | 대상 Application Insights 리소스에 대한 리소스 ID입니다. | 예제:<br> /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/MyRGName/providers/microsoft.insights/components/MyResourceName|
|releaseName | 생성된 릴리스 주석을 지정할 이름입니다. | | 
|releaseProperties | 사용자 지정 메타데이터를 주석에 연결하는 데 사용됩니다. | 선택 사항|

## <a name="view-annotations"></a>주석 보기

> [!NOTE]
> 현재 Application Insights의 메트릭 창에서 릴리스 주석 사용 불가능

이제 릴리스 템플릿을 사용하여 새 릴리스를 배포할 때마다 주석이 Application Insights로 전송됩니다. 다음 위치에서 주석을 볼 수 있습니다.

- 성능

    :::image type="content" source="./media/annotations/performance.png" alt-text="릴리스 속성 탭을 표시하기 위해 릴리스 주석(파란색 화살표)이 선택된 성능 탭의 스크린샷." lightbox="./media/annotations/performance.png":::

- 오류

    :::image type="content" source="./media/annotations/failures.png" alt-text="릴리스 속성 탭을 표시하기 위해 릴리스 주석(파란색 화살표)이 선택된 오류 탭의 스크린샷." lightbox="./media/annotations/failures.png":::
- 사용

    :::image type="content" source="./media/annotations/usage-pane.png" alt-text="릴리스 주석이 선택된 사용자 탭 표시줄의 스크린샷. 릴리스 주석은 릴리스가 발생한 시점을 나타내는 차트 위에 파란색 화살표로 표시됩니다." lightbox="./media/annotations/usage-pane.png":::

- 통합 문서

    모든 로그 기반 통합 문서 쿼리에서는 x축이 시간을 나타내도록 시각화됩니다.
    
    :::image type="content" source="./media/annotations/workbooks-annotations.png" alt-text="주석이 표시된 시계열 로그 기반 쿼리가 있는 통합 문서 창의 스크린샷." lightbox="./media/annotations/workbooks-annotations.png":::
    
    통합 문서에서 주석을 활성화하려면 **고급 설정** 으로 이동하여 **주석 표시** 를 선택합니다.
    
    :::image type="content" source="./media/annotations/workbook-show-annotations.png" alt-text="주석 표시 확인란이 강조 표시된 고급 설정 메뉴의 스크린샷.":::

주석 마커를 클릭하면 요청자, 소스 제어 분기, 릴리스 파이프라인, 환경 등 릴리스에 대한 세부 정보를 열어볼 수 있습니다.

## <a name="next-steps"></a>다음 단계

* [작업 항목 만들기](./diagnostic-search.md#create-work-item)
* [PowerShell을 사용한 Automation](./powershell.md)
