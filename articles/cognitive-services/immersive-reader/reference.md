---
title: 몰입형 리더 SDK 레퍼런스
titleSuffix: Azure Cognitive Services
description: 몰입형 리더 SDK에는 몰입형 리더를 응용 프로그램에 통합할 수 있는 JavaScript 라이브러리가 포함되어 있습니다.
services: cognitive-services
author: metanMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: reference
ms.date: 06/20/2019
ms.author: metan
ms.openlocfilehash: b20a3e6dd3b32b183bbf34dbefd76f0e4cd56b99
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "76156406"
---
# <a name="immersive-reader-sdk-reference-guide"></a>몰입형 리더 SDK 참조 가이드

몰입형 리더 SDK에는 몰입형 리더를 응용 프로그램에 통합할 수 있는 JavaScript 라이브러리가 포함되어 있습니다.

## <a name="functions"></a>함수

SDK는 다음 기능을 노출합니다.

- [`ImmersiveReader.launchAsync(token, subdomain, content, options)`](#launchasync)

- [`ImmersiveReader.close()`](#close)

- [`ImmersiveReader.renderButtons(options)`](#renderbuttons)

## <a name="launchasync"></a>발사동기화

웹 응용 프로그램에서 몰입형 `iframe` 판독기를 시작합니다.

```typescript
launchAsync(token: string, subdomain: string, content: Content, options?: Options): Promise<LaunchResponse>;
```

### <a name="parameters"></a>매개 변수

| 이름 | Type | Description |
| ---- | ---- |------------ |
| `token` | 문자열 | Azure AD 인증 토큰입니다. |
| `subdomain` | 문자열 | Azure에서 몰입형 Reader 리소스의 사용자 지정 하위 도메인입니다. |
| `content` | [콘텐츠](#content) | 몰입형 판독기에 표시할 콘텐츠를 포함하는 개체입니다. |
| `options` | [옵션](#options) | 몰입도 판독기의 특정 동작을 구성하기 위한 옵션입니다. (선택 사항) |

### <a name="returns"></a>반환

몰입형 `Promise<LaunchResponse>`판독기가 로드될 때 확인되는 을 반환합니다. 개체에 `Promise` 대한 [`LaunchResponse`](#launchresponse) 확인입니다.

### <a name="exceptions"></a>예외

몰입형 `Promise` 판독기가 로드되지 않으면 반환된 [`Error`](#error) 개체가 거부됩니다. 자세한 내용은 오류 [코드를](#error-codes)참조하십시오.

## <a name="close"></a>닫기

몰입형 판독기를 닫습니다.

이 함수의 예시 예는 [옵션에서](#options)설정하여 ```hideExitButton: true``` 종료 버튼이 숨겨져 있는 경우입니다. 그런 다음 다른 단추(예: 모바일 헤더의 뒤로 화살표)를 클릭할 때 이 ```close``` 함수를 호출할 수 있습니다.

```typescript
close(): void;
```

## <a name="renderbuttons"></a>렌더링단추

이 함수는 문서의 몰입형 리더 버튼 요소의 스타일과 업데이트를 제공합니다. 제공된 ```options.elements``` 경우 이 함수는 에서 ```options.elements```단추를 렌더링합니다. 그렇지 않으면 단추는 클래스가 ```immersive-reader-button```있는 문서의 요소 내에서 렌더링됩니다.

이 함수는 창이 로드될 때 SDK에서 자동으로 호출됩니다.

자세한 렌더링 옵션은 [선택적 특성을](#optional-attributes) 참조하십시오.

```typescript
renderButtons(options?: RenderButtonsOptions): void;
```

### <a name="parameters"></a>매개 변수

| 이름 | Type | Description |
| ---- | ---- |------------ |
| `options` | [렌더버튼옵션](#renderbuttonsoptions) | renderButtons 기능의 특정 동작을 구성 하기 위한 옵션입니다. (선택 사항) |

## <a name="types"></a>유형

### <a name="content"></a>콘텐츠

몰입형 판독기에 표시할 콘텐츠를 포함합니다.

```typescript
{
    title?: string;    // Title text shown at the top of the Immersive Reader (optional)
    chunks: Chunk[];   // Array of chunks
}
```

### <a name="chunk"></a>청크

몰입형 리더의 콘텐츠로 전달되는 단일 데이터 청크입니다.

```typescript
{
    content: string;        // Plain text string
    lang?: string;          // Language of the text, e.g. en, es-ES (optional). Language will be detected automatically if not specified.
    mimeType?: string;      // MIME type of the content (optional). Currently 'text/plain', 'application/mathml+xml', and 'text/html' are supported. Defaults to 'text/plain' if not specified.
}
```

### <a name="launchresponse"></a>시작 응답

에 대한 호출의 `ImmersiveReader.launchAsync`응답을 포함합니다.

```typescript
{
    container: HTMLDivElement;    // HTML element which contains the Immersive Reader iframe
    sessionId: string;            // Globally unique identifier for this session, used for debugging
}
```

### <a name="cookiepolicy-enum"></a>쿠키정책 열거

몰입형 Reader의 쿠키 사용에 대한 정책을 설정하는 데 사용되는 열거형입니다. [옵션을](#options)참조하십시오.

```typescript
enum CookiePolicy { Disable, Enable }
```

#### <a name="supported-mime-types"></a>지원되는 MIME 유형

| MIME 형식 | 설명 |
| --------- | ----------- |
| 텍스트/일반 | 일반 텍스트입니다. |
| 텍스트/html | HTML 콘텐츠입니다. [자세히 알아보기](#html-support)|
| 응용 프로그램 / 수학 + XML | 수학 마크 업 언어 (수학ML). [자세히 알아봅니다](./how-to/display-math.md).
| 응용 프로그램/vnd.openxmlformats-사무실 문서.워드 프로세싱ml.document | 마이크로 소프트 워드 .docx 형식 문서.

### <a name="html-support"></a>HTML 지원

| HTML | 지원되는 콘텐츠 |
| --------- | ----------- |
| 글꼴 스타일 | 굵게, 기울임꼴, 밑줄, 코드, 스트라이크 스루, 수퍼 스크립트, 자막 |
| 순서가 지정되지 않은 목록 | 디스크, 원, 정사각형, 원, 원, 원, 원, 원 |
| 정렬된 목록 | 소수점, 상부 알파, 하부 알파, 상층 부로마, 로어 로마 |
| 하이퍼링크 | 서비스 예정 |

지원되지 않는 태그는 비교적 렌더링됩니다. 이미지와 테이블은 현재 지원되지 않습니다.

### <a name="options"></a>옵션

몰입형 판독기의 특정 동작을 구성하는 속성을 포함합니다.

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

### <a name="renderbuttonsoptions"></a>렌더버튼옵션

몰입형 판독기 단추를 렌더링하기 위한 옵션입니다.

```typescript
{
    elements: HTMLDivElement[];    // Elements to render the Immersive Reader buttons in
}
```

### <a name="error"></a>Error

오류에 대한 정보를 포함합니다.

```typescript
{
    code: string;    // One of a set of error codes
    message: string; // Human-readable representation of the error
}
```

#### <a name="error-codes"></a>오류 코드

| 코드 | 설명 |
| ---- | ----------- |
| BadArgument | 제공된 인수가 잘못되었습니다. `message` |
| 시간 제한 | 몰입형 판독기가 지정된 시간 제한 내에 로드되지 못했습니다. |
| TokenExpired | 제공된 토큰이 만료되었습니다. |
| 정체됨 | 통화 속도 제한을 초과했습니다. |

## <a name="launching-the-immersive-reader"></a>몰입형 리더 출시

SDK는 몰입형 리더를 실행하기 위한 단추에 대한 기본 스타일을 제공합니다. 클래스 `immersive-reader-button` 특성을 사용하여 이 스타일을 활성화합니다. 자세한 내용은 [이 문서를](./how-to-customize-launch-button.md) 참조하십시오.

```html
<div class='immersive-reader-button'></div>
```

### <a name="optional-attributes"></a>선택적 특성

다음 특성을 사용하여 단추의 모양과 느낌을 구성합니다.

| 특성 | 설명 |
| --------- | ----------- |
| `data-button-style` | 단추의 스타일을 설정합니다. `icon`, `text` 또는 `iconAndText`일 수 있습니다. 기본값은 `icon`입니다. |
| `data-locale` | 로캘을 설정합니다. 예를 들어 `en-US` 또는 `fr-FR`입니다. 기본값은 `en`영어입니다. |
| `data-icon-px-size` | 아이콘의 크기를 픽셀 단위로 설정합니다. 기본값은 20px입니다. |

## <a name="browser-support"></a>브라우저 지원

몰입형 리더를 통해 최상의 환경을 위해 다음 브라우저의 최신 버전을 사용합니다.

* Microsoft Edge
* Internet Explorer 11
* Google Chrome
* Mozilla Firefox
* Apple Safari

## <a name="next-steps"></a>다음 단계

* [GitHub의 몰입형 리더 SDK](https://github.com/microsoft/immersive-reader-sdk) 탐색
* [빠른 시작: 몰입형 리더(C#)를 시작하는 웹 앱 만들기](./quickstart.md)
