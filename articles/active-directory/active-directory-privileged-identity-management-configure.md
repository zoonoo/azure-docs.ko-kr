<properties
	pageTitle="Azure AD Privileged Identity Management | Microsoft Azure "
	description="Azure AD Privileged Identity Management가 무엇이고 클라우드 보안을 개선하기 위한 PIM 사용 방법을 설명하는 항목입니다."
	services="active-directory"
	documentationCenter=""
	authors="kgremban"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/19/2016"
	ms.author="kgremban"/>

# Azure AD 권한 있는 ID 관리

Azure AD(Active Directory) Privileged Identity Management를 사용하여 Azure AD 및 기타 Microsoft Online Services(Office 365 또는 Microsoft Intune 등)에서 권한 있는 ID를 관리, 제어, 모니터링하고 리소스에 액세스할 수 있습니다.

경우에 따라 사용자는 Azure, Office 365 리소스 또는 기타 SaaS 앱에서 권한이 필요한 작업을 수행해야 합니다. 이는 보통 조직이 사용자에게 Azure AD에서 영구 권한 있는 액세스를 제공해야 함을 의미합니다. 조직은 사용자가 관리자 권한으로 수행하는 작업을 충분히 모니터링할 수 없으므로 클라우드에 호스트된 리소스의 보안 위험이 증가합니다. 또한 권한 있는 액세스가 있는 사용자 계정이 손상되면 이로 인해 전반적인 클라우드 보안에 영향을 줄 수 있습니다. Azure AD 권한 있는 ID 관리는 이 위험을 해결하는 데 도움이 됩니다.

Azure AD Privileged Identity Management를 통해 다음을 할 수 있습니다.

- Azure AD 관리자인 사용자를 확인할 수 있습니다.
- Office 365 및 Intune 등의 Microsoft Online Services에 대해 주문형으로 “Just-In-Time”에 관리 권한을 사용하도록 설정할 수 있습니다.
- 관리자 액세스 기록 및 관리자 할당 변경에 대한 보고서 가져오기
- 권한 있는 역할의 액세스에 대한 알림을 받을 수 있습니다.

Azure AD Privileged Identity Management는 다음을 포함하여 기본 제공된 Azure AD 조직 역할을 관리할 수 있습니다.

- 전역 관리자
- 대금 청구 관리자
- 서비스 관리자  
- 사용자 관리자
- 암호 관리자

## 시간에 맞추는 관리자 액세스

지금까지는 이전의 Azure 관리 포털 또는 Windows PowerShell을 통해 사용자를 관리자 역할에 할당할 수 있었습니다. 그 결과, 해당 사용자는 역할에 대한 **영구 관리자**가 되어 할당된 역할에 항상 활성 상태가 됩니다. Azure AD Privileged Identity Management는 할당된 역할에 대한 활성화 프로세스를 완료해야 하는 사용자인, 역할에 대한 **임시 관리자**의 개념을 소개합니다. 활성화 프로세스는 Azure AD에서 할당된 사용자 역할을 지정된 시간(예: 8시간) 동안 비활성에서 활성 상태로 변경합니다.

## 디렉터리에서 Privileged Identity Management 사용

