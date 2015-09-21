<properties 
	pageTitle="기계 학습 예제 앱: 자주 함께 구매됨 | Microsoft Azure" 
	description="온라인 쇼핑 카트 분석을 수행하여 사용자가 제공한 기록 트랜잭션의 자주 함께 구매한 항목에 대한 제품 추천을 생성하는 기계 학습 웹 서비스입니다." 
	services="machine-learning" 
	documentationCenter="" 
	authors="CoromT" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/07/2015" 
	ms.author="luisca"/>

# 기계 학습 예제 앱: 자주 함께 구매됨


## 중요: 이 서비스는 중단되었습니다.

이 서비스에서 제공하는 자주 함께 구매됨 기능은 보다 광범위한 [추천 API](http://gallery.azureml.net/MachineLearningAPI/3574432384684cac9cc766e57729ea4c)에 통합되었습니다. 이 서비스 대신 추천을 사용하는 것이 좋습니다.

##개요

기계 학습의 [자주 함께 구매됨 웹 서비스](https://datamarket.azure.com/dataset/amla/mba)는 온라인 쇼핑 카트 분석을 수행하여 기록 트랜잭션의 자주 함께 구매한 항목에 대한 제품 추천을 생성합니다. 자주 함께 구매됨 추천은 구매자가 특정 항목을 구매할 때 카탈로그에서 가장 관련 있는 제품을 식별하는 데 도움이 됩니다. 이러한 추천을 눈에 띄게 표시하는 것이 온라인 상점의 판매 증대에 효과적이라는 사실이 입증되었습니다.

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]
  
##시작 

자주 함께 구매됨 웹 서비스에 가입한 후에는 [시장 바구니 분석 서비스 예제 웹 응용 프로그램](https://marketbasket.cloudapp.net/)을 사용하여 데이터를 모델에 쉽게 업로드하고 자주 구매된 제품 집합을 확인할 수 있습니다. 응용 프로그램 또는 API를 사용하려면 먼저 [Azure 데이터 마켓 계정 페이지](https://datamarket.azure.com/account)에서 가져올 수 있는 API 키가 필요합니다.

##웹 서비스 사용 

이 서비스에는 자주 함께 구매됨 모델을 만들고, 기록 트랜잭션을 업로드하며, 지정된 제품에 대한 자주 함께 구매되는 최상위 제품을 받기 위한 API가 포함되어 있습니다. 이러한 API를 사용하는 방법을 보여 주는 예제는 GitHub의 [Azure-MachineLearning-DataScience](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Apps/FrequentlyBoughtTogether) 리포지토리에서 찾을 수 있습니다.

 

<!---HONumber=Sept15_HO2-->