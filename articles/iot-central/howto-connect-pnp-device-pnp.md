---
title: IoT 플러그 앤 플레이 장치를 IoT Central에 연결 합니다. Microsoft Docs
description: 장치 개발자는 Visual Studio Code 사용 하 여 IoT Central에 연결 하는 IoT 플러그 앤 플레이 장치를 만들고 테스트 하는 방법에 대해 알아봅니다.
author: dominicbetts
ms.author: dobett
ms.date: 07/10/2019
ms.topic: conceptual
ms.custom: mvc
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 6eb73c1c2b768d82143890415702950905f261f6
ms.sourcegitcommit: 4b8a69b920ade815d095236c16175124a6a34996
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/23/2019
ms.locfileid: "69997238"
---
# <a name="use-visual-studio-code-to-create-an-iot-plug-and-play-device-that-connects-to-iot-central"></a>Visual Studio Code를 사용 하 여 IoT Central에 연결 하는 IoT 플러그 앤 플레이 장치를 만듭니다.

이 방법 가이드에서는 다음 방법을 보여 줍니다.

* 운영자로 서 Azure IoT Central 응용 프로그램에서 실제 장치를 추가 하 고 구성 합니다.

* 장치 개발자로 서 Visual Studio Code를 사용 하 여 IoT Central 응용 프로그램에 연결 하는 [IoT 플러그 앤 플레이](../iot-pnp/overview-iot-plug-and-play.md) 장치를 만들 수 있습니다.

[장치 기능 모델](./concepts-architecture-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) 을 사용 하 여 IoT Central에 연결 하는 장치를 정의 합니다. 이 가이드에서는 환경 센서 장치를 정의 하는 모델을 사용 합니다.

장치 개발자는 모델을 사용 하 여 장치 클라이언트 코드를 생성 하 고 작성기는 모델을 사용 하 여 IoT Central에서 [장치 템플릿을 만듭니다](./howto-set-up-template-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) . 이 모델은 장치와 IoT Central 응용 프로그램 간의 계약 역할을 합니다.

이 가이드의 섹션에서는 생성 된 코드를 빌드하는 방법을 설명 합니다 .이 섹션에서는 Windows를 사용 하는 것으로 가정 합니다.

이 가이드에서는 다음 작업 방법을 배웁니다.

* IoT Central에서 장치 기능 모델을 장치 템플릿으로 가져오기
* 장치 원격 분석을 표시 하는 대시보드를 템플릿에 추가 합니다.
* 템플릿에서 실제 장치 추가
* 장치 기능 모델을 Visual Studio Code로 가져오기
* 모델에서 C 장치 클라이언트 응용 프로그램 생성
* C 장치 클라이언트 응용 프로그램을 빌드하고 IoT Central에 연결

## <a name="prerequisites"></a>전제 조건

이 가이드의 장치 코드를 테스트 하려면 **미리 보기** 응용 프로그램 템플릿에서 만든 IoT Central 응용 프로그램이 필요 합니다. 사용할 IoT Central 응용 프로그램이 아직 없는 경우 빠른 시작 [Azure IoT Central 응용 프로그램 만들기 (미리 보기 기능)](./quick-deploy-iot-central-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)를 완료 합니다.

이 가이드의 장치 기능 모델로 작업 하려면 다음이 필요 합니다.

