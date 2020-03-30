---
title: Windows 서버 가상 머신에서 Azure IoT 에지 실행 | 마이크로 소프트 문서
description: Windows 서버 마켓플레이스 가상 머신의 Azure IoT 에지 설정 지침
author: gregman-msft
manager: arjmands
ms.reviewer: kgremban
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 06/12/2019
ms.author: philmea
ms.openlocfilehash: 5f88a21efd04c9dd24fe31e925a3b911b5ec9df2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77045886"
---
# <a name="run-azure-iot-edge-on-windows-server-virtual-machines"></a>Windows 서버 가상 컴퓨터에서 Azure IoT 에지 실행

Azure IoT Edge 런타임은 디바이스를 IoT Edge 디바이스로 바꿔줍니다. 런타임은 Raspberry Pi처럼 작은 디바이스 또는 산업용 서버처럼 큰 디바이스에 배포할 수 있습니다. IoT Edge 런타임을 사용하여 디바이스를 구성하면 클라우드에서 디바이스에 비즈니스 논리를 배포할 수 있습니다.

IoT Edge 런타임의 작동 방식 및 포함되는 구성 요소에 대한 자세한 내용은 [Azure IoT Edge 런타임 및 해당 아키텍처 이해](iot-edge-runtime.md)를 참조하세요.

이 문서에서는 Windows Server [Azure](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsserver.windowsserver?tab=Overview) 마켓플레이스 오퍼를 사용하여 Windows Server 2019 가상 컴퓨터에서 Azure IoT Edge 런타임을 실행하는 단계를 나열합니다. 다른 버전과 함께 사용할 수 위해 Windows에서 [Azure IoT Edge 런타임 설치의](how-to-install-iot-edge-windows.md) 지침을 따릅니다.

## <a name="deploy-from-the-azure-marketplace"></a>Azure Marketplace에서 배포

