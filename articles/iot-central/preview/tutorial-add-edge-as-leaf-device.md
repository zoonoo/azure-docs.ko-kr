---
title: Azure IoT Central에 Azure IoT Edge 디바이스 추가 | Microsoft Docs
description: 운영자로서 Azure IoT Central에 Azure IoT Edge 디바이스를 추가합니다.
author: rangv
ms.author: rangv
ms.date: 10/22/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: peterpr
ms.openlocfilehash: ae80a624ed1f85a1f59fea79b152a4bc31067ad1
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/09/2019
ms.locfileid: "73892639"
---
# <a name="tutorial-add-an-azure-iot-edge-device-to-your-azure-iot-central-application-preview-features"></a>자습서: Azure IoT Central 애플리케이션에 Azure IoT Edge 디바이스 추가(미리 보기 기능)

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

이 자습서에서는 Microsoft Azure IoT Central 애플리케이션에 *Azure IoT Edge 디바이스*를 추가하고 구성하는 방법을 보여줍니다. 이 자습서에서는 Azure Marketplace에서 Azure IoT Edge 사용이 가능한 Linux VM을 선택했습니다.

이 자습서는 2부로 구성되어 있습니다.

* 첫째, 운영자로서 Azure IoT Edge 디바이스의 클라우드 중심 프로비저닝을 수행하는 방법을 알아봅니다.
* 그런 다음, Azure IoT Edge 디바이스의 디바이스 중심 프로비저닝을 수행하는 방법을 알아봅니다.

이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * Azure IoT Edge 디바이스 추가
> * SAS 키를 사용하여 프로비저닝에 도움이 되도록 Azure IoT Edge 디바이스 구성
> * IoT Central에서 대시보드, 모듈 상태 보기
> * Azure IoT Edge 디바이스에서 실행되는 모듈에 명령 보내기
> * Azure IoT Edge 디바이스에서 실행되는 모듈에서 속성 설정

## <a name="prerequisites"></a>필수 조건

이 자습서를 완료하려면 Azure IoT Central 애플리케이션이 필요합니다. 이 빠른 시작의 단계에 따라 [Azure IoT Central 애플리케이션을 만듭니다](./quick-deploy-iot-central.md).

## <a name="enable-azure-iot-edge-enrollment-group"></a>Azure IoT Edge 등록 그룹을 사용하도록 설정
관리 페이지에서 Azure IoT Edge 등록 그룹에 대한 SAS 키를 사용하도록 설정합니다.

![디바이스 템플릿 - Azure IoT Edge](./media/tutorial-add-edge-as-leaf-device/groupenrollment.png)

## <a name="cloud-first-azure-iot-edge-device-provisioning"></a>클라우드 중심 Azure IoT Edge 디바이스 프로비저닝   
이 섹션에서는 **환경 센서 템플릿**을 사용하여 새로운 Azure IoT Edge 디바이스를 만들고 디바이스를 프로비저닝합니다. 왼쪽 탐색 모음에서 디바이스를 클릭하고 환경 센서 템플릿을 클릭합니다. 

![디바이스 템플릿 - Azure IoT Edge](./media/tutorial-add-edge-as-leaf-device/deviceexplorer.png)

**+ 새로 만들기**를 클릭하고 자신에게 맞는 디바이스 ID 및 이름을 입력합니다. 

![디바이스 템플릿 - Azure IoT Edge](./media/tutorial-add-edge-as-leaf-device/cfdevicecredentials.png)

디바이스가 **등록됨** 모드로 전환됩니다.

![디바이스 템플릿 - Azure IoT Edge](./media/tutorial-add-edge-as-leaf-device/cfregistered.png)

## <a name="deploy-an-azure-iot-edge-enabled-linux-vm"></a>Azure IoT Edge 사용 Linux VM 배포

>참고: 모든 머신 또는 디바이스를 사용하도록 선택할 수 있습니다. OS: Linux 또는 Windows)

이 자습서에서는 Azure에서 만들 수 있는 Azure IoT 사용 Linux VM을 선택했습니다. [Azure Marketplace](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/microsoft_iot_edge.iot_edge_vm_ubuntu?tab=Overview)로 이동하면 **지금 가져오기** 단추를 클릭합니다. 

![Azure Marketplace](./media/tutorial-add-edge-as-leaf-device/cfmarketplace.png)

**계속**을 클릭합니다.

![Azure Marketplace](./media/tutorial-add-edge-as-leaf-device/cfmarketplacecontinue.png)


Azure Portal로 이동합니다. **만들기** 단추를 클릭합니다.

![Azure Marketplace](./media/tutorial-add-edge-as-leaf-device/cfubuntu.png)

구독을 선택하고, 새 리소스 그룹을 만들고, VM 가용성에 대해 미국 서부 2를 선택한 후, 사용자 및 암호를 입력합니다. 사용자 및 암호는 이후 단계에 필요하므로 기억해 두세요. **검토 + 만들기** 클릭

![Ubuntu VM](./media/tutorial-add-edge-as-leaf-device/cfvm.png)

유효성 검사가 끝나면 **만들기**를 클릭합니다.

![Ubuntu VM](./media/tutorial-add-edge-as-leaf-device/cfvmvalidated.png)

리소스를 만드는 데 몇 분 정도 걸립니다. **리소스**로 이동을 클릭합니다.

