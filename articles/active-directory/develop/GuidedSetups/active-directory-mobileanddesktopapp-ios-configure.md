---
title: "Azure AD v2 iOS 시작 - 구성 | Microsoft Docs"
description: "iOS(Swift) 응용 프로그램이 Azure Active Directory v2 끝점으로 보호되는 액세스 토큰을 필요로 하는 API를 호출하는 방식"
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mbaldwin
editor: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/09/2017
ms.author: andret
ms.translationtype: HT
ms.sourcegitcommit: b309108b4edaf5d1b198393aa44f55fc6aca231e
ms.openlocfilehash: 8a4f412102c0e75654030f5d4f8aab575fdcebba
ms.contentlocale: ko-kr

---
## <a name="create-an-application-express"></a>응용 프로그램(Express) 만들기
이제 *Microsoft 응용 프로그램 등록 포털*에서 등용 프로그램을 등록해야 합니다.
1. [Microsoft 응용 프로그램 등록 포털](https://apps.dev.microsoft.com/portal/register-app?appType=mobileAndDesktopApp&appTech=ios&step=configure)을 통해 응용 프로그램을 등록합니다.
2.  응용 프로그램 이름과 메일을 입력합니다.
3.  안내식 설정 옵션이 선택되어 있는지 확인합니다.
4.  지침에 따라 응용 프로그램 ID를 가져와 코드에 붙여넣습니다.

### <a name="add-your-application-registration-information-to-your-solution-advanced"></a>솔루션에 응용 프로그램 등록 정보 추가(고급)

1.  [Microsoft 응용 프로그램 등록 포털](https://apps.dev.microsoft.com/portal/register-app)로 이동합니다.
2.  응용 프로그램 이름과 메일을 입력합니다.
3.  안내식 설정 옵션이 선택 취소되어 있는지 확인합니다.
4.  `Add Platform`을 클릭한 다음 `Native Application`을 선택하고 `Save`를 클릭합니다.
5.  Xcode로 돌아갑니다. `ViewController.swift`에서 '`let kClientID`'로 시작하는 줄을 방금 등록한 응용 프로그램 ID로 바꿉니다.

```swift
let kClientID = "Your_Application_Id_Here"
```

<!-- Workaround for Docs conversion bug -->
<ol start="6">
<li>
Ctrl 키를 누른 채로 <code>Info.plist</code>를 클릭하여 상황에 맞는 메뉴를 표시한 후 다음을 클릭합니다. <code>Open As</code>> <code>Source Code</code>
</li>
<li>
<code>dict</code> 루트 노드 아래에 다음을 추가합니다.
</li>
</ol>

```xml
<key>CFBundleURLTypes</key>
<array>
    <dict>
        <key>CFBundleTypeRole</key>
        <string>Editor</string>
        <key>CFBundleURLName</key>
        <string>$(PRODUCT_BUNDLE_IDENTIFIER)</string>
        <key>CFBundleURLSchemes</key>
        <array>
            <string>msal[Your_Application_Id_Here]</string>
            <string>auth</string>
        </array>
    </dict>
</array>
```
<ol start="8">
<li>
<i><code>[Your_Application_Id_Here]</code></i>를 방금 등록한 응용 프로그램 ID로 바꿉니다.
</li>
</ol>

