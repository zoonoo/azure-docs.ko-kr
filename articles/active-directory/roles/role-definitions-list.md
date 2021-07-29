---
title: Azure AD 역할 정의 나열 - Azure AD
description: Azure 기본 제공 및 사용자 지정 역할을 나열하는 방법을 알아봅니다.
services: active-directory
author: rolyon
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: how-to
ms.date: 03/07/2021
ms.author: rolyon
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 00fc768357d80fa22305b3b85e084dc636bd5a7c
ms.sourcegitcommit: 7f59e3b79a12395d37d569c250285a15df7a1077
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/02/2021
ms.locfileid: "110796413"
---
# <a name="list-azure-ad-role-definitions"></a>Azure AD 역할 정의 나열

역할 정의는 읽기, 쓰기 및 삭제와 같이 수행할 수 있는 작업 집합입니다. 일반적으로 단지 역할이라고 합니다. Azure Active Directory에는 60개 이상의 기본 제공 역할이 있거나 고유한 사용자 지정 역할을 만들 수 있습니다. "이 역할이 실제로 무엇을 수행할까?"가 궁금하다면 각 역할에 대한 자세한 권한 목록을 볼 수 있습니다.

이 문서에서는 권한과 함께 Azure AD 기본 제공 역할 및 사용자 지정 역할을 나열하는 방법을 설명합니다.

## <a name="list-all-roles"></a>모든 역할 나열

1. [Azure AD 관리 센터](https://aad.portal.azure.com)에 로그인하고 **Azure Active Directory** 를 선택합니다.

1. **역할 및 관리자** 를 선택하여 사용 가능한 역할 목록을 봅니다.

    ![Azure Portal의 역할 목록](./media/role-definitions-list/view-roles-in-azure-active-directory.png)

1. 오른쪽에서 줄임표를 선택한 후 **설명** 을 선택하여 역할에 대한 전체 권한 목록을 확인합니다.

    이 페이지에는 디렉터리 역할을 관리하는 데 도움이 되는 관련 설명서의 링크가 포함되어 있습니다.

    !["전역 관리자 - 설명" 페이지를 보여주는 스크린샷](./media/role-definitions-list/role-description.png)

## <a name="next-steps"></a>다음 단계

* 언제든지 [Azure AD 관리 역할 포럼](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032)에서 경험을 공유하세요.
* 역할 권한에 대한 자세한 내용은 [Azure AD 기본 제공 역할](permissions-reference.md)을 참조하세요.
* 기본 사용자 권한의 경우 [기본 게스트 및 멤버 사용자 권한 비교](../fundamentals/users-default-permissions.md)를 참조하세요.
