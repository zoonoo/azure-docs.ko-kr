---
title: "Visual Studio용 Azure Stream Analytics 도구 사용 | Microsoft Docs"
description: "Visual Studio용 Azure Stream Analytics 도구 시작 자습서"
keywords: Visual Studio
documentationcenter: 
services: stream-analytics
author: jeffstokes72
manager: jhubbard
editor: cgronlun
ms.assetid: a473ea0a-3eaa-4e5b-aaa1-fec7e9069f20
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 03/28/2017
ms.author: sujie
translationtype: Human Translation
ms.sourcegitcommit: 503f5151047870aaf87e9bb7ebf2c7e4afa27b83
ms.openlocfilehash: d0125dda4df69279e49a9fad4dc28dcbf6368322
ms.lasthandoff: 03/29/2017


---
# <a name="use-azure-stream-analytics-tool-for-visual-studio"></a>Visual Studio용 Azure Stream Analytics 도구 사용
Visual Studio용 Azure Stream Analytics 도구는 현재 일반 공급 상태입니다. Stream Analytics 사용자는 이러한 도구를 통해 풍부한 경험을 얻고 문제를 해결할 수 있을 뿐만 아니라 복잡한 쿼리를 작성하고 로컬에서도 쿼리를 작성할 수 있습니다. 또한 Stream Analytics 작업을 Visual Studio 프로젝트로 내보낼 수도 있습니다.

## <a name="introduction"></a>소개
이 자습서에서는 Visual Studio용 Azure Stream Analytics 도구를 사용하여 Azure Stream Analytics 작업을 만들고 작성하며 로컬에서 테스트하고 관리 및 디버깅하는 방법을 알아봅니다. 

이 자습서를 완료하고 나면 다음을 수행할 수 있습니다.
* Visual Studio용 Azure Stream Analytics 도구 숙지
* Stream Analytics 작업 구성 및 배포
* 로컬 샘플 데이터로 로컬에서 작업 테스트
* 모니터링을 사용하여 문제 해결
* 기존 작업을 프로젝트로 내보내기

