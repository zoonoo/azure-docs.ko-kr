---
title: Azure Blockchain Service 멤버 만들기 - Azure PowerShell
description: Azure PowerShell을 사용하여 블록체인 컨소시엄을 위한 Azure Blockchain Service 멤버를 만듭니다.
ms.date: 9/22/2020
ms.topic: quickstart
ms.reviewer: ravastra
ms.custom: references_regions, devx-track-azurepowershell
ms.openlocfilehash: b57c44e79d599ab41b2c3356ee337811acdf639d
ms.sourcegitcommit: a2d8acc1b0bf4fba90bfed9241b299dc35753ee6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/12/2020
ms.locfileid: "91948360"
---
# <a name="quickstart-create-an-azure-blockchain-service-blockchain-member-using-azure-powershell"></a>빠른 시작: Azure PowerShell을 사용하여 Azure Blockchain Service 블록체인 멤버 만들기

이 빠른 시작에서는 Azure PowerShell을 사용하여 Azure Blockchain Service에 새 블록체인 멤버 및 컨소시엄을 배포합니다.

## <a name="prerequisites"></a>사전 요구 사항

Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.

PowerShell을 로컬로 사용하도록 선택하는 경우 이 문서에서는 Az PowerShell 모듈을 설치하고 [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) cmdlet을 사용하여 Azure 계정에 연결해야 합니다. Az PowerShell 모듈을 설치하는 방법에 대한 자세한 내용은 [Azure PowerShell 설치](/powershell/azure/install-az-ps)를 참조하세요.

> [!IMPORTANT]
> **Az.Blockchain** PowerShell 모듈은 현재 미리 보기로 제공되며, Az PowerShell 모듈에서 `Install-Module` cmdlet을 사용하여 별도로 설치해야 합니다. 이 PowerShell 모듈이 일반 공급되면 이후 Az PowerShell 모듈 릴리스의 기능으로 포함되어 Azure Cloud Shell 내에서 기본적으로 사용할 수 있습니다.

```azurepowershell-interactive
Install-Module -Name Az.Blockchain
```

[!INCLUDE [cloud-shell-try-it](../../../includes/cloud-shell-try-it.md)]

## <a name="register-resource-provider"></a>리소스 공급자 등록

Azure Blockchain 서비스를 처음 사용하는 경우 **Microsoft.Blockchain** 리소스 공급자를 등록해야 합니다.

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.Blockchain
```

## <a name="choose-a-specific-azure-subscription"></a>특정 Azure 구독 선택

여러 Azure 구독이 있는 경우 리소스에 대한 요금이 청구되는 적절한 구독을 선택합니다. [Set-AzContext](/powershell/module/az.accounts/set-azcontext) cmdlet을 사용하여 특정 구독을 선택합니다.

```azurepowershell-interactive
Set-AzContext -SubscriptionId 00000000-0000-0000-0000-000000000000
```

## <a name="define-variables"></a>변수 정의

여러 가지 정보를 반복해서 사용하게 됩니다. 정보를 저장할 변수를 만듭니다.

```azurepowershell-interactive
# Name of resource group used throughout this article
$resourceGroupName = 'myResourceGroup'

# Azure region
$location = 'eastus'
```

## <a name="create-a-resource-group"></a>리소스 그룹 만들기

[New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) cmdlet을 사용하여 [Azure 리소스 그룹](../../azure-resource-manager/management/overview.md)을 만듭니다. 리소스 그룹은 Azure 리소스가 그룹으로 배포되고 관리되는 논리 컨테이너입니다.

다음 예제에서는 `$location` 변수에 지정된 지역의 `$resourceGroupName` 변수 이름을 기반으로 리소스 그룹을 만듭니다.

```azurepowershell-interactive
New-AzResourceGroup -Name $resourceGroupName -Location $location
```

## <a name="create-a-blockchain-member"></a>블록체인 멤버 만들기

Azure Blockchain Service 멤버는 프라이빗 컨소시엄 블록체인 네트워크의 블록체인 노드입니다.
멤버를 프로비저닝할 때 컨소시엄 네트워크를 만들거나 조인할 수 있습니다. 컨소시엄 네트워크에 멤버가 하나 이상이 필요합니다. 참가자가 필요로 하는 블록체인 멤버 수는 시나리오에 따라 달라집니다. 컨소시엄 참가자는 하나 이상의 블록체인 멤버를 보유하거나 다른 참가자와 멤버를 공유할 수 있습니다. 컨소시엄에 대한 자세한 내용은 [Azure Blockchain Service 컨소시엄](consortium.md)을 참조하세요.

여러 매개 변수와 속성을 전달해야 합니다. 예제 매개 변수를 원하는 값으로 바꿉니다.

```azurepowershell-interactive
$passwd = Read-Host -Prompt 'Enter the members default transaction node password' -AsSecureString
$csPasswd = Read-Host -Prompt 'Enter  the consortium account password' -AsSecureString

