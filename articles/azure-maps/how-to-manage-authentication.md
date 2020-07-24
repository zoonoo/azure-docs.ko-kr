---
title: 인증 관리
titleSuffix: Azure Maps
description: Microsoft Azure 맵에서 인증을 관리 하려면 Azure Portal을 사용 합니다.
author: anastasia-ms
ms.author: v-stharr
ms.date: 06/12/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 69dda537beda1d1bec4f019e1d5cadd16bdd5b39
ms.sourcegitcommit: 0e8a4671aa3f5a9a54231fea48bcfb432a1e528c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/24/2020
ms.locfileid: "87126859"
---
# <a name="manage-authentication-in-azure-maps"></a>Azure Maps의 인증 관리

Azure Maps 계정을 만든 후에는 Azure Active Directory (Azure AD) 인증 및 공유 키 인증을 지원 하기 위해 클라이언트 ID와 키가 생성 됩니다.

## <a name="view-authentication-details"></a>인증 정보 보기

Azure Maps 계정을 만든 후에는 기본 키와 보조 키가 생성 됩니다. [공유 키 인증을 사용 하 여 Azure Maps를 호출](https://docs.microsoft.com/azure/azure-maps/azure-maps-authentication#shared-key-authentication)하는 경우 기본 키를 구독 키로 사용 하는 것이 좋습니다. 키 롤링 변경 등의 시나리오에서 보조 키를 사용할 수 있습니다. 자세한 내용은 [Azure Maps의 인증](https://aka.ms/amauth)을 참조 하세요.

Azure Portal에서 인증 세부 정보를 볼 수 있습니다. 계정에서 **설정** 메뉴의 **인증**을 선택 합니다.

> [!div class="mx-imgBorder"]
> ![인증 세부 정보](./media/how-to-manage-authentication/how-to-view-auth.png)

## <a name="discover-category-and-scenario"></a>범주 및 시나리오 검색

응용 프로그램 요구에 따라 응용 프로그램 보안을 위한 특정 경로가 있습니다. Azure AD는 광범위 한 인증 흐름을 지 원하는 범주를 정의 합니다. 응용 프로그램에 적합 한 범주를 이해 하려면 [응용 프로그램 범주](https://docs.microsoft.com/azure/active-directory/develop/authentication-flows-app-scenarios#application-categories) 를 참조 하세요.

> [!NOTE]
> 공유 키 인증을 사용 하는 경우에도 범주 및 시나리오를 이해 하면 응용 프로그램을 보호 하는 데 도움이 됩니다.

## <a name="determine-authentication-and-authorization"></a>인증 및 권한 부여 결정

다음 표에서는 Azure Maps의 일반적인 인증 및 권한 부여 시나리오를 간략하게 설명 합니다. 이 표에서는 각 시나리오에서 제공 하는 보호 유형을 비교 하 여 보여 줍니다.

> [!IMPORTANT]
> 프로덕션 응용 프로그램에 대해 RBAC (역할 기반 액세스 제어)를 사용 하 여 Azure Active Directory (Azure AD)를 구현 하는 것이 좋습니다.

| 시나리오                                                                                    | 인증 | 권한 부여 | 개발 활동 | 운영 활동 |
| ------------------------------------------------------------------------------------------- | -------------- | ------------- | ------------------ | ------------------ |
| [신뢰할 수 있는 데몬/비 대화형 클라이언트 응용 프로그램](./how-to-secure-daemon-app.md)        | 공유 키     | N/A           | 중간             | 높음               |
| [신뢰할 수 있는 데몬/비 대화형 클라이언트 응용 프로그램](./how-to-secure-daemon-app.md)        | Azure AD       | 높음          | 낮음                | 중간             |
| [대화형 single sign-on을 사용 하는 웹 단일 페이지 응용 프로그램](./how-to-secure-spa-users.md) | Azure AD       | 높음          | 중간             | 중간             |
| [비 대화형 sign-on을 사용 하는 웹 단일 페이지 응용 프로그램](./how-to-secure-spa-app.md)      | Azure AD       | 높음          | 중간             | 중간             |
| [대화형 single sign-on을 사용 하는 웹 응용 프로그램](./how-to-secure-webapp-users.md)          | Azure AD       | 높음          | 높음               | 중간             |
| [IoT 장치/입력 제한 장치](./how-to-secure-device-code.md)                     | Azure AD       | 높음          | 중간             | 중간             |

이 표의 링크를 통해 각 시나리오에 대 한 자세한 구성 정보를 볼 수 있습니다.

## <a name="view-role-definitions"></a>역할 정의 보기

Azure Maps 사용할 수 있는 RBAC 역할을 보려면 **Access control (IAM)** 로 이동 합니다. **역할**을 선택한 다음 *Azure Maps*로 시작 하는 역할을 검색 합니다. 이러한 Azure Maps 역할은 액세스 권한을 부여할 수 있는 역할입니다.

> [!div class="mx-imgBorder"]
> ![사용 가능한 역할 보기](./media/how-to-manage-authentication/how-to-view-avail-roles.png)

## <a name="view-role-assignments"></a>역할 할당 보기

Azure Maps에 RBAC가 부여 된 사용자 및 앱을 보려면 **Access Control (IAM)** 로 이동 합니다. 여기에서 **역할 할당**을 선택 하 고 **Azure Maps**를 기준으로 필터링 합니다.

> [!div class="mx-imgBorder"]
> ![RBAC가 부여 된 사용자 및 앱 보기](./media/how-to-manage-authentication/how-to-view-amrbac.png)

## <a name="request-tokens-for-azure-maps"></a>Azure Maps에 대한 토큰 요청

Azure AD 토큰 끝점에서 토큰을 요청 합니다. Azure AD 요청에서 다음 세부 정보를 사용 합니다.

| Azure 환경      | Azure AD 토큰 끝점             | Azure 리소스 ID              |
| ---------------------- | ----------------------------------- | ------------------------------ |
| Azure 퍼블릭 클라우드     | `https://login.microsoftonline.com` | `https://atlas.microsoft.com/` |
| Azure Government 클라우드 | `https://login.microsoftonline.us`  | `https://atlas.microsoft.com/` |

사용자 및 서비스 사용자를 위해 Azure AD에서 액세스 토큰을 요청 하는 방법에 대 한 자세한 내용은 [AZURE ad에 대 한 인증 시나리오](https://docs.microsoft.com/azure/active-directory/develop/authentication-scenarios) 및 [시나리오](./how-to-manage-authentication.md#determine-authentication-and-authorization)표에서 특정 시나리오 보기를 참조 하세요.

## <a name="next-steps"></a>다음 단계

자세한 내용은 [AZURE AD 및 Azure Maps 웹 SDK](https://docs.microsoft.com/azure/azure-maps/how-to-use-map-control)를 참조 하세요.

Azure Maps 계정에 대 한 API 사용 메트릭을 찾습니다.
> [!div class="nextstepaction"]
> [사용 메트릭 보기](how-to-view-api-usage.md)

Azure Maps와 Azure AD를 통합 하는 방법을 보여 주는 샘플을 탐색 합니다.

> [!div class="nextstepaction"]
> [Azure AD 인증 샘플](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples)
