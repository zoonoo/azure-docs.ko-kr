---
title: Live Video Analytics on IoT Edge 시작 - Azure
description: 이 빠른 시작에서는 Live Video Analytics on IoT Edge를 시작하고 라이브 비디오 스트림에서 동작을 감지하는 방법을 보여 줍니다.
ms.topic: quickstart
ms.date: 04/27/2020
ms.openlocfilehash: 307a81938be3e25b8a6a07bb3696ca3b7647c0aa
ms.sourcegitcommit: 223cea58a527270fe60f5e2235f4146aea27af32
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/01/2020
ms.locfileid: "84261568"
---
# <a name="quickstart-get-started---live-video-analytics-on-iot-edge"></a>빠른 시작: 시작 - IoT Edge의 Live Video Analytics

이 빠른 시작에서는 Live Video Analytics on IoT Edge를 시작하는 단계를 안내합니다. Azure VM을 IoT Edge 디바이스와 시뮬레이션된 라이브 비디오 스트림으로 사용합니다. 설치 단계를 완료한 후에는 해당 스트림에서 동작을 감지하고 보고하는 미디어 그래프를 통해 시뮬레이션된 라이브 비디오 스트림을 실행할 수 있습니다. 아래 다이어그램은 해당 미디어 그래프의 그래픽 표현을 보여 줍니다.

![동작 감지를 기반으로 하는 Live Video Analytics](./media/analyze-live-video/motion-detection.png)

## <a name="prerequisites"></a>필수 구성 요소

