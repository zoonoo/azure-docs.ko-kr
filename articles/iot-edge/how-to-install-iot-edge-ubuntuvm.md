---
title: Ubuntu Virtual Machines에서 Azure IoT Edge 실행 | Microsoft Docs
description: Ubuntu 18.04 LTS Virtual Machines에 대 한 Azure IoT Edge 설정 지침
author: toolboc
manager: veyalla
ms.reviewer: kgremban
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 06/29/2020
ms.author: pdecarlo
ms.custom: devx-track-azurecli
ms.openlocfilehash: 95fd10ab7de4885d3630b5defe4080fe0203b62f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91296980"
---
# <a name="run-azure-iot-edge-on-ubuntu-virtual-machines"></a>Ubuntu Virtual Machines에서 Azure IoT Edge 실행

Azure IoT Edge 런타임은 디바이스를 IoT Edge 디바이스로 바꿔줍니다. 런타임은 Raspberry Pi처럼 작은 디바이스 또는 산업용 서버처럼 큰 디바이스에 배포할 수 있습니다. 디바이스가 IoT Edge 런타임을 사용하여 구성되면 클라우드에서 디바이스에 비즈니스 논리를 배포할 수 있습니다.

IoT Edge 런타임의 작동 방식 및 포함되는 구성 요소에 대한 자세한 내용은 [Azure IoT Edge 런타임 및 해당 아키텍처 이해](iot-edge-runtime.md)를 참조하세요.

이 문서에는 미리 제공 된 장치 연결 문자열을 사용 하 여 설치 하 고 구성 된 Azure IoT Edge 런타임을 사용 하 여 Ubuntu 18.04 LTS 가상 머신을 배포 하는 단계가 나와 있습니다. 배포는 [iotedge-vm 배포](https://github.com/Azure/iotedge-vm-deploy) 프로젝트 리포지토리에서 관리 되는 [클라우드 init](../virtual-machines/linux/using-cloud-init.md
) 기반 [Azure Resource Manager 템플릿을](../azure-resource-manager/templates/overview.md) 사용 하 여 수행 됩니다.

