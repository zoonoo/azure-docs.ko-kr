---
title: "Azure 컨테이너 레지스트리 리포지토리 | Microsoft Docs"
description: "Docker 이미지에 Azure 컨테이너 레지스트리 리포지토리를 사용하는 방법"
services: container-registry
documentationcenter: 
author: cristy
manager: balans
editor: dlepow
ms.service: container-registry
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/30/2017
ms.author: cristyg
ms.translationtype: HT
ms.sourcegitcommit: 83f19cfdff37ce4bb03eae4d8d69ba3cbcdc42f3
ms.openlocfilehash: 1e5d5ea5b1ec121fe008abc48178b1d58f540ce1
ms.contentlocale: ko-kr
ms.lasthandoff: 08/21/2017

---

# <a name="create-a-private-docker-container-registry-using-the-azure-powershell"></a>Azure PowerShell을 사용하여 개인 Docker 컨테이너 레지스트리 만들기
[Azure PowerShell](https://docs.microsoft.com/en-us/powershell/azure/overview)의 명령을 사용하여 Windows 컴퓨터에서 컨테이너 레지스트리를 만들고 설정을 관리합니다. [Azure Portal](container-registry-get-started-portal.md), [Azure CLI](container-registry-get-started-azure-cli.md)을 사용하여 또는 Container Registry [REST API](https://go.microsoft.com/fwlink/p/?linkid=834376)를 사용하여 프로그래밍 방식으로 컨테이너 레지스트리를 만들고 관리할 수도 있습니다.


* 배경 지식 및 개념은 [개요](container-registry-intro.md)를 참조하세요.
* 지원되는 cmdlet의 전체 목록은 [Azure Container Registry 관리 Cmdlet](https://docs.microsoft.com/en-us/powershell/module/azurerm.containerregistry/)을 참조하세요.


## <a name="prerequisites"></a>필수 조건
* **Azure PowerShell**: Azure PowerShell을 설치하고 시작하려면 [설치 지침](https://docs.microsoft.com/en-us/powershell/azure/install-azurerm-ps)을 참조하세요. `Login-AzureRMAccount`을 실행하여 Azure 구독에 로그인합니다. 자세한 내용은 [Azure PowerShell 시작](https://docs.microsoft.com/en-us/powershell/azure/get-started-azurep)을 참조하세요.
* **리소스 그룹**: 컨테이너 레지스트리를 만들기 전에 [리소스 그룹](../azure-resource-manager/resource-group-overview.md#resource-groups)을 만들거나 기존 리소스 그룹을 사용합니다. 리소스 그룹이 Container Registry 서비스를 [사용할 수 있는](https://azure.microsoft.com/regions/services/) 위치에 있도록 해야 합니다. Azure PowerShell을 사용하여 리소스 그룹을 만들려면 [PowerShell 참조](https://docs.microsoft.com/en-us/powershell/azure/get-started-azureps#create-a-resource-group)를 참조하세요.
* **저장소 계정**(선택 사항): 동일한 위치의 컨테이너 레지스트리를 백업할 표준 Azure [저장소 계정](../storage/common/storage-introduction.md)을 만듭니다. `New-AzureRMContainerRegistry`를 사용하여 레지스트리를 만들 때 저장소 계정을 지정하지 않으면 명령에서 자동으로 생성됩니다. PowerShell을 사용하여 저장소 계정을 만들려면 [PowerShell 참조](https://docs.microsoft.com/en-us/powershell/module/azure/new-azurestorageaccount)를 참조하세요. Premium Storage는 현재 지원되지 않습니다.
* **서비스 주체**(선택 사항): PowerShell을 사용하여 레지스트리를 만들 때 기본적으로 액세스가 가능하도록 설정되지 않습니다. 필요에 따라 레지스트리에 기존 Azure Active Directory 서비스 주체를 할당하거나 새 주체를 만들어서 할당할 수 있습니다. 또는 레지스트리의 관리 사용자 계정을 사용할 수 있습니다. 이 문서의 뒷부분에 나오는 섹션을 참조하세요. 레지스트리 액세스에 대한 자세한 내용은 [컨테이너 레지스트리로 인증](container-registry-authentication.md)을 참조하세요.

## <a name="create-a-container-registry"></a>컨테이너 레지스트리 만들기
`New-AzureRMContainerRegistry` 명령을 실행하여 컨테이너 레지스트리를 만듭니다.

> [!TIP]
> 레지스트리를 만들 때 전역적으로 고유한 최상위 도메인 이름(문자 및 숫자만 포함)을 지정합니다. 레지스트리 이름 예제가 `MyRegistry`이지만 자신만의 고유한 이름으로 대체합니다.
>
>

다음 명령은 미국 중남부 지역에 `MyResourceGroup`이라는 리소스 그룹의 `MyRegistry`라는 컨테이너 레지스트리를 만들기 위해 최소의 매개 변수를 사용합니다.

```PowerShell
$Registry = New-AzureRMContainerRegistry -ResourceGroupName "MyResourceGroup" -Name "MyRegistry"
```

* `-StorageAccountName` 는 선택 사항입니다. 지정하지 않으면 지정된 리소스 그룹에서 레지스트리 이름 및 타임스탬프로 구성된 이름으로 저장소 계정이 만들어집니다.

## <a name="assign-a-service-principal"></a>서비스 주체 할당
PowerShell 명령을 사용하여 Azure Active Directory [서비스 주체](../azure-resource-manager/resource-group-authenticate-service-principal.md)를 레지스트리에 할당합니다. 이 예제의 경우 서비스 주체에 소유자 역할이 할당되어 있지만 필요하면 [다른 역할](../active-directory/role-based-access-control-configure.md)을 할당할 수 있습니다.

### <a name="create-a-service-principal"></a>서비스 주체 만들기
다음 명령에서 새 서비스 주체를 만듭니다. `-Password` 매개 변수를 통해 강력한 암호를 지정합니다.

```PowerShell
$ServicePrincipal = New-AzureRMADServicePrincipal -DisplayName ApplicationDisplayName -Password "MyPassword"
```

### <a name="assign-a-new-or-existing-service-principal"></a>새로운 서비스 주체 또는 기존 서비스 주체 할당
레지스트리에 새 서비스 주체 또는 기존 서비스 주체를 할당할 수 있습니다. 레지스트리에 소유자 역할 액세스를 할당하려면 다음 예제와 유사한 명령을 실행합니다.

```PowerShell
New-AzureRMRoleAssignment -RoleDefinitionName Owner -ServicePrincipalName $ServicePrincipal.ApplicationId -Scope $Registry.Id
```

##<a name="sign-in-to-the-registry-with-the-service-principal"></a>서비스 주체를 사용하여 레지스트리에 로그인
레지스트리에 서비스 주체를 할당한 후에 다음 명령을 사용하여 로그인할 수 있습니다.

```PowerShell
docker login -u $ServicePrincipal.ApplicationId -p myPassword
```

## <a name="manage-admin-credentials"></a>관리자 자격 증명 관리
관리자 계정은 각 컨테이너 레지스트리에 대해 자동으로 생성되며 기본적으로 비활성화됩니다. 다음 예제는 컨테이너 레지스트리에 대한 관리자 자격 증명을 관리하는 PowerShell 명령을 보여줍니다.

### <a name="obtain-admin-user-credentials"></a>관리자 사용자 자격 증명 가져오기
```PowerShell
Get-AzureRMContainerRegistryCredential -ResourceGroupName "MyResourceGroup" -Name "MyRegistry"
```

### <a name="enable-admin-user-for-an-existing-registry"></a>기존 레지스트리에 대한 관리자 사용자 활성화
```PowerShell
Update-AzureRMContainerRegistry -ResourceGroupName "MyResourceGroup" -Name "MyRegistry" -EnableAdminUser
```

### <a name="disable-admin-user-for-an-existing-registry"></a>기존 레지스트리에 대한 관리자 사용자 비활성화
```PowerShell
Update-AzureRMContainerRegistry -ResourceGroupName "MyResourceGroup" -Name "MyRegistry" -DisableAdminUser
```

## <a name="next-steps"></a>다음 단계
* [Docker CLI를 사용하여 첫 번째 이미지 푸시](container-registry-get-started-docker-cli.md)

