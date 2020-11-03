---
title: Azure Key Vault 액세스 정책 할당 (포털)
description: Azure Portal를 사용 하 여 서비스 주체 또는 응용 프로그램 id에 Key Vault 액세스 정책을 할당 하는 방법입니다.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 08/27/2020
ms.author: mbaldwin
ms.openlocfilehash: 9277d100eb6744adfc20fbba9f96b822654e64a3
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2020
ms.locfileid: "93287713"
---
# <a name="assign-a-key-vault-access-policy-using-the-azure-portal"></a>Azure Portal를 사용 하 여 Key Vault 액세스 정책 할당

Key Vault 액세스 정책은 지정 된 서비스 주체 (즉, 응용 프로그램 또는 사용자 그룹)가 Key Vault [비밀](../secrets/index.yml), [키](../keys/index.yml)및 [인증서](../certificates/index.yml)에 대해 다른 작업을 수행할 수 있는지 여부를 결정 합니다. Azure Portal (이 문서), [Azure CLI](assign-access-policy-cli.md)또는 [Azure PowerShell](assign-access-policy-powershell.md)를 사용 하 여 액세스 정책을 할당할 수 있습니다.

[!INCLUDE [key-vault-access-policy-limits.md](../../../includes/key-vault-access-policy-limits.md)]

Azure Portal를 통해 Azure Active Directory에서 그룹을 만드는 방법에 대 한 자세한 내용은 [기본 그룹 만들기 및 멤버 추가](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md) 를 참조 하세요.

## <a name="assign-an-access-policy"></a>액세스 정책 할당

1.  [Azure Portal](https://portal.azure.com)에서 Key Vault 리소스로 이동 합니다. 

1.  **설정** 에서 **액세스 정책** 을 선택 하 고 **액세스 정책 추가** 를 선택 합니다.

    ![액세스 정책을 선택 하 고 역할 할당 추가를 선택 합니다.](../media/authentication/assign-policy-portal-01.png)

1.  **인증서 사용** 권한, **키 사용 권한** 및 **비밀 권한** 에서 원하는 권한을 선택 합니다. 일반적인 권한 조합을 포함 하는 템플릿을 선택할 수도 있습니다.

    ![액세스 정책 권한 지정](../media/authentication/assign-policy-portal-02.png)

1. **보안 주체 선택** 에서 선택한 항목 **없음** 링크를 선택 하 여 **주** 선택 창을 엽니다. 검색 필드에 앱 또는 서비스 사용자의 이름을 입력 하 고 적절 한 결과를 선택한 다음 **선택** 을 선택 합니다.

    ![액세스 정책에 대 한 서비스 주체 선택](../media/authentication/assign-policy-portal-03.png)

    앱에 관리 되는 id를 사용 하는 경우 앱 자체의 이름을 검색 하 여 선택 합니다. 관리 id 및 서비스 주체에 대 한 자세한 내용은 [Key Vault 인증-앱 id 및 서비스 주체](authentication.md#app-identity-and-security-principals)를 참조 하세요.)
 
1.  **액세스 정책 추가** 창으로 돌아가서 **추가** 를 선택 하 여 액세스 정책을 저장 합니다.

    ![지정 된 서비스 주체를 사용 하 여 액세스 정책 추가](../media/authentication/assign-policy-portal-04.png)

1. **액세스 정책** 페이지로 돌아가서 **현재** 액세스 정책 아래에 액세스 정책이 나열 되는지 확인 하 고 **저장** 을 선택 합니다. 액세스 정책은 저장할 때까지 적용 되지 않습니다.

    ![액세스 정책 변경 내용 저장](../media/authentication/assign-policy-portal-05.png)


## <a name="next-steps"></a>다음 단계

- [Azure Key Vault 보안: ID 및 액세스 관리](overview-security.md#identity-and-access-management)
- [키 자격 증명 모음을 보호](secure-your-key-vault.md)합니다.
- [Azure Key Vault 개발자 가이드](developers-guide.md)
- [Azure Key Vault 모범 사례](best-practices.md)