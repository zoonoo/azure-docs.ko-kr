---
title: 기본 제공 메트릭 액세스-Azure IoT Edge
description: IoT Edge 런타임 구성 요소에서 기본 제공 메트릭에 대 한 원격 액세스
author: v-tcassi
manager: philmea
ms.author: v-tcassi
ms.date: 10/05/2020
ms.topic: conceptual
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: b6f4e50cac2f809172c2525ea9136a63e6bd9066
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/16/2020
ms.locfileid: "92107014"
---
# <a name="access-built-in-metrics"></a>기본 제공 메트릭 액세스

IoT Edge 런타임 구성 요소 IoT Edge 허브 및 IoT Edge 에이전트는 기본 제공 메트릭을 [프로메테우스 표시 형식](https://prometheus.io/docs/instrumenting/exposition_formats/)으로 생성 합니다. 이러한 메트릭에 원격으로 액세스 하 여 IoT Edge 장치의 상태를 모니터링 하 고 파악 합니다.

릴리스 1.0.10에서 메트릭은 **edgeHub** 및 **edgeAgent** 모듈 (및)의 **포트 9600** 에 기본적으로 자동으로 노출 됩니다 `http://edgeHub:9600/metrics` `http://edgeAgent:9600/metrics` . 기본적으로 호스트에 매핑된 포트는 아닙니다.

모듈의에서 메트릭 포트를 노출 하 고 매핑하여 호스트에서 메트릭에 액세스 합니다 `createOptions` . 아래 예제에서는 기본 메트릭 포트를 호스트의 포트 9601에 매핑합니다.

```
{
  "ExposedPorts": {
    "9600/tcp": {},
  },
  "HostConfig": {
    "PortBindings": {
      "9600/tcp": [
        {
          "HostPort": "9601"
        }
      ]
    }
  }
}
```

EdgeHub 및 edgeAgent의 메트릭 끝점을 모두 매핑하는 경우에는 서로 다른 고유한 호스트 포트 번호를 선택 합니다.

> [!NOTE]
> 메트릭을 사용 하지 않도록 설정 하려면 `MetricsEnabled` edgeAgent에 대해 환경 변수를로 설정 `false` 합니다. **edgeAgent**

## <a name="available-metrics"></a>사용 가능한 메트릭

메트릭은 수집 되는 메트릭의 특성을 식별 하는 데 도움이 되는 태그를 포함 합니다. 모든 메트릭에는 다음 태그가 포함 됩니다.

| 태그 | 설명 |
|-|-|
| iothub | 장치가 통신 하 고 있는 허브 |
| edge_device | 현재 장치의 ID입니다. |
| instance_number | 현재 런타임을 나타내는 GUID입니다. 다시 시작 시 모든 메트릭이 다시 설정 됩니다. 이 GUID를 사용 하면 다시 시작을 보다 쉽게 조정할 수 있습니다. |

프로메테우스 표시 형식에는 카운터, 계기, 히스토그램 및 요약의 네 가지 핵심 메트릭 유형이 있습니다. 여러 메트릭 유형에 대 한 자세한 내용은 [프로메테우스 메트릭 유형 설명서](https://prometheus.io/docs/concepts/metric_types/)를 참조 하세요.

기본 제공 히스토그램 및 요약 메트릭에 대해 제공 되는 변 위치는 0.1, 0.5, 0.9 및 0.99입니다.

**EdgeHub** 모듈은 다음과 같은 메트릭을 생성 합니다.

| Name | 차원 | Description |
|-|-|-|
| `edgehub_gettwin_total` | `source` (작업 원본)<br> `id` (모듈 ID) | 형식: counter<br> GetTwin 호출의 총 수 |
| `edgehub_messages_received_total` | `route_output` (메시지를 보낸 출력)<br> `id` | 형식: counter<br> 클라이언트에서 받은 총 메시지 수입니다. |
| `edgehub_messages_sent_total` | `from` (메시지 원본)<br> `to` (메시지 대상)<br>`from_route_output`<br> `to_route_input` (메시지 대상 입력)<br> `priority` (대상에 대 한 메시지 우선 순위) | 형식: counter<br> 클라이언트 또는 업스트림에 전송 된 총 메시지 수입니다.<br> `to_route_input`가 $upstream 경우 비어 있습니다. `to` |
| `edgehub_reported_properties_total` | `target`(업데이트 대상)<br> `id` | 형식: counter<br> 보고 된 총 속성 업데이트 호출 |
| `edgehub_message_size_bytes` | `id`<br> | 유형: 요약<br> 클라이언트의 메시지 크기<br> `NaN`특정 기간 (현재 10 분) 동안 새 측정이 보고 되지 않는 것 처럼 값을 보고할 수 있습니다. `summary` 형식의 경우 해당 `_count` 및 카운터를 `_sum` 내보냅니다. |
| `edgehub_gettwin_duration_seconds` | `source` <br> `id` | 유형: 요약<br> 쌍 가져오기 작업에 소요 된 시간 |
| `edgehub_message_send_duration_seconds` | `from`<br> `to`<br> `from_route_output`<br> `to_route_input` | 유형: 요약<br> 메시지를 보내는 데 걸린 시간 |
| `edgehub_message_process_duration_seconds` | `from` <br> `to` <br> `priority` | 유형: 요약<br> 큐에서 메시지를 처리 하는 데 걸린 시간 |
| `edgehub_reported_properties_update_duration_seconds` | `target`<br> `id` | 유형: 요약<br> 보고 된 속성을 업데이트 하는 데 걸린 시간 |
| `edgehub_direct_method_duration_seconds` | `from` 호출자<br> `to` 수신자 | 유형: 요약<br> 직접 메시지를 확인 하는 데 걸린 시간 |
| `edgehub_direct_methods_total` | `from`<br> `to` | 형식: counter<br> 보낸 직접 메시지의 총 수 |
| `edgehub_queue_length` | `endpoint` (메시지 원본)<br> `priority` (큐 우선 순위) | 유형: 계기<br> 지정 된 우선 순위의 edgeHub 큐의 현재 길이입니다. |
| `edgehub_messages_dropped_total` | `reason` (no_route, ttl_expiry)<br> `from` <br> `from_route_output` | 형식: counter<br> 이유로 제거 된 총 메시지 수 |
| `edgehub_messages_unack_total` | `reason` (storage_failure)<br> `from`<br> `from_route_output` | 형식: counter<br> 저장소 오류가 발생 하 여 승인 되지 않은 총 메시지 수입니다. |
| `edgehub_offline_count_total` | `id` | 형식: counter<br> EdgeHub 오프 라인 상태가 된 총 횟수입니다. |
| `edgehub_offline_duration_seconds`| `id` | 유형: 요약<br> 오프 라인 상태인 시간 경계 허브 |
| `edgehub_operation_retry_total` | `id`<br> `operation` (작업 이름) | 형식: counter<br> EdgeHub 작업을 다시 시도한 총 횟수입니다. |
| `edgehub_client_connect_failed_total` | `id` <br> `reason` (인증 되지 않음)<br> | 형식: counter<br> 클라이언트에서 edgeHub에 연결 하지 못한 총 횟수입니다. |

**EdgeAgent** 모듈은 다음과 같은 메트릭을 생성 합니다.

| Name | 차원 | Description |
|-|-|-|
| `edgeAgent_total_time_running_correctly_seconds` | `module_name` | 유형: 계기<br> 모듈이 배포에 지정 되 고 실행 중 상태 였던 시간 |
| `edgeAgent_total_time_expected_running_seconds` | `module_name` | 유형: 계기<br> 배포에서 모듈이 지정 된 시간입니다. |
| `edgeAgent_module_start_total` | `module_name`, `module_version` | 형식: counter<br> EdgeAgent에서 모듈을 시작 하 라는 요청 횟수 |
| `edgeAgent_module_stop_total` | `module_name`, `module_version` | 형식: counter<br> EdgeAgent이 모듈을 중지 하는 데 docker를 요청 하는 횟수 |
| `edgeAgent_command_latency_seconds` | `command` | 유형: 계기<br> Docker에서 지정 된 명령을 실행 하는 데 걸린 시간입니다. 가능한 명령은 다음과 같습니다. 만들기, 업데이트, 제거, 시작, 중지, 다시 시작 |
| `edgeAgent_iothub_syncs_total` | | 형식: counter<br> EdgeAgent가 iotHub와 쌍을 동기화 하려고 시도 했을 때 성공 및 실패 한 횟수입니다. 이 수에는 쌍 및 허브를 요청 하는 두 에이전트가 쌍 업데이트에 대해 알립니다. |
| `edgeAgent_unsuccessful_iothub_syncs_total` | | 형식: counter<br> EdgeAgent에서 쌍을 iotHub와 동기화 하지 못한 횟수입니다. |
| `edgeAgent_deployment_time_seconds` | | 형식: counter<br> 변경을 받은 후 새 배포를 완료 하는 데 걸린 시간입니다. |
| `edgeagent_direct_method_invocations_count` | `method_name` | 형식: counter<br> Ping 또는 Restart와 같이 기본 제공 edgeAgent 직접 메서드가 호출 되는 횟수입니다. |
| `edgeAgent_host_uptime_seconds` | | 유형: 계기<br> 호스트가 설정 된 시간 |
| `edgeAgent_iotedged_uptime_seconds` | | 유형: 계기<br> I이상 가장자리가 실행 된 시간 |
| `edgeAgent_available_disk_space_bytes` | `disk_name`, `disk_filesystem`, `disk_filetype` | 유형: 계기<br> 디스크에 남아 있는 공간 크기 |
| `edgeAgent_total_disk_space_bytes` | `disk_name`, `disk_filesystem`, `disk_filetype`| 유형: 계기<br> 디스크 크기 |
| `edgeAgent_used_memory_bytes` | `module_name` | 유형: 계기<br> 모든 프로세스에서 사용 하는 RAM의 양 |
| `edgeAgent_total_memory_bytes` | `module_name` | 유형: 계기<br> 사용 가능한 RAM |
| `edgeAgent_used_cpu_percent` | `module_name` | 형식: 히스토그램<br> 모든 프로세스에서 사용 하는 cpu 비율 |
| `edgeAgent_created_pids_total` | `module_name` | 유형: 계기<br> 컨테이너가 만든 프로세스 또는 스레드 수 |
| `edgeAgent_total_network_in_bytes` | `module_name` | 유형: 계기<br> 네트워크에서 받은 바이트 수입니다. |
| `edgeAgent_total_network_out_bytes` | `module_name` | 유형: 계기<br> 네트워크로 전송 된 바이트 수 |
| `edgeAgent_total_disk_read_bytes` | `module_name` | 유형: 계기<br> 디스크에서 읽은 바이트 수입니다. |
| `edgeAgent_total_disk_write_bytes` | `module_name` | 유형: 계기<br> 디스크에 쓴 바이트 수입니다. |
| `edgeAgent_metadata` | `edge_agent_version`, `experimental_features`, `host_information` | 유형: 계기<br> 장치에 대 한 일반 메타 데이터입니다. 값은 항상 0 이며 정보는 태그에서 인코딩됩니다. 참고 `experimental_features` 및 `host_information` 는 json 개체입니다. `host_information` 처럼 보입니다 ```{"OperatingSystemType": "linux", "Architecture": "x86_64", "Version": "1.0.10~dev20200803.4", "Provisioning": {"Type": "dps.tpm", "DynamicReprovisioning": false, "AlwaysReprovisionOnStartup": true}, "ServerVersion": "19.03.6", "KernelVersion": "5.0.0-25-generic", "OperatingSystem": "Ubuntu 18.04.4 LTS", "NumCpus": 6, "Virtualized": "yes"}``` . 참고 `ServerVersion` 는 Docker 버전 이며 `Version` IoT Edge 보안 디먼 버전입니다. |

## <a name="next-steps"></a>다음 단계

* [Azure IoT Edge 런타임 및 해당 아키텍처 이해](iot-edge-runtime.md)
* [IoT Edge 에이전트 및 IoT Edge 허브 모듈 쌍의 속성](module-edgeagent-edgehub.md)
