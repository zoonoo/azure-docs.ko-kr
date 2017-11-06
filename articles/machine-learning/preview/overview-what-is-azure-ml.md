---
title: "Azure Machine Learning이란 무엇인가요? | Microsoft Docs"
description: "고급 분석 응용 프로그램을 클라우드 규모로 개발, 실험 및 배포하는 전문 데이터 과학자를 위한 통합 종단 간 데이터 과학 솔루션인 Azure Machine Learning 실험 및 모델 관리의 개요입니다."
services: machine-learning
author: haining
ms.author: haining
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: get-started-article
ms.date: 09/21/2017
ms.openlocfilehash: 5535433c478b989e255451a0bf882dfb8ba8f8fe
ms.sourcegitcommit: 3e3a5e01a5629e017de2289a6abebbb798cec736
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/27/2017
---
# <a name="what-is-azure-machine-learning"></a>Azure Machine Learning이란 무엇인가요?

Azure Machine Learning은 통합된 종단 간 데이터 과학 및 고급 분석 솔루션입니다. 이를 통해 데이터 과학자들은 클라우드 규모로 데이터를 준비하고, 실험을 개발하며, 모델을 배포할 수 있습니다.

Azure Machine Learning의 주요 구성 요소는 다음과 같습니다.
- Azure Machine Learning Workbench
- Azure Machine Learning 실험 서비스
- Azure Machine Learning 모델 관리 서비스
- Apache Spark용 Microsoft Machine Learning 라이브러리(MMLSpark 라이브러리)
- AI용 Visual Studio Code 도구

또한, 이러한 응용 프로그램 및 서비스는 크게 데이터 과학 프로젝트 개발 및 배포를 크게 가속화하는 데 기여합니다. 

![Azure Machine Learning 개](media/overview-what-is-azure-ml/aml-concepts.png)

## <a name="open-source-compatible"></a>오픈 소스 호환성

Azure Machine Learning은 오픈 소스 기술을 완벽하게 지원합니다. 다음 Machine Learning 프레임워크와 같이 수많은 오픈 소스 Python 패키지를 사용할 수 있습니다.

