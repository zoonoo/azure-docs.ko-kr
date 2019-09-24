---
title: '참조: Windows DSVM'
description: Windows Data Science VM에 포함 된 도구에 대 한 세부 정보
author: gvashishtha
ms.service: machine-learning
ms.subservice: data-science-vm
ms.author: gopalv
ms.date: 09/11/2019
ms.topic: reference
ms.openlocfilehash: be4397de477891f2a698c9f7dcb131da79479529
ms.sourcegitcommit: 8a717170b04df64bd1ddd521e899ac7749627350
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/23/2019
ms.locfileid: "71200019"
---
# <a name="reference-windows-data-science-virtual-machine"></a>참조: Windows Data Science Virtual Machine

Windows Data Science Virtual Machine에서 사용 가능한 도구 목록은 아래를 참조 하십시오. 

## <a name="microsoft-machine-learning-server-developer-edition"></a>Microsoft Machine Learning Server 디벨로퍼 버전

Machine Learning Server 디벨로퍼 버전이 VM에 설치되어 있으므로 Microsoft Enterprise Library를 분석에 사용할 수 있습니다. Machine Learning Server(이전의 Microsoft R Server)는 광범위하게 배포할 수 있는 분석 플랫폼입니다. 확장성이 있고 상업적으로 지원됩니다.

Machine Learning Server는 다양한 빅 데이터 통계, 예측 모델링 및 기계 학습 작업을 지원합니다. 탐색, 분석, 시각화 및 모델링 등 모든 범위의 분석을 지원합니다. 오픈 소스 R 및 Python을 사용하고 확장함으로써 Machine Learning Server는 R 및 Python 스크립트 및 함수와 호환됩니다. 또한 CRAN, pip 및 Conda 패키지와 호환되어 엔터프라이즈 규모의 데이터를 분석할 수 있습니다.

Machine Learning Server는 데이터에 대한 병렬 및 청크 분할 처리를 추가하여 오픈 소스 R의 메모리 내 한도를 해결합니다. 따라서 주 메모리에 적합한 것보다 훨씬 더 큰 빅 데이터에 대한 분석을 실행할 수 있습니다. 

