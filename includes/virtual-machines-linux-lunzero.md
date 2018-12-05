---
author: cynthn
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 87dd3680aae3e87f78ab2dbe70c44b2008706747
ms.sourcegitcommit: 8d88a025090e5087b9d0ab390b1207977ef4ff7c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/21/2018
ms.locfileid: "52279782"
---
Linux VM에 데이터 디스크를 추가할 때 디스크가 LUN 0에 존재하지 않으면 오류가 발생할 수 있습니다. Azure 플랫폼에서 적절한 LUN을 확인하도록 하지 않고 `azure vm disk attach-new` 명령을 사용하여 디스크를 수동으로 추가한 후 LUN을 지정하는 경우(`--lun`) 디스크가 LUN 0에 이미 존재하거나 이 위치에 상주하게 될지 잘 확인해야 합니다. 

`lsscsi`출력의 코드 조각을 보여 주는 다음 예제를 참조하세요.

```bash
[5:0:0:0]    disk    Msft     Virtual Disk     1.0   /dev/sdc 
[5:0:0:1]    disk    Msft     Virtual Disk     1.0   /dev/sdd 
```

두 개의 데이터 디스크가 LUN 0 및 LUN 1에 존재합니다(`lsscsi` 출력의 첫 번째 열에 `[host:channel:target:lun]`이 자세히 나와 있음). 두 디스크는 VM 내에서 액세스할 수 있어야 합니다. 첫 번째 디스크는 LUN 1에 추가되고, 두 번째 디스크는 LUN 2에 추가되도록 수동으로 지정한 경우 VM 내에서 디스크가 제대로 확인되지 않을 수 있습니다.

> [!NOTE]
> Azure `host` 값은 이 예제에서 5이지만, 이 값은 선택한 저장소 형식 따라 다를 수 있습니다.
> 
> 

이러한 디스크 동작은 Azure 문제가 아니라 Linux 커널이 SCSI 사양을 따르는 방식입니다. Linux 커널이 SCSI 버스에서 연결된 장치를 검색할 때 시스템이 추가 장치를 계속 검색하기 위해서는 장치가 LUN 0에 있어야 합니다. 따라서 다음을 수행해야 합니다.

* 데이터 디스크를 추가한 후에 `lsscsi` 의 출력을 검토하여 디스크가 LUN 0에 있는지 확인합니다.
* 디스크가 VM 내에서 올바르게 표시되지 않으면 디스크가 LUN 0에 있는지 확인합니다.

