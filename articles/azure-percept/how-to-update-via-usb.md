---
title: USB-C 케이블 연결을 통해 Azure Percept DK 업데이트
description: USB-C 케이블 연결을 통해 Azure Percept DK를 업데이트하는 방법을 알아봅니다.
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: how-to
ms.date: 03/18/2021
ms.custom: template-how-to
ms.openlocfilehash: 39889455cd41883f5782f9fb140b400d6ed09ed7
ms.sourcegitcommit: 32ee8da1440a2d81c49ff25c5922f786e85109b4
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/12/2021
ms.locfileid: "109786738"
---
# <a name="how-to-update-azure-percept-dk-over-a-usb-c-cable-connection"></a>USB-C 케이블 연결을 통해 Azure Percept DK를 업데이트하는 방법

이 가이드에서는 USB 연결을 통해 개발 키트의 운영 체제 및 펌웨어를 성공적으로 업데이트하는 방법을 보여 줍니다. 이 절차에서 수행하는 작업의 개요는 다음과 같습니다.
1. 호스트 컴퓨터에 업데이트 패키지 다운로드
1. 업데이트 패키지를 개발 키트로 전송하는 명령 실행
1. 호스트 컴퓨터에서 감지되고 업데이트 패키지를 수신할 수 있도록 개발 키트를 “USB 모드”로 설정(SSH 사용)
1. USB-C 케이블을 통해 개발 키트를 호스트 컴퓨터에 연결
1. 업데이트가 완료될 때까지 대기

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

1. [NXP UUU 도구](https://github.com/NXPmicro/mfgtools/releases)입니다. **자산** 탭에서 **최신 릴리스** uuu.exe 파일(Windows용) 또는 uuu 파일(Linux용)을 다운로드합니다. UUU는 NXP 개발 보드를 업데이트하기 위해 사용되는 NXP에서 만든 도구입니다.

1. [업데이트 파일을 다운로드합니다](https://go.microsoft.com/fwlink/?linkid=2155734). 다음 섹션에서 추출할 zip 파일에 모두 포함되어 있습니다.

1. 세 개의 빌드 아티팩트가 모두 있는지 확인합니다.
    - Azure-Percept-DK- *&lt;버전 번호&gt;* .raw
    - fast-hab-fw.raw
    - emmc_full.txt

## <a name="set-up-your-environment"></a>환경 설정

1. 호스트 컴퓨터에서 명령줄을 통해 쉽게 액세스할 수 있는 위치에 폴더/디렉터리를 만듭니다.

1. UUU 도구(**uuu.exe** 또는 **uuu**)를 새 폴더에 복사합니다.

1. 이전에 다운로드한 업데이트 파일을 UUU 도구가 포함된 새 폴더로 추출합니다.

## <a name="update-your-device"></a>디바이스 업데이트

이 절차에서는 업데이트를 위해 개발 키트의 단일 USB-C 포트를 사용합니다.  컴퓨터에 USB-C 포트가 있으면 Azure Percept Vision 디바이스를 분리하고 해당 케이블을 사용할 수 있습니다.  컴퓨터에 USB-A 포트만 있으면 개발 키트의 USB-C 포트에서 Azure Percept Vision 디바이스를 분리하고 USB-C-USB-A 케이블(별도 판매)을 개발 키트와 호스트 컴퓨터에 연결합니다.

1. Windows 명령 프롬프트(시작 > cmd) 또는 Linux 터미널을 열고 **업데이트 파일 및 UUU 도구가 저장된 폴더로 이동** 합니다. 

1. 명령 프롬프트 또는 터미널에 다음 명령을 입력합니다.

    - Windows:

        ```console
        uuu -b emmc_full.txt fast-hab-fw.raw Azure-Percept-DK-<version number>.raw 
        ```

    - Linux:

        ```bash
        sudo ./uuu -b emmc_full.txt fast-hab-fw.raw Azure-Percept-DK-<version number>.raw
        ```

1. 명령 프롬프트 창에 "**알려진 USB 디바이스가 표시되길 기다리는 중...** " 메시지가 표시됩니다. UUU 도구가 이제 호스트 컴퓨터에서 개발 키트가 감지되기를 기다리는 중입니다. 이제 다음 단계로 진행해도 좋습니다.

1. 제공된 USB-C 케이블을 개발 키트의 USB-C 포트와 호스트 컴퓨터의 USB-C 포트에 연결합니다. 컴퓨터에 USB-A 포트만 있으면 USB-C-USB-A 케이블(별도 판매)을 개발 키트와 호스트 컴퓨터에 연결합니다.

1. SSH를 통해 개발 키트에 연결합니다. SSH에 대한 도움이 필요하면 [다음 지침을 따르세요](./how-to-ssh-into-percept-dk.md).

1. SSH 터미널에서 다음 명령을 입력합니다.

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

1. 업데이트가 완료되면 개발 키트의 전원을 끕니다. PC에서 USB 케이블을 분리합니다.  

## <a name="next-steps"></a>다음 단계

[Azure Percept DK 설치 환경](./quickstart-percept-dk-set-up.md)을 통해 디바이스를 다시 구성합니다.
