---
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 03/18/2019
ms.author: rogarana
ms.openlocfilehash: 10599b47d55a0b4ea1eb79cda3c31a2479efb7c0
ms.sourcegitcommit: 4cb89d880be26a2a4531fedcc59317471fe729cd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/27/2020
ms.locfileid: "92755615"
---
Azure 가상 머신에 여러 데이터 디스크를 연결할 수 있습니다. VM의 데이터 디스크 확장성 및 성능 목표에 따라 성능 및 용량 요구 사항을 충족하는 데 필요한 디스크의 수와 유형을 결정할 수 있습니다.

> [!IMPORTANT]
> 최적의 성능을 얻기 위해 가상 머신에 연결되어 자주 활용되는 디스크의 수를 제한하여 가능한 제한을 방지합니다. 연결된 모든 디스크의 동시 사용률이 높지 않으면 가상 머신이 더 많은 수의 디스크를 지원할 수 있습니다.

**Azure 관리 디스크:**

다음 표에서는 구독당 지역별 리소스 수의 기본 및 최대 제한을 보여 줍니다. 이러한 제한은 플랫폼 관리형 키 또는 고객 관리형 키로 암호화된 디스크에 관계없이 동일하게 유지됩니다. 리소스 그룹당 Managed Disks, 스냅샷 및 이미지 수에는 제한이 없습니다.  

> | 리소스 | 제한 |
> | --- | --- |
> | 표준 관리 디스크 | 50,000 |
> | 표준 SSD 관리 디스크 | 50,000 |
> | 프리미엄 관리 디스크 | 50,000 |
> | 표준_LRS 스냅샷 | 50,000 |
> | 표준_ZRS 스냅샷 | 50,000 |
> | 관리형 이미지 | 50,000 |

**표준 스토리지 계정:** 표준 스토리지 계정의 최대 총 요청 속도는 20,000 IOPS입니다. 표준 스토리지 계정에서 모든 가상 머신 디스크의 총 IOPS는 이 제한을 초과할 수 없습니다.
  
    You can roughly calculate the number of highly utilized disks supported by a single Standard storage account based on the request rate limit. For example, for a Basic tier VM, the maximum number of highly utilized disks is about 66, which is 20,000/300 IOPS per disk. The maximum number of highly utilized disks for a Standard tier VM is about 40, which is 20,000/500 IOPS per disk. 

**프리미엄 스토리지 계정의 경우:** 프리미엄 스토리지 계정의 최대 총 처리량 속도는 50Gbps입니다. 모든 VM 디스크의 총 처리량은 이 제한을 초과할 수 없습니다.

