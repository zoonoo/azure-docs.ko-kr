---
title: 포함 파일
description: 포함 파일
services: active-directory
documentationcenter: dev-center-name
author: brandwe
manager: mtillman
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: ios
ms.workload: identity
ms.date: 09/19/2018
ms.author: brandwe
ms.custom: include file
ms.openlocfilehash: 331d16df55e26df5d49555c636b307499dd052af
ms.sourcegitcommit: 6f59cdc679924e7bfa53c25f820d33be242cea28
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/05/2018
ms.locfileid: "48843565"
---
## <a name="register-your-application"></a>응용 프로그램 등록
다음 두 섹션에 설명된 대로 두 가지 방법 중 하나로 응용 프로그램을 등록할 수 있습니다.

### <a name="option-1-express-mode"></a>옵션 1: 기본 모드
이제 *Microsoft 응용 프로그램 등록 포털*에서 등용 프로그램을 등록해야 합니다.
1. [Microsoft 응용 프로그램 등록 포털](https://apps.dev.microsoft.com/portal/register-app?appType=mobileAndDesktopApp&appTech=ios&step=configure)을 통해 응용 프로그램을 등록합니다.
2.  응용 프로그램 이름과 메일을 입력합니다.
3.  안내식 설정 옵션이 선택되어 있는지 확인합니다.
4.  지침에 따라 응용 프로그램 ID를 가져와 코드에 붙여넣습니다.

### <a name="option-2-advanced-mode"></a>옵션 2: 고급 모드

1.  [Microsoft 응용 프로그램 등록 포털](https://apps.dev.microsoft.com/portal/register-app)로 이동합니다.
2.  응용 프로그램의 이름을 입력합니다.
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
        </array>
    </dict>
</array>
```
<ol start="8">
<li>
<i><code>[Your_Application_Id_Here]</code></i>를 방금 등록한 응용 프로그램 ID로 바꿉니다.
</li>
</ol>
