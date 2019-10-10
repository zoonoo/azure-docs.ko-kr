---
title: Azure AD 자격 관리 (미리 보기)에서 카탈로그 작성자에 게 액세스 거 버 넌 스 위임-Azure Active Directory
description: 액세스를 관리할 수 있도록 IT 관리자의 액세스 관리를 카탈로그 작성자 및 프로젝트 관리자에 게 위임 하는 방법을 알아봅니다.
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
ms.openlocfilehash: da6183599f2dacd1a98d8fe359edda741b903b50
ms.sourcegitcommit: aef6040b1321881a7eb21348b4fd5cd6a5a1e8d8
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2019
ms.locfileid: "72170764"
---
# <a name="delegate-access-governance-to-catalog-creators-in-azure-ad-entitlement-management-preview"></a>Azure AD 자격 관리 (미리 보기)에서 카탈로그 작성자에 게 액세스 관리 위임

> [!IMPORTANT]
> Azure AD(Azure Active Directory) 권한 관리는 현재 공개 미리 보기로 제공됩니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.
> 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

자신의 카탈로그를 만들 수 있도록 관리자가 아닌 사용자에 게 위임 하려면 해당 사용자를 Azure AD 자격 관리에 정의 된 카탈로그 작성자 역할에 추가할 수 있습니다. 개별 사용자를 추가 하거나, 해당 멤버가 카탈로그를 만들 수 있는 그룹을 추가할 수 있습니다.

## <a name="as-an-it-administrator-delegate-to-a-catalog-creator"></a>IT 관리자는 카탈로그 작성자에 게 위임

카탈로그 작성자 역할에 사용자를 할당 하려면 다음 단계를 수행 합니다.

**필수 역할:** 전역 관리자 또는 사용자 관리자

1. Azure Portal에서 **Azure Active Directory**를 클릭한 다음, **Identity Governance**를 클릭합니다.

1. 왼쪽 메뉴의 **자격 관리** 섹션에서 **설정**을 클릭 합니다.

1. **편집**을 클릭합니다.

    ![카탈로그 작성자를 추가 하기 위한 설정](./media/entitlement-management-delegate/settings-delegate.png)

1. **위임 권한 관리** 섹션에서 **카탈로그 작성자 추가** 를 클릭 하 여이 자격 관리 역할을 위임할 사용자 또는 그룹을 선택 합니다.

1. **선택**을 클릭합니다.

1. **저장**을 클릭합니다.

## <a name="next-steps"></a>다음 단계

- [리소스 카탈로그 만들기 및 관리](entitlement-management-catalog-create.md)
- [액세스 관리를 위임 하 여 패키지 관리자 액세스](entitlement-management-delegate-managers.md)

