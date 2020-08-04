---
title: '자습서: Azure Synapse Analytics 시작'
description: 이 자습서에서는 Azure Synapse Analytics를 설정하고 사용하는 기본 단계에 대해 알아봅니다.
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.topic: tutorial
ms.date: 05/19/2020
ms.openlocfilehash: 7affc5aad89fd79e6ba6480f7bf10d37f90dc5e3
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/23/2020
ms.locfileid: "87075867"
---
# <a name="get-started-with-azure-synapse-analytics"></a>Azure Synapse Analytics 시작

이 자습서는 Azure Synapse Analytics의 주요 기능 영역을 안내하는 단계별 가이드입니다. 자습서는 Azure Synapse Analytics의 주요 시나리오를 통해 둘러보기를 원하는 사용자를 위한 이상적인 시작점입니다. 자습서의 단계를 따르면 모든 기능을 갖춘 Synapse 작업 영역이 마련되고 여기에서 SQL, SQL 주문형 및 Apache Spark를 사용하여 데이터 분석을 시작할 수 있습니다.

다음 내용을 배웁니다.
* Azure 구독에서 Synapse 작업 영역 프로비전
* Synapse 작업 영역에서 원활하게 작동하도록 ADLSGEN2 계정에 대한 액세스 제어 구성
* SQL, SQL 주문형 및 Spark에서 사용할 수 있도록 NYCTaxi 샘플 데이터를 Synapse 작업 영역으로 로드
* Synapse Studio를 사용하여 SQL 스크립트 및 Synapse Notebooks 편집 및 실행
* SQL 테이블 및 Spark 테이블 쿼리
* SQL 테이블의 데이터를 Spark 데이터 프레임으로 로드
* Spark 데이터 프레임에서 SQL 테이블로 데이터 로드
* ADLSGEN2 계정 콘텐츠 탐색
* ADLSGEN2 계정에서 Spark 및 SQL 주문형을 사용하여 parquet 데이터 파일 분석 
* 매시간 Synapse Notebook을 자동으로 실행하는 데이터 파이프라인 빌드

아래와 같은 *순서로* 단계를 수행하여 다양한 기능을 살펴보고 핵심 기능을 연습하는 방법을 알아보세요.

* [1단계 - Synapse 작업 영역 만들기 및 설정](get-started-create-workspace.md)
* [2단계 SQL 풀을 사용하여 분석](get-started-analyze-sql-pool.md)
* [3단계 Spark를 사용하여 분석](get-started-analyze-spark.md)
* [4단계 SQL 주문형을 사용하여 분석](get-started-analyze-sql-on-demand.md)
* [5단계 스토리지 계정에서 데이터 분석](get-started-analyze-storage.md)
* [6단계 - 파이프라인을 사용하여 오케스트레이션](get-started-pipelines.md)
* [7단계 - Power BI를 사용하여 데이터 시각화](get-started-visualize-power-bi.md)
