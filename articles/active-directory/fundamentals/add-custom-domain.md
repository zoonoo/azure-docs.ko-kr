---
title: 사용자 지정 도메인 추가 - Azure Active Directory | Microsoft Docs
description: Azure Active Directory를 사용하여 사용자 지정 도메인을 추가하는 방법에 대한 지침입니다.
services: active-directory
author: ajburnle
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: how-to
ms.date: 10/25/2019
ms.author: ajburnle
ms.reviewer: elkuzmen
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 28847ece3c08e93d14d381b35fc93fb16dad95a1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "95973901"
---
# <a name="add-your-custom-domain-name-using-the-azure-active-directory-portal"></a>Azure Active Directory 포털을 사용하여 사용자 지정 도메인 이름 추가

모든 새로운 Azure AD 테넌트는 *\<domainname>.onmicrosoft.com* 이라는 초기 도메인 이름으로 제공됩니다. 초기 도메인 이름을 변경하거나 삭제할 수 없지만 조직의 이름을 추가할 수는 있습니다. 사용자 지정 도메인 이름을 추가하면 *alain\@contoso.com* 처럼 사용자에게 친숙한 사용자 이름을 만들 수 있습니다.

## <a name="before-you-begin"></a>시작하기 전에

사용자 지정 도메인 이름을 추가하기 전에 먼저 도메인 등록 기관을 사용하여 도메인 이름을 생성합니다. 공인된 도메인 등록 기관은 [ICANN-Accredited Registrars](https://www.icann.org/registrar-reports/accredited-list.html)를 참조하십시오.

## <a name="create-your-directory-in-azure-ad"></a>Azure AD에서 디렉터리 생성

도메인 이름을 얻은 후에 첫 번째 Azure AD 디렉터리를 만들 수 있습니다. 구독에 대한 **소유자** 역할이 있는 계정을 사용하여 디렉터리의 Azure Portal에 로그인합니다.

[조직의 새 테넌트 만들기](active-directory-access-create-new-tenant.md#create-a-new-tenant-for-your-organization)의 단계에 따라 새 디렉터리를 만듭니다.

>[!IMPORTANT]
>테넌트를 생성된 사용자는 자동으로 해당 테넌트에 대한 글로벌 관리자가 됩니다. 글로벌 관리자는 해당 테넌트에 추가 관리자를 추가할 수 있습니다.

구독 역할에 대한 자세한 내용은 [Azure 역할](../../role-based-access-control/rbac-and-directory-admin-roles.md#azure-roles)을 참조하세요.

>[!TIP]
> 온-프레미스 Windows Server AD를 Azure AD와 페더레이션하려는 경우에는 Azure AD Connect 도구를 실행하여 디렉터리를 동기화할 때 **내 로컬 Active Directory를 사용하여 이 도메인을 Single Sign-On용으로 구성할 계획입니다** 를 선택해야 합니다.
>
> 마법사의 **Azure AD 도메인** 단계에서 온-프레미스 디렉터리와 페더레이션하기 위해 선택한 동일한 도메인 이름을 등록해야 합니다. 설치 프로그램의 모양을 확인하려면 [페더레이션을 위해 선택한 Azure AD 도메인 확인](../hybrid/how-to-connect-install-custom.md#verify-the-azure-ad-domain-selected-for-federation)을 참조하세요. Azure AD Connect 도구가 없는 경우 [여기에서 다운로드](https://go.microsoft.com/fwlink/?LinkId=615771)할 수 있습니다.

## <a name="add-your-custom-domain-name-to-azure-ad"></a>Azure AD에 사용자 지정 도메인 이름 추가

디렉터리를 생성된 후에 사용자 지정 도메인 이름을 추가할 수 있습니다.

1. 디렉터리에 대한 글로벌 관리자 계정을 사용하여 [Azure Portal](https://portal.azure.com/)에 로그인합니다.

1. 모든 페이지에서 *Azure Active Directory* 를 검색하고 선택합니다. 그런 다음 **사용자 지정 도메인 이름** > **사용자 지정 도메인 추가** 를 선택합니다.

    ![사용자 지정 도메인 추가 옵션이 표시된 사용자 지정 도메인 이름 페이지](media/add-custom-domain/add-custom-domain.png)

1. **사용자 지정 도메인 이름** 에 조직의 새 이름(이 예제에서는 *contoso.com*)을 입력합니다. **도메인 추가** 를 선택합니다.

    ![사용자 지정 도메인 추가 페이지가 표시된 사용자 지정 도메인 이름 페이지](media/add-custom-domain/add-custom-domain-blade.png)

    >[!IMPORTANT]
    >이 기능이 제대로 작동하려면 *.com*, *.net*, 또는 기타 최상위 확장명을 포함해야 합니다.

    확인되지 않은 도메인이 추가됩니다. DNS 정보를 보여 주는 **contoso.com** 페이지가 나타납니다. 이 정보를 저장합니다. 나중에 DNS를 구성하기 위해 TXT 레코드를 만드는 데 필요합니다.

    ![DNS 항목 정보가 포함된 Contoso 페이지](media/add-custom-domain/contoso-blade-with-dns-info.png)

## <a name="add-your-dns-information-to-the-domain-registrar"></a>도메인 등록 기관에 DNS 정보 추가

Azure AD에 사용자 지정 도메인 이름을 추가한 후에 해당 도메인 등록 기관으로 돌아가서 복사된 TXT 파일에서 Azure AD DNS 정보를 추가해야 합니다. 도메인에 대해 이 TXT 레코드를 만들면 해당 도메인 이름의 소유권을 확인합니다.

도메인 등록자로 돌아가서 복사한 DNS 정보에 따라 도메인에 대한 새 TXT 레코드를 생성합니다. TTL(Time to Live)을 3600초(60분)로 설정하고 레코드를 저장합니다.

>[!IMPORTANT]
>원하는 만큼 많은 도메인 이름을 등록할 수 있습니다. 그러나 각 도메인은 Azure AD에서 자체 TXT 레코드를 가져옵니다. 도메인 등록 기관에 TXT 파일 정보를 입력할 때 주의해야 합니다. 실수로 잘못 입력하거나 중복된 정보를 입력한 경우 다시 시도하려면 TTL 시간이 종료될 때까지(60분) 기다려야 합니다.

## <a name="verify-your-custom-domain-name"></a>사용자 지정 도메인 이름 확인

사용자 지정 도메인 이름을 등록한 후에 Azure AD에서 해당 도메인 이름이 유효한지 확인해야 합니다. 도메인 등록 기관에서 Azure AD로의 전파는 즉시 수행되거나 도메인 등록 기관에 따라 며칠이 걸릴 수 있습니다.

사용자 지정 도메인 이름을 확인하려면 다음 단계를 수행하세요.

1. 디렉터리에 대한 글로벌 관리자 계정을 사용하여 [Azure Portal](https://portal.azure.com/)에 로그인합니다.

1. 모든 페이지에서 *Azure Active Directory* 를 검색하여 선택한 다음 **사용자 지정 도메인 이름** 을 선택합니다.

1. **사용자 지정 도메인 이름** 에서 사용자 지정 도메인 이름을 선택합니다. 이 예제에서는 **contoso.com** 을 선택합니다.

    ![Fabrikam - contoso가 강조 표시된 사용자 지정 도메인 이름 페이지](media/add-custom-domain/custom-blade-with-contoso-highlighted.png)

1. **Contoso.com** 페이지에서 **확인** 을 선택하여 사용자 지정 도메인이 제대로 등록되고 Azure AD에 대해 유효한지 확인합니다.

    ![DNS 항목 정보가 포함된 Contoso 페이지 및 Verify 단추](media/add-custom-domain/contoso-blade-with-dns-info-verify.png)

사용자 지정 도메인 이름을 확인한 후 확인 TXT 또는 MX 파일을 삭제할 수 있습니다.

## <a name="common-verification-issues"></a>일반적인 인증 문제

Azure AD에서 사용자 지정 도메인 이름을 확인할 수 없는 경우 다음 제한 사항을 시도하세요.

- **1시간 이상 기다렸다가 다시 시도하세요**. Azure AD가 도메인을 확인할 수 있기 전에 DNS 레코드를 전파해야 합니다. 이 프로세스는 한 시간 이상 걸릴 수 있습니다.

- **DNS 레코드가 올바른지 확인합니다.** 도메인 이름 등록 사이트로 돌아갑니다. 항목이 있고 Azure AD에서 제공하는 DNS 항목 정보와 일치하는지 확인합니다.

  등록자 사이트의 코드를 업데이트할 수는 경우 항목을 추가하고 그것이 정확한지 확인하는 데 필요한 권한이 있는 사용자와 해당 항목을 공유해야 합니다.

- **도메인 이름이 이미 다른 디렉터리에서 사용되고 있지 않은지 확인합니다.** 도메인 이름은 하나의 디렉터리에서만 확인할 수 있습니다. 즉, 도메인 이름이 현재 다른 디렉터리에서 확인되면 그 도메인 이름은 새 디렉터리에서도 확인할 수 없습니다. 이 중복 문제를 해결하려면 이전 디렉터리에서 도메인 이름을 삭제해야 합니다. 도메인 이름을 삭제하는 방법에 대한 자세한 정보는 [사용자 지정 도메인 이름 관리](../enterprise-users/domains-manage.md)를 참조하세요.

- **관리되지 않는 Power BI 테넌트가 없는지 확인합니다.** 사용자가 셀프 서비스 등록을 통해 Power BI를 활성화하고 조직의 관리되지 않는 테넌트를 만든 경우 PowerShell을 사용하여 내부 또는 외부 관리자로 관리를 가져와야 합니다. 자세한 내용은 [Azure Active Directory에서 관리자 권한으로 관리되지 않는 디렉터리 인수](../enterprise-users/domains-admin-takeover.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

- 다른 글로벌 관리자를 디렉터리에 추가합니다. 자세한 정보는 [역할 및 관리자를 할당하는 방법](active-directory-users-assign-role-azure-portal.md)을 참조하세요.

- 도메인에 사용자를 추가합니다. 자세한 내용은 [사용자 추가 또는 삭제 방법](add-users-azure-active-directory.md)을 참조하세요.

- Azure AD에서 도메인 이름 정보를 관리합니다. 자세한 내용은 [사용자 지정 도메인 이름 관리](../enterprise-users/domains-manage.md)를 참조하세요.

- Azure Active Directory와 함께 사용하려는 온-프레미스 버전의 Windows Server가 있는 경우 [Azure Active Directory와 온-프레미스 디렉터리 통합](../hybrid/whatis-hybrid-identity.md)을 참조하세요.