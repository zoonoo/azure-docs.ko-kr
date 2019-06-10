---
title: Azure AD 권한 관리 (미리 보기)-Azure Active Directory 문제 해결
description: Azure Active Directory 권한 관리 (미리 보기)를 해결 하기 위해 확인 해야 일부 항목에 알아봅니다.
services: active-directory
documentationCenter: ''
author: rolyon
manager: mtillman
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 05/30/2019
ms.author: rolyon
ms.reviewer: markwahl-msft
ms.collection: M365-identity-device-management
ms.openlocfilehash: c2526ef10c3080dae1b32881a109a9436a0fd390
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/04/2019
ms.locfileid: "66473829"
---
# <a name="troubleshoot-azure-ad-entitlement-management-preview"></a>Azure AD 권한 관리 (미리 보기) 문제 해결

> [!IMPORTANT]
> Azure AD(Azure Active Directory) 권한 관리는 현재 공개 미리 보기로 제공됩니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.
> 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

이 문서에서는 Azure Active Directory (Azure AD) 권한 관리를 해결 하기 위해 확인 해야 하는 일부 항목을 설명 합니다.

## <a name="checklist-for-entitlement-management-administration"></a>권한 관리 관리 하기 위한 검사 목록

* 액세스 권한 관리를 구성 하는 경우 거부 메시지를 표시 하 고 전역 관리자 인 경우 디렉터리에 있는지 확인 한 [Azure AD Premium P2 (또는 EMS E5) 라이선스](entitlement-management-overview.md#license-requirements)합니다.  
* 액세스 거부 메시지를 만들 때 또는 액세스 패키지 보기 가져오고 카탈로그 작성자 그룹의 멤버인 경우에 첫 번째 액세스 패키지를 만들기 전에 카탈로그를 만들어야 합니다.

## <a name="checklist-for-adding-a-resource"></a>리소스를 추가 하기 위한 검사 목록

* 응용 프로그램 액세스 패키지에 리소스를 할당할 수 있는 리소스 역할을 하나 이상 있어야 합니다. 역할 자체 응용 프로그램에 의해 정의 되 고 Azure AD에서 관리 됩니다. Azure portal 응용 프로그램으로 선택할 수 없는 서비스에 대 한 서비스 주체를 표시할 수도 있습니다는 참고 합니다.  특히 **Exchange Online** 하 고 **SharePoint Online** 는 액세스 패키지에 포함 될 수 없습니다 있도록 디렉터리 리소스 역할에 있는 응용 프로그램이 아닌 서비스입니다.  대신, 해당 서비스에 액세스 해야 하는 사용자에 대 한 적절 한 라이선스를 설정 하려면 그룹 기반 라이선스를 사용 합니다.

* 액세스 패키지에 리소스 그룹에 대 한 Azure AD에서 수정할 수 있어야 합니다.  Azure AD에서 자신의 소유자 또는 멤버 특성을 변경할 수 없으므로 리소스와 온-프레미스 Active Directory에서 발생 하는 그룹을 할당할 수 없습니다.  

* SharePoint Online 문서 라이브러리 및 개별 문서를 리소스로 추가할 수 없습니다.  대신 Azure AD 보안 그룹을 만드는 액세스 패키지에 있는 그룹을 사이트 역할을 포함 하 고 SharePoint Online의 문서 라이브러리 또는 문서에 대 한 액세스를 제어 하는 그룹을 사용 합니다.

* 사용자는 액세스 패키지를 사용 하 여 관리 하려는 리소스에 이미 할당 된 경우 사용자는 적절 한 정책 사용 하 여 액세스 패키지에 할당 되어 해야 합니다. 예를 들어, 다음 그룹에 사용자가 이미 있는 액세스 패키지에는 그룹을 포함 하는 것이 좋습니다. 해당 사용자 그룹 필요에 계속 액세스 하는 경우 그룹에 대 한 액세스 권한이 유지 되도록 액세스 패키지에 대 한 적절 한 정책이 있어야 합니다. 해당 리소스를 포함 하는 액세스 패키지를 요청 하려면 사용자에 게 요청 하거나 또는 직접 액세스 패키지에 할당 하 여 액세스 패키지를 할당할 수 있습니다. 자세한 내용은 [편집 하 고 기존 액세스 패키지를 관리할](entitlement-management-access-package-edit.md)합니다.

## <a name="checklist-for-providing-external-users-access"></a>외부 사용자에 게 액세스를 제공 하기 위한 검사 목록

* 경우는 B2B [허용 목록](../b2b/allow-deny-list.md), 사용자가 해당 디렉터리는 허용 되지 않습니다 액세스를 요청할 수 없습니다.

* 없는지 없습니다 [조건부 액세스 정책을](../conditional-access/require-managed-devices.md) 외부 사용자가 액세스를 요청 하거나 응용 프로그램 액세스 패키지에서 사용할 수 있는 수 없게 됩니다.

## <a name="checklist-for-request-issues"></a>요청 문제에 대 한 검사 목록

* 사용자를 액세스 패키지에 대 한 액세스를 요청 하려는 경우 사용 중인 확인 해야 합니다 **내 액세스 포털 링크** 액세스 패키지에 대 한 합니다. 자세한 내용은 [복사 내 액세스 포털 링크](entitlement-management-access-package-edit.md#copy-my-access-portal-link)합니다.

* 사용자 액세스 패키지를 요청 하려면, 내 액세스 포털에 로그인 하면 자신의 조직 계정을 사용 하 여 인증 해야 합니다. 조직 계정 액세스 패키지의 정책 중 하나에 포함 된 디렉터리 또는 리소스 디렉터리에 두 계정을 수 있습니다. 사용자 계정, 조직 계정이 아닌 경우 디렉터리 정책에 포함 되지 않습니다 사용자는 액세스 패키지를 나타나지 않습니다. 자세한 내용은 [액세스 패키지에 대 한 액세스 요청](entitlement-management-request-access.md)합니다.

* 리소스 디렉터리에 로그인에서 사용자가 차단 되어 있으면 내 액세스 포털에서 액세스를 요청할 수 없습니다. 사용자 액세스를 요청 하려면, 먼저 사용자의 프로필에서 로그인 한 블록을 제거 해야 합니다. Azure portal에서 로그인 블록을 제거 하려면 클릭 **Azure Active Directory**, 클릭 **사용자가**사용자를 클릭 하 고 클릭 **프로필**합니다. 편집 합니다 **설정을** 섹션 및 변경 **로그인 차단** 에 **No**합니다. 자세한 내용은 [추가 Azure Active Directory를 사용 하 여 사용자의 프로필 정보를 업데이트 하거나](../fundamentals/active-directory-users-profile-azure-portal.md)합니다.  사용자로 인해 차단 된 경우를 확인할 수도 있습니다는 [Id 보호 정책을](../identity-protection/howto-unblock-user.md)합니다.

* 내 액세스 포털에서 사용자는 요청자 및 승인자가는 표시 되지 것입니다 액세스 패키지 요청에는 **승인** 페이지입니다. 이 동작은 의도적인-사용자가 자신의 요청을 승인할 수 없습니다. 정책을 구성 하는 추가 승인자가 요청 하는 액세스 패키지를 확인 합니다. 자세한 내용은 [기존 정책을 편집할](entitlement-management-access-package-edit.md#edit-an-existing-policy)합니다.

* 새 외부 사용자를 디렉터리에 이전에 로그인 하지 않은, 하는 SharePoint Online 사이트를 포함 하는 액세스 패키지를 수신 하는 경우 SharePoint Online에서 해당 계정이 프로 비전 될 때까지 완전히 제공 되는 액세스 패키지 표시 됩니다.

## <a name="next-steps"></a>다음 단계

- [사용자 권한 관리에서 액세스를 확보 하는 방법의 보고서 보기](entitlement-management-reports.md)
