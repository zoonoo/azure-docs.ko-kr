---
title: 인스턴스 및 인증 설정 (PowerShell)
titleSuffix: Azure Digital Twins
description: Azure PowerShell를 사용 하 여 Azure Digital Twins 서비스 인스턴스를 설정 하는 방법을 참조 하세요.
author: baanders
ms.author: baanders
ms.date: 12/16/2020
ms.topic: how-to
ms.service: digital-twins
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 024b238ef9a6330831ae6cf4dcd6bb72d72dcc74
ms.sourcegitcommit: 8dd8d2caeb38236f79fe5bfc6909cb1a8b609f4a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/08/2021
ms.locfileid: "98044274"
---
# <a name="set-up-an-azure-digital-twins-instance-and-authentication-powershell"></a>Azure Digital Twins 인스턴스 및 인증 설정 (PowerShell)

[!INCLUDE [digital-twins-setup-selector.md](../../includes/digital-twins-setup-selector.md)]

이 문서에서는 인스턴스를 만들고 인증을 설정 하는 등 **새 Azure Digital Twins 인스턴스를 설정** 하는 단계에 대해 설명 합니다. 이 문서를 완료 한 후에는에 대 한 프로그래밍을 시작할 준비가 된 Azure Digital Twins 인스턴스를 사용할 수 있습니다.

이 문서의 버전은 [Azure PowerShell](/powershell/azure/new-azureps-module-az)를 사용 하 여 이러한 단계를 하나씩 수동으로 진행 합니다.

* Azure Portal를 사용 하 여 이러한 단계를 수동으로 진행 하려면이 문서의 포털 버전 [*(방법: 인스턴스 및 인증 설정 (포털))*](how-to-set-up-instance-portal.md)을 참조 하세요.
* 배포 스크립트 샘플을 사용 하 여 자동화 된 설치를 실행 하려면이 문서의 스크립팅된 버전 [*(방법: 인스턴스 및 인증 설정 (스크립팅된))*](how-to-set-up-instance-scripted.md)을 참조 하세요.

[!INCLUDE [digital-twins-setup-steps.md](../../includes/digital-twins-setup-steps.md)]
[!INCLUDE [digital-twins-setup-permissions.md](../../includes/digital-twins-setup-permissions.md)]

## <a name="prepare-your-environment"></a>환경 준비

