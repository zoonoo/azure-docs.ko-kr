---
title: 원격 모니터링에 연결된 디바이스 일괄 관리 - Azure | Microsoft Docs
description: 이 자습서에서는 원격 모니터링 솔루션에 연결된 디바이스를 일괄적으로 관리하는 방법을 알아봅니다.
author: aditidugar
manager: philmea
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: tutorial
ms.date: 11/29/2018
ms.author: adugar
ms.openlocfilehash: 8a5c74c76662a089675fcbdcd8d5a7ea54b58fd1
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/18/2019
ms.locfileid: "59799043"
---
# <a name="tutorial-manage-your-connected-devices-in-bulk"></a>자습서: 연결된 디바이스 일괄 관리

이 자습서에서는 원격 모니터링 솔루션 가속기를 사용하여 연결된 디바이스의 구성을 일괄적으로 관리합니다.

Contoso의 운영자는 디바이스 그룹을 새 펌웨어 버전으로 구성해야 합니다. 각 디바이스에서 펌웨어를 개별적으로 업데이트할 필요가 없습니다. 디바이스 그룹의 펌웨어를 업데이트하려면 원격 모니터링 솔루션 가속기에서 디바이스 그룹 및 자동 디바이스 관리를 사용합니다. 디바이스 그룹에 추가된 모든 디바이스는 디바이스가 온라인 상태가 되는 즉시 최신 펌웨어를 얻습니다.

이 자습서에서는 다음을 수행했습니다.

>[!div class="checklist"]
> * 디바이스 그룹 만들기
> * 펌웨어 준비 및 호스팅
> * Azure Portal에서 디바이스 구성 만들기
> * 원격 모니터링 솔루션으로 디바이스 구성 가져오기
> * 디바이스 그룹의 디바이스에 구성 배포
> * 배포 모니터링

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

<!--
If this is going to be a tutorial - we need to split this include into two so that we can accommodate the additional prerequisites:

[!INCLUDE [iot-accelerators-tutorial-prereqs](../../includes/iot-accelerators-tutorial-prereqs.md)]
-->

## <a name="prerequisites"></a>필수 조건

이 자습서를 수행하려면 Azure 구독에서 원격 모니터링 솔루션 가속기의 배포된 인스턴스가 필요합니다.

원격 모니터링 솔루션 가속기를 아직 배포하지 않은 경우 [클라우드 기반 원격 모니터링 솔루션 배포](quickstart-remote-monitoring-deploy.md) 빠른 시작을 완료해야 합니다.

펌웨어 파일을 호스팅하려면 Azure 스토리지 계정이 필요합니다. 기존 스토리지 계정을 사용하거나 구독에 [새 스토리지 계정을 만들](../storage/common/storage-quickstart-create-account.md) 수 있습니다.

