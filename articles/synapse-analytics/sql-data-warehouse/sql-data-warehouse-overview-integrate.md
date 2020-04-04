---
title: 통합 솔루션 구축
description: Synapse SQL 풀과 통합되는 솔루션 도구 및 파트너입니다.
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/17/2018
ms.author: martinle
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 2f6e091b6e0285bea5fef9e4d0be40faec936c6b
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/03/2020
ms.locfileid: "80633137"
---
# <a name="integrate-other-services-with-a-sql-analytics-data-warehouse"></a>다른 서비스를 SQL Analytics 데이터 웨어하우스와 통합

Azure Synapse Analytics 내의 SQL 분석 기능을 사용하면 사용자가 Azure의 다른 많은 서비스와 통합할 수 있습니다. SQL Analytics를 사용하면 SQL Pool 리소스를 통해 데이터 웨어하우스를 만들 수 있으며, 그 중 일부는 다음과 같은 몇 가지 추가 서비스를 활용할 수 있습니다.

* Power BI
* Azure 데이터 팩터리
* Azure Machine Learning
* Azure Stream Analytics

Azure 전체의 통합 서비스에 대한 자세한 내용은 [통합 파트너](sql-data-warehouse-partner-data-integration.md)문서를 참조하세요.

## <a name="power-bi"></a>Power BI

Power BI 통합을 사용하면 데이터 웨어하우스의 컴퓨팅 성능을 Power BI의 동적 보고 및 시각화와 결합할 수 있습니다. Power BI 통합에는 현재 다음이 포함됩니다.

* **직접 연결**: SQL 풀을 사용하여 프로비저닝된 데이터 웨어하우스에 대한 논리적 푸시다운을 사용한 고급 연결입니다. 푸시다운은 더 큰 규모를 더욱 빠르게 분석합니다.
* **Power BI에서 열기**: 'Power BI에서 열기' 버튼은 인스턴스 정보를 Power BI에 전달하여 연결하는 간단한 방법을 제공합니다.

자세한 내용은 [Power BI와 통합](sql-data-warehouse-get-started-visualize-with-power-bi.md) 또는 [Power BI 설명서](https://powerbi.microsoft.com/blog/exploring-azure-sql-data-warehouse-with-power-bi/)를 참조하세요.

## <a name="azure-data-factory"></a>Azure 데이터 팩터리

Azure 데이터 팩터리는 복잡한 추출을 만들고 파이프라인을 로드할 수 있는 관리되는 플랫폼을 사용자에게 제공합니다. Azure 데이터 팩터리와의 SQL 풀 통합에는 다음이 포함됩니다.

* **저장 프로시저**: 저장 프로시저의 실행을 오케스트레이션합니다.
* **복사**: ADF를 사용하여 데이터를 SQL 풀로 이동합니다. 이 작업에서는 백그라운드에서 ADF의 표준 데이터 이동 메커니즘 또는 PolyBase가 사용될 수 있습니다.

자세한 내용은 [Azure Data Factory와 통합](../../data-factory/load-azure-sql-data-warehouse.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)을 참조하세요.

## <a name="azure-machine-learning"></a>Azure Machine Learning

Azure Machine Learning은 사용자가 다양한 예측 도구를 활용하여 복잡한 모델을 만들 수 있는 완전히 관리되는 분석 서비스입니다. SQL 풀은 이러한 모델의 원본 및 대상으로 지원되며 다음과 같은 기능이 있습니다.

* **데이터 읽기:** SQL 풀에 대해 T-SQL을 사용하여 대규모 로 모델을 구동합니다.
* **데이터 쓰기:** 모든 모델에서 SQL 풀로 변경 내용을 커밋합니다.

자세한 내용은 [Azure Machine Learning과 통합](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md)을 참조하세요.

## <a name="azure-stream-analytics"></a>Azure Stream Analytics

Azure Stream Analytics는 Azure Event Hub에서 생성된 이벤트 데이터를 처리 및 사용하기 위한 복잡하고 완전히 관리되는 인프라입니다.  SQL 풀과의 통합을 통해 스트리밍 데이터를 관계형 데이터와 함께 효과적으로 처리하고 저장할 수 있으므로 보다 심층적이고 고급 분석이 가능합니다.  

* **작업 출력:** 스트림 애널리틱스 작업의 출력을 SQL 풀로 직접 보냅니다.

자세한 내용은 [Azure Stream Analytics와 통합](sql-data-warehouse-integrate-azure-stream-analytics.md)을 참조하세요.
