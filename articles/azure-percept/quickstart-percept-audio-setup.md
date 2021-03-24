---
title: Azure Percept Audio 시작
description: Azure Percept Audio 디바이스를 Azure Percept DK에 연결하는 방법을 알아봅니다.
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: quickstart
ms.date: 02/18/2021
ms.custom: template-quickstart
ms.openlocfilehash: 660f03ce248a27a00fdd443964fbdba2fe3adeb0
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102179275"
---
# <a name="azure-percept-audio-setup"></a>Azure Percept Audio 설정

Azure Percept Audio는 Azure Percept DK에서 기본 제공됩니다. 특별한 설정은 필요하지 않습니다.

## <a name="prerequisites"></a>사전 요구 사항

- Azure Percept DK(Devkit)
- Azure Percept Audio
- [Azure 구독](https://azure.microsoft.com/free/)
- [Azure Percept DK 설정 환경](./quickstart-percept-dk-set-up.md): devkit를 Wi-Fi 네트워크에 연결하고, IoT Hub를 만들고, devkit를 IoT Hub에 연결
- 3\.5mm 오디오 잭에 연결할 수 있는 스피커 또는 헤드폰(선택 사항)

## <a name="connecting-your-devices"></a>디바이스 연결

1. 포함된 Micro USB - USB Type-A 케이블로 Azure Percept Audio 디바이스를 Azure Percept DK 캐리어 보드에 연결합니다. 케이블의 Micro USB 쪽 끝을 인터포저(개발자) 보드에 연결하고 Type-A 쪽 끝을 Percept DK 캐리어 보드에 연결합니다.
1. (선택 사항) ‘라인 출력’이라고 표시된 헤드폰 잭을 통해 스피커 또는 헤드폰을 Azure Percept Audio에 연결합니다. 그러면 음성 도우미의 오디오 응답을 들을 수 있습니다. 스피커 또는 헤드폰을 연결하지 않는 경우에도 데모 창에서 응답을 텍스트로 볼 수 있습니다. 

1. devkit의 전원을 켭니다. 인터포저 보드의 LED L02가 흰색 점멸로 바뀌며 이는 디바이스의 전원이 켜져 있고 Audio SoM이 인증 중이라는 것을 나타냅니다.

1. 인증 프로세스가 완료될 때까지 기다립니다. 최대 3분 정도 걸릴 수 있습니다.

1. 다음 중 하나가 표시되면 프로토타입 생성을 시작할 준비가 된 것입니다.

    - LED L02가 흰색 고정으로 바뀝니다. 인증이 완료되었지만 아직 키워드로 Devkit를 구성하지 않았다는 뜻입니다.
    - LED 3개가 모두 파란색으로 바뀝니다. 인증이 완료되었으며 Devkit가 키워드로 구성되었다는 뜻입니다.

## <a name="next-steps"></a>다음 단계

[Azure Percept Studio](https://go.microsoft.com/fwlink/?linkid=2135819)에서 [코드 없는 음성 솔루션](./tutorial-no-code-speech.md) 만들기
