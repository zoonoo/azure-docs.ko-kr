---
title: "Azure 웹앱에 대한 Azure Resource Manager 기반 PowerShell 명령 | Microsoft Docs"
description: "새 Azure Resource Manager 기반 PowerShell 명령을 사용하여 Azure 웹앱을 관리하는 방법에 대해 알아봅니다."
services: app-service\web
documentationcenter: 
author: ahmedelnably
manager: stefsch
editor: 
ms.assetid: 4231fbba-61e5-4f92-b958-531c066fb87f
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/29/2016
ms.author: aelnably
ms.translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 8d574f051a327ba0409e6f25a5886af673d3d5e0
ms.contentlocale: ko-kr
ms.lasthandoff: 07/06/2017


---
# <a name="using-azure-resource-manager-based-powershell-to-manage-azure-web-apps"></a>Azure Resource Manager 기반 PowerShell을 사용하여 Azure 웹앱 관리
> [!div class="op_single_selector"]
> * [Azure CLI](app-service-web-app-azure-resource-manager-xplat-cli.md)
> * [Azure PowerShell](app-service-web-app-azure-resource-manager-powershell.md)
> 
> 

Microsoft Azure PowerShell 버전 1.0.0에서는 사용자에게 Azure Resource Manager 기반 PowerShell 명령을 사용하여 웹앱을 관리하는 기능을 제공하는 새로운 명령이 추가되었습니다.

리소스 그룹 관리에 대해 알아보려면 [Azure Resource Manager로 Azure PowerShell 사용](../powershell-azure-resource-manager.md)을 참조하세요. 

