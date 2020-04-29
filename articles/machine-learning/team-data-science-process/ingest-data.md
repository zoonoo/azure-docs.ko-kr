---
title: Azure Storage 환경에 데이터 로드-팀 데이터 과학 프로세스
description: 데이터가 저장 되 고 처리 되는 다양 한 대상 환경으로 데이터를 수집 하는 방법에 대해 알아봅니다.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: f8eab59d810fb825dbebf80d01d8efd2dd0a9841
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "76720540"
---
# <a name="load-data-into-storage-environments-for-analytics"></a>분석용 스토리지 환경에 데이터 로드

팀 데이터 과학 프로세스에서는 각 단계에서 가장 적절 한 방법으로 데이터를 수집 하거나 로드 해야 합니다. 데이터 대상에는 Azure VM의 Azure Blob Storage, SQL Azure 데이터베이스, SQL Server, HDInsight (Hadoop), Synapse Analytics 및 Azure Machine Learning 포함 될 수 있습니다. 

다음 문서에서는 데이터가 저장되고 처리되는 다양한 대상 환경으로 데이터를 수집하는 방법을 설명합니다.

* [Azure Blob Storage](move-azure-blob.md) 간 수집
* [Azure VM의 SQL Server](move-sql-server-virtual-machine.md)로 수집
* [Azure SQL Database](move-sql-azure.md)
* [Hive 테이블](move-hive-tables.md)로 수집
* [SQL 분할된 테이블](parallel-load-sql-partitioned-tables.md)로 수집
* [온-프레미스 SQL Server](move-sql-azure-adf.md)에서 수집

데이터의 초기 위치, 형식 및 크기 뿐만 아니라 기술 및 비즈니스 요구 사항에 따라 최상의 데이터 수집 계획이 결정 됩니다. 모범 사례에 따라 몇 가지 단계를 수행 하는 것은 드문 일이 아닙니다. 이 작업 시퀀스에는 데이터 탐색, 사전 처리, 정리, 다운 샘플링, 모델 학습 등이 포함될 수 있습니다.  Azure Data Factory은 데이터 이동 및 변환을 오케스트레이션 하는 데 권장 되는 Azure 리소스입니다.
