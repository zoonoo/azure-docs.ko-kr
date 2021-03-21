---
title: Azure Active Directory 및 Microsoft 365에서 그룹을 사용 하 여 외부 액세스 보호
description: 외부 사용자가 리소스에 액세스 하는 경우 Azure Active Directory 및 Microsoft 365 그룹을 사용 하 여 보안을 강화할 수 있습니다.
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
ms.openlocfilehash: 2d9d63c7a703987d7b17e6e03d8b5596d5f1dfa5
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102560619"
---
# <a name="securing-external-access-with-groups"></a>그룹을 사용 하 여 외부 액세스 보안 

그룹은 모든 액세스 제어 전략의 필수적인 부분입니다. Azure Active Directory (Azure AD) 보안 그룹 및 Microsoft 365 (M365) 그룹을 리소스에 대 한 액세스를 보호 하기 위한 기준으로 사용할 수 있습니다.

그룹은 다음 액세스 제어 메커니즘의 기반으로 사용할 수 있는 최상의 옵션입니다.

* 조건부 액세스 정책

* 자격 관리 액세스 패키지 

* M365 리소스, Microsoft 팀 및 SharePoint 사이트에 대 한 액세스

그룹의 역할은 다음과 같습니다.

* 소유자 – 그룹 소유자는 그룹 설정과 해당 구성원 자격을 관리 합니다.

* 멤버 – 그룹에 할당 된 권한 및 액세스를 상속 하는 멤버입니다.

* 게스트 – 게스트는 조직 외부의 멤버입니다. 

## <a name="determine-your-group-strategy"></a>그룹 전략 결정

그룹 전략을 개발 하 여 리소스에 대 한 외부 액세스를 보호 하기 위해 [필요한 보안](1-secure-access-posture.md) 상태를 고려 하 여 다음 선택 사항을 확인 합니다.

* **그룹을 만들 수 있는 사용자는 누구 인가요?** 관리자만 그룹을 만들거나 직원과 외부 사용자가 이러한 그룹을 만들도록 할 수 있습니다.

   * *기본적으로 모든 테 넌 트 구성원은 AZURE AD 보안 그룹을 만들 수 있습니다*. 

      * 관리자가 [아닌 사용자의 포털에](../develop/howto-restrict-your-app-to-a-set-of-users.md) 대 한 액세스를 제한 하 고 PowerShell에서 그룹 만들기 기능을 사용 하지 않도록 설정할 수 있습니다 [.](../enterprise-users/groups-troubleshooting.md) 

      * [Azure Active Directory에서 셀프 서비스 그룹 관리를 설정할](../enterprise-users/groups-self-service-management.md)수도 있습니다. 

   * *기본적으로 모든 사용자는 M365 그룹을 만들 수 있으며, 테 넌 트의 모든 (내부 및 외부) 사용자에 대해 그룹이 열립니다*. 

      * [Microsoft 365 그룹 만들기](/microsoft-365/solutions/manage-creation-of-groups) 를 특정 보안 그룹의 구성원으로 제한할 수 있습니다. Windows PowerShell을 사용 하 여이 설정을 구성 합니다. 

* **사용자를 그룹에 초대할 수 있는 사용자는 누구 인가요?** 모든 그룹 멤버가 다른 멤버를 추가 하거나 그룹 소유자만 멤버를 추가할 수 있습니까?

* **그룹에 초대할 수 있는 사람은 누구 인가요?** 기본적으로 외부 사용자는 그룹에 추가할 수 있습니다. 

### <a name="assign-users-to-groups"></a>그룹에 사용자 할당

사용자 개체의 사용자 특성 또는 다른 조건에 따라 사용자를 수동으로 그룹에 할당할 수 있습니다. 사용자는 해당 특성에 따라 동적으로 그룹에만 할당할 수 있습니다.

예를 들어 다음을 기준으로 그룹에 사용자를 할당할 수 있습니다.

* 특정 직함 또는 부서

* 자신이 속한 파트너 조직 (수동으로 또는 연결 된 조직을 통해)

* 사용자 유형 (멤버 또는 게스트)

* 특정 프로젝트에 참여 (수동)

* 위치

동적 그룹에는 사용자 또는 장치 중 하나만 포함 될 수 있습니다. 사용자를 동적 그룹에 할당 하는 사용자 특성을 기반으로 쿼리를 추가 합니다. 아래 예제에서는 그룹에 사용자를 추가 하는 쿼리 (게스트가 아님) 및 재무 부서에서 사용자를 추가 하는 쿼리를 보여 줍니다.

![동적 멤버 자격 규칙을 구성 하는 스크린샷](media/secure-external-access/4-dynamic-membership-rules.png)

