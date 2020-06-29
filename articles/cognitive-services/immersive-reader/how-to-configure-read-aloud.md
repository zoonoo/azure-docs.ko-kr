---
title: 소리내어 읽기 구성
titleSuffix: Azure Cognitive Services
description: 이 문서에서는 다양 한 읽기 옵션을 구성 하는 방법을 보여 줍니다.
author: metanMSFT
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: conceptual
ms.date: 06/29/2020
ms.author: metan
ms.openlocfilehash: 0705977c04afe742718a96aebbac31e78dc5815f
ms.sourcegitcommit: 1d9f7368fa3dadedcc133e175e5a4ede003a8413
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/27/2020
ms.locfileid: "85486967"
---
# <a name="how-to-configure-read-aloud"></a>소리내어 읽기를 구성 하는 방법

이 문서에서는 몰입 형 판독기에서 소리내어 읽기를 위한 다양 한 옵션을 구성 하는 방법을 보여 줍니다.

## <a name="automatically-start-read-aloud"></a>자동으로 소리내어 읽기 시작

`options`매개 변수는 소리내어 읽기를 구성 하는 데 사용할 수 있는 모든 플래그를 포함 합니다. `autoplay` `true` 몰입 형 판독기를 시작한 후에 자동으로 읽기를 자동으로 시작할 수 있도록 하려면로 설정 합니다.

```typescript
const options = {
    readAloudOptions: {
        autoplay: true
    }
};

ImmersiveReader.launchAsync(YOUR_TOKEN, YOUR_SUBDOMAIN, YOUR_DATA, options);
```

> [!NOTE]
> 브라우저 제한으로 인해 Safari에서 자동 재생이 지원 되지 않습니다.

## <a name="configure-the-voice"></a>음성 구성

`voice`또는로 설정 `male` `female` 합니다. 모든 언어가 두 음성을 모두 지 원하는 것은 아닙니다. 자세한 내용은 [언어 지원](./language-support.md) 페이지를 참조 하세요.

```typescript
const options = {
    readAloudOptions: {
        voice: 'female'
    }
};
```

## <a name="configure-playback-speed"></a>재생 속도 구성

`speed` `0.5` (50%) 사이의 숫자로 설정 합니다. 및 `2.5` (250%) 포함. 이 범위를 벗어나는 값은 0.5 또는 2.5로 고정 됩니다.

```typescript
const options = {
    readAloudOptions: {
        speed: 1.5
    }
};
```

## <a name="next-steps"></a>다음 단계

* [몰입형 리더 SDK 참조](./reference.md) 살펴보기