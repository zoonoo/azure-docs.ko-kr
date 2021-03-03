---
title: SSH를 통해 Azure Percept 진한 사용자에 연결
description: PuTTY를 사용 하 여 Azure Percept 진한에 SSH 하는 방법 알아보기
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: how-to
ms.date: 02/03/2021
ms.custom: template-how-to
ms.openlocfilehash: 8d150228be2cf6deff3bc2fd0a0599cca70d24ac
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/02/2021
ms.locfileid: "101663015"
---
# <a name="connect-to-your-azure-percept-dk-over-ssh"></a>SSH를 통해 Azure Percept 진한 사용자에 연결

아래 단계에 따라 Azure Percept 진한 [PuTTY](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html)에 대 한 SSH 연결을 설정 합니다.

## <a name="prerequisites"></a>필수 구성 요소

- Wi-Fi 기능을 사용 하는 Windows, Linux 또는 OS X 기반 호스트 컴퓨터
- SSH 클라이언트
    - 호스트 컴퓨터에서 Windows를 실행 하는 경우 [PuTTY](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html) 는 효과적인 SSH 클라이언트 이며이 가이드 전체에서 사용 됩니다.
    - 호스트 컴퓨터에서 Linux 또는 OS X를 실행 하는 경우 SSH 서비스는 이러한 운영 체제에 포함 되며 별도의 클라이언트 응용 프로그램 없이 실행 될 수 있습니다. SSH 서비스를 실행 하는 방법에 대 한 자세한 내용은 운영 체제 제품 설명서를 참조 하십시오.
- Azure Percept 진한

## <a name="initiate-the-ssh-connection"></a>SSH 연결 시작

1. Azure Percept 진한 (dev kit)의 기능

1. 개발자 키트가 이더넷 또는 Wi-fi를 통해 네트워크에 이미 연결 되어 있는 경우 다음 단계로 건너뜁니다. 그렇지 않으면 다른 Wi-Fi 네트워크에 연결 하는 것 처럼 호스트 컴퓨터를 dev kit의 Wi-Fi 액세스 지점에 직접 연결 합니다.
    - **네트워크 이름**: scz-xxxx(여기서 "xxxx"는 devkit의 MAC 네트워크 주소 마지막 네 자리)
    - **암호**: devkit와 함께 제공된 시작 카드에서 찾을 수 있습니다.

    > [!WARNING]
    > Azure Percept 진한 Wi-Fi 액세스 지점에 연결 되어 있는 동안 호스트 컴퓨터가 일시적으로 인터넷에 대 한 연결을 잃게 됩니다. 활성 비디오 회의, 웹 스트리밍 또는 기타 네트워크 기반 환경은 Azure Percept 진한 온 보 딩 환경의 3 단계가 완료 될 때까지 중단 됩니다.

1. PuTTY를 엽니다. 다음을 입력 하 고 **열기** 를 클릭 하 여 DEVKIT에 SSH를 수행 합니다.

    1. 호스트 이름: 10.1.1.1
    1. 포트: 22
    1. 연결 형식: SSH

    > [!NOTE]
    > **호스트 이름은** 장치의 IP 주소입니다. Dev kit가 dev kit의 Wi-Fi 액세스 지점에 연결 된 경우 IP 주소는 10.1.1.1이 됩니다. 개발자 키트가 이더넷을 통해 연결 된 경우 이더넷 라우터 또는 허브에서 가져올 수 있는 장치의 로컬 IP 주소를 사용 합니다. 장치가 Wi-fi를 통해 연결 된 경우 [Azure PERCEPT 진한 온 보 딩 환경](./quickstart-percept-dk-set-up.md)에서 수집 된 IP 주소를 사용 해야 합니다.

    :::image type="content" source="./media/how-to-ssh-into-percept-dk/ssh-putty.png" alt-text="이미지로.":::

1. PuTTY 터미널에 로그인 합니다. OOBE를 실행 하는 동안 SSH 사용자 이름 및 암호를 설정 하는 경우 메시지가 표시 되 면 해당 로그인 자격 증명을 입력 합니다. 그렇지 않으면 다음을 입력 합니다.  

    1. 로그인: root
    1. 암호: p@ssw0rd

    :::image type="content" source="./media/how-to-ssh-into-percept-dk/putty-terminal.png" alt-text="PuTTY 터미널 창.":::  

## <a name="next-steps"></a>다음 단계

SSH를 통해 Azure Percept 진한에 성공적으로 연결한 후 문제 해결, USB 업데이트 및 DiagTool 또는 SoftAP 도구 실행을 포함 하 여 다양 한 작업을 수행할 수 있습니다.


