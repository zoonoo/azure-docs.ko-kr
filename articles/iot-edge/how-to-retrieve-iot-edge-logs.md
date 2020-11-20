---
title: IoT Edge 로그 검색-Azure IoT Edge
description: 모듈 로그 검색을 IoT Edge Azure Blob Storage에 업로드 합니다.
author: v-tcassi
manager: philmea
ms.author: v-tcassi
ms.date: 11/12/2020
ms.topic: conceptual
ms.reviewer: veyalla
ms.service: iot-edge
ms.custom: devx-track-azurecli
services: iot-edge
ms.openlocfilehash: 97cdc4ad0b1d5e7dfb6642fa0163f810be5d7171
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/20/2020
ms.locfileid: "94966924"
---
# <a name="retrieve-logs-from-iot-edge-deployments"></a>IoT Edge 배포에서 로그 검색

IoT Edge agent 모듈에 포함 된 직접 메서드를 사용 하 여 장치에 대 한 물리적 또는 SSH 액세스 없이 IoT Edge 배포에서 로그를 검색 합니다. 직접 메서드는 장치에서 구현 된 다음 클라우드에서 호출할 수 있습니다. IoT Edge 에이전트에는 IoT Edge 장치를 원격으로 모니터링 하 고 관리 하는 데 도움이 되는 직접 방법이 포함 되어 있습니다. 이 문서에서 설명 하는 직접 메서드는 일반적으로 1.0.10 릴리스와 함께 사용할 수 있습니다.

직접 메서드에 대 한 자세한 내용, 사용 방법 및 사용자 고유의 모듈에서 구현 하는 방법에 대 한 자세한 내용은 [IoT Hub에서 직접 메서드 이해 및 호출](../iot-hub/iot-hub-devguide-direct-methods.md)을 참조 하세요.

이러한 직접 메서드의 이름은 대/소문자를 구분 하 여 처리 됩니다.

## <a name="recommended-logging-format"></a>권장 로깅 형식

필수는 아니지만이 기능과 가장 잘 호환 되는 경우 권장 되는 로깅 형식은 다음과 같습니다.

```
<{Log Level}> {Timestamp} {Message Text}
```

