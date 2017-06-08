---
title: "Azure에서 Linux 가상 컴퓨터 모니터링 | Microsoft Docs"
description: "Azure에서 Linux 가상 컴퓨터에 대한 부팅 진단과 성능 메트릭을 모니터링하는 방법에 대해 알아봅니다."
services: virtual-machines-linux
documentationcenter: virtual-machines
author: davidmu1
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/08/2017
ms.author: davidmu
ms.translationtype: Human Translation
ms.sourcegitcommit: 44eac1ae8676912bc0eb461e7e38569432ad3393
ms.openlocfilehash: 7599ea9fef4b2e8368ffdc18f357fe8938cc024f
ms.contentlocale: ko-kr
ms.lasthandoff: 05/17/2017

---

# <a name="how-to-monitor-a-linux-virtual-machine-in-azure"></a>Azure에서 Linux 가상 컴퓨터를 모니터링하는 방법

Azure에서 VM(가상 컴퓨터)이 올바르게 실행되도록 부팅 진단과 성능 메트릭을 검토할 수 있습니다. 이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * VM에서 부팅 진단을 사용하도록 설정
> * 부트 진단 보기
> * 호스트 메트릭 보기
> * VM에서 진단 확장을 사용하도록 설정
> * VM 메트릭 보기
> * 진단 메트릭을 기반으로 하는 알림 만들기
> * 고급 모니터링 설정

이 자습서에는 Azure CLI 버전 2.0.4 이상이 필요합니다. `az --version`을 실행하여 버전을 찾습니다. 업그레이드해야 하는 경우 [Azure CLI 2.0 설치]( /cli/azure/install-azure-cli)를 참조하세요. 또한 브라우저에서 [Cloud Shell](/azure/cloud-shell/quickstart)을 사용할 수도 있습니다.


