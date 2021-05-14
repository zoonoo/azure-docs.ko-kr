---
title: 권한 관리에서 액세스 패키지 숨기기 또는 삭제 - Azure AD
description: Azure Active Directory 권한 관리에서 액세스 패키지를 숨기거나 삭제하는 방법을 알아봅니다.
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
ms.openlocfilehash: 22aa97c6e627c2072636ca2e079877ff0f608b68
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "87798803"
---
# <a name="hide-or-delete-an-access-package-in-azure-ad-entitlement-management"></a>Azure AD 권한 관리에서 액세스 패키지 숨기기 또는 삭제

액세스 패키지는 기본적으로 검색 가능합니다. 즉, 정책을 통해 사용자가 액세스 패키지를 요청할 수 있는 경우 내 액세스 포털에 나열된 액세스 패키지가 자동으로 표시됩니다. 하지만 액세스 패키지가 사용자의 내 액세스 포털에 표시되지 않도록 **숨김** 설정을 변경할 수 있습니다.

이 문서에서는 액세스 패키지를 숨기거나 삭제하는 방법을 설명합니다.

## <a name="change-the-hidden-setting"></a>숨김 설정 변경

액세스 패키지에 대한 **숨김** 설정을 변경하려면 다음 단계를 수행합니다.

**필수 역할:** 글로벌 관리자, 사용자 관리자, 카탈로그 소유자 또는 액세스 패키지 관리자

1. Azure Portal에서 **Azure Active Directory** 를 클릭한 다음, **Identity Governance** 를 클릭합니다.

1. 왼쪽 메뉴에서 **액세스 패키지** 를 클릭한 다음 액세스 패키지를 엽니다.

1. 개요 페이지에서 **편집** 을 클릭합니다.

1. **숨김** 설정을 지정합니다.

    **아니요** 로 설정하면 액세스 패키지가 사용자의 내 액세스 포털에 나열됩니다.

    **예** 로 설정하면 액세스 패키지가 사용자의 내 액세스 포털에 나열되지 않습니다. 사용자가 액세스 패키지를 볼 수 있는 유일한 방법은 액세스 패키지에 대한 직접 **내 액세스 포털 링크** 가 있는 경우입니다. 자세한 내용은 [액세스 패키지 요청 링크 공유](entitlement-management-access-package-settings.md)를 참조하세요.

## <a name="delete-an-access-package"></a>액세스 패키지 삭제

활성 사용자 할당이 없는 경우에만 액세스 패키지를 삭제할 수 있습니다. 액세스 패키지를 삭제하려면 다음 단계를 수행합니다.

**필수 역할:** 글로벌 관리자, 사용자 관리자, 카탈로그 소유자 또는 액세스 패키지 관리자

1. Azure Portal에서 **Azure Active Directory** 를 클릭한 다음, **Identity Governance** 를 클릭합니다.

1. 왼쪽 메뉴에서 **액세스 패키지** 를 클릭한 다음 액세스 패키지를 엽니다.

1. 왼쪽 메뉴에서 **할당** 을 클릭하고 모든 사용자에 대한 액세스 권한을 제거합니다.

1. 왼쪽 메뉴에서 **개요** 를 클릭하고 **삭제** 를 클릭합니다.

1. 표시되는 메시지 삭제에서 **예** 를 클릭합니다.

## <a name="next-steps"></a>다음 단계

- [액세스 패키지에 대한 할당 보기, 추가, 제거](entitlement-management-access-package-assignments.md)
- [보고서 및 로그 보기](entitlement-management-reports.md)
