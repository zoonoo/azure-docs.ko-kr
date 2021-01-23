---
title: Azure Active Directory를 사용 하 여 Microsoft 팀, SharePoint 및 OneDrive에 대 한 외부 액세스 보호
description: 전반적인 외부 액세스 보안의 일부로 Microsoft 365 서비스에 안전 하 게 액세스할 수 있습니다.
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
ms.openlocfilehash: 218208891cccb4f606a574a9c1c09f30c4ac0b11
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/23/2021
ms.locfileid: "98725081"
---
# <a name="secure-external-access-to-microsoft-teams-sharepoint-and-onedrive-for-business"></a>Microsoft 팀, SharePoint 및 비즈니스용 OneDrive에 대 한 외부 액세스 보호 

Microsoft 팀, SharePoint 및 비즈니스용 OneDrive는 외부 사용자와 공동 작업 하 고 콘텐츠를 공유 하는 가장 많이 사용 되는 세 가지 방법입니다. "승인 된" 방법이 너무 제한적일 경우 사용자는 콘텐츠를 전자 메일로 보내거나 개인 DropBox 또는 OneDrive와 같은 안전 하지 않은 외부 프로세스 및 응용 프로그램을 설정 하 여 승인 된 방법의 외부에 있습니다. 목표는 간편한 공동 작업으로 보안 요구를 분산 하는 것입니다.

이 문서에서는 Microsoft 팀 및 SharePoint를 사용 하 여 비즈니스 목표에 맞게 외부 공동 작업을 결정 하 고 구성 하는 방법을 안내 합니다.

## <a name="governance-begins-in-azure-active-directory"></a>거 버 넌 스 시작 Azure Active Directory

