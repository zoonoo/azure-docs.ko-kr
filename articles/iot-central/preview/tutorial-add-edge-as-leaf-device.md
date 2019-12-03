---
title: Azure IoT Central에 Azure IoT Edge 디바이스 추가 | Microsoft Docs
description: 운영자가 Azure IoT Edge 디바이스를 Azure IoT Central 애플리케이션에 추가합니다.
author: rangv
ms.author: rangv
ms.date: 10/22/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: peterpr
ms.openlocfilehash: f16db7ebff087b164228f2b23d6fa7ec302705bb
ms.sourcegitcommit: dd0304e3a17ab36e02cf9148d5fe22deaac18118
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/22/2019
ms.locfileid: "74406339"
---
# <a name="tutorial-add-an-azure-iot-edge-device-to-your-azure-iot-central-application"></a>자습서: Azure IoT Central 애플리케이션에 Azure IoT Edge 디바이스 추가

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

이 자습서에서는 Azure IoT Edge 디바이스를 Azure IoT Central 애플리케이션에 추가하고 구성하는 방법을 보여 줍니다. 이를 위해 여기서는 Azure Marketplace에서 IoT Edge를 사용할 수 있는 Linux VM을 선택했습니다.

이 자습서는 2부로 구성되어 있습니다.

* 먼저, 운영자가 IoT Edge 디바이스의 클라우드 우선 프로비저닝을 수행하는 방법을 알아봅니다.
* 다음으로, IoT Edge 디바이스의 "디바이스 우선" 프로비저닝을 수행하는 방법을 알아봅니다.

이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * 새 IoT Edge 디바이스 추가
> * SAS(공유 액세스 서명) 키를 사용하여 프로비저닝을 지원하도록 IoT Edge 디바이스 구성
> * IoT Central에서 대시보드 및 모듈 상태 보기
> * IoT Edge 디바이스에서 실행되는 모듈에 명령 보내기
> * IoT Edge 디바이스에서 실행되는 모듈에 대한 속성 설정

## <a name="prerequisites"></a>필수 조건

이 자습서를 완료하려면 Azure IoT Central 애플리케이션이 필요합니다. [이 빠른 시작](./quick-deploy-iot-central.md)에 따라 Azure IoT Central 애플리케이션을 만듭니다.

## <a name="enable-azure-iot-edge-enrollment-group"></a>Azure IoT Edge 등록 그룹 사용
**관리** 페이지에서 SAS 키를 Azure IoT Edge 등록 그룹에 사용하도록 설정합니다.

![디바이스 연결이 강조 표시된 관리 페이지의 스크린샷](./media/tutorial-add-edge-as-leaf-device/groupenrollment.png)

## <a name="provision-a-cloud-first-azure-iot-edge-device"></a>"클라우드 우선" Azure IoT Edge 디바이스 프로비저닝  
이 섹션에서는 환경 센서 템플릿을 사용하여 새 IoT Edge 디바이스를 만들고 디바이스를 프로비저닝합니다. **디바이스** > **환경 센서 템플릿**을 차례로 선택합니다. 

![환경 센서 템플릿이 강조 표시된 디바이스 페이지의 스크린샷](./media/tutorial-add-edge-as-leaf-device/deviceexplorer.png)

**+ 새로 만들기**를 선택하고, 선택한 디바이스 ID와 이름을 입력합니다. **만들기**를 선택합니다.

![디바이스 ID 및 만들기가 강조 표시된 새 디바이스 만들기 대화 상자의 스크린샷](./media/tutorial-add-edge-as-leaf-device/cfdevicecredentials.png)

디바이스가 **등록됨** 모드로 전환됩니다.

![디바이스 상태가 강조 표시된 환경 센서 템플릿 페이지의 스크린샷](./media/tutorial-add-edge-as-leaf-device/cfregistered.png)

## <a name="deploy-an-iot-edge-enabled-linux-vm"></a>IoT Edge 사용 Linux VM 배포

> [!NOTE]
> 모든 머신 또는 디바이스를 사용하도록 선택할 수 있습니다. 운영 체제는 Linux 또는 Windows일 수 있습니다.

이 자습서에서는 Azure에 만든 Azure IoT 사용 Linux VM을 사용합니다. [Azure Marketplace](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/microsoft_iot_edge.iot_edge_vm_ubuntu?tab=Overview)에서 **지금 받기**를 선택합니다. 

![지금 받기가 강조 표시된 Azure Marketplace의 스크린샷](./media/tutorial-add-edge-as-leaf-device/cfmarketplace.png)

**계속**을 선택합니다.

![계속이 강조 표시된 Azure에서 이 앱 만들기 대화 상자의 스크린샷](./media/tutorial-add-edge-as-leaf-device/cfmarketplacecontinue.png)


Azure Portal로 이동합니다. **만들기**를 선택합니다.

![만들기가 강조 표시된 Azure Portal의 스크린샷](./media/tutorial-add-edge-as-leaf-device/cfubuntu.png)

**구독**을 선택하고, 새 리소스 그룹을 만들고, VM 가용성에 대해 **(US) 미국 서부 2**를 선택합니다. 그런 다음, 사용자 및 암호 정보를 입력합니다. 이러한 정보는 이후 단계에서 필요하므로 기억해야 합니다. **검토 + 만들기**를 선택합니다.

![다양한 옵션이 강조 표시된 가상 머신 세부 정보 만들기 페이지의 스크린샷](./media/tutorial-add-edge-as-leaf-device/cfvm.png)

유효성 검사 통과 후에 **만들기**를 선택합니다.

