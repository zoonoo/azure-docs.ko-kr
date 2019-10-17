---
title: Azure AD 자격 관리 (미리 보기) 문제 해결-Azure Active Directory
description: Azure Active Directory 자격 관리 (미리 보기) 문제를 해결 하기 위해 확인 해야 하는 몇 가지 항목에 대해 알아봅니다.
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 05/30/2019
ms.author: ajburnle
ms.reviewer: markwahl-msft
ms.collection: M365-identity-device-management
ms.openlocfilehash: ea979731c27a8d332102c3215e80510994f2ab3f
ms.sourcegitcommit: 77bfc067c8cdc856f0ee4bfde9f84437c73a6141
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/16/2019
ms.locfileid: "72430228"
---
# <a name="troubleshoot-azure-ad-entitlement-management-preview"></a>Azure AD 자격 관리 (미리 보기) 문제 해결

> [!IMPORTANT]
> Azure AD(Azure Active Directory) 권한 관리는 현재 공개 미리 보기로 제공됩니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.
> 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

이 문서에서는 Azure AD (Azure Active Directory) 자격 관리의 문제를 해결 하기 위해 확인 해야 하는 몇 가지 항목을 설명 합니다.

## <a name="checklist-for-entitlement-management-administration"></a>자격 관리 관리를 위한 검사 목록

