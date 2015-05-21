<properties 
    pageTitle="Azure 기계 학습이란 무엇인가요? | Microsoft Azure" 
    description="Azure 기계 학습 서비스의 개요입니다." 
    services="machine-learning" 
    documentationCenter="" 
    authors="tedway" 
    manager="neerajkh" 
    editor="cgronlun"/>

<tags 
    ms.service="machine-learning" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="04/22/2015" 
    ms.author="tedway;olgali"/>


# 기계 학습이란 무엇인가요?
기계 학습은 모든 주변 환경에서 발생합니다. 온라인 쇼핑 시 기계 학습은 구매한 제품에 따라 다른 제품을 추천하는 데 도움이 됩니다. 신용 카드를 읽힐 때 기계 학습은 은행에서 부정 행위를 검색하고 트랜잭션이 의심스러운 경우 알리는 데 도움이 됩니다. 기계 학습은 기존 데이터에서 학습하고 미래 데이터를 예측 분석하는 모델을 만드는 프로세스입니다.

## Azure 기계 학습이란 무엇인가요?
Azure 기계 학습은 신속하게 분석 솔루션을 만들 수 있게 해주는 강력한 클라우드 기반 예측 분석 서비스입니다. Azure 기계 학습은 기계 학습의 보편화를 위해 빌드되었습니다. 모든 사용자가 사용할 수 있도록 기계 학습 기술의 빌드 및 배포와 관련된 번거로운 작업이 제거되었습니다. 완전히 관리되는 서비스이므로 하드웨어를 구입하거나 가상 컴퓨터를 수동으로 관리할 필요가 없습니다.

브라우저 기반 도구 [기계 학습 스튜디오](machine-learning-what-is-ml-studio.md)를 사용하여 신속하게 기계 학습 워크플로를 만들고 자동화할 수 있습니다. 수백 개의 기존 [기계 학습 라이브러리](https://msdn.microsoft.com/library/azure/f5c746fd-dcea-4929-ba50-2a79c4c067d7)를 끌어서 놓아 예측 분석 솔루션을 빠르게 시작한 다음 필요에 따라 고유한 사용자 지정 [R](machine-learning-r-quickstart.md) 및 [Python](machine-learning-execute-python-scripts.md) 스크립트를 추가하여 확장할 수 있습니다. 스튜디오는 모든 브라우저에서 작동하며 신속하게 솔루션을 개발하고 반복할 수 있게 해줍니다. ![Azure 기계 학습 스튜디오를 사용한 클라우드의 예측 분석 실험](./media/machine-learning-what-is-machine-learning/AzureMLStudio.png)

쉽게 [웹 서비스](machine-learning-publish-a-machine-learning-web-service.md)를 검색 및 만들고, [API를 통해 모델을 학습 및 다시 학습](machine-learning-retrain-models-programmatically.md)하고, [끝점을 관리](machine-learning-create-endpoint.md)하고, 고객별로 [웹 서비스를 확장](machine-learning-scaling-endpoints.md)하고, 서비스 모니터링 및 디버깅을 위해 진단을 구성할 수 있습니다. 최신 기능에는 다음이 포함됩니다.

- numpy, scipy, panda 또는 scikit-learn과 같은 대규모 라이브러리 에코시스템을 사용하여 구성 가능한 사용자 지정 R 모듈을 만들고, 고유한 학습/예측 R 스크립트를 통합하고, Python 스크립트를 추가하는 기능. 이제 [개수로 학습][learning-with-counts]을 사용하여 테라바이트 데이터를 학습하고, PCA 또는 단일 클래스 SVM을 이상 검색에 사용하고, 익숙한 SQLite를 사용하여 쉽게 데이터를 수정, 필터링 및 정리할 수 있습니다. 
- [기계 학습 커뮤니티 갤러리](machine-learning-gallery-how-to-use-contribute-publish.md)를 통해 다른 사용자가 작성한 흥미로운 실험을 검색 및 사용할 수 있습니다. [갤러리](http://gallery.azureml.net)에서 실험에 대한 질문을 하거나 의견을 게시할 수 있으며, 또는 고유한 실험을 게시할 수도 있습니다. LinkedIn 및 Twitter와 같은 소셜 채널을 통해 흥미로운 실험에 대한 링크를 공유할 수 있습니다. 갤러리는 사용자가 Azure 기계 학습을 시작하고 커뮤니티의 다른 사용자로부터 배울 수 있는 효율적인 방법입니다. ![Azure 기계 학습 갤러리에서 예측 실험 샘플을 시도하거나 고유한 샘플 기여](./media/machine-learning-what-is-machine-learning/AzureMLGallery.png)
- Azure 구독을 통해 [마켓플레이스 앱](https://datamarket.azure.com/browse?query=machine+learning)을 구매하고 Azure 마켓플레이스에서 직접 권장 사항, 텍스트 분석 및 이상 검색에 완성된 웹 서비스를 사용할 수 있습니다. 
- 원시 데이터에서 사용 가능한 웹 서비스에 이르기까지 클라우드 기반 데이터 과학으로 향하는 길을 안내하는 데이터 과학 여행을 위한 단계별 가이드. Azure 기계 학습과 함께 iPython Notebook 및 Visual Studio용 Python Tools와 같은 인기 있는 도구를 사용하는 기능이 추가되었습니다.

## 지금 시작하기
[단계별 자습서](machine-learning-create-experiment.md) 및 [샘플을 기반으로 빌드](machine-learning-sample-experiments.md)를 사용하여 예측 분석 및 기계 학습의 기본 사항을 알아볼 수 있습니다. Azure 구독이나 신용 카드가 없어도 스튜디오에서 실험을 시도할 때 기계 학습을 사용할 수 있습니다.


<!-- Module References -->
[learning-with-counts]: https://msdn.microsoft.com/library/azure/81c457af-f5c0-4b2d-922c-fdef2274413c/

<!--HONumber=54-->