1. [Windows 서버](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsserver.windowsserver?tab=Overview) Azure 마켓플레이스 제품또는 [Azure 마켓플레이스에서](https://azuremarketplace.microsoft.com/) "Windows 서버"를 검색하여 이동
2. **지금 받기** 선택
3. **소프트웨어 계획에서**"컨테이너가 있는 Windows Server 2019 데이터 센터 서버 코어"를 찾은 다음 다음 대화 상자에서 **계속을** 선택합니다.
    * 컨테이너가 있는 다른 버전의 Windows Server에 이 지침을 사용할 수도 있습니다.
4. Azure Portal에서 **만들기**를 선택하고 마법사를 따라 VM을 배포합니다.
    * VM을 처음 사용해 본 적이 있다면 암호를 사용하고 공용 인바운드 포트 메뉴에서 RDP 및 SSH를 사용하도록 설정하는 것이 가장 쉽습니다.
    * 리소스를 많이 사용하는 워크로드가 있는 경우 CPU 및/또는 메모리를 더 추가하여 가상 머신 크기를 업그레이드해야 합니다.
5. 가상 머신이 배포되고 나면, IoT Hub에 연결되도록 구성합니다.
    1. IoT Hub에서 만든 IoT Edge 장치에서 장치 연결 문자열을 복사합니다. [Azure 포털에서 연결 문자열 검색](how-to-register-device.md#retrieve-the-connection-string-in-the-azure-portal)프로시저를 참조하십시오.
    1. Azure Portal에서 새로 만든 가상 머신 리소스를 선택하고 **명령 실행** 옵션을 엽니다.
    1. **런파워쉘스크립트** 옵션 선택
    1. 장치 연결 문자열을 이 스크립트를 명령 창에 복사합니다.

        ```powershell
        . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
        Install-IoTEdge -Manual -DeviceConnectionString '<connection-string>'
        ```

    1. 스크립트를 실행하여 IoT Edge 런타임을 설치하고 **실행을** 선택하여 연결 문자열을 설정합니다.
    1. 1~2분 후에 Edge 런타임이 설치되고 성공적으로 프로비전되었다는 메시지가 표시됩니다.

## <a name="deploy-from-the-azure-portal"></a>Azure 포털에서 배포

1. Azure 포털에서 "Windows 서버"를 검색하고 **Windows Server 2019 데이터 센터를** 선택하여 VM 만들기 워크플로를 시작합니다.
2. **소프트웨어 요금제 선택에서** "컨테이너가 있는 Windows Server 2019 데이터 센터 서버 코어"를 선택한 다음 **만들기를** 선택합니다.
3. 위의 "Azure 마켓플레이스에서 배포" 지침에서 5단계를 완료합니다.

## <a name="deploy-from-azure-cli"></a>Azure CLI에서 배포

1. 데스크톱에서 Azure CLI를 사용하는 경우 다음에 로그인하여 시작합니다.

   ```azurecli-interactive
   az login
   ```

1. 구독이 여러 개인 경우 사용할 구독을 선택합니다.
   1. 구독 나열:

      ```azurecli-interactive
      az account list --output table
      ```

   1. 사용하려는 구독의 SubscriptionID 필드를 복사합니다.
   1. 복사한 ID를 가지고 이 명령을 실행합니다.

      ```azurecli-interactive
      az account set -s {SubscriptionId}
      ```

1. 새 리소스 그룹을 만들거나, 다음 단계에서 기존 리소스 그룹을 지정합니다.

   ```azurecli-interactive
   az group create --name IoTEdgeResources --location westus2
   ```

1. 새 가상 머신을 만듭니다.

   ```azurecli-interactive
   az vm create -g IoTEdgeResources -n EdgeVM --image MicrosoftWindowsServer:WindowsServer:2019-Datacenter-Core-with-Containers:latest  --admin-username azureuser --generate-ssh-keys --size Standard_DS1_v2
   ```

   * 이 명령은 암호를 묻는 메시지가 표시되지만 스크립트에서 `--admin-password` 더 쉽게 설정할 수 있는 옵션을 추가할 수 있습니다.
   * Windows Server Core 이미지에는 원격 데스크톱에서만 명령줄이 지원되므로 전체 데스크톱 `MicrosoftWindowsServer:WindowsServer:2019-Datacenter-with-Containers:latest` 환경을 원하는 경우 이미지로 지정합니다.

1. 장치 연결 문자열 설정(이 프로세스에 익숙하지 않은 경우 Azure CLI 프로시저를 [통해 연결 문자열 검색에](how-to-register-device.md#retrieve-the-connection-string-with-the-azure-cli) 따를 수 있음).

   ```azurecli-interactive
   az vm run-command invoke -g IoTEdgeResources -n EdgeVM --command-id RunPowerShellScript --script ". {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `Install-IoTEdge -Manual -DeviceConnectionString '<connection-string>'"
   ```

## <a name="next-steps"></a>다음 단계

설치된 런타임을 사용하여 IoT Edge 디바이스를 프로비전했으므로 [IoT Edge 모듈을 배포](how-to-deploy-modules-portal.md)할 수 있습니다.

Edge 런타임 설치에 문제가 있는 경우 [문제 해결](troubleshoot.md) 페이지를 확인하세요.

기존 설치를 최신 버전의 IoT Edge로 업데이트하려면 [IoT Edge 보안 디먼 및 런타임 업데이트](how-to-update-iot-edge.md)를 참조하세요.

Windows 가상 컴퓨터 [설명서에서](https://docs.microsoft.com/azure/virtual-machines/windows/)Windows 가상 컴퓨터 사용에 대해 자세히 알아봅니다.

설치 후 이 VM에 SSH를 사용하려면 원격 데스크톱 또는 원격 전원 셸을 사용하여 [Windows 서버용 OpenSSH 설치](https://docs.microsoft.com/windows-server/administration/openssh/openssh_install_firstuse#installing-openssh-with-powershell) 가이드를 따르십시오.
