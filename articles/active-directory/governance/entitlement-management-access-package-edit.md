---
title: 자격 관리에서 액세스 패키지 숨기기 또는 삭제-Azure AD
description: Azure Active Directory 자격 관리에서 액세스 패키지를 숨기 거 나 삭제 하는 방법에 대해 알아봅니다.
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
ms.openlocfilehash: cf410db5a483fc4f5d9efc1a6bd5c6202694ef4c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "79261997"
---
# <a name="hide-or-delete-an-access-package-in-azure-ad-entitlement-management"></a>Azure AD 자격 관리에서 액세스 패키지를 숨기 거 나 삭제 합니다.

액세스 패키지는 기본적으로 검색 가능 합니다. 즉, 정책을 통해 사용자가 액세스 패키지를 요청할 수 있는 경우 내 액세스 포털에 나열 된 액세스 패키지가 자동으로 표시 됩니다. 그러나 액세스 패키지가 사용자의 내 액세스 포털에 표시 되지 않도록 **숨김** 설정을 변경할 수 있습니다.

이 문서에서는 액세스 패키지를 숨기 거 나 삭제 하는 방법을 설명 합니다.

## <a name="change-the-hidden-setting"></a>숨겨진 설정 변경

액세스 패키지에 대 한 **숨겨진** 설정을 변경 하려면 다음 단계를 수행 합니다.

**필수 역할:** 글로벌 관리자, 사용자 관리자, 카탈로그 소유자 또는 액세스 패키지 관리자

1. Azure Portal에서 **Azure Active Directory**를 클릭한 다음, **Identity Governance**를 클릭합니다.

1. 왼쪽 메뉴에서 **액세스 패키지** 를 클릭 한 다음 액세스 패키지를 엽니다.

1. 개요 페이지에서 **편집**을 클릭 합니다.

1. **숨겨진** 설정을 설정 합니다.

    **아니요**로 설정 하면 액세스 패키지가 사용자의 내 액세스 포털에 나열 됩니다.

    **예**로 설정 되 면 액세스 패키지가 사용자의 내 액세스 포털에 나열 되지 않습니다. 사용자가 액세스 패키지를 볼 수 있는 유일한 방법은 액세스 패키지에 대 한 직접 **내 액세스 포털 링크가** 있는 경우입니다. 자세한 내용은 [액세스 패키지를 요청 하는 공유 링크를](entitlement-management-access-package-settings.md)참조 하세요.

## <a name="delete-an-access-package"></a>액세스 패키지 삭제

활성 사용자 할당이 없는 경우에만 액세스 패키지를 삭제할 수 있습니다. 액세스 패키지를 삭제 하려면 다음 단계를 수행 합니다.

**필수 역할:** 글로벌 관리자, 사용자 관리자, 카탈로그 소유자 또는 액세스 패키지 관리자

1. Azure Portal에서 **Azure Active Directory**를 클릭한 다음, **Identity Governance**를 클릭합니다.

1. 왼쪽 메뉴에서 **액세스 패키지** 를 클릭 한 다음 액세스 패키지를 엽니다.

1. 왼쪽 메뉴에서 **할당** 을 클릭 하 고 모든 사용자에 대 한 액세스 권한 제거를 클릭 합니다.

1. 왼쪽 메뉴에서 **개요** 를 클릭 한 다음 **삭제**를 클릭 합니다.

1. 삭제 메시지가 표시 되 면 **예**를 클릭 합니다.

## <a name="next-steps"></a>다음 단계

- [액세스 패키지에 대 한 할당 보기, 추가 및 제거](entitlement-management-access-package-assignments.md)
- [보고서 및 로그 보기](entitlement-management-reports.md)
