---
title: 공간 분석 컨테이너에 대 한 원격 분석 및 로깅
titleSuffix: Azure Cognitive Services
description: 공간 분석에서는 각 컨테이너에 공통 구성 프레임 워크 정보, 로깅 및 보안 설정이 제공 됩니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 09/11/2020
ms.author: aahi
ms.openlocfilehash: 8154ef7a90011da8c15f52870eebb6c80ebaebca
ms.sourcegitcommit: d6a739ff99b2ba9f7705993cf23d4c668235719f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/24/2020
ms.locfileid: "92496115"
---
# <a name="telemetry-and-troubleshooting"></a>원격 분석 및 문제 해결

공간 분석에는 시스템 상태를 모니터링하고 문제를 진단하는 데 도움이 되는 기능 집합이 포함되어 있습니다.

## <a name="enable-visualizations"></a>시각화 사용

비디오 프레임에서 AI Insights 이벤트의 시각화를 사용 하도록 설정 하려면 `.debug` 데스크톱 컴퓨터에서 [공간 분석 작업](spatial-analysis-operations.md) 의 버전을 사용 해야 합니다. 시각화는 Azure Stack Edge 장치에서 가능 하지 않습니다. 사용할 수 있는 디버그 작업은 네 가지가 있습니다.

장치가 Azure Stack Edge 장치가 아닌 경우 환경 변수에 대해 올바른 값을 사용 하도록 [데스크톱 컴퓨터](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/ComputerVision/spatial-analysis/DeploymentManifest_for_non_ASE_devices.json) 의 배포 매니페스트 파일을 편집 합니다 `DISPLAY` . `$DISPLAY`호스트 컴퓨터의 변수와 일치 해야 합니다. 배포 매니페스트를 업데이트 한 후 컨테이너를 다시 배포 합니다.

배포가 완료 된 후 `.Xauthority` 호스트 컴퓨터의 파일을 컨테이너에 복사 하 고 다시 시작 해야 할 수 있습니다. 아래 샘플에서 `peopleanalytics` 는 호스트 컴퓨터의 컨테이너 이름입니다.

```bash
sudo docker cp $XAUTHORITY peopleanalytics:/root/.Xauthority
sudo docker stop peopleanalytics
sudo docker start peopleanalytics
xhost +
```


## <a name="collect-system-health-telemetry"></a>시스템 상태 원격 분석 수집

Telegraf는 공간 분석에서 작동 하 고 Microsoft Container Registry에서 사용할 수 있는 오픈 소스 이미지입니다. 다음 입력을 받아 Azure Monitor로 보냅니다. 원하는 사용자 지정 입력 및 출력을 사용 하 여 telegraf 모듈을 빌드할 수 있습니다. 공간 분석의 telegraf 모듈 구성은 배포 매니페스트의 일부입니다 (위에 연결 됨). 이 모듈은 선택 사항이 며 필요 하지 않은 경우 매니페스트에서 제거할 수 있습니다. 

입력: 
1. 공간 분석 메트릭
2. 디스크 메트릭
3. CPU 메트릭
4. Docker 메트릭
5. GPU 메트릭

출력:
1. Azure Monitor

제공 된 공간 분석 telegraf 모듈은 공간 분석 컨테이너에서 내보낸 모든 원격 분석 데이터를 Azure Monitor에 게시 합니다. 구독에 Azure Monitor를 추가 하는 방법에 대 한 자세한 내용은 [Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/overview) 를 참조 하세요.

Azure Monitor를 설정한 후에는 모듈이 원격 분석을 보낼 수 있도록 자격 증명을 만들어야 합니다. Azure Portal를 사용 하 여 새 서비스 주체를 만들거나 아래 Azure CLI 명령을 사용 하 여 서비스 주체를 만들 수 있습니다.

> [!NOTE] 
> 이 명령을 사용 하려면 구독에 대 한 소유자 권한이 있어야 합니다. 

