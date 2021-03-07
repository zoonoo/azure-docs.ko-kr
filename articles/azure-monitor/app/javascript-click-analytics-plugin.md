---
title: Application Insights JavaScript SDK 용 분석 자동 수집 플러그 인을 클릭 합니다.
description: Application Insights JavaScript SDK에 대 한 클릭 분석 자동 수집 플러그 인을 설치 하 고 사용 하는 방법입니다.
services: azure-monitor
author: lgayhardt
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 01/14/2021
ms.author: lagayhar
ms.openlocfilehash: e48d669321ad8c58681e8a92e68f2089962bdc17
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/07/2021
ms.locfileid: "102429853"
---
# <a name="click-analytics-auto-collection-plugin-for-application-insights-javascript-sdk"></a>Application Insights JavaScript SDK 용 분석 자동 수집 플러그 인을 클릭 합니다.

이 플러그 인은 웹 페이지에서 클릭 이벤트를 자동으로 추적 하 고 HTML 요소의 data-* 특성을 사용 하 여 이벤트 원격 분석을 채웁니다.

## <a name="getting-started"></a>시작

사용자는 npm를 통해 클릭 분석 자동 수집 플러그 인을 설정할 수 있습니다.

### <a name="npm-setup"></a>npm 설정

Npm 패키지를 설치 합니다.

```bash
npm install --save @microsoft/applicationinsights-clickanalytics-js @microsoft/applicationinsights-web
```

```js

import { ApplicationInsights } from '@microsoft/applicationinsights-web';
import { ClickAnalyticsPlugin } from '@microsoft/applicationinsights-clickanalytics-js';

const clickPluginInstance = new ClickAnalyticsPlugin();
// Click Analytics configuration
const clickPluginConfig = {
  autoCapture: true
};
// Application Insights Configuration
const configObj = {
  instrumentationKey: "YOUR INSTRUMENTATION KEY",
  extensions: [clickPluginInstance],
  extensionConfig: {
    [clickPluginInstance.identifier]: clickPluginConfig
  },
};

const appInsights = new ApplicationInsights({ config: configObj });
appInsights.loadAppInsights();
```

## <a name="how-to-effectively-use-the-plugin"></a>플러그 인을 효과적으로 사용 하는 방법

1. 클릭 이벤트에서 생성 된 원격 분석 데이터는 `customEvents` Azure Portal의 Application Insights 섹션에로 저장 됩니다.
2. `name`CustomEvent의는 다음 규칙에 따라 채워집니다.
    1.  `id`에 제공 된는 `data-*-id` customEvent 이름으로 사용 됩니다. 예를 들어 클릭 한 HTML 요소에 "customEvent-id" = "button1" 특성이 있는 경우 "button1"은 이름이 됩니다.
    2. 이러한 특성이 없고 `useDefaultContentNameOrId` 구성에서가로 설정 되어 있으면 `true` 클릭 된 요소의 HTML 특성 `id` 또는 콘텐츠 이름이 customEvent 이름으로 사용 됩니다.
    3. `useDefaultContentNameOrId`가 false 이면 customEvent 이름이 "not_specified"이 됩니다.

    > [!TIP]
    > `useDefaultContentNameOrId`의미 있는 데이터를 생성 하기 위해를 true로 설정 하는 것이 좋습니다.  
3. `parentDataTag`는 다음 두 가지 작업을 수행합니다.
    1. 이 태그가 있는 경우 플러그 인은 `data-*` 클릭 된 요소의 모든 부모 HTML 요소에서 특성과 값을 페치합니다.
    2. 효율성을 높이기 위해 플러그 인은이 태그를 플래그로 사용 합니다 .이 경우에는 DOM을 더 이상 처리 하지 않습니다 (문서 개체 모델).
    
    > [!CAUTION]
    > `parentDataTag`를 사용 하면 SDK가 사용 되는 HTML 요소 뿐만 아니라 전체 응용 프로그램에서 부모 태그를 검색 하기 시작 합니다.
