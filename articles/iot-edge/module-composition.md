---
title: "Azure IoT Edge 모듈 구성 | Microsoft Docs"
description: "Azure IoT Edge 모듈에 포함할 항목 및 다시 사용하는 방법을 알아봅니다."
services: iot-edge
keywords: 
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 10/05/2017
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 5de67b6f1ce79934a3a6aab623d2e77a56a8ce76
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/21/2018
---
# <a name="understand-how-iot-edge-modules-can-be-used-configured-and-reused---preview"></a>IoT Edge 모듈을 사용, 구성 및 다시 사용하는 방법에 대한 이해 - 미리 보기

Azure IoT Edge를 사용하면 여러 개의 IoT Edge 모듈을 IoT Edge 장치에 배포하기 전에 해당 모듈을 구성할 수 있습니다. 이 문서에서는 배포하기 전에 여러 IoT Edge 모듈을 구성하는 데 있어 가장 중요한 개념에 대해 설명합니다. 

## <a name="the-deployment-manifest"></a>배포 매니페스트
*배포 매니페스트*는 다음 항목을 설명하는 JSON 문서입니다.

* 만들기 및 관리 옵션과 함께 배포해야 하는 IoT Edge 모듈
* 모듈 간 및 모듈과 IoT Hub 간의 메시지 흐름 방식을 설명하는 Edge 허브의 구성
* 필요에 따라 개별 모듈 응용 프로그램을 구성하기 위해 모듈 쌍의 desired 속성에 설정되는 값

Azure IoT Edge 자습서에서는 Azure IoT Edge 포털의 마법사를 통해 배포 매니페스트를 빌드합니다. REST 또는 IoT Hub 서비스 SDK를 사용하여 프로그래밍 방식으로 배포 매니페스트를 적용할 수도 있습니다. IoT Edge 배포에 대한 자세한 내용은 [배포 및 모니터링][lnk-deploy]을 참조하세요.

상위 수준에서 배포 매니페스트는 IoT Edge 장치에 배포된 IoT Edge 모듈에 대해 모듈 쌍의 원하는 속성을 구성합니다. 이러한 모듈 중 두 모듈, 즉 Edge 에이전트 및 Edge 허브가 항상 있습니다.

매니페스트는 다음과 같은 구조를 따릅니다.

    {
        "moduleContent": {
            "$edgeAgent": {
                "properties.desired": {
                    // desired properties of the Edge agent
                }
            },
            "$edgeHub": {
                "properties.desired": {
                    // desired properties of the Edge hub
                }
            },
            "{module1}": {  // optional
                "properties.desired": {
                    // desired properties of module with id {module1}
                }
            },
            "{module2}": {  // optional
                ...
            },
            ...
        }
    }

이 섹션의 끝에는 배포 매니페스트의 예제가 보고됩니다.

> [!IMPORTANT]
> 모든 IoT Edge 장치는 배포 매니페스트로 구성해야 합니다. 새로 설치된 IoT Edge 런타임은 유효한 매니페스트로 구성될 때까지 오류 코드를 보고합니다. 

### <a name="specify-the-modules"></a>모듈 지정
Edge 에이전트의 모듈 쌍 desired 속성에는 Edge 에이전트에 대한 구성 매개 변수와 함께 원하는 모듈, 해당 구성 및 관리 옵션이 포함됩니다.

상위 수준에서 매니페스트의 이 섹션에는 IoT Edge 런타임 모듈(Edge 에이전트 및 Edge 허브) 및 사용자 지정 모듈에 대한 모듈 이미지 및 관리 옵션에 대한 참조가 포함됩니다.

매니페스트의 이 섹션에 대한 자세한 설명은 [Edge 에이전트의 desired 속성][lnk-edgeagent-desired]을 참조하세요.

> [!NOTE]
> IoT Edge 런타임(에이전트 및 허브)만 포함하는 배포 매니페스트가 유효합니다.


### <a name="specify-the-routes"></a>경로 지정
Edge 허브는 모듈 간 및 모듈과 IoT Hub 간에 메시지를 선언적으로 라우팅하는 방법을 제공합니다.

경로의 구문은 다음과 같습니다.

        FROM <source>
        [WHERE <condition>]
        INTO <sink>