PowerShell cmdlet에 대한 매개 변수 및 옵션의 전체 목록에 대해 알아보려면 [웹앱 Azure Resource Manager 기반 PowerShell Cmdlet의 전체 Cmdlet 참조](https://msdn.microsoft.com/library/mt619237.aspx)

## <a name="managing-app-service-plans"></a>앱 서비스 계획 관리
### <a name="create-an-app-service-plan"></a>앱 서비스 계획 만들기
앱 서비스 계획을 만들려면 **New-AzureRmAppServicePlan** cmdlet을 사용합니다.

다음은 서로 다른 매개 변수의 설명입니다.

* **Name**: 앱 서비스 계획의 이름.
* **Location**: 서비스 계획 위치.
* **ResourceGroupName**: 새로 만든 앱 서비스 계획을 포함하는 리소스 그룹.
* **Tier**: 원하는 가격 책정 계층(기본값은 Free, 다른 옵션은 Shared, Basic, Standard 및 Premium)
* **WorkerSize**: 작업자의 크기(Tier 매개 변수가 Basic, Standard 또는 Premium으로 지정된 경우 기본값은 Small입니다. 다른 옵션은 Medium 및 Large입니다.)
* **NumberofWorkers**: 앱 서비스 계획에서 작업자의 수(기본값은 1). 

이 cmdlet을 사용하는 예제:

    New-AzureRmAppServicePlan -Name ContosoAppServicePlan -Location "South Central US" -ResourceGroupName ContosoAzureResourceGroup -Tier Premium -WorkerSize Large -NumberofWorkers 10

### <a name="create-an-app-service-plan-in-an-app-service-environment"></a>앱 서비스 환경에서 앱 서비스 계획 만들기
ASE(App Service Environment)에서 새 App Service 계획을 만들려면 동일한 **New-AzureRmAppServicePlan** 명령과 추가 매개 변수를 사용하여 ASE 이름과 ASE 리소스 그룹 이름을 지정합니다.

이 cmdlet을 사용하는 예제:

    New-AzureRmAppServicePlan -Name ContosoAppServicePlan -Location "South Central US" -ResourceGroupName ContosoAzureResourceGroup -AseName constosoASE -AseResourceGroupName contosoASERG -Tier Premium -WorkerSize Large -NumberofWorkers 10

앱 서비스 환경에 대해 자세히 알아보려면 [앱 서비스 환경 소개](app-service-app-service-environment-intro.md)

### <a name="list-existing-app-service-plans"></a>기존 앱 서비스 계획 나열
기존 앱 서비스 계획을 나열하려면 **Get-AzureRmAppServicePlan** cmdlet을 사용합니다.

구독에서 모든 앱 서비스 계획을 목록화하려면 다음을 사용합니다. 

    Get-AzureRmAppServicePlan

특정 리소스 그룹에서 모든 앱 서비스 계획을 나열하려면 다음을 사용합니다.

    Get-AzureRmAppServicePlan -ResourceGroupname ContosoAzureResourceGroup

특정 앱 서비스 계획을 가져오려면 다음을 사용합니다.

    Get-AzureRmAppServicePlan -Name ContosoAppServicePlan


### <a name="configure-an-existing-app-service-plan"></a>기존 앱 서비스 계획 구성
기존 앱 서비스 계획의 설정을 변경하려면 **Set-AzureRmAppServicePlan** cmdlet를 사용합니다. 계층, 작업자 크기 및 작업자의 수를 변경할 수 있습니다. 

    Set-AzureRmAppServicePlan -Name ContosoAppServicePlan -ResourceGroupName ContosoAzureResourceGroup -Tier Standard -WorkerSize Medium -NumberofWorkers 9

#### <a name="scaling-an-app-service-plan"></a>앱 서비스 계획 크기 조정
기존 앱 서비스 계획을 크기 조정하려면 다음을 사용합니다.

    Set-AzureRmAppServicePlan -Name ContosoAppServicePlan -ResourceGroupName ContosoAzureResourceGroup -NumberofWorkers 9

#### <a name="changing-the-worker-size-of-an-app-service-plan"></a>앱 서비스 계획의 작업자 크기 변경
기존 앱 서비스 계획에서 작업자의 크기를 변경하려면 다음을 사용합니다.

    Set-AzureRmAppServicePlan -Name ContosoAppServicePlan -ResourceGroupName ContosoAzureResourceGroup -WorkerSize Medium

#### <a name="changing-the-tier-of-an-app-service-plan"></a>앱 서비스 계획의 계층 변경
기존 앱 서비스 계획에서 계층을 변경하려면 다음을 사용합니다.

    Set-AzureRmAppServicePlan -Name ContosoAppServicePlan -ResourceGroupName ContosoAzureResourceGroup -Tier Standard

### <a name="delete-an-existing-app-service-plan"></a>기존 앱 서비스 계획 삭제
기존 App Service 계획을 삭제하려면 할당된 모든 웹앱을 먼저 이동하거나 삭제해야 합니다. 그런 후 **Remove-AzureRmAppServicePlan** cmdlet을 사용하여 App Service 계획을 삭제할 수 있습니다.

    Remove-AzureRmAppServicePlan -Name ContosoAppServicePlan -ResourceGroupName ContosoAzureResourceGroup

## <a name="managing-app-service-web-apps"></a>앱 서비스 웹앱 관리
### <a name="create-a-web-app"></a>웹앱 만들기
웹앱을 만들려면 **New-AzureRmWebApp** cmdlet을 사용합니다.

다음은 서로 다른 매개 변수의 설명입니다.

* **Name**: 웹앱에 대한 이름.
* **AppServicePlan**: 웹앱을 호스트하는 데 사용되는 서비스 계획에 대한 이름.
* **ResourceGroupName**: 앱 서비스 계획을 호스트하는 리소스 그룹.
* **Location**: 웹앱 위치.

이 cmdlet을 사용하는 예제:

    New-AzureRmWebApp -Name ContosoWebApp -AppServicePlan ContosoAppServicePlan -ResourceGroupName ContosoAzureResourceGroup -Location "South Central US"

### <a name="create-a-web-app-in-an-app-service-environment"></a>App Service Environment에서 웹앱 만들기
ASE(App Service Environment)에서 웹앱을 만들려면 동일한 **New-AzureRmWebApp** 명령과 추가 매개 변수를 지정하여 ASE가 속하는 ASE 이름 및 리소스 그룹 이름을 지정합니다.

    New-AzureRmWebApp -Name ContosoWebApp -AppServicePlan ContosoAppServicePlan -ResourceGroupName ContosoAzureResourceGroup -Location "South Central US"  -ASEName ContosoASEName -ASEResourceGroupName ContosoASEResourceGroupName

앱 서비스 환경에 대해 자세히 알아보려면 [앱 서비스 환경 소개](app-service-app-service-environment-intro.md)

### <a name="delete-an-existing-web-app"></a>기존 웹앱 삭제
기존 웹앱을 삭제하려면 **Remove-AzureRmWebApp** cmdlet을 사용할 수 있으며 웹앱의 이름 및 리소스 그룹 이름을 지정해야 합니다.

    Remove-AzureRmWebApp -Name ContosoWebApp -ResourceGroupName ContosoAzureResourceGroup


### <a name="list-existing-web-apps"></a>기존 웹앱 나열
기존 웹앱을 나열하려면 **Get-AzureRmWebApp** cmdlet을 사용합니다.

구독에서 모든 웹앱을 나열하려면 다음을 사용합니다.

    Get-AzureRmWebApp

특정 리소스 그룹에서 모든 웹앱을 나열하려면 다음을 사용합니다.

    Get-AzureRmWebApp -ResourceGroupname ContosoAzureResourceGroup

특정 웹앱을 가져오려면 다음을 사용합니다.

    Get-AzureRmWebApp -Name ContosoWebApp

### <a name="configure-an-existing-web-app"></a>기존 웹앱 구성
기존 웹앱에 대한 설정 및 구성을 변경하려면 **Set-AzureRmWebApp** cmdlet을 사용합니다. 매개 변수의 전체 목록은 [Cmdlet 참조 링크](https://msdn.microsoft.com/library/mt652487.aspx)

예 (1): 이 cmdlet을 사용하여 연결 문자열 변경

    $connectionstrings = @{ ContosoConn1 = @{ Type = “MySql”; Value = “MySqlConn”}; ContosoConn2 = @{ Type = “SQLAzure”; Value = “SQLAzureConn”} }
    Set-AzureRmWebApp -Name ContosoWebApp -ResourceGroupName ContosoAzureResourceGroup -ConnectionStrings $connectionstrings

예 (2): 앱 설정 추가 또는 변경

    $appsettings = @{appsetting1 = "appsetting1value"; appsetting2 = "appsetting2value"}
    Set-AzureRmWebApp -Name ContosoWebApp -ResourceGroupName ContosoAzureResourceGroup -AppSettings $appsettings


예 (3): 64비트 모드에서 실행되도록 웹앱 설정

    Set-AzureRmWebApp -Name ContosoWebApp -ResourceGroupName ContosoAzureResourceGroup -Use32BitWorkerProcess $False

### <a name="change-the-state-of-an-existing-web-app"></a>기존 웹앱의 상태 변경
#### <a name="restart-a-web-app"></a>웹앱 다시 시작
웹앱을 다시 시작하려면 웹앱의 이름 및 리소스 그룹을 지정해야 합니다.

    Restart-AzureRmWebapp -Name ContosoWebApp -ResourceGroupName ContosoAzureResourceGroup

#### <a name="stop-a-web-app"></a>웹앱 중지
웹앱을 중지하려면 웹앱의 이름 및 리소스 그룹을 지정해야 합니다.

    Stop-AzureRmWebapp -Name ContosoWebApp -ResourceGroupName ContosoAzureResourceGroup

#### <a name="start-a-web-app"></a>웹앱 시작
웹앱을 시작하려면 웹앱의 이름 및 리소스 그룹을 지정해야 합니다.

    Start-AzureRmWebapp -Name ContosoWebApp -ResourceGroupName ContosoAzureResourceGroup

### <a name="manage-web-app-publishing-profiles"></a>웹앱 게시 프로필 관리
각 웹앱은 앱을 게시하는 데 사용할 수 있는 게시 프로필을 가지며 게시 프로필에서 다양한 작업을 실행할 수 있습니다.

#### <a name="get-publishing-profile"></a>게시 프로필 가져오기
웹앱에 대한 게시 프로필을 가져오려면 다음을 사용합니다.

    Get-AzureRmWebAppPublishingProfile -Name ContosoWebApp -ResourceGroupName ContosoAzureResourceGroup -OutputFile .\publishingprofile.txt

이 명령은 게시 프로필을 명령줄로 반향할 뿐만 아니라 게시 프로필을 텍스트 파일로 출력합니다.

#### <a name="reset-publishing-profile"></a>게시 프로필 다시 설정
FTP에 대한 게시 암호 및 웹앱에 대한 웹 배포를 다시 설정하려면 다음을 사용합니다.

    Reset-AzureRmWebAppPublishingProfile -Name ContosoWebApp -ResourceGroupName ContosoAzureResourceGroup

### <a name="manage-web-app-certificates"></a>웹앱 인증서 관리
웹앱 인증서를 관리하는 방법에 대해 알아보려면 [PowerShell을 사용하여 SSL 인증서 바인딩](app-service-web-app-powershell-ssl-binding.md)

### <a name="next-steps"></a>다음 단계
* Azure Resource Manager PowerShell 지원에 대해 알아보려면 [Azure Resource Manager로 Azure PowerShell 사용](../powershell-azure-resource-manager.md)
* 앱 서비스 환경에 대해 알아보려면 [앱 서비스 환경 소개](app-service-app-service-environment-intro.md)
* PowerShell을 사용하여 앱 서비스 SSL 인증서 관리에 대해 알아보려면 [PowerShell을 사용하여 SSL 인증서 바인딩](app-service-web-app-powershell-ssl-binding.md)
* Azure 웹앱에 대한 Azure Resource Manager 기반 PowerShell cmdlet의 전체 목록에 대해 알아보려면 [웹앱 Azure Resource Manager PowerShell Cmdlet의 전체 Cmdlet 참조](https://msdn.microsoft.com/library/mt619237.aspx)
* * CLI를 사용하여 App Service를 관리하는 방법에 대한 자세한 내용은 [Azure Web App에 Azure Resource Manager 기반 플랫폼 간 CLI 사용](app-service-web-app-azure-resource-manager-xplat-cli.md)을 참조하세요.


