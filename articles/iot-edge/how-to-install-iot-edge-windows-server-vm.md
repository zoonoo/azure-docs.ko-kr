---
title: Windows Server Virtual Machines에서 Azure IoT Edge를 실행 합니다. | Microsoft Docs
description: Azure IoT Edge 설치 지침은 Windows Server Marketplace Virtual Machines에서
author: gregman-msft
manager: arjmands
ms.reviewer: kgremban
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 06/12/2019
ms.author: gregman
ms.openlocfilehash: 9e3f7e3b23cba3fab87ee35aa2a15b6305d9ece4
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67054185"
---
# <a name="run-azure-iot-edge-on-windows-server-virtual-machines"></a>Windows Server Virtual Machines에서 Azure IoT Edge를 실행 합니다.
Azure IoT Edge 런타임은 디바이스를 IoT Edge 디바이스로 바꿔줍니다. 런타임은 Raspberry Pi처럼 작은 디바이스 또는 산업용 서버처럼 큰 디바이스에 배포할 수 있습니다. IoT Edge 런타임을 사용하여 디바이스를 구성하면 클라우드에서 디바이스에 비즈니스 논리를 배포할 수 있습니다.

IoT Edge 런타임의 작동 방식 및 포함되는 구성 요소에 대한 자세한 내용은 [Azure IoT Edge 런타임 및 해당 아키텍처 이해](iot-edge-runtime.md)를 참조하세요.

이 문서에서는 Azure IoT Edge 런타임을 사용 하 여 Windows Server 2019 가상 머신을 실행 하는 단계를 나열 합니다 [Windows Server](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsserver.windowsserver?tab=Overview) Azure Marketplace 제품입니다. 지침을 따르세요 [Azure IoT Edge 런타임을 설치](how-to-install-iot-edge-windows.md) 다른 버전과 함께 사용 하기 위해 Windows에서.

## <a name="deploy-from-the-azure-marketplace"></a>Azure Marketplace에서 배포
1.  로 이동 합니다 [Windows Server](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsserver.windowsserver?tab=Overview) Azure Marketplace 제품에서 "Windows Server"를 검색 하거나 [Azure Marketplace](https://azuremarketplace.microsoft.com/)
2.  선택 **가져오기 지금** 
3.  **소프트웨어 계획**"Windows Server 2019 Datacenter Server Core와 컨테이너" 찾아서 선택할 **계속** 다음 대화 상자에서.
    * 다른 버전의 Windows Server 컨테이너를 사용 하 여 이러한 지침을 사용할 수도 있습니다.
4.  Azure Portal에서 **만들기**를 선택하고 마법사를 따라 VM을 배포합니다. 
    *   처음으로 VM을 사용해 인 경우 것이 가장 암호를 사용 하 고 공용 인바운드 포트 메뉴에서 RDP 및 SSH를 사용 하도록 설정 합니다. 
    *   리소스를 많이 사용하는 워크로드가 있는 경우 CPU 및/또는 메모리를 더 추가하여 가상 머신 크기를 업그레이드해야 합니다.
5.  가상 머신이 배포되고 나면, IoT Hub에 연결되도록 구성합니다.
    1.  IoT Hub에 만든 IoT Edge 디바이스에서 디바이스 연결 문자열을 복사합니다. 이 프로세스에 익숙하지 않은 경우 [Azure Portal에서 새 Azure IoT Edge 디바이스 등록](how-to-register-device-portal.md) 방법 가이드를 따를 수 있습니다.
    1.  Azure Portal에서 새로 만든 가상 머신 리소스를 선택하고 **명령 실행** 옵션을 엽니다.
    1.  선택 된 **RunPowerShellScript** 옵션
    1.  장치 연결 문자열을 사용 하 여 명령 창에이 스크립트를 복사 합니다. 
        ```powershell
        . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
        Install-IoTEdge -Manual -DeviceConnectionString '<connection-string>'
        ```
    1.  Edge 런타임을 설치 하 고 선택 하 여 연결 문자열을 설정 하는 스크립트를 실행 **실행**
    1.  2 분 정도 후 Edge 런타임을 설치 하 고 성공적으로 프로 비전 된 메시지가 표시 됩니다.


## <a name="deploy-from-the-azure-portal"></a>Azure portal에서 배포
1. Azure portal에서 "Windows Server"를 검색 하 고 선택 **Windows Server 2019 Datacenter** VM 만들기 워크플로를 시작 합니다. 
2. **소프트웨어 플랜 선택** "Windows Server 2019 Datacenter Server Core와 컨테이너"를 선택한 다음 선택 **만들기**
3. 위의 "배포에서 Azure Marketplace에서" 5 단계 지침을 완료 합니다.

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
   1. 복사 ID를 사용 하 여이 명령을 실행 합니다.
    
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
   * 이 명령은 묻는 암호에 대 한 옵션을 추가할 수 있습니다 하지만 `--admin-password` 스크립트에서 보다 쉽게 설정 하려면
   * Windows Server Core 이미지에 명령 지원만 원격 데스크톱을 사용 하 여 줄, 따라서 전체 데스크톱 환경을 원하는 경우 지정 `MicrosoftWindowsServer:WindowsServer:2019-Datacenter-with-Containers:latest` 이미지로

1. 디바이스 연결 문자열을 설정합니다. 이 프로세스에 익숙하지 않은 경우 [Azure CLI를 사용하여 새 Azure IoT Edge 디바이스 등록](how-to-register-device-cli.md) 방법 가이드를 따를 수 있습니다.

   ```azurecli-interactive
   az vm run-command invoke -g IoTEdgeResources -n EdgeVM --command-id RunPowerShellScript --script ". {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `Install-IoTEdge -Manual -DeviceConnectionString '<connection-string>'"
   ```

## <a name="next-steps"></a>다음 단계

런타임을 설치하여 IoT Edge 디바이스를 프로비전했으므로 [IoT Edge 모듈을 배포](how-to-deploy-modules-portal.md)할 수 있습니다.

Edge 런타임이 제대로 설치를 사용 하 여 문제를 겪고 있는 경우 체크 아웃 합니다 [문제 해결](troubleshoot.md) 페이지입니다.

기존 설치를 최신 버전의 IoT Edge로 업데이트하려면 [IoT Edge 보안 디먼 및 런타임 업데이트](how-to-update-iot-edge.md)를 참조하세요.

Windows virtual machines에서 사용 하는 방법에 대 한 자세한 정보는 [Windows Virtual Machines 설명서](https://docs.microsoft.com/azure/virtual-machines/windows/)합니다.

수행 하려는 경우 SSH이이 VM에 설치 후에 [설치의 OpenSSH Windows Server 용](https://docs.microsoft.com/windows-server/administration/openssh/openssh_install_firstuse#installing-openssh-with-powershell) 가이드 원격 데스크톱 또는 원격 powershell을 사용 하 여 합니다.
