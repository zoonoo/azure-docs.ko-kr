---
title: 윈도우용 Azure N 시리즈 AMD GPU 드라이버 설정
description: Azure에서 Windows 서버 또는 Windows를 실행하는 N 시리즈 VM에 AMD GPU 드라이버를 설정하는 방법
services: virtual-machines-windows
author: vikancha
manager: jkabat
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 12/4/2019
ms.author: vikancha
ms.openlocfilehash: 02213feb507e9a032a50241fddf31714b9dfd7ee
ms.sourcegitcommit: ae3d707f1fe68ba5d7d206be1ca82958f12751e8
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/10/2020
ms.locfileid: "81011078"
---
# <a name="install-amd-gpu-drivers-on-n-series-vms-running-windows"></a>Windows를 실행하는 N 시리즈 VM에 AMD GPU 드라이버 설치

Windows를 실행하는 새로운 Azure NVv4 시리즈 VM의 GPU 기능을 활용하려면 AMD GPU 드라이버를 설치해야 합니다. AMD 드라이버 확장은 앞으로 몇 주 안에 제공될 예정입니다. 이 문서에서는 지원되는 운영 체제, 드라이버 및 수동 설치 및 확인 단계를 제공합니다.

기본 사양, 스토리지 용량 및 디스크 세부 정보는 [GPU Windows VM 크기](sizes-gpu.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)를 참조하세요.



## <a name="supported-operating-systems-and-drivers"></a>지원되는 운영 체제 및 드라이버

| OS | 드라이버 |
| -------- |------------- |
| 윈도우 10 EVD - 빌드 1903 <br/><br/>윈도우 10 - 빌드 1809<br/><br/>Windows Server 2016<br/><br/>Windows Server 2019 | [20.Q1.1](https://download.microsoft.com/download/3/8/9/3893407b-e8aa-4079-8592-735d7dd1c19a/Radeon-Pro-Software-for-Enterprise-GA.exe) (.exe) |


## <a name="driver-installation"></a>드라이버 설치

1. 원격 데스크톱으로 각 NVv4 시리즈 VM에 연결합니다.

2. NVv4 미리 보기 고객인 경우 VM을 중지하고 중지됨(Deallocated) 상태로 이동할 때까지 기다려 주십시오.

3. VM을 시작하고 최신 [AMD 정리 유틸리티를](https://download.microsoft.com/download/4/f/1/4f19b714-9304-410f-9c64-826404e07857/AMDCleanupUtilityni.exe)다운로드하십시오. "amdcleanuputility-x64.exe"를 실행하여 기존 드라이버를 제거합니다. 이전 드라이버와 함께 설치된 기존 정리 유틸리티는 사용하지 마십시오.  

4. 최신 드라이버를 다운로드하여 설치합니다.

5. VM을 다시 부팅합니다.

## <a name="verify-driver-installation"></a>드라이버 설치 확인

디바이스 관리자에서 드라이버 설치를 확인할 수 있습니다. 다음 예제에서는 Azure NVv4 VM에서 라데온 본능 MI25 카드의 성공적인 구성을 보여 줍니다.
<br />
![GPU 드라이버 속성](./media/n-series-amd-driver-setup/device-manager.png)

dxdiag를 사용하여 비디오 RAM을 포함한 GPU 디스플레이 속성을 확인할 수 있습니다. 다음 예제에서는 Azure NVv4 VM에서 라데온 본능 MI25 카드의 1/2 파티션을 보여 줍니다.
<br />
![GPU 드라이버 속성](./media/n-series-amd-driver-setup/dxdiag-output.png)

Windows를 실행 하는 경우 10 빌드 1903 이상 다음 dxdiag '표시' 탭에 아무 정보 표시 됩니다. 하단에 '모든 정보 저장' 옵션을 사용 하 여 출력 파일 AMD MI25 GPU와 관련 된 정보를 표시 됩니다.

![GPU 드라이버 속성](./media/n-series-amd-driver-setup/dxdiag-details.png)


