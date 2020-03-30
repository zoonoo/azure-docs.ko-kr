---
title: Microsoft 계정으로 등록 및 로그인 설정
titleSuffix: Azure AD B2C
description: Azure Active Directory B2C를 사용하여 응용 프로그램에서 Microsoft 계정을 사용하여 고객에게 등록 및 로그인을 제공합니다.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 25784eb161a860398b0741d1d20375cabd1c4eca
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78188021"
---
# <a name="set-up-sign-up-and-sign-in-with-a-microsoft-account-using-azure-active-directory-b2c"></a>Azure Active Directory B2C를 사용하여 Microsoft 계정으로 등록 설정 및 로그인

## <a name="create-a-microsoft-account-application"></a>Microsoft 계정 애플리케이션 만들기

Azure Active Directory B2C(Azure AD B2C)에서 Microsoft 계정을 [ID 공급자로](openid-connect.md) 사용하려면 Azure AD 테넌트에서 응용 프로그램을 만들어야 합니다. Azure AD 테넌트는 Azure AD B2C 테넌트와 다릅니다. 아직 Microsoft 계정이 없는 경우 에서 [https://www.live.com/](https://www.live.com/)하나를 얻을 수 있습니다.

1. [Azure 포털에](https://portal.azure.com)로그인합니다.
1. 최상위 메뉴에서 **디렉터리 + 구독** 필터를 선택하고 Azure AD 테넌트가 포함된 디렉터리를 선택하여 Azure AD 테넌트를 포함하는 디렉터리를 사용하고 있는지 확인합니다.
1. Azure Portal의 왼쪽 상단 모서리에서 **모든 서비스**를 선택한 다음, **앱 등록**을 검색하여 선택합니다.
1. **새 등록**을 선택합니다.
1. 응용 프로그램의 **이름을** 입력합니다. 예를 들어 *MSAapp1* 등을 입력합니다.
1. **지원되는 계정 유형에서** **모든 조직 디렉터리 및 개인 Microsoft 계정(예: Skype, Xbox, Outlook.com)에서 계정을 선택합니다.** 이 옵션은 가장 광범위한 Microsoft ID 집합을 대상으로 합니다.

   다른 계정 유형 선택에 대한 자세한 내용은 [빠른 시작: Microsoft ID 플랫폼에 응용 프로그램 등록을](../active-directory/develop/quickstart-register-app.md)참조하십시오.
1. **리디렉션 URI(선택 사항)에서** **웹을** 선택하고 텍스트 상자에 입력합니다. `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` Azure `your-tenant-name` AD B2C 테넌트 이름으로 바꿉니다.
1. **레지스터** 선택
1. 응용 프로그램 개요 페이지에 표시된 **응용 프로그램(클라이언트) ID를** 기록합니다. 다음 섹션에서 ID 공급자를 구성할 때이 필요 합니다.
1. **인증서 & 비밀** 선택
1. **새 클라이언트 비밀을 클릭합니다.**
1. 비밀에 대한 **설명을** 입력합니다(예: *응용 프로그램 암호 1*) 다음 **추가**를 클릭합니다.
1. **값** 열에 표시된 응용 프로그램 암호를 기록합니다. 다음 섹션에서 ID 공급자를 구성할 때이 필요 합니다.

## <a name="configure-a-microsoft-account-as-an-identity-provider"></a>Microsoft 계정을 ID 공급자로 구성

1. Azure AD B2C 테넌트의 전역 관리자로 Azure [포털에](https://portal.azure.com/) 로그인합니다.
1. Azure AD B2C 테넌트를 포함하는 디렉터리를 사용하려면 위쪽 메뉴에서 **디렉터리 + 구독** 필터를 선택하고, 테넌트가 포함된 디렉터리를 선택합니다.
1. Azure Portal의 왼쪽 상단 모서리에서 **모든 서비스**를 선택하고 **Azure AD B2C**를 검색하여 선택합니다.
1. **ID 공급자를**선택한 다음 **Microsoft 계정을**선택합니다.
1. **이름**을 입력합니다. 예를 들어, *MSA*.
1. 클라이언트 **ID의**경우 이전에 만든 Azure AD 응용 프로그램의 응용 프로그램(클라이언트) ID를 입력합니다.
1. 클라이언트 **보안 에**대 한 기록 하는 클라이언트 비밀을 입력 합니다.
1. **저장**을 선택합니다.
