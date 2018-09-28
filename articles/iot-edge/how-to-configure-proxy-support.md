---
title: 네트워크 프록시용 Azure IoT Edge 장치 구성 | Microsoft Docs
description: 프록시 서버를 통해 통신하도록 Azure IoT Edge 런타임과 인터넷 연결 IoT Edge 모듈을 구성하는 방법을 설명합니다.
author: kgremban
manager: ''
ms.author: kgremban
ms.date: 09/13/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: cf8f6197c65b0169e2bc61f46ab4a22f212512a6
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46996726"
---
# <a name="configure-an-iot-edge-device-to-communicate-through-a-proxy-server"></a>프록시 서버를 통해 통신하도록 IoT Edge 장치 구성

IoT Edge 장치는 HTTPS 요청을 전송하여 IoT Hub와 통신합니다. 장치가 프록시 서버를 사용하는 네트워크에 연결되어 있는 경우에는 해당 서버를 통해 통신하도록 IoT Edge 런타임을 구성해야 합니다. 또한 프록시 서버는 Edge 허브를 통해 라우팅되지 않는 HTTP 또는 HTTPS 요청을 수행하는 개별 IoT Edge 모듈에도 영향을 줄 수 있습니다. 

프록시 서버를 사용하도록 IoT Edge 장치를 구성할 때는 다음과 같은 기본적인 단계를 수행합니다. 

1. 장치의 Docker 디먼과 IoT Edge 디먼이 프록시 서버를 사용하도록 구성합니다.
2. 장치의 config.yaml 파일에서 edgeAgent 속성을 구성합니다.
3. 배포 매니페스트에서 IoT Edge 런타임 및 기타 IoT Edge 모듈용 환경 변수를 설정합니다. 

## <a name="configure-the-daemons"></a>디먼 구성

IoT Edge 장치에서 실행되는 Docker 및 IoT Edge 디먼이 프록시 서버를 사용하도록 구성해야 합니다. Docker 디먼은 웹 요청을 수행하여 컨테이너 레지스트리에서 컨테이너 이미지를 끌어옵니다. IoT Edge 디먼은 웹 요청을 전송하여 IoT Hub와 통신합니다.

### <a name="docker-daemon"></a>Docker 디먼

환경 변수를 사용하여 Docker 디먼을 구성하려면 Docker 설명서를 참조하세요. DockerHub 및 Azure Container Registry를 비롯한 대부분의 컨테이너 레지스트리는 HTTPS 요청을 지원하므로 **HTTPS_PROXY** 변수를 설정해야 합니다. TLS(전송 계층 보안)를 지원하지 않는 레지스트리에서 이미지를 끌어오는 경우에는 **HTTP_PROXY**를 설정해야 할 수 있습니다. 

사용 중인 Docker 버전에 적용되는 문서를 선택하세요. 

