---
title: 사용자 기본 설정 저장
titleSuffix: Azure Applied AI Services
description: 이 문서에서는 사용자의 기본 설정을 저장하는 방법을 보여 줍니다.
author: metanMSFT
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: how-to
ms.date: 06/29/2020
ms.author: metang
ms.openlocfilehash: b3ac552d60515d907157e9d4d778aef9bd7bb380
ms.sourcegitcommit: 58e5d3f4a6cb44607e946f6b931345b6fe237e0e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/25/2021
ms.locfileid: "110373861"
---
# <a name="how-to-store-user-preferences"></a>사용자 기본 설정을 저장하는 방법

이 문서에서는 [-preferences](./reference.md#options) 및 [-onPreferencesChanged](./reference.md#options) 몰입형 리더 SDK 옵션을 통해 사용자의 UI 설정(공식적으로 **사용자 기본 설정** 이라고 함)을 저장하는 방법을 보여 줍니다.

[CookiePolicy](./reference.md#cookiepolicy-options) SDK 옵션이 *사용* 으로 설정되면 몰입형 리더 애플리케이션에서 **사용자 기본 설정**(텍스트 크기, 테마 색, 글꼴 등)을 특정 브라우저 및 디바이스로 한정된 쿠키에 저장합니다. 사용자가 동일한 브라우저 및 디바이스에서 몰입형 리더를 시작할 때마다 해당 디바이스의 마지막 세션에서 사용자의 기본 설정으로 열립니다. 그러나 사용자가 다른 브라우저 또는 디바이스에서 몰입형 리더를 열면 처음에는 설정이 몰입형 리더의 기본 설정으로 구성되며, 사용자는 사용하는 각 디바이스에 대해 해당 기본 설정을 다시 설정해야 합니다. `-preferences` 및 `-onPreferencesChanged` 몰입형 리더 SDK 옵션은 애플리케이션에서 다양한 브라우저와 디바이스에서 사용자의 기본 설정을 로밍할 수 있는 방법을 제공하여 사용자가 애플리케이션을 사용하는 곳에서 일관된 환경을 사용할 수 있습니다.

먼저 몰입형 리더 애플리케이션을 시작할 때 `-onPreferencesChanged` 콜백 SDK 옵션을 제공하면 사용자가 몰입형 리더 세션 중에 기본 설정을 변경할 때마다 몰입형 리더에서 `-preferences` 문자열을 호스트 애플리케이션에 다시 보냅니다. 그러면 호스트 애플리케이션에서 사용자 기본 설정을 자체 시스템에 저장해야 합니다. 그런 다음, 동일한 사용자가 몰입형 리더를 다시 시작하면 호스트 애플리케이션에서 스토리지로부터 해당 사용자의 기본 설정을 검색하고, 몰입형 리더 애플리케이션을 시작할 때 `-preferences` 문자열 SDK 옵션으로 제공하여 사용자의 기본 설정을 복원 할 수 있습니다.

이 기능은 쿠키를 사용하는 것이 바람직하지 않거나 실행 가능하지 않은 경우 **사용자 기본 설정** 을 저장하는 대체 수단으로 사용할 수 있습니다.

> [!CAUTION]
> **중요** 예기치 않은 동작이 발생하여 고객의 사용자 경험이 저하될 수 있으므로 몰입형 리더 애플리케이션과 보내고 받는 `-preferences` 문자열의 값을 프로그래밍 방식으로 변경하려고 시도하지 마세요. 호스트 애플리케이션에서 사용자 지정 값을 할당하거나 `-preferences` 문자열을 조작하면 안 됩니다. `-preferences` 문자열 옵션을 사용하는 경우 `-onPreferencesChanged` 콜백 옵션에서 반환된 정확한 값만 사용합니다.

## <a name="how-to-enable-storing-user-preferences"></a>사용자 기본 설정을 저장하도록 설정하는 방법

몰입형 리더 SDK [launchAsync](./reference.md#launchasync) `options` 매개 변수에는 `-onPreferencesChanged` 콜백이 포함됩니다. 이 함수는 사용자가 기본 설정을 변경할 때마다 호출됩니다. `value` 매개 변수에는 사용자의 현재 기본 설정을 나타내는 문자열이 포함됩니다. 그런 다음, 이 문자열은 해당 사용자에 대해 호스트 애플리케이션에서 저장됩니다.

```typescript
const options = {
    onPreferencesChanged: (value: string) => {
        // Store user preferences here
    }
};

ImmersiveReader.launchAsync(YOUR_TOKEN, YOUR_SUBDOMAIN, YOUR_DATA, options);
```

## <a name="how-to-load-user-preferences-into-the-immersive-reader"></a>사용자 기본 설정을 몰입형 리더에 로드하는 방법

`-preferences` 옵션을 사용하여 사용자의 기본 설정 몰입형 리더에 전달합니다. 사용자의 기본 설정을 저장하고 로드하는 간단한 예제는 다음과 같습니다.

```typescript
const storedUserPreferences = localStorage.getItem("USER_PREFERENCES");
let userPreferences = storedUserPreferences === null ? null : storedUserPreferences;
const options = {
    preferences: userPreferences,
    onPreferencesChanged: (value: string) => {
        userPreferences = value;
        localStorage.setItem("USER_PREFERENCES", userPreferences);
    }
};
```

## <a name="next-steps"></a>다음 단계

* [몰입형 리더 SDK 참조](./reference.md) 살펴보기