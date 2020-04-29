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
ms.openlocfilehash: bc7ab46113e1b819fc71a9f6e8a18400f8acfbef
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/29/2020
ms.locfileid: "75946307"
---
# <a name="how-to-launch-the-immersive-reader-with-html-content"></a>HTML 콘텐츠를 사용 하 여 몰입 형 판독기를 시작 하는 방법

이 문서에서는 HTML 콘텐츠가 있는 몰입 형 판독기를 시작 하는 방법을 보여 줍니다.

## <a name="prepare-the-html-content"></a>HTML 콘텐츠 준비

컨테이너 요소 내에서 몰입 형 판독기에 렌더링 하려는 콘텐츠를 저장 합니다. Container 요소에 고유한 `id`가 있어야 합니다. 몰입 형 판독기는 기본 HTML 요소에 대 한 지원을 제공 합니다. 자세한 내용은 [참조](./reference.md#html-support) 를 참조 하세요.

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

컨테이너 요소의 `id` 를 사용 하 여 JavaScript 코드에서 HTML 콘텐츠를 가져옵니다.

```javascript
const htmlContent = document.getElementById('immersive-reader-content').innerHTML;
```

## <a name="launch-the-immersive-reader-with-your-html-content"></a>HTML 콘텐츠를 사용 하 여 몰입 형 판독기 시작

를 호출 `ImmersiveReader.launchAsync`하는 경우 청크의 `mimeType` 속성을로 `text/html` 설정 하 여 HTML 렌더링을 사용 하도록 설정 합니다.

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