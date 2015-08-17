
<properties
    pageTitle="Azure RemoteApp에서 Azure Active Directory 테넌트 변경"
    description="Azure RemoteApp과 연결된 Azure Active Directory 테넌트를 변경하는 방법에 대해 알아봅니다."
    services="remoteapp"
	documentationCenter="" 
    authors="lizap"
    manager="mbaldwin" />

<tags
    ms.service="remoteapp"
    ms.workload="compute"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="06/24/2015"
    ms.author="elizapo" />



# Azure RemoteApp에서 Azure Active Directory 테넌트 변경

Azure RemoteApp은 Azure AD(Azure Active Directory)를 사용하여 사용자 액세스를 허용합니다. 사용할 수 있는 Azure AD 테넌트는 Azure 구독과 연결된 것입니다. 포털의 설정 페이지에서 연결된 구독을 볼 수 있습니다. 구독 탭에서 디렉터리 열을 확인합니다.

> [AZURE.NOTE]Azure Active Directory를 변경하려면 먼저 모든 Azure RemoteApp 컬렉션에서 기존 Azure Active Directory 테넌트의 사용자를 모두 제거해야 합니다. 이렇게 하려면 Azure 포털로 이동한 다음 Azure RemoteApp 탭으로 이동하여 모든 Azure RemoteApp 컬렉션을 엽니다. **사용자** 탭으로 이동하여 현재 Azure Active Directory 테넌트에 속하는 사용자를 제거합니다. 모든 기존 Azure RemoteApp 컬렉션에 대해 이 작업을 반복합니다. 이 작업을 수행하지 않으면 컬렉션을 만들거나 패치할 수 없습니다.

다른 테넌트를 사용하려는 경우 다음 단계를 사용하여 구독과의 연결을 변경합니다.

1. 포털에서 Azure RemoteApp 컬렉션에 대한 액세스 권한을 부여한 Azure AD 사용자를 제거합니다.


2. 서비스 관리자로 Microsoft 계정(이전의 Live ID)를 설정합니다. **설정 -> 관리자**를 참조합니다.
	1. 오른쪽 위 모서리에서 현재 로그인된 사용자를 클릭한 다음 **내 청구서 보기**를 클릭합니다.
	2. 구독을 선택한 다음 **구독 세부 정보 편집**을 클릭합니다.
	3. 필요하다면 변경합니다.



3. 포털에서 로그아웃하여 이전 단계에서 지정한 Microsoft 계정으로 다시 로그인합니다.


4. **새로 만들기 -> 앱 서비스 -> Active Directory -> 사용자 지정 만들기 -> 기존 디렉터리 사용**을 클릭합니다. 이 구독과 연결하려는 Azure AD 테넌트를 추가합니다.


5. **설정 -> 구독**에서 구독을 선택한 다음 **디렉터리 편집**을 클릭합니다. 사용하려는 Azure AD 테넌트를 선택합니다.



이제 새 Azure AD 테넌트를 사용하여 Azure 구독에 대한 액세스를 제어하고 Azure RemoteApp에서 사용자 액세스를 구성할 수 있습니다.

<!---HONumber=August15_HO6-->