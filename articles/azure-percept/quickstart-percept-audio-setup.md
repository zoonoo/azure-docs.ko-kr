---
title: Azure Percept Audio 시작
description: Azure Percept Audio 디바이스를 Azure Percept DK에 연결하는 방법을 알아봅니다.
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: quickstart
ms.date: 02/18/2021
ms.custom: template-quickstart
ms.openlocfilehash: 575107859f56df742ab41a299269c250511022b3
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/02/2021
ms.locfileid: "101664302"
---
# <a name="azure-percept-audio-setup"></a>Azure Percept Audio 설정

Azure Percept Audio는 Azure Percept DK에서 기본 제공됩니다. 특별한 설정은 필요하지 않습니다.

## <a name="prerequisites"></a>사전 요구 사항

- Azure Percept DK(Devkit)
- Azure Percept Audio
- [Azure 구독](https://azure.microsoft.com/free/)
- [Azure Percept DK 설정 환경](./quickstart-percept-dk-set-up.md): Devkit를 Wi-Fi 네트워크에 연결하고, IoT Hub를 만들고, Devkit를 IoT Hub에 연결했습니다.

## <a name="connecting-your-devices"></a>디바이스 연결

1. 포함된 USB Micro Type-B - USB Type-A 케이블로 Azure Percept Audio 디바이스를 Azure Percept DK 캐리어 보드에 연결합니다. 케이블의 Micro Type-B 끝을 Audio SoM에 연결하고 Type-A 끝을 Percept DK 캐리어 보드에 연결합니다.

1. Devkit의 전원을 켭니다.

    - Audio SoM의 LED L01이 녹색으로 바뀝니다. 이는 디바이스 전원이 켜졌다는 뜻입니다.
    - LED L02가 녹색으로 점멸합니다. 이는 Audio SoM을 인증 중이라는 뜻입니다.

1. 인증 프로세스가 완료될 때까지 기다립니다. 최대 3분 정도 걸릴 수 있습니다.

1. 다음 중 하나가 표시되면 프로토타입 생성을 시작할 준비가 된 것입니다.

    - LED L01이 꺼지고 L02가 흰색으로 바뀝니다. 인증이 완료되었지만 아직 키워드로 Devkit를 구성하지 않았다는 뜻입니다.
    - LED 3개가 모두 파란색으로 바뀝니다. 인증이 완료되었으며 Devkit가 키워드로 구성되었다는 뜻입니다.

    > [!NOTE]
    > Devkit가 인증되지 않으면 지원을 요청하세요.

## <a name="next-steps"></a>다음 단계

[코드 없는 음성 솔루션](./tutorial-no-code-speech.md)을 만듭니다.