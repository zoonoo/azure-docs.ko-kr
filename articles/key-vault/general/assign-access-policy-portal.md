---
title: Azure Key Vault 액세스 정책 할당(Portal)
description: Azure Portal을 사용하여 보안 주체 또는 애플리케이션 ID에 Key Vault 액세스 정책을 할당하는 방법입니다.
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 08/27/2020
ms.author: mbaldwin
ms.openlocfilehash: 6258de3e6f81ef25c4f515c956662be13eb5f1e2
ms.sourcegitcommit: 8942cdce0108372d6fc5819c71f7f3cf2f02dc60
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/01/2021
ms.locfileid: "113136493"
---
# <a name="assign-a-key-vault-access-policy-using-the-azure-portal"></a>Azure Portal을 사용하여 Key Vault 액세스 정책 할당

Key Vault 액세스 정책은 사용자, 애플리케이션, 사용자 그룹 등의 특정 보안 주체가 Key Vault [비밀](../secrets/index.yml), [키](../keys/index.yml), [인증서](../certificates/index.yml)에 대해 서로 다른 작업을 수행할 수 있는지 여부를 결정합니다. Azure Portal(이 문서에서 설명)[Azure CLI](assign-access-policy-cli.md) 또는 [Azure PowerShell](assign-access-policy-powershell.md)을 사용하여 액세스 정책을 할당할 수 있습니다.

[!INCLUDE [key-vault-access-policy-limits.md](../../../includes/key-vault-access-policy-limits.md)]

Azure Portal을 통해 Azure Active Directory 그룹을 만드는 방법에 대한 자세한 내용은 [기본 그룹 만들기 및 멤버 추가](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md)를 참조하세요.

## <a name="assign-an-access-policy"></a>액세스 정책 할당

1.  [Azure Portal](https://portal.azure.com)에서 Key Vault 리소스로 이동합니다. 

1.  **설정** 아래에서 **액세스 정책** 을 선택한 다음, **액세스 정책 추가** 를 선택합니다.

    ![액세스 정책을 선택하고 역할 할당 추가를 선택합니다.](../media/authentication/assign-policy-portal-01.png)

1.  **인증서 권한**, **키 권한** 및 **비밀 권한** 에서 원하는 권한을 선택합니다. 일반적인 권한 조합을 포함하는 템플릿을 선택할 수도 있습니다.

    ![액세스 정책 권한 지정](../media/authentication/assign-policy-portal-02.png)

1. **보안 주체 선택** 에서 **선택한 항목 없음** 링크를 선택하여 **주** 선택 창을 엽니다. 검색 필드에 사용자, 앱 또는 서비스 사용자의 이름을 입력하고 적절한 결과와 **선택** 을 차례로 선택합니다.

    ![액세스 정책에 대한 보안 주체 선택](../media/authentication/assign-policy-portal-03.png)

    앱에 관리되는 ID를 사용하는 경우 앱 자체의 이름을 검색하여 선택합니다. (보안 주체에 대한 자세한 내용은 [Key Vault 인증](authentication.md)을 참조하세요.
 
1.  **액세스 정책 추가** 창으로 돌아가서 **추가** 를 선택하여 액세스 정책을 저장합니다.

    ![할당된 보안 주체를 사용하여 액세스 정책 추가](../media/authentication/assign-policy-portal-04.png)

1. **액세스 정책** 페이지로 돌아가서 **현재 액세스 정책** 아래에 액세스 정책이 나열되는지 확인하고 **저장** 을 선택합니다. 액세스 정책은 저장할 때까지 적용되지 않습니다.

    ![액세스 정책 변경 내용 저장](../media/authentication/assign-policy-portal-05.png)


## <a name="next-steps"></a>다음 단계

- [Azure Key Vault 보안](security-features.md)
- [Azure Key Vault 개발자 가이드](developers-guide.md)