Microsoft 365 공유는 외부 Id의 영향을 받습니다. [ ](https://aad.portal.azure.com/) Azure Active Directory (AZURE AD)의 외부 공동 작업 설정. Azure AD에서 외부 공유가 사용 하지 않도록 설정 되거나 제한 되는 경우 Microsoft 365에서 구성 된 모든 공유 설정을 재정의 합니다. 단, Azure AD B2B 통합이 사용 하도록 설정 되지 않은 경우 SharePoint 및 OneDrive는 OTP (일회성 암호)를 통해 임시 공유를 지원 하도록 구성할 수 있습니다.

![외부 공동 작업 설정의 스크린샷](media/secure-external-access/9-external-collaboration-settings.png)

### <a name="guest-user-access"></a>게스트 사용자 액세스

게스트 사용자 액세스에는 세 가지 옵션이 있습니다 .이 옵션은 방문자가 초대한 후 볼 수 있는 작업을 제어 합니다. 

게스트 사용자가 다른 게스트 사용자의 세부 정보를 볼 수 없도록 하 고 그룹 멤버 자격을 열거할 수 없도록 하려면 게스트 사용자가 디렉터리 개체의 속성 및 멤버 자격에 대 한 액세스를 제한 합니다 .를 선택 합니다.

### <a name="guest-invite-settings"></a>게스트 초대 설정

이러한 설정은 게스트를 초대할 수 있는 사용자 및 해당 게스트를 초대할 수 있는 방법을 결정 합니다. 이러한 설정은 B2B와의 통합이 설정 된 경우에만 사용할 수 있습니다.

관리자 및 게스트 초대자 역할의 사용자가 초대할 수 있도록 설정 하는 것이 좋습니다. 이 설정을 사용 하면 다음 예제와 같이 제어 된 공동 작업 프로세스를 설정할 수 있습니다.

* 팀 소유자는 게스트 초대자 역할에 할당 되는 티켓을 제출 합니다.

   * 모든 게스트 초대를 담당 하 게 됩니다.

   * 기본 SharePoint에 직접 사용자를 추가 하는 것에 동의 하지 않습니다.

   * 는 일반 액세스 검토를 수행 하 고 적절 하 게 액세스를 취소 합니다.

* Central은 다음을 수행 합니다.

   * 학습 완료 시 요청 된 역할을 부여 하 여 외부 공유를 사용 하도록 설정 합니다.

   * Azure AD P2 라이선스를 Microsoft 365 그룹 소유자에 게 할당 하 여 액세스 검토를 사용 하도록 설정 합니다.
   * Microsoft 365 그룹 액세스 검토를 만듭니다.

   * 액세스 검토가 발생 하는지 확인 합니다.

   * 기본 SharePoint에 직접 추가 된 사용자를 제거 합니다.

 게스트 **에 대해 전자 메일 일회용 암호 사용 (미리 보기)을 설정 하 고 사용자 흐름을 통해 게스트 셀프 서비스 로그인** 을 **예** 로 설정 합니다. 이 설정은 Azure AD 외부 공동 작업 설정과의 통합을 활용 합니다.

### <a name="collaboration-restrictions"></a>협업 제한 사항

공동 작업 제한에는 세 가지 선택 사항이 있습니다. 비즈니스 요구 사항에 따라 선택 하는 것이 결정 됩니다.

* **모든 도메인에 초대를 보낼** 수 있습니다. 즉, 공동 작업을 위해 모든 사용자를 초대할 수 있습니다.

* **지정 된 도메인에 대 한 초대를 거부** 하면 해당 도메인 외부의 사용자가 공동 작업을 위해 초대 될 수 있음을 의미 합니다.

* **지정 된 도메인에만 초대 허용** 은 지정 된 도메인의 외부에 있는 모든 사용자를 초대할 수 없음을 의미 합니다. 

## <a name="govern-access-in-teams"></a>팀의 액세스 제어

[팀은 외부 사용자 (조직 외부 사용자)와 게스트 사용자 (게스트 계정을 사용 하는 사용자)를 구별](/microsoftteams/communicate-with-users-from-other-organizations?WT.mc_id=TeamsAdminCenterCSH%e2%80%8b)합니다. [팀 관리 포털](https://admin.teams.microsoft.com/company-wide-settings/external-communications) 의 조직 전체 설정에서 공동 작업 설정을 관리 합니다. 

> [!NOTE]
> Azure Active Directory의 외부 id 공동 작업 설정은 유효 사용 권한을 제어 합니다. 팀에서 제한을 늘릴 수 있지만 Azure AD에 설정 된 항목에서 제한을 줄일 수는 없습니다.

* **외부 액세스 설정**. 기본적으로 팀은 외부 액세스를 허용 합니다. 즉, 조직이 모든 외부 도메인과 통신할 수 있습니다. 팀의 특정 도메인만 제한 하거나 허용 하려면 여기에서 작업을 수행할 수 있습니다.

* **게스트 액세스**. 게스트 액세스는 게스트 사용자가 팀에서 수행할 수 있는 작업을 제어 합니다.

팀에서 외부 액세스를 관리 하는 방법에 대해 자세히 알아보려면 다음 리소스를 참조 하세요.

* [Microsoft 팀에서 외부 액세스 관리](/microsoftteams/manage-external-access)

* [Microsoft 365 id 모델 및 Azure Active Directory](/microsoft-365/enterprise/about-microsoft-365-identity?view=o365-worldwide)

* [Microsoft 팀에 대 한 id 모델 및 인증](/MicrosoftTeams/identify-models-authentication)

* [Microsoft 팀의 민감도 레이블](/MicrosoftTeams/sensitivity-labels)

## <a name="govern-access-in-sharepoint-and-onedrive"></a>SharePoint 및 OneDrive의 액세스 제어

SharePoint 관리자는 공동 작업에 사용할 수 있는 많은 설정을 갖습니다. 조직 전체의 설정은 SharePoint 관리 센터에서 관리 됩니다. 각 SharePoint 사이트에 대해 설정을 조정할 수 있습니다. 조직의 전체 설정을 필요한 최소 보안 수준으로 설정 하 고 필요에 따라 특정 사이트에 대 한 보안을 강화 하는 것이 좋습니다. 예를 들어 위험 수준이 높은 프로젝트의 경우 사용자를 특정 도메인으로 제한 하 고, 게스트를 초대 하는 구성원의 기능을 사용 하지 않도록 설정할 수 있습니다.

### <a name="integrating-sharepoint-and-one-drive-with-azure-ad-b2b"></a>SharePoint와 한 드라이브를 Azure AD B2B와 통합

외부 공동 작업을 관리 하는 전체 전략의 일부로 [AZURE AD B2B와의 SharePoint 및 OneDrive 통합 미리 보기를 사용 하도록 설정](/sharepoint/sharepoint-azureb2b-integration-preview) 하는 것이 좋습니다.

Azure AD B2B는 게스트 사용자의 인증 및 관리를 제공 합니다. SharePoint 및 OneDrive 통합을 사용 하면 [AZURE AD B2B 일회성 암호](../external-identities/one-time-passcode.md) 파일, 폴더, 목록 항목, 문서 라이브러리 및 사이트의 외부 공유에 사용 됩니다. 이 기능은 기존의 [보안 외부 공유 받는 사람 환경](/sharepoint/what-s-new-in-sharing-in-targeted-release)에서 업그레이드 된 환경을 제공 합니다.

> [!NOTE]
> Azure AD B2B 통합에 대해 미리 보기를 사용 하도록 설정 하는 경우 SharePoint 및 OneDrive 공유에는 **구성원이 초대** 하 고 **게스트가 초대할 수** 있는 것과 같은 azure ad 조직 관계 설정이 적용 됩니다.

### <a name="sharing-policies"></a>정책 공유

SharePoint와 OneDrive 모두에 대해 *외부 공유* 를 설정할 수 있습니다. OneDrive 제한은 SharePoint 설정 보다 허용 될 수 없습니다.

 ![SharePoint 및 OneDrive의 외부 공유 설정 스크린샷](media/secure-external-access/9-sharepoint-settings.png)

Azure AD B2B와 SharePoint 통합은 컨트롤이 계정과 상호 작용 하는 방식을 변경 합니다.

* **누구나**. 권장하지 않음

   * 통합 상태에 관계 없이 모든 링크를 사용 하도록 설정 하면이 유형의 링크를 사용 하는 경우 Azure 정책이 적용 되지 않습니다. 

   * 관리 공동 작업의 시나리오에서는이 기능을 사용 하지 않도록 설정 합니다. 
   > [!NOTE]
   > 특정 사이트에 대해이 설정을 사용 하도록 설정 해야 하는 시나리오를 찾을 수 있습니다 .이 경우에는이 설정을 사용 하도록 설정 하 고 개별 사이트에 대해 더 큰 제한을 설정 합니다.

* **신규 및 기존 게스트**. 통합이 사용 하도록 설정 된 경우 권장 됩니다.

   * **AZURE AD b2b 통합** 을 사용 하도록 설정 하면 신규 및 기존 게스트가 AZURE ad b2b 게스트 계정을 사용 하 여 azure ad 정책을 통해 관리할 수 있습니다.

   * **AZURE AD b2b 통합** 이 사용 하도록 설정 되지 않은 경우 새 게스트가 AZURE ad b2b 계정을 생성 하지 않으며 azure ad에서 관리할 수 없습니다. 기존 게스트에 Azure AD B2B 계정이 있는지 여부는 게스트를 만든 방법에 따라 달라 집니다.

* **기존 게스트**. 통합을 사용 하도록 설정 하지 않은 경우에 권장 됩니다.

   * 이 기능을 사용 하도록 설정 하면 사용자는 디렉터리에 이미 있는 다른 사용자와만 공유할 수 있습니다.

* **조직의 사용자만** 외부 사용자와 공동 작업 해야 하는 경우에는 권장 되지 않습니다.

   * 통합 상태에 관계 없이 사용자는 조직의 사용자와만 공유할 수 있습니다. 

* **도메인 별로 외부 공유를 제한** 합니다. 기본적으로 SharePoint는 외부 액세스를 허용 합니다. 즉, 모든 외부 도메인에서 공유가 허용 됩니다. SharePoint 전용 특정 도메인을 제한 하거나 허용 하려면 여기에서 수행할 수 있습니다.

* **특정 보안 그룹의 사용자만 외부에서 공유할 수** 있습니다.  이 설정은 SharePoint 및 OneDrive의 콘텐츠를 공유할 수 있는 사용자를 제한 하는 반면, Azure AD의 설정은 모든 응용 프로그램에 적용 됩니다. 공유할 수 있는 사용자는 안전 하 게 공유 하는 방법에 대 한 학습을 수행 하도록 요구 하려는 경우에 유용할 수 있습니다. 그런 다음 완료 되 면 승인 된 공유 보안 그룹에 추가 합니다. 이 설정을 선택 하 고 사용자에 게 "승인 된 공유자"에 대 한 액세스 권한을 얻을 수 있는 방법이 없는 경우 대신 공유 하는 승인 되지 않은 방법을 찾을 수 있습니다. 

* **게스트가 소유 하지 않은 항목을 공유 하도록 허용** 합니다. 이를 사용 하지 않도록 설정 하는 것이 좋습니다.

* **확인 코드를 사용 하는 사용자는 며칠 후에 다시 인증 해야 합니다 (기본값은 30)**. 이 설정을 사용 하는 것이 좋습니다.

### <a name="access-controls"></a>액세스 제어

액세스 제어 설정은 조직의 모든 사용자에 게 영향을 줍니다. 외부 사용자가 규격 장치를 보유 하 고 있는지 여부를 제어할 수 없는 경우 여기에서 해당 컨트롤의 주소를 지정 하지 않습니다. 

* **유휴 세션 로그 아웃** 이 컨트롤을 사용 하도록 설정 하는 것이 좋습니다 .이 컨트롤을 사용 하면 비활성 기간 후에 관리 되지 않는 장치에서 사용자에 게 경고 하 고 로그 아웃할 수 있습니다. 비활성 기간 및 경고를 구성할 수 있습니다. 

* **네트워크 위치**. 이 컨트롤을 설정 하면 조직에서 소유 하는 양식 IP 주소에만 액세스를 허용할 수 있습니다. 외부 공동 작업 시나리오에서는 모든 외부 파트너가 네트워크 내에서 또는 VPN을 통해 리소스에만 액세스 하는 경우에만 설정 합니다.

### <a name="file-and-folder-links"></a>파일 및 폴더 링크

SharePoint 관리 센터에서 파일 및 폴더 링크를 공유 하는 방법을 설정할 수도 있습니다. 또한 각 사이트에 대해 이러한 설정을 구성할 수 있습니다. 

 ![파일 및 폴더 링크 설정의 스크린샷](media/secure-external-access/9-file-folder-links.png)

Azure AD B2B와의 통합을 사용 하도록 설정 하면 파일 및 폴더를 조직 외부와 공유 하는 경우 파일 및 폴더가 공유 될 때 B2B 사용자가 생성 됩니다.

기본 링크 형식을 **조직의 사용자** 로 설정 하 고 기본 권한을 **편집할** 수 있도록 설정 하는 것이 좋습니다. 이렇게 하면 항목이 공유 신중 됩니다. 그런 다음 특정 공동 작업 요구를 충족 하는 사이트별 기본값에 대해이 설정을 사용자 지정할 수 있습니다.

### <a name="anyone-links"></a>모든 사용자 링크

사용자 링크를 사용 하지 않는 것이 좋습니다. 이렇게 하는 경우 만료를 설정 하는 것이 좋습니다. 사용 권한을 확인 하도록 제한 하는 것이 좋습니다. 파일이 나 폴더에 대 한 사용 권한 보기만을 선택 하는 경우 사용자는 편집 권한을 포함 하도록 사용자 링크를 변경할 수 없습니다.

SharePoint에 대 한 외부 액세스를 관리 하는 방법에 대 한 자세한 내용은 다음을 참조 하세요.

* [SharePoint 외부 공유 개요](/sharepoint/external-sharing-overview)

* [Azure AD B2B와 SharePoint 및 OneDrive 통합](/sharepoint/sharepoint-azureb2b-integration-preview)

#### <a name="next-steps"></a>다음 단계

리소스에 대 한 외부 액세스 보안에 대 한 다음 문서를 참조 하세요. 이러한 작업은 나열 된 순서 대로 수행 하는 것이 좋습니다.

1. [외부 액세스를 위한 보안 상태 결정](1-secure-access-posture.md)

2. [현재 상태 검색](2-secure-access-current-state.md)

3. [거 버 넌 스 계획 만들기](3-secure-access-plan.md)

4. [보안을 위해 그룹 사용](4-secure-access-groups.md)

5. [Azure AD B2B로 전환](5-secure-access-b2b.md)

6. [권한 관리를 사용 하 여 안전 하 게 액세스](6-secure-access-entitlement-managment.md)

7. [조건부 액세스 정책을 사용 하 여 안전 하 게 액세스](7-secure-access-conditional-access.md)

8. [민감도 레이블을 사용 하 여 안전 하 게 액세스](8-secure-access-sensitivity-labels.md)

9. [Microsoft 팀, OneDrive 및 SharePoint에 안전 하 게 액세스할](9-secure-access-teams-sharepoint.md) 수 있습니다.