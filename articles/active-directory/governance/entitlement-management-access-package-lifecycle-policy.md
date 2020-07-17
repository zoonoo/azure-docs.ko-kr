---
title: Azure AD 자격 관리에서 액세스 패키지의 수명 주기 설정 변경-Azure Active Directory
description: Azure Active Directory 자격 관리에서 액세스 패키지의 수명 주기 설정을 변경 하는 방법에 대해 알아봅니다.
services: active-directory
documentationCenter: ''
author: msaburnley
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
ms.openlocfilehash: 538ed31c1f3bbca8c2151a6ac0d2a3ad561bc6a7
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85078777"
---
# <a name="change-lifecycle-settings-for-an-access-package-in-azure-ad-entitlement-management"></a>Azure AD 자격 관리에서 액세스 패키지의 수명 주기 설정 변경

액세스 패키지 관리자는 기존 정책을 편집 하 여 언제 든 지 액세스 패키지에 대 한 수명 주기 설정을 변경할 수 있습니다. 정책에 대 한 만료 날짜를 변경 하는 경우 이미 보류 중인 승인 또는 승인 된 상태에 있는 요청에 대 한 만료 날짜는 변경 되지 않습니다.

이 문서에서는 기존 액세스 패키지의 수명 주기 설정을 변경 하는 방법을 설명 합니다.

## <a name="open-lifecycle-settings"></a>수명 주기 설정 열기

액세스 패키지의 수명 주기 설정을 변경 하려면 해당 정책을 열어야 합니다. 액세스 패키지의 수명 주기 설정을 열려면 다음 단계를 수행 합니다.

**필수 역할:** 글로벌 관리자, 사용자 관리자, 카탈로그 소유자 또는 액세스 패키지 관리자

1. Azure Portal에서 **Azure Active Directory**를 클릭한 다음, **Identity Governance**를 클릭합니다.

1. 왼쪽 메뉴에서 **액세스 패키지** 를 클릭 한 다음 액세스 패키지를 엽니다.

1. **정책** 을 클릭 한 다음 편집 하려는 수명 주기 설정이 있는 정책을 클릭 합니다.

    정책 세부 정보 창이 페이지 아래쪽에 열립니다.

    ![액세스 패키지-정책 정보 창](./media/entitlement-management-shared/policy-details.png)

1. 정책을 편집 하려면 **편집** 을 클릭 합니다.

    ![액세스 패키지-정책 편집](./media/entitlement-management-shared/policy-edit.png)

1. **수명 주기 탭을** 클릭 하 여 수명 주기 설정을 엽니다.

[!INCLUDE [Entitlement management lifecycle policy](../../../includes/active-directory-entitlement-management-lifecycle-policy.md)]

## <a name="next-steps"></a>다음 단계

- [액세스 패키지에 대 한 변경 요청 및 승인 설정](entitlement-management-access-package-request-policy.md)