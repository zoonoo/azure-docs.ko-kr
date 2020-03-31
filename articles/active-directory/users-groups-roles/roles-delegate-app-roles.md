---
title: 응용 프로그램 관리자 관리 파마 위임 - Azure AD | 마이크로 소프트 문서
description: Azure Active Directory에서 응용 프로그램 액세스 관리에 대한 권한 부여
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79253040"
---
# <a name="delegate-app-registration-permissions-in-azure-active-directory"></a>Azure Active Directory에서 앱 등록 권한 위임

이 문서에서는 Azure Active Directory(Azure AD)에서 사용자 지정 역할에 의해 부여된 권한을 사용하여 응용 프로그램 관리 요구 사항을 해결하는 방법을 설명합니다. Azure AD에서 다음과 같은 방법으로 응용 프로그램 만들기 및 관리 권한을 위임할 수 있습니다.

- 응용 프로그램을 만들고 응용 프로그램을 관리할 수 있는 [사람을 제한합니다.](#restrict-who-can-create-applications) 기본적으로 Azure AD에서 모든 사용자는 응용 프로그램 등록을 등록하고 사용자가 만든 응용 프로그램의 모든 측면을 관리할 수 있습니다. 이 권한은 선택한 사용자만 허용하도록 제한할 수 있습니다.
- [응용 프로그램에 하나 이상의 소유자를 할당합니다.](#assign-application-owners) 이는 특정 응용 프로그램에 대한 Azure AD 구성의 모든 측면을 관리할 수 있는 기능을 다른 사람에게 부여하는 간단한 방법입니다.
- 모든 응용 프로그램에 대해 Azure AD에서 구성을 관리할 수 있는 액세스 권한을 부여하는 [기본 제공 관리 역할을 할당합니다.](#assign-built-in-application-admin-roles) 이는 IT 전문가에게 응용 프로그램 구성과 관련이 없는 Azure AD의 다른 부분을 관리할 수 있는 액세스 권한을 부여하지 않고도 광범위한 응용 프로그램 구성 권한을 관리할 수 있는 액세스 권한을 부여하는 데 권장되는 방법입니다.
- 매우 [구체적인](#create-and-assign-a-custom-role-preview) 사용 권한을 정의하는 사용자 지정 역할을 만들고 단일 응용 프로그램의 범위에 제한된 소유자로 할당하거나 제한된 관리자로 디렉터리 범위(모든 응용 프로그램)에 할당합니다.

두 가지 이유로 위의 방법 중 하나를 사용하여 액세스 권한을 부여하는 것이 중요합니다. 첫째, 관리 작업을 수행하는 기능을 위임하면 전역 관리자 오버헤드가 줄어듭니다. 둘째, 제한된 권한을 사용하면 보안 태세가 향상되고 무단 액세스 가능성이 줄어듭니다. 위임 문제 및 일반 지침은 [Azure Active Directory의 관리 위임](roles-concept-delegation.md)에서 설명합니다.

## <a name="restrict-who-can-create-applications"></a>응용 프로그램을 만들 수 있는 사람 제한

기본적으로 Azure AD에서 모든 사용자는 응용 프로그램 등록을 등록하고 사용자가 만든 응용 프로그램의 모든 측면을 관리할 수 있습니다. 또한 모든 사람은 회사를 대신하여 회사 데이터에 액세스하는 앱에 동의할 수 있습니다. 전역 스위치를 '아니요'로 설정하고 선택한 사용자를 응용 프로그램 개발자 역할에 추가하여 이러한 권한을 선택적으로 부여하도록 선택할 수 있습니다.

### <a name="to-disable-the-default-ability-to-create-application-registrations-or-consent-to-applications"></a>응용 프로그램 등록을 만들거나 응용 프로그램에 대한 동의를 만드는 기본 기능을 사용하지 않도록 설정하려면

1. Azure AD 조직에서 글로벌 관리자 역할을 받을 수 있는 계정으로 Azure AD 조직에 로그인합니다.
1. 다음 중 하나 또는 둘 다 설정합니다.

    - 조직의 사용자 설정 페이지에서 사용자가 응용 프로그램 설정을 아니요로 **등록할 수 있도록**  [설정합니다.](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/UserSettings) 이렇게 하면 사용자가 응용 프로그램 등록을 만들 수 있는 기본 기능이 비활성화됩니다.
    - 엔터프라이즈 [응용 프로그램에 대한 사용자 설정에서](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/UserSettings/menuId/) **사용자가 대신 회사 데이터에 액세스하는 응용 프로그램에 동의할 수 있도록** 설정합니다. 이렇게 하면 사용자가 대신 회사 데이터에 액세스하는 응용 프로그램에 동의하는 기본 기능이 비활성화됩니다.

### <a name="grant-individual-permissions-to-create-and-consent-to-applications-when-the-default-ability-is-disabled"></a>기본 기능이 비활성화된 경우 응용 프로그램을 만들고 동의할 수 있는 개별 권한 부여

사용자가 응용 프로그램 설정을 No로 설정할 때 응용 프로그램 등록을 만들 수 있는 기능을 부여하도록 응용 프로그램 개발자 역할을 **할당합니다.** 또한 이 역할은 사용자가 사용자를 대신하여 회사 데이터에 **액세스하는 앱에 동의할 수** 있는 경우 사용자를 대신하여 동의할 수 있는 권한을 No로 설정합니다. 시스템 동작으로 사용자가 새 응용 프로그램 등록을 만들 때 자동으로 첫 번째 소유자로 추가됩니다. 소유권 권한을 사용하면 사용자가 소유한 응용 프로그램 등록 또는 엔터프라이즈 응용 프로그램의 모든 측면을 관리할 수 있습니다.

## <a name="assign-application-owners"></a>응용 프로그램 소유자 할당

소유자 를 할당하는 간단한 방법은 특정 응용 프로그램 등록 또는 엔터프라이즈 응용 프로그램에 대한 Azure AD 구성의 모든 측면을 관리하는 기능을 부여하는 간단한 방법입니다. 시스템 동작으로 사용자가 새 응용 프로그램 등록을 만들 면 자동으로 첫 번째 소유자로 추가 됩니다. 소유권 권한을 사용하면 사용자가 소유한 응용 프로그램 등록 또는 엔터프라이즈 응용 프로그램의 모든 측면을 관리할 수 있습니다. 원래 소유자를 제거할 수 있으며 추가 소유자를 추가할 수 있습니다.

### <a name="enterprise-application-owners"></a>엔터프라이즈 응용 프로그램 소유자

소유자는 단일 사인온 구성, 프로비저닝 및 사용자 할당과 같은 엔터프라이즈 응용 프로그램의 조직별 구성을 관리할 수 있습니다. 소유자는 다른 소유자를 추가하거나 제거할 수도 있습니다. 글로벌 관리자와 달리 소유자는 소유한 엔터프라이즈 응용 프로그램만 관리할 수 있습니다.

경우에 따라 응용 프로그램 갤러리에서 만든 엔터프라이즈 응용 프로그램에는 엔터프라이즈 응용 프로그램과 응용 프로그램 등록이 모두 포함됩니다. 이 경우 엔터프라이즈 응용 프로그램에 소유자를 추가하면 해당 응용 프로그램 등록에 소유자가 자동으로 추가됩니다.

### <a name="to-assign-an-owner-to-an-enterprise-application"></a>엔터프라이즈 응용 프로그램에 소유자를 할당하려면

1. 조직의 응용 프로그램 관리자 또는 클라우드 응용 프로그램 관리자를 사용할 수 있는 계정으로 [Azure AD 조직에](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) 로그인합니다.
1. 조직의 [앱 등록 페이지에서](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/AllApps/menuId/) 앱을 선택하여 앱의 개요 페이지를 엽니다.
1. **소유자**를 선택하여 앱의 소유자 목록을 확인합니다.
1. **추가**를 선택하여 앱에 추가할 소유자를 하나 이상 선택합니다.

> [!IMPORTANT]
> 사용자와 서비스 주체는 응용 프로그램 등록의 소유자일 수 있습니다. 사용자만 엔터프라이즈 응용 프로그램의 소유자가 될 수 있습니다. 그룹 은 둘 중 하나의 소유자로 할당할 수 없습니다.
>
> 소유자는 응용 프로그램에 자격 증명을 추가하고 해당 자격 증명을 사용하여 응용 프로그램의 ID를 가장할 수 있습니다. 응용 프로그램에는 소유자보다 더 많은 권한이 있을 수 있으므로 소유자가 사용자 또는 서비스 주체로 액세스할 수 있는 권한에 대한 권한 상승이 될 수 있습니다. 응용 프로그램 소유자는 응용 프로그램의 사용 권한에 따라 응용 프로그램을 가장하는 동안 사용자 또는 다른 개체를 만들거나 업데이트할 수 있습니다.

## <a name="assign-built-in-application-admin-roles"></a>기본 제공 응용 프로그램 관리자 역할 할당

Azure AD에는 모든 응용 프로그램에 대한 Azure AD의 구성을 관리할 수 있는 권한을 부여하기 위한 기본 제공 관리자 역할 집합이 있습니다. 이러한 역할은 IT 전문가에게 응용 프로그램 구성과 관련이 없는 Azure AD의 다른 부분을 관리할 수 있는 액세스 권한을 부여하지 않고 광범위한 응용 프로그램 구성 권한을 관리할 수 있는 권한을 부여하는 권장 되는 방법입니다.

- 애플리케이션 관리자: 이 역할의 사용자는 엔터프라이즈 애플리케이션, 애플리케이션 등록 및 애플리케이션 프록시 설정의 모든 측면을 만들고 관리할 수 있습니다. 또한 이 역할은 Microsoft Graph를 제외한 위임된 권한 및 응용 프로그램 권한에 동의할 수 있는 권한을 부여합니다. 이 역할에 할당된 사용자는 새 응용 프로그램 등록 또는 엔터프라이즈 응용 프로그램을 만들 때 소유자로 추가되지 않습니다.
- 클라우드 애플리케이션 관리자: 이 역할의 사용자는 애플리케이션 관리자 역할과 동일한 권한을 가집니다. 애플리케이션 프록시를 관리하는 기능은 제외됩니다. 이 역할에 할당된 사용자는 새 응용 프로그램 등록 또는 엔터프라이즈 응용 프로그램을 만들 때 소유자로 추가되지 않습니다.

자세한 정보 및 이러한 역할에 대한 설명을 보려면 [사용 가능한 역할을](directory-assign-admin-roles.md#available-roles)참조하십시오.

Azure Active Directory 사용 방법 가이드를 [사용하여 사용자에게 역할 할당의](../fundamentals/active-directory-users-assign-role-azure-portal.md) 지침에 따라 응용 프로그램 관리자 또는 클라우드 응용 프로그램 관리자 역할을 할당합니다.

> [!IMPORTANT]
> 응용 프로그램 관리자와 클라우드 응용 프로그램 관리자는 응용 프로그램에 자격 증명을 추가하고 이러한 자격 증명을 사용하여 응용 프로그램의 ID를 가장할 수 있습니다. 응용 프로그램에관리자 역할의 권한에 대한 권한 상승 권한인 권한이 있을 수 있습니다. 이 역할의 관리자는 응용 프로그램의 사용 권한에 따라 응용 프로그램을 가장하는 동안 사용자 또는 다른 개체를 만들거나 업데이트할 수 있습니다.
> 두 역할 모두, 조건부 액세스 설정을 관리하는 기능은 부여하지 않습니다.

## <a name="create-and-assign-a-custom-role-preview"></a>사용자 지정 역할 만들기 및 할당(미리 보기)

사용자 지정 역할을 만들고 사용자 지정 역할을 할당하는 것은 별도의 단계입니다.

- [사용자 지정 *역할 정의를* 만들고](roles-create-custom.md) 사전 설정 목록에서 사용 권한을 [추가합니다.](roles-custom-available-permissions.md) 기본 제공 역할에 사용된 것과 동일한 사용 권한입니다.
- 사용자 지정 역할을 할당하는 [ *역할 할당을* 만듭니다.](roles-assign-powershell.md)

이 구분을 사용하면 단일 역할 정의를 만든 다음 다른 범위에서 여러 번 할당할 *수 있습니다.* 사용자 지정 역할은 조직 전체 범위에서 할당할 수 있으며, 단일 Azure AD 개체인 경우 범위에서 할당할 수 있습니다. 개체 범위의 예는 단일 앱 등록입니다. 다른 범위를 사용하여 조직의 모든 앱 등록에 대해 동일한 역할 정의를 샐리에 할당한 다음 Contoso 비용 보고서 앱 등록만 을 통해 Naveen에 할당할 수 있습니다.

응용 프로그램 관리를 위임하기 위한 사용자 지정 역할을 만들고 사용할 때의 팁:
- 사용자 지정 역할은 Azure AD 포털의 최신 앱 등록 블레이드에서만 액세스 권한을 부여합니다. 레거시 앱 등록 블레이드에서 액세스 권한을 부여하지 않습니다.
- 사용자 지정 역할은 "Azure AD 관리 포털에 대한 액세스 제한" 사용자 설정이 예로 설정되어 있으면 Azure AD 포털에 대한 액세스 권한을 부여하지 않습니다.
- 사용자가 역할 할당을 사용할 수 있는 앱 등록은 앱 등록 페이지의 '모든 응용 프로그램' 탭에만 표시됩니다. '소유 응용 프로그램' 탭에 표시되지 않습니다.

사용자 지정 역할의 기본 사항에 대한 자세한 내용은 [사용자 지정 역할 개요](roles-custom-overview.md)및 사용자 지정 [역할을 만드는](roles-create-custom.md) 방법 및 역할을 할당하는 방법을 [참조하세요.](roles-assign-powershell.md)

## <a name="next-steps"></a>다음 단계

- [응용 프로그램 등록 하위 유형 및 권한](roles-custom-available-permissions.md)
- [Azure AD 관리자 역할 참조](directory-assign-admin-roles.md)