[Azure 포털](https://portal.azure.com/)에 액세스하여 Azure AD 권한 있는 ID 관리를 사용할 수 있습니다. Azure AD Privileged Identity Management는 이전 클래식 포털에 나타나지 않습니다.

>[AZURE.NOTE] 디렉터리에 대해 Azure AD Privileged Identity Management를 사용하려면 Microsoft 계정이 아닌 조직 계정의 전역 관리자여야 합니다.

1. 해당 디렉터리의 전역 관리자로 [Azure 포털](https://portal.azure.com/)에 로그인합니다.
2. 조직에 둘 이상의 디렉터리가 있는 경우 Azure 포털의 오른쪽 위에서 사용자 이름을 클릭하고 Azure AD Privileged Identity Management를 사용할 디렉터리를 선택합니다.
3. 왼쪽 탐색 창에서 **새로 만들기** 아이콘을 클릭합니다.
4. **보안 + ID**를 선택합니다.
5. **Azure AD Privileged Identity Management**를 선택합니다.
6. **대시보드에 고정** 옵션을 선택하고 **만들기** 단추를 클릭합니다. 권위 있는 ID 관리 대시보드가 열립니다.

디렉터리에서 Azure AD Privileged Identity Management를 처음 사용하는 사용자이면 [보안 마법사](active-directory-privileged-identity-management-security-wizard.md)가 초기 할당 환경을 안내합니다. 이 작업 이후 자동으로 디렉터리의 첫 번째 **보안 관리자** 및 **권한 있는 역할 관리자**가 됩니다.

권한 있는 역할 관리자만 PIM 앱을 사용하여 다른 관리자의 액세스 권한을 관리할 수 있습니다. [PIM 관리 기능을 다른 사용자에게 제공](active-directory-privileged-identity-management-how-to-give-access-to-pim.md)할 수 있습니다.

## 권한 있는 ID 관리 대시보드

Azure AD 권한 있는 ID 관리자는 다음과 같은 중요한 정보를 전달하는 대시보드를 제공합니다.

- 각 권한 있는 역할에 할당된 사용자 수  
- 임시 및 영구 관리자 수
- 각 관리자의 액세스 기록

![PIM 대시보드 - 스크린샷][2]

## 권한 있는 역할 관리

Azure AD 권한 있는 ID 관리로 각 역할에 영구 또는 임시 관리자를 추가하거나 제거하여 관리자를 관리할 수 있습니다.

![PIM 추가/제거 관리자 - 스크린샷][3]

## 역할 활성화 설정 구성

역할 활성화 설정을 사용하여 다음을 포함한 임시 역할 활성화 속성을 구성할 수 있습니다.

- 역할 활성화 기간
- 역할 활성화 알림
- 역할 활성화 프로세스 중 사용자가 제공해야 하는 정보  

![PIM 설정 - 관리자 활성화 - 스크린샷][4]

## 역할 활성화  

역할을 활성화하려면 임시 관리자가 역할에 대해 시간 제한이 있는 "활성화"를 요청해야 합니다. Azure AD 권한 있는 ID 관리에서 **내 역할 활성화** 옵션을 사용하여 활성화를 요청할 수 있습니다.

역할을 활성화하려는 관리자는 Azure 포털에서 Azure AD 권한 있는 ID 관리를 초기화해야 합니다.

어떤 유형의 관리자든 Azure AD 권한 있는 ID 관리를 사용하여 자신의 역할을 활성화할 수 있습니다.

역할 활성화는 시간 제한이 있습니다. 역할 활성화 설정에서 역할을 활성화하기 위해 관리자가 제공해야 하는 필수 정보뿐만 아니라 활성화 길이도 설정할 수 있습니다.

![PIM 관리자 요청 역할 활성화 - 스크린샷][5]

## 역할 활성화 기록

Azure AD Privileged Identity Management를 사용하여 권한 있는 역할 할당 및 역할 활성화 기록의 변경 내용을 추적할 수도 있습니다. 이는 다음 감사 로그 옵션을 사용하여 수행할 수 있습니다.

![PIM 활성화 기록 - 스크린샷][6]

## 다음 단계
[AZURE.INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!--Image references-->

[2]: ./media/active-directory-privileged-identity-management-configure/PIM_Dash.png
[3]: ./media/active-directory-privileged-identity-management-configure/PIM_AddRemove.png
[4]: ./media/active-directory-privileged-identity-management-configure/PIM_RoleActivationSettings.png
[5]: ./media/active-directory-privileged-identity-management-configure/PIM_RequestActivation.png
[6]: ./media/active-directory-privileged-identity-management-configure/PIM_ActivationHistory.png

<!---HONumber=AcomDC_0525_2016-->