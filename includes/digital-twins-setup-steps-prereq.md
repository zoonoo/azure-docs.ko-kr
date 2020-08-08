---
author: baanders
description: Azure Digital Twins 설정의 단계 개요 및 사용 권한 필수 구성 요소에 대 한 포함 파일
ms.service: digital-twins
ms.topic: include
ms.date: 8/7/2020
ms.author: baanders
ms.openlocfilehash: b87d45a8be68a77df7b06ebd6e80562ccbe0e444
ms.sourcegitcommit: 98854e3bd1ab04ce42816cae1892ed0caeedf461
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/07/2020
ms.locfileid: "88009664"
---
>[!NOTE]
>이러한 작업은 사용자가 Azure 구독에 대 한 리소스 및 사용자 액세스를 모두 관리할 수 있는 기능을 제공 하기 위한 것입니다. 일부 단계는 낮은 권한으로 완료할 수 있지만 사용 가능한 인스턴스를 완전히 설정 하려면 이러한 사용 권한을 가진 사람의 협력이 필요 합니다. 이에 대 한 자세한 내용은 아래의 [*필수 조건: 필수 권한*](#prerequisites-permission-requirements) 섹션을 참조 하세요.

새 Azure Digital Twins 인스턴스에 대 한 전체 설치는 다음 세 부분으로 구성 됩니다.
1. **인스턴스 만들기**
2. **사용자 액세스 권한 설정**: azure 사용자는 Azure digital twins 인스턴스에서 azure *디지털 쌍 소유자 (미리 보기)* 역할을 사용 하 여 it 및 해당 데이터를 관리할 수 있어야 합니다. 이 단계에서는 azure 구독의 소유자/관리자가 Azure Digital Twins 인스턴스를 관리 하는 사용자에 게이 역할을 할당 합니다. 사용자 또는 조직에서 다른 사람이 될 수 있습니다.
3. **클라이언트 응용 프로그램에 대 한 액세스 권한 설정**: 인스턴스와 상호 작용 하는 데 사용 되는 클라이언트 응용 프로그램을 작성 하는 것이 일반적입니다. 해당 클라이언트 앱이 Azure Digital Twins에 액세스 하려면 클라이언트 응용 프로그램이 인스턴스에 인증 하는 데 사용할 [Azure Active Directory](../articles/active-directory/fundamentals/active-directory-whatis.md) 에서 *앱 등록* 을 설정 해야 합니다.

계속 하려면 Azure 구독이 필요 합니다. [여기](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)에서 무료로 하나를 설정할 수 있습니다.

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