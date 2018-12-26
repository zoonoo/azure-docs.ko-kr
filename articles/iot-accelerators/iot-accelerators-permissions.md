---
title: Azure IoT 솔루션 가속기 및 Azure Active Directory | Microsoft Docs
description: Azure IoT 솔루션 가속기에서 Azure Active Directory를 사용하여 사용 권한을 관리하는 방법을 설명합니다.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 11/10/2017
ms.author: dobett
ms.openlocfilehash: e45954389c8dd1b484a7009460c541bf35266973
ms.sourcegitcommit: c29d7ef9065f960c3079660b139dd6a8348576ce
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/12/2018
ms.locfileid: "44713853"
---
# <a name="permissions-on-the-azureiotsolutionscom-site"></a>azureiotsolutions.com 사이트에 대한 사용 권한

## <a name="what-happens-when-you-sign-in"></a>로그인 후 진행되는 작업

[azureiotsuite.com][lnk-azureiotsolutions]에 처음 로그인하면, 사이트에서는 현재 선택된 AAD(Azure Active Directory) 테넌트 및 Azure 구독을 기반으로 사용자가 가진 사용 권한 수준을 결정합니다.

1. 로그인 사용자 이름 옆에 표시되는 테넌트 목록을 채우려면 먼저 사이트가 Azure로부터 사용자가 속하는 AAD 테넌트를 알아내야 합니다. 현재 사이트는 테넌트에 대한 사용자 토큰을 한 번에 하나만 가져올 수 있습니다. 따라서 오른쪽 상단 모서리의 드롭다운을 사용하여 테넌트로 전환하면, 사이트는 이 테넌트에 대한 토큰을 가져오기 위해서 해당 테넌트로 사용자를 다시 로그인합니다.

2. 다음으로, 사이트는 사용자가 선택한 테넌트와 연결된 구독을 Azure로부터 알아냅니다. 솔루션 가속기를 새로 만들 때 사용할 수 있는 구독이 표시됩니다.

3. 마지막으로 사이트는 솔루션 가속기로 태그가 지정된 구독 및 리소스 그룹에서 모든 리소스를 가져와서 홈 페이지에 타일을 채웁니다.

다음 섹션은 솔루션 가속기에 대한 액세스를 제어하는 역할을 설명합니다.

## <a name="aad-roles"></a>AAD 역할

AAD 역할은 솔루션 가속기를 프로비전하고 솔루션 가속기의 일부 Azure 서비스 및 사용자를 관리할 권한을 제어합니다.

AAD에서 사용자 및 관리자 역할에 대한 자세한 내용은 [Azure AD에서 관리자 역할 할당][lnk-aad-admin]에서 찾을 수 있습니다. 현재 문서에서는 솔루션 가속기에서 사용된 대로 **전역 관리자** 및 **사용자** 디렉터리 역할에 중점을 둡니다.

### <a name="global-administrator"></a>전역 관리자

AAD 테넌트에는 여러 명의 전역 관리자가 있을 수 있습니다.

* AAD 테넌트를 만들 때, 만드는 사람은 기본적으로 해당 테넌트의 전역 관리자입니다.
* 전역 관리자는 기본 및 표준 솔루션 가속기(기본 배포는 단일 Azure Virtual Machine 사용)를 프로비전할 수 있습니다.

### <a name="domain-user"></a>도메인 사용자

AAD 테넌트에는 여러 명의 도메인 사용자가 있을 수 있습니다.

* 도메인 사용자는 [azureiotsolutions.com][lnk-azureiotsolutions] 사이트를 통해 기본 솔루션 가속기를 프로비전할 수 있습니다.
* 도메인 사용자는 CLI를 사용하여 기본 솔루션 가속기를 만들 수 있습니다.

### <a name="guest-user"></a>게스트 사용자

AAD 테넌트에는 여러 명의 게스트 사용자가 있을 수 있습니다. 게스트 사용자는 AAD 테넌트 내에서 제한된 권한 집합을 갖습니다. 결과적으로 게스트 사용자는 AAD 테넌트 내에서 솔루션 가속기를 프로비전할 수 없습니다.

AAD의 사용자 및 역할에 대한 자세한 내용은 다음 리소스를 참조하세요.

* [Azure AD에서 사용자 만들기][lnk-create-edit-users]
* [앱에 사용자 할당][lnk-assign-app-roles]

## <a name="azure-subscription-administrator-roles"></a>Azure 구독 관리자 역할

