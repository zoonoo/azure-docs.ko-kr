---
title: 사용자 기본 설정 저장
titleSuffix: Azure Cognitive Services
description: 이 문서에서는 사용자의 기본 설정을 저장 하는 방법을 보여 줍니다.
author: metanMSFT
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: conceptual
ms.date: 06/29/2020
ms.author: metang
ms.openlocfilehash: 31c1ef8d75b4c12e4dd6a360852feb27857ac412
ms.sourcegitcommit: fb3c846de147cc2e3515cd8219d8c84790e3a442
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/27/2020
ms.locfileid: "92636547"
---
# <a name="how-to-store-user-preferences"></a>사용자 기본 설정을 저장 하는 방법

이 문서에서는 [-기본](./reference.md#options) 설정 및 [-OnPreferencesChanged](./reference.md#options) 몰입 형 판독기 SDK 옵션을 통해 사용자가 **공식적으로 알려진 사용자의** UI 설정을 저장 하는 방법을 보여 줍니다.

[CookiePolicy](./reference.md#cookiepolicy-options) SDK 옵션이 *Enabled* 로 설정 된 경우 몰입 형 판독기 응용 프로그램은 특정 브라우저 및 장치에 로컬인 쿠키에 **사용자 기본 설정** (텍스트 크기, 테마 색, 글꼴 등)을 저장 합니다. 사용자는 동일한 브라우저와 장치에서 몰입 형 판독기를 시작할 때마다 해당 장치에 대 한 마지막 세션에서 사용자의 기본 설정을 사용 하 여 열립니다. 그러나 사용자가 다른 브라우저 또는 장치에서 몰입 형 판독기를 열 경우 처음에는 몰입 형 판독기의 기본 설정으로 설정이 구성 되며 사용자가 사용 하는 각 장치에 대해 해당 기본 설정을 다시 설정 해야 합니다. `-preferences`및 몰입 형 판독기 SDK 옵션을 사용 하면 응용 프로그램에서 사용자가 `-onPreferencesChanged` 응용 프로그램을 사용 하는 모든 위치에 일관 된 환경을 제공할 수 있도록 다양 한 브라우저 및 장치에서 사용자의 기본 설정을 로밍할 수 있습니다.

첫 번째는 몰입 형 판독기 `-onPreferencesChanged` 응용 프로그램을 시작할 때 CALLBACK SDK 옵션을 제공 하 여 `-preferences` 사용자가 몰입 형 판독기 세션 중에 기본 설정을 변경할 때마다 몰입 형 판독기가 다시 호스트 응용 프로그램에 문자열을 보냅니다. 그런 다음 호스트 응용 프로그램은 사용자 기본 설정을 자신의 시스템에 저장 합니다. 그런 다음 동일한 사용자가 몰입 형 판독기를 다시 시작 하면 호스트 응용 프로그램이 저장소에서 해당 사용자의 기본 설정을 검색 하 고 `-preferences` 사용자의 기본 설정이 복원 되도록 몰입 형 판독기 응용 프로그램을 시작할 때 문자열 SDK 옵션으로 제공할 수 있습니다.

이 기능은 쿠키를 사용 하는 것이 바람직하지 않거나 적절 하지 않은 경우 **사용자 기본 설정을** 저장 하는 또 다른 방법으로 사용할 수 있습니다.

> [!CAUTION]
> **중요** 몰입 형 판독기 응용 프로그램에서 전송 된 문자열의 값을 프로그래밍 방식으로 변경 하지 마십시오 `-preferences` .이로 인해 예기치 않은 동작이 발생할 수 있으므로 고객에 대 한 사용자 환경이 저하 될 수 있습니다. 호스트 응용 프로그램은 사용자 지정 값을 할당 하거나 문자열을 조작 하면 안 됩니다 `-preferences` . 문자열 옵션을 사용 하는 경우 `-preferences` 콜백 옵션에서 반환 된 정확한 값만 사용 합니다 `-onPreferencesChanged` .

## <a name="how-to-enable-storing-user-preferences"></a>사용자 기본 설정 저장을 사용 하도록 설정 하는 방법

몰입 형 판독기 SDK [launchAsync](./reference.md#launchasync) `options` 매개 변수에는 콜백이 포함 되어 있습니다 `-onPreferencesChanged` . 이 함수는 사용자가 기본 설정을 변경할 때마다 호출 됩니다. `value`매개 변수는 사용자의 현재 기본 설정을 나타내는 문자열을 포함 합니다. 그런 다음 호스트 응용 프로그램에서 해당 사용자에 대해이 문자열을 저장 합니다.

```typescript
const options = {
    onPreferencesChanged: (value: string) => {
        // Store user preferences here
    }
};

ImmersiveReader.launchAsync(YOUR_TOKEN, YOUR_SUBDOMAIN, YOUR_DATA, options);
```

## <a name="how-to-load-user-preferences-into-the-immersive-reader"></a>사용자 기본 설정을 몰입 형 판독기로 로드 하는 방법

옵션을 사용 하 여 사용자의 기본 설정을 몰입 형 판독기에 전달 합니다 `-preferences` . 사용자 기본 설정을 저장 하 고 로드 하는 간단한 예는 다음과 같습니다.

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