첫 번째 부팅에서 Ubuntu 18.04 LTS 가상 머신은 [클라우드 init를 통해 Azure IoT Edge 런타임의 최신 버전을 설치](https://github.com/Azure/iotedge-vm-deploy/blob/master/cloud-init.txt)합니다. 또한 런타임이 시작 되기 전에 제공 된 연결 문자열을 설정 하 여 SSH 또는 원격 데스크톱 세션을 시작할 필요 없이 IoT Edge 장치를 쉽게 구성 하 고 연결할 수 있습니다. 

## <a name="deploy-using-deploy-to-azure-button"></a>Azure에 배포 단추를 사용 하 여 배포

[Azure에 배포 단추](../azure-resource-manager/templates/deploy-to-azure-button.md) 를 사용 하면 GitHub에서 유지 관리 되는 [Azure Resource Manager 템플릿을](../azure-resource-manager/templates/overview.md) 효율적으로 배포할 수 있습니다.  이 섹션에서는 [iotedge-vm 배포](https://github.com/Azure/iotedge-vm-deploy) 프로젝트 리포지토리에 포함 된 Azure에 배포 단추를 사용 하는 방법을 보여 줍니다.  


1. Iotedge-vm 배포 Azure Resource Manager 템플릿을 사용 하 여 Azure IoT Edge 사용 가능한 Linux VM을 배포 합니다.  시작하려면 아래 단추를 클릭합니다.

    [![iotedge-vm-deploy를 위한 Azure에 배포하기 단추](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fazure%2Fiotedge-vm-deploy%2Fmaster%2FedgeDeploy.json)

1. 새로 시작 된 창에서 사용 가능한 양식 필드를 입력 합니다.

    > [!div class="mx-imgBorder"]
    > [![iotedge-vm-deploy 템플릿을 보여 주는 스크린샷](./media/how-to-install-iot-edge-ubuntuvm/iotedge-vm-deploy.png)](./media/how-to-install-iot-edge-ubuntuvm/iotedge-vm-deploy.png)

    **구독**: 가상 머신을 배포할 활성 Azure 구독입니다.

    **리소스 그룹**: 가상 컴퓨터와 연결 된 리소스를 포함 하는 기존 또는 새로 만든 리소스 그룹입니다.

    **DNS 레이블 접두사**: 가상 컴퓨터의 호스트 이름에 접두사를 추가할 때 사용 하는 필수 값입니다.

    **관리자 사용자 이름**: 배포에 대 한 루트 권한이 제공 되는 사용자 이름입니다.

    **장치 연결 문자열**: 의도 한 [IoT Hub](../iot-hub/about-iot-hub.md)내에 생성 된 장치에 대 한 [장치 연결 문자열](how-to-register-device.md) 입니다.

    **VM 크기**: 배포할 가상 컴퓨터의 [크기](../cloud-services/cloud-services-sizes-specs.md) 입니다.

    **UBUNTU Os 버전**: 기본 가상 머신에 설치할 ubuntu os 버전입니다.

    **위치**: 가상 머신을 배포할 [지리적 지역](https://azure.microsoft.com/global-infrastructure/locations/) 입니다 .이 값은 기본적으로 선택 된 리소스 그룹의 위치로 설정 됩니다.

    **인증 유형**: 기본 설정에 따라 **sshPublicKey** 또는 **password** 를 선택 합니다.

    **관리자 암호 또는 키**: 인증 유형 선택에 따라 SSH 공개 키의 값 또는 암호 값입니다.

    모든 필드가 채워져 있으면 페이지 맨 아래에 있는 확인란을 선택 하 여 약관에 동의한 다음 **구매** 를 선택 하 여 배포를 시작 합니다.

1. 배포가 성공적으로 완료되었는지 확인합니다.  가상 머신 리소스는 선택한 리소스 그룹에 배포되어야 합니다.  컴퓨터 이름을 기록해 둡니다 .이 이름은 형식 이어야 합니다 `vm-0000000000000` . 또한 연결된 **DNS 이름**을 기록해 둡니다. 형식은 `<dnsLabelPrefix>`.`<location>`.cloudapp.azure.com이어야 합니다.

    **DNS 이름**은 Azure Portal 내에 새로 배포된 가상 머신의 **개요** 섹션에서 가져올 수 있습니다.

    > [!div class="mx-imgBorder"]
    > [![Iotedge vm의 dns 이름을 보여 주는 스크린샷](./media/how-to-install-iot-edge-ubuntuvm/iotedge-vm-dns-name.png)](./media/how-to-install-iot-edge-ubuntuvm/iotedge-vm-dns-name.png)

1. 설치 후에이 VM으로 SSH 하려면 다음 명령을 사용 하 여 연결 된 **DNS 이름을** 사용 합니다.  `ssh <adminUsername>@<DNS_Name>`

## <a name="deploy-from-azure-cli"></a>Azure CLI에서 배포

1. 다음을 사용 하 여 Azure CLI iot 확장을 설치 했는지 확인 합니다.
    ```azurecli-interactive
    az extension add --name azure-iot
    ```

1. 그런 다음 바탕 화면에서 Azure CLI를 사용 하는 경우 로그인 하 여 시작 합니다.

   ```azurecli-interactive
   az login
   ```

1. 구독이 여러 개인 경우 사용 하려는 구독을 선택 합니다.
   1. 구독 나열:

      ```azurecli-interactive
      az account list --output table
      ```

   1. 사용 하려는 구독에 대 한 SubscriptionID 필드를 복사 합니다.

   1. 복사한 ID를 사용 하 여 작업 구독을 설정 합니다.

      ```azurecli-interactive
      az account set -s <SubscriptionId>
      ```

1. 새 리소스 그룹을 만들거나, 다음 단계에서 기존 리소스 그룹을 지정합니다.

   ```azurecli-interactive
   az group create --name IoTEdgeResources --location westus2
   ```

1. 새 가상 머신을 만듭니다.

    **AuthenticationType** 을 사용 하려면 `password` 아래 예제를 참조 하세요.

   ```azurecli-interactive
   az deployment group create \
   --resource-group IoTEdgeResources \
   --template-uri "https://aka.ms/iotedge-vm-deploy" \
   --parameters dnsLabelPrefix='my-edge-vm1' \
   --parameters adminUsername='<REPLACE_WITH_USERNAME>' \
   --parameters deviceConnectionString=$(az iot hub device-identity connection-string show --device-id <REPLACE_WITH_DEVICE-NAME> --hub-name <REPLACE-WITH-HUB-NAME> -o tsv) \
   --parameters authenticationType='password' \
   --parameters adminPasswordOrKey="<REPLACE_WITH_SECRET_PASSWORD>"
   ```

    SSH 키를 사용 하 여 인증 하려면 **authenticationType** 를 지정 하 `sshPublicKey` 고 **adminpasswordorkey** 매개 변수에서 ssh 키의 값을 제공 하면 됩니다.  아래에 예제가 나와 있습니다.

    ```azurecli-interactive
    #Generate the SSH Key
    ssh-keygen -m PEM -t rsa -b 4096 -q -f ~/.ssh/iotedge-vm-key -N ""  

    #Create a VM using the iotedge-vm-deploy script
    az deployment group create \
    --resource-group IoTEdgeResources \
    --template-uri "https://aka.ms/iotedge-vm-deploy" \
    --parameters dnsLabelPrefix='my-edge-vm1' \
    --parameters adminUsername='<REPLACE_WITH_USERNAME>' \
    --parameters deviceConnectionString=$(az iot hub device-identity connection-string show --device-id <REPLACE_WITH_DEVICE-NAME> --hub-name <REPLACE-WITH-HUB-NAME> -o tsv) \
    --parameters authenticationType='sshPublicKey' \
    --parameters adminPasswordOrKey="$(< ~/.ssh/iotedge-vm-key.pub)"
    ```

1. 배포가 성공적으로 완료되었는지 확인합니다.  가상 머신 리소스는 선택한 리소스 그룹에 배포되어야 합니다.  컴퓨터 이름을 기록해 둡니다 .이 이름은 형식 이어야 합니다 `vm-0000000000000` . 또한 연결된 **DNS 이름**을 기록해 둡니다. 형식은 `<dnsLabelPrefix>`.`<location>`.cloudapp.azure.com이어야 합니다.

    **DNS 이름은** 이전 단계의 JSON 형식 출력에서 가져올 수 있으며,이는 **공용 SSH** 항목의 일부로 **출력** 섹션 내에서 가져올 수 있습니다.  이 항목의 값을 사용 하 여 새로 배포 된 컴퓨터에 SSH를 수행할 수 있습니다.

    ```bash
    "outputs": {
      "public SSH": {
        "type": "String",
        "value": "ssh <adminUsername>@<DNS_Name>"
      }
    }
    ```

    Azure Portal 내에 새로 배포 된 가상 머신의 **개요** 섹션에서 **DNS 이름을** 가져올 수도 있습니다.

    > [!div class="mx-imgBorder"]
    > [![Iotedge vm의 dns 이름을 보여 주는 스크린샷](./media/how-to-install-iot-edge-ubuntuvm/iotedge-vm-dns-name.png)](./media/how-to-install-iot-edge-ubuntuvm/iotedge-vm-dns-name.png)

1. 설치 후에이 VM으로 SSH 하려면 다음 명령을 사용 하 여 연결 된 **DNS 이름을** 사용 합니다.  `ssh <adminUsername>@<DNS_Name>`

## <a name="next-steps"></a>다음 단계

설치된 런타임을 사용하여 IoT Edge 디바이스를 프로비전했으므로 [IoT Edge 모듈을 배포](how-to-deploy-modules-portal.md)할 수 있습니다.

IoT Edge 런타임에 제대로 설치 하는 데 문제가 있는 경우 [문제 해결](troubleshoot.md) 페이지를 확인 하세요.

기존 설치를 최신 버전의 IoT Edge로 업데이트하려면 [IoT Edge 보안 디먼 및 런타임 업데이트](how-to-update-iot-edge.md)를 참조하세요.

SSH 또는 다른 인바운드 연결을 통해 VM에 액세스 하는 포트를 열려면 [LINUX VM에 대 한 포트 및 끝점을 여](../virtual-machines/linux/nsg-quickstart.md) 는 방법에 대 한 Azure Virtual Machines 설명서를 참조 하세요.
