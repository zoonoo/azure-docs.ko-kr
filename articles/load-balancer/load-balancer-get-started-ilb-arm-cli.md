---
title: "내부 부하 분산 장치 만들기 - Azure CLI | Microsoft Docs"
description: "Resource Manager에서 Azure CLI를 사용하여 내부 부하 분산 장치를 만드는 방법에 대해 알아봅니다."
services: load-balancer
documentationcenter: na
author: kumudd
manager: timlt
tags: azure-resource-manager
ms.assetid: c7a24e92-b4da-43c0-90f2-841c1b7ce489
ms.service: load-balancer
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/23/2017
ms.author: kumud
translationtype: Human Translation
ms.sourcegitcommit: 0d8472cb3b0d891d2b184621d62830d1ccd5e2e7
ms.openlocfilehash: 128b91c685b5f7e494a69ca5b04165a0ee7cbb78
ms.lasthandoff: 03/21/2017

---

# <a name="create-an-internal-load-balancer-by-using-the-azure-cli"></a>Azure CLI를 사용하여 내부 부하 분산 장치 만들기

> [!div class="op_single_selector"]
> * [Azure Portal](../load-balancer/load-balancer-get-started-ilb-arm-portal.md)
> * [PowerShell](../load-balancer/load-balancer-get-started-ilb-arm-ps.md)
> * [Azure CLI](../load-balancer/load-balancer-get-started-ilb-arm-cli.md)
> * [템플릿](../load-balancer/load-balancer-get-started-ilb-arm-template.md)

[!INCLUDE [load-balancer-get-started-ilb-intro-include.md](../../includes/load-balancer-get-started-ilb-intro-include.md)]

> [!NOTE]
> Azure에는 리소스를 만들고 작업하는 [Resource Manager와 클래식](../azure-resource-manager/resource-manager-deployment-model.md)이라는 두 가지 배포 모델이 있습니다.  이 문서에서는 Resource Manager 배포 모델 사용을 설명하며 Microsoft에서는 대부분의 새로운 배포에 대해 [클래식 배포 모델](load-balancer-get-started-ilb-classic-cli.md) 대신 이 모델을 사용하도록 권장합니다.

[!INCLUDE [load-balancer-get-started-ilb-scenario-include.md](../../includes/load-balancer-get-started-ilb-scenario-include.md)]

## <a name="deploy-the-solution-by-using-the-azure-cli"></a>Azure CLI를 사용하여 솔루션 배포

다음 단계에서는 CLI와 함께 Azure Resource Manager를 사용하여 인터넷 연결 부하 분산 장치를 만드는 방법을 보여 줍니다. Azure Resource Manager를 사용하면 각 리소스가 개별적으로 생성되고 구성된 다음, 함께 사용되어 리소스를 만듭니다.

부하 분산 장치를 배포하려면 다음 개체를 만들고 구성해야 합니다.

* **프런트 엔드 IP 구성**: 들어오는 네트워크 트래픽에 대한 공용 IP 주소를 포함합니다.
* **백 엔드 주소 풀**: 부하 분산 장치의 네트워크 트래픽을 받는 가상 컴퓨터를 사용할 수 있게 하는 NIC(네트워크 인터페이스)를 포함합니다.
* **부하 분산 규칙**: 백 엔드 주소 풀에 있는 포트에 부하 분산 장치의 공용 포트를 매핑하는 규칙을 포함합니다.
* **인바운드 NAT 규칙**: 백 엔드 주소 풀에 있는 특정 가상 컴퓨터에 대한 포트에 부하 분산 장치의 공용 포트를 매핑하는 규칙을 포함합니다.
* **프로브**: 백 엔드 주소 풀의 가상 컴퓨터 인스턴스의 가용성을 확인하는 데 사용하는 상태 프로브를 포함합니다.

자세한 내용은 [부하 분산 장치에 대한 Azure Resource Manager 지원](load-balancer-arm.md)을 참조하세요.

## <a name="set-up-cli-to-use-resource-manager"></a>Resource Manager를 사용하도록 CLI 설치

1. Azure CLI를 사용한 적이 없다면 [Azure CLI 설치 및 구성](../cli-install-nodejs.md)을 참조하세요. Azure 계정 및 구독을 선택할 때까지 지침을 따릅니다.
2. 다음과 같이 **azure config mode** 명령을 실행하여 Resource Manager 모드로 전환합니다.

    ```azurecli
    azure config mode arm
    ```

    예상 출력:

        info:    New mode is arm

## <a name="create-an-internal-load-balancer-step-by-step"></a>내부 부하 분산 장치를 단계별로 만듭니다.

