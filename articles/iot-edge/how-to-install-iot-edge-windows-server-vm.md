---
title: Windows Server Virtual Machines에서 Azure IoT Edge를 실행 합니다. Microsoft Docs
description: Windows Server Marketplace의 설치 지침을 Azure IoT Edge Virtual Machines
author: gregman-msft
manager: arjmands
ms.reviewer: kgremban
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 06/12/2019
ms.author: philmea
ms.openlocfilehash: 380e354beb2f58b958e3c88d9f93ad0bda655971
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84266477"
---
# <a name="run-azure-iot-edge-on-windows-server-virtual-machines"></a>Windows Server Virtual Machines에서 Azure IoT Edge 실행

Azure IoT Edge 런타임은 디바이스를 IoT Edge 디바이스로 바꿔줍니다. 런타임은 Raspberry Pi처럼 작은 디바이스 또는 산업용 서버처럼 큰 디바이스에 배포할 수 있습니다. IoT Edge 런타임을 사용하여 디바이스를 구성하면 클라우드에서 디바이스에 비즈니스 논리를 배포할 수 있습니다.

IoT Edge 런타임의 작동 방식 및 포함되는 구성 요소에 대한 자세한 내용은 [Azure IoT Edge 런타임 및 해당 아키텍처 이해](iot-edge-runtime.md)를 참조하세요.

이 문서에는 windows [server](https://www.microsoft.com/cloud-platform/windows-server-pricing) Azure Marketplace 제품을 사용 하 여 windows server 2019 가상 머신에서 Azure IoT Edge 런타임을 실행 하는 단계가 나와 있습니다. 다른 버전과 함께 사용 하기 위해 Windows에 [Azure IoT Edge 런타임 설치](how-to-install-iot-edge-windows.md) 의 지침을 따르세요.

## <a name="deploy-from-the-azure-marketplace"></a>Azure Marketplace에서 배포

1. [Windows server](https://www.microsoft.com/cloud-platform/windows-server-pricing) Azure Marketplace 제품으로 이동 하거나 [Azure Marketplace](https://azuremarketplace.microsoft.com/) 에서 "windows server"를 검색 합니다.
2. **지금 가져오기** 를 선택 합니다.
3. **소프트웨어 계획**에서 "Windows Server 2019 Datacenter Server Core with 컨테이너"를 찾은 후 다음 대화 상자에서 **계속** 을 선택 합니다.
    * 또한 컨테이너를 사용 하 여 다른 버전의 Windows Server에 대해 이러한 지침을 사용할 수 있습니다.
4. Azure Portal에서 **만들기**를 선택하고 마법사를 따라 VM을 배포합니다.
    * VM을 처음 사용 하는 경우 암호를 사용 하 고 공용 인바운드 포트 메뉴에서 RDP 및 SSH를 사용 하는 것이 가장 쉽습니다.
    * 리소스를 많이 사용하는 워크로드가 있는 경우 CPU 및/또는 메모리를 더 추가하여 가상 머신 크기를 업그레이드해야 합니다.
5. 가상 머신이 배포되고 나면, IoT Hub에 연결되도록 구성합니다.
    1. IoT Hub에서 만든 IoT Edge 장치에서 장치 연결 문자열을 복사 합니다. [Azure Portal에서 연결 문자열 검색](how-to-register-device.md#retrieve-the-connection-string-in-the-azure-portal)절차를 참조 하십시오.
    1. Azure Portal에서 새로 만든 가상 머신 리소스를 선택하고 **명령 실행** 옵션을 엽니다.
    1. **Runpowershellscript** 옵션을 선택 합니다.
    1. 장치 연결 문자열을 사용 하 여 명령 창에이 스크립트를 복사 합니다.

        ```powershell
        . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
        Install-IoTEdge -Manual -DeviceConnectionString '<connection-string>'
        ```

    1. 스크립트를 실행 하 여 IoT Edge 런타임을 설치 하 고 **실행** 을 선택 하 여 연결 문자열을 설정 합니다.
    1. 1 ~ 2 분 후에 Edge 런타임이 성공적으로 설치 및 프로 비전 되었다는 메시지가 표시 됩니다.

## <a name="deploy-from-the-azure-portal"></a>Azure Portal에서 배포

1. Azure Portal에서 "Windows Server"를 검색 하 고 **Windows server 2019 Datacenter** 를 선택 하 여 VM 만들기 워크플로를 시작 합니다.
2. **소프트웨어 계획 선택** 에서 "Windows Server 2019 Datacenter Server Core with 컨테이너"를 선택한 다음 **만들기** 를 선택 합니다.
3. 위의 "Azure Marketplace에서 배포" 지침에서 5 단계를 완료 합니다.

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
   1. 복사한 ID를 사용 하 여이 명령을 실행 합니다.

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

   * 이 명령은 암호를 묻는 메시지를 표시 하지만 `--admin-password` 스크립트에서 더 쉽게 설정 하는 옵션을 추가할 수 있습니다.
   * Windows Server Core 이미지에는 원격 데스크톱 에서만 명령줄이 지원 되므로 전체 데스크톱 환경을 원하는 경우 이미지를로 지정 합니다. `MicrosoftWindowsServer:WindowsServer:2019-Datacenter-with-Containers:latest`

1. 장치 연결 문자열을 설정 합니다 .이 프로세스에 익숙하지 않은 경우 [Azure CLI를 사용 하 여 연결 문자열 검색](how-to-register-device.md#retrieve-the-connection-string-with-the-azure-cli) 절차를 따를 수 있습니다.

   ```azurecli-interactive
   az vm run-command invoke -g IoTEdgeResources -n EdgeVM --command-id RunPowerShellScript --script ". {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `Install-IoTEdge -Manual -DeviceConnectionString '<connection-string>'"
   ```

## <a name="next-steps"></a>다음 단계

설치된 런타임을 사용하여 IoT Edge 디바이스를 프로비전했으므로 [IoT Edge 모듈을 배포](how-to-deploy-modules-portal.md)할 수 있습니다.

Edge 런타임에 제대로 설치 하는 데 문제가 있는 경우 [문제 해결](troubleshoot.md) 페이지를 확인 하세요.

기존 설치를 최신 버전의 IoT Edge로 업데이트하려면 [IoT Edge 보안 디먼 및 런타임 업데이트](how-to-update-iot-edge.md)를 참조하세요.

Windows virtual machines 사용에 대 한 자세한 내용은 [Windows Virtual Machines 설명서](https://docs.microsoft.com/azure/virtual-machines/windows/)를 참조 하세요.

설치 후에이 VM으로 SSH 하려면 원격 데스크톱 또는 원격 powershell을 사용 하 여 [Windows Server 용 OpenSSH 설치](https://docs.microsoft.com/windows-server/administration/openssh/openssh_install_firstuse#installing-openssh-with-powershell) 가이드를 참조 하세요.