![Ubuntu VM](./media/tutorial-add-edge-as-leaf-device/cfvmdeploymentcomplete.png)

### <a name="provision-vm-as-azure-iot-edge-device"></a>Azure IoT Edge 디바이스로 VM 프로비저닝 

왼쪽 탐색에서 지원 + 문제 해결 아래에 있는 직렬 콘솔을 클릭합니다.

![Ubuntu VM](./media/tutorial-add-edge-as-leaf-device/cfserialconsole.png)

다음과 유사한 화면이 표시됩니다.

![Ubuntu VM](./media/tutorial-add-edge-as-leaf-device/cfconsole.png)

Enter 키를 누르고, 메시지가 표시되면 사용자 이름 및 암호를 입력하고 Enter 키를 누릅니다. 

![Ubuntu VM](./media/tutorial-add-edge-as-leaf-device/cfconsolelogin.png)

관리자/루트로 명령을 실행하려면 다음 명령을 실행합니다. **sudo su –**

![Ubuntu VM](./media/tutorial-add-edge-as-leaf-device/cfsudo.png)

Azure IoT Edge 런타임 버전을 확인합니다. 현재 GA 버전은 1.0.8입니다.

![Ubuntu VM](./media/tutorial-add-edge-as-leaf-device/cfconsoleversion.png)

Vim 편집기를 설치하거나 선호하는 경우 Nano를 사용합니다. 

![Ubuntu VM](./media/tutorial-add-edge-as-leaf-device/cfconsolevim.png)

![Ubuntu VM](./media/tutorial-add-edge-as-leaf-device/cfvim.png)

Azure IoT Edge config.yaml 파일을 편집합니다.

![Ubuntu VM](./media/tutorial-add-edge-as-leaf-device/cfconsoleconfig.png)

아래로 스크롤하여 yaml 파일의 연결 문자열 부분을 주석으로 처리합니다. 

**이전**

![Ubuntu VM](./media/tutorial-add-edge-as-leaf-device/cfmanualprovisioning.png)

**이후**(Esc 키를 누르고 소문자 a를 눌러 편집을 시작합니다.)

![Ubuntu VM](./media/tutorial-add-edge-as-leaf-device/cfmanualprovisioningcomments.png)

yaml 파일에서 대칭 키 부분의 주석 처리를 제거합니다. 

**이전**

![Ubuntu VM](./media/tutorial-add-edge-as-leaf-device/cfconsolesymmcomments.png)

**이후**

![Ubuntu VM](./media/tutorial-add-edge-as-leaf-device/cfconsolesymmuncomments.png)

IoT Central로 이동하여 Azure IoT Edge 디바이스의 범위 ID, 디바이스 ID 및 대칭 키를 가져옵니다. ![디바이스 연결](./media/tutorial-add-edge-as-leaf-device/cfdeviceconnect.png)

Linux 상자로 이동하여 범위 ID, 등록 ID를 디바이스 ID 및 대칭 키로 바꿉니다.

**Esc**를 누르고 **:wq!** 를 입력한 후 **Enter** 키를 눌러 변경 내용을 저장합니다.

Azure IoT Edge를 다시 시작하여 변경 내용을 처리하고 **Enter** 키를 누릅니다.

![디바이스 연결](./media/tutorial-add-edge-as-leaf-device/cfrestart.png)

**iotedge list**를 입력합니다. 몇 분 정도 후에 배포된 세 가지 모듈이 표시됩니다.

![디바이스 연결](./media/tutorial-add-edge-as-leaf-device/cfconsolemodulelist.png)


## <a name="iot-central-device-explorer"></a>IoT Central 디바이스 탐색기 

IoT Central에서 디바이스가 프로비저닝된 상태로 전환됩니다.

![디바이스 연결](./media/tutorial-add-edge-as-leaf-device/cfprovisioned.png)

모듈 탭에 IoT Central의 디바이스 및 모듈 상태가 표시됩니다. 

![디바이스 연결](./media/tutorial-add-edge-as-leaf-device/cfiotcmodulestatus.png)


양식에 클라우드 속성이 표시됩니다(이전 단계에서 만든 디바이스 템플릿). 값을 입력하고 **저장**을 클릭합니다. 

![디바이스 연결](./media/tutorial-add-edge-as-leaf-device/deviceinfo.png)

대시보드 타일

![디바이스 연결](./media/tutorial-add-edge-as-leaf-device/dashboard.png)

이 자습서에서는 다음 방법에 대해 알아보았습니다.

* Azure IoT Edge 디바이스 추가
* SAS 키를 사용하여 프로비저닝에 도움이 되도록 Azure IoT Edge 디바이스 구성
* IoT Central에서 대시보드, 모듈 상태 보기
* Azure IoT Edge 디바이스에서 실행되는 모듈에 명령 보내기
* Azure IoT Edge 디바이스에서 실행되는 모듈에서 속성 설정

## <a name="next-steps"></a>다음 단계

IoT Central에서 Azure IoT Edge 디바이스를 관리하는 방법을 알아보았으니, 후속 단계로

<!-- Next how-tos in the sequence -->

이 자습서에 이어 투명 게이트웨이 구성 방법을 진행하시기 바랍니다.

> [!div class="nextstepaction"]
> [투명 게이트웨이 구성](../../iot-edge/how-to-create-transparent-gateway.md)
