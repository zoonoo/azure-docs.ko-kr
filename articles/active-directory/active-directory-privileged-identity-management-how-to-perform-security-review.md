<properties
   pageTitle="보안 검토를 수행하는 방법 | Microsoft Azure"
   description="Azure Privileged Identity Management 응용 프로그램을 사용하여 검토를 수행하는 방법을 알아봅니다."
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

# Azure AD Privileged Identity Management에서 보안 검토를 수행하는 방법

Azure AD(Active Directory) Privileged Identity Management를 사용하면 기업이 Azure AD 및 기타 Microsoft Online Services(Office 365 또는 Microsoft Intune 등)에서 권한 있는 ID와 리소스에 대한 액세스를 관리하는 방법을 간소화합니다.

관리 역할에 할당된 경우 조직의 보안 관리자가 정기적으로 검토하도록 요청하고 작업에 해당 역할이 여전히 필요한지 확인할 수 있습니다. 링크가 포함된 메일을 받거나 [Azure 포털](https://portal.azure.com)로 바로 이동할 수 있습니다. 할당된 역할을 자체 검토하려면 이 문서의 단계를 따릅니다.

보안 검토에 관심이 있는 보안 관리자인 경우 [보안 검토를 시작하는 방법](active-directory-privileged-identity-management-how-to-start-security-review.md)에서 자세한 내용을 참조하세요.

## Privileged Identity Management 응용 프로그램 추가

[Azure 포털](https://portal.azure.com/)에서 Azure AD PIM(Privileged Identity Management) 응용 프로그램을 사용하여 검토를 수행할 수 있습니다. Azure AD Privileged Identity Management 응용 프로그램이 포털에 없는 경우 다음 단계에 따라 시작합니다.

1. 아직 로그인하지 않은 경우 [Azure 포털](https://portal.azure.com/)에 로그인합니다.
2. 조직에 둘 이상의 디렉터리가 있는 경우 Azure 포털의 오른쪽 위에서 사용자 이름을 클릭하고 작동할 디렉터리를 선택합니다.
3. 왼쪽 탐색 창에서 **새로 만들기** 아이콘을 선택합니다.
4. 메뉴에서 **보안 + ID**를 선택합니다.
5. **Azure AD Privileged Identity Management**를 선택합니다.
6. **대시보드에 고정** 옵션을 선택하고 **만들기** 단추를 클릭합니다. Privileged Identity Management 응용 프로그램이 열립니다.


## 액세스 승인 또는 거부

액세스 권한은 [검토가 완료](active-directory-privileged-identity-management-how-to-complete-review.md)될 때까지 변경되지 않습니다. 이 프로세스는 역할에 대한 액세스를 변경하는 담당자를 위한 검사 목록만 생성합니다. 최소 한 명의 사용자를 선택하면 **Approve access(액세스 승인)** 및 **Deny access(액세스 거부)** 단추를 사용할 수 있습니다.

1. PIM 응용 프로그램의 주 메뉴에서 **관리 액세스 검토**를 클릭합니다. 보안 리뷰 목록이 표시됩니다.
2. 목록에서 액세스를 변경할 **사용자**를 선택합니다.
3. 선택한 사용자에 대해 **Approve access**(액세스 승인) 또는 **Deny access**(액세스 거부)를 클릭합니다. Azure 포털 주 메뉴에 알림이 표시되고 검토 목록에서 선택한 이름은 사라집니다(필터 옵션을 변경하여 다시 표시할 수 있음). **Review Azure AD roles**(Azure AD 역할 검토)를 닫습니다.


<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## 다음 단계
[AZURE.INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!---HONumber=AcomDC_0420_2016-->