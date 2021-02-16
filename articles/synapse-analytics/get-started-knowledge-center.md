---
title: '자습서: Synapse 지식 센터 살펴보기 시작'
description: 이 자습서에서는 Synapse 지식 센터를 사용하는 방법을 알아봅니다.
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.subservice: workspace
ms.topic: tutorial
ms.date: 11/16/2020
ms.openlocfilehash: 59364a0a2c1d1e411f41dc35dee812c8e920b72d
ms.sourcegitcommit: f5b8410738bee1381407786fcb9d3d3ab838d813
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/14/2021
ms.locfileid: "98209205"
---
# <a name="explore-the-synapse-knowledge-center"></a>Synapse 지식 센터 살펴보기

이 자습서에서는 Synapse Studio 지식 센터를 사용하는 방법에 대해 알아봅니다.

## <a name="getting-to-the-knowledge-center"></a>지식 센터로 이동

Synapse Studio에서 지식 센터를 찾는 방법에는 두 가지가 있습니다.

  1. 홈 허브의 페이지 오른쪽 위 근처에 있는 **학습** 을 클릭합니다.
  2. 위쪽의 메뉴 모음에서 **?** 를 클릭합니다. 그런 다음, **지식 센터** 를 클릭합니다.

두 가지 방법 중 하나를 선택하고 **지식 센터** 를 엽니다.

## <a name="overview"></a>개요

**지식 센터** 를 통해 다음과 같은 세 가지 작업을 수행할 수 있습니다.
* **샘플 즉시 사용**. Synapse의 작동 방식에 대한 빠른 예제를 원하는 경우 이 옵션을 선택합니다.
* **갤러리 찾아보기**. 이 옵션을 사용하면 샘플 데이터 세트를 연결하고, 샘플 코드를 SQL 스크립트, Notebook 및 파이프라인 형식으로 추가할 수 있습니다.
* **Synapse Studio 둘러보기**. 이 옵션을 사용하면 Synapse Studio의 기본 부분을 간략히 둘러볼 수 있습니다. 이 옵션은 Synapse Studio를 사용한 적이 없는 경우에 유용합니다.

## <a name="exploring-blob-storage-with-serverless-sql-pool"></a>서버리스 SQL 풀을 사용하여 Blob 스토리지 살펴보기

1. **지식 센터** 로 이동하여 **샘플 즉시 사용** 을 클릭합니다.
1. **SQL을 사용하여 데이터 쿼리** 를 선택합니다.
1. **샘플 사용** 을 클릭합니다.
1. 새 샘플 SQL 스크립트가 열립니다.
1. 첫 번째 쿼리(28~32번 줄)로 스크롤하여 쿼리 텍스트를 선택합니다.
1. 실행을 클릭합니다. 선택한 코드만 실행됩니다.

## <a name="loading-more-nyc-taxi-data"></a>더 많은 NYC Taxi 데이터 로드
1. **지식 센터** 로 이동하여 **갤러리 찾아보기** 를 클릭합니다.
1. 위쪽에서 **SQL 스크립트** 탭을 선택합니다.
1. 데이터 수집 샘플 **New York Taxicab 로드** 를 선택하고 **계속** 을 클릭합니다.
1. **SQL 풀** 아래에서 **기존 풀 선택** 을 선택하고, **SQLPOOL1** 을 선택하고, 이전에 만든 **SQLPOOL1** 데이터베이스를 선택합니다.
1. **스크립트 열기** 를 클릭합니다.
1. 새 샘플 SQL 스크립트가 열립니다.
1. **실행** 을 클릭합니다.
1. 그러면 모든 NYC Taxi 데이터에 대해 여러 테이블이 만들어지고, T-SQL COPY 명령을 사용하여 로드됩니다. 이전 빠른 시작 단계에서 이러한 테이블을 만든 경우 존재하지 않는 테이블에 대해 CREATE 및 COPY에 대한 코드만 선택하고 실행합니다.

    > [!NOTE] 
    > 전용 SQL 풀(이전의 SQL DW)과 함께 SQL 스크립트용 샘플 갤러리를 사용하는 경우 기존 전용 SQL 풀(이전의 SQL DW)만 사용할 수 있습니다.

## <a name="next-steps"></a>다음 단계

* [Azure Synapse Analytics 시작](get-started.md)
* [작업 영역 만들기](quickstart-create-workspace.md)
* [서버리스 SQL 풀 사용](quickstart-sql-on-demand.md)
