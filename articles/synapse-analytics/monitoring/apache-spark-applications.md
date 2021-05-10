---
title: Synapse Studio를 사용하여 Apache Spark 애플리케이션 모니터링
description: Synapse Studio를 사용하여 Apache Spark 애플리케이션을 모니터링합니다.
services: synapse-analytics
author: matt1883
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: monitoring
ms.date: 04/15/2020
ms.author: mahi
ms.reviewer: mahi
ms.openlocfilehash: ea08c82ed5772be7e3a6094f5477c4450975c1fa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104775886"
---
# <a name="use-synapse-studio-to-monitor-your-apache-spark-applications"></a>Synapse Studio를 사용하여 Apache Spark 애플리케이션 모니터링

Azure Synapse Analytics에서 Apache Spark를 사용하여 작업 영역의 Apache Spark 풀에서 Notebook, 작업 및 기타 종류의 애플리케이션을 실행할 수 있습니다.

이 문서에서는 Apache Spark 애플리케이션을 모니터링하여 최신 상태, 문제 및 진행 상황을 파악할 수 있도록 하는 방법을 설명합니다.

이 자습서에서 다루는 작업은 다음과 같습니다.

* 실행 중인 Apache Spark 애플리케이션 모니터링
* 완료된 Apache Spark 애플리케이션 보기
* 취소된 Apache Spark 애플리케이션 보기
* 실패한 Apache Spark 애플리케이션 디버그

## <a name="prerequisites"></a>필수 구성 요소

이 자습서를 시작하기 전에 다음 요구 사항이 충족되는지 확인합니다.

