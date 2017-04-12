---
title: "Azure Active Directory B2C: 응용 프로그램 등록 | Microsoft Docs"
description: "Azure Active Directory B2C로 응용 프로그램 등록하는 방법"
services: active-directory-b2c
documentationcenter: 
author: parakhj
manager: krassk
editor: parakhj
ms.assetid: 20e92275-b25d-45dd-9090-181a60c99f69
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 3/13/2017
ms.author: parakhj
translationtype: Human Translation
ms.sourcegitcommit: 9553c9ed02fa198d210fcb64f4657f84ef3df801
ms.openlocfilehash: e04fbd97dd4d5ecaf12edf47d80572b32d29ed00
ms.lasthandoff: 03/23/2017



---
# <a name="azure-active-directory-b2c-register-your-application"></a>Azure Active Directory B2C: 응용 프로그램 등록

> [!IMPORTANT]
> Azure Portal의 Azure AD B2C 블레이드에서 만든 응용 프로그램은 동일한 위치에서 관리되어야 합니다. PowerShell 또는 다른 포털을 사용하여 B2C 응용 프로그램을 편집하는 경우 해당 응용 프로그램이 지원되지 않게 되며 Azure AD B2C와 함께 작동하지 않을 가능성이 있습니다.
> 
> 

## <a name="prerequisite"></a>필수 요소
소비자 등록 및 로그인을 수락하는 응용 프로그램을 만들려면 먼저 Azure Active Directory B2C 테넌트를 사용하여 해당 응용 프로그램을 등록해야 합니다. [Azure AD B2C 테넌트 만들기](active-directory-b2c-get-started.md)에 요약한 단계를 사용하여 자신의 테넌트를 가져옵니다. 해당 문서의 모든 단계를 수행한 후 시작 보드에 고정된 B2C 기능 블레이드가 있을 것입니다.

