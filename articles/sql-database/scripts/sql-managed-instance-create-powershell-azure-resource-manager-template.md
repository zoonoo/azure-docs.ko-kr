---
title: PowerShell 예제 - Azure SQL Database에서 Managed Instance 만들기 | Microsoft Docs
description: Azure SQL Database에서 Managd Instance를 만드는 Azure PowerShell 예제 스크립트
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: PowerShell
ms.topic: sample
author: jovanpop-msft
ms.author: jovanpop-msft
ms.reviewer: sstein
manager: craigg
ms.date: 03/12/2019
ms.openlocfilehash: 2d5ecb3035aad657485916a4c472f6f4dc9eb530
ms.sourcegitcommit: 1aefdf876c95bf6c07b12eb8c5fab98e92948000
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/06/2019
ms.locfileid: "66729147"
---
# <a name="use-powershell-with-azure-resource-manager-template-to-create-a-managed-instance-in-azure-sql-database"></a>Azure Resource Manager 템플릿과 함께 PowerShell을 사용하여 Azure SQL Database에서 Managed Instance 만들기

Azure PowerShell 라이브러리 및 Azure Resource Manager 템플릿을 사용하여 Azure SQL Database Managed Instance를 만들 수 있습니다.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

PowerShell을 로컬로 설치하고 사용하도록 선택하는 경우 이 자습서에는 AZ PowerShell 1.4.0 이상이 필요합니다. 업그레이드해야 하는 경우 [Azure PowerShell 모듈 설치](/powershell/azure/install-az-ps)를 참조하세요. 또한 PowerShell을 로컬로 실행하는 경우 `Connect-AzAccount`를 실행하여 Azure와 연결해야 합니다.

Azure PowerShell 명령은 미리 정의된 Azure Resource Manager 템플릿을 사용하여 배포를 시작할 수 있습니다. 템플릿에서 다음 속성을 지정할 수 있습니다.

- 인스턴스 이름
- SQL 관리자 사용자 이름 및 암호
- 인스턴스의 크기(코어 수 및 최대 스토리지 크기)
- 인스턴스가 배치될 VNet 및 서브넷입니다.
- 인스턴스의 서버 수준 데이터 정렬(미리 보기)

인스턴스 이름, SQL 관리자 사용자 이름, VNet/서브넷 및 데이터 정렬을 나중에 변경할 수 없습니다. 다른 인스턴스 속성을 변경할 수 있습니다.

## <a name="prerequisites"></a>필수 조건

이 샘플에서는 Managed Instance에 대한 [유효한 네트워크 환경을 만들](../sql-database-managed-instance-create-vnet-subnet.md)거나 [기존 VNet을 수정](../sql-database-managed-instance-configure-vnet-subnet.md)했다고 가정합니다. 샘플에서는 cmdlet [New-AzResourceGroupDeployment](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroupdeployment) 및 [Get-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/get-azvirtualnetwork)를 사용하므로 다음 PowerShell 모듈을 설치해야 합니다.

```powershell
Install-Module Az.Network
Install-Module Az.Resources
```

## <a name="azure-resource-manager-template"></a>Azure Resource Manager 템플릿

다음 내용을 인스턴스를 만드는 데 사용할 템플릿을 표시하는 파일에 배치해야 합니다.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "1.0.0.1",
    "parameters": {
        "instance": {
            "type": "string"
        },
        "user": {
            "type": "string"
        },
        "pwd": {
            "type": "securestring"
        },
        "subnetId": {
            "type": "string"
        }
    },
    "resources": [
        {
            "name": "[parameters('instance')]",
            "location": "West Central US",
            "tags": {
                "Description":"GP Instance with custom instance collation - Serbian_Cyrillic_100_CS_AS"
            },
            "sku": {
                "name": "GP_Gen4",
                "tier": "GeneralPurpose"
            },
            "properties": {
                "administratorLogin": "[parameters('user')]",
                "administratorLoginPassword": "[parameters('pwd')]",
                "subnetId": "[parameters('subnetId')]",
                "storageSizeInGB": 256,
                "vCores": 8,
                "licenseType": "LicenseIncluded",
                "hardwareFamily": "Gen4",
                "collation": "Serbian_Cyrillic_100_CS_AS"
            },
            "type": "Microsoft.Sql/managedInstances",
            "identity": {
                "type": "SystemAssigned"
            },
            "apiVersion": "2015-05-01-preview"
        }
    ]
}
```

Azure VNet에 적절히 구성된 서브넷이 이미 있다고 가정합니다. 적절히 구성된 서브넷이 없는 경우 독립적으로 실행하거나 이 템플릿에 포함될 수 있는 별도의 [Azure Resource Managed 템플릿](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-managed-instance-azure-environment)을 사용하여 네트워크 환경을 준비합니다.

이 파일의 내용을 .json 파일로 저장하고, 다음 PowerShell 스크립트에 파일 경로를 배치하고, 스크립트에서 개체의 이름을 변경합니다.

```powershell
$subscriptionId = "ed827499-xxxx-xxxx-xxxx-xxxxxxxxxx"
Select-AzSubscription -SubscriptionId $subscriptionId

# Managed Instance properties
$resourceGroup = "rg_mi"
$location = "West Central US"
$name = "managed-instance-name"
$user = "miSqlAdmin"
$secpasswd = ConvertTo-SecureString "<Put some strong password here>" -AsPlainText -Force

# Network configuration
$vNetName = "my_vnet"
$vNetResourceGroup = "rg_mi_vnet"
$subnetName = "ManagedInstances"
$vNet = Get-AzVirtualNetwork -Name $vNetName -ResourceGroupName $vNetResourceGroup
$subnet = Get-AzVirtualNetworkSubnetConfig -Name $SubnetName -VirtualNetwork $vNet
$subnetId = $subnet.Id

# Deploy Instance using Azure Resource Manager template:
New-AzResourceGroupDeployment  -Name MyDeployment -ResourceGroupName $resourceGroup  `
                                    -TemplateFile 'C:\...\create-managed-instance.json' `
                                    -instance $name -user $user -pwd $secpasswd -subnetId $subnetId
```

스크립트가 성공적으로 실행되면 모든 Azure 서비스 및 구성된 IP 주소에서 SQL Database에 액세스할 수 있습니다.

## <a name="next-steps"></a>다음 단계

Azure PowerShell에 대한 자세한 내용은 [Azure PowerShell 설명서](/powershell/azure/overview)를 참조하세요.

추가 SQL Database PowerShell 스크립트 샘플은 [Azure SQL Database PowerShell 스크립트](../sql-database-powershell-samples.md)에 있습니다.
