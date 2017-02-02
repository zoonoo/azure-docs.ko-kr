---
title: "Azure 웹앱용 Azure Resource Manager 기반 플랫폼 간 명령줄 도구 | Microsoft Docs"
description: "새 Azure Resource Manager 기반 플랫폼 간 명령줄 도구를 사용하여 Azure 웹앱을 관리하는 방법에 대해 알아봅니다."
services: app-service\web
documentationcenter: 
author: ahmedelnably
manager: stefsch
editor: 
ms.assetid: d415b195-4262-416f-b59f-7e1aef200054
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/29/2016
ms.author: aelnably
translationtype: Human Translation
ms.sourcegitcommit: f595be46983bf07783b529de885d889c18fdb61a
ms.openlocfilehash: 6f75781af24d1ad4cb6460f0dfd528684aaad57f


---
# <a name="using-azure-resource-manager-based-xplat-cli-for-azure-app-service"></a>Azure App Service용 Azure Resource Manager 기반 XPlat CLI 사용
> [!div class="op_single_selector"]
> * [Azure CLI](app-service-web-app-azure-resource-manager-xplat-cli.md)
> * [Azure PowerShell](app-service-web-app-azure-resource-manager-powershell.md)

Microsoft Azure 플랫폼 간 명령줄 도구 버전 0.10.5 릴리스에서는 새로운 명령이 추가되었습니다. 이러한 명령은 Azure Resource Manager 기반 PowerShell 명령을 사용하여 App Service를 관리하는 기능을 제공합니다.

리소스 그룹을 관리하는 방법에 대한 자세한 내용은 [Azure CLI를 사용하여 Azure 리소스 및 리소스 그룹 관리](../azure-resource-manager/xplat-cli-azure-resource-manager.md)를 참조하세요. 