* 권한 관리를 구성할 때 액세스 거부 메시지가 표시 되 고 전역 관리자 인 경우 디렉터리에 [Azure AD Premium P2 (또는 EMS E5) 라이선스가](entitlement-management-overview.md#license-requirements)있는지 확인 합니다.  
* Access 패키지를 만들거나 볼 때 액세스 거부 메시지가 표시 되 고 사용자가 카탈로그 작성자 그룹의 멤버인 경우 첫 번째 액세스 패키지를 만들기 전에 카탈로그를 만들어야 합니다.

## <a name="checklist-for-adding-a-resource"></a>리소스 추가에 대 한 검사 목록

* 응용 프로그램이 액세스 패키지의 리소스가 되려면 할당 될 수 있는 리소스 역할이 하나 이상 있어야 합니다. 역할은 응용 프로그램 자체에 의해 정의 되며 Azure AD에서 관리 됩니다. Azure Portal는 응용 프로그램으로 선택할 수 없는 서비스에 대 한 서비스 사용자를 표시할 수도 있습니다.  특히 **Exchange online** 및 **SharePoint online** 은 디렉터리에 리소스 역할이 있는 응용 프로그램이 아니라 서비스 이므로 액세스 패키지에 포함 될 수 없습니다.  대신 그룹 기반 라이선스를 사용 하 여 해당 서비스에 액세스 해야 하는 사용자에 게 적절 한 라이선스를 설정 합니다.

* 그룹이 액세스 패키지의 리소스가 되려면 Azure AD에서 수정할 수 있어야 합니다.  Azure AD에서 소유자 또는 멤버 특성을 변경할 수 없으므로 온-프레미스 Active Directory에서 발생 하는 그룹을 리소스로 할당할 수 없습니다.   Exchange Online에서 배포 그룹으로 시작 된 그룹은 Azure AD에서 수정할 수 없습니다. 

* SharePoint Online 문서 라이브러리와 개별 문서를 리소스로 추가할 수 없습니다.  대신, Azure AD 보안 그룹을 만들고, 해당 그룹과 사이트 역할을 액세스 패키지에 포함 하 고, SharePoint Online에서 해당 그룹을 사용 하 여 문서 라이브러리나 문서에 대 한 액세스를 제어 합니다.

* 액세스 패키지를 사용 하 여 관리 하려는 리소스에 이미 할당 된 사용자가 있는 경우 해당 사용자에 게 적절 한 정책을 사용 하 여 액세스 패키지를 할당 해야 합니다. 예를 들어 그룹에 이미 사용자가 있는 액세스 패키지에 그룹을 포함 하려는 경우가 있습니다. 그룹의 사용자가 계속 액세스 해야 하는 경우 그룹에 대 한 액세스 권한이 손실 되지 않도록 액세스 패키지에 대 한 적절 한 정책이 있어야 합니다. 사용자에 게 해당 리소스를 포함 하는 액세스 패키지를 요청 하거나 액세스 패키지에 직접 할당 하 여 액세스 패키지를 할당할 수 있습니다. 자세한 내용은 [액세스 패키지에 대 한 요청 및 승인 설정 변경](entitlement-management-access-package-request-policy.md)을 참조 하세요.

## <a name="checklist-for-providing-external-users-access"></a>외부 사용자에 게 액세스를 제공 하기 위한 검사 목록

* B2B [허용 목록이](../b2b/allow-deny-list.md)있는 경우 해당 디렉터리가 허용 되지 않은 사용자는 액세스를 요청할 수 없습니다.

* 외부 사용자가 액세스를 요청 하거나 액세스 패키지의 응용 프로그램을 사용할 수 없게 하는 [조건부 액세스 정책이](../conditional-access/require-managed-devices.md) 없는지 확인 합니다.

## <a name="checklist-for-request-issues"></a>요청 문제에 대 한 검사 목록

* 사용자가 액세스 패키지에 대 한 액세스를 요청 하려는 경우 액세스 패키지에 대 한 **내 액세스 포털 링크** 를 사용 해야 합니다. 자세한 내용은 [액세스 패키지를 요청 하는 공유 링크를](entitlement-management-access-package-settings.md)참조 하세요.  외부 사용자가 **myaccess.microsoft.com**를 방문 하면 해당 조직에서 사용할 수 있는 액세스 패키지가 표시 됩니다.

* 디렉터리에 아직 없는 사용자가 액세스 패키지를 요청 하기 위해 내 액세스 포털에 로그인 하는 경우 조직 계정을 사용 하 여 인증 해야 합니다. 조직 계정은 리소스 디렉터리의 계정 이거나 액세스 패키지의 정책 중 하나에 포함 된 디렉터리 일 수 있습니다. 사용자 계정이 조직 계정이 아니거나 인증 된 디렉터리가 정책에 포함 되지 않은 경우 사용자에 게 액세스 패키지가 표시 되지 않습니다. 자세한 내용은 [액세스 패키지에 대 한 액세스 요청](entitlement-management-request-access.md)을 참조 하세요.

* 사용자가 리소스 디렉터리에 로그인 하지 못하도록 차단 된 경우에는 내 액세스 포털에서 액세스를 요청할 수 없습니다. 사용자가 액세스를 요청 하려면 먼저 사용자의 프로필에서 로그인 블록을 제거 해야 합니다. 로그인 블록을 제거 하려면 Azure Portal에서 **Azure Active Directory** **를 클릭 하 고 사용자를 클릭**한 다음 사용자를 클릭 하 고 **프로필**을 클릭 합니다. **설정** 섹션을 편집 하 고 **블록 로그인** 을 **아니요**로 변경 합니다. 자세한 내용은 [Azure Active Directory를 사용 하 여 사용자의 프로필 정보 추가 또는 업데이트](../fundamentals/active-directory-users-profile-azure-portal.md)를 참조 하세요.  [Id 보호 정책](../identity-protection/howto-unblock-user.md)으로 인해 사용자가 차단 되었는지 여부도 확인할 수 있습니다.

* 내 액세스 포털에서 사용자가 요청자 이자 승인자 인 경우 **승인** 페이지에서 액세스 패키지에 대 한 요청이 표시 되지 않습니다. 이 동작은 의도적인 것입니다. 사용자가 자신의 요청을 승인할 수 없습니다. 요청 하는 액세스 패키지에 정책에 구성 된 추가 승인자가 있는지 확인 합니다. 자세한 내용은 [액세스 패키지에 대 한 요청 및 승인 설정 변경](entitlement-management-access-package-request-policy.md)을 참조 하세요.

* 이전에 디렉터리에서 서명 하지 않은 새 외부 사용자가 SharePoint Online 사이트를 포함 하는 액세스 패키지를 수신 하는 경우 해당 계정이 SharePoint Online에서 프로 비전 될 때까지 해당 액세스 패키지는 완전히 배달 되지 않음으로 표시 됩니다.

## <a name="next-steps"></a>다음 단계

- [자격 관리에서 사용자가 액세스 하는 방법에 대 한 보고서 보기](entitlement-management-reports.md)
- [외부 사용자에 대 한 액세스 제어](entitlement-management-external-users.md)
