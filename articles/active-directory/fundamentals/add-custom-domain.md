---
title: 사용자 지정 도메인 추가 - Azure Active Directory | Microsoft Docs
description: Azure Active Directory를 사용하여 사용자 지정 도메인을 추가하는 방법에 대한 지침입니다.
services: active-directory
author: msaburnley
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 10/25/2019
ms.author: ajburnle
ms.reviewer: elkuzmen
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: de91bd7e1e4c5f9909213f663dd3ede0f979d4de
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79262153"
---
# <a name="add-your-custom-domain-name-using-the-azure-active-directory-portal"></a>Azure Active Directory 포털을 사용하여 사용자 지정 도메인 이름 추가

모든 새 Azure AD 테넌트에는 초기 도메인 이름인 * \<도메인 이름>.onmicrosoft.com이*함께 제공됩니다. 초기 도메인 이름은 변경하거나 삭제할 수 없지만 조직의 이름을 추가할 수는 있습니다. 사용자 지정 도메인 이름을 추가하면 *alain\@contoso.com*와 같이 사용자에게 친숙한 사용자 이름을 만들 수 있습니다.

## <a name="before-you-begin"></a>시작하기 전에

사용자 지정 도메인 이름을 추가하려면 도메인 등록기관을 사용하여 도메인 이름을 만드세요. 공인된 도메인 등록 기관은 [ICANN-Accredited Registrars](https://www.icann.org/registrar-reports/accredited-list.html)를 참조하십시오.

## <a name="create-your-directory-in-azure-ad"></a>Azure AD에서 디렉터리 만들기

도메인 이름을 얻은 후에 첫 번째 Azure AD 디렉터리를 만들 수 있습니다. 구독에 대한 **소유자** 역할이 있는 계정을 사용하여 디렉터리용 Azure 포털에 로그인합니다.

[조직의 새 테넌트 만들기](active-directory-access-create-new-tenant.md#create-a-new-tenant-for-your-organization)의 단계에 따라 새 디렉터리를 만듭니다.

>[!IMPORTANT]
>테넌트를 만든 사용자는 자동으로 해당 테넌트에 대한 글로벌 관리자가 됩니다. 글로벌 관리자는 해당 테넌트에 추가 관리자를 추가할 수 있습니다.

구독 역할에 대한 자세한 내용은 [Azure RBAC 역할을](../../role-based-access-control/rbac-and-directory-admin-roles.md#azure-rbac-roles)참조하십시오.

>[!TIP]
> 온-프레미스 Windows Server AD를 Azure AD로 페더레이할 계획이라면 Azure AD Connect 도구를 실행하여 디렉터리를 동기화할 때 **로컬 Active Directory를 사용하여 단일 사인온에 대해 이 도메인을 구성할 계획을** 선택해야 합니다.
>
> 마법사의 **Azure AD 도메인** 단계에서 온-프레미스 디렉터리와 페더레이션하기 위해 선택한 동일한 도메인 이름을 등록해야 합니다. 해당 설정의 모양을 보려면 [페더레이션에 대해 선택한 Azure AD 도메인 확인을](../hybrid/how-to-connect-install-custom.md#verify-the-azure-ad-domain-selected-for-federation)참조하십시오. Azure AD Connect 도구가 없는 경우 여기에서 [다운로드할](https://go.microsoft.com/fwlink/?LinkId=615771)수 있습니다.

## <a name="add-your-custom-domain-name-to-azure-ad"></a>Azure AD에 사용자 지정 도메인 이름 추가

디렉터리를 만든 후에 사용자 지정 도메인 이름을 추가할 수 있습니다.

1. 디렉터리에 대한 글로벌 관리자 계정을 사용하여 [Azure Portal](https://portal.azure.com/)에 로그인합니다.

1. 모든 페이지에서 *Azure Active Directory를* 검색하고 선택합니다. 그런 다음 **사용자 지정 도메인 이름** > **사용자 지정 도메인 추가를**선택합니다.

    ![맞춤 도메인 이름 페이지( 맞춤 도메인 추가 표시)](media/add-custom-domain/add-custom-domain.png)

1. **사용자 지정 도메인 이름에서**조직의 새 이름을 입력합니다.이 예제에서는 *contoso.com.* **도메인 추가**를 선택합니다.

    ![사용자 지정 도메인 이름 페이지( 사용자 지정 도메인 추가 페이지)](media/add-custom-domain/add-custom-domain-blade.png)

    >[!IMPORTANT]
    >이 확장이 제대로 작동하려면 *.com,* *.net*또는 기타 최상위 확장을 포함해야 합니다.

    확인되지 않은 도메인이 추가됩니다. **DNS** 정보가 표시되는 contoso.com 페이지가 나타납니다. 이 정보를 저장합니다. 나중에 DNS를 구성하기 위해 TXT 레코드를 만들어야 합니다.

    ![DNS 항목 정보가 포함된 Contoso 페이지](media/add-custom-domain/contoso-blade-with-dns-info.png)

## <a name="add-your-dns-information-to-the-domain-registrar"></a>DNS 정보를 도메인 등록 기관에 추가

Azure AD에 사용자 지정 도메인 이름을 추가한 후에 해당 도메인 등록 기관으로 돌아가서 복사된 TXT 파일에서 Azure AD DNS 정보를 추가해야 합니다. 도메인에 대해 이 TXT 레코드를 만들면 도메인 이름의 소유권이 확인됩니다.

도메인 등록 기관으로 돌아가 복사된 DNS 정보를 기반으로 도메인에 대한 새 TXT 레코드를 만듭니다. TTL(라이브 시간)을 3600초(60분)로 설정한 다음 레코드를 저장합니다.

>[!IMPORTANT]
>원하는 만큼 많은 도메인 이름을 등록할 수 있습니다. 그러나 각 도메인은 Azure AD에서 자체 TXT 레코드를 가져옵니다. 도메인 등록 기관에서 TXT 파일 정보를 입력할 때주의하십시오. 실수로 잘못되었거나 중복된 정보를 입력하면 TTL이 시간(60분)이 초과될 때까지 기다렸다가 다시 시도해야 합니다.

## <a name="verify-your-custom-domain-name"></a>사용자 지정 도메인 이름 확인

사용자 지정 도메인 이름을 등록한 후 Azure AD에서 유효한지 확인합니다. 도메인 등록 기관에서 Azure AD로 전파하는 것은 즉시 수행되거나 도메인 등록 기관에 따라 며칠이 걸릴 수 있습니다.

맞춤 도메인 이름을 확인하려면 다음 단계를 따르세요.

1. 디렉터리에 대한 글로벌 관리자 계정을 사용하여 [Azure Portal](https://portal.azure.com/)에 로그인합니다.

1. 모든 페이지에서 *Azure Active Directory를* 검색하고 선택한 다음 **사용자 지정 도메인 이름을**선택합니다.

1. **사용자 지정 도메인 이름에서**사용자 지정 도메인 이름을 선택합니다. 이 예제에서는 **contoso.com**을 선택합니다.

    ![Fabrikam - contoso가 강조 표시된 사용자 지정 도메인 이름 페이지](media/add-custom-domain/custom-blade-with-contoso-highlighted.png)

1. **contoso.com** 페이지에서 사용자 지정 도메인이 제대로 등록되어 있고 Azure AD에 유효한지 **확인을** 선택합니다.

    ![DNS 항목 정보가 포함된 Contoso 페이지 및 Verify 단추](media/add-custom-domain/contoso-blade-with-dns-info-verify.png)

사용자 지정 도메인 이름을 확인한 후 확인 TXT 또는 MX 파일을 삭제할 수 있습니다.

## <a name="common-verification-issues"></a>일반적인 인증 문제

Azure AD에서 사용자 지정 도메인 이름을 확인할 수 없는 경우 다음 제한 사항을 시도하세요.

- **1시간 이상 기다렸다가 다시 시도하세요**. Azure AD가 도메인을 확인할 수 있기 전에 DNS 레코드를 전파해야 합니다. 이 프로세스는 한 시간 이상 걸릴 수 있습니다.

- **DNS 레코드가 올바른지 확인합니다.** 도메인 이름 등록 기관 사이트로 돌아갑니다. 항목이 있고 Azure AD에서 제공하는 DNS 항목 정보와 일치하는지 확인합니다.

  등록기관 사이트에서 레코드를 업데이트할 수 없는 경우 항목을 추가하고 올바른지 확인할 수 있는 권한이 있는 사용자와 항목을 공유합니다.

- **도메인 이름이 이미 다른 디렉터리에서 사용되고 있지 않은지 확인합니다.** 도메인 이름은 하나의 디렉터리에서만 확인할 수 있습니다. 다른 디렉터리에서 도메인 이름이 현재 확인된 경우 새 디렉터리에서도 확인할 수 없습니다. 이 중복 문제를 해결하려면 이전 디렉터리에서 도메인 이름을 삭제해야 합니다. 도메인 이름을 삭제하는 방법에 대한 자세한 정보는 [사용자 지정 도메인 이름 관리](../users-groups-roles/domains-manage.md)를 참조하세요.

- **관리되지 않는 Power BI 테넌트가 없는지 확인합니다.** 사용자가 셀프 서비스 등록을 통해 Power BI를 활성화하고 조직의 관리되지 않는 테넌트를 만든 경우 PowerShell을 사용하여 내부 또는 외부 관리자로 관리를 가져와야 합니다. 자세한 내용은 [Azure Active Directory에서 관리자 권한으로 관리되지 않는 디렉터리 인수](../users-groups-roles/domains-admin-takeover.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

- 다른 글로벌 관리자를 디렉터리에 추가합니다. 자세한 내용은 [역할 및 관리자를 할당하는 방법을](active-directory-users-assign-role-azure-portal.md)참조하세요.

- 도메인에 사용자를 추가합니다. 자세한 내용은 [사용자를 추가하거나 삭제하는 방법을](add-users-azure-active-directory.md)참조하세요.

- Azure AD에서 도메인 이름 정보를 관리합니다. 자세한 내용은 [사용자 지정 도메인 이름 관리를](../users-groups-roles/domains-manage.md)참조하십시오.

- Azure Active Directory와 함께 사용하려는 온-프레미스 버전의 Windows Server가 있는 경우 [Azure Active Directory와 온-프레미스 디렉터리 통합](../connect/active-directory-aadconnect.md)을 참조하세요.
