---
title: Azure Data Science Virtual Machine이란?
description: Windows 및 Linux 데이터 과학 Virtual Machines에 대한 주요 분석 시나리오 및 구성 요소를 간략히 설명합니다.
keywords: 데이터 과학 도구, 데이터 과학 가상 머신, 데이터 과학용 도구, linux 데이터 과학
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: vijetajo
ms.author: vijetaj
ms.topic: overview
ms.date: 02/22/2019
ms.openlocfilehash: 3295a59ee4496d332f0d886c89ca900ab6b4bcd1
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/30/2019
ms.locfileid: "70191901"
---
# <a name="what-is-the-azure-data-science-virtual-machine-for-linux-and-windows"></a>Linux 및 Windows용 Azure Data Science Virtual Machine이란?

DSVM(Data Science Virtual Machine)은 데이터 과학 수행을 위해 특별히 구축된 Azure 클라우드 플랫폼의 사용자 지정 VM 이미지입니다. 여기에는 고급 분석을 위한 지능형 애플리케이션 구축에 바로 뛰어들 수 있도록 다수의 유명한 데이터 과학 및 기타 도구가 미리 설치 및 구성되어 있습니다. 

도구 구성은 Microsoft의 데이터 과학자와 개발자 및 광범위한 데이터 과학 커뮤니티를 통해 엄격하게 테스트되었습니다. 이 테스트는 안정성과 일반적인 작동성을 보장하는 데 유용합니다.

DSVM은 다음에서 사용할 수 있습니다.
+ Windows Server 2016, Windows Server 2012
+ Ubuntu 16.04 LTS 및 CentOS 7.4

> [!NOTE]
> 딥 러닝을 위한 모든 VM 도구는 Data Science Virtual Machine에 통합되었습니다. 


## <a name="what-can-i-do-with-the-dsvm"></a>DSVM으로 무엇을 할 수 있나요?
Data Science Virtual Machine의 목표는 산업 전반에 걸쳐 모든 기술 수준의 데이터 전문가에게 효율적이고 사전 구성된 완전 통합형 데이터 과학 환경을 제공하는 것입니다. 비슷한 작업 영역을 직접 롤아웃하는 대신 DSVM을 프로비저닝할 수 있습니다. 이러한 선택은 설치, 구성 및 패키지 관리 프로세스에서 며칠 또는 몇 _주_를 절약할 수 있습니다. DSVM이 할당된 후 데이터 과학 프로젝트에 대한 작업을 즉시 시작할 수 있습니다.

DSVM은 광범위한 사용 시나리오에서 작동하도록 설계 및 구성되었습니다. 프로젝트 요구 사항의 변화에 따라 환경을 확장하거나 축소할 수 있습니다. 원하는 언어를 사용하여 데이터 과학 작업을 프로그래밍하고, 요구 사항에 맞게 시스템을 사용자 지정하는 다른 도구를 설치할 수도 있습니다.

### <a name="preconfigured-analytics-desktop-in-the-cloud"></a>클라우드에 미리 구성되어 있는 분석 데스크톱
DSVM은 로컬 데스크톱을 관리형 클라우드 데스크톱으로 바꾸려는 데이터 과학 팀을 위해 기본 구성을 제공합니다. 이러한 기본 구성은 팀의 모든 데이터 과학자가 일관적인 설정을 가지고 실험을 검증하고 협업을 증진할 수 있도록 해 줍니다. 또한 시스템 관리 부담을 줄여 비용을 절감합니다. 시스템 관리 업무가 감소하기 때문에 고급 분석을 위해 소프트웨어 패키지를 평가, 설치 및 유지 관리하는 데 필요한 시간이 절약됩니다.

### <a name="data-science-training-and-education"></a>데이터 과학 학습 및 교육
데이터 과학 수업을 가르치는 엔터프라이즈 트레이너 및 강사는 대개 가상 머신 이미지를 제공합니다. 이미지를 통해 수강생에게 일관된 설정을 제공하여 샘플이 예상대로 작동하도록 할 수 있습니다. 

DSVM은 지원 및 비호환 문제를 덜어주는 일관된 설정을 사용하여 주문형 환경을 생성합니다. 이러한 환경을 빈번하게 구축해야 하는 경우, 특히 짧은 수업의 경우, 실질적인 이점이 있습니다.

