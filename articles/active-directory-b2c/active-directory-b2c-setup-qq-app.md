---
title: Azure Active Directory B2C를 사용하여 QQ 계정으로 등록 설정 및 로그인 | Microsoft Docs
description: 고객에게 Azure Active Directory B2C를 사용하여 애플리케이션에서 QQ 계정으로 등록 및 로그인을 제공합니다.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/11/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 7744d99bb97d11b7702c8ee388cf27bc0cc087cc
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/04/2019
ms.locfileid: "66508337"
---
# <a name="set-up-sign-up-and-sign-in-with-a-qq-account-using-azure-active-directory-b2c"></a>Azure Active Directory B2C를 사용하여 QQ 계정으로 등록 설정 및 로그인

> [!NOTE]
> 이 기능은 미리 보기 상태입니다.
> 

## <a name="create-a-qq-application"></a>QQ 애플리케이션 만들기

Azure AD(Azure Active Directory) B2C에서 QQ 계정을 ID 공급자로 사용하려면 테넌트에 해당 계정을 나타내는 애플리케이션을 만들어야 합니다. QQ 계정이 없는 경우 [https://ssl.zc.qq.com/en/index.html?type=1&ptlang=1033](https://ssl.zc.qq.com/en/index.html?type=1&ptlang=1033)에서 얻을 수 있습니다.

### <a name="register-for-the-qq-developer-program"></a>QQ 개발자 프로그램 등록

1. QQ 계정 자격 증명으로 [QQ 개발자 포털](http://open.qq.com)에 로그인합니다.
2. 로그인한 후에 [http://open.qq.com/reg](http://open.qq.com/reg)로 이동하여 개발자인 사용자를 직접 등록합니다.
3. **个人**(개별 개발자)를 선택합니다.
4. 필요한 정보를 입력하고 **下 一 步**(다음 단계)를 선택합니다.
5. 전자 메일 확인 프로세스를 완료합니다. 개발자로 등록한 후 승인이 되기까지 몇 일 동안 기다려야 합니다. 

### <a name="register-a-qq-application"></a>QQ 애플리케이션 등록

1. [https://connect.qq.com/index.html](https://connect.qq.com/index.html)로 이동합니다.
2. **应用管理**(앱 관리)를 선택합니다.
5. **创建应用**(앱 만들기)를 선택한 후 필수 정보를 입력합니다.
7. **授权回调域**(콜백 URL)에 `https://your-tenant-name.b2clogin.com/your-tenant-name}.onmicrosoft.com/oauth2/authresp`를 입력합니다. 예를 들어 `tenant_name`이 contoso인 경우 URL을 `https://contoso.b2clogin.com/contoso.onmicrosoft.com/oauth2/authresp`가 되도록 설정합니다.
8. **创建应用**(앱 만들기)를 선택합니다.
9. 확인 페이지에서 **应用管理**(앱 관리)를 선택하여 앱 관리 페이지로 돌아갑니다.
10. 만든 앱 옆에 있는 **查看**(보기)를 선택합니다.
11. **修改**(편집)을 선택합니다.
12. **앱 ID** 및 **앱 키**를 복사합니다. 테넌트에 ID 공급자를 추가하려면 두 값이 모두 필요합니다.

## <a name="configure-qq-as-an-identity-provider"></a>QQ를 ID 공급자로 구성

1. Azure AD B2C 테넌트의 전역 관리자로 [Azure Portal](https://portal.azure.com/)에 로그인합니다.
2. Azure AD B2C 테넌트를 포함하는 디렉터리를 사용하려면 위쪽 메뉴에서 **디렉터리 및 구독 필터**를 클릭하고 테넌트가 포함된 디렉터리를 선택합니다.
3. Azure Portal의 왼쪽 상단 모서리에서 **모든 서비스**를 선택하고 **Azure AD B2C**를 검색하여 선택합니다.
4. **ID 공급자**를 선택한 다음, **추가**를 선택합니다.
5. **이름**을 제공합니다. 예를 들어, *QQ*를 입력합니다.
6. **ID 공급자 형식**을 클릭하고 **QQ(미리 보기)** 를 선택한 다음, **확인**을 클릭합니다.
7. **이 ID 공급자 설정**을 선택하고 이전에 기록한 앱 ID를 **클라이언트 ID**로 입력한 후, 기록한 앱 키를 이전에 만든 QQ 애플리케이션의 **클라이언트 암호**로 입력합니다.
8. **확인**을 클릭한 다음 **만들기**를 클릭하여 QQ 구성을 저장합니다.

