---
title: 몰입 형 판독기를 위한 HTML 콘텐츠를 준비 하는 방법
titleSuffix: Azure Cognitive Services
description: HTML, JavaScript, Python, Android 또는 iOS를 사용 하 여 몰입 형 판독기를 시작 하는 방법을 알아봅니다. 몰입 형 판독기는 입증 된 기법을 사용 하 여 학습 차이로 인 한 언어 학습자, 새로운 독자 및 학생의 읽기 이해력을 개선 합니다.
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: include
ms.date: 03/04/2021
ms.author: erhopf
ms.openlocfilehash: 40aab9510eb90c405f92be49a3b4c0a3f756c872
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102620168"
---
# <a name="how-to-prepare-html-content-for-immersive-reader"></a>몰입 형 판독기를 위한 HTML 콘텐츠를 준비 하는 방법

이 문서에서는 몰입 형 판독기에서 사용할 수 있도록 HTML을 구조화 하 고 콘텐츠를 검색 하는 방법을 보여 줍니다.

## <a name="prepare-the-html-content"></a>HTML 콘텐츠 준비

컨테이너 요소 내에서 몰입 형 판독기에 렌더링 하려는 콘텐츠를 저장 합니다. Container 요소에 고유한가 있어야 `id` 합니다. 몰입 형 판독기는 기본 HTML 요소에 대 한 지원을 제공 합니다. 자세한 내용은 [참조](reference.md#html-support) 를 참조 하세요.

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

* [몰입형 리더 SDK 참조](reference.md) 살펴보기