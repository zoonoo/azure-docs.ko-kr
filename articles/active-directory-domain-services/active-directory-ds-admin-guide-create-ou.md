<properties
	pageTitle="Azure Active Directory Domain Services: 관리 가이드 | Microsoft Azure"
	description="Azure AD 도메인 서비스 관리되는 도메인에 OU(조직 구성 단위) 만들기"
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
	ms.topic="article"
	ms.date="09/21/2016"
	ms.author="maheshu"/>

# Azure AD 도메인 서비스 관리되는 도메인에 OU(조직 구성 단위) 만들기
Azure AD 도메인 서비스 관리되는 도메인에는 'AADDC 컴퓨터'와 'AADDC 사용자'라는 두 개의 기본 제공 컨테이너가 각각 있습니다. 'AADDC 컴퓨터' 컨테이너에는 관리되는 도메인에 가입된 모든 컴퓨터에 대한 컴퓨터 개체가 있습니다. 'AADDC 사용자' 컨테이너는 Azure AD 테넌트의 사용자 및 그룹을 포함합니다. 경우에 따라 워크로드를 배포하기 위해 관리되는 도메인에 서비스 계정을 만들어야 할 수 있습니다. 이 용도로 관리되는 도메인에 사용자 지정 OU(조직 구성 단위)를 만들고 해당 OU 내에 서비스 계정을 만들 수 있습니다. 이 문서에서는 관리되는 도메인에 OU를 만드는 방법을 보여 줍니다.


## 도메인 가입된 가상 컴퓨터에 원격 관리를 위한 AD 관리 도구 설치
Azure AD 도메인 서비스 관리되는 도메인을 AD PowerShell 또는 ADAC(Active Directory 관리 센터)와 같은 친숙한 Active Directory 관리 도구를 사용하여 원격으로 관리할 수 있습니다. 테넌트 관리자는 원격 데스크톱을 통해 관리되는 도메인의 도메인 컨트롤러에 연결할 권한이 없습니다. 관리되는 도메인을 관리하려면 관리되는 도메인에 가입된 가상 컴퓨터에 AD 관리 도구 기능을 설치합니다. 지침은 [Azure AD 도메인 서비스 관리되는 도메인 관리](active-directory-ds-admin-guide-administer-domain.md)라는 문서를 참조하세요.

## 관리 도메인에서 조직 구성 단위 만들기
이제 도메인에 가입된 가상 컴퓨터에 AD 관리 도구가 설치되었습니다. 이러한 도구를 사용하여 관리되는 도메인에 조직 구성 단위를 만들 수 있습니다. 다음 단계를 수행합니다.

> [AZURE.NOTE] 'AAD DC 관리자' 그룹의 구성원에게만 사용자 지정 OU를 만드는 데 필요한 권한이 있습니다. 이 그룹에 속한 사용자로 다음 단계를 수행합니다.

1. 시작 화면에서 **관리 도구**를 클릭합니다. 가상 컴퓨터에 설치된 AD 관리 도구가 표시됩니다.

	![서버에 설치된 관리 도구](./media/active-directory-domain-services-admin-guide/install-rsat-admin-tools-installed.png)

2. **Active Directory 관리 센터**를 클릭합니다.

	![Active Directory 관리 센터](./media/active-directory-domain-services-admin-guide/adac-overview.png)

3. 도메인을 보려면 왼쪽 창에서 도메인 이름(예: 'contoso100.com')을 클릭합니다.

    ![ADAC - 도메인 보기](./media/active-directory-domain-services-admin-guide/create-ou-adac-overview.png)

4. 오른쪽 **태스크** 창의 도메인 이름 노드에서 **새로 만들기**를 클릭합니다. 이 예제에서는 오른쪽 **태스크** 창의 'contoso100(로컬)' 노드에서 **새로 만들기**를 클릭합니다.

    ![ADAC - 새 OU](./media/active-directory-domain-services-admin-guide/create-ou-adac-new-ou.png)

5. 조직 구성 단위 만들기에 대한 옵션이 표시됩니다. **조직 구성 단위**를 클릭하여 **조직 구성 단위 만들기** 대화 상자를 시작합니다.

6. **조직 구성 단위 만들기** 대화 상자에서 새 OU의 **이름**을 지정합니다. OU에 대한 간략한 설명을 제공합니다. OU에 대한 **관리자** 필드를 설정할 수도 있습니다. 사용자 지정 OU를 만들려면 **확인**을 클릭합니다.

    ![ADAC - OU 대화 상자 만들기](./media/active-directory-domain-services-admin-guide/create-ou-dialog.png)

7. 이제 새로 만든 OU가 ADAC(AD 관리 센터)에 나타납니다.

    ![ADAC - 만든 OU](./media/active-directory-domain-services-admin-guide/create-ou-done.png)


## 새로 만든 OU에 대한 사용 권한/보안
기본적으로 사용자 지정 OU를 만든 사용자('AAD DC 관리자' 그룹의 구성원)는 OU에 대해 관리자 권한(모든 권한)이 부여됩니다. 사용자는 계속해서 원하는 대로 다른 사용자에게 또는 'AAD DC 관리자' 그룹에 권한을 부여할 수 있습니다. 다음 스크린샷에서 볼 수 있듯이 새 'MyCustomOU' 조직 구성 단위를 만든 사용자 'bob@domainservicespreview.onmicrosoft.com'은 OU에 대해 모든 권한을 부여받습니다.

 ![ADAC - 새 OU 보안](./media/active-directory-domain-services-admin-guide/create-ou-permissions.png)


## 사용자 지정 OU 관리에 대한 참고 사항
이제 사용자 지정 OU를 만들었으므로 계속해서 이 OU에 사용자, 그룹, 컴퓨터 및 서비스 계정을 만들 수 있습니다. 'AAD DC 사용자' OU에서 사용자 지정 OU로 사용자 또는 그룹을 이동할 수 없습니다.

> [AZURE.WARNING] 사용자 지정 OU에서 만든 사용자 계정, 그룹, 서비스 계정 및 컴퓨터 개체는 Azure AD 테넌트에서 사용할 수 없습니다. 즉, 이러한 개체는 Azure AD Graph API를 사용하여 또는 Azure AD UI에 표시되지 않습니다. Azure AD Domain Services 관리되는 도메인에서만 이러한 개체를 사용할 수 있습니다.


## 관련 콘텐츠

- [Azure AD 도메인 서비스 관리되는 도메인 관리](active-directory-ds-admin-guide-administer-domain.md)

- [Active Directory 관리 센터: 시작](https://technet.microsoft.com/library/dd560651.aspx)

- [서비스 계정 단계별 가이드](https://technet.microsoft.com/library/dd548356.aspx)

<!---HONumber=AcomDC_0928_2016-->