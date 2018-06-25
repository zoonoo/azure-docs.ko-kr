---
title: Azure Active Directory 조건부 액세스의 기준 보호 | Microsoft Docs
description: 환경에서 기본 수준 이상의 보안을 유지하는 기준 보호에 대해 알아봅니다.
services: active-directory
keywords: 앱에 조건부 액세스, Azure AD로 조건부 액세스, 회사 리소스에 대한 액세스 보호, 조건부 액세스 정책
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.assetid: 8c1d978f-e80b-420e-853a-8bbddc4bcdad
ms.service: active-directory
ms.component: protection
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/08/2018
ms.author: markvi
ms.reviewer: nigu
ms.openlocfilehash: 25ae4db2cd4f2a2cea74c428a272c6868acaa5c5
ms.sourcegitcommit: 50f82f7682447245bebb229494591eb822a62038
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/08/2018
ms.locfileid: "35248967"
---
# <a name="what-is-baseline-protection"></a>기준 보호란?  

지난 해, ID 공격이 300% 증가했습니다. 점점 증가하는 공격으로부터 환경을 보호하기 위해 Azure AD(Azure Active Directory)에 기준 보호라고 하는 새 기능이 도입되었습니다. 기준 보호는 미리 정의된 조건부 액세스 정책 집합입니다. 환경에서 기본 수준 이상의 보안을 유지하는 것이 이 정책의 목표입니다. 

미리 보기 기간에는 기준 보호를 활성화하려면 직접 기준 보호를 설정해야 합니다. 일반 공급 이후에는 이 정책이 기본적으로 사용됩니다. 

첫 번째 기준 보호 정책에는 MFA가 필요합니다. 공격자가 권한 있는 계정의 제어권을 확보하면 엄청난 피해가 발생할 수 있으므로 이러한 계정을 보호하는 것이 무엇보다도 중요합니다. 다음과 같은 권한 있는 역할은 이 정책의 범위에 포함됩니다. 

- 전역 관리자  

- SharePoint 관리자  

- Exchange 관리자  

- 조건부 액세스 관리자  

- 보안 관리자  


![Azure Active Directory](./media/active-directory-conditional-access-baseline-protection/01.png)

## <a name="how-to-get-started"></a>시작하는 방법 

기준 정책을 사용하려면:  

1. [Azure Portal](https://portal.azure.com)에 전역 관리자, 보안 관리자 또는 조건부 액세스 관리자로 로그인합니다.

2. **Azure Portal**의 왼쪽 탐색 모음에서 **Azure Active Directory**를 클릭합니다.

    ![Azure Active Directory](./media/active-directory-conditional-access-baseline-protection/02.png)

3. **Azure Active Directory** 페이지의 **관리** 섹션에서 **조건부 액세스**를 클릭합니다.

    ![조건부 액세스](./media/active-directory-conditional-access-baseline-protection/03.png)

4. 정책 목록에서 **기준 정책: 관리자에 대한 MFA 필요(미리 보기)** 를 클릭합니다. 

5. 정책을 사용하려면 **즉시 정책 사용**을 클릭합니다.

6. **저장**을 클릭합니다. 
 

기준 정책은 사용자 및 그룹을 제외하는 옵션을 제공합니다. 테넌트에서 차단되지 않도록 한 *[응급 액세스 관리 계정](active-directory-admin-manage-emergency-access-accounts.md)* 을 제외할 수 있습니다.
  
 

## <a name="what-you-should-know"></a>알아야 할 사항 

기준 정책에 포함된 디렉터리 역할은 가장 권한이 많은 Azure AD 역할입니다. 여러분의 피드백에 따라 향후 다른 역할이 포함될 수 있습니다. 

스크립트에 관리자 권한이 있는 계정이 포함된 경우 [MSI(관리 서비스 ID)](managed-service-identity/overview.md) 또는 [서비스 사용자(인증서가 있는)](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authenticate-service-principal)를 대신 사용해야 합니다. 임시 해결 방법으로 기준 정책에서 특정 사용자 계정을 제외할 수 있습니다. 

이 정책은 POP, IMAP, 기존의 Office 데스크톱 클라이언트 같은 레거시 인증 흐름에 적용됩니다. 

## <a name="next-steps"></a>다음 단계

조건부 액세스 정책을 구성하는 방법을 알아보려면 [Azure Active Directory에서 조건부 액세스 시작](active-directory-conditional-access-azure-portal-get-started.md)을 참조하세요.

사용자 환경에 대한 조건부 액세스 정책을 구성할 준비가 완료된 경우 [Azure Active Directory의 조건부 액세스 모범 사례](active-directory-conditional-access-best-practices.md)를 참조하세요. 
