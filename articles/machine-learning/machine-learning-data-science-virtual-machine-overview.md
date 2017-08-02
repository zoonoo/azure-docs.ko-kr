---
title: "데이터 과학 가상 컴퓨터란? | Microsoft Docs"
description: "데이터 과학 가상 컴퓨터에서 주요 분석 시나리오를 수행하는 방법"
keywords: "데이터 과학 도구, 데이터 과학 가상 컴퓨터, 데이터 과학용 도구, linux 데이터 과학"
services: machine-learning
documentationcenter: 
author: bradsev
manager: jhubbard
editor: cgronlun
ms.assetid: d4f91270-dbd2-4290-ab2b-b7bfad0b2703
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/21/2017
ms.author: gokuma;bradsev
ms.translationtype: HT
ms.sourcegitcommit: 2812039649f7d2fb0705220854e4d8d0a031d31e
ms.openlocfilehash: 6a1bde07ea04445849674712361892f5783c0f2d
ms.contentlocale: ko-kr
ms.lasthandoff: 07/22/2017

---
# <a name="introduction-to-the-cloud-based-data-science-virtual-machine-for-linux-and-windows"></a>Linux 및 Windows용 클라우드 기반 데이터 과학 가상 컴퓨터에 대한 소개
데이터 과학 가상 컴퓨터(DSVM)는 데이터 과학 수행을 위해 특별히 구축된 Microsoft Azure 클라우드의 사용자 지정 VM 이미지입니다. 여기에는 고급 분석을 위한 지능형 응용 프로그램 구축에 바로 뛰어들 수 있도록 다수의 유명한 데이터 과학 및 기타 도구가 미리 설치 및 구성되어 있습니다. Windows Server 2012 및 Linux에서 사용할 수 있습니다. Ubuntu 16.04 LTS 또는 OpenLogic 7.2 CentOS 기반 Linux 배포에서 DSVM의 Linux 버전을 제공합니다. 

이 항목은 데이터 과학 VM으로 할 수 있는 작업 및 VM 사용에 대한 몇 가지 주요 시나리오를 설명하고 Windows 및 Linux 버전에서 사용할 수 있는 주요 기능을 항목별로 설명하고 이를 사용하여 시작하는 방법에 대한 지침을 제공합니다.

## <a name="what-can-i-do-with-the-data-science-virtual-machine"></a>데이터 과학 가상 컴퓨터로 무엇을 할 수 있나요?
데이터 과학 가상 컴퓨터의 목표는 기술 수준 및 역할에 상관없이 모든 데이터 전문가에게 효율적이고 이상적인 데이터 과학 환경을 제공하는 것입니다. 이 VM은 유사한 환경을 직접 운영할 경우 들어가는 시간을 상당히 줄여 줍니다. 대신에 새로 작성된 VM 인스턴스에서 데이터 과학 프로젝트를 바로 시작할 수 있습니다. 

데이터 과학 VM은 다양한 사용 시나리오에서의 작업을 위해 설계 및 구성되었습니다. 프로젝트의 요구가 변함에 따라 환경을 확장하거나 축소할 수 있습니다. 원하는 언어를 사용하여 데이터 과학 작업을 프로그래밍할 수 있습니다. 요구에 딱 맞도록 다른 도구를 설치하고 시스템을 사용자 지정할 수 있습니다.

## <a name="key-scenarios"></a>주요 시나리오
이 섹션은 데이터 과학 VM을 배포할 수 있는 주요 시나리오를 제안합니다.

### <a name="preconfigured-analytics-desktop-in-the-cloud"></a>클라우드에 미리 구성되어 있는 분석 데스크톱
데이터 과학 VM은 로컬 데스크톱을 관리되는 클라우드 데스크톱으로 바꾸려는 데이터 과학 팀을 위해 기본 구성을 제공합니다. 이러한 기본 구성은 팀의 모든 데이터 과학자가 일관적인 설정을 가지고 실험을 검증하고 협력을 증진할 수 있도록 해 줍니다. 또한 시스템 관리 부담을 줄여주고 고급 분석 수행에 필요한 다양한 소프트웨어 패키지를 평가, 설치 및 유지 관리하는 데 필요한 시간을 절약하여 비용을 감소시켜 줍니다.  

