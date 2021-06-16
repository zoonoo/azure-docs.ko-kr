---
title: 번역 구성
titleSuffix: Azure Applied AI Services
description: 이 문서에서는 다양한 번역 옵션을 구성하는 방법을 보여줍니다.
author: metanMSFT
manager: guillasi
ms.service: applied-ai-services
ms.subservice: immersive-reader
ms.topic: how-to
ms.date: 06/29/2020
ms.author: metang
ms.openlocfilehash: e8fb21ac286997601ae8896616826651bad8b1f7
ms.sourcegitcommit: 34feb2a5bdba1351d9fc375c46e62aa40bbd5a1f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/10/2021
ms.locfileid: "111893411"
---
# <a name="how-to-configure-translation"></a>번역을 구성하는 방법

이 문서에서는 몰입형 리더에서 다양한 번역 옵션을 구성하는 방법을 보여줍니다.

## <a name="configure-translation-language"></a>번역 언어 구성

`options` 매개 변수에는 번역을 구성하는 데 사용할 수 있는 모든 플래그가 포함됩니다. 번역할 언어로 `language` 매개 변수를 설정합니다. 지원되는 언어의 전체 목록은 [언어 지원](./language-support.md)을 참조하세요.

```typescript
const options = {
    translationOptions: {
        language: 'fr-FR'
    }
};

ImmersiveReader.launchAsync(YOUR_TOKEN, YOUR_SUBDOMAIN, YOUR_DATA, options);
```

## <a name="automatically-translate-the-document-on-load"></a>로드 시 자동으로 문서 번역

`autoEnableDocumentTranslation`을 `true`로 설정하여 몰입형 리더가 로드될 때 전체 문서를 자동으로 변환할 수 있도록 합니다.

```typescript
const options = {
    translationOptions: {
        autoEnableDocumentTranslation: true
    }
};
```

## <a name="automatically-enable-word-translation"></a>자동으로 단어 번역 사용

`autoEnableWordTranslation`을 `true`로 설정하여 단일 단어 번역을 활성화합니다.

```typescript
const options = {
    translationOptions: {
        autoEnableWordTranslation: true
    }
};
```

## <a name="next-steps"></a>다음 단계

* [몰입형 리더 SDK 참조](./reference.md) 살펴보기