---
title: 민감도 레이블을 사용 하 여 Azure Active Directory에서 리소스에 대 한 외부 액세스를 제어 합니다.
description: 외부 액세스를 위한 전체 보안 계획의 일부로 민감도 레이블을 사용 합니다.
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
ms.openlocfilehash: 0dcb6d24eacbe594a907f084874e76fea963c561
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/23/2021
ms.locfileid: "98725131"
---
# <a name="control-access-with-sensitivity-labels"></a>민감도 레이블을 사용 하 여 액세스 제어 

[민감도 레이블은](/microsoft-365/compliance/sensitivity-labels?view=o365-worldwide) Office 365 응용 프로그램의 콘텐츠 및 Microsoft 팀, Microsoft 365 그룹 및 SharePoint 사이트와 같은 컨테이너에 대 한 액세스를 제어 하는 데 도움이 됩니다. 사용자의 공동 작업 및 프로덕션 기능을 hindering 하지 않고 콘텐츠를 보호할 수 있습니다. 민감도 레이블을 사용 하면 데이터를 보호 하 고 규정 준수 및 보안 정책을 충족 하는 동시에 장치, 앱 및 서비스 간에 조직의 콘텐츠를 보낼 수 있습니다. 

민감도 레이블을 사용 하 여 다음을 수행할 수 있습니다.

* **보호 설정을 추가 하지 않고 콘텐츠를 분류** 합니다. 콘텐츠 (예: 스티커)에 사용 하 고 공유 하는 콘텐츠를 유지 하 고 보관 하는 분류를 할당할 수 있습니다. 이 분류를 사용하여 사용 현황 보고서를 생성하고 중요한 콘텐츠의 활동 데이터를 볼 수 있습니다.

* **암호화, 워터 마크, 액세스 제한 등의 보호 설정을 적용** 합니다. 예를 들어 사용자는 문서 또는 전자 메일에 기밀 레이블을 적용할 수 있으며,이 레이블은 [콘텐츠를 암호화](/microsoft-365/compliance/encryption-sensitivity-labels?view=o365-worldwide) 하 고 "기밀" 워터 마크를 추가할 수 있습니다. 또한 SharePoint 사이트와 같은 [컨테이너에 민감도 레이블을 적용](/microsoft-365/compliance/sensitivity-labels-teams-groups-sites?view=o365-worldwide) 하 고 외부 사용자가 포함 하는 콘텐츠에 액세스할 수 있는지 여부를 적용할 수 있습니다.

전자 메일 및 기타 콘텐츠에 대 한 민감도 레이블은 콘텐츠와 함께 이동 합니다. 컨테이너의 민감도 레이블은 컨테이너에 대 한 액세스를 제한할 수 있지만 컨테이너의 콘텐츠는 레이블을 상속 하지 않습니다. 예를 들어, 사용자가 보호 된 사이트에서 콘텐츠를 가져와서 다운로드 한 다음 콘텐츠에 민감도 레이블을 포함 하지 않는 한 제한 없이 공유할 수 있습니다.

 >[!NOTE]
>민감도 레이블을 적용 하려면 사용자에 게 Microsoft 회사 또는 학교 계정으로 로그인 해야 합니다. 

 
## <a name="permissions-necessary-to-create-and-manage-sensitivity-levels"></a>민감도 수준을 만들고 관리 하는 데 필요한 권한

민감도 레이블을 만들 규정 준수 팀의 구성원은 Microsoft 365 준수 센터, Microsoft 365 Security Center 또는 보안 & 준수 센터에 대 한 권한이 필요 합니다.

기본적으로 테 넌 트의 전역 관리자는 이러한 관리 센터에 액세스할 수 있으며, 테 넌 트 관리자의 모든 권한을 부여 하지 않고도 규정 준수 책임자 및 기타 사용자에 게 액세스 권한을 부여할 수 있습니다. 이 위임 된 관리자 액세스에 대해 준수 데이터 관리자, 준수 관리자 또는 보안 관리자 역할 그룹에 사용자를 추가 합니다.

 

## <a name="determine-your-sensitivity-label-strategy"></a>민감도 레이블 전략 결정

콘텐츠에 대 한 외부 액세스를 관리 하는 것에 대해 생각해 볼 때 다음 사항을 확인 합니다.

**모든 콘텐츠 및 컨테이너에 대해**

