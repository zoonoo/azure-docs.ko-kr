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
ms.openlocfilehash: 73322cdee151969e6e765690284bbffc1c871f4e
ms.sourcegitcommit: 07166a1ff8bd23f5e1c49d4fd12badbca5ebd19c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/15/2020
ms.locfileid: "90090196"
---
# <a name="immersive-reader-javascript-sdk-reference-v11"></a>몰입 형 판독기 JavaScript SDK 참조 (v. 1.1)

몰입 형 판독기 SDK에는 몰입 형 판독기를 응용 프로그램에 통합할 수 있는 JavaScript 라이브러리가 포함 되어 있습니다.

## <a name="functions"></a>Functions

SDK는 함수를 노출 합니다.

- [`ImmersiveReader.launchAsync(token, subdomain, content, options)`](#launchasync)

- [`ImmersiveReader.close()`](#close)

- [`ImmersiveReader.renderButtons(options)`](#renderbuttons)

<br>

## <a name="launchasync"></a>launchAsync

웹 응용 프로그램의 내에서 몰입 형 판독기를 시작 `iframe` 합니다. 콘텐츠의 크기는 최대 50 MB로 제한 됩니다.

```typescript
launchAsync(token: string, subdomain: string, content: Content, options?: Options): Promise<LaunchResponse>;
```

#### <a name="launchasync-parameters"></a>launchAsync 매개 변수

| 이름 | Type | Description |
| ---- | ---- |------------ |
| `token` | 문자열 | Azure AD 인증 토큰입니다. 자세한 내용은 [몰입 형 판독기 리소스를 만드는 방법을](./how-to-create-immersive-reader.md) 참조 하세요. |
| `subdomain` | 문자열 | Azure에서 몰입 형 판독기 리소스의 사용자 지정 하위 도메인입니다. 자세한 내용은 [몰입 형 판독기 리소스를 만드는 방법을](./how-to-create-immersive-reader.md) 참조 하세요. |
| `content` | [콘텐츠](#content) | 몰입 형 판독기에 표시할 콘텐츠를 포함 하는 개체입니다. |
| `options` | [옵션](#options) | 몰입 형 판독기의 특정 동작을 구성 하기 위한 옵션입니다. 선택 사항입니다. |

#### <a name="returns"></a>반환

`Promise<LaunchResponse>`몰입 형 판독기가 로드 될 때 확인 되는를 반환 합니다. 는 `Promise` 개체를 확인 [`LaunchResponse`](#launchresponse) 합니다.

#### <a name="exceptions"></a>예외

`Promise` [`Error`](#error) 몰입 형 판독기를 로드 하지 못하는 경우 반환 된는 개체와 함께 거부 됩니다. 자세한 내용은 [오류 코드](#error-codes)를 참조 하세요.

<br>

## <a name="close"></a>닫기

몰입 형 판독기를 닫습니다.

이 함수에 대 한 사용 사례 예는 옵션에서을 설정 하 여 종료 단추를 숨기는 경우입니다 ```hideExitButton: true``` . [options](#options) 그런 다음 클릭할 때 다른 단추 (예: 모바일 헤더의 뒤로 화살표)가이 함수를 호출할 수 있습니다 ```close``` .

```typescript
close(): void;
```

<br>

## <a name="immersive-reader-launch-button"></a>몰입 형 판독기 시작 단추

SDK는 몰입 형 판독기를 시작 하기 위한 단추에 대 한 기본 스타일을 제공 합니다. `immersive-reader-button`이 스타일을 사용 하려면 class 특성을 사용 합니다. 자세한 내용은 [몰입 형 판독기 단추를 사용자 지정 하는 방법을](./how-to-customize-launch-button.md) 참조 하세요.

```html
<div class='immersive-reader-button'></div>
```

#### <a name="optional-attributes"></a>선택적 특성

다음 특성을 사용 하 여 단추의 모양과 느낌을 구성할 수 있습니다.

| attribute | 설명 |
| --------- | ----------- |
| `data-button-style` | 단추의 스타일을 설정 합니다. `icon`, `text` 또는 `iconAndText`일 수 있습니다. 기본값은 `icon`입니다. |
| `data-locale` | 로캘을 설정 합니다. 예를 들어 `en-US` 또는 `fr-FR`입니다. 기본값은 영어 `en` 입니다. |
| `data-icon-px-size` | 아이콘의 크기 (픽셀)를 설정 합니다. 기본값은 20px입니다. |

<br>

## <a name="renderbuttons"></a>renderButtons

```renderButtons```이 함수는 [몰입 형 판독기를 사용자 지정 하는 방법 단추](./how-to-customize-launch-button.md) 를 사용 하는 경우 필요 하지 않습니다.

이 함수는 문서의 모던 판독기 단추 요소를 스타일 및 업데이트 합니다. ```options.elements```가 제공 된 경우에는에서 제공 하는 각 요소 내에서 단추가 렌더링 됩니다 ```options.elements``` . ```options.elements```문서에서 몰입 형 판독기를 시작 하는 여러 섹션이 있고 동일한 스타일을 사용 하 여 여러 단추를 렌더링 하거나 간단 하 고 일관적인 디자인 패턴을 사용 하 여 단추를 렌더링 하려는 경우 매개 변수를 사용 하는 것이 유용 합니다. [Renderbuttons options](#renderbuttons-options) 매개 변수와 함께이 함수를 사용 하려면 ```ImmersiveReader.renderButtons(options: RenderButtonsOptions);``` 아래 코드 조각에 나와 있는 것 처럼 페이지 로드에 대해를 호출 합니다. 그렇지 않은 경우에는 ```immersive-reader-button``` [몰입 형 판독기 단추를 사용자 지정 하는 방법](./how-to-customize-launch-button.md) 에 표시 된 것 처럼 클래스가 있는 문서 요소 내에서 단추가 렌더링 됩니다.

```typescript
// This snippet assumes there are two empty div elements in
// the page HTML, button1 and button2.
const btn1: HTMLDivElement = document.getElementById('button1');
const btn2: HTMLDivElement = document.getElementById('button2');
const btns: HTMLDivElement[] = [btn1, btn2];
ImmersiveReader.renderButtons({elements: btns});
```

더 많은 렌더링 옵션에 대해서는 위의 [선택적 특성](#optional-attributes) 을 참조 하세요. 이러한 옵션을 사용 하려면 페이지 HTML에서 옵션 특성을 각각에 추가 합니다 ```HTMLDivElement``` .

```typescript
renderButtons(options?: RenderButtonsOptions): void;
```

#### <a name="renderbuttons-parameters"></a>renderButtons 매개 변수

| 이름 | Type | 설명 |
| ---- | ---- |------------ |
| `options` | [renderButtons 옵션](#renderbuttons-options) | RenderButtons 함수의 특정 동작을 구성 하는 옵션입니다. 선택 사항입니다. |

### <a name="renderbuttons-options"></a>renderButtons 옵션

몰입 형 판독기 단추를 렌더링 하는 옵션입니다.

```typescript
{
    elements: HTMLDivElement[];
}
```

#### <a name="renderbuttons-options-parameters"></a>renderButtons 옵션 매개 변수

| 설정 | Type | 설명 |
| ------- | ---- | ----------- |
| 요소 | HTMLDivElement[] | 몰입 형 판독기 단추를 렌더링할 요소입니다. |

##### `-elements`
```Parameters
Type: HTMLDivElement[]
Required: false
```

<br>

## <a name="launchresponse"></a>LaunchResponse

에 대 한 호출의 응답을 포함 `ImmersiveReader.launchAsync` 합니다. 몰입 형 판독기를 포함 하는에 대 한 참조는를 `iframe` 통해 액세스할 수 있습니다 `container.firstChild` .

```typescript
{
    container: HTMLDivElement;
    sessionId: string;
}
```

#### <a name="launchresponse-parameters"></a>LaunchResponse 매개 변수

| 설정 | Type | 설명 |
| ------- | ---- | ----------- |
| container | HTMLDivElement | 몰입 형 판독기 iframe을 포함 하는 HTML 요소입니다. |
| sessionID | String | 이 세션의 guid (Globally unique identifier)로, 디버깅에 사용 됩니다. |
 
## <a name="error"></a>Error

오류에 대 한 정보를 포함 합니다.

```typescript
{
    code: string;
    message: string;
}
```

#### <a name="error-parameters"></a>오류 매개 변수

| 설정 | Type | 설명 |
| ------- | ---- | ----------- |
| code | String | 오류 코드 집합 중 하나입니다. [오류 코드](#error-codes)를 참조하세요. |
| message | String | 사람이 읽을 수 있는 오류 표현입니다. |

#### <a name="error-codes"></a>오류 코드

| 코드 | Description |
| ---- | ----------- |
| BadArgument | 제공 된 인수가 잘못 되었습니다 `message` . [오류의](#error)매개 변수를 참조 하십시오. |
| 제한 시간 | 몰입 형 판독기를 지정 된 시간 제한 내에 로드 하지 못했습니다. |
| TokenExpired | 제공 된 토큰이 만료 되었습니다. |
| 정체됨 | 호출 속도로 제한을 초과 했습니다. |

<br>

## <a name="types"></a>유형

### <a name="content"></a>Content

몰입 형 판독기에 표시할 콘텐츠를 포함 합니다.

```typescript
{
    title?: string;
    chunks: Chunk[];
}
```

#### <a name="content-parameters"></a>콘텐츠 매개 변수

| 이름 | Type | 설명 |
| ---- | ---- |------------ |
| title | String | 몰입 형 판독기의 위쪽에 표시 되는 제목 텍스트입니다 (선택 사항). |
| 청크 | [청크 []](#chunk) | 청크 배열 |

##### `-title`
```Parameters
Type: String
Required: false
Default value: "Immersive Reader" 
```

##### `-chunks`
```Parameters
Type: Chunk[]
Required: true
Default value: null 
```

<br>

### <a name="chunk"></a>Chunk

몰입 형 판독기의 콘텐츠로 전달 되는 단일 데이터 청크입니다.

```typescript
{
    content: string;
    lang?: string;
    mimeType?: string;
}
```

#### <a name="chunk-parameters"></a>청크 매개 변수

| 이름 | Type | 설명 |
| ---- | ---- |------------ |
| 콘텐츠 | String | 몰입 형 판독기로 전송 된 콘텐츠를 포함 하는 문자열입니다. |
| lang | String | 텍스트의 언어 값은 IETF BCP 47 언어 태그 형식 (예: en, es)에 있습니다. 지정 하지 않으면 언어가 자동으로 검색 됩니다. [지원되는 언어](#supported-languages)를 참조하세요. |
| mimeType | 문자열 | 일반 텍스트, MathML, HTML & Microsoft Word .DOCX 형식이 지원 됩니다. 자세한 내용은 [지원 되는 MIME 형식](#supported-mime-types) 을 참조 하세요. |

##### `-content`
```Parameters
Type: String
Required: true
Default value: null 
```

##### `-lang`
```Parameters
Type: String
Required: false
Default value: Automatically detected 
```

##### `-mimeType`
```Parameters
Type: String
Required: false
Default value: "text/plain"
```

#### <a name="supported-mime-types"></a>지원 되는 MIME 형식

| MIME 형식 | Description |
| --------- | ----------- |
| 텍스트/일반 | 일반 텍스트입니다. |
| 텍스트/html | HTML 콘텐츠입니다. [자세한 정보](#html-support)|
| application/mathml + xml | MathML (수학 Markup Language). [자세히 알아보기](./how-to/display-math.md).
| 응용 프로그램/vnd.openxmlformats-officedocument.wordprocessingml.document | Microsoft Word .docx 형식 문서입니다.


<br>

## <a name="options"></a>옵션

몰입 형 판독기의 특정 동작을 구성 하는 속성을 포함 합니다.

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

| 이름 | Type | 설명 |
| ---- | ---- |------------ |
| uiLang | String | UI의 언어, 값은 IETF BCP 47 언어 태그 형식 (예: en, es)으로 되어 있습니다. 지정 하지 않으면 기본적으로 브라우저 언어가 지정 됩니다. |
| 시간 제한 | Number | 시간 초과 오류가 발생 하 여 [launchAsync](#launchasync) 가 실패 하기 전 까지의 기간 (밀리초)입니다 (기본값은 15000 밀리초). 이 시간 제한은 판독기 페이지의 초기 시작에만 적용 되며 판독기 페이지가 열리고 회전자가 시작 될 때 성공률이 관찰 됩니다. 제한 시간을 조정 해야 하는 것은 아닙니다. |
| uiZIndex | Number | Z-생성 될 iframe의 인덱스입니다 (기본값은 1000). |
| useWebview 보기 | 부울| Chrome 앱과의 호환성을 위해 iframe 대신 웹 보기 태그를 사용 합니다 (기본값은 false). |
| onExit | 기능 | 몰입 형 판독기가 종료 될 때 실행 됩니다. |
| allowFullscreen | 부울 | 전체 화면을 토글할 수 있습니다 (기본값은 true 임). |
| hideExitButton | 부울 | 몰입 형 판독기의 종료 단추 화살표 (기본값은 false)를 숨길지 여부입니다. 이는 몰입 형 판독기를 종료 하기 위해 다른 메커니즘이 제공 된 경우에만 적용 됩니다 (예: 모바일 도구 모음의 뒤로 화살표). |
| cookiePolicy | [CookiePolicy](#cookiepolicy-options) | 몰입 형 판독기의 쿠키 사용에 대 한 설정입니다 (기본값은 *CookiePolicy*). EU 쿠키 준수 정책에 따라 필요한 사용자 동의를 얻으려면 호스트 응용 프로그램의 책임입니다. [쿠키 정책 옵션](#cookiepolicy-options)을 참조 하세요. |
| disableFirstRun | 부울 | 첫 실행 환경을 사용 하지 않도록 설정 합니다. |
| readAloudOptions | [ReadAloudOptions](#readaloudoptions) | 소리내어 읽기를 구성 하는 옵션입니다. |
| translationOptions | [TranslationOptions](#translationoptions) | 번역을 구성 하는 옵션입니다. |
| displayOptions | [DisplayOptions](#displayoptions) | 텍스트 크기, 글꼴 등을 구성 하는 옵션입니다. |
| ... | String | 몰입 형 판독기에서 사용자의 기본 설정을 나타내는 onPreferencesChanged에서 반환 된 문자열입니다. 자세한 내용은 [설정 매개 변수](#settings-parameters) 및 [방법 저장 방법을](./how-to-store-user-preferences.md) 참조 하세요. |
| onPreferencesChanged | 기능 | 사용자의 기본 설정이 변경 될 때 실행 됩니다. 자세한 내용은 [사용자 기본 설정을 저장 하는 방법을](./how-to-store-user-preferences.md) 참조 하세요. |
| customDomain | String | 내부용으로 예약된 속성입니다. 몰입 형 판독기 webapp 호스트 되는 사용자 지정 도메인입니다 (기본값은 null). |

##### `-uiLang`
```Parameters
Type: String
Required: false
Default value: User's browser language 
```

##### `-timeout`
```Parameters
Type: Number
Required: false
Default value: 15000
```

##### `-uiZIndex`
```Parameters
Type: Number
Required: false
Default value: 1000
```

##### `-onExit`
```Parameters
Type: Function
Required: false
Default value: null
```

##### `-preferences`

> [!CAUTION]
> **중요** 몰입 형 판독기 응용 프로그램에서 전송 된 문자열의 값을 프로그래밍 방식으로 변경 하지 마십시오 `-preferences` .이로 인해 예기치 않은 동작이 발생할 수 있으므로 고객에 대 한 사용자 환경이 저하 될 수 있습니다.

```Parameters
Type: String
Required: false
Default value: null
```

##### `-onPreferencesChanged`
```Parameters
Type: Function
Required: false
Default value: null
```

##### `-customDomain`
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

| 이름 | Type | 설명 |
| ---- | ---- |------------ |
| voice | String | 음성, "여성" 또는 "남성" 중 하나입니다. 모든 언어가 성별을 지 원하는 것은 아닙니다. |
| speed | Number | 재생 속도는 0.5에서 2.5 (포함) 사이 여야 합니다. |
| 재생 | 부울 | 몰입 형 판독기가 로드 되 면 자동으로 소리내어 읽기가 시작 됩니다. |

##### `-voice`
```Parameters
Type: String
Required: false
Default value: "Female" or "Male" (determined by language) 
Values available: "Female", "Male"
```

##### `-speed`
```Parameters
Type: Number
Required: false
Default value: 1
Values available: 0.5, 0.75, 1, 1.25, 1.5, 1.75, 2, 2.25, 2.5
```

> [!NOTE]
> 브라우저 제한으로 인해 Safari에서 자동 재생이 지원 되지 않습니다.

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

| 이름 | Type | 설명 |
| ---- | ---- |------------ |
| language | String | 변환 언어를 설정 합니다. 값은 IETF BCP 47 언어 태그 형식 (예: fr-fr, es-MX, zh-cn-Hans)에 있습니다. Word 또는 문서 번역을 자동으로 활성화 하는 데 필요 합니다. |
| autoEnableDocumentTranslation | 부울 | 전체 문서를 자동으로 변환 합니다. |
| autoEnableWordTranslation | 부울 | 자동으로 단어 번역을 사용 하도록 설정 합니다. |

##### `-language`
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

| 이름 | Type | 설명 |
| ---- | ---- |------------ |
| textSize | Number | 선택한 텍스트 크기를 설정 합니다. |
| increaseSpacing | 부울 | 텍스트 간격을 설정 또는 해제할지 여부를 설정 합니다. |
| fontFamily | String | 선택한 글꼴 ("맑은 고딕", "ComicSans" 또는 "Sitka")을 설정 합니다. |

##### `-textSize`
```Parameters
Type: Number
Required: false
Default value: 20, 36 or 42 (Determined by screen size)
Values available: 14, 20, 28, 36, 42, 48, 56, 64, 72, 84, 96
```

##### `-fontFamily`
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

**아래에 나열 된 설정은**참조용 으로만 사용 됩니다. 몰입 형 판독기는 쿠키에 해당 설정 또는 사용자 기본 설정을 저장 합니다. 이 *cookiePolicy* 옵션은 EU 쿠키 규정 준수 법을 준수 하기 위해 기본적으로 쿠키를 사용 **하지 않도록 설정** 합니다. 쿠키를 다시 사용 하도록 설정 하 고 몰입 형 판독기 사용자 기본 설정에 대 한 기본 기능을 복원 하려면 웹 사이트 또는 응용 프로그램이 쿠키를 사용 하도록 설정 하는 사용자 로부터 적절 한 동의를 얻도록 해야 합니다. 그런 다음 몰입 형 판독기에서 쿠키를 다시 사용 하도록 설정 하려면 몰입 형 판독기를 시작할 때 *cookiePolicy* 옵션을 cookiePolicy로 명시적으로 설정 해야 합니다 *.* 다음 표에서는 *cookiePolicy* 옵션을 사용 하도록 설정 된 경우 몰입 판독기가 쿠키에 저장 하는 설정에 대해 설명 합니다.

#### <a name="settings-parameters"></a>설정 매개 변수

| 설정 | Type | 설명 |
| ------- | ---- | ----------- |
| textSize | Number | 선택한 텍스트 크기를 설정 합니다. |
| fontFamily | String | 선택한 글꼴 ("맑은 고딕", "ComicSans" 또는 "Sitka")을 설정 합니다. |
| textSpacing | Number | 텍스트 간격을 설정 또는 해제할지 여부를 설정 합니다. |
| formattingEnabled | 부울 | HTML 서식 지정의 설정 또는 해제 여부를 설정 합니다. |
| 테마(theme) | String | 선택한 테마 (예: "Light", "진한" ...)를 설정 합니다. |
| syllabificationEnabled | 부울 | Syllabification 설정/해제 여부를 설정 합니다. |
| nounHighlightingEnabled | 부울 | 명사 강조 표시를 설정 또는 해제할지 여부를 설정 하는입니다. |
| nounHighlightingColor | String | 선택한 명사 강조 표시 색을 설정 합니다. |
| verbHighlightingEnabled | 부울 | 동사 강조 표시를 설정 또는 해제할지 여부를 설정 합니다. |
| verbHighlightingColor | String | 선택한 동사 강조 표시 색을 설정 합니다. |
| adjectiveHighlightingEnabled | 부울 | 형용사 강조 표시를 설정 또는 해제할지 여부를 설정 합니다. |
| adjectiveHighlightingColor | String | 선택한 형용사 강조 표시 색을 설정 합니다. |
| adverbHighlightingEnabled | 부울 | 부사 강조 표시를 설정 또는 해제할지 여부를 설정 합니다. |
| adverbHighlightingColor | String | 선택한 부사 강조 표시 색을 설정 합니다. |
| pictureDictionaryEnabled | 부울 | 사진 사전을 설정 또는 해제할지 여부를 설정 합니다. |
| posLabelsEnabled | 부울 | 음성의 강조 표시 된 각 부분의 위 첨자 텍스트 레이블을 설정 하거나 해제할지 여부를 설정 합니다.  |

<br>

## <a name="supported-languages"></a>지원되는 언어

몰입 형 판독기의 번역 기능은 다양 한 언어를 지원 합니다. 자세한 내용은 [이 문서](https://www.onenote.com/learningtools/languagesupport)를 참조하세요.

<br>

## <a name="html-support"></a>HTML 지원

서식 지정을 사용 하는 경우 다음 콘텐츠가 몰입 형 판독기에서 HTML로 렌더링 됩니다.

| HTML | 지원 되는 내용 |
| --------- | ----------- |
| 글꼴 스타일 | 굵게, 기울임꼴, 밑줄, 코드, 취소선, 위 첨자, 아래 첨자 |
| 순서가 지정되지 않은 목록 | 디스크, 원, 사각형 |
| 정렬 된 목록 | 10 진수, 위쪽-알파, 아래쪽 알파, 위쪽 로마, 아래쪽 로마 |

지원 되지 않는 태그는 comparably 렌더링 됩니다. 이미지와 테이블은 현재 지원 되지 않습니다.

<br>

## <a name="browser-support"></a>브라우저 지원

몰입 형 판독기를 사용 하 여 최상의 환경을 제공 하려면 다음 브라우저의 최신 버전을 사용 합니다.

* Microsoft Edge
* Internet Explorer 11
* Google Chrome
* Mozilla Firefox
* Apple Safari

<br>

## <a name="next-steps"></a>다음 단계

* [GitHub의 몰입형 리더 SDK](https://github.com/microsoft/immersive-reader-sdk) 탐색
* [빠른 시작: 몰입 형 판독기를 시작 하는 웹 앱 만들기 (c #)](./quickstarts/client-libraries.md?pivots=programming-language-csharp)
