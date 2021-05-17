---
title: Live Video Analytics on IoT Edge를 1.0에서 2.0으로 업그레이드
description: 이 문서에서는 LVA(Live Video Analytics) on Azure IoT Edge 모듈을 업그레이드할 때 고려해야 할 차이점과 다양한 사항을 설명합니다.
author: naiteeks
ms.topic: how-to
ms.author: naiteeks
ms.date: 12/14/2020
ms.openlocfilehash: 49c17946203bc6c3655b1aaf7b04a1ee3ea67388
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98955652"
---
# <a name="upgrading-live-video-analytics-on-iot-edge-from-10-to-20"></a>Live Video Analytics on IoT Edge를 1.0에서 2.0으로 업그레이드

이 문서에서는 LVA(Live Video Analytics) on Azure IoT Edge 모듈을 업그레이드할 때 고려해야 할 차이점과 다양한 사항을 설명합니다.

## <a name="change-list"></a>변경 목록

> [!div class="mx-tdCol4BreakAll"]
> |제목|Live Video Analytics 1.0|Live Video Analytics 2.0|Description|
> |-------------|----------|---------|---------|
> |컨테이너 이미지|mcr.microsoft.com/media/live-video-analytics:1|mcr.microsoft.com/media/live-video-analytics:2|Live Video Analytics on Azure IoT Edge를 위해 Microsoft에서 게시한 docker 이미지입니다.|
> |**MediaGraph 노드** |    |   |   |
> |원본|:::image type="icon" source="./././media/upgrading-lva/check.png"::: RTSP 원본 </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: IoT Hub 메시지 원본 |:::image type="icon" source="./././media/upgrading-lva/check.png"::: RTSP 원본 </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: IoT Hub 메시지 원본 | 미디어 수집 및 메시지의 원본 역할을 하는 MediaGraph 노드입니다.|
> |프로세서|:::image type="icon" source="./././media/upgrading-lva/check.png"::: 동작 감지 프로세서 </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: 프레임 속도 필터 프로세서 </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: HTTP 확장 프로세서 </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: gRPC 확장 프로세서 </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: 신호 게이트 프로세서 |:::image type="icon" source="./././media/upgrading-lva/check.png"::: 동작 감지 프로세서 </br>:::image type="icon" source="./././media/upgrading-lva/remove.png"::: **프레임 속도 필터 프로세서**</br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: HTTP 확장 프로세서 </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: gRPC 확장 프로세서 </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: 신호 게이트 프로세서 | AI 추론 서버에 보내기 전에 미디어를 포맷하는 데 사용할 수 있는 MediaGraph 노드입니다.|
> |Sinks|:::image type="icon" source="./././media/upgrading-lva/check.png"::: 자산 싱크 </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: 파일 싱크 </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: IoT Hub 메시지 싱크|:::image type="icon" source="./././media/upgrading-lva/check.png"::: 자산 싱크 </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: 파일 싱크 </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: IoT Hub 메시지 싱크| 처리된 미디어를 저장하는 데 사용할 수 있는 MediaGraph 노드입니다.|
> |**지원되는 MediaGraph** |    |   |   |
> |토폴로지|:::image type="icon" source="./././media/upgrading-lva/check.png"::: 연속 비디오 녹화 </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: 동작 분석 및 연속 비디오 녹화 </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: 외부 분석 및 연속 비디오 녹화 </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: 동작에 대한 이벤트 기반 기록 </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: AI에 대한 이벤트 기반 기록</br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: 외부 이벤트에 대한 이벤트 기반 기록 </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: 동작 분석 </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: 동작 분석 후 AI 추론 |:::image type="icon" source="./././media/upgrading-lva/check.png"::: 연속 비디오 녹화 </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: 동작 분석 및 연속 비디오 녹화 </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: 외부 분석 및 연속 비디오 녹화 </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: 동작에 대한 이벤트 기반 기록 </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: AI에 대한 이벤트 기반 기록</br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: 외부 이벤트에 대한 이벤트 기반 기록 </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: 동작 분석 </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: 동작 분석 후 AI 추론 </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: **AI 컴퍼지션** </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: **오디오 및 비디오 녹화** </br>  | 매개 변수를 값의 자리 표시자로 사용하여 그래프의 청사진을 정의할 수 있는 MediaGraph 토폴로지입니다.|

