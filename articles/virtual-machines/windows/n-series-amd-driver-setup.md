---
title: Windows 용 Azure N 시리즈 AMD GPU 드라이버 설치
description: Azure에서 Windows Server 또는 Windows를 실행 하는 N 시리즈 Vm에 대 한 AMD GPU 드라이버를 설정 하는 방법
author: vikancha
manager: jkabat
ms.service: virtual-machines-windows
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 12/4/2019
ms.author: vikancha
ms.openlocfilehash: 1bcc13db3f503c80fda71a2104d0ff8d99e67df6
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/12/2020
ms.locfileid: "83198007"
---
# <a name="install-amd-gpu-drivers-on-n-series-vms-running-windows"></a>Windows를 실행 하는 N 시리즈 Vm에 AMD GPU 드라이버 설치

Windows를 실행 하는 새로운 Azure NVv4 시리즈 Vm의 GPU 기능을 활용 하려면 AMD GPU 드라이버를 설치 해야 합니다. [AMD Gpu 드라이버 확장](../extensions/hpccompute-amd-gpu-windows.md) 은 NVV4 시리즈 VM에 amd gpu 드라이버를 설치 합니다. Azure PowerShell 또는 Azure Resource Manager 템플릿과 같은 도구나 Azure Portal을 사용하여 확장을 설치 또는 관리합니다. 지원 되는 운영 체제 및 배포 단계는 [AMD GPU 드라이버 확장 설명서](../extensions/hpccompute-amd-gpu-windows.md) 를 참조 하세요.

AMD GPU 드라이버를 수동으로 설치 하도록 선택 하는 경우이 문서에서 지원 되는 운영 체제, 드라이버 및 설치 및 확인 단계를 제공 합니다.

Microsoft에서 게시 하는 GPU 드라이버만 NVv4 Vm에서 지원 됩니다. 다른 원본에서 GPU 드라이버를 설치 하지 마세요.

기본 사양, 스토리지 용량 및 디스크 세부 정보는 [GPU Windows VM 크기](sizes-gpu.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)를 참조하세요.



## <a name="supported-operating-systems-and-drivers"></a>지원되는 운영 체제 및 드라이버

| OS | 드라이버 |
| -------- |------------- |
| Windows 10 EVD-빌드 1903 <br/><br/>Windows 10-빌드 1809<br/><br/>Windows Server 2016<br/><br/>시작 | [20. q 1.1](https://download.microsoft.com/download/3/8/9/3893407b-e8aa-4079-8592-735d7dd1c19a/Radeon-Pro-Software-for-Enterprise-GA.exe) (.exe) |


## <a name="driver-installation"></a>드라이버 설치

1. 원격 데스크톱을 통해 각 NVv4 시리즈 VM에 연결 합니다.

2. NVv4 preview 고객 인 경우 VM을 중지 하 고 중지 (할당 취소 됨) 상태로 전환 될 때까지 기다립니다.

3. VM을 시작 하 고 최신 [AMD 정리 유틸리티](https://download.microsoft.com/download/4/f/1/4f19b714-9304-410f-9c64-826404e07857/AMDCleanupUtilityni.exe)를 다운로드 하세요. "Amdcleanuputility-x64"를 실행 하 여 기존 드라이버를 제거 합니다. 이전 드라이버와 함께 설치 된 기존 정리 유틸리티를 사용 하지 마세요.  

4. 최신 드라이버를 다운로드 하 여 설치 합니다.

5. VM을 다시 부팅합니다.

## <a name="verify-driver-installation"></a>드라이버 설치 확인

디바이스 관리자에서 드라이버 설치를 확인할 수 있습니다. 다음 예제에서는 Azure NVv4 VM에서 Radeon 이러한 MI25 카드의 성공적인 구성을 보여 줍니다.
<br />
![GPU 드라이버 속성](./media/n-series-amd-driver-setup/device-manager.png)

Dxdiag를 사용 하 여 비디오 RAM을 비롯 한 GPU 표시 속성을 확인할 수 있습니다. 다음 예제에서는 Azure NVv4 VM에 있는 Radeon 이러한 MI25 카드의 1/2 파티션을 보여 줍니다.
<br />
![GPU 드라이버 속성](./media/n-series-amd-driver-setup/dxdiag-output.png)

Windows 10 빌드 1903 이상을 실행 하는 경우 dxdiag는 ' 표시 ' 탭에 정보를 표시 하지 않습니다. 맨 아래에 있는 ' 모든 정보 저장 ' 옵션을 사용 하세요. 출력 파일은 AMD MI25 GPU와 관련 된 정보를 표시 합니다.

![GPU 드라이버 속성](./media/n-series-amd-driver-setup/dxdiag-details.png)


