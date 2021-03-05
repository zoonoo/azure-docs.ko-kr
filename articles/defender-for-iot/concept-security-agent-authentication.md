---
title: 보안 에이전트 인증
titleSuffix: Azure Defender for IoT
description: 가능한 두 가지 방법을 사용 하 여 마이크로 에이전트 인증을 수행 합니다.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 1/20/2021
ms.topic: conceptual
ms.service: azure
ms.openlocfilehash: b0304bd191626adb71041fb0561862b988ee25cd
ms.sourcegitcommit: dac05f662ac353c1c7c5294399fca2a99b4f89c8
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102124587"
---
# <a name="micro-agent-authentication-methods"></a>마이크로 에이전트 인증 방법 

IoT 마이크로 에이전트의 Defender 인증에는 두 가지 옵션이 있습니다. 

- 연결 문자열 

- 인증서 

## <a name="authentication-using-a-connection-string"></a>연결 문자열을 사용 하 여 인증 

연결 문자열을 사용 하려면 명명 된 파일의 defender 에이전트 디렉터리에서 u t f-8로 인코딩된 연결 문자열을 사용 하는 파일을 추가 해야 `connection_string.txt` 합니다. 예를 들면 다음과 같습니다.

```azurecli
echo “<connection string>” > connection_string.txt 
```

이 작업을 완료 한 후에는이 명령을 사용 하 여 서비스를 다시 시작 해야 합니다.

```azurecli
sudo systemctl restart defender-iot-micro-agent.service
``` 

## <a name="authentication-using-a-certificate"></a>인증서를 사용 하 여 인증 


인증서를 사용 하 여 인증을 수행 하려면: 

1. 인증서의 PEM 인코딩 공개 부분을 이라는 파일의 defender 에이전트 디렉터리에 넣습니다 `certificate_public.pem` .
1. PEM으로 인코딩된 개인 키를 defender 에이전트 디렉터리에 이라는 파일에 저장 합니다 `certificate_private.pem` .
1. 이라는 파일에 적절 한 연결 문자열을 저장 `connection_string.txt` 합니다. 예를 들면 다음과 같습니다.

    ```azurecli
    HostName=<the host name of the iot hub>;DeviceId=<the id of the device>;ModuleId=<the id of the module>;x509=true 
    ```

    이 작업은 defender 에이전트가 인증을 위해 인증서를 제공할 것으로 간주 함을 나타냅니다. 

1. 다음 코드를 사용 하 여 서비스를 다시 시작 합니다. 

    ```azurecli
    sudo systemctl restart defender-iot-micro-agent.service 
    ```

## <a name="ensure-the-micro-agent-is-running-correctly"></a>마이크로 에이전트가 제대로 실행 되 고 있는지 확인 합니다. 

1. 다음 명령 실행: 
    ```azurecli
    systemctl status defender-iot-micro-agent.service 
    ```
1. 서비스가 **활성** 상태이 고 프로세스의 작동 시간이 적절 한지 확인 하 여 서비스가 안정적인 지 확인 합니다. 

    :::image type="content" source="media/concept-security-agent-authentication/active.png" alt-text="서비스가 활성 상태 인지 확인 하 여 안정적인 지 확인 합니다.":::

## <a name="next-steps"></a>다음 단계

보안 상태 [– CIS 벤치 마크](concept-security-posture.md)를 확인 합니다.
