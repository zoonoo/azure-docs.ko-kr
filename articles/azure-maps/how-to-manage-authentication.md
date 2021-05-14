---
title: 인증 관리
titleSuffix: Azure Maps
description: Azure Maps 인증에 대해 알아봅니다. 어떤 방식을 어떤 시나리오에서 가장 효과적인지 알아봅니다. 포털을 사용하여 인증 설정을 확인하는 방법에 대해 알아봅니다.
author: anastasia-ms
ms.author: v-stharr
ms.date: 06/12/2020
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 955b541bdb4ae38066f1eb4d2f09363ec51be1d2
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104864077"
---
# <a name="manage-authentication-in-azure-maps"></a>Azure Maps의 인증 관리

Azure Maps 계정을 만들면 Azure AD(Azure Active Directory) 인증 및 공유 키 인증을 지원하기 위한 클라이언트 ID와 키가 생성됩니다.

## <a name="view-authentication-details"></a>인증 정보 보기

Azure Maps 계정을 만들면 기본 키와 보조 키가 생성됩니다. [공유 키 인증을 사용하여 Azure Maps를 호출](./azure-maps-authentication.md#shared-key-authentication)하는 경우 기본 키를 구독 키로 사용하는 것이 좋습니다. 키 롤링 변경 등의 시나리오에서 보조 키를 사용할 수 있습니다. 자세한 내용은 [Azure Maps의 인증](./azure-maps-authentication.md)을 참조하세요.

Azure Portal에서 인증 세부 정보를 확인할 수 있습니다. 계정에서 **설정** 메뉴의 **인증** 을 선택합니다.

> [!div class="mx-imgBorder"]
> ![인증 세부 정보](./media/how-to-manage-authentication/how-to-view-auth.png)

## <a name="discover-category-and-scenario"></a>카테고리 및 시나리오 검색

애플리케이션의 필요에 따라 애플리케이션 보안을 위한 특정 경로가 있습니다. Azure AD는 다양한 인증 흐름을 지원하는 카테고리를 정의합니다. 애플리케이션이 어떤 카테고리에 속하는지 파악하려면 [애플리케이션 카테고리](../active-directory/develop/authentication-flows-app-scenarios.md#application-categories)를 참조하세요.

> [!NOTE]
> 공유 키 인증을 사용하는 경우에도 카테고리 및 시나리오를 이해하면 애플리케이션 보안에 도움이 됩니다.

## <a name="determine-authentication-and-authorization"></a>인증 및 권한 부여 결정

다음 표에서는 Azure Maps의 일반적인 인증 및 권한 부여 시나리오를 간략하게 설명합니다. 이 표에서는 각 시나리오에서 제공하는 보호 유형을 비교하여 보여 줍니다.

> [!IMPORTANT]
> Microsoft에서는 프로덕션 애플리케이션에 Azure RBAC(역할 기반 액세스 제어)를 사용하여 Azure AD(Active Directory)를 구현하는 것을 권장합니다.

| 시나리오                                                                                    | 인증 | 권한 부여 | 개발 활동 | 운영 활동 |
| ------------------------------------------------------------------------------------------- | -------------- | ------------- | ------------------ | ------------------ |
| [신뢰할 수 있는 디먼/비대화형 클라이언트 애플리케이션](./how-to-secure-daemon-app.md)        | 공유 키     | 해당 없음           | 중간             | 높음               |
| [신뢰할 수 있는 디먼/비대화형 클라이언트 애플리케이션](./how-to-secure-daemon-app.md)        | Azure AD       | 높음          | 낮음                | 중간             |
| [대화형 Single-Sign-On을 사용하는 웹 단일 페이지 애플리케이션](./how-to-secure-spa-users.md) | Azure AD       | 높음          | 중간             | 중간             |
| [비대화형 Sign-On을 사용하는 웹 단일 페이지 애플리케이션](./how-to-secure-spa-app.md)      | Azure AD       | 높음          | 중간             | 중간             |
| [대화형 Single Sign-On을 사용하는 웹 애플리케이션](./how-to-secure-webapp-users.md)          | Azure AD       | 높음          | 높음               | 중간             |
| [IoT 디바이스/입력 제한 디바이스](./how-to-secure-device-code.md)                     | Azure AD       | 높음          | 중간             | 중간             |

이 표의 링크를 통해 각 시나리오에 대한 자세한 구성 정보를 볼 수 있습니다.

## <a name="view-role-definitions"></a>역할 정의 보기

Azure Maps에서 사용할 수 있는 Azure 역할을 보려면 **Access Control(IAM)** 로 이동합니다. **역할** 을 선택한 다음 *Azure Maps* 로 시작하는 역할을 검색합니다. 이 Azure Maps 역할에 액세스 권한을 부여할 수 있습니다.

> [!div class="mx-imgBorder"]
> ![사용 가능한 역할 보기](./media/how-to-manage-authentication/how-to-view-avail-roles.png)

## <a name="view-role-assignments"></a>역할 할당 보기

Azure Maps에 대한 액세스 권한이 부여된 사용자 및 앱을 보려면 **Access Control(IAM)** 로 이동합니다. 여기에서 **역할 할당** 을 선택하고 **Azure Maps** 를 기준으로 필터링합니다.

> [!div class="mx-imgBorder"]
> ![액세스 권한이 부여된 사용자 및 앱 보기](./media/how-to-manage-authentication/how-to-view-amrbac.png)

## <a name="request-tokens-for-azure-maps"></a>Azure Maps에 대한 토큰 요청

Azure AD 토큰 엔드포인트에서 토큰을 요청합니다. Azure AD 요청에서 다음 세부 정보를 사용합니다.

| Azure 환경      | Azure AD 토큰 엔드포인트             | Azure 리소스 ID              |
| ---------------------- | ----------------------------------- | ------------------------------ |
| Azure 퍼블릭 클라우드     | `https://login.microsoftonline.com` | `https://atlas.microsoft.com/` |
| Azure Government 클라우드 | `https://login.microsoftonline.us`  | `https://atlas.microsoft.com/` |

사용자 및 서비스 주체를 위해 Azure AD에서 액세스 토큰을 요청하는 방법에 대한 자세한 내용은 [Azure AD 인증 시나리오](../active-directory/develop/authentication-vs-authorization.md) 및 [시나리오](./how-to-manage-authentication.md#determine-authentication-and-authorization)표의 특정 시나리오를 참조하세요.

## <a name="manage-and-rotate-shared-keys"></a>공유 키 관리 및 회전

Azure Maps 구독 키는 Azure Maps 계정의 루트 암호와 비슷합니다. 항상 구독 키를 보호해야 합니다. Azure Key Vault를 사용하여 키를 안전하게 관리하고 회전합니다. 액세스 키를 다른 사용자에게 배포하거나 하드 코딩하거나 다른 사용자가 액세스할 수 있는 일반 텍스트로 저장하지 않도록 합니다. 키가 손상되었다고 생각되면 키를 교체하세요.

> [!NOTE]
> Microsoft는 가능하면 공유 키 대신 Azure AD(Azure Active Directory)를 사용하여 요청에 권한을 부여하는 것이 좋습니다. Azure AD는 공유 키를 통해 뛰어난 보안과 사용 편의성을 제공합니다.

### <a name="manually-rotate-subscription-keys"></a>수동으로 구독 키 회전

Microsoft에서는 Azure Maps 계정의 보안을 유지하기 위해 구독 키를 정기적으로 회전하는 것을 권장합니다. 가급적 Azure Key Vault를 사용하여 액세스 키를 관리합니다. Key Vault를 사용하지 않는 경우 수동으로 키를 회전해야 합니다.

키를 회전할 수 있도록 두 개의 구독 키가 할당됩니다. 두 개의 키가 있으면 애플리케이션이 프로세스 전체에서 Azure Maps에 대한 액세스 권한을 유지할 수 있습니다.

Azure Portal에서 Azure Maps 구독 키를 회전하려면 다음을 수행합니다.

1. Azure Maps 계정의 보조 키를 참조하도록 애플리케이션 코드를 업데이트하고 배포합니다.
2. [Azure Portal](https://portal.azure.com/)에서 Azure Maps 계정으로 이동합니다.
3. **설정** 에서 **인증** 을 선택합니다.
4. Azure Maps 계정에 대한 기본 키를 다시 생성하려면 기본 키 옆에 있는 **다시 생성** 단추를 선택합니다.
5. 애플리케이션 코드를 업데이트하여 새 기본 키를 참조하고 배포합니다.
6. 같은 방식으로 보조 키를 다시 생성합니다.

> [!WARNING]
> 모든 애플리케이션에서 키 중 하나만 동시에 사용하는 것이 좋습니다. 어떤 경우에는 키 1을 사용하고, 다른 경우에는 키 2를 사용하면 어떤 애플리케이션이 액세스 권한을 상실해야만 키를 순환할 수 있게 됩니다.

## <a name="next-steps"></a>다음 단계

자세한 내용은 [Azure AD 및 Azure Maps 웹 SDK](./how-to-use-map-control.md)를 참조하세요.

Azure Maps 계정에 대한 API 사용 현황 메트릭을 확인하는 방법을 알아봅니다.
> [!div class="nextstepaction"]
> [사용 메트릭 보기](how-to-view-api-usage.md)

Azure Maps와 Azure AD를 통합하는 방법을 보여 주는 샘플을 살펴보세요.

> [!div class="nextstepaction"]
> [Azure AD 인증 샘플](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples)