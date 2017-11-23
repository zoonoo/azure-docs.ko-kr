---
title: "Azure Media Clipper 시작 | Microsoft Docs"
description: "자산을 사용하여 미디어 클립을 빌드하는 도구인 Azure Media Clipper 시작"
services: media-services
keywords: "클립;서브클립;인코딩;미디어"
author: dbgeorge
manager: jasonsue
ms.author: dwgeo
ms.date: 11/10/2017
ms.topic: article
ms.service: media-services
ms.openlocfilehash: 8a4f2c79131664ca0d078fa58c6a75b54243e705
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/15/2017
---
# <a name="create-clips-with-azure-media-clipper"></a>Azure Media Clipper를 사용하여 클립 만들기
이 섹션에서는 Azure Media Clipper를 시작하는 기본 단계를 보여 줍니다. 이후 섹션에서는 Azure Media Clipper 구성 방법에 대한 세부 정보를 제공합니다.

- 먼저 Azure Media Player 및 Azure Media Clipper에 대한 다음 링크를 문서 헤드에 추가합니다. URL에서 Clipper 및 Azure Media Player의 버전을 명시적으로 지정하는 것이 좋습니다. 주문형으로 변경될 수 있으므로 이러한 리소스의 최신 버전을 프로덕션에 사용하지 마세요.

```javascript
<!--Azure Media Player 2.1.4 or later is a prerequisite-->
<link href="//amp.azure.net/libs/amp/2.1.4/skins/amp-default/azuremediaplayer.min.css" rel="stylesheet">
<script src="//amp.azure.net/libs/amp/2.1.4/azuremediaplayer.min.js"></script>
<!--Azure Media Clipper script and stylesheet-->
<link href="//amp.azure.net/libs/amc/0.1.0/azuremediaclipper.css" rel="stylesheet">
<script src="//amp.azure.net/libs/amc/0.1.0/azuremediaclipper.min.js"></script>
```

- 다음으로 Clipper를 인스턴스화할 div 요소에 다음 클래스를 추가합니다.

```javascript
<div id="root" class="azure-subclipper" />
```

선택적으로 어두운 테마를 사용하려면 dark-skin 클래스를 추가합니다.

```javascript
<div id="root" class="azure-subclipper dark-skin" />
```

- 다음 API 호출을 사용하여 Clipper를 인스턴스화합니다.

```javascript
var subclipper = new subclipper({
    selector: '#root',
    logLevel: 'info',
    restVersion: '2.0',
    minimumMarkerGap: 6,
    submitSubclipCallback: onSubmitSubclip,
    singleBitrateMp4Profile: {
            Codecs: [{
                    // Video Codec with single H264Layers
                }, {
                    // Audio Codec
                }]
    },
    multiBitrateMp4Profile: {
            Codecs: [{
                    // Video Codec with multiple H264Layers
                }, {
                    // Audio Codec
                }]
    },
    keymap: {
      // See below for more info
    },
   // Enable the Video Assets panel in the widget to automatically load assets (input contract)
    assetsPanelLoaderCallback: onLoadAssets,
    height: 600,
    subclippingMode: 'all',
    filterAssetsTypes: true,
    speedLevels: [
        { name: '4x', value: 4.0 },
        { name: '3x', value: 3.0 },
        { name: '2x', value: 2.0 },
        { name: 'normal', value: 1.0 },
        { name: '0.75x', value: 0.75 },
        { name: '0.5x', value: 0.5 },
    ],
    resetOnJobDone: false,
    autoplayVideo: true,
    language: 'en'    
});
```

