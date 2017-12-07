---
title: "PowerShell을 사용하여 Azure 환경에서 규정 비준수 리소스를 식별하는 정책 할당 만들기 | Microsoft Docs"
description: "PowerShell을 사용하여 규정 비준수 리소스를 식별하는 Azure Policy 할당을 만듭니다."
services: azure-policy
keywords: 
author: bandersmsft
ms.author: banders
ms.date: 12/06/2017
ms.topic: quickstart
ms.service: azure-policy
ms.custom: mvc
ms.openlocfilehash: 6a9b7cff1341bd898b76a226ca413b8135eec408
ms.sourcegitcommit: cc03e42cffdec775515f489fa8e02edd35fd83dc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/07/2017
---
# <a name="create-a-policy-assignment-to-identify-non-compliant-resources-in-your-azure-environment-using-powershell"></a>PowerShell을 사용하여 Azure 환경에서 규정 비준수 리소스를 식별하는 정책 할당 만들기

Azure의 규정 준수를 이해하기 위한 첫 번째 단계는 보유한 리소스의 현재 위치를 파악하는 것입니다. 이 빠른 시작은 Managed Disks를 사용하지 않는 가상 컴퓨터를 식별하는 정책 할당 만들기 과정을 단계별로 안내합니다.

이 프로세스가 끝나면 Managed Disks를 사용하지 않는 *비규격* 가상 컴퓨터가 식별됩니다.


명령줄 또는 스크립트에서 Azure 리소스를 만들고 관리하는 데 PowerShell이 사용됩니다. 이 가이드에서는 PowerShell을 사용하여 Azure 환경에서 규정 비준수 리소스를 식별하는 정책 할당을 만드는 방법에 대해 자세히 설명합니다.

이 가이드에는 Azure PowerShell 모듈 버전 4.0 이상이 필요합니다.  ```Get-Module -ListAvailable AzureRM``` 을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure PowerShell 모듈 설치](/powershell/azure/install-azurerm-ps)를 참조하세요.

시작하기 전에, 최신 버전의 PowerShell을 설치했는지 확인합니다. 자세한 내용은 [Azure PowerShell을 설치 및 구성하는 방법](/powershell/azureps-cmdlets-docs)을 참조하세요.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.


## <a name="create-a-policy-assignment"></a>정책 할당 만들기

이 빠른 시작에서 정책 할당을 만들고 *Managed Disks가 없는 가상 컴퓨터 감사* 정의를 할당합니다. 이 정책 정의는 정책 정의에 설정한 조건을 따르지 않는 리소스를 식별합니다.

다음 단계에 따라 새 정책 할당을 만듭니다.

다음 명령을 실행하여 모든 정책 정의를 보고 할당하려는 정책 정의를 찾습니다.

```powershell
$definition = Get-AzureRmPolicyDefinition
```

Azure Policy에는 사용 가능한 기본 제공 정책 정의가 이미 제공되고 있습니다. 기본 제공 정책 정의는 다음과 같습니다.

- 태그 및 해당 값 강제 적용
- 태그 및 해당 값 적용
- SQL Server 버전 12.0 필요

다음으로 `New-AzureRmPolicyAssignment` cmdlet을 사용하여 정책 정의를 원하는 범위에 적용합니다.

이 자습서에서는 다음 정보를 명령에 제공합니다.
- 정책 할당에 대한 표시 **이름** - 이 경우 Managed Disks가 없는 가상 컴퓨터 감사를 사용합니다.
- **정책** – 할당을 만드는 데 사용되는 정책 정의입니다. 이 경우 정책 정의는 *Managed Disks가 없는 가상 컴퓨터 감사*입니다.
- **범위** - 범위는 정책 할당이 적용되는 리소스 또는 리소스 그룹을 결정합니다. 구독에서 리소스 그룹까지 다양한 범위가 있습니다. 이 예제에서는 **FabrikamOMS** 리소스 그룹에 정책 정의를 할당합니다.
- **$definition** - 정책 정의의 리소스 ID를 제공해야 합니다. 여기서는 *Managed Disks가 없는 가상 컴퓨터 감사* 정책 정의에 대한 ID를 사용합니다.

```powershell
$rg = Get-AzureRmResourceGroup -Name "FabrikamOMS"
$definition = Get-AzureRmPolicyDefinition -Id /providers/Microsoft.Authorization/policyDefinitions/e5662a6-4747-49cd-b67b-bf8b01975c4c
New-AzureRMPolicyAssignment -Name Audit Virtual Machines without Managed Disks Assignment -Scope $rg.ResourceId -PolicyDefinition $definition
```

이제 규정 비준수 리소스를 식별하여 환경의 준수 상태를 파악할 준비가 되었습니다.

## <a name="identify-non-compliant-resources"></a>규정 비준수 리소스 식별

1. Azure Policy 방문 페이지로 다시 이동합니다.
2. 왼쪽 창에서 **준수**를 선택하고 방금 만든 **정책 할당**을 검색합니다.

   ![정책 준수](media/assign-policy-definition/policy-compliance.png)

   이 새 할당을 준수하지 않는 기존 리소스가 있는 경우 위와 같이 **규정 비준수 리소스** 탭에 표시됩니다.

## <a name="clean-up-resources"></a>리소스 정리

이 컬렉션의 다른 가이드는 이 빠른 시작에 기반하여 작성되었습니다. 후속 자습서를 계속 사용하려면 이 빠른 시작에서 만든 리소스를 정리하지 마세요. 계속하지 않으려면 다음 명령을 실행하여 만든 할당을 삭제합니다.

```powershell
Remove-AzureRmPolicyAssignment -Name “Audit Virtual Machines without Managed Disks Assignment” -Scope /subscriptions/ bc75htn-a0fhsi-349b-56gh-4fghti-f84852/resourceGroups/FabrikamOMS
```

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 정책 정의를 할당하여 Azure 환경에서 규정 비준수 리소스를 식별했습니다.

정책 할당에 대해 자세히 알아보고 **나중에** 만들 리소스에서 정책을 준수하도록 하려면 다음 자습서로 계속 진행하세요.

> [!div class="nextstepaction"]
> [정책 만들기 및 관리](./create-manage-policy.md)
