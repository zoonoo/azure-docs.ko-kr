---
title: 권한 관리 문제 해결 - Azure AD
description: Azure Active Directory 권한 관리 문제를 해결하는 데 도움이 되는 몇 가지 항목에 대해 알아봅니다.
services: active-directory
documentationCenter: ''
author: barclayn
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.subservice: compliance
ms.date: 03/22/2020
ms.author: barclayn
ms.reviewer: markwahl-msft
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7c38e1a61827da547bb39a699a0e92043e63466c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80128469"
---
# <a name="troubleshoot-azure-ad-entitlement-management"></a>Azure AD 권한 관리 문제 해결

이 문서에서는 Azure Active Directory(Azure AD) 권한 관리 문제를 해결하는 데 도움이 되는 몇 가지 항목에 대해 설명합니다.

## <a name="administration"></a>관리

* 권한 부여 관리를 구성할 때 액세스 거부 메시지가 있고 전역 관리자인 경우 디렉터리에 [Azure AD Premium P2(또는 EMS E5) 라이선스가](entitlement-management-overview.md#license-requirements)있는지 확인합니다.

* 액세스 패키지를 만들거나 볼 때 액세스 거부 메시지가 발생하고 카탈로그 작성자 그룹의 구성원인 경우 첫 번째 액세스 패키지를 만들기 전에 [카탈로그를 만들어야](entitlement-management-catalog-create.md) 합니다.

## <a name="resources"></a>리소스

* 애플리케이션의 역할은 애플리케이션 자체에서 정의되며 Azure AD에서 관리됩니다. 응용 프로그램에 리소스 역할이 없는 경우 권한 관리는 사용자를 **기본 액세스** 역할에 할당합니다.

    Azure 포털에는 응용 프로그램으로 선택할 수 없는 서비스에 대한 서비스 주체도 표시될 수 있습니다.  특히 **Exchange Online** 및 **SharePoint Online은** 디렉터리에서 리소스 역할이 있는 응용 프로그램이 아닌 서비스이므로 액세스 패키지에 포함될 수 없습니다.  대신 그룹 기반 라이선스를 사용하여 해당 서비스에 액세스해야 하는 사용자에게 적절한 라이선스를 설정합니다.

* 그룹이 액세스 패키지의 리소스가 되려면 Azure AD에서 수정할 수 있어야 합니다.  Azure AD에서 소유자 또는 구성원 특성을 변경할 수 없으므로 온-프레미스 Active Directory에서 시작된 그룹을 리소스로 할당할 수 없습니다.   Exchange Online에서 배포 그룹으로 시작된 그룹도 Azure AD에서 수정할 수 없습니다. 

* SharePoint Online 문서 라이브러리와 개별 문서를 리소스로 추가할 수 없습니다.  대신 Azure [AD 보안 그룹을](../fundamentals/active-directory-groups-create-azure-portal.md)만들고 액세스 패키지에 해당 그룹 및 사이트 역할을 포함하고 SharePoint Online에서 해당 그룹을 사용하여 문서 라이브러리 또는 문서에 대한 액세스를 제어합니다.

* 액세스 패키지를 사용하여 관리하려는 리소스에 이미 할당된 사용자가 있는 경우 해당 사용자에게 적절한 정책을 사용하여 액세스 패키지를 할당해야 합니다. 예를 들어 이미 사용자가 있는 그룹을 액세스 패키지에 포함하려는 경우가 있습니다. 그룹의 사용자가 계속 액세스해야 하는 경우 사용자에게 그룹에 대한 액세스 권한을 잃지 않도록 액세스 패키지에 대한 적절한 정책이 있어야 합니다. 사용자에게 해당 리소스를 포함하는 액세스 패키지를 요청하거나 사용자를 액세스 패키지에 직접 할당하는 방법으로 액세스 패키지를 할당할 수 있습니다. 자세한 내용은 [액세스 패키지에 대한 요청 및 승인 변경 설정을](entitlement-management-access-package-request-policy.md)참조하십시오.

* 팀 구성원을 제거하면 Office 365 그룹에서도 제거됩니다. 팀 채팅 기능을 제거하면 작업이 지연될 수 있습니다. 자세한 내용은 [그룹 구성원 을](https://docs.microsoft.com/microsoftteams/office-365-groups#group-membership)참조하십시오.

* 디렉터리가 다중 지역에 대해 구성되지 않았는지 확인합니다. 권한 관리는 현재 SharePoint Online에 대한 다중 지역 위치를 지원하지 않습니다. SharePoint Online 사이트는 권한 관리로 제어되는 기본 지리적 위치에 있어야 합니다. 자세한 내용은 [OneDrive 및 SharePoint 온라인의 다중 지리적 기능을](https://docs.microsoft.com/Office365/Enterprise/multi-geo-capabilities-in-onedrive-and-sharepoint-online-in-office-365)참조하십시오.

## <a name="access-packages"></a>액세스 패키지

* 액세스 패키지 또는 정책을 삭제하려고 시도하고 활성 할당이 있다는 오류 메시지가 표시되면 할당이 있는 사용자가 표시되지 않으면 최근에 삭제된 사용자에게 여전히 할당이 있는지 확인합니다. 사용자가 삭제된 후 30일 동안 사용자 계정을 복원할 수 있습니다.   

## <a name="external-users"></a>외부 사용자

* 외부 사용자가 액세스 패키지에 대한 액세스를 요청하려는 경우 액세스 패키지에 **내 액세스 포털 링크를** 사용하고 있는지 확인합니다. 자세한 내용은 [액세스 패키지를 요청하는 링크 공유를](entitlement-management-access-package-settings.md)참조하십시오. 외부 사용자가 **myaccess.microsoft.com** 방문하고 전체 My Access 포털 링크를 사용하지 않는 경우 조직은 아닌 자체 조직에서 사용할 수 있는 액세스 패키지를 볼 수 있습니다.

* 외부 사용자가 액세스 패키지에 대한 액세스를 요청할 수 없거나 리소스에 액세스할 수 없는 경우 [외부 사용자의 설정을](entitlement-management-external-users.md#settings-for-external-users)확인하십시오.

* 이전에 디렉터리에서 로그인하지 않은 새 외부 사용자가 SharePoint Online 사이트를 포함하는 액세스 패키지를 수신하는 경우 해당 계정이 SharePoint Online에서 프로비저닝될 때까지 해당 액세스 패키지는 완전히 전달되지 않음으로 표시됩니다. 공유 설정에 대한 자세한 내용은 [SharePoint Online 외부 공유 설정 검토를](entitlement-management-external-users.md#review-your-sharepoint-online-external-sharing-settings)참조하십시오.

## <a name="requests"></a>요청

* 사용자가 액세스 패키지에 대한 액세스를 요청하려는 경우 액세스 패키지에 **내 액세스 포털 링크를** 사용하고 있는지 확인합니다. 자세한 내용은 [액세스 패키지를 요청하는 링크 공유를](entitlement-management-access-package-settings.md)참조하십시오.

* in-private 또는 incognito 모드로 설정된 브라우저에서 내 액세스 포털을 열면 로그인 동작과 충돌할 수 있습니다. 내 액세스 포털을 방문하는 경우 브라우저를 in-private 또는 incognito 모드로 사용하지 않는 것이 좋습니다.

* 아직 디렉터리에 없는 사용자가 내 액세스 포털에 로그인하여 액세스 패키지를 요청하는 경우 해당 조직의 계정을 사용하여 인증해야 합니다. 조직 계정은 리소스 디렉터리 또는 액세스 패키지의 정책 중 하나에 포함된 디렉터리의 계정일 수 있습니다. 사용자 계정이 조직 계정이 아니거나 인증한 디렉터리가 정책에 포함되지 않은 경우에는 사용자에게 액세스 패키지가 표시되지 않습니다. 자세한 내용은 [액세스 패키지에 대한 액세스 요청을](entitlement-management-request-access.md)참조하십시오.

* 사용자가 리소스 디렉터리에 로그인하지 못하도록 차단된 경우에는 내 액세스 포털에서 액세스를 요청할 수 없습니다. 사용자가 액세스를 요청하려면 먼저 사용자의 프로필에서 로그인 차단을 제거해야 합니다. 로그인 블록을 제거하려면 Azure 포털에서 Azure **Active Directory를**클릭하고 **사용자를**클릭한 다음 **[프로필]** 을 클릭합니다. **설정** 섹션을 편집하고 **블록 로그인을** **아니요로**변경합니다. 자세한 내용은 [Azure Active Directory](../fundamentals/active-directory-users-profile-azure-portal.md)를 사용하여 사용자의 프로필 정보 추가 또는 업데이트를 참조하세요.  [ID 보호 정책으로](../identity-protection/howto-unblock-user.md)인해 사용자가 차단되었는지 확인할 수도 있습니다.

* 내 액세스 포털에서 사용자가 요청자와 승인자인 경우 **승인** 페이지에서 액세스 패키지에 대한 요청이 표시되지 않습니다. 이 동작은 의도적인 것으로, 사용자가 자신의 요청을 승인할 수 없습니다. 요청 중인 액세스 패키지에 정책에 구성된 추가 승인자가 있는지 확인합니다. 자세한 내용은 [액세스 패키지에 대한 요청 및 승인 변경 설정을](entitlement-management-access-package-request-policy.md)참조하십시오.

### <a name="view-a-requests-delivery-errors"></a>요청의 배달 오류 보기

**필수 구성 조건 역할:** 글로벌 관리자, 사용자 관리자, 카탈로그 소유자 또는 액세스 패키지 관리자

1. Azure Portal에서 **Azure Active Directory**를 클릭한 다음, **Identity Governance**를 클릭합니다.

1. 왼쪽 메뉴에서 **패키지 액세스를** 클릭한 다음 액세스 패키지를 엽니다.

1. **요청을 클릭합니다.**

1. 보려는 요청을 선택합니다.

    요청에 배달 오류가 있는 경우 요청 상태가 **배달되지 않거나** **부분적으로 배달됩니다.**

    배달 오류가 있는 경우 요청의 세부 정보 창에 배달 오류가 있을 수 있습니다.

1. 개수를 클릭하여 요청의 모든 배달 오류를 확인합니다.

### <a name="reprocess-a-request"></a>요청 다시 처리

요청에 오류가 발생하면 요청을 다시 처리하여 다시 시도할 수 있습니다. 배달 상태가 **실패했거나** **부분적으로 배달된** 날짜와 1주일 미만의 완료된 날짜가 있는 요청만 다시 처리할 수 있습니다.

**필수 구성 조건 역할:** 글로벌 관리자, 사용자 관리자, 카탈로그 소유자 또는 액세스 패키지 관리자

1. Azure Portal에서 **Azure Active Directory**를 클릭한 다음, **Identity Governance**를 클릭합니다.

1. 왼쪽 메뉴에서 **패키지 액세스를** 클릭한 다음 액세스 패키지를 엽니다.

1. **요청을 클릭합니다.**

1. 다시 처리할 요청을 클릭합니다.

1. 요청 세부 정보 창에서 **요청 다시 처리**를 클릭합니다.

    ![실패한 요청을 다시 처리](./media/entitlement-management-troubleshoot/reprocess-request.png)

### <a name="cancel-a-pending-request"></a>보류 중인 요청 취소

아직 배달되지 않았거나 배달에 실패한 보류 중인 요청만 취소할 수 있습니다.

**필수 구성 조건 역할:** 글로벌 관리자, 사용자 관리자, 카탈로그 소유자 또는 액세스 패키지 관리자

1. Azure Portal에서 **Azure Active Directory**를 클릭한 다음, **Identity Governance**를 클릭합니다.

1. 왼쪽 메뉴에서 **패키지 액세스를** 클릭한 다음 액세스 패키지를 엽니다.

1. **요청을 클릭합니다.**

1. 취소할 요청을 클릭합니다.

1. 요청 세부 정보 창에서 **요청 취소를**클릭합니다.

## <a name="multiple-policies"></a>여러 정책

* 권한 관리는 최소 권한 모범 사례를 따릅니다. 사용자가 여러 정책이 적용되는 액세스 패키지에 대한 액세스를 요청하는 경우 권한 부여 관리에는 일반 정책보다 더 엄격하거나 더 구체적인 정책의 우선 순위를 지정하는 데 도움이 되는 논리가 포함됩니다. 정책이 일반적인 경우 권한 관리에서 정책을 요청자에게 표시하지 않거나 더 엄격한 정책을 자동으로 선택할 수 있습니다.

* 예를 들어 두 정책이 모두 요청자에게 적용되는 내부 직원에 대한 두 가지 정책이 있는 액세스 패키지를 고려합니다. 첫 번째 정책은 요청자가 포함된 특정 사용자에 대한 것입니다. 두 번째 정책은 요청자가 멤버인 디렉터리내의 모든 사용자를 위한 것입니다. 이 시나리오에서는 더 엄격 하기 때문에 첫 번째 정책은 자동으로 요청자에 대 한 선택 됩니다. 요청자는 두 번째 정책을 선택할 수 있는 옵션이 제공되지 않습니다.

* 여러 정책이 적용되는 경우 자동으로 선택되는 정책 또는 요청자에게 표시되는 정책은 다음 우선 순위 논리를 기반으로 합니다.

    | 정책 우선 순위 | Scope |
    | --- | --- |
    | P1 | 디렉터리 또는 특정 연결된 조직의 특정 사용자 및 그룹 |
    | P2 | 디렉토리의 모든 멤버(게스트 제외) |
    | P3 | 디렉터리(게스트 포함) 또는 특정 연결된 조직의 모든 사용자 |
    | P4 | 연결된 모든 조직 또는 모든 사용자(연결된 모든 조직 + 새 외부 사용자) |
    
    정책이 우선 순위가 높은 범주에 있으면 우선 순위가 낮은 범주는 무시됩니다. 우선 순위가 같은 여러 정책이 요청자에게 표시되는 방법의 예는 [정책 선택을](entitlement-management-request-access.md#select-a-policy)참조하십시오.

## <a name="next-steps"></a>다음 단계

- [외부 사용자를 위한 액세스 제어](entitlement-management-external-users.md)
- [권한 부여 관리에서 사용자가 액세스권한을 부여한 방식에 대한 보고서 보기](entitlement-management-reports.md)
