---
title: Azure PowerShell를 사용 하 여 Azure 스프링 클라우드를 배포 하는 방법
description: Azure PowerShell를 사용 하 여 Azure 스프링 클라우드를 배포 하는 방법
author: bmitchell287
ms.author: brendm
ms.topic: conceptual
ms.service: spring-cloud
ms.devlang: azurepowershell
ms.date: 11/16/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 3cb320a37818084f2fbcad22a3cc992655b19c3d
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2020
ms.locfileid: "95550012"
---
# <a name="deploy-azure-spring-cloud-with-azure-powershell"></a>Azure PowerShell를 사용 하 여 Azure 스프링 클라우드 배포

이 문서에서는 [SpringCloud](/powershell/module/Az.SpringCloud) PowerShell 모듈을 사용 하 여 Azure 스프링 클라우드 인스턴스를 만드는 방법을 설명 합니다.

## <a name="requirements"></a>요구 사항

* Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.

[!INCLUDE [azure-powershell-requirements-no-header.md](../../includes/azure-powershell-requirements-no-header.md)]

  > [!IMPORTANT]
  > **SpringCloud** PowerShell 모듈은 미리 보기 상태 이지만 cmdlet을 사용 하 여 별도로 설치 해야 합니다 `Install-Module` . 이 PowerShell 모듈이 일반 공급되면 이후 Az PowerShell 모듈 릴리스의 일부가 되며 기본적으로 Azure Cloud Shell 내에서 사용할 수 있습니다.

  ```azurepowershell-interactive
  Install-Module -Name Az.SpringCloud
  ```

* 여러 Azure 구독이 있는 경우 리소스에 대한 요금이 청구되는 적절한 구독을 선택합니다. [Set-AzContext](/powershell/module/az.accounts/set-azcontext) cmdlet을 사용하여 특정 구독을 선택합니다.

  ```azurepowershell-interactive
  Set-AzContext -SubscriptionId 00000000-0000-0000-0000-000000000000
  ```

## <a name="create-a-resource-group"></a>리소스 그룹 만들기

[New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) cmdlet을 사용하여 [Azure 리소스 그룹](../azure-resource-manager/management/overview.md)을 만듭니다. 리소스 그룹은 Azure 리소스가 그룹으로 배포되고 관리되는 논리 컨테이너입니다.

다음 예제에서는 지정된 이름으로 지정된 위치에 리소스 그룹을 만듭니다.

```azurepowershell-interactive
New-AzResourceGroup -Name <resource group name> -Location eastus
```

## <a name="provision-a-new-instance-of-azure-spring-cloud"></a>Azure 스프링 클라우드의 새 인스턴스 프로 비전

Azure 스프링 클라우드의 새 인스턴스를 만들려면 [AzSpringCloud](/powershell/module/az.springcloud/new-azspringcloud) cmdlet을 사용 합니다. 다음 예제에서는 이전에 만든 리소스 그룹에 지정 된 이름을 사용 하 여 Azure 스프링 클라우드 서비스를 만듭니다.

```azurepowershell-interactive
New-AzSpringCloud -ResourceGroupName <resource group name> -name <service instance name> -Location eastus
```

## <a name="create-a-new-azure-spring-cloud-app"></a>새 Azure 스프링 클라우드 앱 만들기

새 앱을 만들려면 [AzSpringCloudApp](/powershell/module/az.springcloud/new-azspringcloudapp) cmdlet을 사용 합니다. 다음 예제에서는 라는 Azure 스프링 클라우드 앱을 만듭니다 `gateway` .

```azurepowershell-interactive
New-AzSpringCloudApp -ResourceGroupName <resource group name> -ServiceName <service instance name> -AppName gateway
```

## <a name="create-a-new-azure-spring-cloud-deployment"></a>새 Azure 스프링 클라우드 배포 만들기

새 배포를 만들려면 [AzSpringCloudAppDeployment](/powershell/module/az.springcloud/new-azspringcloudappdeployment) cmdlet을 사용 합니다. 다음 예제에서는 앱에 대해 라는 Azure 스프링 클라우드 배포를 만듭니다 `default` `gateway` .

```azurepowershell-interactive
New-AzSpringCloudAppDeployment -ResourceGroupName <resource group name> -Name <service instance name> -AppName gateway -DeploymentName default
```

