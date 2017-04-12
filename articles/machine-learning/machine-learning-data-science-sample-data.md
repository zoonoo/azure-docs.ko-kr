---
title: "Azure Blob 컨테이너, SQL Server 및 Hive 테이블에서 데이터 샘플링 | Microsoft Docs"
description: "다양한 Azure 환경에 저장된 데이터를 탐색하는 방법"
services: machine-learning
documentationcenter: 
author: bradsev
manager: jhubbard
editor: cgronlun
ms.assetid: 80a9dfae-e3a6-4cfb-aecc-5701cfc7e39d
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/24/2017
ms.author: fashah;garye;bradsev
translationtype: Human Translation
ms.sourcegitcommit: a6bc79b2cb5b73109cddd6cf57caeba754b52e2e
ms.openlocfilehash: c046220304075242f655bced2f0f16a6fb692ad3
ms.lasthandoff: 12/20/2016


---
# <a name="heading"></a>Azure blob 컨테이너, SQL Server 및 Hive 테이블의 데이터 샘플링
이 문서는 세 가지 다른 Azure 위치 중 하나에 저장된 데이터를 샘플링하는 방법을 설명하는 토픽에 대한 링크를 포함합니다.

* **Azure blob 컨테이너 데이터** 는 프로그래밍 방식으로 다운로드한 다음 샘플 Python 코드로 샘플링함으로써 샘플링됩니다.
* **SQL Server 데이터** 는 SQL 및 Python 프로그래밍 언어를 모두 사용하여 샘플링됩니다. 
* **Hive 테이블 데이터** 는 Hive 쿼리를 사용하여 샘플링됩니다.

다음의 **메뉴** 는 이러한 각 Azure Storage 환경에서 데이터를 샘플링하는 방법을 설명하는 토픽에 연결되는 링크입니다. 

[!INCLUDE [cap-sample-data-selector](../../includes/cap-sample-data-selector.md)]

이 샘플 작업은 [TDSP(팀 데이터 과학 프로세스)](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/)의 단계입니다.

## <a name="why-sample-data"></a>데이터를 샘플링하는 이유
분석할 데이터 집합이 큰 경우 일반적으로 데이터를 다운 샘플링하여 작지만 전형적이고 관리하기 쉬운 크기로 줄이는 것이 좋습니다. 그러면 데이터 이해, 탐색 및 기능 엔지니어링이 용이해집니다. Cortana 분석 프로세스에서는 데이터 처리 기능 및 기계 학습 모델의 빠른 프로토타입 제작을 지원하는 역할을 합니다.


