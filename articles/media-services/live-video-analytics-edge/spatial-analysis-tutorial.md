---
title: 공간 분석을 위해 Computer Vision을 사용하여 라이브 비디오 분석 - Azure
description: 이 자습서에서는 Azure Cognitive Services에서 Computer Vision 공간 분석 AI 기능과 함께 Live Video Analytics를 사용하여 시뮬레이션된 IP 카메라에서 라이브 비디오 피드를 분석하는 방법을 보여 줍니다.
ms.topic: tutorial
ms.date: 09/08/2020
ms.openlocfilehash: 98ee57d4916ac0a8da8b48a9cdd881468b2d75d5
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90929653"
---
# <a name="analyze-live-video-with-computer-vision-for-spatial-analysis-preview"></a>공간 분석을 위해 Computer Vision을 사용하여 라이브 비디오 분석(미리 보기)

이 자습서에서는 [Azure Cognitive Services에서 공간 분석 AI 서비스에 대한 Computer Vision](https://azure.microsoft.com/services/cognitive-services/computer-vision/)과 함께 Live Video Analytics를 사용하여 시뮬레이션된 IP 카메라에서 라이브 비디오 피드를 분석하는 방법을 보여 줍니다. 이 유추 서버를 통해 스트리밍 비디오를 분석하여 물리적 공간에서 사람과 움직임 간의 공간 관계를 이해하는 방법을 확인할 수 있습니다.  비디오 피드의 프레임 하위 집합이 이 유추 서버에 전송되고 결과가 IoT Edge 허브로 전송되며, 일부 조건이 충족되면 비디오 클립이 기록되고 Azure Media Services 자산으로 저장됩니다.

이 자습서에서는 다음 작업을 수행합니다.

> [!div class="checklist"]
> * 리소스 설정
> * 코드를 검사합니다.
> * 샘플 코드 실행
> * 이벤트 모니터링

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="suggested-pre-reading"></a>추천 참고 자료

시작하기 전에 다음 문서를 읽어보세요.

* [Live Video Analytics on IoT Edge 개요](overview.md)
* [Live Video Analytics on IoT Edge 용어](terminology.md)
* [미디어 그래프 개념](media-graph-concept.md)
* [이벤트 기반 비디오 녹화](event-based-video-recording-concept.md)
* [자습서: IoT Edge 모듈 개발](https://docs.microsoft.com/azure/iot-edge/tutorial-develop-for-linux)
* [Azure Stack Edge에 Live Video Analytics 배포](deploy-azure-stack-edge-how-to.md) 

## <a name="prerequisites"></a>사전 요구 사항

다음은 공간 분석 모듈을 Live Video Analytics 모듈에 연결하기 위한 필수 조건입니다.

* 개발 컴퓨터의 [Visual Studio Code](https://code.visualstudio.com/). [Azure IoT Tools 확장](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)이 있는지 확인합니다.
    * 개발 컴퓨터가 연결된 네트워크에서 5671 포트를 통해 고급 메시지 큐 프로토콜을 허용하는지 확인합니다. 이렇게 설정하면 Azure IoT Tools에서 Azure IoT Hub와 통신할 수 있습니다.
* GPU 가속 기능이 있는 [Azure Stack Edge](https://azure.microsoft.com/products/azure-stack/edge/).  
    GPU 가속 기능이 있는 Azure Stack Edge를 사용하는 것이 좋지만 [NVIDIA Tesla T4 GPU](https://www.nvidia.com/en-us/data-center/tesla-t4/)가 있는 다른 모든 디바이스에서 컨테이너가 실행됩니다. 
* 공간 분석을 위한 [Azure Cognitive Service Computer Vision 컨테이너](https://azure.microsoft.com/services/cognitive-services/computer-vision/).  
    이 컨테이너를 사용하려면 연결된 **API 키** 및 **엔드포인트 URI**를 가져오기 위한 Computer Vision 리소스가 있어야 합니다. API 키는 Azure Portal의 Computer Vision 개요 및 키 페이지에서 확인할 수 있습니다. 컨테이너를 시작하려면 키와 엔드포인트가 필요합니다.

## <a name="overview"></a>개요

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/spatial-analysis-tutorial/overview.png" alt-text="공간 분석 개요":::
 
이 다이어그램에서는 이 자습서의 신호 흐름을 보여 줍니다. [에지 모듈](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555)은 RTSP(Real-Time Streaming Protocol) 서버를 호스팅하는 IP 카메라를 시뮬레이션합니다. [RTSP 원본](media-graph-concept.md#rtsp-source) 노드는 이 서버에서 비디오 피드를 가져와서 비디오 프레임을 [프레임 속도 필터 프로세서](media-graph-concept.md#frame-rate-filter-processor) 노드로 보냅니다. 이 프로세서는 MediaGraphCognitiveServicesVisionExtension 프로세서 노드에 도달하는 비디오 스트림의 프레임 속도를 제한합니다.

MediaGraphCognitiveServicesVisionExtension 노드는 프록시의 역할을 수행합니다. 비디오 프레임을 지정된 이미지 형식으로 변환합니다. 그런 다음 **공유 메모리**를 통해 이미지를 gRPC 엔드포인트 뒤에서 AI 작업을 실행하는 다른 에지 모듈에 릴레이합니다. 이 예에서 에지 모듈은 공간 분석 모듈입니다. MediaGraphCognitiveServicesVisionExtension 프로세서 노드는 다음 두 가지 작업을 수행합니다.

* 결과를 수집하고 [IoT Hub 싱크](media-graph-concept.md#iot-hub-message-sink) 노드에 이벤트를 게시합니다. 그런 다음, 노드에서 이러한 이벤트를 [IoT Edge Hub](https://docs.microsoft.com/azure/iot-edge/iot-edge-glossary#iot-edge-hub)에 보냅니다. 
* 또한 [신호 게이트 프로세서](media-graph-concept.md#signal-gate-processor)를 사용하여 RTSP 원본에서 30초 비디오 클립을 캡처하여 Media Services 자산으로 저장합니다.

## <a name="create-the-computer-vision-resource"></a>Computer Vision 리소스 만들기

[Azure Portal](https://docs.microsoft.com/azure/iot-edge/how-to-deploy-modules-portal) 또는 Azure CLI를 통해 Computer Vision 유형의 Azure 리소스를 만들어야 합니다. 컨테이너에 대한 액세스 요청을 승인하고 Azure 구독 ID를 등록한 후에 리소스를 만들 수 있습니다. https://aka.ms/csgate 로 이동하여 사용 사례와 Azure 구독 ID를 제출합니다.  액세스 양식 요청에서 제공된 것과 동일한 Azure 구독을 사용하여 Azure 리소스를 만들어야 합니다.

### <a name="gathering-required-parameters"></a>필수 매개 변수 수집

공간 분석 컨테이너를 포함하여 필요한 모든 Cognitive Services의 컨테이너에 대한 세 가지 기본 매개 변수가 있습니다. EULA(최종 사용자 사용권 계약)는 수락 값과 함께 제공되어야 합니다. 또한 엔드포인트 URL과 API 키가 모두 필요합니다.

### <a name="endpoint-uri-endpoint_uri"></a>엔드포인트 URI {ENDPOINT_URI}

엔드포인트 URI 값은 Cognitive Services 리소스의 Azure Portal 개요 페이지에서 확인할 수 있습니다. 개요 페이지로 이동하여 엔드포인트 URI를 찾습니다. 

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/spatial-analysis-tutorial/keys-endpoint.png" alt-text="키 및 엔드포인트":::

### <a name="keys-api_key"></a>키 {API_KEY}

이 키는 공간 분석 컨테이너를 시작하는 데 사용되며 해당하는 Cognitive Service 리소스의 Azure Portal 키 페이지에서 확인할 수 있습니다. 키 페이지로 이동하여 키를 찾습니다.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/spatial-analysis-tutorial/endpoint-uri.png" alt-text="엔드포인트 URI":::

## <a name="set-up-azure-stack-edge"></a>Azure Stack Edge 설정

[다음 단계](https://docs.microsoft.com/azure/databox-online/azure-stack-edge-gpu-deploy-prep)에 따라 Azure Stack Edge를 설정하고 아래 단계를 계속 수행하여 Live Video Analytics와 공간 분석 모듈을 배포합니다.

## <a name="set-up-your-development-environment"></a>개발 환경 설정

1. https://github.com/Azure-Samples/live-video-analytics-iot-edge-csharp 에서 리포지토리를 복제합니다.
1. Visual Studio Code에서 리포지토리를 다운로드한 폴더를 엽니다.
1. Visual Studio Code에서 src/cloud-to-device-console-app으로 이동합니다. 여기에서 파일을 만들고 이름을 appsettings.json으로 지정합니다. 이 파일에는 프로그램을 실행하는 데 필요한 설정이 포함됩니다.
1. 다음 단계에 따라 Azure Stack Edge에서 IotHubConnectionString을 가져옵니다.

    * Azure Portal에서 IoT Hub로 이동하여 왼쪽 탐색 창에서 `Shared access policies`를 클릭합니다.
    * `iothubowner`를 클릭하여 공유 액세스 키를 가져옵니다.
    * `Connection String – primary key`를 복사하여 VSCode의 입력 상자에 붙여넣습니다.
    
        연결 문자열은 다음과 같습니다. <br/>`HostName=xxx.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey=xxx`
1. 아래 내용을 파일에 복사합니다. 변수를 바꾸어야 합니다.
    
    ```json
    {
        "IoThubConnectionString" : " HostName=<IoTHubName>.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey=<SharedAccessKey>”,
        "deviceId" : "<your Azure Stack Edge name>",
        "moduleId" : "lvaEdge"
    } 
    ```
1. src/edge 폴더로 이동하고, .env라는 파일을 만듭니다.
1. /clouddrive/lva-sample/edge-deployment/.env 파일의 콘텐츠를 복사합니다. 텍스트는 다음 코드와 비슷합니다.

    ```env
    SUBSCRIPTION_ID="<Subscription ID>"  
    RESOURCE_GROUP="<Resource Group>"  
    AMS_ACCOUNT="<AMS Account ID>"  
    IOTHUB_CONNECTION_STRING="HostName=xxx.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey=xxx"  
    AAD_TENANT_ID="<AAD Tenant ID>"  
    AAD_SERVICE_PRINCIPAL_ID="<AAD SERVICE_PRINCIPAL ID>"  
    AAD_SERVICE_PRINCIPAL_SECRET="<AAD SERVICE_PRINCIPAL ID>"  
    INPUT_VIDEO_FOLDER_ON_DEVICE="/home/lvaadmin/samples/input"  
    OUTPUT_VIDEO_FOLDER_ON_DEVICE="/var/media"
    APPDATA_FOLDER_ON_DEVICE="/var/local/mediaservices"
    CONTAINER_REGISTRY_USERNAME_myacr="<your container registry username>"  
    CONTAINER_REGISTRY_PASSWORD_myacr="<your container registry password>"   
    ```
    
## <a name="set-up-deployment-template"></a>배포 템플릿 설정  

/src/edge/deployment.template.json에 공간 분석 모듈을 추가합니다. 템플릿에는 lvaEdge 모듈, rtspsim 모듈 및 공간 분석 모듈이 있습니다.

<p>
<details>
<summary>이를 확장하고 샘플 배포 템플릿을 봅니다.  
여기에서 콘텐츠를 복사하여 /src/edge/deployment.template.json에 붙여넣습니다.
</summary>
<pre><code>
{
  "$schema-template": "2.0.0",
  "modulesContent": {
    "$edgeAgent": {
      "properties.desired": {
        "schemaVersion": "1.0",
        "runtime": {
          "type": "docker",
          "settings": {
            "minDockerVersion": "v1.25",
            "loggingOptions": "",
            "registryCredentials": {
            }
          }
        },
        "systemModules": {
          "edgeAgent": {
            "type": "docker",
            "settings": {
              "image": "mcr.microsoft.com/azureiotedge-agent:1.0",
              "createOptions": {}
            }
          },
          "edgeHub": {
            "type": "docker",
            "status": "running",
            "restartPolicy": "always",
            "settings": {
              "image": "mcr.microsoft.com/azureiotedge-hub:1.0",
              "createOptions": {
                "HostConfig": {
                  "PortBindings": {
                    "5671/tcp": [
                      {
                        "HostPort": "5671"
                      }
                    ],
                    "8883/tcp": [
                      {
                        "HostPort": "8883"
                      }
                    ],
                    "443/tcp": [
                      {
                        "HostPort": "443"
                      }
                    ]
                  }
                }
              }
            }
          }
        },
        "modules": {
          "lvaEdge": {
            "version": "1.0",
            "type": "docker",
            "status": "running",
            "restartPolicy": "always",
            "settings": {
              "image": "mcr.microsoft.com/media/live-video-analytics:1",
              "createOptions": {
                "HostConfig": {
                  "LogConfig": {
                    "Type": "",
                    "Config": {
                      "max-size": "10m",
                      "max-file": "10"
                    }
                  },
                  "Binds": [
                    "$OUTPUT_VIDEO_FOLDER_ON_DEVICE:/var/media/",
                    "$APPDATA_FOLDER_ON_DEVICE:/var/lib/azuremediaservices"
                  ],
                  "IpcMode": "host",
                  "ShmSize": 1536870912
                }
              }
            },
            "env": {
              "IS_DEVELOPER_ENVIRONMENT": {
                "value": "true"
              }
            }
          },
          "rtspsim": {
              "version": "1.0",
              "type": "docker",
              "status": "running",
              "restartPolicy": "always",
              "settings": {
                "image": "mcr.microsoft.com/lva-utilities/rtspsim-live555:1.2",
                "createOptions": {
                  "HostConfig": {
                    "Mounts": [
                      {
                        "Target": "/live/mediaServer/media",
                        "Source": "lvaspatialanalysislocal",
                        "Type": "volume"
                      }
                    ],
                    "PortBindings": {
                      "554/tcp": [
                        {
                          "HostPort": "554"
                        }
                      ]
                    }
                  }
                }
              }
            },
          "spatialAnalysis": {
            "version": "1.0",
            "type": "docker",
            "status": "running",
            "restartPolicy": "always",
            "settings": {
              "image": "mcr.microsoft.com/azure-cognitive-services/spatial-analysis:1.0",
              "createOptions": {
                "HostConfig": {
                  "PortBindings": {
                    "50051/tcp": [
                      {
                        "HostPort": "50051"
                      }
                    ]
                  },
                  "IpcMode": "host",
                  "Binds": [
                      "/tmp/.X11-unix:/tmp/.X11-unix"
                  ],
                  "Runtime": "nvidia",
                  "ShmSize": 536870911,
                  "LogConfig": {
                      "Type": "json-file",
                      "Config": {
                          "max-size": "10m",
                          "max-file": "200"
                      }
                  }
                }
              }
            },
            "env": {
              "DISPLAY": {
                "value": ":0"
              },
              "ARCHON_SHARED_BUFFER_LIMIT": {
                "value": "377487360"
              },
              "ARCHON_PERF_MARKER": {
                "value": "false"
              },
              "QT_X11_NO_MITSHM": {
                "value": "1"
              },
              "OMP_WAIT_POLICY": {
                "value": "PASSIVE"
              },
              "EULA": {
                "value": "accept"
              },
              "BILLING_ENDPOINT": {
                "value": "<Use one key from Archon azure resource (keys page)>"
              },
              "API_KEY": {
                "value": "<Use endpoint from Archon azure resource (overview page)>"
              }
            }
          }
        }
      }
    },
    "$edgeHub": {
      "properties.desired": {
        "schemaVersion": "1.0",
        "routes": {
          "LVAToHub": "FROM /messages/modules/lvaEdge/outputs/* INTO $upstream"
        },
        "storeAndForwardConfiguration": {
          "timeToLiveSecs": 7200
        }
      }
    },
    "lvaEdge": {
      "properties.desired": {
        "applicationDataDirectory": "/var/lib/azuremediaservices",
        "azureMediaServicesArmId": "/subscriptions/$SUBSCRIPTION_ID/resourceGroups/$RESOURCE_GROUP/providers/microsoft.media/mediaservices/$AMS_ACCOUNT",
        "aadTenantId": "$AAD_TENANT_ID",
        "aadServicePrincipalAppId": "$AAD_SERVICE_PRINCIPAL_ID",
        "aadServicePrincipalSecret": "$AAD_SERVICE_PRINCIPAL_SECRET",
        "aadEndpoint": "https://login.microsoftonline.com",
        "aadResourceId": "https://management.core.windows.net/",
        "armEndpoint": "https://management.azure.com/",
        "diagnosticsEventsOutputName": "AmsDiagnostics",
        "operationalEventsOutputName": "AmsOperational",        
        "logLevel": "Info",
        "logCategories": "Application,Events,MediaPipeline",
        "allowUnsecuredEndpoints": true,
        "telemetryOptOut": false
      }
    },
    "spatialAnalysis": {
      "properties.desired": {
        "globalSettings": {
          "PlatformTelemetryEnabled": true,
          "CustomerTelemetryEnabled": true
        },
        "graphs": {
            "polygonCross": {
              "version": 2,
              "enabled": true,
              "platformloglevel": "info",
              "operationId": "cognitiveservices.vision.spatialanalysis-personcrossingpolygon.livevideoanalytics",
              "parameters": {
                  "BINDING_ADDRESS": "0.0.0.0:50051",
                  "DETECTOR_NODE_CONFIG": "{ \"show_debug_video\": false, \"gpu_index\": 0 }",
                  "SPACEANALYTICS_CONFIG": "{\"zones\":[{\"name\":\"polygon0\",\"polygon\":[[0,0],[0.6,0],[0.6,0.9],[0,0.9],[0,0]],\"threshold\":50,\"events\":[{\"type\":\"enter/exit\",\"config\":{\"trigger\":\"event\"}}]}]}"
              },
              "nodesloglevel": "info"
            },
            "personCount": {
              "version": 2,
              "enabled": false,
              "platformloglevel": "info",
              "operationId": "cognitiveservices.vision.spatialanalysis-personcount.livevideoanalytics",
              "parameters": {
                  "BINDING_ADDRESS": "0.0.0.0:50051",
                  "DETECTOR_NODE_CONFIG": "{ \"show_debug_video\": false, \"gpu_index\": 0 }",
                  "SPACEANALYTICS_CONFIG": "{\"zones\":[{\"name\":\"polygon0\",\"polygon\":[[0.8,0],[1,0],[1,1],[0.8,1],[0.8,0]],\"threshold\":50,\"events\":[{\"type\":\"count\",\"config\":{\"trigger\":\"event\"}}]}]}"
              },
              "nodesloglevel": "info"
            },
            "personDistance": {
              "version": 2,
              "enabled": false,
              "platformloglevel": "info",
              "operationId": "cognitiveservices.vision.spatialanalysis-persondistance.livevideoanalytics",
              "parameters": {
                  "BINDING_ADDRESS": "0.0.0.0:50051",
                  "DETECTOR_NODE_CONFIG": "{ \"show_debug_video\": false, \"gpu_index\": 0,\"gpu_index\": 0,\"do_calibration\": true}",
                  "SPACEANALYTICS_CONFIG": "{\"zones\":[{\"name\": \"distance_zone\", \"polygon\": [[0,0],[0,1],[1,1],[1,0],[0,0]],\"threshold\": 35.00,\"events\":[{\"type\": \"people_distance\",\"config\":{\"trigger\": \"event\",\"output_frequency\":1,\"minimum_distance_threshold\":6.0,\"maximum_distance_threshold\":35.0}}]}]}"
              },
              "nodesloglevel": "info"
            }
        }
      }
    }
  }
}
</code>
</pre>
</details>
</p>

주의해야 할 몇 가지 사항이 있습니다.

1. 포트 바인딩을 설정합니다.
    
    ```
    "PortBindings": {
        "50051/tcp": [
            {
                "HostPort": "50051"
            }
        ]
    },
    ```
1. lvaEdge 및 공간 분석 모듈 createOptions의 IpcMode는 동일하고 host로 설정되어야 합니다.
1. 배포 템플릿 파일은 파일 이름에 "deployment"를 포함해야 합니다. 그러지 않으면 인식할 수 없어 배포용 매니페스트를 생성할 수 없습니다.
1. RTSP 시뮬레이터가 작동하려면 볼륨 범위를 설정했는지 확인합니다. 자세한 내용은 [Docker 볼륨 탑재 설정](deploy-azure-stack-edge-how-to.md#optional-setup-docker-volume-mounts)을 참조하세요.

    1. [SMB 공유에 연결](https://docs.microsoft.com/azure/databox-online/azure-stack-edge-deploy-add-shares#connect-to-an-smb-share)하고 [비디오 파일](https://lvamedia.blob.core.windows.net/public/bulldozer.mkv)을 로컬 공유에 복사합니다.
    1. rtspsim 모듈에 다음이 포함되어 있는지 확인합니다.
        
        ```json
        "createOptions": {
                            "HostConfig": {
                              "Mounts": [
                                {
                                  "Target": "/live/mediaServer/media",
                                  "Source": "<your Local Docker Volume Mount name>",
                                  "Type": "volume"
                                }
                              ],
                              "PortBindings": {
                                "554/tcp": [
                                  {
                                    "HostPort": "554"
                                  }
                                ]
                              }
                            }
                          }
        ```
## <a name="generate-and-deploy-the-deployment-manifest"></a>배포 매니페스트 생성 및 배포

배포 매니페스트는 에지 디바이스에 배포되는 모듈을 정의합니다. 또한 해당 모듈에 대한 구성 설정을 정의합니다.

다음 단계에 따라 템플릿 파일에서 매니페스트를 생성한 다음, 에지 디바이스에 배포합니다.

1. Visual Studio Code를 엽니다.
1. AZURE IOT HUB 창 옆에 있는 추가 작업 아이콘을 선택하여 IoT Hub 연결 문자열을 설정합니다. src/cloud-to-device-console-app/appsettings.json 파일에서 문자열을 복사할 수 있습니다.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/spatial-analysis-tutorial/connection-string.png" alt-text="공간 분석: 연결 문자열":::
1. src/edge/deployment.template.json을 마우스 오른쪽 단추로 클릭하고 IoT Edge 배포 매니페스트 생성을 선택합니다.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/spatial-analysis-tutorial/deployment-amd64-json.png" alt-text="공간 분석: 배포 amd64 json":::
    
    이렇게 하면 src/edge/config 폴더에 deployment.amd64.json이라는 매니페스트 파일이 생성됩니다.
1. src/edge/config/deployment.amd64.json을 마우스 오른쪽 단추로 클릭하고 단일 디바이스용 배포 만들기를 선택하고 에지 디바이스의 이름을 선택합니다.
    
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/spatial-analysis-tutorial/deployment-template-json.png" alt-text="공간 분석: 배포 템플릿 json":::   
1. IoT Hub 디바이스를 선택하라는 메시지가 표시되면 드롭다운 메뉴에서 Azure Stack Edge 이름을 선택합니다.
1. 약 30초 후 창의 왼쪽 아래 모서리에서 Azure IoT Hub를 새로 고칩니다. 이제 에지 디바이스에 다음과 같은 배포된 모듈이 표시됩니다.
    
    * Live Video Analytics on IoT Edge(모듈 이름 lvaEdge)
    * RTSP(Real-Time Streaming Protocol) 시뮬레이터(모듈 이름 rtspsim)
    * 공간 분석(모듈 이름 spatialAnalysis).
    
성공적으로 배포하는 경우 다음과 같이 OUTPUT에 메시지가 표시됩니다.

```
[Edge] Start deployment to device [<Azure Stack Edge name>]
[Edge] Deployment succeeded.
```

그런 다음 디바이스/모듈에서 `lvaEdge`, `rtspsim`, `spatialAnalysis` 및 `rtspsim` 모듈을 찾을 수 있으며, 상태는 "실행 중"이어야 합니다.

## <a name="prepare-to-monitor-events"></a>이벤트 모니터링 준비

이러한 이벤트를 보려면 다음 단계를 수행합니다.

1. Visual Studio Code에서 탐색기 창을 열고, 왼쪽 아래 모서리에서 Azure IoT Hub를 찾습니다.
1. 디바이스 노드를 확장합니다.
1. Azure Stack Edge를 마우스 오른쪽 단추로 클릭하고 기본 제공 이벤트 엔드포인트 모니터링 시작을 선택합니다.
    
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/spatial-analysis-tutorial/start-monitoring.png" alt-text="공간 분석: 모니터링 시작":::
     
## <a name="run-the-program"></a>프로그램 실행

src/cloud-to-device-console-app/operations.json에서 직접 메서드를 호출하는 program.cs가 있습니다. operations.json을 설정하고 미디어 그래프 사용에 대한 토폴로지를 제공해야 합니다.
operations.json:

다음과 같이 토폴로지를 설정합니다(로컬 토폴로지는 topologyFile, 온라인 토폴로지는 topologyUrl).

```json
{
    "opName": "GraphTopologySet",
    "opParams": {
        "topologyFile": "../edge/spatialAnalysistopology.json"
    }
},
```

다음과 같이 그래프 인스턴스를 만들고 여기에서 토폴로지에 매개 변수를 설정합니다.

```json
{
    "opName": "GraphInstanceSet",
    "opParams": {
        "name": "Sample-Graph-1",
        "properties": {
            "topologyName": "InferencingWithCVExtension",
            "description": "Sample graph description",
            "parameters": [
                {
                    "name": "rtspUrl",
                    "value": " rtsp://rtspsim:554/media/bulldozer.mkv"
                },
                {
                    "name": "rtspUserName",
                    "value": "testuser"
                },
                {
                    "name": "rtspPassword",
                    "value": "testpassword"
                }
            ]
        }
    }
},
```

<p>
<details>
<summary>spatialAnalysis 모듈에 대한 샘플 토폴로지 파일을 보려면 확장합니다.
</summary>
<pre><code>
{
    "@apiVersion": "1.0",
    "name": "InferencingWithCVExtension",
    "properties": {
      "description": "Analyzing live video using spatialAnalysis Extension to send images to an external inference engine",
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
        },
        {
          "name": "grpcUrl",
          "type": "String",
          "description": "inferencing Url",
          "default": "tcp://spatialAnalysis:50051"
        },
        {
          "name": "frameRate",
          "type": "String",
          "description": "Rate of the frames per second to be received from LVA.",
          "default": "2"
        },
        {
          "name": "spatialanalysisusername",
          "type": "String",
          "description": "spatialanalysis endpoint username",
          "default": "not-in-use"
        },
        {
          "name": "spatialanalysispassword",
          "type": "String",
          "description": "spatialanalysis endpoint password",
          "default": "not-in-use"  
        }
      ],
      "sources": [
        {
          "@type": "#Microsoft.Media.MediaGraphRtspSource",
          "name": "rtspSource",
          "transport": "tcp",
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
          "@type": "#Microsoft.Media.MediaGraphFrameRateFilterProcessor",
          "name": "frameRateFilter",
          "inputs": [
            {
              "nodeName": "rtspSource"
            }
          ],
          "maximumFps": "${frameRate}"
        },
        {
          "@type": "#Microsoft.Media.MediaGraphCognitiveServicesVisionExtension",
          "name": "computerVisionExtension",
          "endpoint": {
            "@type": "#Microsoft.Media.MediaGraphUnsecuredEndpoint",
            "url": "${grpcUrl}",
            "credentials": {
              "@type": "#Microsoft.Media.MediaGraphUsernamePasswordCredentials",
              "username": "${spatialanalysisusername}",
              "password": "${spatialanalysispassword}"
            }
          },
          "image": {
            "scale": {
              "mode": "pad",
              "width": "1408",
              "height": "786"
            },
            "format": {
              "@type": "#Microsoft.Media.MediaGraphImageFormatRaw",
              "pixelFormat": "bgr24"
            }
          },
          "inputs": [
            {
              "nodeName": "frameRateFilter"
            }
          ]
        },
        {
            "@type": "#Microsoft.Media.MediaGraphSignalGateProcessor",
            "name": "signalGateProcessor",
            "inputs": [
              {
                "nodeName": "computerVisionExtension"
              },
              {
                "nodeName": "rtspSource"
              }
            ],
            "activationEvaluationWindow": "PT1S",
            "activationSignalOffset": "PT0S",
            "minimumActivationTime": "PT30S",
            "maximumActivationTime": "PT30S"
          }
      ],
      "sinks": [
        {
            "@type": "#Microsoft.Media.MediaGraphAssetSink",
            "name": "assetSink",
            "assetNamePattern": "sampleAssetFromEVR-CV-LVAEdge-${System.DateTime}",
            "segmentLength": "PT30S",
            "LocalMediaCacheMaximumSizeMiB": "200",
            "localMediaCachePath": "/var/lib/azuremediaservices/tmp/",
            "inputs": [
                {
                    "nodeName": "signalGateProcessor"
                }
            ]
        },
        {
          "@type": "#Microsoft.Media.MediaGraphIoTHubMessageSink",
          "name": "hubSink",
          "hubOutputName": "inferenceOutput",
          "inputs": [
            {
              "nodeName": "computerVisionExtension"
            }
          ]
        }
      ]
    }
  }
</code>
</pre>
</details>
</p>


MediaGraphRealTimeComputerVisionExtension을 사용하여 공간 분석 모듈에 연결합니다. tcp://spatialAnalysis:<포트 번호>(예: tcp://spatialAnalysis:50051)에서 ${grpcUrl} 설정

```json
{
    "@type": "#Microsoft.Media.MediaGraphCognitiveServicesVisionExtension",
    "name": "computerVisionExtension",
    "endpoint": {
    "@type": "#Microsoft.Media.MediaGraphUnsecuredEndpoint",
    "url": "${grpcUrl}",
    "credentials": {
        "@type": "#Microsoft.Media.MediaGraphUsernamePasswordCredentials",
        "username": "${spatialanalysisusername}",
        "password": "${spatialanalysispassword}"
    }
    },
    "image": {
    "scale": {
        "mode": "pad",
        "width": "1408",
        "height": "786"
    },
    "format": {
        "@type": "#Microsoft.Media.MediaGraphImageFormatRaw",
        "pixelFormat": "bgr24"
    }
    },
    "inputs": [
    {
        "nodeName": "frameRateFilter"
    }
    ]
}
```

디버그 세션을 실행하고 TERMINAL 지침에 따라 토폴로지를 설정하고, 그래프 인스턴스를 설정하고, 그래프 인스턴스를 활성화하고, 마지막으로 리소스를 삭제합니다.

## <a name="interpret-results"></a>결과 해석

미디어 그래프가 인스턴스화되면 "MediaSessionEstablished" 이벤트(여기서는 [sample MediaSessionEstablished event](detect-motion-emit-events-quickstart.md#mediasessionestablished-event))가 표시됩니다.

또한 공간 분석 모듈은 AI 인사이트 이벤트를 Live Video Analytics로 전송한 다음 IoTHub로 전송하여 OUTPUT에 표시합니다. ENTITY는 검색 개체이고 EVENT는 spaceanalytics 이벤트입니다. 이 출력은 Live Video Analytics로 전달됩니다.

personZoneEvent에 대한 샘플 출력(cognitiveservices.vision.spatialanalysis-personcrossingpolygon.livevideoanalytics 작업):

```
{
  "body": {
    "timestamp": 143810610210090,
    "inferences": [
      {
        "type": "entity",
        "subtype": "",
        "inferenceId": "a895c86824db41a898f776da1876d230",
        "relatedInferences": [],
        "entity": {
          "tag": {
            "value": "person",
            "confidence": 0.66026187
          },
          "attributes": [],
          "box": {
            "l": 0.26559368,
            "t": 0.17887735,
            "w": 0.49247605,
            "h": 0.76629657
          }
        },
        "extensions": {},
        "valueCase": "entity"
      },
      {
        "type": "event",
        "subtype": "",
        "inferenceId": "995fe4206847467f8dfde83a15187d76",
        "relatedInferences": [
          "a895c86824db41a898f776da1876d230"
        ],
        "event": {
          "name": "personZoneEvent",
          "properties": {
            "status": "Enter",
            "metadataVersion": "1.0",
            "zone": "polygon0",
            "trackingId": "a895c86824db41a898f776da1876d230"
          }
        },
        "extensions": {},
        "valueCase": "event"
      }
    ]
  },
  "applicationProperties": {
    "topic": "/subscriptions/81631a63-f0bd-4f35-8344-5bc541b36bfc/resourceGroups/lva-sample-resources/providers/microsoft.media/mediaservices/lvasamplea4bylcwoqqypi",
    "subject": "/graphInstances/Sample-Graph-1/processors/spatialanalysisExtension",
    "eventType": "Microsoft.Media.Graph.Analytics.Inference",
    "eventTime": "2020-08-20T03:54:29.001Z",
    "dataVersion": "1.0"
  }
}
```

## <a name="next-steps"></a>다음 단계

배포 매니페스트 파일의 그래프 노드에 있는 "enabled" 플래그를 설정/해제하여 `spatialAnalysis` 모듈에서 제공하는 다양한 작업(예: **personCount** 및 **personDistance**)을 시도합니다.
>[!Tip]
> 프레임에 두 명 이상의 사용자가 있는 [비디오 파일](https://lvamedia.blob.core.windows.net/public/2018-03-07.16-50-00.16-55-00.school.G421.mkv)을 사용합니다.

> [!NOTE]
> 작업은 한 번에 하나만 실행할 수 있습니다. 따라서 하나의 플래그만 **true**로 설정하고 다른 플래그는 **false**로 설정하세요.