Azure 관리자 역할은 Azure 구독을 AAD 테넌트에 매핑할 수 있는 권한을 제어합니다.

[Azure 구독 관리자 추가 또는 변경][lnk-admin-roles] 문서에서 Azure 관리자 역할에 대해 자세히 알아보세요.

## <a name="faq"></a>FAQ

### <a name="im-a-service-administrator-and-id-like-to-change-the-directory-mapping-between-my-subscription-and-a-specific-aad-tenant-how-do-i-complete-this-task"></a>서비스 관리자가 구독과 특정 AAD 테넌트 간의 디렉터리 매핑을 변경하고자 합니다. 이 태스크를 완료하려면 어떻게 해야 하나요?

[Azure AD 디렉터리에 기존 구독을 추가하는 방법](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md#to-associate-an-existing-subscription-to-your-azure-ad-directory)을 참조하세요.

### <a name="i-want-to-change-a-service-administrator-or-co-administrator-when-logged-in-with-an-organizational-account"></a>조직 계정으로 로그인 할 때 서비스 관리자 또는 공동 관리자를 변경하려고 합니다.

지원 문서 [조직 계정으로 로그인하였을 때 서비스 관리자 및 공동 관리자 변경][lnk-service-admins]을 참조하세요.

### <a name="why-am-i-seeing-this-error-your-account-does-not-have-the-proper-permissions-to-create-a-solution-please-check-with-your-account-administrator-or-try-with-a-different-account"></a>왜 이 오류가 표시되나요? "Your account does not have the proper permissions to create a solution.(사용자의 계정에 솔루션을 생성하기에 적합한 권한이 없습니다.) Please check with your account administrator or try with a different account.(계정 관리자에게 문의하거나 다른 계정으로 시도하세요.)"

지침은 다음 다이어그램을 살펴보겠습니다.

![][img-flowchart]

> [!NOTE]
> AAD 테넌트의 전역 관리자이고 구독의 공동 관리자임을 확인한 후에 오류가 계속 표시되는 경우 계정 관리자를 통해 사용자를 제거하고 다음과 같은 순서로 필요한 사용 권한을 다시 할당합니다. 먼저 사용자를 전역 관리자로 추가한 다음, Azure 구독의 공동 관리자로 추가합니다. 문제가 지속되면 [도움말 및 지원][lnk-help-support]에 문의하세요.

### <a name="why-am-i-seeing-this-error-when-i-have-an-azure-subscription-an-azure-subscription-is-required-to-create-pre-configured-solutions-you-can-create-a-free-trial-account-in-just-a-couple-of-minutes"></a>Azure 구독이 있는데 왜 이런 오류가 표시되나요? "Azure 구독은 미리 구성된 솔루션을 만드는 데 필요합니다. 몇 분 만에 무료 평가판 계정을 만들 수 있습니다."

Azure 구독이 있는 것이 확실하다면, 구독에 대한 테넌트 매핑의 유효성을 검사하고 드롭다운에 올바른 테넌트가 선택되어 있는지 확인하세요. 원하는 테넌트가 맞는다는 것을 확인했으면, 이전의 다이어그램에 따라서 구독과 이 AAD 테넌트 매핑의 유효성을 검사합니다.

## <a name="next-steps"></a>다음 단계
IoT 솔루션 가속기에 대해 계속 알아보려면 [솔루션 가속기 사용자 지정][lnk-customize] 방법을 참조하세요.

[img-flowchart]: media/iot-accelerators-permissions/flowchart.png

[lnk-azureiotsolutions]: https://www.azureiotsolutions.com
[lnk-rm-github-repo]: https://github.com/Azure/remote-monitoring-services-dotnet
[lnk-pm-github-repo]: https://github.com/Azure/azure-iot-predictive-maintenance
[lnk-cf-github-repo]: https://github.com/Azure/azure-iot-connected-factory
[lnk-aad-admin]:../active-directory/users-groups-roles/directory-assign-admin-roles.md
[lnk-portal]: https://portal.azure.com
[lnk-create-edit-users]:../active-directory/fundamentals/active-directory-users-profile-azure-portal.md
[lnk-assign-app-roles]:../active-directory/manage-apps/assign-user-or-group-access-portal.md
[lnk-service-admins]: https://azure.microsoft.com/support/changing-service-admin-and-co-admin
[lnk-admin-roles]: ../billing/billing-add-change-azure-subscription-administrator.md
[lnk-help-support]: https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade
[lnk-customize]: iot-accelerators-remote-monitoring-customize.md
