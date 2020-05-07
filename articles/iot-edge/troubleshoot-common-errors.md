---
title: 일반 오류-Azure IoT Edge | Microsoft Docs
description: IoT Edge 솔루션을 배포할 때 발생 하는 일반적인 문제를 해결 하려면이 문서를 사용 합니다.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 04/27/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: ed93d24bc06a6622a8ace2b0ab6b44582da001c0
ms.sourcegitcommit: 31236e3de7f1933be246d1bfeb9a517644eacd61
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82783748"
---
# <a name="common-issues-and-resolutions-for-azure-iot-edge"></a>Azure IoT Edge에 대한 일반적인 문제 및 해결 방법

이 문서를 사용 하 여 IoT Edge 솔루션을 배포할 때 발생할 수 있는 일반적인 문제를 해결 하는 단계를 찾을 수 있습니다. IoT Edge 장치에서 로그 및 오류를 찾는 방법을 알아보려면 [IoT Edge 장치 문제 해결](troubleshoot.md)을 참조 하세요.

## <a name="iot-edge-agent-stops-after-about-a-minute"></a>1 분 후 IoT Edge 에이전트가 중지 됨

**관찰된 동작:**

EdgeAgent 모듈은 약 1 분 동안 성공적으로 시작 되 고 실행 된 후 중지 됩니다. 로그는 IoT Edge 에이전트가 AMQP를 통해 IoT Hub에 연결 하려고 시도 하 고 WebSocket을 통해 AMQP를 사용 하 여 연결을 시도 함을 표시 합니다. 이 작업이 실패 하면 IoT Edge 에이전트가 종료 됩니다.

예제 edgeAgent 로그:

```output
2017-11-28 18:46:19 [INF] - Starting module management agent.
2017-11-28 18:46:19 [INF] - Version - 1.0.7516610 (03c94f85d0833a861a43c669842f0817924911d5)
2017-11-28 18:46:19 [INF] - Edge agent attempting to connect to IoT Hub via AMQP...
2017-11-28 18:46:49 [INF] - Edge agent attempting to connect to IoT Hub via AMQP over WebSocket...
```

**근본 원인:**

호스트 네트워크의 네트워킹 구성으로 인해 IoT Edge 에이전트가 네트워크에 연결할 수 없습니다. 에이전트는 AMQP(포트 5671)를 통해 먼저 연결을 시도합니다. 이 연결이 실패하면 WebSockets(포트 443)을 시도합니다.

IoT Edge 런타임은 각 모듈이 통신할 네트워크를 설정합니다. Linux에서 이 네트워크는 브리지 네트워크입니다. Windows에서는 NAT를 사용합니다. 이 문제는 NAT 네트워크를 사용하는 Windows 컨테이너를 사용하는 Windows 디바이스에서 좀 더 자주 발생합니다.

**해결 방법:**

이 브리지/NAT 네트워크에 할당된 IP 주소가 인터넷에 연결되는 경로가 있는지 확인합니다. 경우에 따라 호스트의 VPN 구성은 IoT Edge 네트워크를 재정의합니다.

## <a name="iot-edge-agent-cant-access-a-modules-image-403"></a>IoT Edge 에이전트가 모듈의 이미지에 액세스할 수 없습니다. (403)

**관찰된 동작:**

컨테이너가 실행 되지 않고 edgeAgent 로그에 403 오류가 표시 됩니다.

**근본 원인:**

IoT Edge 에이전트에 모듈의 이미지에 액세스할 수 있는 권한이 없습니다.

**해결 방법:**

배포 매니페스트에 레지스트리 자격 증명이 올바르게 지정 되어 있는지 확인 합니다.

## <a name="edge-agent-module-reports-empty-config-file-and-no-modules-start-on-the-device"></a>Edge Agent 모듈이 ' 빈 구성 파일 '을 보고 하 고 장치에서 시작 되는 모듈이 없습니다.

**관찰된 동작:**

