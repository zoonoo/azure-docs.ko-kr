---
title: Synapse Studio를 사용 하 여 Apache Spark 응용 프로그램 모니터링
description: Synapse Studio를 사용 하 여 Apache Spark 응용 프로그램을 모니터링 합니다.
services: synapse-analytics
author: matt1883
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: monitoring
ms.date: 04/15/2020
ms.author: mahi
ms.reviewer: mahi
ms.openlocfilehash: 16b37258d922db59f520d4e30c45773f6d1108cf
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2020
ms.locfileid: "93341073"
---
# <a name="use-synapse-studio-to-monitor-your-apache-spark-applications"></a>Synapse Studio를 사용 하 여 Apache Spark 응용 프로그램 모니터링

Azure Synapse Analytics를 사용 하 여 작업 영역의 Apache Spark 풀에서 노트북, 작업 및 다른 종류의 응용 프로그램을 실행 하는 Apache Spark를 사용할 수 있습니다.

이 문서에서는 Apache Spark 응용 프로그램을 모니터링 하 여 최신 상태, 문제 및 진행 상황을 파악할 수 있도록 하는 방법을 설명 합니다.

이 자습서에서 다루는 작업은 다음과 같습니다.

* 실행 중인 Apache Spark 응용 프로그램 모니터링
* 완료 된 Apache Spark 응용 프로그램 보기
* 취소 된 Apache Spark 응용 프로그램 보기
* 응용 프로그램 Apache Spark 디버그 실패

## <a name="prerequisites"></a>필수 구성 요소

이 자습서를 시작하기 전에 다음 요구 사항이 충족되는지 확인합니다.