## <a name="upgrading-the-live-video-analytics-on-iot-edge-module-from-10-to-20"></a>IoT Edge 모듈의 Live Video Analytics를 1.0에서 2.0으로 업그레이드
IoT Edge 모듈의 Live Video Analytics를 업그레이드할 때 다음 정보를 업데이트해야 합니다.
### <a name="container-image"></a>컨테이너 이미지
배포 템플릿의 `modules` 노드 아래에서 Live Video Analytics on IoT Edge 모듈을 찾고 `image` 필드를 다음과 같이 업데이트합니다.
```
"image": "mcr.microsoft.com/media/live-video-analytics:2"
```
> [!TIP]
> Live Video Analytics on IoT Edge 모듈의 이름을 수정하지 않은 경우 모듈 노드에서 `lvaEdge`를 찾습니다.

### <a name="topology-file-changes"></a>토폴로지 파일 변경
토폴로지 파일에서 **`apiVersion`** 이 2.0로 설정되어 있는지 확인합니다.

### <a name="mediagraph-node-changes"></a>MediaGraph 노드 변경


* 이제 카메라 원본에서 오디오를 비디오와 함께 다운스트림에 전달할 수 있습니다. **`outputSelectors`** 를 사용하여 **오디오만** 또는 **비디오만** 또는 **오디오와 비디오 모두** 그래프 노드에 전달할 수 있습니다. 예를 들어 RTSP 원본에서만 비디오를 선택하여 다운스트림에 전달하려면 RTSP 원본 노드에 다음을 추가합니다.
    ```
    "outputSelectors": [
      {
        "property": "mediaType",
        "operator": "is",
        "value": "video"
      }
    ```
>[!NOTE]
>**`outputSelectors`** 는 선택적 속성입니다. 이를 사용하지 않는 경우 미디어 그래프가 RTSP 카메라에서 오디오(사용되는 경우) 및 비디오를 다운스트림에 전달합니다. 

* `MediaGraphHttpExtension` 및 `MediaGraphGrpcExtension` 프로세서에서 다음 변경 내용을 확인합니다.  
    #### <a name="image-properties"></a>이미지 속성
    * `MediaGraphImageFormatEncoded`은 더 이상 지원되지 않습니다. 
      * 대신 **`MediaGraphImageFormatBmp`** , **`MediaGraphImageFormatJpeg`** 또는 **`MediaGraphImageFormatPng`** 를 사용하세요. 예제:
        ```
        "image": {
                "scale": 
                {
                    "mode": "preserveAspectRatio",
                    "width": "416",
                    "height": "416"
                },
                "format": 
                {
                    "@type": "#Microsoft.Media.MediaGraphImageFormatJpeg"
                }
            }
        ```
        * RAW 이미지를 사용하려면 **`MediaGraphImageFormatRaw`** 를 사용합니다. 이를 사용하려면 다음과 같이 이미지 노드를 업데이트합니다.
        ```
        "image": {
                "scale": 
                {
                    "mode": "preserveAspectRatio",
                    "width": "416",
                    "height": "416"
                },
                "format": 
                {
                    "@type": "#Microsoft.Media.MediaGraphImageFormatRaw",
                    "pixelFormat": "rgba"
                }
            }
        ```
        >[!NOTE]
        > pixelFormat의 가능한 값에는 `yuv420p`, `rgb565be`, `rgb565le`, `rgb555be`, `rgb555le`, `rgb24`, `bgr24`, `argb`, `rgba`, `abgr`, `bgra`가 포함됩니다.  

    #### <a name="extensionconfiguration-for-grpc-extension-processor"></a>gRPC 확장 프로세서를 위한 extensionConfiguration  
    * `MediaGraphGrpcExtension`프로세서에서 **`extensionConfiguration`** 이라는 새 속성을 사용할 수 있습니다. 이 속성은 gRPC 계약의 일부로 사용할 수 있는 선택적 문자열입니다. 이 필드는 데이터를 추론 서버로 전달하는 데 사용할 수 있으며, 추론 서버에서 해당 데이터를 사용하는 방법을 정의할 수 있습니다.  
    이 속성의 한 가지 사용 사례는 여러 AI 모델을 단일 추론 서버에 패키징하는 경우입니다. 이 속성을 사용하면 모든 AI 모델을 위한 노드를 공개할 필요가 없습니다. 대신 그래프 인스턴스의 경우 확장 공급자로서 **`extensionConfiguration`** 을 사용하여 다양한 AI 모델을 선택하는 방법을 정의할 수 있고, 실행 중에 LVA가 이 문자열을 추론 서버로 전달하면 추론 서버는 이 문자열을 사용하여 원하는 AI 모델을 호출할 수 있습니다.  

    #### <a name="ai-composition"></a>AI 컴퍼지션
    * 이제 Live Video Analytics 2.0은 하나의 토폴로지 내에서 둘 이상의 미디어 그래프 확장 프로세서 사용을 지원합니다. RTSP 카메라의 미디어 프레임을 여러 AI 모델에 순차적으로, 병렬로 또는 둘의 조합으로 전달할 수 있습니다. 순차적으로 사용되는 두 AI 모델을 보여 주는 샘플 토폴로지를 참조하세요.

### <a name="disk-space-management-with-sink-nodes"></a>싱크 노드를 사용하여 디스크 공간 관리
* 이제 **파일 싱크** 노드에서 Live Video Analytics on IoT Edge 모듈이 처리된 이미지를 저장하는 데 사용할 수 있는 디스크 공간의 크기를 지정할 수 있습니다. 그러려면 FileSink 노드에 **`maximumSizeMiB`** 필드를 추가합니다. 샘플 파일 싱크 노드는 다음과 같습니다.
    ```
    "sinks": [
      {
        "@type": "#Microsoft.Media.MediaGraphFileSink",
        "name": "fileSink",
        "inputs": [
          {
            "nodeName": "signalGateProcessor",
            "outputSelectors": [
              {
                "property": "mediaType",
                "operator": "is",
                "value": "video"
              }
            ]
          }
        ],
        "fileNamePattern": "sampleFiles-${System.DateTime}",
        "maximumSizeMiB":"512",
        "baseDirectoryPath":"/var/media"
      }
    ]
    ```
* **자산 싱크** 노드에서 Live Video Analytics on IoT Edge 모듈이 처리된 이미지를 저장하는 데 사용할 수 있는 디스크 공간의 크기를 지정할 수 있습니다. 그러려면 자산 싱크 노드에 **`localMediaCacheMaximumSizeMiB`** 필드를 추가합니다. 샘플 자산 싱크 노드는 다음과 같습니다.
    ```
    "sinks": [
      {
        "@type": "#Microsoft.Media.MediaGraphAssetSink",
        "name": "AssetSink",
        "inputs": [
          {
            "nodeName": "signalGateProcessor",
            "outputSelectors": [
              {
                "property": "mediaType",
                "operator": "is",
                "value": "video"
              }
            ]
          }
        ],
        "assetNamePattern": "sampleAsset-${System.GraphInstanceName}",
        "segmentLength": "PT30S",
        "localMediaCacheMaximumSizeMiB":"200",
        "localMediaCachePath":"/var/lib/azuremediaservices/tmp/"
      }
    ]
    ```
    >[!NOTE]
    >  **파일 싱크** 경로는 기본 디렉터리 경로와 파일 이름 패턴으로 분할되는 반면 **자산 싱크** 경로에는 기본 디렉터리 경로가 포함됩니다.  

### <a name="frame-rate-management"></a>프레임 속도 관리
* **`MediaGraphFrameRateFilterProcessor`** 는 **Live Video Analytics on IoT Edge 2.0** 모듈에서 사용되지 않습니다.
    * 처리할 들어오는 비디오를 샘플링하려면 **`samplingOptions`** 속성을 MediaGraph 확장 프로세서(`MediaGraphHttpExtension` 또는 `MediaGraphGrpcExtension`)에 추가합니다.  
     ```
          "samplingOptions": 
          {
            "skipSamplesWithoutAnnotation": "false",  // If true, limits the samples submitted to the extension to only samples which have associated inference(s) 
            "maximumSamplesPerSecond": "20"   // Maximum rate of samples submitted to the extension
          },
    ```
### <a name="module-metrics-in-the-prometheus-format-using-telegraf"></a>Telegraf를 사용하는 Prometheus 형식의 모듈 메트릭
이 릴리스에서는 Telegraf를 사용하여 Azure Monitor에 메트릭을 보낼 수 있습니다. Azure Monitor에서 메트릭을 Log Analytics 또는 이벤트 허브로 보낼 수 있습니다.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/telemetry-schema/telegraf.png" alt-text="이벤트 분류":::

docker를 사용하여 쉽게 사용자 지정 구성으로 Telegraf 이미지를 생성할 수 있습니다. [모니터링 및 로깅](monitoring-logging.md#azure-monitor-collection-via-telegraf) 페이지에서 자세히 알아보세요.

## <a name="next-steps"></a>다음 단계

[Live Video Analytics on IoT Edge 시작](get-started-detect-motion-emit-events-quickstart.md)