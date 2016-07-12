<properties
	pageTitle="Azure AD 도메인 서비스: 암호 동기화 활성화 | Microsoft Azure"
	description="Azure Active Directory 도메인 서비스 시작"
	services="active-directory-ds"
	documentationCenter=""
	authors="mahesh-unnikrishnan"
	manager="stevenpo"
	editor="curtand"/>

<tags
	ms.service="active-directory-ds"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="07/06/2016"
	ms.author="maheshu"/>

# Azure AD 도메인 서비스*(미리 보기)* - Azure AD 도메인 서비스에 대한 암호 동기화 활성화

## 작업 5: 클라우드 전용 Azure AD 디렉터리에 대해 AAD 도메인 서비스에 대한 암호 동기화 활성화
Azure AD 테넌트에 Azure AD 도메인 서비스를 사용하도록 설정한 후에 수행할 작업은 Azure AD 도메인 서비스에 동기화하기 위해서 자격 증명을 사용하도록 설정하는 것입니다. 이렇게 하면 사용자가 회사 자격 증명을 사용하여, 관리되는 도메인에 로그인할 수 있습니다.

관련된 단계는 조직이 클라우드 전용 Azure AD 디렉터리인지, 아니면 Azure AD Connect를 사용하여 온-프레미스 디렉터리와 동기화되도록 설정되었는지에 따라 다릅니다.

<br>

> [AZURE.SELECTOR]
- [클라우드 전용 Azure AD 디렉터리](active-directory-ds-getting-started-password-sync.md)
- [동기화된 Azure AD 디렉터리](active-directory-ds-getting-started-password-sync-synced-tenant.md)

<br>

### 클라우드 전용 Azure AD 디렉터리에 대해 NTLM 및 Kerberos 자격 증명 해시 생성 활성화
조직에 클라우드 전용 Azure AD 디렉터리가 있는 경우, Azure AD 도메인 서비스를 사용해야 하는 사용자는 암호를 변경해야 합니다. 암호 변경 프로세스를 수행하면 Kerberos 및 NTLM 인증을 위해 Azure AD 도메인 서비스에 필요한 자격 증명 해시가 Azure AD에서 생성됩니다. Azure AD 도메인 서비스를 사용해야 하는 테넌트의 모든 사용자에 대한 암호를 만료시키거나, 암호를 변경하도록 이러한 사용자에게 지시할 수 있습니다.

암호를 변경하기 위해 최종 사용자에게 제공해야 하는 지침은 다음과 같습니다.

1. 조직의 Azure AD 액세스 패널 페이지로 이동합니다. 이 페이지는 일반적으로 [http://myapps.microsoft.com](http://myapps.microsoft.com)에 있습니다.

2. 이 페이지에서 **프로필** 탭을 선택합니다.

3. 이 페이지에서 **암호 변경** 타일을 클릭하여 암호 변경을 시작합니다.

    ![Azure AD 도메인 서비스에 대한 가상 네트워크를 만듭니다.](./media/active-directory-domain-services-getting-started/user-change-password.png)

4. **암호 변경** 페이지가 표시됩니다. 사용자가 기존(이전) 암호를 입력하고 암호 변경을 진행할 수 있습니다.

    ![Azure AD 도메인 서비스에 대한 가상 네트워크를 만듭니다.](./media/active-directory-domain-services-getting-started/user-change-password2.png)

사용자가 암호를 변경한 후 새 암호를 즉시 Azure AD 도메인 서비스에 사용할 수 있습니다. 몇 분 후 사용자가 새로 변경한 암호를 사용하여 관리되는 도메인에 가입된 컴퓨터에 로그인할 수 있습니다.


<br>

## 관련 콘텐츠

- [동기화된 Azure AD 디렉터리에 대해 AAD 도메인 서비스에 대한 암호 동기화 활성화](active-directory-ds-getting-started-password-sync-synced-tenant.md)

- [Azure AD 도메인 서비스 관리되는 도메인 관리](active-directory-ds-admin-guide-administer-domain.md)

- [Windows 가상 컴퓨터를 Azure AD 도메인 서비스 관리되는 도메인에 가입](active-directory-ds-admin-guide-join-windows-vm.md)

- [Red Hat Enterprise Linux 가상 컴퓨터를 Azure AD 도메인 서비스 관리되는 도메인에 가입](active-directory-ds-admin-guide-join-rhel-linux-vm.md)

<!---HONumber=AcomDC_0706_2016-->