source는 다음 중 하나일 수 있습니다.

| 원본 | 설명 |
| ------ | ----------- |
| `/*` | 모든 장치 또는 모듈의 모든 장치-클라우드 메시지 |
| `/messages/*` | 일부 출력을 통하거나 어떠한 출력도 없이 장치 또는 모듈에서 보낸 모든 장치-클라우드 메시지 |
| `/messages/modules/*` | 일부 출력을 통하거나 어떠한 출력도 없이 모듈에서 보낸 모든 장치-클라우드 메시지 |
| `/messages/modules/{moduleId}/*` | 어떠한 출력도 없이 {moduleId}에서 보낸 모든 장치-클라우드 메시지 |
| `/messages/modules/{moduleId}/outputs/*` | 일부 출력에서 {moduleId}에서 보낸 모든 장치-클라우드 메시지 |
| `/messages/modules/{moduleId}/outputs/{output}` | {output}을 사용하여 {moduleId}에서 보낸 모든 장치-클라우드 메시지 |

조건은 IoT Hub 라우팅 규칙에 대해 [IoT Hub 쿼리 언어][lnk-iothub-query]에서 지원하는 조건일 수 있습니다.

sink는 다음 중 하나일 수 있습니다.

| sink | 설명 |
| ---- | ----------- |
| `$upstream` | 메시지를 IoT Hub로 보냅니다. |
| `BrokeredEndpoint("/modules/{moduleId}/inputs/{input}")` | 메시지를 `{moduleId}` 모듈의 `{input}` 입력으로 보냅니다. |

Edge 허브는 한 번 이상의 보증을 제공한다는 점에 유의해야 합니다. 이는 경로에서 싱크에 메시지를 전달할 수 없는 경우, 예를 들어 Edge 허브에서 IoT Hub에 연결할 수 없거나 대상 모듈이 연결되어 있지 않으면, 메시지가 로컬에 저장된다는 것을 의미합니다.

Edge 허브는 Edge 허브 desired 속성의 `storeAndForwardConfiguration.timeToLiveSecs` 속성에 지정된 시간까지 메시지를 저장합니다.

### <a name="specifying-the-desired-properties-of-the-module-twin"></a>모듈 쌍의 desired 속성 지정

배포 매니페스트는 Edge 에이전트 섹션에 지정된 사용자 모듈 각각에 대한 모듈 쌍의 desired 속성을 지정할 수 있습니다.

desired 속성이 배포 매니페스트에 지정되면 현재 모듈 쌍에 있는 모든 desired 속성을 덮어씁니다.

배포 매니페스트에서 모듈 쌍의 desired 속성을 지정하지 않으면, IoT Hub는 어떤 방식으로든 모듈 쌍을 수정하지 않고 desired 속성을 프로그래밍 방식으로 설정할 수 있습니다.