### <a name="data-science-training-and-education"></a>데이터 과학 학습 및 교육
데이터 과학 수업을 지도하는 엔터프라이즈 학습 및 교육 담당자는 학생들에게 일관된 설정을 제공하여 샘플이 예측대로 작동되도록 하기 위해서 가상 컴퓨터 이미지를 제공하는 것이 일반적입니다. 데이터 과학 VM은 지원 및 비호환 문제를 덜어주는 일관된 설정을 사용하여 주문형 환경을 생성합니다. 이러한 환경을 빈번하게 구축해야 하는 경우, 특히 짧은 수업의 경우, 실질적인 이점이 있습니다.

### <a name="on-demand-elastic-capacity-for-large-scale-projects"></a>대규모 프로젝트를 위한 주문형 탄력적 용량
데이터 과학 해카톤/시합 또는 대규모 데이터 모델링 탐사에는, 일반적으로 짧은 기간 동안 확장된 하드웨어 용량이 필요합니다. 데이터 과학 VM은 요구에 따라서 데이터 과학 환경을 고성능 컴퓨팅 리소스가 필요한 실험을 허용하는 확장된 서버에서 신속하게 복제하도록 지원할 수 있습니다.

### <a name="short-term-experimentation-and-evaluation"></a>단기 실험 및 평가
데이터 과학 VM은 Microsoft R Server, SQL Server, Visual Studio Tools , Jupyter, Deep Learning/ML 도구 키트 및 최소한의 설치 업무로 인해 커뮤니티에서 인기를 끌고 있는 새로운 도구를 비롯한 다양한 도구를 평가하거나 학습하는 데 사용할 수 있습니다. 데이터 과학 VM을 신속하게 설정할 수 있기 때문에 게시된 실험을 복제하고, 데모를 실행하고, 온라인 세션이나 회의 자습서의 과정 따라 연습하는 등의 기타 단기 사용 시나리오에 적용할 수 있습니다.

