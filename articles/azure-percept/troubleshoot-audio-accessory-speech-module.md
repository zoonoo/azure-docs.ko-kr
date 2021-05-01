---
title: Azure Percept Audio 및 음성 모듈 관련 문제 해결
description: Azure Percept Audio 및 azureearspeechclientmodule에 대한 문제 해결 팁 가져오기
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: how-to
ms.date: 03/25/2021
ms.custom: template-how-to
ms.openlocfilehash: c4fc7d7564ecd30326fbec832639b2a81d55e6d5
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105605657"
---
# <a name="azure-percept-audio-and-speech-module-troubleshooting"></a>Azure Percept Audio 및 음성 모듈 문제 해결

음성 도우미 애플리케이션 문제를 해결하려면 아래 지침을 따르십시오.

## <a name="collecting-speech-module-logs"></a>음성 모듈 로그 수집

해당 명령을 실행하려면 [개발자 키트에 SSH](./how-to-ssh-into-percept-dk.md)를 실행하고 SSH 클라이언트 프롬프트에 명령을 입력합니다.

다음과 같이 음성 모듈 로그를 수집합니다.

```console
sudo iotedge logs azureearspeechclientmodule
```

추가 분석을 위해 출력을 .txt 파일로 리디렉션하려면 다음 구문을 사용합니다.

```console
sudo [command] > [file name].txt
```

복사할 수 있도록 .txt 파일의 사용 권한을 변경합니다.

```console
sudo chmod 666 [file name].txt
```

.txt 파일에 출력을 리디렉션하는 경우 SCP를 통해 호스트 PC에 파일을 복사합니다.

```console
scp [remote username]@[IP address]:[remote file path]/[file name].txt [local host file path]
```

[로컬 호스트 파일 경로]는 .txt 파일을 복사할 호스트 PC의 위치를 나타냅니다. [remote username]은 [설정 환경](./quickstart-percept-dk-set-up.md)에서 선택된 SSH 사용자 이름입니다.

## <a name="checking-runtime-status-of-the-speech-module"></a>음성 모듈의 런타임 상태를 확인하는 중

**azureearspeechclientmodule** 의 런타임 상태가 **실행 중** 으로 표시되는지 확인합니다. 디바이스 모듈의 런타임 상태를 찾으려면 [Azure Portal](https://portal.azure.com/)을 열고 **모든 리소스** ->  **[사용자의 IoT 허브]**  -> **IoT Edge** ->  **[사용자의 디바이스 ID]** 로 이동합니다. **모듈** 탭을 클릭하여 설치된 모든 모듈의 런타임 상태를 확인합니다.

:::image type="content" source="./media/troubleshoot-audio-accessory-speech-module/over-the-air-iot-edge-device-page.png" alt-text="Azure Portal의 에지 디바이스 페이지.":::

**azureearspeechclientmodule** 의 런타임 상태가 **실행 중** 으로 표시되지 않으면  -> **azureearspeechclientmodule** **모듈 설정** 을 클릭합니다. **모듈 설정** 페이지에서 **원하는 상태** 를 **실행 중** 으로 설정하고 **업데이트** 를 클릭합니다.

## <a name="understanding-ear-som-led-indicators"></a>Ear SoM LED 표시기 이해

LED 표시기를 사용하여 디바이스의 상태를 파악할 수 있습니다. 일반적으로 디바이스 전원이 켜진 후 모듈을 완전히 초기화하는 데 2분 정도 걸립니다. 초기화 단계를 진행하는 동안 다음을 확인할 수 있습니다.

1. 가운데 흰색 LED 켜짐(정적): 디바이스의 전원이 켜져 있습니다.
2. 가운데 흰색 LED 켜짐(깜박임): 인증이 진행 중입니다.
3. 디바이스가 인증되고 사용할 준비가 되면 세 LED 모두 파란색으로 변경됩니다.

|LED|LED 상태|Ear SoM 상태|
|---|---------|--------------|
|L02|1x 흰색, 정적 켜짐|전원 켜기 |
|L02|1x 흰색, 0.5Hz 깜박임|인증 진행 중 |
|L01 & L02 & L03|3x 파랑, 정적 켜짐|키워드를 기다리는 중|
|L01 & L02 & L03|LED 배열 깜박임, 20fps |수신 또는 말하기|
|L01 & L02 & L03|LED 배열 레이싱, 20fps|생각|
|L01 & L02 & L03|3x 빨강, 고정 켜짐 |음소거|

## <a name="next-steps"></a>다음 단계

Azure Percept DK 관련 문제 해결에 대한 자세한 내용은 [일반적인 문제 해결 가이드](./troubleshoot-dev-kit.md)를 참조하세요.