* [Visual Studio Code](https://code.visualstudio.com/download): Visual Studio Code는 여러 플랫폼에 사용할 수 있습니다.
* Visual Studio Code에 대 한 Azure IoT 장치 워크 벤치 확장. 다음 단계에 따라 Azure IoT Device Workbench 확장을 VS Code에 설치합니다.

    1. VS Code에서 **확장** 탭을 선택합니다.
    1. **Azure IoT Device Workbench**를 검색합니다.
    1. **설치**를 선택합니다.

    > [!NOTE]
    > 확장에 있는 코드 생성기의 현재 버전은 **Geopoint** 및 **Vector** 스키마 형식이 나 **가속**, **속도**및 **위치** 의미 체계 형식을 지원 하지 않습니다. 이러한 스키마 및 의미 체계 형식은 IoT Central 지원 됩니다.

    > [!NOTE]
    > IoT Central을 사용하려면 디바이스 기능 모델의 모든 인터페이스가 동일한 파일에 인라인으로 정의되어 있어야 합니다.

이 가이드의 Windows에서 생성 된 C 코드를 빌드하려면 다음이 필요 합니다.

* [Visual Studio(Community, Professional 또는 Enterprise)](https://visualstudio.microsoft.com/downloads/) - Visual Studio를 설치할 때 **NuGet 패키지 관리자** 구성 요소와 **C++를 사용한 데스크톱 개발** 워크로드를 포함해야 합니다.
* [Git](https://git-scm.com/download)
* [CMake](https://cmake.org/download/) - **CMake**를 설치할 때 **시스템 경로에 CMake 추가** 옵션을 선택합니다.
* Azure IoT C SDK의 로컬 복사본:

    ```cmd
    git clone https://github.com/Azure/azure-iot-sdk-c.git --recursive -b public-preview
    ```

이 방법의 끝에 있는 장치 우선 지침을 따르려면 다음을 설치 해야 합니다.

* [Node.js](https://nodejs.org)
* [Dps ssh-keygen 도구](https://www.npmjs.com/package/dps-keygen):

    ```cmd/sh
    npm i -g dps-keygen
    ```

## <a name="create-device-template"></a>장치 템플릿 만들기

복제 한 Azure IoT C SDK가 포함 된 **pnp_app** **폴더에서** 폴더를 만듭니다. GitHub에서 [capabilitymodel](https://raw.githubusercontent.com/Azure/IoTPlugandPlay/master/samples/EnvironmentalSensorInline.capabilitymodel.json) 장치 기능 모델을 다운로드 하 고 파일을 **pnp_app** 폴더에 저장 합니다. 의 두 인스턴스를 사용자 `<YOUR_COMPANY_NAME_HERE>` 의 이름으로 바꿉니다. a-z, A-Z, 0-9 및 밑줄 문자만 사용합니다. 장치 기능 모델을 고유 하 게 식별 하 `"@id"` 는 필드의 전체 값을 기록해 둡니다. 나중에 필요 합니다. 이 모델에는 두 가지 인터페이스가 있습니다.

* **DeviceManagement** common 인터페이스입니다.
* 환경 **Al센서** 사용자 지정 인터페이스입니다.

IoT Central에서 환경 센서 장치에 대 한 장치 템플릿을 만들려면 다음을 수행 합니다.

1. **장치 템플릿** 페이지로 이동 하 고 **+ 새로 만들기**를 선택 합니다. 그런 다음 **사용자 지정**을 선택 합니다.

1. **환경 센서** 를 장치 템플릿 이름으로 입력 합니다.

1. **기능 모델 가져오기**를 선택 합니다. 그런 다음 사용자가 만든 **capabilitymodel** 를 찾아 **열기**를 선택 합니다. **장치 정보** 및 **환경 센서**의 두 인터페이스가 **환경 센서 기능 모델**에 표시 됩니다.

1. **보기** 를 선택 하 고 **장치를 시각화**합니다.

1. 대시보드에 추가할 수 있는 필드 목록에서 두 가지 **원격 분석** 값인 **습도** 와 **온도** 를 선택 하 고 **결합**을 선택 합니다. 그런 다음, **저장**을 선택합니다.

이제 **환경 센서** 모델을 사용 하 고 장치에서 원격 분석을 표시 하는 간단한 대시보드를 사용 하는 장치 템플릿이 있습니다.

## <a name="publish-the-template-and-add-a-real-device"></a>템플릿을 게시 하 고 실제 장치를 추가 합니다.

템플릿을 게시 하 고 실제 장치를 추가 하려면 **장치 템플릿** 페이지로 이동 하 여 **환경 센서** 장치 템플릿을 선택 합니다. **게시**를 선택 하 고, 정보를 검토 하 고, **게시**를 선택 합니다.

클라이언트 응용 프로그램을 연결 하기 전에 장치 페이지에서 장치를 추가 하 고 연결 정보를 **가져와야 합니다.**

1. **장치** 페이지로 이동 하 고 **환경 센서**를 선택 합니다. **장치** 페이지에서 응용 프로그램에 연결할 수 있는 장치를 관리할 수 있습니다.

1. 실제 장치를 추가 하려면 **+ 새로 만들기**를 선택 하 고 장치 ID를 **sensor01**로 변경 하 고 **만들기**를 선택 합니다. **시뮬레이트된** 이 **해제**되어 있는지 확인 합니다.

1. 장치 목록에서 **환경 센서** 장치를 선택 합니다. 그런 다음 **연결**을 선택 합니다.

1. **범위 ID**, **장치 Id**및 **기본 키**를 적어둡니다. 그런 다음 **닫기**를 선택 합니다.

## <a name="generate-a-device-client-application"></a>장치 클라이언트 응용 프로그램 생성

Visual Studio Code를 사용 하 여 장치 기능 모델에서 기본 장치 클라이언트 응용 프로그램을 생성 합니다.

1. Visual Studio Code를 시작 하 고 **pnp_app** 폴더를 엽니다.

1. **Ctrl+Shift+P**를 사용하여 명령 팔레트를 엽니다.

1. **플러그 앤 플레이** 를 입력 하 고 **장치 코드 스텁 생성**을 선택 합니다.

1. 환경 **alcapabilitymodel** 장치 기능 모델 파일을 선택 합니다.

1. 프로젝트 이름으로 **sensor_app** 을 입력 합니다.

1. 언어로 **ANSI C**를 선택합니다.

1. 대상으로 **CMake 프로젝트**를 선택합니다.

1. 연결 방법으로 **DPS(Device Provisioning Service) 대칭 키를 통해**를 선택합니다.

Visual Studio Code **sensor_app** 폴더에 생성 된 C 코드를 사용 하 여 새 창을 엽니다.

## <a name="add-connection-details-to-the-device-client"></a>장치 클라이언트에 연결 정보 추가

장치 클라이언트에 연결 정보를 추가 하려면 생성 된 코드가 포함 된 폴더에서 **주. c** 를 엽니다.

1. 을 `[DPS Id Scope]` 이전에 적어 둔 **범위 ID** 값으로 바꿉니다.

1. 이전 `[DPS symmetric key]` 에 기록해 둔 **기본 키** 값으로 대체 합니다.

1. 을 `[device registration Id]` 이전에 적어 둔 **장치 ID** 값으로 바꿉니다.

1. 변경 내용을 저장합니다.

## <a name="build-and-run-the-client"></a>클라이언트를 빌드하고 실행 합니다.

클라이언트를 빌드하고 실행 하려면 Azure IoT C SDK에 대해 빌드를 사용자 지정 해야 합니다.

1. **Azure-iot-c** 폴더의 루트에 있는 **cmakelists .txt** 파일을 엽니다.

1. 새 클라이언트 앱을 빌드에 포함 하려면이 파일의 끝에 다음 줄을 추가 합니다.

    ```txt
    add_subdirectory(pnp_app/sensor_app)
    ```

1. 변경 내용을 저장합니다.

1. 명령 프롬프트를 열고 **azure-iot-c** 폴더로 이동 합니다.

1. 응용 프로그램을 빌드하려면 다음 명령을 실행 합니다.

    ```cmd
    mkdir cmake
    cd cmake
    cmake .. -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON -Dskip_samples:BOOL=ON
    cmake --build . -- /m /p:Configuration=Release
    ```

1. 응용 프로그램을 실행 하려면 동일한 명령 프롬프트에서 다음 명령을 실행 합니다.

    ```cmd
    pnp_app\sensor_app\Release\sensor_app.exe
    ```

## <a name="view-the-telemetry"></a>원격 분석 보기

몇 분 후에 IoT Central 응용 프로그램의 장치 대시보드의 장치에서 원격 분석을 볼 수 있습니다.

## <a name="connect-device-first"></a>장치 연결-우선

[연결](concepts-connectivity-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)에 설명 된 대로 장치 우선 연결을 통해 IoT 플러그 앤 플레이 장치를 연결할 수 있습니다. 검색 프로세스는 다음 순서를 따릅니다.

1. 는 이미 IoT Central 응용 프로그램에 게시 된 경우 장치 템플릿에 연결 합니다.

1. 게시 되 고 인증 된 기능 모델의 [공용 리포지토리에서](https://aka.ms/ACFI) 기능 모델을 페치합니다.

위에서 참조 된 Visual Studio Code 및 **장치 코드 스텁 생성** 명령을 사용 하 여 다음 단계를 수행 하 여 장치를 먼저 연결 하 고 게시 된 장치 기능 모델을 공용 리포지토리에서 IoT Central에 자동으로 가져올 수 있습니다.

1. 공용 리포지토리에 게시 된 기존 장치 기능 모델을 사용 합니다. 이 모델의 URN을 기록 하려면 전체 장치 기능 모델이 필요 합니다.

1. 위의 단계에 따라 Visual Studio Code를 사용 하 고 장치 코드를 생성 하는 [장치 클라이언트 응용 프로그램을 생성](#generate-a-device-client-application) 합니다.

1. IoT Central 응용 프로그램에서 **관리** 탭으로 이동 하 여 **장치 연결** 섹션을 선택 합니다. 응용 프로그램에 대 한 **범위 ID** 및 **기본 키** 값을 기록해 둡니다.

    > [!NOTE]
    > 이 페이지에 표시 되는 **기본 키는** 응용 프로그램에 대 한 여러 장치 키를 생성 하는 데 사용할 수 있는 그룹 공유 액세스 서명입니다.

1. [DPS ssh-keygen](https://www.npmjs.com/package/dps-keygen) 도구를 사용 하 여 이전에 기록해 둔 기본 키에서 장치 키를 생성 합니다. 장치 키를 생성 하려면 다음 명령을 실행 합니다.

    ```cmd/sh
    dps-keygen -mk:<Primary_Key> -di:<device_id>
    ```

1. 이전 섹션의 단계에 따라 [장치 클라이언트에 연결 정보를 추가](#add-connection-details-to-the-device-client) 하 여 **범위 ID**, **기본 키**및 **장치 ID**를 추가 합니다.

1. 이전 섹션의 단계에 따라 클라이언트를 [빌드하고 실행](#build-and-run-the-client)합니다.

1. 이제 장치가 IoT Central 응용 프로그램에 연결 되 고 장치 기능 모델을 공용 리포지토리의 장치 템플릿으로 자동으로 가져옵니다.

## <a name="next-steps"></a>다음 단계

이제 실제 장치를 IoT Central에 연결 하는 방법을 배웠으므로 제안 된 다음 단계는 [장치 템플릿 설정](howto-set-up-template-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) 에서 장치 템플릿에 대 한 자세한 정보를 확인 하는 것입니다.