> [!NOTE] 
> 또한 리소스 관리 배포 모델용 차세대 CLI(Python으로 작성)인 [Azure CLI 2.0(미리 보기)](https://github.com/Azure/azure-cli)도 사용해 보세요.
>
>

## <a name="managing-app-service-plans"></a>앱 서비스 계획 관리
### <a name="create-an-app-service-plan"></a>앱 서비스 계획 만들기
App Service 계획을 만들려면 **azure appserviceplan create** 명령을 사용합니다.

다음은 서로 다른 매개 변수의 설명입니다.

* **--resource-group**: 새로 만든 App Service 계획을 포함하는 리소스 그룹
* **--name**: App Service 계획의 이름
* **--location**: App Service 계획 위치
* **--sku**: 원하는 가격 책정 SKU(옵션: F1(무료)) D1(공유) B1(Basic Small), B2(Basic Medium), B3(Basic Large) S1(Standard Small), S2(Standard Medium), S3(Standard Large) P1(Premium Small), P2(Premium Medium), P3(Premium Large)
* **--instances**: App Service 계획의 작업자 수(기본값은 1)

이 cmdlet을 사용하는 예제:

    azure appserviceplan create --name ContosoAppServicePlan --location "South Central US" --resource-group ContosoAzureResourceGroup --sku P1 --instances 10

#### <a name="create-a-linux-app-service-plan"></a>Linux App Service 계획 만들기

동일한 **azure appserviceplan create** 명령과 추가 매개 변수 **-islinux true** 사용 [Linux의 App Service 소개](app-service-linux-intro.md)에 설명된 제한 사항 및 지역 참조

### <a name="list-existing-app-service-plans"></a>기존 앱 서비스 계획 나열
기존 App Service 계획을 나열하려면 **azure appserviceplan list** 명령을 사용합니다.

특정 리소스 그룹에서 모든 앱 서비스 계획을 나열하려면 다음을 사용합니다.

    azure appserviceplan list --resource-group ContosoAzureResourceGroup

특정 App Service 계획을 가져오려면 **azure appserviceplan show** 명령을 사용합니다.

    azure appserviceplan show --name ContosoAppServicePlan --resource-group southeastasia

### <a name="configure-an-existing-app-service-plan"></a>기존 앱 서비스 계획 구성
기존 App Service 계획의 설정을 변경하려면 **azure appserviceplan config** 명령을 사용합니다. SKU 및 작업자 수를 변경할 수 있습니다. 

    azure appserviceplan config --name ContosoAppServicePlan --resource-group ContosoAzureResourceGroup --sku S1 --instances 9

#### <a name="scaling-an-app-service-plan"></a>앱 서비스 계획 크기 조정
기존 앱 서비스 계획을 크기 조정하려면 다음을 사용합니다.

    azure appserviceplan config --name ContosoAppServicePlan --resource-group ContosoAzureResourceGroup --instances 9

#### <a name="changing-the-sku-of-an-app-service-plan"></a>App Service 계획의 SKU 변경
기존 App Service 계획의 SKU를 변경하려면 다음을 사용합니다.

    azure appserviceplan config --name ContosoAppServicePlan --resource-group ContosoAzureResourceGroup --sku S1


### <a name="delete-an-existing-app-service-plan"></a>기존 앱 서비스 계획 삭제
기존 App Service 계획을 삭제하려면 할당된 모든 앱을 먼저 이동하거나 삭제해야 합니다. 그런 후 **azure webapp delete** 명령을 사용하여 App Service 계획을 삭제할 수 있습니다.

    azure appserviceplan delete --name ContosoAppServicePlan --resource-group southeastasia

## <a name="managing-app-service-apps"></a>App Service 앱 관리
### <a name="create-a-web-app"></a>웹앱 만들기
웹앱을 만들려면 **azure webapp create** 명령을 사용합니다.

다음은 서로 다른 매개 변수의 설명입니다.

* **--name**: 웹앱의 이름
* **--plan**: 웹앱을 호스트하는 데 사용되는 서비스 계획의 이름
* **--resource-group**: App Service 계획을 호스트하는 리소스 그룹
* **--location**: 웹앱 위치

이 cmdlet을 사용하는 예제:

    azure webapp create --name ContosoWebApp --resource-group ContosoAzureResourceGroup --plan ContosoAppServicePlan --location "South Central US"

### <a name="delete-an-existing-app"></a>기존 앱 삭제
기존 앱을 삭제하려면 **azure webapp delete** 명령을 사용하면 됩니다. 앱의 이름 및 리소스 그룹 이름을 지정해야 합니다.

    azure webapp delete --name ContosoWebApp --resource-group ContosoAzureResourceGroup

### <a name="list-existing-apps"></a>기존 앱 목록
기존 앱을 나열하려면 **azure webapp list** 명령을 사용합니다.

특정 리소스 그룹의 모든 앱을 나열하려면 다음을 사용합니다.

    azure webapp list --resource-group ContosoAzureResourceGroup

특정 앱을 가져오려면 **azure webapp show** 명령을 사용합니다.

    azure webapp show --name ContosoWebApp --resource-group ContosoAzureResourceGroup

### <a name="configure-an-existing-app"></a>기존 앱 구성
기존 앱에 대한 설정 및 구성을 변경하려면 **azure webapp config set** 명령을 사용합니다.

예 (1): 앱의 php 버전 변경 

    azure webapp config set --name ContosoWebApp --resource-group ContosoAzureResourceGroup --phpversion 5.6

예 (2): 앱 설정 추가 또는 변경

    webapp config appsettings set --name ContosoWebApp --resource-group ContosoAzureResourceGroup appsetting1=appsetting1value,appsetting2=appsetting2value

변경할 수 있는 기타 구성을 확인하려면 **azure webapp config set -h** 명령을 사용합니다.

### <a name="change-the-state-of-an-existing-app"></a>기존 앱의 상태 변경
#### <a name="restart-an-app"></a>앱 다시 시작
앱을 다시 시작하려면 앱의 이름 및 리소스 그룹을 지정해야 합니다.

    azure webapp restart --name ContosoWebApp --resource-group ContosoAzureResourceGroup

#### <a name="stop-an-app"></a>앱 중지
앱을 중지하려면 앱의 이름 및 리소스 그룹을 지정해야 합니다.

    azure webapp stop --name ContosoWebApp --resource-group ContosoAzureResourceGroup

#### <a name="start-an-app"></a>앱 시작
앱을 시작하려면 앱의 이름 및 리소스 그룹을 지정해야 합니다.

    azure webapp start --name ContosoWebApp --resource-group ContosoAzureResourceGroup

### <a name="manage-publishing-profiles-of-an-app"></a>앱의 게시 프로필 관리
각 앱에는 코드를 게시하는 데 사용할 수 있는 게시 프로필이 있습니다.

#### <a name="get-publishing-profile"></a>게시 프로필 가져오기
앱에 대한 게시 프로필을 가져오려면 다음을 사용합니다.

    azure webapp publishingprofile --name ContosoWebApp --resource-group ContosoAzureResourceGroup

이 명령은 게시 프로필 사용자 이름 및 암호를 명령줄에 에코합니다.

### <a name="manage-app-hostnames"></a>앱 호스트 관리
앱에 대한 호스트 이름 바인딩을 관리하려면 **azure webapp config hostnames** 명령을 사용합니다.  

#### <a name="list-hostname-bindings"></a>호스트 이름 바인딩 나열
앱에 대한 현재 호스트 이름 바인딩을 가져오려면 다음을 사용합니다.

    azure webapp config hostnames list --name ContosoWebApp --resource-group ContosoAzureResourceGroup

#### <a name="add-hostname-bindings"></a>호스트 이름 바인딩 추가
앱에 호스트 이름 바인딩을 추가하려면 다음을 사용합니다.

    azure webapp config hostnames add --name ContosoWebApp --resource-group ContosoAzureResourceGroup --hostname www.contoso.com

#### <a name="delete-hostname-bindings"></a>호스트 이름 바인딩 삭제
호스트 이름 바인딩을 삭제하려면 다음을 사용합니다.

    azure webapp config hostnames delete --name ContosoWebApp --resource-group ContosoAzureResourceGroup --hostname www.contoso.com

## <a name="next-steps"></a>다음 단계
* Azure Resource Manager CLI 지원에 대한 자세한 내용은 [Azure CLI를 사용하여 Azure 리소스 및 리소스 그룹 관리](../azure-resource-manager/xplat-cli-azure-resource-manager.md)를 참조하세요.
* PowerShell을 사용하여 App Service를 관리하는 방법에 대한 자세한 내용은 [Azure Resource Manager 기반 PowerShell을 사용하여 Azure 웹앱 관리](app-service-web-app-azure-resource-manager-powershell.md)를 참조하세요.
* Linux의 Azure App Service에 대해 알아보려면 [Linux의 Azure App Service 소개](app-service-linux-intro.md)를 참조하세요.



<!--HONumber=Dec16_HO3-->


