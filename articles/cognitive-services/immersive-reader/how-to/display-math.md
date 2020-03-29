---
title: 몰입형 리더에 수학 표시
titleSuffix: Azure Cognitive Services
description: 이 문서에서는 몰입형 리더에서 수학을 표시하는 방법을 보여 줄 것입니다.
author: pasta
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: conceptual
ms.date: 01/14/2020
ms.author: pasta
ms.openlocfilehash: e01cc9e8cedb6c38da0b56e04419c706d5d0566e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "75946285"
---
# <a name="how-to-display-math-in-the-immersive-reader"></a>몰입 형 리더에 수학을 표시하는 방법

몰입 리더는 수학 마크 업 언어[(MathML)의](https://developer.mozilla.org/docs/Web/MathML)형태로 제공 될 때 수학을 표시 할 수 있습니다.
MIME 유형은 몰입형 판독기 [청크를](../reference.md#chunk)통해 설정할 수 있습니다. 자세한 내용은 [지원되는 MIME 유형을](../reference.md#supported-mime-types) 참조하십시오.

## <a name="send-math-to-the-immersive-reader"></a>몰입형 리더에게 수학 보내기
몰입형 판독기로 수학을 보내려면 MathML이 포함된 청크를 제공하고 MIME ```application/mathml+xml```형식을 로 설정합니다.

예를 들어 콘텐츠가 다음과 같은 경우:

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

그런 다음 다음 자바 스크립트를 사용하여 콘텐츠를 표시 할 수 있습니다.

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

몰입형 리더를 실행하면 다음이 표시됩니다.

![몰입형 리더의 수학](../media/how-tos/1-math.png)

## <a name="next-steps"></a>다음 단계

* [몰입형 판독기 SDK](https://github.com/microsoft/immersive-reader-sdk) 및 [몰입형 판독기 SDK 참조](../reference.md) 살펴보기