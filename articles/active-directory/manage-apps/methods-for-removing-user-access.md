---
title: Azure Active Directory에서 응용 프로그램에 대 한 사용자의 액세스 권한을 제거 하는 방법
description: Azure Active Directory에서 응용 프로그램에 대 한 사용자 액세스를 제거 하는 방법 이해
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 11/02/2020
ms.author: kenwith
ms.openlocfilehash: 28b31d98f283dc957927ab2a35f0ab95bf066473
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/17/2020
ms.locfileid: "94654119"
---
# <a name="how-to-remove-a-users-access-to-an-application"></a>애플리케이션에 대한 사용자 액세스를 제거하는 방법

이 문서는 애플리케이션에 대한 사용자 액세스를 제거하는 방법을 이해하는 데 도움이 됩니다.

## <a name="i-want-to-remove-a-specific-users-or-groups-assignment-to-an-application"></a>애플리케이션에 대한 특정 사용자 또는 그룹의 할당을 제거하려는 경우

애플리케이션에 대한 사용자 또는 그룹 할당을 제거하려면 [Azure Active Directory의 엔터프라이즈 앱에서 사용자 또는 그룹 할당 제거](./assign-user-or-group-access-portal.md) 문서에 나열된 단계를 따릅니다.

## <a name="i-want-to-disable-all-access-to-an-application-for-every-user"></a>모든 사용자에 대해 애플리케이션에 대한 모든 액세스를 비활성화하려는 경우

애플리케이션에 대한 모든 사용자 로그인을 비활성화하려면 [Azure Active Directory의 엔터프라이즈 앱에 대한 사용자 로그인 비활성화](./disable-user-sign-in-portal.md) 문서에 나열된 단계를 따릅니다.

## <a name="i-want-to-delete-an-application-entirely"></a>애플리케이션을 완전히 삭제하려는 경우

[응용 프로그램 관리에 대 한 빠른 시작 시리즈](delete-application-portal.md) 는 Azure Active Directory 테 넌 트에서 응용 프로그램 삭제에 대 한 지침을 포함 합니다.

## <a name="i-want-to-disable-all-future-user-consent-operations-to-any-application"></a>모든 애플리케이션에 대한 모든 이후 사용자 동의 작업을 비활성화하려는 경우

전체 디렉터리에 대한 사용자 동의를 비활성화하면 모든 애플리케이션에 대한 최종 사용자 동의를 방지합니다. 관리자는 사용자를 대신 하 여 동의할 수 있습니다. 애플리케이션 동의 및 이 작업을 수행하거나 수행하지 않을 수 있는 이유에 대한 자세한 내용은 [사용자 및 관리자 동의 이해](../develop/howto-convert-app-to-be-multi-tenant.md#understand-user-and-admin-consent)를 참조하세요. [권한 및 동의](../develop/v2-permissions-and-consent.md)도 참조 하세요.

**전체 디렉터리에서 모든 이후 사용자 동의 작업을 비활성화** 하려면 다음 지침을 따릅니다.

1.  [**Azure Portal**](https://portal.azure.com/)을 열고 **전역 관리자** 권한으로 로그인합니다.

2.  **Azure Active Directory 확장** 을 엽니다. 

3.  탐색 메뉴에서 **엔터프라이즈 애플리케이션** 을 클릭합니다.

5.  **사용자 설정** 을 클릭합니다.

6.  **사용자는 앱이 자신을 대신하여 회사 데이터에 액세스하도록 허용할 수 있습니다** 토글을 **아니요** 로 설정하고, [저장] 단추를 클릭합니다.


## <a name="next-steps"></a>다음 단계

[앱에 대한 액세스 관리](what-is-access-management.md)