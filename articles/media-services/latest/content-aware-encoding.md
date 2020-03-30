---
title: 콘텐츠 인식 인코딩을 위한 사전 설정 - Azure 미디어 서비스
description: 이 문서에서는 Microsoft Azure 미디어 서비스 v3의 콘텐츠 인식 인코딩에 대해 설명합니다.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 01/24/2020
ms.author: juliako
ms.custom: ''
ms.openlocfilehash: 3ea6c4226a59ba020a477cc5811033ff3dc3c2e9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76772071"
---
# <a name="use-the-content-aware-encoding-preset-to-find-the-optimal-bitrate-value-for-a-given-resolution"></a>콘텐츠 인식 인코딩 사전 설정을 사용하여 지정된 해상도에 대한 최적의 비트 전송률 값을 찾습니다.

[적응형 비트 전송률 스트리밍을](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming)통해 전송을 위한 콘텐츠를 준비하려면 비디오를 여러 비트 전송률(높음에서 낮음)으로 인코딩해야 합니다. 이렇게 하면 비트 레이트도 낮아지므로 비디오 해상도가 낮아지므로 품질이 정상적으로 저하됩니다. 이러한 다중 비트 레이트 인코딩은 소위 인코딩 사다리를 사용합니다 - 해상도 및 비트 레이트 테이블, 미디어 서비스 [내장 인코딩 프리셋참조.](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#encodernamedpreset)

처리 중인 콘텐츠를 알고 있어야 하며 개별 비디오의 복잡성에 맞게 인코딩 래더를 사용자 지정/조정해야 합니다. 각 해상도에서 품질 향상이 지각되지 않는 비트 레이트(bitrate)가 있는데, 인코더는 이 최적의 비트레이트 값으로 작동합니다. 최적화의 다음 수준은 내용에 따라 해상도를 선택하는 것입니다 - 예를 들어, 파워 포인트 프리젠 테이션의 비디오는 720p 이하로 가는 혜택을하지 않습니다. 더 나아가, 인코더는 비디오 내에서 각 샷에 대한 설정을 최적화하는 임무를 수행 할 수 있습니다. 

Microsoft의 [적응형 스트리밍](autogen-bitrate-ladder.md) 사전 설정은 소스 비디오의 품질 및 해상도의 가변성 문제를 부분적으로 해결합니다. 우리의 고객은 콘텐츠의 다양한 혼합을 가지고, 일부는 1080p에서, 다른 720p에서, 그리고 SD와 낮은 해상도에서 몇 가지. 또한, 모든 소스 콘텐츠는 영화 또는 TV 스튜디오에서 고품질 의 메자닌입니다. 적응 형 스트리밍 사전 설정은 비트 레이트 래더가 입력 메자닌의 해상도 또는 평균 비트 레이트 (bitanine)를 초과하지 않도록하여 이러한 문제를 해결합니다. 그러나 이 사전 설정은 해상도 및 비트 레이트 이외의 소스 속성을 검사하지 않습니다.

## <a name="the-content-aware-encoding"></a>콘텐츠 인식 인코딩 

콘텐츠 인식 인코딩 사전 설정은 인코더가 지정된 해상도에 대해 최적의 비트 전송률 값을 찾을 수 있지만 광범위한 계산 분석없이 사용할 수 있는 사용자 지정 논리를 통합하여 "적응 비트 전송률 스트리밍" 메커니즘을 확장합니다. 이 사전 설정은 GOP 정렬 MP4 세트를 생성합니다. 입력 콘텐츠가 주어지면 서비스는 입력 콘텐츠의 초기 경량 분석을 수행하고 결과를 사용하여 적응형 스트리밍을 통해 전달을 위한 최적의 레이어 수, 적절한 비트 전송률 및 해상도 설정을 결정합니다. 이 사전 설정은 출력 파일이 적응형 스트리밍 사전 설정보다 낮은 비트레이트이지만 시청자에게 여전히 좋은 환경을 제공하는 품질인 저복잡성 및 중간 복잡성 비디오에 특히 효과적입니다. 출력에는 비디오 및 오디오 인터리브가 있는 MP4 파일이 포함됩니다.

다음 샘플 그래프는 [PSNR](https://en.wikipedia.org/wiki/Peak_signal-to-noise_ratio) 및 [VMAF와](https://en.wikipedia.org/wiki/Video_Multimethod_Assessment_Fusion)같은 품질 메트릭을 사용하여 비교를 보여 준다. 이 소스는 인코더에 스트레스를 주려는 영화와 TV 프로그램의 복잡한 짧은 클립을 연결하여 만들어졌습니다. 정의에 따르면 이 사전 설정은 콘텐츠마다 다른 결과를 생성합니다 .

![PSNR을 사용한 속도 왜곡(RD) 곡선](media/content-aware-encoding/msrv1.png)

**그림 1: 복잡성이 높은 소스에 대한 PSNR 메트릭을 사용한 속도 왜곡(RD) 곡선**

![VMAF를 사용한 속도 왜곡(RD) 곡선](media/content-aware-encoding/msrv2.png)

**그림 2: 높은 복잡성 소스에 대한 VMAF 메트릭을 사용하는 속도 왜곡(RD) 곡선**

다음은 인코더가 입력 품질이 좋지 않은지 확인할 수 있는 소스 콘텐츠의 다른 범주에 대한 결과입니다(낮은 비트 전송률로 인해 많은 압축 아티팩트). 콘텐츠 인식 사전 설정을 사용하면 인코더는 충분한 비트 전송률이 낮은 하나의 출력 계층만 생성하여 대부분의 클라이언트가 실속 없이 스트림을 재생할 수 있도록 하기로 결정했습니다.

![PSNR을 사용하는 RD 커브](media/content-aware-encoding/msrv3.png)

**그림 3: 낮은 품질의 입력을 위해 PSNR을 사용하는 RD 곡선(1080p)**

![VMAF를 사용한 RD 곡선](media/content-aware-encoding/msrv4.png)

**그림 4: 낮은 품질의 입력을 위해 VMAF를 사용하는 RD 곡선(1080p)**

## <a name="how-to-use-the-content-aware-encoding-preset"></a>콘텐츠 인식 인코딩 사전 설정을 사용하는 방법 

이 사전 설정을 사용하는 변환을 다음과 같이 만들 수 있습니다. 

> [!TIP]
> tranform 출력을 사용하는 자습서는 [다음 단계](#next-steps) 섹션을 참조하십시오. 출력 자산은 MPEG-DASH 및 HLS와 같은 프로토콜의 미디어 서비스 스트리밍 엔드포인트에서 제공될 수 있습니다(자습서와 같이).


```csharp
TransformOutput[] output = new TransformOutput[]
{
   new TransformOutput
   {
      // The preset for the Transform is set to one of Media Services built-in sample presets.
      // You can customize the encoding settings by changing this to use "StandardEncoderPreset" class.
      Preset = new BuiltInStandardEncoderPreset()
      {
         // This sample uses the new preset for content-aware encoding
         PresetName = EncoderNamedPreset.ContentAwareEncoding
      }
   }
};
```

> [!NOTE]
> `ContentAwareEncoding` 사전 설정을 사용한 인코딩 작업은 출력 분에 따라 요금이 청구됩니다. 

## <a name="next-steps"></a>다음 단계

* [자습서: 미디어 서비스 v3를 통해 비디오를 업로드, 인코딩 및 스트리밍](stream-files-tutorial-with-api.md)
* [자습서: URL을 기반으로 원격 파일을 인코딩하고 비디오를 스트리밍 - REST](stream-files-tutorial-with-rest.md)
* [자습서: URL을 기반으로 원격 파일을 인코딩하고 비디오를 스트리밍 - CLI](stream-files-cli-quickstart.md)
* [자습서: URL을 기반으로 원격 파일을 인코딩하고 비디오를 스트리밍 - .NET](stream-files-dotnet-quickstart.md)
* [자습서: URL을 기반으로 원격 파일을 인코딩하고 비디오를 스트리밍 - Node.js](stream-files-nodejs-quickstart.md)
