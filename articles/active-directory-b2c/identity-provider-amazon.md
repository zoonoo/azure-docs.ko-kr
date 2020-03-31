---
title: Amazon 계정으로 등록 및 로그인 설정
titleSuffix: Azure AD B2C
description: 고객에게 Azure Active Directory B2C를 사용하여 애플리케이션에서 Amazon 계정으로 등록 및 로그인을 제공합니다.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: d4538c1d15aeae624f5d73e9985448bda2fd8f1b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78188463"
---
# <a name="set-up-sign-up-and-sign-in-with-an-amazon-account-using-azure-active-directory-b2c"></a>Azure Active Directory B2C를 사용하여 Amazon 계정으로 등록 설정 및 로그인

## <a name="create-an-amazon-application"></a>Amazon 애플리케이션 만들기

Azure Active Directory B2C(Azure AD B2C)에서 Amazon 계정을 [ID 공급자로](authorization-code-flow.md) 사용하려면 테넌트에서 해당 계정을 나타내는 응용 프로그램을 만들어야 합니다. 아직 Amazon 계정이 없는 경우 에서 [https://www.amazon.com/](https://www.amazon.com/)가입할 수 있습니다.

1. Amazon 계정 자격 증명을 사용하여 [Amazon 개발자 센터](https://login.amazon.com/)에 로그인합니다.
1. 이미 수행한 경우 **등록**을 클릭하고 개발자 등록 단계를 수행하며 정책에 동의합니다.
1. **새 애플리케이션 등록**을 선택합니다.
1. **이름**, **설명** 및 **개인 정보 알림 URL**을 입력하고 **저장**을 클릭합니다. 개인정보취급방침은 사용자에게 개인 정보를 제공하는 관리 대상 페이지입니다.
1. **웹 설정** 섹션에서 **클라이언트 ID** 값을 복사합니다. **비밀 표시**를 선택하여 클라이언트 암호를 표시한 후 복사합니다. 테넌트에서 Amazon 계정을 ID 공급자로 구성하려면 둘 모두가 필요합니다. **클라이언트 보안 비밀**은 중요한 보안 자격 증명이므로
1. **웹 설정** 섹션에서 **편집**을 선택한 후 **허용된 JavaScript 원본**에 `https://your-tenant-name.b2clogin.com`을 입력하고, **허용된 반환 URL**에 `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp`를 입력합니다. `your-tenant-name`을 테넌트 이름으로 바꿉니다. Azure AD B2C에서 테넌트가 대문자로 정의되어 있더라도 테넌트 이름을 입력할 때는 소문자만 사용해야 합니다.
1. **저장**을 클릭합니다.

## <a name="configure-an-amazon-account-as-an-identity-provider"></a>Amazon 계정을 ID 공급자로 구성

1. Azure AD B2C 테넌트의 전역 관리자로 Azure [포털에](https://portal.azure.com/) 로그인합니다.
1. Azure AD B2C 테넌트를 포함하는 디렉터리를 사용하려면 위쪽 메뉴에서 **디렉터리 + 구독** 필터를 선택하고, 테넌트가 포함된 디렉터리를 선택합니다.
1. Azure Portal의 왼쪽 상단 모서리에서 **모든 서비스**를 선택하고 **Azure AD B2C**를 검색하여 선택합니다.
1. **ID 공급자를**선택한 다음 **Amazon을**선택합니다.
1. **이름**을 입력합니다. 예를 들어, *아마존*.
1. 클라이언트 **ID의**경우 이전에 만든 Amazon 응용 프로그램의 클라이언트 ID를 입력합니다.
1. 클라이언트 **보안**에 대 한 기록 된 클라이언트 비밀을 입력 합니다.
1. **저장**을 선택합니다.
