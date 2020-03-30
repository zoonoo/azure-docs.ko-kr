---
title: 응용 프로그램에 대한 테넌트 전체 관리자 동의 부여 - Azure AD
description: 최종 사용자가 응용 프로그램에 로그인할 때 동의하라는 메시지가 표시되지 않도록 응용 프로그램에 테넌트 전체 동의를 부여하는 방법에 대해 알아봅니다.
services: active-directory
author: psignoret
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: mimart
ms.reviewer: phsignor
ms.collection: M365-identity-device-management
ms.openlocfilehash: c515fef4997720435c64bd5f3ae7b18f8921fc5d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75480919"
---
# <a name="grant-tenant-wide-admin-consent-to-an-application"></a>응용 프로그램에 대한 테넌트 전체 관리자 동의 부여

응용 프로그램에 테넌트 전체 관리자 동의를 부여하여 사용자 환경을 단순화하는 방법을 알아봅니다. 이 문서에서는 이를 달성하는 다양한 방법을 제공합니다. 이러한 방법은 Azure AD(Azure Active Directory) 테넌트의 모든 최종 사용자에게 적용됩니다.

애플리케이션에 동의하는 방법에 대한 자세한 내용은 [Azure Active Directory 동의 프레임워크](../develop/consent-framework.md)를 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

테넌트 전체 관리자 동의를 부여하려면 [글로벌 관리자,](../users-groups-roles/directory-assign-admin-roles.md#global-administrator--company-administrator) [응용 프로그램 관리자](../users-groups-roles/directory-assign-admin-roles.md#application-administrator)또는 클라우드 응용 프로그램 [관리자로](../users-groups-roles/directory-assign-admin-roles.md#cloud-application-administrator)로그인해야 합니다.

> [!IMPORTANT]
> 응용 프로그램에 테넌트 전체 관리자 동의가 부여된 경우 사용자 할당을 요구하도록 구성되지 않은 경우 모든 사용자가 앱에 로그인할 수 있습니다. 응용 프로그램에 로그인할 수 있는 사용자를 제한하려면 사용자 할당을 요구한 다음 응용 프로그램에 사용자 또는 그룹을 할당합니다. 자세한 내용은 [사용자 및 그룹을 할당하는 메서드](methods-for-assigning-users-and-groups.md)를 참조하세요.

> [!WARNING]
> 응용 프로그램에 테넌트 전체 관리자 동의를 부여하면 앱과 앱의 게시자가 조직의 데이터에 액세스할 수 있습니다. 동의를 받기 전에 응용 프로그램이 요청하는 권한을 주의 깊게 검토합니다.

## <a name="grant-admin-consent-from-the-azure-portal"></a>Azure 포털에서 관리자 동의 부여

### <a name="grant-admin-consent-in-enterprise-apps"></a>엔터프라이즈 앱에서 관리자 동의 부여

응용 프로그램이 테넌트에 이미 프로비전된 경우 *Enterprise 응용 프로그램을* 통해 테넌트 전체 관리자 동의를 부여할 수 있습니다. 예를 들어 한 명 이상의 사용자가 이미 응용 프로그램에 동의한 경우 테넌트에서 앱을 프로비전할 수 있습니다. 자세한 내용은 [응용 프로그램이 Azure Active Directory에 추가되는 방법과 이유를](../develop/active-directory-how-applications-are-added.md)참조하세요.

엔터프라이즈 응용 프로그램에 나열된 앱에 테넌트 전체 관리자 동의를 부여하려면 다음단계를 **수행합니다.**

1. [Azure 포털에](https://portal.azure.com) [글로벌 관리자,](../users-groups-roles/directory-assign-admin-roles.md#global-administrator--company-administrator)응용 [프로그램 관리자](../users-groups-roles/directory-assign-admin-roles.md#application-administrator)또는 클라우드 응용 프로그램 [관리자로](../users-groups-roles/directory-assign-admin-roles.md#cloud-application-administrator)로그인합니다.
2. **Azure Active Directory** 다음 **엔터프라이즈 응용 프로그램을 선택합니다.**
3. 테넌트 전체 관리자 동의를 부여할 응용 프로그램을 선택합니다.
4. 권한을 선택한 다음 **관리자 동의 부여를** **클릭합니다.**
5. 응용 프로그램에 필요한 권한을 주의 깊게 검토합니다.
6. 응용 프로그램에 필요한 권한에 동의하는 경우 동의를 부여합니다. 그렇지 않은 경우 창 **취소** 또는 닫기를 클릭합니다.

### <a name="grant-admin-consent-in-app-registrations"></a>앱 등록시 관리자 동의 부여

조직에서 개발했거나 Azure AD 테넌트에 직접 등록된 응용 프로그램의 경우 Azure Portal의 **앱 등록에서** 테넌트 전체 관리자 동의를 부여할 수도 있습니다.

앱 등록에서 테넌트 전체 관리자 **동의를 부여하려면:**

1. [Azure 포털에](https://portal.azure.com) [글로벌 관리자,](../users-groups-roles/directory-assign-admin-roles.md#global-administrator--company-administrator)응용 [프로그램 관리자](../users-groups-roles/directory-assign-admin-roles.md#application-administrator)또는 클라우드 응용 프로그램 [관리자로](../users-groups-roles/directory-assign-admin-roles.md#cloud-application-administrator)로그인합니다.
2. **Azure Active Directory** 다음 **앱 등록을 선택합니다.**
3. 테넌트 전체 관리자 동의를 부여할 응용 프로그램을 선택합니다.
4. API 권한을 선택한 다음 **관리자 동의 부여를** **클릭합니다.**
5. 응용 프로그램에 필요한 권한을 주의 깊게 검토합니다.
6. 응용 프로그램에 필요한 권한에 동의하는 경우 동의를 부여합니다. 그렇지 않은 경우 창 **취소** 또는 닫기를 클릭합니다.

## <a name="construct-the-url-for-granting-tenant-wide-admin-consent"></a>테넌트 전체 관리자 동의를 부여하기 위한 URL 생성

위에서 설명한 두 방법 중 하나를 사용하여 테넌트 전체 관리자 동의를 부여하면 Azure 포털에서 테넌트 전체 관리자 동의를 요청하는 창이 열립니다. 응용 프로그램의 클라이언트 ID(응용 프로그램 ID라고도 함)를 알고 있는 경우 동일한 URL을 빌드하여 테넌트 전체 관리자 동의를 부여할 수 있습니다.

테넌트 전체 관리자 동의 URL은 다음과 같습니다.

    https://login.microsoftonline.com/{tenant-id}/adminconsent?client_id={client-id}

각 항목이 나타내는 의미는 다음과 같습니다.

* `{client-id}`는 응용 프로그램의 클라이언트 ID(앱 ID라고도 함)입니다.
* `{tenant-id}`은 조직의 테넌트 ID 또는 확인된 도메인 이름입니다.

언제나 처럼 동의하기 전에 응용 프로그램이 요청하는 권한을 주의 깊게 검토하십시오.

## <a name="next-steps"></a>다음 단계

[최종 사용자가 응용 프로그램에 동의하는 방식 구성](configure-user-consent.md)

[관리자 동의 워크플로 구성](configure-admin-consent-workflow.md)

[Microsoft ID 플랫폼의 권한 및 동의](../develop/active-directory-v2-scopes.md)

[스택 오버플로에서 Azure AD](https://stackoverflow.com/questions/tagged/azure-active-directory)
