---
title: 윈도우 가상 데스크톱에 대 한 GPU 구성 - Azure
description: Windows 가상 데스크톱에서 GPU 가속 렌더링 및 인코딩을 활성화하는 방법.
services: virtual-desktop
author: gundarev
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: denisgun
ms.openlocfilehash: 8b675a78041b68210fa7583510582783c506c720
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/22/2020
ms.locfileid: "81767034"
---
# <a name="configure-graphics-processing-unit-gpu-acceleration-for-windows-virtual-desktop"></a>Windows 가상 데스크톱용 GPU(그래픽 처리 장치) 가속 구성

Windows Virtual Desktop은 향상된 앱 성능과 확장성을 위해 GPU 가속 렌더링 및 인코딩을 지원합니다. GPU 가속은 그래픽 집약적인 앱에서 특히 중요합니다.

이 문서의 지침에 따라 GPU에 최적화된 Azure 가상 컴퓨터를 만들고 호스트 풀에 추가하고 렌더링 및 인코딩에 GPU 가속을 사용하도록 구성합니다. 이 문서에서는 이미 구성된 Windows 가상 데스크톱 테넌트가 있다고 가정합니다.

## <a name="select-a-gpu-optimized-azure-virtual-machine-size"></a>GPU 최적화 Azure 가상 시스템 크기 선택

Azure는 여러 [GPU에 최적화된 가상 시스템 크기를](/azure/virtual-machines/windows/sizes-gpu)제공합니다. 호스트 풀에 적합한 선택은 특정 앱 워크로드, 원하는 사용자 환경 품질 및 비용을 비롯한 여러 요인에 따라 달라집니다. 일반적으로 더 크고 더 유능한 GPU는 주어진 사용자 밀도에서 더 나은 사용자 환경을 제공합니다.

## <a name="create-a-host-pool-provision-your-virtual-machine-and-configure-an-app-group"></a>호스트 풀 만들기, 가상 시스템 프로비전 및 앱 그룹 구성

선택한 크기의 VM을 사용하여 새 호스트 풀을 만듭니다. 지침은 [자습서: Azure 마켓플레이스를 사용하여 호스트 풀 만들기를](/azure/virtual-desktop/create-host-pools-azure-marketplace)참조하십시오.

Windows Virtual Desktop은 다음 운영 체제에서 GPU 가속 렌더링 및 인코딩을 지원합니다.

* 윈도우 10 버전 1511 이상
* Windows Server 2016 이상

또한 앱 그룹을 구성하거나 새 호스트 풀을 만들 때 자동으로 생성되는 기본 데스크톱 앱 그룹("데스크톱 응용 프로그램 그룹")을 사용해야 합니다. 지침은 [자습서: Windows 가상 데스크톱에 대한 앱 그룹 관리를](/azure/virtual-desktop/manage-app-groups)참조하십시오.

## <a name="install-supported-graphics-drivers-in-your-virtual-machine"></a>가상 컴퓨터에 지원되는 그래픽 드라이버 설치

