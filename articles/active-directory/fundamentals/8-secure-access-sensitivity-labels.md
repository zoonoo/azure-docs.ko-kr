---
title: 민감도 레이블을 사용하여 Azure Active Directory에서 리소스에 대한 외부 액세스를 제어합니다.
description: 외부 액세스를 위한 전체 보안 계획의 일부로 민감도 레이블을 사용합니다.
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 12/18/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6fc8ceddce4d41244d72632db058aa58c0d919db
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102565175"
---
# <a name="control-access-with-sensitivity-labels"></a>민감도 레이블을 사용한 액세스 제어 

[민감도 레이블](/microsoft-365/compliance/sensitivity-labels)은 Office 365 응용 프로그램의 콘텐츠 및 Microsoft Teams, Microsoft 365 Groups, SharePoint 사이트 같은 컨테이너에 대한 액세스를 제어하는 데 유용합니다. 사용자의 공동 작업 및 프로덕션 기능을 저해하지 않고 콘텐츠를 보호할 수 있습니다. 민감도 레이블을 사용하면 데이터를 보호하고 준수 및 보안 정책을 충족하는 동시에 장치, 앱 및 서비스 간에 조직의 콘텐츠를 보낼 수 있습니다. 

민감도 레이블을 사용하여 수행할 수 있는 작업은 다음과 같습니다.

* **보호 설정을 추가하지 않고 콘텐츠 분류**. 콘텐츠를 사용 및 공유할 때 콘텐츠와 함께 이동하고 지속되는 분류를 스티커처럼 콘텐츠에 할당할 수 있습니다. 이 분류를 사용하여 사용 현황 보고서를 생성하고 중요한 콘텐츠의 활동 데이터를 볼 수 있습니다.

* **암호화, 워터마크, 액세스 제한 등의 보호 설정 적용**. 예를 들어 사용자는 문서 또는 이메일에 기밀 레이블을 적용할 수 있으며, 해당 레이블로 [콘텐츠를 암호화](/microsoft-365/compliance/encryption-sensitivity-labels) 하고 "기밀" 워터마크를 추가할 수 있습니다. 또한 SharePoint 사이트와 같은 [민감도 레이블을 컨테이너에 적용](/microsoft-365/compliance/sensitivity-labels-teams-groups-sites) 하고 외부 사용자가 컨테이너에 포함된 콘텐츠에 액세스할 수 있는지 여부를 적용할 수 있습니다.

이메일 및 기타 콘텐츠에 대한 민감도 레이블은 콘텐츠와 함께 이동합니다. 컨테이너의 민감도 레이블은 컨테이너에 대한 액세스를 제한할 수 있지만, 컨테이너의 콘텐츠가 레이블을 상속하지는 않습니다. 예를 들어 사용자가 보호된 사이트에서 콘텐츠를 가져와 다운로드한 다음 콘텐츠에 민감도 레이블이 없는 한 제한 없이 공유할 수 있습니다.

 >[!NOTE]
>민감도 레이블을 적용하려면 사용자가 Microsoft 회사 또는 학교 계정으로 로그인해야 합니다. 

 
## <a name="permissions-necessary-to-create-and-manage-sensitivity-levels"></a>민감도 수준을 만들고 관리하는 데 필요한 권한

민감도 레이블을 만들 준수팀의 구성원에게는 Microsoft 365 규정 준수 센터, Microsoft 365 보안 센터 또는 보안 및 준수 센터에 대한 권한이 필요합니다.

기본적으로 테넌트의 전역 관리자는 이러한 관리 센터에 액세스할 수 있으며, 테넌트 관리자의 모든 권한을 부여하지 않고도 준수 관리자 및 기타 사용자에게 액세스 권한을 부여할 수 있습니다. 이 위임받은 제한된 관리자 액세스 권한의 경우 준수 데이터 관리자, 준수 관리자 또는 보안 관리자 역할 그룹에 사용자를 추가합니다.

 

## <a name="determine-your-sensitivity-label-strategy"></a>민감도 레이블 전략 결정

콘텐츠에 대한 외부 액세스 권한 관리에 대해 생각할 때 다음을 결정합니다.

