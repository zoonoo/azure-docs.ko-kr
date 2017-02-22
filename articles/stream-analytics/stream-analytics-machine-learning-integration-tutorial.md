---
title: "Azure Stream Analytics 및 Machine Learning 통합 | Microsoft Azure"
description: "Stream Analytics 작업에서 사용자 정의 함수 및 Machine Learning을 사용하는 방법"
keywords: 
documentationcenter: 
services: stream-analytics
author: jeffstokes72
manager: jhubbard
editor: cgronlun
ms.assetid: cfced01f-ccaa-4bc6-81e2-c03d1470a7a2
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 02/14/2017
ms.author: jeffstok
translationtype: Human Translation
ms.sourcegitcommit: d1ffb9aba0eb1e17d1efd913f536f6f3997fccbb
ms.openlocfilehash: 7b635b1810536f5b3eb1371d687e9c355e604e41

---

# <a name="sentiment-analysis-by-using-azure-stream-analytics-and-azure-machine-learning"></a>Azure Stream Analytics 및 Azure Machine Learning을 사용한 정서 분석
이 문서는 간단한 Azure Stream Analytics 작업을 Azure Machine Learning과 신속하게 통합하는 방법을 알려주기 위해 설계되었습니다. 여기서는 Cortana Intelligence 갤러리의 정서 분석 Machine Learning 모델을 사용하여 스트리밍 텍스트 데이터를 분석하고 정서 점수를 실시간으로 확인할 것입니다. 이 문서의 정보는 스트리밍 Twitter 데이터의 실시간 정서 분석, 고객 지원 담당자와 고객 대화 기록 분석, 포럼, 블로그, 비디오에 대한 코멘트, 기타 여러 실시간 예측 점수 매기기 등의 시나리오를 이해하는 데 많은 도움이 될 수 있습니다.

이 문서는 다음 이미지에 표시된 Azure Blob 저장소에 대한 입력으로 텍스트를 포함하는 샘플 CSV 파일을 제공합니다. 이 작업에서 정서 분석 모델을 Blob 저장소의 샘플 텍스트에 UDF(사용자 정의 함수)으로 적용할 것입니다. 최종 결과는 같은 Blob 저장소에 또 다른 CSV 파일로 배치됩니다. 

![Stream Analytics Machine Learning](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-figure-2.png)  

