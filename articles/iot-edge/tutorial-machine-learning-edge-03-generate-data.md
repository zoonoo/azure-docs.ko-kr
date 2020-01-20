---
title: '자습서: 시뮬레이션된 디바이스 데이터 생성 - Azure IoT Edge의 Machine Learning'
description: 이 자습서에서는 나중에 기계 학습 모델을 학습하는 데 사용할 수 있는 시뮬레이션된 원격 분석 데이터를 생성하는 가상 디바이스를 만듭니다.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/11/2019
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 1c56dd23084feabeb72f084b03e055d4aa09a11d
ms.sourcegitcommit: f9601bbccddfccddb6f577d6febf7b2b12988911
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/12/2020
ms.locfileid: "75912274"
---
# <a name="tutorial-generate-simulated-device-data"></a>자습서: 시뮬레이션된 디바이스 데이터 생성

> [!NOTE]
> 이 문서는 IoT Edge에서 Azure Machine Learning을 사용하는 방법에 대한 자습서 시리즈의 일부입니다. 이 문서로 바로 이동한 경우에는 학습 효과를 극대화할 수 있도록 시리즈의 [첫 번째 문서](tutorial-machine-learning-edge-01-intro.md)부터 시작하는 것이 좋습니다.

이 문서에서는 기계 학습 데이터를 사용하여 IoT Hub에 원격 분석 데이터를 보내는 디바이스를 시뮬레이션합니다. 소개에서 언급했듯이, 이 엔드투엔드 자습서에서는 [터보팬 엔진 성능 저하 시뮬레이션 데이터 세트](https://c3.nasa.gov/dashlink/resources/139/)를 사용하여 훈련 및 테스트를 위해 일련의 비행기 엔진에서 데이터를 시뮬레이션합니다.

함께 제공되는 readme.txt를 통해 다음 사항을 알 수 있습니다.

* 데이터는 여러 다변량 시계열로 구성됩니다.
* 각 데이터 세트는 학습 및 테스트 하위 집합으로 나뉩니다.
* 각 시계열은 다른 엔진에서 가져온 것입니다.
* 각 엔진은 다양한 정도의 초기 마모 및 제조 편차로 시작됩니다.

이 자습서에서는 단일 데이터 세트(FD003)의 학습 데이터 하위 집합을 사용합니다.

실제로 각 엔진은 독립 IoT 디바이스입니다. 사용 가능한 인터넷에 연결된 터보팬 엔진 컬렉션이 없다고 가정하고, 디바이스에 대한 소프트웨어 대체물을 빌드하겠습니다.

시뮬레이터는 IoT Hub API를 사용하여 가상 디바이스를 IoT Hub에 프로그래밍 방식으로 등록하는 C# 프로그램입니다. 그런 다음, NASA가 제공하는 데이터 하위 집합에서 각 디바이스의 데이터를 읽어서 시뮬레이션된 IoT 디바이스를 사용하여 IoT Hub로 보냅니다. 자습서의 이 부분에 대한 모든 코드는 리포지토리의 DeviceHarness 디렉터리에서 찾을 수 있습니다.

DeviceHarness 프로젝트는 4개 클래스로 구성된 C#으로 작성된 .NET Core 프로젝트입니다.

* **프로그램:** 사용자 입력 및 전반적인 조정 처리를 담당하는 실행을 위한 진입점입니다.
* **TrainingFileManager:** 선택한 데이터 파일을 읽고 구문 분석하는 작업을 담당합니다.
* **CycleData:** 메시지 형식으로 변환된 파일의 단일 행 데이터를 나타냅니다.
* **TurbofanDevice:** 데이터의 단일 디바이스(시계열)에 해당하는 IoT 디바이스를 생성하고 IoT 디바이스를 통해 IoT Hub에 데이터를 전송하는 작업을 담당합니다.

이 문서에 설명된 작업은 완료하는 데 약 20분이 소요됩니다.

이 단계의 작업과 동일한 실제 환경은 디바이스 개발자와 클라우드 개발자가 수행할 가능성이 높습니다.

## <a name="configure-visual-studio-code-and-build-deviceharness-project"></a>Visual Studio Code 구성 및 DeviceHarness 프로젝트 빌드

1. 이전 문서의 설명처럼 가상 머신에 원격 데스크톱 세션을 엽니다.

1. Visual Studio Code를 엽니다.

1. Visual Studio Code에서 **파일** > **폴더 열기...** 를 선택합니다.

1. **폴더** 텍스트 상자에 `C:\source\IoTEdgeAndMlSample\DeviceHarness`를 입력하고 **폴더 선택** 단추를 클릭합니다.

   출력 창에 OmniSharp 오류가 나타나면 C# 확장을 제거하고 VS Code,를 닫았다가 다시 열고 C# 확장을 설치한 다음, 창을 다시 로드해야 합니다.

1. 이 머신에서 확장 기능을 처음 사용하기 때문에 일부 확장은 종속성을 업데이트하고 설치합니다. 확장을 업데이트하라는 메시지가 표시될 수 있습니다. 그런 경우, **창 다시 로드**를 선택합니다.

1. DeviceHarness에 필요한 자산을 추가하라는 메시지가 표시됩니다. **예**를 선택하여 추가합니다.

   * 알림을 표시하는 데 몇 초가 걸릴 수 있습니다.
   * 이 알림을 놓친 경우 오른쪽 아래 모서리에 있는 "종 모양" 아이콘을 확인합니다.

   ![VS Code 확장 팝업](media/tutorial-machine-learning-edge-03-generate-data/add-required-assets.png)

1. **복원**을 선택하여 패키지 종속성을 복원합니다.

   ![VS Code 복원 프롬프트](media/tutorial-machine-learning-edge-03-generate-data/restore-package-dependencies.png)

1. `Ctrl + Shift + B` 또는 **터미널** > **빌드 작업 실행**을 선택하여 빌드를 트리거하고 환경이 올바르게 설정되었는지 확인합니다.

1. 실행할 빌드 작업을 선택하라는 메시지가 표시됩니다. **빌드**를 선택합니다.

1. 빌드가 실행되고 성공 메시지가 출력됩니다.

   ![빌드에 성공한 출력 메시지](media/tutorial-machine-learning-edge-03-generate-data/build-success.png)

1. **터미널** > **Configure Default Build Task...** (기본 빌드 작업 구성...)를 선택하고 프롬프트에서 **빌드**를 선택하여 이 빌드를 기본 빌드 작업으로 만들 수 있습니다.

## <a name="connect-to-iot-hub-and-run-deviceharness"></a>IoT Hub에 연결 및 DeviceHarness 실행

프로젝트가 빌드되었으면 IoT 허브에 연결하여 연결 문자열에 액세스하고 데이터 생성 진행 상황을 모니터링합니다.

### <a name="sign-in-to-azure-in-visual-studio-code"></a>Visual Studio Code에서 Azure에 로그인

1. `Ctrl + Shift + P` 또는 **보기** > **명령 팔레트...** 를 선택하여 명령 팔레트를 열고 Visual Studio Code에서 Azure 구독에 로그인합니다.

1. 프롬프트에서 **Azure: 로그인**을 검색하여 선택합니다.

1. 브라우저 창이 열리고 자격 증명을 묻는 메시지가 나타납니다. 성공 페이지로 리디렉션되면 브라우저를 닫으면 됩니다.

### <a name="connect-to-your-iot-hub-and-retrieve-hub-connection-string"></a>IoT Hub에 연결 및 허브 연결 문자열 검색

1. Visual Studio Code 탐색기의 맨 아래 섹션에서 **Azure IoT Hub 디바이스** 프레임을 선택하여 펼칩니다.

1. 펼쳐진 프레임에서 **IoT Hub 선택**을 클릭합니다.

1. 메시지가 나타나면 Azure 구독을 선택한 다음, IoT 허브를 선택합니다.

1. **Azure IoT Hub 디바이스** 프레임을 클릭하여 **...** 를 클릭하여 더 많은 작업을 표시합니다. **IoT Hub 연결 문자열 복사**를 선택합니다.

   ![IoT Hub 연결 문자열 복사](media/tutorial-machine-learning-edge-03-generate-data/copy-hub-connection-string.png)

### <a name="run-the-deviceharness-project"></a>DeviceHarness 프로젝트 실행

1. **보기** > **터미널**을 선택하여 Visual Studio Code 터미널을 엽니다.

   메시지가 표시되지 않으면 Enter를 선택합니다.

1. 터미널에 `dotnet run`을 입력합니다.

1. IoT Hub 연결 문자열을 묻는 메시지가 표시되면 이전 섹션에서 복사한 연결 문자열을 붙여넣습니다.

1. **Azure IoT Hub 디바이스** 프레임에서 새로 고침 단추를 클릭합니다.

   ![IoT Hub 디바이스 목록 새로 고침](media/tutorial-machine-learning-edge-03-generate-data/refresh-hub-device-list.png)

1. 디바이스가 IoT Hub에 추가되고 디바이스가 녹색으로 표시되어 데이터가 해당 디바이스를 통해 전송되는 것을 나타냅니다.

1. 디바이스를 마우스 오른쪽 단추로 클릭하고 **기본 제공 이벤트 엔드포인트 모니터링 시작**을 선택하면 메시지가 허브로 전송되는 것을 볼 수 있습니다. 메시지는 Visual Studio Code의 출력 창에 표시됩니다.

1. **Azure IoT Hub** 출력 창을 클릭하고 **기본 제공 이벤트 엔드포인트 모니터링 중지**를 선택하면 모니터링을 중지할 수 있습니다.

1. 애플리케이션 실행이 완료될 때까지 기다립니다. 몇 분이 걸립니다.

## <a name="check-iot-hub-for-activity"></a>IoT Hub에서 작업 확인

DeviceHarness에서 보낸 데이터는 IoT 허브로 전송됩니다. Azure Portal을 사용하면 데이터가 허브에 도달했는지 쉽게 확인할 수 있습니다.

1. [Azure Portal](https://portal.azure.com/)을 열고 IoT 허브로 이동합니다.

1. 개요 페이지에 데이터가 허브로 전송된 것이 표시됩니다.  

   ![IoT Hub에서 디바이스-클라우드 메시지 보기](media/tutorial-machine-learning-edge-03-generate-data/iot-hub-usage.png)

## <a name="validate-data-in-azure-storage"></a>Azure Storage에서 데이터 유효성 검사

방금 IoT 허브로 보낸 데이터는 이전 문서에서 만든 스토리지 컨테이너로 라우팅되었습니다. 스토리지 계정의 데이터를 살펴보겠습니다.

1. Azure Portal에서 스토리지 계정으로 이동합니다.

1. 스토리지 계정 탐색기에서 **Storage Explorer(미리 보기)** 를 선택합니다.

1. Storage Explorer에서 **Blob 컨테이너**와 **devicedata**를 차례로 선택합니다.

1. 콘텐츠 창에서 IoT 허브 이름의 폴더를 클릭한 다음, 연도, 월, 일 및 시간을 클릭합니다. 데이터가 기록된 분을 나타내는 여러 폴더가 보입니다.

   ![Blob Storage의 폴더 보기](media/tutorial-machine-learning-edge-03-generate-data/confirm-data-storage-results.png)

1. 이러한 폴더 중 하나를 클릭하여 파티션에 해당하는 **00** 및 **01** 레이블이 지정된 데이터 파일을 찾습니다.

1. 파일은 [Avro](https://avro.apache.org/) 형식으로 작성되지만 이 파일 중 하나를 두 번 클릭하면 또 다른 브라우저 탭이 열리고 데이터가 부분적으로 렌더링됩니다. 대신 프로그램에서 파일을 열지 묻는 메시지가 표시되는 경우 VS Code를 선택하면 올바르게 렌더링됩니다.

1. 지금 당장 데이터를 읽거나 해석하려고 할 필요가 없습니다. 다음 문서에서 수행하게 됩니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 .NET Core 프로젝트를 사용하여 일련의 가상 디바이스를 만들고 이 디바이스를 통해 IoT Hub를 통해 Azure Storage 컨테이너로 데이터를 보냅니다. 이 프로젝트는 실제 디바이스가 센서 판독값, 작동 설정, 오류 신호 및 모드 등을 포함하는 데이터를 IoT Hub로 보내고 큐레이팅된 스토리지로 보내는 실제 시나리오를 시뮬레이션합니다. 충분한 데이터가 수집되면 이 데이터를 사용하여 디바이스의 RUL(잔여 수명)을 예측하는 모델을 학습시키며, 이 내용은 다음 문서에서 설명합니다.

다음 문서를 진행하면 데이터를 사용하여 기계 학습 모델을 학습시킬 수 있습니다.

> [!div class="nextstepaction"]
> [Azure Machine Learning 모델 학습 및 배포](tutorial-machine-learning-edge-04-train-model.md)
