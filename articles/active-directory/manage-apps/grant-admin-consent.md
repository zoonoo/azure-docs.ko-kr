---
title: 응용 프로그램에 테 넌 트 전체 관리자 동의 부여-Azure AD
description: 응용 프로그램에 로그인 할 때 최종 사용자에 게 동의 여부를 묻지 않도록 응용 프로그램에 테 넌 트 차원의 동의를 부여 하는 방법을 알아봅니다.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 11/04/2019
ms.author: kenwith
ms.reviewer: phsignor
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9680c9bee6d0cf5c9605ce7b6009a500abd81ffb
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2020
ms.locfileid: "92369100"
---
# <a name="grant-tenant-wide-admin-consent-to-an-application"></a>애플리케이션에 대한 테넌트 전체 관리자 동의 부여

응용 프로그램에 대 한 테 넌 트 전체 관리자 동의를 부여 하 여 사용자 환경을 간소화 하는 방법을 알아봅니다. 이 문서에서는이를 수행 하는 다양 한 방법을 제공 합니다. 이러한 방법은 Azure AD(Azure Active Directory) 테넌트의 모든 최종 사용자에게 적용됩니다.

애플리케이션에 동의하는 방법에 대한 자세한 내용은 [Azure Active Directory 동의 프레임워크](../develop/consent-framework.md)를 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

