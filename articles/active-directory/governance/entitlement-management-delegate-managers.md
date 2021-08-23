---
title: Azure AD 권한 관리에서 액세스 패키지 관리자에게 액세스 거버넌스 위임 - Azure Active Directory
description: IT 관리자의 액세스 거버넌스를 액세스 패키지 관리자 및 프로젝트 관리자에게 위임하여 자체적으로 액세스를 관리할 수 있도록 하는 방법을 알아봅니다.
services: active-directory
documentationCenter: ''
author: ajburnle
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 06/18/2020
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: a4a33170fecab6efbe86568eb81b87637ca5da24
ms.sourcegitcommit: 5da0bf89a039290326033f2aff26249bcac1fe17
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/10/2021
ms.locfileid: "109714299"
---
# <a name="delegate-access-governance-to-access-package-managers-in-azure-ad-entitlement-management"></a>Azure AD 권한 관리에서 액세스 패키지 관리자에게 액세스 거버넌스 위임

카탈로그에서 액세스 패키지 만들기 및 관리를 위임하려면 액세스 패키지 관리자 역할에 사용자를 추가합니다. 액세스 패키지 관리자는 사용자가 카탈로그에서 리소스에 대한 액세스 권한을 요청해야 하는 것을 잘 알고 있어야 합니다. 예를 들어 프로젝트에 카탈로그를 사용하는 경우 프로젝트 리드가 해당 카탈로그에 대한 액세스 패키지 관리자가 될 수 있습니다.  액세스 패키지 관리자는 카탈로그에 리소스를 추가할 수는 없지만 카탈로그의 액세스 패키지 및 정책을 관리할 수는 있습니다.  액세스 패키지 관리자에 위임하는 경우 해당 사용자는 다음을 담당할 수 있습니다.

- 사용자가 카탈로그의 리소스에 대해 가지는 역할
- 액세스해야 하는 사용자
- 액세스 요청을 승인해야 하는 사용자
- 프로젝트가 지속되는 시간

이 비디오에서는 카탈로그 소유자의 액세스 거버넌스를 액세스 패키지 관리자에게 위임하는 방법에 대한 개요를 제공합니다.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE3Lq08]

## <a name="as-a-catalog-owner-delegate-to-an-access-package-manager"></a>카탈로그 소유자가 액세스 패키지 관리자에게 위임

액세스 패키지 관리자 역할에 사용자를 할당하려면 다음 단계를 수행합니다.

**필수 역할:** 전역 관리자, Identity Governance 관리자, 사용자 관리자 또는 카탈로그 소유자

1. Azure Portal에서 **Azure Active Directory** 를 클릭한 다음, **Identity Governance** 를 클릭합니다.

1. 왼쪽 메뉴에서 **카탈로그** 를 클릭한 후 관리자를 추가할 카탈로그를 엽니다.

1. 왼쪽 메뉴에서 **역할 및 관리자** 를 클릭합니다.

    ![역할 및 관리자 카탈로그](./media/entitlement-management-shared/catalog-roles-administrators.png)

1. **액세스 패키지 관리자 추가** 를 클릭하여 해당 역할의 구성원을 선택합니다.

1. **선택** 을 클릭하여 구성원을 추가합니다.

## <a name="remove-an-access-package-manager"></a>액세스 패키지 관리자 제거

액세스 패키지 관리자 역할에서 사용자를 제거하려면 다음 단계를 수행합니다.

**필수 역할:** 전역 관리자, 사용자 관리자 또는 카탈로그 소유자

1. Azure Portal에서 **Azure Active Directory** 를 클릭한 다음, **Identity Governance** 를 클릭합니다.

1. 왼쪽 메뉴에서 **카탈로그** 를 클릭한 후 관리자를 추가할 카탈로그를 엽니다.

1. 왼쪽 메뉴에서 **역할 및 관리자** 를 클릭합니다.

1. 제거할 액세스 패키지 관리자 옆에 확인 표시를 추가합니다.

1. **제거** 를 클릭합니다.

## <a name="next-steps"></a>다음 단계

- [새 액세스 패키지 만들기](entitlement-management-access-package-create.md)