다음 이미지는 이러한 구성을 보여 줍니다. 좀 더 현실적인 시나리오에서는 Blob 저장소를 Azure 이벤트 허브 입력의 스트리밍 Twitter 데이터로 바꿀 수 있습니다. 또한 종합 정서의 [Microsoft Power BI](https://powerbi.microsoft.com/) 실시간 시각화를 빌드할 수도 있습니다.    

![Stream Analytics Machine Learning](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-figure-1.png)  

## <a name="prerequisites"></a>필수 조건
이 문서에서 설명하는 작업을 완료하기 위한 필수 구성 요소는 다음과 같습니다.

* 활성 Azure 구독.
* 데이터가 포함된 CSV 파일. [GitHub](https://github.com/Azure/azure-stream-analytics/blob/master/Sample Data/sampleinput.csv)에서 그림 1에 표시된 파일을 다운로드하거나 파일을 직접 만들 수 있습니다. 이 문서에서는 GitHub에서 다운로드할 수 있게 제공된 파일을 사용한다고 가정합니다.

좀 더 높은 수준에서 이 문서에 설명된 작업을 완료하려면 다음을 수행합니다.

1. CSV 입력 파일을 Azure Blob 저장소에 업로드합니다.
2. Cortana Intelligence 갤러리의 정서 분석 모델을 Azure Machine Learning 작업 영역에 추가합니다.
3. 이 모델을 Machine Learning 작업 영역 내부의 웹 서비스로 배포합니다.
4. 텍스트 입력의 정서를 결정하는 함수로 이 웹 서비스를 호출하는 Stream Analytics 작업을 만듭니다.
5. Stream Analytics 작업을 시작하고 출력을 관찰합니다.

## <a name="create-a-storage-blob-and-upload-the-csv-input-file"></a>저장소 Blob 만들기 및 CSV 입력 파일 업로드
이 단계의 경우 GitHub에서 다운로드할 수 있도록 이미 지정된 파일을 비롯하여 어떤 CSV 파일도 사용할 수 있습니다. csv 파일 업로드는 저장소 Blob 만들기에 포함된 옵션이므로 간단합니다.

이 자습서에서는 **새로 만들기**를 클릭한 다음 '저장소 계정'을 검색하고 저장소 계정에 대한 결과 아이콘을 선택하고 계정 만들기에 대한 세부 정보를 제공하여 새 저장소 계정을 만듭니다. **이름**(이 예에서 azuresamldemosa)을 제공하고 기존 **리소스 그룹**을 만들거나 사용하고 **위치**(위치의 경우 이 데모에서 만들어진 모든 리소스는 가능한 경우 동일한 위치를 사용)를 지정합니다.

![저장소 계정 만들기](./media/stream-analytics-machine-learning-integration-tutorial/create-sa.png)

완료되면 Blob 서비스를 클릭하고 Blob 컨테이너를 만들 수 있습니다.

![Blob 컨테이너 만들기](./media/stream-analytics-machine-learning-integration-tutorial/create-sa2.png)

그런 다음 컨테이너에 **이름**(이 예에서 azuresamldemoblob)을 제공하고 **액세스 형식**이 'blob'으로 설정되었는지 확인합니다.

![Blob 액세스 유형 만들기](./media/stream-analytics-machine-learning-integration-tutorial/create-sa3.png)

이제 Blob을 데이터로 채울 수 있습니다. **파일**을 선택한 다음 GitHub에서 다운로드한 로컬 드라이브에서 파일을 선택합니다. 블록 Blob 및 4MB의 크기를 선택했습니다. 이는 이 데모에 적합해야 합니다. 그런 다음 **업로드**를 선택하고 포털에서 텍스트 예제로 Blob을 만듭니다.

![Blob 업로드 파일 만들기](./media/stream-analytics-machine-learning-integration-tutorial/create-sa4.png)

이제 Blob에 예제 데이터가 있으므로 Cortana Intelligence Gallery에서 감정 분석 모델을 활성화할 시간입니다.

## <a name="add-the-sentiment-analytics-model-from-the-cortana-intelligence-gallery"></a>Cortana Intelligence Gallery의 정서 분석 모델 추가
1. Cortana Intelligence Gallery에서 [예측 정서 분석 모델](https://gallery.cortanaintelligence.com/Experiment/Predictive-Mini-Twitter-sentiment-analysis-Experiment-1) 을 다운로드합니다.  
2. Machine Learning Studio에서 **Studio에서 열기**를 선택합니다.  
   
   ![Stream Analytics Machine Learning, Machine Learning 스튜디오 열기](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-open-ml-studio.png)  

3. 로그인하여 작업 영역으로 이동합니다. 현재 위치에 가장 적합한 위치를 선택합니다.
4. 페이지 아래쪽에서 **실행** 을 클릭합니다.  
5. 프로세스가 성공적으로 실행되면 **웹 서비스 배포**를 선택합니다.
6. 정서 분석 모델을 사용할 준비가 완료되었습니다. 유효성을 검사하려면 **테스트** 버튼을 선택하고 "I love Microsoft"와 같은 텍스트 입력을 제공합니다. 그러면 다음과 같은 결과가 반환됩니다.

`'Predictive Mini Twitter sentiment analysis Experiment' test returned ["4","0.715057671070099"]...`  

![Stream Analytics Machine Learning, 분석 데이터](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-analysis-data.png)  

**앱** 열에서 **Excel 2010 또는 이전 통합 문서** 링크를 선택하여 API 키 및 URL을 가져옵니다. 나중에 Stream Analytics 작업을 설정할 때 필요합니다. (이 단계는 또 다른 Azure 계정 작업 영역에서 Machine Learning 모델을 사용하려는 경우에만 필요합니다. 이 문서에서는 해당 시나리오를 해결하는 것으로 가정합니다.)  

아래와 같이 다운로드한 Excel 파일의 웹 서비스 URL과 선택키를 기록해 둡니다.  

![Stream Analytics Machine Learning, 간략 상태](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-quick-glance.png)  

## <a name="create-a-stream-analytics-job-that-uses-the-machine-learning-model"></a>Machine Learning 모델을 사용하는 Stream Analytics 작업 만들기
1. [Azure 포털](https://portal.azure.com)로 이동합니다.  
2. **+새로 만들기** > **인텔리전스 + 분석** > **Stream Analytics**를 클릭합니다. **작업 이름**에 작업에 대한 이름을 입력하고 기존 리소스 그룹을 지정하거나 필요에 따라 새로 만들고 **위치** 필드에 작업에 대한 적절한 위치를 입력합니다.    
   
   ![작업 만들기](./media/stream-analytics-machine-learning-integration-tutorial/create-job-1.png)
   
3. 작업이 생성되면 **입력** 탭에서 **입력 추가**를 선택합니다.  
   
   ![Stream Analytics Machine Learning, Machine Learning 입력 추가](./media/stream-analytics-machine-learning-integration-tutorial/create-job-add-input.png)  

4. **추가**를 선택한 다음 **입력 별칭**을 지정하고 입력으로 **데이터 스트림**, **Blob Storage**를 선택한 후 **다음**을 선택합니다.  
5. 마법사의 **Blob 저장소 설정** 페이지에서, 앞에서 데이터를 업로드할 때 정의한 저장소 계정 Blob 컨테이너 이름을 입력합니다. **다음**을 클릭합니다. **이벤트 직렬화 형식**으로 **CSV**를 선택합니다. **직렬화 설정** 페이지의 나머지 부분에서는 기본값을 그대로 적용합니다. **확인**을 클릭합니다.  
   
   ![입력 Blob 컨테이너 추가](./media/stream-analytics-machine-learning-integration-tutorial/create-job-add-input-blob.png)

6. **출력** 탭에서 **출력 추가**를 선택합니다.  
   
   ![Stream Analytics Machine Learning, 출력 추가](./media/stream-analytics-machine-learning-integration-tutorial/create-output.png)  

7. **Blob 저장소**를 클릭한 후 컨테이너를 제외하고 동일한 매개 변수를 입력합니다. **입력** 값은 **CSV** 파일이 업로드된 "test"라는 이름의 컨테이너에서 읽도록 구성되었습니다. **출력**으로 "testoutput"을 입력합니다.
8. 출력의 **Serialization 설정**이 **CSV**로 설정되어 있는지 확인한 다음 **확인** 단추를 선택합니다.
   
   ![Stream Analytics Machine Learning, 출력 추가](./media/stream-analytics-machine-learning-integration-tutorial/create-output2.png) 

9. **함수** 탭에서 **Machine Learning 함수 추가**를 선택합니다.  
   
   ![Stream Analytics Machine Learning, Machine Learning 함수 추가](./media/stream-analytics-machine-learning-integration-tutorial/add-function.png)  

10. **Machine Learning 웹 서비스 설정** 페이지에서 Machine Learning 작업 영역, 웹 서비스 및 기본 끝점을 찾습니다. 이 문서에서는 여러분이 URL 및 API 키를 알고 있고 키를 갖고 있는 작업 영역에 익숙해지도록 수동으로 설정을 적용하겠습니다. 끝점 **URL** 및 **API 키**를 입력합니다. **확인**을 클릭합니다. **함수 별칭**은 'sentiment'입니다.  
    
    ![Stream Analytics Machine Learning, Machine Learning 웹 서비스](./media/stream-analytics-machine-learning-integration-tutorial/add-function-endpoints.png)    

11. **쿼리** 탭에서 다음과 같이 쿼리를 수정합니다.    
    
    ```
    WITH sentiment AS (  
      SELECT text, sentiment(text) as result from datainput  
    )  
    
    Select text, result.[Scored Labels]  
    Into testoutput  
    From sentiment  
    ```    

12. **저장** 을 클릭하여 쿼리를 저장합니다.

## <a name="start-the-stream-analytics-job-and-observe-the-output"></a>Stream Analytics 작업을 시작하고 출력 관찰
1. 작업 페이지 위쪽에서 **시작**을 클릭합니다.
2. **쿼리 시작 대화 상자**에서 **사용자 지정 시간**을 선택한 다음 Blob 저장소에 CSV를 업로드했던&1;일 이전을 선택합니다. **확인**을 클릭합니다.  
3. 예를 들어 CSV 파일을 업로드하는 데 사용했던 도구(예: Visual Studio)를 사용하여 Blob 저장소로 이동합니다.
4. 작업 시작 후 몇 분이 지나면 출력 컨테이너 만들어지고 그 컨테이너에 CSV 파일이 업로드됩니다.  
5. 기본 CSV 편집기에서 파일을 엽니다. 다음과 유사한 항목이 표시됩니다.  
   
   ![Stream Analytics Machine Learning, CSV 보기](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-csv-view.png)  

## <a name="conclusion"></a>결론
이 문서에서는 스트리밍 텍스트 데이터를 읽고 정서 분석을 실시간으로 데이터에 적용하는 Stream Analytics 작업을 만드는 방법을 보여 줍니다. 복잡한 정서 분석 모델 빌드에 대한 걱정 없이 이 모든 작업을 수행할 수 있습니다. 이것이 바로 Cortana Intelligence Suite의 장점 중 하나입니다.

또한 Azure Machine Learning 함수 관련 메트릭도 확인할 수 있습니다. 이를 수행하려면 **모니터** 탭을 선택합니다. 세 가지 함수 관련 메트릭이 표시됩니다.  

* **함수 요청** 은 Machine Learning 웹 서비스로 전송되는 요청 수를 나타냅니다.  
* **함수 이벤트** 는 요청의 이벤트 수를 나타냅니다. 기본적으로 Machine Learning 웹 서비스에 대한 각 요청에는 최대 1,000개의 이벤트가 포함됩니다.  
  
    ![Stream Analytics Machine Learning, Machine Learning 모니터 보기](./media/stream-analytics-machine-learning-integration-tutorial/job-monitor.png)  




<!--HONumber=Feb17_HO2-->


