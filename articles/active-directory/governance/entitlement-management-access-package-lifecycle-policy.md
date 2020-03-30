---
title: Azure AD 권한 관리 - Azure Active Directory에서 액세스 패키지에 대한 수명 주기 설정 변경
description: Azure Active Directory 권한 관리에서 액세스 패키지에 대한 수명 주기 설정을 변경하는 방법을 알아봅니다.
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
ms.openlocfilehash: 959d85f496a4a573a969bf736aba137d5b86154a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79261984"
---
# <a name="change-lifecycle-settings-for-an-access-package-in-azure-ad-entitlement-management"></a>Azure AD 권한 관리에서 액세스 패키지에 대한 수명 주기 설정 변경

액세스 패키지 관리자는 기존 정책을 편집하여 언제든지 액세스 패키지의 수명 주기 설정을 변경할 수 있습니다. 정책의 만료 날짜를 변경하면 이미 승인 대기 중이거나 승인된 상태의 요청의 만료 날짜는 변경되지 않습니다.

이 문서에서는 기존 액세스 패키지의 수명 주기 설정을 변경하는 방법에 대해 설명합니다.

## <a name="open-lifecycle-settings"></a>수명 주기 설정 열기

액세스 패키지의 수명 주기 설정을 변경하려면 해당 정책을 열어야 합니다. 다음 단계에 따라 액세스 패키지의 수명 주기 설정을 엽니다.

**필수 구성 조건 역할:** 글로벌 관리자, 사용자 관리자, 카탈로그 소유자 또는 액세스 패키지 관리자

1. Azure Portal에서 **Azure Active Directory**를 클릭한 다음, **Identity Governance**를 클릭합니다.

1. 왼쪽 메뉴에서 **패키지 액세스를** 클릭한 다음 액세스 패키지를 엽니다.

1. **정책을** 클릭한 다음 편집할 수명 주기 설정이 있는 정책을 클릭합니다.

    정책 세부 정보 창은 페이지 하단에서 열립니다.

    ![액세스 패키지 - 정책 세부 정보 창](./media/entitlement-management-shared/policy-details.png)

1. 정책을 편집하려면 **편집을** 클릭합니다.

    ![액세스 패키지 - 정책 편집](./media/entitlement-management-shared/policy-edit.png)

1. **수명 주기** 탭을 클릭하여 수명 주기 설정을 엽니다.

[!INCLUDE [Entitlement management lifecycle policy](../../../includes/active-directory-entitlement-management-lifecycle-policy.md)]

## <a name="next-steps"></a>다음 단계

- [액세스 패키지에 대한 요청 및 승인 설정 변경](entitlement-management-access-package-request-policy.md)