장치 쌍을 수정할 수 있게 해주는 동일한 메커니즘이 모듈 쌍을 수정하는 데 사용됩니다. 자세한 내용은 [장치 쌍 개발자 가이드](https://docs.microsoft.com/en-us/azure/iot-hub/iot-hub-devguide-device-twins)를 참조하세요.   

### <a name="deployment-manifest-example"></a>배포 매니페스트 예제

다음은 배포 매니페스트 JSON 문서의 예제입니다.

    {
    "moduleContent": {
        "$edgeAgent": {
            "properties.desired": {
                "schemaVersion": "1.0",
                "runtime": {
                    "type": "docker",
                    "settings": {
                        "minDockerVersion": "v1.25",
                        "loggingOptions": ""
                    }
                },
                "systemModules": {
                    "edgeAgent": {
                        "type": "docker",
                        "settings": {
                        "image": "microsoft/azureiotedge-agent:1.0-preview",
                        "createOptions": ""
                        }
                    },
                    "edgeHub": {
                        "type": "docker",
                        "status": "running",
                        "restartPolicy": "always",
                        "settings": {
                        "image": "microsoft/azureiotedge-hub:1.0-preview",
                        "createOptions": ""
                        }
                    }
                },
                "modules": {
                    "tempSensor": {
                        "version": "1.0",
                        "type": "docker",
                        "status": "running",
                        "restartPolicy": "always",
                        "settings": {
                        "image": "microsoft/azureiotedge-simulated-temperature-sensor:1.0-preview",
                        "createOptions": "{}"
                        }
                    },
                    "filtermodule": {
                        "version": "1.0",
                        "type": "docker",
                        "status": "running",
                        "restartPolicy": "always",
                        "settings": {
                        "image": "myacr.azurecr.io/filtermodule:latest",
                        "createOptions": "{}"
                        }
                    }
                }
            }
        },
        "$edgeHub": {
            "properties.desired": {
                "schemaVersion": "1.0",
                "routes": {
                    "sensorToFilter": "FROM /messages/modules/tempSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/filtermodule/inputs/input1\")",
                    "filterToIoTHub": "FROM /messages/modules/filtermodule/outputs/output1 INTO $upstream"
                },
                "storeAndForwardConfiguration": {
                    "timeToLiveSecs": 10
                }
            }
        }
    }
    }

## <a name="reference-edge-agent-module-twin"></a>참조: Edge 에이전트 모듈 쌍

Edge 에이전트에 대한 모듈 쌍은 `$edgeAgent`라고 하며, 장치에서 실행 중인 Edge 에이전트와 IoT Hub 간의 통신을 조정합니다.
단일 장치 또는 대규모 배포의 일부로 특정 장치에 배포 매니페스트를 적용할 때 desired 속성이 설정됩니다. IoT Edge 장치에 모듈을 배포하는 방법에 대한 자세한 내용은 [배포 및 모니터링][lnk-deploy]을 참조하세요.

### <a name="edge-agent-twin-desired-properties"></a>Edge 에이전트 쌍 desired 속성

| 자산 | 설명 | 필수 |
| -------- | ----------- | -------- |
| schemaVersion | "1.0"이어야 합니다. | 예 |
| runtime.type | "docker"여야 합니다. | 예 |
| runtime.settings.minDockerVersion | 이 배포 매니페스트에 필요한 최소 Docker 버전으로 설정합니다. | 예 |
| runtime.settings.loggingOptions | Edge 에이전트 컨테이너에 대한 로깅 옵션을 포함하는 문자열 형식 JSON입니다. [Docker 로깅 옵션][lnk-docker-logging-options] | 아니요 |
| systemModules.edgeAgent.type | "docker"여야 합니다. | 예 |
| systemModules.edgeAgent.settings.image | Edge 에이전트의 이미지에 대한 URI입니다. 현재 Edge 에이전트는 자체적으로 업데이트할 수 없습니다. | 예 |
| systemModules.edgeAgent.settings.createOptions | Edge 에이전트 컨테이너에 대한 만들기 옵션을 포함하는 문자열 형식 JSON입니다. [Docker 만들기 옵션][lnk-docker-create-options] | 아니요 |
| systemModules.edgeAgent.configuration.id | 이 모듈을 배포한 배포의 ID입니다. | 이 매니페스트가 배포를 사용하여 적용될 때 IoT Hub에서 이 속성을 설정합니다. 배포 매니페스트의 일부가 아닙니다. |
| systemModules.edgeHub.type | "docker"여야 합니다. | 예 |
| systemModules.edgeHub.status | "running"이어야 합니다. | 예 |
| systemModules.edgeHub.restartPolicy | "always"여야 합니다. | 예 |
| systemModules.edgeHub.settings.image | Edge 허브의 이미지에 대한 URI입니다. | 예 |
| systemModules.edgeHub.settings.createOptions | Edge 허브 컨테이너에 대한 만들기 옵션을 포함하는 문자열 형식 JSON입니다. [Docker 만들기 옵션][lnk-docker-create-options] | 아니요 |
| systemModules.edgeHub.configuration.id | 이 모듈을 배포한 배포의 ID입니다. | 이 매니페스트가 배포를 사용하여 적용될 때 IoT Hub에서 이 속성을 설정합니다. 배포 매니페스트의 일부가 아닙니다. |
| modules.{moduleId}.version | 이 모듈의 버전을 나타내는 사용자 정의 문자열입니다. | 예 |
| modules.{moduleId}.type | "docker"여야 합니다. | 예 |
| modules.{moduleId}.restartPolicy | {“never” \| “on-failed” \| “on-unhealthy” \| “always”} | 예 |
| modules.{moduleId}.settings.image | 모듈 이미지에 대한 URI입니다. | 예 |
| modules.{moduleId}.settings.createOptions | 모듈 컨테이너에 대한 만들기 옵션을 포함하는 문자열 형식 JSON입니다. [Docker 만들기 옵션][lnk-docker-create-options] | 아니요 |
| modules.{moduleId}.configuration.id | 이 모듈을 배포한 배포의 ID입니다. | 이 매니페스트가 배포를 사용하여 적용될 때 IoT Hub에서 이 속성을 설정합니다. 배포 매니페스트의 일부가 아닙니다. |

### <a name="edge-agent-twin-reported-properties"></a>Edge 에이전트 쌍 reported 속성

Edge 에이전트 reported 속성에는 다음 세 가지 주요 정보가 포함됩니다.

1. 마지막으로 desired 속성이 표시된 응용 프로그램의 상태
2. Edge 에이전트에서 보고한 현재 장치에서 실행 중인 모듈의 상태
3. 현재 장치에서 실행 중인 desired 속성의 복사본

이 마지막 정보는 런타임에서 마지막 desired 속성이 성공적으로 적용되지 않고 장치에서 이전 배포 매니페스트를 계속 실행하는 경우에 유용합니다.

> [!NOTE]
> Edge 에이전트의 reported 속성은 [IoT Hub 쿼리 언어][lnk-iothub-query]로 쿼리하여 대규모 배포의 상태를 조사할 수 있으므로 유용합니다. 이 기능을 사용하는 방법에 대한 자세한 내용은 [배포][lnk-deploy]를 참조하세요.

다음 표에는 desired 속성에서 복사한 정보가 포함되어 있지 않습니다.

| 자산 | 설명 |
| -------- | ----------- |
| lastDesiredVersion | 이 int는 Edge 에이전트에서 처리하는 desired 속성의 마지막 버전을 나타냅니다. |
| lastDesiredStatus.code | Edge 에이전트에서 마지막으로 표시한 desired 속성을 나타내는 상태 코드입니다. 허용되는 값: `200` 성공, `400` 잘못된 구성, `412` 잘못된 스키마 버전, `417` 비어 있는 desired 속성, `500` 실패 |
| lastDesiredStatus.description | 상태에 대한 텍스트 설명입니다. |
| deviceHealth | 모든 모듈의 런타임 상태가 `running` 또는 `stopped`이면 `healthy`, 그렇지 않으면 `unhealthy`입니다 |
| configurationHealth.{deploymentId}.health | {deploymentId} 배포에서 설정한 모든 모듈의 런타임 상태가 `running` 또는 `stopped`이면 `healthy`이고, 그렇지 않으면 `unhealthy`입니다. |
| runtime.platform.OS | 장치에서 실행 중인 OS를 보고합니다. |
| runtime.platform.architecture | 장치의 CPU 아키텍처를 보고합니다. |
| systemModules.edgeAgent.runtimeStatus | Edge 에이전트의 보고된 상태: {“running” \| “unhealthy”} |
| systemModules.edgeAgent.statusDescription | Edge 에이전트의 reported 상태에 대한 텍스트 설명입니다. |
| systemModules.edgeHub.runtimeStatus | Edge 허브의 현재 상태: { “running” \| “stopped” \| “failed” \| “backoff” \| “unhealthy” } |
| systemModules.edgeHub.statusDescription | 비정상인 경우 Edge 허브의 현재 상태에 대한 텍스트 설명입니다. |
| systemModules.edgeHub.exitCode | 종료된 경우 Edge 허브 컨테이너에서 보고한 종료 코드입니다. |
| systemModules.edgeHub.startTimeUtc | Edge 허브가 마지막으로 시작된 시간입니다. |
| systemModules.edgeHub.lastExitTimeUtc | Edge 허브가 마지막으로 종료된 시간입니다. |
| systemModules.edgeHub.lastRestartTimeUtc | Edge 허브가 마지막으로 다시 시작된 시간입니다. |
| systemModules.edgeHub.restartCount | 이 모듈이 다시 시작 정책의 일부로 다시 시작된 횟수입니다. |
| modules.{moduleId}.runtimeStatus | 모듈의 현재 상태: { “running” \| “stopped” \| “failed” \| “backoff” \| “unhealthy” } |
| modules.{moduleId}.statusDescription | 비정상인 경우 모듈의 현재 상태에 대한 텍스트 설명입니다. |
| modules.{moduleId}.exitCode | 종료된 경우 모듈 컨테이너에서 보고한 종료 코드입니다. |
| modules.{moduleId}.startTimeUtc | 모듈이 마지막으로 시작된 시간입니다. |
| modules.{moduleId}.lastExitTimeUtc | 모듈이 마지막으로 종료된 시간입니다. |
| modules.{moduleId}.lastRestartTimeUtc | 모듈이 마지막으로 다시 시작된 시간입니다. |
| modules.{moduleId}.restartCount | 이 모듈이 다시 시작 정책의 일부로 다시 시작된 횟수입니다. |

## <a name="reference-edge-hub-module-twin"></a>참조: Edge 허브 모듈 쌍

Edge 허브에 대한 모듈 쌍은 `$edgeHub`라고 하며, 장치에서 실행 중인 Edge 허브와 IoT Hub 간의 통신을 조정합니다.
단일 장치 또는 대규모 배포의 일부로 특정 장치에 배포 매니페스트를 적용할 때 desired 속성이 설정됩니다. IoT Edge 장치에 모듈을 배포하는 방법에 대한 자세한 내용은 [배포][lnk-deploy]를 참조하세요.

### <a name="edge-hub-twin-desired-properties"></a>Edge 허브 쌍 desired 속성

| 자산 | 설명 | 배포 매니페스트에 필요합니다. |
| -------- | ----------- | -------- |
| schemaVersion | "1.0"이어야 합니다. | 예 |
| routes.{routeName} | Edge 허브 경로를 나타내는 문자열입니다. | `routes` 요소는 존재하지만 비어 있을 수 있습니다. |
| storeAndForwardConfiguration.timeToLiveSecs | 연결이 끊긴 라우팅 끝점의 경우(예: IoT Hub 또는 로컬 모듈에서 연결이 끊김) Edge 허브에서 메시지를 유지하는 시간(초)입니다. | 예 |

### <a name="edge-hub-twin-reported-properties"></a>Edge 허브 쌍 reported 속성

| 자산 | 설명 |
| -------- | ----------- |
| lastDesiredVersion | 이 int는 Edge 허브에서 처리하는 desired 속성의 마지막 버전을 나타냅니다. |
| lastDesiredStatus.code | Edge 허브에서 마지막으로 표시한 desired 속성을 나타내는 상태 코드입니다. 허용되는 값: `200` 성공, `400` 잘못된 구성, `500` 실패 |
| lastDesiredStatus.description | 상태에 대한 텍스트 설명입니다. |
| clients.{device or module identity}.status | 이 장치 또는 모듈의 연결 상태입니다. 가능한 값 {“connected” \| “disconnected”}. 모듈 ID만 연결이 끊긴 상태가 될 수 있습니다. 연결된 경우에만 Edge 허브에 연결된 다운스트림 장치가 표시됩니다. |
| clients.{device or module identity}.lastConnectTime | 장치 또는 모듈이 마지막으로 연결된 시간입니다. |
| clients.{device or module identity}.lastDisconnectTime | 장치 또는 모듈의 연결이 마지막으로 끊긴 시간입니다. |

## <a name="next-steps"></a>다음 단계

이제 IoT Edge 모듈을 사용하는 방법을 알았으므로 [IoT Edge 모듈 개발을 위한 요구 사항 및 도구에 대해 알아봅니다][lnk-module-dev].

[lnk-deploy]: module-deployment-monitoring.md
[lnk-edgeagent-desired]: #edge-agent-twin-desired-properties
[lnk-edgeagent-reported]: #edge-agent-twin-reported-properties
[lnk=edgehub-desired]: #edge-hub-twin-desired-properties
[lnk-edgehub-reported]: #edge-hub-twin-reported-properties
[lnk-iothub-query]: ../iot-hub/iot-hub-devguide-query-language.md
[lnk-docker-create-options]: https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate
[lnk-docker-logging-options]: https://docs.docker.com/engine/admin/logging/overview/
[lnk-module-dev]: module-development.md
