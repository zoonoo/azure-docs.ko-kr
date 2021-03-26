---
title: Azure Percept 오디오 및 음성 모듈 관련 문제 해결
description: Azure Percept Audio 및 azureearspeechclientmodule에 대 한 문제 해결 팁 가져오기
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: how-to
ms.date: 03/25/2021
ms.custom: template-how-to
ms.openlocfilehash: c4fc7d7564ecd30326fbec832639b2a81d55e6d5
ms.sourcegitcommit: 73d80a95e28618f5dfd719647ff37a8ab157a668
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/26/2021
ms.locfileid: "105605657"
---
# <a name="azure-percept-audio-and-speech-module-troubleshooting"></a>Azure Percept 오디오 및 음성 모듈 문제 해결

음성 도우미 응용 프로그램 문제를 해결 하려면 아래 지침을 따르십시오.

## <a name="collecting-speech-module-logs"></a>음성 모듈 로그 수집

이러한 명령을 실행 하려면 [개발자 키트에 ssh](./how-to-ssh-into-percept-dk.md) 를 실행 하 고 ssh 클라이언트 프롬프트에 명령을 입력 합니다.

음성 모듈 로그 수집:

```console
sudo iotedge logs azureearspeechclientmodule
```

추가 분석을 위해 출력을 .txt 파일로 리디렉션하려면 다음 구문을 사용 합니다.

```console
sudo [command] > [file name].txt
```

복사할 수 있도록 .txt 파일의 사용 권한을 변경 합니다.

```console
sudo chmod 666 [file name].txt
```

.Txt 파일에 출력을 리디렉션하는 경우 SCP를 통해 호스트 PC에 파일을 복사 합니다.

```console
scp [remote username]@[IP address]:[remote file path]/[file name].txt [local host file path]
```

[로컬 호스트 파일 경로]는 .txt 파일을 복사할 호스트 PC의 위치를 나타냅니다. [remote username]은 [설치 환경](./quickstart-percept-dk-set-up.md)에서 선택 된 SSH 사용자 이름입니다.

## <a name="checking-runtime-status-of-the-speech-module"></a>음성 모듈의 런타임 상태를 확인 하는 중

**Azureearspeechclientmodule** 의 런타임 상태가 **실행 중** 으로 표시 되는지 확인 합니다. 장치 모듈의 런타임 상태를 찾으려면 [Azure Portal](https://portal.azure.com/) 열고 **모든 리소스**  ->  **[사용자의 IoT hub]**  ->  **IoT Edge**  ->  **[사용자의 장치 ID]** 로 이동 합니다. **모듈** 탭을 클릭 하 여 설치 된 모든 모듈의 런타임 상태를 확인 합니다.

:::image type="content" source="./media/troubleshoot-audio-accessory-speech-module/over-the-air-iot-edge-device-page.png" alt-text="Azure Portal의에 지 장치 페이지.":::

**Azureearspeechclientmodule** 의 런타임 상태가 **실행 중** 으로 표시 되지 않으면 **모듈**  ->  **azureearspeechclientmodule** 설정을 클릭 합니다. **모듈 설정** 페이지에서 **원하는 상태** 를 **실행 중** 으로 설정 하 고 **업데이트** 를 클릭 합니다.

## <a name="understanding-ear-som-led-indicators"></a>귀 SoM LED 표시기 이해

LED 표시기를 사용 하 여 장치가 있는 상태를 파악할 수 있습니다. 일반적으로 장치 전원이 켜진 후 모듈을 완전히 초기화 하는 데 2 분 정도 걸립니다. 초기화 단계를 진행 하는 동안 다음을 확인할 수 있습니다.

1. 가운데 흰색 LED on (정적): 장치의 전원이 켜져 있습니다.
2. 가운데 흰색 LED 켜기 (깜박임): 인증이 진행 중입니다.
3. 장치가 인증 되 고 사용할 준비가 되 면 세 Led가 모두 blue로 변경 됩니다.

|LED|LED 상태|귀 SoM 상태|
|---|---------|--------------|
|L02|1x 흰색, 정적 설정|전원 켜기 |
|L02|1x 흰색, 0.5 Hz 깜박임|인증 진행 중 |
|L01 & L02 & L03|3, 파랑, 정적|키워드를 기다리는 중|
|L01 & L02 & L03|LED 배열 깜박임, 20fps |수신 또는 말하기|
|L01 & L02 & L03|LED 배열 레이스, 20fps|간주|
|L01 & L02 & L03|3, 빨강, 고정 |음소거|

## <a name="next-steps"></a>다음 단계

Azure Percept에 대 한 문제 해결에 대 한 자세한 내용은 [일반 문제 해결 가이드](./troubleshoot-dev-kit.md) 를 참조 하세요.
