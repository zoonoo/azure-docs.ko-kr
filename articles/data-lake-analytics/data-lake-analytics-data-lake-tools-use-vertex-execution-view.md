---
title: "Data Lake Tools for Visual Studio에서 Vertex Execution View 사용 | Microsoft 문서"
description: "Data Lake Analytics 작업을 검사하기 위해 Vertex Execution View를 사용하는 방법을 알아봅니다."
services: data-lake-analytics
documentationcenter: 
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: 5366d852-e7d6-44cf-a88c-e9f52f15f7df
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 10/13/2016
ms.author: jgao
translationtype: Human Translation
ms.sourcegitcommit: ac5a64b759376c06e058ae015b73f1b73b7d1e7b
ms.openlocfilehash: b2acf4fd95c1611ecd580b508339d1305010fdd8


---
# <a name="use-the-vertex-execution-view-in-data-lake-tools-for-visual-studio"></a>Visual Studio용 Data Lake Tools의 Vertex Execution View 사용
Data Lake Analytics 작업을 검사하기 위해 Vertex Execution View를 사용하는 방법을 알아봅니다.

## <a name="prerequisites"></a>필수 조건
* Visual Studio용 Data Lake Tools를 사용한 U-SQL 스크립트 개발에 대한 기본 지식.  [자습서: Visual Studio용 Data Lake Tools를 사용하여 U-SQL 스크립트 개발](data-lake-analytics-data-lake-tools-get-started.md)을 참조하세요.

## <a name="open-the-vertex-execution-view"></a>Vertex Execution View 열기
특정 작업의 경우, 왼쪽 하단 모서리에 있는 “Vertex Execution View” 링크를 클릭 할 수 있습니다. 먼저 프로필을 로드하라는 메시지가 나타날 것이며 네트워크 연결 상태에 따라 약간의 시간이 걸릴 수 있습니다.

![Data Lake Analytics Tools Vertex Execution View](./media/data-lake-analytics-data-lake-tools-use-vertex-execution-view/data-lake-tools-open-vertex-execution-view.png)

## <a name="understand-vertex-execution-view"></a>Vertex Execution View 이해
Vertex Execution View를 입력한 후 세 부분이 있습니다.

![Data Lake Analytics Tools Vertex Execution View](./media/data-lake-analytics-data-lake-tools-use-vertex-execution-view/data-lake-tools-vertex-execution-view.png)

* Vertex 선택기: 왼쪽은 Vertex 선택기입니다.  기능별로(예: 상위 데이터 10개 읽기, 또는 단계별 선택) 꼭짓점을 선택할 수 있습니다.
  
    가장 일반적으로 사용되는 필터 중 하나는 중요 패스 상의 꼭짓점입니다. 중요 패스는 U-SQL 작업의 가장 긴 패스입니다. 중요 패스는 어떤 꼭지점이 가장 긴 시간이 걸리는지 확인하여 작업을 최적화하는 데 유용합니다.
* 맨위 가운데 창:
  
    ![Data Lake Analytics Tools Vertex Execution View](./media/data-lake-analytics-data-lake-tools-use-vertex-execution-view/data-lake-tools-vertex-execution-view-pane2.png)
  
    이 보기는 또한 모든 꼭지점의 실행 상태를 보여줍니다. 시간을 로컬 컴퓨터에 적합하게 변환하고 다른 상태를 다른 색으로 나타내 보여줍니다.
* 맨아래 가운데 창:
  
    ![Data Lake Analytics Tools Vertex Execution View](./media/data-lake-analytics-data-lake-tools-use-vertex-execution-view/data-lake-tools-vertex-execution-view-pane3.png)
  
  * 프로세스 이름: 꼭짓점 인스턴스의 이름입니다. StageName|VertexName|VertexRunInstance의 다른 부분들로 구성됩니다. 예를 들어, SV7_Split[62].v1 꼭짓점은 Stage SV7_Split에서 Vertex 번호 62번의 두 번째 실행 인스턴스(v1, 인덱스는 0부터 시작됨)을 나타냅니다.
  * 총 읽기/쓰기 데이터: 이 꼭짓점에서 읽기/쓰기를 수행한 데이터 크기입니다.
  * 시스템 상태/종료 상태: 꼭짓점이 끝났을 때의 최종 상태입니다.
  * 종료 코드/오류 유형: 꼭짓점이 실패했을 때의 오류입니다.
  * 생성 이유: 꼭짓점이 만들어진 이유입니다.
  * 리소스 대기 시간/프로세스 대기 시간/PN 큐 대기 시간: 꼭짓점이 리소스를 기다리는 데, 데이터를 처리하는 데, 큐에서 기다리는 데 걸리는 시간을 나타냅니다.
  * 프로세스/작성자 GUID:.현재 실행 중인 꼭짓점 또는 작성자에 대한 GUID입니다.
  * 버전: 실행 중인 버전의 N번째 인스턴스(시스템은 장애 조치, 컴퓨터 중복 등과 같은 여러 이유로 꼭짓점의 새 인스턴스 일정을 잡을 수도 있습니다)
  * 버전이 만들어진 시간.
  * 프로세스 만들기 시작 시간/프로세스 대기 중인 시간/프로세스 시작 시간/프로세스 완료 시간: 꼭짓점 프로세스가 만들기를 시작한 때, 꼭짓점 프로세스가 큐에 대기하기 시작한 때, 특정 꼭짓점 프로세스가 시작한 때, 특정 꼭짓점 프로세스가 완료된 때를 각기 나타냅니다.

## <a name="next-steps"></a>다음 단계
* Data Lake Analytics에 대한 개요를 보려면 [Azure Data Lake Analytics 개요](data-lake-analytics-overview.md)를 참조하세요.
* U-SQL 응용 프로그램 개발을 시작하려면 [Visual Studio용 Data Lake 도구를 사용하여 U-SQL 스크립트 개발](data-lake-analytics-data-lake-tools-get-started.md)을 참조하세요.
* U-SQL을 알아보려면 [Azure Data Lake Analytics U-SQL 언어 시작](data-lake-analytics-u-sql-get-started.md)을 참조하세요.
* 관리 작업을 보려면 [Azure Portal을 사용하여 Azure Data Lake Analytics 관리](data-lake-analytics-manage-use-portal.md)를 참조하세요.
* 진단 정보를 기록하려면 [Azure Data Lake Analytics에 대한 진단 로그에 액세스](data-lake-analytics-diagnostic-logs.md)
* 더 복잡한 쿼리를 보려면 [Azure Data Lake Analytics을 사용하여 웹 사이트 로그 분석](data-lake-analytics-analyze-weblogs.md)을 참조하세요.
* 작업 세부 정보를 보려면, [Azure Data lake Analytics 작업에 대한 작업 브라우저 및 작업 보기 사용하기](data-lake-analytics-data-lake-tools-view-jobs.md)를 참조하세요.
* Visual Studio용 Azure Data Lake 도구에 대해 알아보려면 [Visual Studio 코드용 Azure Data Lake 도구 사용](data-lake-analytics-data-lake-tools-for-vscode.md)을 참조하세요.



<!--HONumber=Nov16_HO4-->