테 넌 트 전체 관리자 동의를 부여 하려면 [전역 관리자](../roles/permissions-reference.md#global-administrator--company-administrator), [응용 프로그램 관리자](../roles/permissions-reference.md#application-administrator)또는 [클라우드 응용 프로그램 관리자 권한](../roles/permissions-reference.md#cloud-application-administrator)으로 로그인 해야 합니다.

> [!IMPORTANT]
> 응용 프로그램에 테 넌 트 전체 관리자 동의가 부여 되 면 사용자 할당을 요구 하도록 구성 되지 않은 한 모든 사용자가 앱에 로그인 할 수 있습니다. 응용 프로그램에 로그인 할 수 있는 사용자를 제한 하려면 사용자 할당을 요구 하 고 응용 프로그램에 사용자 또는 그룹을 할당 합니다. 자세한 내용은 [사용자 및 그룹을 할당하는 메서드](methods-for-assigning-users-and-groups.md)를 참조하세요.
>
> 전역 관리자 역할은 Microsoft Graph API에 대 한 응용 프로그램 사용 권한에 대 한 관리자 동의를 제공 하기 위해 필요 합니다.

> [!WARNING]
> 응용 프로그램에 대 한 테 넌 트 전체 관리자 동의를 부여 하면 앱 및 앱의 게시자에 게 조직의 데이터에 대 한 액세스 권한이 부여 됩니다. 동의를 부여 하기 전에 응용 프로그램에서 요청 하는 권한을 신중 하 게 검토 합니다.
>
> 전역 관리자 역할은 Microsoft Graph API에 대 한 응용 프로그램 사용 권한에 대 한 관리자 동의를 제공 하기 위해 필요 합니다.

## <a name="grant-admin-consent-from-the-azure-portal"></a>Azure Portal에서 관리자 동의를 부여 합니다.

### <a name="grant-admin-consent-in-enterprise-apps"></a>엔터프라이즈 앱에서 관리자 동의를 부여 합니다.

응용 프로그램이 테 넌 트에 이미 프로 비전 된 경우 *엔터프라이즈 응용 프로그램* 을 통해 테 넌 트 전체 관리자 동의를 부여할 수 있습니다. 예를 들어 응용 프로그램에 하나 이상의 사용자가 이미 동의한 경우 테 넌 트에서 앱을 프로 비전 할 수 있습니다. 자세한 내용은 [Azure Active Directory에 응용 프로그램을 추가 하는 방법 및 이유](../develop/active-directory-how-applications-are-added.md)를 참조 하세요.

**엔터프라이즈 응용 프로그램**에 나열 된 앱에 대 한 테 넌 트 전체 관리자 동의를 부여 하려면:

1. [전역 관리자](../roles/permissions-reference.md#global-administrator--company-administrator), [응용 프로그램 관리자](../roles/permissions-reference.md#application-administrator)또는 [클라우드 응용 프로그램 관리자 권한](../roles/permissions-reference.md#cloud-application-administrator)으로 [Azure Portal](https://portal.azure.com) 에 로그인 합니다.
2. **Azure Active Directory** 다음 **엔터프라이즈 응용 프로그램**을 선택 합니다.
3. 테 넌 트 전체 관리자 동의를 부여 하려는 응용 프로그램을 선택 합니다.
4. **권한** 을 선택 하 고 **관리자 동의 부여**를 클릭 합니다.
5. 응용 프로그램에 필요한 권한을 신중 하 게 검토 합니다.
6. 응용 프로그램에 필요한 사용 권한에 동의 하는 경우 동의를 부여 합니다. 그렇지 않은 경우 **취소** 를 클릭 하거나 창을 닫습니다.

> [!WARNING]
> **엔터프라이즈 앱** 을 통해 테 넌 트 전체 관리자 동의를 부여 하면 이전에 테 넌 트 전체에 부여 된 모든 사용 권한이 해지 됩니다. 사용자가 대신 하 여 이전에 부여한 권한은 영향을 받지 않습니다. 

### <a name="grant-admin-consent-in-app-registrations"></a>앱 등록에서 관리자 동의를 부여 합니다.

조직에서 개발한 응용 프로그램의 경우 또는 Azure AD 테 넌 트에 직접 등록 된 응용 프로그램의 경우 Azure Portal에서 **앱 등록** 의 테 넌 트 전체 관리자 동의를 부여할 수도 있습니다.

**앱 등록**에서 테 넌 트 전체 관리자 동의를 부여 하려면 다음을 수행 합니다.

1. [전역 관리자](../roles/permissions-reference.md#global-administrator--company-administrator), [응용 프로그램 관리자](../roles/permissions-reference.md#application-administrator)또는 [클라우드 응용 프로그램 관리자 권한](../roles/permissions-reference.md#cloud-application-administrator)으로 [Azure Portal](https://portal.azure.com) 에 로그인 합니다.
2. **Azure Active Directory** 선택한 다음 **앱 등록**합니다.
3. 테 넌 트 전체 관리자 동의를 부여 하려는 응용 프로그램을 선택 합니다.
4. **API 권한** 을 선택한 다음 **관리자 동의 부여**를 클릭 합니다.
5. 응용 프로그램에 필요한 권한을 신중 하 게 검토 합니다.
6. 응용 프로그램에 필요한 사용 권한에 동의 하는 경우 동의를 부여 합니다. 그렇지 않은 경우 **취소** 를 클릭 하거나 창을 닫습니다.

> [!WARNING]
> **앱 등록** 를 통해 테 넌 트 전체 관리자 동의를 부여 하면 이전에 테 넌 트 전체에 부여 된 모든 사용 권한을 취소 합니다. 사용자가 대신 하 여 이전에 부여한 권한은 영향을 받지 않습니다. 

## <a name="construct-the-url-for-granting-tenant-wide-admin-consent"></a>테 넌 트 전체 관리자 동의를 부여 하기 위한 URL 생성

위에서 설명한 방법 중 하나를 사용 하 여 테 넌 트 전체 관리자 동의를 부여 하는 경우 Azure Portal에서 창이 열리며,이를 통해 테 넌 트 전체 관리자 동의를 확인 합니다. 응용 프로그램의 클라이언트 ID (응용 프로그램 ID 라고도 함)를 알고 있으면 동일한 URL을 작성 하 여 테 넌 트 전체 관리자 동의를 부여할 수 있습니다.

테 넌 트 전체 관리자 동의 URL은 다음 형식을 따릅니다.

```http
https://login.microsoftonline.com/{tenant-id}/adminconsent?client_id={client-id}
```

여기서

* `{client-id}` 응용 프로그램의 클라이언트 ID (앱 ID 라고도 함)입니다.
* `{tenant-id}` 조직의 테 넌 트 ID 또는 확인 된 도메인 이름입니다.

언제나 처럼 동의를 부여 하기 전에 응용 프로그램에서 요청 하는 권한을 신중 하 게 검토 합니다.

> [!WARNING]
> 이 URL을 통해 테 넌 트 전체 관리자 동의를 부여 하면 이전에 테 넌 트 전체에 부여 된 모든 사용 권한을 취소 합니다. 사용자가 대신 하 여 이전에 부여한 권한은 영향을 받지 않습니다. 

## <a name="next-steps"></a>다음 단계

[최종 사용자가 애플리케이션에 동의하는 방법 구성](configure-user-consent.md)

[관리자 동의 워크플로 구성](configure-admin-consent-workflow.md)

[Microsoft ID 플랫폼의 권한 및 동의](../develop/active-directory-v2-scopes.md)

[StackOverflow의 Azure AD](https://stackoverflow.com/questions/tagged/azure-active-directory)
