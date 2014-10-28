<properties title="Azure Machine Learning Sample: Credit risk prediction" pageTitle="Machine Learning Sample: Credit risk prediction | Azure" description="A sample Azure Machine Learning experiment to develop a binary classification model that predicts if an applicant is a low credit risk or a high credit risk." metaKeywords="" services="" solutions="" documentationCenter="" authors="garye" videoId="" scriptId="" />

<tags ms.service="machine-learning" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="garye"></tags>

# Azure 기계 학습 샘플: 신용 위험 예측

*기계 학습 스튜디오의 **샘플** 탭 아래 **실험** 섹션에서 이 모델과 연결된 샘플 실험을 찾을 수 있습니다. 실험 이름은 다음과 같습니다.*

    Sample Experiment - German Credit - Development

*이 실험의 간소화된 버전을 만들고 사용하는 방법에 대한 자세한 연습은 [Azure 기계 학습을 사용하여 예측 솔루션 개발][Azure 기계 학습을 사용하여 예측 솔루션 개발](영문)을 참조하세요.*

이 실험의 목적은 신용 확인 시 제공되는 정보에 따라 신용 위험을 예측하는 것입니다. 예측은 이진 값인 낮은 위험 또는 높은 위험입니다.

<!-- Removed until the Training and Scoring parts are fixed This example is divided into 3 sample experiments:  - Development Experiment – for experimenting with different models - Training Experiment – to train the one chosen model - Scoring Experiment – to set up a web service using the trained model -->

## 데이터 집합 설명

이 실험에서는 다음 위치에서 찾을 수 있는 UCI Statlog(독일 신용 카드) 데이터 집합을 사용합니다.
<http://archive.ics.uci.edu/ml/datasets/Statlog+(German+Credit+Data)>.
이 웹 사이트에서 german.data 파일을 사용하고 있습니다.

데이터 집합은 특성 집합으로 설명된 사람을 낮은 신용 위험 또는 높은 신용 위험으로 분류합니다. 각 예제는 개인을 나타냅니다. 숫자 및 범주의 기능 20개와 이진 레이블(신용 위험 값)이 있습니다. 높은 신용 위험 항목의 레이블은 2이고 낮은 신용 위험 항목의 레이블은 1입니다. 낮은 위험 예제를 높음으로 오분류하는 비용은 1이나, 높은 위험 예제를 낮음으로 오분류하는 비용은 5입니다.

## 개발 실험

원래 데이터 집합에는 공백으로 구분된 형식이 있습니다. 이 데이터 집합을 CSV 형식으로 변환하고 기계 학습 스튜디오에 업로드했습니다. 이 변환은 Powershell을 사용하여 수행할 수 있습니다.

    cat dataset.txt | %{$_ -replace " ",","} | sc german.csv

또는 Unix sed 명령을 사용합니다.

    sed 's/ /,/g' german.data > german.csv

먼저 **메타데이터 편집기**를 사용하여 데이터 집합의 기본 열 이름을 UCI 사이트의 데이터 집합 설명에서 가져온 더 의미 있는 이름으로 바꿀 열 이름을 추가합니다. **메타데이터 편집기**의 **새 열 이름** 필드에서 새 열 이름은 쉼표로 구분됩니다.

다음으로 위험 예측 모델을 개발하는 데 사용할 학습 및 테스트 집합을 생성합니다. **입력에 대한 첫 번째 출력 행의 비율**을 0.5로 설정하고 **분할** 모듈을 사용하여 원래 데이터 집합을 같은 크기의 학습 및 테스트 집합으로 분할합니다.

높은 위험 예제를 낮음으로 오분류하는 비용은 낮은 위험을 높음으로 오분류하는 비용보다 5배 더 크므로 이 비용 함수를 반영하는 새 데이터 집합을 생성합니다. 새 데이터 집합에서 각 높은 위험 예제는 5회 복제되지만 낮은 위험 예제는 그대로 유지됩니다. 같은 예제가 학습 및 테스트 집합에 둘 다 포함되지 않도록 학습 및 테스트 데이터 집합 분할은 이 복제 전에 수행됩니다.

