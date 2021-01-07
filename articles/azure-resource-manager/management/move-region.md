---
title: 다른 지역으로 Azure 리소스 이동
description: Azure 지역에서 Azure 리소스를 이동 하는 방법에 대 한 개요를 제공 합니다.
author: rayne-wiselman
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 09/10/2020
ms.author: raynew
ms.openlocfilehash: 7a71502ec361004079e0962d8bc6433316a4ba81
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "90007641"
---
# <a name="moving-azure-resources-across-regions"></a>지역 간에 Azure 리소스 이동

이 문서에서는 azure 지역에서 Azure 리소스를 이동 하는 방법에 대 한 정보를 제공 합니다.

Azure 지역, 지역 및 가용성 영역은 Azure 글로벌 인프라의 토대를 형성 합니다. Azure [지역에는 일반적으로](https://azure.microsoft.com/global-infrastructure/geographies/) 둘 이상의 [azure 지역이](https://azure.microsoft.com/global-infrastructure/regions/)포함 됩니다. 지역은 가용성 영역 및 여러 데이터 센터를 포함 하는 지리 내의 영역입니다. 

특정 Azure 지역에 리소스를 배포한 후에는 리소스를 다른 지역으로 이동 해야 하는 여러 가지 이유가 있습니다.

- **영역 시작에 맞추기**: 이전에는 사용할 수 없었던 새로 도입 된 Azure 지역으로 리소스를 이동 합니다.
- **서비스/기능에 맞추기**: 리소스를 이동하여 특정 지역에서 사용할 수 있는 서비스 또는 기능을 활용합니다.
- **비즈니스 개발에 대응**: 합병 또는 인수 등의 비즈니스 변화에 대응하여 리소스를 지역으로 이동합니다.
- **근접성에 맞추기**: 리소스를 비즈니스 지역으로 이동합니다.
- **데이터 요구 사항 충족**: 데이터 상주 요구 사항 또는 데이터 분류 요구 사항에 맞게 리소스를 이동 합니다. [자세히 알아봅니다](https://azure.microsoft.com/mediahandler/files/resourcefiles/achieving-compliant-data-residency-and-security-with-azure/Achieving_Compliant_Data_Residency_and_Security_with_Azure.pdf).
- **배포 요구 사항에 대응**: 오류로 배포된 리소스를 이동하거나 용량 요구 사항에 따라 이동합니다. 
- **서비스 해제에 응답**: 영역 서비스 해제로 인해 리소스를 이동 합니다.

## <a name="move-resources-with-resource-mover"></a>리소스 이동 기를 사용 하 여 리소스 이동

[Azure 리소스](../../resource-mover/overview.md)이동 기를 사용 하 여 리소스를 다른 지역으로 이동할 수 있습니다. Resource Mover는 다음을 제공합니다.

- 지역 간에 리소스를 이동하기 위한 단일 허브입니다.
- 이동 시간과 복잡성이 줄어듭니다. 필요한 모든 항목은 단일 위치에 있습니다.
- 다양한 유형의 Azure 리소스를 이동할 수 있는 간단하고 일관된 환경을 제공합니다.
- 이동하려는 리소스 간의 종속성을 쉽게 식별할 수 있는 방법입니다. 이를 통해 관련 리소스를 함께 이동함으로써 이동 후 대상 지역에서 모든 것이 예상대로 작동하도록 할 수 있습니다.
- 이동 후 리소스를 삭제하려는 경우 원본 지역의 리소스를 자동으로 정리합니다.
- 테스트 이동을 시도한 다음, 전체 이동을 수행하지 않으려면 취소할 수 있습니다.

여러 가지 방법을 사용 하 여 리소스를 다른 지역으로 이동할 수 있습니다.

- 리소스 **그룹에서 리소스 이동 시작**:이 방법을 사용 하 여 리소스 그룹 내에서 영역 이동을 시작 합니다. 이동 하려는 리소스를 선택한 후에는 리소스 이동 기 허브에서 프로세스가 계속 진행 되 고, 리소스 종속성을 확인 하 고, 이동 프로세스를 오케스트레이션 합니다. [자세히 알아봅니다](../../resource-mover/move-region-within-resource-group.md).
- **리소스 이동 기 허브에서 직접 리소스 이동 시작**:이 방법을 사용 하면 허브에서 직접 지역 이동 프로세스를 시작 합니다. [자세히 알아봅니다](../../resource-mover/tutorial-move-region-virtual-machines.md).


## <a name="support-for-region-move"></a>영역 이동 지원

현재 리소스 이동 기를 사용 하 여 이러한 리소스를 다른 지역으로 이동할 수 있습니다.

- Azure VM 및 연결된 디스크
- NIC
- 가용성 집합
- Azure 가상 네트워크
- 공용 IP 주소
- NSG(네트워크 보안 그룹)::
- 내부 및 공용 부하 분산 장치
- Azure SQL 데이터베이스 및 탄력적 풀

## <a name="region-move-process"></a>영역 이동 프로세스

지역 간에 리소스를 이동 하는 실제 프로세스는 이동 하는 리소스에 따라 달라 집니다. 그러나 다음과 같은 몇 가지 일반적인 주요 단계가 있습니다.

1. **필수 조건 확인**: 필요한 리소스를 대상 지역에서 사용할 수 있는지 확인 하 고, 할당량이 충분 한지 확인 하 고, 구독에서 대상 지역에 액세스할 수 있는지 확인 하는 필수 구성 요소를 포함 합니다.
2. **종속성 분석**: 리소스가 다른 리소스에 종속 될 수 있습니다. 이동 하기 전에 이동 된 리소스가 이동 후 계속 예상 대로 작동 하도록 종속성을 파악 합니다.
3. **이동 준비**: 이동 하기 전에 주 지역에서 수행 하는 단계입니다. 예를 들어 Azure Resource Manager 템플릿을 내보내거나 원본에서 대상으로 리소스 복제를 시작 해야 할 수 있습니다.
4. **리소스 이동**: 리소스를 이동 하는 방법은 해당 리소스를 이동 하는 방법에 따라 달라 집니다. 대상 지역에 템플릿을 배포 하거나 대상에 대 한 리소스를 장애 조치 (failover) 해야 할 수도 있습니다.
5. **대상 리소스 삭제**: 리소스를 이동한 후에는 이제 대상 지역에서 리소스를 확인 하 고 필요 하지 않은 항목이 있는지 여부를 결정할 수 있습니다.
6. **이동 커밋**: 대상 지역에서 리소스를 확인 한 후에는 일부 리소스에 최종 커밋 작업이 필요할 수 있습니다. 예를 들어 이제 주 지역에 있는 대상 지역에서 새 보조 지역에 대 한 재해 복구를 설정 해야 할 수 있습니다. 
7. **원본 정리**: 마지막으로 새 지역에서 모든 항목을 실행 한 후에는 이동 및 주 지역에서 리소스를 정리 하 고 리소스를 해제할 수 있습니다.



## <a name="next-steps"></a>다음 단계

리소스 이동 기의 이동 프로세스에 [대해 자세히 알아보세요](../../resource-mover/about-move-process.md) .
