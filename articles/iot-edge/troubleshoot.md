---
title: 문제 해결 - Azure IoT Edge | Microsoft Docs
description: 이 문서를 사용 하 여 구성 요소 상태 및 로그 검색과 같은 Azure IoT Edge에 대 한 표준 진단 기술을 알아봅니다.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 04/27/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 0e4ec7127df288ec1818df307da1ea9824141309
ms.sourcegitcommit: 4e5560887b8f10539d7564eedaff4316adb27e2c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/06/2020
ms.locfileid: "87902459"
---
# <a name="troubleshoot-your-iot-edge-device"></a>IoT Edge 장치 문제 해결

사용자 환경에서 Azure IoT Edge를 실행 하는 데 문제가 발생 하는 경우이 문서를 문제 해결 및 진단에 대 한 가이드로 사용 합니다.

## <a name="run-the-check-command"></a>' Check ' 명령을 실행 합니다.

IoT Edge 문제를 해결 하는 첫 번째 단계는 `check` 일반적인 문제에 대 한 구성 및 연결 테스트 컬렉션을 실행 하는 명령을 사용 하는 것입니다. `check`이 명령은 [release 1.0.7](https://github.com/Azure/azure-iotedge/releases/tag/1.0.7) 이상에서 사용할 수 있습니다.

>[!NOTE]
>IoT Edge 장치가 프록시 서버 뒤에 있는 경우 문제 해결 도구에서 연결 확인을 실행할 수 없습니다.

`check`다음과 같이 명령을 실행 하거나 플래그를 포함 `--help` 하 여 전체 옵션 목록을 볼 수 있습니다.

Linux에서:

```bash
sudo iotedge check
```

Windows에서:

```powershell
iotedge check
```

문제 해결 도구는 다음과 같은 세 가지 범주로 정렬 된 많은 검사를 실행 합니다.

* *구성 검사* 에서는 구성 *. yaml* 및 컨테이너 엔진과 관련 된 문제를 포함 하 여 IoT Edge 장치가 클라우드에 연결 되지 못하게 하는 세부 정보를 검사 합니다.
* *연결 확인* 은 IoT Edge 런타임이 호스트 장치의 포트에 액세스할 수 있고 모든 IoT Edge 구성 요소가 IoT Hub에 연결할 수 있는지 확인 합니다. IoT Edge 장치가 프록시 뒤에 있는 경우이 검사 집합은 오류를 반환 합니다.
* *프로덕션 준비 검사* 는 장치 인증 기관 (CA) 인증서 및 모듈 로그 파일 구성의 상태와 같은 권장 프로덕션 모범 사례를 확인 합니다.

오류 또는 경고가 발생 하는 경우 수행할 작업을 포함 하 여이 도구를 실행 하는 각 진단 검사에 대 한 자세한 내용은 [IoT Edge 문제 해결 확인](https://github.com/Azure/iotedge/blob/master/doc/troubleshoot-checks.md)을 참조 하세요.

## <a name="gather-debug-information-with-support-bundle-command"></a>' Support-번들 ' 명령을 사용 하 여 디버그 정보 수집

IoT Edge 장치에서 로그를 수집 해야 하는 경우 가장 편리한 방법은 명령을 사용 하는 것입니다 `support-bundle` . 기본적으로이 명령은 모듈, IoT Edge security manager 및 컨테이너 엔진 로그, `iotedge check` JSON 출력 및 기타 유용한 디버그 정보를 수집 합니다. 쉽게 공유할 수 있도록 단일 파일로 압축 합니다. `support-bundle`이 명령은 [release 1.0.9](https://github.com/Azure/azure-iotedge/releases/tag/1.0.9) 이상에서 사용할 수 있습니다.

플래그를 사용 하 여 명령을 실행 하 여 `support-bundle` `--since` 과거부터 로그를 가져올 기간을 지정 합니다. 예를 들어 지난 6 일 이후 지난 6 일 이후 지난 6 시간 후에 `6h` 로그를 가져올 수 `6d` `6m` 있습니다. 플래그를 포함 `--help` 하 여 전체 옵션 목록을 표시 합니다.

Linux에서:

```bash
sudo iotedge support-bundle --since 6h
```

Windows에서:

```powershell
iotedge support-bundle --since 6h
```

> [!WARNING]
> 명령의 출력에는 `support-bundle` 호스트, 장치 및 모듈 이름, 모듈에 의해 기록 된 정보 등이 포함 될 수 있습니다. 공개 포럼의 출력을 공유 하는 경우이 점에 유의 하세요.

## <a name="check-your-iot-edge-version"></a>IoT Edge 버전 확인

이전 버전의 IoT Edge를 실행 하는 경우 업그레이드 하면 문제가 해결 될 수 있습니다. `iotedge check`이 도구는 IoT Edge 보안 데몬이 최신 버전 인지 확인 하지만 IoT Edge 허브 및 에이전트 모듈의 버전을 확인 하지는 않습니다. 장치에서 런타임 모듈의 버전을 확인 하려면 및 명령을 사용 `iotedge logs edgeAgent` `iotedge logs edgeHub` 합니다. 버전 번호는 모듈이 시작 될 때 로그에 선언 됩니다.

장치를 업데이트 하는 방법에 대 한 지침은 [IoT Edge 보안 디먼 및 런타임 업데이트](how-to-update-iot-edge.md)를 참조 하세요.

## <a name="check-the-status-of-the-iot-edge-security-manager-and-its-logs"></a>IoT Edge 보안 관리자 및 해당 로그의 상태를 확인 합니다.

[IoT Edge 보안 관리자](iot-edge-security-manager.md) 는 시작 및 프로 비전 장치에서 IoT Edge 시스템을 초기화 하는 등의 작업을 담당 합니다. IoT Edge를 시작 하지 않는 경우 보안 관리자 로그에서 유용한 정보를 제공할 수 있습니다.

Linux에서:

* IoT Edge 보안 관리자의 상태를 확인 합니다.

   ```bash
   sudo systemctl status iotedge
   ```

* IoT Edge 보안 관리자의 로그를 확인 합니다.

    ```bash
    sudo journalctl -u iotedge -f
    ```

* IoT Edge security manager의 자세한 로그를 확인 합니다.

  * IoT Edge 디먼 설정을 편집 합니다.

      ```bash
      sudo systemctl edit iotedge.service
      ```

  * 다음 줄을 업데이트합니다.

      ```bash
      [Service]
      Environment=IOTEDGE_LOG=edgelet=debug
      ```

  * IoT Edge 보안 디먼을 다시 시작합니다.

      ```bash
      sudo systemctl cat iotedge.service
      sudo systemctl daemon-reload
      sudo systemctl restart iotedge
      ```

Windows에서:

* IoT Edge 보안 관리자의 상태를 확인 합니다.

   ```powershell
   Get-Service iotedge
   ```

* IoT Edge 보안 관리자의 로그를 확인 합니다.

   ```powershell
   . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
   ```

* IoT Edge security manager 로그의 최근 5 분만 표시:

   ```powershell
   . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog -StartTime ([datetime]::Now.AddMinutes(-5))
   ```

* IoT Edge security manager의 자세한 로그를 확인 합니다.

  * 시스템 수준 환경 변수를 추가 합니다.

      ```powershell
      [Environment]::SetEnvironmentVariable("IOTEDGE_LOG", "debug", [EnvironmentVariableTarget]::Machine)
      ```

  * IoT Edge 보안 디먼을 다시 시작합니다.

      ```powershell
      Restart-Service iotedge
      ```

### <a name="if-the-iot-edge-security-manager-is-not-running-verify-your-yaml-configuration-file"></a>IoT Edge security manager가 실행 되 고 있지 않은 경우 yaml 구성 파일을 확인 합니다.

> [!WARNING]
> YAML 파일은 탭을 들여쓰기로 포함할 수 없습니다. 2 공백을 대신 사용합니다. 최상위 요소에는 선행 공백이 없어야 합니다.

Linux에서:

   ```bash
   sudo nano /etc/iotedge/config.yaml
   ```

Windows에서:

   ```cmd
   notepad C:\ProgramData\iotedge\config.yaml
   ```

### <a name="restart-the-iot-edge-security-manager"></a>IoT Edge 보안 관리자를 다시 시작합니다.

문제가 여전히 지속되는 경우 IoT Edge 보안 관리자를 다시 시작할 수 있습니다.

Linux에서:

   ```cmd
   sudo systemctl restart iotedge
   ```

Windows에서:

   ```powershell
   Stop-Service iotedge -NoWait
   sleep 5
   Start-Service iotedge
   ```

## <a name="check-container-logs-for-issues"></a>문제에 대한 컨테이너 로그 확인

IoT Edge 보안 디먼이 실행 되 면 컨테이너의 로그를 확인 하 여 문제를 검색 합니다. 배포 된 컨테이너에서 시작 하 고 IoT Edge 런타임을 구성 하는 컨테이너 (edgeAgent 및 edgeHub)를 확인 합니다. IoT Edge 에이전트 로그는 일반적으로 각 컨테이너의 수명 주기에 대 한 정보를 제공 합니다. IoT Edge 허브 로그는 메시징 및 라우팅에 대 한 정보를 제공 합니다.

```cmd
iotedge logs <container name>
```

## <a name="view-the-messages-going-through-the-iot-edge-hub"></a>IoT Edge 허브를 통과 하는 메시지 보기

IoT Edge 허브를 통과 하는 메시지를 보고 런타임 컨테이너의 자세한 로그에서 정보를 수집할 수 있습니다. 이러한 컨테이너에서 자세한 로그를 켜려면 yaml 구성 파일에서 `RuntimeLogLevel`을 설정합니다. 파일을 열려면:

Linux에서:

   ```bash
   sudo nano /etc/iotedge/config.yaml
   ```

Windows에서:

   ```cmd
   notepad C:\ProgramData\iotedge\config.yaml
   ```

기본적으로 `agent` 요소는 다음 예제와 같습니다.

   ```yaml
   agent:
     name: edgeAgent
     type: docker
     env: {}
     config:
       image: mcr.microsoft.com/azureiotedge-agent:1.0
       auth: {}
   ```

`env: {}`을 다음으로 바꿉니다.

   ```yaml
   env:
     RuntimeLogLevel: debug
   ```

   > [!WARNING]
   > YAML 파일은 들여쓰기로 탭을 포함할 수 없습니다. 2 공백을 대신 사용합니다. 최상위 항목에는 선행 공백이 있을 수 없습니다.

파일을 저장하고 IoT Edge 보안 관리자를 다시 시작합니다.

IoT Hub 및 IoT Edge 디바이스 간에 전송되는 메시지를 확인할 수도 있습니다. [Visual Studio Code에 대 한 Azure IoT Hub 확장](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit)을 사용 하 여 이러한 메시지를 확인 합니다. 자세한 내용은 [Azure IoT로 개발할 때 사용할 수 있는 편리한 도구](https://blogs.msdn.microsoft.com/iotdev/2017/09/01/handy-tool-when-you-develop-with-azure-iot/)를 참조하세요.

## <a name="restart-containers"></a>컨테이너 다시 시작

로그 및 메시지에서 정보를 검토한 후에는 컨테이너를 다시 시작할 수 있습니다.

```cmd
iotedge restart <container name>
```

IoT Edge 런타임 컨테이너를 다시 시작합니다.

```cmd
iotedge restart edgeAgent && iotedge restart edgeHub
```

## <a name="check-your-firewall-and-port-configuration-rules"></a>방화벽 및 포트 구성 규칙 확인

지원 되는 IoT Hub 프로토콜을 사용 하 여 온-프레미스 서버에서 Azure 클라우드로의 통신을 허용 Azure IoT Edge [통신 프로토콜 선택](../iot-hub/iot-hub-devguide-protocols.md)을 참조 하세요. 보안 향상된을 위해 Azure IoT Edge 및 Azure IoT Hub 간의 통신 채널은 항상 아웃바운드되도록 구성됩니다. 이 구성은 [서비스 보조 통신 패턴](https://blogs.msdn.microsoft.com/clemensv/2014/02/09/service-assisted-communication-for-connected-devices/)을 기반으로 합니다. 그러면 탐색할 악의적인 엔터티에 대한 공격 노출 영역을 최소화합니다. 인바운드 통신은 Azure IoT Hub에서 메시지를 Azure IoT Edge 디바이스에 푸시해야 하는 특정 시나리오에만 필요합니다. 클라우드-디바이스 메시지는 보안 TLS 채널을 사용하여 보호되고 X.509 인증서 및 TPM 디바이스 모듈을 사용하여 추가로 보호될 수 있습니다. Azure IoT Edge 보안 관리자는 이 통신을 설정하는 방법을 관리합니다. [IoT Edge 보안 관리자](../iot-edge/iot-edge-security-manager.md)를 참조하세요.

IoT Edge는 Azure IoT Edge 런타임 및 배포된 모듈을 보호하기 위해 향상된 구성을 제공하지만, 기본 컴퓨터 및 네트워크 구성에 여전히 종속됩니다. 따라서 적절 한 네트워크 및 방화벽 규칙이 안전한 edge에서 클라우드 통신에 대해 설정 되었는지 확인 해야 합니다. 다음 표는 Azure IoT Edge 런타임이 호스트 되는 기본 서버에 대 한 구성 방화벽 규칙을 사용 하는 경우 지침으로 사용할 수 있습니다.

|프로토콜|포트|수신|나가는 포트|지침|
|--|--|--|--|--|
|MQTT|8883|BLOCKED(기본값)|BLOCKED(기본값)|<ul> <li>통신 프로토콜로 MQTT를 사용하는 경우 발신(아웃바운드)이 Open이 되도록 구성합니다.<li>MQTT에 대한 1883은 IoT Edge에서 지원되지 않습니다. <li>수신(인바운드) 연결을 차단해야 합니다.</ul>|
|AMQP|5671|BLOCKED(기본값)|OPEN(기본값)|<ul> <li>IoT Edge의 기본 통신 프로토콜입니다. <li> Azure IoT Edge는 지원되는 다른 프로토콜에 대해 구성되지 않았거나 AMQP가 원하는 통신 프로토콜인 경우 Open으로 구성해야 합니다.<li>AMQP에 대한 5672는 IoT Edge에서 지원되지 않습니다.<li>Azure IoT Edge가 다른 IoT Hub 지원 프로토콜을 사용하는 경우 이 포트를 차단합니다.<li>수신(인바운드) 연결을 차단해야 합니다.</ul></ul>|
|HTTPS|443|BLOCKED(기본값)|OPEN(기본값)|<ul> <li>IoT Edge 프로비전을 위해 443에서 발신(아웃바운드)을 보내는 열기로 구성합니다. 수동 스크립트 또는 Azure IoT DPS(디바이스 프로비저닝 서비스)를 사용하는 경우 이 구성이 필요합니다. <li>다음과 같은 특정 시나리오의 경우에만 수신(인바운드) 연결이 Open 상태여야 합니다. <ul> <li>  메서드 요청을 보낼 수 있는 리프 디바이스에 대한 투명 게이트웨이가 있는 경우. 이 경우 IoTHub에 연결하거나 Azure IoT Edge를 통해 IoTHub 서비스를 제공하기 위해 포트 443을 외부 네트워크로 열어둘 필요가 없습니다. 따라서 수신 규칙은 내부 네트워크에서의 수신(인바운드)만 열도록 제한될 수 있습니다. <li> 클라이언트-디바이스(C2D) 시나리오의 경우</ul><li>HTTP에 대한 80은 IoT Edge에서 지원되지 않습니다.<li>비 HTTP 프로토콜(예: AMQP 또는 MQTT)을 엔터프라이즈에서 구성할 수 없는 경우 WebSockets을 통해 메시지를 보낼 수 있습니다. 이 경우 WebSocket 통신에 포트 443이 사용됩니다.</ul>|

## <a name="next-steps"></a>다음 단계

IoT Edge 플랫폼에서 버그를 찾았나요? 지속적인 제품 개선을 위해 [문제를 제출](https://github.com/Azure/iotedge/issues)하세요.

추가 질문이 있으면 [지원 요청](https://portal.azure.com/#create/Microsoft.Support)을 만들어 도움을 받으세요.