## <a name="prerequisites"></a>필수 조건
이 자습서를 완료하려면 다음 필수 구성 요소가 필요합니다.
* [Stream Analytics 자습서를 사용하여 IoT 솔루션 빌드](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-build-an-iot-solution-using-stream-analytics)에서 **Stream Analytics 작업 만들기** 이전의 단계를 완료합니다. 
* Visual Studio 2015, Visual Studio 2013 업데이트 4 또는 Visual Studio 2012. Enterprise(Ultimate/Premium), Professional, Community Edition이 지원됩니다. Express Edition은 지원되지 않습니다. Visual Studio 2017은 현재 지원되지 않습니다. 
* Microsoft Azure SDK for .NET 버전 2.7.1 이상.  [웹 플랫폼 설치 관리자](http://www.microsoft.com/web/downloads/platform.aspx)를 사용하여 설치합니다.
* [Visual Studio용 Azure Stream Analytics 도구](http://aka.ms/asatoolsvs) 설치

## <a name="create-a-stream-analytics-project"></a>Stream Analytics 프로젝트 만들기
Visual Studio에서 **파일 메뉴**를 클릭하고 **새 프로젝트**를 선택합니다. 왼쪽의 템플릿 목록에서 **Stream Analytics**를 선택한 후 **Azure Stream Analytics 응용 프로그램**을 클릭합니다.
다른 프로젝트와 마찬가지로 맨 아래에 프로젝트 이름, 위치 및 솔루션 이름을 입력합니다.

![Azure Stream Analytics 프로젝트 만들기](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-create-project-01.png)

**솔루션 탐색기**에 **Toll** 프로젝트가 생성된 것을 볼 수 있습니다.

![Azure Stream Analytics 프로젝트 만들기](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-create-project-02.png)

## <a name="choose-the-correct-subscription"></a>올바른 구독 선택
1. **보기** 메뉴를 통해 Visual Studio에서 **서버 탐색기**를 엽니다.
2. Azure 계정으로 로그인합니다. 

## <a name="define-input-sources"></a>입력 원본 정의
1.    **솔루션 탐색기**에서 **입력** 노드를 확장하고 이름을 **Input.json**에서 **EntryStream.json**으로 바꿉니다. **EntryStream.json**을 두 번 클릭합니다.
2.    그러면 **입력 별칭**이 **EntryStream**이 됩니다. 입력 별칭은 쿼리 스크립트에 사용됩니다. 
3.    원본 형식은 **데이터 스트림**입니다.
4.    원본은 **Event hub**입니다.
5.    Service Bus 네임스페이스는 드롭다운에서 **tollData** 항목이어야 합니다.
6.    Event hub 이름은 **entry**로 설정해야 합니다.
7.    Event hub 정책 이름은 **RootManageSharedAccessKey**(기본값)입니다.
8.    **이벤트 직렬화 형식**에 대해 **JSON**을, **인코딩**에 대해 **UTF8**을 선택합니다.
   
   설정이 다음과 같이 표시됩니다.
   
   ![입력 원본 정의](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-define-input-01.png)
   
9.    마법사를 완료하려면 페이지 맨 아래에서 **저장**을 클릭합니다. 이제 다른 입력 원본을 추가하여 진출 스트림을 만들 수 있습니다. 입력 노드를 마우스 오른쪽 단추로 클릭하고 **새 항목**을 클릭합니다.
   
   ![입력 원본 정의](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-define-input-02.png)
   
10.    팝업 창에서 **Azure Stream Analytics 입력**을 선택하고 이름을 **ExitStream.json**으로 변경합니다. **추가**를 클릭합니다.
   
   ![입력 원본 정의](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-define-input-03.png)
   
11.    프로젝트에서 **ExitStream.json**을 두 번 클릭하고 진입 스트림과 같은 단계로 입력합니다. 다음 스크린샷과 같이 Event Hub 이름 값을 입력해야 합니다.
   
   ![입력 원본 정의](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-define-input-04.png)
   
   이제 두 입력 스트림을 정의했습니다.
   
   ![입력 원본 정의](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-define-input-05.png)
   
   다음은 차량 등록 데이터가 있는 blob 파일에 대해 참조 데이터 입력을 추가합니다.
   
12.    프로젝트에서 **입력** 노드를 마우스 오른쪽 단추로 클릭한 다음 스트림 입력에 대한 동일한 프로세스를 수행하지만 데이터 스트림 대신 **참조 데이터**를 선택하고 입력 별칭은 **등록**입니다.
   
   ![입력 원본 정의](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-define-input-06.png)
   
13.    **tolldata**가 포함된 저장소 계정을 선택합니다. 컨테이너 이름이 **tolldata**여야 하고 **경로 패턴**은 **registration.json**이어야 합니다. 이 파일 이름은 대/소문자를 구분하므로 소문자여야 합니다.
14.    **저장**을 클릭하여 마법사를 마칩니다.

이제 모든 입력이 정의되었습니다.

## <a name="define-output"></a>출력 정의
1.    **솔루션 탐색기**에서 **입력** 노드를 확장하고 **Output.json**을 두 번 클릭합니다.
2.    출력 별칭을 **출력**으로 설정한 다음 싱크를 SQL Database로 설정합니다.
2.    데이터베이스 이름으로 **TollDataDB**를 입력합니다.
3.    **사용자 이름** 필드에 **tolladmin**을, **암호** 필드에 **123toll!**을, **테이블** 필드에 **TollDataRefJoin**을 입력합니다.
4.    **Save**를 클릭합니다.

![출력 정의](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-define-output-01.png)
 
## <a name="azure-stream-analytics-query"></a>Azure Stream Analytics 쿼리
이 자습서에서는 톨게이트 요금 데이터와 관련된 몇 가지 비즈니스 질문에 대답하고 관련 대답을 제공하기 위해 Azure Stream Analytics에서 사용할 수 있는 Stream Analytics 쿼리를 생성해봅니다.
첫 번째 Stream Analytics 작업을 시작하기 전에 간단한 시나리오 및 쿼리 구문을 살펴보겠습니다.

### <a name="introduction-to-azure-stream-analytics-query-language"></a>Azure Stream Analytics 쿼리 언어 소개
요금 창구에 들어가는 차량 수를 계산해야 한다고 가정해봅시다. 이 작업은 연속 이벤트 스트림이므로 "기간"을 정의해야 합니다. 이 질문을 "3분 간격으로 요금 창구에 진입하는 차량은 몇 대입니까?”로 수정해보겠습니다. 이를 일반적으로 연속 개수(Tumbling Count)라고 합니다.

이 질문에 대답하는 Azure Stream Analytics 쿼리를 살펴보겠습니다.

        SELECT TollId, System.Timestamp AS WindowEnd, COUNT(*) AS Count 
        FROM EntryStream TIMESTAMP BY EntryTime 
        GROUP BY TUMBLINGWINDOW(minute, 3), TollId 

아시다시피 Azure Stream Analytics는 SQL과 유사한 쿼리어를 사용하고 몇 가지 확장을 추가하여 쿼리의 시간 관련 측면을 지정합니다.

자세한 내용을 보려면 MSDN의 쿼리에 사용된 [시간 관리](https://msdn.microsoft.com/library/azure/mt582045.aspx) 및 [창 작업](https://msdn.microsoft.com/library/azure/dn835019.aspx) 구문을 참조하세요.

이제 첫 번째 Azure Stream Analytics 쿼리를 작성했으므로 다음 경로의 TollApp 폴더에 있는 샘플 데이터 파일을 사용하여 테스트해보겠습니다.

<seg>
  **..\TollApp\TollApp\Data**</seg>

이 폴더에는 다음 파일이 포함되어 있습니다. •    Entry.json •    Exit.json •    Registration

## <a name="question-number-of-vehicles-entering-a-toll-booth"></a>질문: 요금 창구에 들어가는 차량 수
프로젝트에서 Script.asaql을 두 번 클릭하여 편집기에서 스크립트를 열고 이전 섹션의 스크립트를 편집기에 붙여 넣습니다. 쿼리 편집기에서는 IntelliSense, 구문 색 지정 및 오류 마커를 지원합니다.

![쿼리 편집](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-query-01.png)
 
### <a name="testing-azure-stream-analytics-queries-locally"></a>로컬에서 Azure Stream Analytics 쿼리 테스트

1. 먼저 쿼리를 컴파일하여 구문 오류가 있는지 확인할 수 있습니다. [TBD]
2. 샘플 데이터에 대해 이 쿼리의 유효성을 검사하려면 입력을 마우스 오른쪽 단추로 클릭하고 상황에 맞는 메뉴에서 **로컬 입력 추가**를 선택하여 로컬 샘플 데이터를 사용할 수 있습니다.
   
   ![로컬 입력 추가](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-add-local-input-01.png)
   
   팝업 창에서 로컬 경로의 샘플 데이터를 선택합니다. **Save**를 클릭합니다.
   
   ![로컬 입력 추가](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-add-local-input-02.png)
   
   **local_EntryStream.json** 파일이 입력 폴더에 자동으로 추가됩니다.
   
   ![로컬 입력 추가](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-add-local-input-03.png)
   
3. 쿼리 편집기에서 로컬로 실행을 클릭합니다. 또는 F5 키를 눌러도 됩니다.
   
   ![로컬 실행](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-local-run-01.png)
   
   콘솔에서 출력 경로를 찾아 아무 키나 누르면 결과 폴더가 열립니다.
   
   ![로컬 실행](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-local-run-02.png)
   
4. 로컬 폴더에서 결과를 확인합니다.
   
   ![로컬 실행](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-local-run-03.png)
   
   
### <a name="sample-input"></a>샘플 입력
입력 원본에서 로컬 파일로 입력 데이터를 샘플링할 수도 있습니다. 입력 구성 파일을 마우스 오른쪽 단추로 클릭하고 **데이터 샘플링**을 선택합니다. 

![샘플 데이터](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-sample-data-01.png)

현재는 Event Hub 또는 IoT Hub만 샘플링할 수 있습니다. 다른 입력 원본은 지원되지 않습니다.  팝업 대화 상자 창에서 샘플 데이터를 저장할 로컬 경로를 입력하세요. **샘플링**을 클릭합니다.

![샘플 데이터](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-sample-data-02.png)
 
출력 창에서 진행률을 볼 수 있습니다. 

![샘플 데이터](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-sample-data-03.png)
 
### <a name="submit-azure-stream-analytics-query-to-azure"></a>Azure Stream Analytics 쿼리를 Azure에 제출
**쿼리 편집기**에서 **Submit To Azure in script editor(스크립트 편집기에서 Azure에 제출)**를 클릭합니다.

![작업 제출](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-submit-job-01.png)
 
Create a New Azure Stream Analytics Job(새 Azure Stream Analytics 작업 만들기)을 선택합니다. 아래와 같이 작업 이름을 입력합니다. 올바른 구독을 선택합니다. 제출을 클릭합니다.

![작업 제출](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-submit-job-02.png)

 
### <a name="start-job"></a>작업 시작
이제 작업이 생성되었고 작업 보기가 자동으로 열립니다. **녹색** 단추를 클릭하여 작업을 시작합니다.

![작업 시작](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-start-job-01.png)
 
기본 설정을 선택하고 **시작**을 클릭합니다.
 
![작업 시작](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-start-job-02.png)

작업 상태가 **실행 중**으로 변경되고 입력/출력 이벤트가 있는 것을 볼 수 있습니다.

![작업 시작](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-start-job-03.png)

## <a name="check-results-in-visual-studio"></a>Visual Studio에서 결과 확인
1. Visual Studio 서버 탐색기를 열고 **TollDataRefJoin** 테이블을 마우스 오른쪽 단추로 클릭합니다.
2. **테이블 데이터 표시** 를 선택하면 작업의 출력을 볼 수 있습니다.
   
   ![서버 탐색기의 "테이블 데이터 표시" 선택 항목](media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-check-results.jpg)
   

### <a name="view-job-metrics"></a>작업 메트릭 보기
몇 가지 기본 작업 통계는 **Job Metrics(작업 메트릭)**에서 확인할 수 있습니다. 

![작업 메트릭](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-job-metrics-01.png)

 
## <a name="list-job-in-server-explorer"></a>서버 탐색기에 작업 나열
**서버 탐색기**에서 **Stream Analytics 작업**을 클릭하고 **새로 고침**을 클릭합니다. **Stream Analytics 작업** 아래에 작업이 나타나는 것을 확인할 수 있습니다.

![작업 나열](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-list-jobs-01.png)


## <a name="open-job-view"></a>작업 보기 열기
작업 노드를 확장하고 **작업 보기** 노드를 두 번 클릭하여 작업 보기를 엽니다.

![작업 보기](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-job-view-01.png)


## <a name="export-an-existing-job-to-a-project"></a>기존 작업을 프로젝트로 내보내기
두 가지 방법으로 기존 작업을 프로젝트로 내보낼 수 있습니다.
1. **서버 탐색기**의 **Stream Analytics 작업** 아래에서 작업 노드를 마우스 오른쪽 단추로 클릭합니다. 상황에 맞는 메뉴에서 **Export to New Stream Analytics Project(새 Stream Analytics 프로젝트로 내보내기)**를 클릭합니다.
   
   ![작업 내보내기](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-export-job-01.png)
   
   **솔루션 탐색기**에서 생성된 프로젝트를 볼 수 있습니다.
   
   ![작업 내보내기](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-export-job-02.png)
   
2. 작업 보기에서 **Generate Project(프로젝트 생성)**를 클릭합니다.
   
   ![작업 내보내기](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-export-job-03.png)
   
## <a name="known-issues-and-limitations"></a>알려진 문제 및 제한 사항
 
1. 쿼리에 지리 공간 함수가 포함된 경우 로컬 테스트가 작동하지 않습니다. 
2. 편집기에서는 JavaScript UDF 추가 또는 변경이 지원되지 않습니다.
3. 로컬 테스트에서 JSON 형식으로 출력 저장은 지원되지 않습니다. 
4. Power BI 출력 및 ADLS 출력은 지원되지 않습니다.



## <a name="next-steps"></a>다음 단계
* [Azure Stream Analytics 소개](stream-analytics-introduction.md)
* [Azure Stream Analytics 사용 시작](stream-analytics-get-started.md)
* [Azure 스트림 분석 작업 규모 지정](stream-analytics-scale-jobs.md)
* [Azure 스트림 분석 쿼리 언어 참조](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Azure Stream Analytics 관리 REST API 참조](https://msdn.microsoft.com/library/azure/dn835031.aspx)



