---
title: 사용 하지 않도록 설정 된 ACS (Azure Access Control Service) 네임 스페이스 다시 활성화
description: ACS (Azure Access Control Service) 네임 스페이스를 찾아서 사용 하도록 설정 하 고 2019 년 2 월 4 일까 지 사용할 수 있도록 확장을 요청 합니다.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 01/21/2019
ms.author: ryanwi
ms.reviewer: jlu
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9cc038e67e5528a52b0b98ea1698da07e8120242
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/10/2019
ms.locfileid: "74966948"
---
# <a name="how-to-reactivate-disabled-access-control-service-namespaces"></a>방법: 비활성화 된 Access Control Service 네임 스페이스 다시 활성화

11 월 2017에 년 11 월 7 2018 일에 ACS (Azure Active Directory 서비스) (Azure AD) 서비스를 사용 중지 Microsoft Azure Access Control Service 하는 것이 발표 되었습니다.

이후부터 acs 구독 관리자에 대 한 전자 메일을 acs 구독 관리자에 게 전송 합니다 .이 전자 메일에는 2018 년 11 월 7 일 이전에 ACS 사용 중지 시간 12 개월, 9 개월, 6 개월, 3 개월, 1 개월, 2 주, 1 주, 1 일이 만료 됩니다.

2018 년 10 월 3 일, 2018 7 년 11 월 7 일 이전에 마이그레이션을 완료할 수 없는 고객에 게 내선 번호를 제공 하는 전자 메일 및 [블로그 게시물](https://azure.microsoft.com/blog/one-month-retirement-notice-access-control-service/)을 통해 발표 되었습니다. 또한이 공지에는 확장을 요청 하기 위한 지침도 포함 되어 있습니다.

## <a name="why-your-namespace-is-disabled"></a>네임 스페이스를 사용할 수 없는 이유

확장을 옵트인 (opt in) 하지 않은 경우 2018 년 11 월 7 일부 터 ACS 네임 스페이스를 사용 하지 않도록 설정 하기 시작 합니다. 2019 년 2 월 4 일에 대 한 확장을 이미 요청 했어야 합니다. 그렇지 않으면 PowerShell을 통해 네임 스페이스를 사용 하도록 설정할 수 없습니다.

> [!NOTE]
> PowerShell 명령을 실행 하 고 확장을 요청 하려면 구독의 공동 관리자 또는 서비스 관리자 여야 합니다.

## <a name="find-and-enable-your-acs-namespaces"></a>ACS 네임 스페이스 찾기 및 사용

ACS PowerShell을 사용 하 여 모든 ACS 네임 스페이스를 나열 하 고 사용 하지 않도록 설정 된 ACS 네임 스페이스를 다시 활성화할 수 있습니다.

1. ACS PowerShell 다운로드 및 설치:
    1. PowerShell 갤러리로 이동 하 여 [Acs. 네임 스페이스](https://www.powershellgallery.com/packages/Acs.Namespaces/1.0.2)를 다운로드 합니다.
    1. 모듈을 설치 합니다.

        ```powershell
        Install-Module -Name Acs.Namespaces
        ```

    1. 가능한 모든 명령 목록을 가져옵니다.

        ```powershell
        Get-Command -Module Acs.Namespaces
        ```

        특정 명령에 대 한 도움말을 보려면 다음을 실행 합니다.

        ```powershell
        Get-Help [Command-Name] -Full
        ```
    
        여기서 `[Command-Name]`은 ACS 명령의 이름입니다.
1. **연결-AcsAccount** cmdlet을 사용 하 여 ACS에 연결 합니다. 

    명령을 실행 하기 전에 **set-executionpolicy** 를 실행 하 여 실행 정책을 변경 해야 할 수 있습니다.
1. **Get AcsSubscription** cmdlet을 사용 하 여 사용 가능한 Azure 구독을 나열 합니다.
1. **Get AcsNamespace** cmdlet을 사용 하 여 ACS 네임 스페이스를 나열 합니다.
1. `State` `Disabled`되었는지 확인 하 여 네임 스페이스를 사용 하지 않도록 설정 했는지 확인 합니다.

    [네임 스페이스를 사용 하지 않도록 설정 ![확인](./media/howto-reactivate-disabled-acs-namespaces/confirm-disabled-namespace.png)](./media/howto-reactivate-disabled-acs-namespaces/confirm-disabled-namespace.png#lightbox)

    `nslookup {your-namespace}.accesscontrol.windows.net`를 사용 하 여 도메인이 여전히 활성 상태 인지 확인할 수도 있습니다.

1. **Enable-AcsNamespace** cmdlet을 사용 하 여 ACS 네임 스페이스를 사용 하도록 설정 합니다.

    네임 스페이스를 사용 하도록 설정한 후에는 네임 스페이스를 2019 년 2 월 4 일 이전에 다시 사용 하지 않도록 확장을 요청할 수 있습니다. 이 날짜 이후에는 ACS에 대 한 모든 요청이 실패 합니다.

## <a name="request-an-extension"></a>확장 요청

2019 년 1 월 21 일에 시작 하는 새 확장 요청을 수행 합니다.

2019 년 2 월 4 일에 대 한 확장을 요청한 고객에 대 한 네임 스페이스를 사용 하지 않도록 시작 합니다. PowerShell을 통해 네임 스페이스를 다시 사용 하도록 설정할 수 있지만, 48 시간 후에는 네임 스페이스를 다시 사용 하지 않도록 설정할 수 있습니다.

2019 년 3 월 4 일부 터 고객은 더 이상 PowerShell을 통해 네임 스페이스를 다시 사용 하도록 설정할 수 없습니다.

추가 확장은 더 이상 자동으로 승인 되지 않습니다. 마이그레이션하는 데 추가 시간이 필요한 경우 [Azure 지원](https://portal.azure.com/#create/Microsoft.Support) 에 문의 하 여 자세한 마이그레이션 타임 라인을 제공 합니다.

### <a name="to-request-an-extension"></a>확장을 요청 하려면

1. Azure Portal에 로그인 하 고 [새 지원 요청](https://portal.azure.com/#create/Microsoft.Support)을 만듭니다.
1. 다음 예와 같이 새 지원 요청 양식을 작성 합니다.

    | 지원 요청 필드 | Value (Díj) |
    |-----------------------|--------------------|
    | **문제 유형** | `Technical` |
    | **Előfizetés** | 구독으로 설정 |
    | **Szolgáltatás** | `All services` |
    | **Erőforrás** | `General question/Resource not available` |
    | **Probléma típusa** | `ACS to SAS Migration` |
    | **Tárgy** | 문제 설명 |

   ![새 기술 지원 요청의 예를 보여 줍니다.](./media/howto-reactivate-disabled-acs-namespaces/new-technical-support-request.png)

<!--

1. Navigate to your ACS namespace's management portal by going to `https://{your-namespace}.accesscontrol.windows.net`.
1. Select the **Read Terms** button to read the [updated Terms of Use](https://azure.microsoft.com/support/legal/access-control/), which will direct you to a page with the updated Terms of Use.

    [![Select the Read Terms button](./media/howto-reactivate-disabled-acs-namespaces/read-terms-button-expanded.png)](./media/howto-reactivate-disabled-acs-namespaces/read-terms-button-expanded.png#lightbox)

1. Select **Request Extension** on the banner at the top of the page. The button will only be enabled after you read the [updated Terms of Use](https://azure.microsoft.com/support/legal/access-control/).

    [![Select the Request Extension button](./media/howto-reactivate-disabled-acs-namespaces/request-extension-button-expanded.png)](./media/howto-reactivate-disabled-acs-namespaces/request-extension-button-expanded.png#lightbox)

1. After the extension request is registered, the page will refresh with a new banner at the top of the page.

    [![Updated page with refreshed banner](./media/howto-reactivate-disabled-acs-namespaces/updated-banner-expanded.png)](./media/howto-reactivate-disabled-acs-namespaces/updated-banner-expanded.png#lightbox)
-->

## <a name="help-and-support"></a>Súgó és támogatás

- 이 방법에 따라 문제가 발생 한 경우 [Azure 지원](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)에 문의 하세요.
- ACS 사용 중지에 대 한 질문이 나 피드백이 있는 경우 acsfeedback@microsoft.com에서 문의해 주세요.

## <a name="next-steps"></a>Következő lépések

- [방법: Azure Access Control Service에서 마이그레이션](active-directory-acs-migration.md)에서 ACS 사용 중지에 대 한 정보를 검토 합니다.
