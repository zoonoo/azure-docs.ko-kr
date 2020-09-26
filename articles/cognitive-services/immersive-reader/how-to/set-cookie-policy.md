---
title: 몰입 형 판독기 쿠키 정책 설정
titleSuffix: Azure Cognitive Services
description: 이 문서에서는 몰입 형 판독기에 대 한 쿠키 정책을 설정 하는 방법을 보여 줍니다.
services: cognitive-services
author: nitinme
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: conceptual
ms.date: 01/06/2020
ms.author: nitinme
ms.custom: devx-track-js
ms.openlocfilehash: 02901e6b4a75257b2cda8dee84dbe3438dc15c18
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/25/2020
ms.locfileid: "91332375"
---
# <a name="how-to-set-the-cookie-policy-for-the-immersive-reader"></a>몰입 형 판독기에 대 한 쿠키 정책을 설정 하는 방법

몰입 형 판독기는 기본적으로 쿠키 사용을 사용 하지 않도록 설정 합니다. 쿠키 사용을 사용 하도록 설정 하면 몰입 형 판독기는 쿠키를 사용 하 여 사용자 기본 설정을 유지 하 고 기능 사용을 추적할 수 있습니다. 몰입 형 판독기에서 쿠키 사용을 사용 하도록 설정 하는 경우 EU 쿠키 준수 정책의 요구 사항을 고려 하세요. EU 쿠키 준수 정책에 따라 필요한 사용자 동의를 얻으려면 호스트 응용 프로그램의 책임입니다.

쿠키 정책은 몰입 형 판독기 [옵션](../reference.md#options)을 통해 설정할 수 있습니다.

## <a name="enable-cookie-usage"></a>쿠키 사용 사용

```javascript
var options = {
    'cookiePolicy': ImmersiveReader.CookiePolicy.Enable
};

ImmersiveReader.launchAsync(YOUR_TOKEN, YOUR_SUBDOMAIN, YOUR_DATA, options);
```

## <a name="disable-cookie-usage"></a>쿠키 사용 안 함

```javascript
var options = {
    'cookiePolicy': ImmersiveReader.CookiePolicy.Disable
};

ImmersiveReader.launchAsync(YOUR_TOKEN, YOUR_SUBDOMAIN, YOUR_DATA, options);
```

## <a name="next-steps"></a>다음 단계

* Node.js를 사용하여 몰입형 판독기 SDK를 통해 수행할 수 있는 다른 작업을 확인하려면 [Node.js 빠른 시작](../quickstarts/client-libraries.md?pivots=programming-language-nodejs)을 살펴보세요.
* [Android 자습서](../tutorial-android.md)를 보고 Java 또는 Android용 Kotlin을 사용하여 몰입형 리더 SDK를 통해 수행할 수 있는 다른 작업을 확인합니다.
* [iOS 자습서](../tutorial-ios.md)를 보고 iOS용 Swift를 사용하여 몰입형 리더 SDK를 통해 수행할 수 있는 다른 작업을 확인합니다.
* Python을 사용하여 몰입형 리더 SDK를 통해 수행할 수 있는 다른 작업을 확인하려면 [Python 자습서](../tutorial-python.md)를 살펴보세요.
* [몰입형 판독기 SDK](https://github.com/microsoft/immersive-reader-sdk) 및 [몰입형 판독기 SDK 참조](../reference.md) 살펴보기