장치에서 배포에 정의 된 모듈을 시작 하는 데 문제가 있습니다. EdgeAgent 실행 중이지만 ' 빈 구성 파일 ... '을 계속 보고 합니다.

**근본 원인:**

기본적으로 IoT Edge는 자체 격리 된 컨테이너 네트워크에서 모듈을 시작 합니다. 장치가이 개인 네트워크 내에서 DNS 이름 확인에 문제가 있을 수 있습니다.

**해결 방법:**

**옵션 1: 컨테이너 엔진 설정에서 DNS 서버 설정**

엔진에서 시작 하는 모든 컨테이너 모듈에 적용 되는 컨테이너 엔진 설정에서 사용자 환경에 대 한 DNS 서버를 지정 합니다. 사용할 DNS 서버를 `daemon.json` 지정 하는 라는 파일을 만듭니다. 예를 들면 다음과 같습니다.

```json
{
    "dns": ["1.1.1.1"]
}
```

위의 예에서는 DNS 서버를 공개적으로 액세스할 수 있는 DNS 서비스로 설정 합니다. 에 지 장치가 해당 환경에서이 IP에 액세스할 수 없는 경우 액세스할 수 있는 DNS 서버 주소로 대체 합니다.

플랫폼 `daemon.json` 의 올바른 위치에 배치 합니다.

| 플랫폼 | 위치 |
| --------- | -------- |
| Linux | `/etc/docker` |
| Windows 컨테이너를 사용 하는 windows 호스트 | `C:\ProgramData\iotedge-moby\config` |

위치에 이미 파일이 있는 `daemon.json` 경우 **dns** 키를 추가 하 고 파일을 저장 합니다.

업데이트를 적용 하려면 컨테이너 엔진을 다시 시작 합니다.

| 플랫폼 | 명령 |
| --------- | -------- |
| Linux | `sudo systemctl restart docker` |
| Windows (관리자 PowerShell) | `Restart-Service iotedge-moby -Force` |

**옵션 2: 모듈 당 IoT Edge 배포에서 DNS 서버 설정**

IoT Edge 배포에서 각 모듈의 *Createoptions* 에 대해 DNS 서버를 설정할 수 있습니다. 예를 들면 다음과 같습니다.

```json
"createOptions": {
  "HostConfig": {
    "Dns": [
      "x.x.x.x"
    ]
  }
}
```

*EdgeAgent* 및 *edgeHub* 모듈에 대해서도이 구성을 설정 해야 합니다.

## <a name="iot-edge-hub-fails-to-start"></a>IoT Edge 허브가 시작 되지 않음

**관찰된 동작:**

EdgeHub 모듈을 시작 하지 못했습니다. 로그에서 다음 오류 중 하 나와 같은 메시지가 표시 될 수 있습니다.

```output
One or more errors occurred.
(Docker API responded with status code=InternalServerError, response=
{\"message\":\"driver failed programming external connectivity on endpoint edgeHub (6a82e5e994bab5187939049684fb64efe07606d2bb8a4cc5655b2a9bad5f8c80):
Error starting userland proxy: Bind for 0.0.0.0:443 failed: port is already allocated\"}\n)
```

또는

```output
info: edgelet_docker::runtime -- Starting module edgeHub...
warn: edgelet_utils::logging -- Could not start module edgeHub
warn: edgelet_utils::logging --     caused by: failed to create endpoint edgeHub on network nat: hnsCall failed in Win32:  
        The process cannot access the file because it is being used by another process. (0x20)
```

**근본 원인:**

호스트 컴퓨터의 일부 다른 프로세스에서 edgeHub 모듈이 바인딩하려는 포트를 바인딩 했습니다. IoT Edge 허브는 게이트웨이 시나리오에서 사용 하기 위해 포트 443, 5671 및 8883를 매핑합니다. 다른 프로세스에서 해당 포트 중 하나를 이미 바인딩한 경우 모듈을 시작 하지 못합니다.

**해결 방법:**

