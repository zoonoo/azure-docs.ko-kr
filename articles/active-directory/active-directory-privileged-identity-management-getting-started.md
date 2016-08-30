<properties
   pageTitle="Azure AD Privileged Identity Management 시작 | Microsoft Azure"
   description="Azure 포털에서 Azure Active Directory Privileged Identity Management 응용 프로그램을 사용하여 권한 있는 ID를 관리하는 방법을 알아봅니다."
   services="active-directory"
   documentationCenter=""
   authors="kgremban"
   manager="femila"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="06/29/2016"
   ms.author="kgremban"/>

# Azure AD Privileged Identity Management 시작


Azure AD(Active Directory) Privileged Identity Management를 사용하여 Azure AD 및 기타 Microsoft Online Services(Office 365 또는 Microsoft Intune 등)에서 권한 있는 ID를 관리, 제어, 모니터링하고 리소스에 액세스할 수 있습니다.

이 문서에서는 Azure AD PIM 앱을 Azure 포털 대시보드에 추가하는 방법을 알려줍니다.

## Privileged Identity Management 응용 프로그램 추가

Azure AD Privileged Identity Management를 사용하기 전에 응용 프로그램을 Azure 포털 대시보드에 추가해야 합니다.

1. 해당 디렉터리의 전역 관리자로 [Azure 포털](https://portal.azure.com/)에 로그인합니다.
2. 조직에 둘 이상의 디렉터리가 있는 경우 Azure 포털의 오른쪽 위에서 사용자 이름을 클릭하고 PIM을 사용할 디렉터리를 선택합니다.
3. **새로 만들기** > **보안 + ID** > **Azure AD Privileged Identity Management**를 선택합니다.

    ![포털에서 PIM 사용][1]

4. **대시보드에 고정** 옵션을 선택하고 **만들기**를 클릭합니다. Privileged Identity Management 응용 프로그램이 열립니다.


디렉터리에서 Azure AD Privileged Identity Management를 처음 사용하는 사용자이면 [보안 마법사](active-directory-privileged-identity-management-security-wizard.md)가 초기 할당 환경을 안내합니다. 이 작업 이후 자동으로 디렉터리의 첫 번째 **보안 관리자** 및 **권한 있는 역할 관리자**가 됩니다. 권한 있는 역할 관리자만 이 응용 프로그램에 액세스하여 다른 관리자의 액세스 권한을 관리할 수 있습니다.

하나 이상의 역할에 할당된 경우 **내 역할을 활성화**하는 옵션이 있습니다. 또한 권한 있는 역할 관리자인 경우 **권한 있는 역할을 관리**하는 옵션을 볼 수 있습니다.


<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## 다음 단계

[Azure AD Privileged Identity Management 개요](active-directory-privileged-identity-management-configure.md)에는 조직의 관리 액세스 권한을 관리할 수 있는 방법에 대한 자세한 정보가 포함되어 있습니다.

[AZURE.INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!--Image references-->

[1]: ./media/active-directory-privileged-identity-management-configure/PIM_EnablePim.png

<!-----HONumber=AcomDC_0706_2016-->