```bash
# Find your Azure IoT Hub resource ID by running this command. The resource ID  should start with something like 
# "/subscriptions/b60d6458-1234-4be4-9885-c7e73af9ced8/resourceGroups/...”
az iot hub list

# Create a Service Principal with `Monitoring Metrics Publisher` role in the IoTHub resource:
# Save the output from this command. The values will be used in the deployment manifest. The password won't be shown again so make sure to write it down
az ad sp create-for-rbac --role="Monitoring Metrics Publisher" --name "<principal name>" --scopes="<resource ID of IoT Hub>"
```

[Azure Stack Edge 장치](https://go.microsoft.com/fwlink/?linkid=2142179) 또는 다른 [데스크톱 컴퓨터](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/ComputerVision/spatial-analysis/DeploymentManifest_for_non_ASE_devices.json)의 배포 매니페스트에서 *telegraf* 모듈을 찾아 다음 값을 이전 단계의 서비스 사용자 정보로 바꾸고 다시 배포 합니다.

```json

"telegraf": { 
  "settings": {
  "image":   "mcr.microsoft.com/azure-cognitive-services/vision/spatial-analysis/telegraf:1.0",
  "createOptions":   "{\"HostConfig\":{\"Runtime\":\"nvidia\",\"NetworkMode\":\"azure-iot-edge\",\"Memory\":33554432,\"Binds\":[\"/var/run/docker.sock:/var/run/docker.sock\"]}}"
},
"type": "docker",
"env": {
    "AZURE_TENANT_ID": {
        "value": "<Tenant Id>"
    },
    "AZURE_CLIENT_ID": {
        "value": "Application Id"
    },
    "AZURE_CLIENT_SECRET": {
        "value": "<Password>"
    },
    "region": {
        "value": "<Region>"
    },
    "resource_id": {
        "value": "/subscriptions/{subscriptionId}/resourceGroups/{resoureGroupName}/providers/Microsoft.Devices/IotHubs/{IotHub}"
    },
...
```

Telegraf 모듈이 배포 되 면 Azure Monitor 서비스를 통해 또는 Azure Portal IoT Hub에서 **모니터링** 을 선택 하 여 보고 된 메트릭을 액세스할 수 있습니다.

:::image type="content" source="./media/spatial-analysis/iot-hub-telemetry.png" alt-text="Azure Monitor 원격 분석 보고서":::

### <a name="system-health-events"></a>시스템 상태 이벤트

| 이벤트 이름 | 설명|
|------|---------|
|archon_exit    |사용자가 공간 분석 모듈 상태를 *실행 중* 에서 *중지 됨*으로 변경 하면 전송 됩니다.  |
|archon_error   |컨테이너 내 프로세스가 충돌 하는 경우 전송 됩니다. 이것은 심각한 오류입니다.  |
|InputRate  |그래프가 비디오 입력을 처리 하는 속도입니다. 5 분 마다 보고 됩니다. | 
|OutputRate     |그래프가 AI 통찰력을 출력 하는 속도입니다. 5 분 마다 보고 됩니다. |
|archon_allGraphsStarted | 모든 그래프가 시작을 완료 하면 전송 됩니다. |
|archon_configchange    | 그래프 구성이 변경 될 때 전송 됩니다. |
|archon_graphCreationFailed     |보고 된을 포함 하는 그래프가 `graphId` 시작 되지 않을 때 전송 됩니다. |
|archon_graphCreationSuccess    |보고 된이 있는 그래프가 성공적으로 시작 될 때 전송 됩니다 `graphId` . |
|archon_graphCleanup    | 보고 된이 있는 그래프가 정리 되 `graphId` 고 종료 될 때 전송 됩니다. |
|archon_graphHeartbeat  |하트 비트는 기술 그래프 마다 1 분 마다 전송 됩니다. |
|archon_apiKeyAuthFail |Computer Vision 리소스 키가 24 시간 넘게 컨테이너를 인증 하는 데 실패 하는 경우 (할당량 부족, 잘못 됨, 오프 라인)에 전송 됩니다. |
|VideoIngesterHeartbeat     |비디오가 비디오 원본에서 스트리밍되는 시간을 표시 하기 위해 1 시간 마다 전송 됩니다. 각 그래프에 대해 보고 됩니다. |
|VideoIngesterState | 비디오 스트리밍에 대해 *중지* 또는 *시작* 된 보고서입니다. 각 그래프에 대해 보고 됩니다. |

##  <a name="troubleshooting-an-iot-edge-device"></a>IoT Edge 장치 문제 해결

`iotedge`명령줄 도구를 사용 하 여 실행 중인 모듈의 상태와 로그를 확인할 수 있습니다. 예:
* `iotedge list`: 실행 중인 모듈의 목록을 보고 합니다. 
  에서 오류를 추가로 확인할 수 있습니다 `iotedge logs edgeAgent` . 가 중단 되 면를 `iotedge` 사용 하 여 다시 시작 해 볼 수 있습니다. `iotedge restart edgeAgent`
* `iotedge logs <module-name>`
* `iotedge restart <module-name>` 특정 모듈을 다시 시작 하려면 

## <a name="collect-log-files-with-the-diagnostics-container"></a>진단 컨테이너를 사용 하 여 로그 파일 수집

공간 분석에서는 런타임 문제를 진단 하거나 지원 티켓에 포함 하는 데 사용할 수 있는 Docker 디버깅 로그가 생성 됩니다. 공간 분석 진단 모듈은 Microsoft Container Registry에서 다운로드할 수 있습니다. [Azure Stack Edge 장치](https://go.microsoft.com/fwlink/?linkid=2142179) 또는 다른 [데스크톱 컴퓨터](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/ComputerVision/spatial-analysis/DeploymentManifest_for_non_ASE_devices.json)에 대 한 매니페스트 배포 파일에서 *진단* 모듈을 찾습니다.

"Env" 섹션에서 다음 구성을 추가 합니다.

```json
"diagnostics": {  
  "settings": {
  "image":   "mcr.microsoft.com/azure-cognitive-services/vision/spatial-analysis/diagnostics:1.0",
  "createOptions":   "{\"HostConfig\":{\"Mounts\":[{\"Target\":\"/usr/bin/docker\",\"Source\":\"/home/data/docker\",\"Type\":\"bind\"},{\"Target\":\"/var/run\",\"Source\":\"/run\",\"Type\":\"bind\"}],\"LogConfig\":{\"Config\":{\"max-size\":\"500m\"}}}}"
  }
```    

Azure Blob Storage와 같은 원격 끝점에 업로드 된 로그를 최적화 하려면 작은 파일 크기를 유지 관리 하는 것이 좋습니다. 권장 되는 Docker 로그 구성은 아래 예제를 참조 하세요.

```json
{
    "HostConfig": {
        "LogConfig": {
            "Config": {
                "max-size": "500m",
                "max-file": "1000"
            }
        }
    }
}
```

### <a name="configure-the-log-level"></a>로그 수준 구성

로그 수준 구성을 사용 하 여 생성 된 로그의 자세한 정도를 제어할 수 있습니다. 지원 되는 로그 수준은 `none` , `verbose` , `info` , `warning` 및 `error` 입니다. 노드와 플랫폼 모두에 대 한 기본 로그 정보 수준은 `info` 입니다. 

`ARCHON_LOG_LEVEL`환경 변수를 허용 되는 값 중 하나로 설정 하 여 로그 수준을 전역적으로 수정할 수 있습니다.
모든 배포 된 기술에 대해 전역적으로 또는 아래와 같이 및에 대 한 값을 설정 하 여 모든 특정 기술에 대 한 IoT Edge 모듈 쌍 문서를 통해 설정할 수도 있습니다 `platformLogLevel` `nodeLogLevel` .

```json
{
    "version": 1,
    "properties": {
        "desired": {
            "globalSettings": {
                "platformLogLevel": "verbose"
            },
            "graphs": {
                "samplegraph": {
                    "nodeLogLevel": "verbose",
                    "platformLogLevel": "verbose"
                }
            }
        }
    }
}
```

### <a name="collecting-logs"></a>로그 수집

> [!NOTE]
> `diagnostics`모듈은 로깅 콘텐츠에 영향을 주지 않으며 기존 로그를 수집, 필터링 및 업로드 하는 데에만 도움이 됩니다.
> 이 모듈을 사용 하려면 Docker API 버전 1.40 이상이 있어야 합니다.

[Azure Stack Edge 장치](https://go.microsoft.com/fwlink/?linkid=2142179) 또는 다른 [데스크톱 컴퓨터](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/ComputerVision/spatial-analysis/DeploymentManifest_for_non_ASE_devices.json) 의 샘플 배포 매니페스트 파일에는 `diagnostics` 로그를 수집 하 고 업로드 하는 라는 모듈이 포함 되어 있습니다. 이 모듈은 기본적으로 사용 하지 않도록 설정 되어 있으며, 로그에 액세스 해야 할 때 IoT Edge 모듈 구성을 통해 사용 하도록 설정 해야 합니다. 

`diagnostics`컬렉션은 주문형 이며 IoT Edge 직접 메서드를 통해 제어 되며 Azure Blob Storage에 로그를 보낼 수 있습니다.

### <a name="configure-diagnostics-upload-targets"></a>진단 업로드 대상 구성

IoT Edge 포털에서 장치를 선택 하 고 **진단** 모듈을 선택 합니다. [Azure Stack Edge 장치](https://go.microsoft.com/fwlink/?linkid=2142179) 또는 다른 [데스크톱 컴퓨터](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/ComputerVision/spatial-analysis/DeploymentManifest_for_non_ASE_devices.json)에 대 한 샘플 배포 매니페스트 파일에서 진단에 대 한 **환경 변수** 섹션을 찾아서 이라는 정보를 찾은 `env` 후 다음 정보를 추가 합니다.

**Azure Blob Storage 업로드 구성**

1. 사용자 고유의 Azure Blob Storage 계정을 만듭니다 (아직 없는 경우).
2. Azure Portal에서 저장소 계정에 대 한 **연결 문자열** 을 가져옵니다. **액세스 키**에 배치 됩니다.
3. 공간 분석 로그는 다음과 같은 파일 이름 형식으로 *rtcvlogs* 이라는 Blob Storage 컨테이너에 자동으로 업로드 `{CONTAINER_NAME}/{START_TIME}-{END_TIME}-{QUERY_TIME}.log` 됩니다.

```json
"env":{
    "IOTEDGE_WORKLOADURI":"fd://iotedge.socket",
    "AZURE_STORAGE_CONNECTION_STRING":"XXXXXX",   //from the Azure Blob Storage account
    "ARCHON_LOG_LEVEL":"info"
}
```

### <a name="uploading-spatial-analysis-logs"></a>공간 분석 로그 업로드

로그는 `getRTCVLogs` 모듈에서 IoT Edge 메서드를 사용 하 여 요청 시 업로드 됩니다 `diagnostics` . 


1. IoT Hub 포털 페이지로 이동 하 여 **Edge 장치**를 선택 하 고 장치 및 진단 모듈을 선택 합니다. 
2. 모듈의 세부 정보 페이지로 이동 하 여 **_직접 메서드_*_ 탭을 클릭 합니다.
3. `getRTCVLogs`메서드 이름에를 입력 하 고 페이로드에 json 형식 문자열을 입력 합니다. `{}`빈 페이로드로를 입력할 수 있습니다. 
4. 연결 및 메서드 시간 제한을 설정 하 고 _ * 호출 메서드 * *를 클릭 합니다.
5. 대상 컨테이너를 선택 하 고 **로깅 구문** 섹션에서 설명 하는 매개 변수를 사용 하 여 페이로드 json 문자열을 작성 합니다. **메서드 호출** 을 클릭 하 여 요청을 수행 합니다.

>[!NOTE]
> `getRTCVLogs`빈 페이로드를 사용 하 여 메서드를 호출 하면 장치에 배포 된 모든 컨테이너 목록이 반환 됩니다. 메서드 이름은 대/소문자를 구분합니다. 잘못 된 메서드 이름이 지정 된 경우 501 오류가 발생 합니다.

:::image type="content" source="./media/spatial-analysis/direct-log-collection.png" alt-text="Azure Monitor 원격 분석 보고서":::
![getRTCVLogs Direct 메서드 페이지](./media/spatial-analysis/direct-log-collection.png)

 
### <a name="logging-syntax"></a>로깅 구문

다음 표에서는 로그를 쿼리할 때 사용할 수 있는 매개 변수를 나열 합니다.

| 키워드 | Description | 기본값 |
|--|--|--|
| StartTime | 원하는 로그 시작 시간 (밀리초)입니다. | `-1`컨테이너 런타임의 시작 부분입니다. `[-1.-1]`가 시간 범위로 사용 되는 경우 API는 최근 1 시간에서 로그를 반환 합니다.|
| EndTime | 원하는 로그 종료 시간 (밀리초)입니다. | `-1`현재 시간입니다. `[-1.-1]`시간 범위를 사용 하는 경우 api는 최근 1 시간에서 로그를 반환 합니다. |
| ContainerId | 로그를 인출 하기 위한 대상 컨테이너입니다.| `null`컨테이너 ID가 없는 경우입니다. API는 Id를 사용 하 여 사용 가능한 모든 컨테이너 정보를 반환 합니다.|
| DoPost | 업로드 작업을 수행 합니다. 로 설정 되 면 `false` 요청 된 작업을 수행 하 고 업로드를 수행 하지 않고 업로드 크기를 반환 합니다. 로 설정 되 면 `true` 선택한 로그의 비동기 업로드가 시작 됩니다. | `false`, 업로드 하지 않습니다.|
| 제한 | 일괄 처리당 업로드할 로그의 줄 수를 표시 합니다. | `1000`이 매개 변수를 사용 하 여 post 속도를 조정 합니다. |
| 필터 | 업로드할 로그를 필터링 합니다. | `null`에서는 공간 분석 로그 구조를 기반으로 하는 키 값 쌍으로 필터를 지정할 수 있습니다 `[UTC, LocalTime, LOGLEVEL,PID, CLASS, DATA]` . `{"TimeFilter":[-1,1573255761112]}, {"TimeFilter":[-1,1573255761112]}, {"CLASS":["myNode"]`|

다음 표에서는 쿼리 응답의 특성을 나열 합니다.

| 키워드 | 설명|
|--|--|
|DoPost| *True* 또는 *false*입니다. 로그가 업로드 되었는지 여부를 나타냅니다. 로그를 업로드 하지 않도록 선택 하는 경우 api는 정보 ***동기적**_을 반환 합니다. 로그를 업로드 하도록 선택 하는 경우 api는 200을 반환 하 고, 요청이 올바르면 로그를 _*_비동기식_*_ 으로 업로드 하기 시작 합니다.|
|TimeFilter| 로그에 적용 된 시간 필터입니다.|
|ValueFilters| 로그에 적용 되는 키워드 필터입니다. |
|타임스탬프| 메서드 실행 시작 시간입니다. |
|ContainerId| 대상 컨테이너 ID입니다. |
|FetchCounter| 총 로그 줄 수입니다. |
|FetchSizeInByte| 로그 데이터의 총 크기 (바이트)입니다. |
|MatchCounter| 유효한 로그 줄 수입니다. |
|MatchSizeInByte| 유효한 로그 데이터 크기 (바이트)입니다. |
|FilterCount| 필터를 적용 한 후의 총 로그 줄 수입니다. |
|FilterSizeInByte| 필터를 적용 한 후의 총 로그 데이터 크기 (바이트)입니다. |
|FetchLogsDurationInMiliSec| 가져오기 작업 기간입니다. |
|PaseLogsDurationInMiliSec| 필터 작업 기간입니다. |
|PostLogsDurationInMiliSec| 사후 작업 기간입니다. |

#### <a name="example-request"></a>요청 예 

```json
{
    "StartTime": -1,
    "EndTime": -1,
    "ContainerId": "5fa17e4d8056e8d16a5a998318716a77becc01b36fde25b3de9fde98a64bf29b",
    "DoPost": false,
    "Filters": null
}
```

#### <a name="example-response"></a>예제 응답 

```json
{
    "status": 200,
    "payload": {
        "DoPost": false,
        "TimeFilter": [-1, 1581310339411],
        "ValueFilters": {},
        "Metas": {
            "TimeStamp": "2020-02-10T04:52:19.4365389+00:00",
            "ContainerId": "5fa17e4d8056e8d16a5a998318716a77becc01b36fde25b3de9fde98a64bf29b",
            "FetchCounter": 61,
            "FetchSizeInByte": 20470,
            "MatchCounter": 61,
            "MatchSizeInByte": 20470,
            "FilterCount": 61,
            "FilterSizeInByte": 20470,
            "FetchLogsDurationInMiliSec": 0,
            "PaseLogsDurationInMiliSec": 0,
            "PostLogsDurationInMiliSec": 0
        }
    }
}
```

Fetch log의 줄, 시간 및 크기를 확인 합니다. 해당 설정이에 대 한 올바른 대체 _*_Dopost_*_ 로 표시 되 `true` 고 대상에 동일한 필터가 있는 로그를 푸시합니다. 

문제를 해결할 때 Azure Blob Storage에서 로그를 내보낼 수 있습니다. 

## <a name="common-issues"></a>일반적인 문제

모듈 로그에 다음 메시지가 표시 되는 경우 Azure 구독을 승인 해야 함을 의미할 수 있습니다. 

"컨테이너가 올바른 상태가 아닙니다. ' 불일치 ' 상태의 구독 유효성 검사에 실패 했습니다. 지정 된 컨테이너 형식에 대 한 Api 키가 아닙니다. "

자세한 내용은 [컨테이너 실행에 대 한 승인 요청](spatial-analysis-container.md#request-approval-to-run-the-container)을 참조 하세요.

## <a name="troubleshooting-the-azure-stack-edge-device"></a>Edge 장치 Azure Stack 문제 해결

다음 섹션에서는 Azure Stack Edge 장치의 상태에 대 한 디버깅 및 유효성 검사에 대 한 도움말을 제공 합니다.

### <a name="access-the-kubernetes-api-endpoint"></a>Kubernetes API 끝점에 액세스 합니다. 

1. 장치의 로컬 UI에서 _*장치** 페이지로 이동 합니다. 
2. **장치 끝점**에서 Kubernetes API 서비스 끝점을 복사 합니다. 이 엔드포인트는 `https://compute..[device-IP-address]` 같은 형식의 문자열입니다.
3. 엔드포인트 문자열을 저장합니다. 나중에 `kubectl` Kubernetes 클러스터에 액세스 하도록를 구성할 때이를 사용 합니다.

### <a name="connect-to-powershell-interface"></a>PowerShell 인터페이스에 연결

원격으로 Windows 클라이언트에서 연결 합니다. Kubernetes 클러스터를 만든 후이 클러스터를 통해 응용 프로그램을 관리할 수 있습니다. 장치의 PowerShell 인터페이스에 연결 해야 합니다. 클라이언트의 운영 체제에 따라 장치에 원격으로 연결 하는 절차가 다를 수 있습니다. PowerShell을 실행 하는 Windows 클라이언트에 대 한 단계는 다음과 같습니다.

> [!TIP]
> * 시작 하기 전에 Windows 클라이언트에서 Windows PowerShell 5.0 이상을 실행 하 고 있는지 확인 합니다.
> * PowerShell은 [Linux 에서도 사용할 수](https://docs.microsoft.com/powershell/scripting/install/installing-powershell-core-on-linux)있습니다.

1. 관리자 권한으로 Windows PowerShell 세션을 실행 합니다. 
    1. Windows 원격 관리 서비스가 클라이언트에서 실행 되 고 있는지 확인 합니다. 명령 프롬프트에서 `winrm quickconfig`를 입력합니다.

2. 장치 IP 주소에 대 한 변수를 할당 합니다. 정의합니다(예: `$ip = "<device-ip-address>"`).

3. 다음 명령을 사용 하 여 장치의 IP 주소를 클라이언트의 신뢰할 수 있는 호스트 목록에 추가 합니다. 

    ```powershell
    Set-Item WSMan:\localhost\Client\TrustedHosts $ip -Concatenate -Force
    ```
 
4. 장치에서 Windows PowerShell 세션을 시작 합니다. 

    ```powershell
    Enter-PSSession -ComputerName $ip -Credential $ip\EdgeUser -ConfigurationName Minishell
    ```

5. 메시지가 표시 되 면 암호를 입력 합니다. 로컬 웹 인터페이스에 로그인 하는 데 사용 되는 것과 동일한 암호를 사용 합니다. 기본 로컬 웹 인터페이스 암호는 `Password1` 입니다. 

### <a name="access-the-kubernetes-cluster"></a>Kubernetes 클러스터에 액세스

Kubernetes 클러스터를 만든 후 명령줄 도구를 사용 하 여 `kubectl` 클러스터에 액세스할 수 있습니다.

1. 새 네임 스페이스를 만듭니다. 

    ```powershell
    New-HcsKubernetesNamespace -Namespace
    ```

2. 사용자를 만들고 구성 파일을 가져옵니다. 이 명령은 Kubernetes 클러스터에 대 한 구성 정보를 출력 합니다. 이 정보를 복사 하 여 *config*라는 파일에 저장 합니다. 파일 확장명을 파일에 저장 하지 않습니다.
    
    ```powershell
    New-HcsKubernetesUser -UserName
    ```

3. 로컬 컴퓨터의 사용자 프로필에 있는 *kube* 폴더에 *구성* 파일을 추가 합니다.   

4. 만든 사용자와 네임 스페이스를 연결 합니다.

    ```powershell
    Grant-HcsKubernetesNamespaceAccess -Namespace -UserName
    ```

5. `kubectl`다음 명령을 사용 하 여 Windows 클라이언트에를 설치 합니다.
    
    ```powershell
    curl https://storage.googleapis.com/kubernetesrelease/release/v1.15.2/bin/windows/amd64/kubectl.exe -O kubectl.exe
    ```

6. 시스템의 호스트 파일에 DNS 항목을 추가 합니다. 
    1. 관리자 권한으로 메모장을 실행 하 고에 있는 *hosts* 파일을 엽니다 `C:\windows\system32\drivers\etc\hosts` . 
    2. 로컬 UI의 **장치** 페이지에서 가져온 장치 IP 주소 및 DNS 도메인을 사용 하 여 호스트 파일에 항목을 만듭니다. 사용 해야 하는 끝점은 다음과 같이 표시 됩니다 `https://compute.asedevice.microsoftdatabox.com/10.100.10.10` .

7. Kubernetes pod에 연결할 수 있는지 확인 합니다.

    ```powershell
    kubectl get pods -n "iotedge"
    ```

컨테이너 로그를 가져오려면 다음 명령을 실행 합니다.

```powershell
kubectl logs <pod-name> -n <namespace> --all-containers
```

### <a name="useful-commands"></a>유용한 명령

|명령  |설명  |
|---------|---------|
|`Get-HcsKubernetesUserConfig -AseUser`     | Kubernetes 구성 파일을 생성 합니다. 명령을 사용 하는 경우이 정보를 *config*라는 파일로 복사 합니다. 파일 확장명을 사용 하 여 파일을 저장 하지 않습니다.        |
| `Get-HcsApplianceInfo` | 장치에 대 한 정보를 반환 합니다. |
| `Enable-HcsSupportAccess` | 지원 세션을 시작 하기 위한 액세스 자격 증명을 생성 합니다. |

## <a name="next-steps"></a>다음 단계

* [사용자를 계산 하는 웹 응용 프로그램 배포](spatial-analysis-web-app.md)
* [공간 분석 작업 구성](./spatial-analysis-operations.md)
* [카메라 배치 가이드](spatial-analysis-camera-placement.md)
* [영역 및 줄 배치 가이드](spatial-analysis-zone-line-placement.md)