- Synapse Studio 작업 영역. 지침에 대해서는 [Log Analytics 작업 영역 만들기](../../machine-learning/how-to-manage-workspace.md#create-a-workspace)를 참조하세요.

- Apache Spark 풀.

## <a name="view-apache-spark-applications"></a>Apache Spark 애플리케이션 보기 
모든 Apache Spark 애플리케이션은 **모니터** -> **Apache Spark 애플리케이션** 에서 볼 수 있습니다.
   ![Apache Spark 애플리케이션](./media/how-to-monitor-spark-applications/apache-spark-applications.png)

## <a name="view-completed-apache-spark-application"></a>완료된 Apache Spark 애플리케이션 보기

**모니터** 를 연 다음, **Apache Spark 애플리케이션** 을 선택합니다. 완료된 Apache Spark 애플리케이션에 대한 세부 정보를 보려면 Apache Spark 애플리케이션을 선택하고 세부 정보를 확인합니다.

  ![완료된 작업 선택](./media/how-to-monitor-spark-applications/select-completed-job.png)

1. **완료된 작업**, **상태** 및 **전체 기간** 을 확인합니다.

2. 로그 쿼리를 새로 고칩니다.

3. **Spark 기록 서버** 를 클릭하여 Apache Spark 기록 서버 링크를 엽니다.

4. **요약** 정보를 확인합니다.

5. **로그** 를 확인합니다. 드롭다운 목록에서 다른 유형의 로그를 선택할 수 있으며, **로그 다운로드** 를 클릭하여 로그 정보를 다운로드하고, **필터 오류 및 경고** 의 확인란을 선택하여 필요한 오류 및 경고를 필터링할 수 있습니다.

6. 생성된 작업 그래프에서 작업에 대한 개요를 볼 수 있습니다. 기본적으로 이 그래프는 모든 작업을 표시합니다. **작업 ID** 를 기준으로 이 뷰를 필터링할 수 있습니다.

7. 기본적으로 **진행률** 표시가 선택되어 있습니다. **표시** 드롭다운 목록에서 **진행률**/**읽기**/**쓰기**/**기간** 을 선택하여 데이터 흐름을 확인할 수 있습니다.

8. 작업을 재생하려면 **재생** 단추를 클릭합니다. 언제든지 **중지** 단추를 클릭하여 중지할 수 있습니다.

9. 마우스 스크롤 또는 스크롤 막대를 사용하여 작업 그래프를 확대 및 축소하고 **크기에 맞게** 를 선택하여 화면에 맞출 수도 있습니다.

10. 작업 그래프 노드에는 각 스테이지에 대한 다음 정보가 표시됩니다.

    * ID를 삭제합니다.

    * 이름 또는 설명

    * 총 작업 수

    * 읽은 데이터: 입력 크기 및 순서 섞기 읽기 크기의 합계입니다.

    * 데이터 쓰기: 출력 크기 및 순서 섞기 쓰기 크기의 합계입니다.

    * 실행 시간: 첫 번째 시도 시작 시간과 마지막 시도 완료 시간 사이의 시간

    * 행 개수: 입력 레코드, 출력 레코드, 무작위 읽기 레코드 및 무작위 쓰기 레코드의 합계

    * 진행률

     ![완료된 작업 보기](./media/how-to-monitor-spark-applications/view-completed-job.png)
    
11. 그래프에서 **세부 정보 보기** 를 클릭하면 스테이지에 대한 세부 정보가 표시됩니다.

    ![스테이지에 대한 세부 정보](./media/how-to-monitor-spark-applications/details-for-stage.png)


## <a name="monitor-running-apache-spark-application"></a>실행 중인 Apache Spark 애플리케이션 모니터링

**모니터** 를 연 다음, **Apache Spark 애플리케이션** 을 선택합니다. 실행 중인 Apache Spark 애플리케이션에 대한 세부 정보를 보려면 제출하는 Apache Spark 애플리케이션을 선택하고 세부 정보를 확인합니다. Apache Spark 애플리케이션이 여전히 실행 중인 경우 진행률을 모니터링할 수 있습니다.

   ![실행 중인 작업 선택](./media/how-to-monitor-spark-applications/select-running-job.png)

1. **완료된 작업**, **상태** 및 **전체 기간** 을 확인합니다.

2. Apache Spark 애플리케이션을 **취소** 합니다.

3. 로그 쿼리를 **새로 고칩니다**.

4. **Spark UI** 단추를 클릭하여 Spark 작업 페이지로 이동합니다.

5. 그래프를 봅니다. 생성된 작업 그래프에서 작업에 대한 개요를 볼 수 있습니다. [완료된 Apache Spark 애플리케이션](#view-completed-apache-spark-application)의 6, 7, 8, 9, 10단계를 참조하세요.

6. **요약** 정보를 확인합니다.

7. **진단** 탭에서 진단을 선택합니다.

8. 이 탭에서 **로그** 를 선택합니다. 드롭다운 목록에서 다른 유형의 로그를 선택할 수 있으며, **로그 다운로드** 를 클릭하여 로그 정보를 다운로드하고, **필터 오류 및 경고** 의 확인란을 선택하여 필요한 오류 및 경고를 필터링할 수 있습니다.

    ![실행 중인 작업 보기](./media/how-to-monitor-spark-applications/view-running-job.png)

## <a name="view-canceled-apache-spark-application"></a>취소된 Apache Spark 애플리케이션 보기

**모니터** 를 연 다음, **Apache Spark 애플리케이션** 을 선택합니다. 취소된 Apache Spark 애플리케이션에 대한 세부 정보를 보려면 Apache Spark 애플리케이션을 선택하고 세부 정보를 확인합니다.

 ![취소된 작업 선택](./media/how-to-monitor-spark-applications/select-cancelled-job.png) 

1. **완료된 작업**, **상태** 및 **전체 기간** 을 확인합니다.

2. 로그 쿼리를 새로 고칩니다.

3. **Spark 기록 서버** 를 클릭하여 Apache 기록 서버 링크를 엽니다.

4. 그래프를 봅니다. 생성된 작업 그래프에서 작업에 대한 개요를 볼 수 있습니다. [완료된 Apache Spark 애플리케이션](#view-completed-apache-spark-application)의 6, 7, 8, 9, 10단계를 참조하세요.

5. **요약** 정보를 확인합니다.

6. **로그** 를 확인합니다. 드롭다운 목록에서 다른 유형의 로그를 선택할 수 있으며, **로그 다운로드** 를 클릭하여 로그 정보를 다운로드하고, **필터 오류 및 경고** 의 확인란을 선택하여 필요한 오류 및 경고를 필터링할 수 있습니다.

7. 그래프에서 **세부 정보 보기** 를 클릭하면 스테이지에 대한 세부 정보가 표시됩니다.

   ![취소한 작업 보기](./media/how-to-monitor-spark-applications/view-cancelled-job.png)

## <a name="debug-failed-apache-spark-application"></a>실패한 Apache Spark 애플리케이션 디버그

**모니터** 를 연 다음, **Apache Spark 애플리케이션** 을 선택합니다. 실패한 Apache Spark 애플리케이션에 대한 세부 정보를 보려면 Apache Spark 애플리케이션을 선택하고 세부 정보를 확인합니다.

![실패한 작업 선택](./media/how-to-monitor-spark-applications/select-failed-job.png)

1. **완료된 작업**, **상태** 및 **전체 기간** 을 확인합니다.

2. 로그 쿼리를 새로 고칩니다.

3. **Spark 기록 서버** 를 클릭하여 Apache Spark 기록 서버 링크를 엽니다.

4. 그래프를 봅니다. 생성된 작업 그래프에서 작업에 대한 개요를 볼 수 있습니다. [완료된 Apache Spark 애플리케이션](#view-completed-apache-spark-application)의 6, 7, 8, 9, 10단계를 참조하세요.

5. **요약** 정보를 확인합니다.

6. 오류 정보를 확인합니다.

   ![실패한 작업 정보](./media/how-to-monitor-spark-applications/failed-job-info.png)

## <a name="compare-apache-spark-applications"></a>Apache Spark 애플리케이션 비교

애플리케이션을 비교하는 방법에는 두 가지가 있습니다. **애플리케이션 비교** 를 선택하거나, **Notebook에서 비교** 단추를 클릭하여 전자 필기장에서 볼 수 있습니다.

### <a name="compare-by-choose-an-application"></a>애플리케이션을 선택하여 비교

**애플리케이션 비교** 단추를 클릭하고 애플리케이션을 선택하여 성능을 비교할 때 두 애플리케이션 간의 차이를 한눈에 확인할 수 있습니다.

![애플리케이션 비교](./media/how-to-monitor-spark-applications/compare-applications.png)

![애플리케이션 비교 세부 정보](./media/how-to-monitor-spark-applications/details-compare-applications.png)

1. 마우스를 애플리케이션 위로 가져하면 **애플리케이션 비교** 아이콘이 표시됩니다.

2. **애플리케이션 비교** 아이콘을 클릭하면 애플리케이션 비교 페이지가 팝업됩니다.

3. **애플리케이션 선택** 단추를 클릭하여 **비교 애플리케이션 선택** 페이지를 엽니다.

4. 비교 애플리케이션을 선택할 때 애플리케이션 URL을 입력하거나 되풀이 목록에서 선택해야 합니다. 그런 후 **확인** 단추를 클릭합니다. 

   ![비교 애플리케이션 선택](./media/how-to-monitor-spark-applications/choose-comparison-application.png)

5. 비교 결과는 애플리케이션 비교 페이지에 표시됩니다.

   ![비교 결과](./media/how-to-monitor-spark-applications/comparison-result.png)

### <a name="compare-by-compare-in-notebook"></a>Notebook에서 비교하여 비교

**애플리케이션 비교** 페이지에서 **Notebook에서 비교** 단추를 클릭하여 Notebook을 엽니다. .ipsb 파일의 기본 이름은 **Recurrent Application Analytics** 입니다.

![Notebook에서 비교](./media/how-to-monitor-spark-applications/compare-in-notebook.png)

Notebook: Recurrent Application Analytics 파일에서 Spark 풀 및 언어를 설정한 후 바로 실행할 수 있습니다.

![recurrent application analytics](./media/how-to-monitor-spark-applications/recurrent-application-analytics.png)

## <a name="next-steps"></a>다음 단계

파이프라인 실행을 모니터링하는 방법에 대한 자세한 내용은 [Synapse Studio에서 파이프라인 실행 모니터링](how-to-monitor-pipeline-runs.md) 문서를 참조하세요.
