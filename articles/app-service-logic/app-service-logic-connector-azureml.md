<properties
   pageTitle="논리 앱에서 Azure 기계 학습 커넥터 사용 | Microsoft Azure 앱 서비스"
   description="Azure 기계 학습 커넥터를 만들어서 구성하고 Azure 앱 서비스의 논리 앱에서 사용하는 방법"
   services="app-service\logic"
   documentationCenter=".net,nodejs,java"
   authors="jeffhollan"
   manager="dwrede"
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="11/11/2015"
   ms.author="jehollan"/>
   
# 개요
논리 앱용 Azure ML 커넥터를 통해 일괄 처리 점수 매기기(배치 실행 서비스) 및 재학습을 위한 Azure ML API를 호출할 수 있습니다. 논리 앱 트리거와 이러한 기능을 함께 사용하여 배치 작업을 예약하고 모델에 대해 예약된 재학습을 설정할 수 있습니다.

 ![][1]
 
## Azure 기계 학습 커넥터 시작 및 논리 앱에 추가
시작하려면 Azure 기계 학습 스튜디오에서 실험을 만든 후 웹 서비스를 설정 및 배포합니다. 그런 다음 배치 실행 도움말 페이지에 있는 API URL 및 BES Post URL의 Key를 사용할 수 있습니다([자세한 정보](https://github.com/Azure/azure-content/blob/master/articles/machine-learning/machine-learning-walkthrough-5-publish-web-service.md)).

커넥터를 사용하여 BES 작업을 실행하려면 Azure ML 커넥터를 논리 앱에 추가합니다. 그런 다음 필요한 정보를 입력합니다(이에 대한 자세한 내용은 아래 참조). 재학습을 설정하려면 두 번째 Azure ML 커넥터를 추가하고 입력 매개 변수를 제공합니다(재학습을 위한 모델 설정에 대한 자세한 내용은 [여기](machine-learning-retrain-models-programmatically.md) 참조).

## Azure ML 배치 실행 작업 실행
Azure ML 커넥터는 배치 실행(BES) 작업을 실행하기 위한 다음 네 가지 옵션을 제공합니다. 1. 입력 및 출력이 있는 배치 작업: 실험에 웹 서비스 입력 및 출력 모듈이 있음 2. 입력 및 출력이 없는 배치 작업: 실험에 웹 서비스 입력 또는 출력 모듈이 없음(예: 판독기 및 기록기 모듈 사용) 3. 입력만 있는 배치 작업: 실험에 웹 서비스 입력 모듈이 있으나 웹 서비스 출력 모듈은 없음(예: 기록기 모듈 사용) 4. 출력만 있는 배치 작업: 실험에 웹 서비스 입력 모듈이 없으나 웹 서비스 출력 모듈은 있음(예: 판독기 모듈 사용) BES는 비동기 요청이며 데이터 크기 및 모델의 복잡성에 따라 완료하는 데 시간이 소요될 수 있습니다. 작업이 완료되면 커넥터는 출력 결과를 반환합니다.

### 배치 실행: 입력 및 출력 포함
스튜디오 실험에 웹 서비스 입력 및 출력 모듈이 있는 경우 저장소 Blob 계정 및 위치에 대한 정보를 제공해야 합니다([여기서 자세히 보기](machine-learning-consume-web-services.md)). 또한 실험에 설정하는 경우 전역(웹 서비스) 매개 변수를 포함할 수 있습니다([여기서 자세히 보기](machine-learning-web-service-parameters.md)).

![][2]

줄임표를 클릭하여 전역 매개 변수 필드를 표시하고 숨깁니다. 이렇게 하면 하나 이상의 전역(웹 서비스) 매개 변수를 쉼표로 구분된 필드 및 값 목록으로 제공할 수 있습니다.

![][3]

웹 서비스 입력 또는 출력이 없는 작업과 같은 BES 작업의 다른 변형도 커넥터를 통해 사용할 수 있습니다.

## 재학습 설정

재학습 설정 작업을 사용하여 ML 모델의 일회성 또는 예약된 재학습을 설정할 수 있습니다. 커넥터에서 만든 배치 실행 작업과 함께 조합하여 웹 서비스의 모델을 학습 및 업데이트하는 단계를 완료할 수 있습니다. 이 워크플로에서는 커넥터를 두 번 사용합니다. 1. 첫 번째 커넥터는 모델을 재학습하고 출력을 반환하는 BES 작업을 실행하는 데 사용합니다. 이 실행의 출력은 새 모델에 대한 URL을 포함합니다(.ilearner). 또한 선택적으로 실험에서 설정한 경우 평가 모듈의 출력(csv 파일)에 대한 URL을 반환합니다. 다음 단계에서는 평가 모듈 출력의 데이터를 사용하여 웹 서비스에서 모델을 바꿀지 여부를 결정할 수 있습니다(예: 정확도 > 0.85). 1. 두 번째 커넥터는 재학습을 설정하는 데 사용됩니다. 첫 번째 커넥터의 출력에서 매개 변수를 사용하여 필요에 따라 업데이트 모델 조건을 확인하고 웹 서비스를 새로 학습한 모델로 업데이트합니다. * 예를 들어 학습된 모델 URL 필드에 `@{body('besconnector').Results.output2.FullURL}`을 입력하여 새로 학습된 모델에 대한 URL로 BES 작업의 출력에 액세스할 수 있습니다. 여기서는 학습 실험의 웹 서비스 출력을 output2라고 한다고 가정합니다. * 리소스 이름의 경우 예측 실험에 저장된 학습 모델의 전체 이름을 사용합니다(예: MyTrainedModel [trained model]). * 평가 결과 키 필드의 경우 학습 실험의 평가 모듈 출력에 반환된 모든 매개 변수를 입력할 수 있습니다(포함된 경우). Azure 기계 학습 스튜디오에서 학습 실험의 평가 모듈 결과를 시각화하여 사용 가능한 매개 변수 목록을 볼 수 있습니다. 분류 실험의 경우 여기에는 정확도, 정밀도, 재현율, F-Score, AUC, 평균 로그 손실 및 학습 로그 손실이 포함됩니다.

![][4]
 
### 예약된 재학습
 
논리 앱 트리거를 사용하여 일정에 따라 실행하도록 커넥터를 설정할 수 있습니다. 이를 통해 새 데이터가 도착하면 정기적으로 모델을 다시 학습할 수 있습니다. BES 작업은 모델을 다시 학습하고 재학습 작업은 재학습이 완료되면 모델을 업데이트합니다.
 
![][5]
 
## 커넥터 출력 
 
**BES**: 배치 작업이 성공적으로 완료되면 커넥터 출력에 각 웹 서비스 출력에 대한 다음 정보가 포함됩니다.
 
 ![][6]
 
웹 서비스 출력이 포함되지 않은 경우 사용할 수 없습니다(예: 기록기 모듈을 사용하여 스튜디오의 실험에서 DB에 기록하는 경우).

**재학습**: 재학습이 성공적으로 완료되면 출력에 다음 정보가 포함됩니다.

![][7]

## 요약

논리 앱용 Azure ML 커넥터를 사용하여 일괄 처리 점수 매기기를 실행할 수 있으며 재학습 작업이 필요 시 또는 반복되는 일정에 따라 실행되도록 할 수 있습니다. 이 두 작업을 함께 사용하면 코드를 작성하지 않고도 데이터 점수 매기기 및 재학습, 평가 및 웹 서비스 모델 업데이트를 자동으로 수행할 수 있습니다.

 <!--Image references-->
[1]: ./media/app-service-logic-connector-azureml/img1.png
[2]: ./media/app-service-logic-connector-azureml/img2.png
[3]: ./media/app-service-logic-connector-azureml/img3.png
[4]: ./media/app-service-logic-connector-azureml/img4.png
[5]: ./media/app-service-logic-connector-azureml/img5.png
[6]: ./media/app-service-logic-connector-azureml/img6.png
[7]: ./media/app-service-logic-connector-azureml/img7.png

<!---HONumber=AcomDC_1217_2015-->