$memberParams = @{
  Name = 'myblockchainmember'
  ResourceGroupName = $resourceGroupName
  Consortium = 'myconsortium'
  ConsortiumManagementAccountPassword = $csPasswd
  Location = $location
  Password = $passwd
  Protocol = 'Quorum'
  Sku = 'S0'
}
New-AzBlockchainMember @memberParams
```

| 매개 변수 | Description |
|---------|-------------|
| **ResourceGroupName** | Azure Blockchain Service 리소스가 만들어지는 리소스 그룹 이름입니다. 이전 섹션에서 만든 리소스 그룹을 사용합니다.
| **이름** | Azure Blockchain Service 블록체인 멤버를 식별하는 고유한 이름입니다. 이 이름은 공용 엔드포인트 주소에 사용됩니다. 예들 들어 `myblockchainmember.blockchain.azure.com`입니다.
| **위치** | 블록체인 멤버가 만들어지는 Azure 지역입니다. `westus2`)을 입력합니다. 사용자 또는 다른 Azure 애플리케이션에 가장 가까운 위치를 선택합니다. 기능은 일부 지역에서 사용하지 못할 수도 있습니다. Azure Blockchain Data Manager는 다음 Azure 지역에서 사용할 수 있습니다. 즉 미국 동부 및 서유럽에서 실행되는 기본 웹 사이트의 두 인스턴스
| **암호** | 멤버의 기본 트랜잭션 노드에 대한 암호입니다. 블록체인 멤버의 기본 트랜잭션 노드 공용 엔드포인트에 연결할 때 기본 인증용 암호를 사용합니다.
| **프로토콜** | Blockchain 프로토콜입니다. 현재 _Quorum_ 프로토콜이 지원됩니다.
| **컨소시엄** | 참가할 또는 만들 컨소시엄의 이름입니다. 컨소시엄에 대한 자세한 내용은 [Azure Blockchain Service 컨소시엄](consortium.md)을 참조하세요.
| **ConsortiumManagementAccountPassword** | 컨소시엄 계정 암호는 멤버 계정 암호라고도 합니다. 멤버 계정 암호는 멤버용으로 생성된 Ethereum 계정의 프라이빗 키를 암호화하는 데 사용됩니다. 컨소시엄 관리용 멤버 계정과 멤버 계정 암호를 사용합니다.
| **Sku** | 계층 유형입니다. 표준의 경우 **S0**이고, 기본의 경우 **B0**입니다. 개발, 테스트 및 개념 증명에 _기본_ 계층을 사용합니다. 프로덕션 등급 배포에 _표준_ 계층을 사용합니다. Blockchain Data Manager를 사용하거나 대량의 프라이빗 트랜잭션을 전송하는 경우 _표준_ 계층도 사용해야 합니다. 멤버를 만든 후 기본과 표준 간의 가격 책정 계층 변경은 지원되지 않습니다.

블록체인 멤버 및 지원 리소스를 만드는 데 약 10분이 걸립니다.

## <a name="clean-up-resources"></a>리소스 정리

앞에서 만든 블록체인 멤버를 다음 빠른 시작 또는 자습서에 사용할 수 있습니다. 더 이상 필요 없는 경우 빠른 시작을 위해 만든 `myResourceGroup` 리소스 그룹을 삭제하여 리소스를 삭제할 수 있습니다.

> [!CAUTION]
> 다음 예제에서는 지정된 리소스 그룹과 해당 그룹에 포함된 모든 리소스를 삭제합니다.
> 지정된 리소스 그룹에 이 문서의 범위에 속하지 않는 리소스가 포함된 경우에도 해당 리소스가 삭제됩니다.

```azurepowershell-interactive
Remove-AzResourceGroup -Name $resourceGroupName
```

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 Azure Blockchain Service 멤버 및 새로운 컨소시엄을 배포했습니다. 다음 빠른 시작을 통해 Ethereum용 Azure Blockchain Development Kit를 사용하여 Azure Blockchain Service 멤버에 연결합니다.

> [!div class="nextstepaction"]
> [Visual Studio Code를 사용하여 Azure Blockchain Service에 연결](connect-vscode.md)