## <a name="get-an-azure-spring-cloud-service"></a>Azure 스프링 클라우드 서비스 가져오기

Azure 스프링 클라우드 서비스 및 해당 속성을 가져오려면 [AzSpringCloud](/powershell/module/az.springcloud/get-azspringcloud) cmdlet을 사용 합니다. 다음 예제에서는 지정 된 Azure 스프링 클라우드 서비스에 대 한 정보를 검색 합니다.

```azurepowershell-interactive
Get-AzSpringCloud -ResourceGroupName <resource group name> -ServiceName <service instance name>
```

## <a name="get-an-azure-spring-cloud-app"></a>Azure 스프링 클라우드 앱 가져오기

Azure 스프링 클라우드 앱 및 해당 속성을 가져오려면 [AzSpringCloudApp](/powershell/module/az.springcloud/get-azspringcloudapp) cmdlet을 사용 합니다. 다음 예제에서는 스프링 클라우드 앱에 대 한 정보를 검색 합니다 `gateway` .

```azurepowershell-interactive
Get-AzSpringCloudApp -ResourceGroupName <resource group name> -ServiceName <service instance name> -AppName gateway
```

## <a name="get-an-azure-spring-cloud-deployment"></a>Azure 스프링 클라우드 배포 가져오기

Azure 스프링 클라우드 배포 및 해당 속성을 가져오려면 [AzSpringCloudAppDeployment](/powershell/module/az.springcloud/get-azspringcloudappdeployment) cmdlet을 사용 합니다. 다음 예제에서는 스프링 클라우드 배포에 대 한 정보를 검색 합니다 `default` .

```azurepowershell-interactive
Get-AzSpringCloudAppDeployment -ResourceGroupName <resource group name> -ServiceName <service instance name> -AppName gateway -DeploymentName default
```

## <a name="clean-up-resources"></a>리소스 정리

이 문서에서 만든 리소스가 필요하지 않은 경우 다음 예제를 실행하여 삭제할 수 있습니다.

### <a name="delete-an-azure-spring-cloud-deployment"></a>Azure 스프링 클라우드 배포 삭제

Azure 스프링 클라우드 배포를 제거 하려면 [AzSpringCloudAppDeployment](/powershell/module/az.springcloud/remove-azspringcloudappdeployment) cmdlet을 사용 합니다. 다음 예제에서는 `default` 지정 된 서비스 및 앱에 대해 라는 Azure 스프링 클라우드 앱 배포를 삭제 합니다.

```azurepowershell-interactive
Remove-AzSpringCloudAppDeployment -ResourceGroupName <resource group name> -ServiceName <service instance name> -AppName gateway -DeploymentName default
```

### <a name="delete-an-azure-spring-cloud-app"></a>Azure 스프링 클라우드 앱 삭제

스프링 클라우드 앱을 제거 하려면 [AzSpringCloudApp](/powershell/module/Az.SpringCloud/remove-azspringcloudapp) cmdlet을 사용 합니다. 다음 예에서는 지정 된 `gateway` 서비스 및 리소스 그룹에서 앱을 삭제 합니다.

```azurepowershell
Remove-AzSpringCloudApp -ResourceGroupName <resource group name> -ServiceName <service instance name> -AppName gateway
```

### <a name="delete-an-azure-spring-cloud-service"></a>Azure 스프링 클라우드 서비스 삭제

Azure 스프링 클라우드 서비스를 제거 하려면 [AzSpringCloud](/powershell/module/Az.SpringCloud/remove-azspringcloud) cmdlet을 사용 합니다. 다음 예에서는 지정 된 Azure 스프링 클라우드 서비스를 삭제 합니다.

```azurepowershell
Remove-AzSpringCloud -ResourceGroupName <resource group name> -ServiceName <service instance name>
```

### <a name="delete-the-resource-group"></a>리소스 그룹 삭제

> [!CAUTION]
> 다음 예제에서는 지정된 리소스 그룹과 해당 그룹에 포함된 모든 리소스를 삭제합니다.
> 지정된 리소스 그룹에 이 문서의 범위에 속하지 않는 리소스가 포함된 경우에도 해당 리소스가 삭제됩니다.

```azurepowershell-interactive
Remove-AzResourceGroup -Name <resource group name>
```

## <a name="next-steps"></a>다음 단계

[Azure 스프링 클라우드 개발자 리소스](spring-cloud-resources.md).