Windows 가상 데스크톱에서 Azure N 시리즈 VM의 GPU 기능을 활용하려면 적절한 그래픽 드라이버를 설치해야 합니다. [지원되는 운영 체제 및 드라이버의](/azure/virtual-machines/windows/sizes-gpu#supported-operating-systems-and-drivers) 지침에 따라 해당 그래픽 공급업체의 드라이버를 수동으로 또는 Azure VM 확장을 사용하여 설치합니다.

Azure에서 배포한 드라이버만 Windows 가상 데스크톱에 대해 지원됩니다. 또한 NVIDIA GPU가 있는 Azure VM의 경우 Windows 가상 데스크톱에서 [NVIDIA GRID 드라이버만](/azure/virtual-machines/windows/n-series-driver-setup#nvidia-grid-drivers) 지원됩니다.

드라이버 설치 후 VM을 다시 시작해야 합니다. 위의 지침의 확인 단계를 사용하여 그래픽 드라이버가 성공적으로 설치되었는지 확인합니다.

## <a name="configure-gpu-accelerated-app-rendering"></a>GPU 가속 앱 렌더링 구성

기본적으로 다중 세션 구성에서 실행되는 앱과 데스크톱은 CPU로 렌더링되며 렌더링에 사용 가능한 GPU를 활용하지 않습니다. GPU 가속 렌더링을 사용하도록 세션 호스트에 대한 그룹 정책을 구성합니다.

1. 로컬 관리자 권한이 있는 계정을 사용하여 VM의 데스크톱에 연결합니다.
2. 시작 메뉴를 열고 "gpedit.msc"를 입력하여 그룹 정책 편집기를 엽니다.
3. 트리를 **컴퓨터 구성** > **관리 템플릿으로** > 이동**Windows 구성 요소** > **원격 데스크톱 서비스** > **원격 데스크톱 세션 호스트** > **원격 세션 환경.**
4. 정책 선택 **모든 원격 데스크톱 서비스 세션에 대한 하드웨어 기본 그래픽 어댑터를 사용하고** 이 정책을 원격 세션에서 GPU 렌더링을 사용하도록 **사용하도록** 설정합니다.

## <a name="configure-gpu-accelerated-frame-encoding"></a>GPU 가속 프레임 인코딩 구성

원격 데스크톱은 원격 데스크톱 클라이언트로 전송하기 위해 앱 및 데스크톱(GPU로 렌더링된 그래픽 또는 CPU)에서 렌더링된 모든 그래픽을 인코딩합니다. 기본적으로 원격 데스크톱은 이 인코딩에 사용할 수 있는 GPU를 활용하지 않습니다. GPU 가속 프레임 인코딩을 사용하도록 세션 호스트에 대한 그룹 정책을 구성합니다. 위의 단계를 계속하십시오.

1. 원격 **데스크톱 연결에 대해 H.264/AVC 444 그래픽 모드의 우선 순위를** 지정하는 정책의 우선 순위를 선택하고 원격 세션에서 H.264/AVC 444 코덱을 강제로 **사용하도록** 설정합니다.
2. 정책 을 선택 **원격 데스크톱 연결에 대 한 H.264/AVC 하드웨어 인코딩을** 구성 하 고 원격 세션에서 AVC/H.264에 대 한 하드웨어 인코딩을 사용 **하도록** 이 정책을 설정 합니다.

    >[!NOTE]
    >Windows Server 2016에서 설정 옵션 항상 **시도하려면** **AVC 하드웨어 인코딩을 선호합니다.**

3. 그룹 정책이 편집되었으므로 그룹 정책 업데이트를 강제로 합니다. 명령 프롬프트를 열고 다음을 입력합니다.

    ```batch
    gpupdate.exe /force
    ```

4. 원격 데스크톱 세션에서 로그아웃합니다.

## <a name="verify-gpu-accelerated-app-rendering"></a>GPU 가속 앱 렌더링 확인

앱이 렌더링에 GPU를 사용하고 있는지 확인하려면 다음 중 한 가지 를 시도해 보십시오.

* NVIDIA GPU가 있는 Azure VM의 경우 `nvidia-smi` [드라이버 설치 확인에](/azure/virtual-machines/windows/n-series-driver-setup#verify-driver-installation) 설명된 대로 이 유틸리티를 사용하여 앱을 실행할 때 GPU 사용률을 확인합니다.
* 지원되는 운영 체제 버전에서는 작업 관리자를 사용하여 GPU 사용률을 확인할 수 있습니다. "성능" 탭에서 GPU를 선택하여 앱이 GPU를 활용하고 있는지 확인합니다.

## <a name="verify-gpu-accelerated-frame-encoding"></a>GPU 가속 프레임 인코딩 확인

원격 데스크톱이 GPU 가속 인코딩을 사용하고 있는지 확인하려면 다음을 수행하십시오.

1. Windows 가상 데스크톱 클라이언트를 사용하여 VM의 데스크톱에 연결합니다.
2. 이벤트 뷰어를 시작하고 다음 노드로 이동: **응용 프로그램 및 서비스 로그** > **마이크로소프트** > **윈도우** > **원격 데스크톱서비스-RdpCoreCDV** > **운영**
3. GPU 가속 인코딩이 사용되는지 확인하려면 이벤트 ID 170을 찾습니다. "AVC 하드웨어 인코더 사용: 1"이 표시되면 GPU 인코딩이 사용됩니다.
4. AVC 444 모드가 사용되는지 확인하려면 이벤트 ID 162를 찾습니다. "AVC 사용 가능: 1 초기 프로필: 2048"이 표시되면 AVC 444가 사용됩니다.

## <a name="next-steps"></a>다음 단계

이 지침은 하나의 세션 호스트(하나의 VM)에서 GPU 가속을 실행하여 실행해야 합니다. 더 큰 호스트 풀에서 GPU 가속을 활성화하기 위한 몇 가지 추가 고려 사항:

* [VM 확장을](/azure/virtual-machines/extensions/overview) 사용하여 여러 VM에서 드라이버 설치 및 업데이트를 간소화하는 것이 좋습니다. [엔비디아 GPU와](/azure/virtual-machines/extensions/hpccompute-gpu-windows) VM에 대 한 엔비디아 GPU 드라이버 확장을 사용 하 여, AMD GPU와 VM에 대 한 AMD GPU 드라이버 확장 (곧) 사용.
* Active Directory 그룹 정책을 사용하여 여러 VM에서 그룹 정책 구성을 단순화하는 것이 좋습니다. Active Directory 도메인에 그룹 정책을 배포하는 방법에 대한 자세한 내용은 [그룹 정책 개체 작업](https://go.microsoft.com/fwlink/p/?LinkId=620889)영역을 참조하십시오.
