---
title: Machine Learning Studio FAQ(질문과 대답)
titleSuffix: Azure Machine Learning Studio
description: 'Azure Machine Learning Studio: 간소화된 예측 모델링에 대한 클라우드 서비스의 요금 청구, 기능 및 제한 사항을 다루는 FAQ'
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: ericlicoding
ms.author: amlstudiodocs
ms.custom: seodec18
ms.date: 06/02/2017
ms.openlocfilehash: 8c381ca02dcfb194b2807735415ef894d72f74fa
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/31/2019
ms.locfileid: "55462282"
---
# <a name="azure-machine-learning-studio-faq-capabilities-and-limitations"></a>Azure Machine Learning Studio FAQ: 기능 및 제한 사항
Azure Machine Learning, 예측 모델 개발을 위한 클라우드 서비스 및 웹 서비스를 통한 운용성 솔루션에 대한 질문(FAQ)과 해당하는 대답입니다. 

## <a name="general-questions"></a>일반적인 질문
**Machine Learning Studio란 무엇인가요?**

Machine Learning Studio는 웹 브라우저를 사용하여 액세스하는 끌어서 놓기 캔버스 환경입니다. Machine Learning Studio는 실험 형태의 종단 간 데이터 과학 워크플로를 구성할 수 있는 시각적 컴퍼지션 인터페이스로 모듈 팔레트를 호스트합니다.

Machine Learning Studio에 대한 자세한 내용은 [Machine Learning Studio란 무엇인가요?](what-is-ml-studio.md)

**Azure Machine Learning API 서비스란 무엇인가요?**

Machine Learning API 서비스를 통해 Machine Learning Studio에 기본 제공되는 것과 같은 예측 모델을 확장성 있는 내결함성 웹 서비스로 배포할 수 있습니다. Machine Learning API 서비스로 만든 웹 서비스는, 외부 애플리케이션과 예측 분석 모델 간의 통신용 인터페이스를 제공하는 REST API입니다.

자세한 내용은 [Azure Machine Learning 웹 서비스 사용 방법](consume-web-services.md)을 참조하세요.

**내 클래식 웹 서비스는 어디에 나열되나요? 내 새 Azure Resource Manager 기반 웹 서비스는 어디에 나열되나요?**

