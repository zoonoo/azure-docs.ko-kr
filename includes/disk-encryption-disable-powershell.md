---
title: 포함 파일
description: 포함 파일
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 06/19/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 517bde8d365793906c24d0f242d4adc1b6241e7c
ms.sourcegitcommit: 8651d19fca8c5f709cbb22bfcbe2fd4a1c8e429f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/14/2021
ms.locfileid: "112084005"
---
Azure PowerShell, Azure CLI 또는 Resource Manager 템플릿을 사용하여 암호화를 사용하지 않도록 설정할 수 있습니다. OS와 데이터 디스크가 모두 암호화되었을 때 Windows VM에서 데이터 디스크 암호화를 비활성화하면 예상대로 작동하지 않습니다. 대신 모든 디스크에 암호화를 사용하지 않도록 설정하십시오.

- **Azure PowerShell을 사용하여 디스크 암호화 사용 안 함:** 암호화를 사용하지 않도록 설정하려면 [Disable-AzVMDiskEncryption](/powershell/module/az.compute/disable-azvmdiskencryption) cmdlet을 사용합니다. 
     ```azurepowershell-interactive
     Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM' -VolumeType "all"
     ```

- **Azure CLI를 사용하여 암호화 사용 안 함:** 암호화를 사용하지 않도록 설정하려면 [az vm encryption disable](/cli/azure/vm/encryption#az_vm_encryption_disable) 명령을 사용합니다. 
     ```azurecli-interactive
     az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type "all"
     ```
- **Resource Manager 템플릿으로 암호화를 사용하지 않도록 설정:** 

    1. [실행중인 Windows VM에서 디스크 암호화 사용 안 함](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.compute/decrypt-running-windows-vm-without-aad) 템플릿에서 **Azure에 배포** 를 클릭합니다.
    2. 구독, 리소스 그룹, 위치, VM, 볼륨 유형, 약관 및 규약을 선택합니다.
    3.  **구매** 를 클릭하여 실행 중인 Windows VM에서 디스크 암호화를 사용하지 않도록 설정합니다. 
