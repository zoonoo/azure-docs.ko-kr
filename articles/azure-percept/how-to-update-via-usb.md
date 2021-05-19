---
title: USB 연결을 통해 Azure Percept DK 업데이트
description: USB 연결을 통해 Azure Percept DK를 업데이트하는 방법을 알아봅니다.
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: how-to
ms.date: 03/18/2021
ms.custom: template-how-to
ms.openlocfilehash: cd6e4e62123b4d4b927cf385aaf64a066eecc1e0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104887753"
---
# <a name="how-to-update-azure-percept-dk-over-a-usb-connection"></a>USB 연결을 통해 Azure Percept DK를 업데이트하는 방법

OTA(무선) 업데이트를 사용하여 개발 키트의 운영 체제와 펌웨어를 최신 상태로 유지하는 것이 좋지만, USB 연결을 통해 개발 키트를 업데이트(또는 "플래싱")하는 시나리오가 필요합니다.

- 연결 또는 기타 기술 문제로 인해 OTA 업데이트를 수행할 수 없습니다.
- 디바이스를 공장 상태로 다시 설정해야 합니다.

이 가이드에서는 USB 연결을 통해 개발 키트의 운영 체제 및 펌웨어를 성공적으로 업데이트하는 방법을 보여 줍니다.

> [!WARNING]
> USB를 통해 개발 키트를 업데이트하면 AI 모델 및 컨테이너를 포함하여 디바이스의 기존 데이터가 모두 삭제됩니다.
>
> 모든 지침을 순서대로 수행합니다. 단계를 건너뛰면 개발 키트가 사용할 수 없는 상태가 될 수 있습니다.

## <a name="prerequisites"></a>필수 구성 요소

- Azure Percept DK
- Wi-Fi 기능 및 사용 가능한 USB-C 또는 USB-A 포트를 사용하는 Windows, Linux 또는 OS X 기반 호스트 컴퓨터
- USB-C - USB-A 케이블(선택 사항, 별도로 판매)
- [Azure Percept DK 설치 환경](./quickstart-percept-dk-set-up.md) 중에 만든 SSH 로그인

## <a name="download-software-tools-and-update-files"></a>소프트웨어 도구 다운로드 및 파일 업데이트

1. [NXP UUU 도구](https://github.com/NXPmicro/mfgtools/releases)입니다. **자산** 탭 아래에서 **최신 릴리스** uuu.exe 파일(Windows의 경우) 또는 uuu 파일(Linux의 경우)을 다운로드합니다.

1. [7-Zip](https://www.7-zip.org/). 이 소프트웨어는 XZ 압축 파일에서 원시 이미지 파일을 추출하는 데 사용됩니다. .exe 파일의 적절한 버전을 다운로드하고 설치합니다.

1. [업데이트 파일을 다운로드합니다](https://go.microsoft.com/fwlink/?linkid=2155734).

1. 세 개의 빌드 아티팩트가 모두 있는지 확인합니다.
    - Azure-Percept-DK- *&lt;버전 번호&gt;* .raw.xz
    - fast-hab-fw.raw
    - emmc_full.txt

## <a name="set-up-your-environment"></a>환경 설정

1. 호스트 컴퓨터에서 명령줄을 통해 쉽게 액세스할 수 있는 위치에 폴더/디렉터리를 만듭니다.

1. UUU 도구(**uuu.exe** 또는 **uuu**)를 새 폴더에 복사합니다.

1. **Azure-Percept-DK- *&lt;버전 번호&gt;* .raw.xz** 를 마우스 오른쪽 단추로 클릭하고 **7-Zip** &gt; **여기에서 추출** 을 선택하여 압축 파일에서 **Azure-Percept-DK- *&lt;버전 번호&gt;* .raw** 파일을 추출합니다.

1. 추출된 **Azure-Percept-DK- *&lt;버전 번호&gt;* .raw** 파일, **fast-hab-fw.raw**, **emmc_full.txt** 를 UUU 도구가 포함된 폴더로 이동합니다.

## <a name="update-your-device"></a>디바이스 업데이트

1. [개발자 키트에 SSH를 수행합니다](./how-to-ssh-into-percept-dk.md).

1. 다음으로 Windows 명령 프롬프트(**시작** > **cmd**) 또는 Linux 터미널을 열고 업데이트 파일 및 UUU 도구가 저장된 폴더로 이동합니다. 컴퓨터에서 플래시 가능 디바이스를 지원하도록 준비하려면 명령 프롬프트 또는 터미널에서 다음 명령을 입력합니다.

    - Windows:

        ```console
        uuu -b emmc_full.txt fast-hab-fw.raw Azure-Percept-DK-<version number>.raw 
        ```

    - Linux:

        ```bash
        sudo ./uuu -b emmc_full.txt fast-hab-fw.raw Azure-Percept-DK-<version number>.raw
        ```

1. 캐리어 보드의 USB-C 포트에서 Azure Percept Vision 디바이스의 연결을 끊습니다.

1. 제공된 USB-C 케이블을 캐리어 보드의 USB-C 포트와 호스트 컴퓨터의 USB-C 포트에 연결합니다. 컴퓨터에 USB-A 포트만 있으면 USB-C -USB-A 케이블(별도 판매)을 캐리어 보드와 호스트 컴퓨터에 연결합니다.

1. SSH 클라이언트 프롬프트에서 다음 명령을 입력합니다.

    1. 디바이스를 USB 업데이트 모드로 설정합니다.

        ```bash
        sudo flagutil    -wBfRequestUsbFlash    -v1
        ```

    1. 디바이스를 다시 부팅합니다. 업데이트 설치가 시작됩니다.

        ```bash
        sudo reboot -f
        ```

1. 다른 명령 프롬프트 또는 터미널로 다시 이동합니다. 업데이트가 완료되면 ```Success 1    Failure 0``` 메시지가 표시됩니다.

    > [!NOTE]
    > 업데이트 후 디바이스가 공장 설정으로 다시 설정되며 Wi-Fi 연결 및 SSH 로그인이 손실됩니다.

1. 업데이트가 완료되면 캐리어 보드의 전원을 끕니다. PC에서 USB 케이블을 분리합니다.  

## <a name="next-steps"></a>다음 단계

[Azure Percept DK 설치 환경](./quickstart-percept-dk-set-up.md)을 통해 디바이스를 다시 구성합니다.