`{Log Level}` 은 [Syslog 심각도 수준 형식을](https://wikipedia.org/wiki/Syslog#Severity_lnevel) 따라야 하며 형식 `{Timestamp}` 으로 지정 해야 합니다 `yyyy-mm-dd hh:mm:ss.fff zzz` .

[IoT Edge의로 거 클래스](https://github.com/Azure/iotedge/blob/master/edge-util/src/Microsoft.Azure.Devices.Edge.Util/Logger.cs) 는 정식 구현으로 사용 됩니다.

## <a name="retrieve-module-logs"></a>모듈 로그 검색

**GetModuleLogs** direct 메서드를 사용 하 여 IoT Edge 모듈의 로그를 검색 합니다.

이 메서드는 다음 스키마를 사용 하 여 JSON 페이로드를 수락 합니다.

```json
    {
       "schemaVersion": "1.0",
       "items": [
          {
             "id": "regex string",
             "filter": {
                "tail": int,
                "since": int,
                "until": int,
                "loglevel": int,
                "regex": "regex string"
             }
          }
       ],
       "encoding": "gzip/none",
       "contentType": "json/text" 
    }
```

| 속성 | 유형 | Description |
|-|-|-|
| schemaVersion | 문자열 | `1.0` |
| items | JSON 배열 | 및 튜플을 포함 하는 배열 `id` `filter` 입니다. |
| ID | 문자열 | 모듈 이름을 제공 하는 정규식입니다. Edge 장치에서 여러 모듈을 일치 시킬 수 있습니다. [.Net 정규식](/dotnet/standard/base-types/regular-expressions) 형식이 필요 합니다. |
| filter | JSON 섹션 | 튜플의 정규식과 일치 하는 모듈에 적용할 로그 필터 `id` 입니다. |
| 비상 | integer | 최근부터 검색 하는 과거의 로그 줄 수입니다. 옵션. |
| since | integer | 이 시간 이후에만 로그를 duration (1 d, 90 m, 2 일 3 시간 2 분), rfc3339 timestamp 또는 UNIX 타임 스탬프로 반환 합니다.  및를 모두 지정 하는 경우에는 `tail` `since` 먼저 값을 사용 하 여 로그를 검색 합니다 `since` . 그런 다음 `tail` 값이 결과에 적용 되 고 최종 결과가 반환 됩니다. 옵션. |
| until | integer | 지정 된 시간 이전에 rfc3339 타임 스탬프, UNIX 타임 스탬프 또는 기간 (1 d, 90 m, 2 일 3 시간 2 분)의 로그만 반환 합니다. 옵션. |
| 로그 수준 | integer | 지정 된 로그 수준 보다 작거나 같은 로그 줄을 필터링 합니다. 로그 줄은 권장 되는 로깅 형식을 따르고 [Syslog 심각도 수준](https://en.wikipedia.org/wiki/Syslog#Severity_level) 표준을 사용 해야 합니다. 옵션. |
| regex | 문자열 | [.Net 정규식](/dotnet/standard/base-types/regular-expressions) 형식을 사용 하 여 지정 된 정규식과 일치 하는 내용이 있는 로그 줄을 필터링 합니다. 옵션. |
| encoding | 문자열 | `gzip` 또는 `none`입니다. 기본값은 `none`입니다. |
| contentType | 문자열 | `json` 또는 `text`입니다. 기본값은 `text`입니다. |

> [!NOTE]
> 로그 내용이 직접 메서드의 응답 크기 제한 (현재 128 KB)을 초과 하는 경우 응답에서 오류를 반환 합니다.

로그를 성공적으로 검색 하면 **"status": 200** 다음에 모듈에서 검색 된 로그가 포함 된 페이로드가 반환 되어 요청에 지정 하는 설정에 따라 필터링 됩니다.

예를 들면 다음과 같습니다.

```azurecli
az iot hub invoke-module-method --method-name 'GetModuleLogs' -n <hub name> -d <device id> -m '$edgeAgent' --method-payload \
'
    {
       "schemaVersion": "1.0",
       "items": [
          {
             "id": "edgeAgent",
             "filter": {
                "tail": 10
             }
          }
       ],
       "encoding": "none",
       "contentType": "text"
    }
'
```

Azure Portal에서 메서드 이름 `GetModuleLogs` 및 다음 JSON 페이로드를 사용 하 여 메서드를 호출 합니다.

```json
    {
       "schemaVersion": "1.0",
       "items": [
          {
             "id": "edgeAgent",
             "filter": {
                "tail": 10
             }
          }
       ],
       "encoding": "none",
       "contentType": "text"
    }
```

![Azure Portal에서 직접 메서드 ' GetModuleLogs '를 호출 합니다.](./media/how-to-retrieve-iot-edge-logs/invoke-get-module-logs.png)

CLI 출력을 [gzip](https://en.wikipedia.org/wiki/Gzip)과 같은 Linux 유틸리티로 파이프 하 여 압축 된 응답을 처리할 수도 있습니다. 예를 들면 다음과 같습니다.

```azurecli
az iot hub invoke-module-method \
  --method-name 'GetModuleLogs' \
  -n <hub name> \
  -d <device id> \
  -m '$edgeAgent' \
  --method-payload '{"contentType": "text","schemaVersion": "1.0","encoding": "gzip","items": [{"id": "edgeHub","filter": {"since": "2d","tail": 1000}}],}' \
  -o tsv --query 'payload[0].payloadBytes' \
  | base64 --decode \
  | gzip -d
```

## <a name="upload-module-logs"></a>모듈 로그 업로드

**UploadModuleLogs** direct 메서드를 사용 하 여 요청 된 로그를 지정 된 Azure Blob Storage 컨테이너로 보냅니다.

<!-- 1.2.0 -->
::: moniker range=">=iotedge-2020-11"

> [!NOTE]
> 게이트웨이 장치 뒤의 장치에서 로그를 업로드 하려는 경우에는 최상위 계층 장치에서 [API 프록시 및 blob 저장소 모듈](how-to-configure-api-proxy-module.md) 을 구성 해야 합니다. 이러한 모듈은 게이트웨이 장치를 통해 하위 계층 장치의 로그를 클라우드의 저장소로 라우팅합니다.

::: moniker-end

이 메서드는 "sasUrl" 키를 추가 하 여 **GetModuleLogs** 와 유사한 JSON 페이로드를 수락 합니다.

```json
    {
       "schemaVersion": "1.0",
       "sasUrl": "Full path to SAS URL",
       "items": [
          {
             "id": "regex string",
             "filter": {
                "tail": int,
                "since": int,
                "until": int,
                "loglevel": int,
                "regex": "regex string"
             }
          }
       ],
       "encoding": "gzip/none",
       "contentType": "json/text" 
    }
```

| 속성 | 유형 | Description |
|-|-|-|
| sasURL | 문자열 (URI) | [Azure Blob Storage 컨테이너에 대 한 쓰기 권한이 있는 공유 액세스 서명 URL](/archive/blogs/jpsanders/easily-create-a-sas-to-download-a-file-from-azure-storage-using-azure-storage-explorer)입니다. |

로그 업로드 요청이 성공 하면 **"status": 200** 다음에 스키마가 포함 된 페이로드가 반환 됩니다.

```json
    {
        "status": "string",
        "message": "string",
        "correlationId": "GUID"
    }
```

| 속성 | 유형 | Description |
|-|-|-|
| 상태 | 문자열 | `NotStarted`, `Running` , `Completed` , 또는 중 하나 `Failed` `Unknown` 입니다. |
| message | 문자열 | 오류가 발생 하면 메시지이 고, 그렇지 않으면 빈 문자열입니다. |
| correlationId | 문자열   | 업로드 요청의 상태를 쿼리할 ID입니다. |

예를 들면 다음과 같습니다.

다음 호출은 모든 모듈의 마지막 100 로그 줄을 압축 된 JSON 형식으로 업로드 합니다.

```azurecli
az iot hub invoke-module-method --method-name UploadModuleLogs -n <hub name> -d <device id> -m \$edgeAgent --method-payload \
'
    {
        "schemaVersion": "1.0",
        "sasUrl": "<sasUrl>",
        "items": [
            {
                "id": ".*",
                "filter": {
                    "tail": 100
                }
            }
        ],
        "encoding": "gzip",
        "contentType": "json"
    }
'
```

다음 호출에서는 edgeAgent 및 edgeHub의 마지막 100 로그 줄에서 tempSensor 모듈의 마지막 1000 로그 줄을 압축 되지 않은 텍스트 형식으로 업로드 합니다.

```azurecli
az iot hub invoke-module-method --method-name UploadModuleLogs -n <hub name> -d <device id> -m \$edgeAgent --method-payload \
'
    {
        "schemaVersion": "1.0",
        "sasUrl": "<sasUrl>",
        "items": [
            {
                "id": "edge",
                "filter": {
                    "tail": 100
                }
            },
            {
                "id": "tempSensor",
                "filter": {
                    "tail": 1000
                }
            }
        ],
        "encoding": "none",
        "contentType": "text"
    }
'
```

Azure Portal에서 `UploadModuleLogs` sasURL을 사용자의 정보로 채운 후 메서드 이름 및 다음 JSON 페이로드를 사용 하 여 메서드를 호출 합니다.

```json
    {
       "schemaVersion": "1.0",
       "sasUrl": "<sasUrl>",
       "items": [
          {
             "id": "edgeAgent",
             "filter": {
                "tail": 10
             }
          }
       ],
       "encoding": "none",
       "contentType": "text"
    }
```

![Azure Portal에서 직접 메서드 ' UploadModuleLogs '를 호출 합니다.](./media/how-to-retrieve-iot-edge-logs/invoke-upload-module-logs.png)

## <a name="upload-support-bundle-diagnostics"></a>업로드 지원 번들 진단

**UploadSupportBundle** direct 메서드를 사용 하 여 IoT Edge 모듈 로그의 zip 파일을 사용 가능한 Azure Blob Storage 컨테이너에 번들 하 고 업로드할 수 있습니다. 이 직접 메서드는 [`iotedge support-bundle`](./troubleshoot.md#gather-debug-information-with-support-bundle-command) IoT Edge 장치에서 명령을 실행 하 여 로그를 가져옵니다.

<!-- 1.2.0 -->
::: moniker range=">=iotedge-2020-11"

> [!NOTE]
> 게이트웨이 장치 뒤의 장치에서 로그를 업로드 하려는 경우에는 최상위 계층 장치에서 [API 프록시 및 blob 저장소 모듈](how-to-configure-api-proxy-module.md) 을 구성 해야 합니다. 이러한 모듈은 게이트웨이 장치를 통해 하위 계층 장치의 로그를 클라우드의 저장소로 라우팅합니다.

::: moniker-end

이 메서드는 다음 스키마를 사용 하 여 JSON 페이로드를 수락 합니다.

```json
    {
        "schemaVersion": "1.0",
        "sasUrl": "Full path to SAS url",
        "since": "2d",
        "until": "1d",
        "edgeRuntimeOnly": false
    }
```

| 속성 | 유형 | Description |
|-|-|-|
| schemaVersion | 문자열 | `1.0` |
| sasURL | 문자열 (URI) | [Azure Blob Storage 컨테이너에 대 한 쓰기 권한이 있는 공유 액세스 서명 URL](/archive/blogs/jpsanders/easily-create-a-sas-to-download-a-file-from-azure-storage-using-azure-storage-explorer) |
| since | integer | 이 시간 이후에만 로그를 duration (1 d, 90 m, 2 일 3 시간 2 분), rfc3339 timestamp 또는 UNIX 타임 스탬프로 반환 합니다. 옵션. |
| until | integer | 지정 된 시간 이전에 rfc3339 타임 스탬프, UNIX 타임 스탬프 또는 기간 (1 d, 90 m, 2 일 3 시간 2 분)의 로그만 반환 합니다. 옵션. |
| edgeRuntimeOnly | boolean | True 이면 Edge 에이전트, Edge Hub 및 Edge 보안 디먼의 로그만 반환 합니다. 기본값: false입니다.  옵션. |

> [!IMPORTANT]
> IoT Edge 지원 번들은 개인 식별이 가능한 정보를 포함할 수 있습니다.

로그 업로드 요청이 성공 하면 **"status": 200** 다음에 **UploadModuleLogs** 응답과 동일한 스키마를 가진 페이로드가 반환 됩니다.

```json
    {
        "status": "string",
        "message": "string",
        "correlationId": "GUID"
    }
```

| 속성 | 유형 | Description |
|-|-|-|
| 상태 | 문자열 | `NotStarted`, `Running` , `Completed` , 또는 중 하나 `Failed` `Unknown` 입니다. |
| message | 문자열 | 오류가 발생 하면 메시지이 고, 그렇지 않으면 빈 문자열입니다. |
| correlationId | 문자열   | 업로드 요청의 상태를 쿼리할 ID입니다. |

예를 들면 다음과 같습니다.

```azurecli
az iot hub invoke-module-method --method-name 'UploadSupportBundle' -n <hub name> -d <device id> -m '$edgeAgent' --method-payload \
'
    {
        "schemaVersion": "1.0",
        "sasUrl": "Full path to SAS url",
        "since": "2d",
        "until": "1d",
        "edgeRuntimeOnly": false
    }
'
```

Azure Portal에서 `UploadSupportBundle` sasURL을 사용자의 정보로 채운 후 메서드 이름 및 다음 JSON 페이로드를 사용 하 여 메서드를 호출 합니다.

```json
    {
        "schemaVersion": "1.0",
        "sasUrl": "Full path to SAS url",
        "since": "2d",
        "until": "1d",
        "edgeRuntimeOnly": false
    }
```

![Azure Portal에서 직접 메서드 ' UploadSupportBundle '를 호출 합니다.](./media/how-to-retrieve-iot-edge-logs/invoke-upload-support-bundle.png)

## <a name="get-upload-request-status"></a>업로드 요청 상태 가져오기

**Gettaskstatus** direct 메서드를 사용 하 여 로그 업로드 요청의 상태를 쿼리 합니다. **Gettaskstatus** 요청 페이로드는 로그 업로드 요청의를 사용 하 여 `correlationId` 작업의 상태를 가져옵니다. `correlationId` **UploadModuleLogs** 직접 메서드 호출에 대 한 응답으로이 반환 됩니다.

이 메서드는 다음 스키마를 사용 하 여 JSON 페이로드를 수락 합니다.

```json
    {
      "schemaVersion": "1.0",
      "correlationId": "<GUID>"
    }
```

로그 업로드 요청이 성공 하면 **"status": 200** 다음에 **UploadModuleLogs** 응답과 동일한 스키마를 가진 페이로드가 반환 됩니다.

```json
    {
        "status": "string",
        "message": "string",
        "correlationId": "GUID"
    }
```

| 속성 | 유형 | Description |
|-|-|-|
| 상태 | 문자열 | `NotStarted`, `Running` , `Completed` , 또는 중 하나 `Failed` `Unknown` 입니다. |
| message | 문자열 | 오류가 발생 하면 메시지이 고, 그렇지 않으면 빈 문자열입니다. |
| correlationId | 문자열   | 업로드 요청의 상태를 쿼리할 ID입니다. |

예를 들면 다음과 같습니다.

```azurecli
az iot hub invoke-module-method --method-name 'GetTaskStatus' -n <hub name> -d <device id> -m '$edgeAgent' --method-payload \
'
    {
      "schemaVersion": "1.0",
      "correlationId": "<GUID>"
    }
'
```

Azure Portal에서 `GetTaskStatus` GUID를 정보로 채운 후 메서드 이름 및 다음 JSON 페이로드를 사용 하 여 메서드를 호출 합니다.

```json
    {
      "schemaVersion": "1.0",
      "correlationId": "<GUID>"
    }
```

![Azure Portal에서 직접 메서드 ' GetTaskStatus '를 호출 합니다.](./media/how-to-retrieve-iot-edge-logs/invoke-get-task-status.png)

## <a name="next-steps"></a>다음 단계

[IoT Edge 에이전트 및 IoT Edge 허브 모듈 쌍의 속성](module-edgeagent-edgehub.md)
