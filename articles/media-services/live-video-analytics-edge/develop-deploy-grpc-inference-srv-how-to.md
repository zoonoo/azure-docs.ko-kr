---
title: GRPC 유추 서버 개발 및 배포-Azure
description: 이 문서에서는 gRPC 유추 서버를 개발 하 고 배포 하는 방법에 대 한 지침을 제공 합니다.
ms.topic: how-to
ms.date: 12/02/2020
ms.openlocfilehash: dbf46a26626a4143d76385968d092c4f238729da
ms.sourcegitcommit: bb330af42e70e8419996d3cba4acff49d398b399
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/24/2021
ms.locfileid: "105034863"
---
# <a name="how-to-guide--develop-and-deploy-a-grpc-inference-server"></a>방법 가이드 – gRPC 유추 서버 개발 및 배포

## <a name="overview"></a>개요

이 문서에서는 graph 확장을 통해 LVA (Live Video Analytics)와 통합할 수 있도록 gRPC 유추 서버 내에서 선택한 AI 모델을 래핑하는 방법을 보여 줍니다. 

## <a name="suggested-pre-reading"></a>추천 참고 자료

* [미디어 그래프 확장](media-graph-extension-concept.md)
* [gRPC 확장 프로토콜](grpc-extension-protocol.md)
* [유추 메타 데이터 스키마](inference-metadata-schema.md)
* [GRPC 소개](https://www.grpc.io/docs/what-is-grpc/introduction/)
* [proto3 언어 가이드](https://developers.google.com/protocol-buffers/docs/proto3)

## <a name="prerequisites"></a>사전 요구 사항

* [지원 되는 Linux 운영 체제](../../iot-edge/support.md#operating-systems) 또는 Windows 컴퓨터 중 하나를 실행 하는 x86-64 또는 ARM64 장치입니다.
* 컴퓨터에 [Docker를 설치](https://docs.docker.com/desktop/#download-and-install) 합니다.
* [IoT Edge runtime](../../iot-edge/how-to-install-iot-edge.md?tabs=linux)을 설치 합니다.

## <a name="grpc-implementation-steps"></a>gRPC 구현 단계

GRPC 유추 서버를 만들어 라이브 비디오 분석과 함께 확장으로 구현 하려면 다음 단계를 사용 합니다.

### <a name="setup"></a>설정:

[라이브 비디오 분석 모듈을 IoT Edge 장치에 배포 하 고 작동](deploy-iot-edge-device.md)하는 데 필요한 단계를 수행 합니다.

### <a name="high-level-implementation-steps"></a>개략적인 구현 단계:

1. GRPC에서 지원 되는 여러 언어 중 하나를 선택 합니다. c #, c + +, Dart, Go, Java, Node, 객관적인-C, PHP, Python, Ruby.
1. [Proto3 파일을](https://github.com/Azure/live-video-analytics/tree/master/contracts/grpc)사용 하 여 라이브 비디오 분석과 통신 하는 Grpc 서버를 구현 합니다.

    :::image type="content" source="./media/develop-deploy-grpc-inference-srv-how-to/inference-srv-container-process.png" alt-text="proto3 파일을 사용 하 여 라이브 비디오 분석과 통신 하는 gRPC 서버":::

    이 서비스 내에서:
    1. 서버와 클라이언트 간의 세션 설명 메시지 교환을 처리 합니다.
    1. [미디어 샘플 메시지](https://github.com/Azure/live-video-analytics/blob/master/contracts/grpc/extension.proto) 를 처리 하 고 결과를 반환 합니다.

        1. 학습 된 모델을 사용 하는 추론 엔진을 호출 하 여 들어오는 메시지에 대해 추론를 만듭니다.
        1. 엔진에서 추론 결과를 수신 하 고 미디어 샘플로 다시 패키지 한 다음 [추론](https://github.com/Azure/live-video-analytics/blob/master/contracts/grpc/inferencing.proto) 파일을 사용 하 여 라이브 비디오 분석으로 다시 제출 합니다.

            또는 미디어 변환 함수를 미디어 샘플로 호출 합니다.
1. GRPC 서버 구현을 배포 합니다. 이 작업을 수행 하는 방법에는 다음 두 가지가 있습니다.

    1. 라이브 비디오 분석 모듈과 함께 배치 된 IoT 모듈로 배포
    1. 라이브 비디오 분석 모듈을 사용 하 여 데이터를 교환할 수 있는 네트워크 액세스 가능 노드 (온-프레미스 또는 클라우드)에 IoT 모듈로 배포 합니다.
1. 라이브 비디오 분석 모듈을 사용 하 여 라이브 비디오 분석 그래프 토폴로지를 구성 하 고 gRPC 서버를 가리킵니다.

### <a name="recommendation"></a>권장 사항:

동일한 노드에 절감할 때 최상의 성능을 위해 공유 메모리를 사용할 수 있습니다. 이렇게 하려면 프로그래밍 언어/환경에서 노출 하는 Linux 공유 메모리 기능을 사용 해야 합니다.

1. Linux 공유 메모리 핸들을 엽니다.
1. 프레임이 수신 되 면 공유 메모리 내의 주소 오프셋에 액세스 합니다.
1. 라이브 비디오 분석을 통해 메모리를 회수할 수 있도록 프레임 처리 완료를 승인 합니다.

## <a name="create-a-grpc-inference-server"></a>GRPC 유추 서버 만들기

이제 공유 메모리를 통해 [protobuf](https://github.com/Azure/live-video-analytics/tree/master/contracts/grpc) 메시지를 사용 하 여 라이브 비디오 분석에서 비디오 프레임을 수락 하 고, 프레임을 "진한" 또는 "light"로 분류 하 고, [유추 메타 데이터 스키마](inference-metadata-schema.md)를 사용 하 여 라이브 비디오 분석의 IoT Hub 메시지 싱크로 유추 결과를 다시 반환 하는 IOT EDGE 모듈 (외부 AI)을 빌드합니다.

:::image type="content" source="./media/develop-deploy-grpc-inference-srv-how-to/external-ai.png" alt-text="IoT Edge 모듈 (외부 AI) 빌드":::

이 gRPC 유추 서버는 라이브 비디오 분석과 사용자 지정 AI 간에 전송 되는 [protobuf](https://github.com/Azure/live-video-analytics/tree/master/contracts/grpc) 메시지를 처리 하는 .net Core 콘솔 응용 프로그램입니다. 다음은 라이브 비디오 분석과 gRPC 유추 서버 간의 메시지 흐름입니다.

1. Live Video Analytics는 미디어 스트림 설명자 ( [확장](https://github.com/Azure/live-video-analytics/blob/master/contracts/grpc/extension.proto))를 전송 하 고,이를 통해 비디오 프레임을 grpc 스트림 세션을 통해 [protobuf](https://github.com/Azure/live-video-analytics/tree/master/contracts/grpc) 메시지로 서버에 보낼 미디어 스트림 정보를 정의 합니다. 
1. 서버는 스트림 설명자의 유효성을 검사 하 고이를 확인 하 고 원하는 데이터 전송 방법을 설정 합니다.
1. 그러면 라이브 비디오 분석이 비디오 프레임을 포함 하는 MediaSample 파일을 보내기 시작 합니다.
1. 서버는 수신 되는 비디오 프레임을 분석 하 고 사용자가 정의한 이미지 프로세서를 사용 하 여 비디오 프레임을 처리 하기 시작 합니다.
1. 그런 다음 서버는 유추 결과를 사용할 수 있게 되는 즉시 [protobuf](https://github.com/Azure/live-video-analytics/tree/master/contracts/grpc) 메시지로 반환 합니다. 

    :::image type="content" source="./media/develop-deploy-grpc-inference-srv-how-to/grpc-external-srv.png" alt-text="GRPC 유추 서버 만들기":::

비디오 프레임은 [공유 메모리](https://en.wikipedia.org/wiki/Shared_memory#:~:text=In%20computer%20science%2C%20shared%20memory,of%20passing%20data%20between%20programs.) 를 통해 전송 하거나 protobuf 메시지 내에 포함할 수 있습니다. 데이터 전송 모드는 LVA graph 토폴로지에서 구성 하 여 프레임을 전송 하는 방법을 결정할 수 있습니다. 이렇게 하려면 아래와 같이 MediaGraphGrpcExtension 속성의 **dataTransfer** 요소를 구성 합니다.

Embedded:

```
"dataTransfer": {
              "mode": "Embedded"
            }
```

공유 메모리:

```
"dataTransfer": {
              "mode": "sharedMemory",
              "SharedMemorySizeMiB": "20"
            }
```

> [!NOTE]
> 공유 메모리를 통해 통신할 때 IpcMode 값 **은 공유 가능** 으로 설정 되 고 grpc 서버 모듈에서 IpcMode 값을 **컨테이너: {CONTAINER_NAME}** 로 설정 해야 합니다. 이러한 설정은 모듈을 Azure IoT Hub 배포 하는 데 사용 되는 배포 매니페스트 파일에서 수행 됩니다. 다음은 IoT Edge 모듈을 설정할 때 사용할 컨테이너 옵션의 샘플입니다.

라이브 비디오 분석 모듈:

```
{
    "HostConfig": {
        "LogConfig": {
            "Config": {
                "max-size": "10m",
                "max-file": "10"
            }
        },
        "IpcMode": "shareable"
    }
}
```

gRPC 확장 모듈:

```
{
    "HostConfig": {
        "LogConfig": {
            "Config": {
                "max-size": "10m",
                "max-file": "10"
            }
        },
        "IpcMode": "container:lvaEdge"
    }
}
```

> [!NOTE]
> GrpcExtension 내에서 "container: lvaEdge"의 공유 메모리 영역에 액세스할 수 있는지 확인 합니다.

## <a name="sample-grpc-server"></a>샘플 gRPC 서버

GRPC 서버를 개발 하는 방법에 대 한 세부 정보를 이해 하려면 코드 샘플을 살펴보겠습니다.

1. GitHub 링크에서 리포지토리를 복제 [https://github.com/Azure-Samples/live-video-analytics-iot-edge-csharp](https://github.com/Azure-Samples/live-video-analytics-iot-edge-csharp) 합니다.
1. VSCode를 시작 하 고/src/edge/modules/grpcExtension 폴더로 이동 합니다.
1. 파일에 대 한 간단한 연습을 수행해 보겠습니다.

    1. **Program .cs**: 응용 프로그램의 진입점입니다. 이는 호스트 역할을 하는 gRPC 서버를 초기화 하 고 관리 하는 일을 담당 합니다. 이 샘플에서 gRPC 클라이언트 (예: 라이브 비디오 분석)에서 들어오는 gRPC 메시지를 수신 대기 하는 포트는 AppConfig.js의 grpcBindings 구성 요소에 의해 지정 됩니다.
    
        ```json    
        {
          "grpcBinding": "tcp://0.0.0.0:5001"
        }
        ```
    1. **Services\MediaGraphExtensionService.cs**:이 클래스는 [protobuf](https://github.com/Azure/live-video-analytics/tree/master/contracts/grpc) 메시지를 처리 하는 일을 담당 합니다. 메시지의 프레임을 읽고 ImageProcessor를 호출 하 고 유추 결과를 작성 합니다.
이제 gRPC 서버 포트 연결을 구성 하 고 초기화 했으므로 들어오는 gRPC 메시지를 처리할 수 있는 방법을 살펴보겠습니다.

        * GRPC 세션이 설정 되 면 gRPC 서버에서 클라이언트 로부터 수신 하는 첫 번째 메시지 (라이브 비디오 분석)는 [확장](https://github.com/Azure/live-video-analytics/blob/master/contracts/grpc/extension.proto) 파일에 정의 된 MediaStreamDescriptor입니다. 

            ```
            message MediaStreamDescriptor {
              oneof stream_identifier {
                GraphIdentifier graph_identifier = 1;     // Media Stream graph identifier
                string extension_identifier = 2;          // Media Stream extension identifier
              }
              MediaDescriptor media_descriptor = 5;       // Session media information.
              // Additional data transfer properties. If none is set, it is assumed
              // that all content will be transferred through messages (embedded transfer).
              oneof data_transfer_properties {
                SharedMemoryBufferTransferProperties shared_memory_buffer_transfer_properties = 10;
              }
            }
            ```
        * 서버 구현에서 메서드는 `ProcessMediaStreamDescriptor` 비디오 파일에 대해 MediaStreamDescriptor의 mediadescriptor 속성의 유효성을 검사 한 다음, 사용 되는 토폴로지와 배포 템플릿 파일에 따라 데이터 전송 모드 (공유 메모리를 사용 하거나 포함 된 프레임 전송 모드를 사용 하는)를 설정 합니다. 
        * 메시지를 받고 데이터 전송 모드를 성공적으로 설정 하면 gRPC 서버는 MediaStreamDescriptor 메시지를 클라이언트에 게 승인으로 다시 반환 하 여 서버와 클라이언트 간에 연결을 설정 합니다.    
        * Live Video Analytics에서 승인을 받은 후에는 gRPC 서버로 미디어 스트림을 전송 하기 시작 합니다. 서버 구현에서 메서드는 `ProcessMediaStream` 들어오는 MediaStreamMessage를 처리 합니다. MediaStreamMessage는 [확장 프로그램](https://github.com/Azure/live-video-analytics/blob/master/contracts/grpc/extension.proto)에도 정의 되어 있습니다.

            ```
            message MediaStreamMessage {
              uint64 sequence_number = 1;       // Monotonically increasing directional message identifier starting from 1 when the gRPC connection is created
              uint64 ack_sequence_number = 2;   // 0 if this message is not referencing any sent message.
            
            
              // Possible payloads are strongly defined by the contract below
              oneof payload {
                MediaStreamDescriptor media_stream_descriptor = 5;
                MediaSample media_sample = 6;
              }
            }
            ```
        * Appconfig.jsbatchSize의 값에 따라 서버는 메시지를 계속 받고 비디오 프레임을 목록에 저장 합니다. BatchSize 제한에 도달 하면 함수는 이미지를 처리 하는 파일 또는 함수를 호출 합니다. 이 경우 메서드는 BatchImageProcessor .cs 라는 파일을 호출 합니다.
    1. **Processors\BatchImageProcessor.cs**:이 클래스는 이미지를 처리 하는 일을 담당 합니다. 이 샘플에서는 이미지 분류 모델을 사용 했습니다. 처리 되는 모든 이미지에 대해 사용 되는 알고리즘은 다음과 같습니다.

        1. 처리를 위해 바이트 배열의 이미지를 변환 합니다. 자세한 내용은 메서드: `GetBytes(Bitmap image)`
        
            사용 중인 샘플 프로세서는 JPG 인코딩 이미지 프레임만 지원 하 고 픽셀 형식으로는 지원 하지 않습니다. 사용자 지정 프로세서가 다른 인코딩 및/또는 형식을 지원 하는 경우 `IsMediaFormatSupported` 프로세서 클래스의 메서드를 업데이트 합니다.
        1. [Colormatrix 클래스](/dotnet/api/system.drawing.imaging.colormatrix?preserve-view=true&view=dotnet-plat-ext-3.1)를 사용 하 여 이미지를 회색 눈금으로 변환 합니다. 메서드:를 참조 하세요 `ToGrayScale(Image source)` .
        1. 회색 눈금 이미지를 가져온 후에는 회색 배율 바이트의 평균을 계산 합니다.
        1. 평균 값이 127 < 경우 이미지를 "진한"으로 분류 하 고, 그렇지 않은 경우 신뢰도 값이 1.0 인 "light"로 분류 합니다. 메서드:를 참조 하세요 `ProcessImage(List<Image> images)` .

    이 클래스를 수정 하거나 새 클래스를 추가 하 고 메서드를 구현 하 여 사용자 고유의 프로세서 논리를 추가할 수 있습니다.

    ```
    IEnumerable<Inference> ProcessImage(List<Image> images) 
    ```

    새 클래스를 추가한 후에는 MediaGraphExtensionService를 업데이트 하 여 클래스를 인스턴스화하고 처리 논리를 실행 하기 위해 ProcessImage 메서드를 호출 해야 합니다. 

## <a name="connect-with-live-video-analytics-module"></a>라이브 비디오 분석 모듈을 사용 하 여 연결

이제 gRPC 확장 모듈을 만들었으므로 미디어 그래프 토폴로지를 만들어 배포할 것입니다.

1. Visual Studio Code를 사용하여 [이러한 지침](../../iot-edge/tutorial-develop-for-linux.md#build-and-push-your-solution)에 따라 Docker에 로그인합니다.
1. Visual Studio Code에서 src/edge로 이동합니다. env 파일 및 몇 가지 배포 템플릿 파일이 표시됩니다.

    배포 템플릿은 에지 디바이스에 대한 배포 매니페스트를 나타냅니다. 일부 자리 표시자 값을 포함합니다. .env 파일에는 이러한 변수에 대한 값이 포함되어 있습니다.
    
    그런 다음, IoT Edge 솔루션 빌드 및 푸시를 선택합니다. 이 단계에서는 src/edge/deployment.grpc.template.js를 사용 합니다.
        
    :::image type="content" source="./media/develop-deploy-grpc-inference-srv-how-to/build-push-iot-edge-solution.png" alt-text="라이브 비디오 분석 모듈을 사용 하 여 연결":::
    
    이 작업을 수행 하면 grpc 서버 모듈이 빌드되고 이미지를 Azure Container Registry으로 푸시합니다.
    .env 파일에 CONTAINER_REGISTRY_USERNAME_myacr 및 CONTAINER_REGISTRY_PASSWORD_myacr 환경 변수가 정의되었는지 확인합니다.
1. src/cloud-to-device-console-app 폴더로 이동합니다. 여기에는 appsettings.json 파일 및 몇 가지 다른 파일이 표시됩니다.

    * c2d-console-app.csproj - Visual Studio Code에 대한 프로젝트 파일
    * operations.json - 프로그램을 실행하려는 작업의 목록입니다.
    * Program.cs - 샘플 프로그램 코드입니다. 이 코드에서는 다음을 수행합니다.

        * 앱 설정을 로드합니다.
        * Live Video Analytics on IoT Edge 모듈에서 공개하는 직접 메서드를 호출합니다. 모듈을 사용하여 해당 [직접 메서드](direct-methods.md)를 호출하여 라이브 비디오 스트림을 분석할 수 있습니다.
        * 터미널 창에서 프로그램의 출력을 검사하고, 출력 창에서 모듈에서 생성된 이벤트를 검사할 수 있도록 일시 중지합니다.
        * 리소스를 정리하는 직접 메서드를 호출합니다.
1. operations.json 파일을 편집합니다.

    * 그래프 토폴로지의 링크를 변경합니다.

        * `"topologyUrl" : https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/grpcExtension/topology.json`
        * GraphInstanceSet에서 이전 링크의 값과 일치하도록 그래프 토폴로지의 이름을 편집합니다.<br/>`"topologyName": "InferencingWithGrpcExtension"`
        * GraphTopologyDelete에서 이름을 편집합니다.<br/>`"name": "InferencingWithGrpcExtension"`

            토폴로지 (예 `https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/grpcExtensionOpenVINO/2.0/topology.json` :)는 확장 주소를 정의 해야 합니다.
    * 확장 주소 매개 변수

        ```
        {
            "name": "grpcExtensionAddress",
            "type": "String",
            "description": "gRPC LVA Extension Address",
            "default": "https://<REPLACE-WITH-IP-OR-CONTAINER-NAME>/score"
        },
        ```
    * 구성

        ```
        {
            "@apiVersion": "1.0",
            "name": "TopologyName",
            "properties": {
            "processors": [
              {
                "@type": "#Microsoft.Media.MediaGraphGrpcExtension",
                "name": "grpcExtension",
                "endpoint": {
                  "@type": "#Microsoft.Media.MediaGraphUnsecuredEndpoint",
                  "url": "${grpcExtensionAddress}",
                  "credentials": {
                    "@type": "#Microsoft.Media.MediaGraphUsernamePasswordCredentials",
                    "username": "${grpcExtensionUserName}",
                    "password": "${grpcExtensionPassword}"
                  }
                },
                "dataTransfer": {
                        "mode": "sharedMemory",
                        "SharedMemorySizeMiB": "5"
                },
                "image": {
                  "scale": {
                    "mode": "${imageScaleMode}",
                    "width": "${frameWidth}",
                    "height": "${frameHeight}"
                  },
                  "format": {
                    "@type": "#Microsoft.Media.MediaGraphImageFormatEncoded",
                    "encoding": "${imageEncoding}",
                    "quality": "${imageQuality}"
                  }
                }
            ]
          }
        }
        ```
    
## <a name="generate-and-deploy-the-iot-edge-deployment-manifest"></a>IoT Edge 배포 매니페스트 생성 및 배포

배포 매니페스트는 에지 디바이스에 배포되는 모듈 및 해당 모듈의 구성 설정을 정의합니다. 다음 단계에 따라 템플릿 파일에서 매니페스트를 생성한 다음, 에지 디바이스에 배포합니다.
이 단계에서는 src/Edge/config/deployment.grpc.amd64.js에 IoT Edge 배포 매니페스트를 만듭니다. 해당 파일을 마우스 오른쪽 단추로 클릭 하 고 **단일 장치에 대 한 배포 만들기** 를 선택 합니다.

:::image type="content" source="./media/develop-deploy-grpc-inference-srv-how-to/create-deployment-single-device.png" alt-text="IoT Edge 배포 매니페스트 생성 및 배포":::

다음으로, Visual Studio Code에서 IoT Hub 디바이스를 선택하라는 메시지를 표시합니다. lva-sample-device라는 IoT Edge 디바이스를 선택합니다.
이 단계에서는 IoT Edge 디바이스에 에지 모듈을 배포하는 작업이 시작되었습니다. 약 30초 후에 Visual Studio Code의 왼쪽 아래 섹션에서 Azure IoT Hub를 새로 고칩니다. 새 모듈이 lvaExtension 이라는 이름으로 배포 된 것을 볼 수 있습니다.

:::image type="content" source="./media/develop-deploy-grpc-inference-srv-how-to/devices.png" alt-text="새 모듈이 lvaExtension 이라는 이름으로 배포 되었습니다.":::

## <a name="next-steps"></a>다음 단계

[모델을 사용 하 여 라이브 비디오 분석](use-your-model-quickstart.md) 빠른 시작에 설명 된 **이벤트 모니터링 준비** 단계에 따라 샘플을 실행 하 고 결과를 해석 합니다. 또한 [Grpcextension](https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/grpcExtensionOpenVINO/2.0/topology.json), [CVRWithGrpcExtension](https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/cvr-with-grpcExtension/topology.json), [EVRtoAssetsByGrpcExtension 및 [EVROnMotionPlusGrpcExtension](https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/motion-with-grpcExtension/topology.json)샘플 grpc 토폴로지를 확인 하세요.