![유효성 검사 통과 및 만들기가 강조 표시된 가상 머신 만들기 페이지의 스크린샷](./media/tutorial-add-edge-as-leaf-device/cfvmvalidated.png)

리소스를 만드는 데 몇 분 정도 걸립니다. **리소스로 이동**을 선택합니다.

![리소스로 이동이 강조 표시된 배포 완료 페이지의 스크린샷](./media/tutorial-add-edge-as-leaf-device/cfvmdeploymentcomplete.png)

### <a name="provision-vm-as-an-iot-edge-device"></a>VM을 IoT Edge 디바이스로 프로비저닝 

**지원 + 문제 해결** 아래에서 **직렬 콘솔**을 선택합니다.

![직렬 콘솔이 강조 표시된 지원 + 문제 해결 옵션의 스크린샷](./media/tutorial-add-edge-as-leaf-device/cfserialconsole.png)

다음과 비슷한 화면이 표시됩니다.

![콘솔의 스크린샷](./media/tutorial-add-edge-as-leaf-device/cfconsole.png)

Enter 키를 누르고, 프롬프트에 따라 사용자 이름과 암호를 제공한 다음, Enter 키를 다시 누릅니다. 

![콘솔의 스크린샷](./media/tutorial-add-edge-as-leaf-device/cfconsolelogin.png)

명령을 관리자("root" 사용자) 권한으로 실행하려면 **sudo su –** 를 입력합니다.

![콘솔의 스크린샷](./media/tutorial-add-edge-as-leaf-device/cfsudo.png)

IoT Edge 런타임 버전을 확인합니다. 이 문서를 작성한 시점에서 현재 GA 버전은 1.0.8입니다.

![콘솔의 스크린샷](./media/tutorial-add-edge-as-leaf-device/cfconsoleversion.png)

Vim 편집기를 설치하거나, 원하는 경우 Nano를 사용합니다. 

![콘솔의 스크린샷](./media/tutorial-add-edge-as-leaf-device/cfconsolevim.png)

![콘솔의 스크린샷](./media/tutorial-add-edge-as-leaf-device/cfvim.png)

IoT Edge config.yaml 파일을 편집합니다.

![콘솔의 스크린샷](./media/tutorial-add-edge-as-leaf-device/cfconsoleconfig.png)

아래로 스크롤하여 yaml 파일의 연결 문자열 부분을 주석으로 처리합니다. 

**이전**

![콘솔의 스크린샷](./media/tutorial-add-edge-as-leaf-device/cfmanualprovisioning.png)

**이후**(Esc 키를 누르고 소문자 a를 눌러 편집을 시작합니다.)

![콘솔의 스크린샷](./media/tutorial-add-edge-as-leaf-device/cfmanualprovisioningcomments.png)

yaml 파일에서 대칭 키 부분의 주석 처리를 제거합니다. 

**이전**

![콘솔의 스크린샷](./media/tutorial-add-edge-as-leaf-device/cfconsolesymmcomments.png)

**이후**

![콘솔의 스크린샷](./media/tutorial-add-edge-as-leaf-device/cfconsolesymmuncomments.png)

IoT Central로 이동합니다. IoT Edge 디바이스의 범위 ID, 디바이스 ID 및 대칭 키를 가져옵니다.
![다양한 디바이스 연결 옵션이 강조 표시된 IoT Central의 스크린샷](./media/tutorial-add-edge-as-leaf-device/cfdeviceconnect.png)

Linux 컴퓨터로 이동하여 범위 ID와 등록 ID를 디바이스 ID와 대칭 키로 바꿉니다.

Esc 키를 누르고 **:wq!** 를 입력합니다. Enter 키를 눌러 변경 내용을 저장합니다.

IoT Edge를 다시 시작하여 변경 내용을 처리하고 Enter 키를 누릅니다.

![콘솔의 스크린샷](./media/tutorial-add-edge-as-leaf-device/cfrestart.png)

**iotedge 목록**을 입력합니다. 몇 분 후에 세 개의 모듈이 배포되었음을 알 수 있습니다.

![콘솔의 스크린샷](./media/tutorial-add-edge-as-leaf-device/cfconsolemodulelist.png)


## <a name="iot-central-device-explorer"></a>IoT Central 디바이스 탐색기 

IoT Central에서 디바이스가 프로비저닝됨 상태로 전환됩니다.

![디바이스 상태가 강조 표시된 IoT Central 디바이스 옵션의 스크린샷](./media/tutorial-add-edge-as-leaf-device/cfprovisioned.png)

**모듈** 탭에서 IoT Central의 디바이스 및 모듈의 상태가 표시됩니다. 

![IoT Central 모듈 탭의 스크린샷](./media/tutorial-add-edge-as-leaf-device/cfiotcmodulestatus.png)


양식에서 이전 단계에서 만든 디바이스 템플릿의 클라우드 속성을 확인할 수 있습니다. 값을 입력하고, **저장**을 선택합니다. 

![내 Linux Edge 디바이스 양식의 스크린샷](./media/tutorial-add-edge-as-leaf-device/deviceinfo.png)

대시보드 타일 형식으로 표시된 보기는 다음과 같습니다.

![내 Linux Edge 디바이스 대시보드 타일의 스크린샷](./media/tutorial-add-edge-as-leaf-device/dashboard.png)

## <a name="next-steps"></a>다음 단계

이제 IoT Central에서 IoT Edge 디바이스를 사용하고 관리하는 방법을 알아보았으므로 제안되는 다음 단계는 다음과 같습니다.

<!-- Next how-tos in the sequence -->

> [!div class="nextstepaction"]
> [투명한 게이트웨이 구성](../../iot-edge/how-to-create-transparent-gateway.md)
