---
title: "Visual Studio용 Azure Stream Analytics 도구 사용 | Microsoft Docs"
description: "Visual Studio용 Azure Stream Analytics 도구 시작 자습서"
keywords: Visual Studio
documentationcenter: 
services: stream-analytics
author: 
manager: 
editor: 
ms.assetid: a473ea0a-3eaa-4e5b-aaa1-fec7e9069f20
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 
ms.author: 
ms.translationtype: Human Translation
ms.sourcegitcommit: 6dbb88577733d5ec0dc17acf7243b2ba7b829b38
ms.openlocfilehash: 618c1055795a75e0ed71dacddba3e076f81f4946
ms.contentlocale: ko-kr
ms.lasthandoff: 07/04/2017


---
# <a name="use-azure-stream-analytics-tools-for-visual-studio"></a>Azure Stream Analytics Tools for Visual Studio 사용
## <a name="introduction"></a>소개
이 자습서에서는 Azure Stream Analytics Tools for Visual Studio를 사용하여 Azure Stream Analytics 작업을 만들고 작성하며 로컬에서 테스트하고 관리 및 디버깅하는 방법을 알아봅니다. 

이 자습서를 완료하고 나면 다음을 수행할 수 있습니다.
* Stream Analytics Tools for Visual Studio를 익숙하게 사용 가능
* Stream Analytics 작업 구성 및 배포
* 로컬 샘플 데이터로 로컬에서 작업 테스트
* 모니터링을 사용하여 문제 해결
* 기존 작업을 프로젝트로 내보내기

