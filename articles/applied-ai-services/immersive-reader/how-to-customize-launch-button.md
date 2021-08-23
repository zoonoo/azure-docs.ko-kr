---
title: 몰입형 리더 시작 단추 편집
titleSuffix: Azure Applied AI Services
description: 이 문서에서는 몰입형 리더를 시작하는 단추를 사용자 지정하는 방법을 보여줍니다.
services: cognitive-services
author: metanMSFT
manager: guillasi
ms.service: applied-ai-services
ms.subservice: immersive-reader
ms.topic: how-to
ms.date: 03/08/2021
ms.author: metang
ms.openlocfilehash: 1f5feff0857b98b077ed1e19dbb7aaa82004df69
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122536233"
---
# <a name="how-to-customize-the-immersive-reader-button"></a>몰입형 리더 단추를 사용자 지정하는 방법

이 문서에서는 애플리케이션의 요구 사항에 맞게 몰입형 리더를 시작하는 단추를 사용자 지정하는 방법을 보여줍니다.

## <a name="add-the-immersive-reader-button"></a>몰입형 리더 단추 추가

몰입형 리더 SDK는 몰입형 리더를 시작하는 단추에 대한 기본 스타일을 제공합니다. `immersive-reader-button` 클래스 특성을 사용하여 이 스타일링을 활성화합니다.

```html
<div class='immersive-reader-button'></div>
```

## <a name="customize-the-button-style"></a>단추 스타일 사용자 지정

`data-button-style` 특성을 사용하여 단추의 스타일을 설정합니다. 허용되는 값은 `icon`, `text` 및 `iconAndText`입니다. 기본값은 `icon`입니다.

### <a name="icon-button"></a>아이콘 단추

```html
<div class='immersive-reader-button' data-button-style='icon'></div>
```

그러면 다음이 렌더링됩니다.

![렌더링된 텍스트 단추입니다.](./media/button-icon.png)

### <a name="text-button"></a>텍스트 단추

```html
<div class='immersive-reader-button' data-button-style='text'></div>
```

그러면 다음이 렌더링됩니다.

![이는 렌더링된 몰입형 리더 단추입니다.](./media/button-text.png)

### <a name="icon-and-text-button"></a>아이콘 및 텍스트 단추

```html
<div class='immersive-reader-button' data-button-style='iconAndText'></div>
```

그러면 다음이 렌더링됩니다.

![아이콘 단추](./media/button-icon-and-text.png)

## <a name="customize-the-button-text"></a>단추 텍스트 사용자 지정

`data-locale` 특성을 사용하여 단추의 언어 및 대체 텍스트를 구성합니다. 기본 언어는 한국어(Korean)입니다.

```html
<div class='immersive-reader-button' data-locale='fr-FR'></div>
```

## <a name="customize-the-size-of-the-icon"></a>아이콘 크기 사용자 지정

몰입형 리더 아이콘의 크기는 `data-icon-px-size` 특성을 사용하여 구성할 수 있습니다. 아이콘의 크기(픽셀 단위)를 설정합니다. 기본 크기는 20px입니다.

```html
<div class='immersive-reader-button' data-icon-px-size='50'></div>
```

## <a name="next-steps"></a>다음 단계

* [몰입형 리더 SDK 참조](./reference.md) 살펴보기