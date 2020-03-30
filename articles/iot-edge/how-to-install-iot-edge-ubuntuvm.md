---
title: Ubuntu Virtual Machines에서 Azure IoT Edge 실행 | Microsoft Docs
description: 우분투 18.04 LTS 가상 머신에 대 한 Azure IoT 가장자리 설정 지침
author: toolboc
manager: veyalla
ms.reviewer: kgremban
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 03/19/2020
ms.author: pdecarlo
ms.openlocfilehash: 64e2787aa282e75893fa34e6de1373e6afed09fe
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "80349628"
---
# <a name="run-azure-iot-edge-on-ubuntu-virtual-machines"></a>Ubuntu Virtual Machines에서 Azure IoT Edge 실행

Azure IoT Edge 런타임은 디바이스를 IoT Edge 디바이스로 바꿔줍니다. 런타임은 Raspberry Pi처럼 작은 디바이스 또는 산업용 서버처럼 큰 디바이스에 배포할 수 있습니다. IoT Edge 런타임을 사용하여 디바이스를 구성하면 클라우드에서 디바이스에 비즈니스 논리를 배포할 수 있습니다.

IoT Edge 런타임의 작동 방식 및 포함되는 구성 요소에 대한 자세한 내용은 [Azure IoT Edge 런타임 및 해당 아키텍처 이해](iot-edge-runtime.md)를 참조하세요.

이 문서에서는 Azure IoT Edge 런타임이 설치되고 사전 제공된 장치 연결 문자열을 사용하여 구성된 Ubuntu 18.04 LTS 가상 컴퓨터를 배포하는 단계를 나열합니다. 배포는 [iotedge-vm 배포](https://github.com/Azure/iotedge-vm-deploy) 프로젝트 리포지토리에서 유지 관리되는 [클라우드 init](../virtual-machines/linux/using-cloud-init.md
) 기반 [Azure 리소스 관리자 템플릿을](../azure-resource-manager/templates/overview.md) 사용하여 수행됩니다.

