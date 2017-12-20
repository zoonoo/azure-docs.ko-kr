---
title: "X.509 시뮬레이션된 장치를 Azure IoT Hub로 프로비전 | Microsoft Docs"
description: "Azure 빠른 시작 - Azure IoT Hub Device Provisioning Service를 사용하여 X.509 시뮬레이션된 장치 만들기 및 프로비전"
services: iot-dps
keywords: 
author: dsk-2015
ms.author: dkshir
ms.date: 11/08/2017
ms.topic: hero-article
ms.service: iot-dps
documentationcenter: 
manager: timlt
ms.devlang: na
ms.custom: mvc
ms.openlocfilehash: a51debfc80c28cd7d7e271448e30619f2bc72f86
ms.sourcegitcommit: 68aec76e471d677fd9a6333dc60ed098d1072cfc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/18/2017
---
# <a name="create-and-provision-an-x509-simulated-device-using-iot-hub-device-provisioning-service"></a>IoT Hub Device Provisioning 서비스를 사용하여 X.509 시뮬레이션된 장치 만들기 및 프로비전
> [!div class="op_single_selector"]
> * [TPM](quick-create-simulated-device.md)
> * [X.509](quick-create-simulated-device-x509.md)

다음 단계에서는 Windows OS를 실행 중인 개발 컴퓨터에서 X.509 장치를 시뮬레이션하고, 코드 샘플을 사용하여 이 시뮬레이션된 장치를 Device Provisioning Service 및 IoT Hub와 연결하는 방법을 보여 줍니다. 

진행하기 전에 [Azure Portal에서 IoT Hub Device Provisioning Service 설정](./quick-setup-auto-provision.md)에 나와 있는 단계를 완료해야 합니다.

<a id="setupdevbox"></a>
## <a name="prepare-the-development-environment"></a>개발 환경 준비 

