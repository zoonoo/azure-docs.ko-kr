---
title: Azure Blob 컨테이너, SQL Server 및 Hive 테이블에서 데이터 샘플링 | Microsoft Docs
description: 다양한 Azure 환경에 저장된 데이터를 탐색하는 방법
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.component: team-data-science-process
ms.topic: article
ms.date: 11/13/2017
ms.author: tdsp
ms.custom: (previous author=deguhath, ms.author=deguhath)
ms.openlocfilehash: b7b233532a526879e886950c3a0d8cfe169251a7
ms.sourcegitcommit: 5aed7f6c948abcce87884d62f3ba098245245196
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/28/2018
ms.locfileid: "52441505"
---
# <a name="heading"></a>Azure blob 컨테이너, SQL Server 및 Hive 테이블의 데이터 샘플링

다음 문서에서는 세 가지 Azure 위치 중 하나에 저장된 데이터를 샘플링하는 방법을 설명합니다.

* [**Azure BLOB 컨테이너 데이터**](sample-data-blob.md)는 프로그래밍 방식으로 다운로드한 다음 샘플 Python 코드로 샘플링함으로써 샘플링됩니다.
* [**SQL Server 데이터**](sample-data-sql-server.md)는 SQL 및 Python 프로그래밍 언어를 둘 다 사용하여 샘플링됩니다. 
* [**Hive 테이블 데이터**](sample-data-hive.md)는 Hive 쿼리를 사용하여 샘플링됩니다.

이 샘플 작업은 [TDSP(팀 데이터 과학 프로세스)](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/)의 단계입니다.

**데이터를 샘플링하는 이유**

분석할 데이터 세트가 큰 경우 일반적으로 데이터를 다운 샘플링하여 작지만 전형적이고 관리하기 쉬운 크기로 줄이는 것이 좋습니다. 그러면 데이터 이해, 탐색 및 기능 엔지니어링이 용이해집니다. Cortana 분석 프로세스에서는 데이터 처리 기능 및 기계 학습 모델의 빠른 프로토타입 제작을 지원하는 역할을 합니다.

