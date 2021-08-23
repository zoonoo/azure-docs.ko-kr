---
title: Azure PowerShell을 사용하여 Azure Spring Cloud 배포하는 방법
description: Azure PowerShell을 사용하여 Azure Spring Cloud 배포하는 방법
author: karlerickson
ms.author: karler
ms.topic: conceptual
ms.service: spring-cloud
ms.devlang: azurepowershell
ms.date: 11/16/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: f0badb1954cfbd78bc347bf949a6f7ad29bc0177
ms.sourcegitcommit: 7f3ed8b29e63dbe7065afa8597347887a3b866b4
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122529559"
---
# <a name="deploy-azure-spring-cloud-with-azure-powershell"></a>Azure PowerShell을 사용하여 Azure Spring Cloud 배포

이 문서에서는 [Az.SpringCloud](/powershell/module/Az.SpringCloud) PowerShell 모듈을 사용하여 Azure Spring Cloud의 인스턴스를 만들 수 있는 방법을 설명합니다.

## <a name="requirements"></a>요구 사항

* Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.

[!INCLUDE [azure-powershell-requirements-no-header.md](../../includes/azure-powershell-requirements-no-header.md)]

   > [!IMPORTANT]
   > **Az.SpringCloud** PowerShell 모듈은 미리 보기 상태지만 `Install-Module` cmdlet을 사용하여 별도로 설치해야 합니다. 이 PowerShell 모듈이 일반 공급되면 이후 Az PowerShell 모듈 릴리스의 일부가 되며 기본적으로 Azure Cloud Shell 내에서 사용할 수 있습니다.

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

## <a name="provision-a-new-instance-of-azure-spring-cloud"></a>Azure Spring Cloud의 새 인스턴스 프로비저닝

Azure Spring Cloud의 새 인스턴스를 만들려면 [New-AzSpringCloud](/powershell/module/az.springcloud/new-azspringcloud) cmdlet을 사용합니다. 다음 예제에서는 이전에 만든 리소스 그룹에 지정된 이름을 사용하여 Azure Spring Cloud 서비스를 만듭니다.

```azurepowershell-interactive
New-AzSpringCloud -ResourceGroupName <resource group name> -name <service instance name> -Location eastus
```

## <a name="create-a-new-azure-spring-cloud-app"></a>새 Azure Spring Cloud 앱 만들기

새 앱을 만들려면 [New-AzSpringCloudApp](/powershell/module/az.springcloud/new-azspringcloudapp) cmdlet을 사용합니다. 다음 예제에서는 `gateway`라는 Azure Spring Cloud 앱을 만듭니다.

```azurepowershell-interactive
New-AzSpringCloudApp -ResourceGroupName <resource group name> -ServiceName <service instance name> -AppName gateway
```

## <a name="create-a-new-azure-spring-cloud-deployment"></a>새 Azure Spring Cloud 배포 만들기

새 배포를 만들려면 [New-AzSpringCloudAppDeployment](/powershell/module/az.springcloud/new-azspringcloudappdeployment) cmdlet을 사용합니다. 다음 예제에서는 `gateway` 앱에 대한 `default`라는 Azure Spring Cloud 배포를 만듭니다.

```azurepowershell-interactive
New-AzSpringCloudAppDeployment -ResourceGroupName <resource group name> -Name <service instance name> -AppName gateway -DeploymentName default
```

## <a name="get-an-azure-spring-cloud-service"></a>Azure Spring Cloud 서비스 가져오기

Azure Spring Cloud 서비스 및 해당 속성을 가져오려면 [Get-AzSpringCloud](/powershell/module/az.springcloud/get-azspringcloud) cmdlet을 사용합니다. 다음 예제에서는 지정된 Azure Spring Cloud 서비스에 대한 정보를 검색합니다.

```azurepowershell-interactive
Get-AzSpringCloud -ResourceGroupName <resource group name> -ServiceName <service instance name>
```

## <a name="get-an-azure-spring-cloud-app"></a>Azure Spring Cloud 앱 가져오기

Azure Spring Cloud 앱 및 해당 속성을 가져오려면 [Get-AzSpringCloudApp](/powershell/module/az.springcloud/get-azspringcloudapp) cmdlet을 사용합니다. 다음 예에서는 `gateway` Spring Cloud 앱에 대한 정보를 검색합니다.

```azurepowershell-interactive
Get-AzSpringCloudApp -ResourceGroupName <resource group name> -ServiceName <service instance name> -AppName gateway
```

## <a name="get-an-azure-spring-cloud-deployment"></a>Azure Spring Cloud 배포 가져오기

Azure Spring Cloud 배포 및 해당 속성을 가져오려면 [Get-AzSpringCloudAppDeployment](/powershell/module/az.springcloud/get-azspringcloudappdeployment) cmdlet을 사용합니다. 다음 예에서는 `default` Spring Cloud 배포에 대한 정보를 검색합니다.

```azurepowershell-interactive
Get-AzSpringCloudAppDeployment -ResourceGroupName <resource group name> -ServiceName <service instance name> -AppName gateway -DeploymentName default
```

## <a name="clean-up-resources"></a>리소스 정리

이 문서에서 만든 리소스가 필요하지 않은 경우 다음 예제를 실행하여 삭제할 수 있습니다.

### <a name="delete-an-azure-spring-cloud-deployment"></a>Azure Spring Cloud 배포 삭제

Azure Spring Cloud 배포를 제거하려면 [Remove-AzSpringCloudAppDeployment](/powershell/module/az.springcloud/remove-azspringcloudappdeployment) cmdlet을 사용합니다. 다음 예제에서는 지정된 서비스 및 앱에 대한 `default`라는 Azure Spring Cloud 앱 배포를 삭제합니다.

```azurepowershell-interactive
Remove-AzSpringCloudAppDeployment -ResourceGroupName <resource group name> -ServiceName <service instance name> -AppName gateway -DeploymentName default
```

### <a name="delete-an-azure-spring-cloud-app"></a>Azure Spring Cloud 앱 삭제

Spring Cloud 앱을 제거하려면 [Remove-AzSpringCloudApp](/powershell/module/Az.SpringCloud/remove-azspringcloudapp) cmdlet을 사용합니다. 다음 예에서는 지정된 서비스 및 리소스 그룹에서 `gateway` 앱을 삭제합니다.

```azurepowershell
Remove-AzSpringCloudApp -ResourceGroupName <resource group name> -ServiceName <service instance name> -AppName gateway
```

### <a name="delete-an-azure-spring-cloud-service"></a>Azure Spring Cloud 서비스 삭제

Azure Spring Cloud 서비스를 제거하려면 [Remove-AzSpringCloud](/powershell/module/Az.SpringCloud/remove-azspringcloud) cmdlet을 사용합니다. 다음 예에서는 지정된 Azure Spring Cloud 서비스를 삭제합니다.

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

[Azure Spring Cloud 개발자 리소스](./resources.md).
