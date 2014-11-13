<properties title="Azure 기계 학습으로 예측 솔루션 개발" pageTitle="기계 학습으로 예측 솔루션 개발 | Azure" description="Azure 기계 학습 스튜디오에서 예측 분석 실험을 만드는 방법의 연습을 소개합니다." metaKeywords="" services="machine-learning" solutions="" documentationCenter="" authors="garye" manager="paulettm" editor="cgronlun" videoId="" scriptId="" />

<tags ms.service="machine-learning" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="08/06/2014" ms.author="garye" />

# Azure 기계 학습으로 예측 솔루션 개발

신용대출 지원 시 제공한 정보를 기반으로 개인의 신용 위험을 예측해야 한다고 가정합니다.

물론 이건 복잡한 문제이지만 질문의 매개 변수를 약간 단순화하고 Microsoft Azure 기계 학습과 기계 학습 스튜디오 및 기계 학습 API 서비스를 사용하여 이러한 예측 솔루션을 만들 수 있는 방법의 예제로 사용해보겠습니다.

이 연습에서는 기계 학습 스튜디오에서 예측 분석 모델을 개발한 다음 기계 학습 API 서비스에 게시하는 프로세스를 따릅니다. 공개적으로 사용 가능한 신용 위험 데이터에서 시작하여 해당 데이터를 기반으로 예측 모델의 학습을 진행한 다음 다른 사람이 사용할 수 있는 웹 서비스로 모델을 게시합니다.

다음 단계를 따릅니다.

1.  [기계 학습 작업 영역 만들기][기계 학습 작업 영역 만들기]
2.  [기존 데이터 업로드][기존 데이터 업로드]
3.  [새로운 실험 만들기][새로운 실험 만들기]
4.  [모델 학습 및 평가][모델 학습 및 평가]
5.  [웹 서비스 게시][웹 서비스 게시]
6.  [웹 서비스 액세스][웹 서비스 액세스]

이 연습은 기계 학습 스튜디오에 포함된
[신용 위험 예측 샘플 실험][신용 위험 예측 샘플 실험]의 간소화된 버전을 기반으로 합니다.

  [기계 학습 작업 영역 만들기]: ../machine-learning-walkthrough-1-create-ml-workspace/
  [기존 데이터 업로드]: ../machine-learning-walkthrough-2-upload-data/
  [새로운 실험 만들기]: ../machine-learning-walkthrough-3-create-new-experiment/
  [모델 학습 및 평가]: ../machine-learning-walkthrough-4-train-and-evaluate-models/
  [웹 서비스 게시]: ../machine-learning-walkthrough-5-publish-web-service/
  [웹 서비스 액세스]: ../machine-learning-walkthrough-6-access-web-service/
  [신용 위험 예측 샘플 실험]: ../machine-learning-sample-credit-risk-prediction/
