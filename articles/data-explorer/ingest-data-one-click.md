---
title: 단일 클릭 수집을 사용 하 여 데이터를 Azure 데이터 탐색기 수집 합니다.
description: 한 번의 클릭으로 수집을 사용 하 여 데이터를 Azure 데이터 탐색기 수집 (로드) 하는 방법에 대해 알아봅니다.
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 08/14/2019
ms.openlocfilehash: b1ce2d9efe44021b4e3191739bd2f922e34c44cb
ms.sourcegitcommit: 0e59368513a495af0a93a5b8855fd65ef1c44aac
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/15/2019
ms.locfileid: "69520050"
---
# <a name="use-one-click-ingestion-to-ingest-data-into-azure-data-explorer"></a>단일 클릭 수집을 사용 하 여 데이터를 Azure 데이터 탐색기 수집 합니다.

이 문서에서는 단일 클릭 수집을 사용 하 여 저장소에서 Azure 데이터 탐색기로 json 또는 csv 형식의 새 테이블을 신속 하 게 수집 하는 방법을 보여 줍니다. 데이터가 수집 되 면 테이블을 편집 하 고 웹 UI를 사용 하 여 쿼리를 실행할 수 있습니다.

## <a name="prerequisites"></a>필수 구성 요소

* Azure 구독이 아직 없는 경우 시작하기 전에 [Azure 체험 계정](https://azure.microsoft.com/free/)을 만듭니다.
* [애플리케이션](https://dataexplorer.azure.com/)에 로그인합니다.
* [Azure 데이터 탐색기 클러스터 및 데이터베이스](create-cluster-database-portal.md) 만들기
* Azure Storage의 데이터 원본입니다.

## <a name="ingest-new-data"></a>새 데이터 수집

1. *데이터베이스 이름을* 마우스 오른쪽 단추로 클릭 하 고 **새 데이터 수집 (미리 보기)** 을 선택 합니다.

    ![웹 UI에서 한 번 클릭으로 수집을 선택 합니다.](media/ingest-data-one-click/one-click-ingestion-in-webui.png)   
 
1. 데이터 수집 **(미리 보기)** 창의 **원본** 탭에서 **프로젝트 세부 정보**를 완료 합니다.

    * 새 **테이블 이름을**입력 합니다. 
    * **저장소에서**수집 **유형** > 을 선택 합니다.
    * 저장소 **에 대 한 링크를** 입력 하 여 저장소에 url을 추가 합니다. 개인 저장소 계정에는 Blob SAS URL을 사용 합니다. 
    * **스키마 편집** 선택
 
    ![한 번의 클릭으로 수집 소스 세부 정보](media/ingest-data-one-click/one-click-ingestion-source.png) 

1. **스키마** 탭의 드롭다운 > **JSON** 또는 **CSV**에서 **데이터 형식** 을 선택 합니다. 
   
   **CSV**선택:
    * Csv 파일의 머리글 행을 무시 하려면 제목 **무시** 확인란을 선택 합니다.    
    * **매핑 이름은** 자동으로 설정 되지만 편집할 수 있습니다.

    ![한 번 클릭으로 수집 csv 형식 스키마 .png](media/ingest-data-one-click/one-click-csv-format.png)

   Select **JSON**:
    * **JSON 수준**선택: 1-10부터 시작 합니다. Json 파일의 수준은 오른쪽 아래에 있는 표에 표시 됩니다. 
    * **매핑 이름은** 자동으로 설정 되지만 편집할 수 있습니다.

    ![한 번의 클릭으로 수집 json 형식 스키마](media/ingest-data-one-click/one-click-json-format.png)  

1. **편집기**에서 **V** 오른쪽을 선택 하 여 편집기를 엽니다. 편집기에서 사용자 입력 으로부터 생성 된 자동 쿼리를 보고 복사할 수 있습니다. 

1.  오른쪽 아래에 있는 테이블에서 다음을 수행 합니다. 
    * 열 **이름 바꾸기**, 열 **삭제**, **오름차순 정렬**또는 **내림차순 정렬** 의 열 오른쪽에서 **V** 를 선택 합니다.
    * 편집할 열 이름을 두 번 클릭 합니다.
    * 열 이름 왼쪽에 있는 아이콘을 선택 하 여 데이터 형식을 변경 합니다. 

1. 수집 **시작** 을 선택 하 여 테이블 만들기, 매핑 만들기 및 데이터 수집을 선택 합니다.
 
## <a name="query-data"></a>쿼리 데이터

1. 데이터 수집 **완료** 창에서 데이터 수집이 성공적으로 완료 되 면 세 단계 모두 녹색 표시 표시로 표시 됩니다. 
 
    ![한 번의 클릭으로 데이터 수집 완료](media/ingest-data-one-click/one-click-data-ingestion-complete.png)

1. 쿼리를 열려면 **V** 를 선택 합니다. 웹 UI에 복사 하 여 쿼리를 편집 합니다.

1. 오른쪽의 메뉴에는 **빠른 쿼리** 및 **도구가**포함 되어 있습니다. 

    * **빠른 쿼리** 는 예제 쿼리를 사용 하는 웹 UI에 대 한 링크를 포함 합니다.
    * **도구** 에는 관련 `.drop` 명령을 실행 하 여 문제를 해결할 수 있는 **Drop 명령을** 사용 하 여 웹 UI에 대 한 링크가 포함 되어 있습니다.

    > [!TIP]
    > 명령을 사용 하 여 `.drop` 데이터가 손실 될 수 있습니다. 신중 하 게 사용 합니다.

## <a name="next-steps"></a>다음 단계

* [Azure 데이터 탐색기 웹 UI에서 데이터 쿼리](web-query-data.md)
* [Kusto 쿼리 언어를 사용 하 여 Azure 데이터 탐색기에 대 한 쿼리 작성](write-queries.md)