4. `customDataPrefix` 예를 들어 사용자가 제공 하는은 항상로 시작 해야 `data-` `data-sample-` 합니다. HTML에서 `data-*` 전역 특성은 사용자 지정 데이터 특성 이라고 하는 특성 클래스를 형성 합니다 .이 특성을 사용 하면 HTML과 스크립트의 DOM 표시 간에 독점 정보를 교환할 수 있습니다. 이전 브라우저 (Internet Explorer, Safari)는로 시작 하지 않는 한 특성이 인식 되지 않는 특성을 삭제 `data-` 합니다.

    `*`의는 `data-*` [XML 이름에 대 한 프로덕션 규칙](https://www.w3.org/TR/REC-xml/#NT-Name) 뒤의 임의의 이름으로 대체 될 수 있으며, 다음과 같은 제한 사항이 있습니다.
    - 이름은 "xml"로 시작 해서는 안 되며, 어떤 경우에 든 이러한 문자에 사용 됩니다.
    - 이름에는 세미콜론 (U + 003A)을 포함 하면 안 됩니다.
    - 이름은 대문자를 포함 하지 않아야 합니다.

## <a name="configuration"></a>구성

| 이름                  | Type                               | 기본값 | 설명                                                                                                                              |
| --------------------- | -----------------------------------| --------| ---------------------------------------------------------------------------------------------------------------------------------------- |
| autoCapture           | boolean                            | true    | 자동 캡처 구성.                                                                                                         |
| 콜백(callback)              | [IValueCallback](#ivaluecallback)  | null    | 콜백 구성.                                                                                                                 |
| pageTags              | 문자열                             | null    | 페이지 태그.                                                                                                                               |
| dataTags              | [ICustomDataTags](#icustomdatatags)| null    | 클릭 데이터를 캡처하는 데 사용 되는 기본 태그를 재정의 하기 위해 제공 된 사용자 지정 데이터 태그입니다.                                                           |
| urlCollectHash        | boolean                            | false   | URL의 "#" 문자 뒤에 있는 값의 로깅을 사용 하도록 설정 합니다.                                                                          |
| urlCollectQuery       | boolean                            | false   | URL의 쿼리 문자열에 대 한 로깅을 사용 하도록 설정 합니다.                                                                                      |
| behaviorValidator     | 함수                           | null  | 값 유효성 검사에 사용할 콜백 함수 `data-*-bhvr` 입니다. 자세한 내용은 [behaviorValidator 섹션](#behaviorvalidator)을 참조 하세요.|
| defaultRightClickBhvr | 문자열 (또는) 숫자                 | ''      | 마우스 오른쪽 단추 클릭 이벤트가 발생 하는 경우의 기본 동작 값입니다. 요소에 특성이 있으면이 값이 재정의 됩니다 `data-*-bhvr` . |
| dropInvalidEvents     | boolean                            | false   | 유용한 클릭 데이터를 포함 하지 않는 이벤트를 삭제 하기 위한 플래그입니다.                                                                                   |

### <a name="ivaluecallback"></a>IValueCallback

| 이름               | Type     | 기본값 | 설명                                                                             |
| ------------------ | -------- | ------- | --------------------------------------------------------------------------------------- |
| pageName           | 함수 | null    | 기본 pageName 캡처 동작을 재정의 하는 함수입니다.                           |
| pageActionPageTags | 함수 | null    | PageAction 이벤트 중에 수집 된 기본 pageTags를 확대 하는 콜백 함수입니다.  |
| contentName        | 함수 | null    | 사용자 지정 된 contentName를 채우기 위한 콜백 함수입니다.                                 |

### <a name="icustomdatatags"></a>ICustomDataTags

| 이름                      | Type    | 기본값   | HTML에 사용할 기본 태그 |   설명                                                                                |
|---------------------------|---------|-----------|-------------|----------------------------------------------------------------------------------------------|
| useDefaultContentNameOrId | boolean | false     | 해당 없음         |특정 요소가 기본 customDataPrefix로 태그가 지정 되지 않은 경우 또는 사용자가 customDataPrefix를 제공 하지 않는 경우 contentName에 대 한 표준 HTML 특성을 수집 합니다. |
| customDataPrefix          | 문자열  | `data-`   | `data-*`| 제공 된 접두사로 태그가 지정 된 요소의 콘텐츠 이름과 값을 자동으로 캡처합니다. 예를 들어,는 `data-*-id` `data-<yourcustomattribute>` HTML 태그에서 사용할 수 있습니다.   |
| aiBlobAttributeTag        | 문자열  | `ai-blob` |  `data-ai-blob`| 플러그 인은 개별 특성 대신 JSON blob 특성을 지원 합니다 `data-*` . |
| metaDataPrefix            | 문자열  | null      | 해당 없음  | 캡처 시 제공 된 접두사를 사용 하 여 HTML 헤드의 meta 요소 이름 및 콘텐츠를 자동으로 캡처합니다. 예를 들어는 `custom-` HTML meta 태그에서 사용할 수 있습니다. |
| captureAllMetaDataContent | boolean | false     | 해당 없음   | 모든 HTML 헤드의 메타 요소 이름 및 콘텐츠를 자동으로 캡처합니다. 기본값은 false입니다. 사용 하도록 설정 하면 제공 된 metaDataPrefix이 재정의 됩니다. |
| parentDataTag             | 문자열  | null      |  해당 없음  | 이 태그를 사용 하 여 발견 된 경우 DOM을 탐색 하 여 콘텐츠 이름 및 요소 값을 캡처합니다. 예를 들어는 `data-<yourparentDataTag>` HTML 태그에서 사용할 수 있습니다.|
| dntDataTag                | 문자열  | `ai-dnt`  |  `data-ai-dnt`| 이 특성이 있는 HTML 요소는 원격 분석 데이터를 캡처하기 위해 플러그 인에서 무시 됩니다.|

### <a name="behaviorvalidator"></a>behaviorValidator

BehaviorValidator 함수는 코드의 태그가 지정 된 동작이 미리 정의 된 목록에 맞는지 자동으로 확인 합니다. 이렇게 하면 태그가 지정 된 동작이 기업의 설정 분류와 일치 하 게 됩니다. 대부분의 Azure Monitor 고객은이 기능을 사용 하지만 고급 시나리오에 사용할 수 있는 것은 필수는 아닙니다. 이 확장의 일부로 노출 되는 세 가지 behaviorValidator 콜백 함수가 있습니다. 그러나 노출 된 함수에서 요구 사항을 해결 하지 못하면 사용자가 자신의 콜백 함수를 사용할 수 있습니다. 사용자 고유의 동작 데이터 구조를 가져오기 위해 플러그 인은 데이터 태그에서 동작을 추출 하는 동안이 유효성 검사기 함수를 사용 합니다.

| 이름                   | 설명                                                                        |
| ---------------------- | -----------------------------------------------------------------------------------|
| BehaviorValueValidator | 동작 데이터 구조가 문자열 배열인 경우이 콜백 함수를 사용 합니다.|
| BehaviorMapValidator   | 동작 데이터 구조가 사전 인 경우이 콜백 함수를 사용 합니다.       |
| BehaviorEnumValidator  | 동작 데이터 구조가 열거형 인 경우이 콜백 함수를 사용 합니다.            |

#### <a name="sample-usage-with-behaviorvalidator"></a>BehaviorValidator를 사용한 샘플 사용

```js
var clickPlugin = Microsoft.ApplicationInsights.ClickAnalyticsPlugin;
var clickPluginInstance = new clickPlugin();

// Behavior enum values
var behaviorMap = {
  UNDEFINED: 0, // default, Undefined

  ///////////////////////////////////////////////////////////////////////////////////////////////////
  // Page Experience [1-19]
  ///////////////////////////////////////////////////////////////////////////////////////////////////
  NAVIGATIONBACK: 1, // Advancing to the previous index position within a webpage
  NAVIGATION: 2, // Advancing to a specific index position within a webpage
  NAVIGATIONFORWARD: 3, // Advancing to the next index position within a webpage
  APPLY: 4, // Applying filter(s) or making selections
  REMOVE: 5, // Applying filter(s) or removing selections
  SORT: 6, // Sorting content
  EXPAND: 7, // Expanding content or content container
  REDUCE: 8, // Sorting content
  CONTEXTMENU: 9, // Context Menu
  TAB: 10, // Tab control
  COPY: 11, // Copy the contents of a page
  EXPERIMENTATION: 12, // Used to identify a third party experimentation event
  PRINT: 13, // User printed page
  SHOW: 14, //  Displaying an overlay
  HIDE: 15, //  Hiding an overlay
  MAXIMIZE: 16, //  Maximizing an overlay
  MINIMIZE: 17, // Minimizing an overlay
  BACKBUTTON: 18, //  Clicking the back button

  ///////////////////////////////////////////////////////////////////////////////////////////////////
  // Scenario Process [20-39]
  ///////////////////////////////////////////////////////////////////////////////////////////////////
  STARTPROCESS: 20, // Initiate a web process unique to adopter
  PROCESSCHECKPOINT: 21, // Represents a checkpoint in a web process unique to adopter
  COMPLETEPROCESS: 22, // Page Actions that complete a web process unique to adopter
  SCENARIOCANCEL: 23, // Actions resulting from cancelling a process/scenario

  ///////////////////////////////////////////////////////////////////////////////////////////////////
  // Download [40-59]
  ///////////////////////////////////////////////////////////////////////////////////////////////////
  DOWNLOADCOMMIT: 40, // Initiating an unmeasurable off-network download
  DOWNLOAD: 41, // Initiating a download

  ///////////////////////////////////////////////////////////////////////////////////////////////////
  // Search [60-79]
  ///////////////////////////////////////////////////////////////////////////////////////////////////
  SEARCHAUTOCOMPLETE: 60, // Auto-completing a search query during user input
  SEARCH: 61, // Submitting a search query
  SEARCHINITIATE: 62, // Initiating a search query
  TEXTBOXINPUT: 63, // Typing or entering text in the text box

  ///////////////////////////////////////////////////////////////////////////////////////////////////
  // Commerce [80-99]
  ///////////////////////////////////////////////////////////////////////////////////////////////////
  VIEWCART: 82, // Viewing the cart
  ADDWISHLIST: 83, // Adding a physical or digital good or services to a wishlist
  FINDSTORE: 84, // Finding a physical store
  CHECKOUT: 85, // Before you fill in credit card info
  REMOVEFROMCART: 86, // Remove an item from the cart
  PURCHASECOMPLETE: 87, // Used to track the pageView event that happens when the CongratsPage or Thank You page loads after a successful purchase
  VIEWCHECKOUTPAGE: 88, // View the checkout page
  VIEWCARTPAGE: 89, // View the cart page
  VIEWPDP: 90, // View a PDP
  UPDATEITEMQUANTITY: 91, // Update an item's quantity
  INTENTTOBUY: 92, // User has the intent to buy an item
  PUSHTOINSTALL: 93, // User has selected the push to install option

  ///////////////////////////////////////////////////////////////////////////////////////////////////
  // Authentication [100-119]
  ///////////////////////////////////////////////////////////////////////////////////////////////////
  SIGNIN: 100, // User sign-in
  SIGNOUT: 101, // User sign-out

  ///////////////////////////////////////////////////////////////////////////////////////////////////
  // Social [120-139]
  ///////////////////////////////////////////////////////////////////////////////////////////////////
  SOCIALSHARE: 120, // "Sharing" content for a specific social channel
  SOCIALLIKE: 121, // "Liking" content for a specific social channel
  SOCIALREPLY: 122, // "Replying" content for a specific social channel
  CALL: 123, // Click on a "call" link
  EMAIL: 124, // Click on an "email" link
  COMMUNITY: 125, // Click on a "community" link

  ///////////////////////////////////////////////////////////////////////////////////////////////////
  // Feedback [140-159]
  ///////////////////////////////////////////////////////////////////////////////////////////////////
  VOTE: 140, // Rating content or voting for content
  SURVEYCHECKPOINT: 145, // reaching the survey page/form

  ///////////////////////////////////////////////////////////////////////////////////////////////////
  // Registration, Contact [160-179]
  ///////////////////////////////////////////////////////////////////////////////////////////////////
  REGISTRATIONINITIATE: 161, // Initiating a registration process
  REGISTRATIONCOMPLETE: 162, // Completing a registration process
  CANCELSUBSCRIPTION: 163, // Canceling a subscription
  RENEWSUBSCRIPTION: 164, // Renewing a subscription
  CHANGESUBSCRIPTION: 165, // Changing a subscription
  REGISTRATIONCHECKPOINT: 166, // Reaching the registration page/form

  ///////////////////////////////////////////////////////////////////////////////////////////////////
  // Chat [180-199]
  ///////////////////////////////////////////////////////////////////////////////////////////////////
  CHATINITIATE: 180, // Initiating a chat experience
  CHATEND: 181, // Ending a chat experience

  ///////////////////////////////////////////////////////////////////////////////////////////////////
  // Trial [200-209]
  ///////////////////////////////////////////////////////////////////////////////////////////////////
  TRIALSIGNUP: 200, // Signing-up for a trial
  TRIALINITIATE: 201, // Initiating a trial

  ///////////////////////////////////////////////////////////////////////////////////////////////////
  // Signup [210-219]
  ///////////////////////////////////////////////////////////////////////////////////////////////////
  SIGNUP: 210, // Signing-up for a notification or service
  FREESIGNUP: 211, // Signing-up for a free service

  ///////////////////////////////////////////////////////////////////////////////////////////////////
  // Referals [220-229]
  ///////////////////////////////////////////////////////////////////////////////////////////////////
  PARTNERREFERRAL: 220, // Navigating to a partner's web property

  ///////////////////////////////////////////////////////////////////////////////////////////////////
  // Intents [230-239]
  ///////////////////////////////////////////////////////////////////////////////////////////////////
  LEARNLOWFUNNEL: 230, // Engaging in learning behavior on a commerce page (ex. "Learn more click")
  LEARNHIGHFUNNEL: 231, // Engaging in learning behavior on a non-commerce page (ex. "Learn more click")
  SHOPPINGINTENT: 232, // Shopping behavior prior to landing on a commerce page

  ///////////////////////////////////////////////////////////////////////////////////////////////////
  // Video [240-259]
  ///////////////////////////////////////////////////////////////////////////////////////////////////
  VIDEOSTART: 240, // Initiating a video
  VIDEOPAUSE: 241, // Pausing a video
  VIDEOCONTINUE: 242, // Pausing or resuming a video.
  VIDEOCHECKPOINT: 243, // Capturing predetermined video percentage complete.
  VIDEOJUMP: 244, // Jumping to a new video location.
  VIDEOCOMPLETE: 245, // Completing a video (or % proxy)
  VIDEOBUFFERING: 246, // Capturing a video buffer event
  VIDEOERROR: 247, // Capturing a video error
  VIDEOMUTE: 248, // Muting a video
  VIDEOUNMUTE: 249, // Unmuting a video
  VIDEOFULLSCREEN: 250, // Making a video full screen
  VIDEOUNFULLSCREEN: 251, // Making a video return from full screen to original size
  VIDEOREPLAY: 252, // Making a video replay
  VIDEOPLAYERLOAD: 253, // Loading the video player
  VIDEOPLAYERCLICK: 254, //  Click on a button within the interactive player
  VIDEOVOLUMECONTROL: 255, //  Click on video volume control
  VIDEOAUDIOTRACKCONTROL: 256, // Click on audio control within a video
  VIDEOCLOSEDCAPTIONCONTROL: 257, //  Click on the closed caption control
  VIDEOCLOSEDCAPTIONSTYLE: 258, //  Click to change closed caption style
  VIDEORESOLUTIONCONTROL: 259, //  Click to change resolution

  ///////////////////////////////////////////////////////////////////////////////////////////////////
  //    Advertisement Engagement [280-299]
  ///////////////////////////////////////////////////////////////////////////////////////////////////
  ADBUFFERING: 283, // Ad is buffering
  ADERROR: 284, // Ad error
  ADSTART: 285, // Ad start
  ADCOMPLETE: 286, // Ad complete
  ADSKIP: 287, // Ad skipped
  ADTIMEOUT: 288, // Ad timed-out
  OTHER: 300 // Other
};

// Application Insights Configuration
var configObj = {
  instrumentationKey: "YOUR INSTRUMENTATION KEY",
  extensions: [clickPluginInstance],
  extensionConfig: {
    [clickPluginInstance.identifier]: {
      behaviorValidator: Microsoft.ApplicationInsights.BehaviorMapValidator(behaviorMap),
      defaultRightClickBhvr: 9
    },
  },
};
var appInsights = new Microsoft.ApplicationInsights.ApplicationInsights({
  config: configObj
});
appInsights.loadAppInsights();
```

## <a name="sample-app"></a>샘플 앱

[클릭 분석 자동 수집 플러그 인을 사용 하는 간단한 웹 앱](https://go.microsoft.com/fwlink/?linkid=2152871)입니다.

## <a name="next-steps"></a>다음 단계

- 클릭 분석 자동 수집 플러그 인에 대 한 [GitHub 리포지토리](https://github.com/microsoft/ApplicationInsights-JS/tree/master/extensions/applicationinsights-clickanalytics-js) 및 [NPM 패키지](https://www.npmjs.com/package/@microsoft/applicationinsights-clickanalytics-js) 를 확인 합니다.
- 사용 [환경에서 이벤트 분석](usage-segmentation.md) 을 사용 하 여 상위 클릭 및 사용 가능한 차원과 조각화를 분석 합니다.
- [Log Analytics](../logs/log-analytics-tutorial.md#write-a-query)의 CustomEvents 테이블에서 customdimensions 특성의 내용 필드 아래에서 데이터 클릭을 찾습니다. 추가 지침은 [샘플 앱](https://go.microsoft.com/fwlink/?linkid=2152871) 을 참조 하세요.
- [통합 문서](../visualize/workbooks-overview.md) 를 빌드하거나 [Power BI로 내보내](../logs/log-powerbi.md#integrating-queries) 클릭 데이터의 사용자 지정 시각화를 만듭니다.
