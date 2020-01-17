---
title: 몰입 형 판독기 SDK 참조
titleSuffix: Azure Cognitive Services
description: 몰입 형 판독기 SDK에는 몰입 형 판독기를 응용 프로그램에 통합할 수 있는 JavaScript 라이브러리가 포함 되어 있습니다.
services: cognitive-services
author: metanMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: reference
ms.date: 06/20/2019
ms.author: metan
ms.openlocfilehash: b20a3e6dd3b32b183bbf34dbefd76f0e4cd56b99
ms.sourcegitcommit: 276c1c79b814ecc9d6c1997d92a93d07aed06b84
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/16/2020
ms.locfileid: "76156406"
---
# <a name="immersive-reader-sdk-reference-guide"></a>몰입 형 판독기 SDK 참조 가이드

몰입 형 판독기 SDK에는 몰입 형 판독기를 응용 프로그램에 통합할 수 있는 JavaScript 라이브러리가 포함 되어 있습니다.

## <a name="functions"></a>Functions

SDK는 함수를 노출 합니다.

- [`ImmersiveReader.launchAsync(token, subdomain, content, options)`](#launchasync)

- [`ImmersiveReader.close()`](#close)

- [`ImmersiveReader.renderButtons(options)`](#renderbuttons)

## <a name="launchasync"></a>launchAsync

웹 응용 프로그램의 `iframe` 내에서 몰입 형 판독기를 시작 합니다.

```typescript
launchAsync(token: string, subdomain: string, content: Content, options?: Options): Promise<LaunchResponse>;
```

### <a name="parameters"></a>매개 변수

| 이름 | 유형 | Description |
| ---- | ---- |------------ |
| `token` | 문자열 | Azure AD 인증 토큰입니다. |
| `subdomain` | 문자열 | Azure에서 몰입 형 판독기 리소스의 사용자 지정 하위 도메인입니다. |
| `content` | [콘텐츠](#content) | 몰입 형 판독기에 표시할 콘텐츠를 포함 하는 개체입니다. |
| `options` | [옵션](#options) | 몰입 형 판독기의 특정 동작을 구성 하기 위한 옵션입니다. (선택 사항) |

### <a name="returns"></a>반환

몰입 형 판독기가 로드 될 때 확인 되는 `Promise<LaunchResponse>`를 반환 합니다. `Promise` [`LaunchResponse`](#launchresponse) 개체로 확인 됩니다.

### <a name="exceptions"></a>예외

몰입 형 판독기를 로드 하지 못하는 경우 반환 된 `Promise` [`Error`](#error) 개체와 함께 거부 됩니다. 자세한 내용은 [오류 코드](#error-codes)를 참조 하세요.

## <a name="close"></a>닫기

몰입 형 판독기를 닫습니다.

이 함수에 대 한 사용 사례 예는 [옵션](#options)에 ```hideExitButton: true```를 설정 하 여 종료 단추를 숨기는 경우입니다. 그런 다음 단추를 클릭할 때 다른 단추 (예: 모바일 헤더의 뒤로 화살표)가이 ```close``` 함수를 호출할 수 있습니다.

```typescript
close(): void;
```

## <a name="renderbuttons"></a>renderButtons

이 함수는 문서의 모던 판독기 단추 요소를 스타일 및 업데이트 합니다. ```options.elements``` 제공 되는 경우이 함수는 ```options.elements```내에서 단추를 렌더링 합니다. 그렇지 않으면 ```immersive-reader-button```클래스가 있는 문서 요소 내에서 단추가 렌더링 됩니다.

이 함수는 창이 로드 될 때 SDK에서 자동으로 호출 됩니다.

추가 렌더링 옵션은 [선택적 특성](#optional-attributes) 을 참조 하세요.

```typescript
renderButtons(options?: RenderButtonsOptions): void;
```

### <a name="parameters"></a>매개 변수

| 이름 | 유형 | Description |
| ---- | ---- |------------ |
| `options` | [RenderButtonsOptions](#renderbuttonsoptions) | RenderButtons 함수의 특정 동작을 구성 하는 옵션입니다. (선택 사항) |

## <a name="types"></a>유형

### <a name="content"></a>목차

몰입 형 판독기에 표시할 콘텐츠를 포함 합니다.

```typescript
{
    title?: string;    // Title text shown at the top of the Immersive Reader (optional)
    chunks: Chunk[];   // Array of chunks
}
```

### <a name="chunk"></a>청크

몰입 형 판독기의 콘텐츠로 전달 되는 단일 데이터 청크입니다.

```typescript
{
    content: string;        // Plain text string
    lang?: string;          // Language of the text, e.g. en, es-ES (optional). Language will be detected automatically if not specified.
    mimeType?: string;      // MIME type of the content (optional). Currently 'text/plain', 'application/mathml+xml', and 'text/html' are supported. Defaults to 'text/plain' if not specified.
}
```

### <a name="launchresponse"></a>LaunchResponse

`ImmersiveReader.launchAsync`에 대 한 호출의 응답을 포함 합니다.

```typescript
{
    container: HTMLDivElement;    // HTML element which contains the Immersive Reader iframe
    sessionId: string;            // Globally unique identifier for this session, used for debugging
}
```

### <a name="cookiepolicy-enum"></a>CookiePolicy 열거형

몰입 형 판독기의 쿠키 사용에 대 한 정책을 설정 하는 데 사용 되는 열거형입니다. [옵션](#options)을 참조 하세요.

```typescript
enum CookiePolicy { Disable, Enable }
```

#### <a name="supported-mime-types"></a>지원 되는 MIME 형식

| MIME 형식 | Description |
| --------- | ----------- |
| 텍스트/일반 | 일반 텍스트입니다. |
| 텍스트/html | HTML 콘텐츠입니다. [자세히 알아보기](#html-support)|
| application/mathml + xml | MathML (수학 Markup Language). [자세히 알아보기](./how-to/display-math.md).
| application/vnd. vnd.openxmlformats-officedocument.spreadsheetml.sheet. wordprocessingml | Microsoft Word .docx 형식 문서입니다.

### <a name="html-support"></a>HTML 지원

| HTML | 지원 되는 내용 |
| --------- | ----------- |
| 글꼴 스타일 | 굵게, 기울임꼴, 밑줄, 코드, 취소선, 위 첨자, 아래 첨자 |
| 정렬되지 않은 목록 | 디스크, 원, 사각형 |
| 정렬 된 목록 | 10 진수, 위쪽-알파, 아래쪽 알파, 위쪽 로마, 아래쪽 로마 |
| 하이퍼링크 | 서비스 예정 |

지원 되지 않는 태그는 comparably 렌더링 됩니다. 이미지와 테이블은 현재 지원 되지 않습니다.

### <a name="options"></a>옵션

몰입 형 판독기의 특정 동작을 구성 하는 속성을 포함 합니다.

```typescript
{
    uiLang?: string;           // Language of the UI, e.g. en, es-ES (optional). Defaults to browser language if not specified.
    timeout?: number;          // Duration (in milliseconds) before launchAsync fails with a timeout error (default is 15000 ms).
    uiZIndex?: number;         // Z-index of the iframe that will be created (default is 1000)
    useWebview?: boolean;      // Use a webview tag instead of an iframe, for compatibility with Chrome Apps (default is false).
    onExit?: () => any;        // Executes when the Immersive Reader exits
    customDomain?: string;     // Reserved for internal use. Custom domain where the Immersive Reader webapp is hosted (default is null).
    allowFullscreen?: boolean; // The ability to toggle fullscreen (default is true).
    hideExitButton?: boolean;  // Whether or not to hide the Immersive Reader's exit button arrow (default is false). This should only be true if there is an alternative mechanism provided to exit the Immersive Reader (e.g a mobile toolbar's back arrow).
    cookiePolicy?: CookiePolicy; // Setting for the Immersive Reader's cookie usage (default is CookiePolicy.Disable). It's the responsibility of the host application to obtain any necessary user consent in accordance with EU Cookie Compliance Policy.
}
```

### <a name="renderbuttonsoptions"></a>RenderButtonsOptions

몰입 형 판독기 단추를 렌더링 하는 옵션입니다.

```typescript
{
    elements: HTMLDivElement[];    // Elements to render the Immersive Reader buttons in
}
```

### <a name="error"></a>오류

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
| BadArgument | 제공 된 인수가 잘못 되었습니다. 자세한 내용은 `message`를 참조 하십시오. |
| 시간 제한 | 몰입 형 판독기를 지정 된 시간 제한 내에 로드 하지 못했습니다. |
| TokenExpired | 제공 된 토큰이 만료 되었습니다. |
| 정체됨 | 호출 속도로 제한을 초과 했습니다. |

## <a name="launching-the-immersive-reader"></a>몰입 형 판독기 시작

SDK는 몰입 형 판독기를 시작 하기 위한 단추에 대 한 기본 스타일을 제공 합니다. `immersive-reader-button` class 특성을 사용 하 여이 스타일을 사용 하도록 설정 합니다. 자세한 내용은 [이 문서](./how-to-customize-launch-button.md) 를 참조하세요.

```html
<div class='immersive-reader-button'></div>
```

### <a name="optional-attributes"></a>선택적 특성

다음 특성을 사용 하 여 단추의 모양과 느낌을 구성할 수 있습니다.

| attribute | Description |
| --------- | ----------- |
| `data-button-style` | 단추의 스타일을 설정 합니다. `icon`, `text` 또는 `iconAndText`일 수 있습니다. 기본값은 `icon`입니다. |
| `data-locale` | 로캘을 설정 합니다. 예를 들어 `en-US` 또는 `fr-FR`입니다. 기본값은 영어 `en`입니다. |
| `data-icon-px-size` | 아이콘의 크기 (픽셀)를 설정 합니다. 기본값은 20px입니다. |

## <a name="browser-support"></a>브라우저 지원

몰입 형 판독기를 사용 하 여 최상의 환경을 제공 하려면 다음 브라우저의 최신 버전을 사용 합니다.

* Microsoft Edge
* Internet Explorer 11
* Google Chrome
* Mozilla Firefox
* Apple Safari

## <a name="next-steps"></a>다음 단계

* [GitHub의 몰입형 리더 SDK](https://github.com/microsoft/immersive-reader-sdk) 탐색
* [빠른 시작: 몰입 형 판독기를 시작 하는 웹 앱C#만들기 ()](./quickstart.md)
