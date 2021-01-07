---
title: 사용자에 대 한 역할 기반 액세스 권한 부여
titleSuffix: Azure Maps
description: 역할 기반 액세스 제어를 사용 하 여 사용자에 게 권한 부여 Azure Maps
author: anastasia-ms
ms.author: v-stharr
ms.date: 06/17/2020
ms.topic: include
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 19806fe24d0ff3b87ebe61b45ac302947c734fa0
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/28/2020
ms.locfileid: "92895571"
---
## <a name="grant-role-based-access-for-users-to-azure-maps"></a>Azure Maps 사용자에 게 역할 기반 액세스 권한 부여

Azure AD 그룹 또는 보안 주체를 하나 이상의 Azure Maps 역할 정의에 할당 하 여 azure *RBAC (역할 기반 액세스 제어)* 를 부여 합니다. Azure Maps 사용할 수 있는 Azure 역할 정의를 보려면 **Access control (IAM)** 로 이동 합니다. **역할** 을 선택한 다음 *Azure Maps* 로 시작 하는 역할을 검색 합니다.

* Azure Maps에 대 한 많은 양의 사용자 액세스를 효율적으로 관리 하려면 [AZURE AD 그룹](../../active-directory/fundamentals/active-directory-manage-groups.md)을 참조 하세요.
* 사용자가 응용 프로그램에 대 한 인증을 받을 수 있도록 하려면 Azure AD에서 사용자를 만들어야 합니다. [AZURE AD를 사용 하 여 사용자 추가 또는 삭제](../../active-directory/fundamentals/add-users-azure-active-directory.md)를 참조 하세요.

[AZURE AD](../../active-directory/fundamentals/index.yml) 에서 사용자를 위한 디렉터리를 효과적으로 관리 하는 방법에 대해 자세히 알아보세요.

1. **Azure Maps 계정** 으로 이동 합니다. **액세스 제어 (IAM)**  >  **역할 할당** 을 선택 합니다.

    ![Azure RBAC를 사용 하 여 액세스 권한 부여](../media/how-to-manage-authentication/how-to-grant-rbac.png)

2. **역할 할당** 탭의 **역할** 에서 **Azure Maps 데이터 판독기** 또는 **Azure Maps 데이터 참가자** 와 같은 기본 제공 Azure Maps 역할 정의를 선택 합니다. **액세스 할당** 에서 **Azure AD 사용자, 그룹 또는 서비스 보안 주체** 를 선택합니다. 이름으로 보안 주체를 선택 합니다. 그런 다음 **저장** 을 선택합니다.

   * [역할 할당 추가 또는 제거](../../role-based-access-control/role-assignments-portal.md)에 대 한 세부 정보를 참조 하세요.

> [!WARNING]
> Azure Maps 기본 제공 역할 정의는 많은 Azure Maps REST Api에 대 한 매우 큰 권한 부여 액세스를 제공 합니다. 사용자에 대 한 Api를 최소한으로 제한 하려면 [사용자 지정 역할 정의 만들기 및](../../role-based-access-control/custom-roles.md) 사용자 지정 역할 정의에 사용자 할당을 참조 하세요. 이렇게 하면 사용자가 응용 프로그램에 필요한 최소 권한을 가질 수 있습니다.