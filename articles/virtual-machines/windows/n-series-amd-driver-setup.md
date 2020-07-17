---
title: Windows용 Azure N 시리즈 AMD GPU 드라이버 설정
description: Azure에서 Windows Server 또는 Windows를 실행하는 N 시리즈 VM용 AMD GPU 드라이버를 설정하는 방법
author: vikancha-MSFT
manager: jkabat
ms.service: virtual-machines-windows
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 12/4/2019
ms.author: vikancha
ms.openlocfilehash: 4693ad8b168ce1ddd7c07afe650a89fc1888ccd7
ms.sourcegitcommit: ec682dcc0a67eabe4bfe242fce4a7019f0a8c405
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/09/2020
ms.locfileid: "86183327"
---
# <a name="install-amd-gpu-drivers-on-n-series-vms-running-windows"></a>Windows를 실행하는 N 시리즈 VM에 AMD GPU 드라이버 설치

Windows를 실행하는 새로운 Azure NVv4 시리즈 VM의 GPU 기능을 활용하려면 AMD GPU 드라이버를 설치해야 합니다. [AMD GPU 드라이버 확장](../extensions/hpccompute-amd-gpu-windows.md)은 NVv4 시리즈 VM에 AMD GPU 드라이버를 설치합니다. Azure PowerShell 또는 Azure Resource Manager 템플릿과 같은 도구나 Azure Portal을 사용하여 확장을 설치 또는 관리합니다. 지원되는 운영 체제 및 배포 단계는 [AMD GPU 드라이버 확장 설명서](../extensions/hpccompute-amd-gpu-windows.md)를 참조하세요.

AMD GPU 드라이버를 수동으로 설치하려는 경우 이 문서는 지원되는 운영 체제, 드라이버, 설치 및 확인 단계를 제공합니다.

Microsoft에서 게시하는 GPU 드라이버만 NVv4 VM에서 지원됩니다. 다른 원본에서 GPU 드라이버를 설치하지 마세요.

기본 사양, 스토리지 용량 및 디스크 세부 정보는 [GPU Windows VM 크기](sizes-gpu.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)를 참조하세요.



## <a name="supported-operating-systems-and-drivers"></a>지원되는 운영 체제 및 드라이버

| OS | 드라이버 |
| -------- |------------- |
| Windows 10 Enterprise 다중 세션-빌드 1903 <br/><br/>Windows 10 - 빌드 1809<br/><br/>Windows Server 2016<br/><br/>Windows Server 2019 | [20.Q1.1](https://download.microsoft.com/download/3/8/9/3893407b-e8aa-4079-8592-735d7dd1c19a/Radeon-Pro-Software-for-Enterprise-GA.exe)(.exe) |


## <a name="driver-installation"></a>드라이버 설치

1. 원격 데스크톱을 통해 각 NVv4 시리즈 VM에 연결합니다.

2. 이전 드라이버 버전을 제거 해야 하는 경우에는 [여기](https://download.microsoft.com/download/4/f/1/4f19b714-9304-410f-9c64-826404e07857/AMDCleanupUtilityni.exe) 에서 이전 버전의 드라이버와 함께 제공 되는 유틸리티를 사용 하지 마세요.

3. 최신 드라이버를 다운로드하여 설치합니다.

4. VM을 다시 부팅합니다.

## <a name="verify-driver-installation"></a>드라이버 설치 확인

디바이스 관리자에서 드라이버 설치를 확인할 수 있습니다. 다음 예제에서는 Azure NVv4 VM에서 Radeon Instinct MI25 카드의 성공적인 구성을 보여줍니다.
<br />
![GPU 드라이버 속성](./media/n-series-amd-driver-setup/device-manager.png)

dxdiag를 사용하여 비디오 RAM을 비롯한 GPU 표시 속성을 확인할 수 있습니다. 다음 예제에서는 Azure NVv4 VM에서 Radeon Instinct MI25 카드의 1/2 파티션을 보여줍니다.
<br />
![GPU 드라이버 속성](./media/n-series-amd-driver-setup/dxdiag-output-new.png)

Windows 10 빌드 1903 이상을 실행하는 경우 dxdiag는 'Display' 탭에 정보를 표시하지 않습니다. 하단에 있는 '모든 정보 저장' 옵션을 사용하면 출력 파일에 AMD MI25 GPU와 관련된 정보가 표시됩니다.

![GPU 드라이버 속성](./media/n-series-amd-driver-setup/dxdiag-details.png)


