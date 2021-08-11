---
title: Azure Percept Audio 및 음성 모듈 관련 문제 해결
description: Azure Percept Audio 및 azureearspeechclientmodule에 대한 문제 해결 팁 가져오기
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: how-to
ms.date: 03/25/2021
ms.custom: template-how-to
ms.openlocfilehash: c02c93acd23b71f73d5e27d8914d08fb1a1f99f9
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/19/2021
ms.locfileid: "110071401"
---
# <a name="azure-percept-audio-and-speech-module-troubleshooting"></a>Azure Percept Audio 및 음성 모듈 문제 해결

음성 도우미 애플리케이션 문제를 해결하려면 아래 지침을 따르십시오.

## <a name="understanding-ear-som-led-indicators"></a>Ear SoM LED 표시기 이해

LED 표시기를 사용하여 디바이스의 상태를 파악할 수 있습니다. 디바이스 전원을 켜고 모듈이 완전히 초기화되기까지 4~5분 정도가 소요됩니다. 초기화 단계를 진행하는 동안 다음을 확인할 수 있습니다.

1. 가운데 흰색 LED 켜짐(정적): 디바이스의 전원이 켜져 있습니다.
1. 가운데 흰색 LED 켜짐(깜박임): 인증이 진행 중입니다.
1. 가운데 흰색 LED 켜짐(고정): 디바이스가 인증되었지만 키워드가 구성되지 않았습니다.
1. 데모가 배포되고 디바이스를 사용할 준비가 되면 세 개의 LED가 모두 파란색으로 바뀝니다.

자세한 내용은 [Azure Percept 오디오 단추 및 LED 동작](./audio-button-led-behavior.md)에 대해 이 문서를 참조하세요.

### <a name="troubleshooting-led-issues"></a>LED 문제 해결
- **중앙 LED가 흰색이면** [템플릿을 사용하여 음성 도우미를 만드세요](./tutorial-no-code-speech.md).
- **중앙 LED가 항상 깜박이면** 인증 문제가 있다는 것입니다. 이러한 문제 해결 단계를 수행합니다.
    - USB A 및 마이크로 USB 연결이 안전한지 확인합니다. 
    - [음성 모듈이 실행 중인지](./troubleshoot-audio-accessory-speech-module.md#checking-runtime-status-of-the-speech-module) 확인합니다.
    - 디바이스를 다시 시작합니다.
    - [로그를 수집](./troubleshoot-audio-accessory-speech-module.md#collecting-speech-module-logs)하여 지원 요청에 첨부
    - 개발 키트에서 최신 소프트웨어를 실행하고 있는지 확인하고 사용 가능한 경우 업데이트를 적용합니다.

## <a name="checking-runtime-status-of-the-speech-module"></a>음성 모듈의 런타임 상태를 확인하는 중

**azureearspeechclientmodule** 의 런타임 상태가 **실행 중** 으로 표시되는지 확인합니다. 디바이스 모듈의 런타임 상태를 찾으려면 [Azure Portal](https://portal.azure.com/)을 열고 **모든 리소스** ->  **[사용자의 IoT 허브]**  -> **IoT Edge** ->  **[사용자의 디바이스 ID]** 로 이동합니다. **모듈** 탭을 클릭하여 설치된 모든 모듈의 런타임 상태를 확인합니다.

:::image type="content" source="./media/troubleshoot-audio-accessory-speech-module/over-the-air-iot-edge-device-page.png" alt-text="Azure Portal의 에지 디바이스 페이지.":::

**azureearspeechclientmodule** 의 런타임 상태가 **실행 중** 으로 표시되지 않으면  -> **azureearspeechclientmodule** **모듈 설정** 을 클릭합니다. **모듈 설정** 페이지에서 **원하는 상태** 를 **실행 중** 으로 설정하고 **업데이트** 를 클릭합니다.

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

## <a name="known-issues"></a>알려진 문제
- 무료 평가판을 사용하는 경우 음성 모델이 무료 평가판 가격 계획을 초과할 수 있습니다. 이 경우 모델은 오류 메시지 없이 작동을 중지합니다.
- 6개 이상의 IoT Edge 디바이스가 연결된 경우 보고서(원격 분석을 통해 IoT Hub 및 Speech Studio로 전송된 텍스트)가 차단될 수 있습니다.
- 디바이스가 리소스와 다른 지역에 있는 경우 보고서 메시지가 지연될 수 있습니다. 

## <a name="useful-links"></a>유용한 링크
- [Azure Percept Audio 설정](./quickstart-percept-audio-setup.md)
- [Azure Percept Audio 단추 및 LED 동작](./audio-button-led-behavior.md)
- [Azure Percept DK 및 Azure Percept Audio를 사용하여 음성 도우미 만들기](./tutorial-no-code-speech.md)
- [Azure Percept DK 일반 문제 해결 가이드](./troubleshoot-dev-kit.md)
