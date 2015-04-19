<properties 
	pageTitle="기계 학습 예제 앱: 자주 함께 구매됨 | Azure" 
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
	ms.date="10/17/2014" 
	ms.author="coromt"/> 

# 기계 학습 예제 앱: 자주 함께 구매됨
 
이 기계 학습 [웹 서비스]( https://datamarket.azure.com/dataset/amla/mba)에서는 온라인 쇼핑 카트 분석을 수행하여 사용자가 제공한 기록 트랜잭션의 자주 함께 구매한 항목에 대한 제품 추천을 생성합니다. 자주 함께 구매됨 추천은 구매자가 특정 항목을 구매할 때 카탈로그에서 가장 관련 있는 제품을 식별하는 데 도움이 됩니다. 이러한 추천을 눈에 띄게 표시하는 것이 온라인 상점의 판매 증대에 효과적이라는 사실이 입증되었습니다. 
  
##시작 
이 [웹 서비스]( https://datamarket.azure.com/dataset/amla/mba)를 구독한 후에는 [자주 함께 구매됨 샘플 웹 응용 프로그램](https://marketbasket.cloudapp.net/)을 사용하여 쉽게 데이터를 모델에 업로드하고 자주 구매되는 제품 집합을 검색할 수 있습니다.  응용 프로그램 또는 API를 사용하려면 먼저 [Azure 데이터 마켓 계정 페이지](https://datamarket.azure.com/account)에서 가져올 수 있는 API 키가 필요합니다.

##웹 서비스 사용 

이 서비스에는 자주 함께 구매됨 모델을 관리하고 만들며, 기록 트랜잭션을 업로드하고, 지정된 제품에 대한 자주 함께 구매되는 최상위 제품을 받기 위한 API가 포함되어 있습니다.  이러한 API를 사용하는 방법을 보여 주는 샘플은 [GitHub의 Azure-MachineLearning-DataScience 리포지토리](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Apps/FrequentlyBoughtTogether)(영문)에서 찾을 수 있습니다.


<!--HONumber=46--> 
