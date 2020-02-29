---
title: Microsoft Graph를 사용 하 여 리소스 관리
titleSuffix: Azure AD B2C
description: 필요한 Graph API 권한이 부여 된 응용 프로그램을 등록 하 여 Microsoft Graph를 통해 Azure AD B2C 리소스를 관리할 준비를 합니다.
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
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/29/2020
ms.locfileid: "78184356"
---
# <a name="manage-azure-ad-b2c-with-microsoft-graph"></a>Microsoft Graph를 사용 하 여 Azure AD B2C 관리

[Microsoft Graph][ms-graph] 를 사용 하면 고객 사용자 계정 및 사용자 지정 정책을 포함 하 여 Azure AD B2C 테 넌 트 내의 많은 리소스를 관리할 수 있습니다. [MICROSOFT GRAPH API][ms-graph-api]를 호출 하는 스크립트나 응용 프로그램을 작성 하 여 다음과 같은 테 넌 트 관리 작업을 자동화할 수 있습니다.

* 기존 사용자 저장소를 Azure AD B2C 테 넌 트로 마이그레이션
* Azure DevOps에서 Azure 파이프라인을 사용 하 여 사용자 지정 정책 배포 및 사용자 지정 정책 키 관리
* 사용자 고유의 페이지에서 사용자 등록을 호스트 하 고 백그라운드에서 Azure AD B2C 디렉터리에 사용자 계정을 만듭니다.
* 응용 프로그램 등록 자동화
* 감사 로그 가져오기

다음 섹션에서는 Microsoft Graph API를 사용 하 여 Azure AD B2C 디렉터리에서 리소스의 관리를 자동화 하는 방법을 준비 하는 방법을 설명 합니다.

## <a name="microsoft-graph-api-interaction-modes"></a>Microsoft Graph API 상호 작용 모드

Azure AD B2C 테 넌 트에서 리소스를 관리 하기 위해 Microsoft Graph API를 사용할 때 사용할 수 있는 두 가지 통신 모드가 있습니다.

* **대화형** -한 번 실행 하는 작업에 적합 합니다. B2C 테 넌 트의 관리자 계정을 사용 하 여 관리 작업을 수행 합니다. 이 모드에서는 관리자가 Microsoft Graph API를 호출 하기 전에 자격 증명을 사용 하 여 로그인 해야 합니다.

* **자동** -예약 또는 연속 실행 작업의 경우이 방법은 관리 작업을 수행 하는 데 필요한 권한을 사용 하 여 구성 하는 서비스 계정을 사용 합니다. 응용 프로그램 및 스크립트가 응용 *프로그램 (클라이언트) ID* 및 OAuth 2.0 클라이언트 자격 증명 부여를 사용 하 여 인증 하는 데 사용 하는 응용 프로그램을 등록 하 여 Azure AD B2C에서 "서비스 계정"을 만듭니다. 이 경우 응용 프로그램은 앞에서 설명한 대화형 방법으로 관리자가 아닌 Microsoft Graph API를 호출 하는 역할을 합니다.

다음 섹션에 표시 된 응용 프로그램 등록을 만들어 **자동화** 된 상호 작용 시나리오를 사용 하도록 설정 합니다.

## <a name="register-management-application"></a>관리 응용 프로그램 등록

스크립트 및 응용 프로그램이 [MICROSOFT GRAPH api][ms-graph-api] 와 상호 작용 하 여 Azure AD B2C 리소스를 관리 하려면 필요한 api 권한을 부여 하는 Azure AD B2C 테 넌 트에서 응용 프로그램 등록을 만들어야 합니다.

[!INCLUDE [active-directory-b2c-appreg-mgmt](../../includes/active-directory-b2c-appreg-mgmt.md)]

### <a name="grant-api-access"></a>API 액세스 권한 부여

다음으로 Microsoft Graph API에 대 한 호출을 통해 테 넌 트 리소스를 조작 하기 위해 등록 된 응용 프로그램에 권한을 부여 합니다.

[!INCLUDE [active-directory-b2c-permissions-directory](../../includes/active-directory-b2c-permissions-directory.md)]

### <a name="create-client-secret"></a>클라이언트 암호 만들기

[!INCLUDE [active-directory-b2c-client-secret](../../includes/active-directory-b2c-client-secret.md)]

이제 Azure AD B2C 테 넌 트에서 사용자를 *만들고*, *읽고*, *업데이트*하 고, *삭제할* 수 있는 권한을 가진 응용 프로그램이 있습니다. 다음 섹션을 계속 진행 하 여 *암호 업데이트* 권한을 추가 합니다.

## <a name="enable-user-delete-and-password-update"></a>사용자 삭제 및 암호 업데이트 사용

*디렉터리 데이터 읽기 및 쓰기* 권한에는 사용자를 삭제 하거나 사용자 계정 암호를 업데이트 하는 기능이 포함 되어 **있지** 않습니다.

응용 프로그램 또는 스크립트가 사용자를 삭제 하거나 암호를 업데이트 해야 하는 경우 *사용자 관리자* 역할을 응용 프로그램에 할당 합니다.

1. [Azure Portal](https://portal.azure.com) 에 로그인 하 고 **디렉터리 + 구독** 필터를 사용 하 여 Azure AD B2C 테 넌 트로 전환 합니다.
1. **Azure AD B2C**를 검색 하 고 선택 합니다.
1. **관리**에서 **역할 및 관리자**를 선택 합니다.
1. **사용자 관리자** 역할을 선택 합니다.
1. **할당 추가**를 선택 합니다.
1. **선택** 텍스트 상자에 이전에 등록 한 응용 프로그램의 이름 (예: *managementapp1*)을 입력 합니다. 검색 결과에 표시 되는 응용 프로그램을 선택 합니다.
1. **추가**를 선택합니다. 권한이 완전히 전파 되는 데 몇 분 정도 걸릴 수 있습니다.

## <a name="next-steps"></a>다음 단계

이제 관리 응용 프로그램을 등록 하 고 필요한 권한을 부여 했으므로 응용 프로그램 및 서비스 (예: Azure Pipelines)에서 자격 증명 및 사용 권한을 사용 하 여 Microsoft Graph API와 상호 작용할 수 있습니다.

* [Microsoft Graph에서 지 원하는 B2C 작업](microsoft-graph-operations.md)
* [Microsoft Graph를 사용 하 여 Azure AD B2C 사용자 계정 관리](manage-user-accounts-graph-api.md)
* [Azure AD reporting API를 사용 하 여 감사 로그 가져오기](view-audit-logs.md#get-audit-logs-with-the-azure-ad-reporting-api)

<!-- LINKS -->
[ms-graph]: https://docs.microsoft.com/graph/
[ms-graph-api]: https://docs.microsoft.com/graph/api/overview
