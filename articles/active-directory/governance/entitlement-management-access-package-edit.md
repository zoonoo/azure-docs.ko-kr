---
title: 권한 부여 관리에서 액세스 패키지 숨기기 또는 삭제 - Azure AD
description: Azure Active Directory 권한 관리에서 액세스 패키지를 숨기거나 삭제하는 방법을 알아봅니다.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79261997"
---
# <a name="hide-or-delete-an-access-package-in-azure-ad-entitlement-management"></a>Azure AD 권한 관리에서 액세스 패키지 숨기기 또는 삭제

액세스 패키지는 기본적으로 검색할 수 있습니다. 즉, 정책에서 사용자가 액세스 패키지를 요청할 수 있는 경우 내 액세스 포털에 나열된 액세스 패키지가 자동으로 표시됩니다. 그러나 액세스 패키지가 사용자의 내 액세스 포털에 나열되지 않도록 **숨겨진** 설정을 변경할 수 있습니다.

이 문서에서는 액세스 패키지를 숨기거나 삭제하는 방법에 대해 설명합니다.

## <a name="change-the-hidden-setting"></a>숨겨진 설정 변경

다음 단계에 따라 액세스 패키지에 대한 **숨김** 설정을 변경합니다.

**필수 구성 조건 역할:** 글로벌 관리자, 사용자 관리자, 카탈로그 소유자 또는 액세스 패키지 관리자

1. Azure Portal에서 **Azure Active Directory**를 클릭한 다음, **Identity Governance**를 클릭합니다.

1. 왼쪽 메뉴에서 **패키지 액세스를** 클릭한 다음 액세스 패키지를 엽니다.

1. 개요 페이지에서 **편집을**클릭합니다.

1. 숨김 설정을 **설정합니다.**

    **아니오로**설정하면 액세스 패키지가 사용자의 내 액세스 포털에 나열됩니다.

    **예로**설정하면 액세스 패키지가 사용자의 내 액세스 포털에 나열되지 않습니다. 사용자가 액세스 패키지를 볼 수 있는 유일한 방법은 액세스 패키지에 대한 직접 **내 액세스 포털 링크가** 있는 경우입니다. 자세한 내용은 [액세스 패키지를 요청하는 링크 공유를](entitlement-management-access-package-settings.md)참조하십시오.

## <a name="delete-an-access-package"></a>액세스 패키지 삭제

액세스 패키지는 활성 사용자 할당이 없는 경우에만 삭제할 수 있습니다. 다음 단계에 따라 액세스 패키지를 삭제합니다.

**필수 구성 조건 역할:** 글로벌 관리자, 사용자 관리자, 카탈로그 소유자 또는 액세스 패키지 관리자

1. Azure Portal에서 **Azure Active Directory**를 클릭한 다음, **Identity Governance**를 클릭합니다.

1. 왼쪽 메뉴에서 **패키지 액세스를** 클릭한 다음 액세스 패키지를 엽니다.

1. 왼쪽 메뉴에서 할당을 클릭하고 모든 사용자에 대한 액세스를 **제거합니다.**

1. 왼쪽 메뉴에서 개요를 클릭한 다음 **에서 삭제를** **클릭합니다.**

1. 표시되는 삭제 메시지에서 **예**.

## <a name="next-steps"></a>다음 단계

- [액세스 패키지에 대한 할당 보기, 추가 및 제거](entitlement-management-access-package-assignments.md)
- [보고서 및 로그 보기](entitlement-management-reports.md)