이 복제는 **R 스크립트 실행** 모듈을 사용하여 실행되는 다음 R 코드를 통해 수행됩니다.

    dataset1 <- maml.mapInputPort(1)
    data.set<-dataset1[dataset1[,21]==1,]
    pos<-dataset1[dataset1[,21]==2,]
    for (i in 1:5) data.set<-rbind(data.set,pos)
    maml.mapOutputPort("data.set")

실험에서 모델을 생성하기 위한 두 가지 접근법으로 원래 데이터 집합에 대한 학습 및 복제된 데이터 집합에 대한 학습을 비교합니다. 두 접근법에서 모두 문제의 비용 함수에 맞게 조정되도록 복제를 통해 테스트 집합에서 테스트합니다. 분할 및 복제의 마지막 워크플로는 다음과 같이 설명됩니다. 이 워크플로에서 **분할** 모듈의 왼쪽 출력은 학습 집합이고 오른쪽 출력은 테스트 집합입니다. 학습 집합은 **R 스크립트 실행**을 사용하거나 사용하지 않고, 즉 복제를 사용하거나 사용하지 않고 순차적으로 사용됩니다.

![학습 및 테스트 데이터 분할][학습 및 테스트 데이터 분할]

학습 집합에서 예제 복제의 영향을 확인하고 두 가지 알고리즘, SVM(Support Vector Machine) 및 향상된 의사 결정 트리의 성능도 비교합니다. 이 방법으로 다음 모델 4개를 효과적으로 생성합니다.

-   SVM, 원래 데이터로 학습됨
-   SVM, 복제된 데이터로 학습됨
-   향상된 의사 결정 트리, 원래 데이터로 학습됨
-   향상된 의사 결정 트리, 복제된 데이터로 학습됨

향상된 의사 결정 트리는 모든 기능 유형에서 제대로 작동합니다. 그러나 SVM 모듈은 선형 분류자를 생성하므로 여기서 생성하는 모델에는 모든 기능의 크기가 같을 때 최적의 테스트 오류가 포함됩니다. 모든 기능을 같은 크기로 변환하기 위해 **크기 조정으로 데이터 변환** 모듈을 Tanh 변환과 함께 사용합니다. 이 변환을 통해 모든 숫자 기능을 [0,1] 범위로 변환합니다. 문자열 기능은 SVM 모듈을 통해 범주 기능으로 변환되고 나서 이진 0/1 기능으로 변환되므로 문자열 기능을 수동으로 변환할 필요가 없습니다.

**2클래스 Support Vector Machine** 모듈 또는 **2Class 향상된 의사 결정 트리** 모듈을 사용하여 학습 알고리즘을 초기화하고 **모델 학습** 모듈을 사용하여 실제 모델을 만듭니다. 이러한 모델은 **모델 점수 매기기** 모듈에서 사용하여 테스트 예제의 점수를 생성합니다. 이러한 모듈을 결합하고 SVM과 복제된 학습 집합을 사용하는 예제 워크플로는 다음과 같이 설명됩니다. **모델 학습**은 학습 집합에 연결되지만 **모델 점수 매기기**는 테스트 집합에 연결됩니다.

![모델 학습 및 점수 매기기][모델 학습 및 점수 매기기]

실험의 평가 단계에서는 위 모델 4개 각각의 정확도를 계산합니다. 이런 목적으로 **모델 평가** 모듈을 사용합니다. 이 모듈은 모든 예제에 같은 오분류 비용이 있을 때만 정확도를 계산합니다. 그러나 이전에 긍정적 예제를 복제했으므로 **모델 평가**를 통해 계산된 정확도는 비용에 따라 달라지며 다음과 같습니다.

![정확도 계산][정확도 계산]

여기서 *n+* 및 *n-*는 원래 데이터 집합의 긍정적 및 부정적 예제 수이고, *e+* 및 *e-*는 원래 데이터 집합의 오분류된 긍정적 및 부정적 예제 수입니다.