다음 두 가지 방법으로이 문제를 해결할 수 있습니다.

IoT Edge 장치가 게이트웨이 장치로 작동 하는 경우 포트 443, 5671 또는 8883을 사용 하는 프로세스를 찾아 중지 해야 합니다. 포트 443에 대 한 오류는 일반적으로 다른 프로세스가 웹 서버 임을 의미 합니다.

IoT Edge 장치를 게이트웨이로 사용할 필요가 없으면 edgeHub의 모듈 만들기 옵션에서 포트 바인딩을 제거할 수 있습니다. Azure Portal에서 또는 배포. json 파일에서 직접 만들기 옵션을 변경할 수 있습니다.

Azure Portal에서 다음을 수행합니다.

1. IoT hub로 이동 하 여 **IoT Edge**을 선택 합니다.

2. 업데이트 하려는 IoT Edge 장치를 선택 합니다.

3. **모듈 설정**을 선택 합니다.

4. **런타임 설정**을 선택 합니다.

5. **Edge 허브** 모듈 설정에서 **옵션 만들기** 텍스트 상자에 있는 모든 항목을 삭제 합니다.

6. 변경 내용을 저장 하 고 배포를 만듭니다.

배포. json 파일에서 다음을 수행 합니다.

1. IoT Edge 장치에 적용 한 배포 json 파일을 엽니다.

2. EdgeAgent desired `edgeHub` 속성 섹션에서 설정을 찾습니다.

   ```json
   "edgeHub": {
       "settings": {
           "image": "mcr.microsoft.com/azureiotedge-hub:1.0",
           "createOptions": "{\"HostConfig\":{\"PortBindings\":{\"8883/tcp\":[{\"HostPort\":\"8883\"}],\"443/tcp\":[{\"HostPort\":\"443\"}]}}}"
       },
       "type": "docker",
       "status": "running",
       "restartPolicy": "always"
   }
   ```

3. `createOptions` 줄을 제거 하 고 `image` 줄의 끝에 있는 후행 쉼표를 제거 합니다.

   ```json
   "edgeHub": {
       "settings": {
           "image": "mcr.microsoft.com/azureiotedge-hub:1.0"
       },
       "type": "docker",
       "status": "running",
       "restartPolicy": "always"
   }
   ```

4. 파일을 저장 하 고 IoT Edge 장치에 다시 적용 합니다.

## <a name="iot-edge-security-daemon-fails-with-an-invalid-hostname"></a>IoT Edge 보안 디먼이 유효하지 않은 호스트 이름으로 실패합니다.

**관찰된 동작:**

