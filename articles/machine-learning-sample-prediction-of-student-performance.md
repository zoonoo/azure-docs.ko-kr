<properties title="Azure Machine Learning Sample: Prediction of student performance" pageTitle="기계 학습 샘플: 학생 성취도 예측 | Azure" description="학생의 시험 성적을 예측하는 모델을 개발할 샘플 Azure 기계 학습 실험입니다." metaKeywords="" services="machine-learning" solutions="" documentationCenter="" authors="garye" manager="paulettm" editor="cgronlun"  videoId="" scriptId="" />

<tags ms.service="machine-learning" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/23/2014" ms.author="garye" />


# Azure 기계 학습 샘플: 학생 성취도 예측

>[AZURE.NOTE]
>이 모델과 관련된 [샘플 실험] 및 [샘플 데이터 집합]은 기계 학습 스튜디오에서 사용할 수 있습니다. 자세한 내용은 다음을 참조하세요.
[샘플 실험]: #sample-experiment
[샘플 데이터 집합]: #sample-dataset


##데이터 집합 및 문제 설명
이 실험에서 데이터 집합은 KDD Cup 2010의 Algebra_2008_2009 학습 집합입니다. 이 데이터 집합은 [https://pslcdatashop.web.cmu.edu/KDDCup/downloads.jsp](https://pslcdatashop.web.cmu.edu/KDDCup/downloads.jsp)에서 다운로드할 수 있습니다. 데이터 집합에는 학생 개인 지도 시스템의 로그 파일이 포함됩니다. 제공된 기능에는 문제 ID와 간략한 설명, 학생 ID, 타임스탬프, 올바른 방법으로 문제를 해결하기 전에 학생이 시도한 횟수가 포함됩니다. 학습 문제는 학생이 첫 시도부터 제공된 문제를 해결할지를 예측하는 것입니다. KDD Cup 규칙에 따라 RMSE([제곱 평균 오류](http://en.wikipedia.org/wiki/Root-mean-square_deviation))를 사용하여 학습 알고리즘의 정확도를 측정합니다. 원래 데이터 집합에는 8,900,000개의 레코드가 있습니다. 실험 시간을 단축하려고 데이터 집합의 처음 100,000개 행으로 샘플 수를 줄였습니다. 데이터 집합에는 숫자, 범주 및 타임스탬프와 같은 다양한 유형의 열 23개가 있습니다. 열은 탭으로 구분됩니다.
 
##개발 워크플로
데이터 집합에는 **R 작업 적용** 모듈에서 처리할 수 없는 ASCII가 아닌 문자가 있습니다. Passau에서 데이터 집합을 사용하기 전에 다음 Powershell 명령을 사용하여 ASCII가 아닌 문자를 제거했습니다.  

	gc algebra_2008_2009_train.txt -head 100000 | sc algebra_train_small.txt
	sc tmp.txt -Encoding ASCII -Value (gc algebra_train_small.txt)
	cat tmp.txt | %{$_ -replace "\?\?sqrt","+sqrt"} | sc algebra_train_small.txt_ascii  

결과 파일 algebra_train_small.txt_ascii는 아직도 상당히 크고 36M를 차지합니다. 이 파일을 Azure Blob 저장소에 저장하고 **판독기** 모듈을 사용하여 파일을 실험으로 로드합니다. 파일을 Blob 저장소에 저장하기 위한 Powershell 명령은 다음과 같습니다.  

	Add-AzureAccount
	$key = Get-AzureStorageKey -StorageAccountName <your storage account name>
	$ctxt = New-AzureStorageContext -StorageAccountName $key.StorageAccountName -StorageAccountKey $key.Primary
	Set-AzureStorageBlobContent -Container <container name in your storage account> -File "algebra_train_small.txt_ascii" -Context $ctxt

![][1]
 
**판독기** 모듈의 매개 변수는 위에 나와 있습니다. 이 예제에서 저장소 계정 이름은 "datascience"이고, 데이터 집합 파일 algebra_train_small.txt_ascii는 "sampleexperiments" 컨테이너에 배치됩니다. 계정 키는 Azure 저장소 계정의 액세스 키입니다. 이 키는 Azure 관리 포털([https://manage.windowsazure.com](https://manage.windowsazure.com))의 계정에서 검색할 수 있습니다.
 
![][2]
 
위에 나와 있는 다음 단계에서는 많은 변환을 수행하여 데이터 집합을 학습 알고리즘에 맞는 형식으로 가져옵니다. **메타데이터 편집기**를 사용하여 타임스탬프 열 "First Transaction Time"을 문자열 형식으로 변환합니다. 학습/테스트 분할을 생성하려면 이 작업이 필요합니다. 그리고 나서 **프로젝트 열**을 사용하여 실험에서 사용되지 않을 많은 열을 제거합니다. 누락 값 스크러버를 사용하여 모든 누락 값을 0으로 바꿉니다. 다음 단계에서는 "Unit Name, Section Name" 열을 두 개의 "Unit Name" 및 "Section Name" 열로 분할합니다. **R 실행** 모듈에서 다음 R 코드를 사용하여 이 작업을 합니다.  

	dataset <- maml.mapInputPort(1)
	b <- data.frame(do.call(rbind,strsplit(as.vector(dataset[,3]),",")))
	names(b) <- c("Unit Name","Section Name")
	data.set <- cbind(dataset[,1:2],b,dataset[,4:15])
	maml.mapOutputPort("data.set")  

 
데이터 및 초기 변환 로드가 포함된 실험의 첫 단계는 위에 나와 있습니다. 데이터를 조작하고 나서 실험을 분기 4개로 분할합니다. 각 분기에서 다른 기능 집합을 테스트합니다. 몇몇 기능 집합은 이전에 [1]에서 사용했습니다. 첫 번째 분기에서 아래 설명된 기능 집합은 StudentID, Unit  

![][3]  
![][4]
 
 
Name, Section Name, Problem Name, Problem View 및 Opportunity 기능과 문제에 대한 텍스트 설명입니다. 정수가 아닌 기능은 범주 기능으로 표시됩니다. Hints 열은 새 학생의 성공을 예측할 때 사용할 수 없으므로 이 분기에서 먼저 **프로젝트 열**을 사용하여 Hints 열을 제거합니다. 그리고 나서 **분할** 모듈을 사용하여 데이터 집합을 학습 및 테스트 집합으로 분할합니다. 모든 예제에는 타임스탬프가 있으므로 분할은 시간을 기준으로 합니다.  2008년부터 First Transaction Time이 있는 모든 행이 학습 집합에 있고 2009년부터는 테스트 집합에 있습니다. **분할** 모듈의 매개 변수는 오른쪽에 나와 있습니다. 분할하고 나서 향상된 의사 결정 트리를 사용하여 이진 분류 모델을 생성하고 테스트 데이터의 점수를 매깁니다. **모델 학습** 모듈의 레이블 열은 Correct First Attempt입니다.  

![][5]  

 
위에 나와 있는 **수치 연산 적용** 및 **기본 통계** 모듈의 다음 시퀀스에서는 원시 점수 및 실제 레이블을 사용하여 모델의 RMSE를 계산합니다. 회귀 모델의 경우 이 메트릭은 **평가기** 모듈을 통해 계산됩니다. 하지만 기타 모델의 경우 이 메트릭을 수동으로 계산해야 합니다. 첫 번째 **수치 연산 적용** 모듈에서는 레이블 열과 **모델 점수 매기기** 모듈을 통해 생성된 점수 열의 차이를 계산합니다. 두 번째 **수치 연산 적용** 모듈에서는 이 차이의 제곱을 계산합니다. 그리고 나서 **기본 통계** 모듈에서는 차이 제곱의 평균을 계산했습니다. 마지막으로 최종 **수치 연산 적용** 모듈을 사용하여 제곱근을 계산합니다. 이러한 모듈 4개의 매개 변수는 아래에 나와 있습니다.  

![][6] 
![][7]  
![][8]  
![][9] 
 
 
위에 나와 있는 두 번째 분기에서는 시간 차원을 활용합니다. 첫 번째 분기의 기능 이외에 현재 사용자가 해결하는 문제의 마지막 두 단계 이름을 해당 설명과 함께 사용합니다. 제공된 데이터 집합에서 모든 사용자의 활동은 타임스탬프의 오름차순으로 저장됩니다. 또한 제공된 데이터 집합에서 사용자의 활동은 인터리브되지 않습니다. 즉, 처음에는 첫 번째 사용자의 모든 행이 있고 다음에는 두 번째 사용자의 모든 행이 있는 방식으로 진행됩니다. 따라서 사용자의 마지막 단계를 찾기 위해 RowID 열을 활용합니다. 수정된 사용자의 경우 이 열이 시간 축으로 사용됩니다. 수치 연산****을 사용하여 이 열에 1을 더하여 각 행을 한 번 단위로 앞으로 이동합니다. 그리고 나서 **조인** 모듈을 통해 RowID, StudentID 및 ProblemName을 키로 사용하여 원래 데이터 집합을 이동한 데이터 집합과 조인합니다. 결과로는 각 행에 같은 StudentID 및 ProblemName에 대한 시간 t 및 t-1의 레코드가 있는 확장된 데이터 집합을 얻습니다. *왼쪽 우선 외부 조인*을 사용하여 이전 단계가 없는 행을 같은 StudentID 및 ProblemID로 유지합니다. **수치 연산 적용** 및 **조인**의 조합을 한 번 더 적용하여 두 단계 전의 기능을 얻습니다. **수치 연산 적용** 및 **조인**의 정확한 매개 변수는 아래에 나와 있습니다.  

![][10]  
![][11]  
![][12]  

 
두 번 수행하고 나면 결과로 똑같은 많은 열을 얻습니다. 예를 들어 조인 모듈을 사용하기 때문에 ProblemName 열은 단계 t, t-1 및 t-2에 대해 세 번 복제됩니다. **프로젝트 열** 모듈을 사용하여 중복 열을 제거합니다. 마지막으로 *왼쪽 우선 외부 조인*을 사용했기 때문에 조인 연산자에 의해 생성된 몇몇 행에는 누락 값이 있을 수 있습니다. **누락 값 스크러버**를 사용하여 모든 누락 값을 "0" 문자열로 바꿉니다. **누락 값 스크러버**의 매개 변수는 위에 나와 있습니다.  

새 기능 집합을 계산하고 나면 두 번째 분기의 워크플로가 첫 번째 분기의 워크플로와 똑같습니다.  

![][13]
 
세 번째 분기에서는 두 번째 분기에서 사용된 기능 이외에 첫 번째 분기에서 원래 기능의 연결인 정방형 및 입방형 기능도 사용합니다. **R 작업 실행** 모듈을 위에 나와 있는 R 코드와 함께 사용하여 정방형 및 입방형 기능을 계산합니다. 새 기능 집합을 계산하고 나서 처음 두 분기에서와 같은 방법으로 학습, 점수 매기기 및 평가를 진행합니다.  

네 번째 분기에서는 처음 세 개 분기의 기능과 완전히 다른 기능을 사용합니다. 각 StudentID에 대해 시간 t까지(포함하지 않음) Correct First Attempt의 평균 값을 계산합니다. 이 값을 CFA(StudentID,t)로 나타냅니다. 마찬가지로 시간 t까지(포함하지 않음) 수정된 StudentID에 대한 Hints의 평균 값을 Hints(StudentID,t)로 나타냅니다. 이러한 평균의 계산 시간을 단축하기 위해 전체 데이터 집합을 고려하는 대신 시간 t 이전의 가장 최근 레코드 10개만 살펴봅니다. CFA(Problem Name,t), CFA(Step Name,t), CFA(Description,t), Hints(Problem Name,t), Hints(Step Name,t) 및 Hints(Description,t)의 정의는 비슷합니다. First Transaction Time=t(x)이고 열 값이 StudentID(x), Problem Name(x), Step Name(x), Description(x)인 예제 x에서 위의 CFA 및 Hints 기능 8개를 생성합니다.  

	CFA(StudentID(x),t(x)), CFA(Problem Name(x),t(x)), CFA(Step Name(x),t(x)), CFA(Description(x),t(x)), 
	Hints(StudentID(x),t(x)), Hints(Problem Name(x),t(x)), Hints(Step Name(x),t(x)), Hints(Description(x),t(x))  

![][14]  

 
마찬가지로 StudentID 및 Problem Name, Problem Name 및 Step Name, StudentId 및 UnitName, StudentID 및 Problem Description의 연결을 사용하여 CFA 및 Hints 기능 8개를 추가로 계산합니다. 전체적으로 Correct First Attempt 열의 값을 예측하는 데 사용되는 기능 16개를 얻습니다. 이러한 16개의 기능은 **R 작업 실행** 모듈 내에서 R 코드를 사용하여 계산합니다. 이 코드는 길고 지루하지만 상당히 최적화되어 있습니다. 이러한 기능을 계산하고 나서 R 코드를 통해 추가된 몇몇 보조 열을 제거합니다. 이런 목적으로 **프로젝트 열** 모듈을 사용합니다. 네 번째 분기에서 기능 계산을 위한 전체 워크플로는 위에 나와 있습니다. 새 기능 집합을 계산하고 나서 처음 세 분기에서와 같은 방법으로 학습, 점수 매기기 및 평가를 진행합니다.  

모든 분기 4개에서 RMSE 값을 계산하고 나서 **행 추가** 모듈을 사용하여 결과를 수집합니다. 또한 **R 실행** 모듈을 사용하여 주석을 생성합니다. 이 마지막 실험 부분의 워크플로는 다음과 같이 설명됩니다.  

![][15]  
 
 
실험의 마지막 출력은 다음 테이블이고, 첫 번째 열은 기능 집합의 이름이고, 두 번째 열은 테스트 예제에 대해 측정된 RSME입니다.  

![][16]
 

결과적으로 네 번째 기능 집합이 가장 낮은 RMSE를 제공합니다.  

##참조
H.-F. Yu et al. Feature Engineering and Classifier Ensemble for KDD Cup 2010. KDD Cup 2010 Workshop, 2010.



## 샘플 실험

이 모델과 연결된 다음 샘플 실험은 기계 학습 스튜디오의 **샘플** 탭 아래 **실험** 섹션에 있습니다.

> **샘플 실험 - 학생 성취도 - 개발**


## 샘플 데이터 집합

이 실험에서 사용되는 다음 샘플 데이터 집합은 기계 학습 스튜디오의 **저장된 데이터 집합** 아래 모듈 팔레트에 있습니다.

###student_performance.txt
[AZURE.INCLUDE [machine-learning-sample-dataset-student-performance](../includes/machine-learning-sample-dataset-student-performance.md)]



[1]: ./media/machine-learning-sample-prediction-of-student-performance/student-performance-1.jpg
[2]: ./media/machine-learning-sample-prediction-of-student-performance/student-performance-2.jpg
[3]: ./media/machine-learning-sample-prediction-of-student-performance/student-performance-3.jpg
[4]: ./media/machine-learning-sample-prediction-of-student-performance/student-performance-4.jpg
[5]: ./media/machine-learning-sample-prediction-of-student-performance/student-performance-5.jpg
[6]: ./media/machine-learning-sample-prediction-of-student-performance/student-performance-6.jpg
[7]: ./media/machine-learning-sample-prediction-of-student-performance/student-performance-7.jpg
[8]: ./media/machine-learning-sample-prediction-of-student-performance/student-performance-8.jpg
[9]: ./media/machine-learning-sample-prediction-of-student-performance/student-performance-9.jpg
[10]: ./media/machine-learning-sample-prediction-of-student-performance/student-performance-10.jpg
[11]: ./media/machine-learning-sample-prediction-of-student-performance/student-performance-11.jpg
[12]: ./media/machine-learning-sample-prediction-of-student-performance/student-performance-12.jpg
[13]: ./media/machine-learning-sample-prediction-of-student-performance/student-performance-13.jpg
[14]: ./media/machine-learning-sample-prediction-of-student-performance/student-performance-14.jpg
[15]: ./media/machine-learning-sample-prediction-of-student-performance/student-performance-15.jpg
[16]: ./media/machine-learning-sample-prediction-of-student-performance/student-performance-16.jpg