* [Docker](https://docs.docker.com/config/daemon/systemd/#httphttps-proxy)
* [Windows용 Docker](https://docs.docker.com/docker-for-windows/#proxies)

### <a name="iot-edge-daemon"></a>IoT Edge 디먼

IoT Edge 디먼은 Docker 디먼과 비슷한 방식으로 구성됩니다. IoT Edge가 IoT Hub로 보내는 모든 요청은 HTTPS를 사용합니다. 사용 중인 운영 체제에 따라 다음 단계를 수행하여 서비스에 대한 환경 변수를 설정합니다. 

#### <a name="linux"></a>Linux

터미널에서 편집기를 열어 IoT Edge 디먼을 구성합니다. 

```bash
sudo systemctl edit iotedge
```

아래 텍스트를 입력합니다. **\<proxy URL>** 은 실제 프록시 서버 주소와 포트로 바꾸세요. 그런 다음 입력 내용을 저장하고 편집기를 종료합니다. 

```text
[Service]
Environment="https_proxy=<proxy URL>"
```

서비스 관리자를 새로 고쳐 iotedge의 새 구성을 선택합니다.

```bash
sudo systemctl daemon-reload
```

IoT Edge를 다시 시작하여 변경 내용을 적용합니다.

```bash
sudo systemctl restart iotedge
```

환경 변수가 생성되었으며 새 구성이 로드되었는지 확인합니다. 

```bash
systemctl show --property=Environment iotedge
```

#### <a name="windows"></a>Windows

관리자 권한으로 PowerShell 창을 연 후에 다음 명령을 실행해 새 환경 변수를 사용하여 레지스트리를 편집합니다. **\<proxy url>** 은 실제 프록시 서버 주소와 포트로 바꾸세요. 

```powershell
reg add HKLM\SYSTEM\CurrentControlSet\Services\iotedge /v Environment /t REG_MULTI_SZ /d https_proxy=<proxy URL>
```

IoT Edge를 다시 시작하여 변경 내용을 적용합니다.

```powershell
Restart-Service iotedge
```

## <a name="configure-the-edge-agent"></a>Edge 에이전트 구성

Edge 에이전트는 모든 IoT Edge 장치에서 처음으로 시작되는 모듈로, IoT Edge config.yaml 파일의 정보에 따라 처음으로 시작됩니다. 그런 후에 IoT Hub에 연결하여 배포 매니페스트를 검색합니다. 배포 매니페스트는 장치에서 배포해야 하는 다른 모듈을 선언합니다.

IoT Edge 장치에서 config.yaml 파일을 엽니다. Linux 시스템에서 이 파일의 위치는 **/etc/iotedge/config.yaml**입니다. Windows 시스템에서 이 파일의 위치는 **C:\ProgramData\iotedge\config.yaml**입니다. 구성 파일은 보호되어 있으므로 관리 권한이 있어야 액세스할 수 있습니다. Linux 시스템에서는 원하는 텍스트 편집기에서 파일을 열기 전에 `sudo` 명령을 사용해야 합니다. Windows에서는 메모장 등의 텍스트 편집기를 관리자로 실행하도록 연 다음 파일을 열어야 합니다. 

config.yaml 파일에서 **Edge Agent module spec** 섹션을 찾습니다. Edge 에이전트 정의에는 **env** 매개 변수가 포함되어 있으며, 이 매개 변수에 환경 변수를 추가할 수 있습니다. 

![edgeAgent 정의](./media/how-to-configure-proxy-support/edgeagent-unedited.png)

env 매개 변수의 자리 표시자인 중괄호를 제거하고 새 줄에 새 변수를 추가합니다. YAML에서 들여쓰기를 하려면 공백 두 개를 추가해야 합니다. 

```yaml
https_proxy: "<proxy URL>"
```

IoT Edge 런타임은 기본적으로 AMQP를 사용하여 IoT Hub와 통신합니다. 그런데 일부 프록시 서버는 AMQP 포트를 차단합니다. 이 경우에는 WebSocket을 통해 AMQP를 사용하도록 edgeAgent도 구성해야 합니다. 이렇게 하려면 두 번째 환경 변수를 추가합니다.

```yaml
UpstreamProtocol: "AmqpWs"
```

![환경 변수가 포함된 edgeAgent 정의](./media/how-to-configure-proxy-support/edgeagent-edited.png)

config.yaml의 변경 내용을 저장하고 편집기를 닫습니다. IoT Edge를 다시 시작하여 변경 내용을 적용합니다. 

* Linux: 

   ```bash
   sudo systemctl restart iotedge
   ```

* Windows:

   ```powershell
   Restart-Service iotedge
   ```

## <a name="configure-deployment-manifests"></a>배포 매니페스트 구성  

IoT Edge 장치가 프록시 서버를 사용하도록 구성한 후에는 이후의 모든 배포 매니페스트에서도 환경 변수를 선언해야 합니다. IoT Hub와 계속 통신하려면 edgeAgent와 edgeHub의 두 런타임 모듈에는 항상 프록시 서버가 구성되어 있어야 합니다. 모든 IoT Edge 모듈이 프록시 서버를 통해 통신하도록 구성할 수는 있지만, edgeHub를 통해 메시지를 라우팅하는 모듈이나 장치의 다른 모듈과만 통신하는 모듈의 경우에는 이렇게 구성할 필요가 없습니다. 

Azure Portal을 사용하거나 JSON 파일을 직접 편집하여 배포 매니페스트를 만들 수 있습니다. 

### <a name="azure-portal"></a>Azure portal

**모듈 설정** 마법사를 사용하여 IoT Edge 장치의 배포를 만들 때는 모든 모듈에 **환경 변수** 섹션에 있으므로 이 섹션을 통해 프록시 서버 연결을 구성할 수 있습니다. 

Edge 에이전트 및 Edge 허브 모듈을 구성하려면 마법사의 첫 단계에서 **고급 Edge 런타임 설정 구성**을 선택합니다. 

![고급 Edge 런타임 설정 구성](./media/how-to-configure-proxy-support/configure-runtime.png)

Edge 에이전트 및 Edge 허브 모듈 정의에 모두 **https_proxy** 환경 변수를 추가합니다. IoT Edge 장치의 config.yaml 파일에 **UpstreamProtocol** 환경 변수를 포함한 경우에는 Edge 에이전트 모듈 정의에도 해당 환경 변수를 포함합니다. 

![환경 변수 설정](./media/how-to-configure-proxy-support/edgehub-environmentvar.png)

배포 매니페스트에 추가하는 기타 모든 모듈은 같은 패턴을 따릅니다. 모듈 이름 및 이미지를 설정하는 페이지에는 환경 변수 섹션이 있습니다.

### <a name="json-deployment-manifest-files"></a>JSON 배포 매니페스트 파일

Visual Studio Code에서 템플릿을 사용하거나 JSON 파일을 직접 만들어 IoT Edge 장치용 배포를 만든 경우에는 각 모듈 정의에 환경 변수를 직접 추가할 수 있습니다. 

다음 JSON 형식을 사용합니다. 

```json
"env": {
    "https_proxy": {
        "value": "<proxy URL>"
    }
}
```

환경 변수가 포함된 모듈 정의는 다음 edgeHub 예제와 같이 표시됩니다.

```json
"edgeHub": {
    "type": "docker",
    "settings": {
        "image": "mcr.microsoft.com/azureiotedge-hub:1.0",
        "createOptions": ""
    },
    "env": {
        "https_proxy": {
            "value": "https://proxy.example.com:3128"
        }
    },
    "status": "running",
    "restartPolicy": "always"
}
```

IoT Edge 장치의 config.yaml 파일에 **UpstreamProtocol** 환경 변수를 포함한 경우에는 Edge 에이전트 모듈 정의에도 해당 환경 변수를 포함합니다. 

```json
"env": {
    "https_proxy": {
        "value": "<proxy URL"
    },
    "UpstreamProtocol": {
        "value": "AmqpWs"
    }
}
```

## <a name="next-steps"></a>다음 단계

[IoT Edge 런타임](iot-edge-runtime.md)의 역할에 대해 자세히 알아봅니다.

[Azure IoT Edge와 관련된 일반적인 문제 및 해결 방법](troubleshoot.md)을 사용하여 설치 및 구성 오류가 발생하는 문제를 해결합니다.