[!INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## <a name="navigate-to-the-b2c-features-blade"></a>B2C 기능 블레이드로 이동
시작 보드에 고정된 B2C 기능 블레이드가 있다면 [Azure 포털](https://portal.azure.com/) 에 B2C 테넌트의 전역 관리자 권한으로 로그인하는 즉시 해당 블레이드가 나타납니다.

또한 [Azure Portal](https://portal.azure.com/)의 왼쪽 탐색 창에서 **추가 서비스**를 클릭한 다음 **Azure AD B2C**를 검색하여 블레이드에 액세스할 수도 있습니다.

> [!IMPORTANT]
> B2C 기능 블레이드에 액세스하려면 B2C 테넌트의 전역 관리자가 되어야 합니다. 다른 테넌트의 전역 관리자 또는 사용자는 액세스할 수 없습니다.  Azure Portal의 오른쪽 위 모서리에 있는 테넌트 전환기를 사용하여 B2C 테넌트로 전환할 수 있습니다.
> 
> 

## <a name="register-a-web-application"></a>웹 응용 프로그램 등록
1. Azure 포털의 B2C 기능 블레이드에서 **응용 프로그램**을 클릭합니다.
2. 블레이드의 위쪽에서 **+추가** 를 클릭합니다.
3. 소비자에게 응용 프로그램을 설명하는 응용 프로그램의 **이름** 을 입력합니다. 예를 들어 "Contoso B2C 앱"을 입력할 수 있습니다.
4. **웹앱/ 웹 API 포함** 스위치를 **예**로 설정합니다. **회신 URL** 은 Azure AD B2C에서 응용 프로그램이 요청한 토큰을 반환하는 끝점입니다. 예를 들어 `https://localhost:44316/`을 입력합니다.
5. **만들기** 를 클릭하여 응용 프로그램을 등록합니다.
6. 방금 만든 응용 프로그램을 클릭하고, 나중에 코드에서 사용할 전역적으로 고유한 **응용 프로그램 클라이언트 ID** 를 적어둡니다. 
7. 또한 웹 응용 프로그램이 Azure AD B2C에서 보호하는 Web API를 호출하는 경우 **키** 블레이드로 이동하고 **키 생성** 단추를 클릭하여 **응용 프로그램 암호**를 만들려고 합니다.

> [!NOTE]
> **응용 프로그램 암호** 는 중요한 보안 자격 증명이며 적절하게 보호해야 합니다.
> 
   

## <a name="register-a-web-api"></a>웹 API 등록
1. Azure 포털의 B2C 기능 블레이드에서 **응용 프로그램**을 클릭합니다.
2. 블레이드의 위쪽에서 **+추가** 를 클릭합니다.
3. 소비자에게 응용 프로그램을 설명하는 응용 프로그램의 **이름** 을 입력합니다. 예를 들어 "Contoso B2C api"를 입력할 수 있습니다.
4. **웹앱/ 웹 API 포함** 스위치를 **예**로 설정합니다. **회신 URL** 은 Azure AD B2C에서 응용 프로그램이 요청한 토큰을 반환하는 끝점입니다. 예를 들어 `https://localhost:44316/`을 입력합니다.
5. **앱 ID URI**를 입력합니다. Web API에 사용되는 식별자입니다. 예를 들어 '참고'를 입력합니다. 아래에서 전체 식별자 URI를 생성합니다. 
6. **만들기** 를 클릭하여 응용 프로그램을 등록합니다.
7. 방금 만든 응용 프로그램을 클릭하고, 나중에 코드에서 사용할 전역적으로 고유한 **응용 프로그램 클라이언트 ID** 를 적어둡니다.
8. **게시된 범위**를 클릭합니다. 다른 응용 프로그램에 부여할 수 있는 사용 권한(범위)을 정의한 위치입니다.
9. 필요에 따라 더 많은 범위를 추가합니다. 기본적으로 "user_impersonation" 범위를 정의합니다. 이렇게 하면 로그인한 사용자를 대신하여 다른 응용 프로그램이 이 API에 액세스할 수 있는 기능을 부여합니다. 원하는 경우 제거할 수 있습니다. 
10. **Save**를 클릭합니다.

## <a name="register-a-mobilenative-application"></a>모바일/네이티브 응용 프로그램 등록
1. Azure 포털의 B2C 기능 블레이드에서 **응용 프로그램**을 클릭합니다.
2. 블레이드의 위쪽에서 **+추가** 를 클릭합니다.
3. 소비자에게 응용 프로그램을 설명하는 응용 프로그램의 **이름** 을 입력합니다. 예를 들어 "Contoso B2C 앱"을 입력할 수 있습니다.
4. **네이티브 클라이언트 포함** 스위치를 **예**로 설정합니다.
5. 사용자 지정 체계로 **리디렉션 URI**를 입력합니다. 예를 들어 com.onmicrosoft.contoso.appname://redirect/경로입니다. [올바른 리디렉션 URI](#choosing-a-redirect-uri)를 선택합니다.
6. **저장**을 클릭하여 응용 프로그램을 등록합니다.
7. 방금 만든 응용 프로그램을 클릭하고, 나중에 코드에서 사용할 전역적으로 고유한 **응용 프로그램 클라이언트 ID** 를 적어둡니다.
8. 또한 네이티브 응용 프로그램이 Azure AD B2C에서 보호하는 Web API를 호출하는 경우 **키** 블레이드로 이동하고 **키 생성** 단추를 클릭하여 **응용 프로그램 암호**를 만들려고 합니다.

> [!NOTE]
> **응용 프로그램 암호** 는 중요한 보안 자격 증명이며 적절하게 보호해야 합니다.
> 

### <a name="choosing-a-redirect-uri"></a>리디렉션 URI 선택
모바일/네이티브 응용 프로그램에 대한 리디렉션 URI를 선택하는 경우 두 가지 중요한 고려 사항이 있습니다. 
* **고유**: 리디렉션 URI의 체계는 모든 응용 프로그램에 대해 고유해야 합니다. 예제에서는(com.onmicrosoft.contoso.appname://redirect/path) 체계로 com.onmicrosoft.contoso.appname을 사용합니다. 이 패턴을 따르는 것이 좋습니다. 두 개의 응용 프로그램이 동일한 체계를 공유하는 경우 "앱 선택" 대화 상자가 나타납니다. 사용자가 잘못 선택하는 경우 로그인이 실패합니다. 
* **전체**: 리디렉션 URI에는 체계 및 경로가 있어야 합니다. 경로는 도메인 뒤에 하나 이상의 슬래시를 포함해야 합니다(예: //contoso/는 작동, //contoso는 실패). 

## <a name="build-a-quick-start-application"></a>빠른 시작 응용 프로그램 빌드하기
Azure AD B2C로 등록된 응용 프로그램이 있다면 작동할 빠른 시작 자습서 중 하나를 완료할 수 있습니다. 몇 가지 권장 사항입니다.

[!INCLUDE [active-directory-v2-quickstart-table](../../includes/active-directory-b2c-quickstart-table.md)]


