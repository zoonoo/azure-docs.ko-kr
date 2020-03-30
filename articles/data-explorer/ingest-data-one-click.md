---
title: 한 번의 클릭으로 수집을 사용하여 Azure 데이터 탐색기로 데이터 수집
description: 한 번의 클릭으로 데이터를 Azure 데이터 탐색기로 수집(로드)하는 방법에 대해 알아봅니다.
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 10/31/2019
ms.openlocfilehash: 4a53f7e68501ce7f9b19dea0822d3896ec241fb8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75444550"
---
# <a name="use-one-click-ingestion-to-ingest-data-into-azure-data-explorer"></a>한 번의 클릭으로 수집을 사용하여 Azure 데이터 탐색기로 데이터 수집

이 문서에서는 JSON 또는 CSV 형식의 새 테이블을 빠르게 사용하기 위해 원클릭 사용법을 사용하는 방법을 보여 주며, 이 문서에서는 이를 사용합니다. 데이터는 저장소 또는 로컬 파일에서 기존 테이블 또는 새 테이블로 수집할 수 있습니다. 직관적인 원클릭 마법사를 사용하고 몇 분 내에 데이터를 수집합니다. 그런 다음 Azure 데이터 탐색기 웹 UI를 사용하여 테이블을 편집하고 쿼리를 실행할 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

* Azure 구독이 아직 없는 경우 시작하기 전에 [Azure 체험 계정](https://azure.microsoft.com/free/)을 만듭니다.
* [응용 프로그램에 로그인합니다.](https://dataexplorer.azure.com/)
* [Azure 데이터 탐색기 클러스터 및 데이터베이스](create-cluster-database-portal.md)만들기.
* [웹 UI에](https://dataexplorer.azure.com/) 로그인하고 [클러스터에 연결을 추가합니다.](/azure/data-explorer/web-query-data#add-clusters)

## <a name="ingest-new-data"></a>새 데이터 수집

1. 웹 UI의 왼쪽 메뉴에서 *데이터베이스* 또는 *테이블* 행을 마우스 오른쪽 단추로 클릭하고 **새 데이터 수집(미리 보기)을**선택합니다.

    ![웹 UI에서 한 번의 클릭으로 섭취 선택](media/ingest-data-one-click/one-click-ingestion-in-webui.png)   
 
1. 새 **데이터 수집(미리 보기)** 창에서 **소스** 탭을 선택하고 **프로젝트 세부 정보를 완료합니다.**

    * **테이블의**경우 드롭다운 메뉴에서 기존 테이블 이름을 선택하거나 **새** 테이블만들기를 선택합니다.
    * **인기 변환 유형의**경우 **저장소 또는** **파일에서**하나를 선택합니다.
      * **저장소에서**선택한 경우 **저장소에 대한 링크를** 선택하여 URL을 추가합니다. 개인 저장소 계정에 [Blob SAS URL을](/azure/vs-azure-tools-storage-explorer-blobs#get-the-sas-for-a-blob-container) 사용합니다. 
      * **파일에서**선택한 경우 **찾아보기를** 선택하고 파일을 상자로 드래그합니다.
    * **스키마 편집을** 선택하여 테이블 열 구성을 보고 편집합니다.
 
    ![원클릭 가져오기 소스 세부 정보](media/ingest-data-one-click/one-click-ingestion-source.png) 

    > [!TIP]
    > *테이블* 행에서 **새 데이터 수집(미리 보기)을** 선택하면 선택한 테이블 이름이 **프로젝트 세부 정보에**나타납니다.

1. 기존 테이블을 선택한 경우 **맵 열** 창이 열리면 원본 데이터 열을 대상 테이블 열에 매핑합니다. 
    * **Omit 열을** 사용하여 테이블에서 대상 열을 제거합니다.
    * **새 열을** 사용하여 테이블에 새 열을 추가합니다.

    ![지도 열 창](media/ingest-data-one-click/one-click-map-columns-window.png)

1. **스키마** 탭에서:

    * 드롭다운 메뉴에서 **압축 유형을** 선택한 다음 **압축해제** 또는 **GZip**을 선택합니다.
    * 드롭다운 메뉴에서 **데이터 형식을** 선택한 다음 **JSON,** **CSV,** **TSV,** **SCSV,** **SOHSV,** **TSVE**또는 **PSV를**선택합니다. 
        * **JSON** 형식을 선택할 때 **JSON 수준도**1에서 10까지 선택해야 합니다. 수준은 테이블 열 데이터 묘사에 영향을 미칩니다. 
        * JSON 이외의 형식을 선택하는 경우 파일의 제목 행을 무시하려면 **열 이름 포함** 확인란을 선택해야 합니다.
    * **매핑 이름은** 자동으로 설정되지만 편집할 수 있습니다.
    * 기존 테이블을 선택한 경우 맵 **열을** 선택하여 맵 열 창을 열 수 **있습니다.**

    ![원클릭 인식 CSV 형식 스키마](media/ingest-data-one-click/one-click-csv-format.png)

1. **편집기** 창 위에서 **v** 단추를 선택하여 편집기를 엽니다. 편집기에서 입력에서 생성된 자동 쿼리를 보고 복사할 수 있습니다. 

1. 표에서: 
    * **데이터 유형 변경,** **열 이름 바꾸기, 열** **삭제,** **오름차순 정렬**또는 **내림차순 정렬을**위해 새 열 헤더를 마우스 오른쪽 단추로 클릭합니다. 기존 열에서는 데이터 정렬만 사용할 수 있습니다. 
    * 편집할 새 열 이름을 두 번 클릭합니다.

1. **수집 시작을** 선택하여 테이블 및 매핑을 만들고 데이터 수집을 시작합니다.

    ![원클릭 으로 JSON 형식 스키마](media/ingest-data-one-click/one-click-json-format.png) 
 
## <a name="query-data"></a>쿼리 데이터

1. 데이터 **수집이 완료된** 창에서 데이터 수집이 성공적으로 완료되면 세 단계 모두 녹색 확인 표시로 표시됩니다.
 
    ![원클릭 데이터 수집 완료](media/ingest-data-one-click/one-click-data-ingestion-complete.png)

1. **v** 버튼을 선택하여 쿼리를 엽니다. 웹 UI에 복사하여 쿼리를 편집합니다.

1. 오른쪽 메뉴에는 빠른 쿼리 및 **도구** 옵션이 포함되어 **있습니다.** 

    * **빠른 쿼리에는** 예제 쿼리가 있는 웹 UI에 대한 링크가 포함됩니다.
    * **도구에는** 웹 UI에서 **Drop 명령에** 대한 링크가 포함되어 있으므로 관련 `.drop` 명령을 실행하여 문제를 해결할 수 있습니다.

    > [!TIP]
    > 명령을 사용하면 `.drop` 데이터가 손실될 수 있습니다. 신중하게 사용하십시오.

## <a name="next-steps"></a>다음 단계

* [Azure Data Explorer Web UI에서 데이터 쿼리](web-query-data.md)
* [Kusto 쿼리 언어를 사용하여 Azure 데이터 탐색기용 쿼리 작성](write-queries.md)
