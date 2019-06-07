---
title: 웹 애플리케이션 추가 - Azure Active Directory B2C | Microsoft Docs
description: Active Directory B2C 테넌트에 웹 애플리케이션을 추가하는 방법을 알아봅니다.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.author: marsma
ms.date: 04/16/2019
ms.custom: mvc
ms.topic: conceptual
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: a1d098550c6fb733e088f8ad211d29f48f55d2d6
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/04/2019
ms.locfileid: "66511713"
---
# <a name="add-a-web-api-application-to-your-azure-active-directory-b2c-tenant"></a>Azure Active Directory B2C 테넌트에 웹 API 애플리케이션 추가

 동의 하 고 액세스 토큰을 제공 하는 클라이언트 응용 프로그램에서 요청에 응답할 수 있도록 테 넌 트의 웹 API 리소스를 등록 합니다. 이 문서에서는 Azure Active Directory (Azure AD) B2C에서 응용 프로그램을 등록 하는 방법을 보여 줍니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. Azure AD B2C 테 넌 트를 포함 하는 디렉터리 사용 했는지 확인 합니다. 선택 된 **디렉터리 및 구독 필터** 최상위 메뉴에서 테 넌 트를 포함 하는 디렉터리를 선택 합니다.
3. Azure Portal의 왼쪽 위에서 **모든 서비스**를 선택하고 **Azure AD B2C**를 검색하여 선택합니다.
4. **응용 프로그램**을 선택하고 **추가**를 선택합니다.
5. 애플리케이션의 이름을 입력합니다. 예를 들어 *webapi1*과 같습니다.
6. **웹앱/웹 API 포함** 및 **암시적 흐름 허용**에 **예**를 선택합니다.
7. **회신 URL**에는 Azure AD B2C에서 애플리케이션이 요청한 토큰을 반환하는 엔드포인트를 입력합니다. 프로덕션 응용 프로그램에서 설정할 수 있습니다 회신 URL 값과 같은 `https://localhost:44332`합니다. 테스트를 위해 회신 URL로 설정 `https://jwt.ms`합니다.
8. **앱 ID URI**에 웹 API에 사용하는 식별자를 입력합니다. 도메인을 포함하는 전체 식별자 URI가 생성됩니다. 예: `https://contosotenant.onmicrosoft.com/api`.
9. **만들기**를 클릭합니다.
10. 속성 페이지에서 웹 애플리케이션을 구성할 때 사용할 애플리케이션 ID를 기록합니다.

## <a name="configure-scopes"></a>범위 구성

범위는 보호된 리소스에 대한 액세스를 제어하는 방법을 제공합니다. 범위는 웹 API에서 범위 기반 액세스 제어를 구현하는 데 사용됩니다. 예를 들어 웹 API 사용자가 읽기 및 쓰기 액세스 권한을 모두 갖고 있을 수도 있고, 읽기 권한만 갖고 있을 수도 있습니다. 이 자습서에서는 범위를 사용하여 웹 API에 대한 읽기 및 쓰기 권한을 정의합니다.

1. **애플리케이션**을 선택한 다음, *webapi1*을 선택합니다.
2. **게시된 범위**를 선택합니다.
3. **범위**에 `Read`를 입력하고, 설명에 `Read access to the application`를 입력합니다.
4. **범위**에 `Write`를 입력하고, 설명에 `Write access to the application`를 입력합니다.
5. **저장**을 클릭합니다.

게시된 범위는 클라이언트 애플리케이션 사용 권한을 웹 API에 부여하는 데 사용할 수 있습니다.

## <a name="grant-permissions"></a>권한 부여

애플리케이션에서 보호된 웹 API를 호출하려면 애플리케이션 사용 권한을 API에 부여해야 합니다. 예를 들어 [자습서: Azure Active Directory B2C에서 애플리케이션 등록](tutorial-register-applications.md)의 웹 애플리케이션은 *webapp1*이라는 Azure AD B2C에 만들어집니다. 이 애플리케이션을 사용하여 웹 API를 호출할 수 있습니다.

1. **애플리케이션**을 선택한 다음, 웹 애플리케이션을 선택합니다.
2. **API 액세스**를 선택한 다음, **추가**를 선택합니다.
3. **API 선택** 드롭다운에서 *webapi1*을 선택합니다.
4. **범위 선택** 드롭다운에서, 이전에 정의한 **읽기** 및 **쓰기** 범위를 선택합니다.
5. **확인**을 클릭합니다.

애플리케이션이 보호된 웹 API를 호출하도록 등록됩니다. 사용자가 Azure AD B2C로 인증하여 애플리케이션을 사용합니다. 애플리케이션은 Azure AD B2C에서 권한을 부여받아 보호된 웹 API에 액세스합니다.
