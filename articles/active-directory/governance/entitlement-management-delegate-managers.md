---
title: 액세스 관리를 위임 하 여 Azure AD 자격 관리 (미리 보기)의 패키지 관리자 액세스-Azure Active Directory
description: IT 관리자의 액세스 관리를 위임 하 여 액세스를 관리할 수 있도록 패키지 관리자 및 프로젝트 관리자에 액세스 하는 방법을 알아봅니다.
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
ms.openlocfilehash: c7a2b6bfdb4904e11ffba3a9fe1097c7f5cfe9d6
ms.sourcegitcommit: aef6040b1321881a7eb21348b4fd5cd6a5a1e8d8
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2019
ms.locfileid: "72170738"
---
# <a name="delegate-access-governance-to-access-package-managers-in-azure-ad-entitlement-management-preview"></a>액세스 관리를 위임 하 여 Azure AD 자격 관리 (미리 보기)의 패키지 관리자 액세스

> [!IMPORTANT]
> Azure AD(Azure Active Directory) 권한 관리는 현재 공개 미리 보기로 제공됩니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.
> 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

카탈로그에서 액세스 패키지의 생성 및 관리를 위임 하려면 access 패키지 관리자 역할에 사용자를 추가 합니다. 액세스 패키지 관리자는 사용자가 카탈로그에서 리소스에 대 한 액세스를 요청 해야 하는 필요성에 대해 잘 알고 있어야 합니다. 예를 들어 프로젝트에 카탈로그를 사용 하는 경우 프로젝트 리더가 해당 카탈로그에 대 한 액세스 패키지 관리자가 될 수 있습니다.  액세스 패키지 관리자는 카탈로그에 리소스를 추가할 수 없지만 카탈로그의 액세스 패키지 및 정책을 관리할 수는 있습니다.  액세스 패키지 관리자에 위임 하는 경우 해당 사용자는 다음을 담당할 수 있습니다.

- 사용자가 카탈로그의 리소스에 대해 가져야 하는 역할
- 액세스 해야 하는 사용자
- 액세스 요청을 승인 해야 하는 사용자
- 프로젝트가 마지막으로 지속 되는 시간

이 비디오는 카탈로그 소유자의 액세스 관리를 위임 하 여 패키지 관리자에 액세스 하는 방법에 대 한 개요를 제공 합니다.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE3Lq08]

## <a name="as-a-catalog-owner-delegate-to-an-access-package-manager"></a>카탈로그 소유자는 액세스 패키지 관리자에 위임

액세스 패키지 관리자 역할에 사용자를 할당 하려면 다음 단계를 수행 합니다.

**필수 역할:** 전역 관리자, 사용자 관리자 또는 카탈로그 소유자

1. Azure Portal에서 **Azure Active Directory**를 클릭한 다음, **Identity Governance**를 클릭합니다.

1. 왼쪽 메뉴에서 **카탈로그** 를 클릭 한 다음 관리자를 추가 하려는 카탈로그를 엽니다.

1. 왼쪽 메뉴에서 **역할 및 관리자**를 클릭 합니다.

    ![역할 및 관리자 카탈로그](./media/entitlement-management-shared/catalog-roles-administrators.png)

1. **액세스 패키지 관리자 추가** 를 클릭 하 여 이러한 역할에 대 한 멤버를 선택 합니다.

1. **선택** 을 클릭 하 여 이러한 멤버를 추가 합니다.

## <a name="remove-an-access-package-manager"></a>액세스 패키지 관리자 제거

액세스 패키지 관리자 역할에서 사용자를 제거 하려면 다음 단계를 수행 합니다.

**필수 역할:** 전역 관리자, 사용자 관리자 또는 카탈로그 소유자

1. Azure Portal에서 **Azure Active Directory**를 클릭한 다음, **Identity Governance**를 클릭합니다.

1. 왼쪽 메뉴에서 **카탈로그** 를 클릭 한 다음 관리자를 추가 하려는 카탈로그를 엽니다.

1. 왼쪽 메뉴에서 **역할 및 관리자**를 클릭 합니다.

1. 제거 하려는 액세스 패키지 관리자 옆에 있는 확인 표시를 추가 합니다.

1. **제거**를 클릭합니다.

## <a name="next-steps"></a>다음 단계

- [새 액세스 패키지 만들기](entitlement-management-access-package-create.md)
