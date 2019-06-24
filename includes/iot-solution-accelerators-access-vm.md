---
title: 포함 파일
description: 포함 파일
services: iot-accelerators
author: dominicbetts
ms.service: iot-accelerators
ms.topic: include
ms.date: 08/16/2018
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: 8d1f0f6ae3ffc123f1ae8318a6fbce4a9a3024fa
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "66814913"
---
## <a name="access-the-virtual-machine"></a>가상 머신에 액세스

다음 단계는 Azure Cloud Shell에서 Azure CLI를 사용 합니다. 원한다 면 있습니다 [Azure CLI 설치](/cli/azure/install-azure-cli) 개발의 컴퓨터 하 고 로컬로 명령을 실행 합니다.

다음 단계에서는 **SSH** 액세스를 허용하도록 Azure 가상 머신을 구성하는 방법을 보여줍니다. 표시되는 단계는 솔루션 가속기에 대해 선택한 이름이 **contoso-simulation**이라고 가정합니다. 이 값을 배포의 이름으로 바꿉니다.

1. 솔루션 가속기 리소스를 포함하는 리소스 그룹의 콘텐츠를 나열합니다.

    ```azurecli-interactive
    az resource list -g contoso-simulation -o table
    ```

    가상 머신, 공용 IP 주소 및 네트워크 보안 그룹의 이름을 기록해 둡니다. 나중에 이러한 값이 필요합니다.

1. SSH 액세스를 허용하도록 네트워크 보안 그룹을 업데이트합니다. 다음 명령에서는 네트워크 보안 그룹의 이름이 **contoso-simulation-nsg**라고 가정합니다. 이 값을 네트워크 보안 그룹의 이름으로 바꿉니다.

    ```azurecli-interactive
    az network nsg rule update --name SSH --nsg-name contoso-simulation-nsg -g contoso-simulation --access Allow -o table
    ```

    테스트 및 개발하는 동안 SSH 액세스만 활성화합니다. SSH를 활성화하는 경우 [가능한 한 빨리 비활성화해야 합니다](https://docs.microsoft.com/azure/security/azure-security-network-security-best-practices#disable-rdpssh-access-to-virtual-machines).

1. 가상 머신에서 **azureuser** 계정에 대한 암호를 아는 암호로 업데이트합니다. 다음 명령을 실행하는 경우 고유한 암호를 선택합니다.

    ```azurecli-interactive
    az vm user update --name vm-vikxv --username azureuser --password YOURSECRETPASSWORD  -g contoso-simulation
    ```

1. 가상 머신의 공용 IP 주소를 찾습니다. 다음 명령에서는 가상 머신의 이름이 **vm-vikxv**라고 가정합니다. 이 값을 이전에 기록해 둔 가상 머신의 이름으로 바꿉니다.

    ```azurecli-interactive
    az vm list-ip-addresses --name vm-vikxv -g contoso-simulation -o table
    ```

    가상 머신의 공용 IP 주소를 기록해 둡니다.
