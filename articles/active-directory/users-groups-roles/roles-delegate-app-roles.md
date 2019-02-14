---
title: 애플리케이션 관리자 역할 위임 - Azure Active Directory | Microsoft Docs
description: 역할을 위임하여 Azure Active Directory의 권한을 부여하는 애플리케이션 액세스 관리
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.component: users-groups-roles
ms.topic: article
ms.date: 01/31/2019
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 316ebb7774a0420087bf9ed5c099a91d78a96fb0
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/13/2019
ms.locfileid: "56181221"
---
# <a name="delegate-app-administrator-roles-in-azure-active-directory"></a>Azure Active Directory에서 앱 관리자 역할 위임

 Azure AD를 사용하면 기본 제공 관리 역할 집합에 애플리케이션 액세스 관리를 위임할 수 있습니다. 전역 관리자 오버헤드를 줄이는 것 외에도, 애플리케이션 액세스 작업 관리를 위한 특수 권한을 위임하면 보안 상태를 향상하고 무단 액세스 가능성을 줄일 수 있습니다. 위임 문제 및 일반 지침은 [Azure Active Directory의 관리 위임](roles-concept-delegation.md)에서 설명합니다.

## <a name="delegate-app-administration"></a>앱 관리 위임

다음 역할은 애플리케이션 등록, Single Sign-On 설정, 사용자 및 그룹 할당을 관리하고 위임된 권한 및 애플리케이션 권한에 동의할 수 있는 권한을 부여합니다(Microsoft Graph 및 Azure AD Graph 제외). 유일한 차이점은 애플리케이션 관리자 역할의 경우 애플리케이션 프록시 설정을 관리할 수 있는 권한도 부여한다는 것입니다. 두 역할 모두, 조건부 액세스 설정을 관리하는 기능은 부여하지 않습니다.
> [!IMPORTANT]
> 이 역할이 할당된 사용자는 애플리케이션에 자격 증명을 추가하고 해당 자격 증명을 사용하여 애플리케이션 ID를 가장할 수 있습니다. 이 애플리케이션 ID 가장은 사용자가 Azure AD의 다른 역할 할당을 통해 수행할 수 있는 작업에 대한 권한 상승일 수 있습니다. 이 역할에 할당된 사용자는 애플리케이션을 가장하는 동안 사용자 또는 다른 개체를 만들거나 업데이트할 수 있습니다.

Azure Portal에서 애플리케이션 액세스 관리 기능을 부여하려면 다음을 수행합니다.

1. 테넌트의 전역 관리자 역할에 적합한 계정으로 [Azure AD 테넌트](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview)에 로그인합니다.
2. 충분한 권한이 있는 경우 [역할 및 관리자 페이지](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RolesAndAdministrators)를 엽니다.
3. 다음 역할 중 하나를 열어 해당 멤버 할당을 확인합니다.
  * **애플리케이션 관리자**
  * **클라우드 애플리케이션 관리자**
4. 역할의 **멤버** 페이지에서 **멤버 추가**를 선택합니다.
5. 역할에 추가할 멤버를 하나 이상 선택합니다. <!--Members can be users or groups.-->

