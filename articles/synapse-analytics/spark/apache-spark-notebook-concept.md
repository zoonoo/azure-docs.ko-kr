---
title: Azure Synapse Analytics 노트북 개요
description: 이 문서에서는 Azure Synapse Analytics 노트북을 통해 제공 되는 기능에 대 한 개요를 제공 합니다.
services: synapse-analytics
author: midesa
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 11/18/2020
ms.author: midesa
ms.reviewer: jrasnick
ms.subservice: spark
ms.openlocfilehash: 4387aadd70ddb373df1bdfa61cbe9ed7f2af283d
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/25/2020
ms.locfileid: "95920135"
---
# <a name="azure-synapse-analytics-notebooks"></a>Azure Synapse Analytics 노트북
Synapse Studio (미리 보기) 노트북은 라이브 코드, 시각화 및 설명을 포함 하는 파일을 만들 수 있는 웹 인터페이스입니다. Notebook은 아이디어를 검증하고 빠른 실험을 사용하여 데이터를 통해 인사이트를 확보하기 좋은 도구입니다. 

Azure Synapse Studio Notebook을 사용하면 다음이 가능합니다.

* 시작에 필요한 설정 작업이 전혀 없습니다.
* 기본 제공 엔터프라이즈 보안 기능으로 데이터 보안을 유지합니다.
* 원시 형식(CSV, txt, JSON 등), 처리된 파일 형식(parquet, Delta Lake, ORC 등), SQL 및 Spark에 대한 SQL 테이블 형식 데이터 파일 모두에서 데이터를 분석합니다.
* 향상된 제작 기능 및 기본 제공 데이터 시각화를 통해 생산성을 높일 수 있습니다.

이 섹션에는 언어 혼합, 데이터 시각화 만들기, 노트북 매개 변수화, 파이프라인 빌드 등에 대 한 문서가 포함 되어 있습니다. 또한 노트북 개발을 시작 하는 방법에 대 한 참조 및 자습서가 포함 되어 있습니다.

## <a name="create-manage-and-use-notebooks"></a>노트북 만들기, 관리 및 사용
Synapse Studio UI를 사용 하 여 노트북을 관리할 수 있습니다. 

전자 필기장을 만들고 관리 하는 방법에 대해 자세히 알아보려면 다음 문서를 참조 하세요.
  - 노트북 관리
    - [노트북 만들기](./spark/../apache-spark-development-using-notebooks.md#create-a-notebook)
    - [Notebook 개발](./spark/../apache-spark-development-using-notebooks.md#develop-notebooks)
    - [Notebook에 데이터 가져오기](./spark/../apache-spark-development-using-notebooks.md#bring-data-to-a-notebook)
    - [매직 명령 및 임시 테이블을 사용 하 여 여러 언어 사용](./spark/../apache-spark-development-using-notebooks.md#integrate-a-notebook)
    - [셀 매직 명령 사용](./spark/../apache-spark-development-using-notebooks.md#magic-commands)
  - 개발
    - [Spark 세션 설정 구성](./spark/../apache-spark-development-using-notebooks.md#spark-session-config)
    - [Microsoft Spark 유틸리티 사용](./spark/../microsoft-spark-utilities.md)
    - [노트북 및 라이브러리를 사용 하 여 데이터 시각화](./spark/../apache-spark-data-visualization.md)
    - [파이프라인에 노트북 통합](./spark/../apache-spark-development-using-notebooks.md#integrate-a-notebook)


## <a name="next-steps"></a>다음 단계
또한 노트북은 데이터 준비, 데이터 시각화, 기계 학습 및 기타 빅 데이터 시나리오에서 널리 사용 됩니다. 데이터 분석 및 빅 데이터 시나리오에 대해 노트북을 사용 하는 방법에 대해 자세히 알아보려면 다음 자습서를 참조 하세요.
  - [Notebook 만들기](./spark/../../quickstart-apache-spark-notebook.md)
  - [Synapse Studio 노트북을 사용 하 여 시각화 만들기](./spark/../apache-spark-data-visualization-tutorial.md)
  - [Apache Spark MLlib를 사용 하 여 기계 학습 모델 빌드](./spark/../apache-spark-machine-learning-mllib-notebook.md)
  - [Azure AutoML을 사용 하 여 기계 학습 모델 빌드](./spark/../apache-spark-azure-machine-learning-tutorial.md)