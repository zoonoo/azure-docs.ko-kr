---
title: Azure Blob 컨테이너, SQL Server 및 Hive 테이블에서 데이터 샘플링 | Microsoft Docs
description: 다양한 Azure 환경에 저장된 데이터를 탐색하는 방법
services: machine-learning
documentationcenter: ''
author: deguhath
manager: cgronlun
editor: cgronlun
ms.assetid: 80a9dfae-e3a6-4cfb-aecc-5701cfc7e39d
ms.service: machine-learning
ms.component: team-data-science-process
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/13/2017
ms.author: deguhath
ms.openlocfilehash: 60431578ba8b5c740fe5e1124dcdad32c3cb2ede
ms.sourcegitcommit: 96527c150e33a1d630836e72561a5f7d529521b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/09/2018
ms.locfileid: "51344089"
---
# <a name="heading"></a>Azure blob 컨테이너, SQL Server 및 Hive 테이블의 데이터 샘플링

다음 문서에서는 세 가지 Azure 위치 중 하나에 저장된 데이터를 샘플링하는 방법을 설명합니다.

* [**Azure BLOB 컨테이너 데이터**](sample-data-blob.md)는 프로그래밍 방식으로 다운로드한 다음 샘플 Python 코드로 샘플링함으로써 샘플링됩니다.
* [**SQL Server 데이터**](sample-data-sql-server.md)는 SQL 및 Python 프로그래밍 언어를 둘 다 사용하여 샘플링됩니다. 
* [**Hive 테이블 데이터**](sample-data-hive.md)는 Hive 쿼리를 사용하여 샘플링됩니다.

이 샘플 작업은 [TDSP(팀 데이터 과학 프로세스)](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/)의 단계입니다.

**데이터를 샘플링하는 이유**

분석할 데이터 집합이 큰 경우 일반적으로 데이터를 다운 샘플링하여 작지만 전형적이고 관리하기 쉬운 크기로 줄이는 것이 좋습니다. 그러면 데이터 이해, 탐색 및 기능 엔지니어링이 용이해집니다. Cortana 분석 프로세스에서는 데이터 처리 기능 및 기계 학습 모델의 빠른 프로토타입 제작을 지원하는 역할을 합니다.

