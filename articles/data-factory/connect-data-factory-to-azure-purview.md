---
title: Azure Purview에 Data Factory 연결
description: Azure 부서의 범위에 Data Factory를 연결 하는 방법에 대해 알아봅니다.
services: data-factory
ms.author: lle
author: lrtoyou1223
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom:
- seo-lt-2019
- references_regions
ms.date: 12/3/2020
ms.openlocfilehash: d1a07a3733bddc1516a4d2e19f3cac31a7702954
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/28/2021
ms.locfileid: "98944941"
---
# <a name="connect-data-factory-to-azure-purview-preview"></a>Azure 부서의 범위에 Data Factory 연결 (미리 보기)
[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

이 문서에서는 data factory를 Azure 부서의 범위에 연결 하 고 ADF 활동의 데이터 계보를 보고 하는 방법, 데이터 흐름 및 SSIS 패키지를 실행 하는 방법을 설명 합니다.

## <a name="connect-data-factory-to-azure-purview"></a>Azure 부서의 범위에 data factory 연결
Azure 부서의 범위는 데이터 사용자가 클라우드 및 온-프레미스 환경 전반에 걸친 데이터 관리를 중앙에서 관리 하는 데 사용할 수 있는 새로운 클라우드 서비스입니다. 데이터 팩터리를 Azure 부서의 범위에 연결 하 고 연결을 사용 하 여 복사, 데이터 흐름 및 SSIS 실행 패키지의 계보 데이터를 캡처하기 위해 Azure 부서의 범위를 활용할 수 있습니다. Azure 부서의 범위에서 데이터 팩터리를 등록 하는 방법에 대 한 자세한 내용은 [Azure Data Factory 및 Azure 부서의 범위를 연결 하는 방법](../purview/how-to-link-azure-data-factory.md)을 참조 하세요. 

## <a name="report-lineage-data-to-azure-purview"></a>Azure 부서의 범위에 계보 데이터 보고
고객은 Azure Data Factory에서 복사, 데이터 흐름 또는 SSIS 패키지 실행 작업을 실행할 때 종속성 관계를 얻을 수 있으며 데이터 원본 및 대상 간의 전체 워크플로 프로세스에 대 한 개략적인 개요를 사용할 수 있습니다.
Azure Data Factory에서 계보를 수집 하는 방법은 [데이터 팩터리 계보](../purview/how-to-link-azure-data-factory.md#supported-azure-data-factory-activities)를 참조 하세요.

## <a name="next-steps"></a>다음 단계
[카탈로그 계보 사용자 가이드](../purview/catalog-lineage-user-guide.md)

[자습서: Azure 부서의 범위에 Data Factory 계보 데이터 푸시](turorial-push-lineage-to-purview.md)