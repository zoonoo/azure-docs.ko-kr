---
title: Azure Active Directory를 사용하여 Microsoft Teams, SharePoint 및 OneDrive에 대한 외부 액세스 보호
description: 전반적인 외부 액세스 보안의 일부로 Microsoft 365 서비스에 대한 액세스를 보호합니다.
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
ms.openlocfilehash: f373c849cd542769fec7a10f20dad04e742c9591
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102565141"
---
# <a name="secure-external-access-to-microsoft-teams-sharepoint-and-onedrive-for-business"></a>Microsoft Teams, SharePoint 및 비즈니스용 OneDrive에 대한 외부 액세스 보호 

Microsoft Teams, SharePoint 및 비즈니스용 OneDrive는 외부 사용자와 협업하고 콘텐츠를 공유하는 데 가장 많이 사용되는 세 가지 방법입니다. “승인된” 방법이 너무 제한적일 경우 사용자는 콘텐츠를 이메일로 보내거나 개인 DropBox 또는 OneDrive와 같은 안전하지 않은 외부 프로세스 및 애플리케이션을 설정하여 승인되지 않은 방법을 사용하게 됩니다. 목표는 손쉬운 협업으로 보안 요구를 분산시키는 것입니다.

이 문서에서는 Microsoft Teams 및 SharePoint를 사용하여 비즈니스 목표에 맞게 외부 협업을 결정하고 구성하는 방법을 안내합니다.

## <a name="governance-begins-in-azure-active-directory"></a>Azure Active Directory에서 거버넌스 시작