**모든 콘텐츠 및 컨테이너**

* HBI, MBI, LBI(높음, 중간 또는 낮음 비즈니스 영향)를 정의하는 방법은 무엇인가요? 특정 유형의 콘텐츠를 부적절하게 공유하는 경우 조직에 미치는 영향을 고려합니다.

   * 신용 카드 또는 여권 번호와 같이 본질적으로 [중요한 콘텐츠](/microsoft-365/compliance/apply-sensitivity-label-automatically)의 특정 유형이 포함된 콘텐츠

   * 특정 그룹 또는 사용자가 만든 콘텐츠(예: 준수 관리자, 재무 관리자 또는 임원)

   * 특정 라이브러리 또는 사이트의 콘텐츠. 예를 들어 조직 전략 또는 비공개 재무 데이터를 호스팅하는 컨테이너

   * 기타 조건

* 외부 사용자의 액세스를 제한해야 하는 콘텐츠 범주에는 무엇이 있나요(예: HBI 콘텐츠)?

   * 제한 사항에는 컨테이너에 대한 액세스 제한 및 콘텐츠 암호화와 같은 작업이 포함될 수 있습니다.

* HBI 데이터, 사이트 또는 Microsoft 365 그룹에 대한 기본값은 무엇입니까?

* [레이블 지정 및 모니터링](/microsoft-365/compliance/sensitivity-labels)은 [암호화 적용](/microsoft-365/compliance/encryption-sensitivity-labels) 이나 [컨테이너 액세스 제한](/microsoft-365/compliance/sensitivity-labels-teams-groups-sites) 에 사용할 때 어디에 사용합니까?

**이메일 및 콘텐츠**

* 콘텐츠에 [민감도 레이블을 자동으로 적용](/microsoft-365/compliance/apply-sensitivity-label-automatically) 하시겠습니까, 아니면 수동으로 적용하시겠습니까?

   * 수동으로 적용하도록 선택하는 경우 [사용자가 레이블을 적용하도록 권장](/microsoft-365/compliance/apply-sensitivity-label-automatically)하시겠습니까?

**컨테이너**

* M365 Groups, Teams, SharePoint 사이트에서 민감도 레이블을 사용하여 액세스를 제한해야 하는지를 결정하는 기준은 무엇입니까?

* 앞으로 이러한 컨테이너의 콘텐츠에만 레이블을 지정하시겠습니까, 아니면 SharePoint 및 OneDrive의 기존 파일에 [자동으로 레이블을 지정](/microsoft-365/compliance/apply-sensitivity-label-automatically) 하시겠습니까?

민감도 레이블을 사용하는 방법에 대한 다른 아이디어는 [민감도 레이블에 대한 일반적인 시나리오](/microsoft-365/compliance/get-started-with-sensitivity-labels)를 참조하세요.

### <a name="sensitivity-labels-on-email-and-content"></a>이메일 및 콘텐츠에 대한 민감도 레이블

문서 또는 이메일에 민감도 레이블을 할당하는 경우, 레이블은 사용자 지정 가능하고 영구적인 일반 텍스트에 적용되는 스탬프와 같습니다. 

* **사용자 지정 가능함** 은 조직에 적합한 레이블을 만들고 이를 적용할 때 발생하는 일을 결정할 수 있음을 의미합니다.

* **일반 텍스트** 는 항목의 메타데이터에 포함되어 있고 응용 프로그램 및 서비스에서 읽을 수 있으므로 자체 보호 작업을 적용할 수 있음을 의미합니다.

* **영구적** 은 레이블 및 관련 보호 조치가 콘텐츠와 함께 로밍되며, 정책 적용 및 집행의 기반이 됨을 의미합니다.

 

> [!NOTE]
> 콘텐츠의 각 항목에는 단일 민감도 레이블이 적용될 수 있습니다.


### <a name="sensitivity-labels-on-containers"></a>컨테이너의 민감도 레이블

