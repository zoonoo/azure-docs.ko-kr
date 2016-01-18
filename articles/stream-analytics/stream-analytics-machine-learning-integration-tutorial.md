<properties 
	pageTitle="자습서: Azure 스트림 분석 및 Azure 기계 학습 통합 | Microsoft Azure" 
	description="스트림 분석 작업에 UDF 및 기계 학습을 활용하는 방법"
	keywords=""
	documentationCenter=""
	services="stream-analytics"
	authors="jeffstokes72" 
	manager="paulettm" 
	editor="cgronlun"
/>

<tags 
	ms.service="stream-analytics" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.workload="data-services" 
	ms.date="12/30/2015" 
	ms.author="jeffstok"
/>

# 자습서: 스트림 분석과 기계 학습 통합에 대한 소개 #

이 자습서는 간단한 스트림 분석 작업을 기계 학습과 신속하게 통합하는 방법을 알려주기 위해 설계되었습니다. Cortana Analytics 갤러리의 정서 분석 기계 학습 모델을 활용하여 스트리밍 텍스트 데이터를 분석하고 정서 점수를 실시간으로 확인하겠습니다. 스트리밍 Twitter 데이터의 실시간 정서 분석, 고객 지원 담당자와 고객 대화 기록 분석, 포럼/블로그/비디오에 대한 코멘트, 기타 여러 실시간 예측 점수 매기기 등의 시나리오를 이해하는 데 많은 도움이 되는 자습서입니다.
  