Microsoft 365에서의 공유는 Azure AD(Azure Active Directory)의 [외부 ID | 외부 협업 설정](https://aad.portal.azure.com/)에서 제어되는 부분입니다. Azure AD에서 외부 공유가 사용하도록 설정되지 않거나 제한되는 경우 Microsoft 365에서 구성된 모든 공유 설정을 재정의합니다. 단, Azure AD B2B 통합이 사용하도록 설정되지 않은 경우 SharePoint 및 OneDrive는 OTP(일회성 암호)를 통해 임시 공유를 지원하도록 구성할 수 있습니다.

![외부 협업 설정을 보여 주는 스크린샷](media/secure-external-access/9-external-collaboration-settings.png)

### <a name="guest-user-access"></a>게스트 사용자 액세스

게스트 사용자 액세스에는 세 가지 옵션이 있습니다. 이 옵션은 게스트 사용자가 초대된 후에 볼 수 있는 작업을 제어합니다. 

게스트 사용자가 다른 게스트 사용자의 세부 정보를 보거나 그룹 멤버 자격을 열거하는 것을 방지하려면 디렉터리 개체의 속성 및 멤버 자격에 대한 게스트 사용자의 액세스를 제한하도록 선택합니다.

### <a name="guest-invite-settings"></a>게스트 초대 설정

이러한 설정은 게스트를 초대할 수 있는 사용자 및 게스트를 초대할 수 있는 방법을 결정합니다. 이러한 설정은 B2B와의 통합이 설정된 경우에만 사용할 수 있습니다.

관리자 및 게스트 초대자 역할의 사용자가 초대할 수 있도록 설정하는 것이 좋습니다. 이 설정을 사용하면 다음 예제와 같이 제어된 협업 프로세스를 설정할 수 있습니다.

* 팀 소유자는 게스트 초대자 역할에 할당할 티켓을 제출합니다. 또한

   * 모든 게스트 초대를 담당하게 됩니다.

   * 기본 SharePoint에 사용자를 직접 추가하지 않음에 동의합니다.

   * 일반 액세스 검토를 수행하고 적절하게 액세스를 취소하는 데 책임이 있습니다.

* 중앙 IT는 다음을 수행합니다.

   * 학습 완료 시 요청된 역할을 부여하여 외부 공유를 사용하도록 설정합니다.

   * Azure AD P2 라이선스를 Microsoft 365 그룹 소유자에게 할당하여 액세스 검토를 사용하도록 설정합니다.
   * Microsoft 365 그룹 액세스 검토를 만듭니다.

   * 액세스 검토가 발생하는지 확인합니다.

   * 기본 SharePoint에 직접 추가된 사용자를 제거합니다.

 **게스트에 대해 이메일 일회용 암호 사용(미리 보기) 및 사용자 흐름을 통해 게스트 셀프 서비스 로그인 사용** 을 **예** 로 설정합니다. 이 설정은 Azure AD 외부 협업 설정과의 통합을 활용합니다.

### <a name="collaboration-restrictions"></a>협업 제한 사항

협업 제한 사항에는 세 가지 선택 사항이 있습니다. 비즈니스 요구 사항에 따라 선택할 내용이 결정됩니다.

* **도메인에 초대 보내기 허용** 은 협업을 위해 사용자를 초대할 수 있음을 의미합니다.

* **지정된 도메인에 초대 거부** 는 협업을 위해 해당 도메인이 아닌 사용자를 초대할 수 있음을 의미합니다.

* **지정된 도메인에 대해서만 초대 허용** 은 지정된 도메인이 아닌 사용자를 초대할 수 없음을 의미합니다. 

## <a name="govern-access-in-teams"></a>Teams에서의 액세스 제어

[Teams는 외부 사용자(조직 외부 사용자)와 게스트 사용자(게스트 계정을 사용하는 사용자)를 구별](/microsoftteams/communicate-with-users-from-other-organizations?WT.mc_id=TeamsAdminCenterCSH%e2%80%8b)합니다. 조직 전체 설정 아래에 있는 [Teams 관리 포털](https://admin.teams.microsoft.com/company-wide-settings/external-communications)에서 협업 설정을 관리합니다. 

> [!NOTE]
> Azure Active Directory의 외부 ID 협업 설정은 유효 사용 권한을 제어합니다. Teams에서 제한을 늘릴 수 있지만 Azure AD에 설정된 항목에서 제한을 줄일 수는 없습니다.

* **외부 액세스 설정**. 기본적으로 Teams는 외부 액세스를 허용합니다. 즉, 조직이 모든 외부 도메인과 통신할 수 있습니다. Teams만을 위해서 특정 도메인을 제한하거나 허용하려면 여기서 수행할 수 있습니다.

* **게스트 액세스**. 게스트 액세스는 게스트 사용자가 Teams에서 수행할 수 있는 작업을 제어합니다.

Teams에서 외부 액세스를 관리하는 방법에 대해 자세히 알아보려면 다음 리소스를 참조하세요.

* [Microsoft Teams에서 외부 액세스 관리](/microsoftteams/manage-external-access)

* [Microsoft 365 ID 모델 및 Azure Active Directory](/microsoft-365/enterprise/about-microsoft-365-identity)

* [Microsoft Teams를 위한 ID 모델 및 인증](/MicrosoftTeams/identify-models-authentication)

* [Microsoft Teams를 위한 민감도 레이블](/MicrosoftTeams/sensitivity-labels)

## <a name="govern-access-in-sharepoint-and-onedrive"></a>SharePoint 및 OneDrive의 액세스 제어

SharePoint 관리자는 많은 설정을 협업에 사용할 수 있습니다. 조직 전체 설정은 SharePoint 관리 센터에서 관리됩니다. 각 SharePoint 사이트에 대해 설정을 조정할 수 있습니다. 조직 전체 설정을 최소한의 필수 보안 수준으로 설정하고 필요에 따라 특정 사이트에 대한 보안을 강화하는 것이 좋습니다. 예를 들어 위험 수준이 높은 프로젝트의 경우 사용자를 특정 도메인으로 제한하고, 게스트를 초대하는 멤버의 기능을 사용하지 않도록 설정할 수 있습니다.

### <a name="integrating-sharepoint-and-one-drive-with-azure-ad-b2b"></a>SharePoint와 OneDrive를 Azure AD B2B와 통합

외부 협업을 관리하는 전체 전략의 일부로 [SharePoint 및 OneDrive의 Azure AD B2B와 통합 미리 보기를 사용하도록 설정](/sharepoint/sharepoint-azureb2b-integration-preview)하는 것이 좋습니다.

Azure AD B2B는 게스트 사용자의 인증 및 관리를 제공합니다. SharePoint 및 OneDrive 통합을 사용하면 파일, 폴더, 목록 항목, 문서 라이브러리 및 사이트를 외부와 공유하는 데 [Azure AD B2B 일회성 암호](../external-identities/one-time-passcode.md)를 사용합니다. 이 기능은 기존의 [외부 공유 수신자 보호 환경](/sharepoint/what-s-new-in-sharing-in-targeted-release)에서 업그레이드된 환경을 제공합니다.

> [!NOTE]
> Azure AD B2B 통합에 대한 미리 보기를 사용하도록 설정하면 **멤버가 초대할 수 있음** 및 **게스트가 초대할 수 있음** 과 같은 Azure AD 조직 관계 설정이 SharePoint 및 OneDrive 공유에 적용됩니다.

### <a name="sharing-policies"></a>공유 정책

SharePoint와 OneDrive 모두에 대해 *외부 공유* 를 설정할 수 있습니다. OneDrive 제한은 SharePoint 설정보다 허용 범위가 클 수 없습니다.

 ![SharePoint 및 OneDrive의 외부 공유 설정에 대한 스크린샷](media/secure-external-access/9-sharepoint-settings.png)

Azure AD B2B와 SharePoint 통합은 컨트롤이 계정과 상호 작용하는 방식을 변경합니다.

* **모든 사용자**. 권장하지 않음

   * 통합 상태에 관계 없이 모든 링크를 사용하도록 설정하면 이 유형의 링크를 사용할 때 Azure 정책이 적용되지 않습니다. 

   * 제어되는 협업 시나리오에서는 이 기능을 사용하도록 설정하지 않습니다. 
   > [!NOTE]
   > 특정 사이트에 대해 이 설정을 사용하도록 설정해야 하는 시나리오가 있는 경우 이 설정을 사용하도록 설정하고 개별 사이트에 대해 더 큰 제한을 설정합니다.

* **신규 및 기존 게스트**. 통합이 사용하도록 설정된 경우 권장됩니다.

   * **Azure AD B2B 통합** 을 사용하도록 설정하면 신규 및 기존 게스트가 Azure AD 정책을 통해 관리할 수 있는 Azure AD B2B 게스트 계정을 갖습니다.

   * **Azure AD B2B 통합** 을 사용하도록 설정하지 않은 경우 신규 게스트에게는 Azure AD B2B 계정이 생성되지 않으며 Azure AD에서 관리할 수 없습니다. 기존 게스트에 Azure AD B2B 계정이 있는지 여부는 게스트를 생성하는 방법에 따라 달라집니다.

* **기존 게스트**. 통합을 사용하도록 설정하지 않은 경우에 권장됩니다.

   * 이 기능을 사용하도록 설정하면 사용자는 디렉터리에 이미 있는 다른 사용자하고만 공유할 수 있습니다.

* **조직 내 사용자만**. 외부 사용자와 협업해야 하는 경우에는 권장되지 않습니다.

   * 통합 상태에 관계 없이 사용자는 조직의 사용자와만 공유할 수 있습니다. 

* **도메인별 외부 공유 제한**. 기본적으로 SharePoint는 외부 액세스를 허용합니다. 즉, 모든 외부 도메인에서 공유가 허용됩니다. SharePoint만을 위해서 특정 도메인을 제한하거나 허용하려면 여기서 수행할 수 있습니다.

* **특정 보안 그룹의 사용자만 외부에서 공유 허용**.  이 설정은 SharePoint 및 OneDrive의 콘텐츠를 공유할 수 있는 사용자를 제한하는 한편, Azure AD의 설정이 모든 애플리케이션에 적용됩니다. 공유할 수 있는 사용자를 제한하는 것은 사용자가 안전하게 공유에 대한 교육을 받도록 한 다음, 완료 시 승인된 공유 보안 그룹에 추가하려는 경우에 유용할 수 있습니다. 이 설정을 선택했는데 사용자가 “승인된 공유자”에 액세스할 수 없는 경우, 대신 승인되지 않은 공유 방법을 찾을 수 있습니다. 

* **게스트가 소유하지 않은 항목을 공유하도록 허용**. 이 항목은 사용하지 않도록 설정된 상태 그대로 두는 것이 좋습니다.

* **확인 코드를 사용하는 사용자는 며칠 후에(기본적으로 30일) 다시 인증해야 함**. 이 설정은 사용하는 것이 좋습니다.

### <a name="access-controls"></a>액세스 제어

액세스 제어 설정은 조직의 모든 사용자에게 적용됩니다. 외부 사용자에게 규격 디바이스가 있는지 여부를 제어할 수 없는 경우 여기서는 해당 컨트롤을 다루지 않습니다. 

* **유휴 세션 로그아웃**. 이 컨트롤을 사용하도록 설정하는 것이 좋습니다. 이 컨트롤을 사용하면 비활성 기간 후에 관리되지 않은 디바이스에서 사용자에게 경고하고 로그아웃시킬 수 있습니다. 비활성 기간 및 경고를 구성할 수 있습니다. 

* **네트워크 위치**. 이 컨트롤을 설정하면 조직에서 소유한 양식 IP 주소에 대해서만 액세스를 허용할 수 있습니다. 외부 협업 시나리오에서 이 설정은 모든 외부 파트너가 네트워크 내에서 또는 VPN을 통해서만 리소스에 액세스하는 경우에만 설정합니다.

### <a name="file-and-folder-links"></a>파일 및 폴더 링크

SharePoint 관리 센터에서 파일 및 폴더 링크를 공유하는 방법을 설정할 수도 있습니다. 또한 각 사이트에 대해 이러한 설정을 구성할 수 있습니다. 

 ![파일 및 폴더 링크 설정의 스크린샷](media/secure-external-access/9-file-folder-links.png)

Azure AD B2B와의 통합을 사용하도록 설정하면 파일 및 폴더를 조직 외부와 공유하는 경우 파일 및 폴더가 공유될 때 B2B 사용자가 생성됩니다.

기본 링크 유형을 **조직의 사용자만** 으로, 기본 권한을 **편집** 으로 설정하는 것이 좋습니다. 이렇게 하면 항목이 신중하게 공유됩니다. 그런 다음, 특정 협업 요구를 충족하는 사이트별 기본값에 대해 이 설정을 사용자 지정할 수 있습니다.

### <a name="anyone-links"></a>모든 사용자 링크

모든 사용자 링크를 사용하지 않는 것이 좋습니다. 사용하는 경우에는 만료를 설정하는 것이 좋습니다. 사용 권한을 확인하도록 사용자를 제한하는 것이 좋습니다. 파일이나 폴더에 대한 사용 권한 보기만을 선택하는 경우 사용자는 편집 권한을 포함하도록 사용자 링크를 변경할 수 없습니다.

SharePoint에 대한 외부 액세스를 관리하는 방법에 대해 자세히 알아보려면 다음을 참조하세요.

* [SharePoint 외부 공유 개요](/sharepoint/external-sharing-overview)

* [Azure AD B2B와 SharePoint 및 OneDrive 통합](/sharepoint/sharepoint-azureb2b-integration-preview)

#### <a name="next-steps"></a>다음 단계

리소스에 대한 외부 액세스 보안에 관해서는 다음 문서를 참조하세요. 작업은 나열된 순서대로 수행하는 것이 좋습니다.

1. [외부 액세스를 위한 보안 상태 결정](1-secure-access-posture.md)

2. [현재 상태 파악](2-secure-access-current-state.md)

3. [거버넌스 계획 만들기](3-secure-access-plan.md)

4. [보안을 위한 그룹 사용](4-secure-access-groups.md)

5. [Azure AD B2B로 전환](5-secure-access-b2b.md)

6. [권한 관리를 사용한 보안 액세스](6-secure-access-entitlement-managment.md)

7. [조건부 액세스 정책을 사용한 보안 액세스](7-secure-access-conditional-access.md)

8. [민감도 레이블을 통한 보안 액세스](8-secure-access-sensitivity-labels.md)

9. [Microsoft Teams, OneDrive 및 SharePoint에 대한 보안 액세스](9-secure-access-teams-sharepoint.md)(현재 문서)