### <a name="deep-learning"></a>심층 학습
GPU(그래픽 처리 장치) 기반 하드웨어에서 심층 학습 알고리즘을 사용하여 학습 모델에 대한 데이터 과학 VM을 사용할 수 있습니다. DSVM은 큰 모델을 학습하거나 GPU의 힘을 활용하는 고속 계산을 할 때 필요에 따라 클라우드에서만 GPU 기반 하드웨어를 사용하도록 도와줍니다.  Windows에서는 현재 DSVM 상단에 있는 별도 추가 기능으로 [DSVM에 대한 심층 학습 도구 키트](http://aka.ms/dsvm/deeplearning)를 제공합니다. 이 추가 기능은 VM 인스턴스를 만드는 동안 심층 학습 알고리즘의 GPU 드라이버, 프레임워크 및 GPU 버전을 자동으로 설치합니다. Linux에서 GPU 심층 학습은 [Linux(Ubuntu) 버전에 대한 데이터 과학 가상 컴퓨터](http://aka.ms/dsvm/ubuntu)에서만 사용 가능합니다. 모든 심층 학습 프레임워크가 CPU 모드로 대체하는 경우 데이터 과학 VM의 Ubuntu 버전을 비GPU 기반 Azure 가상 컴퓨터로 배포할 수 있습니다. DSVM의 CentOS 기반 Linux 버전은 일부 심층 학습 도구(CNTK, Tensorflow, MXNet)의 CPU 빌드만 포함하지만 GPU 드라이버 및 프레임워크와 사전 설치되지 않습니다. 

## <a name="whats-included-in-the-data-science-vm"></a>데이터 과학 VM에는 무엇이 포함되어 있나요?
데이터 과학 가상 컴퓨터에는 다수의 유명한 데이터 과학 및 심층 학습 도구가 이미 설치 및 구성되어 있습니다. 다양한 Azure 데이터 및 분석 제품을 사용하기 쉽게 해주는 도구도 포함되어 있습니다. Microsoft R Server를 사용하거나 SQL Server 2016을 사용하여 대규모 데이터 집합에서 예측 모델을 조사하고 구축할 수 있습니다. 오픈 소스 커뮤니티 및 Microsoft의 다양한 도구는 물론 샘플 코드와 노트북이 포함되어 있습니다. 다음 테이블에는 데이터 과학 가상 컴퓨터의 Windows 및 Linux 버전에 포함된 주요 구성 요소를 항목별로 정리하여 비교해 두었습니다.


| **도구**                                                           | **Windows 버전** | **Linux 버전** |
| :------------------------------------------------------------------ |:-------------------:|:------------------:|
| 인기 있는 패키지가 사전 설치된 [Microsoft R Open](https://mran.microsoft.com/open/)   |Y                      | Y             |
| [Microsoft R Server](https://msdn.microsoft.com/microsoft-r/) Developer Edition은 다음을 포함합니다. <br />  &nbsp;&nbsp;&nbsp;&nbsp;* [ScaleR](https://msdn.microsoft.com/microsoft-r/scaler-getting-started) 병렬 및 분산된 고성능 R 프레임워크<br />  &nbsp;&nbsp;&nbsp;&nbsp;* [MicrosoftML](https://msdn.microsoft.com/microsoft-r/microsoftml-introduction) -Microsoft의 새로운 기계 학습 알고리즘 <br />  &nbsp;&nbsp;&nbsp;&nbsp;* [R 운영](https://msdn.microsoft.com/microsoft-r/operationalize/about)                                            |Y                      | Y <br/> (MicrosoftML은 아직 사용할 수 없음)|
| 인기 있는 패키지가 사전 설치된 [Anaconda Python](https://www.continuum.io/) 2.7, 3.5    |Y                      |Y              |
| Julia 언어에 대해 인기 있는 패키지가 사전 설치된 [JuliaPro](https://juliacomputing.com/products/juliapro.html)                         |Y                      |Y              |
| 관계형 데이터베이스                                                            | [SQL Server 2016 SP1](https://www.microsoft.com/sql-server/sql-server-2016) <br/> Developer Edition| [PostgreSQL](https://www.postgresql.org/) |
| 데이터베이스 도구                                                       | * SQL Server Management Studio <br/>* SQL Server Integration Services<br/>* [bcp, sqlcmd](https://docs.microsoft.com/sql/tools/command-prompt-utility-reference-database-engine)<br /> * ODBC/JDBC 드라이버| * [SQuirreL SQL](http://squirrel-sql.sourceforge.net/)(쿼리 도구), <br /> * bcp, sqlcmd <br /> * ODBC/JDBC 드라이버|
| SQL Server R Services를 통한 확장성 있는 데이터베이스 내 분석 | Y     |N              |
| 다음의 커널이 있는 **[Jupyter 노트북 서버](http://jupyter.org/),**                                  | Y     | Y |
|     &nbsp;&nbsp;&nbsp;&nbsp;* R | Y | Y |
|     &nbsp;&nbsp;&nbsp;&nbsp;* Python 2.7 & 3.5 | Y | Y |
|     &nbsp;&nbsp;&nbsp;&nbsp;* Julia | Y | Y |
|     &nbsp;&nbsp;&nbsp;&nbsp;* PySpark | N | Y |
|     &nbsp;&nbsp;&nbsp;&nbsp;* [Sparkmagic](https://github.com/jupyter-incubator/sparkmagic) | N | Y (Ubuntu에만 해당) |
|     &nbsp;&nbsp;&nbsp;&nbsp;* SparkR     | N | Y |
| JupyterHub(다중 사용자 노트북 서버)| N | Y |
| **개발 도구, IDE 및 코드 편집기**| | |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Visual Studio 2015 (Community Edition)](https://www.visualstudio.com/community/) >Git 플러그인, Azure HDInsight (Hadoop), Data Lake, SQL Server Data Tools, [Node.js](https://github.com/Microsoft/nodejstools), [Python](http://aka.ms/ptvs) 및 [R Tools for Visual Studio (RTVS)](http://microsoft.github.io/RTVS-docs/) 포함 | Y | N |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Visual Studio Code](https://code.visualstudio.com/) | Y | Y |
| &nbsp;&nbsp;&nbsp;&nbsp;* [RStudio Desktop](https://www.rstudio.com/products/rstudio/#Desktop) | Y | Y |
| &nbsp;&nbsp;&nbsp;&nbsp;* [RStudio Server](https://www.rstudio.com/products/rstudio/#Server) | N | Y |
| &nbsp;&nbsp;&nbsp;&nbsp;* [PyCharm](https://www.jetbrains.com/pycharm/) | N | Y |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Atom](https://atom.io/) | N | Y |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Juno (Julia IDE)](http://junolab.org/)| Y | Y |
| &nbsp;&nbsp;&nbsp;&nbsp;* Vim 및 Emacs | Y | Y |
| &nbsp;&nbsp;&nbsp;&nbsp;* Git 및 GitBash | Y | Y |
| &nbsp;&nbsp;&nbsp;&nbsp;* OpenJDK | Y | Y |
| &nbsp;&nbsp;&nbsp;&nbsp;* .Net Framework | Y | N |
| PowerBI Desktop | Y | N |
| Azure 및 Cortana Intelligence Suite 서비스 액세스를 위한 SDK | Y | Y |
| **데이터 이동 및 관리 도구** | | |
| &nbsp;&nbsp;&nbsp;&nbsp;* Azure Storage Explorer | Y | Y |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Azure CLI](https://docs.microsoft.com/cli/azure/overview) | Y | Y |
| &nbsp;&nbsp;&nbsp;&nbsp;* Azure Powershell | Y | N |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Azcopy](https://docs.microsoft.com/azure/storage/storage-use-azcopy) | Y | N |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Adlcopy(Azure Data Lake 저장소)](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-copy-data-azure-storage-blob) | Y | N |
| &nbsp;&nbsp;&nbsp;&nbsp;* [DocDB 데이터 마이그레이션 도구](https://docs.microsoft.com/azure/documentdb/documentdb-import-data) | Y | N |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Microsoft 데이터 관리 게이트웨이](https://msdn.microsoft.com/library/dn879362.aspx) : 온-프레미스 및 클라우드 간 데이터 이동 | Y | N |
| &nbsp;&nbsp;&nbsp;&nbsp;* Unix/Linux 명령줄 유틸리티 | Y | Y |
| 데이터 탐색에 대한 [Apache Drill](http://drill.apache.org) | Y | Y |
| **Machine Learning 도구** |||
| &nbsp;&nbsp;&nbsp;&nbsp;* [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/)(R, Python)과 통합 | Y | Y |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Xgboost](https://github.com/dmlc/xgboost) | Y | Y |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Vowpal Wabbit](https://github.com/JohnLangford/vowpal_wabbit) | Y | Y |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Weka](http://www.cs.waikato.ac.nz/ml/weka/) | Y | Y |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Rattle](http://rattle.togaware.com/) | Y | Y |
| &nbsp;&nbsp;&nbsp;&nbsp;* [LightGBM](https://github.com/Microsoft/LightGBM) | N | Y (Ubuntu에만 해당) |
| &nbsp;&nbsp;&nbsp;&nbsp;* [H2O](https://www.h2o.ai/h2o/) | N | Y (Ubuntu에만 해당) |
| **GPU 기반 심층 학습 도구** |[DSVM에 대한 심층 학습 도구 키트](http://aka.ms/dsvm/deeplearning) 사용 |Ubuntu 버전에만 해당|
| &nbsp;&nbsp;&nbsp;&nbsp;* [Microsoft Cognitive Toolkit (CNTK)](http://cntk.ai) | Y | Y |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Tensorflow](https://www.tensorflow.org/) | Y | Y |
| &nbsp;&nbsp;&nbsp;&nbsp;* [MXNet](http://mxnet.io/) | Y | Y|
| &nbsp;&nbsp;&nbsp;&nbsp;* [Caffe & Caffe2](https://github.com/caffe2/caffe2) | N | Y |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Torch](http://torch.ch/) | N | Y |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Theano](https://github.com/Theano/Theano) | N | Y |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Keras](https://keras.io/)| N | Y |
| &nbsp;&nbsp;&nbsp;&nbsp;* [NVidia Digits](https://github.com/NVIDIA/DIGITS) | N | Y |
| &nbsp;&nbsp;&nbsp;&nbsp;* [CUDA, CUDNN, Nvidia 드라이버](https://developer.nvidia.com/cuda-toolkit) | Y | Y |
| **빅 데이터 플랫폼(Devtest에만 해당)**|||
| &nbsp;&nbsp;&nbsp;&nbsp;* 로컬 [Spark](http://spark.apache.org/) 독립 실행형 | N | Y |
| &nbsp;&nbsp;&nbsp;&nbsp;* 로컬 [Hadoop](http://hadoop.apache.org/)(HDFS, YARN) | N | Y |



## <a name="get-started-with-the-windows-data-science-vm"></a>Windows 데이터 과학 VM 시작
* [이 페이지](https://azure.microsoft.com/marketplace/partners/microsoft-ads/standard-data-science-vm/)로 이동하고 녹색 **가상 컴퓨터 만들기**를 선택하여 Windows에서 VM 인스턴스를 생성합니다.
* VM을 생성할 때 지정해둔 자격 증명을 사용하여 원격 데스크톱에서 VM에 로그인합니다.
* 사용할 수 있는 도구를 검색하고 시작하려면 **시작** 메뉴를 클릭합니다.

## <a name="get-started-with-the-linux-data-science-vm"></a>Linux 데이터 과학 VM 시작
* Linux에서 VM의 인스턴스를 만듭니다
  * OpenLogic CentOS 기반 버전의 경우 [이 페이지](http://aka.ms/dsvm/centos)로 이동하여 **지금 시작** 단추를 선택합니다.
  * Ubuntu 버전의 경우 [이 페이지](http://aka.ms/dsvm/ubuntu)로 이동하여 **지금 시작** 단추를 선택합니다.
* VM을 생성할 때 지정해둔 자격 증명을 사용하여 Putty 또는 SSH 명령과 같은 SSH 클라이언트에서 VM에 로그인합니다.
* Shell 프롬프트에서 dsvm-more-info를 입력합니다.
* 그래픽 데스크톱은 [여기](http://wiki.x2go.org/doku.php/doc:installation:x2goclient)에서 클라이언트 플랫폼을 위한 X2Go 클라이언트를 다운로드하고 Linux 데이터 과학 VM 문서 [Linux 데이터 과학 가상 컴퓨터 프로비전](machine-learning-data-science-linux-dsvm-intro.md#installing-and-configuring-x2go-client)의 지침을 따르십시오.

## <a name="next-steps"></a>다음 단계
### <a name="for-the-windows-data-science-vm"></a>Windows 데이터 과학 VM
* Windows 버전에서 사용할 수 있는 도구를 실행하는 방법에 대한 자세한 내용은 [Microsoft 데이터 과학 가상 컴퓨터 프로비전](machine-learning-data-science-provision-vm.md)을 참조하세요.
* Windows VM에서 데이터 과학 프로젝트에 필요한 다양한 작업을 수행하는 방법에 대한 자세한 내용은 [데이터 과학 가상 컴퓨터로 할 수 있는 10가지 일](machine-learning-data-science-vm-do-ten-things.md)을 참조하세요.

### <a name="for-the-linux-data-science-vm"></a>Linux 데이터 과학 VM
* Linux 버전에서 사용할 수 있는 도구를 실행하는 방법에 대한 자세한 내용은 [Linux 데이터 과학 가상 컴퓨터 프로비전](machine-learning-data-science-linux-dsvm-intro.md)을 참조하세요.
* Linux VM을 통해 몇 가지 일반적인 데이터 과학 작업을 수행하는 방법을 보여주는 연습은 [Linux 데이터 과학 가상 컴퓨터의 데이터 과학](machine-learning-data-science-linux-dsvm-walkthrough.md)을 참조하세요.


