---
title: Microsoft 계정으로 등록 및 로그인 설정 - Azure Active Directory B2C | Microsoft Docs
description: 고객에게 Azure Active Directory B2C를 사용하여 애플리케이션에서 Microsoft 계정으로 등록 및 로그인을 제공합니다.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/21/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: c0d236fbe350a36bc67e26349fe3c3145bd01616
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/04/2019
ms.locfileid: "66508442"
---
# <a name="set-up-sign-up-and-sign-in-with-a-microsoft-account-using-azure-active-directory-b2c"></a>Azure Active Directory B2C를 사용하여 Microsoft 계정으로 등록 설정 및 로그인

## <a name="create-a-microsoft-account-application"></a>Microsoft 계정 애플리케이션 만들기

Azure AD(Azure Active Directory) B2C에서 Microsoft 계정을 [ID 공급자](active-directory-b2c-reference-oidc.md)로 사용하려면 테넌트에 해당 계정을 나타내는 애플리케이션을 만들어야 합니다. Microsoft 계정이 없는 경우 [https://www.live.com/](https://www.live.com/)에서 얻을 수 있습니다.

1. Microsoft 계정 자격 증명을 사용하여 [Microsoft 애플리케이션 등록 포털](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList)에 로그인합니다.
2. 오른쪽 위 모서리에서 **앱 추가**를 선택합니다.
3. 애플리케이션의 **이름**을 입력합니다. 예를 들어 *MSAapp1* 등을 입력합니다.
4. **새 암호 생성**을 선택하고 ID 공급자를 구성할 때 사용한 암호를 복사합니다. **응용 프로그램 ID**도 복사합니다. 
5. **플랫폼 추가**를 선택하고 **웹**을 선택합니다.
4. **리디렉션 URL**에 `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp`를 입력합니다. `your-tenant-name`을 테넌트 이름으로 바꿉니다.
5. **저장**을 선택합니다.

## <a name="configure-a-microsoft-account-as-an-identity-provider"></a>Microsoft 계정을 ID 공급자로 구성

1. Azure AD B2C 테넌트의 전역 관리자로 [Azure Portal](https://portal.azure.com/)에 로그인합니다.
2. Azure AD B2C 테넌트를 포함하는 디렉터리를 사용하려면 위쪽 메뉴에서 **디렉터리 및 구독 필터**를 클릭하고 테넌트가 포함된 디렉터리를 선택합니다.
3. Azure Portal의 왼쪽 상단 모서리에서 **모든 서비스**를 선택하고 **Azure AD B2C**를 검색하여 선택합니다.
4. **ID 공급자**를 선택한 다음, **추가**를 선택합니다.
5. **이름**을 제공합니다. 예를 들어, *MSA*를 입력합니다.
6. **ID 공급자 형식**을 선택하고 **Microsoft 계정**을 선택하고 **확인**을 클릭합니다.
7. **이 ID 공급자 설정**을 선택하고 이전에 기록한 애플리케이션 ID를 **클라이언트 ID**로 입력한 후, 기록한 암호를 이전에 만든 Microsoft 계정 애플리케이션의 **클라이언트 암호**로 입력합니다.
8. **확인**을 클릭한 다음 **만들기**를 클릭하여 Microsoft 계정 구성을 저장합니다.

