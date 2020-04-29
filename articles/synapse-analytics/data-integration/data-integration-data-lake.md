---
title: Azure Synapse Analytics에서 Azure Data Lake Storage Gen2에 수집
description: Azure Synapse Analytics에서 Azure Data Lake Storage Gen2으로 데이터를 수집 하는 방법을 알아봅니다.
services: synapse-analytics
author: djpmsft
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: daperlov
ms.reviewer: jrasnick
ms.openlocfilehash: 4d7d7be523749797e5dbce0e50c307fc682974f2
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81430579"
---
# <a name="ingesting-data-into-azure-data-lake-storage-gen2"></a>데이터를 Azure Data Lake Storage Gen2으로 수집 

이 문서에서는 Azure Synapse Analytics를 사용 하 여 Azure Data Lake Gen 2 (Azure Data Lake Gen 2) 저장소 계정에서 한 위치에서 다른 위치로 데이터를 수집 하는 방법을 알아봅니다.

## <a name="prerequisites"></a>전제 조건

* **Azure 구독**: azure 구독이 없는 경우 시작 하기 전에 [무료 azure 계정](https://azure.microsoft.com/free/) 을 만듭니다.
* **Azure Storage 계정**: Azure Data Lake Gen 2를 *원본* 데이터 저장소로 사용 합니다. 저장소 계정이 없는 경우 새로 만드는 단계는 [Azure Storage 계정 만들기](../../storage/blobs/data-lake-storage-quickstart-create-account.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) 를 참조 하세요.

## <a name="create-linked-services"></a>연결된 서비스 만들기

Azure Synapse Analytics에서 연결 된 서비스는 다른 서비스에 대 한 연결 정보를 정의 합니다. 이 섹션에서는 Azure Synapse Analytics 및 Azure Data Lake Gen 2를 연결 된 서비스로 추가 합니다.

1. Azure Synapse Analytics UX를 열고 **관리** 탭으로 이동 합니다.
1. **외부 연결**에서 **연결 된 서비스**를 선택 합니다.
1. 연결 된 서비스를 추가 하려면 **새로 만들기**를 클릭 합니다.
1. 목록에서 Azure Data Lake Storage Gen2 타일을 선택 하 고 **계속**을 클릭 합니다.
1. 인증 자격 증명을 입력 합니다. 계정 키, 서비스 주체 및 관리 id는 현재 지원 되는 인증 유형입니다. 연결 테스트를 클릭 하 여 자격 증명이 올바른지 확인 합니다. 
1. 작업을 마쳤으면 **만들기**를 클릭합니다.

## <a name="create-pipeline"></a>파이프라인 만들기

파이프라인은 일련의 활동을 실행 하기 위한 논리적 흐름을 포함 합니다. 이 섹션에서는 Azure Data Lake Gen 2에서 SQL 풀로 데이터를 수집 하는 복사 작업이 포함 된 파이프라인을 만듭니다.

1. **오케스트레이션** 탭으로 이동 합니다. 파이프라인 헤더 옆에 있는 더하기 아이콘을 클릭 하 고 **파이프라인**을 선택 합니다.
1. 작업 창의 **이동 및 변환** 에서 파이프라인 캔버스로 **데이터 복사** 를 끌어 옵니다.
1. 복사 작업을 클릭 하 고 **원본** 탭으로 이동 합니다. 새 원본 데이터 집합을 만들려면 **새로** 만들기를 클릭 합니다.
1. 데이터 저장소로 Azure Data Lake Storage Gen2를 선택 하 고 계속을 클릭 합니다.
1. 형식으로 DelimitedText를 선택 하 고 계속을 클릭 합니다.
1. 설정 속성 창에서 만든 ADLS 연결 된 서비스를 선택 합니다. 원본 데이터의 파일 경로를 지정 하 고 첫 번째 행에 머리글이 있는지 여부를 지정 합니다. 파일 저장소나 샘플 파일에서 스키마를 가져올 수 있습니다. 작업이 완료되면 [확인]을 클릭합니다.
1. **싱크** 탭으로 이동 하 **고 새로 만들기를 클릭 하** 여 새 싱크 데이터 집합을 만듭니다.
1. 데이터 저장소로 Azure Data Lake Storage gen2를 선택 하 고 계속을 클릭 합니다.
1. 형식으로 DelimitedText를 선택 하 고 계속을 클릭 합니다.
1. 설정 속성 창에서 만든 ADLS 연결 된 서비스를 선택 합니다. 데이터를 쓸 폴더의 경로를 지정 합니다. 작업이 완료되면 [확인]을 클릭합니다.

## <a name="debug-and-publish-pipeline"></a>디버그 및 게시 파이프라인

파이프라인 구성을 완료 한 후에는 아티팩트를 게시 하기 전에 디버그 실행을 실행 하 여 모든 것이 올바른지 확인할 수 있습니다.

1. 파이프라인을 디버그하려면 도구 모음에서 **디버그**를 선택합니다. 창의 아래쪽에 있는 **출력** 탭에서 파이프라인 실행 상태가 표시됩니다. 
1. 파이프라인이 성공적으로 실행 되 면 상단 도구 모음에서 **모두 게시**를 선택 합니다. 이 작업은 사용자가 만든 엔터티 (데이터 집합 및 파이프라인)를 Synapse Analytics 서비스에 게시 합니다.
1. **게시됨** 메시지가 표시될 때까지 기다립니다. 알림 메시지를 보려면 오른쪽 위에 있는 종 모양 단추를 클릭 합니다. 


## <a name="trigger-and-monitor-the-pipeline"></a>파이프라인 트리거 및 모니터링

이 단계에서는 이전 단계에서 게시 된 파이프라인을 수동으로 트리거합니다. 

1. 도구 모음에서 **트리거 추가**를 선택한 다음, **지금 트리거**를 선택합니다. **파이프라인 실행** 페이지에서 **마침**을 선택합니다.  
1. 왼쪽 세로 막대에 있는 **모니터** 탭으로 이동 합니다. 수동 트리거로 트리거되는 파이프라인 실행이 표시됩니다. **작업** 열의 링크를 사용 하 여 활동 세부 정보를 보고 파이프라인을 다시 실행할 수 있습니다.
1. 파이프라인 실행과 연결된 활동 실행을 확인하려면 **작업** 열에서 **활동 실행 보기** 링크를 선택합니다. 이 예제에서는 활동이 하나뿐이므로 목록에 하나의 항목만 표시됩니다. 복사 활동에 대한 자세한 내용은 **작업** 열에서 **세부 정보** 링크(안경 아이콘)를 선택합니다. 위쪽의 **파이프라인 실행**을 선택하여 파이프라인 실행 보기로 돌아갑니다. 보기를 새로 고치려면 **새로 고침**을 선택합니다.
1. 데이터가 SQL 풀에 올바르게 기록 되었는지 확인 합니다.


## <a name="next-steps"></a>다음 단계

Synapse Analytics의 데이터 통합에 대 한 자세한 내용은 [수집 data to a SQL pool](data-integration-sql-pool.md) 문서를 참조 하세요.
