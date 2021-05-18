---
title: Azure 스토리지 환경으로 데이터 로드 - 팀 데이터 과학 프로세스
description: 다음 문서는 데이터가 저장되고 처리되는 다양한 대상 환경으로 데이터를 수집하는 방법을 설명합니다.
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
ms.openlocfilehash: 46e911ad6bdf5a478d46e425f8700740ece56c6e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "96452660"
---
# <a name="load-data-into-storage-environments-for-analytics"></a>분석용 스토리지 환경에 데이터 로드

팀 데이터 과학 프로세스에 따르면 데이터는 프로세스의 각 단계에서 가장 적절한 방법으로 수집되거나 로드되어야 합니다. 데이터 대상에는 Azure Blob Storage, SQL Azure 데이터베이스, Azure VM의 SQL Server, HDInsight(Hadoop), Azure Synaps Analytics 및 Azure Machine Learning이 포함될 수 있습니다. 

다음 문서에서는 데이터가 저장되고 처리되는 다양한 대상 환경으로 데이터를 수집하는 방법을 설명합니다.

* [Azure Blob Storage](move-azure-blob.md) 간 수집
* [Azure VM의 SQL Server](move-sql-server-virtual-machine.md)로 수집
* [Azure SQL Database](move-sql-azure.md)로 수집
* [Hive 테이블](move-hive-tables.md)로 수집
* [SQL 분할된 테이블](parallel-load-sql-partitioned-tables.md)로 수집
* [온-프레미스 SQL Server](move-sql-azure-adf.md)에서 수집

데이터의 처음 위치, 형식 및 크기는 물론 기술 및 비즈니스 요구에 따라 가장 좋은 데이터 수집 계획을 결정합니다. 모범 사례에 따라 몇 가지 단계를 수행하는 경우도 흔합니다. 이 작업 시퀀스에는 데이터 탐색, 사전 처리, 정리, 다운 샘플링, 모델 학습 등이 포함될 수 있습니다.  Azure Data Factory는 데이터 이동 및 변환을 오케스트레이션하는 데 권장되는 Azure 리소스입니다.
