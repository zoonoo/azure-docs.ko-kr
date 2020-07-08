---
title: Amazon 계정으로 등록 및 로그인 설정
titleSuffix: Azure AD B2C
description: 고객에게 Azure Active Directory B2C를 사용하여 애플리케이션에서 Amazon 계정으로 등록 및 로그인을 제공합니다.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 04/05/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: d1a771cb13fcfa76449500ad71c67dcf7c446fa4
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85388445"
---
# <a name="set-up-sign-up-and-sign-in-with-an-amazon-account-using-azure-active-directory-b2c"></a>Azure Active Directory B2C를 사용하여 Amazon 계정으로 등록 설정 및 로그인

## <a name="create-an-app-in-the-amazon-developer-console"></a>Amazon developer console에서 앱 만들기

Azure Active Directory B2C (Azure AD B2C)에서 Amazon 계정을 페더레이션 id 공급자로 사용 하려면 [Amazon 개발자 서비스 및 기술](https://developer.amazon.com)에서 응용 프로그램을 만들어야 합니다. Amazon 계정이 아직 없는 경우에서 등록할 수 있습니다 [https://www.amazon.com/](https://www.amazon.com/) .

> [!NOTE]  
> 아래의 **8 단계** 에서 다음 url을 사용 하 여를 `your-tenant-name` 테 넌 트의 이름으로 바꿉니다. 테 넌 트 이름을 입력 하는 경우 Azure AD B2C에 대 문자가 대문자로 정의 된 경우에도 모든 소문자를 사용 합니다.
> - **허용 되는 원본**에 대해 다음을 입력 합니다.`https://your-tenant-name.b2clogin.com` 
> - **허용 되는 반환 url**에 대해 다음을 입력 합니다.`https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp`

[!INCLUDE [identity-provider-amazon-idp-register.md](../../includes/identity-provider-amazon-idp-register.md)]

## <a name="configure-an-amazon-account-as-an-identity-provider"></a>Amazon 계정을 ID 공급자로 구성

1. Azure AD B2C 테넌트의 전역 관리자로 [Azure Portal](https://portal.azure.com/)에 로그인합니다.
1. Azure AD B2C 테넌트를 포함하는 디렉터리를 사용하려면 위쪽 메뉴에서 **디렉터리 + 구독** 필터를 선택하고, 테넌트가 포함된 디렉터리를 선택합니다.
1. Azure Portal의 왼쪽 상단 모서리에서 **모든 서비스**를 선택하고 **Azure AD B2C**를 검색하여 선택합니다.
1. **Id 공급자**를 선택한 다음 **Amazon**를 선택 합니다.
1. **이름**을 입력합니다. 예: *Amazon*.
1. **클라이언트 id**에 대해 이전에 만든 Amazon 응용 프로그램의 클라이언트 id를 입력 합니다.
1. **클라이언트 암호**에 대해 기록한 클라이언트 암호를 입력 합니다.
1. **저장**을 선택합니다.
