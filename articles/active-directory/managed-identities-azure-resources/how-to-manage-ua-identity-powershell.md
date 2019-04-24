---
title: Azure PowerShell을 사용하여 사용자 할당 관리 ID를 생성, 나열 및 삭제하는 방법
description: Azure PowerShell을 사용하여 사용자 할당 관리 ID를 만들고 나열하고 삭제하는 방법에 대한 단계별 지침입니다.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/16/2018
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 886b56de194f38fbb4b94f96b92bff11f2288b37
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60293514"
---
# <a name="create-list-or-delete-a-user-assigned-managed-identity-using-azure-powershell"></a>Azure PowerShell을 사용하여 사용자 할당 관리 ID 생성, 나열 또는 삭제

[!INCLUDE [preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

Azure 리소스에 대한 관리 ID는 Azure Active Directory에서 관리 ID를 Azure 서비스에 제공합니다. 이 ID를 사용하면 코드에 자격 증명을 포함할 필요 없이 Azure AD 인증을 지원하는 서비스에 인증할 수 있습니다. 

이 문서에서는 Azure PowerShell을 사용하여 사용자 할당 관리 ID 만들고 나열하고 삭제하는 방법을 알아봅니다.

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>필수 조건

- Azure 리소스에 대한 관리 ID를 잘 모르는 경우 [개요 섹션](overview.md)을 확인하세요. **[시스템 할당 ID와 사용자 할당 관리 ID의 차이점](overview.md#how-does-it-work)을 반드시 검토하세요**.
- 아직 Azure 계정이 없으면 계속하기 전에 [평가판 계정](https://azure.microsoft.com/free/)에 등록해야 합니다.
- 아직 설치하지 않은 경우 [Azure PowerShell 최신 버전](/powershell/azure/install-az-ps)을 설치합니다.
- PowerShell을 로컬로 실행하는 경우 다음이 필요합니다. 
    - `Connect-AzAccount`를 실행하여 Azure와 연결합니다.
    - [PowerShellGet 최신 버전](/powershell/gallery/installing-psget#for-systems-with-powershell-50-or-newer-you-can-install-the-latest-powershellget)을 설치합니다.
    - `Install-Module -Name PowerShellGet -AllowPrerelease` 명령을 실행하여 `PowerShellGet` 모듈의 시험판 버전을 가져옵니다. 이 명령을 실행한 후 `Az.ManagedServiceIdentity` 모듈을 설치하기 위해 현재 PowerShell 세션에서 `Exit`해야 할 수도 있습니다.
    - `Install-Module -Name Az.ManagedServiceIdentity -AllowPrerelease` 명령을 실행하여 `Az.ManagedServiceIdentity` 모듈의 시험판 버전을 설치하고 이 문서의 사용자 할당 관리 ID 작업을 수행합니다.

## <a name="create-a-user-assigned-managed-identity"></a>사용자 할당 관리 ID 만들기

사용자 할당 관리 ID를 만들려면 계정에 [관리 ID 기여자](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) 역할 할당이 필요합니다.

사용자 할당 관리 ID를 만들려면 `New-AzUserAssignedIdentity` 명령을 사용합니다. `ResourceGroupName` 매개 변수는 사용자 할당 관리 ID가 만들어질 리소스 그룹을 지정하고 `-Name` 매개 변수는 그 이름을 지정합니다. `<RESOURCE GROUP>` 및 `<USER ASSIGNED IDENTITY NAME>` 매개 변수 값을 원하는 값으로 바꿉니다.

[!INCLUDE [ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

 ```azurepowershell-interactive
New-AzUserAssignedIdentity -ResourceGroupName <RESOURCEGROUP> -Name <USER ASSIGNED IDENTITY NAME>
```
## <a name="list-user-assigned-managed-identities"></a>사용자 할당 관리 ID 나열

사용자 할당 관리 ID를 나열하려면/읽으려면 계정에 [관리 ID 운영자](/azure/role-based-access-control/built-in-roles#managed-identity-operator) 또는 [관리 ID 기여자](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) 역할 할당이 필요합니다.

사용자 할당 관리 ID를 나열하려면 [Get-AzUserAssigned] 명령을 사용합니다.  `-ResourceGroupName` 매개 변수는 사용자 할당 관리 ID가 만들어지는 리소스 그룹을 지정합니다. `<RESOURCE GROUP>`을 원하는 값으로 바꿉니다.

```azurepowershell-interactive
Get-AzUserAssignedIdentity -ResourceGroupName <RESOURCE GROUP>
```
응답에서 사용자 할당 관리 ID에는 키 `Type`에 대해 반환된 `"Microsoft.ManagedIdentity/userAssignedIdentities"` 값이 있습니다.

`Type :Microsoft.ManagedIdentity/userAssignedIdentities`

## <a name="delete-a-user-assigned-managed-identity"></a>사용자 할당 관리 ID 삭제

사용자 할당 관리 ID를 삭제하려면 계정에 [관리 ID 기여자](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) 역할 할당이 필요합니다.

사용자 할당 관리 ID를 삭제하려면 `Remove-AzUserAssignedIdentity` 명령을 사용합니다.  `-ResourceGroupName` 매개 변수는 사용자 할당 ID가 만들어진 리소스 그룹을 지정하고 `-Name` 매개 변수는 그 이름을 지정합니다. `<RESOURCE GROUP>` 및 `<USER ASSIGNED IDENTITY NAME>` 매개 변수 값을 원하는 값으로 바꿉니다.

 ```azurepowershell-interactive
Remove-AzUserAssignedIdentity -ResourceGroupName <RESOURCE GROUP> -Name <USER ASSIGNED IDENTITY NAME>
```
> [!NOTE]
> 사용자 할당 관리 ID를 삭제해도 할당된 리소스에서 참조는 제거되지 않습니다. ID 할당은 별도로 제거해야 합니다.

## <a name="next-steps"></a>다음 단계

Azure 리소스용 Azure PowerShell 관리 ID 명령의 전체 목록 및 자세한 내용은 [Az.ManagedServiceIdentity](/powershell/module/az.managedserviceidentity#managed_service_identity)를 참조하세요.
