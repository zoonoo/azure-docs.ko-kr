---
title: "Machine Learning 웹 서비스에 연결 | Microsoft Docs"
description: "C# 또는 Python을 사용하는 경우 권한 부여 키를 사용하여 Azure Machine Learning 웹 서비스에 연결합니다."
services: machine-learning
documentationcenter: 
author: garyericson
manager: jhubbard
editor: cgronlun
ms.assetid: 59b07bab-b60f-48c4-a385-a162e50ec7c2
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/10/2016
ms.author: garye
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 56bee9c0a2da3e522290c2274b6d0301defe3f71


---
# <a name="connect-to-an-azure-machine-learning-web-service"></a>Azure 기계 학습 웹 서비스에 연결
Azure Machine Learning 개발자 환경은 실시간 또는 일괄 처리 모드로 입력 데이터에서 예측하는 웹 서비스 API입니다. Azure Machine Learning 스튜디오를 사용하여 예측을 만들고 Azure Machine Learning 웹 서비스를 배포할 수 있습니다.

[!INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

Machine Learning 스튜디오를 사용하여 Machine Learning 웹 서비스를 만들고 배포하는 방법에 대한 자세한 내용은 다음을 참조하세요.

* 기계 학습 스튜디오에서 실험을 만드는 방법에 대한 자습서는 [첫 번째 실험 만들기](machine-learning-create-experiment.md)를 참조하세요.
* 웹 서비스를 배포하는 방법에 대한 자세한 내용은 [Machine Learning 웹 서비스 배포](machine-learning-publish-a-machine-learning-web-service.md)를 참조하세요.
* 기계 학습에 대한 자세한 내용은 [기계 학습 설명서 센터](https://azure.microsoft.com/documentation/services/machine-learning/)를 참조하세요.

## <a name="azure-machine-learning-web-service"></a>Azure Machine Learning 웹 서비스
Azure Machine Learning 웹 서비스를 통해 외부 응용 프로그램에서 Machine Learning 워크플로 점수 매기기 모델과 실시간으로 통신할 수 있습니다. Machine Learning 웹 서비스 호출은 외부 응용 프로그램에 예측 결과를 반환합니다. Machine Learning 웹 서비스를 호출하려면 예측을 배포할 때 만들어진 API 키를 전달합니다. Machine Learning 웹 서비스는 웹 프로그래밍 프로젝트에 일반적으로 사용되는 아키텍처인 REST를 기반으로 합니다.

Azure 기계 학습에는 다음 두 가지 유형의 서비스가 있습니다.

* RRS(요청-응답 서비스) - 대기 시간이 짧고 확장성이 높은 서비스로, 기계 학습 스튜디오에서 생성 및 배포되는 상태 비저장 모델에 대한 인터페이스를 제공합니다.
* BES(일괄 처리 실행 서비스) – 데이터 레코드의 점수를 일괄적으로 매기는 비동기 서비스입니다.

Machine Learning 웹 서비스에 대한 자세한 내용은 [Machine Learning 웹 서비스 배포](machine-learning-publish-a-machine-learning-web-service.md)를 참조하세요.

## <a name="get-an-azure-machine-learning-authorization-key"></a>Azure 기계 학습 권한 부여 키 가져오기
실험을 배포할 때 웹 서비스에 API 키가 생성됩니다. 여러 위치에서 키를 검색할 수 있습니다.

## <a name="from-the-microsoft-azure-machine-learning-web-services-portal"></a>Microsoft Azure Machine Learning 웹 서비스 포털에서
[Microsoft Azure Machine Learning 웹 서비스](https://services.azureml.net) 포털에 로그인합니다.

새 Machine Learning 웹 서비스에 대한 API 키를 가져오려면:

1. Azure Machine Learning 웹 서비스 포털의 최상위 메뉴에서 **웹 서비스**를 클릭합니다.
2. 키를 검색하려는 웹 서비스를 클릭합니다.
3. 위쪽 메뉴에서 **사용**을 클릭합니다.
4. **기본 키**를 복사하고 저장합니다.

클래식 Machine Learning 웹 서비스에 대한 API 키를 가져오려면:

1. Azure Machine Learning 웹 서비스 포털의 최상위 메뉴에서 **클래식 웹 서비스**를 클릭합니다.
2. 사용하고 있는 웹 서비스를 클릭합니다.
3. 키를 검색하려는 끝점을 클릭합니다.
4. 위쪽 메뉴에서 **사용**을 클릭합니다.
5. **기본 키**를 복사하고 저장합니다.

## <a name="classic-web-service"></a>기존 웹 서비스
 Machine Learning 스튜디오 또는 Azure Portal에서 클래식 웹 서비스에 대한 키를 검색할 수도 있습니다.

### <a name="machine-learning-studio"></a>기계 학습 스튜디오
1. 기계 학습 스튜디오의 왼쪽에서 **웹 서비스** 를 클릭합니다.
2. 웹 서비스를 클릭합니다. **API 키**는 **대시보드** 탭에 있습니다.

### <a name="azure-portal"></a>Azure 포털
1. 왼쪽에서 **기계 학습** 을 클릭합니다.
2. 웹 서비스의 위치에 있는 작업 영역을 클릭합니다.
3. **웹 서비스**를 클릭합니다.
4. 웹 서비스를 클릭합니다.
5. 끝점을 클릭합니다. "API 키"는 오른쪽 아래에 있습니다.

## <a name="a-idconnectaconnect-to-a-machine-learning-web-service"></a><a id="connect"></a>Machine Learning 웹 서비스에 연결
HTTP 요청 및 응답을 지원하는 모든 프로그래밍 언어를 사용하여 Machine Learning 웹 서비스에 연결할 수 있습니다. Machine Learning 웹 서비스 도움말 페이지에서 C#, Python 및 R로 작성된 예제를 볼 수 있습니다.

**Machine Learning API 도움말** 웹 서비스를 배포하면 Machine Learning API 도움말이 생성됩니다. [Azure 기계 학습 연습 - 웹 서비스 배포](machine-learning-walkthrough-5-publish-web-service.md)를 참조하세요.
Machine Learning API 도움말에는 예측 웹 서비스에 대한 세부 정보가 포함되어 있습니다.

1. 사용하고 있는 웹 서비스를 클릭합니다.
2. API 도움말 페이지를 보려는 끝점을 클릭합니다.
3. 위쪽 메뉴에서 **사용**을 클릭합니다.
4. 응답 간 또는 Batch 실행 끝점에서 **API 도움말 페이지**를 클릭합니다.

**새 웹 서비스에 대한 Machine Learning API 도움말을 보려면**

Azure Machine Learning 웹 서비스 포털에서:

1. 최상위 메뉴에서 **웹 서비스** 를 클릭합니다.
2. 키를 검색하려는 웹 서비스를 클릭합니다.

**사용** 을 클릭하여 C#, R 및 Python으로 요청-응답 및 배치 실행 서비스와 샘플 코드에 대한 URI를 가져옵니다.

**Swagger API**를 클릭하여 제공된 URI에서 호출된 API에 대한 Swagger 기반 설명서를 가져옵니다.

### <a name="c-sample"></a>C# 샘플
Machine Learning 웹 서비스에 연결하려면 ScoreData를 전달하는 **HttpClient**를 사용합니다. ScoreData는 ScoreData를 나타내는 수치의 n 차원 벡터인 FeatureVector를 포함합니다. API 키로 기계 학습 서비스를 인증합니다.

Machine Learning 웹 서비스에 연결하려면 **Microsoft.AspNet.WebApi.Client** NuGet 패키지를 설치해야 합니다.

**Visual Studio에서 Microsoft.AspNet.WebApi.Client NuGet 설치**

1. UCI: Adult 2 클래스 데이터 집합에서 데이터 집합 다운로드 웹 서비스를 게시합니다.
2. **도구** > **NuGet 패키지 관리자** > **패키지 관리자 콘솔**을 클릭합니다.
3. **Install-Package Microsoft.AspNet.WebApi.Client**를 선택합니다.

**코드 샘플을 실행하려면**

1. 기계 학습 샘플 컬렉션의 “샘플 1: UCI: Adult 2 클래스 데이터 집합에서 데이터 집합 다운로드” 실험 부분을 게시합니다.
2. 웹 서비스에서 가져온 키로 apiKey를 할당합니다. 위의 **Azure 기계 학습 권한 부여 키 가져오기** 를 참조하세요.
3. 요청 URI로 serviceUri를 할당합니다.

### <a name="python-sample"></a>Python 샘플
Machine Learning 웹 서비스에 연결하려면 ScoreData를 전달하는 **urllib2** 라이브러리를 사용합니다. ScoreData는 ScoreData를 나타내는 수치의 n 차원 벡터인 FeatureVector를 포함합니다. API 키로 기계 학습 서비스를 인증합니다.

**코드 샘플을 실행하려면**

1. 기계 학습 샘플 컬렉션의 “샘플 1: UCI: Adult 2 클래스 데이터 집합에서 데이터 집합 다운로드” 실험 부분을 배포합니다.
2. 웹 서비스에서 가져온 키로 apiKey를 할당합니다. 이 문서의 시작 부분 가까이에 있는 **Azure Machine Learning 권한 부여 키 가져오기** 섹션을 참조하세요.
3. 요청 URI로 serviceUri를 할당합니다.




<!--HONumber=Nov16_HO3-->


