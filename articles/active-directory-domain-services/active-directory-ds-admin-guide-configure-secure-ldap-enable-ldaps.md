---
title: Azure AD Domain Services에서 보안 LDAP(LDAPS) 사용하도록 설정 | Microsoft Docs
description: Azure AD Domain Services 관리되는 도메인에 대해 보안 LDAP(LDAPS) 사용하도록 설정
services: active-directory-ds
documentationcenter: ''
author: eringreenlee
manager: daveba
editor: curtand
ms.assetid: c6da94b6-4328-4230-801a-4b646055d4d7
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/01/2018
ms.author: ergreenl
ms.openlocfilehash: df189e405dcd5277c1ccbd94e9d5d302660be79b
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "60359824"
---
# <a name="enable-secure-ldap-ldaps-for-an-azure-ad-domain-services-managed-domain"></a>Azure AD Domain Services 관리되는 도메인에 대해 보안 LDAP(LDAPS) 사용하도록 설정

## <a name="before-you-begin"></a>시작하기 전에
[작업 2 - 보안 LDAP 인증서를 .PFX 파일로 내보내기](active-directory-ds-admin-guide-configure-secure-ldap-export-pfx.md)를 완료합니다.


## <a name="task-3-enable-secure-ldap-for-the-managed-domain-using-the-azure-portal"></a>작업 3: Azure Portal을 사용하여 관리형 도메인에 보안 LDAP를 사용하도록 설정
보안 LDAP를 사용하도록 설정하려면 다음 구성 단계를 수행합니다.

1. **[Azure Portal](https://portal.azure.com)** 로 이동합니다.

2. **리소스 검색** 검색 상자에서 'domain services'를 검색합니다. 검색 결과에서 **Azure AD Domain Services**를 선택합니다. **Azure AD Domain Services** 페이지에 관리되는 도메인이 나열됩니다.

    ![프로비전되는 관리되는 도메인 찾기](./media/getting-started/domain-services-provisioning-state-find-resource.png)

2. 도메인에 대한 자세한 내용을 보려면 관리되는 도메인의 이름(예: 'contoso100.com')을 클릭합니다.

    ![Domain Services - 프로비저닝 상태](./media/getting-started/domain-services-provisioning-state.png)

3. 탐색 창에서 **보안 LDAP**를 클릭합니다.

    ![Domain Services - 보안 LDAP 페이지](./media/active-directory-domain-services-admin-guide/secure-ldap-blade.png)

4. 기본적으로 관리되는 도메인에 대한 보안 LDAP 액세스는 사용하지 않도록 설정되어 있습니다. **보안 LDAP**를 **사용**으로 토글합니다.

    ![보안 LDAP를 사용하도록 설정](./media/active-directory-domain-services-admin-guide/secure-ldap-blade-configure.png)
5. 기본적으로 인터넷에서 관리되는 도메인에 대한 보안 LDAP 액세스는 사용하지 않도록 설정되어 있습니다. 필요한 경우 **인터넷을 통한 보안 LDAP 액세스 허용**을 **사용**으로 토글합니다.

    > [!WARNING]
    > 인터넷을 통해 보안 LDAP 액세스를 사용하도록 설정하면 도메인이 인터넷을 통한 무차별 암호 대입 공격(brute force attack)에 취약해질 수 있습니다. 따라서 필요한 원본 IP 주소 범위에 대한 액세스를 잠그도록 NSG를 설정하는 것이 좋습니다. [인터넷을 통해 관리되는 도메인에 대한 LDAPS 액세스 잠금](active-directory-ds-ldaps-bind-lockdown.md#task-6-lock-down-secure-ldap-access-to-your-managed-domain-over-the-internet)에 대한 지침을 참조하세요.
    >

6. **보안 LDAP 인증서가 포함된 .PFX 파일** 뒤의 폴더 아이콘을 클릭합니다. 관리되는 도메인에 대한 보안 LDAP 액세스를 위해 인증서를 사용하여 PFX 파일의 경로를 지정합니다.

7. **.PFX 파일의 암호를 해독하도록 암호를** 지정합니다. 인증서를 PFX 파일로 내보낼 때 사용한 것과 동일한 암호를 입력합니다.

8. 완료되면 **저장** 단추를 클릭합니다.

9. 관리되는 도메인에 대해 보안 LDAP가 구성되었음을 알려 주는 알림이 표시됩니다. 이 작업이 완료될 때까지 도메인에 대한 다른 설정을 수정할 수 없습니다.

    ![관리되는 도메인에 대해 보안 LDAP 구성](./media/active-directory-domain-services-admin-guide/secure-ldap-blade-configuring.png)

> [!NOTE]
> 관리되는 도메인에 대해 보안 LDAP를 사용하도록 설정하는 데 약 10-15분이 소요됩니다. 제공된 보안 LDAP 인증서가 필수 조건을 만족하지 않으면 보안 LDAP는 디렉터리에 사용할 수 없으며 오류가 표시됩니다. 도메인 이름이 올바르지 않거나 인증서가 만료되었거나 곧 만료된다는 오류 등이 여기에 해당합니다. 이 경우 유효한 인증서로 다시 시도해 보세요.
>
>

## <a name="next-step"></a>다음 단계
[작업 4: 인터넷에서 관리되는 도메인에 액세스하도록 DNS 구성](active-directory-ds-ldaps-configure-dns.md)