[IoT Edge security manager 로그를 확인 하는](troubleshoot.md#check-the-status-of-the-iot-edge-security-manager-and-its-logs) 데 실패 하 고 다음 메시지가 출력 됩니다.

```output
Error parsing user input data: invalid hostname. Hostname cannot be empty or greater than 64 characters
```

**근본 원인:**

IoT Edge 런타임은 64자 미만인 호스트 이름만을 지원할 수 있습니다. 물리적 머신은 일반적으로 긴 호스트 이름을 사용하지 않지만 문제는 가상 머신에서 자주 발생합니다. 특히 Azure에서 호스팅되는 Windows 가상 머신에 자동으로 생성된 호스트 이름이 너무 긴 경향이 있습니다.

**해결 방법:**

이 오류를 표시하는 경우 가상 머신의 DNS 이름을 구성한 다음, 설정 명령에서 DNS 이름을 호스트 이름으로 설정하여 해결할 수 있습니다.

1. Azure Portal에서 가상 머신의 개요 페이지로 이동합니다.
2. DNS 이름에서 **구성**을 선택합니다. 가상 머신에 DNS 이름이 이미 구성되어 있으면 새 이름을 구성할 필요가 없습니다.

   ![가상 머신의 DNS 이름 구성](./media/troubleshoot/configure-dns.png)

3. **DNS 이름 레이블**에 대한 값을 입력하고 **저장**을 선택합니다.
4. 새 DNS 이름을 복사 합니다 .이 이름은 ** \<DNSnamelabel\>형식 이어야 합니다.\< vmlocation\>. cloudapp.azure.com**.
5. 가상 머신 내에서 다음 명령을 사용하여 DNS 이름으로 IoT Edge 런타임을 설정합니다.

   * Linux에서:

      ```bash
      sudo nano /etc/iotedge/config.yaml
      ```

   * Windows에서:

      ```cmd
      notepad C:\ProgramData\iotedge\config.yaml
      ```

## <a name="cant-get-the-iot-edge-daemon-logs-on-windows"></a>Windows에서 IoT Edge 디먼 로그를 가져올 수 없습니다.

**관찰된 동작:**

Windows에서을 사용할 `Get-WinEvent` 때 EventLogException를 가져옵니다.

**근본 원인:**

`Get-WinEvent` PowerShell 명령은 특정 `ProviderName`별로 로그를 찾기 위해 표시된 레지스트리 항목을 사용합니다.

**해결 방법:**

IoT Edge 디먼에 대한 레지스트리 항목을 설정합니다. 다음 콘텐츠를 사용하여 **iotedge.reg** 파일을 만들고, 파일을 두 번 클릭하거나 `reg import iotedge.reg` 명령을 사용하여 Windows 레지스트리로 가져옵니다.

```reg
Windows Registry Editor Version 5.00

[HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\EventLog\Application\iotedged]
"CustomSource"=dword:00000001
"EventMessageFile"="C:\\ProgramData\\iotedge\\iotedged.exe"
"TypesSupported"=dword:00000007
```

## <a name="stability-issues-on-smaller-devices"></a>더 작은 장치에서 안정성 문제

**관찰된 동작:**

특히 게이트웨이로 사용 하는 경우 Raspberry Pi와 같은 리소스 제한 장치에서 안정성 문제가 발생할 수 있습니다. 몇 시간 후에는 장치에서 원격 분석 메시지를 전송 하지 못한 IoT Edge 허브 모듈에 메모리 부족 예외가 포함 되어 있습니다.

**근본 원인:**

IoT Edge 런타임의 일부인 IoT Edge 허브는 기본적으로 성능에 맞게 최적화 되 고 많은 양의 메모리를 할당 하려고 시도 합니다. 이 최적화는 제한된 에지 디바이스에 대해 이상적이지 않으며 안정성 문제를 유발할 수 있습니다.

**해결 방법:**

IoT Edge 허브의 경우 환경 변수 **OptimizeForPerformance** 을 **false**로 설정 합니다. 환경 변수를 설정 하는 방법에는 다음 두 가지가 있습니다.

Azure Portal에서 다음을 수행합니다.

IoT Hub에서 IoT Edge 장치를 선택 하 고 장치 세부 정보 페이지에서 **모듈** > **런타임 설정**설정을 선택 합니다. *False*로 설정 된 *OptimizeForPerformance* 이라는 IoT Edge hub 모듈에 대 한 환경 변수를 만듭니다.

![OptimizeForPerformance를 false로 설정](./media/troubleshoot/optimizeforperformance-false.png)

배포 매니페스트의 경우:

```json
"edgeHub": {
  "type": "docker",
  "settings": {
    "image": "mcr.microsoft.com/azureiotedge-hub:1.0",
    "createOptions": <snipped>
  },
  "env": {
    "OptimizeForPerformance": {
      "value": "false"
    }
  },
```

## <a name="iot-edge-module-fails-to-send-a-message-to-edgehub-with-404-error"></a>IoT Edge 모듈이 404 오류로 edgeHub에 메시지를 보내지 못함

**관찰된 동작:**

사용자 지정 IoT Edge 모듈에서 404 `Module not found` 오류가 발생 하 여 IoT Edge 허브로 메시지를 보내지 못합니다. IoT Edge 디먼은 로그에 다음 메시지를 출력합니다.

```output
Error: Time:Thu Jun  4 19:44:58 2018 File:/usr/sdk/src/c/provisioning_client/adapters/hsm_client_http_edge.c Func:on_edge_hsm_http_recv Line:364 executing HTTP request fails, status=404, response_buffer={"message":"Module not found"}u, 04 )
```

**근본 원인:**

IoT Edge 디먼은 보안상의 이유로 edgeHub에 연결하는 모든 모듈에 대해 프로세스 확인을 적용합니다. 이 디먼은 모듈이 전송하는 모든 메시지가 해당 모듈의 기본 프로세스 ID에서 온 것인지 확인합니다. 메시지가 처음에 설정한 것과는 다른 프로세스 ID의 모듈에서 전송될 경우 404 오류 메시지가 표시되며 거부됩니다.

**해결 방법:**

버전 1.0.7 모든 모듈 프로세스에 연결할 수 있는 권한이 부여 됩니다. 자세한 내용은 [1.0.7 릴리스 변경 로그](https://github.com/Azure/iotedge/blob/master/CHANGELOG.md#iotedged-1)를 참조 하세요.

1.0.7로 업그레이드할 수 없는 경우 다음 단계를 완료 합니다. 사용자 지정 IoT Edge 모듈이 edgeHub에 메시지를 보내는 데 항상 동일한 프로세스 ID를 사용하는지 확인합니다. 예를 들어 Docker 파일의 `ENTRYPOINT` `CMD` 명령 대신를 사용 해야 합니다. 이 `CMD` 명령은 모듈에 대해 하나의 프로세스 id와 주 프로그램을 실행 하는 bash 명령의 다른 프로세스 id를 대상으로 하며 `ENTRYPOINT` 단일 프로세스 id로 이어집니다.

## <a name="iot-edge-module-deploys-successfully-then-disappears-from-device"></a>IoT Edge 모듈 배포는 장치에서 성공적으로 사라집니다.

**관찰된 동작:**

IoT Edge 장치에 대 한 모듈을 설정한 후에는 모듈이 성공적으로 배포 되지만 몇 분 후에 장치 및 Azure Portal의 장치 세부 정보에서 사라집니다. 정의 된 모듈이 아니라 기타 모듈도 장치에 표시 될 수 있습니다.

**근본 원인:**

자동 배포에서 장치를 대상으로 하는 경우에는 단일 장치에 대 한 모듈을 수동으로 설정 하는 것 보다 우선 순위가 높습니다. Azure Portal의 **모듈 설정** 기능 또는 Visual Studio Code의 **단일 장치 기능에 대 한 배포 만들기** 는 잠시 적용 됩니다. 정의한 모듈이 장치에서 시작 되는 것을 볼 수 있습니다. 그러면 자동 배포의 우선 순위가 자동으로 시작 되어 장치의 desired 속성을 덮어씁니다.

**해결 방법:**

장치 마다 한 가지 유형의 배포 메커니즘 (자동 배포 또는 개별 장치 배포)만 사용 합니다. 장치를 대상으로 하는 여러 자동 배포를 사용 하는 경우 지정 된 장치에 올바른 항목을 적용할 수 있도록 우선 순위 또는 대상 설명을 변경할 수 있습니다. 자동 배포의 대상 설명과 더 이상 일치 하지 않도록 장치 쌍을 업데이트할 수도 있습니다.

자세한 내용은 [단일 장치에 대 한 자동 배포의 이해 또는 규모에 IoT Edge](module-deployment-monitoring.md)을 참조 하세요.

## <a name="next-steps"></a>다음 단계

IoT Edge 플랫폼에서 버그를 찾았나요? 지속적인 제품 개선을 위해 [문제를 제출](https://github.com/Azure/iotedge/issues)하세요.

추가 질문이 있으면 [지원 요청](https://portal.azure.com/#create/Microsoft.Support)을 만들어 도움을 받으세요.
