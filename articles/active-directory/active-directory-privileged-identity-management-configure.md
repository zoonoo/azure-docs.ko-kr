<properties
	pageTitle="Azure AD 권한 있는 ID 관리"
	description="Azure AD 권한 있는 ID 관리의 정의 및 구성 방법을 설명하는 항목입니다."
	services="active-directory"
	documentationCenter=""
	authors="IHenkel"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/21/2015"
	ms.author="inhenk"/>

# Azure AD 권한 있는 ID 관리

Azure AD 권한 있는 ID 관리를 사용하여 Azure AD 및 기타 Microsoft 온라인 서비스(Office 365 또는 Microsoft Intune 등)에서 권한 있는 ID 및 리소스에 대한 액세스를 관리, 제어, 모니터링할 수 있습니다.

사용자가 권한이 필요한 작업을 수행할 수 있도록 조직에서는 Azure AD에서나 Azure 또는 Office 365 리소스에 대해서나 기타 SaaS 앱에 대해 영구 액세스 권한을 해당 사용자에게 자주 제공해야 합니다. 고객이 많은 경우 사용자가 관리자 권한으로 수행하는 작업을 충분히 모니터링할 수 없으므로 클라우드에 호스트된 리소스의 보안 위험이 증가합니다. 또한 액세스 권한이 있는 손상된 사용자 계정이 전반적인 클라우드 보안에 영향을 줄 수 있습니다. Azure AD 권한 있는 ID 관리는 이 위험을 해결하는 데 도움이 됩니다.

이 미리 보기의 Azure AD 권한 있는 ID 관리를 통해 다음을 수행할 수 있습니다.

- Azure AD 관리자인 사용자를 찾을 수 있습니다.
- 디렉터리 리소스에 대해 주문형으로 "적시에" 관리 권한을 사용하도록 설정할 수 있습니다.
- 관리자 액세스 기록 및 관리자 할당 변경에 대한 보고서를 얻을 수 있습니다.
- 권한 있는 역할의 액세스에 대한 알림을 받을 수 있습니다.

이 미리 보기에서 Azure AD 권한 있는 ID 관리는 다음 기본 제공 Azure Active Directory 조직 역할을 관리할 수 있습니다.

- 전역 관리자
- 대금 청구 관리자
- 서비스 관리자  
- 사용자 관리자
- 암호 관리자

## 시간에 맞추는 관리자 액세스

지금까지 Azure 관리 포털 또는 Windows PowerShell을 통해 사용자를 관리자 역할에 할당할 수 있었습니다. 그 결과, 해당 사용자는 **영구 관리자**가 되어 할당된 역할에 항상 활성 상태가 됩니다. 이 미리 보기에서는 할당된 역할에 대한 활성화 프로세스를 완료해야 하는 사용자인 **임시 관리자**에 대한 지원을 추가합니다. 활성화 프로세스는 Azure AD에서 할당된 사용자 역할을 비활성에서 활성으로 변경합니다.

## 디렉터리에 권한 있는 ID 관리 사용

[Microsoft Azure 포털](https://portal.azure.com/)에 액세스하여 Azure AD 권한 있는 ID 관리를 사용할 수 있습니다. 현재 Azure AD 권한 있는 ID 관리만 Microsoft Azure 포털에 나타납니다. 디렉터리에 Azure AD 권한 있는 ID 관리를 사용하려면 전역 관리자여야 합니다.

![][1]

이 확장 프로그램을 초기화하고 나면 자동으로 디렉터리의 첫 번째 **보안 관리자**가 됩니다. 보안 관리자만 이 확장 프로그램에 액세스하여 다른 관리자의 액세스 권한을 관리할 수 있습니다. 초기화하는 동안 Azure AD 권한 있는 ID 관리의 타일이 Azure Preview 포털의 시작 보드에 추가됩니다.

## 권한 있는 ID 관리 대시보드

Azure AD 권한 있는 ID 관리자는 다음과 같은 중요한 정보를 전달하는 대시보드를 제공합니다.

- 각 권한 있는 역할에 할당된 사용자 수  
- 임시 및 영구 관리자 수
- 관리자의 액세스 기록

![][2]

## 권한 있는 역할 관리

Azure AD 권한 있는 ID 관리로 각 역할에 영구 또는 임시 관리자를 추가하거나 제거하여 관리자를 관리할 수 있습니다.

![][3]

## 역할 활성화 설정 구성

역할 활성화 설정을 사용하여 다음을 포함한 임시 역할 활성화 속성을 구성할 수 있습니다.

- 역할 활성화 기간
- 역할 활성화 알림
- 역할 활성화 프로세스 중 사용자가 제공해야 하는 정보  

![][4]

## 역할 활성화  

역할을 활성화하려면 임시 관리자가 역할에 대해 시간 제한이 있는 "활성화"를 요청해야 합니다. Azure AD 권한 있는 ID 관리에서 **내 역할 활성화** 옵션을 사용하여 활성화를 요청할 수 있습니다.

역할을 활성화하려는 관리자는 Azure Preview 포털에서 Azure AD 권한 있는 ID 관리를 초기화해야 합니다.

어떤 유형의 관리자든 Azure AD 권한 있는 ID 관리를 사용하여 자신의 역할을 활성화할 수 있습니다.

역할 활성화는 시간 제한이 있습니다. 역할 활성화 설정에서 역할을 활성화하기 위해 관리자가 제공해야 하는 필수 정보뿐만 아니라 활성화 길이도 구성할 수 있습니다.

![][5]

## 역할 활성화 기록

Azure AD 권한 있는 ID 관리를 사용하여 권한 있는 역할 할당 및 역할 활성화 기록의 변경 내용을 추적할 수도 있습니다. 이는 다음 감사 로그 옵션을 사용하여 수행할 수 있습니다.

![][6]

## 다음 단계
[AZURE.INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!--Image references-->
[1]: ./media/active-directory-privileged-identity-management-configure/Search_PIM.png
[2]: ./media/active-directory-privileged-identity-management-configure/PIM_Dash.png
[3]: ./media/active-directory-privileged-identity-management-configure/PIM_AddRemove.png
[4]: ./media/active-directory-privileged-identity-management-configure/PIM_RoleActivationSettings.png
[5]: ./media/active-directory-privileged-identity-management-configure/PIM_RequestActivation.png
[6]: ./media/active-directory-privileged-identity-management-configure/PIM_ActivationHistory.png

<!---HONumber=Oct15_HO3-->