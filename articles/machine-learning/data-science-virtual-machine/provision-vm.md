---
title: Windows Data Science Virtual Machine 만들기
titleSuffix: Azure
description: 분석 및 기계 학습을 수행하기 위해 Azure에서 데이터 과학 Virtual Machine 구성 및 만들기
services: machine-learning
documentationcenter: ''
author: gopitk
manager: cgronlun
ms.custom: seodec18
ms.assetid: e1467c0f-497b-48f7-96a0-7f806a7bec0b
ms.service: machine-learning
ms.subservice: data-science-vm
ms.workload: data-services
ms.devlang: na
ms.topic: article
ms.date: 02/22/2019
ms.author: gokuma
ms.openlocfilehash: 64642da88850e9a0a17173bc50ae9e750cab55a2
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/27/2019
ms.locfileid: "66235094"
---
# <a name="provision-a-windows-data-science-virtual-machine-on-azure"></a>Azure에서 Windows 데이터 과학 Virtual Machine 프로비전

Microsoft Windows DSVM(Data Science Virtual Machine)은 데이터 분석 및 기계 학습용 도구와 함께 미리 설치되고 구성되는 Azure의 Windows Server 2016 VM(가상 머신) 이미지입니다.

## <a name="included-data-science-tools"></a>포함된 데이터 과학 도구

DSVM에는 다음과 같은 도구가 포함되어 있습니다.

