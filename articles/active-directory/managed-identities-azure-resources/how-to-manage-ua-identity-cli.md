---
title: 사용자 할당 관리 ID 관리 - Azure CLI - Azure AD
description: Azure CLI를 사용하여 사용자 할당 관리 ID를 만들고 나열하고 삭제하는 방법에 대한 단계별 지침입니다.
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/17/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.custom: devx-track-azurecli
ms.openlocfilehash: a26f13b71ae96f4d6593cb4a4d9107f8ef6c207c
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107784878"
---
# <a name="create-list-or-delete-a-user-assigned-managed-identity-using-the-azure-cli"></a>Azure CLI를 사용하여 사용자가 할당한 관리 ID 생성, 나열 또는 삭제


Azure 리소스에 대한 관리 ID는 Azure Active Directory에서 관리 ID를 Azure 서비스에 제공합니다. 이 ID를 사용하면 코드에 자격 증명을 포함할 필요 없이 Azure AD 인증을 지원하는 서비스에 인증할 수 있습니다. 

이 문서에서는 Azure CLI를 사용하여 사용자 할당 관리 ID 만들고 나열하고 삭제하는 방법을 알아봅니다.

아직 Azure 계정이 없으면 계속하기 전에 [평가판 계정](https://azure.microsoft.com/free/)에 등록해야 합니다.

## <a name="prerequisites"></a>사전 요구 사항

- Azure 리소스에 대한 관리 ID에 익숙하지 않은 경우 [Azure 리소스에 대한 관리 ID란?](overview.md)을 참조하세요. 시스템 할당 및 사용자 할당 관리 ID 형식에 대한 자세한 내용은 [관리 ID 형식](overview.md#managed-identity-types)을 참조하세요.

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../../includes/azure-cli-prepare-your-environment-no-header.md)]

> [!NOTE]   
> CLI를 사용하는 앱 서비스 주체를 사용할 때 사용자 권한을 수정하려면 CLI의 일부에서 Graph API에 대해 GET 요청을 수행하는 서비스 주체에게 Azure AD Graph API의 추가 권한을 제공해야 합니다. 그러지 않으면 '작업을 완료할 수 있는 권한이 없습니다.' 메시지가 표시될 수 있습니다. 이렇게 하려면 Azure Active Directory에서 앱 등록으로 이동하여 앱을 선택하고, API 권한을 클릭하고, 아래로 스크롤하여 Azure Active Directory Graph를 선택합니다. 여기에서 애플리케이션 권한을 선택하고 적절한 권한을 추가합니다. 

## <a name="create-a-user-assigned-managed-identity"></a>사용자 할당 관리 ID 만들기 

사용자 할당 관리 ID를 만들려면 계정에 [관리 ID 기여자](../../role-based-access-control/built-in-roles.md#managed-identity-contributor) 역할 할당이 필요합니다.

사용자 할당 관리 ID를 만들려면 [az identity create](/cli/azure/identity#az_identity_create) 명령을 사용합니다. `-g` 매개 변수는 사용자 할당 관리 ID가 만들어질 리소스 그룹을 지정하고 `-n` 매개 변수는 그 이름을 지정합니다. `<RESOURCE GROUP>` 및 `<USER ASSIGNED IDENTITY NAME>` 매개 변수 값을 원하는 값으로 바꿉니다.

[!INCLUDE [ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

```azurecli-interactive
az identity create -g <RESOURCE GROUP> -n <USER ASSIGNED IDENTITY NAME>
```
## <a name="list-user-assigned-managed-identities"></a>사용자 할당 관리 ID 나열

사용자 할당 관리 ID를 나열하려면/읽으려면 계정에 [관리 ID 운영자](../../role-based-access-control/built-in-roles.md#managed-identity-operator) 또는 [관리 ID 기여자](../../role-based-access-control/built-in-roles.md#managed-identity-contributor) 역할 할당이 필요합니다.

사용자 할당 관리 ID를 나열하려면 [az identity list](/cli/azure/identity#az_identity_list) 명령을 사용합니다. `<RESOURCE GROUP>`을 원하는 값으로 바꿉니다.

```azurecli-interactive
az identity list -g <RESOURCE GROUP>
```

JSON 응답에서 사용자 할당 관리 ID에는 키 `type`에 대해 반환된 `"Microsoft.ManagedIdentity/userAssignedIdentities"` 값이 있습니다.

`"type": "Microsoft.ManagedIdentity/userAssignedIdentities"`

## <a name="delete-a-user-assigned-managed-identity"></a>사용자 할당 관리 ID 삭제

사용자 할당 관리 ID를 삭제하려면 계정에 [관리 ID 기여자](../../role-based-access-control/built-in-roles.md#managed-identity-contributor) 역할 할당이 필요합니다.

사용자 할당 관리 ID를 삭제하려면 [az identity delete](/cli/azure/identity#az_identity_delete) 명령을 사용합니다.  -n 매개 변수는 그 이름을 지정하고 -g 매개 변수는 사용자 할당 관리 ID가 만들어진 리소스 그룹을 지정합니다. `<USER ASSIGNED IDENTITY NAME>` 및 `<RESOURCE GROUP>` 매개 변수 값을 원하는 값으로 바꿉니다.

```azurecli-interactive
az identity delete -n <USER ASSIGNED IDENTITY NAME> -g <RESOURCE GROUP>
```
> [!NOTE]
> 사용자 할당 관리 ID를 삭제해도 할당된 리소스에서 참조는 제거되지 않습니다. 이러한 항목을 VM/VMSS에서 제거하려면 `az vm/vmss identity remove` 명령을 사용합니다.

## <a name="next-steps"></a>다음 단계

Azure CLI identity 명령의 전체 목록을 보려면 [az identity](/cli/azure/identity)를 참조하세요.

Azure VM에 사용자 할당 관리 ID를 할당하는 방법에 대한 자세한 내용은 [Azure CLI를 사용하여 Azure VM에서 Azure 리소스에 대한 관리 ID 구성](qs-configure-cli-windows-vm.md#user-assigned-managed-identity)을 참조하세요.
