---
title: Azure Data Factory와의 차이점
description: Azure Synapse Analytics의 데이터 통합 기능이와 어떻게 Azure Data Factory 다른 지 알아보세요.
services: synapse-analytics
author: djpmsft
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 11/06/2020
ms.author: daperlov
ms.reviewer: jrasnick
ms.openlocfilehash: 10f5336dd4c8a02acf623b1b14226ca676006953
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/07/2020
ms.locfileid: "94357652"
---
# <a name="data-integration-in-azure-synapse-analytics-versus-azure-data-factory"></a>Azure Synapse Analytics의 데이터 통합 및 Azure Data Factory

Azure Synapse Analytics에서 Synapse 파이프라인 및 데이터 흐름과 같은 데이터 통합 기능은 Azure Data Factory을 기반으로 합니다. 자세한 내용은 [Azure Data Factory 정의](../../data-factory/introduction.md)를 참조 하세요. 거의 모든 기능이 동일 하거나 유사 하며 두 서비스 간에 문서가 공유 됩니다. 이 문서에서는 Azure Data Factory와 Azure Synapse의 현재 차이점을 강조 표시 하 고 식별 합니다.

Azure Data Factory 기능이 나 아티클이 Azure Synapse에 적용 되는지 확인 하려면 문서 맨 위에 있는 모니커를 확인 합니다.

![모니커에 적용](../media/concepts-data-factory-differences/applies-to-moniker.png "모니커에 적용")

## <a name="features-in-azure-data-factory-not-planned-for-azure-synapse"></a>Azure Synapse에 대해 계획 되지 Azure Data Factory의 기능

다음 기능은 Azure Data Factory에서 사용할 수 있지만 Azure Synapse 계획 되지 않았습니다.

* **수명 및 이동 SSIS 패키지:** Azure Data Factory SSIS integration runtime을 사용 하 여 SSIS 패키지를 리프트 앤 시프트 할 수 있습니다. SSIS 통합 런타임과 SSIS 패키지 실행 작업은 Synapse 작업 영역에서 사용할 수 없습니다. 
* **Ttl (Time to live):** Time to live는 데이터 흐름이 완료 된 후 일정 *기간 동안 데이터* 흐름을 매핑할 spark 클러스터를 허용 하는 Azure 통합 런타임의 설정입니다. 이 기능은 Synapse 작업 영역에서 사용할 수 없습니다.

## <a name="azure-synapse-features-not-supported-in-azure-data-factory"></a>Azure Data Factory에서 지원 되지 않는 Azure Synapse 기능

다음 기능은 Azure Synapse에서 사용할 수 있지만 Azure Data Factory는 계획 되지 않았습니다.

* **데이터 흐름 매핑의 Spark 작업 모니터링:** Synapse에서 Spark 엔진은 사용자가 자세한 Spark 로그를 볼 수 있도록 사용자의 구독에 포함 되어 있습니다. Azure Data Factory에서 작업 실행은 Azure Data Factory 관리 되는 Spark 클러스터에서 수행 되며,이 정보를 사용할 수 없습니다. 

## <a name="azure-data-factory-features-that-behave-differently-in-synapse"></a>Synapse에서 다르게 동작 하는 Azure Data Factory 기능

다음 기능은 서로 다르게 동작 하거나 현재 Azure Synapse에 존재 하지 않습니다. 

* **랭 글 링 데이터 흐름:** 랭 글 링 데이터 흐름 작업은 현재 Azure Data Factory 에서만 사용할 수 있습니다.
* **솔루션 템플릿 갤러리:** Azure Data Factory에서 사용자는 솔루션 템플릿 갤러리에서 파이프라인 템플릿을 찾을 수 있습니다. Synapse 작업 영역에서 기술 센터는 추가 데이터 집합 및 SQL 스크립트와 함께 다른 템플릿 집합을 포함 합니다. 
* **Git 통합 및 기본 CI/CD 솔루션:** 현재 Synapse 작업 영역은 Git 리포지토리에 연결할 수 없으며 Azure Data Factory와 동일한 연속 통합 및 배달 프로세스를 따르지 않습니다.
* **Azure monitor와 통합:** Synapse 작업 영역은 Azure Data Factory 처럼 Azure Monitor와 통합 되지 않습니다.
* **하이브리드 통합 런타임 구성:** Synapse 작업 영역 내에서 사용자에 게 관리 되는 VNet IR 및 Azure IR 모두 있을 수는 없습니다. 이 기능은 Azure Data Factory에서 지원 됩니다.
* **Integration runtime 공유:** 자체 호스팅 통합 런타임을 Synapse 작업 영역 간에 공유할 수 없습니다. 이 기능은 Azure Data Factory에서 지원 됩니다.
* **데이터 흐름에 대 한 지역 간 통합 런타임:** 데이터 흐름은 Synapse 작업 영역과는 다른 지역의 통합 런타임에서 실행할 수 없습니다. 이 기능은 Azure Data Factory에서 지원 됩니다.

## <a name="next-steps"></a>다음 단계

[Azure Data Lake Storage gen2 계정으로 데이터를 수집](data-integration-data-lake.md)하는 방법을 학습 하 여 Synapse 작업 영역에서 데이터 통합을 시작 하세요.
