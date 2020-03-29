---
title: 몰입형 리더 버튼 사용자 지정
titleSuffix: Azure Cognitive Services
description: 이 문서에서는 몰입형 Reader를 실행하는 단추를 사용자 지정하는 방법을 보여 줄 것입니다.
services: cognitive-services
author: metanMSFT
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: conceptual
ms.date: 01/14/2020
ms.author: metan
ms.openlocfilehash: 6461cc60a237cf834226b07db5e1ea6fbbea7a92
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "75946301"
---
# <a name="how-to-customize-the-immersive-reader-button"></a>몰입형 리더 버튼을 사용자 지정하는 방법

이 문서에서는 응용 프로그램의 요구에 맞게 몰입형 Reader를 실행하는 단추를 사용자 지정하는 방법을 보여 줍니다.

## <a name="add-the-immersive-reader-button"></a>몰입형 리더 버튼 추가

몰입형 리더 SDK는 몰입형 리더를 실행해제하는 단추에 대한 기본 스타일을 제공합니다. 클래스 `immersive-reader-button` 특성을 사용하여 이 스타일을 활성화합니다.

```html
<div class='immersive-reader-button'></div>
```

## <a name="customize-the-button-style"></a>버튼 스타일 사용자 지정

`data-button-style` 속성을 사용하여 단추의 스타일을 설정합니다. 허용된 `icon`값은 `text`" `iconAndText`및 기본값은 `icon`입니다.

### <a name="icon-button"></a>아이콘 버튼

```html
<div class='immersive-reader-button' data-button-style='icon'></div>
```

이렇게 하면 다음이 렌더링됩니다.

![아이콘 버튼](./media/button-icon.png)

### <a name="text-button"></a>텍스트 버튼

```html
<div class='immersive-reader-button' data-button-style='text'></div>
```

이렇게 하면 다음이 렌더링됩니다.

![아이콘 버튼](./media/button-text.png)

### <a name="icon-and-text-button"></a>아이콘 및 텍스트 버튼

```html
<div class='immersive-reader-button' data-button-style='iconAndText'></div>
```

이렇게 하면 다음이 렌더링됩니다.

![아이콘 버튼](./media/button-icon-and-text.png)

## <a name="customize-the-button-text"></a>단추 텍스트 사용자 지정

특성을 사용하여 단추의 언어 및 `data-locale` 대체 텍스트를 구성합니다. 기본 언어는 한국어(Korean)입니다.

```html
<div class='immersive-reader-button' data-locale='fr-FR'></div>
```

## <a name="customize-the-size-of-the-icon"></a>아이콘 의 크기 사용자 지정

몰입형 판독기 아이콘의 크기는 특성을 `data-icon-px-size` 사용하여 구성할 수 있습니다. 이렇게 하면 아이콘의 크기가 픽셀 단위로 설정됩니다. 기본 크기는 20px입니다.

```html
<div class='immersive-reader-button' data-icon-px-size='50'></div>
```

## <a name="next-steps"></a>다음 단계

* [몰입형 리더 SDK 참조](./reference.md) 살펴보기