---
title: Windows 가상 데스크톱 미리 보기-Azure에 대 한 GPU 구성
description: GPU 가속 렌더링 및 Windows 가상 데스크톱 미리 보기에서 인코딩을 사용 하도록 설정 하는 방법.
services: virtual-desktop
author: gundarev
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 05/06/2019
ms.author: denisgun
ms.openlocfilehash: a6a67c89253a1b16f9266d7917655d1b1104022e
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65159572"
---
# <a name="configure-graphics-processing-unit-gpu-acceleration-for-windows-virtual-desktop-preview"></a>그래픽 처리 유닛 (GPU) 가속 Windows 가상 데스크톱 미리 보기에 대 한 구성

Windows 가상 데스크톱 미리 보기에는 GPU 가속 렌더링 및 향상 된 앱 성능 및 확장성에 대 한 인코딩을 지원 합니다. GPU 가속은 그래픽 위주 응용 프로그램에 특히 중요 합니다.

인코딩 및 렌더링에 대 한 GPU 가속을 사용 하도록 GPU 액세스에 최적화 된 Azure 가상 머신을 만들 호스트 풀에 추가 하 고 구성 하려면이 문서의 지침을 따릅니다. 이 문서에서는 이미 구성 된 Windows 가상 데스크톱 테 넌 트를 가정 합니다.

## <a name="select-a-gpu-optimized-azure-virtual-machine-size"></a>GPU 액세스에 최적화 된 Azure 가상 머신 크기를 선택 합니다.

Azure는 다양 한 제공 [GPU 최적화 가상 머신 크기](/azure/virtual-machines/windows/sizes-gpu)합니다. 호스트 풀에 적합 다양 한 사용자 특정 앱 워크 로드, 사용자 환경 및 비용의 원하는 품질 등의 요인에 따라 달라 집니다. 일반적으로 크고 더욱 강력한 Gpu에 지정 된 사용자 밀도 더 나은 사용자 환경을 제공합니다.

## <a name="create-a-host-pool-provision-your-virtual-machine-and-configure-an-app-group"></a>호스트 그룹을 만들고 가상 컴퓨터를 프로 비전, 앱 그룹 구성

선택한 크기의 VM을 사용 하 여 새 호스트 풀을 만듭니다. 자세한 내용은 [자습서: Azure Marketplace를 사용 하 여 호스트 풀을 만들](/azure/virtual-desktop/create-host-pools-azure-marketplace)합니다.

Windows 가상 데스크톱 미리 보기는 GPU 가속 렌더링에서 및 인코딩에 다음 운영 체제를 지원 합니다.

* Windows 10 버전 1511 이상
* Windows Server 2016 이상

또한 앱 그룹을 구성 하거나 기본 데스크톱 앱 그룹 ("데스크톱 응용 프로그램 그룹" 이라는) 새 호스트 풀을 만들 때 자동으로 만들어지는 사용 해야 합니다. 자세한 내용은 [자습서: Windows 가상 데스크톱 미리 보기에 대 한 앱 그룹을 관리](/azure/virtual-desktop/manage-app-groups)합니다.

>[!NOTE]
>Windows 가상 데스크톱 미리 보기 "데스크톱" 앱 그룹 유형 호스트 GPU 사용 풀만 지원합니다. 앱 그룹 "RemoteApp" 유형의 호스트 GPU 사용 풀에 대 한 지원 되지 않습니다.

## <a name="install-supported-graphics-drivers-in-your-virtual-machine"></a>가상 컴퓨터에서 지원 되는 그래픽 드라이버를 설치 합니다.

Windows 가상 데스크톱 미리 보기에서 Azure N 시리즈 Vm의 GPU 기능을 사용 하려면 NVIDIA 그래픽 드라이버를 설치 해야 합니다. 지침을 따르세요 [Windows를 실행 하는 N 시리즈 Vm에 설치 하는 NVIDIA GPU 드라이버](/azure/virtual-machines/windows/n-series-driver-setup) 하거나 수동으로 드라이버를 설치 하를 사용 하 여 또는 합니다 [NVIDIA GPU 드라이버 확장](/azure/virtual-machines/extensions/hpccompute-gpu-windows)합니다.

