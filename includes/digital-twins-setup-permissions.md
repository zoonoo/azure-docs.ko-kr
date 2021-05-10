---
author: baanders
description: Azure Digital Twins 설정의 권한 필수 구성 요소에 대한 포함 파일
ms.service: digital-twins
ms.topic: include
ms.date: 10/14/2020
ms.author: baanders
ms.openlocfilehash: 919cc934920d922a82ceb9e30630006627af44e9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "92205495"
---
## <a name="prerequisites-permission-requirements"></a>필수 조건: 권한 요구 사항

이 문서의 모든 단계를 완료하려면 [구독에 다음 권한이 있는 역할](../articles/role-based-access-control/rbac-and-directory-admin-roles.md)이 있어야 합니다.
* Azure 리소스 만들기 및 관리
* Azure 리소스에 대한 사용자 액세스 관리(권한 부여 및 위임 포함)

이 요구 사항을 충족하는 일반적인 역할은 *소유자*, *계정 관리자* 또는 *사용자 액세스 관리자* 와 *기여자* 의 조합입니다. 다른 역할에 포함된 권한을 포함하여 역할 및 권한에 대한 자세한 설명은 Azure RBAC 설명서에서 [*클래식 구독 관리자 역할, Azure 역할 및 Azure AD 역할*](../articles/role-based-access-control/rbac-and-directory-admin-roles.md)을 참조하세요.

구독에서 역할을 보려면 Azure Portal의 [구독 페이지](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)를 방문하세요. 이 링크를 사용하거나 포털 검색 창에서 *구독* 을 찾을 수 있습니다. 사용 중인 구독의 이름을 찾고 *내 역할* 열에서 해당 구독에 대한 사용자 역할을 확인합니다.

:::image type="content" source="../articles/digital-twins/media/how-to-set-up-instance/portal/subscriptions-role.png" alt-text="사용자를 소유자로 표시하는 Azure Portal의 구독 페이지 보기" lightbox="../articles/digital-twins/media/how-to-set-up-instance/portal/subscriptions-role.png":::

값이 *기여자* 또는 위에 설명된 필수 권한이 없는 다른 역할인 경우 이러한 권한이 *있는* 구독의 사용자(예: 구독 소유자 또는 계정 관리자)에게 연락하여 다음 방법 중 하나로 진행할 수 있습니다.
* 사용자 대신 이 문서의 단계를 완료하도록 요청
* 사용자가 직접 진행할 수 있는 권한을 갖게 구독에 대한 역할을 승격 하도록 요청합니다. 이것이 적절한지 여부는 조직과 조직 내의 사용자 역할에 따라 다릅니다.