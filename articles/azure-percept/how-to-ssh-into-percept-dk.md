---
title: SSH를 통해 Azure Percept 진한 사용자에 연결
description: PuTTY를 사용 하 여 Azure Percept 진한에 SSH 하는 방법 알아보기
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: how-to
ms.date: 03/18/2021
ms.custom: template-how-to
ms.openlocfilehash: 39ee1c1cc5b52dc62e3199536234c1f7d9381436
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "104721480"
---
# <a name="connect-to-your-azure-percept-dk-over-ssh"></a>SSH를 통해 Azure Percept 진한 사용자에 연결

아래 단계에 따라 Azure Percept 진한 OpenSSH 또는 [PuTTY](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html)에 대 한 SSH 연결을 설정 합니다.

## <a name="prerequisites"></a>필수 조건

- Wi-Fi 기능을 사용 하는 Windows, Linux 또는 OS X 기반 호스트 컴퓨터
- SSH 클라이언트 (설치 지침에 대해서는 다음 섹션 참조)
- Azure Percept 진한 (dev kit)
- [Azure PERCEPT 진한 설치 환경](./quickstart-percept-dk-set-up.md) 에서 만든 SSH 로그인

## <a name="install-your-preferred-ssh-client"></a>기본 SSH 클라이언트 설치

호스트 컴퓨터에서 Linux 또는 OS X를 실행 하는 경우 SSH 서비스는 이러한 운영 체제에 포함 되며 별도의 클라이언트 응용 프로그램 없이 실행 될 수 있습니다. SSH 서비스를 실행 하는 방법에 대 한 자세한 내용은 운영 체제 제품 설명서를 참조 하십시오.

호스트 컴퓨터에서 Windows를 실행 하는 경우 두 개의 SSH 클라이언트 옵션인 OpenSSH 및 PuTTY를 선택할 수 있습니다.

### <a name="openssh"></a>OpenSSH

Windows 10에는 명령 프롬프트 내의 간단한 명령을 사용 하 여 실행할 수 있는 OpenSSH 라는 기본 제공 SSH 클라이언트가 포함 되어 있습니다. 사용할 수 있는 경우 Azure Percept와 함께 OpenSSH를 사용 하는 것이 좋습니다. Windows 컴퓨터에 OpenSSH가 설치 되어 있는지 확인 하려면 다음 단계를 수행 합니다.

1. **시작**  ->  **설정** 으로 이동 합니다.

1. **앱** 을 선택합니다.

1. **앱 & 기능** 에서 **선택적 기능** 을 선택 합니다.

1. **설치 된 기능** 검색 표시줄에 **OpenSSH Client** 를 입력 합니다. OpenSSH이 표시 되 면 클라이언트가 이미 설치 되어 있으며 다음 섹션으로 이동할 수 있습니다. OpenSSH가 표시 되지 않으면 **기능 추가** 를 클릭 합니다.

    :::image type="content" source="./media/how-to-ssh-into-percept-dk/open-ssh-install.png" alt-text="OpenSSH 설치 상태를 보여 주는 설정의 스크린샷":::

1. **OpenSSH Client** 를 선택 하 고 **설치** 를 클릭 합니다. 이제 다음 섹션으로 이동할 수 있습니다. 컴퓨터에 OpenSSH를 설치할 수 없는 경우 아래 단계에 따라 타사 SSH 클라이언트 인 PuTTY를 설치 합니다.

### <a name="putty"></a>PuTTY

Windows 컴퓨터에 OpenSSH가 포함 되어 있지 않은 경우 [PuTTY](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html)를 사용 하는 것이 좋습니다. PuTTY를 다운로드 하 여 설치 하려면 다음 단계를 완료 합니다.

1. [PuTTY 다운로드 페이지로](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html)이동 합니다.

