---
title: Azure Percept Dev Tools Pack 설치 관리자 개요
description: 개발자 도구 팩 설치 관리자를 사용 하 여 Azure Percept로 고급 개발을 가속화 하는 방법에 대해 자세히 알아보세요.
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: how-to
ms.date: 02/18/2021
ms.custom: template-how-to
ms.openlocfilehash: d684311ef959ac13f3be8bac7ffbbb06a741962a
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102097706"
---
# <a name="dev-tools-pack-installer-overview"></a>개발자 도구 팩 설치 관리자 개요

Dev Tools Pack Installer는 지능형에 지 솔루션을 개발 하는 데 필요한 모든 도구를 설치 하 고 구성 하는 단일 중지 솔루션입니다. 아래 나열 된 소프트웨어 패키지를 이미 설치한 경우 개발자 도구 팩 설치 관리자는 해당 도구가 설치 관리자 소프트웨어 버전과 일치 하도록 해당 패키지를 다시 설치 합니다.

## <a name="mandatory-tools-installed"></a>필수 도구 설치 됨

* [Visual Studio Code](https://code.visualstudio.com/)
* [Python 3.6 (Windows) 또는 3.5 (Linux)](https://www.python.org/)
* [Docker 19.03](https://www.docker.com/)
* [PIP3](https://pip.pypa.io/en/stable/user_guide/)
* [TensorFlow 1.13](https://www.tensorflow.org/)
* [Azure Machine Learning SDK 1.1](https://docs.microsoft.com/python/api/overview/azure/ml/?view=azure-ml-py)

## <a name="optional-tools-available-for-installation"></a>설치에 사용할 수 있는 선택적 도구

* [Nvidia DEEPSTREAM SDK 5](https://developer.nvidia.com/deepstream-sdk) (nvidia 가속기에 대 한 솔루션을 개발 하기 위한 도구 키트)
* [Intel OpenVino toolkit 2020.2](https://docs.openvinotoolkit.org/) (intel accelerator 용 솔루션 개발 도구 키트)
* [Lobe.ai](https://lobe.ai/)  
* [Streamlit](https://www.streamlit.io/)
* [Pytorch 1.4.0 (Windows) 또는 1.2.0 (Linux)](https://pytorch.org/)
* [Miniconda3](https://docs.conda.io/en/latest/miniconda.html)
* [체 이너 5.2](https://chainer.org/)
* [Caffe](https://caffe.berkeleyvision.org/)
* [10.0.130 DA Toolkit](https://developer.nvidia.com/cuda-toolkit)
* [Microsoft Cognitive Toolkit 2.5.1](https://www.microsoft.com/research/product/cognitive-toolkit/?lang=fr_ca)

## <a name="known-issues"></a>알려진 문제

- Docker가 시스템에서 제대로 실행 되지 않는 경우 선택적 Caffe 설치가 실패할 수 있습니다. Caffe를 설치 하려면 개발 도구 팩 설치 관리자를 통해 Caffe 설치를 시도 하기 전에 Docker가 설치 되어 실행 중인지 확인 합니다. 

- 호환 되지 않는 시스템에서 선택적으로 설치 되지 않습니다. 개발 도구 팩 설치 관리자를 통해 [10.0.130 된 Hoda Toolkit](https://developer.nvidia.com/cuda-toolkit) 를 설치 하기 전에 시스템 호환성을 확인 하세요.

## <a name="minimum-requirements"></a>최소 요구 사항

* Docker 최소 요구 사항:

    * Windows:
        * https://docs.docker.com/docker-for-windows/install/#system-requirements

        - Windows 10 64 비트: Pro, Enterprise 또는 교육용 (빌드 16299 이상).

             Windows 10 Home의 경우 Windows Home에서 Docker Desktop 설치를 참조 하세요.
           - Hyper-v 및 컨테이너 Windows 기능을 사용 하도록 설정 해야 합니다.
           - Windows 10에서 클라이언트 Hyper-v를 성공적으로 실행 하려면 다음 하드웨어 필수 구성 요소가 필요 합니다.

              - [두 번째 수준 주소 변환 (SLAT)](https://en.wikipedia.org/wiki/Second_Level_Address_Translation) 을 사용 하는 64 비트 프로세서
              - 4gb 시스템 RAM
              - Bios 수준 하드웨어 가상화 지원은 BIOS 설정에서 사용 하도록 설정 해야 합니다. 자세한 내용은 가상화를 참조 하세요.

        > [!NOTE]
        > Docker는 windows 10 운영 체제에 대 한 Microsoft의 지원 수명 주기를 기반으로 Windows의 Docker 데스크톱을 지원 합니다. 자세한 내용은 [Windows 수명 주기 팩트 시트](https://support.microsoft.com/help/13853/windows-lifecycle-fact-sheet)를 참조 하십시오.

    * Mac:
        * https://docs.docker.com/docker-for-mac/install/#system-requirements
       
        Docker Desktop을 성공적으로 설치 하려면 Mac에서 다음 요구 사항을 충족 해야 합니다.
         
         - Mac 하드웨어는 EPT (확장 페이지 테이블) 및 무제한 모드를 비롯 하 여 Intel의 MMU (메모리 관리 단위) 가상화를 지 원하는 intel 프로세서를 사용 하는 **2010 또는 최신 모델 이어야 합니다**. 터미널에서 다음 명령을 실행 하 여 컴퓨터에이 지원이 있는지 확인할 수 있습니다. ```sysctl kern.hv_support```

        Mac에서 하이퍼바이저 프레임 워크를 지 원하는 경우 명령이 인쇄 ```kern.hv_support: 1``` 됩니다.

         - **Macos는 10.14 이상 버전 이어야** 합니다. 즉, Mojave, Catalina.properties 또는 Big 성입니다. 최신 버전의 macOS로 업그레이드 하는 것이 좋습니다.

        MacOS를 버전 10.15로 업그레이드 한 후 문제가 발생 하는 경우이 버전의 macOS와 호환 되도록 최신 버전의 Docker Desktop을 설치 해야 합니다.

        - RAM 4GB 이상
        - 버전 4.3.30 이전의 VirtualBox는 Docker 데스크톱과 호환 되지 않으므로 설치 되지 않아야 합니다.

        > [!NOTE]
        > Docker는 최신 버전의 macOS에서 Docker 데스크톱을 지원 합니다. 즉, 현재 macOS 릴리스와 이전 두 릴리스가 있습니다. 새 주요 버전의 macOS를 일반적으로 사용할 수 있게 되 면 Docker는 가장 오래 된 버전을 지원 하 고 최신 버전의 macOS (이전 두 릴리스와 함께)를 지원 합니다. Docker Desktop은 현재 macOS Mojave, macOS Catalina.properties 및 macOS Big 성를 지원 합니다.
        > 
        - 설치 관리자는 Apple M1에서 지원 되지 않습니다.

## <a name="instructions"></a>Instructions

1. [Windows](https://go.microsoft.com/fwlink/?linkid=2132187), [Linux](https://go.microsoft.com/fwlink/?linkid=2132186)및 [Mac](https://go.microsoft.com/fwlink/?linkid=2132296)용 개발 도구 팩 설치 관리자를 다운로드 합니다.

1. 플랫폼에 따라 설치 관리자를 시작할 때 약간의 차이점이 있습니다.

    1. Windows의 경우:
    
        1. **개발-도구 팩** -설치 관리자를 클릭 하 여 설치 마법사를 엽니다.
        
    1. Mac:
    
        1. 다운로드 한 후 Dev-Tools-Pack-Installer 파일을 응용 프로그램 폴더로 이동 합니다.
        
        1. **Dev-Tools-Pack-Installer** 를 클릭 하 여 설치 마법사를 엽니다.
        
        1. "미확인 개발자" 보안 대화 상자가 표시 되는 경우:
        
            1. 시스템 기본 설정-> 보안 & 개인 정보-> 일반으로 이동 하 고 "Dev-Tools-Pack-Installer" 옆의 "열기" 단추를 클릭 합니다.
        
            1. 항구에서 전자 아이콘을 다시 클릭 합니다.
        
            1. 보안 대화 상자에서 "열기" 단추를 클릭 합니다.
    
    1. Linux의 경우:
    
        1. 브라우저에서 메시지가 표시 되 면 "저장"을 클릭 하 여 설치 관리자 다운로드를 완료 합니다.
        
        1. 실행 권한을 **. appimage** 파일 메서드 1 (명령줄)에 추가 합니다.
            
            1. Linux 터미널을 엽니다.
            
            1. 터미널에서 다운로드 폴더로 이동 하려면 다음을 입력 합니다.
            
                1. cd ~/Downloads/
                
            1. AppImage 실행 파일을 만들려면 터미널에서 다음을 입력 합니다.
            
                1. chmod + x **Dev-Tools-Pack-Installer. AppImage**
                
            1. 터미널에서 설치 관리자를 실행 하려면 다음을 입력 합니다.
            
                1. ./Dev-Tools-Pack-Installer.AppImage
        
        1. 실행 권한을 **. appimage** 파일 방법 2 (UI)에 추가 합니다.
        
            1. Appimage 파일을 마우스 오른쪽 단추로 클릭 하 고 속성을 선택 합니다.
            
            1. 권한 탭 열기
            
            1. ' 프로그램으로 파일 실행 허용 ' 확인란을 선택 합니다.
            
            1. 속성을 닫고 appimage 파일을 엽니다.

1. **개발 도구 팩 설치 관리자 설치** 페이지에서 **라이선스 보기** 를 클릭 하 여 설치 관리자에 포함 된 각 소프트웨어 패키지의 사용권 계약을 확인 합니다. 사용권 계약 조건에 동의 하면 확인란을 선택 하 고 **다음** 을 클릭 합니다.

    :::image type="content" source="./media/dev-tools-installer/dev-tools-license-agreements.png" alt-text="설치 관리자의 사용권 계약 화면입니다.":::

1. **개인정보 취급** 방침을 클릭 하 여 Microsoft 개인 정보 취급 방침을 검토 합니다. 개인정보 처리 방침 약관에 동의 하 고 Microsoft에 진단 데이터를 보내려면 **예** 를 선택 하 고 **다음** 을 클릭 합니다. 그렇지 않은 경우 **아니요** 를 선택 하 고 **다음** 을 클릭 합니다.

    :::image type="content" source="./media/dev-tools-installer/dev-tools-privacy-statement.png" alt-text="설치 관리자의 개인 정보 취급 방침 계약 화면입니다.":::

1. **구성 요소 구성** 페이지에서 설치 하려는 선택적 도구를 선택 합니다 (필수 도구는 기본적으로 설치 됨).

    1. Azure Percept에 포함 된 Azure Percept Audio SoM을 사용 하는 경우 Intel OpenVino Toolkit 및 Miniconda3를 설치 해야 합니다.

    1. 설치 **를 클릭 하** 여 설치를 계속 합니다.

    :::image type="content" source="./media/dev-tools-installer/dev-tools-configure-components.png" alt-text="사용 가능한 소프트웨어 패키지를 보여 주는 설치 관리자 화면":::

1. 선택한 모든 구성 요소를 성공적으로 설치 하면 마법사가 **설치 마법사 완료** 페이지로 진행 됩니다. **마침** 을 클릭 하 여 설치 관리자를 종료 합니다.

    :::image type="content" source="./media/dev-tools-installer/dev-tools-finish.png" alt-text="설치 관리자 완료 화면.":::

## <a name="docker-status-check"></a>Docker 상태 확인

설치 관리자에서 Docker 데스크톱이 정상 실행 상태 인지 확인 하는 데 알림을 표시 하는 경우 다음 단계를 참조 하세요.

   1. Windows:
   
      1. 시스템 트레이 숨김 아이콘을 확장 합니다.
      
         1. 숨겨진 경우 시스템 트레이 숨김 아이콘을 확장 합니다.

            :::image type="content" source="./media/dev-tools-installer/system-tray.png" alt-text="시스템 트레이.":::
         
         1. Docker 바탕 화면 아이콘에 ' Docker 데스크톱이 실행 중입니다. '가 표시 되는지 확인 합니다.

            :::image type="content" source="./media/dev-tools-installer/docker-status-running.png" alt-text="Docker 상태.":::
         
         1. 시스템 트레이에 위에 나열 된 아이콘이 표시 되지 않으면 시작 메뉴에서 Docker Desktop을 시작 합니다.
         
         1. Docker에서 다시 부팅 하 라는 메시지가 표시 되 면 설치 관리자를 닫고 다시 부팅이 완료 되 고 Docker가 실행 중 상태가 된 후 다시 시작 합니다. 성공적으로 설치 된 타사 응용 프로그램은 모두 검색 되며 자동으로 다시 설치 되지 않습니다.

## <a name="next-steps"></a>다음 단계

Azure Percept 진한 Azure에 대 한 고급 개발을 시작 하려면 [Azure Percept advanced development 리포지토리](https://github.com/microsoft/azure-percept-advanced-development) 를 확인 하세요.