---
title: 몰입 형 판독기 SDK 참조
titlesuffix: Azure Cognitive Services
description: 몰입 형 판독기 SDK에 대 한 참조
services: cognitive-services
author: metanMSFT
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: reference
ms.date: 06/20/2019
ms.author: metan
ms.openlocfilehash: c128608b3c4a8e1155c3ac962bcfd07f589fbf23
ms.sourcegitcommit: 5cb0b6645bd5dff9c1a4324793df3fdd776225e4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/21/2019
ms.locfileid: "67311795"
---
# <a name="immersive-reader-sdk-reference"></a>몰입 형 판독기 SDK 참조

몰입 형 판독기 SDK에는 몰입 형 판독기를 웹 응용 프로그램에 통합할 수 있는 JavaScript 라이브러리입니다.

## <a name="functions"></a>Functions

SDK는 단일 함수로 노출 `ImmersiveReader.launchAsync(token, resourceName, content, options)`합니다.

### <a name="launchasync"></a>launchAsync

몰입 형 판독기 내에서 시작을 `iframe` 웹 응용 프로그램에서 합니다.

```typescript
launchAsync(token: string, resourceName: string, content: Content, options?: Options): Promise<HTMLDivElement>;
```

#### <a name="parameters"></a>매개 변수

| 이름 | 형식 | 설명 |
| ---- | ---- |------------ |
| `token` | 문자열 | 에 대 한 호출에서 얻은 액세스 토큰을 `issueToken` 끝점입니다. |
| `resourceName` | 문자열 | 예약되어 있습니다. `null`로 설정해야 합니다. |
| `content` | [콘텐츠](#content) | 몰입 형 판독기에 표시할 콘텐츠를 포함 하는 개체입니다. |
| `options` | [옵션](#options) | 몰입 형 판독기의 특정 동작을 구성 하기 위한 옵션입니다. 선택 사항입니다. |

#### <a name="returns"></a>반환 값

반환 된 `Promise<HTMLDivElement>` 몰입 형 판독기를 로드할 때를 확인 하는 합니다. `Promise` 로 확인 되는 `div` 만 해당 자식 요소는 `iframe` 몰입 형 판독기 페이지를 포함 하는 요소입니다.

#### <a name="exceptions"></a>예외

반환 된 `Promise` 거부 되며를 [ `Error` ](#error) 개체 몰입 형 판독기 로드 되지 않습니다. 자세한 내용은 참조는 [오류 코드](#error-codes)합니다.

## <a name="types"></a>유형

### <a name="content"></a>Content

몰입 형 판독기에 표시할 콘텐츠를 포함 합니다.

```typescript
{
    title?: string;      // Title text shown at the top of the Immersive Reader (optional)
    chunks: [ {          // Array of chunks
        content: string; // Plain text string
        lang?: string;   // Language of the text, e.g. en, es-ES (optional). Language will be detected automatically if not specified.
        mimeType?: string; // MIME type of the content (optional). Defaults to 'text/plain' if not specified.
    } ];
}
```

#### <a name="supported-mime-types"></a>지원 되는 MIME 형식

| MIME 형식 | 설명 |
| --------- | ----------- |
| 텍스트/일반 | 일반 텍스트입니다. |
| application/mathml+xml | 수학 Markup Language (MathML)입니다. [자세히 알아보기](https://developer.mozilla.org/en-US/docs/Web/MathML).

### <a name="options"></a>옵션

몰입 형 판독기의 특정 동작을 구성 하는 속성을 포함 합니다.

```typescript
{
    uiLang?: string;   // Language of the UI, e.g. en, es-ES (optional). Defaults to browser language if not specified.
    timeout?: number;  // Duration (in milliseconds) before launchAsync fails with a timeout error (default is 15000 ms).
    uiZIndex?: number; // Z-index of the iframe that will be created (default is 1000)
    useWebview?: boolean; // Use a webview tag instead of an iframe, for compatibility with Chrome Apps (default is false).
}
```

### <a name="error"></a>오류

오류에 대 한 정보를 포함합니다.

```typescript
{
    code: string;    // One of a set of error codes
    message: string; // Human-readable representation of the error
}
```

#### <a name="error-codes"></a>오류 코드

| 코드 | 설명 |
| ---- | ----------- |
| BadArgument | 제공 된 인수가 잘못 되었습니다. 내용은 `message` 세부 정보에 대 한 합니다. |
| 시간 제한 | 몰입 형 판독기를 지정된 된 시간 제한 내에서 로드 하지 못했습니다. |
| TokenExpired| 제공 된 토큰이 만료 되었습니다. |

## <a name="launching-the-immersive-reader"></a>몰입 형 판독기를 시작합니다.

SDK는 몰입 형 판독기를 시작 하는 단추에 대 한 기본 스타일을 제공 합니다. 사용 된 `immersive-reader-button` 클래스 특성을이 스타일을 사용 합니다.

```html
<div class='immersive-reader-button'></div>
```

### <a name="optional-attributes"></a>선택적 특성

단추의 모양과 느낌을 구성 하려면 다음 특성을 사용 합니다.

| 특성 | 설명 |
| --------- | ----------- |
| `data-button-style` | 단추 스타일을 설정합니다. `icon`, `text` 또는 `iconAndText`일 수 있습니다. 기본값은 `icon`입니다. |
| `data-locale` | 설정 하는 예를 들어 `en-US`, `fr-FR`합니다. 영어 기본값은입니다. |
| `data-icon-px-size` | 아이콘의 크기를 픽셀 단위로 설정합니다. 20px 기본값은입니다. |

## <a name="browser-support"></a>브라우저 지원

몰입 형 판독기를 사용 하 여 최상의 환경을 위해 다음 브라우저의 최신 버전을 사용 합니다.

* Microsoft Edge
* Internet Explorer 11
* Google Chrome
* Mozilla Firefox
* Apple Safari

## <a name="next-steps"></a>다음 단계

* 탐색을 [몰입 형 판독기 github SDK](https://github.com/Microsoft/immersive-reader-sdk)
* [빠른 시작: 몰입 형 판독기를 시작 하는 웹 앱 만들기 (C#)](./quickstart.md)