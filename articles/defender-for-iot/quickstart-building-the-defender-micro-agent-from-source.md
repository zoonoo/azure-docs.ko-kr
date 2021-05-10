---
title: '빠른 시작: 소스 코드에서 Defender 마이크로 에이전트 빌드(미리 보기)'
description: 이 빠른 시작에서는 배포를 사용자 지정하는 데 사용할 수 있는 인프라를 포함하는 마이크로 에이전트에 대해 알아봅니다.
ms.date: 1/18/2021
ms.topic: quickstart
ms.openlocfilehash: a3a8f82989d6abbaf2657e4b45638c77b3b2f704
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/05/2021
ms.locfileid: "106384600"
---
# <a name="quickstart-build-the-defender-micro-agent-from-source-code-preview"></a>빠른 시작: 소스 코드에서 Defender 마이크로 에이전트 빌드(미리 보기)

마이크로 에이전트는 배포를 사용자 지정하는 데 사용할 수 있는 인프라를 포함합니다. 사용 가능한 구성 매개 변수 목록을 보려면 `configs/LINUX_BASE.conf` 파일을 확인합니다.

단일 배포의 경우 기본 `.conf` 파일을 수정합니다. 

둘 이상의 배포가 필요한 경우 기본 구성에서 상속하고 해당 값을 재정의할 수 있습니다. 

값을 재정의하려면 다음을 수행합니다.

1. 새 `.dist` 파일을 만듭니다.

1. 위쪽에 `CONF_DEFINE_BASE(${g_plat_config_path} LINUX_BASE.conf)`를 추가합니다.
 
1. 필요한 모든 항목에 대한 새 값을 정의합니다. 예를 들면 다음과 같습니다. 

    `set(ASC_LOW_PRIORITY_INTERVAL 60*60*24)` 

1. 빌드할 때 `.dist` 파일에 참조를 제공합니다. 예를 들면 다음과 같습니다. 

    `cmake -DCMAKE_BUILD_TYPE=Debug -Dlog_level=DEBUG -Dlog_level_cmdline:BOOL=ON -DIOT_SECURITY_MODULE_DIST_TARGET=UBUNTU1804 ..` 

## <a name="prerequisites"></a>사전 요구 사항

1. Defender for IoT 소스 코드에 대한 액세스를 요청하려면 계정 관리자에게 문의하세요.
 
1. 소스 코드를 디스크의 폴더에 복제하거나 압축을 풉니다.

1. 해당 디렉터리로 이동합니다.

1. 다음 코드를 사용하여 하위 모듈을 끌어옵니다.

    ```bash
    git submodule update --init
    ```
    
1. 다음으로 다음 코드를 사용하여 Azure IoT SDK에 대한 하위 모듈을 끌어옵니다. 

    ```bash
    git -C deps/azure-iot-sdk-c/ submodule update –init
    ```
 

1. 실행 권한을 추가하고 개발자 환경 설치 스크립트를 실행합니다.

    ```bash
    chmod +x scripts/install_development_environment.sh && ./scripts/install_development_environment.sh 
    ```

1. 빌드 출력에 대한 디렉터리를 만듭니다. 

    ```bash
    mkdir cmake 
    ```

1. (선택 사항) [VSCode](https://code.visualstudio.com/download ) 다운로드 및 설치 

1. (선택 사항) VSCode에 대한 [C/C++ 확장](https://code.visualstudio.com/docs/languages/cpp )을 설치합니다. - 없음

## <a name="baseline-configuration-signing"></a>기준 구성 서명 

에이전트는 기본적으로 변조를 완화하기 위해 디스크에 배치된 구성 파일의 신뢰성을 확인합니다.

전처리기 플래그 `ASC_BASELINE_CONF_SIGN_CHECK_DISABLE`을 정의하여 이 프로세스를 중지할 수 있습니다.

프로덕션 환경에 대한 서명 확인을 해제하지 않는 것이 좋습니다. 

프로덕션 시나리오에 대해 다른 구성이 필요한 경우 Defender for IoT 팀에 문의하세요. 

## <a name="building-the-defender-iot-micro-agent"></a>Defender IoT 마이크로 에이전트 빌드 

1. VSCode를 사용하여 디렉터리 열기 

1. `cmake` 디렉터리로 이동합니다. 

1. 다음 명령을 실행합니다. 

    ```bash
    cmake -DCMAKE_BUILD_TYPE=Debug -Dlog_level=DEBUG -Dlog_level_cmdline:BOOL=ON -DIOT_SECURITY_MODULE_DIST_TARGET<the appropriate distro configuration file name> .. 
    
    cmake --build . -- -j${env:NPROC}
    ```

    예를 들면 다음과 같습니다. 

    ```bash
    cmake -DCMAKE_BUILD_TYPE=Debug -Dlog_level=DEBUG -Dlog_level_cmdline:BOOL=ON -DIOT_SECURITY_MODULE_DIST_TARGETUBUNTU1804 ..
    
    cmake --build . -- -j${env:NPROC}
    ```

## <a name="next-steps"></a>다음 단계

[Azure Defender for IoT 솔루션을 구성합니다](quickstart-configure-your-solution.md).
