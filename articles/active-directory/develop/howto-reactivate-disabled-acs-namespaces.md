---
title: 사용할 수 없는 Azure Access Control Service 네임스페이스를 다시 활성화하는 방법
description: ACS(Access Control Service)를 검색하여 사용하도록 설정하고 2019년 2월 4일까지 사용 상태를 유지하는 연장 요청 방법을 알아봅니다.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/07/2018
ms.author: celested
ms.reviewer: jlu
ms.custom: aaddev
ms.openlocfilehash: 298afda7549690a9ea0314bff63a714be50a33b9
ms.sourcegitcommit: 00dd50f9528ff6a049a3c5f4abb2f691bf0b355a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/05/2018
ms.locfileid: "51019878"
---
# <a name="how-to-reactivate-disabled-access-control-service-namespaces"></a>방법: 비활성화된 Access Control Service 네임스페이스 다시 활성화

2017년 11월에 Microsoft Azure AD(Azure Active Directory) 서비스의 하나인 Azure ACS(Access Control Service)는 2018년 11월 7일부터 사용 중지된다고 발표했습니다.

이후 사용 중지일인 2018년 11월 7일로부터 12개월, 9개월, 6개월, 3개월, 1개월, 2주, 1주 및 1일 전에 ACS 사용 중지에 관한 ACS 구독 관리자 이메일로 여러 차례 이메일을 보냈습니다.

2018년 10월 3일에 (이메일과 [블로그 게시물](https://azure.microsoft.com/blog/one-month-retirement-notice-access-control-service/)을 통해) 2018년 11월 7일까지 마이그레이션을 완료할 수 없는 고객에게 연장을 제공한다고 발표했습니다. 또한 이 공지에는 연장 요청을 위한 지침이 포함되었습니다.

## <a name="why-your-namespace-is-disabled"></a>네임스페이스를 사용할 수 없는 이유

연장을 신청하지 않은 경우 2018년 11월 7일에 ACS 네임스페이스를 사용하지 않도록 설정하기 시작했습니다. 커뮤니케이션을 받지 못했고 2019년 2월 4일까지 연장하고자 한다면 다음 섹션의 지침에 따르세요.

> [!NOTE]
> PowerShell 명령을 실행하고 연장을 요청하려면 구독의 관리자여야 합니다.

## <a name="find-and-enable-your-acs-namespaces"></a>ACS 네임스페이스 찾기 및 사용하도록 설정

ACS 네임스페이스를 사용하여 모든 ACS 네임스페이스를 나열하고 사용하지 않게 설정된 항목을 비활성화할 수 있습니다.

1. ACS PowerShell 다운로드 및 설치:
    1. PowerShell 갤러리로 이동하여 [Acs.Namespaces](https://www.powershellgallery.com/packages/Acs.Namespaces/1.0.2)를 다운로드합니다.
    1. 모듈 설치:

        ```powershell
        Install-Module -Name Acs.Namespaces
        ```

    1. 가능한 모든 명령 목록 가져오기:

        ```powershell
        Get-Command -Module Acs.Namespaces
        ```

        특정 명령에 대한 도움말을 보려면 다음을 실행합니다.

        ```
        Get-Help [Command-Name] -Full
        ```
    
        여기서 `[Command-Name]`은 ACS 명령의 이름입니다.
1. **Get-AcsSubscription** cmdlet을 사용하여 사용 가능한 Azure 구독을 나열합니다.
1. **Get-AcsNamespace** cmdlet을 사용하여 ACS 네임스페이스를 나열합니다.
1. `State`가 `Disabled`인지 확인하여 네임스페이스가 사용하도록 설정되었는지 확인합니다.

    [![네임스페이스가 사용하도록 설정되었는지 확인](./media/howto-reactivate-disabled-acs-namespaces/confirm-disabled-namespace.png)](./media/howto-reactivate-disabled-acs-namespaces/confirm-disabled-namespace.png#lightbox)

    `nslookup {your-namespace}.accesscontrol.windows.net`을 사용하여 도메인이 여전히 활성화된 상태인지 확인할 수도 있습니다.

1. **Get-AcsNamespace** cmdlet을 사용하여 ACS 네임스페이스를 사용하도록 설정합니다.

    네임스페이스를 사용하도록 설정한 후에는 네임스페이스가 2019년 2월 4일 이전에는 다시 사용 중지되지 않도록 연장을 요청할 수 있습니다. 이 날짜가 지나면 모든 ACS 요청이 실패합니다.

## <a name="request-an-extension"></a>연장 요청

1. `https://{your-namespace}.accesscontrol.windows.net`으로 이동하여 ACS 네임스페이스의 관리 포털로 이동합니다.
1. **사용 약관 읽기** 단추를 선택하여 [사용 약관 업데이트](https://azure.microsoft.com/support/legal/access-control/)를 읽습니다. 업데이트된 사용 약관이 있는 페이지로 이동하게 됩니다.

    [![사용 약관 읽기 단추 선택](./media/howto-reactivate-disabled-acs-namespaces/read-terms-button-expanded.png)](./media/howto-reactivate-disabled-acs-namespaces/read-terms-button-expanded.png#lightbox)

1. 페이지 상단의 **연장 요청**을 선택합니다. [사용 약관 업데이트](https://azure.microsoft.com/support/legal/access-control/)를 읽어야만 단추를 사용할 수 있습니다.

    [![연장 요청 단추 선택](./media/howto-reactivate-disabled-acs-namespaces/request-extension-button-expanded.png)](./media/howto-reactivate-disabled-acs-namespaces/request-extension-button-expanded.png#lightbox)

1. 연장 요청이 등록되면 페이지가 새로 고쳐지고 페이지 맨 위에 새 배너가 표시됩니다.

    [![새로 고친 배너로 페이지 업데이트](./media/howto-reactivate-disabled-acs-namespaces/updated-banner-expanded.png)](./media/howto-reactivate-disabled-acs-namespaces/updated-banner-expanded.png#lightbox)

## <a name="help-and-support"></a>도움말 및 지원

- 이 방법을 수행한 후 문제가 발생하면 [Azure 지원 팀](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)에 문의하세요.
- ACS 사용 중지에 대한 질문이나 의견은 acsfeedback@microsoft.com에 문의하세요.

## <a name="next-steps"></a>다음 단계

- [방법: Azure Access Control Service에서 마이그레이션](active-directory-acs-migration.md)에서 ACS 사용 중지에 대한 정보를 검토합니다.
