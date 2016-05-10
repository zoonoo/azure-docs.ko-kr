<properties
	pageTitle="IoT Hub 장치 관리 시작 | Microsoft Azure"
	description="C#로 장치 관리에 대한 Azure IoT Hub 시작 자습서입니다. Microsoft Azure IoT SDK를 포함한 Azure IoT Hub 및 C#을 사용하여 장치 관리를 구현합니다."
	services="iot-hub"
	documentationCenter=".net"
	authors="ellenfosborne"
	manager="timlt"
	editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="dotnet"
 ms.topic="hero-article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="04/29/2016"
 ms.author="elfarber"/>

# Azure IoT Hub 장치 관리 시작(미리 보기)

## 소개
Azure IoT Hub 장치 관리를 시작하려면 Azure IoT Hub를 만들고 IoT Hub의 장치를 프로비전하며 여러 시뮬레이션된 장치를 시작해야 합니다. 이 자습서에서는 다음 단계를 안내합니다.

> [AZURE.NOTE]  기존 IoT Hub에 아직 장치 관리 기능이 없기 때문에 기존 IoT Hub가 있더라도 새 IoT Hub를 만들어서 장치 관리 기능을 사용해야 합니다. 장치 관리가 일반적으로 지원되면 모든 기존 IoT Hub는 장치 관리 기능을 가져도록 업그레이드됩니다.

## 필수 조건

단계를 완료하려면 다음을 설치해야 합니다.

- Microsoft Visual Studio 2015
- Git
- CMake(버전 2.8이상). <https://cmake.org/download/>에서 CMake를 설치합니다. Windows PC에서 Windows Installer(.msi) 옵션을 선택합니다. CMake를 현재 사용자 경로 변수에 추가하도록 확인란을 선택해야 합니다.
- 활성 Azure 구독.

	계정이 없는 경우 몇 분 만에 무료 평가판 계정을 만들 수 있습니다. 자세한 내용은 [Azure 무료 체험][lnk-free-trial]을 참조하세요.

## IoT Hub를 사용하는 장치 관리 만들기

연결할 시뮬레이션된 장치에 IoT Hub를 사용하는 장치 관리를 만들어야 합니다. 다음 단계는 Azure 포털을 사용하여 이 작업을 완료하는 방법을 보여줍니다.

1.  [Azure 포털]에 로그인합니다.
2.  점프 모음에서 **새로 만들기**를 클릭하고 **사물 인터넷**을 클릭한 다음 **Azure IoT Hub**를 클릭합니다.

	![][img-new-hub]

3.  **IoT Hub** 블레이드에서 IoT Hub의 구성을 선택합니다.

	![][img-configure-hub]

  -   **이름** 상자에 IoT Hub의 이름을 입력합니다. **이름**의 유효하고 사용 가능하면 **이름** 상자에 녹색 확인 표시가 나타납니다.
  -   **가격 및 크기 조정 계층**을 선택합니다. 이 자습서에는 특정 계층이 필요하지 않습니다.
  -   **리소스 그룹**에서 새 리소스 그룹을 만들거나 기존 리소스 그룹을 선택합니다. 자세한 내용은 [리소스 그룹을 사용하여 Azure 리소스 관리]를 참조하세요.
  -   **장치 관리 사용**하도록 확인란을 선택합니다.
  -   **위치**에서 IoT Hub를 호스트하는 위치를 선택합니다. IoT Hub 장치 관리는 미국 동부, 북유럽 및 동아시아에서만 사용할 수 있습니다.

4.  IoT Hub 구성 옵션을 선택한 경우 **만들기**를 클릭합니다. Azure가 IoT Hub를 만드는 데 몇 분 정도 걸릴 수 있습니다. 상태를 확인하려면 **시작 보드** 또는 **알림** 패널에서 진행률을 모니터링할 수 있습니다.

	![][img-monitor]

5.  IoT Hub가 성공적으로 만들어지면, 새 IoT Hub의 블레이드를 열고, **호스트 이름**를 기록한 다음 **키** 아이콘을 클릭합니다.

	![][img-keys]

6.  **iothubowner** 정책을 클릭한 다음, **iothubowner** 블레이드에 있는 연결 문자열을 복사하여 기록해둡니다. 이 자습서의 나머지 단계를 완료하는 데 필요하기 때문에 나중에 액세스할 수 있는 위치에 복사합니다.

 	> [AZURE.NOTE] 프로덕션 시나리오에서 **iothubowner** 자격 증명을 사용하지 않도록 합니다.

	![][img-connection]

이제 IoT Hub를 사용하는 장치 관리를 만들었습니다. 이 자습서의 나머지 단계를 완료하기 위해 연결 문자열이 필요합니다.

## 샘플 빌드 및 IoT Hub에 장치 프로비전

이 섹션에서는 시뮬레이션된 장치 및 샘플을 빌드하고 IoT Hub의 장치 레지스트리에 일련의 새 장치 ID를 프로비전하는 스크립트를 실행합니다. 장치 레지스트리에 항목이 없는 경우 장치를 IoT Hub에 연결할 수 없습니다.

샘플을 빌드하고 IoT Hub에 장치를 프로비전하려면 다음 단계를 수행합니다.

1.  **VS2015용 개발자 명령 프롬프트**를 엽니다.

2.  Github 리포지토리를 복제합니다. **공백이 없는 디렉터리에 복제하도록 합니다.**

  ```
  git clone --recursive --branch dmpreview https://github.com/Azure/azure-iot-sdks.git
  ```

