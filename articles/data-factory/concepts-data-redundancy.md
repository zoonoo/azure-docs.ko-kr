---
title: Azure Data Factory의 데이터 중복성 | Microsoft Docs
description: Azure Data Factory의 메타 데이터 중복 메커니즘에 대해 알아봅니다.
services: data-factory
documentationcenter: ''
author: nabhishek
manager: weehyongtok
ms.reviewer: abnarain
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 11/05/2020
ms.author: abnarain
ms.openlocfilehash: f71fdf66624d67939f915f91c2cc1dbe7553cad7
ms.sourcegitcommit: 46c5ffd69fa7bc71102737d1fab4338ca782b6f1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/06/2020
ms.locfileid: "94332115"
---
# <a name="azure-data-factory-data-redundancy"></a>**Azure Data Factory 데이터 중복성**

Azure Data Factory 데이터에는 메타 데이터 (파이프라인, 데이터 집합, 연결 된 서비스, 통합 런타임 및 트리거) 및 모니터링 데이터 (파이프라인, 트리거 및 작업 실행)가 포함 됩니다. 

모든 지역 (브라질 남부 및 동남 아시아)에서 Azure Data Factory 데이터는 메타 데이터 손실 로부터 보호 하기 위해 [쌍을 이루는 지역](https://docs.microsoft.com/azure/best-practices-availability-paired-regions#azure-regional-pairs) 에 저장 되 고 복제 됩니다. 지역 데이터 센터 오류가 발생 하는 동안 Microsoft는 Azure Data Factory 인스턴스의 지역 장애 조치 (failover)를 시작할 수 있습니다. 대부분의 경우 사용자의 작업은 필요 하지 않습니다. Microsoft에서 관리 하는 장애 조치 (failover)가 완료 되 면 장애 조치 (failover) 지역에서 Azure Data Factory에 액세스할 수 있습니다. 

브라질 남부 및 동남 아시아의 데이터 상주 요구 사항으로 인해 Azure Data Factory 데이터는 [로컬 지역에만](https://docs.microsoft.com/azure/storage/common/storage-redundancy#locally-redundant-storage)저장 됩니다. 동남 아시아의 경우 모든 데이터가 싱가포르에 저장 됩니다. 브라질 남부의 경우 모든 데이터는 브라질에 저장 됩니다. 심각한 재해가 발생 하 여 지역이 손실 된 경우 Microsoft는 Azure Data Factory 데이터를 복구할 수 없습니다.  

> [!NOTE]
> Microsoft에서 관리 하는 장애 조치 (failover)는 일반적으로 고객 관리 되므로 SHIR (자체 호스팅 integration runtime)에는 적용 되지 않습니다. Azure VM에 SHIR이 설정 된 경우 azure [site recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview) 를 활용 하 여 다른 지역에 대 한 [azure vm 장애 조치 (failover)](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-architecture) 를 처리 하는 것이 좋습니다.



## <a name="using-source-control-in-azure-data-factory"></a>**Azure Data Factory에서 소스 제어 사용**

Azure data factory 메타 데이터에 대 한 변경 내용을 추적 하 고 감사할 수 있도록 하려면 Azure Data Factory에 대 한 소스 제어를 설정 하는 것을 고려해 야 합니다. 파이프라인, 데이터 집합, 연결 된 서비스 및 트리거에 대 한 메타 데이터 JSON 파일에도 액세스할 수 있습니다. Azure Data Factory를 사용 하 여 다른 Git 리포지토리 (Azure DevOps 및 GitHub)를 사용할 수 있습니다. 

 [Azure Data Factory에서 소스 제어](https://docs.microsoft.com/azure/data-factory/source-control)를 설정 하는 방법을 알아봅니다. 

> [!NOTE]
> 재해 (지역 손실)의 경우 새 데이터 팩터리를 수동으로 또는 자동화 된 방식으로 프로 비전 할 수 있습니다. 새 데이터 팩터리를 만든 후 기존 Git 리포지토리에서 파이프라인, 데이터 집합 및 연결 된 서비스 JSON을 복원할 수 있습니다. 



## <a name="data-stores"></a>**데이터 저장소**

Azure Data Factory를 사용 하 여 온-프레미스 및 클라우드에 있는 데이터 저장소 간에 데이터를 이동할 수 있습니다. 데이터 저장소의 비즈니스 연속성을 보장 하려면 이러한 각 데이터 저장소에 대 한 비즈니스 연속성 권장 사항을 참조 해야 합니다. 

 

## <a name="see-also"></a>추가 정보

- [Azure 지역 쌍](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)
- [Azure의 데이터 상주](https://azure.microsoft.com/global-infrastructure/data-residency/) 