## <a name="create-vm"></a>VM 만들기
작동 중인 진단과 메트릭을 확인하려면 VM이 필요합니다. 먼저 [az group create](/cli/azure/gropu#create)를 사용하여 리소스 그룹을 만듭니다. 다음 예제에서는 *eastus* 위치에 *myResourceGroupMonitor*라는 리소스 그룹을 만듭니다.

```azurecli
az group create --name myResourceGroupMonitor --location eastus
```

이제 [az vm create](https://docs.microsoft.com/cli/azure/vm#create)로 VM을 만듭니다. 다음 예제에서는 *myVM*이라는 VM을 만듭니다.

```azurecli
az vm create \
  --resource-group myResourceGroupMonitor \
  --name myVM \
  --image UbuntuLTS \
  --admin-username azureuser \
  --generate-ssh-keys
```


## <a name="enable-boot-diagnostics"></a>부팅 진단을 사용하도록 설정

Linux VM이 부팅할 때 부팅 진단 확장에서는 부팅 출력을 캡처하여 Azure 저장소에 저장합니다. 이 데이터는 VM 부팅 문제를 해결하는 데 사용할 수 있습니다. Azure CLI를 사용하여 Linux VM을 만들 때 부팅 진단을 사용하도록 자동으로 설정되지 않습니다.

부팅 진단을 사용하도록 설정하기 전에 먼저 부팅 로그를 저장할 저장소 계정을 만들어야 합니다. 저장소 계정은 전역적으로 고유한 이름을 가져야 하며, 3-24자 사이의 숫자와 소문자만 포함해야 합니다. [az storage account create](/cli/azure/storage/account#create)를 사용하여 저장소 계정을 만듭니다. 이 예제에서는 임의의 문자열을 사용하여 고유한 저장소 계정 이름을 만듭니다. 

```azurecli
storageacct=mydiagdata$RANDOM

az storage account create \
  --resource-group myResourceGroupMonitor \
  --name $storageacct \
  --sku Standard_LRS \
  --location eastus
```

부팅 진단을 사용하도록 설정하는 경우 Blob 저장소 컨테이너에 대한 URI가 필요합니다. 다음 명령은 저장소 계정을 쿼리하여 이 URI를 반환합니다. URI 값은 *bloburi* 변수 이름에 저장되며 다음 단계에서 사용됩니다.

```azurecli
bloburi=$(az storage account show --resource-group myResourceGroupMonitor --name $storageacct --query 'primaryEndpoints.blob' -o tsv)
```

이제 [az vm boot-diagnostics enable](https://docs.microsoft.com/cli/azure/vm/boot-diagnostics#enable)을 사용하여 부팅 진단을 사용하도록 설정합니다. `--storage` 값은 이전 단계에서 수집된 Blob URI입니다.

```azurecli
az vm boot-diagnostics enable \
  --resource-group myResourceGroupMonitor \
  --name myVM \
  --storage $bloburi
```


## <a name="view-boot-diagnostics"></a>부트 진단 보기

부팅 진단을 사용하도록 설정되면, VM을 중지하고 시작할 때마다 부팅 프로세스에 대한 정보가 로그 파일에 기록됩니다. 이 예제에서는 먼저 다음과 같이 [az vm deallocate](/cli/azure/vm#deallocate) 명령을 사용하여 VM의 할당을 취소합니다.

```azurecli
az vm deallocate --resource-group myResourceGroupMonitor --name myVM
```

이제 다음과 같이 [az vm start]( /cli/azure/vm#stop) 명령을 사용하여 VM을 시작합니다.

```azurecli
az vm start --resource-group myResourceGroupMonitor --name myVM
```

다음과 같이 [az vm boot-diagnostics get-boot-log](https://docs.microsoft.com/cli/azure/vm/boot-diagnostics#get-boot-log) 명령을 사용하여 *myVM*에 대한 부팅 진단 데이터를 얻을 수 있습니다.

```azurecli
az vm boot-diagnostics get-boot-log --resource-group myResourceGroupMonitor --name myVM
```


## <a name="view-host-metrics"></a>호스트 메트릭 보기

Linux VM에는 Azure에서 상호 작용하는 전용 호스트가 있습니다. 이 호스트에 대한 메트릭이 자동으로 수집되며, 다음과 같이 Azure Portal에서 볼 수 있습니다.

1. Azure Portal에서 **리소스 그룹**을 클릭하고 **myResourceGroupMonitor**를 선택한 다음 리소스 목록에서 **myVM**을 선택합니다.
2. 호스트 VM의 성능을 보려면 VM 블레이드에서 **메트릭**을 클릭한 다음 **사용 가능한 메트릭**에서 *[호스트]* 메트릭 중 하나를 선택합니다.

    ![호스트 메트릭 보기](./media/tutorial-monitoring/monitor-host-metrics.png)


## <a name="install-diagnostics-extension"></a>진단 확장 설치

기본 호스트 메트릭을 사용할 수 있지만, 더 세분화된 VM 관련 메트릭을 보려면 VM에 Azure 진단 확장을 설치해야 합니다. Azure 진단 확장을 사용하면 VM에서 추가 모니터링 및 진단 데이터를 검색할 수 있습니다. 이러한 성능 메트릭을 보고 VM 성능에 따라 경고를 만들 수 있습니다. 진단 확장은 다음과 같이 Azure Portal을 통해 설치됩니다.

1. Azure Portal에서 **리소스 그룹**을 클릭하고 **myResourceGroup**을 선택한 다음 리소스 목록에서 **myVM**을 선택합니다.
2. **진단 설정**을 클릭합니다. 목록에서는 *부팅 진단*이 이전 섹션에서 이미 사용하도록 설정되었음을 보여 줍니다. *기본 메트릭*에 대한 확인란을 클릭합니다.
3. *저장소 계정* 섹션에서 이전 섹션에서 만든 *mydiagdata[1234]* 계정을 찾아 선택합니다.
4. **저장** 단추를 클릭합니다.

    ![진단 메트릭 보기](./media/tutorial-monitoring/enable-diagnostics-extension.png)


## <a name="view-vm-metrics"></a>VM 메트릭 보기

호스트 VM 메트릭을 본 것과 동일한 방법으로 VM 메트릭을 볼 수 있습니다.

1. Azure Portal에서 **리소스 그룹**을 클릭하고 **myResourceGroup**을 선택한 다음 리소스 목록에서 **myVM**을 선택합니다.
2. VM의 성능을 보려면 VM 블레이드에서 **메트릭**을 클릭한 다음 **사용 가능한 메트릭**에서 진단 메트릭 중 하나를 선택합니다.

    ![VM 메트릭 보기](./media/tutorial-monitoring/monitor-vm-metrics.png)


## <a name="create-alerts"></a>경고 만들기

특정 성능 메트릭을 기반으로 하는 경고를 만들 수 있습니다. 예를 들어 평균 CPU 사용량이 특정 임계값을 초과하거나 사용 가능한 디스크 공간이 일정량 이하로 떨어지면 경고를 사용하여 사용자에게 알릴 수 있습니다. 경고는 Azure Portal에 표시되거나 전자 메일을 통해 보낼 수 있습니다. 또한 생성되는 경고에 대한 응답으로 Azure Automation Runbook 또는 Azure Logic Apps를 트리거할 수도 있습니다.

다음 예제에서는 평균 CPU 사용량에 대한 경고를 만듭니다.

1. Azure Portal에서 **리소스 그룹**을 클릭하고 **myResourceGroup**을 선택한 다음 리소스 목록에서 **myVM**을 선택합니다.
2. VM 블레이드에서 **경고 규칙**을 클릭한 다음, 경고 블레이드 위쪽에 있는 **메트릭 경고 추가**를 클릭합니다.
4. *myAlertRule*과 같이 경고에 대한 **이름**을 입력합니다.
5. CPU 사용률이 5분 동안 1.0을 초과할 때 경고를 트리거하려면 다른 모든 기본값을 선택한 상태로 둡니다.
6. 필요한 경우 전자 메일 알림을 보내도록 *전자 메일 소유자, 참가자 및 읽기 권한자*의 확인란을 선택합니다. 기본 작업은 포털에서 알림을 제공하는 것입니다.
7. **확인** 단추를 클릭합니다.


## <a name="advanced-monitoring"></a>고급 모니터링 

[Operations Management Suite](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-overview)를 사용하여 VM에 대한 고급 모니터링을 수행할 수 있습니다. 아직 사용해 보지 않았으면 Operations Management Suite의 [평가판](https://www.microsoft.com/en-us/cloud-platform/operations-management-suite-trial)에 등록할 수 있습니다.

OMS 포털에 액세스할 수 있으면 [설정] 블레이드에서 작업 영역 키와 작업 영역 식별자를 찾을 수 있습니다. <workspace-key> 및 <workspace-id>를 OMS 작업 영역의 값으로 바꾼 다음, **az vm extension set**을 사용하여 VM에 OMS 확장을 추가할 수 있습니다.

```azurecli
az vm extension set \
  --resource-group myResourceGroupMonitor \
  --vm-name myVM \
  --name OmsAgentForLinux \
  --publisher Microsoft.EnterpriseCloud.Monitoring \
  --version 1.3 \
  --protected-settings '{"workspaceKey": "<workspace-key>"}' \
  --settings '{"workspaceId": "<workspace-id>"}'
```

OMS 포털의 [로그 검색] 블레이드에서 다음 그림과 같이 *myVM*이 표시됩니다.

![OMS 블레이드](./media/tutorial-monitoring/tutorial-monitor-oms.png)

## <a name="next-steps"></a>다음 단계

이 자습서에서는 Azure Security Center를 사용하여 VM을 구성하고 검토했습니다. 다음 방법에 대해 알아보았습니다.

> [!div class="checklist"]
> * VM에서 부팅 진단을 사용하도록 설정
> * 부트 진단 보기
> * 호스트 메트릭 보기
> * VM에서 진단 확장을 사용하도록 설정
> * VM 메트릭 보기
> * 진단 메트릭을 기반으로 하는 알림 만들기
> * 고급 모니터링 설정

Azure Security Center에 대해 알아보려면 다음 자습서로 진행합니다.

> [!div class="nextstepaction"]
> [VM 보안 관리](./tutorial-azure-security.md)
