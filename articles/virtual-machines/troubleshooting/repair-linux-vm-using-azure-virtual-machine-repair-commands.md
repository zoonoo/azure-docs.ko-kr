---
title: Azure Virtual Machine 복구 명령을 사용하여 Linux VM 복구 | Microsoft Docs
description: 이 문서에서는 Azure Virtual Machine 복구 명령을 사용하여 디스크를 다른 Windows VM에 연결함으로써 모든 오류를 수정한 다음, 원래 VM을 다시 빌드하는 방법을 자세히 설명합니다.
services: virtual-machines-linux
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: virtual-machines
ms.service: virtual-machines
ms.topic: troubleshooting
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.date: 09/10/2019
ms.author: v-miegge
ms.openlocfilehash: bfd3b2351a280f423ba0ef0b15318449554b5e3b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91595932"
---
# <a name="repair-a-linux-vm-by-using-the-azure-virtual-machine-repair-commands"></a>Azure Virtual Machine 복구 명령을 사용하여 Linux VM 복구

Azure에서 Linux VM(가상 머신)에 부팅 또는 디스크 오류가 발생하는 경우 디스크 자체에서 완화를 수행해야 할 수 있습니다. 일반적인 예로는 애플리케이션 업데이트가 실패하여 VM이 성공적으로 부팅되지 않는 경우입니다. 이 문서에서는 Azure Virtual Machine 복구 명령을 사용하여 디스크를 다른 Windows VM에 연결함으로써 모든 오류를 수정한 다음, 원래 VM을 다시 빌드하는 방법을 자세히 설명합니다.

> [!IMPORTANT]
> * 이 문서의 스크립트는 [Azure Resource Manager](../../azure-resource-manager/management/overview.md)를 사용하는 VM에만 적용됩니다.
> * 스크립트를 실행하려면 VM(포트 443)에서 아웃바운드 연결이 필요합니다.
> * 한 번에 하나의 스크립트만 실행할 수 있습니다.
> * 실행 중인 스크립트는 취소할 수 없습니다.
> * 스크립트를 실행할 수 있는 최대 시간은 90분으로, 그 후에는 시간이 초과됩니다.
> * Azure Disk Encryption를 사용 하는 Vm의 경우 단일 패스 암호화로 암호화 된 관리 디스크 (KEK 포함 또는 제외)만 지원 됩니다.

## <a name="repair-process-overview"></a>복구 프로세스 개요

이제 Azure Virtual Machine 복구 명령을 사용하여 VM의 OS 디스크를 변경할 수 있으며 더 이상 VM을 삭제하고 다시 만들 필요가 없습니다.

VM 문제를 해결하려면 다음 단계를 수행합니다.

1. Azure Cloud Shell 시작
2. az extension add/update 실행
3. az vm repair create 실행
4. Az vm repair run을 실행 하거나 완화 단계를 수행 합니다.
5. az vm repair restore 실행

추가 설명서 및 지침은 [az vm repair](/cli/azure/ext/vm-repair/vm/repair)를 참조하세요.

## <a name="repair-process-example"></a>복구 프로세스 예

