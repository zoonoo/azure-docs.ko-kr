---
title: Azure 가상 컴퓨터 복구 명령을 사용 하 여 Linux VM 복구 | Microsoft Docs
description: 이 문서에서는 Azure 가상 컴퓨터 복구 명령을 사용 하 여 다른 Linux VM에 디스크를 연결 하 여 오류를 수정한 다음 원래 VM을 다시 빌드하는 방법을 자세히 설명 합니다.
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
ms.openlocfilehash: 49fdfde402938ce8d0ee1b141a47e68c99c502e7
ms.sourcegitcommit: 018e3b40e212915ed7a77258ac2a8e3a660aaef8
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/07/2019
ms.locfileid: "73796210"
---
# <a name="repair-a-linux-vm-by-using-the-azure-virtual-machine-repair-commands"></a>Azure Virtual Machine 복구 명령을 사용하여 Linux VM 복구

Azure의 Linux VM (가상 머신)에 부팅 또는 디스크 오류가 발생 하는 경우 디스크 자체에서 완화를 수행 해야 할 수 있습니다. 일반적인 예로는 애플리케이션 업데이트가 실패하여 VM이 성공적으로 부팅되지 않는 경우입니다. 이 문서에서는 Azure 가상 컴퓨터 복구 명령을 사용 하 여 다른 Linux VM에 디스크를 연결 하 여 오류를 수정한 다음 원래 VM을 다시 빌드하는 방법을 자세히 설명 합니다.

> [!IMPORTANT]
> 이 문서의 스크립트는 [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)를 사용 하는 vm에만 적용 됩니다.

## <a name="repair-process-overview"></a>프로세스 복구 개요

이제 Azure 가상 컴퓨터 복구 명령을 사용 하 여 VM에 대 한 OS 디스크를 변경할 수 있으며 더 이상 VM을 삭제 하 고 다시 만들 필요가 없습니다.

VM 문제를 해결 하려면 다음 단계를 수행 합니다.

1. Azure Cloud Shell 시작
2. 실행 az extension add/update
3. 실행 az vm repair create
4. 완화 단계 수행
5. 실행 az vm repair restore

추가 설명서 및 지침은 [az vm repair](https://docs.microsoft.com/cli/azure/ext/vm-repair/vm/repair)를 참조 하세요.

## <a name="repair-process-example"></a>복구 프로세스 예제

1. Azure Cloud Shell 시작

   Azure Cloud Shell은 이 항목의 단계를 실행하는 데 무료로 사용할 수 있는 대화형 셸입니다. 여기에는 계정에 사용 하도록 미리 설치 되 고 구성 된 일반적인 Azure 도구가 포함 됩니다.

   Cloud Shell 열려면 코드 블록의 오른쪽 위 모퉁이에서 **시도** 를 선택 합니다. 또한 [https://shell.azure.com](https://shell.azure.com)로 이동하여 별도의 브라우저 탭에서 Cloud Shell을 열 수도 있습니다.

   **복사** 를 선택 하 여 코드 블록을 복사한 다음 Cloud Shell에 코드를 붙여넣고 **Enter 키** 를 선택 하 여 실행 합니다.

   CLI를 로컬에서 설치하여 사용하려면, 빠른 시작에 Azure CLI 버전 2.0.30 이상이 필요합니다. ``az --version``을 실행하여 버전을 찾습니다. Azure CLI를 설치 하거나 업그레이드 해야 하는 경우 [Azure CLI 설치](https://docs.microsoft.com/cli/azure/install-azure-cli)를 참조 하세요.

2. `az vm repair` 명령을 처음 사용 하는 경우 vm 복구 CLI 확장을 추가 합니다.

   ```azurecli-interactive
   az extension add -n vm-repair
   ```

   이전에 `az vm repair` 명령을 사용한 경우 vm 복구 확장에 업데이트를 적용 합니다.

   ```azurecli-interactive
   az extension update -n vm-repair
   ```

3. `az vm repair create`을 실행합니다. 이 명령은 작동 하지 않는 VM에 대 한 OS 디스크의 복사본을 만들고, 복구 VM을 만들고, 디스크를 연결 합니다.

   ```azurecli-interactive
   az vm repair create -g MyResourceGroup -n myVM --repair-username username --repair-password password!234 --verbose
   ```

4. 만든 복구 VM에서 필요한 모든 완화 단계를 수행한 후 5 단계를 진행 합니다.

5. `az vm repair restore`을 실행합니다. 이 명령은 복구 된 OS 디스크를 VM의 원래 OS 디스크와 교환 합니다.

   ```azurecli-interactive
   az vm repair restore -g MyResourceGroup -n MyVM --verbose
   ```

## <a name="verify-and-enable-boot-diagnostics"></a>부팅 진단 확인 및 사용하도록 설정

다음 예제에서는 리소스 그룹 ``myVMDeployed``의 VM ``myResourceGroup``에서 진단 확장을 사용할 수 있습니다.

Azure CLI

```azurecli-interactive
az vm boot-diagnostics enable --name myVMDeployed --resource-group myResourceGroup --storage https://mystor.blob.core.windows.net/
```

## <a name="next-steps"></a>다음 단계

* VM에 연결 하는 데 문제가 있는 경우 [Azure 가상 머신에 대 한 RDP 연결 문제 해결](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/troubleshoot-rdp-connection)을 참조 하세요.
* VM에서 실행 중인 응용 프로그램에 액세스 하는 데 문제가 있는 경우 [Azure의 가상 머신에서 응용 프로그램 연결 문제 해결](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/troubleshoot-app-connection)을 참조 하세요.
* Resource Manager를 사용하는 방법에 대한 자세한 내용은 [Azure Resource Manager 개요](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)를 참조하세요.