동적 그룹에 대 한 자세한 내용은 [Azure Active Directory에서 동적 그룹 만들기 또는 업데이트를 참조 하세요.](../enterprise-users/groups-create-rule.md)

### <a name="do-not-use-groups-for-multiple-purposes"></a>여러 용도로 그룹 사용 안 함

보안 또는 리소스 액세스를 위해 그룹을 사용 하는 경우 단일 함수를 사용 하는 것이 중요 합니다. 그룹을 사용 하 여 리소스에 대 한 액세스 권한을 부여 하는 경우 다른 용도로 사용 하면 안 됩니다. 그룹이 위치나 팀 멤버 자격을 정의 하는 등의 일반적인 용도로 사용 되는 경우 액세스를 보호 하는 데에도 사용 되지 않아야 합니다. 

용도를 명확 하 게 하는 보안 그룹에 대 한 명명 규칙을 사용 하는 것이 좋습니다. 예를 들면 다음과 같습니다.

* *Secure_access_finance_apps*

* *Team_membership_finance_team*

* *Location_finance_building*



### <a name="types-of-groups"></a>그룹 유형

Azure ad 포털 또는 M365 관리 포털에서 Azure AD 보안 그룹 및 Microsoft 365 그룹을 모두 만들 수 있습니다. 외부 액세스를 보호 하기 위한 기준으로 두 유형을 모두 사용할 수 있습니다.

|고려 사항 | Azure AD 보안 그룹 (수동 및 동적)| Microsoft 365 그룹 |
| - | - | - |
| 그룹에 포함 될 수 있는 항목| 사용자<br>그룹<br>서비스 원칙<br>디바이스| 사용자만 |
| 그룹이 만들어지는 위치| Azure AD 포털<br>M365 포털 (메일을 사용 하도록 설정 된 경우)<br>PowerShell<br>Microsoft Graph<br>최종 사용자 포털| M365 포털<br>Azure AD 포털<br>PowerShell<br>Microsoft Graph<br>Microsoft 365 응용 프로그램 |
| 기본적으로 생성 되는 사람은 누구 인가요?| 관리자 <br>최종 사용자| 관리자<br>최종 사용자 |
| 기본적으로 추가할 수 있는 사용자는 누구 인가요?| 내부 사용자 (멤버)| 모든 조직의 테 넌 트 구성원 및 게스트 |
| 액세스 권한을 부여 하는 대상| 할당 된 리소스만| 모든 그룹 관련 리소스:<br>(그룹 사서함, 사이트, 팀, 채팅 및 기타 포함 된 M365 리소스)<br>그룹이 추가 되는 기타 모든 리소스 |
| 와 함께 사용할 수 있습니다.| 조건부 액세스<br>자격 관리<br>그룹 라이선스| 조건부 액세스<br>자격 관리<br>민감도 레이블 |



Microsoft 365 그룹을 사용 하 여 팀 및 관련 사이트 및 콘텐츠와 같은 Microsoft 365 리소스 집합을 만들고 관리할 수 있습니다. 이러한 항목은 프로젝트 기반 활동에 적합 합니다. 

 

## <a name="azure-ad-security-groups"></a>Azure AD 보안 그룹 

[AZURE AD 보안 그룹](./active-directory-manage-groups.md) 은 사용자 또는 장치를 포함할 수 있으며에 대 한 액세스를 관리 하는 데 사용할 수 있습니다. 

* Microsoft 365 apps, 사용자 지정 앱, Dropbox의 ServiceNow와 같은 SaaS (Software as a Service) 앱 등의 Azure 리소스

* Azure 데이터 및 구독.

* Azure 서비스.

Azure AD 보안 그룹을 사용 하 여 다음을 수행할 수도 있습니다.

* M365, Dynamics 365, Enterprise Mobility 및 Security와 같은 서비스에 대 한 라이선스를 할당 합니다. 자세한 내용은 [그룹 기반 라이선스](./active-directory-licensing-whatis-azure-portal.md)를 참조 하세요.

* 승격 된 권한을 할당 합니다. 자세한 내용은 [클라우드 그룹을 사용 하 여 역할 할당 관리 (미리 보기](../roles/groups-concept.md))를 참조 하세요. 

[Azure Portal에서](./active-directory-groups-create-azure-portal.md) 그룹을 만들려면 Azure Active Directory으로 이동한 다음 그룹으로 이동 합니다. [PowerShell cmdlet](../enterprise-users/groups-settings-v2-cmdlets.md)을 사용 하 여 Azure AD 보안 그룹을 만들 수도 있습니다. 

> [!NOTE]
> 최대 1500 응용 프로그램을 할당 하는 데 보안 그룹을 사용할 수 있지만, 더 이상 사용할 수 없습니다. 