- Synapse Studio 작업 영역입니다. 자세한 내용은 [Synapse Studio 작업 영역 만들기](https://docs.microsoft.com/azure/machine-learning/how-to-manage-workspace#create-a-workspace)를 참조 하세요.

- Apache Spark 풀.

## <a name="view-apache-spark-applications"></a>Apache Spark 응용 프로그램 보기 
모든 Apache Spark 응용 프로그램은 **모니터**  ->  **Apache Spark 응용 프로그램** 에서 볼 수 있습니다.
   ![apache spark 응용 프로그램](./media/how-to-monitor-spark-applications/apache-spark-applications.png)

## <a name="view-completed-apache-spark-application"></a>완료 된 Apache Spark 응용 프로그램 보기

**모니터** 를 열고 **Apache Spark 응용 프로그램** 을 선택 합니다. 완료 된 Apache Spark 응용 프로그램에 대 한 세부 정보를 보려면 Apache Spark 응용 프로그램을 선택 하 고 세부 정보를 확인 합니다.

  ![완료 된 작업 선택](./media/how-to-monitor-spark-applications/select-completed-job.png)

1. 완료 된 **작업** , **상태** 및 **전체 기간** 을 확인 합니다.

2. 로그 쿼리를 새로 고칩니다.

3. **Spark 기록 서버** 를 클릭 하 여 Apache Spark 기록 서버 링크를 엽니다.

4. **요약** 정보를 확인 합니다.

5. **로그** 를 확인 합니다. 드롭다운 목록에서 다른 유형의 로그를 선택할 수 있으며, 로그 **다운로드** 를 클릭 하 여 로그 정보를 다운로드 하 고, **필터 오류 및 경고** 의 확인란을 선택 하 여 필요한 오류 및 경고를 필터링 할 수 있습니다.

6. 생성 된 작업 그래프에서 작업에 대 한 개요를 볼 수 있습니다. 기본적으로 그래프는 모든 작업을 표시 합니다. **작업 ID** 를 기준으로이 뷰를 필터링 할 수 있습니다.

7. 기본적으로 **진행률** 표시가 선택 되어 있습니다. **Progress** / **Read** / **Written** / **표시** 드롭다운 목록에서 진행률 읽기 기록 **기간** 을 선택 하 여 데이터 흐름을 확인할 수 있습니다.

8. 작업을 재생 하려면 **재생** 단추를 클릭 합니다. 언제 든 지 중지 **단추를** 클릭 하 여 중지할 수 있습니다.

9. 마우스 스크롤 또는 스크롤 막대를 사용 하 여 작업 그래프를 확대 및 축소 하 고 화면에 맞게 **확대/** 축소를 선택할 수도 있습니다.

10. 작업 그래프 노드에는 각 단계에 대 한 다음 정보가 표시 됩니다.

    * ID를 삭제합니다.

    * 이름 또는 설명

    * 총 작업 수

    * 읽은 데이터: 입력 크기 및 순서 섞기 읽기 크기의 합계입니다.

    * 데이터 쓰기: 출력 크기 및 순서 섞기 크기의 합계입니다.

    * 실행 시간: 첫 번째 시도 시작 시간과 마지막 시도 완료 시간 사이의 시간

    * 행 개수: 입력 레코드, 출력 레코드, 무작위 읽기 레코드 및 무작위 쓰기 레코드의 합계

    * 진행률

     ![완료 된 작업 보기](./media/how-to-monitor-spark-applications/view-completed-job.png)
    
11. 그래프에서 **세부 정보 보기** 를 클릭 하면 단계에 대 한 세부 정보가 표시 됩니다.

    ![단계에 대 한 세부 정보](./media/how-to-monitor-spark-applications/details-for-stage.png)


## <a name="monitor-running-apache-spark-application"></a>실행 중인 Apache Spark 응용 프로그램 모니터링

**모니터** 를 열고 **Apache Spark 응용 프로그램** 을 선택 합니다. 실행 되는 Apache Spark 응용 프로그램에 대 한 세부 정보를 보려면 Apache Spark 응용 프로그램 제출을 선택 하 고 세부 정보를 확인 합니다. Apache Spark 응용 프로그램이 계속 실행 중인 경우 진행률을 모니터링할 수 있습니다.

   ![실행 중인 작업 선택](./media/how-to-monitor-spark-applications/select-running-job.png)

1. 완료 된 **작업** , **상태** 및 **전체 기간** 을 확인 합니다.

2. Apache Spark 응용 프로그램을 **취소** 합니다.

3. **새로 고침** 로그 쿼리입니다.

4. Spark **UI** 단추를 클릭 하 여 spark 작업 페이지로 이동 합니다.

5. 그래프를 봅니다. 생성 된 작업 그래프에서 작업에 대 한 개요를 볼 수 있습니다. [완료 된 Apache Spark 응용 프로그램](#view-completed-apache-spark-application)의 6 단계, 7, 8, 9, 10 단계를 참조 하세요.

6. **요약** 정보를 확인 합니다.

7. **진단** 탭에서 진단을 선택 합니다.

8. 이 탭에서 **로그** 를 확인 합니다. 드롭다운 목록에서 다른 유형의 로그를 선택할 수 있으며, 로그 **다운로드** 를 클릭 하 여 로그 정보를 다운로드 하 고, **필터 오류 및 경고** 의 확인란을 선택 하 여 필요한 오류 및 경고를 필터링 할 수 있습니다.

    ![실행 중인 작업 보기](./media/how-to-monitor-spark-applications/view-running-job.png)

## <a name="view-canceled-apache-spark-application"></a>취소 된 Apache Spark 응용 프로그램 보기

**모니터** 를 열고 **Apache Spark 응용 프로그램** 을 선택 합니다. 취소 된 Apache Spark 응용 프로그램에 대 한 세부 정보를 보려면 Apache Spark 응용 프로그램을 선택 하 고 세부 정보를 확인 합니다.

 ![취소 한 작업 선택](./media/how-to-monitor-spark-applications/select-cancelled-job.png) 

1. 완료 된 **작업** , **상태** 및 **전체 기간** 을 확인 합니다.

2. 로그 쿼리를 새로 고칩니다.

3. **Spark 기록 서버** 를 클릭 하 여 Apache history 서버 링크를 엽니다.

4. 그래프를 봅니다. 생성 된 작업 그래프에서 작업에 대 한 개요를 볼 수 있습니다. [완료 된 Apache Spark 응용 프로그램](#view-completed-apache-spark-application)의 6 단계, 7, 8, 9, 10 단계를 참조 하세요.

5. **요약** 정보를 확인 합니다.

6. **로그** 를 확인 합니다. 드롭다운 목록에서 다른 유형의 로그를 선택할 수 있으며, 로그 **다운로드** 를 클릭 하 여 로그 정보를 다운로드 하 고, **필터 오류 및 경고** 의 확인란을 선택 하 여 필요한 오류 및 경고를 필터링 할 수 있습니다.

7. 그래프에서 **세부 정보 보기** 를 클릭 하면 단계에 대 한 세부 정보가 표시 됩니다.

   ![취소 한 작업 보기](./media/how-to-monitor-spark-applications/view-cancelled-job.png)

## <a name="debug-failed-apache-spark-application"></a>응용 프로그램 Apache Spark 디버그 실패

**모니터** 를 열고 **Apache Spark 응용 프로그램** 을 선택 합니다. 실패 한 Apache Spark 응용 프로그램에 대 한 세부 정보를 보려면 Apache Spark 응용 프로그램을 선택 하 고 세부 정보를 확인 합니다.

![실패 한 작업 선택](./media/how-to-monitor-spark-applications/select-failed-job.png)

1. 완료 된 **작업** , **상태** 및 **전체 기간** 을 확인 합니다.

2. 로그 쿼리를 새로 고칩니다.

3. **Spark 기록 서버** 를 클릭 하 여 Apache Spark 기록 서버 링크를 엽니다.

4. 그래프를 봅니다. 생성 된 작업 그래프에서 작업에 대 한 개요를 볼 수 있습니다. [완료 된 Apache Spark 응용 프로그램](#view-completed-apache-spark-application) 의 6 단계, 7, 8, 9, 10 단계를 참조 하세요.

5. **요약** 정보를 확인 합니다.

6. 오류 정보를 확인 합니다.

   ![실패 한 작업 정보](./media/how-to-monitor-spark-applications/failed-job-info.png)

## <a name="next-steps"></a>다음 단계

파이프라인 실행 모니터링에 대 한 자세한 내용은 [Synapse Studio를 사용 하 여 파이프라인 실행 모니터링](how-to-monitor-pipeline-runs.md) 문서를 참조 하세요.  
