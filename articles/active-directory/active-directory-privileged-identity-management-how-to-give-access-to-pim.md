<properties
   pageTitle="PIM에 대한 액세스 권한을 제공하는 방법 | Microsoft Azure"
   description="PIM을 관리할 수 있도록 Azure Active Directory Privileged Identity Management 확장을 사용하여 사용자에 역할을 추가하는 방법을 알아봅니다."
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
   ms.date="04/15/2016"
   ms.author="kgremban"/>

# Azure AD Privileged Identity Management를 관리하기 위해 액세스 권한을 제공하는 방법

조직에 대한 Azure AD PIM(Privileged Identity Management)을 사용하도록 설정하는 첫 번째 사용자는 전역 관리자여야 합니다. 그러나 다른 전역 관리자는 기본적으로 PIM에 액세스하지 않으므로 임시 할당을 관리할 수 없습니다. PIM에 대한 액세스를 제공하기 위해 첫 번째 사용자가 다른 사용자를 보안 관리자 역할에 할당할 수 있습니다. 이 할당은 PIM 자체 내에서 수행해야 하고 PowerShell 또는 다른 포털을 통해 변경할 수 없습니다.

> [AZURE.NOTE] Azure AD PIM 관리에 Azure MFA가 필요합니다. Azure MFA에 대해 Microsoft 계정을 등록할 수 없기 때문에 Microsoft 계정으로 로그인하는 사용자는 Azure AD PIM에 액세스할 수 없습니다.

한 명의 사용자가 잠긴 경우 또는 해당 계정이 삭제된 경우 최소한 두 사용자가 항상 보안 관리자 역할에 있어야 합니다.

## PIM을 관리하기 위해 다른 사용자에게 액세스 권한 제공

1. [Azure 포털](https://portal.azure.com/)에 로그인하고 대시보드에서 **Azure AD Privileged Identity Management** 앱을 선택합니다.
2. **보안 관리자** 역할을 선택합니다. 현재 해당 역할 상태인 사용자 목록이 표시됩니다.
3. **추가**를 클릭하면 마법사 블레이드가 표시됩니다. 역할이 이미 선택되어 있습니다.
4. **사용자 선택**을 클릭하면 사용자 목록 블레이드가 열립니다.
5. 검색 필드에 사용자의 이름을 입력합니다. 사용자가 디렉터리 내에 있으면 입력하는 동안 해당 계정이 표시됩니다.
6. 검색 결과에서 사용자를 선택하고 **완료**를 클릭합니다.
7. **확인**을 클릭하여 선택 내용을 저장합니다. 선택한 사용자가 목록에 표시되며 역할은 기본적으로 임시 지정됩니다.

  - 역할을 영구적으로 지정하려면 목록에서 사용자를 클릭합니다. 사용자의 정보가 새 블레이드에 표시됩니다. 사용자 정보 메뉴에서 **make perm**(영구 지정)을 선택합니다.

8. [Azure AD Privileged Identity Management 시작](active-directory-privileged-identity-management-getting-started.md)의 Azure 설명서에 대한 링크를 사용자에게 보냅니다.


## PIM 관리에 대한 다른 사용자의 액세스 권한 제거

보안 관리자 역할에서 사용자를 제거하기 전에 항상 두 사용자가 이 역할에 할당되어 있는지 확인합니다.

1. PIM 대시보드에서 역할 **보안 관리자**를 클릭합니다. 현재 해당 역할 상태인 사용자 목록이 표시됩니다.
2. 사용자 목록에서 사용자를 클릭합니다.
3. **제거**를 클릭합니다. 확인 메시지가 나타납니다.
4. 사용자로부터 역할을 제거하려면 **예**를 클릭합니다.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## 다음 단계
[AZURE.INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!---HONumber=AcomDC_0420_2016-->