[Microsoft 365 Groups](../enterprise-users/groups-assign-sensitivity-labels.md), [Microsoft Teams](/microsoft-365/compliance/sensitivity-labels-teams-groups-sites), [SharePoint 사이트](/microsoft-365/compliance/sensitivity-labels-teams-groups-sites)와 같은 컨테이너에 민감도 레이블을 적용할 수 있습니다. 지원되는 컨테이너에 이 민감도 레이블을 적용하면 레이블이 연결된 사이트 또는 그룹에 분류 및 보호 설정을 자동으로 적용합니다. 이러한 컨테이너의 민감도 레이블은 컨테이너의 다음 측면을 제어할 수 있습니다.

* **개인 정보**. 특정 사용자, 모든 내부 사용자 또는 모든 사용자 등 사이트를 볼 수 있는 대상을 선택할 수 있습니다.

* **외부 사용자 액세스**. 그룹 소유자가 그룹에 게스트를 추가할 수 있는지 여부를 제어합니다.

* **관리되지 않는 장치에서의 액세스**. 관리되지 않는 장치에서 콘텐츠에 액세스할 수 있는지 여부 및 방법을 결정합니다.

 

![민감도 레이블 편집 스크린샷](media/secure-external-access/8-edit-label.png)

 

SharePoint 사이트와 같은 컨테이너에 민감도 레이블을 적용하는 경우, 이 레이블은 콘텐츠에 적용되지 않습니다. 컨테이너의 민감도 레이블은 컨테이너 내의 콘텐츠에 대한 액세스를 제어합니다. 

* 컨테이너 내의 콘텐츠에 레이블을 자동으로 적용하려면 [콘텐츠에 민감도를 자동으로 적용](/microsoft-365/compliance/apply-sensitivity-label-automatically)을 참조하세요.

* 사용자가 이 콘텐츠에 레이블을 수동으로 적용할 수 있도록 하려면, [SharePoint 및 OneDrive에서 Office 파일의 민감도 레이블을 사용하도록 설정](/microsoft-365/compliance/sensitivity-labels-sharepoint-onedrive-files)했는지 확인합니다.

### <a name="plan-to-implement-sensitivity-labels"></a>민감도 레이블 구현 계획

민감도 레이블을 사용하는 방법과 적용하려는 콘텐츠 및 사이트를 결정한 후 구현하는 데 도움을 받으려면 다음 설명서를 참조하세요.

1. [민감도 레이블 시작](/microsoft-365/compliance/get-started-with-sensitivity-labels)

2. [배포 전략 만들기](/microsoft-365/compliance/get-started-with-sensitivity-labels)

3. [민감도 레이블 만들기 및 게시](/microsoft-365/compliance/create-sensitivity-labels)

4. [민감도 레이블을 통해 암호화를 적용하여 콘텐츠에 대한 액세스 제한](/microsoft-365/compliance/encryption-sensitivity-labels)

5. [팀, 그룹 및 사이트에서 민감도 레이블 사용](/microsoft-365/compliance/sensitivity-labels-teams-groups-sites)

6. [SharePoint 및 OneDrive에서 Office 파일의 민감도 레이블 사용](/microsoft-365/compliance/sensitivity-labels-sharepoint-onedrive-files)

### <a name="next-steps"></a>다음 단계

리소스에 대한 외부 액세스 보안에 관해 다음 문서를 참조하세요. 이러한 작업은 나열된 순서에 따라 수행하는 것이 좋습니다.

1. [외부 액세스에 대해 원하는 보안 태세 결정](1-secure-access-posture.md)

2. [현재 상태 검색](2-secure-access-current-state.md)

3. [거버넌스 계획 만들기](3-secure-access-plan.md)

4. [보안을 위해 그룹 사용](4-secure-access-groups.md)

5. [Azure AD B2B로 전환](5-secure-access-b2b.md)

6. [권한 관리를 사용한 보안 액세스](6-secure-access-entitlement-managment.md)

7. [조건부 액세스 정책을 사용한 보안 액세스](7-secure-access-conditional-access.md)

8. [민감도 레이블을 사용한 보안 액세스](8-secure-access-sensitivity-labels.md)(현재 여기에 있습니다.)

9. [Microsoft Teams, OneDrive 및 SharePoint에 대한 보안 액세스](9-secure-access-teams-sharepoint.md)