- [scikit-learn](http://scikit-learn.org/)
- [Tensorflow](https://www.tensorflow.org/)
- [Microsoft Cognitive 도구 키트](https://www.microsoft.com/en-us/cognitive-toolkit/)
- [Spark ML](https://spark.apache.org/docs/2.1.1/ml-pipeline.html)

Docker 컨테이너 및 Spark 클러스터와 같이 관리되는 환경에서 실험을 실행할 수 있습니다. 또한 [Azure의 GPU 사용 가상 컴퓨터](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-gpu)와 같은 고급 하드웨어를 사용하여 실행을 가속화할 수 있습니다.

Azure Machine Learning은 다음과 같은 오픈 소스 기술을 기반으로 빌드됩니다.

- [Jupyter Notebook](http://jupyter.org/)
- [Apache Spark](https://spark.apache.org/)
- [Docker](https://www.docker.com/)
- [Kubernetes](https://kubernetes.io/)
- [Python](https://www.python.org/)
- [Conda](https://conda.io/docs/)

또한 [Apache Spark를 위한 Microsoft Machine Learning 라이브러리](https://github.com/Azure/mmlspark) 및 Cognitive 도구 키트와 같은 Microsoft의 자체 오픈 소스 기술을 포함합니다.

또한 대규모 문제를 해결하기 위해 Microsoft에서 개발한 고급의 신뢰성이 확보된 Machine Learning 기술을 몇 가지 활용할 수 있습니다. 해당 기술들은 다음과 같은 다양한 Microsoft 제품에서 입증되었습니다.

- Windows
- Bing
- Xbox
- Office
- SQL Server

다음은 Azure Machine Learning에 포함된 Microsoft Machine Learning 기술 중 일부입니다.

- [PROSE](https://microsoft.github.io/prose/)(PROgram Synthesis using Examples, 예를 사용하는 프로그램 합성)
- [microsoftml](https://docs.microsoft.com/r-server/r/concept-what-is-the-microsoftml-package)
- [revoscalepy](https://docs.microsoft.com/r-server/python-reference/revoscalepy/revoscalepy-package)

## <a name="azure-machine-learning-workbench"></a>Azure Machine Learning Workbench
Azure Machine Learning Workbench는 Windows와 macOS 모두에서 지원되는 데스크톱 응용 프로그램 및 명령줄 도구입니다. 이를 사용하면 전체 데이터 과학 수명 주기를 통해 Machine Learning 솔루션을 관리할 수 있습니다.

- 데이터 수집 및 준비
- 모델 개발 및 실험 관리
- 다양한 대상 환경에서 모델 배포

다음은 Azure Machine Learning Workbench에서 제공하는 핵심 기능입니다.

- 예제를 통해 데이터 변환 논리를 학습할 수 있는 데이터 준비 도구
- UX 및 Python 코드를 통해 액세스할 수 있는 데이터 원본
- 시각적으로 생성된 데이터 준비 패키지를 호출하기 위한 Python SDK
- 기본 제공 Jupyter Notebook 서비스 및 클라이언트 UX
- 실행 기록 보기를 통한 실험 모니터링 및 관리
- Azure Active Directory를 통해 공유 및 공동 작업을 활성화하는 역할 기반 액세스 제어
- 네이티브 Git 통합으로 활성화된 각 실행 및 명시적 버전 제어에 대한 자동 프로젝트 스냅숏 
- 인기 있는 Python IDE와의 통합

자세한 내용은 다음 문서를 참조하세요.
- [데이터 준비 사용자 가이드](data-prep-user-guide.md)
- [Azure Machine Learning으로 Git 사용](using-git-ml-project.md)
- [Azure Machine Learning에서 Jupyter Notebook 사용](how-to-use-jupyter-notebooks.md)
- 로밍 및 공유
- [실행 기록 가이드](how-to-use-run-history-model-metrics.md)
- [IDE 통합](how-to-configure-your-IDE.md)

## <a name="azure-machine-learning-experimentation-service"></a>Azure Machine Learning 실험 서비스
실험 서비스는 Machine Learning 실험의 실행을 처리합니다. 또한 프로젝트 관리, Git 통합, 액세스 제어, 로밍 및 공유를 제공하여 Workbench를 지원합니다. 

쉬운 구성을 통해 다양한 계산 환경 옵션에서 실험을 실행할 수 있습니다.

- 로컬 네이티브
- 로컬 Docker 컨테이너
- 원격 VM의 Docker 컨테이너
- Azure에서 Spark 클러스터 스케일 아웃

실험 서비스는 재현 가능한 결과와 격리된 상태에서 스크립트를 실행할 수 있도록 가상 환경을 생성합니다. 실행 기록 정보를 기록하고 사용자에게 기록을 시각적으로 제공합니다. 실험 실행 외에 최상의 모델을 쉽게 선택할 수 있습니다. 

자세한 내용은 [실험 서비스 구성](experimentation-service-configuration.md)을 참조하세요.

## <a name="azure-machine-learning-model-management-service"></a>Azure Machine Learning 모델 관리 서비스

데이터 과학자와 개발 운영팀은 모델 관리 서비스를 사용하여 다양한 환경에 예측 모델을 배포할 수 있습니다. 모델 버전 및 계보는 교육 실행에서 배포까지 추적됩니다. 모델은 클라우드에서 저장, 등록 및 관리됩니다. 

간단한 CLI 명령을 사용하면 모델, 스크립트 점수 매기기 및 종속성을 Docker 이미지로 컨테이너화할 수 있습니다. 이러한 이미지는 Azure(Azure Container Registry)에 호스트된 사용자 고유의 Docker 레지스트리에 등록됩니다. 또한 다음 대상으로 안정적으로 배포될 수 있습니다.

- 로컬 컴퓨터
- 온-프레미스 서버
- 클라우드
- IoT Edge 장치

ACS(Azure Container Service)에서 실행 중인 Kubernetes는 클라우드 스케일 아웃 배포에 사용됩니다. 모델 실행 원격 분석은 시각적 분석을 위해 AppInsights로 캡처됩니다. 

모델 관리 서비스에 대한 자세한 내용은 [모델 관리 개요](model-management-overview.md)를 참조하세요.


## <a name="microsoft-machine-learning-library-for-apache-spark"></a>Apache Spark에 대한 Microsoft Machine Learning 라이브러리

[MMLSpark](https://github.com/Azure/mmlspark)(Apache Spark를 위한 Microsoft Machine Learning 라이브러리)는 Apache Spark를 위한 심층 학습 및 데이터 과학 도구를 제공하는 오픈 소스 Spark 패키지입니다. [Spark Machine Learning 파이프라인](https://spark.apache.org/docs/2.1.1/ml-pipeline.html)을 [Microsoft Cognitive 도구 키트](https://www.microsoft.com/en-us/cognitive-toolkit/) 및 [OpenCV](http://opencv.org/) 라이브러리와 통합합니다. 대용량 이미지 및 텍스트 데이터 집합을 위한 강력하고 확장성이 높은 예측 및 분석 모델을 빠르게 만들 수 있습니다. 중요 사항은 다음과 같습니다.

- Spark DataFrame으로 HDFS에서 이미지를 쉽게 수집
- OpenCV에서 변환을 사용한 전처리 이미지 데이터
- Microsoft Cognitive 도구 키트를 사용하여 미리 학습된 심층 신경망 네트워크를 사용한 이미지 기능화 
- 의료 엔터티 추출을 위한 Keras의 미리 학습된 양방향 LSTM 사용
- Azure에서 N 시리즈 GPU VM에 DNN 기반 이미지 분류 모델 학습
- 단일 변환기를 통해 SparkML의 기본 형식을 기반으로 하는 편리한 API를 사용하여 자유 형식 텍스트 데이터 기능화
- 데이터의 암시적 기능 생성을 통한 손쉬운 분류 및 회귀 모델 학습
- 인스턴스당 메트릭을 비롯한 다양한 평가 메트릭 계산

자세한 내용은 [Azure Machine Learning에서 MMLSpark 사용](how-to-use-mmlspark.md)을 참조하세요.

## <a name="visual-studio-code-tools-for-ai"></a>AI용 Visual Studio Code 도구
AI용 Visual Studio Code 도구는 심층 학습 및 AI 솔루션을 빌드, 테스트 및 배포하기 위한 Visual Studio Code의 확장입니다. 다음을 포함하는 Azure Machine Learning과의 많은 통합 요소들을 제공합니다.
- 교육 실행 및 로그된 메트릭의 성능을 표시하는 실행 기록 보기
- 사용자가 Microsoft Cognitive 도구 키트, TensorFlow, 다른 많은 심층 학습 프레임워크로 새 프로젝트를 찾아보고 부트스트랩할 수 있는 갤러리 보기 
- 실행할 스크립트에 대한 계산 대상을 선택할 수 있는 탐색기 보기
 

## <a name="what-are-the-machine-learning-options-from-microsoft"></a>Microsoft의 Machine Learning 옵션은 무엇입니까?
Azure Machine Learning 외에도 Machine Learning 모델을 빌드, 배포 및 관리하는 Azure의 옵션이 다양하게 있습니다. 
* SQL Server의 Microsoft Machine Learning 서비스
* Microsoft Machine Learning 서버
* 데이터 과학 가상 컴퓨터
* HDInsight의 Spark MLLib
* 일괄 처리 AI 교육 서비스
* Microsoft Cognitive 도구 키트
* Microsoft Cognitive Services


### <a name="microsoft-machine-learning-services-in-sql-server"></a>SQL Server의 Microsoft Machine Learning 서비스
[Microsoft Machine Learning 서비스](https://docs.microsoft.com/sql/advanced-analytics/r/r-services)를 통해 R 또는 Python을 사용하여 Machine Learning 모델을 실행, 학습 및 배포할 수 있습니다. 온-프레미스 및 SQL Server 데이터베이스에 있는 데이터를 사용할 수 있습니다. 

온-프레미스 또는 Microsoft SQL Server 내부에 모델을 학습 또는 배포해야 할 때 Microsoft Machine Learning 서비스를 사용합니다. Machine Learning 서비스로 빌드된 모델은 Azure Machine Learning 모델 관리를 사용하여 배포할 수 있습니다. 

### <a name="microsoft-machine-learning-server"></a>Microsoft Machine Learning 서버 
[Microsoft Machine Learning Server](https://docs.microsoft.com/sql/advanced-analytics/r/r-server-standalone)는 R 및 Python 프로세스의 병렬 및 분산 워크로드를 호스트하고 관리하기 위한 엔터프라이즈 서버입니다. Microsoft Machine Learning Server는 Linux, Windows, Hadoop 및 Apache Spark에서 실행됩니다. 또한 [HDInsight](https://azure.microsoft.com/services/hdinsight/r-server/)에서도 사용할 수 있습니다. [Microsoft Machine Learning 패키지](https://docs.microsoft.com/r-server/r/concept-what-is-the-microsoftml-package)를 사용하여 빌드된 솔루션을 위한 실행 엔진을 제공하며 다음과 같은 시나리오를 지원하도록 오픈 소스 R 및 Python을 확장합니다.

- 고성능 분석
- 통계 분석
- Machine Learning
- 매우 큰 데이터 집합

부가 가치 기능은 서버와 함께 설치되는 전용 패키지를 통해 제공 됩니다. 개발을 위해 [Visual Studio용 R 도구](https://www.visualstudio.com/vs/rtvs/) 및 [Visual Studio용 Python 도구](https://www.visualstudio.com/vs/python/)와 같은 IDE를 사용할 수 있습니다.

다음을 수행해야 하는 경우 Microsoft Machine Learning Server를 사용합니다.

- 서버에서 R 및 Python으로 작성된 모델 빌드 및 배포
- Hadoop 또는 Spark 클러스터에서 규모 수준으로 R 및 Python 교육 배포

### <a name="data-science-virtual-machine"></a>데이터 과학 가상 컴퓨터
[DSVM(데이터 과학 가상 컴퓨터)](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/overview)은 데이터 과학 수행을 위해 특별히 구축된 Microsoft Azure 클라우드의 사용자 지정 VM 이미지입니다. 여기에는 고급 분석을 위한 지능형 응용 프로그램 구축에 바로 뛰어들 수 있도록 다수의 유명한 데이터 과학 및 기타 도구가 미리 설치 및 구성되어 있습니다. DSVM은 Windows Server 및 Linux에서 사용할 수 있습니다. Windows Server 2016 및 2012에서 Windows 버전의 DSVM이 제공됩니다. Linux 버전의 DSVM은 Ubuntu 16.04 LTS 및 OpenLogic 7.2 CentOS 기반 Linux 배포에서 제공됩니다. 

단일 노드에서 작업을 실행하거나 호스트해야 하는 경우 데이터 과학 가상 컴퓨터를 사용합니다. 또는 단일 컴퓨터에서 처리를 원격으로 강화해야 하는 경우에 사용합니다. 데이터 과학 가상 컴퓨터는 Azure Machine Learning 실험과 Azure Machine Learning 모델 관리 모두를 대상으로 지원됩니다. 

### <a name="spark-mllib-in-hdinsight"></a>HDInsight의 Spark MLLib
[HDInsight의 Spark MLLib](https://docs.microsoft.com/azure/hdinsight/hdinsight-apache-spark-ipython-notebook-machine-learning)를 사용하면 빅 데이터에서 실행 중인 Spark 작업의 일환으로 모델을 만들 수 있습니다. Spark를 사용하면 데이터를 쉽게 변환 및 준비한 다음, 하나의 작업에서 모델 생성을 스케일 아웃할 수 있습니다. Spark MLLib를 통해 만든 모델은 Azure Machine Learning 모델 관리를 통해 배포, 관리 및 모니터링할 수 있습니다. 교육 실행은 Azure Machine Learning 실험을 통해 디스패치되고 관리됩니다. Spark는 Machine Learning Workbench에서 만든 데이터 준비 작업을 스케일 아웃하는 데 사용할 수 있습니다. 

데이터 파이프라인의 일환으로 데이터 처리를 스케일 아웃하고 모델을 만들어야 하는 경우 Spark를 사용합니다. Scala, Java, Python 또는 R에 Spark 작업을 작성할 수 있습니다. 

### <a name="batch-ai-training"></a>일괄 처리 AI 교육 
[Azure 일괄 처리 AI 교육](https://aka.ms/batchaitraining)은 사용자가 모든 프레임워크를 사용하여 AI 모델과 동시에 실험하도록 도움을 주며, 클러스터링된 GPU에 걸친 규모로 해당 모델을 교육합니다. 실행할 작업 요구 사항 및 구성을 설명하면, 나머지는 알아서 처리됩니다. 

일괄 처리 AI 교육을 사용하면 다음과 같이 프레임워크를 사용하여 클러스터링된 GPU에서 심층 교육 작업을 스케일 아웃할 수 있습니다.

- Cognitive 도구 키트
- Caffe
- Chainer
- TensorFlow

Azure Machine Learning 모델 관리를 일괄 처리 AI 교육에서 모델을 생성하여 배포, 관리 및 모니터링하는 데 사용할 수 있습니다.  일괄 처리 AI 교육은 나중에 Azure Machine Learning 실험과 통합됩니다. 

### <a name="microsoft-cognitive-toolkit"></a>Microsoft Cognitive 도구 키트
[Microsoft Cognitive 도구 키트](https://www.microsoft.com/en-us/cognitive-toolkit/)는 신경망을 방향성 그래프의 계산 단계로 설명하는 통합된 심층 학습 도구 키트입니다. 이 방향성 그래프에서 리프 노드는 입력된 값이나 네트워크 매개 변수를 나타내며, 다른 노드는 입력 시 매트릭스 작업을 나타냅니다. Cognitive 도구 키트를 사용하면 피드 정방향 DNN, 나선형 네트워크(CNN) 및 되풀이 네트워크(RNN/LSTM)와 같은 인기 있는 모델 유형을 빠르게 실현하고 결합할 수 있습니다. 여러 GPU 및 서버에서 자동 차별화 및 병렬화를 통해 SGD(추측 기울기 하강, 오류 역전파) 학습을 구현합니다.

심층 학습을 사용하여 모델을 빌드하려는 경우 Cognitive 도구 키트를 사용합니다.  앞의 서비스 중 하나에 Cognitive 도구 키트를 사용할 수 있습니다.

### <a name="microsoft-cognitive-services"></a>Microsoft Cognitive Services
Microsoft Cognitive Services는 자연스러운 통신 방법을 사용하는 앱을 빌드할 수 있는 30가지의 API 집합입니다. 이러한 API를 사용하면 단 몇 줄의 코드로 사용자 앱이 사용자의 요구를 보고, 듣고, 말하고, 이해하며, 해석할 수 있습니다. 다음과 같은 앱에 지능형 기능을 쉽게 추가합니다. 

- 이모티콘 및 감정 검색
- 음성 인식
- 언어 이해
- 지식 및 검색

Microsoft Cognitive Services를 장치 및 플랫폼에서 앱을 개발하는 데 사용할 수 있습니다. API는 끊임없이 개선되며, 설치하기가 매우 쉽습니다. 

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

## <a name="next-steps"></a>다음 단계
* [Azure Machine Learning 설치 및 만들기](quickstart-installation.md)