이 자습서에서는 [IoT DevKit](https://microsoft.github.io/azure-iot-developer-kit/) 디바이스를 샘플 디바이스로 사용합니다.

로컬 머신에 설치해야 하는 소프트웨어는 다음과 같습니다.

* [VS Code(Visual Studio Code)](https://code.visualstudio.com/)
* [Azure IoT Workbench](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.vscode-iot-workbench) VS Code 확장

시작하기 전에 다음을 수행합니다.

* [IoT DevKit 디바이스의 부팅 로더가 1.4.0 이상의 버전인지](https://microsoft.github.io/azure-iot-developer-kit/docs/firmware-upgrading/) 확인합니다.
* IoT DevKit SDK가 부팅 로더와 동일한 버전인지 확인합니다. VS Code의 Azure IoT Workbench를 사용하여 IoT DevKit SDK를 업데이트할 수 있습니다. 명령 팔레트를 열고 **Arduino: 보드 관리자**를 입력하고 선택합니다. 자세한 내용은 [개발 환경 준비](../iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started.md#prepare-the-development-environment)를 참조하세요.

또한 하나 이상의 IoT DevKit 디바이스를 원격 모니터링 솔루션 가속기에 연결해야 합니다. IoT DevKit 디바이스를 연결하지 않은 경우 [MXChip IoT DevKit AZ3166을 IoT 원격 모니터링 솔루션 가속기에 연결](iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2.md)을 참조하세요.

## <a name="navigate-to-the-dashboard"></a>대시보드로 이동

브라우저에서 원격 모니터링 솔루션 대시보드를 보려면 먼저 [Microsoft Azure IoT 솔루션 가속기](https://www.azureiotsolutions.com/Accelerators#dashboard)로 이동합니다. Azure 구독 자격 증명을 사용하여 로그인하라는 메시지가 표시될 수 있습니다.

[빠른 시작](quickstart-remote-monitoring-deploy.md)에서 배포한 원격 모니터링 솔루션 가속기의 타일에서 **시작**을 클릭합니다.

## <a name="create-a-device-group"></a>디바이스 그룹 만들기

디바이스 그룹의 펌웨어를 자동으로 업데이트하려면 디바이스가 원격 모니터링 솔루션에서 디바이스 그룹의 멤버여야 합니다.

1. **디바이스** 페이지에서 솔루션 가속기에 연결된 모든 **IoT DevKit** 디바이스를 선택합니다. 그런 다음, **작업**을 클릭합니다.

1. **작업** 패널에서 **태그**를 선택하고, 작업 이름을 **AddDevKitTag**로 설정한 다음, 값이 **Y**인 **IsDevKitDevice**라는 텍스트 태그를 추가합니다. 그런 다음, **적용**을 클릭합니다.

1. 이제 태그 값을 사용하여 디바이스 그룹을 만들 수 있습니다. **디바이스** 페이지에서 **디바이스 그룹 관리**를 클릭합니다.

1. 조건에서 **IsDevKitDevice** 태그 이름 및 **Y** 값을 사용하는 텍스트 필터를 만듭니다. 디바이스 그룹을 **IoT DevKit 디바이스**로 저장합니다.

이 자습서의 뒷부분에서 이 디바이스 그룹을 사용하여 모든 멤버의 펌웨어를 업데이트하는 디바이스 구성을 적용합니다.

## <a name="prepare-and-host-the-firmware"></a>펌웨어 준비 및 호스팅

[Azure IoT Workbench](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.vscode-iot-workbench) VS Code 확장에는 펌웨어 업데이트를 위한 디바이스 코드 샘플이 포함되어 있습니다.

### <a name="open-the-firmware-ota-sample-in-vs-code"></a>VS Code에서 펌웨어 OTA 샘플 열기

1. IoT DevKit가 컴퓨터에 연결되어 있지 않은지 확인합니다. VS Code를 시작한 다음, DevKit를 컴퓨터에 연결합니다.

1. **F1** 키를 눌러 명령 팔레트를 연 다음, **IoT Workbench: 예제**를 입력하고 선택합니다. 그런 다음, **IoT DevKit**를 보드로 선택합니다.

1. **펌웨어 OTA**를 찾은 다음, **샘플 열기**를 클릭합니다. 새 VS Code 창이 열리고 **firmware_ota** 프로젝트 폴더가 표시됩니다.

    ![IoT Workbench, 펌웨어 OTA 예제 선택](media/iot-accelerators-remote-monitoring-bulk-configuration-update/iot-workbench-firmware-example.png)

### <a name="build-the-new-firmware"></a>새 펌웨어 빌드

디바이스 펌웨어의 초기 버전은 1.0.0입니다. 새 펌웨어의 버전 번호는 더 높아야 합니다.

1. VS Code에서 **FirmwareOTA.ino** 파일을 열고 `currentFirmwareVersion`을 `1.0.0`에서 `1.0.1`로 변경합니다.

    ![펌웨어 버전 변경](media/iot-accelerators-remote-monitoring-bulk-configuration-update/version-1-0-1.png)

1. 명령 팔레트를 연 다음, **IoT Workbench: 디바이스**를 입력하고 선택합니다. 그런 다음, **디바이스 컴파일**을 선택하여 코드를 컴파일합니다.

    ![디바이스 컴파일](media/iot-accelerators-remote-monitoring-bulk-configuration-update/iot-workbench-device-compile.png)

    VS Code에서 컴파일된 파일을 프로젝트의 `.build` 폴더에 저장합니다. VS Code는 설정에 따라 탐색기 보기에서 `.build` 폴더를 숨길 수 있습니다.

### <a name="generate-the-crc-value-and-calculate-the-firmware-file-size"></a>CRC 값 생성 및 펌웨어 파일 크기 계산

1. 명령 팔레트를 연 다음, **IoT Workbench: 디바이스**를 입력하고 선택합니다. 그런 다음, **CRC 생성**을 선택합니다.

    ![CRC 생성](media/iot-accelerators-remote-monitoring-bulk-configuration-update/iot-workbench-device-crc.png)

1. VS Code는 출력 창에서 CRC 값, 펌웨어 파일 이름과 경로 및 파일 크기를 생성하고 출력합니다. 나중에 사용할 수 있도록 다음 값을 적어 두세요.

    ![CRC 정보](media/iot-accelerators-remote-monitoring-bulk-configuration-update/crc-info.png)

### <a name="upload-the-firmware-to-the-cloud"></a>클라우드에 펌웨어 업로드

Azure 스토리지 계정을 사용하여 클라우드에서 새 펌웨어 파일을 호스팅합니다.

1. Azure Portal의 저장소 계정으로 이동합니다. [서비스] 섹션에서 **Blob**을 선택합니다. 펌웨어 파일을 저장할 **firmware**라는 공용 컨테이너를 만듭니다.

    ![폴더 만들기](media/iot-accelerators-remote-monitoring-bulk-configuration-update/blob-folder.png)

1. 펌웨어 파일을 컨테이너에 업로드하려면 **firmware** 컨테이너를 선택하고 **업로드**를 클릭합니다.

1. **FirmwareOTA.ino.bin**을 선택합니다. 이 파일에 대한 전체 경로는 이전 섹션에서 적어 두었습니다.

1. 펌웨어 파일 업로드가 완료되면 파일 URL을 적어 둡니다.

### <a name="build-and-upload-the-original-firmware-to-the-iot-devkit-device"></a>IoT DevKit 디바이스에 원래 펌웨어 빌드 및 업로드

1. VS Code에서 **FirmwareOTA.ino** 파일을 열고 `currentFirmwareVersion`을 `1.0.0`으로 다시 변경합니다.

    ![Version 1.0.0](media/iot-accelerators-remote-monitoring-bulk-configuration-update/version-1-0-1.png)

1. 명령 팔레트를 연 다음, **IoT Workbench: 디바이스**를 입력하고 선택합니다. 그런 다음, **디바이스 업로드**를 선택합니다.

    ![디바이스 업로드](media/iot-accelerators-remote-monitoring-bulk-configuration-update/device-upload.png)

1. VS Code에서 코드를 확인하고 IoT DevKit 디바이스에 업로드합니다.

1. 업로드가 완료되면 IoT DevKit 디바이스가 다시 부팅됩니다. 다시 부팅이 완료되면 IoT DevKit 화면에 **FW 버전: 1.0.0**이 표시되고 새 펌웨어를 확인합니다.

    ![ota-1](media/iot-accelerators-remote-monitoring-bulk-configuration-update/ota-1.jpg)

## <a name="create-a-device-configuration"></a>디바이스 구성 만들기

디바이스 구성은 원하는 디바이스 상태를 지정합니다. 일반적으로 개발자는 Azure Portal의 **IoT 디바이스 구성** 페이지에서 [구성을 만듭니다](../iot-hub/iot-hub-automatic-device-management.md#create-a-configuration). 디바이스 구성은 원하는 디바이스 상태와 메트릭 세트를 지정하는 JSON 문서입니다.

다음 구성을 **firmware-update.json**이라는 파일로 로컬 머신에 저장합니다. `YOURSTRORAGEACCOUNTNAME`, `YOURCHECKSUM` 및 `YOURPACKAGESIZE` 자리 표시자를 앞에서 적어 둔 값으로 바꿉니다.

```json
{
  "id": "firmware-update",
  "schemaVersion": null,
  "labels": {
    "Version": "1.0.1",
    "Devices": "IoT DevKit"
  },
  "content": {
    "deviceContent": {
      "properties.desired.firmware": {
        "fwVersion": "1.0.1",
        "fwPackageURI": "https://YOURSTRORAGEACCOUNTNAME.blob.core.windows.net/firmware/FirmwareOTA.ino.bin",
        "fwPackageCheckValue": "YOURCHECKSUM",
        "fwSize": YOURPACKAGESIZE
      }
    }
  },
  "targetCondition": "",
  "priority": 10,
  "systemMetrics": {
    "results": {
      "targetedCount": 0,
      "appliedCount": 0
    },
    "queries": {
      "targetedCount": "",
      "appliedCount": "select deviceId from devices where configurations.[[firmware-update]].status = 'Applied'"
    }
  },
  "metrics": {
    "results": {
      "Current": 1
    },
    "queries": {
      "Current": "SELECT deviceId FROM devices WHERE properties.reported.firmware.fwUpdateStatus='Current' AND properties.reported.firmware.type='IoTDevKit'",
      "Downloading": "SELECT deviceId FROM devices WHERE properties.reported.firmware.fwUpdateStatus='Downloading' AND properties.reported.firmware.type='IoTDevKit'",
      "Verifying": "SELECT deviceId FROM devices WHERE properties.reported.firmware.fwUpdateStatus='Verifying' AND properties.reported.firmware.type='IoTDevKit'",
      "Applying": "SELECT deviceId FROM devices WHERE properties.reported.firmware.fwUpdateStatus='Applying' AND properties.reported.firmware.type='IoTDevKit'",
      "Error": "SELECT deviceId FROM devices WHERE properties.reported.firmware.fwUpdateStatus='Error' AND properties.reported.firmware.type='IoTDevKit'"
    }
  }
}
```

이 구성 파일은 다음 섹션에서 사용합니다.

## <a name="import-a-configuration"></a>구성 가져오기

이 섹션에서는 디바이스 구성을 패키지로 원격 모니터링 솔루션 가속기에 가져옵니다. 일반적으로 운영자가 이 작업을 수행합니다.

1. 원격 모니터링 웹 UI에서 **패키지** 페이지로 이동한 후 **+새 패키지**를 클릭합니다.

    ![새 패키지](media/iot-accelerators-remote-monitoring-bulk-configuration-update/packagepage.png)

1. **새 패키지** 패널에서 패키지 유형으로 **디바이스 구성**을 선택하고, 구성 유형으로 **펌웨어**를 선택합니다. 로컬 머신에서 **찾아보기**를 클릭하여 **firmware-update.json** 파일을 찾은 다음, **업로드**를 클릭합니다.

    ![패키지 업로드](media/iot-accelerators-remote-monitoring-bulk-configuration-update/uploadpackage.png)

1. 이제 패키지 목록에는 **firmware-update** 패키지가 포함되어 있습니다.

## <a name="deploy-the-configuration-to-your-devices"></a>디바이스에 구성 배포

이 섹션에서는 IoT DevKit 디바이스에 디바이스 구성을 적용하는 배포를 만들고 실행합니다.

1. 원격 모니터링 웹 UI에서 **배포** 페이지로 이동한 후 **+새 배포**를 클릭합니다.

    ![새 배포](media/iot-accelerators-remote-monitoring-bulk-configuration-update/deploymentpage.png)

1. **새 배포** 패널에서 다음 설정을 사용하여 배포를 만듭니다.

    |옵션|값|
    |---|---|
    |이름|펌웨어 업데이트 배포|
    |패키지 유형|디바이스 구성|
    |구성 유형|펌웨어|
    |패키지|firmware-update.json|
    |디바이스 그룹|IoT DevKit 디바이스|
    |우선 순위|10|

    ![배포 만들기](media/iot-accelerators-remote-monitoring-bulk-configuration-update/newdeployment.png)

    **적용**을 클릭합니다. **배포** 페이지에 다음과 같은 메트릭이 표시된 새 배포가 표시됩니다.

    * **대상 지정됨**은 디바이스 그룹의 디바이스 수를 표시합니다.
    * **적용됨**에는 구성 콘텐츠를 사용하여 업데이트된 디바이스의 수가 표시됩니다.
    * **성공**에는 배포에서 성공을 보고하는 디바이스의 수가 표시됩니다.
    * **실패**에는 배포에서 오류를 보고하는 디바이스의 수가 표시됩니다.

## <a name="monitor-the-deployment"></a>배포 모니터링

몇 분 후 IoT DevKit에서 새 펌웨어 정보를 검색하여 해당 펌웨어를 디바이스에 다운로드하기 시작합니다.

![ota-2](media/iot-accelerators-remote-monitoring-bulk-configuration-update/ota-2.jpg)

네트워크 속도에 따라 다운로드하는 데 몇 분 정도 걸릴 수 있습니다. 펌웨어가 다운로드되면 디바이스에서 파일 크기와 CRC 값을 확인합니다. 확인이 성공하면 MXChip의 화면에 **통과됨**이 표시됩니다.

![ota-3](media/iot-accelerators-remote-monitoring-bulk-configuration-update/ota-3.jpg)

확인이 성공하면 디바이스가 다시 부팅됩니다. 다시 부팅하기 전에 **5**에서 **0**까지의 카운트 다운이 표시됩니다.

![ota-4](media/iot-accelerators-remote-monitoring-bulk-configuration-update/ota-4.jpg)

다시 부팅되면 IoT DevKit 부팅 로더에서 펌웨어를 새 버전으로 업그레이드합니다. 업그레이드하는 데 몇 초 정도 걸릴 수 있습니다. 이 단계에서는 디바이스의 RGB LED가 빨간색이고, 화면이 비어 있습니다.

![ota-5](media/iot-accelerators-remote-monitoring-bulk-configuration-update/ota-5.jpg)

다시 부팅이 완료되면 이제 IoT DevKit 디바이스에서 펌웨어 1.0.1 버전을 실행하고 있습니다

![ota-6](media/iot-accelerators-remote-monitoring-bulk-configuration-update/ota-6.jpg)

**배포** 페이지에서 배포를 클릭하여 업데이트되는 디바이스의 상태를 확인합니다. 디바이스 그룹의 각 디바이스에 대한 상태와 정의한 사용자 지정 메트릭이 표시됩니다.

![배포 세부 정보](media/iot-accelerators-remote-monitoring-bulk-configuration-update/deploymentstatus.png)

[!INCLUDE [iot-accelerators-tutorial-cleanup](../../includes/iot-accelerators-tutorial-cleanup.md)]

## <a name="next-steps"></a>다음 단계

이 자습서에서는 솔루션에 연결된 디바이스 그룹의 펌웨어를 업데이트하는 방법을 보여 주었습니다. 디바이스를 업데이트하기 위해 솔루션에서 자동 디바이스 관리를 사용합니다. 솔루션의 기본 IoT Hub에 있는 자동 디바이스 관리 기능에 대한 자세한 내용은 [Azure Portal을 사용하여 대규모 IoT 디바이스 구성 및 모니터링](../iot-hub/iot-hub-auto-device-config.md)을 참조하세요.