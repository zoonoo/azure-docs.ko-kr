---
title: 포함 파일
description: 포함 파일
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 09/24/2018
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: b28830ac2634ad2238d834238de83c9184bcd6f0
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2018
ms.locfileid: "47010394"
---
# <a name="enabling-azure-ultra-ssds"></a>Azure Ultra SSD를 사용하도록 설정

Azure Ultra SSD는Azure IaaS VM에 대한 높은 처리량, 높은 IOP 및 일관된 짧은 대기 시간 디스크 저장소를 제공합니다. 이 새 제품은 Microsoft의 기존 디스크 제품과 동일한 가용성 수준에서 최상의 성능을 제공합니다. Ultra SSD의 추가 혜택에는 가상 머신을 다시 시작할 필요없이 워크로드와 함께 디스크의 성능을 동적으로 변경하는 기능이 포함됩니다. Ultra SSD는 SAP HANA, 최상위 계층 데이터베이스 및 트랜잭션 작업이 많은 워크로드와 같은 데이터 집약적인 워크로드에 적합합니다.

현재 Ultra SSD는 미리 보기 상태에 있으며 미리 보기에 [등록](https://aka.ms/UltraSSDPreviewSignUp)해야 Ultra SSD에 액세스할 수 있습니다.

승인되면 다음 명령 중 하나를 실행하여 울트라 SSD를 배포할 미국 동부 2의 지역을 결정합니다.

PowerShell: `Get-AzureRmComputeResourceSku | where {$_.ResourceType -eq "disks" -and $_.Name -eq "UltraSSD_LRS" }`

CLI: `az vm list-skus --resource-type disks --query “[?name==’UltraSSD_LRS’]”`

응답은 아래 형식과 유사합니다. 여기서 X는 미국 동부 2에서 배포하기 위해 사용하는 지역입니다. X는 1, 2 또는 3이 될 수 있습니다.

|ResourceType  |이름  |위치  |영역  |제한  |기능  |값  |
|---------|---------|---------|---------|---------|---------|---------|
|디스크     |UltraSSD_LRS         |eastus2         |X         |         |         |         |

명령에서 응답이 없으면 기능에 등록이 계속 보류 중이거나 아직 승인되지 않았다는 의미입니다.

배포할 지역을 파악했으므로 이 문서의 배포 단계를 따라 Ultra SSD 디스크를 통해 첫 번째 VM이 배포되게 합니다.

## <a name="deploying-an-ultra-ssd"></a>Ultra SSD 배포하기

먼저 배포할 VM 크기를 결정합니다. 이 미리 보기의 일부인 DsV3 및 EsV3 VM 제품군만 지원됩니다. 이러한 VM 크기에 대 한 자세한 내용은 이 [블로그](https://azure.microsoft.com/blog/introducing-the-new-dv3-and-ev3-vm-sizes/)의 두 번째 표를 참조하세요.
또한 여러 Ultra SSD 디스크를 사용하여 VM을 만드는 방법을 보여주는 [여러 Ultra SSD 디스크를 사용한 VM 만들기](https://aka.ms/UltraSSDTemplate) 샘플을 참조하세요.

다음은 새로운/수정된 Resource Manager 템플릿 변경 내용을 설명합니다. 즉, `Microsoft.Compute/virtualMachines` 및 `Microsoft.Compute/Disks`에 대한 **apiVersion**은 `2018-06-01` 이후로 설정되어야 합니다.

Disk Sku UltraSSD_LRS, 디스크 용량, IOPS 및 처리량(MBps)을 지정하여 Ultra SSD 디스크를 만듭니다. 다음은 1,024GiB(GiB = 2^30바이트), 80,000 IOPS 및 1,200MBps (MBps = 초당 10^6바이트)의 디스크를 만드는 예제입니다.

```json
"properties": {  
    "creationData": {  
    "createOption": "Empty"  
},  
"diskSizeGB": 1024,  
"diskIOPSReadWrite": 80000,  
"diskMBpsReadWrite": 1200,  
}
```

해당 Ultra SSD 사용을 나타내려면 VM의 속성에 추가 기능을 추가합니다(전체 Resource Manager 템플릿은 [샘플](https://aka.ms/UltraSSDTemplate)을 참조하세요).

```json
{
    "apiVersion": "2018-06-01",
    "type": "Microsoft.Compute/virtualMachines",
    "properties": {
                    "hardwareProfile": {},
                    "additionalCapabilities" : {
                                    "ultraSSDEnabled" : "true"
                    },
                    "osProfile": {},
                    "storageProfile": {},
                    "networkProfile": {}
    }
}
```

VM을 프로비전한 후 데이터 디스크를 분할 및 포맷하고 워크로드용 데이터 디스크를 구성할 수 있습니다.

## <a name="additional-ultra-ssd-scenarios"></a>추가 Ultra SSD 시나리오

- VM을 만드는 동안 Ultra SSD를 암시적으로 만들 수도 있습니다. 그러나 이러한 디스크는 IOPS(500) 및 처리량(8MiB/s)에 대한 기본값을 받게 됩니다.
- 추가 Ultra SSD는 Ultra SSD와 호환되는 VM에 연결될 수 있습니다.
- Ultra SSD는 가상 머신에서 디스크를 분리하지 않고 런타임 시 디스크 성능 특성(IOPS 및 처리량) 조정을 지원합니다. 디스크 성능 크기 조정 작업이 디스크에서 실행된 후 변경 내용이 실제로 적용되려면 최대 한 시간이 걸릴 수 있습니다.
- 디스크 용량을 증가하려면 가상 머신이 할당 취소되어야 합니다.

# <a name="next-steps"></a>다음 단계

새 디스크 형식을 시도하려는데 아직 미리 보기에 등록하지 않은 경우 [이 설문 조사를 통해 액세스를 요청](https://aka.ms/UltraSSDPreviewSignUp)합니다.