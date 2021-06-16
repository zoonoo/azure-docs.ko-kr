---
title: 몰입형 리더에 수식 표시
titleSuffix: Azure Applied AI Services
description: 이 문서에서는 몰입형 리더에 수식을 표시하는 방법을 보여줍니다.
author: nitinme
manager: guillasi
ms.service: applied-ai-services
ms.subservice: immersive-reader
ms.topic: conceptual
ms.date: 01/14/2020
ms.author: nitinme
ms.custom: devx-track-js
ms.openlocfilehash: 0d4a0237d502cb353c262a40c92f99dfdbb8e7d6
ms.sourcegitcommit: 34feb2a5bdba1351d9fc375c46e62aa40bbd5a1f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/10/2021
ms.locfileid: "111888179"
---
# <a name="how-to-display-math-in-the-immersive-reader"></a>몰입형 리더에 수식을 표시하는 방법

[MathML](https://developer.mozilla.org/docs/Web/MathML)(Mathematical Markup Language) 형식으로 제공될 때 몰입형 리더에서 수식을 표시할 수 있습니다.
MIME 형식은 몰입형 리더 [청크](../reference.md#chunk)를 통해 설정할 수 있습니다. 자세한 내용은 [지원되는 MIME 형식](../reference.md#supported-mime-types)을 참조하세요.

## <a name="send-math-to-the-immersive-reader"></a>몰입형 리더로 수식 보내기
몰입형 리더로 수식을 보내기 위해서 MathML을 포함하는 청크를 제공하고 MIME 형식을 ```application/mathml+xml```로 설정합니다.

예를 들어 콘텐츠가 다음과 같은 경우입니다.

```html
<div id='ir-content'>
    <math xmlns='http://www.w3.org/1998/Math/MathML'>
        <mfrac>
            <mrow>
                <msup>
                    <mi>x</mi>
                    <mn>2</mn>
                </msup>
                <mo>+</mo>
                <mn>3</mn>
                <mi>x</mi>
                <mo>+</mo>
                <mn>2</mn>
            </mrow>
            <mrow>
                <mi>x</mi>
                <mo>−</mo>
                <mn>3</mn>
            </mrow>
        </mfrac>
        <mo>=</mo>
        <mn>4</mn>
    </math>
</div>
```

그러면 다음 JavaScript를 사용하여 콘텐츠를 표시할 수 있습니다.

```javascript
const data = {
    title: 'My Math',
    chunks: [{
        content: document.getElementById('ir-content').innerHTML.trim(),
        mimeType: 'application/mathml+xml'
    }]
};

ImmersiveReader.launchAsync(YOUR_TOKEN, YOUR_SUBDOMAIN, data, YOUR_OPTIONS);
```

몰입형 리더를 시작하면 다음과 같이 표시됩니다.

![몰입형 리더의 수식](../media/how-tos/1-math.png)

## <a name="next-steps"></a>다음 단계

* [몰입형 판독기 SDK](https://github.com/microsoft/immersive-reader-sdk) 및 [몰입형 판독기 SDK 참조](../reference.md) 살펴보기