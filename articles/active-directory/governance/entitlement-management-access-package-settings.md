---
title: Azure AD 권한 관리 - Azure Active Directory에서 액세스 패키지를 요청하는 링크 공유
description: Azure Active Directory 권한 관리에서 액세스 패키지를 요청하기 위해 링크를 공유하는 방법을 알아봅니다.
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 10/15/2019
ms.author: ajburnle
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: ea90032b1f0cfe598ffdb3d35448a996f3111036
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78968754"
---
# <a name="share-link-to-request-an-access-package-in-azure-ad-entitlement-management"></a>Azure AD 권한 관리에서 액세스 패키지를 요청하는 링크 공유

디렉터리에 있는 대부분의 사용자는 내 액세스 포털에 로그인할 수 있으며 요청할 수 있는 액세스 패키지 목록을 자동으로 볼 수 있습니다. 그러나 디렉터리에 아직 없는 외부 비즈니스 파트너 사용자의 경우 액세스 패키지를 요청하는 데 사용할 수 있는 링크를 보내야 합니다. 

[외부 사용자에](entitlement-management-catalog-create.md) 대한 액세스 패키지카탈로그를 사용하도록 설정하고 외부 [사용자의 디렉터리에 대한 정책이](entitlement-management-access-package-request-policy.md)있는 경우 외부 사용자는 My Access 포털 링크를 사용하여 액세스 패키지를 요청할 수 있습니다.

## <a name="share-link-to-request-an-access-package"></a>액세스 패키지를 요청하는 링크 공유

**필수 구성 조건 역할:** 글로벌 관리자, 사용자 관리자, 카탈로그 소유자 또는 액세스 패키지 관리자

1. Azure Portal에서 **Azure Active Directory**를 클릭한 다음, **Identity Governance**를 클릭합니다.

1. 왼쪽 메뉴에서 **패키지 액세스를** 클릭한 다음 액세스 패키지를 엽니다.

1. 개요 페이지에서 내 액세스 **포털 링크를**복사합니다.

    ![액세스 패키지 개요 - 내 액세스 포털 링크](./media/entitlement-management-shared/my-access-portal-link.png)

    내부 비즈니스 파트너에게 보낼 때 전체 내 액세스 포털 링크를 복사하는 것이 중요합니다. 이렇게 하면 파트너가 디렉터리의 포털에 액세스하여 요청을 할 수 있습니다. 링크는 `myaccess`에서 시작하여 디렉터리 힌트를 포함하고 액세스 패키지 ID로 끝납니다.  (미국 정부의 경우 내 액세스 포털 링크의 `myaccess.microsoft.us`도메인이 됩니다.)

    `https://myaccess.microsoft.com/@<directory_hint>#/access-packages/<access_package_id>`

1. 외부 비즈니스 파트너에게 이메일을 보내거나 링크를 보냅니다. 사용자와 링크를 공유하여 액세스 패키지를 요청할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [액세스 패키지에 대한 액세스 요청](entitlement-management-request-access.md)
- [액세스 요청 승인 또는 거부](entitlement-management-request-approve.md)