## <a name="prerequisites"></a>필수 조건
이 자습서를 완료하려면 다음 필수 구성 요소가 필요합니다.
* [Stream Analytics 자습서를 사용하여 IoT 솔루션 빌드](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-build-an-iot-solution-using-stream-analytics)에서 “Stream Analytics 작업 만들기” 이전의 단계를 완료합니다. 
* Visual Studio 2015, Visual Studio 2013 업데이트 4 또는 Visual Studio 2012를 사용합니다. Enterprise(Ultimate/Premium), Professional 및 Community Edition이 지원됩니다. Express Edition은 지원되지 않습니다. Visual Studio 2017은 지원되지 않습니다. 
* .NET용 Azure SDK 버전 2.7.1 이상을 사용합니다. [웹 플랫폼 설치 관리자](http://www.microsoft.com/web/downloads/platform.aspx)를 사용하여 설치합니다.
* [Stream Analytics Tools for Visual Studio](http://aka.ms/asatoolsvs)를 설치합니다.

## <a name="create-a-stream-analytics-project"></a>Stream Analytics 프로젝트 만들기
1. Visual Studio에서 **파일** 메뉴를 클릭하고 **새 프로젝트**를 선택합니다. 

2. 왼쪽의 템플릿 목록에서 **Stream Analytics**를 선택한 후 **Azure Stream Analytics 응용 프로그램**을 클릭합니다.

3. 다른 프로젝트와 마찬가지로 프로젝트 **이름**, **위치** 및 **솔루션 이름**을 입력합니다.

    ![새 프로젝트 창](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-create-project-01.png)

    **솔루션 탐색기**에 **Toll** 프로젝트가 생성됩니다.

    ![솔루션 탐색기에 생성된 Toll 프로젝트](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-create-project-02.png)

## <a name="choose-the-correct-subscription"></a>올바른 구독 선택
1. Visual Studio에서 **보기** 메뉴를 클릭하고 **서버 탐색기**를 엽니다.

2. Azure 계정으로 로그인합니다. 

## <a name="define-the-input-sources"></a>입력 원본 정의
1.  **솔루션 탐색기**에서 **입력** 노드를 확장하고 이름을 **Input.json**에서 **EntryStream.json**으로 바꿉니다. **EntryStream.json**을 두 번 클릭합니다.
2.  이제 **입력 별칭**은 **EntryStream**입니다. 입력 별칭이 쿼리 스크립트에 사용됩니다. 
3.  **원본 형식**으로 **데이터 스트림**을 선택합니다.
4.  **원본**에서 **이벤트 허브**를 선택합니다.
5.  **서비스 버스 네임스페이스**에서 **TollData** 옵션을 선택합니다.
6.  **이벤트 허브 이름**에서 **entry**를 선택합니다.
7.  **이벤트 허브 정책 이름**에서 **RootManageSharedAccessKey**(기본값)를 선택합니다.
8.  **이벤트 직렬화 형식**으로 **Json**을 선택합니다. 
9.  **인코딩**으로 **UTF8**을 선택합니다. 설정은 다음 스크린샷과 유사합니다.

    ![입력 창](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-define-input-01.png)
 
10. 마법사를 마치려면 **저장**을 클릭합니다. 이제 다른 입력 원본을 추가하여 진출 스트림을 만들 수 있습니다. **입력** 노드를 마우스 오른쪽 단추로 클릭하고 **새 항목**을 선택합니다.

    ![새 항목](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-define-input-02.png)
 
11. 창에서 **Azure Stream Analytics 입력**을 선택하고 **이름**을 **ExitStream.json**으로 변경합니다. **추가**를 클릭합니다.

    ![새 항목 추가 창](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-define-input-03.png)
 
12. 프로젝트에서 **ExitStream.json**을 두 번 클릭하고 진입 스트림과 같은 단계를 진행합니다. 다음 스크린샷과 같이 **이벤트 허브 이름**에 대해 **exit**을 입력해야 합니다.

    ![ExitStream 창](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-define-input-04.png)

    이제 두 입력 스트림을 정의했습니다.

    ![진입 및 종료 입력 스트림](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-define-input-05.png)
 
    다음으로, 차량 등록 데이터가 있는 Blob 파일에 대해 참조 데이터 입력을 추가합니다.

13. 프로젝트에서 **입력** 노드를 마우스 오른쪽 단추로 클릭한 후 스트림 입력에 대해 수행한 것과 동일한 단계를 진행합니다. **입력 별칭**에 **Registration**을 입력하고 **원본 형식**에서 **참조 데이터**를 선택합니다.

    ![등록 창](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-define-input-06.png)

14. **저장소 계정**에서 **tolldata** 옵션을 선택합니다. **컨테이너**에서 **tolldata**를 선택하고 **경로 패턴**에 **registration.json**을 입력합니다. 이 파일 이름은 대/소문자를 구분하므로 소문자여야 합니다.
15. 마법사를 마치려면 **저장**을 클릭합니다.

이제 모든 입력이 정의되었습니다.

## <a name="define-the-output"></a>출력 정의
1.  **솔루션 탐색기**에서 **입력** 노드를 확장하고 **Output.json**을 두 번 클릭합니다.

2.  **출력 별칭**에 **output**을 입력합니다. 
3.  **싱크**에서 **SQL Database**를 선택합니다.
4.  **데이터베이스**에서 **TollDataDB**를 선택합니다.
5.  **사용자 이름**에 **tolladmin**을 입력합니다. 
6.  **암호**에 **123toll!**를 입력합니다.
7.  **테이블**에 **TollDataRefJoin**을 입력합니다.
8.  **Save**를 클릭합니다.

    ![출력 창](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-define-output-01.png)
 
## <a name="create-a-stream-analytics-query"></a>Stream Analytics 쿼리 만들기
이 자습서에서는 요금 데이터와 관련된 여러 가지 비즈니스 질문에 답변합니다. 관련 답변을 제공하기 위해 Stream Analytics에서 사용할 수 있는 Stream Analytics 쿼리도 생성합니다.
첫 번째 Stream Analytics 작업을 시작하기 전에 간단한 시나리오 및 쿼리 구문을 살펴보겠습니다.

### <a name="introduction-to-the-stream-analytics-query-language"></a>Stream Analytics 쿼리 언어 소개
요금 창구에 들어가는 차량 수를 계산해야 한다고 가정해봅시다. 이 예제는 연속 이벤트 스트림이므로 기간을 정의해야 합니다. 이 질문을 “3분 간격으로 요금 창구에 진입하는 차량은 몇 대입니까?”로 수정합니다. 데이터 수를 세는 이 방법을 일반적으로 연속 개수(Tumbling Count)라고 합니다.

이 질문에 대답하는 Stream Analytics 쿼리를 살펴보겠습니다.

        SELECT TollId, System.Timestamp AS WindowEnd, COUNT(*) AS Count 
        FROM EntryStream TIMESTAMP BY EntryTime 
        GROUP BY TUMBLINGWINDOW(minute, 3), TollId 

Azure Stream Analytics는 SQL과 유사한 쿼리를 사용하고 몇 가지 확장을 추가하여 쿼리의 시간 관련 측면을 지정합니다.

자세한 내용을 보려면 MSDN의 쿼리에 사용된 [시간 관리](https://msdn.microsoft.com/library/azure/mt582045.aspx) 및 [창 작업](https://msdn.microsoft.com/library/azure/dn835019.aspx) 구문을 참조하세요.

이제 첫 번째 Stream Analytics 쿼리를 작성했고 테스트할 시간입니다. 다음 경로의 TollApp 폴더에 있는 샘플 데이터 파일을 사용합니다.

..\TollApp\TollApp\Data

이 폴더에는 다음 파일이 포함되어 있습니다.
*   Entry.json
*   Exit.json
*   registration.json

## <a name="count-the-number-of-vehicles-entering-a-toll-booth"></a>요금 창구에 들어가는 차량 수 세기
프로젝트에서 **Script.asaql**을 두 번 클릭하여 **쿼리 편집기**에서 스크립트를 엽니다. 이전 섹션의 스크립트를 편집기에 복사하여 붙여넣습니다. 쿼리 편집기에서는 IntelliSense, 구문 색 지정 및 오류 마커를 지원합니다.

![쿼리 편집기](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-query-01.png)
 
### <a name="test-stream-analytics-queries-locally"></a>로컬에서 Stream Analytics 쿼리 테스트

1. 구문 오류가 있는지 확인하도록 쿼리를 컴파일하려면 프로젝트를 마우스 오른쪽 단추로 클릭하고 **빌드**를 선택합니다. 

2. 샘플 데이터에 대해 이 쿼리의 유효성을 검사하려면 로컬 샘플 데이터를 사용하면 됩니다. 입력을 마우스 오른쪽 단추로 클릭하고 **로컬 입력 추가**를 선택합니다.

    ![로컬 입력 추가](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-add-local-input-01.png)
 
3. 팝업 창에서 로컬 경로의 샘플 데이터를 선택합니다. **Save**를 클릭합니다.

    ![로컬 입력 추가 창](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-add-local-input-02.png)
 
    **local_EntryStream.json** 파일이 입력 폴더에 자동으로 추가됩니다.

    ![입력 폴더에 추가된 파일](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-add-local-input-03.png)
 
4. **쿼리 편집기**에서 **로컬로 실행**을 클릭합니다. 또는 F5 키를 눌러도 됩니다.

    ![로컬로 실행](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-local-run-01.png)

    ![로컬 실행 출력](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-local-run-02.png)

    아무 키나 누르면 Visual Studio의 **ASA 로컬 실행 결과** 창에서 출력을 확인할 수 있습니다. 

    ![ASA 로컬 실행 결과 창](./media/stream-analytics-tools-for-vs/local-testing-output.png)

5. **결과 폴더 열기**를 클릭하여 출력 파일을 CSV 및 JSON 형식 모두로 확인할 수 있습니다.

    ![결과 폴더 열기 출력](./media/stream-analytics-tools-for-vs/local-testing-files.png)
 

### <a name="sample-the-input-data"></a>입력 데이터 샘플링
입력 원본에서 로컬 파일로 입력 데이터를 샘플링할 수도 있습니다. 
1. 입력 구성 파일을 마우스 오른쪽 단추로 클릭하고 **샘플 데이터**를 선택합니다. 

   ![샘플 데이터](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-sample-data-01.png)

    지금은 이벤트 허브 또는 IoT Hub를 샘플링할 수 있습니다. 다른 입력 원본은 지원되지 않습니다.

2. 팝업 창에서 샘플 데이터를 저장하는 데 사용된 로컬 경로를 입력합니다. **샘플링**을 클릭합니다.

    ![샘플 데이터 창](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-sample-data-02.png)
 
    **출력** 창에서 진행률을 볼 수 있습니다. 

    ![출력 창](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-sample-data-03.png)
 
### <a name="submit-a-stream-analytics-query-to-azure"></a>Stream Analytics 쿼리를 Azure에 제출
1. **쿼리 편집기**의 스크립트 편집기에서 **Azure에 제출**을 클릭합니다.

    ![Azure에 제출](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-submit-job-01.png)
 
2. **새 Azure Stream Analytics 작업 만들기**를 선택합니다. **작업 이름**을 입력하고 올바른 **구독**을 선택합니다. **Submit**를 클릭합니다.

    ![작업 전송 창](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-submit-job-02.png)

 
### <a name="start-a-job"></a>작업 시작
이제 작업이 생성되었고 작업 보기가 자동으로 열립니다. 
1. 작업을 시작하려면 **녹색** 단추를 클릭합니다.

    ![작업 시작](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-start-job-01.png)
 
2. 기본 설정을 선택하고 **시작**을 클릭합니다.
 
    ![작업 시작 창](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-start-job-02.png)

    작업 **상태**가 **실행 중**으로 변경되고 **입력 이벤트** 및 **출력 이벤트**가 나타납니다.

    ![작업 요약에서 실행 중 상태](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-start-job-03.png)

## <a name="check-the-results-in-visual-studio"></a>Visual Studio에서 결과 확인
1. Visual Studio에서 **서버 탐색기**를 열고 **TollDataRefJoin** 테이블을 마우스 오른쪽 단추로 클릭합니다.
2. **테이블 데이터 표시**를 선택하면 작업의 출력을 볼 수 있습니다.
   
    ![서버 탐색기의 테이블 데이터 표시 선택 영역](media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-check-results.jpg)


### <a name="view-the-job-metrics"></a>작업 메트릭 보기
몇 가지 기본 작업 통계는 **Job Metrics(작업 메트릭)**에서 확인할 수 있습니다. 

![작업 메트릭 창](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-job-metrics-01.png)

 
## <a name="list-the-job-in-server-explorer"></a>서버 탐색기에 작업 나열
**서버 탐색기**에서 **Stream Analytics 작업**을 클릭하고 **새로 고침**을 클릭합니다. 작업이 **Stream Analytics 작업** 아래 나타납니다.

![서버 탐색기에 나열된 Stream Analytics 작업](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-list-jobs-01.png)


## <a name="open-the-job-view"></a>작업 보기 열기
작업 보기를 열려면 작업 노드를 확장하고 **작업 보기** 노드를 두 번 클릭합니다.

![작업 보기 노드](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-job-view-01.png)


## <a name="export-an-existing-job-to-a-project"></a>기존 작업을 프로젝트로 내보내기
두 가지 방법으로 기존 작업을 프로젝트로 내보낼 수 있습니다.

**서버 탐색기**에서 **Stream Analytics 작업** 노드의 작업 노드를 마우스 오른쪽 단추로 클릭하고 **새 Stream Analytics 프로젝트로 내보내기**를 선택합니다.

![새 Stream Analytics 프로젝트로 내보내기](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-export-job-01.png)

**솔루션 탐색기**에 프로젝트가 생성됩니다.

![솔루션 탐색기에 생성된 프로젝트](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-export-job-02.png)
 
작업 보기를 사용하고 **프로젝트 생성**을 클릭할 수 있습니다.

![프로젝트 생성](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-export-job-03.png)

## <a name="known-issues-and-limitations"></a>알려진 문제 및 제한 사항
 
- Power BI 출력 및 Azure Date Lake Store 출력에 대한 지원은 없습니다.
- JavaScript 사용자 정의 함수를 추가 또는 변경하기 위한 편집기 지원은 없습니다.

## <a name="next-steps"></a>다음 단계
* [Azure Stream Analytics 소개](stream-analytics-introduction.md)
* [Azure Stream Analytics 사용 시작](stream-analytics-real-time-fraud-detection.md)
* [Azure 스트림 분석 작업 규모 지정](stream-analytics-scale-jobs.md)
* [Azure 스트림 분석 쿼리 언어 참조](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Azure Stream Analytics 관리 REST API 참조](https://msdn.microsoft.com/library/azure/dn835031.aspx)

