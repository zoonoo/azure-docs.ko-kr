---
title: "Azure Active Directory B2C: Weibo 구성 | Microsoft Docs"
description: "소비자에게 Azure Active Directory B2C를 사용하여 보안이 유지되는 응용 프로그램에서 Weibo 계정으로 등록 및 로그인을 제공합니다."
services: active-directory-b2c
documentationcenter: 
author: parakhj
manager: krassk
editor: parakhj
ms.assetid: 1860de34-94cb-4ceb-851e-102f930f7230
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 3/26/2017
ms.author: parakhj
translationtype: Human Translation
ms.sourcegitcommit: 503f5151047870aaf87e9bb7ebf2c7e4afa27b83
ms.openlocfilehash: b7283da1a3774b06d782fbac204b18cc074d413f
ms.lasthandoff: 03/29/2017


---
# <a name="azure-active-directory-b2c-provide-sign-up-and-sign-in-to-consumers-with-weibo-accounts"></a>Azure Active Directory B2C: 고객에게 Weibo 계정으로 등록 및 로그인 제공

> [!NOTE]
> 이 기능은 아직 미리 보기 버전입니다.
> 

## <a name="create-a-weibo-application"></a>Weibo 응용 프로그램 만들기

Azure AD(Azure Active Directory) B2C에서 Weibo를 ID 공급자로 사용하려면 Weibo 응용 프로그램을 만들고 올바른 매개 변수를 제공해야 합니다. 이렇게 하려면 Weibo 계정이 필요합니다. 없는 경우 [http://weibo.com/signup/signup.php?lang=en-us](http://weibo.com/signup/signup.php?lang=en-us)에서 가져올 수 있습니다.

### <a name="register-for-the-weibo-developer-program"></a>Weibo 개발자 프로그램 등록

1. [Weibo 개발자 포털](http://open.weibo.com/)로 이동하고 Weibo 계정 자격 증명으로 로그인합니다.
2. 로그인한 후 오른쪽 위 모서리의 표시 이름을 클릭합니다.
3. 드롭다운에서 **编辑开发者信息**(개발자 정보 편집)을 선택합니다.
4. 양식에 필요한 정보를 입력하고 **提交**(제출)을 클릭합니다.
5. 전자 메일 확인 프로세스를 완료합니다.
6. [ID 확인 페이지](http://open.weibo.com/developers/identity/edit)로 이동합니다.
7. 양식에 필요한 정보를 입력하고 **提交**(제출)을 클릭합니다.

### <a name="register-a-weibo-application"></a>Weibo 응용 프로그램 등록

1. [새 Weibo 앱 등록 페이지](http://open.weibo.com/apps/new)로 이동합니다.
2. 필요한 응용 프로그램 정보를 입력합니다.
3. **创 建**(만들기)를 클릭합니다.
4. **앱 키** 및 **앱 암호**의 값을 복사합니다. 이 ID는 나중에 필요합니다.
5. 필요한 사진을 업로드하고 필요한 정보를 입력합니다.
6. **保存以上信息**(저장)을 클릭합니다.
7. **高级信息**(고급 정보)를 클릭합니다.
8. OAuth2.0 **授权设置**(리디렉션 URL)에 대한 필드 옆의 **编辑**(편집)을 클릭합니다.
9. OAuth2.0 **授权设置**(리디렉션 URL)에 `https://login.microsoftonline.com/te/{tenant_name}/oauth2/authresp`를 입력합니다. 예를 들어 `tenant_name`이 contoso.onmicrosoft.com인 경우 URL을 `https://login.microsoftonline.com/te/contoso.onmicrosoft.com/oauth2/authresp`가 되도록 설정합니다.
10. **提交**(제출)을 클릭합니다.  

## <a name="configure-weibo-as-an-identity-provider-in-your-tenant"></a>테넌트에서 Weibo를 ID 공급자로 구성
1. 다음 단계에 따라 [Azure 포털의 B2C 기능 블레이드로 이동합니다](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade) .
2. B2C 기능 블레이드에서 **ID 공급자**를 클릭합니다.
3. 블레이드의 위쪽에서 **+추가** 를 클릭합니다.
4. ID 공급자 구성에 친숙한 **이름** 을 제공합니다. 예를 들어 "Weibo"를 입력합니다.
5. **ID 공급자 형식**을 클릭하고 **Weibo**를 선택한 다음 **확인**을 클릭합니다.
6. **이 ID 공급자 설정**을 클릭합니다.
7. **클라이언트 ID**로 앞에서 복사한 **앱 키**를 입력합니다.
8. **클라이언트 암호**로 앞에서 복사한 **앱 암호**를 입력합니다.
9. **확인**을 클릭한 다음 **만들기**를 클릭하여 Weibo 구성을 저장합니다.


