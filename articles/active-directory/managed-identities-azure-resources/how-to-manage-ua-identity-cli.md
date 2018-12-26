---
title: Azure CLI를 사용하여 사용자가 할당한 관리 ID를 관리하는 방법
description: Azure CLI를 사용하여 사용자 할당 관리 ID를 만들고 나열하고 삭제하는 방법에 대한 단계별 지침입니다.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/16/2018
ms.author: daveba
ms.openlocfilehash: d55eab128c8ad295b09ee4fe54f803330a1cb1b4
ms.sourcegitcommit: d372d75558fc7be78b1a4b42b4245f40f213018c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/09/2018
ms.locfileid: "51298805"
---
# <a name="create-list-or-delete-a-user-assigned-managed-identity-using-the-azure-cli"></a>Azure CLI를 사용하여 사용자 할당 관리 ID 생성, 나열 또는 삭제

[!INCLUDE [preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

Azure 리소스에 대한 관리 ID는 Azure Active Directory에서 관리 ID를 Azure 서비스에 제공합니다. 이 ID를 사용하면 코드에 자격 증명을 포함할 필요 없이 Azure AD 인증을 지원하는 서비스에 인증할 수 있습니다. 

이 문서에서는 Azure CLI를 사용하여 사용자 할당 관리 ID 만들고 나열하고 삭제하는 방법을 알아봅니다.

## <a name="prerequisites"></a>필수 조건

- Azure 리소스에 대한 관리 ID를 잘 모르는 경우 [개요 섹션](overview.md)을 확인하세요. **[시스템 할당 ID와 사용자 할당 관리 ID의 차이점](overview.md#how-does-it-work)을 반드시 검토하세요**.
- 아직 Azure 계정이 없으면 계속하기 전에 [평가판 계정](https://azure.microsoft.com/free/)에 등록해야 합니다.
- CLI 스크립트 예제는 다음의 세 가지 옵션 중 하나로 실행할 수 있습니다.
    - Azure Portal에서 [Azure Cloud Shell](../../cloud-shell/overview.md)을 사용합니다(다음 섹션 참조).
    - 각 코드 블록의 오른쪽 위에 있는 "사용해 보세요." 단추를 통해 포함된 Azure Cloud Shell을 사용합니다.
    - 로컬 CLI 콘솔을 사용하려는 경우 [Azure CLI의 최신 버전을 설치](https://docs.microsoft.com/cli/azure/install-azure-cli)합니다(2.0.13 이상). 사용자 할당 관리 ID를 배포하려는 Azure 구독과 연결된 계정으로 `az login`을 사용하여 Azure에 로그인합니다.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-a-user-assigned-managed-identity"></a>사용자 할당 관리 ID 만들기 

사용자 할당 관리 ID를 만들려면 계정에 [관리 ID 기여자](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) 역할 할당이 필요합니다.

사용자 할당 관리 ID를 만들려면 [az identity create](/cli/azure/identity#az-identity-create) 명령을 사용합니다. `-g` 매개 변수는 사용자 할당 관리 ID가 만들어질 리소스 그룹을 지정하고 `-n` 매개 변수는 그 이름을 지정합니다. `<RESOURCE GROUP>` 및 `<USER ASSIGNED IDENTITY NAME>` 매개 변수 값을 원하는 값으로 바꿉니다.

[!INCLUDE [ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

 ```azurecli-interactive
az identity create -g <RESOURCE GROUP> -n <USER ASSIGNED IDENTITY NAME>
```
## <a name="list-user-assigned-managed-identities"></a>사용자 할당 관리 ID 나열

사용자 할당 관리 ID를 나열하려면/읽으려면 계정에 [관리 ID 운영자](/azure/role-based-access-control/built-in-roles#managed-identity-operator) 또는 [관리 ID 기여자](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) 역할 할당이 필요합니다.

사용자 할당 관리 ID를 나열하려면 [az identity list](/cli/azure/identity#az-identity-list) 명령을 사용합니다. `<RESOURCE GROUP>`을 원하는 값으로 바꿉니다.

```azurecli-interactive
az identity list -g <RESOURCE GROUP>
```
JSON 응답에서 사용자 할당 관리 ID에는 키 `type`에 대해 반환된 `"Microsoft.ManagedIdentity/userAssignedIdentities"` 값이 있습니다.

`"type": "Microsoft.ManagedIdentity/userAssignedIdentities"`

## <a name="delete-a-user-assigned-managed-identity"></a>사용자 할당 관리 ID 삭제

사용자 할당 관리 ID를 삭제하려면 계정에 [관리 ID 기여자](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) 역할 할당이 필요합니다.

사용자 할당 관리 ID를 삭제하려면 [az identity delete](/cli/azure/identity#az-identity-delete) 명령을 사용합니다.  -n 매개 변수는 그 이름을 지정하고 -g 매개 변수는 사용자 할당 관리 ID가 만들어진 리소스 그룹을 지정합니다. `<USER ASSIGNED IDENTITY NAME>` 및 `<RESOURCE GROUP>` 매개 변수 값을 원하는 값으로 바꿉니다.

 ```azurecli-interactive
az identity delete -n <USER ASSIGNED IDENTITY NAME> -g <RESOURCE GROUP>
```
> [!NOTE]
> 사용자 할당 관리 ID를 삭제해도 할당된 리소스에서 참조는 제거되지 않습니다. 이러한 항목을 VM/VMSS에서 제거하려면 `az vm/vmss identity remove` 명령을 사용합니다.

## <a name="next-steps"></a>다음 단계

Azure CLI identity 명령의 전체 목록을 보려면 [az identity](/cli/azure/identity)를 참조하세요.

Azure VM에 사용자 할당 관리 ID를 할당하는 방법에 대한 자세한 내용은 [Azure CLI를 사용하여 Azure VM에서 Azure 리소스에 대한 관리 ID 구성](qs-configure-cli-windows-vm.md#user-assigned-managed-identity)을 참조하세요.


 
