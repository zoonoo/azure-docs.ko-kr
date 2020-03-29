---
title: 몰입형 리더 쿠키 정책 설정
titleSuffix: Azure Cognitive Services
description: 이 문서에서는 몰입형 리더에 대한 쿠키 정책을 설정하는 방법을 설명합니다.
services: cognitive-services
author: pasta
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: conceptual
ms.date: 01/06/2020
ms.author: pasta
ms.openlocfilehash: 6de651f1eb51ea1bc941c2af675c8a8d5b0f9cd7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "75946283"
---
# <a name="how-to-set-the-cookie-policy-for-the-immersive-reader"></a>몰입형 리더에 대한 쿠키 정책을 설정하는 방법

몰입형 리더는 기본적으로 쿠키 사용을 비활성화합니다. 쿠키 사용을 활성화하는 경우 몰입형 리더는 쿠키를 사용하여 사용자 기본 설정을 유지하고 기능 사용을 추적할 수 있습니다. 몰입형 리더에서 쿠키 사용을 활성화하는 경우 EU 쿠키 준수 정책의 요구 사항을 고려하십시오. EU 쿠키 준수 정책에 따라 필요한 사용자 동의를 얻는 것은 호스트 애플리케이션의 책임입니다.

쿠키 정책은 몰입형 판독기 [옵션을](../reference.md#options)통해 설정할 수 있습니다. 자세한 내용은 [쿠키정책 열거형을](../reference.md#cookiepolicy-enum) 참조하십시오.

## <a name="enable-cookie-usage"></a>쿠키 사용 활성화

```javascript
var options = {
    'cookiePolicy': ImmersiveReader.CookiePolicy.Enable
};

ImmersiveReader.launchAsync(YOUR_TOKEN, YOUR_SUBDOMAIN, YOUR_DATA, options);
```

## <a name="disable-cookie-usage"></a>쿠키 사용 사용 안 함

```javascript
var options = {
    'cookiePolicy': ImmersiveReader.CookiePolicy.Disable
};

ImmersiveReader.launchAsync(YOUR_TOKEN, YOUR_SUBDOMAIN, YOUR_DATA, options);
```

## <a name="next-steps"></a>다음 단계

* Node.js를 사용하여 몰입형 판독기 SDK를 통해 수행할 수 있는 다른 작업을 확인하려면 [Node.js 빠른 시작](../quickstart-nodejs.md)을 살펴보세요.
* Python을 사용하여 몰입형 리더 SDK를 통해 수행할 수 있는 다른 작업을 확인하려면 [Python 자습서](../tutorial-python.md)를 살펴보세요.
* Swift [자습서를](../tutorial-ios-picture-immersive-reader.md) 보고 Swift를 사용하여 몰입형 리더 SDK로 수행할 수 있는 다른 작업을 확인합니다.
* [몰입형 판독기 SDK](https://github.com/microsoft/immersive-reader-sdk) 및 [몰입형 판독기 SDK 참조](../reference.md) 살펴보기