![보안 그룹을 만드는 스크린샷](media/secure-external-access/4-create-security-group.png)

> [!IMPORTANT]
> **메일 사용이 가능한 보안 그룹을 만들려면 [Microsoft 365 관리 센터](https://admin.microsoft.com/)로 이동** 합니다. Azure AD 포털에서는 만들 수 없습니다. 
<br>만들 때 메일에 대 한 보안 그룹을 사용 하도록 설정 해야 합니다. 나중에이 기능을 사용 하도록 설정할 수 없습니다.

### <a name="hybrid-organizations-and-azure-ad-security-groups"></a>하이브리드 조직 및 Azure AD 보안 그룹

하이브리드 조직에는 온-프레미스 인프라와 Azure AD 클라우드 인프라가 모두 있습니다. Active Directory를 사용 하는 많은 하이브리드 조직에서는 온-프레미스의 보안 그룹을 만들고 클라우드와 동기화 합니다. 이 방법을 사용 하 여 온-프레미스 환경의 사용자만 보안 그룹에 추가할 수 있습니다.

온 **-프레미스 위반을 사용 하 여 Microsoft 365 테 넌 트에 액세스할 수 있으므로 온-프레미스 인프라를 손상 으로부터 보호** 하세요. 지침은 [온-프레미스 공격 으로부터 Microsoft 365 보호](./protect-m365-from-on-premises-attacks.md) 를 참조 하세요.

## <a name="microsoft-365-groups"></a>Microsoft 365 그룹

[Microsoft 365 그룹](/microsoft-365/admin/create-groups/office-365-groups) 은 M365에서 모든 액세스를 구동 하는 기본 멤버 자격 서비스입니다. [Azure Portal](https://portal.azure.com/)또는 [M365 포털](https://admin.microsoft.com/)에서 만들 수 있습니다. M365 그룹이 만들어지면 공동 작업에 사용 되는 리소스 그룹에 대 한 액세스 권한을 부여 합니다. 이러한 리소스의 전체 목록은 [관리자를 위한 Microsoft 365 그룹 개요](/microsoft-365/admin/create-groups/office-365-groups) 를 참조 하세요.

M365 그룹에는 역할에 대 한 다음 미묘한 차이가 있습니다.

* **소유자** -그룹 소유자는 구성원을 추가 하거나 제거할 수 있으며 공유 받은 편지함에서 대화를 삭제 하거나 그룹 설정을 변경할 수 있습니다. 그룹 소유자는 그룹 이름을 바꾸고 설명 또는 그림 등을 업데이트할 수 있습니다.

* **멤버** -멤버는 그룹의 모든 항목에 액세스할 수 있지만 그룹 설정은 변경할 수 없습니다. 기본적으로 그룹 구성원은 게스트를 초대 하 여 그룹에 가입할 수 있지만 [해당 설정을 제어할](/microsoft-365/admin/create-groups/manage-guest-access-in-groups)수 있습니다.

* **게스트** 그룹 게스트는 조직 외부의 멤버입니다. 기본적으로 게스트는 팀의 기능에 몇 가지 제한이 있습니다.

 

### <a name="m365-group-settings"></a>M365 그룹 설정

전자 메일 별칭, 개인 정보 및 설정 시 팀에 그룹을 사용할지 여부를 선택 합니다. 

![Microsoft 365 그룹 설정 편집 스크린샷](media/secure-external-access/4-edit-group-settings.png)

설치 후에는 구성원을 추가 하 고 전자 메일 사용 등에 대 한 설정을 구성 합니다.

### <a name="next-steps"></a>다음 단계

리소스에 대 한 외부 액세스 보안에 대 한 다음 문서를 참조 하세요. 이러한 작업은 나열 된 순서 대로 수행 하는 것이 좋습니다.

1. [외부 액세스를 위해 원하는 보안 상태 결정](1-secure-access-posture.md)

2. [현재 상태 검색](2-secure-access-current-state.md)

3. [거 버 넌 스 계획 만들기](3-secure-access-plan.md)

4. [보안을 위해 그룹을 사용](4-secure-access-groups.md) 합니다.

5. [Azure AD B2B로 전환](5-secure-access-b2b.md)

6. [권한 관리를 사용 하 여 안전 하 게 액세스](6-secure-access-entitlement-managment.md)

7. [조건부 액세스 정책을 사용 하 여 안전 하 게 액세스](7-secure-access-conditional-access.md)

8. [민감도 레이블을 사용 하 여 안전 하 게 액세스](8-secure-access-sensitivity-labels.md)

9. [Microsoft 팀, OneDrive 및 SharePoint에 대 한 액세스 보호](9-secure-access-teams-sharepoint.md)