* [Azure Machine Learning Service](../service/index.yml) Python SDK
* [Microsoft Machine Learning Server](https://docs.microsoft.com/machine-learning-server/index) 디벨로퍼 버전
* Enthought Python 배포
* Jupyter Notebook(R, Python 및 PySpark 커널 포함)
* Microsoft Visual Studio Community
* Microsoft Power BI Desktop
* Microsoft SQL Server 2017 디벨로퍼 버전
* 독립 실행형 Apache Spark 인스턴스(로컬 개발 및 테스트용)
* [JuliaPro](https://juliacomputing.com/products/juliapro.html)
* 기계 학습 및 데이터 분석 도구는 다음과 같습니다.
  * 심화 학습 프레임워크 - 다양한 AI 프레임워크의 세트가 VM에 포함되어 있습니다. [Microsoft Cognitive Toolkit](https://www.microsoft.com/en-us/cognitive-toolkit/), [TensorFlow](https://www.tensorflow.org/), [Chainer](https://chainer.org/), mxNet 및 Keras
  * [Vowpal Wabbit](https://github.com/JohnLangford/vowpal_wabbit) - 온라인 해시, allreduce, 축소, learning2search 및 활성/대화형 학습과 같은 기술을 지원하는 속성 기계 학습 시스템입니다.
  * [XGBoost](https://xgboost.readthedocs.org/en/latest/) - 빠르고 정확하게 부스트되는 트리 구현을 제공하는 도구입니다.
  * [Rattle](https://togaware.com/rattle/) - R에서 데이터 분석 및 기계 학습을 시작할 수 있는 도구이며, 자동 R 코드 생성 기능을 갖춘 GUI 기반 데이터 탐색 및 모델링이 포함되어 있습니다.
  * [Weka](https://www.cs.waikato.ac.nz/ml/weka/) - Java 형식의 시각적인 데이터 마이닝 및 기계 학습 소프트웨어입니다.
  * [Apache Drill](https://drill.apache.org/) - Apache Hadoop, NoSQL 및 클라우드 스토리지용의 스키마 없는 SQL 쿼리 엔진입니다. Power BI, Microsoft Excel 및 Tableau와 같은 표준 BI 도구에서 NoSQL 및 파일을 쿼리하기 위한 ODBC 및 JDBC 인터페이스를 지원합니다.
* Azure Machine Learning 및 기타 Azure 서비스에서 사용하기 위한 R 및 Python의 라이브러리
* Git(Git Bash 포함)에서 GitHub 및 Azure DevOps가 포함된 소스 코드 리포지토리를 사용하여 작업할 수 있습니다. Git은 Git Bash와 명령 프롬프트에서 모두 액세스할 수 있는 몇 가지 인기 있는 Linux 명령줄 유틸리티를 제공합니다. 예를 들어 awk, sed, perl, grep, find, wget 및 curl이 있습니다.

### <a name="about-data-science"></a>데이터 과학에 대한 정보

데이터 과학은 다음과 같은 일련의 작업을 반복합니다.

1. 데이터 찾기, 로드 및 전처리
1. 모델 빌드 및 테스트
1. 지능형 애플리케이션에서 사용하기 위한 모델 배포

데이터 과학자는 이러한 작업에 몇 가지 도구를 사용합니다. 적절한 버전의 소프트웨어를 찾고 다운로드하여 설치하는 데 시간이 오래 걸릴 수 있습니다. DSVM은 인기 있는 몇 가지 도구가 사전 설치되고 구성되어 Azure에서 프로비전할 수 있는 즉시 사용 가능한 이미지를 제공하여 시간을 절약합니다.

DSVM을 사용하면 분석 프로젝트가 바로 시작됩니다. R, Python, SQL 및 C#을 포함하여 다양한 언어로 작업을 수행할 수 있습니다. Visual Studio는 사용하기 쉬운 IDE(통합 개발 환경)를 제공하여 코드를 개발하고 테스트합니다. VM에 포함된 Azure SDK를 통해 Microsoft의 클라우드 플랫폼에서 다양한 서비스를 사용하여 애플리케이션을 빌드할 수 있습니다.

이 데이터 과학 VM 이미지에 대한 소프트웨어 요금은 부과되지 않습니다. Azure 사용 요금만 지불하면 됩니다. 이 요금은 프로비전하는 가상 머신의 크기에 따라 다릅니다. 계산 요금에 대한 자세한 내용은 [Data Science Virtual Machine](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-ads.windows-data-science-vm?tab=PlansAndPrice) 페이지의 **가격 책정 세부 정보** 섹션에 나와 있습니다.

### <a name="other-dsvm-versions"></a>다른 DSVM 버전

* [Ubuntu](dsvm-ubuntu-intro.md) 이미지. DSVM과 비슷한 많은 도구와 몇 가지 추가 딥 러닝 프레임워크가 있습니다.
* [Linux CentOS](linux-dsvm-intro.md) 이미지.
* Data Science Virtual Machine의 [Windows Server 2012 버전](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-ads.standard-data-science-vm)입니다. 몇 가지 도구는 Windows Server 2016 버전에서만 사용할 수 있습니다. 그렇지 않은 경우 이 문서는 Windows Server 2012 버전에도 적용됩니다.

## <a name="prerequisite"></a>필수 요소

Microsoft Data Science Virtual Machine를 만들려면 Azure 구독이 있어야 합니다. [Azure 평가판](https://azure.com/free)을 참조하세요.

## <a name="create-your-dsvm"></a>DSVM 만들기

DSVM 인스턴스를 만들려면

1. [Azure Portal](https://portal.azure.com/#create/microsoft-dsvm.dsvm-windowsserver-2016)에서 가상 머신 목록으로 이동합니다. 아직 로그인하지 않은 경우 Azure 계정에 로그인하라는 메시지가 표시될 수 있습니다.
1. 아래쪽에 있는 **만들기** 단추를 선택합니다.

   ![configure-data-science-vm](./media/provision-vm/configure-data-science-virtual-machine.png)

1. 각 스크린샷의 오른쪽 창에 표시된 단계를 구성하려면 다음 정보를 입력해야 합니다.

   1. **기본 사항**:
      * **이름**: DSVM의 이름입니다.
      * **VM 디스크 유형**: **SSD** 또는 **HDD**입니다. NC_v1 GPU 인스턴스(NVidia Tesla K80 기반)의 경우 디스크 유형으로 **HDD**를 선택합니다.
      * **사용자 이름**: 관리자 계정 ID입니다.
      * **암호**: 관리자 계정 암호입니다.
      * **구독**: 둘 이상의 구독이 있는 경우 머신을 만들고 요금을 청구할 구독을 선택합니다.
      * **리소스 그룹**. 새 그룹을 만들거나 기존 그룹을 사용할 수 있습니다.
      * **위치** - 가장 적합한 데이터 센터를 선택합니다. 가장 빠른 네트워크 액세스를 위해 대부분의 데이터가 있거나 물리적 위치에 가장 가까운 데이터 센터입니다.
   1. **크기**: 기능 요구 사항과 비용 제약 조건이 충족되는 서버 유형 중 하나를 선택합니다. VM 크기를 더 많이 선택하려면 **모두 보기**를 선택합니다.
   1. **설정**:  
      * **Managed Disks 사용**. Azure에서 VM용 디스크를 관리하도록 하려면 **관리**를 선택합니다. 그렇지 않으면 새 또는 기존 저장소 계정을 지정해야 합니다.  
      * **기타 매개 변수**. 기본값을 사용할 수 있습니다. 기본값 이외의 값을 사용하려면 정보 제공 링크를 마우스로 가리켜 특정 필드에 대한 도움말을 확인합니다.
   1. **요약**: 입력한 모든 정보가 올바른지 확인합니다. **만들기**를 선택합니다.

> [!NOTE]
> * VM에는 **크기** 단계에서 선택한 서버 크기에 대한 계산 비용 이외의 추가 요금이 없습니다.
> * 프로비전에는 약 10-20분이 걸립니다. Azure Portal에서 VM의 상태를 볼 수 있습니다.

## <a name="how-to-access-the-dsvm"></a>DSVM에 액세스하는 방법

VM이 만들어지고 프로비전되면 앞의 **기본 사항** 섹션에서 구성한 관리자 계정 자격 증명을 사용하여 원격 데스크톱에 연결할 수 있습니다. VM에 설치되고 구성된 도구를 사용할 준비가 되었습니다. 대부분의 도구에는 시작 메뉴 타일과 바탕 화면 아이콘을 통해 액세스할 수 있습니다.

또한 Azure VM에서 Jupyter notebook을 실행 하 여 무료 서비스 계층의 제한 사항을 무시 하는 Notebook에는 데이터 과학 VM을 연결할 수 있습니다. 자세한 내용은 [관리 Notebook 프로젝트-계산 계층을 구성 하 고](/azure/notebooks/configure-manage-azure-notebooks-projects#compute-tier)입니다.

## <a name="tools-installed-on-the-microsoft-data-science-virtual-machine"></a>Microsoft 데이터 과학 Virtual Machine에 설치된 도구

DSVM에 설치되는 도구에 대한 자세한 정보:

### <a name="microsoft-machine-learning-server-developer-edition"></a>Microsoft Machine Learning Server 디벨로퍼 버전

Machine Learning Server 디벨로퍼 버전이 VM에 설치되었으므로 분석을 위해 확장 가능한 R 또는 Python용 Microsoft 엔터프라이즈 라이브러리를 사용할 수 있습니다. 이전에는 Microsoft R Server로 알려진 Machine Learning Server는 광범위하게 배포 가능한 엔터프라이즈급 분석 플랫폼입니다. R 및 Python 둘 다에 사용할 수 있습니다. 또한 확장 가능하고, 상업적으로 지원되고, 안전하기도 합니다.

Machine Learning Server는 다양한 빅 데이터 통계, 예측 모델링 및 기계 학습 작업을 지원합니다. 탐색, 분석, 시각화 및 모델링 등 모든 범위의 분석을 지원합니다. 오픈 소스 R 및 Python을 사용하고 확장함으로써 Machine Learning Server는 R 및 Python 스크립트 및 함수와 호환됩니다. 또한 CRAN, pip 및 Conda 패키지와 호환되어 엔터프라이즈 규모의 데이터를 분석할 수 있습니다.

Machine Learning Server는 데이터에 대한 병렬 및 청크 분할 처리를 추가하여 오픈 소스 R의 메모리 내 한도를 해결합니다. 따라서 주 메모리에 적합한 것보다 훨씬 더 큰 빅 데이터에 대한 분석을 실행할 수 있습니다. Visual Studio Community는 VM에 포함되어 있습니다. R 또는 Python을 통해 작업할 수 있는 완벽한 IDE를 제공하는 Visual Studio용 R 도구와 PTVS(Visual Studio용 Python Tools) 확장이 있습니다. 또한 [RStudio](https://www.rstudio.com) 및 [PyCharm Community 버전](https://www.jetbrains.com/pycharm/)과 같은 다른 IDE도 VM에 제공됩니다.

### <a name="python"></a>Python

Python을 사용하여 개발할 수 있도록 Anaconda Python 배포 2.7 및 3.6이 설치됩니다. 이러한 배포에는 가장 인기 있는 약 300개의 수학, 엔지니어링 및 데이터 분석 패키지와 함께 기본 Python이 있습니다. Visual Studio Community 2017에 설치된 PTVS를 사용할 수 있습니다. 또는 Anaconda와 함께 번들로 제공되는 IDE(예: IDLE 또는 Spyder) 중 하나를 사용할 수 있습니다. 이러한 패키지 중 하나를 검색하여 시작합니다(Win+S).

> [!NOTE]
> Anaconda Python 2.7의 Python Tools for Visual Studio를 가리키려면 각 버전에 대한 사용자 지정 환경을 만들어야 합니다. Visual Studio 2017 Community Edition에서 이러한 환경 경로를 설정하려면 **도구** > **Python 도구** > **Python 환경**으로 차례로 이동합니다. 그런 다음, **+ 사용자 지정**을 선택합니다.

Anaconda Python 3.6은 **C:\Anaconda**에 설치됩니다. Anaconda Python 2.7은 **c:\Anaconda\envs\python2**에 설치됩니다. 자세한 단계는 [PTVS 설명서](https://docs.microsoft.com/visualstudio/python/installing-python-interpreters)를 참조하세요.

### <a name="the-jupyter-notebook"></a>Jupyter Notebook

Anaconda 배포는 코드와 분석을 공유하는 환경인 Jupyter Notebook과 함께 제공됩니다. Jupyter Notebook 서버는 Python 2.7, Python 3.x, PySpark, Julia 및 R 커널로 미리 구성되어 있습니다. Jupyter 서버를 시작하고 브라우저를 실행하여 노트북 서버에 액세스하려면 **Jupyter Notebook** 바탕 화면 아이콘을 사용합니다.

Python과 R에서 몇 가지 노트북 샘플이 패키지되었습니다. Jupyter에 액세스하면 노트북에서 다음 기술을 사용하는 방법을 보여 줍니다.

* Machine Learning Server
* SQL Server Machine Learning Services(데이터베이스 내 분석)
* Python
* Microsoft Cognitive Toolkit
* Tensorflow
* 다른 Azure 기술

이전 단계에서 만든 암호를 사용하여 Jupyter Notebook에 인증하면 노트북 홈 페이지에서 샘플에 대한 링크를 찾을 수 있습니다.

### <a name="visual-studio-community-2017"></a>Visual Studio Community 2017

DSVM에는 Visual Studio Community가 포함되어 있습니다. Microsoft에서 제공하여 평가 용도 및 소규모 팀에 사용할 수 있는 인기 있는 IDE 평가판 버전입니다. [사용 조건](https://www.visualstudio.com/support/legal/mt171547)을 참조하세요.

바탕 화면 아이콘이나 **시작** 메뉴를 사용하여 Visual Studio를 엽니다. 프로그램(Win+S)을 검색한 다음, **Visual Studio**를 찾습니다. 여기서는 C#, Python, R, node.js와 같은 언어로 프로젝트를 만들 수 있습니다. 설치된 플러그 인을 사용하면 다음 Azure 서비스를 사용하여 편리하게 작업할 수 있습니다.

* Azure Data Catalog
* Azure HDInsight Hadoop 및 Spark
* Azure Data Lake

Azure Machine Learning에 원활하게 통합되고 AI 애플리케이션을 빠르게 빌드하는 데 도움이 되는 **Azure Machine Learning for Visual Studio Code**라는 플러그 인도 있습니다.

> [!NOTE]
> 평가 기간이 만료되었다는 메시지가 표시될 수 있습니다. Microsoft 계정 자격 증명을 입력합니다. 또는 새 체험 계정을 만들어 Visual Studio Community에 액세스할 수 있습니다.

### <a name="sql-server-2017-developer-edition"></a>SQL Server 2017 디벨로퍼 버전

DSVM에는 Machine Learning Services가 포함된 SQL Server 2017 디벨로퍼 버전이 함께 제공됩니다. 이 SQL Server 버전은 R 또는 Python을 포함하며 데이터베이스 내 분석을 실행할 수 있습니다. Machine Learning Services는 지능형 애플리케이션을 개발하고 배포하기 위한 플랫폼을 제공합니다. 이러한 언어와 커뮤니티의 많은 패키지를 사용하여 모델을 만들고 SQL Server 데이터에 대한 예측을 생성할 수 있습니다. 데이터베이스 내 Machine Learning Services는 SQL Server 내에서 R 및 Python 언어를 모두 통합하므로 데이터와의 긴밀한 분석을 유지할 수 있습니다. 이 통합은 데이터 이동과 관련된 비용 및 보안 위험을 제거합니다.

> [!NOTE]
> SQL Server 디벨로퍼 버전은 개발 및 테스트 용도로만 사용할 수 있습니다. 프로덕션에서 실행하려면 라이선스가 필요합니다.

Microsoft SQL Server Management Studio를 시작하여 SQL Server에 액세스할 수 있습니다. VM 이름은 **서버 이름**으로 채워집니다. Windows에서 관리자 권한으로 로그인할 때 Windows 인증을 사용합니다. SQL Server Management Studio를 사용하면 다른 사용자를 만들고, 데이터베이스를 만들며, 데이터를 가져오고, SQL 쿼리를 실행할 수 있습니다.

SQL Machine Learning Services를 사용하여 데이터베이스 내 분석을 사용하도록 설정하려면, 서버 관리자 권한으로 로그인한 후에 SQL Server Management Studio에서 일회성 작업으로 다음 명령을 실행합니다.

```
CREATE LOGIN [%COMPUTERNAME%\SQLRUserGroup] FROM WINDOWS
```

`%COMPUTERNAME%`을 VM 이름으로 바꿉니다.

### <a name="azure"></a>Azure

몇 개의 Azure 도구가 VM에 설치됩니다.

* 바탕 화면 바로 가기를 사용하면 Azure SDK 설명서로 이동합니다.
* **AzCopy**를 사용하여 Azure Storage 계정 간에 데이터를 복사합니다. 사용법을 보려면 명령 프롬프트에서 **Azcopy**를 입력합니다.
* **Azure Storage 탐색기**를 사용하여 Azure Storage 계정에 저장한 개체를 찾아봅니다. Azure Storage 간에도 데이터를 복사합니다. 이 도구에 액세스하려면 **검색** 필드에서 **Storage Explorer**를 입력합니다. 또는 Windows **시작** 메뉴에서 찾습니다.
* **Adlcopy**는 데이터를 Azure Data Lake로 복사합니다. 사용법을 보려면 명령 프롬프트에서 **adlcopy**를 입력합니다.
* **dtui**는 클라우드의 NoSQL 데이터베이스인 Azure Cosmos DB 간에 데이터를 복사합니다. 명령 프롬프트에서 **dtui**를 입력합니다.
* **Azure Data Factory Integration Runtime**은 온-프레미스 데이터 원본과 클라우드 간에 데이터를 복사합니다. Azure Data Factory와 같은 도구 내에서 사용합니다.
* **Microsoft Azure PowerShell**을 사용하여 PowerShell 스크립팅 언어로 Azure 리소스를 관리할 수 있습니다. VM에도 설치됩니다.

### <a name="power-bi"></a>Power BI

DSVM은 대시보드 및 시각화를 작성하는 데 도움을 주기 위해 **Power BI Desktop**과 함께 설치됩니다. 이 도구를 사용하여 여러 원본에서 데이터를 추출하고, 대시보드 및 보고서를 작성하며, 클라우드에 게시합니다. 자세한 내용은 [Power BI](https://powerbi.microsoft.com) 사이트를 참조하세요. **시작** 메뉴에서 Power BI 데스크톱을 찾을 수 있습니다.

> [!NOTE]
> Power BI에 액세스하려면 Microsoft Office 365 계정이 필요합니다.

### <a name="azure-machine-learning-service-python-sdk"></a>Azure Machine Learning Service Python SDK

데이터 과학자 및 AI 개발자는 Python용 Azure Machine Learning SDK를 사용하여 [Azure Machine Learning Service](../service/overview-what-is-azure-ml.md)에서 기계 학습 워크플로를 빌드 및 실행합니다. TensorFlow 및 scikit-learn 등의 오픈 소스 프레임워크를 사용하여 Jupyter Notebook 또는 다른 Python IDE에서 서비스와 상호 작용할 수 있습니다.

Python SDK 사용을 시작하려면 [Python을 사용하여 Azure Machine Learning 시작](../service/quickstart-create-workspace-with-python.md)을 참조하세요.

Python SDK는 Microsoft Data Science Virtual Machine에 미리 설치되어 있습니다.

## <a name="more-microsoft-development-tools"></a>기타 Microsoft 개발 도구

[Microsoft 웹 플랫폼 설치 관리자](https://www.microsoft.com/web/downloads/platform.aspx)를 사용하여 다른 Microsoft 개발 도구를 찾고 다운로드할 수 있습니다. 또한 Microsoft Data Science Virtual Machine 바탕 화면에는 해당 도구에 대한 바로 가기도 있습니다.  

## <a name="important-directories-on-the-vm"></a>VM의 중요 디렉터리

| 항목 | 디렉터리 |
| --- | --- |
| Jupyter Notebook 서버 구성 | C:\ProgramData\jupyter |
| Jupyter Notebook 예제 홈 디렉터리 | C:\dsvm\notebooks and c:\users\\<username\>\notebooks |
| 다른 샘플 | C:\dsvm\samples |
| Anaconda, 기본값: Python 3.6 | C:\Anaconda |
| Anaconda Python 2.7 환경 | C:\Anaconda\envs\python2 |
| Microsoft Machine Learning Server(독립 실행형) Python | C:\Program Files\Microsoft\ML Server\PYTHON_SERVER |
| 기본 R 인스턴스, Machine Learning Server(독립 실행형) | C:\Program Files\Microsoft\ML Server\R_SERVER |
| SQL Machine Learning Services 데이터베이스 내 인스턴스 디렉터리 | C:\Program Files\Microsoft SQL Server\MSSQL14.MSSQLSERVER |
| 기타 도구 | C:\dsvm\tools |

> [!NOTE]
> 2018년 3월 이전에 릴리스된 DSVM의 Windows Server 2012 버전 및 Windows Server 2016 버전에서 기본 Anaconda 환경은 Python 2.7입니다. 보조 환경은 **:\Anaconda\envs\py35**에 있는 Python 3.5입니다.

## <a name="next-steps"></a>다음 단계

* **시작** 메뉴를 선택하여 데이터 과학 VM의 도구를 탐색합니다.
* [Azure Machine Learning Service란?](../service/overview-what-is-azure-ml.md)을 참조하여 Azure Machine Learning Service에 대해 알아보고 제공되는 [빠른 시작 및 자습서](../service/index.yml)를 사용해 보세요.
* 엔터프라이즈 규모의 데이터 분석을 지원하는 R에서 RevoScaleR 라이브러리를 사용하는 샘플을 보려면 파일 탐색기에서 **C:\Program Files\Microsoft\ML Server\R_SERVER\library\RevoScaleR\demoScripts**로 이동합니다.  
* [Data Science Virtual Machine으로 할 수 있는 10가지 작업](https://aka.ms/dsvmtenthings) 문서를 참조합니다.
* [팀 데이터 과학 프로세스](../team-data-science-process/index.yml)를 사용하여 종단 간 분석 솔루션을 체계적으로 구축하는 방법을 알아봅니다.
* Azure에서 Azure Machine Learning 및 관련 데이터 서비스를 사용하는 기계 학습 및 데이터 분석 샘플을 보려면 [Azure AI Gallery](https://gallery.cortanaintelligence.com)를 방문합니다. 또한 가상 머신의 **시작** 메뉴 및 바탕 화면에는 이 갤러리에 대한 아이콘도 제공됩니다.
