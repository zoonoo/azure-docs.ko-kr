---
title: Azure Media Services v3 API에서 얼굴 교정 | Microsoft Docs
description: Azure Media Services v3은 비디오 파일을 제출 하 고, 얼굴을 감지 하 고, 결합 된 단일 패스에서 또는 편집을 허용 하는 두 단계 작업을 통해 비디오 파일을 제출 하 고, 얼굴을 감지 하 고, 흐리게 효과를 적용할 수 있는 얼굴 감지 및 수정 이 문서에서는 v3 API에서 얼굴 탐지기 사전 설정을 사용 하 여 얼굴을 교정 하는 방법을 보여 줍니다.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/25/2021
ms.author: johndeu
ms.custom: devx-track-csharp
ms.openlocfilehash: 6db93aa369366936c90446c41406eafe9ee6e414
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2021
ms.locfileid: "105630491"
---
# <a name="redact-faces-with-the-face-detector-preset"></a>얼굴 감지기 사전 설정으로 얼굴 교정

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Azure Media Services v3 API에는 클라우드에서 흐리게 (확장 가능한 얼굴 검색 및 교정)를 제공 하는 얼굴 감지기 미리 설정이 포함 되어 있습니다. 얼굴 편집을 사용하면 선택한 개인의 얼굴을 흐리게 표시하기 위해 동영상을 수정할 수 있습니다. 공공 안전과 새 미디어 시나리오를 위해 얼굴 편집 서비스를 사용할 수 있습니다. 여러 얼굴을 포함 하는 몇 분 동안의 푸티지에는 수동으로 교정 하는 데 몇 시간이 걸릴 수 있지만,이 미리 설정을 사용 하면 얼굴 편집 프로세스에 몇 가지 간단한 단계만 필요 합니다.

이 문서에서는 **얼굴 탐지기 사전 설정** 에 대 한 세부 정보를 제공 하 고 .net 용 Azure Media Services SDK와 함께 사용 하는 방법을 보여 줍니다.

## <a name="compliance-privacy-and-security"></a>규정 준수, 프라이버시 및 보안
 
