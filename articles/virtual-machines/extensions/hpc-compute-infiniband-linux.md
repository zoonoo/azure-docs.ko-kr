---
title: InfiniBand 드라이버 확장-Azure Linux Vm
description: Linux를 실행 하는 H-및 N 시리즈 계산 Vm에 InfiniBand 드라이버를 설치 하기 위한 확장을 Microsoft Azure 합니다.
services: virtual-machines-linux
documentationcenter: ''
author: vermagit
editor: ''
ms.assetid: ''
ms.service: virtual-machines-linux
ms.subservice: extensions
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 07/20/2020
ms.author: amverma
ms.openlocfilehash: 2a5aa8983e6cbb0745e05ce275edeadeccb60736
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/20/2020
ms.locfileid: "94966040"
---
# <a name="infiniband-driver-extension-for-linux"></a>Linux 용 InfiniBand 드라이버 확장

이 확장은 Linux를 실행 하는 InfiniBand 및 SR-IOV 사용 (' r ' 크기) [H 시리즈](../sizes-hpc.md) 및 [N 시리즈](../sizes-gpu.md) vm에 InfiniBand OFED 드라이버를 설치 합니다. VM 제품군에 따라 확장은 연결-X NIC에 적절 한 드라이버를 설치 합니다.

OFED 드라이버의 수동 설치에 대 한 지침은 [여기](../workloads/hpc/enable-infiniband.md#manual-installation)에서 제공 됩니다.

확장은 [Windows vm](hpc-compute-infiniband-windows.md)용 InfiniBand 드라이버를 설치 하는 데도 사용할 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

### <a name="operating-system"></a>운영 체제

이 확장은 특정 OS 버전의 드라이버 지원에 따라 다음 OS 배포판을 지원합니다.

| 배포 | 버전 |
|---|---|
| Linux: Ubuntu | 16.04 LTS, 18.04 LTS, 20.04 LTS |
| Linux: CentOS | 7.4, 7.5, 7.6, 7.7, 8.1, 8, 2 |
| Linux: Red Hat Enterprise Linux | 7.4, 7.5, 7.6, 7.7, 8.1, 8, 2 |

### <a name="internet-connectivity"></a>인터넷 연결

InfiniBand 드라이버를 Microsoft Azure 확장 하려면 대상 VM이에 연결 되어 있고 인터넷에 액세스할 수 있어야 합니다.

## <a name="extension-schema"></a>확장 스키마

확장에 대한 스키마를 보여 주는 JSON은 다음과 같습니다.

```json
{
  "name": "<myExtensionName>",
  "type": "extensions",
  "apiVersion": "2015-06-15",
  "location": "<location>",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', <myVM>)]"
  ],
  "properties": {
    "publisher": "Microsoft.HpcCompute",
    "type": "InfiniBandDriverLinux",
    "typeHandlerVersion": "1.1",
    "autoUpgradeMinorVersion": true,
    "settings": {
    }
  }
}
```

### <a name="properties"></a>속성

| 속성 | 값/예제 | 데이터 형식 |
| ---- | ---- | ---- |
| apiVersion | 2015-06-15 | date |
| publisher | Microsoft.HpcCompute | 문자열 |
| type | InfiniBandDriverLinux | 문자열 |
| typeHandlerVersion | 1.1 | int |



## <a name="deployment"></a>배포


### <a name="azure-resource-manager-template"></a>Azure Resource Manager 템플릿 

Azure Resource Manager 템플릿을 사용하여 Azure VM 확장을 배포할 수 있습니다. 배포 후 구성이 필요한 하나 이상의 가상 머신을 배포하는 경우 템플릿을 사용하는 것이 좋습니다.

가상 머신 확장에 대한 JSON 구성은 가상 머신 리소스 내에 중첩되거나 루트 또는 최상위 수준의 Resource Manager JSON 템플릿에 배치될 수 있습니다. JSON 구성의 배치는 리소스 이름 및 형식 값에 영향을 줍니다. 자세한 내용은 [자식 리소스의 이름 및 형식 설정](../../azure-resource-manager/templates/child-resource-name-type.md)을 참조하세요. 

다음 예제에서는 확장이 가상 머신 리소스 내에 중첩되어 있다고 가정합니다. 확장 리소스를 중첩하는 경우 JSON은 가상 머신의 `"resources": []` 개체에 배치됩니다.

```json
{
  "name": "myExtensionName",
  "type": "extensions",
  "location": "[resourceGroup().location]",
  "apiVersion": "2015-06-15",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', myVM)]"
  ],
  "properties": {
    "publisher": "Microsoft.HpcCompute",
    "type": "InfiniBandDriverLinux",
    "typeHandlerVersion": "1.1",
    "autoUpgradeMinorVersion": true,
    "settings": {
    }
  }
}
```

### <a name="powershell"></a>PowerShell

```powershell
Set-AzVMExtension
    -ResourceGroupName "myResourceGroup" `
    -VMName "myVM" `
    -Location "southcentralus" `
    -Publisher "Microsoft.HpcCompute" `
    -ExtensionName "InfiniBandDriverLinux" `
    -ExtensionType "InfiniBandDriverLinux" `
    -TypeHandlerVersion 1.1 `
    -SettingString '{ `
    }'
