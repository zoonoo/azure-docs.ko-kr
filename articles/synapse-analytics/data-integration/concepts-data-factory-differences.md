---
title: Azure Data Factory와의 차이점
description: Azure Synapse Analytics의 데이터 통합 기능이와 어떻게 Azure Data Factory 다른 지 알아보세요.
services: synapse-analytics
author: kromerm
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 12/10/2020
ms.author: makromer
ms.reviewer: jrasnick
ms.openlocfilehash: 9cd3fc9353c684ec6617761616b958477ca83ee3
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/05/2021
ms.locfileid: "102183644"
---
# <a name="data-integration-in-azure-synapse-analytics-versus-azure-data-factory"></a>Azure Synapse Analytics의 데이터 통합 및 Azure Data Factory

Azure Synapse Analytics에서 Synapse 파이프라인 및 데이터 흐름과 같은 데이터 통합 기능은 Azure Data Factory을 기반으로 합니다. 자세한 내용은 [Azure Data Factory 정의](../../data-factory/introduction.md)를 참조 하세요.


## <a name="available-features-in-adf--azure-synapse-analytics"></a>ADF & Azure Synapse Analytics에서 사용할 수 있는 기능

다음 표에서 기능 가용성을 확인 하십시오.

| 범주                 | 기능    |  Azure 데이터 팩터리  | Azure Synapse Analytics |
| ------------------------ | ---------- | :------------------: | :---------------------: |
| **Integration Runtime**  | SSIS 및 SSIS Integration Runtime 사용 | ✓ | ✗ |
|                          | 지역 간 Integration Runtime 지원 (데이터 흐름) | ✓ | ✗ |
|                          | 공유 Integration Runtime | ✓<br><small>*서로 다른 데이터 팩터리에서 공유할 수 있습니다.* | ✗ |
|                          | Ttl (Time to Live) | ✓ | ✗ |
| **파이프라인 활동** | SSIS 패키지 작업 | ✓ | ✗ |
|                          | 파워 쿼리 작업에 대 한 지원 | ✓ | ✓ |
| **템플릿 갤러리 및 지식 센터** | 솔루션 템플릿 | ✓<br><small>*Azure Data Factory 템플릿 갤러리* | ✓<br><small>*Synapse 작업 영역 기술 센터* |
| **GIT 리포지토리 통합** | GIT 통합 | ✓ | ✓ |
| **Monitoring**           | 데이터 흐름에 대 한 Spark 작업 모니터링 | ✗ | ✓<br><small>*Synapse Spark 풀 활용* |
|                          | Azure Monitor와 통합 | ✓ | ✗ |
| **계보** | 부서의 범위에 파이프라인 계보 데이터 게시를 지원 합니다.  | ✓ | ✗ |  

> [!Note]
> Ttl ( **time To Live** )은 Spark 클러스터가 데이터 흐름을 실행 한 후 일정 기간 동안 *준비 상태를 유지할* 수 있도록 하는 Azure Integration Runtime 설정입니다.
>


## <a name="next-steps"></a>다음 단계

[Azure Data Lake Storage gen2 계정으로 데이터를 수집](data-integration-data-lake.md)하는 방법을 학습 하 여 Synapse 작업 영역에서 데이터 통합을 시작 하세요.