Azure Media Services 분석을 사용 하는 데 관련 된 모든 법률을 준수 해야 합니다. Azure Media Services 또는 다른 Azure 서비스를 사용 하지 않는 방식으로 다른 Azure 서비스를 사용 하지 않아야 합니다. 모든 생체 인식 데이터를 포함 하 여 처리 및 저장소에 대 한 Azure Media Services 서비스에 모든 비디오를 업로드 하기 전에 비디오의 개인 으로부터 적절 한 모든 동의을 포함 하 여 적절 한 모든 권한을 보유 해야 합니다. Azure Media Services의 규정 준수, 개인 정보 보호 및 보안에 대해 알아보려면 Azure [Cognitive Services 용어](https://azure.microsoft.com/support/legal/cognitive-services-compliance-and-privacy/)를 사용 합니다. Microsoft의 개인 정보 취급 방침 및 데이터 처리에 대 한 내용은 Microsoft의 [개인 정보](https://privacy.microsoft.com/PrivacyStatement)취급 방침, OST ( [Online Services 약관](https://www.microsoft.com/licensing/product-licensing/products) ) 및 [데이터 처리 추 록](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=67) ("DPA")을 참조 하세요. 데이터 보존, 삭제/소멸을 비롯 한 더 많은 개인 정보 보호 정보는 OST 및 [여기](../video-indexer/faq.md)에서 사용할 수 있습니다. Azure Media Services를 사용 하 여 Cognitive Services 용어, OST, DPA 및 개인정보 취급 방침에 따라 바인딩되는 것에 동의 합니다.


## <a name="face-redaction-modes"></a>얼굴 편집 모드

얼굴 편집은 동일한 개인이 다른 각도에서도 흐리게 표시될 수 있도록 동영상의 모든 프레임에서 얼굴을 감지하고 앞뒤 시간의 얼굴 개체를 추적합니다. 자동화 된 편집 프로세스는 복잡 하며 항상 100% 보장 되는 모든 면에서 흐리게 나타나지 않습니다. 이러한 이유로, 최종 흐림 패스에 대 한 파일을 전송 하기 전에 편집 단계를 통해 흐림 효과의 품질 및 정확도를 향상 시키기 위해 기본 설정에서 2 단계 모드를 사용할 수 있습니다. 

완전히 자동으로 **결합** 된 모드 외에도, 2 단계 워크플로를 사용 하면 얼굴 id 목록을 통해 흐리게 표시 하거나 흐리게 표시 하지 않으려는 얼굴을 선택할 수 있습니다. 프레임별 조정을 임의로 수행 하기 위해 기본 설정에서는 JSON 형식의 메타 데이터 파일을 두 번째 pass에 대 한 입력으로 사용 합니다. 이 워크플로는 **분석** 및 **편집** 모드로 분할됩니다.

한 작업에서 두 작업을 실행 하는 단일 패스에서 두 모드를 쉽게 결합할 수도 있습니다. 이 모드를 **결합** 이라고 합니다.  이 문서에서 샘플 코드는 샘플 소스 파일에 대해 간소화 된 단일 패스 **조합** 모드를 사용 하는 방법을 보여 줍니다.

### <a name="combined-mode"></a>결합된 모드

이렇게 하면 필요한 JSON 파일을 수동으로 편집 하지 않고 교정 된 MP4 비디오 파일이 단일 패스에 생성 됩니다. 작업의 자산 폴더에 있는 출력은 선택한 흐림 효과를 사용 하 여 흐린 얼굴을 포함 하는 단일 mp4 파일이 됩니다. **SourceResolution** 로 설정 된 resolution 속성을 사용 하 여 최상의 교정 결과를 얻습니다.

| 단계 | 파일 이름 | 참고 |
| --- | --- | --- |
| 입력 자산 |"ignite-sample.mp4" |WMV, MOV 또는 MP4 형식의 동영상 |
| 사전 설정 구성 |얼굴 탐지기 구성 | **모드**: FaceRedactorMode,  **blurType**: blurType, **resolution**: AnalysisResolution |
| 출력 자산 |"ignite-redacted.mp4 |얼굴에 흐리게 효과를 적용 한 비디오 |

### <a name="analyze-mode"></a>분석 모드

두 단계 워크플로의 **분석** 단계는 비디오 입력을 가져오고 검색 된 각 얼굴의 얼굴 위치, 얼굴 ID 및 jpg 이미지 목록과 함께 JSON 파일을 생성 합니다. 

| 단계 | 파일 이름 | 참고 |
| --- | --- | --- |
| 입력 자산 |"ignite-sample.mp4" |WMV, MPV 또는 MP4 형식의 동영상 |
| 사전 설정 구성 |얼굴 탐지기 구성 |**모드**: FaceRedactorMode, **해결** 방법: AnalysisResolution. SourceResolution|
| 출력 자산 |ignite-sample_annotations.js |얼굴 위치의 주석 데이터(JSON 형식). 사용자가 흐리게 표시하는 테두리 상자를 수정하기 위해 편집할 수 있습니다. 아래 샘플을 참조하세요. |
| 출력 자산 |foo_thumb%06d.jpg [foo_thumb000001.jpg, foo_thumb000002.jpg] |검색된 각 얼굴을 잘라낸 jpg(숫자는 얼굴의 레이블 ID) |

#### <a name="output-example"></a>출력 예제

```json
{
  "version": 1,
  "timescale": 24000,
  "offset": 0,
  "framerate": 23.976,
  "width": 1280,
  "height": 720,
  "fragments": [
    {
      "start": 0,
      "duration": 48048,
      "interval": 1001,
      "events": [
        [],
        [],
        [],
        [],
        [],
        [],
        [],
        [],
        [],
        [],
        [],
        [],
        [],
        [
          {
            "index": 13,
            "id": 1138,
            "x": 0.29537,
            "y": -0.18987,
            "width": 0.36239,
            "height": 0.80335
          },
          {
            "index": 13,
            "id": 2028,
            "x": 0.60427,
            "y": 0.16098,
            "width": 0.26958,
            "height": 0.57943
          }
        ],

    ... truncated
```

### <a name="redact-blur-mode"></a>교정 (흐리게) 모드

워크플로의 두 번째 단계에서는 하나의 자산으로 결합해야 하는 여러 입력을 사용합니다.

여기에는 흐리게 표시할 ID 목록, 원본 동영상 및 주석 JSON이 포함됩니다. 이 모드에서는 주석을 사용하여 입력 동영상에 흐리게 표시를 적용합니다.

분석 단계의 출력에는 원본 동영상이 포함되지 않습니다. 동영상을 편집 모드 작업의 입력 자산으로 업로드하고 기본 파일로 선택해야 합니다.

| 단계 | 파일 이름 | 참고 |
| --- | --- | --- |
| 입력 자산 |"ignite-sample.mp4" |WMV, MPV 또는 MP4 형식의 동영상. 1단계와 동일한 동영상입니다. |
| 입력 자산 |"ignite-sample_annotations.js" |단계 1의 주석 메타 데이터 파일로, 흐린 얼굴을 변경 하려는 경우에는 선택적으로 수정 합니다. 외부 응용 프로그램, 코드 또는 텍스트 편집기에서 편집 해야 합니다. |
| 입력 자산 | "ignite-sample_IDList.txt" (선택 사항) | 줄로 구분된 편집할 얼굴 ID의 새 목록(선택 사항). 비워 두면 원본의 모든 면에 흐림을 적용 됩니다. 이 목록을 사용 하 여 특정 면을 흐리게 표시 하지 않도록 선택할 수 있습니다. |
| 얼굴 탐지기 사전 설정  |사전 설정 구성  | **모드**: FaceRedactorMode, **blurType**: blurType |
| 출력 자산 |"ignite-sample-redacted.mp4" |주석을 기반으로 흐리게 표시하기가 적용된 동영상 |

#### <a name="example-output"></a>예제 출력

IDList에서 하나의 ID가 선택된 출력입니다.

예제 foo_IDList.txt

```
1
2
3
```

## <a name="blur-types"></a>흐리게 형식

**결합** 또는 **교정** 모드에서는 JSON 입력 구성을 통해 선택할 수 있는 5 가지 흐리게 모드 ( **낮음**, **중간**, **높음**, **상자** 및 **검정**)가 있습니다. 기본적으로 **중간** 이 사용됩니다.

아래에 흐리게 형식의 샘플을 확인할 수 있습니다.

### <a name="example-settings-for-face-detector-preset"></a>얼굴 감지기 사전 설정에 대 한 예제 설정
[!code-csharp[Main](../../../media-services-v3-dotnet/VideoAnalytics/FaceRedactor/Program.cs#FaceDetectorPreset)]


#### <a name="low"></a>낮음

![저해상도 흐림 설정 예제입니다.](./media/media-services-face-redaction/blur-1.png)

#### <a name="med"></a>중간

![중간 해상도 흐림 설정 예제입니다.](./media/media-services-face-redaction/blur-2.png)

#### <a name="high"></a>높음

![고해상도 흐림 설정 예제입니다.](./media/media-services-face-redaction/blur-3.png)

#### <a name="box"></a>Box

![출력을 디버그 하는 데 사용할 Box 모드입니다.](./media/media-services-face-redaction/blur-4.png)

#### <a name="black"></a>흑인

![블랙 박스 모드는 검정색 상자를 포함 하는 모든 면을 포함 합니다.](./media/media-services-face-redaction/blur-5.png)

## <a name="elements-of-the-output-json-file"></a>출력 JSON 파일의 요소

편집 MP는 한 동영상 프레임 내에서 최대 64명의 얼굴을 검색할 수 있는 고정밀도 얼굴 위치 검색 및 추적을 제공합니다. 정면이 최상의 결과를 제공하며 측면 또는 작은 얼굴(24x24 픽셀보다 작거나 같음)의 경우에는 어려울 수 있습니다.

[!INCLUDE [media-services-analytics-output-json](../../../includes/media-services-analytics-output-json.md)]

## <a name="net-sample-code"></a>.NET 샘플 코드

다음 프로그램에서는 **결합** 된 단일 단계 교정 모드를 사용 하는 방법을 보여 줍니다.

- 자산을 만들고 미디어 파일을 자산에 업로드합니다.
- Mode 및 blurType 설정을 사용 하는 얼굴 탐지기 미리 설정을 구성 합니다.
- 얼굴 탐지기 사전 설정을 사용 하 여 새 변환 만들기
- 출력 교정 된 비디오 파일을 다운로드 합니다.

## <a name="download-and-configure-the-sample"></a>샘플 다운로드 및 구성

다음 명령을 사용하여 .NET 샘플이 포함된 GitHub 리포지토리를 컴퓨터에 복제합니다.  

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet.git
 ```

이 샘플은 [FaceRedactor](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/VideoAnalytics/FaceRedactor) 폴더에 있습니다. 다운로드한 프로젝트에서 [appsettings.json](https://github.com/Azure-Samples/media-services-v3-dotnet/blob/main/VideoAnalytics/FaceRedactor/appsettings.json) 파일을 엽니다. 값을 [API에 액세스](./access-api-howto.md)하여 가져온 자격 증명으로 바꿉니다.

**필요에 따라** 리포지토리의 루트에서 **[.sample](https://github.com/Azure-Samples/media-services-v3-dotnet/blob/main/sample.env)** 파일을 복사 하 고 여기에 세부 정보를 입력 한 다음 해당 파일의 이름을 env로 바꿀 수 있습니다 (front의 점을 참고 하 여 리포지토리의 모든 샘플 프로젝트에서 사용할 수 있음) **.**  이렇게 하면 각 샘플에서 파일에 appsettings.js채워진 요구 사항이 제거 되 고 사용자 고유의 Git 허브 복제 된 리포지토리에 대 한 설정을 확인 하는 것이 방지 됩니다.

#### <a name="examples"></a>예제

이 코드는 **결합** 된 모드 흐림 효과를 위해 **FaceDetectorPreset** 를 설정 하는 방법을 보여 줍니다.

[!code-csharp[Main](../../../media-services-v3-dotnet/VideoAnalytics/FaceRedactor/Program.cs#FaceDetectorPreset)]

이 코드 샘플은 만드는 동안 미리 설정이 Transform 개체로 전달 되는 방법을 보여 줍니다. 변환을 만든 후에는 작업에 직접 제출할 수 있습니다.

[!code-csharp[Main](../../../media-services-v3-dotnet/VideoAnalytics/FaceRedactor/Program.cs#FaceDetectorPresetTransform)]

## <a name="next-steps"></a>다음 단계

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>피드백 제공

[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

