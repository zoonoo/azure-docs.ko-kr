---
title: "Azure Active Directory Domain Services: 암호 동기화 활성화 | Microsoft Docs"
description: "Azure Active Directory 도메인 서비스 시작"
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: stevenpo
editor: curtand
ms.assetid: 5a32a0df-a3ca-4ebe-b980-91f58f8030fc
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/06/2017
ms.author: maheshu
translationtype: Human Translation
ms.sourcegitcommit: c300ba45cd530e5a606786aa7b2b254c2ed32fcd
ms.openlocfilehash: 51e4665b6372859021237144a0bf7f6763a2638b
ms.lasthandoff: 04/14/2017


---
# <a name="enable-password-synchronization-with-azure-active-directory-domain-services"></a>Azure Active Directory Domain Services에 대한 암호 동기화 활성화
이전 작업에서 Azure AD(Azure Active Directory) 테넌트를 위해 Azure AD DS(Active Directory Domain Services)를 사용하도록 설정했습니다. 다음 작업은 NTLM(NT LAN Manager) 및 Kerberos 인증이 Azure Active Directory Domain Services와 동기화하는 데 필요한 자격 증명 해시를 사용하도록 설정하는 것입니다. 자격 증명 동기화를 설정하면 사용자는 회사 자격 증명을 사용하여 관리되는 도메인에 로그인할 수 있습니다.

절차는 조직에 클라우드 전용 Azure AD 테넌트가 있는지 또는 Azure AD Connect를 사용하여 온-프레미스 디렉터리와 동기화되도록 설정되었는지에 따라 다릅니다.

> [!div class="op_single_selector"]
> * [클라우드 전용 Azure AD 테넌트](active-directory-ds-getting-started-password-sync.md)
> * [동기화된 Azure AD 테넌트](active-directory-ds-getting-started-password-sync-synced-tenant.md)
>
>

## <a name="task-5-enable-password-synchronization-with-azure-active-directory-domain-services-for-a-cloud-only-azure-ad-tenant"></a>작업 5: 클라우드 전용 Azure AD 테넌트에 대한 Azure Active Directory Domain Services와 암호 동기화 활성화
관리되는 도메인에서 사용자를 인증하려면 Azure Active Directory Domain Services에 NTLM 및 Kerberos 인증에 적합한 형식의 자격 증명 해시가 필요합니다. 테넌트에 대해 Azure Active Directory Domain Services를 활성화하지 않는 한 Azure AD에서는 NTLM 또는 Kerberos 인증에 필요한 형식으로 자격 증명 해시를 생성하거나 저장하지 않습니다. 확실한 보안을 위해 Azure AD는 일반 텍스트 형식으로 자격 증명을 저장하지 않습니다. 따라서 Azure AD를 통해 사용자의 기존 자격 증명에 따라 이러한 NTLM 또는 Kerberos 자격 증명 해시를 생성할 수 없습니다.

> [!NOTE]
> 조직에 클라우드 전용 Azure AD 테넌트가 있는 경우 Azure Active Directory Domain Services를 사용해야 하는 사용자는 암호를 변경해야 합니다.
>
>

이 암호 변경 프로세스를 수행하면 Azure AD에서 Kerberos 및 NTLM 인증을 위해 Azure Active Directory Domain Services에 필요한 자격 증명 해시가 생성됩니다. Azure Active Directory Domain Services를 사용해야 하는 테넌트의 모든 사용자에 대한 암호를 만료시키거나, 암호를 변경하도록 해당 사용자에게 지시할 수 있습니다.

### <a name="enable-ntlm-and-kerberos-credential-hash-generation-for-a-cloud-only-azure-ad-tenant"></a>클라우드 전용 Azure AD 테넌트에 대해 NTLM 및 Kerberos 자격 증명 해시 생성 활성화
암호를 변경할 수 있도록 사용자에게 제공되는 지침은 다음과 같습니다.

1. 사용자의 조직에 대한 [Azure AD 액세스 패널](http://myapps.microsoft.com) 페이지로 이동합니다.
2. [액세스 패널] 창에서 **프로필** 탭을 선택합니다.
3. **암호 변경** 타일을 클릭합니다.

    ![Azure AD 액세스 패널의 "암호 변경" 타일](./media/active-directory-domain-services-getting-started/user-change-password.png)

   > [!NOTE]
   > **암호 변경** 옵션이 [액세스 패널] 창에 표시되지 않으면 조직이 [Azure AD에서 암호 관리](../active-directory/active-directory-passwords-getting-started.md)를 구성했는지 확인합니다.
   >
   >
4. **암호 변경** 페이지에서 기존(이전) 암호를 입력하고 새 암호를 입력한 다음 확인합니다.

    ![Azure AD 도메인 서비스에 대한 가상 네트워크를 만듭니다.](./media/active-directory-domain-services-getting-started/user-change-password2.png)

5. **제출**을 클릭합니다.

암호를 변경하고 나서 몇 분 후에 Azure Active Directory Domain Services에서 새 암호를 사용할 수 있습니다. 몇 분(일반적으로 약 20분) 후에 새로 변경한 암호를 사용하여 관리되는 도메인에 가입된 컴퓨터에 로그인할 수 있습니다.

## <a name="next-steps"></a>다음 단계
* [고유한 암호를 업데이트하는 방법](../active-directory/active-directory-passwords-update-your-own-password.md#reset-my-password)
* [Azure AD에서 암호 관리 시작](../active-directory/active-directory-passwords-getting-started.md)
* [동기화된 Azure AD 테넌트의 Azure Active Directory Domain Services에 대한 암호 동기화 활성화](active-directory-ds-getting-started-password-sync-synced-tenant.md)
* [Azure Active Directory Domain Services 관리되는 도메인 관리](active-directory-ds-admin-guide-administer-domain.md)
* [Windows 가상 컴퓨터를 Azure Active Directory Domain Services 관리되는 도메인에 가입](active-directory-ds-admin-guide-join-windows-vm.md)
* [Red Hat Enterprise Linux 가상 컴퓨터를 Azure Active Directory Domain Services 관리되는 도메인에 가입](active-directory-ds-admin-guide-join-rhel-linux-vm.md)

