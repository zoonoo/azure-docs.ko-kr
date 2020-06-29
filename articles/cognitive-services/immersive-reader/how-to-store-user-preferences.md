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
ms.author: metan
ms.openlocfilehash: beb053551dc1fa28672c488b31dfb29ca3b53651
ms.sourcegitcommit: 1d9f7368fa3dadedcc133e175e5a4ede003a8413
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/27/2020
ms.locfileid: "85486964"
---
# <a name="how-to-store-user-preferences"></a>사용자 기본 설정을 저장 하는 방법

이 문서에서는 사용자의 기본 설정을 저장 하는 방법을 보여 줍니다. 이 기능은 쿠키를 사용 하는 것이 바람직하지 않거나 적절 하지 않은 경우에 사용자의 기본 설정을 저장 하는 또 다른 방법으로 사용 됩니다.

## <a name="how-to-enable-storing-user-preferences"></a>사용자 기본 설정 저장을 사용 하도록 설정 하는 방법

`options`매개 변수는 콜백을 포함 합니다 `onPreferencesChanged` . 이 함수는 사용자가 기본 설정을 변경할 때마다 호출 됩니다. 예를 들어 텍스트 크기, 테마 색, 글꼴 등을 변경할 수 있습니다. `value`매개 변수는 사용자의 현재 기본 설정을 나타내는 문자열을 포함 합니다. 이 문자열은 선호 하는 메커니즘을 사용 하 여 저장할 수 있습니다.

```typescript
const options = {
    onPreferencesChanged: (value: string) => {
        // Store user preferences here
    }
};

ImmersiveReader.launchAsync(YOUR_TOKEN, YOUR_SUBDOMAIN, YOUR_DATA, options);
```

## <a name="how-to-load-user-preferences-into-the-immersive-reader"></a>사용자 기본 설정을 몰입 형 판독기로 로드 하는 방법

매개 변수를 사용 하 여 사용자의 기본 설정을 몰입 형 판독기에 전달 합니다 `preferences` . 사용자 기본 설정을 저장 하 고 로드 하는 간단한 예는 다음과 같습니다.

```typescript
let userPreferences = null;
const options = {
    preferences: userPreferences,
    onPreferencesChanged: (value: string) => {
        userPreferences = value;
    }
};
```

## <a name="next-steps"></a>다음 단계

* [몰입형 리더 SDK 참조](./reference.md) 살펴보기