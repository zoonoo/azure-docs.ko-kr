---
title: USB 연결을 통해 Azure Percept 진한 업데이트
description: USB 연결을 통해 Azure Percept 진한을 업데이트 하는 방법을 알아봅니다.
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: how-to
ms.date: 02/18/2021
ms.custom: template-how-to
ms.openlocfilehash: 7f5e5e4da9fea671fc85a55fc8cc191fa14b720f
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/02/2021
ms.locfileid: "101663005"
---
# <a name="how-to-update-azure-percept-dk-over-a-usb-connection"></a>USB 연결을 통해 Azure Percept 진한를 업데이트 하는 방법

이 가이드에 따라 Azure Percept의 캐리어 보드에 대 한 USB 업데이트를 수행 하는 방법을 알아봅니다.

## <a name="prerequisites"></a>필수 구성 요소
- 사용 가능한 USB-C 또는 USB-A 포트를 사용 하는 호스트 컴퓨터
- Azure Percept 진한 (dev kit) 캐리어 보드와 usb-c를 USB-C 케이블로 제공 합니다. 호스트 컴퓨터에 usb A 포트가 있지만 USB-C 포트가 아닌 경우 usb-A 케이블 (별도로 판매)을 사용할 수 있습니다.
- [PuTTY](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html) (관리자 액세스 필요)를 설치 합니다.
- NXP UUU 도구를 설치 합니다. 자산 탭 아래에서 최신 릴리스 uuu.exe 파일 (Windows의 경우) 또는 uuu 파일 (Linux의 경우) [을 다운로드](https://github.com/NXPmicro/mfgtools/releases) 합니다.
- [7-Zip을 설치](https://www.7-zip.org/)합니다. 이 소프트웨어는 XZ 압축 파일에서 원시 이미지 파일을 추출 하는 데 사용 됩니다. 적절 한 .exe 파일을 다운로드 하 여 설치 합니다.

## <a name="steps"></a>단계
1.  다음 [세 가지 USB 업데이트 파일](https://go.microsoft.com/fwlink/?linkid=2155734)을 다운로드 합니다.
    - pe101-***&lt; 버전 번호 &gt;***. raw..
    - emmc_full.txt
    - fast-hab-fw
 
1. _압축 된 pe101-uefi_**&lt; &gt;** * _&lt; 버전 번호 &gt;_* *.. pe101 파일에서 압축을 풉니다. 추출 하는 방법이 확실 하지 않나요? 7-Zip을 다운로드 하 여 설치 하 고, **xz** 이미지 파일을 마우스 오른쪽 단추로 클릭 한 다음, 7-zip &gt; 압축 풀기를 선택 합니다.

1. UUU 도구를 포함 하는 폴더에 다음 세 개의 파일을 복사 합니다.
    - Pe101-uefi-***&lt; 버전 번호 &gt;*** raw 파일 (2 단계에서)을 추출 했습니다.
    - 1 단계에서 emmc_full.txt 합니다.
    - fast-hab-fw (1 단계).
 
1. Dev kit의 기능입니다.
1. [SSH를 통해 dev kit에 연결](./how-to-ssh-into-percept-dk.md)
1. Windows 명령 프롬프트 ( &gt; Cmd 시작) 또는 Linux 터미널을 열고 업데이트 파일이 저장 된 폴더로 이동 합니다. 다음 명령을 실행 하 여 업데이트를 시작 합니다.
    - Windows: ```uuu -b emmc_full.txt fast-hab-fw.raw pe101-uefi-<version number>.raw```
    - Linux: ```sudo ./uuu -b emmc_full.txt fast-hab-fw.raw pe101-uefi-<version number>.raw```
    
이러한 명령을 실행 하면 "장치 대기 중 ..." 라는 메시지가 표시 될 수 있습니다. 명령 프롬프트에서을 입력 합니다. 이는 예상 된 것 이며 다음 단계를 진행 해야 합니다.
    
1. USB 케이블을 통해 개발자 키트 캐리어 보드를 호스트 컴퓨터에 연결 합니다. 사용 가능한 포트에 따라 항상 캐리어 보드 USB-C 포트에서 호스트 컴퓨터의 USB-c 또는 USB-A 포트 (USB-C에서 USB-A 케이블로 판매)로 연결 합니다. 
 
1. SSH/PuTTY 터미널에서 다음 명령을 입력 하 여 dev kit를 USB 모드로 설정한 다음 dev kit를 다시 부팅 합니다.
    - ```flagutil    -wBfRequestUsbFlash    -v1```
    - ```reboot -f```
 
1. 호스트 컴퓨터에서 장치를 인식 하 고 업데이트 프로세스를 자동으로 시작 한다는 메시지가 표시 될 수 있습니다. 명령 프롬프트로 다시 이동 하 여 상태를 확인 합니다. 이 프로세스는 최대 10 분 정도 걸리며 업데이트가 성공 하면 "성공 1 오류 0" 이라는 메시지가 표시 됩니다.
 
1. 업데이트가 완료 되 면 캐리어 보드의 전원을 끕니다. PC에서 USB 케이블을 분리 합니다.  USB 케이블을 사용 하 여 Azure Percept 비전 모듈을 캐리어 보드에 다시 꽂습니다.

1. 캐리어 보드를 다시 켭니다.

## <a name="next-steps"></a>다음 단계

이제 dev kit가 성공적으로 업데이트 되었습니다. Devkit를 사용 하 여 개발 및 작업을 계속할 수 있습니다.