1. 먼저이 문서의 명령을 실행할 위치를 선택 합니다. Azure PowerShell의 로컬 설치를 사용 하거나 [Azure Cloud Shell](https://shell.azure.com)를 사용 하 여 브라우저 창에서 Azure PowerShell 명령을 실행 하도록 선택할 수 있습니다.
    * Azure PowerShell을 로컬로 사용하도록 선택하는 경우:
       1. [Az PowerShell 모듈을 설치합니다](/powershell/azure/install-az-ps).
       1. 컴퓨터에서 PowerShell 창을 엽니다.
       1. [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) cmdlet을 사용하여 Azure 계정에 로그인합니다.
    * Azure Cloud Shell을 사용하도록 선택하는 경우:
        1. Cloud Shell에 대 한 자세한 내용은 [Azure Cloud Shell 개요](../cloud-shell/overview.md) 를 참조 하세요.
        1. 브라우저에서 [이 링크](https://shell.azure.com) 를 따라 Cloud Shell 창을 엽니다.
        1. Cloud Shell 아이콘 모음에서 Cloud Shell PowerShell 버전을 실행 하도록 설정 되었는지 확인 합니다.
    
            :::image type="content" source="media/how-to-set-up-instance/cloud-shell/cloud-shell-powershell.png" alt-text="PowerShell 버전 선택을 보여 주는 Cloud Shell 창":::
    
1. 여러 Azure 구독이 있는 경우 리소스에 대한 요금이 청구되는 적절한 구독을 선택합니다. [Set-AzContext](/powershell/module/az.accounts/set-azcontext) cmdlet을 사용하여 특정 구독을 선택합니다.

   ```azurepowershell-interactive
   Set-AzContext -SubscriptionId 00000000-0000-0000-0000-000000000000
   ```

1. 이 구독에서 Azure Digital Twins를 처음 사용 하는 경우 **DigitalTwins** 리소스 공급자를 등록 해야 합니다. (확실하지 않으면 이전에 수행한 적이 있더라도 다시 실행해도 됩니다.)

   ```azurepowershell-interactive
   Register-AzResourceProvider -ProviderNamespace Microsoft.DigitalTwins
   ```

1. 다음 명령을 사용 하 여 **DigitalTwins** PowerShell 모듈을 설치 합니다.
    ```azurepowershell-interactive
    Install-Module -Name Az.DigitalTwins
    ```

> [!IMPORTANT]
> **DigitalTwins** PowerShell 모듈은 미리 보기 상태 이지만 `Install-Module` 위에서 설명한 대로 cmdlet을 사용 하 여 별도로 설치 해야 합니다. 이 PowerShell 모듈이 일반 공급되면 이후 Az PowerShell 모듈 릴리스의 일부가 되며 기본적으로 Azure Cloud Shell 내에서 사용할 수 있습니다.

## <a name="create-the-azure-digital-twins-instance"></a>Azure Digital Twins 인스턴스 만들기

이 섹션에서는 Azure PowerShell를 사용 하 여 **Azure Digital Twins의 새 인스턴스를 만듭니다** .
다음을 제공 해야 합니다.

* 인스턴스가 배포 될 [Azure 리소스 그룹](../azure-resource-manager/management/overview.md) 입니다. 기존 리소스 그룹이 아직 없는 경우 [AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) cmdlet을 사용 하 여 만들 수 있습니다.

  ```azurepowershell-interactive
  New-AzResourceGroup -Name <name-for-your-resource-group> -Location <region>
  ```

* 배포에 대 한 지역입니다. Azure Digital Twins를 지 원하는 지역을 확인 하려면 [*지역별 제공 되는 azure 제품*](https://azure.microsoft.com/global-infrastructure/services/?products=digital-twins)을 방문 하세요.
* 인스턴스의 이름입니다. 새 인스턴스의 이름은 구독에 대 한 지역 내에서 고유 해야 합니다. 구독에 이미 지정 된 이름을 사용 하 고 있는 지역에 다른 Azure 디지털 Twins 인스턴스가 있는 경우 다른 이름을 선택 하 라는 메시지가 표시 됩니다.

다음 명령에서 값을 사용 하 여 인스턴스를 만듭니다.

```azurepowershell-interactive
New-AzDigitalTwinsInstance -ResourceGroupName <your-resource-group> -ResourceName <name-for-your-Azure-Digital-Twins-instance> -Location <region>
```

### <a name="verify-success-and-collect-important-values"></a>성공 확인 및 중요 한 값 수집

인스턴스가 성공적으로 만들어진 경우 결과는 사용자가 만든 리소스에 대 한 정보를 포함 하는 다음 출력과 유사 합니다.

```Output
Location   Name                                         Type
--------   ----                                         ----
<region>   <name-for-your-Azure-Digital-Twins-instance> Microsoft.DigitalTwins/digitalTwinsInstances
```

그런 다음를 실행 하 여 새 인스턴스의 속성을 표시 하 `Get-AzDigitalTwinsInstance` 고 다음과 같이를 파이프 합니다 `Select-Object -Property *` .

```azurepowershell-interactive
Get-AzDigitalTwinsInstance -ResourceGroupName <your-resource-group> -ResourceName <name-for-your-Azure-Digital-Twins-instance> |
  Select-Object -Property *
```

> [!TIP]
> 이 명령을 사용 하 여 언제 든 지 인스턴스의 모든 속성을 볼 수 있습니다.

Azure Digital Twins 인스턴스의 **호스트** **이름, 이름** 및 **ResourceGroup** 을 확인 합니다. 이러한 값은 Azure Digital Twins 인스턴스를 계속 사용 하 여 인증을 설정 하 고 관련 Azure 리소스를 설정 하는 데 필요할 수 있는 중요 한 값입니다. 다른 사용자가 인스턴스에 대 한 프로그래밍을 수행 하는 경우 이러한 값을 공유 해야 합니다.

이제 Azure Digital Twins 인스턴스를 사용할 준비가 되었습니다. 그런 다음 적절 한 Azure 사용자에 게 관리 권한을 부여 합니다.

## <a name="set-up-user-access-permissions"></a>사용자 액세스 권한 설정

[!INCLUDE [digital-twins-setup-role-assignment.md](../../includes/digital-twins-setup-role-assignment.md)]

먼저 역할을 할당 해야 하는 사용자의 Azure AD 계정에 대 한 **ObjectId** 를 확인 합니다. 이 값은 [AzAdUser](/powershell/module/az.resources/get-azaduser) cmdlet을 사용 하 여 찾을 수 있습니다 .이 Cmdlet은 Azure AD 계정의 사용자 계정 이름을 전달 하 여 ObjectId (및 기타 사용자 정보)를 검색 합니다. 대부분의 경우 사용자 계정 이름은 Azure AD 계정의 사용자 전자 메일과 일치 합니다.

```azurepowershell-interactive
Get-AzADUser -UserPrincipalName <Azure-AD-user-principal-name-of-user-to-assign>
```

다음으로 다음 명령의 **ObjectId** 를 사용 하 여 역할을 할당 합니다. 또한이 명령을 사용 하려면 인스턴스를 만들 때 이전에 선택한 것과 동일한 구독 ID, 리소스 그룹 이름 및 Azure Digital Twins 인스턴스 이름을 입력 해야 합니다. 명령은 Azure 구독에서 [충분 한 권한이](#prerequisites-permission-requirements) 있는 사용자가 실행 해야 합니다.

```azurepowershell-interactive
$Params = @{
  ObjectId = '<Azure-AD-user-object-id-of-user-to-assign>'
  RoleDefinitionName = 'Azure Digital Twins Data Owner'
  Scope = '/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.DigitalTwins/digitalTwinsInstances/<name-for-your-Azure-Digital-Twins-instance>'
}
New-AzRoleAssignment @Params
```

이 명령의 결과는 생성 된 역할 할당에 대 한 출력 된 정보입니다.

### <a name="verify-success"></a>성공 확인

[!INCLUDE [digital-twins-setup-verify-role-assignment.md](../../includes/digital-twins-setup-verify-role-assignment.md)]

이제 Azure Digital Twins 인스턴스를 사용할 준비가 되 고 관리 권한이 할당 됩니다.

## <a name="next-steps"></a>다음 단계

인증 코드를 사용 하 여 인스턴스에 클라이언트 응용 프로그램을 연결 하는 방법을 참조 하세요.
* [*방법: 앱 인증 코드 작성*](how-to-authenticate-client.md)
