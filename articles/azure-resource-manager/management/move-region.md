---
title: 다른 지역으로 Azure 리소스 이동
description: Azure 지역에서 Azure 리소스를 이동 하는 방법에 대 한 개요를 제공 합니다.
author: rayne-wiselman
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: raynew
ms.openlocfilehash: 22d8bcee96b4ac52641d4f0841267195f44fe15a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "75485209"
---
# <a name="moving-azure-resources-across-regions"></a>지역 간에 Azure 리소스 이동

이 문서에서는 azure 지역에서 Azure 리소스를 이동 하는 방법에 대 한 정보를 제공 합니다.

Azure 지역, 지역 및 가용성 영역 Azure 글로벌 인프라의 토대를 형성 합니다. Azure [지역에는 일반적으로](https://azure.microsoft.com/global-infrastructure/geographies/) 둘 이상의 [azure 지역이](https://azure.microsoft.com/global-infrastructure/regions/)포함 됩니다. 지역은 가용성 영역 및 여러 데이터 센터를 포함 하는 지리 내의 영역입니다. 

특정 Azure 지역에 리소스를 배포한 후에는 리소스를 다른 지역으로 이동 해야 하는 여러 가지 이유가 있습니다.

- **영역 시작에 맞추기**: 이전에는 사용할 수 없었던 새로 도입 된 Azure 지역으로 리소스를 이동 합니다.
- **서비스/기능에 맞게 맞춤**: 특정 지역에서 사용할 수 있는 서비스 또는 기능을 활용 하도록 리소스를 이동 합니다.
- **비즈니스 개발에 대응**: 합병 또는 인수 등의 비즈니스 변화에 대 한 응답으로 영역으로 리소스를 이동 합니다.
- **근접에 맞게 맞춤**: 리소스를 비즈니스 지역으로 이동 합니다.
- **데이터 요구 사항 충족**: 데이터 상주 요구 사항 또는 데이터 분류 요구 사항에 맞게 리소스를 이동 합니다. [자세히 알아보기](https://azure.microsoft.com/mediahandler/files/resourcefiles/achieving-compliant-data-residency-and-security-with-azure/Achieving_Compliant_Data_Residency_and_Security_with_Azure.pdf).
- **배포 요구 사항에 대응**: 오류가 발생 하 여 배포 된 리소스를 이동 하거나 용량 요구 사항에 대 한 응답으로 이동 합니다. 
- **서비스 해제에 응답**: 영역 서비스 해제로 인해 리소스를 이동 합니다.

## <a name="move-process"></a>프로세스 이동

실제 이동 프로세스는 이동 하는 리소스에 따라 달라 집니다. 그러나 다음과 같은 몇 가지 일반적인 주요 단계가 있습니다.

- **필수 조건 확인**: 필요한 리소스를 대상 지역에서 사용할 수 있는지 확인 하 고, 할당량이 충분 한지 확인 하 고, 구독에서 대상 지역에 액세스할 수 있는지 확인 하는 필수 구성 요소를 포함 합니다.
- **종속성 분석**: 리소스가 다른 리소스에 종속 될 수 있습니다. 이동 하기 전에 이동 된 리소스가 이동 후 계속 예상 대로 작동 하도록 종속성을 파악 합니다.
- **이동 준비**: 이동 하기 전에 주 지역에서 수행 하는 단계입니다. 예를 들어 Azure Resource Manager 템플릿을 내보내거나 원본에서 대상으로 리소스 복제를 시작 해야 할 수 있습니다.
- **리소스 이동**: 리소스를 이동 하는 방법은 해당 리소스를 이동 하는 방법에 따라 달라 집니다. 대상 지역에 템플릿을 배포 하거나 대상에 대 한 리소스를 장애 조치 (failover) 해야 할 수도 있습니다.
- **대상 리소스 삭제**: 리소스를 이동한 후에는 이제 대상 지역에서 리소스를 확인 하 고 필요 하지 않은 항목이 있는지 여부를 결정할 수 있습니다.
- **이동 커밋**: 대상 지역에서 리소스를 확인 한 후에는 일부 리소스에 최종 커밋 작업이 필요할 수 있습니다. 예를 들어 이제 주 지역에 있는 대상 지역에서 새 보조 지역에 대 한 재해 복구를 설정 해야 할 수 있습니다. 
- **원본 정리**: 마지막으로 새 지역에서 모든 항목을 실행 한 후에는 이동 및 주 지역에서 리소스를 정리 하 고 리소스를 해제할 수 있습니다.



## <a name="next-steps"></a>다음 단계

영역 간 이동을 지 원하는 리소스 목록은 [리소스에 대 한 이동 작업 지원](region-move-support.md)을 참조 하세요.
