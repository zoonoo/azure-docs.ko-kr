---
title: Azure AD 권한 관리에서 액세스 패키지의 수명 주기 설정 변경 - Azure Active Directory
description: Azure Active Directory 권한 관리에서 액세스 패키지의 요청자 정보 및 수명 주기 설정을 변경하는 방법을 알아봅니다.
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
ms.openlocfilehash: 4fd119238476b842a4dba1efeec86544ca263920
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "90980095"
---
# <a name="change-lifecycle-settings-for-an-access-package-in-azure-ad-entitlement-management"></a>Azure AD 권한 관리에서 액세스 패키지의 수명 주기 설정 변경

액세스 패키지 관리자는 기존 정책을 편집하여 언제든지 액세스 패키지의 수명 주기 설정을 변경할 수 있습니다. 정책의 만료 날짜를 변경하는 경우 이미 승인 보류 중이거나 승인된 상태인 요청의 만료 날짜는 변경되지 않습니다.

이 문서에서는 기존 액세스 패키지의 수명 주기 설정을 변경하는 방법을 설명합니다.

## <a name="open-requestor-information"></a>요청자 정보 열기
사용자에게 액세스 패키지에 대한 적절한 액세스 권한이 있는지 확인하기 위해 특정 액세스 패키지에 대한 액세스를 요청하는 사용자를 위한 사용자 지정 질문을 구성할 수 있습니다. 구성 옵션으로는 지역화, 필수/선택 사항, 텍스트/다중 선택 응답 형식이 있습니다. 패키지를 요청할 때 요청자에게 질문이 표시되고 승인자는 질문에 대한 답변을 확인하여 결정을 내릴 수 있습니다. 액세스 패키지에서 질문을 구성하려면 다음 단계를 사용합니다.

## <a name="open-lifecycle-settings"></a>수명 주기 설정 열기

액세스 패키지의 수명 주기 설정을 변경하려면 해당 정책을 열어야 합니다. 액세스 패키지의 수명 주기 설정을 열려면 다음 단계를 수행합니다.

**필수 역할:** 글로벌 관리자, 사용자 관리자, 카탈로그 소유자 또는 액세스 패키지 관리자

1. Azure Portal에서 **Azure Active Directory** 를 클릭한 다음, **Identity Governance** 를 클릭합니다.

1. 왼쪽 메뉴에서 **액세스 패키지** 를 클릭한 다음 액세스 패키지를 엽니다.

1. **정책** 을 클릭한 다음 편집할 수명 주기 설정이 있는 정책을 클릭합니다.

    정책 세부 정보 창이 페이지 맨 아래에 열립니다.

    ![액세스 패키지 - 정책 세부 정보 창](./media/entitlement-management-shared/policy-details.png)

1. 정책을 편집하려면 **편집** 을 클릭합니다.

    ![액세스 패키지 - 정책 편집](./media/entitlement-management-shared/policy-edit.png)

1. **수명 주기** 탭을 클릭하여 수명 주기 설정을 엽니다.

[!INCLUDE [Entitlement management lifecycle policy](../../../includes/active-directory-entitlement-management-lifecycle-policy.md)]

## <a name="next-steps"></a>다음 단계

- [액세스 패키지의 요청 및 승인 설정 변경](entitlement-management-access-package-request-policy.md)