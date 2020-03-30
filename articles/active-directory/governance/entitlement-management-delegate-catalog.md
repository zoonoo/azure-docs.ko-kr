---
title: Azure AD 권한 관리 - Azure Active Directory에서 카탈로그 작성자에게 액세스 거버넌스 위임
description: 액세스 거버넌스를 IT 관리자에서 카탈로그 작성자 및 프로젝트 관리자에게 위임하여 액세스 권한을 스스로 관리할 수 있도록 하는 방법을 알아봅니다.
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
ms.date: 10/26/2019
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 51e37ad006da5042291614c773f242b5a1f3be97
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77120185"
---
# <a name="delegate-access-governance-to-catalog-creators-in-azure-ad-entitlement-management"></a>Azure AD 권한 관리에서 카탈로그 작성자에게 액세스 거버넌스 위임

카탈로그는 리소스 및 액세스 패키지의 컨테이너입니다. 관련 리소스및 액세스 패키지를 그룹화하려는 경우 카탈로그를 만듭니다. 기본적으로 글로벌 관리자 또는 사용자 관리자는 [카탈로그를 만들](entitlement-management-catalog-create.md)수 있으며 추가 사용자를 카탈로그 소유자로 추가할 수 있습니다.

관리자가 아닌 사용자에게 위임하여 자체 카탈로그를 만들 수 있도록 해당 사용자를 Azure AD 권한 관리 정의 카탈로그 작성자 역할에 추가할 수 있습니다. 개별 사용자를 추가하거나 구성원을 카탈로그를 만들 수 있는 그룹을 추가할 수 있습니다.  카탈로그를 만든 후 나중에 자신의 카탈로그에 소유한 리소스를 추가할 수 있습니다.

## <a name="as-an-it-administrator-delegate-to-a-catalog-creator"></a>IT 관리자는 카탈로그 작성자에게 위임합니다.

다음 단계에 따라 사용자를 카탈로그 작성자 역할에 할당합니다.

**필수 구성 조건 역할:** 글로벌 관리자 또는 사용자 관리자

1. Azure Portal에서 **Azure Active Directory**를 클릭한 다음, **Identity Governance**를 클릭합니다.

1. 왼쪽 메뉴에서 권한 **관리** 섹션에서 **설정을**클릭합니다.

1. **편집**을 클릭합니다.

    ![카탈로그 작성자 추가 설정](./media/entitlement-management-delegate-catalog/settings-delegate.png)

1. **대리자 권한 관리** 섹션에서 **카탈로그 작성자 추가를** 클릭하여 이 권한 관리 역할을 위임할 사용자 또는 그룹을 선택합니다.

1. **선택**을 클릭합니다.

1. **저장**을 클릭합니다.

## <a name="allow-delegated-roles-to-access-the-azure-portal"></a>위임된 역할이 Azure 포털에 액세스할 수 있도록 허용

카탈로그 작성자 및 액세스 패키지 관리자와 같은 위임된 역할이 Azure 포털에 액세스하여 액세스 패키지를 관리할 수 있도록 하려면 관리 포털 설정을 확인해야 합니다.

**필수 구성 조건 역할:** 글로벌 관리자 또는 사용자 관리자

1. Azure 포털에서 **Azure Active 디렉터리를** 클릭한 다음 **사용자를**클릭합니다.

1. 왼쪽 메뉴에서 **사용자 설정을 클릭합니다.**

1. Azure **AD 관리 포털에 대한 액세스 제한이** **아니오로**설정되어 있는지 확인합니다.

    ![Azure AD 사용자 설정 - 관리 포털](./media/entitlement-management-delegate-catalog/user-settings.png)

## <a name="next-steps"></a>다음 단계

- [리소스 카탈로그 생성 및 관리](entitlement-management-catalog-create.md)
- [패키지 관리자에 액세스하기 위해 액세스 거버넌스 위임](entitlement-management-delegate-managers.md)

