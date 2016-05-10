<properties
	pageTitle="Azure AD 도메인 서비스: AAD DC Administrators 그룹 만들기 | Microsoft Azure"
	description="Azure Active Directory 도메인 서비스 시작(미리 보기)"
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
	ms.date="04/25/2016"
	ms.author="maheshu"/>

# Azure AD 도메인 서비스*(미기 보기)* - 'AAD DC Administrators' 그룹 만들기

이 문서에서는 Azure AD 테넌트에 Azure AD 도메인 서비스를 사용하도록 설정하는 데 필요한 구성 작업을 안내합니다.

## 작업 1: 'AAD DC Administrators' 그룹 만들기
첫 번째 작업은 Azure Active Directory 테넌트에 관리 그룹을 만드는 것입니다. 이 특수 관리 그룹을 **AAD DC Administrators**라고 합니다. 이 그룹의 멤버에게는 설정할 Azure AD 도메인 서비스 도메인에 가입한 컴퓨터에 대한 관리 권한이 부여됩니다. 도메인 가입 후 이 그룹은 이러한 도메인에 가입한 컴퓨터에서 'Administrators' 그룹에 추가됩니다. 또한 이 그룹의 멤버는 원격 데스크톱을 사용하여 도메인에 가입한 컴퓨터에 원격으로 연결할 수도 있습니다.

> [AZURE.NOTE] Azure AD 도메인 서비스를 사용하여 만든 관리되는 도메인에 도메인 관리자 또는 엔터프라이즈 관리자 권한이 없습니다. 이 도메인은 관리되는 도메인이므로 이러한 권한은 서비스에서 예약되며 디렉터리 내의 사용자가 사용할 수 없습니다. 하지만, 높은 권한의 작업을 수행하기 위하여, 이 구성 작업을 통해 만드는 특수 관리자 그룹을 사용할 수 있게 됩니다. 이 작업에는 도메인에 가입한 컴퓨터에서 관리자 그룹에 속하는 도메인에 컴퓨터를 가입시키고, 그룹 정책을 구성하는 등의 작업이 포함됩니다.

구성 작업에서는 관리 그룹을 만들고 디렉터리 내 한 명 이상의 사용자를 그룹에 추가합니다. Azure AD 도메인 서비스에 대한 관리 그룹을 만들기 위해 다음 단계를 수행합니다.

1. **Azure 클래식 포털**(즉, [https://manage.windowsazure.com](https://manage.windowsazure.com))로 이동합니다.

2. 왼쪽 창에서 **Active Directory** 노드를 선택합니다.

3. Azure AD 도메인 서비스를 사용하도록 설정할 Azure AD 테넌트(디렉터리)를 선택합니다. Azure AD 디렉터리마다 도메인을 하나만 만들 수 있습니다.

    ![Azure AD 디렉터리 선택](./media/active-directory-domain-services-getting-started/select-aad-directory.png)

4. **그룹** 탭을 클릭합니다.

5. 디렉터리에 그룹을 추가하기 위해 페이지 아래쪽의 작업창에서 **그룹 추가**를 클릭합니다.

6. **AAD DC Administrators**라는 그룹을 만듭니다.

    > [AZURE.WARNING] Azure AD 도메인 서비스 내에서 액세스할 수 있도록 설정하기 위해 정확히 이 이름으로 그룹을 만들어야 합니다.

	![관리자 그룹 만들기](./media/active-directory-domain-services-getting-started/create-admin-group.png)

7. 이 그룹이 Azure AD 도메인 서비스 내에서 관리 권한을 부여하는 데 사용될 것임을 Azure AD 테넌트 내의 다른 사용자가 이해할 수 있도록 이 그룹에 대한 설명을 추가할 수 있습니다.

8. 그룹을 만든 후 그룹의 이름을 클릭하여 그룹의 속성을 표시합니다. 이 그룹의 멤버로 사용자를 추가하기 위해 아래쪽 패널에서 **멤버 추가** 단추를 클릭합니다.

9. **멤버 추가** 대화 상자에서 이 그룹의 멤버여야 하는 사용자를 선택하고 완료하면 확인란을 선택합니다.

    ![관리자 그룹에 사용자 추가](./media/active-directory-domain-services-getting-started/add-group-members.png)

<br>

## 작업 2: Azure 가상 네트워크 만들기 또는 선택
다음 구성 작업은 [Azure 가상 네트워크를 만들거나 선택하는](active-directory-ds-getting-started-vnet.md) 작업입니다.

<!---HONumber=AcomDC_0427_2016-->