1. **패키지 파일** 에서 32 비트 또는 64 비트 .msi 파일을 클릭 하 여 설치 관리자를 다운로드 합니다. 선택할 버전을 모를 경우 [faq](https://www.chiark.greenend.org.uk/~sgtatham/putty/faq.html#faq-32bit-64bit)를 확인 하세요.

1. 설치 관리자를 클릭 하 여 설치 프로세스를 시작 합니다. 필요에 따라 메시지를 따릅니다.

1. 축하합니다! PuTTY SSH 클라이언트를 설치 했습니다.

## <a name="initiate-the-ssh-connection"></a>SSH 연결 시작

1. Azure Percept의 기능을 활용 하세요.

1. 개발자 키트가 이더넷 또는 Wi-fi를 통해 네트워크에 이미 연결 되어 있는 경우 다음 단계로 건너뜁니다. 그렇지 않은 경우에는 호스트 컴퓨터를 dev kit의 Wi-Fi 액세스 지점에 직접 연결 합니다. 다른 Wi-Fi 네트워크에 연결 하는 것과 마찬가지로 컴퓨터에서 네트워크 및 인터넷 설정을 열고, 다음 네트워크를 클릭 하 고, 메시지가 표시 되 면 네트워크 암호를 입력 합니다.

    - **네트워크 이름**: dev kit의 운영 체제 버전에 따라 Wi-Fi 액세스 지점의 이름은 **scz** 또는 **apd-xxxx** (여기서 "XXXX"는 dev kit의 MAC 주소의 마지막 4 자리)입니다.
    - **암호**: dev kit와 함께 제공 된 시작 카드에서 찾을 수 있습니다.

    > [!WARNING]
    > Azure Percept 진한 Wi-Fi 액세스 지점에 연결 되어 있는 동안 호스트 컴퓨터가 일시적으로 인터넷에 대 한 연결을 잃게 됩니다. 활성 비디오 회의, 웹 스트리밍 또는 기타 네트워크 기반 환경이 중단 됩니다.

1. Ssh 클라이언트에 따라 SSH 연결 프로세스를 완료 합니다.

### <a name="using-openssh"></a>OpenSSH 사용

1. 명령 프롬프트를 엽니다.  ->  **명령 프롬프트** 를 시작 합니다.

1. 명령 프롬프트에 다음을 입력 합니다.

    ```console
    ssh [your ssh user name]@[IP address]
    ```

    컴퓨터가 dev kit의 Wi-Fi 액세스 지점에 연결 된 경우 IP 주소는 10.1.1.1이 됩니다. 개발자 키트가 이더넷을 통해 연결 된 경우 이더넷 라우터 또는 허브에서 가져올 수 있는 장치의 로컬 IP 주소를 사용 합니다. 개발자 키트가 Wi-fi를 통해 연결 된 경우 [Azure PERCEPT 진한 설치 환경](./quickstart-percept-dk-set-up.md)에서 개발자 키트에 할당 된 IP 주소를 사용 해야 합니다.

    > [!TIP]
    > Dev kit가 Wi-Fi 네트워크에 연결 되어 있지만 해당 IP 주소를 알 수 없는 경우 Azure Percept Studio로 이동 하 여 [장치의 비디오 스트림을 엽니다](./how-to-view-video-stream.md). 비디오 스트림 브라우저 탭의 주소 표시줄에 장치의 IP 주소가 표시 됩니다.

1. 메시지가 표시 되 면 SSH 암호를 입력 합니다.

    :::image type="content" source="./media/how-to-ssh-into-percept-dk/open-ssh-prompt.png" alt-text="SSH 명령 프롬프트 로그인 열기의 스크린샷":::

1. OpenSSH를 통해 dev kit에 처음 연결 하는 경우 호스트의 키를 허용 하 라는 메시지가 표시 될 수도 있습니다. **예** 를 입력 하 여 키를 적용 합니다.

1. 축하합니다! SSH를 통해 dev kit에 성공적으로 연결 되었습니다.

### <a name="using-putty"></a>PuTTY 사용

1. PuTTY를 엽니다. **PuTTY 구성** 창에 다음을 입력 하 고 **열기** 를 클릭 하 여 dev kit에 SSH를 수행 합니다.

    1. 호스트 이름: [IP 주소]
    1. 포트: 22
    1. 연결 형식: SSH

    **호스트 이름은** 개발자 키트의 IP 주소입니다. 컴퓨터가 dev kit의 Wi-Fi 액세스 지점에 연결 된 경우 IP 주소는 10.1.1.1이 됩니다. 개발자 키트가 이더넷을 통해 연결 된 경우 이더넷 라우터 또는 허브에서 가져올 수 있는 장치의 로컬 IP 주소를 사용 합니다. 개발자 키트가 Wi-fi를 통해 연결 된 경우 [Azure PERCEPT 진한 설치 환경](./quickstart-percept-dk-set-up.md)에서 개발자 키트에 할당 된 IP 주소를 사용 해야 합니다.

    > [!TIP]
    > Dev kit가 Wi-Fi 네트워크에 연결 되어 있지만 해당 IP 주소를 알 수 없는 경우 Azure Percept Studio로 이동 하 여 [장치의 비디오 스트림을 엽니다](./how-to-view-video-stream.md). 비디오 스트림 브라우저 탭의 주소 표시줄에 장치의 IP 주소가 표시 됩니다.

    :::image type="content" source="./media/how-to-ssh-into-percept-dk/ssh-putty.png" alt-text="PuTTY 구성 창의 스크린샷":::

1. PuTTY 터미널이 열립니다. 메시지가 표시 되 면 터미널에 SSH 사용자 이름 및 암호를 입력 합니다.

1. 축하합니다! SSH를 통해 dev kit에 성공적으로 연결 되었습니다.

## <a name="next-steps"></a>다음 단계

SSH를 통해 Azure Percept 진한에 연결한 후 [장치 문제 해결](./troubleshoot-dev-kit.md) 및 [USB 업데이트](./how-to-update-via-usb.md)를 포함 하 여 다양 한 작업을 수행할 수 있습니다.