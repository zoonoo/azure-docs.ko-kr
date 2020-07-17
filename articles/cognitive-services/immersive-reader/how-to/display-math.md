---
title: 몰입 형 판독기에 수학 표시
titleSuffix: Azure Cognitive Services
description: 이 문서에서는 몰입 형 판독기에 수치를 표시 하는 방법을 보여 줍니다.
author: pasta
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: conceptual
ms.date: 01/14/2020
ms.author: pasta
ms.openlocfilehash: e01cc9e8cedb6c38da0b56e04419c706d5d0566e
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/29/2020
ms.locfileid: "75946285"
---
# <a name="how-to-display-math-in-the-immersive-reader"></a>몰입 형 판독기에서 수학을 표시 하는 방법

[MathML](https://developer.mozilla.org/docs/Web/MathML)(수학 Markup Language) 형식으로 제공 된 경우 몰입 형 판독기에서 수학을 표시할 수 있습니다.
MIME 형식은 몰입 형 판독기 [청크](../reference.md#chunk)를 통해 설정할 수 있습니다. 자세한 내용은 [지원 되는 MIME 형식](../reference.md#supported-mime-types) 을 참조 하세요.

## <a name="send-math-to-the-immersive-reader"></a>몰입 형 판독기로 수학을 보냅니다.
MathML를 포함 하는 청크를 입력 하 고 MIME 형식을로 ```application/mathml+xml```설정 합니다.

예를 들어 다음과 같은 내용이 있습니다.

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

그러면 다음 JavaScript를 사용 하 여 콘텐츠를 표시할 수 있습니다.

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

몰입 형 판독기를 시작 하면 다음이 표시 됩니다.

![몰입 형 판독기의 수학](../media/how-tos/1-math.png)

## <a name="next-steps"></a>다음 단계

* [몰입형 판독기 SDK](https://github.com/microsoft/immersive-reader-sdk) 및 [몰입형 판독기 SDK 참조](../reference.md) 살펴보기