---
title: 위임 응용 프로그램 관리 관리 perms-Azure AD | Microsoft Docs
description: Azure Active Directory에서 응용 프로그램 액세스 관리에 대 한 권한 부여
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 11/08/2019
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 251bc1c2277f9e43543f95c49d0b730a5a41c3d9
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/13/2020
ms.locfileid: "79253040"
---
# <a name="delegate-app-registration-permissions-in-azure-active-directory"></a>Azure Active Directory에서 앱 등록 권한 위임

이 문서에서는 Azure AD (Azure Active Directory)에서 사용자 지정 역할에 부여 된 사용 권한을 사용 하 여 응용 프로그램 관리 요구 사항을 해결 하는 방법을 설명 합니다. Azure AD에서 다음과 같은 방법으로 응용 프로그램 만들기 및 관리 권한을 위임할 수 있습니다.

- [응용 프로그램을 만들고 사용자가](#restrict-who-can-create-applications) 만드는 응용 프로그램을 관리할 수 있는 사용자를 제한 합니다. 기본적으로 Azure AD에서는 모든 사용자가 응용 프로그램 등록을 등록 하 고 자신이 만든 응용 프로그램의 모든 측면을 관리할 수 있습니다. 이는 사용 권한을 가진 사용자만 허용 하도록 제한할 수 있습니다.
- [하나 이상의 소유자를 응용 프로그램에 할당](#assign-application-owners)합니다. 이 방법은 사용자에 게 특정 응용 프로그램에 대 한 Azure AD 구성의 모든 측면을 관리할 수 있는 기능을 부여 하는 간단한 방법입니다.
- 모든 응용 프로그램에 대해 Azure AD에서 구성을 관리 하는 액세스 권한을 부여 하는 [기본 제공 관리 역할을 할당](#assign-built-in-application-admin-roles) 합니다. 응용 프로그램 구성과 관련이 없는 Azure AD의 다른 부분을 관리 하기 위한 액세스 권한을 부여 하지 않고 광범위 한 응용 프로그램 구성 권한을 관리 하기 위해 IT 전문가에 게 액세스 권한을 부여 하는 데 권장 되는 방법입니다.
- 매우 구체적인 사용 권한을 정의 하 고 사용자에 게 단일 응용 프로그램의 범위를 제한 된 소유자로 할당 하거나 디렉터리 범위 (모든 응용 프로그램)에서 제한 된 관리자로 할당 하 [는 사용자 지정 역할을 만듭니다](#create-and-assign-a-custom-role-preview) .

위의 방법 중 하나를 사용 하 여 두 가지 이유 때문에 액세스 권한을 부여 하는 것이 중요 합니다. 먼저 관리 작업을 수행 하는 기능을 위임 하면 전역 관리자 오버 헤드가 줄어듭니다. 둘째, 제한 된 권한을 사용 하 여 보안 상태를 개선 하 고 무단 액세스 가능성을 줄입니다. 위임 문제 및 일반 지침은 [Azure Active Directory의 관리 위임](roles-concept-delegation.md)에서 설명합니다.

## <a name="restrict-who-can-create-applications"></a>응용 프로그램을 만들 수 있는 사용자 제한

기본적으로 Azure AD에서는 모든 사용자가 응용 프로그램 등록을 등록 하 고 자신이 만든 응용 프로그램의 모든 측면을 관리할 수 있습니다. 또한 누구나 회사 데이터에 액세스 하는 앱에 동의할 수 있습니다. 전역 스위치를 ' 아니요 '로 설정 하 고 선택한 사용자를 응용 프로그램 개발자 역할에 추가 하 여 해당 권한을 선택적으로 부여 하도록 선택할 수 있습니다.

### <a name="to-disable-the-default-ability-to-create-application-registrations-or-consent-to-applications"></a>응용 프로그램 등록을 만들거나 응용 프로그램에 동의할 수 있는 기본 기능을 사용 하지 않도록 설정 하려면

1. Azure AD 조직에서 전역 관리자 역할을 수행할 수 있는 계정을 사용 하 여 Azure AD 조직에 로그인 합니다.
1. 다음 중 하나 또는 둘 다를 설정 합니다.

    -  [조직의 사용자 설정 페이지](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/UserSettings)에서 **사용자가 응용 프로그램을 등록할 수 있음** 설정을 아니요로 설정 합니다. 이렇게 하면 사용자가 응용 프로그램 등록을 만들 수 있는 기본 기능을 사용 하지 않도록 설정 됩니다.
    -  [엔터프라이즈 응용 프로그램에 대 한 사용자 설정](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/UserSettings/menuId/)에서 사용자를 대신 하 여 **회사 데이터에 액세스 하는 응용 프로그램에 동의할 수 있음** 설정을 아니요로 설정 합니다. 이렇게 하면 사용자가 대신 회사 데이터에 액세스 하는 응용 프로그램에 동의할 수 있는 기본 기능이 사용 되지 않습니다.

### <a name="grant-individual-permissions-to-create-and-consent-to-applications-when-the-default-ability-is-disabled"></a>기본 기능이 사용 하지 않도록 설정 된 경우 응용 프로그램을 만들고 동의할 수 있는 개별 사용 권한 부여

**사용자가 응용 프로그램을 등록할 수 있습니다** . 설정이 아니요로 설정 된 경우 응용 프로그램 등록을 만들 수 있는 기능을 부여 하도록 응용 프로그램 개발자 역할을 할당 합니다. 이 역할은 **사용자가 대신 회사 데이터에 액세스 하는 앱에 동의할 수** 있는 경우 사용자를 대신 하 여 사용자에 게 동의할 수 있는 권한을 부여 합니다. 설정이 아니요로 설정 됩니다. 시스템 동작으로, 사용자가 새 응용 프로그램 등록을 만들면 자동으로 첫 번째 소유자로 추가 됩니다. 소유권 사용 권한은 사용자가 소유 하 고 있는 응용 프로그램 등록 또는 엔터프라이즈 응용 프로그램의 모든 측면을 관리할 수 있는 기능을 제공 합니다.

## <a name="assign-application-owners"></a>응용 프로그램 소유자 할당

소유자 할당은 특정 응용 프로그램 등록 또는 엔터프라이즈 응용 프로그램에 대 한 Azure AD 구성의 모든 측면을 관리 하는 기능을 부여 하는 간단한 방법입니다. 시스템 동작으로, 사용자가 새 응용 프로그램 등록을 만들면 자동으로 첫 번째 소유자로 추가 됩니다. 소유권 사용 권한은 사용자가 소유 하 고 있는 응용 프로그램 등록 또는 엔터프라이즈 응용 프로그램의 모든 측면을 관리할 수 있는 기능을 제공 합니다. 원래 소유자를 제거 하 고 추가 소유자를 추가할 수 있습니다.

### <a name="enterprise-application-owners"></a>엔터프라이즈 응용 프로그램 소유자

사용자는 소유자로 서 Single Sign-On 구성, 프로 비전 및 사용자 할당과 같은 엔터프라이즈 응용 프로그램의 조직 특정 구성을 관리할 수 있습니다. 소유자는 다른 소유자를 추가하거나 제거할 수도 있습니다. 전역 관리자와 달리 소유자는 자신이 소유한 엔터프라이즈 응용 프로그램만 관리할 수 있습니다.

경우에 따라 응용 프로그램 갤러리에서 만든 엔터프라이즈 응용 프로그램에는 엔터프라이즈 응용 프로그램과 응용 프로그램 등록이 모두 포함 됩니다. 이 속성이 true 이면 엔터프라이즈 응용 프로그램에 소유자를 추가 하면 해당 응용 프로그램 등록에 소유자가 자동으로 추가 됩니다.

### <a name="to-assign-an-owner-to-an-enterprise-application"></a>엔터프라이즈 응용 프로그램에 소유자를 할당 하려면

1. 조직의 응용 프로그램 관리자 또는 클라우드 응용 프로그램 관리자가 사용할 수 있는 계정을 사용 하 여 [AZURE AD 조직](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) 에 로그인 합니다.
1. 조직의  [앱 등록 페이지](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/AllApps/menuId/) 에서 앱을 선택 하 여 앱에 대 한 개요 페이지를 엽니다.
1. **소유자**를 선택하여 앱의 소유자 목록을 확인합니다.
1. **추가**를 선택하여 앱에 추가할 소유자를 하나 이상 선택합니다.

> [!IMPORTANT]
> 사용자 및 서비스 주체는 응용 프로그램 등록의 소유자가 될 수 있습니다. 사용자만 엔터프라이즈 응용 프로그램의 소유자가 될 수 있습니다. 그룹을 소유자로 할당할 수 없습니다.
>
> 소유자는 응용 프로그램에 자격 증명을 추가 하 고 이러한 자격 증명을 사용 하 여 응용 프로그램의 id를 가장할 수 있습니다. 응용 프로그램은 소유자 보다 많은 권한을 가질 수 있으므로 소유자가 사용자 또는 서비스 사용자로 액세스할 수 있는 권한 상승을 갖습니다. 응용 프로그램 소유자는 응용 프로그램의 권한에 따라 응용 프로그램을 가장 하는 동안 사용자 또는 기타 개체를 만들거나 업데이트할 수 있습니다.

## <a name="assign-built-in-application-admin-roles"></a>기본 제공 응용 프로그램 관리자 역할 할당

Azure AD에는 모든 응용 프로그램에 대해 Azure AD의 구성 관리에 대 한 액세스 권한을 부여 하기 위한 기본 제공 관리자 역할 집합이 있습니다. 이러한 역할은 IT 전문가에 게 응용 프로그램 구성과 관련이 없는 Azure AD의 다른 부분을 관리 하기 위한 액세스 권한을 부여 하지 않고도 광범위 한 응용 프로그램 구성 권한을 관리할 수 있는 권한을 부여 하는 데 권장 되는 방법입니다.

- 응용 프로그램 관리자:이 역할의 사용자는 엔터프라이즈 응용 프로그램, 응용 프로그램 등록 및 응용 프로그램 프록시 설정의 모든 측면을 만들고 관리할 수 있습니다. 이 역할은 위임 된 권한에 동의할 수 있는 기능 및 Microsoft Graph를 제외한 응용 프로그램 권한을 부여 합니다. 이 역할에 할당 된 사용자는 새 응용 프로그램 등록 또는 엔터프라이즈 응용 프로그램을 만들 때 소유자로 추가 되지 않습니다.
- 클라우드 응용 프로그램 관리자:이 역할의 사용자는 응용 프로그램 프록시를 관리 하는 기능을 제외 하 고 응용 프로그램 관리자 역할과 동일한 권한을 갖습니다. 이 역할에 할당 된 사용자는 새 응용 프로그램 등록 또는 엔터프라이즈 응용 프로그램을 만들 때 소유자로 추가 되지 않습니다.

자세한 내용 및 이러한 역할에 대 한 설명을 보려면 [사용 가능한 역할](directory-assign-admin-roles.md#available-roles)을 참조 하세요.

[사용자에 게 역할 할당 Azure Active Directory](../fundamentals/active-directory-users-assign-role-azure-portal.md) 방법 가이드의 지침에 따라 응용 프로그램 관리자 또는 클라우드 응용 프로그램 관리자 역할을 할당 합니다.

> [!IMPORTANT]
> 응용 프로그램 관리자와 클라우드 응용 프로그램 관리자는 자격 증명을 응용 프로그램에 추가 하 고 이러한 자격 증명을 사용 하 여 응용 프로그램의 id를 가장할 수 있습니다. 응용 프로그램에는 관리자 역할의 사용 권한에 대 한 권한 상승 권한이 있을 수 있습니다. 이 역할의 관리자는 응용 프로그램의 사용 권한에 따라 응용 프로그램을 가장 하는 동안 사용자 또는 기타 개체를 만들거나 업데이트할 수 있습니다.
> 두 역할 모두, 조건부 액세스 설정을 관리하는 기능은 부여하지 않습니다.

## <a name="create-and-assign-a-custom-role-preview"></a>사용자 지정 역할 만들기 및 할당 (미리 보기)

사용자 지정 역할을 만들고 사용자 지정 역할을 할당 하는 단계는 다음과 같습니다.

- [사용자 지정 *역할 정의* ](roles-create-custom.md) 를 만들고 [기본 설정 목록에서 해당 정의에 대 한 권한을 추가](roles-custom-available-permissions.md)합니다. 이러한 권한은 기본 제공 역할에 사용 되는 것과 동일한 권한입니다.
- 사용자 지정 역할을 할당 하 [는 *역할 할당* 을 만듭니다](roles-assign-powershell.md) .

이러한 분리를 통해 단일 역할 정의를 만든 다음 여러 *범위*에서 여러 번 할당할 수 있습니다. 사용자 지정 역할은 조직 전체의 범위에서 할당 되거나 단일 Azure AD 개체의 경우 범위에서 할당 될 수 있습니다. 개체 범위의 예로는 단일 앱 등록이 있습니다. 서로 다른 범위를 사용 하 여 조직의 모든 앱 등록에 대해 Sally에 동일한 역할 정의를 할당 한 후 Contoso Expense Reports 앱 등록만 Naveen 수 있습니다.

응용 프로그램 관리 위임에 대 한 사용자 지정 역할을 만들고 사용 하는 경우의 팁:
- 사용자 지정 역할은 Azure AD 포털의 최신 앱 등록 블레이드에만 액세스 권한을 부여 합니다. 레거시 앱 등록 블레이드에는 액세스 권한을 부여 하지 않습니다.
- "Azure AD 관리 포털에 대 한 액세스 제한" 사용자 설정이 예로 설정 된 경우 사용자 지정 역할은 Azure AD 포털에 대 한 액세스 권한을 부여 하지 않습니다.
- 사용자가 역할 할당을 사용 하 여 액세스할 수 있는 앱 등록 앱 등록 페이지의 ' 모든 응용 프로그램 ' 탭에만 표시 됩니다. ' 소유 응용 프로그램 ' 탭에 표시 되지 않습니다.

사용자 지정 역할의 기본 사항에 대 한 자세한 내용은 사용자 지정 역할 [개요](roles-custom-overview.md)를 참조 하 고 [사용자 지정 역할을 만드는](roles-create-custom.md) 방법 및 역할을 [할당](roles-assign-powershell.md)하는 방법을 참조 하세요.

## <a name="next-steps"></a>다음 단계

- [응용 프로그램 등록 하위 유형 및 권한](roles-custom-available-permissions.md)
- [Azure AD 관리자 역할 참조](directory-assign-admin-roles.md)