1. 컴퓨터에 Visual Studio 2015 또는 [Visual Studio 2017](https://www.visualstudio.com/vs/)이 설치되어 있는지 확인합니다. ‘C++를 사용한 데스크톱 개발’ 워크로드를 Visual Studio 설치에 사용할 수 있도록 해야 합니다.

2. [CMake 빌드 시스템](https://cmake.org/download/)을 다운로드하여 설치합니다.

3. 컴퓨터에 `git`이 설치되어 있고 명령 창에서 액세스할 수 있는 환경 변수에 추가되었는지 확인합니다. 설치할 `git` 도구의 최신 버전은 [Software Freedom Conservancy의 Git 클라이언트 도구](https://git-scm.com/download/)를 참조하세요. 여기에는 로컬 Git 리포지토리와 상호 작용하는 데 사용할 수 있는 명령줄 앱인 **Git Bash**가 포함됩니다. 

4. 명령 프롬프트 또는 Git Bash를 엽니다. 장치 시뮬레이션 샘플 코드에 대한 GitHub 리포지토리를 복제합니다.
    
    ```cmd/sh
    git clone https://github.com/Azure/azure-iot-sdk-c.git --recursive
    ```

5. CMake 빌드 프로세스에 대한 이 GitHub 리포지토리의 로컬 복사본에 폴더를 만듭니다. 

    ```cmd/sh
    cd azure-iot-sdk-c
    mkdir cmake
    cd cmake
    ```

6. 다음 명령을 실행하여 프로비전 클라이언트에 대한 Visual Studio 솔루션을 만듭니다.

    ```cmd/sh
    cmake -Duse_prov_client:BOOL=ON ..
    ```


## <a name="create-a-device-enrollment-entry-in-the-device-provisioning-service"></a>Device Provisioning Service에서 장치 등록 항목 만들기

1. *cmake* 폴더에 생성된 `azure_iot_sdks.sln`이라는 솔루션을 열고 Visual Studio에서 빌드합니다.

2. **Provision\_Samples** 폴더 아래에서 **dice\_device\_enrollment** 프로젝트를 마우스 오른쪽 단추로 클릭하고 **시작 프로젝트로 설정**을 선택합니다. 솔루션을 실행합니다. 출력 창에서 메시지가 표시되면 개별 등록에 대한 `i`를 입력합니다. 출력 창에는 시뮬레이션된 장치에 대해 로컬로 생성된 X.509 인증서가 표시됩니다. *-----BEGIN CERTIFICATE-----*에서 시작하여 *-----END PUBLIC KEY-----*에서 끝나는 출력 문자열을 클립보드로 복사하고, 두 줄을 모두 포함해야 합니다. 
 
3. Windows 컴퓨터에 **_X509testcertificate.pem_**이라는 파일을 만들고, 원하는 편집기에서 이 파일을 열고, 클립보드의 내용을 이 파일에 복사합니다. 파일을 저장합니다. 

4. Azure Portal에 로그인하고, 왼쪽 메뉴에서 **모든 리소스** 단추를 클릭하고, 프로비전 서비스를 엽니다.

4. Device Provisioning Service 요약 블레이드에서 **등록 관리**를 선택합니다. **개별 등록** 탭을 선택하고 맨 위에서 **추가** 단추를 클릭합니다. 

5. **등록 목록 항목 추가** 아래에 다음 정보를 입력합니다.
    - ID 증명 *메커니즘*으로 **X.509**를 선택합니다.
    - *인증서 .pem 또는 .cer 파일* 아래에서, *파일 탐색기* 위젯을 사용하여 이전 단계에서 만든 **_X509testcertificate.pem_** 인증서 파일을 선택합니다.
    - 필요에 따라 다음 정보를 입력합니다.
        - 프로비전 서비스와 연결된 IoT Hub를 선택합니다.
        - 고유한 장치 ID를 입력합니다. 장치 이름을 지정할 때 중요한 데이터가 포함되지 않도록 합니다. 
        - 장치에 대해 원하는 초기 구성으로 **초기 장치 쌍 상태**를 업데이트합니다.
    - 완료되면 **저장** 단추를 클릭합니다. 

    ![포털 블레이드에 X.509 장치 등록 정보 입력](./media/quick-create-simulated-device-x509/enter-device-enrollment.png)  

   등록에 성공하면 X.509 장치가 *개별 등록* 탭의 *등록 ID* 열에 **riot-device-cert**로 표시됩니다. 



<a id="firstbootsequence"></a>
## <a name="simulate-first-boot-sequence-for-the-device"></a>장치에 대한 첫 번째 부팅 시퀀스 시뮬레이션

1. Azure Portal에서 Device Provisioning 서비스에 대한 **개요** 블레이드를 선택하고 **_ID 범위_** 값을 기록해 둡니다.

    ![포털 블레이드에서 DPS 끝점 정보 추출](./media/quick-create-simulated-device-x509/extract-dps-endpoints.png) 

2. 컴퓨터의 Visual Studio에서, **Provision\_Samples** 폴더 아래에 있는 **prov\_dev\_client\_sample**이라는 샘플 프로젝트로 이동하여 **prov\_dev\_client\_sample.c** 파일을 엽니다.

3. _ID 범위_ 값을 `scope_id` 변수에 할당합니다. 

    ```c
    static const char* scope_id = "[ID Scope]";
    ```

4. **prov\_dev\_client\_sample** 프로젝트를 마우스 오른쪽 단추로 클릭하고 **시작 프로젝트로 설정**을 선택합니다. 샘플을 실행합니다. 장치를 부팅하고 IoT Hub 정보를 얻기 위해 Device Provisioning Service에 연결하는 과정을 시뮬레이션하는 메시지를 확인합니다. 허브가 성공적으로 등록되었다는 *서비스에서 등록 정보 수신: yourhuburl!* 메시지를 살펴봅니다. 메시지가 표시되면 창을 닫습니다.

5. 포털에서 프로비전 서비스에 연결된 IoT 허브로 이동하여 **Device Explorer** 블레이드를 엽니다. 시뮬레이션된 X.509 장치가 허브에 성공적으로 프로비전되면 장치 ID가 **Device Explorer** 블레이드에 표시되고 *상태*가 **사용**으로 표시됩니다. 샘플 장치 응용 프로그램을 실행하기 전에 블레이드를 이미 열어 놓은 경우 화면 상단의 **새로 고침** 단추를 클릭해야 합니다. 

    ![장치가 IoT Hub에 등록됨](./media/quick-create-simulated-device/hub-registration.png) 

> [!NOTE]
> 장치에 대한 등록 항목의 기본값으로부터 *초기 장치 쌍 상태*를 변경한 경우, 허브에서 원하는 쌍 상태를 가져와서 그에 맞게 작동할 수 있습니다. 자세한 내용은 [IoT Hub의 장치 쌍 이해 및 사용](../iot-hub/iot-hub-devguide-device-twins.md)을 참조하세요.
>


## <a name="clean-up-resources"></a>리소스 정리

장치 클라이언트 샘플을 계속해서 작업하고 탐색할 계획인 경우 이 빠른 시작에서 만든 리소스를 정리하지 마세요. 계속하지 않으려는 경우 다음 단계를 사용하여 이 빠른 시작에서 만든 모든 리소스를 삭제합니다.

1. 컴퓨터에서 장치 클라이언트 샘플 출력 창을 닫습니다.
1. Azure Portal의 왼쪽 메뉴에서 **모든 리소스**를 클릭한 다음 사용자의 Device Provisioning Service를 선택합니다. **모든 리소스** 블레이드 위쪽에서 **삭제**를 클릭합니다.  
1. Azure Portal의 왼쪽 메뉴에서 **모든 리소스**를 클릭한 다음 사용자의 IoT Hub를 선택합니다. **모든 리소스** 블레이드 위쪽에서 **삭제**를 클릭합니다.  

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 Windows 컴퓨터에 시뮬레이션된 X.509 장치를 만들고 Azure IoT Hub Device Provisioning 서비스를 사용하여 IoT Hub에 프로비전했습니다. 장치 프로비전에 대해 자세히 알아보려면 Azure Portal에서 Device Provisioning Service 설치에 대한 자습서를 살펴보세요. 

> [!div class="nextstepaction"]
> [Azure IoT Hub Device Provisioning 서비스 자습서](./tutorial-set-up-cloud.md)
