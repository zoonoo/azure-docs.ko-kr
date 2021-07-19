---
title: Azure AD 권한 관리에서 액세스 패키지를 요청하는 공유 링크 - Azure Active Directory
description: Azure Active Directory 권한 관리에서 액세스 패키지를 요청하는 링크를 공유하는 방법을 알아봅니다.
services: active-directory
documentationCenter: ''
author: ajburnle
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 06/18/2020
ms.author: ajburnle
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 25cbef009f73ac788da36d60f2313dcfe9b49ebe
ms.sourcegitcommit: 5da0bf89a039290326033f2aff26249bcac1fe17
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/10/2021
ms.locfileid: "109713561"
---
# <a name="share-link-to-request-an-access-package-in-azure-ad-entitlement-management"></a>Azure AD 권한 관리에서 액세스 패키지를 요청하는 공유 링크

디렉터리의 사용자는 대부분 내 액세스 포털에 로그인하여 요청할 수 있는 액세스 패키지 목록을 자동으로 볼 수 있습니다. 하지만 디렉터리에 아직 없는 외부 비즈니스 파트너 사용자의 경우 액세스 패키지를 요청하는 데 사용할 수 있는 링크를 보내야 합니다. 

액세스 패키지 카탈로그가 [외부 사용자에 대해 사용](entitlement-management-catalog-create.md)하도록 설정되어 있고 [외부 사용자 디렉터리 정책](entitlement-management-access-package-request-policy.md)이 있는 경우 외부 사용자는 내 액세스 포털 링크를 사용하여 액세스 패키지를 요청할 수 있습니다.

## <a name="share-link-to-request-an-access-package"></a>액세스 패키지를 요청하는 링크 공유

**필수 역할:** 전역 관리자, Identity Governance 관리자, 사용자 관리자, 카탈로그 소유자, 액세스 패키지 관리자

1. Azure Portal에서 **Azure Active Directory** 를 클릭한 다음, **Identity Governance** 를 클릭합니다.

1. 왼쪽 메뉴에서 **액세스 패키지** 를 클릭한 다음 액세스 패키지를 엽니다.

1. 개요 페이지에서 **내 액세스 포털 링크** 를 복사합니다.

    ![액세스 패키지 개요 - 내 액세스 포털 링크](./media/entitlement-management-shared/my-access-portal-link.png)

    내부 비즈니스 파트너에게 보낼 때 전체 내 액세스 포털 링크를 복사하는 것이 중요합니다. 이렇게 하면 파트너가 디렉터리 포털에 액세스하여 요청을 수행할 수 있습니다. 링크는 `myaccess`로 시작하고, 디렉터리 힌트를 포함하며, 액세스 패키지 ID로 끝납니다.  (미국 정부의 경우 내 액세스 포털 링크의 도메인은 `myaccess.microsoft.us`입니다.)

    `https://myaccess.microsoft.com/@<directory_hint>#/access-packages/<access_package_id>`

1. 외부 비즈니스 파트너에게 링크를 보내거나 메일로 보냅니다. 이 링크를 해당 사용자와 공유하여 액세스 패키지를 요청할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [액세스 패키지에 대한 액세스 요청](entitlement-management-request-access.md)
- [액세스 요청 승인 또는 거부](entitlement-management-request-approve.md)