첫 번째 부팅시, 우분투 18.04 LTS 가상 [머신은 클라우드 init을 통해 Azure IoT 에지 런타임의 최신 버전을 설치합니다.](https://github.com/Azure/iotedge-vm-deploy/blob/master/cloud-init.txt) 또한 런타임이 시작되기 전에 제공된 연결 문자열을 설정하여 SSH 또는 원격 데스크톱 세션을 시작할 필요 없이 IoT Edge 장치를 쉽게 구성하고 연결할 수 있습니다. 

## <a name="deploy-using-deploy-to-azure-button"></a>Azure 단추에 배포를 사용하여 배포

[Azure에 배포 단추를](../azure-resource-manager/templates/deploy-to-azure-button.md) 사용하면 GitHub에서 유지 관리되는 [Azure 리소스 관리자 템플릿을](../azure-resource-manager/templates/overview.md) 간소화할 수 있습니다.  이 섹션에서는 [iotedge-vm 배포](https://github.com/Azure/iotedge-vm-deploy) 프로젝트 리포지토리에 포함된 Azure 단추에 대한 배포 의 사용을 보여 줍니다.  


1. iotedge-vm 배포 Azure 리소스 관리자 템플릿을 사용하여 Azure IoT Edge 지원 Linux VM을 배포합니다.  시작하려면 아래 버튼을 클릭하십시오.

    [![iotedge-vm 배포를 위해 Azure 단추에 배포](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fazure%2Fiotedge-vm-deploy%2Fmaster%2FedgeDeploy.json)

1. 새로 시작된 창에서 사용 가능한 양식 필드를 입력합니다.

    > [!div class="mx-imgBorder"]
    > [![iotedge-vm 배포 템플릿을 보여주는 스크린샷](./media/how-to-install-iot-edge-ubuntuvm/iotedge-vm-deploy.png)](./media/how-to-install-iot-edge-ubuntuvm/iotedge-vm-deploy.png)

    **구독**: 가상 컴퓨터를 배포하는 활성 Azure 구독입니다.

    **리소스 그룹**: 가상 컴퓨터를 포함하도록 기존 또는 새로 만든 리소스 그룹이며 관련 리소스입니다.

    **DNS 레이블 접두사**: 가상 시스템의 호스트 이름을 접두사로 지정하는 데 사용되는 선택한 필수 값입니다.

    **관리자 사용자 이름**: 배포시 루트 권한이 제공되는 사용자 이름입니다.

    **장치 연결 문자열**: 의도한 [IoT Hub](../iot-hub/about-iot-hub.md)내에서 생성된 장치의 장치 연결 [문자열입니다.](how-to-register-device.md)

    **VM 크기**: 배포할 가상 시스템의 [크기](../cloud-services/cloud-services-sizes-specs.md)

    **우분투 OS 버전**: 우분투 OS의 버전은 기본 가상 머신에 설치됩니다.

    **위치**: 가상 컴퓨터를 배포할 [지리적 영역으로,](https://azure.microsoft.com/global-infrastructure/locations/) 이 값은 선택한 리소스 그룹의 위치로 기본설정됩니다.

    **인증 유형**: 기본 설정에 따라 **sshPublicKey** 또는 **암호를** 선택합니다.

    **관리자 암호 또는 키**: 인증 유형 선택에 따라 SSH 공개 키의 값 또는 암호 값입니다.

    모든 필드가 채워진 경우 페이지 하단의 확인란을 선택하여 약관에 동의하고 **구입을** 선택하여 배포를 시작합니다.

1. 배포가 성공적으로 완료되었는지 확인합니다.  가상 시스템 리소스가 선택한 리소스 그룹에 배포되어야 합니다.  컴퓨터 이름을 기록해 두면 형식이 `vm-0000000000000`어야합니다. 또한 형식에 `<dnsLabelPrefix>`있어야 하는 관련 **DNS 이름을**기록해 둡니다. `<location>`.cloudapp.azure.com.

    **DNS 이름은** Azure 포털 내에서 새로 배포된 가상 시스템의 **개요** 섹션에서 가져올 수 있습니다.

    > [!div class="mx-imgBorder"]
    > [![iotedge vm의 dns 이름을 보여주는 스크린샷](./media/how-to-install-iot-edge-ubuntuvm/iotedge-vm-dns-name.png)](./media/how-to-install-iot-edge-ubuntuvm/iotedge-vm-dns-name.png)

1. 설치 후 이 VM에 SSH를 사용하려면 연결된 **DNS 이름을** 명령과 함께 사용합니다.`ssh <adminUsername>@<DNS_Name>`

## <a name="deploy-from-azure-cli"></a>Azure CLI에서 배포

1. 다음과 같은 경우 Azure CLI iot 확장을 설치한 것이 있는지 확인합니다.
    ```azurecli-interactive
    az extension add --name azure-iot
    ```

1. 다음으로 데스크톱에서 Azure CLI를 사용하는 경우 먼저 로그인하여 시작합니다.

   ```azurecli-interactive
   az login
   ```

1. 구독이 여러 개인 경우 사용할 구독을 선택합니다.
   1. 구독 나열:

      ```azurecli-interactive
      az account list --output table
      ```

   1. 사용하려는 구독ID 필드의 복사

   1. 복사한 ID로 작업 구독 설정:

      ```azurecli-interactive
      az account set -s <SubscriptionId>
      ```

1. 새 리소스 그룹을 만들거나, 다음 단계에서 기존 리소스 그룹을 지정합니다.

   ```azurecli-interactive
   az group create --name IoTEdgeResources --location westus2
   ```

1. 새 가상 머신을 만듭니다.

    **인증 유형을** `password`사용하려면 아래 예제를 참조하십시오.

   ```azurecli-interactive
   az group deployment create \
   --name edgeVm \
   --resource-group IoTEdgeResources \
   --template-uri "https://aka.ms/iotedge-vm-deploy" \
   --parameters dnsLabelPrefix='my-edge-vm1' \
   --parameters adminUsername='<REPLACE_WITH_USERNAME>' \
   --parameters deviceConnectionString=$(az iot hub device-identity show-connection-string --device-id <REPLACE_WITH_DEVICE-NAME> --hub-name <REPLACE-WITH-HUB-NAME> -o tsv) \
   --parameters authenticationType='password' \
   --parameters adminPasswordOrKey="<REPLACE_WITH_SECRET_PASSWORD>"
   ```

    SSH 키를 사용 하 여 인증 하려면 **인증을** 지정 `sshPublicKey`하 여 이렇게 할 수 있습니다 .의 인증 유형 다음 **adminPasswordOrKey** 매개 변수에서 SSH 키의 값을 제공 합니다.  아래에 예제가 나와 있습니다.

    ```azurecli-interactive
    #Generate the SSH Key
    ssh-keygen -m PEM -t rsa -b 4096 -q -f ~/.ssh/iotedge-vm-key -N ""  

    #Create a VM using the iotedge-vm-deploy script
    az group deployment create \
    --name edgeVm \
    --resource-group IoTEdgeResources \
    --template-uri "https://aka.ms/iotedge-vm-deploy" \
    --parameters dnsLabelPrefix='my-edge-vm1' \
    --parameters adminUsername='<REPLACE_WITH_USERNAME>' \
    --parameters deviceConnectionString=$(az iot hub device-identity show-connection-string --device-id <REPLACE_WITH_DEVICE-NAME> --hub-name <REPLACE-WITH-HUB-NAME> -o tsv) \
    --parameters authenticationType='sshPublicKey' \
    --parameters adminPasswordOrKey="$(< ~/.ssh/iotedge-vm-key.pub)"
     
    ```

1. 배포가 성공적으로 완료되었는지 확인합니다.  가상 시스템 리소스가 선택한 리소스 그룹에 배포되어야 합니다.  컴퓨터 이름을 기록해 두면 형식이 `vm-0000000000000`어야합니다. 또한 형식에 `<dnsLabelPrefix>`있어야 하는 관련 **DNS 이름을**기록해 둡니다. `<location>`.cloudapp.azure.com.

    **DNS 이름은** **공용 SSH** 항목의 일부로 출력 섹션 내에서 이전 단계의 JSON 형식의 출력에서 가져올 수 **있습니다.**  이 항목의 값은 새로 배포된 컴퓨터에 SSH를 사용할 수 있습니다.

    ```bash
    "outputs": {
      "public SSH": {
        "type": "String",
        "value": "ssh <adminUsername>@<DNS_Name>"
      }
    }
    ```

    **DNS 이름은** Azure 포털 내에서 새로 배포된 가상 시스템의 **개요** 섹션에서 가져올 수도 있습니다.

    > [!div class="mx-imgBorder"]
    > [![iotedge vm의 dns 이름을 보여주는 스크린샷](./media/how-to-install-iot-edge-ubuntuvm/iotedge-vm-dns-name.png)](./media/how-to-install-iot-edge-ubuntuvm/iotedge-vm-dns-name.png)

1. 설치 후 이 VM에 SSH를 사용하려면 연결된 **DNS 이름을** 명령과 함께 사용합니다.`ssh <adminUsername>@<DNS_Name>`

## <a name="next-steps"></a>다음 단계

설치된 런타임을 사용하여 IoT Edge 디바이스를 프로비전했으므로 [IoT Edge 모듈을 배포](how-to-deploy-modules-portal.md)할 수 있습니다.

IoT Edge 런타임 설치에 문제가 있는 경우 문제 [해결](troubleshoot.md) 페이지를 확인하십시오.

기존 설치를 최신 버전의 IoT Edge로 업데이트하려면 [IoT Edge 보안 디먼 및 런타임 업데이트](how-to-update-iot-edge.md)를 참조하세요.

SSH 또는 기타 인바운드 연결을 통해 VM에 액세스하기 위해 포트를 열려면 Linux [VM에 대한 포트 및 끝점을 여는](../virtual-machines/linux/nsg-quickstart.md) Azure 가상 컴퓨터 설명서를 참조하십시오.