[사용 가능한 역할](directory-assign-admin-roles.md#available-roles)에서 이러한 역할에 대한 설명을 확인할 수 있습니다.

## <a name="delegate-app-registration"></a>앱 등록 위임

기본적으로 모든 사용자가 애플리케이션 등록을 만들 수 있지만, 애플리케이션 등록을 만들 수 있는 권한이나 앱 인증에 동의할 수 있는 권한을 선택적으로 부여할 수 있습니다.

1. 테넌트의 전역 관리자 역할에 적합한 계정으로 [Azure AD 테넌트](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview)에 로그인합니다.
2. 충분한 권한을 얻은 경우 다음 중 하나 또는 둘 다를 설정합니다.
  * [테넌트의 사용자 설정 페이지](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/UserSettings)에서 **사용자가 애플리케이션을 등록할 수 있음**을 아니요로 설정합니다.
  * [엔터프라이즈 애플리케이션의 사용자 설정](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/UserSettings/menuId/)에서 **사용자는 애플리케이션이 사용자 대신 회사 데이터에 액세스하도록 동의할 수 있음**을 아니요로 설정합니다.
3. 그런 다음, 이 권한이 필요한 사용자를 필요에 따라 애플리케이션 개발자 역할의 멤버로 할당합니다.

애플리케이션을 등록하는 사용자가 자동으로 애플리케이션의 첫 번째 소유자로 추가됩니다.

## <a name="delegate-app-ownership"></a>앱 소유권 위임

앱 소유자와 앱 등록 소유자는 각각 자신이 소유한 앱 애플리케이션 또는 앱 등록만 관리할 수 있습니다. 예를 들어 Salesforce 애플리케이션의 소유자를 추가하는 경우 해당 소유자는 Salesforce에 대한 액세스 및 구성을 관리할 수 있지만 다른 애플리케이션에 대해서는 관리할 수 없습니다. 한 앱에 많은 소유자가 있을 수 있으며, 한 사용자가 많은 앱의 소유자가 될 수도 있습니다.

애플리케이션 소유자는 다음을 수행할 수 있습니다.

* 이름, 앱이 요청하는 권한 등의 애플리케이션 속성 변경
* 자격 증명 관리
* Single Sign-On 구성
* 사용자 액세스 할당
* 다른 소유자 추가 또는 제거
* 앱 매니페스트 편집
* 앱 갤러리에 앱 게시

> [!IMPORTANT]
> 이 역할이 할당된 사용자는 애플리케이션에 자격 증명을 추가하고 해당 자격 증명을 사용하여 애플리케이션 ID를 가장할 수 있습니다. 이 애플리케이션 ID 가장은 사용자가 Azure AD의 다른 역할 할당을 통해 수행할 수 있는 작업에 대한 권한 상승일 수 있습니다. 이 역할에 할당된 사용자는 애플리케이션을 가장하는 동안 사용자 또는 다른 개체를 만들거나 업데이트할 수 있습니다.

앱 등록의 소유자는 앱 등록을 보고 편집할 수 있습니다.

<!-- ### To assign an enterprise app ownership role to a user

1. Sign in to your [Azure AD tenant](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) with an account that is the Global Administrator for the tenant.
2. On the [Roles and administrators page](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RolesAndAdministrators), open one of the following roles to see its member assignments:
  * **Enterprise Application Owner**
  * **Application Registration Owner**
3. On the **Members** page for the role, select **Add member**.
4. Select one or more members to add to the role. -->

### <a name="to-assign-an-owner-to-an-application"></a>애플리케이션에 소유자를 할당하려면 다음을 수행합니다.

1. 테넌트의 애플리케이션 관리자 또는 클라우드 애플리케이션 관리자에 적합한 계정으로 [Azure AD 테넌트](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview)에 로그인합니다.
2. 테넌트의 [앱 등록 페이지](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/AllApps/menuId/)에서 앱을 선택하여 앱의 **개요** 페이지를 엽니다.
3. **소유자**를 선택하여 앱의 소유자 목록을 확인합니다.
4. **추가**를 선택하여 앱에 추가할 소유자를 하나 이상 선택합니다.

### <a name="to-assign-an-owner-to-an-application-registration"></a>애플리케이션 등록에 소유자를 할당하려면 다음을 수행합니다.

1. 테넌트의 애플리케이션 관리자 또는 클라우드 애플리케이션 관리자 역할에 적합한 계정으로 [Azure AD 테넌트](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview)에 로그인합니다.
2. 충분한 권한이 있는 경우 테넌트의 [엔터프라이즈 애플리케이션 페이지](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/AllApps/menuId/)에서 앱 등록을 선택하여 엽니다.
3. **설정**을 선택합니다.
4. **설정** 페이지에서 **소유자**를 선택하여 앱의 소유자 목록을 확인합니다.
5. **소유자 추가**를 선택하여 앱에 추가할 소유자를 하나 이상 선택합니다.

## <a name="next-steps"></a>다음 단계

* [Azure AD 관리자 역할 참조](directory-assign-admin-roles.md)
