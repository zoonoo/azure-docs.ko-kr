---
title: 포함 파일
description: 포함 파일
services: active-directory
documentationcenter: dev-center-name
author: danieldobalian
manager: CelesteDG
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: ios
ms.workload: identity
ms.date: 09/19/2018
ms.author: jmprieur
ms.reviwer: brandwe
ms.custom: include file
ms.openlocfilehash: a4b0c2e2d7732c6d2a4f57411731f7145cf3e73e
ms.sourcegitcommit: e9a46b4d22113655181a3e219d16397367e8492d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/21/2019
ms.locfileid: "65967665"
---
## <a name="register-your-application"></a>애플리케이션 등록

다음 두 섹션에 설명된 대로 두 가지 방법 중 하나로 애플리케이션을 등록할 수 있습니다.

### <a name="option-1-express-mode"></a>옵션 1: 기본 모드

이제 *Microsoft 애플리케이션 등록 포털*에서 애플리케이션을 등록해야 합니다.

1. [Microsoft 응용 프로그램 등록 포털](https://apps.dev.microsoft.com/portal/register-app?appType=mobileAndDesktopApp&appTech=ios&step=configure)을 통해 응용 프로그램을 등록합니다.
2. 애플리케이션 이름과 이메일을 입력합니다.
3. [단계별 설정] 옵션이 선택되어 있는지 확인합니다.
4. 지침에 따라 애플리케이션 ID를 가져와 코드에 붙여넣습니다.

### <a name="option-2-advanced-mode"></a>옵션 2: 고급 모드

1. [Microsoft 애플리케이션 등록 포털](https://apps.dev.microsoft.com/portal/register-app)로 이동합니다.
2. 애플리케이션의 이름을 입력합니다.
3. [단계별 설정] 옵션이 선택 취소되어 있는지 확인합니다.
4. `Add Platform`, `Native Application`을 차례로 선택합니다.
5. `Save`을(를) 선택합니다.
6. Xcode로 돌아갑니다. `ViewController.swift`에서 '`let kClientID`'로 시작하는 줄을 방금 등록한 애플리케이션 ID로 바꿉니다.

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
<i><code>[Your_Application_Id_Here]</code></i>를 방금 등록한 애플리케이션 ID로 바꿉니다.
</li>
</ol>