**모델 평가** 모듈에서는 점수가 매겨진 모델 두 개를 비교하므로 하나의 **모델 평가** 모듈을 사용하여 SVM 모델 두 개를 비교하고 다른 하나를 사용하여 향상된 의사 결정 트리 모델 두 개를 비교합니다. 이러한 모델을 테이블로 결합하여 결과 4개를 모두 확인합니다. **모델 평가**에서는 다양한 메트릭이 포함된 단일 행이 있는 테이블을 생성합니다. **행 추가** 모듈을 사용하여 모든 결과를 단일 테이블로 결합합니다. 그리고 나서 **R 스크립트 실행** 모듈에서 R 스크립트를 사용하여 모듈 4개의 정확도가 포함된 테이블에 주석을 지정합니다. 이 모듈에서 마지막 테이블의 행 이름을 수동으로 입력합니다. 마지막으로 **프로젝트 열** 모듈을 사용하여 관련되지 않는 메트릭이 있는 열을 제거합니다.

**프로젝트 열**의 **결과 데이터 집합** 출력을 마우스 오른쪽 단추로 클릭하여 얻은 실험의 마지막 결과는 다음과 같습니다.

![결과][결과]

첫 번째 열이 모델을 생성하는 데 사용된 기계 학습 알고리즘의 이름인 경우 두 번째 열은 학습 집합 유형을 나타내고 세 번째 열은 비용에 따른 정확도입니다. 이 실험에서는 복제된 학습 데이터 집합을 사용하는 SVM 모델이 최적의 정확도를 제공합니다.

<!-- Removed until the Training and Scoring parts are fixed ##Training Experiment  The sample training experiment is a simplified version of the larger experiment using just the chosen SVM training model.   Notice that unlike the development experiment, in the training experiment we chose to load the dataset from Azure blob storage using the **Reader** module. Having the dataset stored in Azure is very common when it is generated by other programs. By reading the dataset directly from Azure we skip the step of manually uploading the dataset into ML Studio. The parameters of the **Reader** module are shown below. In this example, the storage account name is “datascience” and the dataset file “german.csv” is placed in container “sampleexperiments”. The account key is an access key of an Azure storage account. This key can be retrieved from your account in the Azure management portal.  ![Azure storage parameters][screen3]   ##Scoring Experiment  The purpose of the sample scoring experiment is to set up a REST API web service that will score test examples. The trained model in this experiment (“Credit Risk model”) was created from the training experiment by right-clicking the Train Model module and selecting **Save as Trained Model**. In this scoring experiment we load test examples, normalize them, and perform scoring using this saved trained model.   After running this experiment and verifying that it generates the right scores we prepare to publish it as a web service by defining the service input and output. We define the web service input as the input port to the **Transform Data By Scaling** module by right-clicking the port and selecting **Set as Publish Input**. The web service output is set to the output of the **Score Model** module by right-clicking the output of **Score Model** and selecting **Set as Publish Output**.   After setting up the service input and output we need to rerun the experiment and then click **Publish Web Service**. This publishes the web service to the staging environment and takes us to the ML Studio **WEB SERVICES** page. Here we can configure and test the service with sample data.  When the service is ready to go live, go to the **CONFIGURATION** tab on the **WEB SERVICES** page and click **READY FOR PRODUCTION?**. A request will be sent to the IT administrator for Machine Learning who can promote the service to the production environment.  ![Web service ready for production][screen4]  -->

  [Azure 기계 학습을 사용하여 예측 솔루션 개발]: http://azure.microsoft.com/ko--kr/documentation/articles/machine-learning-walkthrough-develop-predictive-solution/
  [학습 및 테스트 데이터 분할]: ./media/machine-learning-sample-credit-risk-prediction/screen1.jpg
  [모델 학습 및 점수 매기기]: ./media/machine-learning-sample-credit-risk-prediction/screen2.jpg
  [정확도 계산]: ./media/machine-learning-sample-credit-risk-prediction/formula.jpg
  [결과]: ./media/machine-learning-sample-credit-risk-prediction/results.jpg
