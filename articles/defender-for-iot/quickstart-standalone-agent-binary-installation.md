---
title: IoT 마이크로 에이전트용 Defender 설치(미리 보기)
titleSuffix: Azure Defender for IoT
description: Defender 마이크로 에이전트를 설치하고 인증하는 방법을 알아봅니다.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 1/18/2021
ms.topic: quickstart
ms.service: azure
ms.openlocfilehash: 8d7e6dffcc40ba1e34a4a84ecccccc1f8b181393
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101703051"
---
# <a name="install-defender-for-iot-micro-agent-preview"></a>IoT 마이크로 에이전트용 Defender 설치(미리 보기)

이 문서에서는 Defender 마이크로 에이전트를 설치하고 인증하는 방법에 대해 설명합니다.

## <a name="prerequisites"></a>사전 요구 사항

Defender for IoT 모듈을 설치하기 전에 IoT Hub에서 모듈 ID를 만들어야 합니다. 모듈 ID를 만드는 방법에 대한 자세한 내용은 [azureiotsecurity 모듈 쌍 만들기](quickstart-create-security-twin.md)를 참조하세요.

## <a name="install-the-package"></a>패키지 설치

[이 지침](/windows-server/administration/linux-package-repository-for-microsoft-software)에 따라 Microsoft 패키지 리포지토리를 설치하고 구성합니다. 

Debian 9의 경우 추가해야 하는 리포지토리가 지침에 나와 있지 않으므로 다음 명령을 사용하여 리포지토리를 추가하세요. 

```azurecli
curl -sSL https://packages.microsoft.com/keys/microsoft.asc | sudo apt-key add - 

sudo apt-get install software-properties-common

sudo apt-add-repository https://packages.microsoft.com/debian/9/multiarch/prod

sudo apt-get update
```

Debian 및 Ubuntu 기반 Linux 배포판에 Defender 마이크로 에이전트 패키지를 설치하려면 다음 명령을 사용합니다.

```azurecli
sudo apt-get install defender-iot-micro-agent 
```

## <a name="micro-agent-authentication-methods"></a>마이크로 에이전트 인증 방법 

Defender for IoT 마이크로 에이전트를 인증하는 데 사용 되는 두 가지 옵션은 다음과 같습니다. 

- 연결 문자열. 

- 인증서

### <a name="authenticate-using-a-connection-string"></a>연결 문자열을 사용하여 인증

연결 문자열을 사용하여 인증하려면 다음을 수행합니다.

1. 다음 명령을 입력하여 utf-8로 인코딩된 연결 문자열이 포함된 `connection_string.txt`라는 파일을 Defender 에이전트 디렉터리 `/var/defender_iot_micro_agent` 경로에 저장합니다.

    ```azurecli
    sudo bash -c 'echo "<connection string" > /var/defender_iot_micro_agent/connection_string.txt' 
    ```

    이제 `connection_string.txt`가 `/var/defender_iot_micro_agent/connection_string.txt` 경로 위치에 있어야 합니다.

1. 다음 명령을 사용하여 서비스를 다시 시작합니다.  

    ```azurecli
    sudo systemctl restart defender-iot-micro-agent.service 
    ```

### <a name="authenticate-using-a-certificate"></a>인증서를 사용하여 인증

인증서를 사용하여 인증하려면 다음을 수행합니다.

1. [이 지침](../iot-hub/iot-hub-security-x509-get-started.md)에 따라 인증서를 확보합니다.

1. 인증서 및 프라이빗 키의 PEM으로 인코딩된 공개 부분을 Defender 에이전트 디렉터리의 `certificate_public.pem` 및 `certificate_private.pem` 파일에 저장합니다. 

1. 적절한 연결 문자열을 `connection_string.txt` 파일에 저장합니다. 연결 문자열은 다음과 같아야 합니다. 

    `HostName=<the host name of the iot hub>;DeviceId=<the id of the device>;ModuleId=<the id of the module>;x509=true` 

    이 문자열은 인증을 위해 인증서가 제공되어야 함을 Defender 에이전트에 알립니다. 

1. 다음 명령을 사용하여 서비스를 다시 시작합니다.  

    ```azurecli
    sudo systemctl restart defender-iot-micro-agent.service
    ```

### <a name="validate-your-installation"></a>설치 유효성 검사

설치 유효성을 검사하려면 다음을 수행합니다.

1. 다음 명령을 사용하여 마이크로 에이전트가 제대로 실행 되고 있는지 확인합니다.  

    ```azurecli
    systemctl status defender-iot-micro-agent.service
    ```
1. 서비스가 `active`인지 확인하여 서비스가 안정적이고, 프로세스의 작동 시간이 적절한지 확인합니다.

    :::image type="content" source="media/quickstart-standalone-agent-binary-installation/active-running.png" alt-text="서비스가 안정적이며 활성 상태인지 확인합니다.":::
 
## <a name="testing-the-system-end-to-end"></a>엔드투엔드 시스템 테스트 

디바이스에 트리거 파일을 만들어 시스템을 끝에서 끝까지 테스트할 수 있습니다. 트리거 파일은 에이전트의 기준 검사에서 해당 파일을 기준 위반으로 감지하도록 합니다. 

다음 명령을 사용하여 파일 시스템에 파일을 만듭니다.

```azurecli
sudo touch /tmp/DefenderForIoTOSBaselineTrigger.txt 
```
허브에서 CIS-debian-9-DEFENDER_FOR_IOT_TEST_CHECKS-0.0의 `CceId`와 함께 기준 유효성 검사 실패 권장 사항이 발생합니다. 

:::image type="content" source="media/quickstart-standalone-agent-binary-installation/validation-failure.png" alt-text="허브에서 발생하는 기준 유효성 검사 실패 권장 사항입니다." lightbox="media/quickstart-standalone-agent-binary-installation/validation-failure-expanded.png":::

권장 사항이 허브에 표시되기까지 최대 1시간까지 걸릴 수 있습니다. 

## <a name="micro-agent-versioning"></a>마이크로 에이전트 버전 관리 

특정 버전의 Defender IoT 마이크로 에이전트를 설치하려면 다음 명령을 실행합니다. 

```azurecli
sudo apt-get install defender-iot-micro-agent=<version>
```

## <a name="next-steps"></a>다음 단계

[소스 코드에서 Defender 마이크로 에이전트 빌드](quickstart-building-the-defender-micro-agent-from-source.md)