<properties title="Step 2: Upload existing data into an Azure Machine Learning experiment" pageTitle="2단계: 기계 학습 실험에 데이터 업로드 | Azure" description="2단계: Azure 기계 학습 실험 스튜디오에 기존 공용 데이터 업로드" metaKeywords="" services="machine-learning" solutions="big-data" documentationCenter="" authors="garye" videoId="" scriptId="" manager="paulettm" editor="cgronlun" />

<tags ms.service="machine-learning" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/02/2014" ms.author="garye" />

[Azure 기계 학습을 사용한 예측 솔루션 개발][develop] 자습서의 두 번째 단계입니다.

[develop]: ../machine-learning-walkthrough-develop-predictive-solution/


1.	[기계 학습 작업 영역 만들기][create-workspace]
2.	**기존 데이터 업로드**
3.	[새로운 실험 만들기][create-new]
4.	[모델 학습 및 평가][train-models]
5.	[웹 서비스 게시][publish]
6.	[웹 서비스 액세스][access-ws]

[create-workspace]: ../machine-learning-walkthrough-1-create-ml-workspace/
[upload-data]: ../machine-learning-walkthrough-2-upload-data/
[create-new]: ../machine-learning-walkthrough-3-create-new-experiment/
[train-models]: ../machine-learning-walkthrough-4-train-and-evaluate-models/
[publish]: ../machine-learning-walkthrough-5-publish-web-service/
[access-ws]: ../machine-learning-walkthrough-6-access-web-service/

----------

# 2단계: Azure 기계 학습 실험에 기존 데이터 업로드  

신용 위험에 대한 예측 모델을 개발하기 위해 UCI 기계 학습 리포지토리의 "UCI Statlog(독일 신용 데이터) 데이터 집합"을 사용합니다. 다음 위치에서 찾을 수 있습니다.  
<a href="http://archive.ics.uci.edu/ml/datasets/Statlog+(German+Credit+Data)">http://archive.ics.uci.edu/ml/datasets/Statlog+(German+Credit+Data)</a>

여기서는 **german.data**라는 파일을 사용합니다. 로컬 하드 드라이브로 이 파일을 다운로드하세요.  

이 데이터 집합에는 지난 1000명의 신용대출 신청자에 대한 20개 변수 행이 포함되어 있습니다. 이러한 20개 변수는 각 대출 신청자에 대한 특성을 파악하는 데이터 집합의 기능 벡터를 나타냅니다. 각 행의 추가 열은 신청자의 신용 위험을 나타내며, 700명의 신청자는 신용 위험이 낮은 것으로, 300명의 신청자는 위험이 높은 것으로 파악되었습니다.   

UCI 웹 사이트는 금융 정보, 신용 기록, 취업 상태, 개인 정보를 포함하는 기능 벡터의 특성에 대한 설명을 제공합니다. 각 지원자에 대해 신용 위험이 낮은지 아니면 높은지 여부를 나타내는 이진 등급이 제공되었습니다.  

이 데이터를 사용하여 예측 분석 모델의 학습을 진행합니다. 학습을 완료하면 모델에서 새로운 개인에 대한 정보를 가져와 신용 위험이 낮은지 아니면 높은지 여부를 예측할 수 있습니다.  

그런데 한 가지 흥미로운 변형이 있습니다. 데이터 집합에 대한 설명에서는 지원자가 실제로 신용 위험이 높은데 낮은 신용 위험으로 잘못 분류하면 낮은 신용 위험을 높은 것으로 잘못 분류하는 경우보다 금융 기관의 비용이 5배 더 커진다고 말합니다. 이 실험에서 이런 점을 고려할 수 있는 간단한 방법은 신용 위험이 높은 사람을 나타내는 항목을 5배 복제하는 것입니다. 그런 다음 모델에서 높은 신용 위험을 낮은 것으로 잘못 분류하는 경우 각 복제마다 한 번씩 분류 오류를 5번 수행합니다. 그러면 학습 결과에서 이 오류의 비용이 증가됩니다.  

## 데이터 집합 형식 변환
원래 데이터 집합은 공백으로 구분된 형식을 사용합니다. 기계 학습 스튜디오는 CSV(쉼표로 구분된) 파일에서 더 원활하게 작동하므로 공백을 쉼표로 바꿔서 데이터 집합을 변환합니다.  

다음 Windows PowerShell 명령을 사용하여 이 작업을 수행할 수 있습니다.   

	cat german.data | %{$_ -replace " ",","} | sc german.csv  

또한 Unix sed 명령을 사용해서도 이 작업을 수행할 수 있습니다.  

	sed 's/ /,/g' german.data > german.csv  

## 기계 학습 스튜디오에 데이터 집합 업로드

데이터를 CSV 형식으로 변환한 후 기계 학습 스튜디오에 업로드해야 합니다.  

1.	기계 학습 스튜디오의 창 하단에 있는 **+새로 만들기**를 클릭합니다.
2.	**데이터 집합**을 선택합니다.
3.	**로컬 파일에서**를 선택합니다.
4.	**새 데이터 집합 업로드 대화 상자**에서 **찾아보기**를 클릭하여 앞서 만든 **german.csv** 파일을 찾습니다.
5.	데이터 집합의 이름을 입력합니다. 이 예제에서는 데이터 집합을 "UCI 독일 신용 카드 데이터"라고 합니다.
6.	데이터 형식의 경우 "머리글 없는 일반 CSV 파일(.nh.csv)"을 선택합니다.
7.	원하는 경우 설명을 추가합니다.
8.	**확인**을 클릭합니다.  

![Upload the dataset][1]  

 
그러면 데이터가 실험에 사용할 수 있는 데이터 집합 모듈에 업로드됩니다.

**다음: [새로운 실험 만들기][create-new]**

[1]: ./media/machine-learning-walkthrough-2-upload-data/upload1.png