이 자습서에서는 아래의 그림 1과 같이 텍스트가 포함된 샘플 CSV 파일이 Azure Blob 저장소의 입력으로 제공됩니다. 이 작업에서 정서 분석 모델을 Blob 저장소의 샘플 텍스트에 UDF(사용자 정의 함수)으로 적용할 것입니다. 최종 결과는 같은 Azure Blob 저장소에 또 다른 CSV 파일로 배치됩니다. 아래의 그림 2는 이 구성의 다이어그램을 나타낸 것입니다. 이 Blob 저장소 입력을 Azure 이벤트 허브 입력의 스트리밍 Twitter 데이터로 바꾸면 시나리오의 현실성을 더욱 높일 수 있습니다. 또한 종합 정서의 [Power BI](https://powerbi.microsoft.com/) 실시간 시각화를 구축할 수도 있습니다. 이 문서의 이후 버전에는 이러한 확장 기능이 포함될 것입니다.

그림 1:

![스트림 분석 기계 학습 자습서 그림 1](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-figure-1.png)

그림 2:

![스트림 분석 기계 학습 자습서 그림 2](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-figure-2.png)

## 필수 조건

이 문서에 대한 필수 조건은 다음과 같습니다.

1.	활성 Azure 구독
2.	데이터가 포함된 CSV 파일. [GitHub](https://github.com/jeffstokes72/azure-stream-analytics-repository/blob/master/sampleinputs.csv)에 제공된 그림 2의 CSV 파일을 다운로드하거나 직접 만들 수 있습니다. 이 자습서는 제공된 CSV 파일을 사용한다는 가정 하에 작성되었습니다.

상위 수준에서 다음 단계를 수행할 것입니다.

1.	Blob 저장소에 CSV 입력 파일 업로드
2.	Cortana Analytics 갤러리의 정서 분석 모델을 기계 학습 작업 영역에 추가
3.	이 모델을 Azure 기계 학습 작업 영역 내부의 웹 서비스로 배포
4.	텍스트 입력의 정서를 결정하는 함수로 이 웹 서비스를 호출하는 스트림 분석 작업 만들기
5.	스트림 분석 작업을 시작하고 출력 관찰 


## Blob 저장소에 CSV 입력 파일 업로드

이 단계에서는 소개 부분에서 지정한 파일을 포함하여 모든 CSV 파일을 사용할 수 있습니다. 사용자 지정 코드 외에도 [Azure 저장소 탐색기](http://storageexplorer.com/) 또는 Visual Studio를 사용하여 파일을 업로드할 수 있습니다. 이 자습서에서는 Visual Studio에 대한 예제가 제공됩니다.

1.	Azure를 확장하고 **저장소**를 마우스 오른쪽 단추로 클릭합니다. **외부 저장소 연결**을 선택하고 **계정 이름** 및 **계정 키**를 입력합니다.  

    ![스트림 분석 기계 학습 자습서 서버 탐색기](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-server-explorer.png)

2.	방금 연결한 저장소를 확장하고 **Blob 컨테이너 만들기**를 선택한 다음 논리적 이름을 입력합니다. 컨테이너를 만들었으면 컨테이너를 두 번 클릭하여 콘텐츠를 살펴봅니다. 이 시점에는 컨테이너가 비어 있을 것입니다.

    ![스트림 분석 기계 학습 자습서 Blob 만들기](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-create-blob.png)

3.	**Blob 업로드** 아이콘을 클릭하고 **로컬 디스크의 파일**을 선택하여 CSV 파일을 업로드합니다.

## Cortana Analytics 갤러리의 정서 분석 모델 추가

1.	Cortana Analytics 갤러리에서 [예측 정서 분석 모델](https://gallery.cortanaanalytics.com/Experiment/Predictive-Mini-Twitter-sentiment-analysis-Experiment-1)을 다운로드합니다.  
2.	Studio에서 **열기**를 클릭합니다.  

    ![스트림 분석 기계 학습 자습서 기계 학습 스튜디오 열기](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-open-ml-studio.png)

3.	로그인하면 작업 영역으로 이동됩니다. 현재 위치에 가장 적합한 위치를 선택합니다.
4.	이제 Studio 하단에 있는 **실행**을 클릭합니다.  
5.	성공적으로 실행되면 **웹 서비스 배포**를 클릭합니다.
6.	이제 정서 분석 모델을 사용할 준비가 완료되었습니다. 유효성을 검사하기 위해 **테스트** 단추를 클릭하고 "I love Microsoft"와 같이 텍스트 입력을 제공합니다. 그러면 아래와 비슷한 테스트 결과가 반환될 것입니다.

`'Predictive Mini Twitter sentiment analysis Experiment' test returned ["4","0.715057671070099"]...`

![스트림 분석 기계 학습 자습서 분석 데이터](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-analysis-data.png)

**Excel 2010 또는 이전** 통합 문서 링크를 클릭하여 API 키 및 URL을 가져옵니다. 나중에 스트림 분석 작업을 설정할 때 필요합니다. (이 단계는 또 다른 Azure 계정의 작업 영역에서 기계 학습 모델을 활용하려는 경우에만 필요합니다. 이 자습서에서는 이 시나리오를 해결하는 것으로 가정합니다.)

![스트림 분석 기계 학습 자습서 분석 실험](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-analysis-experiement.png)

아래와 같이 다운로드한 excel의 웹 서비스 URL과 선택키를 기록해 둡니다.

![스트림 분석 기계 학습 자습서 빠른 보기](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-quick-glance.png)

## 기계 학습 모델을 사용하는 스트림 분석 작업 만들기

1.	[Azure 관리 포털](https://manage.windowsazure.com)로 이동합니다.  
2.	**새로 만들기**, **데이터 서비스**, **스트림 분석**, **빠른 생성**을 차례대로 클릭합니다. **작업 이름**, 작업에 맞는 **지역**을 입력하고 **지역별 모니터링 저장소 계정**을 선택합니다.    
3.	작업이 생성되면 **입력** 탭으로 이동하여 **입력 추가**를 클릭합니다.  

    ![스트림 분석 기계 학습 자습서 데이터 및 기계 학습 입력](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-add-input-screen.png)

4.	**입력 추가** 마법사 창의 첫 번째 페이지에서 **데이터 스트림**을 선택하고 다음을 클릭합니다. 두 번째 페이지에서 입력으로 **Blob 저장소**를 선택하고 **다음**을 클릭합니다.
5.	마법사의 **Blob 저장소 설정** 페이지에서, 앞에서 데이터를 업로드할 때 정의한 저장소 계정 Blob 컨테이너 이름을 입력합니다. **다음**을 클릭합니다. **이벤트 serialization 형식**으로 **CSV**를 선택합니다. **Serialization 설정**의 나머지 부분에는 기본값을 그대로 적용합니다. **확인**을 클릭합니다.  
6.	**출력** 탭으로 이동하여 **출력 추가**를 클릭합니다.  

    ![스트림 분석 기계 학습 자습서 출력 추가](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-add-output-screen.png)

7.	**Blob 저장소**를 선택한 다음 컨테이너를 제외하고 동일한 매개 변수를 입력합니다. **입력**은 **CSV** 파일이 업로드된 "test"라는 이름의 컨테이너에서 읽도록 구성되었습니다. **출력**에 대해서는 "testoutput"을 입력합니다. 컨테이너 이름이 달라야 합니다. 그리고 이 컨테이너가 있는지 확인해야 합니다.
8.	**다음**을 클릭하여 출력의 **Serialization 설정**을 구성합니다. 입력과 마찬가지로 **CSV**를 선택하고 **확인** 단추를 클릭합니다.
9.	**함수** 탭으로 이동하여 **기계 학습 함수 추가**를 클릭합니다.  

    ![스트림 분석 기계 학습 자습서 기계 학습 함수 추가](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-add-ml-function.png)

10.	**기계 학습 웹 서비스 설정** 페이지에서 기계 학습 작업 영역, 웹 서비스 및 기본 끝점을 찾습니다. 이 자습서에서는 여러분이 URL을 알고 있고 키를 갖고 있는 작업 영역에 익숙해지도록 수동으로 설정을 적용하겠습니다. 끝점 **URL** 및 **API 키**를 입력합니다. 그런 후 **OK**를 클릭합니다.

    ![스트림 분석 기계 학습 자습서 기계 학습 웹 서비스](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-ml-web-service.png)

11.	**쿼리** 탭으로 이동하여 아래와 같이 쿼리를 수정합니다.

```
	WITH subquery AS (  
		SELECT text, sentiment(text) as result from input  
	)  
	  
	Select text, result.[Score]  
	Into output  
	From subquery  
```

12. **저장**을 클릭하여 쿼리를 저장합니다.    

## 스트림 분석 작업을 시작하고 출력 관찰

1.	작업 아래쪽에서 **시작**을 클릭합니다. 
2.	**쿼리 시작 대화 상자**에서 **사용자 지정 시간**을 선택한 다음 Blob 저장소에 CSV가 업로드되기 이전 시간을 선택합니다. **확인**을 클릭합니다.  

    ![스트림 분석 기계 학습 자습서 사용자 지정 시간](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-custom-time.png)

3.	CSV 파일을 업로드할 때 사용한 도구를 사용하여 Blob 저장소로 이동합니다. 이 자습서에서는 Visual Studio를 사용했습니다.
4.	작업 시작 후 몇 분이 지나면 출력 컨테이너 만들어지고 그 컨테이너에 CSV 파일이 업로드됩니다.  
5.	파일을 두 번 클릭하면 기본 CSV 편집기가 열리고 아래와 같은 내용이 표시됩니다.  

    ![스트림 분석 기계 학습 자습서 csv 뷰](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-csv-view.png)

## 결론

이 자습서에서는 스트리밍 텍스트 데이터를 읽어서 실시간 정서 분석을 적용하는 스트림 분석 작업을 만들었습니다. 여러분은 복잡한 정서 분석 모델 구축에 대한 걱정 없이 이 모든 작업을 수행할 수 있었습니다. 이것이 바로 Cortana Analytics Suite의 장점 중 하나입니다.

또한 Azure 기계 학습 함수 관련 메트릭을 관찰할 수 있습니다. **모니터** 탭을 클릭하세요. 세 가지 함수 관련 메트릭이 있습니다.
  
- FUNCTION REQUESTS는 기계 학습 웹 서비스로 전송되는 요청 수를 나타냅니다.  
- FUNCTION EVENTS는 요청의 이벤트 수를 나타냅니다. 기본적으로 ML 웹 서비스로 전송되는 각 요청에는 최대 1000개의 이벤트가 포함됩니다.  
- FAILED FUNCTION REQUESTS는 기계 학습 웹 서비스로 전송되는 요청 중 실패한 요청 수를 나타냅니다.  

    ![스트림 분석 기계 학습 자습서 ml 모니터링 뷰](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-ml-monitor-view.png)

<!---HONumber=AcomDC_0107_2016-->