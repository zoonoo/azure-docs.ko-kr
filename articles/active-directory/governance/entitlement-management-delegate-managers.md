---
title: Azure AD 권한 관리 - Azure Active Directory에서 패키지 관리자에 액세스하기 위해 액세스 거버넌스를 위임합니다.
description: IT 관리자로부터 액세스 거버넌스를 위임하여 패키지 관리자 및 프로젝트 관리자가 스스로 액세스를 관리할 수 있도록 하는 방법을 알아봅니다.
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 10/07/2019
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: efdc61b82a19cc5d370d6069e8c9dcd3ce5e8ae5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "73174363"
---
# <a name="delegate-access-governance-to-access-package-managers-in-azure-ad-entitlement-management"></a>Azure AD 권한 관리에서 패키지 관리자에 액세스하기 위해 액세스 거버넌스 위임

카탈로그에서 액세스 패키지의 생성 및 관리를 위임하려면 액세스 패키지 관리자 역할에 사용자를 추가합니다. 액세스 패키지 관리자는 사용자가 카탈로그의 리소스에 대한 액세스를 요청해야 하는 필요성에 대해 잘 알고 있어야 합니다. 예를 들어 프로젝트에 카탈로그를 사용하는 경우 프로젝트 리더가 해당 카탈로그의 액세스 패키지 관리자일 수 있습니다.  액세스 패키지 관리자는 카탈로그에 리소스를 추가할 수 없지만 카탈로그의 액세스 패키지 및 정책을 관리할 수 있습니다.  액세스 패키지 관리자에게 위임할 때 해당 사용자는 다음에 대한 책임을 질 수 있습니다.

- 사용자가 카탈로그의 리소스에 대해 어떤 역할을 할 것인가
- 액세스가 필요한 사용자
- 액세스 요청을 승인해야 하는 사용자
- 프로젝트가 지속되는 시간

이 비디오에서는 카탈로그 소유자에서 패키지 관리자에 액세스하는 데 액세스 거버넌스를 위임하는 방법에 대한 개요를 제공합니다.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE3Lq08]

## <a name="as-a-catalog-owner-delegate-to-an-access-package-manager"></a>카탈로그 소유자로서 액세스 패키지 관리자에게 위임

다음 단계에 따라 사용자를 액세스 패키지 관리자 역할에 할당합니다.

**필수 구성 조건 역할:** 글로벌 관리자, 사용자 관리자 또는 카탈로그 소유자

1. Azure Portal에서 **Azure Active Directory**를 클릭한 다음, **Identity Governance**를 클릭합니다.

1. 왼쪽 메뉴에서 카탈로그를 클릭한 다음 **관리자를** 추가할 카탈로그를 엽니다.

1. 왼쪽 메뉴에서 **역할 및 관리자를**클릭합니다.

    ![역할 및 관리자 카탈로그](./media/entitlement-management-shared/catalog-roles-administrators.png)

1. **액세스 패키지 관리자 추가를** 클릭하여 이러한 역할에 대한 구성원을 선택합니다.

1. 이러한 멤버를 추가하려면 **선택을** 클릭합니다.

## <a name="remove-an-access-package-manager"></a>액세스 패키지 관리자 제거

다음 단계에 따라 액세스 패키지 관리자 역할에서 사용자를 제거합니다.

**필수 구성 조건 역할:** 글로벌 관리자, 사용자 관리자 또는 카탈로그 소유자

1. Azure Portal에서 **Azure Active Directory**를 클릭한 다음, **Identity Governance**를 클릭합니다.

1. 왼쪽 메뉴에서 카탈로그를 클릭한 다음 **관리자를** 추가할 카탈로그를 엽니다.

1. 왼쪽 메뉴에서 **역할 및 관리자를**클릭합니다.

1. 제거할 액세스 패키지 관리자 옆에 검사 표시를 추가합니다.

1. **제거**를 클릭합니다.

## <a name="next-steps"></a>다음 단계

- [새 액세스 패키지 만들기](entitlement-management-access-package-create.md)