1. Azure Cloud Shell 시작

   Azure Cloud Shell은 이 항목의 단계를 실행하는 데 무료로 사용할 수 있는 대화형 셸입니다. 사전 설치되어 계정에서 사용하도록 구성된 공용 Azure 도구가 포함됩니다.

   Cloud Shell을 열려면 코드 블록의 오른쪽 위 모서리에 있는 **사용해 보세요**를 선택합니다. 또한 [https://shell.azure.com](https://shell.azure.com)로 이동하여 별도의 브라우저 탭에서 Cloud Shell을 열 수도 있습니다.

   **복사**를 선택하여 코드 블록을 복사한 다음 Cloud Shell에 해당 코드를 붙여넣고, **Enter**를 선택하여 실행합니다.

   CLI를 로컬에서 설치하여 사용하려면, 빠른 시작에 Azure CLI 버전 2.0.30 이상이 필요합니다. ``az --version``을 실행하여 버전을 찾습니다. Azure CLI를 설치하거나 업그레이드해야 하는 경우 [Azure CLI 설치](/cli/azure/install-azure-cli)를 참조하세요.
   
   현재 Azure Portal에 로그인 하는 것과 다른 계정을 사용 하 여 Cloud Shell에 로그인 해야 하는 경우 ``az login`` [az login reference](/cli/azure/reference-index?view=azure-cli-latest#az-login&preserve-view=true)를 사용할 수 있습니다.  계정과 연결 된 구독 간에 전환 하려면 ``az account set --subscription`` [az account set reference](/cli/azure/account?view=azure-cli-latest#az-account-set&preserve-view=true)를 사용할 수 있습니다.

2. `az vm repair` 명령을 처음 사용하는 경우 vm-repair CLI 확장을 추가합니다.

   ```azurecli-interactive
   az extension add -n vm-repair
   ```

   이전에 `az vm repair` 명령을 사용한 경우 모든 업데이트를 vm-repair 확장에 적용합니다.

   ```azurecli-interactive
   az extension update -n vm-repair
   ```

3. `az vm repair create`을 실행합니다. 이 명령은 비기능적 VM에 대한 OS 디스크의 복사본을 만들고, 새 리소스 그룹에 복구 VM을 만들고, OS 디스크 복사본을 연결합니다.  복구 VM은 지정된 비기능적 VM과 동일한 크기와 지역을 갖습니다. 모든 단계에서 사용 되는 리소스 그룹 및 VM 이름은 작동 하지 않는 VM에 대 한 것입니다. VM이 Azure Disk Encryption 사용 하는 경우 복구 VM에 연결 될 때 액세스할 수 있도록 명령이 암호화 된 디스크의 잠금을 해제 하려고 시도 합니다.

   ```azurecli-interactive
   az vm repair create -g MyResourceGroup -n myVM --repair-username username --repair-password password!234 --verbose
   ```

4. `az vm repair run`을 실행합니다. 이 명령은 복구 VM을 통해 연결된 디스크에서 지정된 복구 스크립트를 실행합니다. 사용 중인 문제 해결 가이드에서 실행 id를 지정 하는 경우 여기에서 사용 하세요. 그렇지 않으면 az vm repair list-scripts를 사용 하 여 사용 가능한 복구 스크립트를 볼 수 있습니다. 여기에 사용 된 리소스 그룹 및 VM 이름은 3 단계에서 사용 되는 작동 하지 않는 VM에 대 한 것입니다. 복구 스크립트에 대 한 추가 정보는 [복구 스크립트 라이브러리](https://github.com/Azure/repair-script-library)에서 찾을 수 있습니다.

   ```azurecli-interactive
   az vm repair run -g MyResourceGroup -n MyVM --run-on-repair --run-id lin-hello-world --verbose
   ```

   필요에 따라 VM 복구를 사용 하 여 필요한 수동 완화 단계를 수행 하 고 5 단계를 진행할 수 있습니다.

5. `az vm repair restore`을 실행합니다. 이 명령은 복구된 OS 디스크를 VM의 원래 OS 디스크와 교환합니다. 여기에 사용 된 리소스 그룹 및 VM 이름은 3 단계에서 사용 되는 작동 하지 않는 VM에 대 한 것입니다.

   ```azurecli-interactive
   az vm repair restore -g MyResourceGroup -n MyVM --verbose
   ```

## <a name="verify-and-enable-boot-diagnostics"></a>부팅 진단 확인 및 사용하도록 설정

다음 예제에서는 리소스 그룹 ``myResourceGroup``의 VM ``myVMDeployed``에서 진단 확장을 사용할 수 있습니다.

Azure CLI

```azurecli-interactive
az vm boot-diagnostics enable --name myVMDeployed --resource-group myResourceGroup --storage https://mystor.blob.core.windows.net/
```

## <a name="next-steps"></a>다음 단계

* VM에 연결하는 데 문제가 있는 경우 [Azure 가상 머신에 RDP 연결 문제 해결](./troubleshoot-rdp-connection.md)을 참조하세요.
* VM에서 실행 중인 애플리케이션에 액세스하는 데 문제가 있는 경우 [Azure의 가상 머신에서 애플리케이션 연결 문제 해결](./troubleshoot-app-connection.md)을 참조하세요.
* Resource Manager를 사용하는 방법에 대한 자세한 내용은 [Azure Resource Manager 개요](../../azure-resource-manager/management/overview.md)를 참조하세요.