3.  **azure-iot-sdks** 리포지토리를 복제한 루트 폴더에서 **\\azure-iot-sdks\\csharp\\service\\samples**로 이동하고 실행하여 자리 표시자 값을 이전 섹션의 연결 문자열로 바꿉니다.

  ```
  setup.bat <IoT Hub Connection String>
  ```

이 스크립트는 다음을 수행합니다.

1.  **cmake**를 실행하여 시뮬레이션된 장치에 Visual Studio 2015 솔루션을 만듭니다. 이 프로젝트 파일은 **azure-iot-sdks\\csharp\\service\\samples\\cmake\\iotdm\_client\\samples\\iotdm\_simple\_sample\\iotdm\_simple\_sample.vcxproj**입니다. 원본 파일은 ****azure-iot-sdks\\c\\iotdm\_client\\samples\\iotdm\_simple\_sample** 폴더에 있습니다.

2.  시뮬레이션된 장치 프로젝트 **iotdm\_simple\_sample.vcxproj**를 빌드합니다.

3.  장치 관리 샘플 **azure-iot-sdks\\csharp\\service\\samples\\GetStartedWithIoTDM\\GetStartedWithIoTDM.sln**을 빌드합니다.

4.  **GenerateDevices.exe**를 실행하여 IoT Hub에 장치 ID를 프로비전합니다. 장치는 **sampledevices.json**(**azure-iot-sdks\\node\\service\\samples** 폴더에 있음)에 설명되어 있고 장치를 프로비전한 후에 자격 증명이 **devicecreds.txt** 파일(**azure-iot-sdks\\csharp\\service\\samples\\bin** 폴더에 있음)에 저장됩니다.

## 시뮬레이션된 장치 시작

장치가 장치 레지스트리에 추가되었으므로 시뮬레이션된 관리 장치를 시작할 수 있습니다. Azure IoT Hub에 프로비전된 각 장치 ID에 하나의 시뮬레이션된 장치가 시작됩니다.

개발자 명령 프롬프트를 사용하여 **\\azure-iot-sdks\\csharp\\service\\samples\\bin** 폴더에서 다음을 실행합니다.

  ```
  simulate.bat
  ```

이 스크립트는 **devicecreds.txt** 파일에 나열된 각 장치에 하나의 **iotdm\_simple\_sample.exe** 인스턴스만을 실행합니다. 시뮬레이션된 장치는 명령 창을 닫을 때까지 계속 실행됩니다.

**iotdm\_simple\_sample** 샘플 응용 프로그램은 C용 Azure IoT Hub 장치 관리 클라이언트 라이브러리를 사용하여 빌드되며 이를 통해 Azure IoT Hub에 의해 관리될 수 있는 IoT 장치를 만들 수 있습니다. 장치 제조업체는 이 라이브러리를 사용하여 장치 속성을 보고하고 장치 작업에 필요한 실행 동작을 구현할 수 있습니다. 이 라이브러리는 오픈 소스 Azure IoT Hub SDK의 일부로 제공되는 구성 요소입니다.

**simulate.bat**을 실행하는 경우 출력 창에서 데이터의 스트림을 참조하세요. 이 출력은 들어오고 나가는 트래픽 뿐만 아니라 응용 프로그램 특정 콜백 함수의 **printf** 문도 보여 줍니다. 이를 통해 샘플 응용 프로그램이 디코딩된 패킷을 처리하는 방법과 함께 들어오고 나가는 트래픽을 볼 수 있습니다. 장치가 IoT Hub에 연결된 경우 서비스는 장치에서 리소스를 자동으로 확인하기 시작합니다. 그런 다음 IoT Hub DM 클라이언트 라이브러리는 장치 콜백을 호출하여 장치에서 최신 값을 검색합니다.

아래는 **iotdm\_simple\_sample** 샘플 응용 프로그램의 출력입니다. 맨 위쪽에서 성공적으로 **등록되었습니다**라는 메시지가 표시되며 IoT Hub에 연결된 ID **Device11-7ce4a850**를 가진 장치를 보여 줍니다.

> [AZURE.NOTE]  대략적인 출력을 보려면 소매 구성을 빌드하고 실행합니다.

![][img-output]

"다음 단계"의 자습서를 완료하는 대로 모든 시뮬레이션된 장치를 실행하도록 합니다.

## 다음 단계

Azure IoT Hub 장치 관리 기능에 대해 자세히 알아보려면 이 자습서를 진행합니다.

- [장치 쌍을 사용하는 방법][lnk-tutorial-twin]

- [쿼리를 사용하여 쌍 장치를 찾는 방법][lnk-tutorial-queries]

- [장치 작업을 사용하여 장치 펌웨어를 업데이트하는 방법][lnk-tutorial-jobs]

<!-- images and links -->
[img-new-hub]: media/iot-hub-device-management-get-started/image1.png
[img-configure-hub]: media/iot-hub-device-management-get-started/image2.png
[img-monitor]: media/iot-hub-device-management-get-started/image3.png
[img-keys]: media/iot-hub-device-management-get-started/image4.png
[img-connection]: media/iot-hub-device-management-get-started/image5.png
[img-output]: media/iot-hub-device-management-get-started/image6.png

[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[Azure 포털]: https://portal.azure.com/
[리소스 그룹을 사용하여 Azure 리소스 관리]: ../azure-portal/resource-group-portal.md
[lnk-tutorial-twin]: iot-hub-device-management-device-twin.md
[lnk-tutorial-queries]: iot-hub-device-management-device-query.md
[lnk-tutorial-jobs]: iot-hub-device-management-device-jobs.md

<!---HONumber=AcomDC_0504_2016-->