초기화 메서드 호출의 매개 변수는 다음과 같습니다.
- `selector` {REQUIRED, string}: 위젯을 렌더링해야 하는 일치하는 HTML 요소의 CSS 선택기입니다.
- `restVersion` {REQUIRED, string}: 대상으로 지정할 Azure Media Services REST API 버전입니다. REST 버전은 위젯에서 생성된 출력 형식을 정의합니다. 현재는 2.0만 지원됩니다.
- `submitSubclipCallback` {REQUIRED, promise}: 위젯의 “제출” 단추를 클릭할 때 호출되는 콜백 함수입니다. 콜백 함수에는 위젯(렌더링 작업 구성 또는 필터 정의)에서 생성된 출력이 필요합니다. 자세한 내용은 서브클립 콜백 제출을 참조하세요.
- `logLevel` {OPTIONAL, {'info', 'warn', 'error'}}: 브라우저 콘솔에 표시되는 로깅 수준입니다. 기본값: error
- `minimumMarkerGap` {OPTIONAL, int}: 서브클립의 최소 값입니다(초). 참고: 값은 기본값인 6보다 크거나 같아야 합니다.
- `singleBitrateMp4Profile` {OPTIONAL, JSON object}: 위젯에서 생성된 렌더링 작업 구성에 사용할 단일 비트 전송률 mp4 프로필입니다. 제공하지 않으면 [기본 단일 비트 전송률 MP4 프로필](https://docs.microsoft.com/azure/media-services/media-services-mes-preset-h264-single-bitrate-1080p)이 사용됩니다.
- `multiBitrateMp4Profile` {OPTIONAL, JSON object}: 위젯에서 생성된 렌더링 작업 구성에 사용할 다중 비트 전송률 mp4 프로필입니다. 제공하지 않으면 [기본 다중 비트 전송률 MP4 프로필](https://docs.microsoft.com/azure/media-services/media-services-mes-preset-h264-multiple-bitrate-1080p)이 사용됩니다.
- `keymap` {OPTIONAL, json object}: 위젯의 바로 가기 키를 사용자 지정할 수 있습니다. 자세한 정보는 [사용자 지정할 수 있는 바로 가기 키](media-services-azure-media-clipper-keyboard-shortcuts.md)를 참조하세요.
- `assetsPanelLoaderCallback` {OPTIONAL, promise}: 사용자가 창 아래쪽으로 스크롤할 때마다 자산의 새 페이지를 자산 창에 로드하기 위해 비동기적으로 호출되는 콜백 함수입니다. 자세한 내용은 자산 창 로더 콜백을 참조하세요.
- `height` {OPTIONAL, number}: 위젯의 전체 높이입니다. 최소 높이는 자산 창 없이 600px이고 자산 창을 포함하여 850px입니다.
- `subclippingMode` (OPTIONAL, {'all', 'render', 'filter'}): 허용되는 서브클리핑 모드입니다. 기본값은 all입니다.
- `filterAssetsTypes` (OPTIONAL, bool): filterAssetsTypes를 사용하여 [자산] 창에서 [필터] 드롭다운을 표시하거나 숨길 수 있습니다. 기본값은 true입니다.
- `speedLevels` (OPTIONAL, array): speedLevels를 사용하여 비디오 플레이어에 대한 여러 가지 속도 수준을 설정할 수 있습니다. 자세한 내용은 [Azure Media Player 설명서](http://amp.azure.net/libs/amp/latest/docs/#amp.player.playbackspeedoptions)를 참조하세요.
- `resetOnJobDone` (OPTIONAL, bool): resetOnJobDone을 사용하여 작업이 성공적으로 제출될 때 Clipper가 서브클리퍼를 초기 상태로 다시 설정할 수 있습니다.
- `autoplayVideo` (OPTIONAL, bool): autoplayVideo를 사용하여 Clipper가 로드 시 비디오를 자동으로 재생할 수 있습니다. 기본값은 true입니다.
- `language` {OPTIONAL, string}: language는 위젯의 언어를 설정합니다. 지정하지 않으면 위젯은 브라우저 언어를 기반으로 메시지를 지역화하려고 합니다. 브라우저에서 언어가 발견되지 않으면 위젯은 기본적으로 영어로 설정됩니다. 자세한 내용은 지원되는 언어 섹션을 참조하세요.
- `languages` {OPTIONAL, JSON}: languages 매개 변수는 언어의 기본 사전을 사용자가 정의한 사용자 지정 사전으로 바꿉니다. 자세한 내용은 지원되는 언어 섹션을 참조하세요.
- `extraLanguages` (OPTIONAL, JSON): extraLanaguages 매개 변수는 기본 사전에 새 언어를 추가합니다. 자세한 내용은 지원되는 언어 섹션을 참조하세요.

## <a name="typescript-definition"></a>TypeScript 정의
Clipper에 대한 [TypeScript](https://www.typescriptlang.org/) 정의 파일은 [여기](http://amp.azure.net/libs/amc/latest/azuremediaclipper.d.ts)에서 찾을 수 있습니다.

## <a name="azure-media-clipper-api"></a>Azure Media Clipper API
이 섹션에서는 Clipper에서 제공되는 API 화면을 설명합니다.

- `load(assets)`: 자산 목록을 [자산] 창에 로드합니다(`assetsPanelLoaderCallback`와 함께 사용하면 안 됨). 자산을 Clipper로 로드하는 방법에 대한 자세한 내용은 이 [문서](media-services-azure-media-clipper-load-assets.md)를 참조하세요.
- `setLogLevel(level)`: 브라우저 콘솔에 표시할 로깅 수준을 설정합니다. 가능한 값: `info`, `warn`, `error`.
- `setHeight(height)`: 위젯의 전체 높이(픽셀)입니다. 최소 높이는 자산 창 없이 600px이고 자산 창을 포함하여 850px입니다.
- `version`: 위젯 버전을 가져옵니다.

## <a name="configuring-azure-media-clipper"></a>Azure Media Clipper 구성
Azure Media Clipper 구성을 위한 다음 단계를 참조하세요.
- [Azure Media Clipper에 자산 로드](media-services-azure-media-clipper-load-assets.md)
- [사용자 지정 바로 가기 키 구성](media-services-azure-media-clipper-keyboard-shortcuts.md)
- [Clipper에서 클리핑 작업 제출](media-services-azure-media-clipper-submit-job.md)

## <a name="supported-languages"></a>지원되는 언어
Clipper 위젯은 18개 언어로 제공됩니다. 위젯 언어를 설정하려면 초기화 중에 `language` 매개 변수를 정의해야 합니다. 다음 목록에서 원하는 언어 코드 문자열에서 전달합니다.
- 중국어(간체): zh-hans
- 중국어(번체): zh-hant
- 체코어: cs
- 네덜란드어, 플라망어: nl
- 영어: en
- 프랑스어: fr
- 독일어: de
- 헝가리어: hu
- 이탈리아어: it
- 일본어: ja
- 한국어: ko
- 폴란드어: pl
- 포르투갈어(브라질): pt-br
- 포르투갈어(포르투갈): pt-pt
- 러시아어: ru
- 스페인어: es
- 스웨덴어: sv
- 터키어: tr

사용자 지정 언어 사전을 설정하거나 기본 언어 사전을 확장하려면 각각 `languages` 또는 `extraLanguages` 매개 변수를 정의해야 합니다. 다음 JSON 형식을 사용하여 사용자 지정 사전에서 전달합니다.

```javascript
{
      "{language-code}":
          "{message-id}": "{message}"
          ...
      }
      ...
}
```

예를 들어, 다음 샘플은 지역화된 영어 문자열을 정의합니다.

```javascript
export default {
  'VideoPlayer.noPreview': 'No video preview',
  'VideoPlayer.loadAsset': 'You must provide a valid asset',
  'AssetsPanel.name': 'Name',
  'AssetsPanel.type': 'Asset type',
  'AssetsPanel.actions': 'Actions',
  'AssetsPanel.loading': 'Loading...',
  'AssetsPanel.duration': 'Duration',
  'AssetsPanel.resolution': 'Resolution',
  'AssetsPanel.pluralFiles': '{0} assets',
  'AssetsPanel.searchFiles': 'Search assets',
  'AssetsPanel.showTypes': 'Show:',
  'AssetsPanel.typesInfo': 'Rendered assets are actual MP4 files. Dynamic manifest filters are filters applied to a parent asset\'s video segment playlist.',
  'AssetsPanel.filterTypes': 'Filters',
  'AssetsPanel.assetTypes': 'Assets',
  'AssetsPanel.assetsAll': 'All',
  'AssetsPanel.addAsset': 'Add asset to the end',
  'AssetsPanel.addFilter': 'Add filter to the timeline',
  'AssetsPanel.invalidAsset': 'The metadata of this asset is not compatible with the other assets in the timeline',
  'AssetsPanel.addAssetWarning': 'Subclipping on assets with different resolutions may cause resolution autoscaling.',
  'AssetsPanel.live': 'LIVE',
  'AssetsPanel.unknown': 'UNKNOWN',
  'AssetsPanel.minimGapNotMet': 'The asset duration must be greater than the minimum clip duration ({0} seconds)',
  'VideoPlayer.openAdvancedSettings': 'Advanced settings',
  'VideoPlayer.singleBitrate': 'Single-bitrate MP4 (rendered)',
  'VideoPlayer.multiBitrate': 'Multi-bitrate MP4 (rendered)',
  'VideoPlayer.dynamicManifest': 'Dynamic manifest filter',
  'VideoPlayer.ErrorWithMessage': 'There was an error in the video player, code {0}, message: {1}',
  'Common.cancel': 'Cancel',
  'Common.OK': 'OK',
  'AdvancedSettings.framerate': 'Frame rate',
  'Dropdown.select': 'Select an option...',
  'InputAsset.RemoveInput': 'Remove source',
  'Zoom.startTime': 'Start time',
  'Zoom.endTime': 'End time',
  'VideoPlayer.subclips': 'Subclips:',
  'VideoPlayer.length': 'Clip length:',
  'Accordion.scrollLeft': 'Scroll to the left',
  'Accordion.scrollRight': 'Scroll to the right',
  'AdvancedSettings.title': 'Advanced settings',
  'AdvancedSettings.subclipName': 'Subclip name',
  'AdvancedSettings.subclipType': 'Subclipping mode',
  'AdvancedSettings.includeAudioTracks': 'Include audio tracks',
  'AdvancedSettings.subclipTypeInfo': 'Single-bitrate and multi-bitrate MP4s are frame accurate rendered assets. Dynamic manifest filters are group-of-pictures (GOP) accurate filters applied to a parent asset. Creating filters does not create a new asset and does not require encoding. Subclipping jobs on live assets are valid as long as their mark times are within the archive window of the parent asset. Filters are valid as long as the parent asset exists and mark times are within its archive window.',
  'AdvancedSettings.frameRateInfo': 'We autodetect frame rate under most scenarios. however, If we cannot autodetect, choose a frame rate from the dropdown for the selected asset(s).',
  'AdvancedSettings.frameRateError': 'Unable to determine frame rate',
  'AdvancedSettings.subclipNameInfo': 'Choose a name for your subclip.',
  'AdvancedSettings.singleAudioTrack': '1 audio track selected',
  'AdvancedSettings.allAudioTracks': 'All audio tracks selected',
  'AdvancedSettings.someAudioTracks': '{0} audio tracks selected',
  'AdvancedSettings.includeAllAudioTracks': 'Include all audio tracks',
  'AssetsPanel.loadingError': 'Failed to retreive assets from server.',
  'AssetsPanel.retry': 'Retry?',
  'CommandBar.prevFrameTitle': 'Back up one frame',
  'CommandBar.prevKeyFrameTitle': 'Back up one GOP',
  'CommandBar.cleanJob': 'Remove all assets',
  'CommandBar.cleanJobTitle': 'Remove all assets from timeline',
  'CommandBar.cleanJobMessage': 'This will empty all video clips from your timeline.',
  'CommandBar.update': 'Update filter',
  'CommandBar.createFilter': 'Create filter',
  'CommandBar.submit': 'Submit subclipper job',
  'CommandBar.jobErrorTitle': 'Operation failed',
  'CommandBar.jobErrorMessage': 'Your subclip failed to submit. Please try again.',
  'CommandBar.markInTitle': 'Set in at playhead',
  'CommandBar.markInPosition': 'Mark in timecode',
  'CommandBar.markOutTitle': 'Set out at playhead',
  'CommandBar.markOutPosition': 'Mark out timecode',
  'CommandBar.nextFrameTitle': 'Advance one frame',
  'CommandBar.nextKeyFrameTitle': 'Advance one GOP',
  'CommandBar.play': 'Play video',
  'CommandBar.pause': 'Pause video',
  'CommandBar.playPreviewTitle': 'Play subclip preview',
  'CommandBar.pausePreviewTitle': 'Pause subclip preview',
  'CommandBar.redoTitle': 'Redo last action',
  'CommandBar.removeAsset': 'Remove current asset',
  'CommandBar.undoTitle': 'Undo last action',
  'VideoPlayer.errorTitle': 'Error',
  'VideoPlayer.errorMessage': 'There was an error loading the selected asset.',
  'Timeline.markIn': 'Mark in bracket',
  'Timeline.markOut': 'Mark out bracket',
  'Timeline.playHead': 'Play head',
};
```