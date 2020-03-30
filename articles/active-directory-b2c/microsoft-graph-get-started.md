---
title: 마이크로소프트 그래프로 리소스 관리
titleSuffix: Azure AD B2C
description: 필요한 그래프 API 권한이 부여된 응용 프로그램을 등록하여 Microsoft 그래프를 통해 Azure AD B2C 리소스를 관리할 준비를 합니다.
services: B2C
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/14/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 32117d4bfcf0c0af94eced095b94ab0c1b6f88af
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78184356"
---
# <a name="manage-azure-ad-b2c-with-microsoft-graph"></a>마이크로소프트 그래프로 Azure AD B2C 관리

[Microsoft 그래프를][ms-graph] 사용하면 고객 사용자 계정 및 사용자 지정 정책을 포함하여 Azure AD B2C 테넌트 내의 많은 리소스를 관리할 수 있습니다. [Microsoft Graph API를][ms-graph-api]호출하는 스크립트 또는 응용 프로그램을 작성하여 다음과 같은 테넌트 관리 작업을 자동화할 수 있습니다.

* 기존 사용자 저장소를 Azure AD B2C 테넌트로 마이그레이션
* Azure DevOps에서 Azure 파이프라인을 사용하는 사용자 지정 정책을 배포하고 사용자 지정 정책 키를 관리합니다.
* 자신의 페이지에서 사용자 등록을 호스팅하고 Azure AD B2C 디렉터리에서 백그라운드에서 사용자 계정을 만듭니다.
* 응용 프로그램 등록 자동화
* 감사 로그 구하기

다음 섹션에서는 Microsoft 그래프 API를 사용하여 Azure AD B2C 디렉터리에서 리소스 관리를 자동화할 준비를 하는 데 도움이 됩니다.

## <a name="microsoft-graph-api-interaction-modes"></a>마이크로소프트 그래프 API 상호 작용 모드

Azure AD B2C 테넌트의 리소스를 관리하기 위해 Microsoft 그래프 API로 작업할 때 사용할 수 있는 두 가지 통신 모드가 있습니다.

* **대화형** - 한 번 실행하는 작업에 적합하며 B2C 테넌트의 관리자 계정을 사용하여 관리 작업을 수행합니다. 이 모드를 사용하려면 관리자가 Microsoft 그래프 API를 호출하기 전에 자격 증명을 사용하여 로그인해야 합니다.

* **자동화** - 예약되거나 지속적으로 실행되는 작업의 경우 이 메서드는 관리 작업을 수행하는 데 필요한 권한으로 구성하는 서비스 계정을 사용합니다. 응용 프로그램 및 스크립트가 *응용 프로그램(클라이언트) ID및* OAuth 2.0 클라이언트 자격 증명 부여를 사용하여 인증하는 데 사용하는 응용 프로그램을 등록하여 Azure AD B2C에서 "서비스 계정"을 만듭니다. 이 경우 응용 프로그램은 앞에서 설명한 대화형 메서드와 같이 관리자 사용자가 아니라 Microsoft Graph API를 호출하는 자체 역할을 합니다.

다음 섹션에 표시된 응용 프로그램 등록을 만들어 **자동** 상호 작용 시나리오를 사용하도록 설정합니다.

## <a name="register-management-application"></a>등록 관리 응용 프로그램

스크립트 및 응용 프로그램이 [Microsoft 그래프 API와][ms-graph-api] 상호 작용하여 Azure AD B2C 리소스를 관리하려면 필요한 API 권한을 부여하는 Azure AD B2C 테넌트에서 응용 프로그램 등록을 만들어야 합니다.

[!INCLUDE [active-directory-b2c-appreg-mgmt](../../includes/active-directory-b2c-appreg-mgmt.md)]

### <a name="grant-api-access"></a>API 액세스 권한 부여

다음으로 등록된 응용 프로그램 권한을 부여하여 Microsoft Graph API를 호출하여 테넌트 리소스를 조작합니다.

[!INCLUDE [active-directory-b2c-permissions-directory](../../includes/active-directory-b2c-permissions-directory.md)]

### <a name="create-client-secret"></a>클라이언트 비밀 만들기

[!INCLUDE [active-directory-b2c-client-secret](../../includes/active-directory-b2c-client-secret.md)]

이제 Azure AD B2C 테넌트에서 사용자를 *만들고,* *읽고,* *업데이트하고,* *삭제할* 수 있는 권한이 있는 응용 프로그램이 생겼습니다. *암호 업데이트* 권한을 추가하려면 다음 섹션으로 계속 이동합니다.

## <a name="enable-user-delete-and-password-update"></a>사용자 삭제 및 암호 업데이트 사용

*읽기 및 쓰기 디렉터리 데이터* 권한에는 사용자를 삭제하거나 사용자 계정 암호를 업데이트하는 기능이 포함되지 **않습니다.**

응용 프로그램 또는 스크립트에서 사용자를 삭제하거나 암호를 업데이트해야 하는 경우 응용 프로그램에 *사용자 관리자* 역할을 할당합니다.

1. [Azure 포털에](https://portal.azure.com) 로그인하고 **디렉터리 + 구독** 필터를 사용하여 Azure AD B2C 테넌트로 전환합니다.
1. **Azure AD B2C를**검색하고 선택합니다.
1. **에서 관리**에서 역할 **및 관리자를**선택합니다.
1. 사용자 **관리자** 역할을 선택합니다.
1. **과제 추가를 선택합니다.**
1. 텍스트 **선택** 상자에 이전에 등록한 응용 프로그램의 이름을 입력합니다(예: *managementapp1.* 검색 결과에 표시될 때 응용 프로그램을 선택합니다.
1. **추가**를 선택합니다. 사용 권한이 완전히 전파되는 데 몇 분 정도 걸릴 수 있습니다.

## <a name="next-steps"></a>다음 단계

관리 응용 프로그램을 등록하고 필요한 권한을 부여한 이제 응용 프로그램 및 서비스(예: Azure 파이프라인)는 자격 증명 및 권한을 사용하여 Microsoft Graph API와 상호 작용할 수 있습니다.

* [마이크로소프트 그래프에서 지원하는 B2C 작업](microsoft-graph-operations.md)
* [마이크로 소프트 그래프와 Azure AD B2C 사용자 계정 관리](manage-user-accounts-graph-api.md)
* [Azure AD 보고 API를 통해 감사 로그 받기](view-audit-logs.md#get-audit-logs-with-the-azure-ad-reporting-api)

<!-- LINKS -->
[ms-graph]: https://docs.microsoft.com/graph/
[ms-graph-api]: https://docs.microsoft.com/graph/api/overview