* 높음, 중간 또는 낮음 비즈니스 영향 (HBI, MBI, LBI)을 정의 하는 방법은 무엇입니까? 특정 유형의 콘텐츠를 부적절 하 게 공유 하는 경우 조직에 미치는 영향을 고려해 야 합니다.

   * 신용 카드 또는 여권 번호와 같이 본질적으로 [중요 한](/microsoft-365/compliance/apply-sensitivity-label-automatically?view=o365-worldwide)특정 유형의 콘텐츠를 포함 하는 콘텐츠

   * 특정 그룹 또는 사용자가 만든 콘텐츠 (예: 규정 준수 직원, 금융 담당자 또는 임원)

   * 특정 라이브러리 또는 사이트의 콘텐츠 예: 조직 전략 또는 비공개 재무 데이터를 호스팅하는 컨테이너

   * 기타 조건

* 외부 사용자의 액세스를 제한 해야 하는 콘텐츠 범주 (예: HBI 콘텐츠)

   * 제한 사항에는 컨테이너에 대 한 액세스 제한 및 콘텐츠 암호화와 같은 작업이 포함 될 수 있습니다.

* HBI 데이터, 사이트 또는 Microsoft 365 그룹에 대 한 기본값은 무엇 인가요?

* 에서 민감도 레이블을 사용 하 여 [암호화를 적용](/microsoft-365/compliance/encryption-sensitivity-labels?view=o365-worldwide) 하거나 [컨테이너 액세스 제한을 적용](/microsoft-365/compliance/sensitivity-labels-teams-groups-sites?view=o365-worldwide)하는 것과 비교 하 여 [레이블 및 모니터링](/microsoft-365/compliance/label-analytics?view=o365-worldwide)을 수행 하나요?

**전자 메일 및 콘텐츠**

* 콘텐츠에 [민감도 레이블을 자동으로 적용](/microsoft-365/compliance/apply-sensitivity-label-automatically?view=o365-worldwide) 하 시겠습니까, 아니면 수동으로 적용 하 시겠습니까?

   * 수동으로 선택 하 [는 경우 사용자가 레이블을 적용 하도록 권장](/microsoft-365/compliance/apply-sensitivity-label-automatically?view=o365-worldwide)하 시겠습니까?

**컨테이너의 경우**

* M365 그룹, 팀 또는 SharePoint 사이트에서 민감도 레이블을 사용 하 여 액세스를 제한 해야 하는지 여부를 결정 하는 조건

* 이러한 컨테이너의 콘텐츠를 앞으로 이동 하거나 SharePoint 및 OneDrive에서 기존 파일에 [레이블을 자동으로](/microsoft-365/compliance/apply-sensitivity-label-automatically?view=o365-worldwide) 표시 하 시겠습니까?

민감도 레이블을 사용 하는 방법에 대 한 다른 아이디어는 [민감도 레이블에 대 한 일반적인 시나리오](/microsoft-365/compliance/get-started-with-sensitivity-labels?view=o365-worldwide) 를 참조 하세요.

### <a name="sensitivity-labels-on-email-and-content"></a>전자 메일 및 콘텐츠에 대 한 민감도 레이블

문서 또는 전자 메일에 민감도 레이블을 할당 하는 경우 사용자 지정 가능 하 고 일반 텍스트 및 영구 콘텐츠에 적용 되는 스탬프와 같습니다. 

* **사용자 지정 가능** 은 조직에 적합 한 레이블을 만들고 적용 될 때 발생 하는 상황을 확인할 수 있습니다.

* **일반 텍스트** 는 항목의 메타 데이터에 포함 되어 있으며 응용 프로그램 및 서비스에서 읽을 수 있으므로 자체 보호 작업을 적용할 수 있습니다.

* **Persistent** 는 레이블과 모든 관련 보호가 콘텐츠와 함께 로밍 되 고 정책을 적용 하 고 적용 하기 위한 기초가 됨을 의미 합니다.

 

> [!NOTE]
> 콘텐츠의 각 항목에는 단일 민감도 레이블이 적용 될 수 있습니다.


### <a name="sensitivity-labels-on-containers"></a>컨테이너의 민감도 레이블

