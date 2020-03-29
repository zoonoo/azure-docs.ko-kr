---
title: HTML 콘텐츠를 사용하여 몰입형 판독기 시작
titleSuffix: Azure Cognitive Services
description: 이 문서에서는 HTML 콘텐츠가 있는 몰입형 리더를 시작하는 방법을 설명합니다.
author: metanMSFT
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: conceptual
ms.date: 01/14/2020
ms.author: metan
ms.openlocfilehash: bc7ab46113e1b819fc71a9f6e8a18400f8acfbef
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "75946307"
---
# <a name="how-to-launch-the-immersive-reader-with-html-content"></a>HTML 콘텐츠와 몰입 리더를 시작 하는 방법

이 문서에서는 HTML 콘텐츠로 몰입형 Reader를 시작하는 방법을 보여 줍니다.

## <a name="prepare-the-html-content"></a>HTML 콘텐츠 준비

컨테이너 요소 내부에 몰입형 판독기에서 렌더링할 콘텐츠를 배치합니다. 컨테이너 요소에 고유한 `id`이 항목이 있는지 확인합니다. 몰입형 리더는 기본 HTML 요소에 대한 지원을 제공하며 자세한 내용은 [참조를](./reference.md#html-support) 참조하십시오.

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

## <a name="get-the-html-content-in-javascript"></a>자바 스크립트에서 HTML 콘텐츠 받기

`id` 컨테이너 요소를 사용하여 JavaScript 코드에서 HTML 콘텐츠를 가져옵니다.

```javascript
const htmlContent = document.getElementById('immersive-reader-content').innerHTML;
```

## <a name="launch-the-immersive-reader-with-your-html-content"></a>HTML 콘텐츠로 몰입형 리더 시작

호출 `ImmersiveReader.launchAsync`할 때 DC `mimeType` 렌더링을 `text/html` 사용하도록 청크의 속성을 설정합니다.

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