```

### <a name="azure-cli"></a>Azure CLI

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name InfiniBandDriverLinux \
  --publisher Microsoft.HpcCompute \
  --version 1.1 
```

### <a name="add-extension-to-a-virtual-machine-scale-set"></a>가상 머신 확장 집합에 확장 추가

다음 예제에서는 *Myresourcegroup* 이라는 리소스 그룹에 배포 된 *myvmss* 라는 기존 가상 머신 확장 집합의 모든 RDMA 지원 vm에 최신 버전 1.1 InfiniBandDriverLinux 확장을 설치 합니다.

  ```powershell
  $VMSS = Get-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myVMSS"
  Add-AzVmssExtension -VirtualMachineScaleSet $VMSS -Name "InfiniBandDriverLinux" -Publisher "Microsoft.HpcCompute" -Type "InfiniBandDriverLinux" -TypeHandlerVersion "1.1"
  Update-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "MyVMSS" -VirtualMachineScaleSet $VMSS
  Update-AzVmssInstance -ResourceGroupName "myResourceGroup" -VMScaleSetName "myVMSS" -InstanceId "*"
```


## <a name="troubleshoot-and-support"></a>문제 해결 및 지원

### <a name="troubleshoot"></a>문제 해결

확장 배포 상태에 대한 데이터는 Azure Portal에서 Azure PowerShell 및 Azure CLI를 사용하여 검색할 수 있습니다. 지정된 VM에 대한 확장의 배포 상태를 보려면 다음 명령을 실행합니다.

```powershell
Get-AzVMExtension -ResourceGroupName myResourceGroup -VMName myVM -Name myExtensionName
```

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

확장 실행 출력은 다음 파일에 기록 됩니다. 설치 상태를 추적 하 고 오류를 해결 하려면이 파일을 참조 하세요.

```bash
/var/log/azure/ib-vmext-status
```

### <a name="exit-codes"></a>종료 코드

다음 표에서는 확장 설치 프로세스의 종료 코드를 기반으로 하는 의미와 권장 작업에 대해 설명 합니다.

| 종료 코드 | 의미 | 가능한 작업 |
| :---: | --- | --- |
| 0 | 작업이 성공했습니다. |
| 1 | 확장의 사용이 잘못되었습니다. | 실행 출력 로그를 확인합니다. |
| 10 | Hyper-V 및 Azure에 대한 Linux Integration Services를 사용하거나 설치할 수 없습니다. | lspci의 출력을 확인합니다. |
| 11 | 이 VM 크기에서 Mellanox InfiniBand을 찾을 수 없습니다. | [지원되는 VM 크기 및 OS](../sizes-hpc.md)를 사용합니다. |
| 12 | 지원되지 않는 이미지 제품 |
| 13 | 지원되지 않는 VM 크기 | InfiniBand 사용 (' r ' 크기) [H 시리즈](../sizes-hpc.md) 및 [n](../sizes-gpu.md)시리즈 n 시리즈 VM을 사용 하 여 배포 |
| 14 | 작업 실패 | 실행 출력 로그를 확인합니다. |


### <a name="support"></a>지원

이 문서의 어디에서든 도움이 필요한 경우 [MSDN Azure 및 Stack Overflow 포럼](https://azure.microsoft.com/support/community/)에서 Azure 전문가에게 문의할 수 있습니다. 또는 [Azure 지원 사이트](https://azure.microsoft.com/support/options/)를 통해 지원 인시던트를 파일에 추가할 수 있습니다. Azure 지원을 사용하는 방법에 대한 자세한 내용은 [Microsoft Azure 지원 FAQ](https://azure.microsoft.com/support/faq/)를 참조하세요.

## <a name="next-steps"></a>다음 단계
InfiniBand 사용 (' r ' 크기)에 대 한 자세한 내용은 [H 시리즈](../sizes-hpc.md) 및 [N 시리즈](../sizes-gpu.md) vm을 참조 하세요.

> [!div class="nextstepaction"]
> [Linux Vm 확장 및 기능에 대 한 자세한 정보](features-linux.md)