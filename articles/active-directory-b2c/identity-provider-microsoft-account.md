---
title: Microsoft 계정으로 등록 및 로그인 설정
titleSuffix: Azure AD B2C
description: 고객에게 Azure Active Directory B2C를 사용하여 애플리케이션에서 Microsoft 계정으로 등록 및 로그인을 제공합니다.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 05/12/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: ad4b08a12e63b4ae3eed0eb09e295d9730de97bb
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "85388003"
---
# <a name="set-up-sign-up-and-sign-in-with-a-microsoft-account-using-azure-active-directory-b2c"></a>Azure Active Directory B2C를 사용하여 Microsoft 계정으로 등록 설정 및 로그인

## <a name="create-a-microsoft-account-application"></a>Microsoft 계정 애플리케이션 만들기

Azure AD B2C(Azure Active Directory B2C)에서 Microsoft 계정을 [ID 공급자](openid-connect.md)로 사용하려면 Azure AD 테넌트에 애플리케이션을 만들어야 합니다. Azure AD 테넌트는 Azure AD B2C 테넌트와 다릅니다. Microsoft 계정이 없는 경우 [https://www.live.com/](https://www.live.com/)에서 하나의 계정을 얻을 수 있습니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. Azure AD 테넌트를 포함하는 디렉터리를 사용하려면 위쪽 메뉴에서 **디렉터리 + 구독** 필터를 선택하고, Azure AD 테넌트가 포함된 디렉터리를 선택합니다.
1. Azure Portal의 왼쪽 상단 모서리에서 **모든 서비스**를 선택한 다음, **앱 등록**을 검색하여 선택합니다.
1. **새 등록**을 선택합니다.
1. 애플리케이션의 **이름**을 입력합니다. 예를 들어 *MSAapp1* 등을 입력합니다.
1. **지원되는 계정 유형**에서 **모든 조직 디렉터리의 계정(모든 Azure AD 디렉터리 - 다중 테넌트) 및 개인 Microsoft 계정(예: Skype, Xbox)** 을 선택합니다.

   다른 계정 유형 선택에 대한 자세한 내용은 다음을 참조하세요. [빠른 시작: Microsoft ID 플랫폼에 애플리케이션 등록](../active-directory/develop/quickstart-register-app.md)
1. **리디렉션 URI(선택 사항)** 에서 **웹**을 선택하고 텍스트 상자에 `https://<tenant-name>.b2clogin.com/<tenant-name>.onmicrosoft.com/oauth2/authresp`를 입력합니다. `<tenant-name>`은 Azure AD B2C 테넌트 이름으로 바꿉니다.
1. **등록**을 선택합니다.
1. 애플리케이션 개요 페이지에 표시된 **애플리케이션(클라이언트) ID**를 기록합니다. 다음 섹션에서 ID 공급자를 구성할 때 필요합니다.
1. **인증서 및 비밀**을 선택합니다.
1. **새 클라이언트 비밀**을 클릭합니다.
1. 비밀에 대한 **설명**(예: *애플리케이션 암호 1*)을 입력한 다음 **추가**를 클릭합니다.
1. **값** 열에 표시된 애플리케이션 암호를 기록합니다. 다음 섹션에서 ID 공급자를 구성할 때 필요합니다.

## <a name="configure-a-microsoft-account-as-an-identity-provider"></a>Microsoft 계정을 ID 공급자로 구성

1. Azure AD B2C 테넌트의 전역 관리자로 [Azure Portal](https://portal.azure.com/)에 로그인합니다.
1. Azure AD B2C 테넌트를 포함하는 디렉터리를 사용하려면 위쪽 메뉴에서 **디렉터리 + 구독** 필터를 선택하고, 테넌트가 포함된 디렉터리를 선택합니다.
1. Azure Portal의 왼쪽 상단 모서리에서 **모든 서비스**를 선택하고 **Azure AD B2C**를 검색하여 선택합니다.
1. **ID 공급자**를 선택한 다음 **Microsoft 계정**을 선택합니다.
1. **이름**을 입력합니다. 예: *MSA*
1. **클라이언트 ID**에 이전에 만든 Azure AD 애플리케이션의 애플리케이션(클라이언트) ID를 입력합니다.
1. **클라이언트 비밀**에는 기록한 클라이언트 비밀을 입력합니다.
1. **저장**을 선택합니다.
