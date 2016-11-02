<properties
    pageTitle="Azure AD 도메인 서비스: AAD DC Administrators 그룹 만들기 | Microsoft Azure"
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
    ms.topic="article"
    ms.date="10/03/2016"
    ms.author="maheshu"/>


# <a name="get-started-with-azure-ad-domain-services"></a>Azure AD Domain Services 시작

이 문서에서는 Azure AD 테넌트에 Azure AD 도메인 서비스를 사용하도록 설정하는 데 필요한 구성 작업을 안내합니다.

## <a name="task-1:-create-the-'aad-dc-administrators'-group"></a>작업 1: 'AAD DC Administrators' 그룹 만들기
첫 번째 작업은 Azure Active Directory 테넌트에 관리 그룹을 만드는 것입니다. 이 특수 관리 그룹을 **AAD DC Administrators**라고 합니다. 이 그룹의 구성원에게는 Azure AD Domain Services 관리 도메인에 가입한 컴퓨터에 대한 관리 권한이 부여됩니다. 이 그룹은 도메인 가입 컴퓨터에서 '관리자' 그룹에 추가됩니다. 또한 이 그룹의 구성원은 원격 데스크톱을 사용하여 도메인에 가입한 컴퓨터에 원격으로 연결할 수 있습니다.  

> [AZURE.NOTE] Azure AD 도메인 서비스를 사용하여 만든 관리되는 도메인에 도메인 관리자 또는 엔터프라이즈 관리자 권한이 없습니다. 관리되는 도메인에서 이러한 권한은 서비스별로 예약되며 테넌트 내의 사용자가 사용할 수 없습니다. 하지만, 권한 있는 작업을 수행하기 위해 이 구성 태스크를 통해 만든 특수 관리자 그룹을 사용할 수 있습니다. 이 작업에는 도메인에 가입한 컴퓨터에서 관리자 그룹에 속하는 도메인에 컴퓨터를 가입시키고, 그룹 정책을 구성하는 등의 작업이 포함됩니다.

구성 태스크에서는 관리 그룹을 만들고 디렉터리에 있는 한 명 이상의 사용자를 그룹에 추가합니다. Azure AD Domain Services에 대한 관리 그룹을 만들려면 다음 단계를 수행합니다.

1. **Azure 클래식 포털** ([https://manage.windowsazure.com](https://manage.windowsazure.com))로 이동합니다.

2. 왼쪽 창에서 **Active Directory** 노드를 선택합니다.

3. Azure AD 도메인 서비스를 사용하도록 설정할 Azure AD 테넌트(디렉터리)를 선택합니다. Azure AD 디렉터리에 한 개의 도메인만을 만들 수 있습니다.

    ![Azure AD 디렉터리 선택](./media/active-directory-domain-services-getting-started/select-aad-directory.png)

4. **그룹** 탭을 클릭합니다.

5. Azure AD 테넌트에 그룹을 추가하려면 페이지 아래쪽의 태스크 창에서 **그룹 추가** 를 클릭합니다.

    ![그룹 단추 추가](./media/active-directory-domain-services-getting-started/add-group-button.png)

6. **AAD DC Administrators**라는 그룹을 만듭니다. **그룹 종류**를 **보안**으로 설정합니다.

    > [AZURE.WARNING] Azure AD Domain Services 관리되는 도메인 내에서 액세스할 수 있도록 설정하려면 정확히 이 이름으로 그룹을 만듭니다.

    ![관리자 그룹 만들기](./media/active-directory-domain-services-getting-started/create-admin-group.png)

7. Azure AD Domain Services 내에 관리 권한을 부여하는 데 이 그룹을 사용한다는 것을 다른 사용자가 이해할 수 있도록 이 그룹에 대한 설명을 추가합니다.

8. 그룹을 만든 후에 그룹의 이름을 클릭하여 그룹의 속성을 확인합니다. 이 그룹의 구성원으로 사용자를 추가하려면 아래쪽 패널에서 **멤버 추가** 단추를 클릭합니다.

    ![그룹 구성원 단추 추가](./media/active-directory-domain-services-getting-started/add-group-members-button.png)

9. **멤버 추가** 대화 상자에서 이 그룹의 멤버여야 하는 사용자를 선택하고 완료하면 확인란을 선택합니다.

    ![관리자 그룹에 사용자 추가](./media/active-directory-domain-services-getting-started/add-group-members.png)

<br>

## <a name="task-2:-create-or-select-an-azure-virtual-network"></a>작업 2: Azure 가상 네트워크 만들기 또는 선택
다음 구성 태스크에서는 [Azure 가상 네트워크를 만들거나 선택](active-directory-ds-getting-started-vnet.md)합니다.



<!--HONumber=Oct16_HO2-->