* 활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* 개발 머신에 [Azure IoT Tools 확장](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)과 함께 설치된 [Visual Studio Code](https://code.visualstudio.com/)
* 개발 머신이 연결된 네트워크는 Azure IoT Tools가 Azure IoT Hub와 통신할 수 있도록 포트 5671을 통해 AMQP 프로토콜을 허용해야 합니다.

> [!TIP]
> Azure IoT Tools 확장을 설치하는 동안 docker를 설치할지 묻는 메시지가 표시될 수 있습니다. 이 메시지는 무시해도 됩니다.

## <a name="set-up-azure-resources"></a>Azure 리소스 설정

이 자습서에 필요한 Azure 리소스는 다음과 같습니다.

* IoT Hub
* 스토리지 계정
* Azure Media Services 계정
* [IoT Edge 런타임](https://docs.microsoft.com/azure/iot-edge/how-to-install-iot-edge-linux)이 설치된 Azure의 Linux VM

이 빠른 시작을 진행하려면 [Live Video Analytics 리소스 설치 스크립트](https://github.com/Azure/live-video-analytics/tree/master/edge/setup)를 사용하여 Azure 구독에서 위에 언급된 Azure 리소스를 배포하는 것이 좋습니다. 이 작업을 수행하려면 다음 단계를 따르세요.

1. [https://www.microsoft.com]\(https://shell.azure.com ) 로 이동합니다.
1. Cloud Shell을 처음 사용하는 경우 스토리지 계정 및 Microsoft Azure 파일 공유를 만들려면 구독을 선택하라는 메시지가 표시됩니다. Cloud Shell 세션 정보를 저장할 스토리지 계정을 만들려면 "스토리지 만들기"를 선택합니다. 이 스토리지 계정은 Azure Media Services 계정에서 사용하기 위해 스크립트에서 만드는 것과 별개입니다.
1. 셸 창의 왼쪽에 있는 드롭다운 목록에서 "Bash"를 환경으로 선택합니다.

    ![환경 선택기](./media/quickstarts/env-selector.png)

1. 다음 명령을 실행합니다.

    ```
    bash -c "$(curl -sL https://aka.ms/lva-edge/setup-resources-for-samples)"
    ```
    
스크립트가 성공적으로 완료되면 위에서 언급한 모든 리소스가 구독에 표시되어야 합니다. 스크립트 출력의 일부로 IoT 허브 이름을 나열하는 리소스 테이블이 생성됩니다. **"Microsoft.Devices/IotHubs"** 라는 리소스 종류를 찾고 이름을 적어 둡니다. 이는 다음 단계에서 필요합니다. 또한 스크립트는 ~/clouddrive/lva-sample/ 디렉터리에 몇 가지 구성 파일을 생성합니다. 이러한 파일은 이 빠른 시작의 뒷부분에서 필요합니다.

## <a name="deploy-modules-on-your-edge-device"></a>에지 디바이스에 모듈 배포

Cloud Shell에서 다음 명령 실행

```
az iot edge set-modules --hub-name <iot-hub-name> --device-id lva-sample-device --content ~/clouddrive/lva-sample/edge-deployment/deployment.amd64.json
```

위의 명령은 에지 디바이스(Linux VM)에 다음 모듈을 배포합니다.

* Live Video Analytics on IoT Edge(모듈 이름 "lvaEdge")
* RTSP 시뮬레이터(모듈 이름 "rtspsim")

RTSP 시뮬레이터 모듈은 [Live Video Analytics 리소스 설치 스크립트](https://github.com/Azure/live-video-analytics/tree/master/edge/setup)를 실행할 때 에지 디바이스에 복사된 비디오 파일을 사용하여 라이브 비디오 스트림을 시뮬레이션합니다. 이 단계에서는 모듈을 배포했지만 미디어 그래프가 활성화되어 있지 않습니다.

## <a name="configure-azure-iot-tools-extension-in-visual-studio-code"></a>Visual Studio Code에 Azure IoT Tools 확장 구성

Visual Studio Code를 시작하고 아래 지침에 따라 Azure IoT Tools 확장을 사용하여 Azure IoT Hub에 연결합니다.

1. **보기** > **탐색기**를 통해 Visual Studio Code의 탐색기 탭으로 이동하거나 Ctrl+Shift+E를 누릅니다.
1. 탐색기 탭의 왼쪽 하단 모서리에서 "Azure IoT Hub"를 클릭합니다.
1. 추가 옵션 아이콘을 클릭하여 바로 가기 메뉴를 표시하고 "IoT Hub 연결 문자열 설정" 옵션을 선택합니다.
1. 입력 상자가 팝업 창에 표시되고 IoT Hub 연결 문자열을 입력합니다. Cloud Shell의 ~/clouddrive/lva-sample/appsettings.json에서 IoT Hub에 대한 연결 문자열을 가져올 수 있습니다.
1. 연결에 성공하면 에지 디바이스 목록이 표시됩니다. "lva-sample-device"라는 디바이스가 하나 이상 있어야 합니다.
1. 이제 IoT Edge 디바이스를 관리하고 바로 가기 메뉴를 통해 Azure IoT Hub와 상호 작용할 수 있습니다.
1. "lva-sample-device" 아래의 모듈 노드를 확장하여 에지 디바이스에 배포된 모듈을 볼 수 있습니다.

    ![lva-sample-device 노드](./media/quickstarts/lva-sample-device-node.png)

## <a name="use-direct-methods"></a>직접 메서드 사용

이 모듈을 통해 직접 메서드를 호출하여 라이브 비디오 스트림을 분석할 수 있습니다. 모듈에서 제공하는 모든 직접 메서드를 이해하려면 [Live Video Analytics on IoT Edge의 직접 메서드](direct-methods.md)를 읽어 보세요. 

### <a name="invoke-graphtopologylist"></a>GraphTopologyList 호출
이 작업을 수행하면 모듈에 모든 [그래프 토폴로지](media-graph-concept.md#media-graph-topologies-and-instances)가 열거됩니다.

1. "lvaEdge" 모듈을 마우스 오른쪽 단추로 클릭하고 바로 가기 메뉴에서 "모듈 직접 메서드 호출"을 선택합니다.
1. Visual Studio Code 창의 상단 가운데에 편집 상자 팝업 창이 표시됩니다. 편집 상자에 "GraphTopologyList"를 입력하고 Enter 키를 누릅니다.
1. 그런 다음, 편집 상자에 아래 JSON 페이로드를 복사하여 붙여넣고 Enter 키를 누릅니다.

    ```
    {
        "@apiVersion" : "1.0"
    }
    ```

    몇 초 이내에 Visual Studio Code 팝업 창의 출력 창에 다음 응답이 표시됩니다.

    ```
    [DirectMethod] Invoking Direct Method [GraphTopologyList] to [lva-sample-device/lvaEdge] ...
    [DirectMethod] Response from [lva-sample-device/lvaEdge]:
    {
      "status": 200,
      "payload": {
        "value": []
      }
    }
    ```
    
    생성된 그래프 토폴로지가 없으므로 위의 응답은 올바른 것입니다.
    

### <a name="invoke-graphtopologyset"></a>GraphTopologySet 호출

GraphTopologyList 호출에서 설명한 것과 동일한 단계를 따라 다음 JSON을 페이로드로 사용하여 GraphTopologySet를 호출하면 [그래프 토폴로지](media-graph-concept.md#media-graph-topologies-and-instances)를 설정할 수 있습니다.

```
{
    "@apiVersion": "1.0",
    "name": "MotionDetection",
    "properties": {
        "description": "Analyzing live video to detect motion and emit events",
        "parameters": [
            {
                "name": "rtspUserName",
                "type": "String",
                "description": "rtsp source user name.",
                "default": "dummyUserName"
            },
            {
                "name": "rtspPassword",
                "type": "String",
                "description": "rtsp source password.",
                "default": "dummyPassword"
            },
            {
                "name": "rtspUrl",
                "type": "String",
                "description": "rtsp Url"
            }
        ],
        "sources": [
            {
                "@type": "#Microsoft.Media.MediaGraphRtspSource",
                "name": "rtspSource",
                "endpoint": {
                    "@type": "#Microsoft.Media.MediaGraphUnsecuredEndpoint",
                    "url": "${rtspUrl}",
                    "credentials": {
                        "@type": "#Microsoft.Media.MediaGraphUsernamePasswordCredentials",
                        "username": "${rtspUserName}",
                        "password": "${rtspPassword}"
                    }
                }
            }
        ],
        "processors": [
            {
                "@type": "#Microsoft.Media.MediaGraphMotionDetectionProcessor",
                "name": "motionDetection",
                "sensitivity": "medium",
                "inputs": [
                    {
                        "nodeName": "rtspSource"
                    }
                ]
            }
        ],
        "sinks": [
            {
                "@type": "#Microsoft.Media.MediaGraphIoTHubMessageSink",
                "name": "hubSink",
                "hubOutputName": "inferenceOutput",
                "inputs": [
                    {
                        "nodeName": "motionDetection"
                    }
                ]
            }
        ]
    }
}

```


위의 JSON 페이로드는 세 개의 매개 변수(이 중 두 개는 기본값)를 정의하는 그래프 토폴로지를 생성합니다. 토폴로지에는 원본(RTSP 원본) 노드 하나, 프로세서(동작 감지 프로세서) 노드 하나, 싱크(IoT Hub 싱크) 노드 하나가 있습니다.

몇 초 이내에 출력 창에 다음 응답이 표시됩니다.

```
[DirectMethod] Invoking Direct Method [GraphTopologySet] to [lva-sample-device/lvaEdge] ...
[DirectMethod] Response from [lva-sample-device/lvaEdge]:
{
  "status": 201,
  "payload": {
    "systemData": {
      "createdAt": "2020-05-19T07:41:34.507Z",
      "lastModifiedAt": "2020-05-19T07:41:34.507Z"
    },
    "name": "MotionDetection",
    "properties": {
      "description": "Analyzing live video to detect motion and emit events",
      "parameters": [
        {
          "name": "rtspUserName",
          "type": "String",
          "description": "rtsp source user name.",
          "default": "dummyUserName"
        },
        {
          "name": "rtspPassword",
          "type": "String",
          "description": "rtsp source password.",
          "default": "dummyPassword"
        },
        {
          "name": "rtspUrl",
          "type": "String",
          "description": "rtsp Url"
        }
      ],
      "sources": [
        {
          "@type": "#Microsoft.Media.MediaGraphRtspSource",
          "name": "rtspSource",
          "transport": "Tcp",
          "endpoint": {
            "@type": "#Microsoft.Media.MediaGraphUnsecuredEndpoint",
            "url": "${rtspUrl}",
            "credentials": {
              "@type": "#Microsoft.Media.MediaGraphUsernamePasswordCredentials",
              "username": "${rtspUserName}"
            }
          }
        }
      ],
      "processors": [
        {
          "@type": "#Microsoft.Media.MediaGraphMotionDetectionProcessor",
          "sensitivity": "medium",
          "name": "motionDetection",
          "inputs": [
            {
              "nodeName": "rtspSource",
              "outputSelectors": []
            }
          ]
        }
      ],
      "sinks": [
        {
          "@type": "#Microsoft.Media.MediaGraphIoTHubMessageSink",
          "hubOutputName": "inferenceOutput",
          "name": "hubSink",
          "inputs": [
            {
              "nodeName": "motionDetection",
              "outputSelectors": []
            }
          ]
        }
      ]
    }
  }
}
```

상태 코드 201이 반환되며, 이는 새 토폴로지가 생성되었음을 나타냅니다. 다음 단계에서는 다음을 시도해 보세요.

* GraphTopologySet를 다시 호출하고 상태 코드 200이 반환되는지 확인합니다. 상태 코드 200은 기존 토폴로지가 성공적으로 업데이트되었음을 나타냅니다.
* 설명 문자열을 변경하여 GraphTopologySet를 다시 호출합니다. 응답의 상태 코드가 200이고 설명이 새 값으로 업데이트되었는지 확인합니다.
* 이전 섹션에서 설명한 대로 GraphTopologyList를 호출합니다. 이제 반환된 페이로드에서 "MotionDetection" 토폴로지를 볼 수 있습니다.

### <a name="invoke-graphtopologyget"></a>GraphTopologyGet 호출

이제 다음 페이로드를 사용하여 GraphTopologyGet을 호출합니다.

```

{
    "@apiVersion" : "1.0",
    "name" : "MotionDetection"
}
```

몇 초 이내에 출력 창에 다음 응답이 표시됩니다.

```
[DirectMethod] Invoking Direct Method [GraphTopologyGet] to [lva-sample-device/lvaEdge] ...
[DirectMethod] Response from [lva-sample-device/lvaEdge]:
{
  "status": 200,
  "payload": {
    "systemData": {
      "createdAt": "2020-05-19T07:41:34.507Z",
      "lastModifiedAt": "2020-05-19T07:41:34.507Z"
    },
    "name": "MotionDetection",
    "properties": {
      "description": "Analyzing live video to detect motion and emit events",
      "parameters": [
        {
          "name": "rtspUserName",
          "type": "String",
          "description": "rtsp source user name.",
          "default": "dummyUserName"
        },
        {
          "name": "rtspPassword",
          "type": "String",
          "description": "rtsp source password.",
          "default": "dummyPassword"
        },
        {
          "name": "rtspUrl",
          "type": "String",
          "description": "rtsp Url"
        }
      ],
      "sources": [
        {
          "@type": "#Microsoft.Media.MediaGraphRtspSource",
          "name": "rtspSource",
          "transport": "Tcp",
          "endpoint": {
            "@type": "#Microsoft.Media.MediaGraphUnsecuredEndpoint",
            "url": "${rtspUrl}",
            "credentials": {
              "@type": "#Microsoft.Media.MediaGraphUsernamePasswordCredentials",
              "username": "${rtspUserName}"
            }
          }
        }
      ],
      "processors": [
        {
          "@type": "#Microsoft.Media.MediaGraphMotionDetectionProcessor",
          "sensitivity": "medium",
          "name": "motionDetection",
          "inputs": [
            {
              "nodeName": "rtspSource",
              "outputSelectors": []
            }
          ]
        }
      ],
      "sinks": [
        {
          "@type": "#Microsoft.Media.MediaGraphIoTHubMessageSink",
          "hubOutputName": "inferenceOutput",
          "name": "hubSink",
          "inputs": [
            {
              "nodeName": "motionDetection",
              "outputSelectors": []
            }
          ]
        }
      ]
    }
  }
}
```

응답 페이로드에서 다음 사항을 확인하세요.

* 상태 코드가 200이며, 이는 성공을 나타냅니다.
* 페이로드에 "created" 및 "lastModified" 타임스탬프가 있습니다.

### <a name="invoke-graphinstanceset"></a>GraphInstanceSet 호출

다음으로, 위의 그래프 토폴로지를 참조하는 그래프 인스턴스를 만듭니다. [여기](media-graph-concept.md#media-graph-topologies-and-instances)에 설명된 대로, 그래프 인스턴스를 사용하면 동일한 그래프 토폴로지를 사용하는 여러 카메라의 라이브 비디오 스트림을 분석할 수 있습니다.

다음 페이로드를 사용하여 직접 메서드 GraphInstanceSet를 호출합니다.

```
{
    "@apiVersion" : "1.0",
    "name" : "Sample-Graph-1",
    "properties" : {
        "topologyName" : "MotionDetection",
        "description" : "Sample graph description",
        "parameters" : [
            { "name" : "rtspUrl", "value" : "rtsp://rtspsim:554/media/camera-300s.mkv" }
        ]
    }
}
```

다음 사항에 유의하세요.

* 위의 페이로드는 인스턴스를 만들어야 하는 토폴로지 이름(MotionDetection)을 지정합니다.
* 그래프 토폴로지 페이로드에 기본값이 없는 "rtspUrl"에 대한 매개 변수 값이 페이로드에 포함되어 있습니다.

몇 초 이내에 출력 창에 다음 응답이 표시됩니다.

```
[DirectMethod] Invoking Direct Method [GraphInstanceSet] to [lva-sample-device/lvaEdge] ...
[DirectMethod] Response from [lva-sample-device/lvaEdge]:
{
  "status": 201,
  "payload": {
    "name": "Sample-Graph-1",
    "properties": {
      "created": "2020-05-19T07:44:33.868Z",
      "lastModified": "2020-05-19T07:44:33.868Z",
      "state": "Inactive",
      "description": "Sample graph description",
      "topologyName": "MotionDetection",
      "parameters": [
        {
          "name": "rtspUrl",
          "value": "rtsp://rtspsim:554/media/camera-300s.mkv"
        }
      ]
    }
  }
}
```

응답 페이로드에서 다음 사항을 확인하세요.

* 상태 코드가 201이며, 이는 새 인스턴스가 생성되었음을 나타냅니다.
* 상태가 "비활성"이며, 이는 그래프 인스턴스가 생성되었지만 활성화되지 않았음을 나타냅니다. 자세한 내용은 [미디어 그래프 상태](media-graph-concept.md)를 참조하세요.

다음 단계에서는 다음을 시도해 보세요.

* 동일한 페이로드를 사용하여 GraphInstanceSet를 다시 호출하고 반환된 상태 코드가 200인지 확인합니다.
* 다른 설명을 사용하여 GraphInstanceSet를 다시 호출하고 응답 페이로드에 그래프 인스턴스가 성공적으로 업데이트되었음을 나타내는 업데이트된 설명이 있는지 확인합니다.
* 이름을 "Sample-Graph-2"로 변경하여 GraphInstanceSet를 호출하고 응답 페이로드를 확인합니다. 새 그래프 인스턴스가 생성됩니다. 즉, 상태 코드는 201입니다.

### <a name="invoke-graphinstanceactivate"></a>GraphInstanceActivate 호출

이제 그래프 인스턴스를 활성화합니다. 그러면 모듈을 통해 라이브 비디오 흐름이 시작됩니다. 다음 페이로드를 사용하여 직접 메서드 GraphInstanceActivate를 호출합니다.

```
{
    "@apiVersion" : "1.0",
    "name" : "Sample-Graph-1"
}
```

몇 초 이내에 출력 창에 다음 응답이 표시됩니다.

```
[DirectMethod] Invoking Direct Method [GraphInstanceActivate] to [lva-sample-device/lvaEdge] ...
[DirectMethod] Response from [lva-sample-device/lvaEdge]:
{
  "status": 200,
  "payload": null
}
```

응답 페이로드에서 상태 코드 200은 그래프 인스턴스가 성공적으로 활성화되었음을 나타냅니다.

### <a name="invoke-graphinstanceget"></a>GraphInstanceGet 호출

이제 다음 페이로드를 사용하여 직접 메서드 GraphInstanceGet을 호출합니다.

```
 {
     "@apiVersion" : "1.0",
     "name" : "Sample-Graph-1"
 }
 ```

몇 초 이내에 출력 창에 다음 응답이 표시됩니다.

```
[DirectMethod] Invoking Direct Method [GraphInstanceGet] to [lva-sample-device/lvaEdge] ...
[DirectMethod] Response from [lva-sample-device/lvaEdge]:
{
  "status": 200,
  "payload": {
    "name": "Sample-Graph-1",
    "properties": {
      "created": "2020-05-19T07:44:33.868Z",
      "lastModified": "2020-05-19T07:44:33.868Z",
      "state": "Active",
      "description": "graph description",
      "topologyName": "MotionDetection",
      "parameters": [
        {
          "name": "rtspUrl",
          "value": "rtsp://rtspsim:554/media/camera-300s.mkv"
        }
      ]
    }
  }
}
```

응답 페이로드에서 다음 사항을 확인하세요.

* 상태 코드가 200이며, 이는 성공을 나타냅니다.
* 상태가 "활성"이며, 이는 그래프 인스턴스가 현재 "활성" 상태임을 나타냅니다.

## <a name="observe-results"></a>결과 확인

위에서 만들고 활성화한 그래프 인스턴스는 동작 감지 프로세서 노드를 사용하여 들어오는 라이브 비디오 스트림에서 동작을 감지하고 IoT Hub 싱크 노드로 이벤트를 보냅니다. 그런 다음, 이러한 이벤트가 IoT Edge Hub로 릴레이되면 확인할 수 있습니다. 이렇게 하려면 다음 단계를 따르세요.

1. Visual Studio Code에서 탐색기 창을 열고 왼쪽 하단 모서리에서 Azure IoT Hub를 찾습니다.
2. 디바이스 노드를 확장합니다.
3. lva-sample-device를 마우스 오른쪽 단추로 클릭하고 "기본 제공 이벤트 모니터링 시작" 옵션을 선택합니다.

![Iot Hub 이벤트 모니터링 시작](./media/quickstarts/start-monitoring-iothub-events.png)

출력 창에 다음 메시지가 표시됩니다.

```
[IoTHubMonitor] [7:44:33 AM] Message received from [lva-sample-device/lvaEdge]:
{
    "body": {
    "timestamp": 143005362606360,
    "inferences": [
        {
        "type": "motion",
        "motion": {
            "box": {
            "l": 0.828452,
            "t": 0.455224,
            "w": 0.1,
            "h": 0.088889
            }
        }
        },
        {
        "type": "motion",
        "motion": {
            "box": {
            "l": 0.661088,
            "t": 0.597015,
            "w": 0.0625,
            "h": 0.051852
            }
        }
        }
    ]
    },
    "applicationProperties": {
    "topic": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/microsoft.media/mediaservices/{amsAccountName}",
    "subject": "/graphInstances/Sample-Graph-1/processors/motionDetection",
    "eventType": "Microsoft.Media.Graph.Analytics.Inference",
    "eventTime": "2020-05-19T07:45:34.404Z",
    "dataVersion": "1.0"
    }
}
```

위의 메시지에서 다음 사항을 확인하세요.

* 메시지에는 "body" 섹션과 "applicationProperties" 섹션이 포함되어 있습니다. 이러한 섹션이 나타내는 내용을 이해하려면 [IoT Hub 메시지 작성 및 읽기](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-construct) 문서를 참조하세요.
* applicationProperties의 "subject"는 메시지가 생성된 MediaGraph의 노드를 나타냅니다. 이 경우 메시지는 동작 감지 프로세서에서 온 것입니다.
* applicationProperties의 "eventType"은 분석 이벤트임을 나타냅니다.
* "eventTime"은 이벤트가 발생한 시간을 나타냅니다.
* "body"는 분석 이벤트에 대한 데이터를 포함합니다. 이 경우 이벤트는 유추 이벤트이므로 본문에 "timestamp" 및 "inferences" 데이터가 포함됩니다.
* "inferences" 섹션은 "type"이 "motion"이며 "motion" 이벤트에 대한 추가 데이터가 있음을 나타냅니다.

MediaGraph를 얼마간 실행하면 출력 창에 다음과 같은 메시지가 표시됩니다.

```
[IoTHubMonitor] [7:47:45 AM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "sdp": "SDP:\nv=0\r\no=- 1588948185746703 1 IN IP4 172.xx.xx.xx\r\ns=Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\ni=media/camera-300s.mkv\r\nt=0 0\r\na=tool:LIVE555 Streaming Media v2020.04.12\r\na=type:broadcast\r\na=control:*\r\na=range:npt=0-300.000\r\na=x-qt-text-nam:Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\na=x-qt-text-inf:media/camera-300s.mkv\r\nm=video 0 RTP/AVP 96\r\nc=IN IP4 0.0.0.0\r\nb=AS:500\r\na=rtpmap:96 H264/90000\r\na=fmtp:96 packetization-mode=1;profile-level-id=4D0029;sprop-parameter-sets={SPS}\r\na=control:track1\r\n"
  },
  "applicationProperties": {
    "dataVersion": "1.0",
    "topic": "/subscriptions/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/resourceGroups/<my-resource-group>/providers/microsoft.media/mediaservices/<ams-account-name>",
    "subject": "/graphInstances/Sample-Graph-1/sources/rtspSource",
    "eventType": "Microsoft.Media.Graph.Diagnostics.MediaSessionEstablished",
    "eventTime": "2020-05-19T07:47:45.747Z"
  }
}
```

위의 메시지에서 다음 사항을 확인하세요.

* applicationProperties의 "subject"는 메시지가 미디어 그래프의 RTSP 원본 노드에서 생성되었음을 나타냅니다.
* applicationProperties의 "eventType"은 진단 이벤트임을 나타냅니다.
* "body"는 진단 이벤트에 대한 데이터를 포함합니다. 이 경우 이벤트는 MediaSessionEstablished이므로 본문입니다.

## <a name="invoke-additional-direct-methods-to-clean-up"></a>추가 직접 메서드를 호출하여 정리

이제 직접 메서드를 호출하여 그래프 인스턴스를 순서대로 비활성화하고 삭제합니다.

### <a name="invoke-graphinstancedeactivate"></a>GraphInstanceDeactivate 호출

다음 페이로드를 사용하여 직접 메서드 GraphInstanceDeactivate를 호출합니다.

```
{
    "@apiVersion" : "1.0",
    "name" : "Sample-Graph-1"
}
```

몇 초 이내에 출력 창에 다음 응답이 표시됩니다.

```
[DirectMethod] Invoking Direct Method [GraphInstanceDeactivate] to [lva-sample-device/lvaEdge] ...
[DirectMethod] Response from [lva-sample-device/lvaEdge]:
{
  "status": 200,
  "payload": null
}
```

상태 코드 200은 그래프 인스턴스가 성공적으로 비활성화되었음을 나타냅니다.

다음 단계에서는 다음을 시도해 보세요.

* 이전 섹션에서 설명한 것처럼 GraphInstanceGet을 호출하고 "state" 값을 관찰합니다.

### <a name="invoke-graphinstancedelete"></a>GraphInstanceDelete 호출

다음 페이로드를 사용하여 직접 메서드 GraphInstanceDelete를 호출합니다.

```
{
    "@apiVersion" : "1.0",
    "name" : "Sample-Graph-1"
}
```

몇 초 이내에 출력 창에 다음 응답이 표시됩니다.

```
[DirectMethod] Invoking Direct Method [GraphInstanceDelete] to [lva-sample-device/lvaEdge] ...
[DirectMethod] Response from [lva-sample-device/lvaEdge]:
{
  "status": 200,
  "payload": null
}
```

응답에서 상태 코드 200은 그래프 인스턴스가 성공적으로 삭제되었음을 나타냅니다.

### <a name="invoke-graphtopologydelete"></a>GraphTopologyDelete 호출

다음 페이로드를 사용하여 직접 메서드 GraphTopologyDelete를 호출합니다.

```
{
    "@apiVersion" : "1.0",
    "name" : "MotionDetection"
}
```

몇 초 이내에 출력 창에 다음 응답이 표시됩니다.

```
[DirectMethod] Invoking Direct Method [GraphTopologyDelete] to [lva-sample-device/lvaEdge] ...
[DirectMethod] Response from [lva-sample-device/lvaEdge]:
{
  "status": 200,
  "payload": null
}
```

상태 코드 200은 그래프 토폴로지가 성공적으로 삭제되었음을 나타냅니다.

다음 단계에서는 다음을 시도해 보세요.

* GraphTopologyList를 호출하고 모듈에 그래프 토폴로지가 없음을 확인합니다.
* GraphTopologyList와 동일한 페이로드를 사용하여 GraphInstanceList를 호출하고 열거된 그래프 인스턴스가 없음을 확인합니다.

## <a name="clean-up-resources"></a>리소스 정리

이 애플리케이션을 계속 사용할 계획이 없으면 이 빠른 시작에서 생성된 리소스를 삭제합니다.

## <a name="next-steps"></a>다음 단계

* Live Video Analytics on IoT Edge를 사용하여 비디오를 녹화하는 방법 알아보기
* 진단 메시지에 대해 자세히 알아보세요.
