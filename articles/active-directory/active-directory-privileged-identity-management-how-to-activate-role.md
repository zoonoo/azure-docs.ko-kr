<properties
   pageTitle="역할을 활성화하거나 비활성화하는 방법 | Microsoft Azure"
   description="Azure Privileged Identity Management 응용 프로그램을 사용하여 권한 있는 ID에 대한 역할을 활성화하는 방법을 알아봅니다."
   services="active-directory"
   documentationCenter=""
   authors="kgremban"
   manager="stevenpo"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="05/19/2016"
   ms.author="kgremban"/>

# Azure AD Privileged Identity Management 역할을 활성화하거나 비활성화하는 방법

Azure AD(Active Directory) Privileged Identity Management를 사용하면 기업이 Azure AD 및 기타 Microsoft Online Services(Office 365 또는 Microsoft Intune 등)에서 권한 있는 ID와 리소스에 대한 액세스를 관리하는 방법을 간소화합니다.

이 문서는 Azure AD PIM(Privileged Identity Management)에서 해당 역할을 활성화해야 하는 관리자를 위해 작성되었습니다. 사용 권한이 필요할 때 역할을 활성화하고 완료 시 역할을 비활성화하는 단계를 안내합니다.

관리 역할에 할당되면 해당 역할에 필요한 작업을 수행해야 할 경우 해당 역할을 활성화할 수 있습니다. 예를 들어 경우에 따라 Office 365만 관리해야 하는 경우 조직의 권한 있는 역할 관리자는 영구 관리자 역할을 할당하려 하지 않습니다. 대신, Azure AD에서 전역 관리자 또는 Exchange Online 관리자 역할에 대한 후보로 지정합니다. 즉, 이러한 권한이 필요한 경우 임시 역할 할당을 요청할 수 있고 미리 결정된 기간에 대해 Office 365에 대한 관리자 제어가 가능합니다.


## Privileged Identity Management 응용 프로그램 추가

다른 포털 또는 PowerShell을 통해 작동하려는 경우에도 [Azure 포털](https://portal.azure.com/)에서 Azure AD Privileged Identity Management 응용 프로그램을 사용하여 역할 "활성화"를 요청합니다. Azure 포털에 Azure AD Privileged Identity Management 응용 프로그램이 없는 경우 다음 단계에 따라 시작합니다.

1. 아직 로그인하지 않은 경우 [Azure 포털](https://portal.azure.com/)에 로그인합니다.
2. 조직에 둘 이상의 디렉터리가 있는 경우 Azure 포털의 오른쪽 위에서 사용자 이름을 클릭하고 작동할 디렉터리를 선택합니다.
3. 왼쪽 탐색 창에서 **새로 만들기** 아이콘을 클릭합니다.
4. 만들기 메뉴에서 **보안 + ID**를 선택합니다.
5. **Azure AD Privileged Identity Management**를 선택합니다.
6. **대시보드에 고정** 옵션을 선택하고 **만들기** 단추를 클릭합니다. Privileged Identity Management 응용 프로그램이 열립니다.

## 역할 활성화

역할을 사용해야 할 경우 Azure AD Privileged Identity Management 응용 프로그램의 **내 역할 활성화** 단추를 사용하여 활성화를 요청할 수 있습니다.


1. [Azure 포털](https://portal.azure.com/)에 로그인하고 Azure AD Privileged Identity Management 타일을 선택합니다.
2. **내 역할 활성화**를 선택합니다. 할당된 역할 목록이 나타납니다.
3. 활성화하려는 역할을 선택합니다.
4. **활성화**를 선택합니다. **역할 활성화 요청** 블레이드가 나타납니다.
5. 전역 관리자와 같은 일부 역할의 경우 역할을 활성화하는 데 MFA(Multi-Factor Authentication)가 필요합니다. 로그인할 때 MFA를 수행해야 역할을 활성화할 수 있습니다.
6. 텍스트 필드에 활성화 요청 이유를 입력합니다. 권한 있는 역할 관리자에게 문제 티켓 번호를 제공해야 할 수도 있습니다.
7. **확인**을 선택합니다. 이제 Microsoft Online Services에 역할이 활성화되고 역할 변경이 표시됩니다.

## 역할 비활성화

역할이 활성화된 후 시간 제한에 도달하면 자동으로 비활성화됩니다.

먼저 작업을 완료한 경우 Azure AD Privileged Identity Management 응용 프로그램에서 수동으로 역할을 비활성화할 수 있습니다. **내 역할 활성화**를 선택하고 현재 더 이상 필요 없는 역할을 찾아서 **비활성화**를 선택합니다.


<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## 다음 단계

Azure AD Privileged Identity Management에 대한 자세한 정보는 다음 링크를 참조하세요.

[AZURE.INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!---HONumber=AcomDC_0525_2016-->