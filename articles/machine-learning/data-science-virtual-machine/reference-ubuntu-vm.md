---
title: '참조: Ubuntu Data Science Virtual Machine'
titleSuffix: Azure Data Science Virtual Machine
description: Ubuntu Data Science Virtual Machine에 포함된 도구에 대한 세부 정보
author: timoklimmer
services: machine-learning
ms.service: data-science-vm
ms.custom: devx-track-python
ms.author: tklimmer
ms.date: 05/12/2021
ms.topic: reference
ms.openlocfilehash: d2a960b2a924d06b7ecc2a2f613fdd5a5695071a
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/19/2021
ms.locfileid: "110076387"
---
# <a name="reference-ubuntu-linux-data-science-virtual-machine"></a>참조: Ubuntu(Linux) Data Science Virtual Machine

Ubuntu Data Science Virtual Machine에서 사용 가능한 도구 목록은 아래를 참조하세요. 

## <a name="deep-learning-libraries"></a>딥 러닝 라이브러리

### <a name="pytorch"></a>PyTorch

[PyTorch](https://pytorch.org/)는 기계 학습 알고리즘을 광범위하게 지원하며 널리 사용되는 공학용 계산 프레임워크입니다. 컴퓨터에 GPU가 내장된 경우 해당 GPU를 사용하여 딥 러닝을 가속화할 수 있습니다. PyTorch는 `py38_pytorch` 환경에서 사용할 수 있습니다.

### <a name="h2o"></a>H2O

H2O는 빠른 메모리 내 분산형 기계 학습 및 예측 분석 플랫폼입니다. Python 패키지는 루트 및 py35 Anaconda 환경 둘 다에 설치됩니다. R 패키지도 설치됩니다. 

명령줄에서 H2O를 열려면 `java -jar /dsvm/tools/h2o/current/h2o.jar`을 실행합니다. 구성해야 할 수도 있는 다양한 [명령줄 옵션](http://docs.h2o.ai/h2o/latest-stable/h2o-docs/starting-h2o.html#from-the-command-line)이 있습니다. 시작하려면 `http://localhost:54321`로 이동하여 Flow 웹 UI에 액세스할 수 있습니다. 샘플 노트북은 JupyterHub에서도 사용할 수 있습니다.

### <a name="tensorflow"></a>TensorFlow

[TensorFlow](https://tensorflow.org)는 Google의 심층 학습 라이브러리입니다. 이는 데이터 흐름 그래프를 사용한 수치 계산용 오픈 소스 소프트웨어 라이브러리입니다. 컴퓨터에 기본 GPU가 있는 경우 해당 GPU를 사용하여 심층 학습을 가속화할 수 있습니다. TensorFlow는 `py38_tensorflow` Conda 환경에서 사용할 수 있습니다.


## <a name="python"></a>Python

DSVM에는 Python 버전이 Python 3.8 또는 Python 3.6인 여러 Python 환경이 사전 설치되어 있습니다.
설치된 환경의 전체 목록을 보려면 명령줄에서 `conda env list`를 실행합니다.


## <a name="jupyter"></a>Jupyter

DSVM은 코드 및 분석을 공유하는 환경인 Jupyter와 함께 제공됩니다. Jupyter는 다음과 같은 다양한 종류의 DSVM에 설치됩니다.
 - Jupyter Lab
 - Jupyter 노트북
 - Jupyter Hub

Jupyter Lab을 열려면 애플리케이션 메뉴에서 Jupyter를 열거나 바탕 화면 아이콘을 클릭합니다. 또는 명령줄에서 `jupyter lab`을 실행하여 Jupyter Lab을 열 수 있습니다.

Jupyter Notebook을 열려면 명령줄을 열고 `jupyter notebook`을 실행합니다.

Jupyter Hub를 열고 **https://\<VM DNS name or IP address\>:8000/** 을 엽니다. 그러면 로컬 Linux 사용자 이름 및 암호를 입력하라는 메시지가 표시됩니다.

> [!NOTE]
> 인증서 경고가 나타나는 경우 계속 진행하세요.

> [!NOTE]
> Ubuntu 이미지의 경우 VM이 프로비저닝될 때 포트 8000이 방화벽에 기본적으로 열립니다.


## <a name="apache-spark-standalone"></a>Apache Spark 독립 실행형

Apache Spark의 독립 실행형 인스턴스는 Linux DSVM에 미리 설치되어 Spark 애플리케이션을 먼저 로컬로 개발한 후에 대형 클러스터에서 테스트 및 배포하는 데 도움이 됩니다. 

Jupyter 커널을 통해 PySpark 프로그램을 실행할 수 있습니다. Jupyter를 열어 **새로 만들기** 단추를 선택하면 사용 가능한 커널의 목록이 표시됩니다. **Spark - Python** 은 Python 언어를 사용하여 Spark 애플리케이션을 빌드할 수 있는 PySpark 커널입니다. 또한 VS.Code 또는 PyCharm과 같은 Python IDE를 사용하여 Spark 프로그램을 빌드할 수도 있습니다. 

이 독립 실행형 인스턴스에서 Spark 스택은 호출 클라이언트 프로그램 내에서 실행됩니다. 이 기능을 사용하면 Spark 클러스터에서 개발하는 것에 비해 문제를 더 빠르고 쉽게 해결할 수 있습니다.


## <a name="ides-and-editors"></a>IDE 및 편집기

VS.Code, PyCharm, RStudio, IntelliJ, vi/Vim, Emacs를 포함한 몇 가지 코드 편집기 중에서 선택할 수 있습니다. 

VS.Code, PyCharm, RStudio 및 IntelliJ는 그래픽 편집기입니다. 이를 사용하려면 그래픽 데스크톱에 로그인해야 합니다. 데스크톱 및 애플리케이션 메뉴 바로 가기를 사용하여 엽니다.

Vim 및 Emacs는 텍스트 기반 편집기입니다. Emacs의 경우 ESS 추가 기능 패키지를 통해 Emacs 편집기 내에서 R을 더 쉽게 사용할 수 있습니다. 자세한 내용은 [ESS 웹 사이트](https://ess.r-project.org/)에서 확인할 수 있습니다.


## <a name="databases"></a>데이터베이스

### <a name="graphical-sql-client"></a>그래픽 SQL 클라이언트

그래픽 SQL 클라이언트인 SQuirrel SQL은 다양한 데이터베이스(예: Microsoft SQL Server 및 MySQL)에 연결하고 SQL 쿼리를 실행할 수 있습니다. SQuirrel SQL을 여는 가장 빠른 방법은 그래픽 데스크톱 세션(예: X2Go 클라이언트를 통해) 애플리케이션 메뉴를 사용하는 것입니다.

SQuirrel SQL을 처음 사용하기 전에 드라이버 및 데이터베이스 별칭을 설정합니다. JDBC 드라이버는 /usr/share/java/jdbcdrivers에 있습니다.

자세한 내용은 [SQuirrel SQL](http://squirrel-sql.sourceforge.net/index.php?page=screenshots)을 참조하세요.

### <a name="command-line-tools-for-accessing-microsoft-sql-server"></a>Microsoft SQL Server에 액세스하기 위한 명령줄 도구

SQL Server용 ODBC 드라이버 패키지에서는 다음의 두 가지 명령줄 도구도 제공됩니다.

- **bcp**: bcp 도구는 Microsoft SQL Server 인스턴스와 사용자 지정 형식의 데이터 파일 간에 데이터를 대량 복사합니다. bcp 도구를 사용하여 많은 수의 새 행을 SQL Server 테이블로 가져오거나 데이터를 테이블에서 데이터 파일로 내보낼 수 있습니다. 데이터를 테이블로 가져오려면 해당 테이블에 대해 만든 서식 파일을 사용해야 합니다. 또는 테이블의 구조와 해당 열에 유효한 데이터 형식을 이해해야 합니다.

자세한 내용은 [bcp를 사용하여 연결](/sql/connect/odbc/linux-mac/connecting-with-bcp)을 참조하세요.

- **sqlcmd**: sqlcmd 도구를 사용하여 Transact-SQL 문을 입력할 수 있습니다. 또한 명령 프롬프트에서 시스템 프로시저 및 스크립트 파일을 입력할 수 있습니다. 이 도구는 ODBC를 사용하여 Transact-SQL 일괄 처리를 실행합니다.

  자세한 내용은 [sqlcmd를 사용하여 연결](/sql/connect/odbc/linux-mac/connecting-with-sqlcmd)을 참조하세요.

  > [!NOTE]
  > 이 도구는 Linux 및 Windows 플랫폼 간에 약간의 차이가 있습니다. 자세한 내용은 (영문) 설명서를 참조하세요.

### <a name="database-access-libraries"></a>데이터베이스 액세스 라이브러리

데이터베이스 액세스용 라이브러리는 R 및 Python에서 사용할 수 있습니다.

* R에서는 RODBC 패키지 또는 dplyr 패키지를 사용하여 데이터베이스 서버에서 SQL 문을 쿼리하거나 실행할 수 있습니다.
* Python에서 pyodbc 라이브러리는 ODBC를 기본 계층으로 사용하여 데이터베이스 액세스를 제공합니다.  

## <a name="azure-tools"></a>Azure 도구

다음 Azure 도구가 VM에 설치됩니다.

* **Azure CLI**: Azure에서 명령줄 인터페이스를 사용하여 셸 명령을 통해 Azure 리소스를 만들고 관리할 수 있습니다. Azure 도구를 열려면 **azure help** 를 입력합니다. 자세한 내용은 [Azure CLI 설명서 페이지](/cli/azure/get-started-with-az-cli2)를 참조하세요.
* **Azure Storage Explorer**: Azure Storage Explorer는 Azure 스토리지 계정에 저장한 개체를 검색하고 Azure Blob 간에 데이터를 업로드 및 다운로드하는 데 사용할 수 있는 그래픽 도구입니다. 바탕 화면 바로 가기 아이콘을 사용하여 Storage Explorer에 액세스할 수 있습니다. 셸 프롬프트에서 **StorageExplorer** 를 입력하여 열 수도 있습니다. X2Go 클라이언트에서 로그인하거나 X11 전달이 설정되어 있어야 합니다.
* **Azure 라이브러리**: 다음은 미리 설치된 라이브러리 중 일부입니다.
  
  * **Python**:: Python의 Azure 관련 라이브러리는 *azure*, *azureml*, *pydocumentdb* 및 *pyodbc* 입니다. 처음 세 개의 라이브러리를 사용하면 Azure Storage 서비스, Azure Machine Learning 및 Azure Cosmos DB(Azure의 NoSQL 데이터베이스)에 액세스할 수 있습니다. 네 번째 라이브러리인 pyodbc(SQL Serve용 Microsoft ODBC 드라이버와 함께)는 ODBC 인터페이스를 사용하여 Python에서 SQL Server, Azure SQL Database 및 Azure Synapse Analytics에 액세스할 수 있습니다. **pip list** 를 입력하면 나열된 라이브러리를 모두 확인할 수 있습니다. Python 2.7 및 3.5 환경 모두에서 이 명령을 실행해야 합니다.
  * **R**: R의 Azure 관련 라이브러리는 AzureML 및 RODBC입니다.
  * **Java**: VM의 /dsvm/sdk/AzureSDKJava 디렉터리에서 Azure Java 라이브러리 목록을 찾을 수 있습니다. 핵심 라이브러리는 Azure Storage 및 관리 API, Azure Cosmos DB, SQL Server용 JDBC 드라이버입니다.  

## <a name="azure-machine-learning"></a>Azure Machine Learning

Azure Machine Learning은 예측 분석 솔루션을 빌드, 배포 및 공유할 수 있는 완전 관리형 클라우드 서비스입니다. Azure Machine Learning 스튜디오(미리 보기)에서 실험 및 모델을 빌드할 수 있습니다. [Microsoft Azure Machine Learning](https://ml.azure.com)을 방문하여 Data Science Virtual Machine의 웹 브라우저에서 액세스할 수도 있습니다.

Azure Machine Learning 스튜디오에 로그인하면 실험 캔버스를 사용하여 기계 학습 알고리즘에 대한 논리적 흐름을 작성할 수 있습니다. Azure Machine Learning에서 호스팅되고 Azure Machine Learning 스튜디오의 실험에서 원활하게 작동할 수 있는 Jupyter Notebook에도 액세스할 수 있습니다. 

빌드한 기계 학습 모델은 웹 서비스 인터페이스에 래핑하여 운용 가능하도록 설정합니다. 기계 학습 모델을 운용하면 모든 언어로 작성된 클라이언트가 해당 모델에서 예측을 호출할 수 있습니다. 자세한 내용은 [Machine Learning 설명서](https://azure.microsoft.com/documentation/services/machine-learning/)를 참조하세요.

VM에서 모델을 R 또는 Python으로 빌드한 다음, Azure Machine Learning에서 프로덕션 환경에 배포할 수도 있습니다. R 및 Python에는 이 기능을 사용하는 데 필요한 라이브러리(각각 **AzureML** 과 **azureml**)가 설치되어 있습니다.

> [!NOTE]
> 이러한 지침은 Windows 버전의 Data Science Virtual Machine용으로 작성되었습니다. 하지만 Azure Machine Learning에 모델을 배포하는 방법에 대해 제공되는 정보는 Linux VM에도 적용할 수 있습니다.

## <a name="machine-learning-tools"></a>기계 학습 도구

VM에는 로컬로 미리 컴파일되고 미리 설치된 기계 학습 도구 및 알고리즘이 제공됩니다. 이러한 위협은 다음과 같습니다.

* **Vowpal Wabbit**: 속성 온라인 학습 알고리즘입니다.
* **xgboost**: 최적화되고 향상된 트리 알고리즘을 제공하는 도구입니다.
* **Rattle**: 쉬운 데이터 탐색 및 모델링을 위한 R 기반 그래픽 도구입니다.
* **Python**: Anaconda Python에서는 Scikit-learn 등의 라이브러리가 포함된 기계 학습 알고리즘이 번들로 제공됩니다. `pip install` 하여 다른 라이브러리를 설치할 수 있습니다.
* **LightGBM**: 의사 결정 트리 알고리즘을 기준으로 하는 빠른 분산형 고성능 그라데이션 향상 프레임워크입니다.
* **R**: R에서는 다양한 기계 학습 기능 라이브러리를 사용할 수 있습니다. 미리 설치된 라이브러리에는 lm, glm, randomForest 및 rpart가 포함됩니다. 다른 라이브러리는 다음 명령을 실행하여 설치할 수 있습니다.

    ```r
    install.packages(<lib name>)
    ```

아래에서는 목록의 처음 3개 기계 학습 도구에 대해 추가로 설명합니다.

### <a name="vowpal-wabbit"></a>Vowpal Wabbit

Vowpal Wabbit은 온라인, 해시, allreduce, 축소, learning2search, 활성 및 대화형 학습 등의 기술을 사용하는 기계 학습 시스템입니다.

기본 예제에서 도구를 실행하려면 다음 명령을 사용합니다.

```bash
cp -r /dsvm/tools/VowpalWabbit/demo vwdemo
cd vwdemo
vw house_dataset
```

해당 디렉터리에는 더 큰 다른 데모도 있습니다. Vowpal Wabbit에 대한 자세한 내용은 [이 GitHub 섹션](https://github.com/JohnLangford/vowpal_wabbit) 및 [Vowpal Wabbit wiki](https://github.com/JohnLangford/vowpal_wabbit/wiki)를 참조하세요.

### <a name="xgboost"></a>xgboost

xgboost 라이브러리는 향상된(트리) 알고리즘에 맞게 설계 및 최적화되었습니다. 이 라이브러리는 확장, 이동성 및 정확도가 높은 대규모 트리 향상 기능을 제공하는 데 필요한 최대 한계까지 기계의 계산 제한을 높이는 데 사용되며

명령줄 및 R 라이브러리로 제공됩니다. R에서 이 라이브러리를 사용하려면 대화형 R 세션을 시작하고(셸에서 **R** 입력) 라이브러리를 로드할 수 있습니다.

R 프롬프트에서 실행할 수 있는 간단한 예제는 다음과 같습니다.

```R
library(xgboost)

data(agaricus.train, package='xgboost')
data(agaricus.test, package='xgboost')
train <- agaricus.train
test <- agaricus.test
bst <- xgboost(data = train$data, label = train$label, max.depth = 2,
                eta = 1, nthread = 2, nround = 2, objective = "binary:logistic")
pred <- predict(bst, test$data)
```

xgboost 명령줄을 실행하려면 셸에서 다음 명령을 실행합니다.

```bash
cp -r /dsvm/tools/xgboost/demo/binary_classification/ xgboostdemo
cd xgboostdemo
xgboost mushroom.conf
```

.model 파일이 지정된 디렉터리에 기록됩니다. 이 데모 예제에 대한 정보는 [GitHub](https://github.com/dmlc/xgboost/tree/master/demo/CLI/binary_classification)에서 찾을 수 있습니다.

xgboost에 대한 자세한 내용은 [xgboost 설명서 페이지](https://xgboost.readthedocs.org/en/latest/) 및 해당 [GitHub 리포지토리](https://github.com/dmlc/xgboost)를 참조하세요.

### <a name="rattle"></a>Rattle

Rattle(**R** **A** nalytical **T** ool **T** o **L** earn **E** asily)은 GUI 기반 데이터 탐색 및 모델링 기능을 사용합니다. Rattle은 통계 및 시각적 데이터 요약을 표시하고, 즉시 모델링할 수 있도록 데이터를 변환하고, 해당 데이터에서 감독되거나 감독되지 않는 모델을 빌드하고, 모델의 성능을 그래픽으로 표시하고, 새 데이터 집합의 점수를 매깁니다. 또한 UI에서 작업을 복제하여 R에서 직접 실행하거나 추가 분석을 위한 시작점으로 사용할 수 있는 R 코드를 생성할 수도 있습니다.

Rattle을 실행하려면 그래픽 데스크톱 로그인 세션을 사용 중이어야 합니다. 터미널에서 **R** 을 입력하여 R 환경을 엽니다. R 프롬프트에서 다음 명령을 입력합니다.

```R
library(rattle)
rattle()
```

이제 탭 세트가 있는 그래픽 인터페이스가 열립니다. Rattle에서 다음 빠른 시작 단계를 사용하여 날씨 데이터 세트 샘플을 사용하고 모델을 빌드합니다. 일부 단계에서는 아직 시스템에 없는 몇 가지 필수 R 패키지를 자동으로 설치하고 로드하라는 메시지가 표시됩니다.

> [!NOTE]
> 패키지를 시스템 디렉터리(기본값)에 설치할 수 있는 액세스 권한이 없는 경우 R 콘솔 창에 패키지를 개인 라이브러리에 설치하라는 메시지가 표시될 수 있습니다. 이러한 메시지가 표시되면 **y** 로 응답합니다.

1. **실행** 을 선택합니다.
1. 날씨 데이터 세트 예제를 사용할지 여부를 묻는 대화 상자가 표시됩니다. **예** 를 선택하여 예제를 로드합니다.
1. **모델** 탭을 선택합니다.
1. **실행** 을 선택하여 의사 결정 트리를 작성합니다.
1. **그리기** 를 선택하여 의사 결정 트리를 표시합니다.
1. **포리스트** 옵션을 선택하고, **실행** 을 선택하여 임의 포리스트를 작성합니다.
1. **평가** 탭을 선택합니다.
1. **위험** 옵션을 선택하고, **실행** 을 선택하여 두 개의 **위험(누적)** 성능 도표를 표시합니다.
1. **로그** 탭을 선택하여 이전 작업에서 생성된 R 코드를 표시합니다.
   (현재 Rattle 릴리스의 버그로 인해 로그 텍스트의 **Export this log**(이 로그 내보내기) 앞에 **#** 문자를 삽입해야 합니다.)
1. **내보내기** 단추를 선택하여 *weather_script.R* 이라는 R 스크립트 파일을 홈 폴더에 저장합니다.

Rattle 및 R을 종료할 수 있습니다. 이제 생성된 R 스크립트를 수정할 수 있습니다. 또는 스크립트를 있는 그대로 사용하고, 언제든지 이를 실행하여 Rattle UI 내에서 수행된 모든 작업을 반복합니다. 특히 이는 R 초보자를 위해 간단한 그래픽 인터페이스에서 분석 및 기계 학습을 빠르게 수행하는 동시에 R에서 코드를 자동으로 생성하여 수정하거나 학습할 수 있는 방법입니다.

## <a name="next-steps"></a>다음 단계

추가 질문이 있나요? [지원 티켓](https://azure.microsoft.com/support/create-ticket/) 생성을 고려하세요.