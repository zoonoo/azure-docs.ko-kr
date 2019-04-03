---
title: Azure CLI를 사용하여 부하 분산 및 아웃바운드 규칙 구성
titlesuffix: Azure Load Balancer
description: 이 문서에서는 Azure CLI를 사용하여 표준 Load Balancer에서 부하 분산 및 아웃바운드 규칙을 구성하는 방법을 보여줍니다.
services: load-balancer
documentationcenter: na
author: KumudD
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/01/2019
ms.author: kumud
ms.openlocfilehash: 0b46cbdec6d0ffe2a614a976f70b833726fb0e8a
ms.sourcegitcommit: 04716e13cc2ab69da57d61819da6cd5508f8c422
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/02/2019
ms.locfileid: "58849949"
---
# <a name="configure-load-balancing-and-outbound-rules-in-standard-load-balancer-using-azure-cli"></a>Azure CLI를 사용하여 표준 Load Balancer에서 부하 분산 및 아웃바운드 규칙 구성

이 빠른 시작에서는 Azure CLI를 사용하여 표준 Load Balancer에서 아웃바운드 규칙을 구성하는 방법을 보여줍니다.  

모두 완료하면 Load Balancer 리소스에 두 개의 프런트 엔드 및 프런트 엔드와 연결된 규칙이 포함되며, 하나는 인바운드용이고 다른 하나는 아웃바운드용입니다.  각 프런트 엔드에는 공용 IP 주소에 대한 참조가 있으며, 이 시나리오에서는 인바운드와 아웃바운드에 각각 다른 공용 IP 주소를 사용합니다.   부하 분산 규칙은 인바운드 부하 분산만 제공하고 아웃바운드 규칙은 VM에 제공된 아웃바운드 NAT를 제어합니다.  이 빠른 시작 사용 하 여 두 개의 별도 백 엔드 풀에 대 한 인바운드 및 아웃 바운드, 기능을 설명 하 고이 시나리오에 대 한 유연성을 허용 합니다.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)] 

CLI를 로컬로 설치하고 사용하도록 선택하는 경우 이 자습서에서는 Azure CLI 버전 2.0.28 이상을 실행해야 합니다. 버전을 확인하려면 `az --version`을 실행합니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 2.0 설치]( /cli/azure/install-azure-cli)를 참조하세요.

## <a name="create-resource-group"></a>리소스 그룹 만들기

