---
title: Azure 시냅스 분석에서 SQL 풀로 수집
description: Azure Synapse 분석에서 SQL 풀로 데이터를 수집하는 방법 알아보기
services: synapse-analytics
author: djpmsft
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: daperlov
ms.reviewer: jrasnick
ms.openlocfilehash: fbd8e03b1f8af7802133c35ae4860116aaea0c3c
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81430566"
---
# <a name="ingesting-data-into-a-sql-pool"></a>SQL 풀에 데이터 수집

이 문서에서는 Azure 시냅스 분석을 사용하여 Azure Data Lake Gen 2 저장소 계정의 데이터를 SQL 풀로 데이터를 수집하는 방법을 알아봅니다.

## <a name="prerequisites"></a>사전 요구 사항

* **Azure 구독**: Azure 구독이 없는 경우 시작하기 전에 [무료 Azure 계정을](https://azure.microsoft.com/free/) 만듭니다.
* **Azure 저장소 계정**: Azure Data Lake Storage Gen 2를 *원본* 데이터 저장소로 사용합니다. 저장소 계정이 없는 경우 [Azure Storage 계정 만들기를](../../storage/blobs/data-lake-storage-quickstart-create-account.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) 참조하여 계정을 만드는 단계를 확인합니다.
* **Azure 시냅스 분석**: SQL 풀을 *싱크* 데이터 저장소로 사용합니다. Azure Synapse 분석 인스턴스가 없는 경우 SQL [풀 만들기를](../../sql-database/sql-database-get-started-portal.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) 사용하여 만들 수 있습니다.

## <a name="create-linked-services"></a>연결된 서비스 만들기

Azure Synapse Analytics에서 연결된 서비스는 다른 서비스에 대한 연결 정보를 정의하는 곳입니다. 이 섹션에서는 Azure 시냅스 분석 및 Azure 데이터 레이크 저장소 Gen2 연결 서비스를 추가합니다.

1. Azure 시냅스 분석 UX를 열고 **관리** 탭으로 이동합니다.
1. **외부 연결에서** **연결된 서비스를**선택합니다.
1. 연결된 서비스를 추가하려면 **새**을 클릭합니다.
1. 목록에서 Azure 데이터 호수 저장소 Gen2 타일을 선택하고 **계속을**클릭합니다.
1. 인증 자격 증명을 입력합니다. 계정 키, 서비스 주체 및 관리되는 ID는 현재 지원되는 인증 유형입니다. 테스트 연결을 클릭하여 자격 증명이 올바른지 확인합니다. 작업을 마쳤으면 **만들기**를 클릭합니다.
1. 3-5단계를 반복하지만 Azure Data Lake Storage Gen2 대신 Azure Synapse 분석 타일을 선택하고 해당 연결 자격 증명을 입력합니다. Azure Synapse 분석의 경우 SQL 인증, 관리되는 ID 및 서비스 주체가 현재 지원됩니다.

## <a name="create-pipeline"></a>파이프라인 만들기

파이프라인에는 활동 집합의 실행에 대한 논리적 흐름이 포함되어 있습니다. 이 섹션에서는 ADLS 세대 2의 데이터를 SQL 풀로 수집하는 복사 활동이 포함된 파이프라인을 만듭니다.

1. **오케스트레이션** 탭으로 이동합니다. 파이프라인 헤더 옆의 더하기 아이콘을 클릭하고 **파이프라인**을 선택합니다.
1. 활동 창에서 **이동 및 변환** 아래에서 데이터 **복사를** 파이프라인 캔버스로 드래그합니다.
1. 복사 활동을 클릭하고 **소스** 탭으로 이동합니다. 새 원본 데이터 집합을 만들려면 **새로** 를 클릭합니다.
1. 데이터 저장소를 저장할 때 Azure Data Lake Storage gen2를 선택하고 계속을 클릭합니다.
1. 서식으로 구분텍스트를 선택하고 계속을 클릭합니다.
1. 속성 설정 창에서 만든 ADLS 연결 서비스를 선택합니다. 원본 데이터의 파일 경로를 지정하고 첫 번째 행에 헤더가 있는지 여부를 지정합니다. 파일 저장소 또는 샘플 파일에서 스키마를 가져올 수 있습니다. 작업이 완료되면 [확인]을 클릭합니다.
1. **싱크** 탭으로 이동합니다. 새 싱크 데이터 집합을 만들려면 **새** 를 클릭합니다.
1. 데이터 저장소로 Azure Synapse 분석을 선택하고 계속을 클릭합니다.
1. 속성 설정 창에서 만든 Azure Synapse 분석 링크된 서비스를 선택합니다. 기존 테이블에 쓰는 경우 드롭다운에서 테이블을 선택합니다. 그렇지 않으면 **편집을** 선택하고 새 테이블 이름을 입력합니다. 작업이 완료되면 [확인]을 클릭합니다.
1. 테이블을 만드는 경우 테이블 옵션 필드에서 **테이블 만들기 자동을** 사용하도록 설정합니다.

## <a name="debug-and-publish-pipeline"></a>파이프라인 디버그 및 게시

파이프라인 구성이 완료되면 아티팩트를 게시하기 전에 디버그 실행을 실행하여 모든 것이 올바른지 확인할 수 있습니다.

1. 파이프라인을 디버그하려면 도구 모음에서 **디버그**를 선택합니다. 창의 아래쪽에 있는 **출력** 탭에서 파이프라인 실행 상태가 표시됩니다. 
1. 파이프라인이 성공적으로 실행될 수 있다면 최상위 도구 모음에서 **모두 게시**를 선택합니다. 이 작업은 Synapse 분석 서비스에 만든 엔터티(데이터 집합 및 파이프라인)를 게시합니다.
1. **게시됨** 메시지가 표시될 때까지 기다립니다. 알림 메시지를 보려면 오른쪽 상단에 있는 벨 버튼을 클릭합니다. 


## <a name="trigger-and-monitor-the-pipeline"></a>파이프라인 트리거 및 모니터링

이 단계에서는 이전 단계에서 게시된 파이프라인을 수동으로 트리거합니다. 

1. 도구 모음에서 **트리거 추가**를 선택한 다음, **지금 트리거**를 선택합니다. **파이프라인 실행** 페이지에서 **마침**을 선택합니다.  
1. 왼쪽 사이드바에 있는 **모니터** 탭으로 이동합니다. 수동 트리거로 트리거되는 파이프라인 실행이 표시됩니다. **작업** 열의 링크를 사용하여 활동 세부 정보를 보고 파이프라인을 다시 실행할 수 있습니다.
1. 파이프라인 실행과 연결된 활동 실행을 확인하려면 **작업** 열에서 **활동 실행 보기** 링크를 선택합니다. 이 예제에서는 활동이 하나뿐이므로 목록에 하나의 항목만 표시됩니다. 복사 활동에 대한 자세한 내용은 **작업** 열에서 **세부 정보** 링크(안경 아이콘)를 선택합니다. 위쪽의 **파이프라인 실행**을 선택하여 파이프라인 실행 보기로 돌아갑니다. 보기를 새로 고치려면 **새로 고침**을 선택합니다.
1. 데이터가 SQL 풀에 올바르게 기록되었는지 확인합니다.


## <a name="next-steps"></a>다음 단계

Synapse Analytics의 데이터 통합에 대한 자세한 내용은 [Azure Data Lake Storage Gen2](data-integration-data-lake.md) 에 데이터를 수집합니다.
