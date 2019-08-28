---
title: Azure Active Directory B2C를 사용하여 QQ 계정으로 등록 설정 및 로그인
description: 고객에게 Azure Active Directory B2C를 사용하여 애플리케이션에서 QQ 계정으로 등록 및 로그인을 제공합니다.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 11fc41374089c0ab7258dd191a255387713836eb
ms.sourcegitcommit: 55e0c33b84f2579b7aad48a420a21141854bc9e3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/19/2019
ms.locfileid: "69622315"
---
# <a name="set-up-sign-up-and-sign-in-with-a-qq-account-using-azure-active-directory-b2c"></a>Azure Active Directory B2C를 사용하여 QQ 계정으로 등록 설정 및 로그인

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

## <a name="create-a-qq-application"></a>QQ 애플리케이션 만들기

Azure AD(Azure Active Directory) B2C에서 QQ 계정을 ID 공급자로 사용하려면 테넌트에 해당 계정을 나타내는 애플리케이션을 만들어야 합니다. 아직 QQ 계정이 없는 경우에는에서 [https://ssl.zc.qq.com/en/index.html?type=1&ptlang=1033](https://ssl.zc.qq.com/en/index.html?type=1&ptlang=1033)등록할 수 있습니다.

### <a name="register-for-the-qq-developer-program"></a>QQ 개발자 프로그램 등록

1. QQ 계정 자격 증명으로 [QQ 개발자 포털](http://open.qq.com)에 로그인합니다.
1. 로그인한 후에 [http://open.qq.com/reg](http://open.qq.com/reg)로 이동하여 개발자인 사용자를 직접 등록합니다.
1. **个人**(개별 개발자)를 선택합니다.
1. 필요한 정보를 입력하고 **下 一 步**(다음 단계)를 선택합니다.
1. 전자 메일 확인 프로세스를 완료합니다. 개발자로 등록한 후 승인이 되기까지 몇 일 동안 기다려야 합니다.

### <a name="register-a-qq-application"></a>QQ 애플리케이션 등록

1. [https://connect.qq.com/index.html](https://connect.qq.com/index.html)로 이동합니다.
1. **应用管理**(앱 관리)를 선택합니다.
1. **创建应用**(앱 만들기)를 선택한 후 필수 정보를 입력합니다.
1. **授权回调域**(콜백 URL)에 `https://your-tenant-name.b2clogin.com/your-tenant-name}.onmicrosoft.com/oauth2/authresp`를 입력합니다. 예를 들어 `tenant_name`이 contoso인 경우 URL을 `https://contoso.b2clogin.com/contoso.onmicrosoft.com/oauth2/authresp`가 되도록 설정합니다.
1. **创建应用**(앱 만들기)를 선택합니다.
1. 확인 페이지에서 **应用管理**(앱 관리)를 선택하여 앱 관리 페이지로 돌아갑니다.
1. 만든 앱 옆에 있는 **查看**(보기)를 선택합니다.
1. **修改**(편집)을 선택합니다.
1. **앱 ID** 및 **앱 키**를 복사합니다. 테넌트에 ID 공급자를 추가하려면 두 값이 모두 필요합니다.

## <a name="configure-qq-as-an-identity-provider"></a>QQ를 ID 공급자로 구성

1. Azure AD B2C 테넌트의 전역 관리자로 [Azure Portal](https://portal.azure.com/)에 로그인합니다.
1. 상단 메뉴에서 **디렉터리 + 구독** 필터를 선택 하 고 테 넌 트가 포함 된 디렉터리를 선택 하 여 Azure AD B2C 테 넌 트를 포함 하는 디렉터리를 사용 하 고 있는지 확인 합니다.
1. Azure Portal의 왼쪽 상단 모서리에서 **모든 서비스**를 선택하고 **Azure AD B2C**를 검색하여 선택합니다.
1. **Id 공급자**를 선택한 다음 **Qq (미리 보기)** 를 선택 합니다.
1. **이름**을 입력합니다. 예를 들어 *Qq*입니다.
1. **클라이언트 ID**에 대해 이전에 만든 qq 응용 프로그램의 앱 ID를 입력 합니다.
1. **클라이언트 암호**의 경우 기록한 앱 키를 입력 합니다.
1. **저장**을 선택합니다.
