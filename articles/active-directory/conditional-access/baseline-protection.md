---
title: Azure Active Directory 조건부 액세스의 기준 보호란? - 미리 보기 | Microsoft Docs
description: Azure Active Directory 환경에서 기준 수준 이상의 보안을 유지하는 기준 보호에 대해 알아봅니다.
services: active-directory
keywords: 앱에 조건부 액세스, Azure AD로 조건부 액세스, 회사 리소스에 대한 액세스 보호, 조건부 액세스 정책
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.assetid: 8c1d978f-e80b-420e-853a-8bbddc4bcdad
ms.service: active-directory
ms.component: conditional-access
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/08/2018
ms.author: markvi
ms.reviewer: nigu
ms.openlocfilehash: 57fef112186834ead76f6223e32cb358e4d6d053
ms.sourcegitcommit: d211f1d24c669b459a3910761b5cacb4b4f46ac9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/06/2018
ms.locfileid: "44024076"
---
# <a name="what-is-baseline-protection-preview"></a>기준 보호(미리 보기)란?  

지난 해, ID 공격이 300% 증가했습니다. 점점 증가하는 공격으로부터 환경을 보호하기 위해 Azure AD(Azure Active Directory)에 기준 보호라고 하는 새 기능이 도입되었습니다. 기준 보호는 미리 정의된 [조건부 액세스 정책](../active-directory-conditional-access-azure-portal.md) 집합입니다. 모든 버전의 Azure AD에서 기준 수준 이상의 보안을 유지하는 것이 이 정책의 목표입니다. 

이 문서에서는 Azure Active Directory의 기준 보호에 대해 간략하게 설명합니다.


 
## <a name="require-mfa-for-admins"></a>관리자에 대해 MFA 요구

권한 있는 계정에 대한 액세스 권한이 있는 사용자는 사용자 환경을 무제한 액세스할 수 있습니다. 이러한 계정은 권한이 크기 때문에 특별히 주의해서 처리해야 합니다. 권한 있는 계정의 보호를 향상하는 한 가지 일반적인 방법은 로그인에 사용할 때 보다 강력한 형태의 계정 확인을 요구하는 것입니다. Azure Active Directory에서 MFA(다단계 인증)를 요구하면 보다 강력한 계정 확인이 가능합니다.  

**관리자에 대해 MFA 요구**는 다음 디렉터리 역할에 대해 MFA를 요구하는 기준 정책입니다. 

- 전역 관리자  

- SharePoint 관리자  

- Exchange 관리자  

- 조건부 액세스 관리자  

- 보안 관리자  


![Azure Active Directory](./media/baseline-protection/01.png)

이 기준 정책은 사용자 및 그룹을 제외하는 옵션을 제공합니다. 테넌트에서 차단되지 않도록 한 *[응급 액세스 관리 계정](../users-groups-roles/directory-emergency-access.md)* 을 제외할 수 있습니다.


## <a name="enable-a-baseline-policy"></a>기준 정책 사용 

기준 정책은 미리 보기 상태인 동안 기본적으로 활성화되지 않습니다. 활성화하려면 수동으로 정책을 사용하도록 설정해야 합니다. 이 기능이 일반 공급에 도달하는 즉시 기본적으로 정책이 활성화됩니다. 계획된 동작 변경 때문에 정책의 상태를 설정하는 세 번째 옵션인 **향후 자동으로 정책 사용**도 활성화 및 비활성화해야 합니다. 이 옵션을 선택하면 Microsoft에서 정책을 활성화할 시기를 결정합니다.      


**기준 정책을 사용하도록 설정하려면**  

1. [Azure Portal](https://portal.azure.com)에 전역 관리자, 보안 관리자 또는 조건부 액세스 관리자로 로그인합니다.

2. **Azure Portal**의 왼쪽 탐색 모음에서 **Azure Active Directory**를 클릭합니다.

    ![Azure Active Directory](./media/baseline-protection/02.png)

3. **Azure Active Directory** 페이지의 **보안** 섹션에서 **조건부 액세스**를 클릭합니다.

    ![조건부 액세스](./media/baseline-protection/05.png)

4. 정책 목록에서 **기준 정책:** 으로 시작하는 정책을 클릭합니다. 

5. 정책을 사용하려면 **즉시 정책 사용**을 클릭합니다.

6. **저장**을 클릭합니다. 
 
  
 

## <a name="what-you-should-know"></a>알아야 할 사항 

사용자 지정 조건부 액세스 정책을 관리하려면 Azure AD Premium 라이선스가 필요하지만, 모든 버전의 Azure AD에서 기준 정책을 사용할 수 있습니다.     

기준 정책에 포함된 디렉터리 역할은 가장 권한이 많은 Azure AD 역할입니다. 

스크립트에서 권한 있는 계정을 사용한 경우, [MSI(관리 서비스 ID)](../managed-identities-azure-resources/overview.md) 또는 [서비스 보안 주체(인증서 사용)](../../azure-resource-manager/resource-group-authenticate-service-principal.md)를 대신 사용해야 합니다. 임시 해결 방법으로 기준 정책에서 특정 사용자 계정을 제외할 수 있습니다. 

기준 정책은 POP, IMAP, 기존의 Office 데스크톱 클라이언트 같은 레거시 인증 흐름에 적용됩니다. 




## <a name="next-steps"></a>다음 단계

자세한 내용은 다음을 참조하세요.

- [ID 인프라를 보호하기 위한 5단계](https://docs.microsoft.com/azure/security/azure-ad-secure-steps)

- [Azure Active Directory의 조건부 액세스란?](overview.md) 

