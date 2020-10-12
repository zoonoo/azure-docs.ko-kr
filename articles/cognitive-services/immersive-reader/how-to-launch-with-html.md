---
title: HTML 콘텐츠를 사용하여 몰입형 판독기 시작
titleSuffix: Azure Cognitive Services
description: 이 문서에서는 HTML 콘텐츠가 있는 몰입 형 판독기를 시작 하는 방법을 보여 줍니다.
author: metanMSFT
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: conceptual
ms.date: 01/14/2020
ms.author: metan
ms.custom: devx-track-js
ms.openlocfilehash: f7f55d1d349fc7d02c2ac53f7a50f9d649688cbd
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91326765"
---
# <a name="how-to-launch-the-immersive-reader-with-html-content"></a>HTML 콘텐츠를 사용 하 여 몰입 형 판독기를 시작 하는 방법

이 문서에서는 HTML 콘텐츠가 있는 몰입 형 판독기를 시작 하는 방법을 보여 줍니다.

## <a name="prepare-the-html-content"></a>HTML 콘텐츠 준비

컨테이너 요소 내에서 몰입 형 판독기에 렌더링 하려는 콘텐츠를 저장 합니다. Container 요소에 고유한가 있어야 `id` 합니다. 몰입 형 판독기는 기본 HTML 요소에 대 한 지원을 제공 합니다. 자세한 내용은 [참조](./reference.md#html-support) 를 참조 하세요.

```html
<div id='immersive-reader-content'>
    <b>Bold</b>
    <i>Italic</i>
    <u>Underline</u>
    <strike>Strikethrough</strike>
    <code>Code</code>
    <sup>Superscript</sup>
    <sub>Subscript</sub>
    <ul><li>Unordered lists</li></ul>
    <ol><li>Ordered lists</li></ol>
</div>
```

## <a name="get-the-html-content-in-javascript"></a>JavaScript에서 HTML 콘텐츠 가져오기

`id`컨테이너 요소의를 사용 하 여 JavaScript 코드에서 HTML 콘텐츠를 가져옵니다.

```javascript
const htmlContent = document.getElementById('immersive-reader-content').innerHTML;
```

## <a name="launch-the-immersive-reader-with-your-html-content"></a>HTML 콘텐츠를 사용 하 여 몰입 형 판독기 시작

를 호출 하 `ImmersiveReader.launchAsync` 는 경우 청크의 `mimeType` 속성을로 설정 하 여 HTML 렌더링을 사용 하도록 설정 합니다 `text/html` .

```javascript
const data = {
    chunks: [{
        content: htmlContent,
        mimeType: 'text/html'
    }]
};

ImmersiveReader.launchAsync(YOUR_TOKEN, YOUR_SUBDOMAIN, data, YOUR_OPTIONS);
```

## <a name="next-steps"></a>다음 단계

* [몰입형 리더 SDK 참조](./reference.md) 살펴보기