### <a name="on-demand-elastic-capacity-for-large-scale-projects"></a>대규모 프로젝트를 위한 주문형 탄력적 용량
데이터 과학 해카톤/시합 또는 대규모 데이터 모델링 탐사에는, 일반적으로 짧은 기간 동안 확장된 하드웨어 용량이 필요합니다. DSVM은 요구에 따라 고성능 컴퓨팅 리소스를 실행하는 실험이 가능하도록 확장된 서버에 신속하게 데이터 과학 환경을 복제할 수 있습니다.

### <a name="custom-compute-power-for-azure-notebooks"></a>Azure Notebooks의 사용자 지정 컴퓨팅 능력
[Azure Notebooks](../../notebooks/azure-notebooks-overview.md)는 무료 호스트형 서비스이며 설치 없이 클라우드에서 Jupyter Notebook을 개발, 실행 및 공유합니다. 평가판 서비스 계층은 메모리 4GB, 데이터 1GB로 제한됩니다. 

모든 한도를 해제하려면 DSVM 또는 Jupyter 서버에서 실행되는 다른 모든 VM에 Notebook 프로젝트를 연결하면 됩니다. Azure Active Directory를 사용하는 계정(예: 회사 계정)으로 Azure Notebooks에 로그인하면 Notebooks가 해당 계정과 연결된 모든 구독의 DSVM을 자동으로 보여줍니다. [DSVM을 Azure Notebooks에 연결](../../notebooks/configure-manage-azure-notebooks-projects.md#compute-tier)하여 사용 가능한 컴퓨팅 성능을 확장할 수 있습니다.

### <a name="short-term-experimentation-and-evaluation"></a>단기 실험 및 평가
DSVM을 사용하면 최소한의 설정 활동으로 다음과 같은 도구를 평가하거나 학습할 수 있습니다.

- Microsoft Machine Learning 서버
- SQL Server
- Visual Studio 도구
- Jupyter
- 딥 러닝 및 기계 학습 도구 키트
- 커뮤니티에서 인기 있는 새 도구 

DSVM을 빠르게 설정할 수 있기 때문에 다른 단기 사용 시나리오에 적용할 수 있습니다. 이러한 시나리오에는 게시된 실험을 복제하고, 데모를 실행하고, 온라인 세션 및 회의 자습서를 연습하는 과정이 있습니다.

### <a name="deep-learning"></a>심층 학습
DSVM에서 학습 모델은 GPU(그래픽 처리 장치)를 기반으로 하는 하드웨어에서 딥 러닝 알고리즘을 사용할 수 있습니다. DSVM을 사용하면 Azure 플랫폼의 VM 확장 기능을 활용하여 필요에 따라 클라우드에서 GPU 기반 하드웨어를 사용할 수 있습니다. 큰 모델을 학습할 때 또는 동일한 OS 디스크를 유지하면서 고속 계산이 필요한 경우 GPU 기반 VM으로 전환할 수 있습니다.  

Windows Server 2016 버전 DSVM에는 GPU 드라이버, 프레임워크 및 GPU 버전 딥 러닝 프레임워크가 미리 설치되어 있습니다. Linux 버전의 경우 CentOS 및 Ubuntu DSVM 모두에서 GPU에 대한 딥 러닝이 활성화됩니다. 

비 GPU 기반 Azure 가상 머신에 DSVM의 Ubuntu, CentOS 또는 Windows 2016 버전을 배포할 수도 있습니다. 이 경우 모든 딥 러닝 프레임워크는 CPU 모드로 다시 대체됩니다.
 
[사용 가능한 딥 러닝 및 AI 프레임워크에 대해 자세히 알아보세요.](dsvm-deep-learning-ai-frameworks.md)

<a name="included"></a>

## <a name="whats-included-on-the-dsvm"></a>DSVM에는 무엇이 포함되나요?
데이터 과학 Virtual Machine에는 다수의 유명한 데이터 과학 및 심층 학습 도구가 이미 설치 및 구성되어 있습니다. 다양한 Azure 데이터 및 분석 제품을 사용하기 쉽게 해주는 도구도 포함되어 있습니다. 이러한 제품에는 예측 모델을 빌드하기 위한 Microsoft Machine Learning Server(R, Python) 및 대규모 데이터 세트 탐색을 위한 SQL Server 2017이 포함됩니다. DSVM에는 오픈 소스 커뮤니티 및 Microsoft의 다른 도구가 [샘플 코드 및 노트북](dsvm-samples-and-walkthroughs.md)과 함께 포함되어 있습니다. 

다음은 도구 및 플랫폼 목록입니다.
+ [지원되는 프로그래밍 언어](dsvm-languages.md)

+ [지원되는 데이터 플랫폼](dsvm-data-platforms.md)

+ [개발 도구 및 IDE](dsvm-tools-development.md)

+ [딥 러닝 및 AI 프레임워크](dsvm-deep-learning-ai-frameworks.md)

+ [기계 학습 및 데이터 과학 도구](dsvm-ml-data-science-tools.md)

+ [데이터 수집 도구](dsvm-tools-ingestion.md)

+ [데이터 탐색 및 시각화 도구](dsvm-tools-explore-and-visualize.md)

다음 테이블에는 데이터 과학 Virtual Machine의 Windows 및 Linux 버전에 포함된 주요 구성 요소를 항목별로 정리하여 비교해 두었습니다.

| **도구**                                                           | **Windows 버전** | **Linux 버전** |
| :------------------------------------------------------------------ |:-------------------:|:------------------:|
| 인기 있는 패키지가 사전 설치된 [Microsoft R Open](https://mran.microsoft.com/open/)   |Y                      | Y             |
| [Microsoft Machine Learning Server(R, Python)](https://docs.microsoft.com/machine-learning-server/) Developer Edition은 다음을 포함합니다. <br />  &nbsp;&nbsp;&nbsp;&nbsp; [RevoScaleR/revoscalepy](https://docs.microsoft.com/machine-learning-server/r/concept-what-is-revoscaler) 병렬 및 분산 고성능 프레임워크(R 및 Python)<br />  &nbsp;&nbsp;&nbsp;&nbsp; [MicrosoftML](https://docs.microsoft.com/machine-learning-server/r/concept-what-is-the-microsoftml-package), Microsoft의 최신 기계 학습 알고리즘 <br />  &nbsp;&nbsp;&nbsp;&nbsp; [R 및 Python 연산화](https://docs.microsoft.com/machine-learning-server/what-is-operationalization)                                            |Y                      | Y |
| 공유 활성화를 통한 [Microsoft Office](https://products.office.com/business/office-365-proplus-business-software) ProPlus: Excel, Word 및 PowerPoint   |Y                      |N              |
| 인기 있는 패키지가 사전 설치된 [Anaconda Python](https://www.continuum.io/) 2.7 및 3.5    |Y                      |Y              |
| Julia 언어에 대해 인기 있는 패키지가 사전 설치된 [JuliaPro](https://juliacomputing.com/products/juliapro.html)                         |Y                      |Y              |
| 관계형 데이터베이스                                                            | [SQL Server 2017](https://www.microsoft.com/sql-server/sql-server-2017) <br/> Developer Edition| [PostgreSQL](https://www.postgresql.org/)(CentOS),<br/>[SQL Server 2017](https://www.microsoft.com/sql-server/sql-server-2017) <br/> 디벨로퍼 버전(Ubuntu) |
| 데이터베이스 도구                                                       |  SQL Server Management Studio <br/> SQL Server Integration Services<br/> [bcp, sqlcmd](https://docs.microsoft.com/sql/tools/command-prompt-utility-reference-database-engine)<br />  ODBC/JDBC 드라이버|  [SQuirreL SQL](http://squirrel-sql.sourceforge.net/)(쿼리 도구), <br />  bcp, sqlcmd <br />  ODBC/JDBC 드라이버|
| SQL Server 기계 학습 서비스(R, Python)를 통한 확장성 있는 데이터베이스 내 분석 | Y     |N              |
| 다음 커널이 있는 [Jupyter Notebook 서버](https://jupyter.org/):                                  | Y     | Y |
|     &nbsp;&nbsp;&nbsp;&nbsp; R | Y | Y |
|     &nbsp;&nbsp;&nbsp;&nbsp; Python | Y | Y |
|     &nbsp;&nbsp;&nbsp;&nbsp; Julia | Y | Y |
|     &nbsp;&nbsp;&nbsp;&nbsp; PySpark | Y | Y |
|     &nbsp;&nbsp;&nbsp;&nbsp; [Sparkmagic](https://github.com/jupyter-incubator/sparkmagic) | N | Y (Ubuntu에만 해당) |
|     &nbsp;&nbsp;&nbsp;&nbsp; SparkR     | N | Y |
| JupyterHub(다중 사용자 노트북 서버)| N | Y |
| JupyterLab(다중 사용자 노트북 서버) | N | Y (Ubuntu에만 해당) |
| 개발 도구, IDE 및 코드 편집기:| | |
| &nbsp;&nbsp;&nbsp;&nbsp; [Visual Studio 2019(Community Edition)](https://www.visualstudio.com/community/) Git 플러그 인, Azure HDInsight(Hadoop), Azure Data Lake, SQL Server Data Tools, [Node.js](https://github.com/Microsoft/nodejstools), [Python](https://aka.ms/ptvs) 및 [RTVS(Visual Studio용 R 도구)](https://microsoft.github.io/RTVS-docs/) 포함 | Y | N |
| &nbsp;&nbsp;&nbsp;&nbsp; [Visual Studio Code](https://code.visualstudio.com/) | Y | Y |
| &nbsp;&nbsp;&nbsp;&nbsp; [RStudio Desktop](https://www.rstudio.com/products/rstudio/#Desktop) | Y | Y |
| &nbsp;&nbsp;&nbsp;&nbsp; [RStudio Server](https://www.rstudio.com/products/rstudio/#Server) | N | Y |
| &nbsp;&nbsp;&nbsp;&nbsp; [PyCharm Community Edition](https://www.jetbrains.com/pycharm/) | N | Y |
| &nbsp;&nbsp;&nbsp;&nbsp; [Atom](https://atom.io/) | N | Y |
| &nbsp;&nbsp;&nbsp;&nbsp; [Juno(Julia IDE)](https://junolab.org/)| Y | Y |
| &nbsp;&nbsp;&nbsp;&nbsp; Vim 및 Emacs | Y | Y |
| &nbsp;&nbsp;&nbsp;&nbsp; Git 및 Git Bash | Y | Y |
| &nbsp;&nbsp;&nbsp;&nbsp; OpenJDK | Y | Y |
| &nbsp;&nbsp;&nbsp;&nbsp; .NET Framework | Y | N |
| Power BI Desktop | Y | N |
| Azure 및 Cortana Intelligence Suite 서비스 액세스를 위한 SDK | Y | Y |
| 데이터 이동 및 관리 도구: | | |
| &nbsp;&nbsp;&nbsp;&nbsp; Azure Storage Explorer | Y | Y |
| &nbsp;&nbsp;&nbsp;&nbsp; [Azure CLI](https://docs.microsoft.com/cli/azure) | Y | Y |
| &nbsp;&nbsp;&nbsp;&nbsp; Azure PowerShell | Y | N |
| &nbsp;&nbsp;&nbsp;&nbsp; [Azcopy](https://docs.microsoft.com/azure/storage/storage-use-azcopy) | Y | N |
| &nbsp;&nbsp;&nbsp;&nbsp; [Blob FUSE 드라이버](https://github.com/Azure/azure-storage-fuse) | N | Y |
| &nbsp;&nbsp;&nbsp;&nbsp; [Adlcopy(Azure Data Lake Storage)](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-copy-data-azure-storage-blob) | Y | N |
| &nbsp;&nbsp;&nbsp;&nbsp; [Azure Cosmos DB 데이터 마이그레이션 도구](https://docs.microsoft.com/azure/documentdb/documentdb-import-data) | Y | N |
| &nbsp;&nbsp;&nbsp;&nbsp; [Microsoft 데이터 관리 게이트웨이](https://msdn.microsoft.com/library/dn879362.aspx): 온-프레미스와 클라우드 간 데이터 이동 | Y | N |
| &nbsp;&nbsp;&nbsp;&nbsp; Unix/Linux 명령줄 도구 | Y | Y |
| 데이터 탐색을 위한 [Apache Drill](https://drill.apache.org) | Y | Y |
| 기계 학습 도구: |||
| &nbsp;&nbsp;&nbsp;&nbsp; [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/)(R, Python)과 통합 | Y | Y |
| &nbsp;&nbsp;&nbsp;&nbsp; [XGBoost](https://github.com/dmlc/xgboost) | Y | Y |
| &nbsp;&nbsp;&nbsp;&nbsp; [Vowpal Wabbit](https://github.com/JohnLangford/vowpal_wabbit) | Y | Y |
| &nbsp;&nbsp;&nbsp;&nbsp; [Weka](https://www.cs.waikato.ac.nz/ml/weka/) | Y | Y |
| &nbsp;&nbsp;&nbsp;&nbsp; [Rattle](https://togaware.com/rattle/) | Y | Y |
| &nbsp;&nbsp;&nbsp;&nbsp; [LightGBM](https://github.com/Microsoft/LightGBM) | N | Y (Ubuntu에만 해당) |
| &nbsp;&nbsp;&nbsp;&nbsp; [CatBoost](https://tech.yandex.com/catboost/) | N | Y (Ubuntu에만 해당) |
| &nbsp;&nbsp;&nbsp;&nbsp; [H2O](https://www.h2o.ai/h2o/), [Sparkling Water](https://www.h2o.ai/sparkling-water/) | N | Y (Ubuntu에만 해당) |
| GPU 또는 CPU에서 작동하는 딥 러닝 도구: |  |  |
| &nbsp;&nbsp;&nbsp;&nbsp; [Microsoft Cognitive 도구 키트(CNTK)](https://docs.microsoft.com/cognitive-toolkit/)(Windows 2016) | Y | Y |
| &nbsp;&nbsp;&nbsp;&nbsp; [TensorFlow](https://www.tensorflow.org/) | Y(Windows 2016) | Y |
| &nbsp;&nbsp;&nbsp;&nbsp; [Horovod](https://github.com/uber/horovod) | N | Y(Ubuntu) |
| &nbsp;&nbsp;&nbsp;&nbsp; [MXNet](https://mxnet.io/) | Y(Windows 2016) | Y|
| &nbsp;&nbsp;&nbsp;&nbsp; [Caffe and Caffe2](https://github.com/caffe2/caffe2) | N | Y |
| &nbsp;&nbsp;&nbsp;&nbsp; [Chainer](https://chainer.org/) | N | Y |
| &nbsp;&nbsp;&nbsp;&nbsp; [Torch](http://torch.ch/) | N | Y |
| &nbsp;&nbsp;&nbsp;&nbsp; [Theano](https://github.com/Theano/Theano) | N | Y |
| &nbsp;&nbsp;&nbsp;&nbsp; [Keras](https://keras.io/)| N | Y |
| &nbsp;&nbsp;&nbsp;&nbsp; [PyTorch](https://pytorch.org/)| N | Y |
| &nbsp;&nbsp;&nbsp;&nbsp; [NVidia Digits](https://github.com/NVIDIA/DIGITS) | N | Y |
| &nbsp;&nbsp;&nbsp;&nbsp; [MXNet 모델 서버](https://github.com/awslabs/mxnet-model-server) | N | Y |
| &nbsp;&nbsp;&nbsp;&nbsp; [TensorFlow 서비스](https://www.tensorflow.org/serving/) | N | Y |
| &nbsp;&nbsp;&nbsp;&nbsp; [TensorRT](https://developer.nvidia.com/tensorrt) | N | Y |
| &nbsp;&nbsp;&nbsp;&nbsp; [CUDA, cuDNN, NVIDIA 드라이버](https://developer.nvidia.com/cuda-toolkit) | Y | Y |

## <a name="next-steps"></a>다음 단계

다음 문서에서 자세한 내용을 알아보세요.

+ Windows:
  + [Windows DSVM 설정](provision-vm.md)
  + [Windows DSVM에서 수행할 수 있는 10 가지 작업](vm-do-ten-things.md)

+ Linux:
  + [Linux DSVM(Ubuntu) 설정](dsvm-ubuntu-intro.md)
  + [Linux DSVM(CentOS) 설정](linux-dsvm-intro.md)
  + [Linux DSVM의 데이터 과학](linux-dsvm-walkthrough.md)
