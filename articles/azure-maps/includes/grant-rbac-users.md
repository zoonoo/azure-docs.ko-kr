---
title: 사용자에 대한 역할 기반 액세스 권한 부여
titleSuffix: Azure Maps
description: 역할 기반 액세스 제어를 사용하여 사용자에게 Azure Maps에 대한 권한 부여
author: anastasia-ms
ms.author: v-stharr
ms.date: 06/17/2020
ms.topic: include
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 234c3358b98b7af677793fba58c1602a8bd976f0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101102821"
---
## <a name="grant-role-based-access-for-users-to-azure-maps"></a>사용자에게 Azure Maps에 대한 역할 기반 액세스 권한 부여

하나 이상의 Azure Maps 역할 정의에 Azure AD 그룹 또는 보안 주체를 할당하여 *Azure RBAC(Azure 역할 기반 액세스 제어)* 를 부여합니다. Azure Maps에 사용할 수 있는 Azure 역할 정의를 보려면 **Access control(IAM)** 로 이동합니다. **역할** 을 선택한 다음, *Azure Maps* 로 시작하는 역할을 검색합니다.

* Azure Maps에 대한 많은 사용자의 액세스 권한을 효율적으로 관리하려면 [Azure AD 그룹](../../active-directory/fundamentals/active-directory-manage-groups.md)을 참조하세요.
* 사용자가 애플리케이션에 대한 인증을 받을 수 있게 하려면 Azure AD에서 사용자를 만들어야 합니다. [Azure AD를 사용한 사용자 추가 또는 삭제](../../active-directory/fundamentals/add-users-azure-active-directory.md)를 참조하세요.

사용자의 디렉터리를 효과적으로 관리하려면 [Azure AD](../../active-directory/fundamentals/index.yml)에 대해 자세히 알아보세요.

1. **Azure Maps 계정** 으로 이동합니다. **액세스 제어(IAM)**  > **역할 할당** 을 선택합니다.

    ![Azure RBAC를 사용하여 액세스 권한 부여](../media/how-to-manage-authentication/how-to-grant-rbac.png)

2. **역할 할당** 탭의 **역할** 에서 **Azure Maps Data Reader** 또는 **Azure Maps Data Contributor** 와 같은 기본 제공 Azure Maps 역할 정의를 선택합니다. **액세스 할당** 에서 **Azure AD 사용자, 그룹 또는 서비스 보안 주체** 를 선택합니다. 이름으로 보안 주체를 선택합니다. 그런 다음 **저장** 을 선택합니다.

   * [Azure 역할 할당](../../role-based-access-control/role-assignments-portal.md)에 대한 세부 정보를 참조하세요.

> [!WARNING]
> Azure Maps 기본 제공 역할 정의는 많은 Azure Maps REST API에 강력한 액세스 권한을 부여합니다. 사용자에 대한 API를 최소한으로 제한하려면 [사용자 지정 역할 정의 만들기 및 사용자 지정 역할 정의에 사용자 할당](../../role-based-access-control/custom-roles.md)을 참조하세요. 이렇게 하면 사용자가 애플리케이션에 필요한 최소 권한을 가질 수 있습니다.