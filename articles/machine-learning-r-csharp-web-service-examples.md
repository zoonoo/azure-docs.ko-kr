<properties 
	pageTitle="Azure 기계 학습에서 R로 작성되고 마켓플레이스에 게시되는 샘플 웹 서비스 | Azure" 
	description="Azure 기계 학습에서 R로 작성되고 마켓플레이스에 게시되는 샘플 웹 서비스" 
	services="machine-learning" 
	documentationCenter="" 
	authors="jaymathe" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/11/2015" 
	ms.author="jaymathe"/> 


#Azure 기계 학습에서 R로 작성되고 마켓플레이스에 게시되는 샘플 웹 서비스




Azure 기계 학습 스튜디오를 사용하여 사용자는 R 코드를 작성하고 몇 번의 단추 클릭으로 전 세계에서 다른 개인과 장치가 소비하도록 웹 서비스로 게시할 수 있습니다. 예를 들어 통계 기능을 제공하는 단순 계산 생성부터 사용자 지정 텍스트 마이닝 정서 분석 예측 변수 생성까지, 초보 사용자와 숙련된 사용자가 둘 다 Azure 기계 학습 사용자로서 간편하게 R 코드를 운영할 수 있습니다. 이 웹 서비스를 사용자가 모바일 앱이나 웹 사이트를 통해 소비할 수 있지만 이 웹 서비스의 목적은 Azure 기계 학습에서 분석용으로 R 스크립트를 운영하고 R 코드의 맨 위에 웹 서비스를 만드는 데 Azure 기계 학습을 사용하는 방법의 예로 제공하는 것입니다.

이 페이지에서 Azure 기계 학습을 사용하여 만들고 Microsoft Azure 마켓플레이스에 게시한 광범위한 예제 웹 서비스 집합을 찾을 수 있습니다. 각 웹 서비스에는 서비스를 테스트하고 사용자가 비슷한 서비스를 직접 만들 수 있는 방법을 설명하는 샘플 데이터 집합이 포함된 광범위한 문서가 첨부되어 있습니다. 

![Workflow][1]

다음과 같은 시나리오를 고려해 보세요.

####시나리오 1: 일반 모델 
사용자는 시계열 데이터의 기본 예측 또는 고급 분석을 통한 사용자 지정으로 작성된 R 메서드와 같이 새 사용자의 데이터에 적용할 수 있는 일반 모델을 사용하여 작업합니다. 이 사용자는 다른 사용자가 해당 데이터와 함께 소비하도록 모델을 웹 서비스로 게시합니다.

* [이진 분류자](http://azure.microsoft.com/documentation/articles/machine-learning-r-csharp-binary-classifier/)
* [클러스터 모델](http://azure.microsoft.com/documentation/articles/machine-learning-r-csharp-cluster-model/)
* [다변량 선형 회귀](http://azure.microsoft.com/documentation/articles/machine-learning-r-csharp-multivariate-linear-regression/)
* [예측 - 지수 평활법](http://azure.microsoft.com/documentation/articles/machine-learning-r-csharp-forecasting-exponential-smoothing/)
* [ETS + STL 예측](http://azure.microsoft.com/documentation/articles/machine-learning-r-csharp-retail-demand-forecasting/)
* [예측 자동 회귀 통합 이동 평균(ARIMA)](http://azure.microsoft.com/documentation/articles/machine-learning-r-csharp-arima/)
* [생존 분석](http://azure.microsoft.com/documentation/articles/machine-learning-r-csharp-survival-analysis/)

####시나리오 2: 학습된 모델 - 특정 데이터 
사용자의 성격 유형을 예측하기 위한 k-means 알고리즘을 통해 클러스터된 성격 질문서의 큰 샘플 또는 생존 분석 R 패키지를 통해 개인의 위암에 대한 위험을 예측하는 데 사용할 수 있는 건강 설문 데이터와 같이 R 코드를 통해 유용한 예측을 제공하는 데이터가 사용자에게 있습니다. 사용자는 새 사용자의 결과를 예측하는 웹 서비스를 통해 데이터를 게시합니다.

####시나리오 3: 학습된 모델 - 일반 데이터 
일반적으로 다양한 유형의 사용 사례와 시나리오에서 적용할 수 있는 웹 서비스를 작성할 수 있는 일반 데이터(예: 텍스트 모음)가 사용자에게 있습니다.

* [어휘집 기반 감정 분석](http://azure.microsoft.com/documentation/articles/machine-learning-r-csharp-lexicon-based-sentiment-analysis/)

####시나리오 4: 고급 계산기 
사용자는 고급 계산 또는 시뮬레이션을 제공하므로 학습된 모델이 필요하지 않고 모델을 사용자 데이터에 맞출 필요가 없습니다.

* [비율 차이 테스트](http://azure.microsoft.com/documentation/articles/machine-learning-r-csharp-difference-in-two-proportions/)
* [정규 분포 제품군](http://azure.microsoft.com/documentation/articles/machine-learning-r-csharp-normal-distribution/)
* [이항 분포 패키지](http://azure.microsoft.com/documentation/articles/machine-learning-r-csharp-binomial-distribution/)

##FAQ
웹 서비스 사용 또는 마켓플레이스 게시 방법과 관련한 질문과 대답은 [여기](http://azure.microsoft.com/documentation/articles/machine-learning-marketplace-faq)를 참조하세요.

[1]: ./media/machine-learning-r-csharp-web-service-examples/base1.png



<!--HONumber=46--> 
