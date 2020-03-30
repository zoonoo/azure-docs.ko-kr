---
title: Azure 리소스를 다른 리전으로 이동
description: Azure 리전에서 Azure 리소스이동에 대한 개요를 제공합니다.
author: rayne-wiselman
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: raynew
ms.openlocfilehash: 22d8bcee96b4ac52641d4f0841267195f44fe15a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75485209"
---
# <a name="moving-azure-resources-across-regions"></a>리전 간에 Azure 리소스 이동

이 문서에서는 Azure 리전에서 Azure 리소스이동에 대한 정보를 제공합니다.

Azure 지역, 지역 및 가용성 영역은 Azure 글로벌 인프라의 기초를 형성합니다. Azure [지역에는](https://azure.microsoft.com/global-infrastructure/geographies/) 일반적으로 두 개 이상의 [Azure 영역이](https://azure.microsoft.com/global-infrastructure/regions/)포함됩니다. 지역은 가용성 영역 및 여러 데이터 센터를 포함하는 지역 내의 영역입니다. 

특정 Azure 지역에 리소스를 배포한 후 리소스를 다른 지역으로 이동하려는 데는 여러 가지 이유가 있습니다.

- **지역 시작에 맞추기**: 이전에는 사용할 수 없었던 새로 도입된 Azure 리전으로 리소스를 이동합니다.
- **서비스/기능에 맞게 정렬**: 리소스를 이동하여 특정 지역에서 사용할 수 있는 서비스 또는 기능을 활용합니다.
- **비즈니스 개발에 대응**: 인수 합병 또는 인수와 같은 비즈니스 변화에 대응하여 리전으로 리소스를 이동합니다.
- **근접에 맞게 정렬**: 리소스를 비즈니스에 로컬로 이동합니다.
- **데이터 요구 사항 충족**: 데이터 상주 요구 사항 또는 데이터 분류 요구 사항에 맞게 리소스를 이동합니다. [자세히 알아봅니다](https://azure.microsoft.com/mediahandler/files/resourcefiles/achieving-compliant-data-residency-and-security-with-azure/Achieving_Compliant_Data_Residency_and_Security_with_Azure.pdf).
- **배포 요구 사항에 응답:** 오류로 배포된 리소스를 이동하거나 용량 요구 사항에 맞게 이동합니다. 
- **서비스 해제에 대응**: 리전 해제로 인해 리소스를 이동합니다.

## <a name="move-process"></a>프로세스 이동

실제 이동 프로세스는 이동하는 리소스에 따라 다릅니다. 그러나 몇 가지 일반적인 주요 단계가 있습니다.

- **전제 조건 확인:** 전제 조건은 필요한 리소스를 대상 리전에서 사용할 수 있는지 확인하고, 할당량이 충분한지 확인하고, 구독이 대상 지역에 액세스할 수 있는지 확인하는 것입니다.
- **종속성 분석**: 리소스에 다른 리소스에 종속성이 있을 수 있습니다. 이동하기 전에 이동된 리소스가 이동 후 예상대로 계속 작동하도록 종속성을 파악합니다.
- **이동 준비**: 이동 하기 전에 기본 지역에서 수행 하는 단계입니다. 예를 들어 Azure 리소스 관리자 템플릿을 내보내거나 소스에서 대상으로 리소스 복제를 시작해야 할 수 있습니다.
- **리소스 이동**: 리소스를 이동하는 방법은 리소스가 무엇인지에 따라 다릅니다. 대상 지역에 템플릿을 배포하거나 대상에 리소스를 장애 조치해야 할 수 있습니다.
- **대상 리소스 삭제:** 리소스를 이동한 후 대상 지역에 있는 리소스를 살펴보고 필요하지 않은 리소스가 있는지 결정할 수 있습니다.
- **이동 커밋**: 대상 지역의 리소스를 확인한 후 일부 리소스에 최종 커밋 작업이 필요할 수 있습니다. 예를 들어 이제 기본 지역인 대상 지역에서 새 보조 지역으로 재해 복구를 설정해야 할 수 있습니다. 
- **소스 정리**: 마지막으로 새 리전에서 모든 것이 실행되고 나면 이동을 위해 만든 리소스와 기본 리전의 리소스를 정리하고 해제할 수 있습니다.



## <a name="next-steps"></a>다음 단계

리전 간에 이동하는 데 지원하는 리소스 목록은 [리소스에 대한 작업 지원 이동을](region-move-support.md)참조하십시오.
