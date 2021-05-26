---
title: 몰입형 리더 SDK 참조
titleSuffix: Azure Applied AI Services
description: 몰입형 리더 SDK에는 몰입형 리더를 애플리케이션에 통합할 수 있는 JavaScript 라이브러리가 포함되어 있습니다.
services: cognitive-services
author: metanMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: reference
ms.date: 06/20/2019
ms.author: metang
ms.openlocfilehash: 852e3d91b9d90d79bc45b4478ee433fbbd13255b
ms.sourcegitcommit: 58e5d3f4a6cb44607e946f6b931345b6fe237e0e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/25/2021
ms.locfileid: "110370791"
---
# <a name="immersive-reader-javascript-sdk-reference-v11"></a>몰입형 리더 JavaScript SDK 참조(v1.1)

몰입형 리더 SDK에는 몰입형 리더를 애플리케이션에 통합할 수 있는 JavaScript 라이브러리가 포함되어 있습니다.

## <a name="functions"></a>Functions

SDK는 다음 함수를 노출합니다.

- [`ImmersiveReader.launchAsync(token, subdomain, content, options)`](#launchasync)

- [`ImmersiveReader.close()`](#close)

- [`ImmersiveReader.renderButtons(options)`](#renderbuttons)

<br>

## <a name="launchasync"></a>launchAsync

웹 애플리케이션의 `iframe` 내에서 몰입형 리더를 시작합니다. 콘텐츠의 크기는 최대 50MB로 제한됩니다.

```typescript
launchAsync(token: string, subdomain: string, content: Content, options?: Options): Promise<LaunchResponse>;
```

#### <a name="launchasync-parameters"></a>launchAsync 매개 변수

| 이름 | Type | Description |
| ---- | ---- |------------ |
| `token` | 문자열 | Azure AD 인증 토큰입니다. 자세한 내용은 [몰입형 리더 리소스를 만드는 방법](./how-to-create-immersive-reader.md)을 참조하세요. |
| `subdomain` | 문자열 | Azure에서 몰입형 리더 리소스의 사용자 지정 하위 도메인입니다. 자세한 내용은 [몰입형 리더 리소스를 만드는 방법](./how-to-create-immersive-reader.md)을 참조하세요. |
| `content` | [콘텐츠](#content) | 몰입형 리더에 표시할 콘텐츠를 포함하는 개체입니다. |
| `options` | [옵션](#options) | 몰입형 리더의 특정 동작을 구성하기 위한 옵션입니다. 선택 사항입니다. |

#### <a name="returns"></a>반환

몰입형 리더가 로드된 때를 확인하는 `Promise<LaunchResponse>`를 반환합니다. `Promise`는 [`LaunchResponse`](#launchresponse) 개체로 해결됩니다.

#### <a name="exceptions"></a>예외

몰입형 리더를 로드할 수 없는 경우 반환된 `Promise`는 [`Error`](#error) 개체와 함께 거부됩니다. 자세한 내용은 [오류 코드](#error-codes)를 참조하세요.

<br>

## <a name="close"></a>닫기

몰입형 리더를 닫습니다.

이 함수에 대한 사용 예는 [옵션](#options)에서 ```hideExitButton: true```를 설정하여 종료 단추를 숨기는 경우입니다. 그런 다음 다른 단추(예: 모바일 헤더의 뒤로 화살표)를 클릭하면 이 ```close``` 함수를 호출할 수 있습니다.

```typescript
close(): void;
```

<br>

## <a name="immersive-reader-launch-button"></a>몰입형 리더 시작 단추

SDK는 몰입형 리더 시작을 위한 단추에 대한 기본 스타일 지정을 제공합니다. 이 스타일 지정을 사용하도록 설정하려면 `immersive-reader-button` 클래스 특성을 사용합니다. 자세한 내용은 [몰입형 리더 단추를 사용자 지정하는 방법](./how-to-customize-launch-button.md)을 참조하세요.

```html
<div class='immersive-reader-button'></div>
```

#### <a name="optional-attributes"></a>선택적 특성

다음 특성을 사용하여 단추의 모양과 느낌을 구성합니다.

| attribute | Description |
| --------- | ----------- |
| `data-button-style` | 단추의 스타일을 설정합니다. `icon`, `text` 또는 `iconAndText`일 수 있습니다. 기본값은 `icon`입니다. |
| `data-locale` | 로캘을 설정합니다. 예를 들어 `en-US` 또는 `fr-FR`입니다. 기본값은 영어 `en`입니다. |
| `data-icon-px-size` | 아이콘의 크기를 픽셀 단위로 설정합니다. 기본값은 20px입니다. |

<br>

## <a name="renderbuttons"></a>renderButtons

```renderButtons``` 함수는 [몰입형 리더 단추를 사용자 지정하는 방법](./how-to-customize-launch-button.md) 참고 자료를 사용하는 경우 필요하지 않습니다.

이 함수는 문서의 몰입형 리더 단추 요소를 스타일 지정하고 업데이트합니다. ```options.elements```가 제공되면 ```options.elements```에 제공된 각 요소 내에서 단추가 렌더링됩니다. 몰입형 리더를 시작할 문서에 여러 개의 섹션이 있고 동일한 스타일 지정으로 여러 개의 단추를 렌더링하는 간략한 방법을 원하거나 단순하고 일관된 디자인 패턴으로 단추를 렌더링하려고 할 때 ```options.elements``` 매개 변수를 사용하는 것이 유용합니다. 이 함수를 [renderButtons 옵션](#renderbuttons-options) 매개 변수와 함께 사용하려면 아래 코드 조각에 나타나 있는 것처럼 페이지 로드에 ```ImmersiveReader.renderButtons(options: RenderButtonsOptions);```를 호출합니다. 그러지 않으면 [몰입형 리더 단추를 사용자 지정하는 방법](./how-to-customize-launch-button.md)에 표시된 것처럼 ```immersive-reader-button``` 클래스가 있는 문서 요소 내에서 단추가 렌더링됩니다.

```typescript
// This snippet assumes there are two empty div elements in
// the page HTML, button1 and button2.
const btn1: HTMLDivElement = document.getElementById('button1');
const btn2: HTMLDivElement = document.getElementById('button2');
const btns: HTMLDivElement[] = [btn1, btn2];
ImmersiveReader.renderButtons({elements: btns});
```

더 많은 렌더링 옵션에 대해서는 위의 [선택적 특성](#optional-attributes)을 참조하세요. 이러한 옵션을 사용하려면 페이지 HTML에서 각각의 ```HTMLDivElement```에 옵션 특성을 추가합니다.

```typescript
renderButtons(options?: RenderButtonsOptions): void;
```

#### <a name="renderbuttons-parameters"></a>renderButtons 매개 변수

| 이름 | Type | Description |
| ---- | ---- |------------ |
| `options` | [renderButtons 옵션](#renderbuttons-options) | renderButtons 함수의 특정 동작을 구성하는 옵션입니다. 선택 사항입니다. |

### <a name="renderbuttons-options"></a>renderButtons 옵션

몰입형 리더 단추를 렌더링하는 옵션입니다.

```typescript
{
    elements: HTMLDivElement[];
}
```

#### <a name="renderbuttons-options-parameters"></a>renderButtons 옵션 매개 변수

| 설정 | Type | Description |
| ------- | ---- | ----------- |
| 요소 | HTMLDivElement[] | 몰입형 리더 단추를 렌더링할 요소입니다. |

##### `elements`
```Parameters
Type: HTMLDivElement[]
Required: false
```

<br>

## <a name="launchresponse"></a>LaunchResponse

`ImmersiveReader.launchAsync`에 대한 호출의 응답을 포함합니다. 몰입형 리더를 포함하는 `iframe`에 대한 참조는 `container.firstChild`를 통해 액세스할 수 있습니다.

```typescript
{
    container: HTMLDivElement;
    sessionId: string;
}
```

#### <a name="launchresponse-parameters"></a>LaunchResponse 매개 변수

| 설정 | Type | Description |
| ------- | ---- | ----------- |
| container | HTMLDivElement | 몰입형 리더 iframe을 포함하는 HTML 요소입니다. |
| sessionID | String | 이 세션에 대해 디버깅에 사용되는 GUID(Globally unique identifier)입니다. |
 
## <a name="error"></a>Error

오류에 대한 정보를 포함합니다.

```typescript
{
    code: string;
    message: string;
}
```

#### <a name="error-parameters"></a>오류 매개 변수

| 설정 | Type | 설명 |
| ------- | ---- | ----------- |
| code | String | 오류 코드의 집합 중 하나입니다. [오류 코드](#error-codes)를 참조하세요. |
| message | String | 사람이 읽을 수 있는 오류 표현입니다. |

#### <a name="error-codes"></a>오류 코드

| 코드 | Description |
| ---- | ----------- |
| BadArgument | 제공된 인수가 잘못되었으므로 [오류](#error)의 `message` 매개 변수를 참조하세요. |
| 제한 시간 | 몰입형 리더를 지정된 시간 제한 내에 로드하지 못했습니다. |
| TokenExpired | 제공된 토큰이 만료되었습니다. |
| 정체됨 | 호출 속도 제한을 초과했습니다. |

<br>

## <a name="types"></a>형식

### <a name="content"></a>콘텐츠

몰입형 리더에 표시할 콘텐츠를 포함합니다.

```typescript
{
    title?: string;
    chunks: Chunk[];
}
```

#### <a name="content-parameters"></a>콘텐츠 매개 변수

| 이름 | Type | Description |
| ---- | ---- |------------ |
| title | String | 몰입형 리더의 위쪽에 표시되는 제목 텍스트(선택 사항)입니다. |
| 청크 | [Chunk[]](#chunk) | 청크 배열입니다. |

##### `title`
```Parameters
Type: String
Required: false
Default value: "Immersive Reader" 
```

##### `chunks`
```Parameters
Type: Chunk[]
Required: true
Default value: null 
```

<br>

### <a name="chunk"></a>Chunk

몰입형 리더의 콘텐츠로 전달되는 단일 데이터 청크입니다.

```typescript
{
    content: string;
    lang?: string;
    mimeType?: string;
}
```

#### <a name="chunk-parameters"></a>청크 매개 변수

| 이름 | Type | Description |
| ---- | ---- |------------ |
| 콘텐츠 | String | 몰입형 리더로 전송된 콘텐츠를 포함하는 문자열입니다. |
| lang | String | 텍스트 언어입니다. 값은 IETF BCP 47 언어 태그 형식(예: en, es-ES)입니다. 지정하지 않으면 언어가 자동으로 검색됩니다. [지원되는 언어](#supported-languages)를 참조하세요. |
| mimeType | 문자열 | 일반 텍스트, MathML, HTML, Microsoft Word DOCX 형식이 지원됩니다. 자세한 내용은 [지원되는 MIME 형식](#supported-mime-types)을 참조하세요. |

##### `content`
```Parameters
Type: String
Required: true
Default value: null 
```

##### `lang`
```Parameters
Type: String
Required: false
Default value: Automatically detected 
```

##### `mimeType`
```Parameters
Type: String
Required: false
Default value: "text/plain"
```

#### <a name="supported-mime-types"></a>지원되는 MIME 형식

| MIME 형식 | Description |
| --------- | ----------- |
| 텍스트/일반 | 일반 텍스트입니다. |
| 텍스트/html | HTML 콘텐츠입니다. [자세히 알아보기](#html-support)|
| application/mathml+xml | MathML(Mathematical Markup Language)입니다. [자세히 알아봅니다](./how-to/display-math.md).
| application/vnd.openxmlformats-officedocument.wordprocessingml.document | Microsoft Word .docx 형식의 문서입니다.


<br>

## <a name="options"></a>옵션

몰입형 리더의 특정 동작을 구성하는 속성을 포함합니다.

```typescript
{
    uiLang?: string;
    timeout?: number;
    uiZIndex?: number;
    useWebview?: boolean;
    onExit?: () => any;
    allowFullscreen?: boolean;
    hideExitButton?: boolean;
    cookiePolicy?: CookiePolicy;
    disableFirstRun?: boolean;
    readAloudOptions?: ReadAloudOptions;
    translationOptions?: TranslationOptions;
    displayOptions?: DisplayOptions;
    preferences?: string;
    onPreferencesChanged?: (value: string) => any;
    customDomain?: string;
}
```

#### <a name="options-parameters"></a>옵션 매개 변수

| 이름 | Type | Description |
| ---- | ---- |------------ |
| uiLang | String | UI 언어입니다. 값은 IETF BCP 47 언어 태그 형식(예: en, es-ES)입니다. 지정하지 않으면 기본적으로 브라우저 언어로 지정됩니다. |
| 시간 제한 | 숫자 | [launchAsync](#launchasync)에서 시간 제한 오류가 발생하기 전까지의 기간(밀리초)입니다(기본값은 15000밀리초). 리더 페이지가 열리고 회전자가 시작될 때 성공이 관찰되는 리더 페이지의 초기 시작에만 이 시간 제한이 적용됩니다. 시간 제한을 조정할 필요가 없습니다. |
| uiZIndex | 숫자 | 생성되는 iframe의 Z-인덱스입니다(기본값은 1000). |
| useWebview | 부울| Chrome 앱과의 호환성을 위해 iframe 대신 webview 태그를 사용합니다(기본값은 false). |
| onExit | 함수 | 몰입형 리더가 종료될 때 실행됩니다. |
| allowFullscreen | 부울 | 전체 화면을 토글하는 기능입니다(기본값은 true). |
| hideExitButton | 부울 | 몰입형 리더의 종료 단추 화살표를 숨길지 여부입니다(기본값은 false). 몰입형 리더를 종료하기 위해 제공되는 대체 메커니즘(예: 모바일 툴바의 뒤로 화살표)이 있는 경우 이는 true여야 합니다. |
| cookiePolicy | [CookiePolicy](#cookiepolicy-options) | 몰입형 리더의 쿠키 사용에 대한 설정입니다(기본값은 *CookiePolicy.Disable*). EU 쿠키 준수 정책에 따라 필요한 모든 사용자 동의를 얻는 것은 호스트 애플리케이션의 책임입니다. [쿠키 정책 옵션](#cookiepolicy-options)을 참조하세요. |
| disableFirstRun | 부울 | 첫 실행 경험을 사용하지 않도록 설정합니다. |
| readAloudOptions | [ReadAloudOptions](#readaloudoptions) | 소리 내어 읽기를 구성하는 옵션입니다. |
| translationOptions | [TranslationOptions](#translationoptions) | 번역을 구성하는 옵션입니다. |
| displayOptions | [DisplayOptions](#displayoptions) | 텍스트 크기, 글꼴 등을 구성하는 옵션입니다. |
| preferences | String | 몰입형 리더의 사용자 기본 설정을 나타내는 onPreferencesChanged에서 반환된 문자열입니다. 자세한 내용은 [매개 변수 설정](#settings-parameters) 및 [사용자 기본 설정 저장 방법](./how-to-store-user-preferences.md)을 참조하세요. |
| onPreferencesChanged | 함수 | 사용자 기본 설정이 변경될 때 실행됩니다. 자세한 내용은 [사용자 기본 설정 저장 방법](./how-to-store-user-preferences.md)을 참조하세요. |
| customDomain | String | 내부용으로 예약된 속성입니다. 몰입형 리더 웹앱이 호스트되는 사용자 지정 도메인입니다(기본값은 null). |

##### `uiLang`
```Parameters
Type: String
Required: false
Default value: User's browser language 
```

##### `timeout`
```Parameters
Type: Number
Required: false
Default value: 15000
```

##### `uiZIndex`
```Parameters
Type: Number
Required: false
Default value: 1000
```

##### `onExit`
```Parameters
Type: Function
Required: false
Default value: null
```

##### `preferences`

> [!CAUTION]
> **중요** 예기치 않은 동작이 발생하여 고객의 사용자 경험이 저하될 수 있으므로 몰입형 리더 애플리케이션과 주고받는 `-preferences` 문자열의 값을 프로그래밍 방식으로 변경하려고 시도하지 않습니다. 호스트 애플리케이션에서 사용자 지정 값을 할당하거나 `-preferences` 문자열을 조작하면 안 됩니다. `-preferences` 문자열 옵션을 사용할 때 `-onPreferencesChanged` 콜백 옵션에서 반환된 정확한 값만 사용합니다.

```Parameters
Type: String
Required: false
Default value: null
```

##### `onPreferencesChanged`
```Parameters
Type: Function
Required: false
Default value: null
```

##### `customDomain`
```Parameters
Type: String
Required: false
Default value: null
```

<br>

## <a name="readaloudoptions"></a>ReadAloudOptions

```typescript
type ReadAloudOptions = {
    voice?: string;
    speed?: number;
    autoplay?: boolean;
};
```

#### <a name="readaloudoptions-parameters"></a>ReadAloudOptions 매개 변수

| 이름 | Type | Description |
| ---- | ---- |------------ |
| voice | String | 음성("여성" 또는 "남성")입니다. 모든 언어가 양쪽 성별을 지원하지는 않습니다. |
| speed | 숫자 | 재생 속도입니다. 0.5 ~ 2.5(포함)여야 합니다. |
| autoplay | 부울 | 몰입형 리더가 로드되면 자동으로 소리 내어 읽기를 시작합니다. |

##### `voice`
```Parameters
Type: String
Required: false
Default value: "Female" or "Male" (determined by language) 
Values available: "Female", "Male"
```

##### `speed`
```Parameters
Type: Number
Required: false
Default value: 1
Values available: 0.5, 0.75, 1, 1.25, 1.5, 1.75, 2, 2.25, 2.5
```

> [!NOTE]
> 브라우저 제한 사항으로 인해 Safari에서는 자동 재생이 지원되지 않습니다.

<br>

## <a name="translationoptions"></a>TranslationOptions

```typescript
type TranslationOptions = {
    language: string;
    autoEnableDocumentTranslation?: boolean;
    autoEnableWordTranslation?: boolean;
};
```

#### <a name="translationoptions-parameters"></a>TranslationOptions 매개 변수

| 이름 | Type | Description |
| ---- | ---- |------------ |
| 언어 | String | 번역 언어를 설정합니다. 값은 IETF BCP 47 언어 태그 형식(예: fr-FR, es-MX, zh-Hans-CN)입니다. 단어 또는 문서 번역을 사용하도록 자동으로 설정하려면 필요합니다. |
| autoEnableDocumentTranslation | 부울 | 전체 문서를 자동으로 번역합니다. |
| autoEnableWordTranslation | 부울 | 단어 번역을 사용하도록 자동으로 설정합니다. |

##### `language`
```Parameters
Type: String
Required: true
Default value: null 
Values available: See the Supported Languages section
```

<br>

## <a name="displayoptions"></a>DisplayOptions

```typescript
type DisplayOptions = {
    textSize?: number;
    increaseSpacing?: boolean;
    fontFamily?: string;
};
```

#### <a name="displayoptions-parameters"></a>DisplayOptions 매개 변수

| 이름 | Type | Description |
| ---- | ---- |------------ |
| textSize | 숫자 | 선택한 텍스트 크기를 설정합니다. |
| increaseSpacing | 부울 | 텍스트 간격 지정을 켜거나 끌지 설정합니다. |
| fontFamily | String | 선택한 글꼴("Calibri", "ComicSans", "Sitka")을 설정합니다. |

##### `textSize`
```Parameters
Type: Number
Required: false
Default value: 20, 36 or 42 (Determined by screen size)
Values available: 14, 20, 28, 36, 42, 48, 56, 64, 72, 84, 96
```

##### `fontFamily`
```Parameters
Type: String
Required: false
Default value: "Calibri"
Values available: "Calibri", "Sitka", "ComicSans"
```

<br>

## <a name="cookiepolicy-options"></a>CookiePolicy 옵션

```typescript
enum CookiePolicy { Disable, Enable }
```

**아래에 나열된 설정은 정보 제공 목적으로만 사용됩니다**. 몰입형 리더는 쿠키에 해당 설정 또는 사용자 기본 설정을 저장합니다. 이 *cookiePolicy* 옵션은 EU 쿠키 규정 준수법을 준수하기 위해 기본적으로 쿠키 사용을 **사용하지 않도록 설정** 합니다. 쿠키를 다시 사용하도록 설정하고 몰입형 리더 사용자 기본 설정에 대해 기본 기능을 복원하려 하는 경우 웹 사이트 또는 애플리케이션이 쿠키를 사용하도록 설정하기 위해 사용자로부터 적절한 동의를 얻었는지 확인해야 합니다. 그런 다음 몰입형 리더에서 쿠키를 다시 사용하도록 설정하려면 몰입형 리더를 시작할 때 *cookiePolicy* 옵션을 *CookiePolicy.Enable* 로 명시적으로 설정해야 합니다. 아래 표는 *cookiePolicy* 옵션이 사용하도록 설정되어 있을 때 몰입형 리더가 쿠키에 저장하는 설정을 설명합니다.

#### <a name="settings-parameters"></a>설정 매개 변수

| 설정 | Type | Description |
| ------- | ---- | ----------- |
| textSize | 숫자 | 선택한 텍스트 크기를 설정합니다. |
| fontFamily | String | 선택한 글꼴("Calibri", "ComicSans", "Sitka")을 설정합니다. |
| textSpacing | 숫자 | 텍스트 간격 지정을 켜거나 끌지 설정합니다. |
| formattingEnabled | 부울 | HTML 서식 지정을 켜거나 끌지 설정합니다. |
| 테마(theme) | String | 선택한 테마(예: "Light", "Dark"...)를 설정합니다. |
| syllabificationEnabled | 부울 | 음절 구분을 켜거나 끌지 설정합니다. |
| nounHighlightingEnabled | 부울 | 명사 강조 표시를 켜거나 끌지 설정합니다. |
| nounHighlightingColor | String | 선택한 명사 강조 표시 색을 설정합니다. |
| verbHighlightingEnabled | 부울 | 동사 강조 표시를 켜거나 끌지 설정합니다. |
| verbHighlightingColor | String | 선택한 동사 강조 표시 색을 설정합니다. |
| adjectiveHighlightingEnabled | 부울 | 형용사 강조 표시를 켜거나 끌지 설정합니다. |
| adjectiveHighlightingColor | String | 선택한 형용사 강조 표시 색을 설정합니다. |
| adverbHighlightingEnabled | 부울 | 부사 강조 표시를 켜거나 끌지 설정합니다. |
| adverbHighlightingColor | String | 선택한 부사 강조 표시 색을 설정합니다. |
| pictureDictionaryEnabled | 부울 | 그림 사전을 켜거나 끌지 설정합니다. |
| posLabelsEnabled | 부울 | 강조 표시된 각 품사의 위 첨자 텍스트 레이블을 켜거나 끌지 설정합니다.  |

<br>

## <a name="supported-languages"></a>지원되는 언어

몰입형 리더의 번역 기능은 다양한 언어를 지원합니다. 자세한 내용은 [언어 지원](./language-support.md) 을 참조하세요.

<br>

## <a name="html-support"></a>HTML 지원

서식 지정을 사용할 때 다음 콘텐츠가 몰입형 리더에서 HTML로 렌더링됩니다.

| HTML | 지원되는 콘텐츠 |
| --------- | ----------- |
| 글꼴 스타일 | 굵게, 기울임꼴, 밑줄, 코드, 취소선, 위 첨자, 아래 첨자 |
| 순서가 지정되지 않은 목록 | 디스크, 원, 사각형 |
| 순서가 지정된 목록 | 숫자, 영문 대문자, 영문 소문자, 로마 숫자 대문자, 로마 숫자 소문자 |

지원되지 않는 태그는 비슷하게 렌더링됩니다. 이미지와 표는 현재 지원되지 않습니다.

<br>

## <a name="browser-support"></a>브라우저 지원

몰입형 리더를 사용하는 최상의 환경을 위해 다음 브라우저의 최신 버전을 사용합니다.

* Microsoft Edge
* Internet Explorer 11
* Google Chrome
* Mozilla Firefox
* Apple Safari

<br>

## <a name="next-steps"></a>다음 단계

* [GitHub의 몰입형 리더 SDK](https://github.com/microsoft/immersive-reader-sdk) 탐색
* [빠른 시작: 몰입형 리더를 시작하는 웹앱 만들기(C#)](./quickstarts/client-libraries.md?pivots=programming-language-csharp)
