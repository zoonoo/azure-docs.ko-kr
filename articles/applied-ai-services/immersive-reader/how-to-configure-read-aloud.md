---
title: 소리 내어 읽기 구성
titleSuffix: Azure Applied AI Services
description: 이 문서에서는 다양한 소리내어 읽기 옵션을 구성하는 방법을 보여줍니다.
author: metanMSFT
manager: guillasi
ms.service: applied-ai-services
ms.subservice: immersive-reader
ms.topic: how-to
ms.date: 06/29/2020
ms.author: metang
ms.openlocfilehash: 2694525c406a40a4c133eb3199d2222542905ae6
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122536474"
---
# <a name="how-to-configure-read-aloud"></a>소리 내어 읽기를 구성하는 방법

이 문서에서는 몰입형 리더에서 다양한 소리내어 읽기 옵션을 구성하는 방법을 보여줍니다.

## <a name="automatically-start-read-aloud"></a>자동으로 소리내어 읽기 시작

`options` 매개 변수에는 소리내어 읽기를 구성하는 데 사용할 수 있는 모든 플래그가 포함됩니다. 몰입형 리더를 시작한 후에 소리내어 읽기를 자동으로 시작할 수 있도록 `autoplay`를 `true`로 설정합니다.

```typescript
const options = {
    readAloudOptions: {
        autoplay: true
    }
};

ImmersiveReader.launchAsync(YOUR_TOKEN, YOUR_SUBDOMAIN, YOUR_DATA, options);
```

> [!NOTE]
> 브라우저 제한으로 인해 Safari에서는 자동 재생이 지원되지 않습니다.

## <a name="configure-the-voice"></a>음성 구성

`voice`를 `male` 또는 `female`로 설정합니다. 모든 언어가 두 음성을 모두 지원하는 것은 아닙니다. 자세한 내용은 [언어 지원](./language-support.md) 페이지를 참조하세요.

```typescript
const options = {
    readAloudOptions: {
        voice: 'female'
    }
};
```

## <a name="configure-playback-speed"></a>재생 속도 구성

`speed`를 `0.5`(50%) 및 `2.5`(250%) 사이의 숫자로 설정합니다. 이 범위를 벗어나는 값은 0.5 또는 2.5로 고정됩니다.

```typescript
const options = {
    readAloudOptions: {
        speed: 1.5
    }
};
```

## <a name="next-steps"></a>다음 단계

* [몰입형 리더 SDK 참조](./reference.md) 살펴보기