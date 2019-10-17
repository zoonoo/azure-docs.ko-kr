---
title: Azure AD 자격 관리 (미리 보기)에서 액세스 패키지를 요청 하는 공유 링크-Azure Active Directory
description: Azure Active Directory 자격 관리 (미리 보기)에서 액세스 패키지를 요청 하는 링크를 공유 하는 방법을 알아봅니다.
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
ms.openlocfilehash: 91b98df1f93991d4781283bc38b02dc20d11268d
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/16/2019
ms.locfileid: "72392333"
---
# <a name="share-link-to-request-an-access-package-in-azure-ad-entitlement-management-preview"></a>Azure AD 자격 관리 (미리 보기)에서 액세스 패키지를 요청 하는 공유 링크

> [!IMPORTANT]
> Azure AD(Azure Active Directory) 권한 관리는 현재 공개 미리 보기로 제공됩니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.
> 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

디렉터리의 대부분의 사용자는 내 액세스 포털에 로그인 하 여 요청할 수 있는 액세스 패키지의 목록을 자동으로 볼 수 있습니다. 그러나 디렉터리에 아직 없는 외부 비즈니스 파트너 사용자의 경우 액세스 패키지를 요청 하는 데 사용할 수 있는 링크를 전송 해야 합니다. 

액세스 패키지의 카탈로그가 [외부 사용자에 대해 사용 하도록 설정](entitlement-management-catalog-create.md) 되어 있고 [외부 사용자의 디렉터리에 대 한 정책이](entitlement-management-access-package-request-policy.md)있는 경우 외부 사용자는 내 액세스 포털 링크를 사용 하 여 액세스 패키지를 요청할 수 있습니다.

## <a name="share-link-to-request-an-access-package"></a>액세스 패키지를 요청 하는 공유 링크

**필수 역할:** 전역 관리자, 사용자 관리자, 카탈로그 소유자 또는 액세스 패키지 관리자

1. Azure Portal에서 **Azure Active Directory**를 클릭한 다음, **Identity Governance**를 클릭합니다.

1. 왼쪽 메뉴에서 **액세스 패키지** 를 클릭 한 다음 액세스 패키지를 엽니다.

1. 개요 페이지에서 **내 액세스 포털 링크**를 복사 합니다.

    ![액세스 패키지 개요 - 내 액세스 포털 링크](./media/entitlement-management-shared/my-access-portal-link.png)

    내부 비즈니스 파트너에 게 보낼 때 전체 내 액세스 포털 링크를 복사 하는 것이 중요 합니다. 이렇게 하면 파트너가 디렉터리 포털에 액세스 하 여 요청을 수행할 수 있습니다. 링크는 `myaccess`으로 시작 하 고, 디렉터리 힌트를 포함 하 고, 액세스 패키지 ID로 끝납니다.

    `https://myaccess.microsoft.com/@<directory_hint>#/access-packages/<access_package_id>`

1. 전자 메일을 보내거나 외부 비즈니스 파트너에 게 링크를 보냅니다. 사용자와의 링크를 공유 하 여 액세스 패키지를 요청할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [액세스 패키지에 대 한 액세스 요청](entitlement-management-request-access.md)
- [액세스 요청 승인 또는 거부](entitlement-management-request-approve.md)