1. Azure에 로그인합니다.

    ```azurecli
    azure login
    ```

    메시지가 표시되면 Azure 자격 증명을 입력합니다.

2. 명령 도구를 Azure Resource Manager 모드로 변경합니다.

    ```azurecli
    azure config mode arm
    ```

## <a name="create-a-resource-group"></a>리소스 그룹 만들기

Azure Resource Manager의 모든 리소스는 리소스 그룹과 연결됩니다. 리소스 그룹을 아직 만들지 않았으면 만들도록 합니다.

```azurecli
azure group create <resource group name> <location>
```

## <a name="create-an-internal-load-balancer-set"></a>내부 부하 분산 장치 집합을 만듭니다.

1. 내부 부하 분산 장치 만들기

    다음 시나리오에서 nrprg라는 이름의 리소스 그룹이 미국 동부 지역에 만들어집니다.

    ```azurecli
    azure network lb create --name nrprg --location eastus
    ```

   > [!NOTE]
   > 가상 네트워크 및 가상 네트워크 서브넷 같은 내부 부하 분산 장치의 모든 리소스는 동일한 리소스 그룹 및 동일한 지역에 속해야 합니다.

2. 내부 부하 분산 장치의 프런트 엔드 IP 주소를 만듭니다.

    사용하는 IP 주소는 가상 네트워크의 서브넷 범위 안에 있어야 합니다.

    ```azurecli
    azure network lb frontend-ip create --resource-group nrprg --lb-name ilbset --name feilb --private-ip-address 10.0.0.7 --subnet-name nrpvnetsubnet --subnet-vnet-name nrpvnet
    ```

3. 백 엔드 주소 풀 만들기.

    ```azurecli
    azure network lb address-pool create --resource-group nrprg --lb-name ilbset --name beilb
    ```

    프런트 엔드 IP 주소 및 백 엔드 주소 풀을 정의한 후에 부하 분산 장치 규칙, 인바운드 NAT 규칙을 만들고 상태 프로브를 사용자 지정할 수 있습니다.

4. 내부 부하 분산 장치에 대한 부하 분산 장치 규칙을 만듭니다.

    이전 단계를 따를 경우, 이 명령은 프런트 엔드 풀의 1433 포트를 수신 대기하고, 역시 1433 포트를 사용하여 백 엔드 주소 풀에 부하 분산 네트워크 트래픽을 보내기 위한 부하 분산 장치 규칙을 만듭니다.

    ```azurecli
    azure network lb rule create --resource-group nrprg --lb-name ilbset --name ilbrule --protocol tcp --frontend-port 1433 --backend-port 1433 --frontend-ip-name feilb --backend-address-pool-name beilb
    ```

5. 인바운드 NAT 규칙을 만듭니다.

    인바운드 NAT 규칙은 특정 가상 컴퓨터 인스턴스로 이동할 부하 분산 장치에 끝점을 만드는 데 사용됩니다. 이전 단계에서 원격 데스크톱에 대해 두 개의 NAT 규칙 만들었습니다.

    ```azurecli
    azure network lb inbound-nat-rule create --resource-group nrprg --lb-name ilbset --name NATrule1 --protocol TCP --frontend-port 5432 --backend-port 3389

    azure network lb inbound-nat-rule create --resource-group nrprg --lb-name ilbset --name NATrule2 --protocol TCP --frontend-port 5433 --backend-port 3389
    ```

6. 부하 분산 장치에 대한 상태 프로브를 만듭니다.

    상태 프로브는 네트워크 트래픽을 보낼 수 있도록 모든 가상 컴퓨터 인스턴스를 검사합니다. 프로브 검사에 실패한 가상 컴퓨터 인스턴스는 다시 온라인 상태가 되어 프로브 검사가 정상으로 나올 때까지 부하 분산 장치에서 제거됩니다.

    ```azurecli
    azure network lb probe create --resource-group nrprg --lb-name ilbset --name ilbprobe --protocol tcp --interval 300 --count 4
    ```

    > [!NOTE]
    > Microsoft Azure 플랫폼에서는 다양한 관리 시나리오에 공개적으로 라우팅할 수 있는 고정 IPv4 주소를 사용합니다. IP 주소는 168.63.129.16입니다. 이 IP 주소를 방화벽으로 차단하면 안 됩니다. 예기치 않은 동작이 발생할 수 있습니다.
    > Azure 내부 부하 분산과 관련하여 이 IP 주소는 부하 분산된 집합에서 가상 컴퓨터의 상태를 확인하기 위해 부하 분산 장치에서 프로브를 모니터링하는 데 사용됩니다. 내부적으로 부하 분산된 집합의 Azure 가상 컴퓨터로 트래픽을 제한하는 네트워크 보안 그룹이 사용된 경우 168.63.129.16의 트래픽을 허용하도록 네트워크 보안 규칙을 추가해야 합니다.

