---
title: 포함 파일
description: 포함 파일
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mtillman
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: ios
ms.workload: identity
ms.date: 09/19/2018
ms.author: andret
ms.custom: include file
ms.openlocfilehash: b3ced6bbd69b38e187b3ed4fa1f2b22e5dd05ce4
ms.sourcegitcommit: 0f54b9dbcf82346417ad69cbef266bc7804a5f0e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/26/2018
ms.locfileid: "50142759"
---
## <a name="add-the-applications-registration-information-to-your-app"></a>앱에 응용 프로그램의 등록 정보 추가

이 단계에서는 프로젝트에 응용 프로그램 ID를 추가해야 합니다.

1. `ViewController.swift`에서 '`let kClientID`'로 시작하는 줄을 다음으로 바꿉니다.

```swift
let kClientID = "[Enter the application Id here]"
```

<!-- Workaround for Docs conversion bug -->
<ol start="2">
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
            <string>msal[Enter the application Id here]</string>
        </array>
    </dict>
</array>
```
