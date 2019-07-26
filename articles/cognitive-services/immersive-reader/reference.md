---
title: 몰입 형 판독기 SDK 참조
titleSuffix: Azure Cognitive Services
description: 몰입 형 판독기 SDK에 대 한 참조
services: cognitive-services
author: metanMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: reference
ms.date: 06/20/2019
ms.author: metan
ms.openlocfilehash: 22860e0798ec852b6d25da27e108befe8a3089a2
ms.sourcegitcommit: bafb70af41ad1326adf3b7f8db50493e20a64926
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/25/2019
ms.locfileid: "68488826"
---
# <a name="immersive-reader-sdk-reference"></a>몰입 형 판독기 SDK 참조

몰입 형 판독기 SDK는 몰입 형 판독기를 웹 응용 프로그램에 통합할 수 있는 JavaScript 라이브러리입니다.

## <a name="functions"></a>함수

SDK는 단일 함수인를 `ImmersiveReader.launchAsync(token, subdomain, content, options)`노출 합니다.

### <a name="launchasync"></a>launchAsync

웹 응용 프로그램 `iframe` 의 내에서 몰입 형 판독기를 시작 합니다.

```typescript
launchAsync(token: string, subdomain: string, content: Content, options?: Options): Promise<HTMLDivElement>;
```

#### <a name="parameters"></a>매개 변수

| 이름 | 형식 | 설명 |
| ---- | ---- |------------ |
| `token` | string | Azure AD 인증 토큰입니다. [AZURE AD 인증 방법을](./azure-active-directory-authentication.md)참조 하세요. |
| `subdomain` | string | Azure에서 몰입 형 판독기 리소스의 사용자 지정 하위 도메인입니다. [AZURE AD 인증 방법을](./azure-active-directory-authentication.md)참조 하세요. |
| `content` | [콘텐츠](#content) | 몰입 형 판독기에 표시할 콘텐츠를 포함 하는 개체입니다. |
| `options` | [옵션](#options) | 몰입 형 판독기의 특정 동작을 구성 하기 위한 옵션입니다. 선택 사항입니다. |

#### <a name="returns"></a>반환 값

몰입 형 `Promise<HTMLDivElement>` 판독기가 로드 될 때를 확인 하는을 반환 합니다. 는 `Promise` 자식만이 `div` `iframe` 몰입 형 판독기 페이지가 포함 된 요소인 요소로 확인 됩니다.

#### <a name="exceptions"></a>예외

몰입 형 `Promise` 판독기를 로드 하지 못하는 [`Error`](#error) 경우 반환 된는 개체와 함께 거부 됩니다. 자세한 내용은 [오류 코드](#error-codes)를 참조 하세요.

## <a name="types"></a>유형

### <a name="content"></a>콘텐츠

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

| MIME 형식 | Description |
| --------- | ----------- |
| 텍스트/일반 | 일반 텍스트입니다. |
| application/mathml + xml | MathML (수학 Markup Language). [자세히 알아보기](https://developer.mozilla.org/en-US/docs/Web/MathML).

### <a name="options"></a>변수

몰입 형 판독기의 특정 동작을 구성 하는 속성을 포함 합니다.

```typescript
{
    uiLang?: string;   // Language of the UI, e.g. en, es-ES (optional). Defaults to browser language if not specified.
    timeout?: number;  // Duration (in milliseconds) before launchAsync fails with a timeout error (default is 15000 ms).
    uiZIndex?: number; // Z-index of the iframe that will be created (default is 1000)
    useWebview?: boolean; // Use a webview tag instead of an iframe, for compatibility with Chrome Apps (default is false).
}
```

### <a name="error"></a>Error

오류에 대 한 정보를 포함 합니다.

```typescript
{
    code: string;    // One of a set of error codes
    message: string; // Human-readable representation of the error
}
```

#### <a name="error-codes"></a>오류 코드

| 코드 | Description |
| ---- | ----------- |
| BadArgument | 제공 된 인수가 잘못 되었습니다. `message` 자세한 내용은를 참조 하십시오. |
| 제한 시간 | 몰입 형 판독기를 지정 된 시간 제한 내에 로드 하지 못했습니다. |
| TokenExpired| 제공 된 토큰이 만료 되었습니다. |

## <a name="launching-the-immersive-reader"></a>몰입 형 판독기 시작

SDK는 몰입 형 판독기를 시작 하기 위한 단추에 대 한 기본 스타일을 제공 합니다. 이 스타일을 사용 하려면 class특성을사용합니다.`immersive-reader-button`

```html
<div class='immersive-reader-button'></div>
```

### <a name="optional-attributes"></a>선택적 특성

다음 특성을 사용 하 여 단추의 모양과 느낌을 구성할 수 있습니다.

| 특성 | 설명 |
| --------- | ----------- |
| `data-button-style` | 단추의 스타일을 설정 합니다. `icon`, `text` 또는 `iconAndText`일 수 있습니다. 기본값은 `icon`입니다. |
| `data-locale` | 로캘을 설정 합니다 `en-US` `fr-FR`(예:). 기본값은 영어입니다. |
| `data-icon-px-size` | 아이콘의 크기 (픽셀)를 설정 합니다. 기본값은 20px입니다. |

## <a name="browser-support"></a>브라우저 지원

몰입 형 판독기를 사용 하 여 최상의 환경을 제공 하려면 다음 브라우저의 최신 버전을 사용 합니다.

* Microsoft Edge
* Internet Explorer 11
* Google Chrome
* Mozilla Firefox
* Apple Safari

## <a name="next-steps"></a>다음 단계

* [GitHub의 몰입형 리더 SDK](https://github.com/Microsoft/immersive-reader-sdk) 탐색
* [빠른 시작: 몰입 형 판독기를 시작 하는 웹 앱 만들기C#()](./quickstart.md)