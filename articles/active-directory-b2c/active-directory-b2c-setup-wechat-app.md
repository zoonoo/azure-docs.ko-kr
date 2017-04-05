---
title: "Azure Active Directory B2C: WeChat 구성 | Microsoft Docs"
description: "소비자에게 Azure Active Directory B2C를 사용하여 보안이 유지되는 응용 프로그램에서 WeChat 계정으로 등록 및 로그인을 제공합니다."
services: active-directory-b2c
documentationcenter: 
author: parakhj
manager: krassk
editor: parakhj
ms.assetid: d2424c66-ba68-4d82-847e-d137683527b0
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 3/26/2017
ms.author: parakhj
translationtype: Human Translation
ms.sourcegitcommit: 503f5151047870aaf87e9bb7ebf2c7e4afa27b83
ms.openlocfilehash: d1c18a3cf8ca9d55e4bca224cce8d6dba75fcb0d
ms.lasthandoff: 03/29/2017


---
# <a name="azure-active-directory-b2c-provide-sign-up-and-sign-in-to-consumers-with-wechat-accounts"></a>Azure Active Directory B2C: 고객에게 WeChat 계정으로 등록 및 로그인 제공

> [!NOTE]
> 이 기능은 아직 미리 보기 버전입니다.
> 

## <a name="create-a-wechat-application"></a>WeChat 응용 프로그램 만들기

Azure AD(Azure Active Directory) B2C에서 WeChat을 ID 공급자로 사용하려면 WeChat 응용 프로그램을 만들고 올바른 매개 변수를 제공해야 합니다. 이렇게 하려면 WeChat 계정이 필요합니다. 없는 경우 모바일 앱 중 하나를 통해 등록하거나 QQ 번호를 사용하여 하나를 얻을 수 있습니다. 그 후에 WeChat 개발자 프로그램에 등록된 계정을 가져옵니다. 자세한 내용은 [여기](http://kf.qq.com/faq/161220Brem2Q161220uUjERB.html)에서 찾을 수 있습니다.

### <a name="register-a-wechat-application"></a>WeChat 응용 프로그램 등록

1. [https://open.weixin.qq.com/](https://open.weixin.qq.com/)으로 이동하고 로그인합니다.
2. **管理中心**(관리 센터)를 클릭합니다.
3. 새 응용 프로그램을 등록하는 데 필요한 단계를 따릅니다.
4. **授权回调域**(콜백 URL)에 `https://login.microsoftonline.com/te/{tenant_name}/oauth2/authresp`를 입력합니다. 예를 들어 `tenant_name`이 contoso.onmicrosoft.com인 경우 URL을 `https://login.microsoftonline.com/te/contoso.onmicrosoft.com/oauth2/authresp`가 되도록 설정합니다.
5. **앱 ID** 및 **앱 키**를 찾고 복사합니다. 나중에 필요합니다.

## <a name="configure-wechat-as-an-identity-provider-in-your-tenant"></a>테넌트에서 WeChat을 ID 공급자로 구성
1. 다음 단계에 따라 [Azure 포털의 B2C 기능 블레이드로 이동합니다](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade) .
2. B2C 기능 블레이드에서 **ID 공급자**를 클릭합니다.
3. 블레이드의 위쪽에서 **+추가** 를 클릭합니다.
4. ID 공급자 구성에 친숙한 **이름** 을 제공합니다. 예를 들어 "WeChat"을 입력합니다.
5. **ID 공급자 형식**을 클릭하고 **WeChat**을 선택한 다음 **확인**을 클릭합니다.
6. **이 ID 공급자 설정**을 클릭합니다.
7. **클라이언트 ID**로 앞에서 복사한 **앱 키**를 입력합니다.
8. **클라이언트 암호**로 앞에서 복사한 **앱 암호**를 입력합니다.
9. **확인**을 클릭한 다음 **만들기**를 클릭하여 WeChat 구성을 저장합니다.