Visual Studio Community는 VM에 포함되어 있습니다. R 또는 Python을 통해 작업할 수 있는 완벽한 IDE를 제공하는 Visual Studio용 R 도구와 PTVS(Visual Studio용 Python Tools) 확장이 있습니다. 또한 [RStudio](https://www.rstudio.com) 및 [PyCharm Community 버전](https://www.jetbrains.com/pycharm/)과 같은 다른 IDE도 VM에 제공됩니다.

## <a name="python"></a>Python

Python을 사용하여 개발할 수 있도록 Anaconda Python 배포 2.7 및 3.6이 설치됩니다. 이러한 배포에는 가장 인기 있는 약 300개의 수학, 엔지니어링 및 데이터 분석 패키지와 함께 기본 Python이 있습니다. Visual Studio Community 2017에 설치된 PTVS를 사용할 수 있습니다. 또는 Anaconda와 함께 번들로 제공되는 IDE(예: IDLE 또는 Spyder) 중 하나를 사용할 수 있습니다. 이러한 패키지 중 하나를 검색(Windows 로고 키+S)하여 엽니다.

> [!NOTE]
> Anaconda Python 2.7의 Python Tools for Visual Studio를 가리키려면 각 버전에 대한 사용자 지정 환경을 만들어야 합니다. Visual Studio 2017 Community Edition에서 이러한 환경 경로를 설정하려면 **도구** > **Python 도구** > **Python 환경**으로 이동합니다. 그런 다음, **+ 사용자 지정**을 선택합니다.

Anaconda Python 3.6은 C:\Anaconda에 설치됩니다. Anaconda Python 2.7은 C:\Anaconda\envs\python2에 설치됩니다. 자세한 단계는 [PTVS 설명서](https://docs.microsoft.com/visualstudio/python/installing-python-interpreters)를 참조하세요.

## <a name="the-jupyter-notebook"></a>Jupyter Notebook

Anaconda 배포는 코드와 분석을 공유하는 환경인 Jupyter Notebook과 함께 제공됩니다. Jupyter Notebook 서버는 Python 2.7, Python 3.x, PySpark, Julia 및 R 커널로 미리 구성되어 있습니다. Jupyter 서버를 시작하고 브라우저를 열어서 노트북 서버에 액세스하려면 **Jupyter Notebook** 바탕 화면 아이콘을 사용합니다.

Python과 R에서 몇 가지 노트북 샘플이 패키지되었습니다. Jupyter에 액세스하면 노트북에서 다음 기술을 사용하는 방법을 보여 줍니다.

* Machine Learning Server
* SQL Server Machine Learning Services(데이터베이스 내 분석)
* Python
* Microsoft Cognitive 도구 키트
* TensorFlow
* 다른 Azure 기술

이전에 만든 암호를 사용하여 Jupyter Notebook에 인증하면 노트북 홈 페이지에서 샘플에 대한 링크를 찾을 수 있습니다.

## <a name="visual-studio-community-2017"></a>Visual Studio Community 2017

DSVM에는 Visual Studio Community가 포함되어 있습니다. Microsoft에서 제공하여 평가 용도 및 소규모 팀에 사용할 수 있는 인기 있는 IDE 평가판 버전입니다. [Microsoft 소프트웨어 사용 조건](https://www.visualstudio.com/support/legal/mt171547)을 참조하세요.

바탕 화면 아이콘이나 **시작** 메뉴를 사용하여 Visual Studio를 엽니다. 프로그램을 검색(Windows 로고 키+S)한 다음, **Visual Studio**를 찾습니다. 여기서는 C#, Python, R, Node.js와 같은 언어로 프로젝트를 만들 수 있습니다. 설치된 플러그 인을 사용하면 다음 Azure 서비스를 사용하여 편리하게 작업할 수 있습니다.

* Azure Data Catalog
* Hadoop 및 Spark용 Azure HDInsight
* Azure Data Lake

Azure Machine Learning for Visual Studio Code라는 플러그인은 Azure Machine Learning과 통합되어 AI 애플리케이션을 신속하게 빌드하는 데 유용합니다.

> [!NOTE]
> 평가 기간이 만료되었다는 메시지가 표시될 수 있습니다. Microsoft 계정 자격 증명을 입력합니다. 또는 새 체험 계정을 만들어 Visual Studio Community에 액세스할 수 있습니다.

## <a name="sql-server-2017-developer-edition"></a>SQL Server 2017 디벨로퍼 버전

DSVM에는 Machine Learning Services가 포함된 SQL Server 2017 디벨로퍼 버전이 함께 제공됩니다. 이 SQL Server 버전은 R 또는 Python을 포함하며 데이터베이스 내 분석을 실행할 수 있습니다. 

Machine Learning Services는 지능형 애플리케이션을 개발하고 배포하기 위한 플랫폼을 제공합니다. 이러한 언어와 커뮤니티의 많은 패키지를 사용하여 모델을 만들고 SQL Server 데이터에 대한 예측을 생성할 수 있습니다. 데이터베이스 내 Machine Learning Services는 SQL Server 내에서 R 및 Python 언어를 모두 통합하므로 데이터와의 긴밀한 분석을 유지할 수 있습니다. 이 통합은 데이터 이동과 관련된 비용 및 보안 위험을 제거합니다.

> [!NOTE]
> SQL Server 디벨로퍼 버전은 개발 및 테스트 용도로만 사용할 수 있습니다. 프로덕션에서 실행하려면 라이선스가 필요합니다.

Microsoft SQL Server Management Studio를 열어서 SQL Server에 액세스할 수 있습니다. VM 이름이 **서버 이름**으로 채워집니다. Windows에서 관리자 권한으로 로그인할 때 Windows 인증을 사용합니다. SQL Server Management Studio를 사용하면 다른 사용자를 만들고, 데이터베이스를 만들며, 데이터를 가져오고, SQL 쿼리를 실행할 수 있습니다.

SQL Server Machine Learning Services를 사용하여 데이터베이스 내 분석을 사용하도록 설정하려면, 서버 관리자 권한으로 로그인한 후에 SQL Server Management Studio에서 일회성 작업으로 다음 명령을 실행합니다.

```
CREATE LOGIN [%COMPUTERNAME%\SQLRUserGroup] FROM WINDOWS
```

`%COMPUTERNAME%`을 VM 이름으로 바꿉니다.

## <a name="azure"></a>Azure

몇 개의 Azure 도구가 VM에 설치됩니다.

* 바탕 화면 바로 가기를 사용하면 Azure SDK 설명서로 이동합니다.
* AzCopy를 사용하여 Azure Storage 계정 간에 데이터를 복사합니다. 사용법을 보려면 명령 프롬프트에서 **Azcopy**를 입력합니다.
* Azure Storage Explorer를 사용하여 Azure Storage 계정에 저장한 개체를 찾아봅니다. Azure Storage 간에도 데이터를 복사합니다. 이 도구에 액세스하려면 **검색** 필드에서 **Storage Explorer**를 입력합니다. 또는 Windows **시작** 메뉴에서 찾습니다.
* AdlCopy는 데이터를 Azure Data Lake로 복사합니다. 사용법을 보려면 명령 프롬프트에서 **adlcopy**를 입력합니다.
* dtui 도구는 클라우드의 NoSQL 데이터베이스인 Azure Cosmos DB 간에 데이터를 복사합니다. 명령 프롬프트에서 **dtui**를 입력합니다.
* 통합 런타임은 온-프레미스 데이터 원본과 클라우드 간에 데이터를 복사합니다. Azure Data Factory와 같은 도구 내에서 사용합니다.
* Azure PowerShell을 사용하여 PowerShell 스크립팅 언어로 Azure 리소스를 관리할 수 있습니다. VM에도 설치됩니다.

## <a name="power-bi"></a>Power BI

DSVM에는 대시보드 및 시각화를 작성할 수 있도록 Power BI Desktop이 설치되어 있습니다. 이 도구를 사용하여 여러 원본에서 데이터를 추출하고, 대시보드 및 보고서를 작성하며, 클라우드에 게시합니다. 자세한 내용은 [Power BI 사이트](https://powerbi.microsoft.com) 사이트를 참조하세요. **시작** 메뉴에서 Power BI 데스크톱을 찾을 수 있습니다.

> [!NOTE]
> Power BI에 액세스하려면 Microsoft Office 365 계정이 필요합니다.

## <a name="azure-machine-learning-sdk-for-python"></a>Python용 Azure Machine Learning SDK

데이터 과학자 및 AI 개발자는 Python용 Azure Machine Learning SDK를 사용하여 [Azure Machine Learning Service](../service/overview-what-is-azure-ml.md)에서 기계 학습 워크플로를 빌드 및 실행합니다. TensorFlow 및 scikit-learn 등의 오픈 소스 프레임워크를 사용하여 Jupyter Notebook 또는 다른 Python IDE에서 서비스와 상호 작용할 수 있습니다.

Python SDK는 Microsoft Data Science Virtual Machine에 미리 설치되어 있습니다. Python SDK 사용을 시작하려면 [Python을 사용하여 Azure Machine Learning 시작](../service/quickstart-create-workspace-with-python.md)을 참조하세요.

## <a name="more-microsoft-development-tools"></a>Microsoft 개발 도구 추가

[Microsoft 웹 플랫폼 설치 관리자](https://www.microsoft.com/web/downloads/platform.aspx)를 사용하여 다른 Microsoft 개발 도구를 찾고 다운로드할 수 있습니다. 또한 Microsoft Data Science Virtual Machine 바탕 화면에는 해당 도구에 대한 바로 가기도 있습니다.  

## <a name="important-directories-on-the-virtual-machine"></a>가상 컴퓨터의 중요 한 디렉터리

| 항목 | 디렉터리 |
| --- | --- |
| Jupyter Notebook 서버 구성 | C:\ProgramData\jupyter |
| Jupyter Notebook 예제 홈 디렉터리 | C:\dsvm\notebooks and c:\users\\<username\>\notebooks |
| 다른 샘플 | C:\dsvm\samples |
| Anaconda, 기본값: Python 3.6 | C:\Anaconda |
| Anaconda Python 2.7 환경 | C:\Anaconda\envs\python2 |
| Python용 Microsoft Machine Learning Server(독립 실행형) | C:\Program Files\Microsoft\ML Server\PYTHON_SERVER |
| 기본 R 인스턴스, Machine Learning Server(독립 실행형) | C:\Program Files\Microsoft\ML Server\R_SERVER |
| SQL Server Machine Learning Services 데이터베이스 내 인스턴스 디렉터리 | C:\Program Files\Microsoft SQL Server\MSSQL14.MSSQLSERVER |
| 기타 도구 | C:\dsvm\tools |

> [!NOTE]
> 2018년 3월 이전에 릴리스된 DSVM의 Windows Server 2012 버전 및 Windows Server 2016 버전에서 기본 Anaconda 환경은 Python 2.7입니다. 보조 환경은 C:\Anaconda\envs\py35에 있는 Python 3.5입니다.

## <a name="next-steps"></a>다음 단계

추가 질문이 있나요? [지원 티켓](https://azure.microsoft.com/support/create-ticket/)을 만드는 것이 좋습니다.
