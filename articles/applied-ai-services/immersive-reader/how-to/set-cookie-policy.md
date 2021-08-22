---
title: 몰입형 리더 쿠키 정책 설정
titleSuffix: Azure Applied AI Services
description: 이 문서에서는 몰입형 리더에 대한 쿠키 정책을 설정하는 방법을 보여 줍니다.
services: cognitive-services
author: nitinme
manager: guillasi
ms.service: applied-ai-services
ms.subservice: immersive-reader
ms.topic: conceptual
ms.date: 01/06/2020
ms.author: nitinme
ms.custom: devx-track-js
ms.openlocfilehash: 69cf0eb1659b243220eed1199dac06effc22f363
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122529149"
---
# <a name="how-to-set-the-cookie-policy-for-the-immersive-reader"></a>몰입형 리더에 대한 쿠키 정책을 설정하는 방법

몰입형 리더는 기본적으로 쿠키 사용 기능을 사용하지 않도록 설정합니다. 쿠키 사용 기능을 사용하도록 설정하는 경우 몰입형 리더는 쿠키를 사용하여 사용자 기본 설정을 유지 관리하고 기능 사용을 추적할 수 있습니다. 몰입형 리더에서 쿠키 사용 기능을 사용하도록 설정하는 경우 EU 쿠키 준수 정책의 요구 사항을 고려하세요. EU 쿠키 준수 정책에 따라 필요한 모든 사용자 동의를 얻는 것은 호스트 애플리케이션의 책임입니다.

쿠키 정책은 몰입형 리더 [옵션](../reference.md#options)을 통해 설정할 수 있습니다.

## <a name="enable-cookie-usage"></a>쿠키 사용 기능을 사용하도록 설정

```javascript
var options = {
    'cookiePolicy': ImmersiveReader.CookiePolicy.Enable
};

ImmersiveReader.launchAsync(YOUR_TOKEN, YOUR_SUBDOMAIN, YOUR_DATA, options);
```

## <a name="disable-cookie-usage"></a>쿠키 사용 기능을 사용하지 않도록 설정

```javascript
var options = {
    'cookiePolicy': ImmersiveReader.CookiePolicy.Disable
};

ImmersiveReader.launchAsync(YOUR_TOKEN, YOUR_SUBDOMAIN, YOUR_DATA, options);
```

## <a name="next-steps"></a>다음 단계

* Node.js를 사용하여 몰입형 판독기 SDK를 통해 수행할 수 있는 다른 작업을 확인하려면 [Node.js 빠른 시작](../quickstarts/client-libraries.md?pivots=programming-language-nodejs)을 살펴보세요.
* [Android 자습서](../how-to-launch-immersive-reader.md)를 보고 Java 또는 Android용 Kotlin을 사용하여 몰입형 리더 SDK를 통해 수행할 수 있는 다른 작업을 확인합니다.
* [iOS 자습서](../how-to-launch-immersive-reader.md)를 보고 iOS용 Swift를 사용하여 몰입형 리더 SDK를 통해 수행할 수 있는 다른 작업을 확인합니다.
* Python을 사용하여 몰입형 리더 SDK를 통해 수행할 수 있는 다른 작업을 확인하려면 [Python 자습서](../how-to-launch-immersive-reader.md)를 살펴보세요.
* [몰입형 판독기 SDK](https://github.com/microsoft/immersive-reader-sdk) 및 [몰입형 판독기 SDK 참조](../reference.md) 살펴보기