클래식 배포 모델을 사용하여 만든 웹 서비스와 새로운 Azure Resource Manager 배포 모델을 사용하여 만든 웹 서비스가 [Microsoft Azure Machine Learning 웹 서비스](https://services.azureml.net/) 포털에 나열됩니다.

또한 클래식 웹 서비스는 **웹 서비스** 탭의 [Machine Learning Studio](http://studio.azureml.net)에 나열됩니다.

## <a name="azure-machine-learning-questions"></a>Azure Machine Learning 질문
**Azure Machine Learning 웹 서비스란?**

Machine Learning 웹 서비스는 애플리케이션과 Machine Learning 워크플로 점수 매기기 모델 간의 인터페이스를 제공합니다. 외부 애플리케이션에서 Azure Machine Learning을 사용하여 Machine Learning 워크플로 점수 매기기 모델과 실시간으로 통신할 수 있습니다. Machine Learning 웹 서비스에 대한 호출은 외부 애플리케이션에 예측 결과를 반환합니다. 웹 서비스를 호출하려면 웹 서비스를 배포할 때 만들어진 API 키를 전달합니다. Machine Learning 웹 서비스는 웹 프로그래밍 프로젝트에 일반적으로 사용되는 아키텍처인 REST를 기반으로 합니다.

Azure Machine Learning에는 다음 두 가지 유형의 웹 서비스가 있습니다.

* RRS(요청-응답 서비스): 대기 시간이 짧고 확장성 있는 서비스로, Machine Learning Studio를 사용하여 생성 및 배포되는 상태 비저장 모델에 대한 인터페이스를 제공합니다.
* BES(일괄 처리 실행 서비스): 데이터 레코드의 점수를 일괄적으로 매기는 비동기 서비스입니다.

REST API를 사용하고 웹 서비스에 액세스하는 여러 가지 방법이 있습니다. 예를 들어, 웹 서비스를 배포할 때 생성된 샘플 코드를 사용하여 C#, R 또는 Python에서 애플리케이션을 작성할 수 있습니다.

샘플 코드는 다음에서 제공됩니다.
- Azure Machine Learning 웹 서비스 포털에서 웹 서비스에 대한 사용 페이지
- Machine Learning Studio에서 웹 서비스 대시보드에서 API 도움말 페이지

사용자용으로 만들고 Machine Learning Studio의 웹 서비스 대시보드에서 사용 가능한 샘플 Microsoft Excel 통합 문서도 사용할 수 있습니다.

**Azure Machine Learning에 대한 주요 업데이트는 무엇인가요?**

최신 업데이트는 [Azure Machine Learning의 새로운 기능](../../active-directory/fundamentals/whats-new.md)을 참조하세요.

## <a name="import-and-export-data-for-machine-learning"></a>Machine Learning에 대한 데이터 가져오기 및 내보내기
**Machine Learning에서 지원하는 데이터 원본은 무엇인가요?**

세 가지 방법으로 Machine Learning Studio 실험으로 데이터를 다운로드할 수 있습니다.

- 데이터 세트로 로컬 파일 업로드
- 모듈을 사용하여 클라우드 데이터 서비스에서 데이터 가져오기
- 다른 실험에서 저장된 데이터 세트 가져오기

지원된 파일 형식에 대한 자세한 내용은 [Machine Learning Studio로 학습 데이터 가져오기](import-data.md)를 참조하세요.

### <a id="ModuleLimit"></a>모듈에 대해 설정할 수 있는 데이터 집합의 크기는 어느 정도인가요?
Machine Learning Studio의 모듈은 일반적인 사용 사례의 경우 최대 10GB 숫자 데이터의 데이터 세트를 지원합니다. 모듈에서 둘 이상의 입력을 사용하는 경우에는 모든 입력 크기의 합계 값이 10GB입니다. Hive 또는 Azure SQL Database 쿼리를 사용하여 더 큰 데이터 세트를 샘플링하거나 수집하기 전에 Learning by Counts 전처리를 사용할 수 있습니다.  

다음 데이터 형식은 기능 정규화 중에 확장할 수 있으며 10GB 미만으로 제한됩니다.

* 스파스
* 범주
* 문자열
* 이진 데이터

다음 모듈은 10GB 미만의 데이터 세트로 제한됩니다.

* Recommender 모듈
* SMOTE(Synthetic Minority Oversampling Technique) 모듈
* 스크립팅 모듈: R, Python, SQL
* 출력 데이터 크기가 입력 데이터 크기보다 클 수 있는 모듈(예: Join 또는 Feature Hashing)
* Cross-validation, Tune Model Hyperparameters, Ordinal Regression 및 One-vs-All Multiclass(반복 횟수가 매우 많은 경우)

### <a id="UploadLimit"></a>데이터 업로드에 대한 제한 사항은 무엇인가요?
몇 GB보다 큰 데이터 세트의 경우 로컬 파일에서 직접 업로드하지 않고 Azure Storage 또는 Azure SQL Database에 데이터를 업로드하거나 Azure HDInsight를 사용합니다.

**Amazon S3에서 데이터를 읽을 수 있나요?**

소량의 데이터를 HTTP URL을 통해 노출하려는 경우 [데이터 가져오기][import-data] 모듈을 사용할 수 있습니다. 전송할 데이터가 많은 경우에는 먼저 Azure Storage로 전송한 다음 [데이터 가져오기][import-data] 모듈을 사용하여 실험으로 가져옵니다.
<!--

<SEE CLOUD DS PROCESS>
-->

**기본 제공 이미지 입력 기능이 있나요?**

이미지 입력 기능은 [이미지 가져오기][image-reader] 참조에서 확인할 수 있습니다.

## <a name="modules"></a>모듈
**원하는 알고리즘, 데이터 원본, 데이터 형식, 데이터 변환 작업이 Azure Machine Learning Studio에 없습니다. 어떻게 해야 하나요?**

[사용자 피드백 포럼](https://go.microsoft.com/fwlink/?LinkId=404231)으로 이동하여 Microsoft에서 추적 중인 기능 요청을 확인할 수 있습니다. 원하는 기능이 이미 요청된 경우 해당 요청에 투표할 수 있습니다. 원하는 기능이 없는 경우 새로운 요청을 만드세요. 이 포럼에서 요청의 상태를 확인할 수도 있습니다. Microsoft는 이 목록을 긴밀하게 추적하여 기능의 사용 가능성 상태를 자주 업데이트합니다. R 및 Python에 대한 기본적인 지원 외에 필요에 따라 사용자 지정 변환을 만들 수 있습니다.

**기존 코드를 Machine Learning Studio로 가져올 수 있나요?**

예, Machine Learning Studio로 기존 R 또는 Python 코드를 가져와서, Azure Machine Learning 학습자와 동일한 실험에서 실행하고 Azure Machine Learning을 통해 솔루션을 웹 서비스로 배포할 수 있습니다. 자세한 내용은 [R을 사용하여 실험 확장](extend-your-experiment-with-r.md) 및 [Azure Machine Learning Studio에서 Python 기계 학습 스크립트 실행](execute-python-scripts.md)을 참조하세요.

**[PMML](http://en.wikipedia.org/wiki/Predictive_Model_Markup_Language)과 유사한 기능을 사용하여 모델을 정의할 수 있나요?**

아니요, PMML(Predictive Model Markup Language)은 지원되지 않습니다. 모듈을 정의하려면 사용자 지정 R 및 Python 코드를 사용할 수 있습니다.

**실험에서 병렬로 실행할 수 있는 모듈은 몇 개인가요?**  

실험에서 동시에 최대 네 개의 모듈을 실행할 수 있습니다.

## <a name="data-processing"></a>데이터 처리
**실험 내에서 대화형으로 데이터를 시각화하는 기능(R 시각화 외)이 있나요?**

모듈의 출력을 클릭하여 데이터를 시각화하고 통계를 가져옵니다.

**브라우저에서 결과 또는 데이터를 미리 볼 때 행 및 열 수가 제한됩니다. 그 이유는 무엇일까요?**

많은 양의 데이터를 브라우저에 전송할 수 있으므로 Machine Learning Studio의 속도 저하를 방지하기 위해 데이터 크기가 제한됩니다. 모든 데이터/결과를 시각화하려면, 데이터를 다운로드하고 Excel 또는 다른 도구를 사용하는 것이 좋습니다.

## <a name="algorithms"></a>알고리즘
**Machine Learning Studio에서 지원되는 기존 알고리즘은 무엇인가요?**

Machine Learning Studio는 Microsoft Research에서 개발된 확장 가능한 고급 의사 결정 트리, Bayesian 권장 시스템, 심층적인 신경망, 의사 결정 정글 등 최신 알고리즘을 제공합니다. Vowpal Wabbit과 같은 확장성 있는 오픈 소스 기계 학습 패키지도 포함되어 있습니다. Machine Learning Studio는 여러 클래스의 이진 분류, 회귀 및 클러스터링을 위한 기계 학습 알고리즘을 지원합니다. 전체 목록은 [Machine Learning 모듈][machine-learning-modules]을 참조하세요.

**데이터에 사용할 올바른 Machine Learning 알고리즘이 자동으로 제안되나요?**

아니요, 그러나 Machine Learning Studio에서 각 알고리즘의 결과를 비교하여 문제에 적합한 알고리즘을 확인할 수 있는 다양한 방법이 있습니다.

**제공된 알고리즘에서 적합한 알고리즘 하나를 선택하는 데 대한 지침이 있나요?**

 [알고리즘을 선택하는 방법](algorithm-choice.md)를 참조하세요.

**제공되는 알고리즘은 R 또는 Python으로 작성되었나요?**

아니요, 이러한 알고리즘은 더 나은 성능을 제공하기 위해 주로 컴파일된 언어로 작성됩니다.

**제공되는 알고리즘에 대한 세부 정보가 있나요?**

설명서에는 알고리즘에 대한 일부 정보가 나와 있으며, 용도에 맞게 알고리즘을 최적화할 수 있도록 조정할 매개 변수에 대한 설명이 있습니다.  

**온라인 학습을 지원하나요?**

아니요, 현재는 프로그래밍 방식의 재학습만 지원됩니다.

**기본 제공 모듈을 사용하여 신경망 모델의 계층을 시각화할 수 있나요?**

 아니요.

**C# 또는 일부 다른 언어로 모듈을 만들 수 있나요?**

현재는 R을 사용해서만 새 사용자 지정 모듈을 만들 수 있습니다.

## <a name="r-module"></a>R 모듈
**Machine Learning Studio에서 사용 가능한 R 패키지는 무엇인가요?**

Machine Learning Studio는 현재 400개가 넘는 CRAN R 패키지를 지원하며 다음은 모든 포함된 패키지의 [현재 목록](http://az754797.vo.msecnd.net/docs/RPackages.xlsx) 입니다. 이 목록을 직접 검색하는 방법에 대해 알아보려면 [R을 사용하여 실험 확장](extend-your-experiment-with-r.md) 을 참조하세요. 원하는 패키지가 이 목록에 없는 경우 [사용자 피드백 포럼](https://go.microsoft.com/fwlink/?LinkId=404231)에서 패키지 이름을 제공해 주세요.

**사용자 지정 R 모듈을 빌드할 수 있나요?**

예, 자세한 내용은 [Azure Machine Learning에서 사용자 지정 R 모듈 작성](custom-r-modules.md)을 참조하세요.

**R에 대한 REPL 환경이 있나요?**

아니요, R에 대한 REPL(Read-Eval-Print-Loop) 환경은 스튜디오에 없습니다.

## <a name="python-module"></a>Python 모듈
**사용자 지정 Python 모듈을 빌드할 수 있나요?**

현재는 안 되지만, 하나 이상의 [Python 스크립트 실행][python] 모듈을 사용하여 동일한 결과를 낼 수 있습니다.

**Python에 대한 REPL 환경이 있나요?**

Machine Learning Studio에서 Jupyter 노트북을 사용할 수 있습니다. 자세한 내용은 [Azure Machine Learning Studio에 Jupyter 노트북 도입](http://blogs.technet.com/b/machinelearning/archive/2015/07/24/introducing-jupyter-notebooks-in-azure-ml-studio.aspx)을 참조하세요.

## <a name="web-service"></a>웹 서비스

**Azure Machine Learning 모델 프로그래밍 방식을 다시 학습하려면 어떻게 하나요?**

API 다시 학습을 사용합니다. 자세한 내용은 [프로그래밍 방식으로 Machine Learning 모델 다시 학습](retrain-models-programmatically.md)을 참조하세요. 샘플 코드는 [Microsoft Azure Machine Learning 다시 학습 데모](https://azuremlretrain.codeplex.com/)에서도 제공됩니다.

**모델을 로컬로 배포하거나 인터넷에 연결하지 않고 애플리케이션에서 배포할 수 있나요?**

 아니요.

**모든 웹 서비스에 예상되는 기준 대기 시간이 있나요?**

[Azure 구독 제한](../../azure-subscription-service-limits.md)을 참조하세요.

**어떤 경우에 내 예측 모델을 일괄 처리 실행 서비스로 실행하고 어떤 경우에 요청-응답 웹 서비스를 실행하나요?**

RRS(요청-응답 서비스)는 대기 시간이 짧고, 확장성이 높은 웹 서비스로, 실험 환경에서 생성하여 배포하는 상태 비저장 모델에 대한 인터페이스를 제공하는 데 사용됩니다. BES(Batch 실행 서비스)는 데이터 레코드의 배치에 대한 점수를 비동기적으로 계산하는 서비스입니다. BES에 대한 입력은 RRS에서 사용하는 데이터 입력과 유사합니다. 가장 중요한 차이는 BES에서는 Azure Blob Storage 및 Azure Table Storage, Azure SQL Database, HDInsight(Hive 쿼리), HTTP 소스 등의 다양한 소스에서 레코드 블록을 읽는다는 점입니다. 자세한 내용은 [Azure Machine Learning 웹 서비스 사용 방법](consume-web-services.md)을 참조하세요.

**배포된 웹 서비스의 모델을 업데이트하려면 어떻게 해야 하나요?**

이미 배포된 서비스의 예측 모델을 업데이트하려면 학습된 모델을 작성하여 저장하는 데 사용된 실험을 수정하고 다시 실행합니다. 새 버전의 학습된 모델이 확보된 후에는, Machine Learning Studio에서 웹 서비스를 업데이트할지 여부를 묻습니다. 배포된 웹 서비스를 업데이트하는 방법에 대한 자세한 내용은 [Machine Learning 웹 서비스 배포](publish-a-machine-learning-web-service.md)를 참조하세요.

다시 학습 API도 사용할 수 있습니다.
자세한 내용은 [프로그래밍 방식으로 Machine Learning 모델 다시 학습](retrain-models-programmatically.md)을 참조하세요. 샘플 코드는 [Microsoft Azure Machine Learning 다시 학습 데모](https://azuremlretrain.codeplex.com/)에서도 제공됩니다.

**프로덕션에 배포된 웹 서비스를 모니터링하려면 어떻게 해야 하나요?**

예측 모델을 배포한 후에는 Azure Machine Learning 웹 서비스 포털에서 모니터링할 수 있습니다. 배포된 각 서비스에는 고유한 대시보드가 있어, 이 대시보드에서 해당 서비스에 대한 모니터링 정보를 볼 수 있습니다. 배포된 웹 서비스를 관리하는 방법에 대한 자세한 내용은 [Azure Machine Learning 웹 서비스 포털을 사용하여 웹 서비스 관리](manage-new-webservice.md) 및 [Azure Machine Learning 작업 영역 관리](manage-workspace.md)를 참조하세요.

**RRS/BES의 출력을 볼 수 있는 곳이 있나요?**

RRS의 경우 웹 서비스 응답은 일반적으로 결과를 보는 위치입니다. Azure Blob Storage에 기록도 가능합니다. BES의 경우 기본적으로 출력은 Blob에 기록됩니다. [데이터 내보내기][export-data] 모듈을 사용하여 출력을 데이터베이스 또는 테이블에 기록할 수도 있습니다.

**Machine Learning Studio에서 만든 모델에서만 웹 서비스를 만들 수 있나요?**

아니요. Jupyter Notebook 및 RStudio를 사용하여 직접 웹 서비스를 만들 수도 있습니다.

**오류 코드에 대한 정보를 어디서 찾을 수 있습니까?**

오류 코드 목록 및 설명은 [Machine Learning 모듈 오류 코드](https://msdn.microsoft.com/library/azure/dn905910.aspx) 를 참조하세요.

**웹 서비스의 확장성이란 무엇인가요?**

현재 기본 엔드포인트는 엔드포인트당 20개의 동시 RRS 요청으로 프로비전됩니다. [웹 서비스 확장](scaling-webservice.md)에 설명된 것처럼 엔드포인트당 동시 요청 수를 200개까지 확장할 수 있으며 웹 서비스당 10,000개 엔드포인트로 각 웹 서비스를 확장할 수 있습니다. BES의 경우 각 엔드포인트는 한 번에 40개의 요청을 처리할 수 있으며 40개를 초과하는 추가 요청은 큐에 대기됩니다. 이러한 큐에 대기 중인 요청은 큐에서 나옴과 동시에 자동으로 실행됩니다.

**R 작업은 노드 간에 분산되나요?**

 아니요.  

**학습에 사용할 수 있는 데이터의 양은 얼마인가요?**

Machine Learning Studio의 모듈은 일반적인 사용 사례의 경우 최대 10GB 숫자 데이터의 데이터 세트를 지원합니다. 모듈에서 둘 이상의 입력을 사용하는 경우에는 모든 입력에 대한 총 크기가 10GB입니다. Hive 쿼리 또는 Azure SQL Database 쿼리를 통하거나 수집 전에 [개수로 알아보기][counts] 모듈로 전처리하여 더 큰 데이터 세트를 샘플링할 수도 있습니다.  

다음 데이터 형식은 기능 정규화 중에 확장할 수 있으며 10GB 미만으로 제한됩니다.

* 스파스
* 범주
* 문자열
* 이진 데이터

다음 모듈은 10GB 미만의 데이터 세트로 제한됩니다.

* Recommender 모듈
* SMOTE(Synthetic Minority Oversampling Technique) 모듈
* 스크립팅 모듈: R, Python, SQL
* 출력 데이터 크기가 입력 데이터 크기보다 클 수 있는 모듈(예: Join 또는 Feature Hashing)
* Cross-Validate, Tune Model Hyperparameters, Ordinal Regression 및 One-vs-All Multiclass(반복 횟수가 매우 많은 경우)

몇 GB보다 큰 데이터 세트의 경우 로컬 파일에서 직접 업로드하지 않고 Azure Storage 또는 Azure SQL Database에 데이터를 업로드하거나 HDInsight를 사용합니다.

**벡터 크기 제한이 있나요?**

행 및 열은 각각 .NET 제한인 Max Int: 2,147,483,647로 제한됩니다.

**웹 서비스를 실행하는 가상 머신의 크기를 조정할 수 있나요?**

 아니요.  

## <a name="security-and-availability"></a>보안 및 사용 가능성
**웹 서비스에 대한 http 엔드포인트에 기본적으로 액세스할 수 있는 사람은 누구인가요? 엔드포인트에 대한 액세스는 어떻게 제한하나요?**

웹 서비스가 배포된 후 해당 서비스에 대한 기본 엔드포인트가 만들어집니다. 기본 엔드포인트는 API 키를 사용하여 호출할 수 있습니다. 웹 서비스 포털에서 또는 웹 서비스 관리 API를 사용하여 프로그래밍 방식으로 해당 고유 키로 엔드포인트를 더 추가할 수 있습니다. 액세스 키는 웹 서비스를 호출하는 데 필요합니다. 자세한 내용은 [Azure Machine Learning 웹 서비스 사용 방법](consume-web-services.md)을 참조하세요.

**내 Azure Storage 계정을 찾을 수 없는 경우 어떻게 되나요?**

Machine Learning Studio는 워크플로를 실행할 때 사용자가 제공한 Azure Storage 계정을 기반으로 중간 데이터를 저장합니다. 이 저장소 계정은 작업 영역을 만들 때 Machine Learning Studio에 제공됩니다. 작업 영역을 만든 후 저장소 계정이 삭제되고 더 이상 찾을 수 없는 경우에는 해당 작업 영역의 작동이 중지되고 작업 영역의 모든 실험이 실패합니다.

저장소 계정을 실수로 삭제한 경우 삭제한 저장소 계정과 동일한 지역과 동일한 이름으로 저장소 계정을 다시 만듭니다. 그 후 액세스 키를 다시 동기화하세요.

**저장소 계정 액세스 키가 동기화되지 않은 경우 어떻게 되나요?**

Machine Learning Studio는 워크플로를 실행할 때 사용자가 제공한 Azure Storage 계정을 기반으로 중간 데이터를 저장합니다. 이 저장소 계정은 작업 영역을 만들 때 Machine Learning Studio에 제공되고 액세스 키가 해당 작업 영역과 연결됩니다. 액세스 키가 변경되면 작업 영역이 만들어진 후에 작업 영역은 저장소 계정에 더 이상 액세스할 수 없습니다. 작동이 중지되고 해당 작업 영역의 모든 실험이 실패합니다.

저장소 계정 액세스 키를 변경한 경우에는, Azure Portal을 사용하여 작업 영역에서 액세스 키를 다시 동기화합니다.  


## <a name="billing-questions"></a>대금 청구 관련 질문

대금 청구 및 가격 책정 정보는 [Machine Learning 가격](https://azure.microsoft.com/pricing/details/machine-learning/)을 참조하세요.


<!-- Module References -->
[image-reader]: https://msdn.microsoft.com/library/azure/893f8c57-1d36-456d-a47b-d29ae67f5d84/
[join]: https://msdn.microsoft.com/library/azure/124865f7-e901-4656-adac-f4cb08248099/
[machine-learning-modules]: https://msdn.microsoft.com/library/azure/6d9e2516-1343-4859-a3dc-9673ccec9edc/
[partition-and-sample]: https://msdn.microsoft.com/library/azure/a8726e34-1b3e-4515-b59a-3e4a475654b8/
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
[export-data]: https://msdn.microsoft.com/library/azure/7A391181-B6A7-4AD4-B82D-E419C0D6522C
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
[python]: https://msdn.microsoft.com/library/azure/CDB56F95-7F4C-404D-BDE7-5BB972E6F232
[counts]: https://msdn.microsoft.com/library/azure/dn913056.aspx
