---
author: baanders
description: Azure Digital Twins 설정에서 사용 권한 필수 구성 요소에 대 한 파일 포함
ms.service: digital-twins
ms.topic: include
ms.date: 10/14/2020
ms.author: baanders
ms.openlocfilehash: 919cc934920d922a82ceb9e30630006627af44e9
ms.sourcegitcommit: 957c916118f87ea3d67a60e1d72a30f48bad0db6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/19/2020
ms.locfileid: "92205495"
---
## <a name="prerequisites-permission-requirements"></a>필수 조건: 사용 권한 요구 사항

이 문서의 모든 단계를 완료 하려면 [구독에](../articles/role-based-access-control/rbac-and-directory-admin-roles.md) 다음 권한이 있는 역할이 있어야 합니다.
* Azure 리소스 만들기 및 관리
* Azure 리소스에 대 한 사용자 액세스 관리 (권한 부여 및 위임 포함)

이 요구 사항을 충족 하는 일반적인 역할은 *소유자*, *계정 관리자*또는 *사용자 액세스 관리자* 와 *참가자*의 조합입니다. 다른 역할에 포함 된 사용 권한을 비롯 하 여 역할 및 권한에 대 한 전체 설명은 Azure RBAC 설명서의 [*클래식 구독 관리자 역할, azure 역할 및 AZURE AD 역할*](../articles/role-based-access-control/rbac-and-directory-admin-roles.md) 을 참조 하세요.

구독에서 역할을 보려면 Azure Portal의 [구독 페이지](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) 를 방문 하세요 .이 링크를 사용 하거나 포털 검색 표시줄을 사용 하 여 *구독* 을 찾을 수 있습니다. 사용 중인 구독의 이름을 찾고 *내 역할* 열에서 해당 역할의 역할을 확인 합니다.

:::image type="content" source="../articles/digital-twins/media/how-to-set-up-instance/portal/subscriptions-role.png" alt-text="사용자를 소유자로 표시 하는 Azure Portal의 구독 페이지 보기" lightbox="../articles/digital-twins/media/how-to-set-up-instance/portal/subscriptions-role.png":::

값이 *참여자*이거나 위에서 설명한 필수 사용 권한이 없는 다른 역할을 찾은 경우 이러한 *권한이 있는 구독* (예: 구독 소유자 또는 계정 관리자)의 사용자에 게 연락 하 여 다음 방법 중 하나를 진행할 수 있습니다.
* 사용자 대신이 문서의 단계를 완료 하도록 요청 합니다.
* 사용자가 직접 계속할 수 있는 권한을 갖도록 구독에 대 한 역할을 승격 하도록 요청 합니다. 이 방법이 적절 한지 여부는 조직 및 조직 내 사용자의 역할에 따라 달라 집니다.