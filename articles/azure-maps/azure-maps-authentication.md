---
title: Microsoft Azure 맵으로 인증
titleSuffix: Azure Maps
description: 이 문서에서는 Azure Active Directory 및 공유 키 인증에 대해 알아봅니다.
author: anastasia-ms
ms.author: v-stharr
ms.date: 07/27/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: af3f9b4595be5af2477fdbef4e5f0a15224e8a93
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87285835"
---
# <a name="authentication-with-azure-maps"></a>Azure Maps 인증

Azure Maps에서는 요청을 인증 하는 두 가지 방법인 공유 키 인증 및 [Azure Active Directory (AZURE AD)](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis) 인증을 지원 합니다. 이 문서에서는 Azure Maps 서비스의 구현을 안내 하는 데 도움이 되는 두 가지 인증 방법을 설명 합니다.

> [!NOTE]
> Azure Maps와의 보안 통신을 개선 하기 위해 이제 TLS (Transport Layer Security) 1.2를 지원 하 고 TLS 1.0 및 1.1에 대 한 지원을 중단 하 고 있습니다. 현재 TLS 1.x를 사용 하는 경우 tls 1.2 준비 상태를 평가 하 고 [tls 1.0 문제 해결](https://docs.microsoft.com/security/solving-tls1-problem)에 설명 된 테스트를 사용 하 여 마이그레이션 계획을 개발 합니다.

## <a name="shared-key-authentication"></a>공유 키 인증

 Azure Maps 계정이 만들어진 후 기본 및 보조 키가 생성 됩니다. 공유 키 인증을 사용 하 여 Azure Maps를 호출할 때 기본 키를 구독 키로 사용 하는 것이 좋습니다. 공유 키 인증은 Azure Maps 계정에서 생성 된 키를 Azure Maps 서비스에 전달 합니다. Azure Maps 서비스에 대 한 각 요청에 대해 *구독 키* 를 URL에 매개 변수로 추가 합니다. 키 롤링 변경 등의 시나리오에서 보조 키를 사용할 수 있습니다.  

Azure Portal에서 키를 보는 방법에 대 한 자세한 내용은 [인증 관리](https://aka.ms/amauthdetails)를 참조 하세요.

> [!TIP]
> 보안을 위해 기본 키와 보조 키 사이를 회전 하는 것이 좋습니다. 키를 회전하려면 보조 키를 사용하도록 앱을 업데이트하고 배포한 다음, 기본 키 옆에 있는 주기/새로 고침 단추를 눌러 새 기본 키를 생성합니다. 이전 기본 키는 사용할 수 없습니다. 키 회전에 대한 자세한 내용은 [키 회전 및 감사를 사용하여 Azure Key Vault 설정](https://docs.microsoft.com/azure/key-vault/secrets/key-rotation-log-monitoring)을 참조하세요.

## <a name="azure-ad-authentication"></a>Azure AD 인증

Azure 구독은 세분화 된 액세스 제어를 사용할 수 있도록 Azure AD 테 넌 트와 함께 제공 됩니다. Azure Maps는 Azure AD를 사용 하 여 Azure Maps 서비스에 대 한 인증을 제공 합니다. Azure AD는 Azure AD 테 넌 트에 등록 된 사용자 및 응용 프로그램에 대 한 id 기반 인증을 제공 합니다.

Azure Maps는 Azure Maps 계정을 포함하고 있는 Azure 구독과 연결된 Azure AD 테넌트에 대한 **OAuth 2.0** 액세스 토큰을 허용합니다. 또한 Azure Maps는 다음에 대 한 토큰을 허용 합니다.

* Azure AD 사용자
* 사용자가 위임한 권한을 사용 하는 파트너 응용 프로그램
* Azure 리소스에 대한 관리 ID

Azure Maps는 각 Azure Maps 계정에 대해 *고유 식별자(클라이언트 ID)* 를 생성합니다. 이 클라이언트 ID를 추가 매개 변수와 함께 사용 하는 경우 Azure AD에서 토큰을 요청할 수 있습니다.

Azure AD를 구성하고 Azure Maps에 대한 토큰을 요청하는 방법에 대한 자세한 내용은 [Azure Maps의 인증 관리](https://docs.microsoft.com/azure/azure-maps/how-to-manage-authentication)를 참조하세요.

Azure AD를 사용 하 여 인증 하는 방법에 대 한 일반 정보 [는 인증 이란?](https://docs.microsoft.com/azure/active-directory/develop/authentication-scenarios)을 참조 하세요.

### <a name="managed-identities-for-azure-resources-and-azure-maps"></a>Azure 리소스 및 Azure Maps에 대한 관리형 ID

Azure [리소스에 대 한 관리 id](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) 는 azure AD를 사용 하 여 인증할 수 있는 자동으로 관리 되는 응용 프로그램 기반 보안 주체를 azure 서비스에 제공 합니다. RBAC (역할 기반 액세스 제어)를 사용 하면 관리 되는 id 보안 주체에 Azure Maps 서비스에 액세스할 수 있는 권한이 부여 될 수 있습니다. 관리 되는 id의 몇 가지 예는 Azure App Service, Azure Functions 및 Azure Virtual Machines입니다. 관리 id 목록은 [Azure 리소스에 대 한 관리 되는 id](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/services-support-managed-identities)를 참조 하세요.

### <a name="configuring-application-azure-ad-authentication"></a>응용 프로그램 Azure AD 인증 구성

응용 프로그램은 Azure AD에서 제공 하는 하나 이상의 지원 되는 시나리오를 사용 하 여 Azure AD 테 넌 트로 인증 합니다. 각 Azure AD 응용 프로그램 시나리오는 비즈니스 요구에 따라 다양 한 요구 사항을 나타냅니다. 일부 응용 프로그램에는 사용자 로그인 환경이 필요할 수 있으며, 다른 응용 프로그램에는 응용 프로그램 로그인 환경이 필요할 수 있습니다. 자세한 내용은 [인증 흐름 및 응용 프로그램 시나리오](https://docs.microsoft.com/azure/active-directory/develop/authentication-flows-app-scenarios)를 참조 하세요.

응용 프로그램이 액세스 토큰을 받은 후 SDK 및/또는 응용 프로그램은 다른 REST API HTTP 헤더 외에도 다음과 같은 필수 HTTP 헤더 집합을 사용 하 여 HTTPS 요청을 보냅니다.

| 헤더 이름    | 값               |
| :------------- | :------------------ |
| x-ms-client-id | 30d7cc….9f55        |
| 권한 부여  | Bearer eyJ0e….HNIVN |

> [!NOTE]
> `x-ms-client-id`는 Azure Maps 인증 페이지에 표시되는 Azure Maps 계정 기반 GUID입니다.

Azure AD OAuth 전달자 토큰을 사용 하는 Azure Maps 경로 요청의 예는 다음과 같습니다.

```http
GET /route/directions/json?api-version=1.0&query=52.50931,13.42936:52.50274,13.43872
Host: atlas.microsoft.com
x-ms-client-id: 30d7cc….9f55
Authorization: Bearer eyJ0e….HNIVN
```

클라이언트 ID를 보는 방법에 대한 내용은 [인증 세부 정보 보기](https://aka.ms/amauthdetails)를 참조하세요.

## <a name="authorization-with-role-based-access-control"></a>역할 기반 액세스 제어를 사용 하는 권한 부여

Azure Maps는 개별 Azure AD 사용자, 그룹, 응용 프로그램, Azure 리소스 및 Azure 관리 되는 id를 포함 하 여 Azure [역할 기반 액세스 제어](https://docs.microsoft.com/azure/role-based-access-control/overview) 를 위한 모든 보안 주체 유형에 대 한 액세스를 지원 합니다. 보안 주체 유형에는 역할 정의 라고도 하는 권한 집합이 부여 됩니다. 역할 정의는 REST API 작업에 대 한 권한을 제공 합니다. 하나 이상의 Azure Maps 계정에 대 한 액세스를 적용 하는 것을 범위 라고 합니다. 보안 주체, 역할 정의 및 범위를 적용 하는 경우 역할 할당이 만들어집니다. 

다음 섹션에서는 Azure AD 역할 기반 액세스 제어와 Azure Maps 통합의 개념 및 구성 요소에 대해 설명 합니다. Azure Maps 계정을 설정 하는 프로세스의 일부로 Azure AD 디렉터리는 Azure Maps 계정이 있는 Azure 구독에 연결 됩니다. 

Azure RBAC를 구성 하는 경우 보안 주체를 선택 하 여 역할 할당에 적용 합니다. Azure Portal에 역할 할당을 추가 하는 방법에 대 한 자세한 내용은 [Azure 역할 할당 추가 또는 제거](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal)를 참조 하세요.

### <a name="picking-a-role-definition"></a>역할 정의 선택

응용 프로그램 시나리오를 지원 하기 위해 다음 역할 정의 형식이 있습니다.

| Azure 역할 정의       | 설명                                                                                              |
| :-------------------------- | :------------------------------------------------------------------------------------------------------- |
| Azure Maps 데이터 읽기 권한자      | 변경할 수 없는 Azure Maps REST Api에 대 한 액세스를 제공 합니다.                                                       |
| Azure Maps 데이터 기여자 | 변경 가능한 Azure Maps REST Api에 대 한 액세스를 제공 합니다. 가변성는 write 및 delete 작업으로 정의 됩니다. |
| 사용자 지정 역할 정의      | Azure Maps REST Api에 유연 하 게 제한 된 액세스를 사용할 수 있도록 하는 특수 한 역할을 만듭니다.                      |

일부 Azure Maps 서비스에는 Azure Maps REST Api에 대 한 쓰기 또는 삭제 작업을 수행 하기 위해 상승 된 권한이 필요할 수 있습니다. 쓰기 또는 삭제 작업을 제공 하는 서비스에는 Azure Maps 데이터 참가자 역할이 필요 합니다. 다음 표에서는 지정 된 서비스에서 쓰기 또는 삭제 작업을 사용 하는 경우에 적용할 수 있는 데이터 참가자 Azure Maps 서비스에 대해 설명 합니다. 서비스에서 읽기 동작만 사용 되는 경우 데이터 참여자 Azure Maps 대신 Azure Maps 데이터 판독기를 사용할 수 있습니다.

| Azure Maps 서비스 | Azure Maps 역할 정의  |
| :----------------- | :-------------------------- |
| 데이터               | Azure Maps 데이터 기여자 |
| 만든 이            | Azure Maps 데이터 기여자 |
| 공간            | Azure Maps 데이터 기여자 |

RBAC 설정을 보는 방법에 대한 내용은 [Azure Maps에 대한 RBAC 구성 방법](https://aka.ms/amrbac)을 참조하세요.

#### <a name="custom-role-definitions"></a>사용자 지정 역할 정의

응용 프로그램 보안의 한 가지 측면은 최소 권한의 원칙을 적용 하는 것입니다. 이 원칙은 보안 주체가 필요한 액세스 권한만 허용 하 고 추가 액세스 권한이 없는 것을 의미 합니다. 사용자 지정 역할 정의를 만드는 것은 액세스 제어에 추가 세분성이 필요한 사용 사례를 지원할 수 있습니다. 사용자 지정 역할 정의를 만들려면 정의에 포함 하거나 제외할 특정 데이터 작업을 선택할 수 있습니다.

그런 다음 사용자 지정 역할 정의를 모든 보안 주체에 대 한 역할 할당에 사용할 수 있습니다. Azure 사용자 지정 역할 정의에 대해 자세히 알아보려면 [azure 사용자 지정 역할](https://docs.microsoft.com/azure/role-based-access-control/custom-roles)을 참조 하세요.

사용자 지정 역할이 응용 프로그램 보안을 향상 시킬 수 있는 몇 가지 예제 시나리오는 다음과 같습니다.

| 시나리오                                                                                                                                                                                                                 | 사용자 지정 역할 데이터 작업                                                                                                                  |
| :----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | :------------------------------------------------------------------------------------------------------------------------------------------ |
| 기본 맵 타일과 다른 REST Api가 없는 공용 연결 또는 대화형 로그인 웹 페이지입니다.                                                                                                                              | `Microsoft.Maps/accounts/services/render/read`                                                                                              |
| 역방향 지 오 코딩 필요 하 고 다른 REST Api는 없는 응용 프로그램입니다.                                                                                                                                             | `Microsoft.Maps/accounts/services/search/read`                                                                                              |
| Azure Maps Creator 기반 지도 데이터 및 기본 맵 타일 REST Api의 읽기를 요청 하는 보안 주체에 대 한 역할입니다.                                                                                                 | `Microsoft.Maps/accounts/services/data/read`, `Microsoft.Maps/accounts/services/render/read`                                                |
| 작성자 기반 맵 데이터를 읽고, 쓰고, 삭제 해야 하는 보안 주체에 대 한 역할입니다. 이는 지도 데이터 편집기 역할로 정의 될 수 있지만 기본 맵 타일과 같은 다른 REST Api에 대 한 액세스를 허용 하지 않습니다. | `Microsoft.Maps/accounts/services/data/read`, `Microsoft.Maps/accounts/services/data/write`, `Microsoft.Maps/accounts/services/data/delete` |

### <a name="understanding-scope"></a>범위 이해

역할 할당을 만들 때 Azure 리소스 계층 구조 내에서 정의 됩니다. 계층의 맨 위에는 [관리 그룹이](https://docs.microsoft.com/azure/governance/management-groups/overview) 있고, 가장 낮은 것은 Azure Maps 계정과 같은 Azure 리소스입니다.
리소스 그룹에 역할 할당을 할당 하면 그룹의 여러 Azure Maps 계정 또는 리소스에 대 한 액세스를 설정할 수 있습니다.

> [!TIP]
> Microsoft의 일반적인 권장 사항은 동일한 Azure 구독에 존재 하는 **다른 Azure Maps 계정에 대 한 의도** 하지 않은 액세스를 방지 하기 때문에 Azure Maps 계정 범위에 대 한 액세스 권한을 할당 하는 것입니다.

## <a name="next-steps"></a>다음 단계

RBAC에 대 한 자세한 내용은 다음을 참조 하세요.
> [!div class="nextstepaction"]
> [역할 기반 액세스 제어](https://docs.microsoft.com/azure/role-based-access-control/overview)

Azure AD 및 Azure Maps로 응용 프로그램을 인증 하는 방법에 대 한 자세한 내용은 다음을 참조 하세요.
> [!div class="nextstepaction"]
> [Azure Maps의 인증 관리](https://docs.microsoft.com/azure/azure-maps/how-to-manage-authentication)

Azure AD에서 Azure Maps 맵 컨트롤을 인증 하는 방법에 대 한 자세한 내용은 다음을 참조 하세요.
> [!div class="nextstepaction"]
> [Azure Maps 맵 컨트롤 사용](https://aka.ms/amaadmc)