[az group create](https://docs.microsoft.com/cli/azure/group)를 사용하여 리소스 그룹을 만듭니다. Azure 리소스 그룹은 Azure 리소스가 배포 및 관리되는 논리적 컨테이너입니다.

다음 예제에서는 *eastus2* 위치에 *myresourcegroupoutbound*라는 리소스 그룹을 만듭니다.

```azurecli-interactive
  az group create \
    --name myresourcegroupoutbound \
    --location eastus2
```
## <a name="create-virtual-network"></a>가상 네트워크 만들기
[az network vnet create](https://docs.microsoft.com/cli/azure/network/vnet)를 사용하여 *myresourcegroupoutbound*에 *mysubnetoutbound*라는 서브넷이 있는 *myvnetoutbound*라는 가상 네트워크를 만듭니다.

```azurecli-interactive
  az network vnet create \
    --resource-group myresourcegroupoutbound \
    --name myvnetoutbound \
    --address-prefix 192.168.0.0/16 \
    --subnet-name mysubnetoutbound
    --subnet-prefix 192.168.0.0/24
```

## <a name="create-inbound-public-ip-address"></a>인바운드 공용 IP 주소 만들기 

인터넷에서 웹앱에 액세스하려면 부하 분산 장치에 대한 공용 IP 주소가 필요합니다. 표준 부하 분산 장치는 표준 공용 IP 주소만 지원합니다. [az network public-ip create](https://docs.microsoft.com/cli/azure/network/public-ip) 명령을 사용하여 *myresourcegroupoutbound*에 *mypublicipinbound*라는 표준 공용 IP 주소를 만듭니다.

```azurecli-interactive
  az network public-ip create --resource-group myresourcegroupoutbound --name mypublicipinbound --sku standard
```

## <a name="create-outbound-public-ip-address"></a>아웃바운드 공용 IP 주소 만들기 

[az network public-ip create](https://docs.microsoft.com/cli/azure/network/public-ip) 명령을 사용하여 Load Balancer 프런트 엔드 아웃바운드 구성의 표준 IP 주소를 만듭니다.

```azurecli-interactive
  az network public-ip create --resource-group myresourcegroupoutbound --name mypublicipoutbound --sku standard
```

## <a name="create-azure-load-balancer"></a>Azure Load Balancer 만들기

이 섹션에서는 다음과 같은 부하 분산 장치 구성 요소를 만들고 구성하는 방법에 대해 자세히 설명합니다.
  - 부하 분산 장치에서 들어오는 네트워크 트래픽을 수신하는 프런트 엔드 IP.
  - 프런트 엔드 IP는 부하를 보내는 백 엔드 풀에는 네트워크 트래픽을 분산 합니다.
  - 아웃 바운드 연결에 대 한 백 엔드 풀입니다. 
  - 백 엔드 VM 인스턴스의 상태를 확인하는 상태 프로브.
  - 트래픽이 VM에 분산되는 방법을 정의하는 부하 분산 장치 인바운드 규칙.
  - 트래픽이 VM에 분산되는 방법을 정의하는 부하 분산 장치 아웃바운드 규칙.

### <a name="create-load-balancer"></a>부하 분산 장치 만들기

Load Balancer를 사용 하 여 인바운드 IP 주소 만들기 [az network lb 만듭니다](https://docs.microsoft.com/cli/azure/network/lb?view=azure-cli-latest) 라는 *lb* 인바운드 프런트 엔드 IP 구성 및 백 엔드 풀을 포함 하는 *bepoolinbound*공용 IP 주소와 연결 된 *mypublicipinbound* 이전 단계에서 만든 합니다.

```azurecli-interactive
  az network lb create \
    --resource-group myresourcegroupoutbound \
    --name lb \
    --sku standard \
    --backend-pool-name bepoolinbound \
    --frontend-ip-name myfrontendinbound \
    --location eastus2 \
    --public-ip-address mypublicipinbound   
  ```

### <a name="create-outbound-pool"></a>아웃 바운드 풀 만들기

풀을 사용 하 여 Vm에 대 한 아웃 바운드 연결을 정의 하려면 추가 백 엔드 주소 풀을 만듭니다 [az network lb address-pool-만듭니다](https://docs.microsoft.com/cli/azure/network/lb?view=azure-cli-latest) 이름의 *bepooloutbound*합니다.  최대한의 유연성을 제공 하는 별도 아웃 바운드 풀 만들기 하지만이 단계를 생략 하 고만 인바운드를 사용할 수 있습니다 *bepoolinbound* 도 합니다.

```azurecli-interactive
  az network lb address-pool \
    --resource-group myresourcegroupoutbound \
    --lb-name lb \
    --name bepooloutbound
```

### <a name="create-outbound-frontend-ip"></a>아웃바운드 프런트 엔드 IP 만들기
[az network lb frontend-ip create](https://docs.microsoft.com/cli/azure/network/lb?view=azure-cli-latest) 명령을 사용하여 공용 IP 주소 *mypublicipoutbound*에 연결된 *myfrontendoutbound*라는 아웃바운드 프런트 엔드 IP 구성을 포함하는 Load Balancer용 아웃바운드 프런트 엔드 IP 구성을 만듭니다.

```azurecli-interactive
  az network lb frontend-ip create \
    --resource-group myresourcegroupoutbound \
    --name myfrontendoutbound \
    --lb-name lb \
    --public-ip-address mypublicipoutbound 
  ```

### <a name="create-health-probe"></a>상태 프로브 만들기

상태 프로브는 네트워크 트래픽을 보낼 수 있도록 모든 가상 컴퓨터 인스턴스를 검사합니다. 프로브 검사에 실패한 가상 머신 인스턴스는 다시 온라인 상태가 되어 프로브 검사가 정상으로 나올 때까지 부하 분산 장치에서 제거됩니다. [az network lb probe create](https://docs.microsoft.com/cli/azure/network/lb/probe?view=azure-cli-latest)를 사용하여 가상 머신의 상태를 모니터링하는 상태 프로브를 만듭니다. 

```azurecli-interactive
  az network lb probe create \
    --resource-group myresourcegroupoutbound \
    --lb-name lb \
    --name http \
    --protocol http \
    --port 80 \
    --path /  
```

### <a name="create-load-balancing-rule"></a>부하 분산 규칙 만들기

부하 분산 장치 규칙은 들어오는 트래픽에 대한 프런트 엔드 IP 구성 및 트래픽을 받을 백 엔드 풀과 필요한 원본 및 대상 포트를 함께 정의합니다. [az network lb rule create](https://docs.microsoft.com/cli/azure/network/lb/rule?view=azure-cli-latest) 명령을 사용하여 프런트 엔드 풀 *myfrontendinbound*의 포트 80에서 수신 대기하고 마찬가지로 포트 80을 사용하여 백 엔드 주소 풀 *bepool*에 부하 분산된 네트워크 트래픽을 보내는 *myinboundlbrule*이라는 부하 분산 장치 규칙을 만듭니다. 

>[!NOTE]
>이 부하 분산 규칙은 --disable-outbound-snat 매개 변수를 사용하는 이 규칙의 결과로 자동 아웃바운드 (S)NAT를 해제합니다. 아웃바운드 NAT는 아웃바운드 규칙을 통해서만 제공됩니다.

```azurecli-interactive
az network lb rule create \
--resource-group myresourcegroupoutbound \
--lb-name lb \
--name inboundlbrule \
--protocol tcp \
--frontend-port 80 \
--backend-port 80 \
--probe http \
--frontend-ip-name myfrontendinbound \
--backend-pool-name bepoolinbound \
--disable-outbound-snat
```

### <a name="create-outbound-rule"></a>아웃바운드 규칙 만들기

아웃바운드 규칙은 *myfrontendoutbound*를 통해 표현되는 프런트 엔드 공용 IP를 정의하며, 모든 아웃바운드 NAT 트래픽과 이 규칙이 적용되는 백 엔드 풀에 사용됩니다.  백 엔드 풀의 모든 가상 머신(NIC IP 구성) 아웃바운드 네트워크 변환에 대한 아웃바운드 규칙 *myoutboundrule*을 *bepool* 백 엔드 풀에 만듭니다.  또한 아래 명령은 아웃바운드 유휴 시간 제한을 4분에서 15분으로 변경하고 1024 대신 10000 SNAT 포트를 할당합니다.  자세한 내용은 [아웃바운드 규칙](https://aka.ms/lboutboundrules)을 참조하세요.

```azurecli-interactive
az network lb outbound-rule create \
 --resource-group myresourcegroupoutbound \
 --lb-name lb \
 --name outboundrule \
 --frontend-ip-configs myfrontendoutbound \
 --protocol All \
 --idle-timeout 15 \
 --outbound-ports 10000 \
 --address-pool bepooloutbound
```

별도 아웃 바운드 풀을 사용 하려면 지정 하는 이전 명령의 주소 풀 인수를 변경할 수 있습니다 *bepoolinbound* 대신 합니다.  유연성 및 가독성 결과 구성에 대 한 별도 풀을 사용 하도록 권장 합니다.

이 시점에서 백 엔드 풀 VM의 추가 사용 하 여 진행할 수 있습니다 *bepoolinbound* __하 고__ *bepooloutbound* 각 NIC의 IP 구성을 업데이트 하 여 사용 하 여 리소스 [az network nic ip 구성-address-pool add](https://docs.microsoft.com/cli/azure/network/lb/rule?view=azure-cli-latest)합니다.

## <a name="clean-up-resources"></a>리소스 정리

더 이상 필요하지 않은 경우 [az group delete](/cli/azure/group#az-group-delete) 명령을 사용하여 리소스 그룹, 부하 분산 장치 및 모든 관련 리소스를 제거할 수 있습니다.

```azurecli-interactive 
  az group delete --name myresourcegroupoutbound
```

## <a name="next-steps"></a>다음 단계
이 문서에서는 표준 Load Balancer를 만들고, 인바운드 부하 분산 장치 트래픽 규칙을 구성하고, 백 엔드 풀의 VM에 대한 상태 프로브를 구성했습니다. Azure Load Balancer에 대해 자세히 알아보려면 Azure Load Balancer에 대한 자습서를 계속 진행합니다.

> [!div class="nextstepaction"]
> [Azure Load Balancer 자습서](tutorial-load-balancer-standard-public-zone-redundant-portal.md)