[Microsoft 365 그룹](../enterprise-users/groups-assign-sensitivity-labels.md), [Microsoft 팀](/microsoft-365/compliance/sensitivity-labels-teams-groups-sites?view=o365-worldwide)및 [SharePoint 사이트](/microsoft-365/compliance/sensitivity-labels-teams-groups-sites?view=o365-worldwide)와 같은 컨테이너에 민감도 레이블을 적용할 수 있습니다. 지원 되는 컨테이너에이 민감도 레이블을 적용 하면 레이블은 자동으로 분류 및 보호 설정을 연결 된 사이트 또는 그룹에 적용 합니다. 이러한 컨테이너의 민감도 레이블은 컨테이너의 다음 측면을 제어할 수 있습니다.

* **개인 정보**. 사이트를 볼 수 있는 사용자, 특정 사용자, 모든 내부 사용자 또는 모든 사용자를 선택할 수 있습니다.

* **외부 사용자 액세스**. 그룹 소유자가 그룹에 게스트를 추가할 수 있는지 여부를 제어 합니다.

* **관리 되지 않는 장치에서 액세스**. 관리 되지 않는 장치에서 콘텐츠에 액세스할 수 있는지 여부 및 방법을 결정 합니다.

 

![민감도 레이블 편집 스크린샷](media/secure-external-access/8-edit-label.png)

 

SharePoint 사이트와 같은 컨테이너에 민감도 레이블을 적용 하는 경우이 레이블은 콘텐츠에 적용 되지 않습니다. 컨테이너의 민감도 레이블은 컨테이너 내의 콘텐츠에 대 한 액세스를 제어 합니다. 

* 컨테이너 내의 콘텐츠에 레이블을 자동으로 적용 하려면 [콘텐츠에 민감도를 자동으로 적용](/microsoft-365/compliance/apply-sensitivity-label-automatically?view=o365-worldwide)을 참조 하세요.

* 사용자가이 콘텐츠에 레이블을 수동으로 적용할 수 있도록 하려면 [SharePoint 및 OneDrive에서 Office 파일의 민감도 레이블을 사용 하도록 설정](/microsoft-365/compliance/sensitivity-labels-sharepoint-onedrive-files?view=o365-worldwide)해야 합니다.

### <a name="plan-to-implement-sensitivity-labels"></a>민감도 레이블 구현 계획

민감도 레이블을 사용 하는 방법 및 적용 하려는 콘텐츠와 사이트를 결정 한 후에는 다음 설명서를 참조 하 여 구현을 수행 하는 데 도움이 됩니다.

1. [민감도 레이블 시작](/microsoft-365/compliance/get-started-with-sensitivity-labels?view=o365-worldwide)

2. [배포 전략 만들기](/microsoft-365/compliance/get-started-with-sensitivity-labels?view=o365-worldwide)

3. [민감도 레이블 만들기 및 게시](/microsoft-365/compliance/create-sensitivity-labels?view=o365-worldwide)

4. [민감도 레이블을 사용 하 여 콘텐츠에 대 한 액세스를 제한 하 여 암호화 적용](/microsoft-365/compliance/encryption-sensitivity-labels?view=o365-worldwide)

5. [팀, 그룹 및 사이트에서 민감도 레이블 사용](/microsoft-365/compliance/sensitivity-labels-teams-groups-sites?view=o365-worldwide)

6. [SharePoint 및 OneDrive에서 Office 파일의 민감도 레이블 사용](/microsoft-365/compliance/sensitivity-labels-sharepoint-onedrive-files?view=o365-worldwide)

### <a name="next-steps"></a>다음 단계

리소스에 대 한 외부 액세스 보안에 대 한 다음 문서를 참조 하세요. 이러한 작업은 나열 된 순서 대로 수행 하는 것이 좋습니다.

1. [외부 액세스를 위해 원하는 보안 상태 결정](1-secure-access-posture.md)

2. [현재 상태 검색](2-secure-access-current-state.md)

3. [거 버 넌 스 계획 만들기](3-secure-access-plan.md)

4. [보안을 위해 그룹 사용](4-secure-access-groups.md)

5. [Azure AD B2B로 전환](5-secure-access-b2b.md)

6. [권한 관리를 사용 하 여 안전 하 게 액세스](6-secure-access-entitlement-managment.md)

7. [조건부 액세스 정책을 사용 하 여 안전 하 게 액세스](7-secure-access-conditional-access.md)

8. [민감도 레이블로 안전 하 게 액세스할](8-secure-access-sensitivity-labels.md) 수 있습니다.

9. [Microsoft 팀, OneDrive 및 SharePoint에 대 한 액세스 보호](9-secure-access-teams-sharepoint.md)