에서만 [NVIDIA GRID 드라이버](/azure/virtual-machines/windows/n-series-driver-setup#nvidia-grid-drivers) distributed Azure에서 Windows 가상 데스크톱 미리 보기에 대해 지원 됩니다.

드라이버 설치 후 VM을 다시 시작할 필요합니다. 그래픽 드라이버 성공적으로 설치 되었는지 확인 하려면 위의 지침에 있는 확인 단계를 따르세요.

## <a name="configure-gpu-accelerated-app-rendering"></a>GPU 가속 응용 프로그램 렌더링 구성

기본적으로 앱 및 다중 세션 구성에서 실행 되는 데스크톱 CPU를 사용 하 여 렌더링 되 고 렌더링에 대 한 사용 가능한 Gpu를 활용 하지 않습니다. GPU 가속 렌더링을 사용 하도록 설정 하려면 세션 호스트에 대 한 그룹 정책을 구성 합니다.

1. 로컬 관리자 권한이 있는 계정을 사용 하 여 VM의 바탕 화면에 연결 합니다.
2. 시작 메뉴 및 형식 "gpedit.msc" 그룹 정책 편집기를 엽니다.
3. 트리를 탐색 **컴퓨터 구성** > **관리 템플릿** > **Windows 구성 요소**  >   **원격 데스크톱 서비스** > **원격 데스크톱 세션 호스트** > **원격 세션 환경을**합니다.
4. 정책 선택 **하드웨어 기본 그래픽 어댑터를 사용 하 여 모든 원격 데스크톱 서비스 세션에 대 한** 이 정책을 설정 하 고 **사용** 원격 세션에서 GPU 렌더링을 사용 하도록 설정 합니다.

## <a name="configure-gpu-accelerated-frame-encoding"></a>GPU 가속 프레임 인코딩을 구성합니다

원격 데스크톱에 대 한 원격 데스크톱 클라이언트에 전송 (CPU 또는 GPU를 사용 하 여 렌더링) 여부 앱 및 데스크톱에서 렌더링 하는 모든 그래픽을 인코딩합니다. 기본적으로 원격 데스크톱이 인코딩에 사용 하는 사용 가능한 Gpu 활용 하지 않습니다. GPU 가속 프레임 인코딩을 사용 하려면 세션 호스트에 대 한 그룹 정책을 구성 합니다. 위의 단계를 계속 합니다.

1. 정책 선택 **원격 데스크톱 연결에 대 한 우선 순위를 지정 H.264/AVC 444 그래픽 모드** 이 정책을 설정 하 고 **Enabled** H.264/AVC 444 코덱을 원격 세션에서 적용할 합니다.
2. 정책을 선택 **원격 데스크톱 연결에 대 한 구성 H.264/AVC 하드웨어 인코딩을** 이 정책을 설정 하 고 **Enabled** 하드웨어 원격 세션에서 AVC/H.264 인코딩에 사용할 수 있도록 합니다.

    >[!NOTE]
    >Windows Server 2016에서 옵션 설정 **AVC 하드웨어 인코딩 선호** 하 **항상**합니다.

3. 그룹 정책을 편집한, 이제는 그룹 정책 업데이트를 강제 합니다. 명령 프롬프트를 열고 입력 합니다.

    ```batch
    gpupdate.exe /force
    ```

4. 원격 데스크톱 세션에서 로그 아웃 합니다.

## <a name="verify-gpu-accelerated-app-rendering"></a>GPU 가속 응용 프로그램 렌더링 확인

앱 사용 하는 GPU 렌더링에 대 한을 확인 하려면 다음 중 하나를 시도 합니다.

* 사용 하 여는 `nvidia-smi` 에 설명 된 대로 유틸리티 [드라이버 설치 확인](/azure/virtual-machines/windows/n-series-driver-setup#verify-driver-installation) 앱을 실행 하는 경우 GPU 사용률에 대 한 확인 합니다.
* 지원 되는 운영 체제 버전에서는 GPU 사용률에 대 한 확인 하려면 작업 관리자를 사용할 수 있습니다. 앱 GPU를 활용 하는 여부를 확인 하려면 "Performance" 탭에 GPU를 선택 합니다.

## <a name="verify-gpu-accelerated-frame-encoding"></a>GPU 가속 프레임 인코딩을 확인 합니다.

GPU 가속 인코딩이 가능 원격 데스크톱을 사용 하는지 확인 합니다.

1. Windows 가상 데스크톱 클라이언트를 사용 하 여 VM의 바탕 화면에 연결 합니다.
2. 이벤트 뷰어를 시작 하 고 다음 노드로 이동 합니다. **Applications and Services Logs** > **Microsoft** > **Windows** > **RemoteDesktopServices-RdpCoreTS**  >  **운영**
3. GPU 가속 인코딩이 사용 되는 경우를 확인 하려면 이벤트 ID 170를 찾습니다. 표시 되 면 "AVC 하드웨어 인코더를 사용 하도록 설정 합니다. GPU 인코딩 후 1 "이 사용 됩니다.
4. AVC 444 모드 사용 되는 경우 결정, 이벤트 ID 162를 찾습니다. 표시 되 면 "AVC 사용 가능 합니다. 초기 프로필 1: 2048"다음 AVC 444가 사용 됩니다.

## <a name="next-steps"></a>다음 단계

이러한 지침은 있어야 있습니다 실행 단일 세션 호스트 VM에서 GPU 가속을 사용 하 여 합니다. GPU 가속 큰 호스트 풀에서 사용 하기 위한 몇 가지 추가 고려 사항:

* 사용을 고려 합니다 [NVIDIA GPU 드라이버 확장](/azure/virtual-machines/extensions/hpccompute-gpu-windows) Vm의 여러 드라이버 설치 및 업데이트를 단순화 하기 위해.
* Active Directory 그룹 정책을 사용 하 여 Vm의 여러 그룹 정책 구성을 단순화 하는 것이 좋습니다. Active Directory 도메인에서 그룹 정책을 배포 하는 방법에 대 한 내용은 [그룹 정책 개체를 사용 하 여 작업](https://go.microsoft.com/fwlink/p/?LinkId=620889)합니다.