## <a name="create-nics"></a>NIC 만들기

NIC를 만들고(또는 기존 NIC 수정) NAT 규칙, 부하 분산 장치 규칙 및 프로브에 연결해야 합니다.

1. *lb-nic1-be*라는 NIC를 만들고 *rdp1* NAT 규칙 및 *beilb* 백 엔드 주소 풀과 연결합니다.

    ```azurecli
    azure network nic create --resource-group nrprg --name lb-nic1-be --subnet-name nrpvnetsubnet --subnet-vnet-name nrpvnet --lb-address-pool-ids "/subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/backendAddressPools/beilb" --lb-inbound-nat-rule-ids "/subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/inboundNatRules/rdp1" --location eastus
    ```

    예상 출력:

        info:    Executing command network nic create
        + Looking up the network interface "lb-nic1-be"
        + Looking up the subnet "nrpvnetsubnet"
        + Creating network interface "lb-nic1-be"
        + Looking up the network interface "lb-nic1-be"
        data:    Id                              : /subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/networkInterfaces/lb-nic1-be
        data:    Name                            : lb-nic1-be
        data:    Type                            : Microsoft.Network/networkInterfaces
        data:    Location                        : eastus
        data:    Provisioning state              : Succeeded
        data:    Enable IP forwarding            : false
        data:    IP configurations:
        data:      Name                          : NIC-config
        data:      Provisioning state            : Succeeded
        data:      Private IP address            : 10.0.0.4
        data:      Private IP Allocation Method  : Dynamic
        data:      Subnet                        : /subscriptions/####################################/resourceGroups/NRPRG/providers/Microsoft.Network/virtualNetworks/NRPVnet/subnets/NRPVnetSubnet
        data:      Load balancer backend address pools
        data:        Id                          : /subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/backendAddressPools/NRPbackendpool
        data:      Load balancer inbound NAT rules:
        data:        Id                          : /subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/inboundNatRules/rdp1
        data:
        info:    network nic create command OK

2. *lb-nic2-be*라는 NIC를 만들고 *rdp2* NAT 규칙 및 *beilb* 백 엔드 주소 풀과 연결합니다.

    ```azurecli
    azure network nic create --resource-group nrprg --name lb-nic2-be --subnet-name nrpvnetsubnet --subnet-vnet-name nrpvnet --lb-address-pool-ids "/subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/backendAddressPools/beilb" --lb-inbound-nat-rule-ids "/subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/inboundNatRules/rdp2" --location eastus
    ```

3. *DB1*이라는 가상 컴퓨터를 만들고 *lb-nic1-be*라는 NIC에 연결합니다. *web1nrp* 라는 저장소 계정은 다음 명령을 실행되기 전에 만들어집니다.

    ```azurecli
    azure vm create --resource--resource-grouproup nrprg --name DB1 --location eastus --vnet-name nrpvnet --vnet-subnet-name nrpvnetsubnet --nic-name lb-nic1-be --availset-name nrp-avset --storage-account-name web1nrp --os-type Windows --image-urn MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:4.0.20150825
    ```
    > [!IMPORTANT]
    > 부하 분산 장치의 VM은 동일한 가용성 집합에 있어야 합니다. `azure availset create` 을(를) 사용하여 가용성 집합을 만듭니다.

4. *DB2*라는 VM(가상 컴퓨터)을 만들고 *lb-nic2-be*라는 NIC에 연결합니다. *web1nrp* 라는 저장소 계정은 다음 명령을 실행되기 전에 만들어졌습니다.

    ```azurecli
    azure vm create --resource--resource-grouproup nrprg --name DB2 --location eastus --vnet-name nrpvnet --vnet-subnet-name nrpvnetsubnet --nic-name lb-nic2-be --availset-name nrp-avset --storage-account-name web2nrp --os-type Windows --image-urn MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:4.0.20150825
    ```

## <a name="delete-a-load-balancer"></a>부하 분산 장치 삭제

부하 분산 장치를 제거하려면 다음 명령을 사용합니다.

```azurecli
azure network lb delete --resource-group nrprg --name ilbset
```

## <a name="next-steps"></a>다음 단계

[원본 IP 선호도를 사용하여 부하 분산 장치 배포 모드 구성](load-balancer-distribution-mode.md)

[부하 분산 장치에 대한 유휴 TCP 시간 제한 설정 구성](load-balancer-tcp-idle-timeout.md)


