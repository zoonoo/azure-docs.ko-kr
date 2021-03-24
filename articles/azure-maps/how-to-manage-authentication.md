---
title: 인증 관리
titleSuffix: Azure Maps
description: Azure Maps 인증에 대해 잘 알고 있어야 합니다. 어떤 방법을 어떤 시나리오에서 가장 잘 작동 하는지 확인 합니다. 포털을 사용 하 여 인증 설정을 보는 방법에 대해 알아봅니다.
author: anastasia-ms
ms.author: v-stharr
ms.date: 06/12/2020
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 955b541bdb4ae38066f1eb4d2f09363ec51be1d2
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/23/2021
ms.locfileid: "104864077"
---
# <a name="manage-authentication-in-azure-maps"></a>Azure Maps의 인증 관리

Azure Maps 계정을 만든 후에는 Azure Active Directory (Azure AD) 인증 및 공유 키 인증을 지원 하기 위해 클라이언트 ID와 키가 생성 됩니다.

## <a name="view-authentication-details"></a>인증 정보 보기

Azure Maps 계정을 만든 후에는 기본 키와 보조 키가 생성 됩니다. [공유 키 인증을 사용 하 여 Azure Maps를 호출](./azure-maps-authentication.md#shared-key-authentication)하는 경우 기본 키를 구독 키로 사용 하는 것이 좋습니다. 키 롤링 변경 등의 시나리오에서 보조 키를 사용할 수 있습니다. 자세한 내용은 [Azure Maps의 인증](./azure-maps-authentication.md)을 참조 하세요.

Azure Portal에서 인증 세부 정보를 볼 수 있습니다. 계정에서 **설정** 메뉴의 **인증** 을 선택 합니다.

> [!div class="mx-imgBorder"]
> ![인증 세부 정보](./media/how-to-manage-authentication/how-to-view-auth.png)

## <a name="discover-category-and-scenario"></a>범주 및 시나리오 검색

응용 프로그램 요구에 따라 응용 프로그램 보안을 위한 특정 경로가 있습니다. Azure AD는 광범위 한 인증 흐름을 지 원하는 범주를 정의 합니다. 응용 프로그램에 적합 한 범주를 이해 하려면 [응용 프로그램 범주](../active-directory/develop/authentication-flows-app-scenarios.md#application-categories) 를 참조 하세요.

> [!NOTE]
> 공유 키 인증을 사용 하는 경우에도 범주 및 시나리오를 이해 하면 응용 프로그램을 보호 하는 데 도움이 됩니다.

## <a name="determine-authentication-and-authorization"></a>인증 및 권한 부여 결정

다음 표에서는 Azure Maps의 일반적인 인증 및 권한 부여 시나리오를 간략하게 설명 합니다. 이 표에서는 각 시나리오에서 제공 하는 보호 유형을 비교 하 여 보여 줍니다.

> [!IMPORTANT]
> 프로덕션 응용 프로그램에 대 한 azure RBAC (역할 기반 액세스 제어)를 사용 하 여 Azure Active Directory (Azure AD)를 구현 하는 것이 좋습니다.

| 시나리오                                                                                    | 인증 | 권한 부여 | 개발 활동 | 운영 활동 |
| ------------------------------------------------------------------------------------------- | -------------- | ------------- | ------------------ | ------------------ |
| [신뢰할 수 있는 데몬/비 대화형 클라이언트 응용 프로그램](./how-to-secure-daemon-app.md)        | 공유 키     | 해당 없음           | 중간             | 높은               |
| [신뢰할 수 있는 데몬/비 대화형 클라이언트 응용 프로그램](./how-to-secure-daemon-app.md)        | Azure AD       | 높음          | 낮음                | 중간             |
| [대화형 single sign-on을 사용 하는 웹 단일 페이지 응용 프로그램](./how-to-secure-spa-users.md) | Azure AD       | 높음          | 중간             | 중간             |
| [비 대화형 sign-on을 사용 하는 웹 단일 페이지 응용 프로그램](./how-to-secure-spa-app.md)      | Azure AD       | 높음          | 중간             | 중간             |
| [대화형 single sign-on을 사용 하는 웹 응용 프로그램](./how-to-secure-webapp-users.md)          | Azure AD       | 높음          | 높음               | 중간             |
| [IoT 장치/입력 제한 장치](./how-to-secure-device-code.md)                     | Azure AD       | 높음          | 중간             | 중간             |

이 표의 링크를 통해 각 시나리오에 대 한 자세한 구성 정보를 볼 수 있습니다.

## <a name="view-role-definitions"></a>역할 정의 보기

Azure Maps 사용할 수 있는 Azure 역할을 보려면 **Access control (IAM)** 로 이동 합니다. **역할** 을 선택한 다음 *Azure Maps* 로 시작 하는 역할을 검색 합니다. 이러한 Azure Maps 역할은 액세스 권한을 부여할 수 있는 역할입니다.

> [!div class="mx-imgBorder"]
> ![사용 가능한 역할 보기](./media/how-to-manage-authentication/how-to-view-avail-roles.png)

## <a name="view-role-assignments"></a>역할 할당 보기

Azure Maps에 대 한 액세스 권한이 부여 된 사용자 및 앱을 보려면 **Access Control (IAM)** 로 이동 합니다. 여기에서 **역할 할당** 을 선택 하 고 **Azure Maps** 를 기준으로 필터링 합니다.

> [!div class="mx-imgBorder"]
> ![액세스 권한이 부여 된 사용자 및 앱 보기](./media/how-to-manage-authentication/how-to-view-amrbac.png)

## <a name="request-tokens-for-azure-maps"></a>Azure Maps에 대한 토큰 요청

Azure AD 토큰 끝점에서 토큰을 요청 합니다. Azure AD 요청에서 다음 세부 정보를 사용 합니다.

| Azure 환경      | Azure AD 토큰 끝점             | Azure 리소스 ID              |
| ---------------------- | ----------------------------------- | ------------------------------ |
| Azure 퍼블릭 클라우드     | `https://login.microsoftonline.com` | `https://atlas.microsoft.com/` |
| Azure Government 클라우드 | `https://login.microsoftonline.us`  | `https://atlas.microsoft.com/` |

사용자 및 서비스 사용자를 위해 Azure AD에서 액세스 토큰을 요청 하는 방법에 대 한 자세한 내용은 [AZURE ad에 대 한 인증 시나리오](../active-directory/develop/authentication-vs-authorization.md) 및 [시나리오](./how-to-manage-authentication.md#determine-authentication-and-authorization)표에서 특정 시나리오 보기를 참조 하세요.

## <a name="manage-and-rotate-shared-keys"></a>공유 키 관리 및 회전

Azure Maps 구독 키는 Azure Maps 계정의 루트 암호와 비슷합니다. 구독 키는 항상 보호 해야 합니다. Azure Key Vault를 사용 하 여 키를 안전 하 게 관리 하 고 회전 합니다. 액세스 키를 다른 사용자에 게 배포 하거나 하드 코딩 하거나 다른 사용자가 액세스할 수 있는 일반 텍스트로 저장 하지 않도록 합니다. 키가 손상 된 것으로 판단 되 면 키를 회전 합니다.

> [!NOTE]
> Azure AD (Azure Active Directory)를 사용 하 여 공유 키 대신 가능한 경우 요청에 권한을 부여 하는 것이 좋습니다. Azure AD는 공유 키를 통해 뛰어난 보안과 사용 편의성을 제공 합니다.

### <a name="manually-rotate-subscription-keys"></a>수동으로 구독 키 회전

Azure Maps 계정을 안전 하 게 유지 하기 위해 구독 키를 정기적으로 회전 하는 것이 좋습니다. 가능 하면 Azure Key Vault를 사용 하 여 액세스 키를 관리 합니다. Key Vault 사용 하지 않는 경우 수동으로 키를 회전 해야 합니다.

키를 회전할 수 있도록 두 개의 구독 키가 할당 됩니다. 두 개의 키가 있으면 응용 프로그램이 프로세스 전체에서 Azure Maps에 대 한 액세스를 유지 관리할 수 있습니다.

Azure Portal에서 Azure Maps 구독 키를 회전 하려면:

1. Azure Maps 계정의 보조 키를 참조 하도록 응용 프로그램 코드를 업데이트 하 고 배포 합니다.
2. [Azure Portal](https://portal.azure.com/)에서 Azure Maps 계정으로 이동 합니다.
3. **설정** 에서 **인증** 을 선택 합니다.
4. Azure Maps 계정에 대 한 기본 키를 다시 생성 하려면 기본 키 옆에 있는 **다시 생성** 단추를 선택 합니다.
5. 응용 프로그램 코드를 업데이트 하 여 새 기본 키를 참조 하 고 배포 합니다.
6. 동일한 방식으로 보조 키를 다시 생성 합니다.

> [!WARNING]
> 모든 애플리케이션에서 키 중 하나만 동시에 사용하는 것이 좋습니다. 일부 위치에서 키 1을 사용 하 고 다른 위치에서 키 2를 사용 하는 경우 일부 응용 프로그램이 액세스 권한을 상실 하지 않아도 키를 회전할 수 없습니다.

## <a name="next-steps"></a>다음 단계

자세한 내용은 [AZURE AD 및 Azure Maps 웹 SDK](./how-to-use-map-control.md)를 참조 하세요.

Azure Maps 계정에 대 한 API 사용 메트릭을 찾습니다.
> [!div class="nextstepaction"]
> [사용 메트릭 보기](how-to-view-api-usage.md)

Azure Maps와 Azure AD를 통합 하는 방법을 보여 주는 샘플을 탐색 합니다.

> [!div class="nextstepaction"]
> [Azure AD 인증 샘플](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples)