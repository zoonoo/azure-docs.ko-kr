---
title: IoT Edge에서 Live Video Analytics를 1.0에서 2.0으로 업그레이드
description: 이 문서에서는 Azure IoT Edge 모듈에서 LVA (Live Video Analytics)를 업그레이드할 때 고려해 야 하는 차이점과 차이점에 대해 설명 합니다.
author: naiteeks
ms.topic: how-to
ms.author: naiteeks
ms.date: 12/14/2020
ms.openlocfilehash: 49c17946203bc6c3655b1aaf7b04a1ee3ea67388
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98955652"
---
# <a name="upgrading-live-video-analytics-on-iot-edge-from-10-to-20"></a>IoT Edge에서 Live Video Analytics를 1.0에서 2.0으로 업그레이드

이 문서에서는 Azure IoT Edge 모듈에서 LVA (Live Video Analytics)를 업그레이드할 때 고려해 야 하는 차이점과 차이점에 대해 설명 합니다.

## <a name="change-list"></a>목록 변경

> [!div class="mx-tdCol4BreakAll"]
> |제목|라이브 비디오 분석 1.0|라이브 비디오 분석 2.0|설명|
> |-------------|----------|---------|---------|
> |컨테이너 이미지|mcr.microsoft.com/media/live-video-analytics:1|mcr.microsoft.com/media/live-video-analytics:2|Azure IoT Edge에서 라이브 비디오 분석을 위해 Microsoft에서 게시 한 docker 이미지|
> |**MediaGraph 노드** |    |   |   |
> |원본|:::image type="icon" source="./././media/upgrading-lva/check.png"::: RTSP 원본 </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: IoT Hub 메시지 원본 |:::image type="icon" source="./././media/upgrading-lva/check.png"::: RTSP 원본 </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: IoT Hub 메시지 원본 | 미디어 수집 및 메시지의 원본 역할을 하는 MediaGraph 노드입니다.|
> |프로세서|:::image type="icon" source="./././media/upgrading-lva/check.png"::: 동작 감지 프로세서 </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: 프레임 속도로 필터 프로세서 </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: Http 확장 프로세서 </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: Grpc 확장 프로세서 </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: 신호 게이트 프로세서 |:::image type="icon" source="./././media/upgrading-lva/check.png"::: 동작 감지 프로세서 </br>:::image type="icon" source="./././media/upgrading-lva/remove.png":::**프레임 속도로 필터 프로세서**</br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: Http 확장 프로세서 </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: Grpc 확장 프로세서 </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: 신호 게이트 프로세서 | AI 유추 서버에 보내기 전에 미디어를 포맷 하는 데 사용할 수 있는 MediaGraph 노드입니다.|
> |Sinks|:::image type="icon" source="./././media/upgrading-lva/check.png"::: 자산 싱크 </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: 파일 싱크 </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: IoT Hub 메시지 싱크|:::image type="icon" source="./././media/upgrading-lva/check.png"::: 자산 싱크 </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: 파일 싱크 </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: IoT Hub 메시지 싱크| 처리 된 미디어를 저장 하는 데 사용할 수 있는 MediaGraph 노드입니다.|
> |**지원 되는 MediaGraphs** |    |   |   |
> |토폴로지|:::image type="icon" source="./././media/upgrading-lva/check.png"::: 연속 비디오 녹화 </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: 동작 분석 및 연속 비디오 녹화 </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: 외부 분석 및 연속 비디오 녹화 </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: 동작에 대 한 이벤트 기반 기록 </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: AI에 대 한 이벤트 기반 기록</br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: 외부 이벤트에 대 한 이벤트 기반 기록 </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: 동작 분석 </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: 동작 분석 후 AI 추론 |:::image type="icon" source="./././media/upgrading-lva/check.png"::: 연속 비디오 녹화 </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: 동작 분석 및 연속 비디오 녹화 </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: 외부 분석 및 연속 비디오 녹화 </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: 동작에 대 한 이벤트 기반 기록 </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: AI에 대 한 이벤트 기반 기록</br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: 외부 이벤트에 대 한 이벤트 기반 기록 </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: 동작 분석 </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: 동작 분석 후 AI 추론 </br>:::image type="icon" source="./././media/upgrading-lva/check.png":::**AI 컴퍼지션** </br>:::image type="icon" source="./././media/upgrading-lva/check.png":::**오디오 및 비디오 녹음/녹화** </br>  | MediaGraph 토폴로지를 사용 하 여 매개 변수를 값의 자리 표시자로 사용 하 여 그래프의 청사진을 정의할 수 있습니다.|

## <a name="upgrading-the-live-video-analytics-on-iot-edge-module-from-10-to-20"></a>IoT Edge 모듈의 Live Video Analytics를 1.0에서 2.0으로 업그레이드
IoT Edge 모듈에서 Live Video Analytics를 업그레이드할 때 다음 정보를 업데이트 해야 합니다.
### <a name="container-image"></a>컨테이너 이미지
배포 템플릿의 노드 아래 IoT Edge 모듈에서 라이브 비디오 분석을 찾고 `modules` `image` 필드를 다음과 같이 업데이트 합니다.
```
"image": "mcr.microsoft.com/media/live-video-analytics:2"
```
> [!TIP]
> IoT Edge 모듈에서 라이브 비디오 분석의 이름을 수정 하지 않은 경우 `lvaEdge` 모듈 노드에서를 찾습니다.

### <a name="topology-file-changes"></a>토폴로지 파일 변경
토폴로지 파일에서 **`apiVersion`** 가 2.0로 설정 되어 있는지 확인 합니다.

### <a name="mediagraph-node-changes"></a>MediaGraph 노드 변경


* 이제 카메라 원본에서 오디오를 비디오와 함께 다운스트림에 전달할 수 있습니다. 의 도움말을 사용 하 여 모든 그래프 노드에 **오디오 전용** 또는 **비디오 전용** 이나 **오디오 및 비디오** 를 전달할 수 있습니다 **`outputSelectors`** . 예를 들어 RTSP 원본 에서만 비디오를 선택 하 고 다운스트림에 전달 하려면 RTSP 원본 노드에 다음을 추가 합니다.
    ```
    "outputSelectors": [
      {
        "property": "mediaType",
        "operator": "is",
        "value": "video"
      }
    ```
>[!NOTE]
>는 **`outputSelectors`** 선택적 속성입니다. 이를 사용 하지 않는 경우 미디어 그래프가 RTSP 카메라 다운스트림에서 오디오 (사용 되는 경우) 및 비디오를 전달 합니다. 

* `MediaGraphHttpExtension`및 `MediaGraphGrpcExtension` 프로세서에서 다음 변경 내용을 확인 합니다.  
    #### <a name="image-properties"></a>이미지 속성
    * `MediaGraphImageFormatEncoded`은 더 이상 지원되지 않습니다. 
      * 대신 **`MediaGraphImageFormatBmp`** 또는 또는를 **`MediaGraphImageFormatJpeg`** 사용 **`MediaGraphImageFormatPng`** 합니다. 예제:
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
        * 원시 이미지를 사용 하려면를 사용 **`MediaGraphImageFormatRaw`** 합니다. 이를 사용 하려면 다음과 같이 이미지 노드를 업데이트 합니다.
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
        > PixelFormat의 가능한 값에는,,,,,,,,, `yuv420p` `rgb565be` `rgb565le` `rgb555be` `rgb555le` `rgb24` `bgr24` `argb` `rgba` `abgr` , 등이 있습니다. `bgra`  

    #### <a name="extensionconfiguration-for-grpc-extension-processor"></a>Grpc 확장 프로세서에 대 한 extensionConfiguration  
    * `MediaGraphGrpcExtension`프로세서에서 라는 새 속성을 사용할 수 있습니다 .이 속성은 **`extensionConfiguration`** grpc 계약의 일부로 사용할 수 있는 선택적 문자열입니다. 이 필드는 데이터를 유추 서버로 전달 하는 데 사용할 수 있으며 유추 서버에서 해당 데이터를 사용 하는 방법을 정의할 수 있습니다.  
    이 속성의 한 가지 사용 사례는 단일 유추 서버에 여러 AI 모델을 패키징하는 경우입니다. 이 속성을 사용 하는 경우 모든 AI 모델에 대 한 노드를 노출할 필요가 없습니다. 대신, 그래프 인스턴스의 경우에는 속성을 사용 하 여 여러 AI 모델을 선택 하는 방법을 정의 하 **`extensionConfiguration`** 고 실행 하는 동안 LVA는이 문자열을 추론 서버로 전달 합니다. 그러면이 문자열을 사용 하 여 원하는 AI 모델을 호출할 수 있습니다.  

    #### <a name="ai-composition"></a>AI 컴퍼지션
    * 이제 라이브 비디오 분석 2.0은 토폴로지 내에서 둘 이상의 미디어 그래프 확장 프로세서 사용을 지원 합니다. RTSP 카메라의 미디어 프레임을 여러 AI 모델에 순차적으로 또는 양쪽의 조합으로 순차적으로 전달할 수 있습니다. 순차적으로 사용 되는 두 AI 모델을 보여 주는 샘플 토폴로지를 참조 하세요.

### <a name="disk-space-management-with-sink-nodes"></a>싱크 노드를 사용 하 여 디스크 공간 관리
* 이제 **파일 싱크** 노드에서 IoT Edge 모듈의 라이브 비디오 분석에서 처리 된 이미지를 저장 하는 데 사용할 수 있는 디스크 공간의 크기를 지정할 수 있습니다. 이렇게 하려면 **`maximumSizeMiB`** FileSink 노드에 필드를 추가 합니다. 샘플 파일 싱크 노드는 다음과 같습니다.
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
* **자산 싱크** 노드에서 IoT Edge 모듈의 라이브 비디오 분석에서 처리 된 이미지를 저장 하는 데 사용할 수 있는 디스크 공간의 크기를 지정할 수 있습니다. 이렇게 하려면 **`localMediaCacheMaximumSizeMiB`** 자산 싱크 노드에 필드를 추가 합니다. 샘플 자산 싱크 노드는 다음과 같습니다.
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
    >  **파일 싱크** 경로는 기본 디렉터리 경로 및 파일 이름 패턴으로 분할 되는 반면 **자산 싱크** 경로에는 기본 디렉터리 경로가 포함 됩니다.  

### <a name="frame-rate-management"></a>프레임 주기 관리
* **`MediaGraphFrameRateFilterProcessor`** 는 **IoT Edge 2.0 모듈의 Live Video Analytics** 에서 더 이상 사용 되지 않습니다.
    * 처리할 들어오는 비디오를 샘플링 하려면 **`samplingOptions`** MediaGraph 확장 프로세서 ( `MediaGraphHttpExtension` 또는)에 속성을 추가 합니다. `MediaGraphGrpcExtension`  
     ```
          "samplingOptions": 
          {
            "skipSamplesWithoutAnnotation": "false",  // If true, limits the samples submitted to the extension to only samples which have associated inference(s) 
            "maximumSamplesPerSecond": "20"   // Maximum rate of samples submitted to the extension
          },
    ```
### <a name="module-metrics-in-the-prometheus-format-using-telegraf"></a>Telegraf를 사용 하는 프로메테우스 형식의 모듈 메트릭
이 릴리스에서는 Telegraf을 사용 하 여 Azure Monitor에 메트릭을 보낼 수 있습니다. 여기에서 메트릭을 Log Analytics 또는 이벤트 허브로 보낼 수 있습니다.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/telemetry-schema/telegraf.png" alt-text="이벤트 분류":::

Docker를 사용 하 여 쉽게 사용자 지정 구성을 사용 하 여 Telegraf 이미지를 생성할 수 있습니다. [모니터링 및 로깅](monitoring-logging.md#azure-monitor-collection-via-telegraf) 페이지에서 자세히 알아보세요.

## <a name="next-steps"></a>다음 단계

[IoT Edge에서 라이브 비디오 분석 시작](get-started-detect-motion-emit-events-quickstart.md)