---
title: Windows에서 데이터 및 모델 살펴보기
titleSuffix: Azure Data Science Virtual Machine
description: Windows 데이터 과학 가상 컴퓨터에서 데이터 탐색 및 모델링 작업을 수행합니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: vijetajo
ms.author: vijetaj
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 147f43148c0f804adf70f1a792ba1b8a772fdae4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80294485"
---
# <a name="ten-things-you-can-do-on-the-windows-data-science-virtual-machine"></a>Windows 데이터 과학 Virtual Machine으로 할 수 있는 10가지 작업

Windows 데이터 과학 가상 머신(DSVM)은 데이터 탐색 및 모델링 작업을 수행할 수 있는 강력한 데이터 과학 개발 환경입니다. 온-프레미스, 클라우드 또는 하이브리드 배포에 대한 분석을 쉽게 시작할 수 있도록 몇 가지 인기 있는 데이터 분석 도구가 이미 구축되고 번들로 제공됩니다. 

DSVM은 Azure 서비스와 긴밀하게 작동합니다. Azure, Azure SQL 데이터 웨어하우스, Azure 데이터 레이크, Azure 저장소 또는 Azure Cosmos DB에서 이미 저장된 데이터를 읽고 처리할 수 있습니다. 또한 Azure 기계 학습 및 Azure 데이터 팩터리와 같은 다른 분석 도구를 활용할 수도 있습니다.

이 문서에서는 DSVM을 사용하여 데이터 과학 작업을 수행하고 다른 Azure 서비스와 상호 작용하는 방법을 알아봅니다. 다음은 DSVM에서 수행할 수 있는 작업 중 일부입니다.

- Microsoft 기계 학습 서버 및 파이썬을 사용하여 DSVM에서 데이터를 탐색하고 모델을 로컬로 개발합니다.
- Jupyter 노트북을 사용하여 파이썬 2, 파이썬 3 및 Microsoft R.(Microsoft R은 성능을 위해 설계된 엔터프라이즈급 버전의 R 버전입니다)을 사용하여 브라우저에서 데이터를 실험합니다.
- 클라이언트 응용 프로그램이 간단한 웹 서비스 인터페이스를 사용하여 모델에 액세스할 수 있도록 Azure Machine Learning에서 R 및 Python을 통해 빌드된 모델을 배포합니다.
- Azure 포털 또는 PowerShell을 사용하여 Azure 리소스를 관리합니다.
- DSVM에서 마운트 가능한 드라이브로 Azure Files 공유를 만들어 스토리지 공간을 확장하고 전체 팀 전체에서 대규모 데이터 집합/코드를 공유합니다.
- GitHub를 사용하여 팀과 코드를 공유합니다. 미리 설치된 Git 클라이언트인 Git Bash 및 Git GUI를 사용하여 리포지토리에 액세스할 수 있습니다.
- Azure Blob 저장소, Azure 데이터 레이크, Azure HDInsight(하도시프), Azure 코스모스 DB, Azure SQL 데이터 웨어하우스 및 Azure SQL 데이터베이스와 같은 Azure 데이터 및 분석 서비스에 액세스합니다.
- DSVM에 사전 설치된 Power BI Desktop 인스턴스를 사용하여 보고서 및 대시보드를 빌드하고 클라우드에 배포합니다.
- 프로젝트의 요구에 맞게 DSVM을 동적으로 확장합니다.
- 가상 컴퓨터에 추가 도구를 설치합니다.   

> [!NOTE]
> 이 문서에 나열된 많은 데이터 저장소 및 분석 서비스에 는 추가 사용 요금이 적용됩니다. 자세한 내용은 [Azure 가격 페이지를](https://azure.microsoft.com/pricing/) 참조하세요.
> 
> 

## <a name="prerequisites"></a>사전 요구 사항

* Azure 구독이 필요합니다. 무료 [평가판에 등록할](https://azure.microsoft.com/free/)수 있습니다.
* Azure 포털에서 데이터 과학 가상 컴퓨터를 프로비전하기 위한 지침은 [가상 컴퓨터 만들기에서](https://portal.azure.com/#create/microsoft-dsvm.dsvm-windowsserver-2016)사용할 수 있습니다.


[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="explore-data-and-develop-models-with-microsoft-machine-learning-server"></a>Microsoft 기계 학습 서버를 사용하여 데이터 탐색 및 모델 개발
R 및 Python 같은 언어를 사용하여 DSVM에서 바로 데이터를 분석할 수 있습니다.

R의 경우 시작 메뉴 또는 데스크톱에서 찾을 수 있는 RStudio와 같은 IDE를 사용할 수 있습니다. 또는 비주얼 스튜디오용 R 도구를 사용할 수 있습니다. Microsoft는 확장 가능한 분석과 병렬 청크 분석에서 허용되는 메모리 크기보다 큰 데이터를 분석할 수 있는 기능을 사용할 수 있도록 오픈 소스 CRAN R 위에 추가 라이브러리를 제공했습니다. 

Python의 경우 PTVS(Python Tools for Visual Studio) 확장 기능이 사전 설치된 Visual Studio Community Edition 같은 IDE를 사용할 수 있습니다. 기본적으로 루트 콘다 환경인 파이썬 3.6만 PTVS에서 구성됩니다. 아나콘다 파이썬 2.7을 사용하려면 다음 단계를 수행하십시오.

1. **도구** > **파이썬 도구** > **파이썬 환경으로**이동하여 각 버전에 대한 사용자 정의 환경을 만든 다음 Visual Studio 커뮤니티 에디션에서 **+ 사용자 지정을** 선택합니다.
1. 아나콘다 파이썬 2.7에 대해 설명을 제공하고 환경 접두사 경로를 **c:\anaconda\envs\python2로** 설정합니다.
1. 환경 저장을 위해 **자동 감지** > **적용을** 선택합니다.

파이썬 환경을 만드는 방법에 대한 자세한 내용은 [PTVS 설명서를](https://aka.ms/ptvsdocs) 참조하십시오.

이제 새 파이썬 프로젝트를 만들도록 설정되었습니다.  > **새****Project**프로젝트 > **파이썬** **파일로** > 이동하여 빌드하는 파이썬 응용 프로그램의 유형을 선택합니다. 파이썬 환경을 마우스 오른쪽 단추로 클릭한 다음 **파이썬 환경 추가/제거를**선택하여 현재 프로젝트의 파이썬 **환경을** 원하는 버전(파이썬 2.7 또는 3.6)으로 설정할 수 있습니다. PTVS 작업에 대한 자세한 내용은 [제품 설명서에서](https://aka.ms/ptvsdocs)확인할 수 있습니다.

## <a name="use-jupyter-notebooks"></a>Jupyter Notebook 사용
Jupyter 노트북은 데이터 탐색 및 모델링을 위한 브라우저 기반 IDE를 제공합니다. Jupyter 노트북에서 파이썬 2, 파이썬 3 또는 R(오픈 소스와 Microsoft R 서버 모두)을 사용할 수 있습니다.

Jupyter 노트북을 시작하려면 **시작** 메뉴 또는 데스크톱에서 **Jupyter 노트북** 아이콘을 선택합니다. DSVM 명령 프롬프트에서 기존 전자 ```jupyter notebook``` 필기장이 있거나 새 전자 필기를 만들려는 디렉터리에서 명령을 실행할 수도 있습니다.  

Jupyter를 시작한 후에는 DSVM에 미리 패키지된 몇 가지 예제 노트북이 포함된 디렉터리가 표시됩니다. 이제 다음을 수행할 수 있습니다.

* 코드를 보려면 노트북을 선택합니다.
* 시프트 +Enter를 선택하여 각 셀을 실행합니다.
* **셀** > **실행을**선택하여 전체 전자 필기장을 실행합니다.
* Jupyter 아이콘(왼쪽 위 모서리)을 선택하고 오른쪽의 **새** 단추를 선택한 다음 전자 필기장 언어(커널이라고도 함)를 선택하여 새 전자 필기장을 만듭니다.   

> [!NOTE]
> 현재 주피터의 파이썬 2.7, 파이썬 3.6, R, 줄리아 및 파이스파크 커널이 지원됩니다. R 커널은 오픈 소스 R과 Microsoft R 모두에서 프로그래밍을 지원합니다.   
> 
> 

노트북에 있을 때 선택한 라이브러리를 사용하여 데이터를 탐색하고 모델을 빌드하고 모델을 테스트할 수 있습니다.

## <a name="train-and-deploy-models-by-using-azure-machine-learning"></a>Azure 기계 학습을 사용하여 모델 학습 및 배포
모델을 빌드하고 유효성을 검사한 후 다음 단계는 일반적으로 모델을 프로덕션에 배포하는 것입니다. 이 단계를 통해 클라이언트 응용 프로그램은 모델 예측을 실시간 또는 일괄 처리 모드 단위로 호출할 수 있습니다. Azure Machine Learning은 R 또는 Python에서 구축된 모델을 운영하는 메커니즘을 제공합니다.

Azure 기계 학습에서 모델을 작동하면 웹 서비스가 노출됩니다. 이를 통해 클라이언트는 입력 매개 변수를 전달하는 REST 호출을 수행하고 모델에서 예측을 출력으로 받을 수 있습니다.

### <a name="build-and-operationalize-python-models"></a>파이썬 모델 빌드 및 운영
다음은 Scikit 학습 라이브러리를 사용하여 간단한 모델을 빌드하는 Python Jupyter 노트북에서 개발된 코드 조각입니다.

```python
# IRIS classification
from sklearn import datasets
from sklearn import svm
clf = svm.SVC()
iris = datasets.load_iris()
X, y = iris.data, iris.target
clf.fit(X, y)
```

Azure Machine Learning에 파이썬 모델을 배포하는 데 사용되는 메서드는 모델의 예측을 함수로 래핑하고 사전 설치된 Azure Machine Learning Python 라이브러리에서 제공하는 특성으로 장식합니다. 속성은 Azure 기계 학습 작업 영역 ID, API 키 및 입력 및 반환 매개 변수를 나타냅니다.  

```python
from azureml import services
@services.publish(workspaceid, auth_token)
@services.types(sep_l=float, sep_w=float, pet_l=float, pet_w=float)
@services.returns(int)  # 0, or 1, or 2
def predictIris(sep_l, sep_w, pet_l, pet_w):
    inputArray = [sep_l, sep_w, pet_l, pet_w]


return clf.predict(inputArray)
```

이제 클라이언트에서 웹 서비스에 대한 호출을 만들 수 있습니다. 편의 래퍼는 REST API 요청을 생성합니다. 다음은 웹 서비스를 사용하는 샘플 코드입니다.

```python
# Consume through web service URL and keys
from azureml import services
@services.service(url, api_key)
@services.types(sep_l = float, sep_w = float, pet_l=float, pet_w=float)
@services.returns(float)
def IrisPredictor(sep_l, sep_w, pet_l, pet_w):
pass

IrisPredictor(3,2,3,4)
```

> [!NOTE]
> 현재 Azure 기계 학습 라이브러리는 Python 2.7에서만 지원됩니다.   
> 
> 

### <a name="build-and-operationalize-r-models"></a>R 모델 빌드 및 운영
Python에 대해 수행하는 방법과 유사한 방식으로 데이터 과학 가상 머신 또는 다른 곳에서 빌드된 R 모델을 Azure 기계 학습에 배포할 수 있습니다. 다음 단계를 참조하십시오.

1. 작업 영역 ID 및 인증 토큰을 제공하기 위해 settings.json 파일을 만듭니다. 
2. 모델의 예측 함수에 대한 래퍼를 작성합니다.
3. Azure ```publishWebService``` 기계 학습 라이브러리에서 호출하여 함수 래퍼를 전달합니다.  

다음 절차 및 코드 조각을 사용하여 Azure Machine Learning에서 모델을 웹 서비스로 설정, 빌드, 게시 및 사용합니다.

#### <a name="set-up"></a>설정

홈 디렉터리 아래에 호출된 ```.azureml``` 디렉터리 아래에 settings.json 파일을 만듭니다. Azure 기계 학습 작업 영역에서 매개 변수를 입력합니다.

다음은 settings.json 파일 구조입니다.

```json
{"workspace":{
"id"                  : "ENTER YOUR AZUREML WORKSPACE ID",
"authorization_token" : "ENTER YOUR AZUREML AUTH TOKEN"
}}
```

#### <a name="build-a-model-in-r-and-publish-it-in-azure-machine-learning"></a>R에서 모델을 구축하고 Azure Machine Learning에 게시

```r
library(AzureML)
ws <- workspace(config="~/.azureml/settings.json")

if(!require("lme4")) install.packages("lme4")
library(lme4)
set.seed(1)
train <- sleepstudy[sample(nrow(sleepstudy), 120),]
m <- lm(Reaction ~ Days + Subject, data = train)

# Define a prediction function to publish based on the model:
sleepyPredict <- function(newdata){
        predict(m, newdata=newdata)
}

ep <- publishWebService(ws, fun = sleepyPredict, name="sleepy lm", inputSchema = sleepstudy, data.frame=TRUE)
```

#### <a name="consume-the-model-deployed-in-azure-machine-learning"></a>Azure Machine Learning에 배포된 모델 사용
클라이언트 응용 프로그램에서 모델을 사용 하려면 Azure 기계 학습 라이브러리를 사용 하 여 게시 된 웹 서비스를 이름으로 조회 합니다. API `services` 호출을 사용하여 끝점을 확인합니다. 그런 다음 `consume` 함수를 호출하여 예측할 데이터 프레임을 전달합니다.

다음 코드를 사용하여 Azure 기계 학습 웹 서비스로 게시된 모델을 사용합니다.

```r
library(AzureML)
library(lme4)
ws <- workspace(config="~/.azureml/settings.json")

s <-  services(ws, name = "sleepy lm")
s <- tail(s, 1) # use the last published function, in case of duplicate function names

ep <- endpoints(ws, s)

# Try this out, and compare with raw data
ans = consume(ep, sleepstudy)$ans
```

기계 학습 [스튜디오에서 R 패키지에](/azure/machine-learning/studio-module-reference/r-packages-supported-by-azure-machine-learning)대한 자세한 정보를 참조하십시오.

## <a name="manage-azure-resources"></a>Azure 리소스 관리
DSVM은 가상 컴퓨터에서 로컬로 분석 솔루션을 빌드할 수 없습니다. 또한 Azure 클라우드 플랫폼에서 서비스에 액세스할 수 있습니다. Azure는 DSVM에서 관리하고 액세스할 수 있는 여러 계산, 저장소, 데이터 분석 및 기타 서비스를 제공합니다.

Azure 구독 및 클라우드 리소스를 관리하려면 다음 두 가지 옵션이 있습니다.
+ 브라우저를 사용하고 Azure [포털로](https://portal.azure.com)이동합니다.

+ PowerShell 스크립트를 사용합니다. 바탕 화면또는 **시작** 메뉴의 바로 가기에서 Azure PowerShell을 실행합니다. 자세한 내용은 [Microsoft Azure PowerShell 설명서를](../../powershell-azure-resource-manager.md) 참조하십시오. 

## <a name="extend-storage-by-using-shared-file-systems"></a>공유 파일 시스템을 사용하여 스토리지 확장
데이터 과학자는 팀 내에서 대용량 데이터 세트, 코드 또는 기타 리소스를 공유할 수 있습니다. DSVM에는 약 45GB의 사용 가능한 공간이 있습니다. 저장소를 확장하려면 Azure Files를 사용하여 하나 이상의 DSVM 인스턴스에 탑재하거나 REST API를 통해 액세스할 수 있습니다. [Azure 포털을](../../virtual-machines/windows/attach-managed-disk-portal.md) 사용하거나 [Azure PowerShell을](../../virtual-machines/windows/attach-disk-ps.md) 사용하여 전용 데이터 디스크를 추가할 수도 있습니다. 

> [!NOTE]
> Azure 파일 공유의 최대 공간은 5TB입니다. 각 파일의 크기 제한은 1TB입니다. 

Azure PowerShell에서 이 스크립트를 사용하여 Azure 파일 공유를 만들 수 있습니다.

```powershell
# Authenticate to Azure.
Connect-AzAccount
# Select your subscription
Get-AzSubscription –SubscriptionName "<your subscription name>" | Select-AzSubscription
# Create a new resource group.
New-AzResourceGroup -Name <dsvmdatarg>
# Create a new storage account. You can reuse existing storage account if you want.
New-AzStorageAccount -Name <mydatadisk> -ResourceGroupName <dsvmdatarg> -Location "<Azure Data Center Name For eg. South Central US>" -Type "Standard_LRS"
# Set your current working storage account
Set-AzCurrentStorageAccount –ResourceGroupName "<dsvmdatarg>" –StorageAccountName <mydatadisk>

# Create an Azure Files share
$s = New-AzStorageShare <<teamsharename>>
# Create a directory under the file share. You can give it any name
New-AzStorageDirectory -Share $s -Path <directory name>
# List the share to confirm that everything worked
Get-AzStorageFile -Share $s
```

Azure Files 공유를 만들었으니 Azure의 모든 가상 컴퓨터에 탑재할 수 있습니다. 대기 시간 및 데이터 전송 요금을 방지하려면 VM을 저장소 계정과 동일한 Azure 데이터 센터에 두는 것이 좋습니다. 다음은 DSVM에 드라이브를 탑재하는 Azure PowerShell 명령입니다.

```powershell
# Get the storage key of the storage account that has the Azure Files share from the Azure portal. Store it securely on the VM to avoid being prompted in the next command.
cmdkey /add:<<mydatadisk>>.file.core.windows.net /user:<<mydatadisk>> /pass:<storage key>

# Mount the Azure Files share as drive Z on the VM. You can choose another drive letter if you want.
net use z:  \\<mydatadisk>.file.core.windows.net\<<teamsharename>>
```

이제 VM의 일반적인 드라이브처럼 이 드라이브에 액세스할 수 있습니다.

## <a name="share-code-in-github"></a>GitHub에서 코드 공유
GitHub는 개발자 커뮤니티에서 공유하는 기술을 사용하여 다양한 도구에 대한 코드 샘플 및 소스를 찾을 수 있는 코드 리포지토리입니다. Github는 코드 파일 버전을 추적하고 저장하는 기술로 Git를 사용합니다. GitHub는 팀의 공유 코드 및 설명서를 저장하고, 버전 제어를 구현하고, 코드를 보고 기여할 수 있는 사람을 제어하기 위해 자체 리포지토리를 만들 수 있는 플랫폼이기도 합니다. 

Git 사용에 대한 자세한 내용을 보려면 [GitHub 도움말 페이지](https://help.github.com/)를 방문하세요. GitHub를 팀과 공동 작업하고, 커뮤니티에서 개발한 코드를 사용하고, 커뮤니티에 코드를 다시 기여하는 방법 중 하나로 사용할 수 있습니다.

DSVM에는 GitHub 리포지토리에 액세스하기 위해 명령줄과 GUI에 클라이언트 도구가 로드되어 있습니다. Git 및 GitHub와 함께 작동하는 명령줄 도구를 Git Bash라고 합니다. 비주얼 스튜디오는 DSVM에 설치되며 Git 확장이 있습니다. **시작** 메뉴와 바탕 화면에서 이러한 도구에 대한 아이콘을 찾을 수 있습니다.

GitHub 리포지토리에서 코드를 다운로드하려면 ```git clone``` 명령을 사용합니다. 예를 들어 Microsoft에서 게시한 데이터 과학 리포지토리를 현재 디렉터리로 다운로드하려면 Git Bash에서 다음 명령을 실행할 수 있습니다.

    git clone https://github.com/Azure/DataScienceVM.git

Visual Studio에서 동일한 복제 작업을 수행할 수 있습니다. 다음 스크린샷은 Visual Studio에서 Git 및 GitHub 도구에 액세스하는 방법을 보여 주며 다음과 같은 방법을 보여 주며 다음과 같은 방법을 보여 주며 다음과 같은 방법을 보여 주며 있습니다.

![GitHub 연결이 표시된 Visual Studio 스크린샷](./media/vm-do-ten-things/VSGit.PNG)

git을 사용하여 github.com 사용 가능한 리소스에서 GitHub 리포지토리로 작업하는 방법에 대한 자세한 정보를 찾을 수 있습니다. [참고 자료](https://services.github.com/on-demand/downloads/github-git-cheat-sheet.pdf) 를 보시면 많은 도움이 될 것입니다.

## <a name="access-azure-data-and-analytics-services"></a>Azure 데이터 및 분석 서비스에 액세스
### <a name="azure-blob-storage"></a>Azure Blob 스토리지
Azure Blob 저장소는 크고 작은 데이터를 위한 안정적이고 경제적인 클라우드 스토리지 서비스입니다. 이 섹션에서는 Azure Blob에 저장된 데이터를 Blob 저장소로 이동하고 데이터에 액세스하는 방법에 대해 설명합니다.

#### <a name="prerequisites"></a>사전 요구 사항

* [Azure 포털에서](https://portal.azure.com)Azure Blob 저장소 계정을 만듭니다.

   ![Azure 포털의 저장소 계정 생성 프로세스 의 스크린샷](./media/vm-do-ten-things/Create_Azure_Blob.PNG)

* 명령줄 AzCopy 도구가 미리 설치되어 있는지 ```C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy.exe```확인합니다. azcopy.exe가 포함된 디렉터리가 PATH 환경 변수에 이미 있으므로 이 도구를 실행할 때 전체 명령 경로를 입력하지 않도록 할 수 있습니다. AzCopy 도구에 대한 자세한 내용은 [AzCopy 문서를](../../storage/common/storage-use-azcopy.md)참조하십시오.
* Azure Storage Explorer 도구를 시작합니다. [저장소 탐색기 웹 페이지에서](https://storageexplorer.com/)다운로드할 수 있습니다. 

   ![저장소 계정에 액세스하는 Azure 저장소 탐색기의 스크린샷](./media/vm-do-ten-things/AzureStorageExplorer_v4.png)

#### <a name="move-data-from-a-vm-to-an-azure-blob-azcopy"></a>VM에서 Azure Blob로 데이터 이동: AzCopy

로컬 파일과 Blob 저장소 간에 데이터를 이동하려면 명령줄또는 PowerShell에서 AzCopy를 사용할 수 있습니다.

    AzCopy /Source:C:\myfolder /Dest:https://<mystorageaccount>.blob.core.windows.net/<mycontainer> /DestKey:<storage account key> /Pattern:abc.txt

**C:\myfolder를** 파일이 저장된 경로로 바꾸고, **Mystorageaccount에** Blob 저장소 계정 이름, 컨테이너 이름이 있는 **mycontainer** 및 Blob 저장소 액세스 키가 있는 **저장소 계정 키를** 바꿉니다. [Azure 포털](https://portal.azure.com)에서 저장소 계정 자격 증명을 찾을 수 있습니다.

PowerShell 또는 명령 프롬프트에서 AzCopy 명령을 실행합니다. 다음은 AzCopy 명령의 몇 가지 예입니다.

```powershell
# Copy *.sql from a local machine to an Azure blob
"C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Source:"c:\Aaqs\Data Science Scripts" /Dest:https://[ENTER STORAGE ACCOUNT].blob.core.windows.net/[ENTER CONTAINER] /DestKey:[ENTER STORAGE KEY] /S /Pattern:*.sql

# Copy back all files from an Azure blob container to a local machine

"C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Dest:"c:\Aaqs\Data Science Scripts\temp" /Source:https://[ENTER STORAGE ACCOUNT].blob.core.windows.net/[ENTER CONTAINER] /SourceKey:[ENTER STORAGE KEY] /S
```

Azure Blob에 복사하는 AzCopy 명령을 실행하면 파일이 Azure 저장소 탐색기에서 나타납니다.

![업로드된 CSV 파일을 표시하는 저장소 계정의 스크린샷](./media/vm-do-ten-things/AzCopy_run_finshed_Storage_Explorer_v3.png)

#### <a name="move-data-from-a-vm-to-an-azure-blob-azure-storage-explorer"></a>데이터를 VM에서 Azure Blob로 이동: Azure 저장소 탐색기

Azure 저장소 탐색기를 사용하여 VM의 로컬 파일에서 데이터를 업로드할 수도 있습니다.

* 컨테이너에 데이터를 업로드하려면 대상 컨테이너를 선택하고 **업로드** 단추를 선택합니다. ![Azure 저장소 탐색기에서 업로드 단추의 스크린샷](./media/vm-do-ten-things/storage-accounts.png)
* **파일** 상자의 오른쪽에 있는 타원(... ) 을 선택하고 파일 시스템에서 업로드할 하나 또는 여러 파일을 선택하고 **업로드를** 선택하여 파일 업로드를 시작합니다.**...** ![파일 업로드 대화 상자의 스크린샷](./media/vm-do-ten-things/upload-files-to-blob.png)

#### <a name="read-data-from-an-azure-blob-machine-learning-reader-module"></a>Azure Blob에서 데이터 읽기: 기계 학습 리더 모듈

Azure 기계 학습 스튜디오에서 데이터 가져오기 모듈을 사용하여 Blob에서 데이터를 읽을 수 있습니다.

![Machine Learning Studio에서 데이터 가져오기 모듈의 스크린샷](./media/vm-do-ten-things/AML_ReaderBlob_Module_v3.png)

#### <a name="read-data-from-an-azure-blob-python-odbc"></a>Azure Blob에서 데이터 읽기: 파이썬 ODBC

BlobService 라이브러리를 사용하여 Jupyter 노트북이나 파이썬 프로그램의 Blob에서 직접 데이터를 읽을 수 있습니다.

먼저 필요한 패키지를 가져옵니다.

```python
import pandas as pd
from pandas import Series, DataFrame
import numpy as np
import matplotlib.pyplot as plt
from time import time
import pyodbc
import os
from azure.storage.blob import BlobService
import tables
import time
import zipfile
import random
```

그런 다음 Blob 저장소 계정 자격 증명을 연결하고 Blob에서 데이터를 읽습니다.

```python
CONTAINERNAME = 'xxx'
STORAGEACCOUNTNAME = 'xxxx'
STORAGEACCOUNTKEY = 'xxxxxxxxxxxxxxxx'
BLOBNAME = 'nyctaxidataset/nyctaxitrip/trip_data_1.csv'
localfilename = 'trip_data_1.csv'
LOCALDIRECTORY = os.getcwd()
LOCALFILE =  os.path.join(LOCALDIRECTORY, localfilename)

#download from blob
t1 = time.time()
blob_service = BlobService(account_name=STORAGEACCOUNTNAME,account_key=STORAGEACCOUNTKEY)
blob_service.get_blob_to_path(CONTAINERNAME,BLOBNAME,LOCALFILE)
t2 = time.time()
print(("It takes %s seconds to download "+BLOBNAME) % (t2 - t1))

#unzip downloaded files if needed
#with zipfile.ZipFile(ZIPPEDLOCALFILE, "r") as z:
#    z.extractall(LOCALDIRECTORY)

df1 = pd.read_csv(LOCALFILE, header=0)
df1.columns = ['medallion','hack_license','vendor_id','rate_code','store_and_fwd_flag','pickup_datetime','dropoff_datetime','passenger_count','trip_time_in_secs','trip_distance','pickup_longitude','pickup_latitude','dropoff_longitude','dropoff_latitude']
print 'the size of the data is: %d rows and  %d columns' % df1.shape
```

데이터는 데이터 프레임으로 읽습니다.

![데이터의 처음 10개 행의 스크린샷](./media/vm-do-ten-things/IPNB_data_readin.PNG)

### <a name="azure-data-lake"></a>Azure 데이터 레이크
Azure Data Lake Storage는 빅 데이터 분석 워크로드를 위한 하이퍼스케일 리포지토리이며 HADoop 분산 파일 시스템(HDFS)과 호환됩니다. Hadoop, Spark 및 Azure Data Lake Analytics와 작동합니다. 이 섹션에서는 Azure Data Lake Analytics를 사용하여 데이터를 Azure Data Lake 저장소로 이동하고 분석을 실행하는 방법을 알아봅니다.

#### <a name="prerequisites"></a>사전 요구 사항

* Azure [포털에서](https://portal.azure.com)Azure 데이터 호수 분석 인스턴스를 만듭니다.

   ![Azure 포털에서 데이터 레이크 분석 인스턴스를 만드는 스크린샷](./media/vm-do-ten-things/Azure_Data_Lake_Create_v2.png)

* [Visual Studio 플러그인용 Azure 데이터 레이크 및 스트림 분석 도구는](https://www.microsoft.com/download/details.aspx?id=49504) 가상 컴퓨터의 Visual Studio 커뮤니티 에디션에 이미 설치되어 있습니다. Visual Studio를 시작하고 Azure 구독에 로그인하면 Visual Studio의 왼쪽 패널에 Azure 데이터 분석 계정 및 저장소가 표시됩니다.

   ![비주얼 스튜디오에서 데이터 레이크 도구에 대한 플러그인의 스크린 샷](./media/vm-do-ten-things/Azure_Data_Lake_PlugIn_v2.PNG)

#### <a name="move-data-from-a-vm-to-data-lake-azure-data-lake-explorer"></a>VM에서 데이터 레이크로 데이터 이동: Azure 데이터 레이크 탐색기

Azure Data Lake 탐색기를 사용하여 [가상 컴퓨터의 로컬 파일에서 데이터 레이크 저장소에 데이터를 업로드할](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-get-started-portal)수 있습니다.

[Azure Data Factory를](https://azure.microsoft.com/services/data-factory/)사용하여 Azure Data Lake로 의 데이터 이동을 운영하기 위해 데이터 파이프라인을 빌드할 수도 있습니다. [이 문서에서는](https://azure.microsoft.com/blog/creating-big-data-pipelines-using-azure-data-lake-and-azure-data-factory/) 데이터 파이프라인을 빌드하는 단계를 안내합니다.

#### <a name="read-data-from-an-azure-blob-to-data-lake-u-sql"></a>Azure Blob에서 데이터 레이크로 데이터 읽기: U-SQL

데이터가 Azure Blob 저장소에 있는 경우 U-SQL 쿼리에서 Azure Blob에서 데이터를 직접 읽을 수 있습니다. U-SQL 쿼리를 작성하기 전에 Blob 저장소 계정이 Azure Data Lake 인스턴스에 연결되어 있는지 확인합니다. Azure 포털로 이동하여 Azure Data Lake Analytics 대시보드를 찾고, **데이터 원본 추가를**선택하고, **Azure 저장소의**저장소 유형을 선택하고, Azure 저장소 계정 이름 및 키를 연결합니다. 그런 다음 저장소 계정에 저장된 데이터를 참조할 수 있습니다.

![데이터 원본 추가 대화 상자의 스크린샷](./media/vm-do-ten-things/Link_Blob_to_ADLA_v2.PNG)

Visual Studio에서 Blob 저장소에서 데이터를 읽고, 데이터를 조작하고, 기능을 엔지니어링하고, 결과 데이터를 Azure Data Lake 또는 Azure Blob 저장소로 보낼 수 있습니다. Blob 저장소의 데이터를 참조할 때 **wasb://** 사용합니다. Azure Data Lake에서 데이터를 참조할 때 **swbhdfs://.**

Visual Studio에서 다음 U-SQL 쿼리를 사용할 수 있습니다.

```usql
@a =
    EXTRACT medallion string,
            hack_license string,
            vendor_id string,
            rate_code string,
            store_and_fwd_flag string,
            pickup_datetime string,
            dropoff_datetime string,
            passenger_count int,
            trip_time_in_secs double,
            trip_distance double,
            pickup_longitude string,
            pickup_latitude string,
            dropoff_longitude string,
            dropoff_latitude string

    FROM "wasb://<Container name>@<Azure Blob Storage Account Name>.blob.core.windows.net/<Input Data File Name>"
    USING Extractors.Csv();

@b =
    SELECT vendor_id,
    COUNT(medallion) AS cnt_medallion,
    SUM(passenger_count) AS cnt_passenger,
    AVG(trip_distance) AS avg_trip_dist,
    MIN(trip_distance) AS min_trip_dist,
    MAX(trip_distance) AS max_trip_dist,
    AVG(trip_time_in_secs) AS avg_trip_time
    FROM @a
    GROUP BY vendor_id;

OUTPUT @b   
TO "swebhdfs://<Azure Data Lake Storage Account Name>.azuredatalakestore.net/<Folder Name>/<Output Data File Name>"
USING Outputters.Csv();

OUTPUT @b   
TO "wasb://<Container name>@<Azure Blob Storage Account Name>.blob.core.windows.net/<Output Data File Name>"
USING Outputters.Csv();
```

쿼리가 서버에 제출되면 다이어그램에 작업 상태가 표시됩니다.

![작업 상태 다이어그램의 스크린샷](./media/vm-do-ten-things/USQL_Job_Status.PNG)

#### <a name="query-data-in-data-lake-u-sql"></a>Data Lake의 데이터 쿼리: U SQL

Azure Data Lake에서 데이터 집합을 수집한 후 [U-SQL 언어를](../../data-lake-analytics/data-lake-analytics-u-sql-get-started.md) 사용하여 데이터를 쿼리하고 탐색할 수 있습니다. U-SQL 언어는 T-SQL과 유사하지만 사용자가 사용자 지정된 모듈 및 사용자 정의 함수를 작성할 수 있도록 C#의 일부 기능을 결합합니다. 이전 단계의 스크립트를 사용할 수 있습니다.

쿼리가 서버에 제출된 후 tripdata_summary. CSV는 Azure 데이터 레이크 탐색기에서 나타납니다. 파일을 마우스 오른쪽 단추로 클릭하여 데이터를 미리 볼 수 있습니다.

![데이터 레이크 탐색기에서 CSV 파일의 스크린샷](./media/vm-do-ten-things/USQL_create_summary.png)

파일 정보가 나타납니다.

![파일 요약 정보의 스크린샷](./media/vm-do-ten-things/USQL_tripdata_summary.png)

### <a name="hdinsight-hadoop-clusters"></a>HDInsight 하두프 클러스터
Azure HDInsight는 클라우드에서 관리되는 아파치 하두프, 스파크, HBase 및 스톰 서비스입니다. 데이터 과학 가상 머신의 Azure HDInsight 클러스터를 사용하여 쉽게 작업할 수 있습니다.

#### <a name="prerequisites"></a>사전 요구 사항

* [Azure 포털에서](https://portal.azure.com)Azure Blob 저장소 계정을 만듭니다. 이 스토리지 계정은 HDInsight 클러스터에 대한 데이터를 저장하는 데 사용됩니다.

   ![Azure 포털에서 저장소 계정을 만드는 스크린샷](./media/vm-do-ten-things/Create_Azure_Blob.PNG)

* [Azure 포털에서](../team-data-science-process/customize-hadoop-cluster.md)Azure HDInsight Hadoop 클러스터를 사용자 지정합니다.
  
   HDInsight 클러스터가 생성될 때 생성된 스토리지 계정을 연결합니다. 이 스토리지 계정은 클러스터 내에서 처리할 수 있는 데이터에 액세스하는 데 사용됩니다.

   ![HDInsight 클러스터로 만든 스토리지 계정을 연결하기 위한 선택 사항](./media/vm-do-ten-things/Create_HDI_v4.PNG)

* 클러스터를 만든 후 클러스터의 헤드 노드에 대한 원격 데스크톱 액세스를 활성화합니다. 후속 프로시저에서 필요하므로 여기에서 지정한 원격 액세스 자격 증명을 기억하십시오.

   ![HDInsight 클러스터에 대한 원격 액세스를 가능하게 하는 원격 데스크톱 버튼](./media/vm-do-ten-things/Create_HDI_dashboard_v3.PNG)

* Azure Machine Learning 작업 영역을 만듭니다. 기계 학습 실험은 이 기계 학습 작업 영역에 저장됩니다. 다음 스크린샷과 같이 포털에서 강조 표시된 옵션을 선택합니다.

   ![Azure Machine Learning 작업 영역 만들기](./media/vm-do-ten-things/Create_ML_Space.PNG)

* 작업 영역에 대한 매개 변수를 입력합니다.

   ![Machine Learning 작업 영역 매개 변수 입력](./media/vm-do-ten-things/Create_ML_Space_step2_v2.PNG)

* IPython 노트북을 사용하여 데이터를 업로드합니다. 필수 패키지를 가져오고, 자격 증명을 연결하고, 저장소 계정에 데이터베이스를 만든 다음 데이터를 HDI 클러스터에 로드합니다.

```python
# Import required packages
import pyodbc
import time as time
import json
import os
import urllib
import urllib2
import warnings
import re
import pandas as pd
import matplotlib.pyplot as plt
from azure.storage.blob import BlobService
warnings.filterwarnings("ignore", category=UserWarning, module='urllib2')


# Create the connection to Hive by using ODBC
SERVER_NAME = 'xxx.azurehdinsight.net'
DATABASE_NAME = 'nyctaxidb'
USERID = 'xxx'
PASSWORD = 'xxxx'
DB_DRIVER = 'Microsoft Hive ODBC Driver'
driver = 'DRIVER={' + DB_DRIVER + '}'
server = 'Host=' + SERVER_NAME + ';Port=443'
database = 'Schema=' + DATABASE_NAME
hiveserv = 'HiveServerType=2'
auth = 'AuthMech=6'
uid = 'UID=' + USERID
pwd = 'PWD=' + PASSWORD
CONNECTION_STRING = ';'.join(
    [driver, server, database, hiveserv, auth, uid, pwd])
connection = pyodbc.connect(CONNECTION_STRING, autocommit=True)
cursor = connection.cursor()


# Create the Hive database and tables
queryString = "create database if not exists nyctaxidb;"
cursor.execute(queryString)

queryString = """
                create external table if not exists nyctaxidb.trip
                (
                    medallion string,
                    hack_license string,
                    vendor_id string,
                    rate_code string,
                    store_and_fwd_flag string,
                    pickup_datetime string,
                    dropoff_datetime string,
                    passenger_count int,
                    trip_time_in_secs double,
                    trip_distance double,
                    pickup_longitude double,
                    pickup_latitude double,
                    dropoff_longitude double,
                    dropoff_latitude double)  
                PARTITIONED BY (month int)
                ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' lines terminated by '\\n'
                STORED AS TEXTFILE LOCATION 'wasb:///nyctaxidbdata/trip' TBLPROPERTIES('skip.header.line.count'='1');
            """
cursor.execute(queryString)

queryString = """
                create external table if not exists nyctaxidb.fare
                (
                    medallion string,
                    hack_license string,
                    vendor_id string,
                    pickup_datetime string,
                    payment_type string,
                    fare_amount double,
                    surcharge double,
                    mta_tax double,
                    tip_amount double,
                    tolls_amount double,
                    total_amount double)
                PARTITIONED BY (month int)
                ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' lines terminated by '\\n'
                STORED AS TEXTFILE LOCATION 'wasb:///nyctaxidbdata/fare' TBLPROPERTIES('skip.header.line.count'='1');
            """
cursor.execute(queryString)


# Upload data from Blob storage to an HDI cluster
for i in range(1, 13):
    queryString = "LOAD DATA INPATH 'wasb:///nyctaxitripraw2/trip_data_%d.csv' INTO TABLE nyctaxidb2.trip PARTITION (month=%d);" % (
        i, i)
    cursor.execute(queryString)
    queryString = "LOAD DATA INPATH 'wasb:///nyctaxifareraw2/trip_fare_%d.csv' INTO TABLE nyctaxidb2.fare PARTITION (month=%d);" % (
        i, i)
    cursor.execute(queryString)
```

또는 [이 연습에](../team-data-science-process/hive-walkthrough.md) 따라 NYC Taxi 데이터를 HDI 클러스터에 업로드할 수 있습니다. 주요 단계는 다음과 같습니다.
  
* AzCopy를 사용하여 공용 Blob에서 로컬 폴더로 압축된 CSV를 다운로드합니다.
* AzCopy를 사용하여 로컬 폴더에서 압축 해제된 CSV를 HDI 클러스터로 업로드합니다.
* Hadoop 클러스터의 헤드 노드에 로그인하여 예비 데이터 분석을 준비합니다.

데이터가 HDI 클러스터에 로드된 후 Azure 저장소 탐색기에서 데이터를 확인할 수 있습니다. 그리고 nyctaxidb 데이터베이스는 HDI 클러스터에서 만들어졌습니다.

#### <a name="data-exploration-hive-queries-in-python"></a>데이터 탐색: Python에서 Hive 쿼리

데이터가 Hadoop 클러스터에 있기 때문에 pyodbc 패키지를 사용하여 Hive를 사용하여 탐색 및 기능 엔지니어링을 수행하여 Hadoop 클러스터에 연결하고 데이터베이스를 쿼리할 수 있습니다. 필수 구성 조건 단계에서 만든 기존 테이블을 볼 수 있습니다.

```python
queryString = """
    show tables in nyctaxidb2;
    """
pd.read_sql(queryString, connection)
```

![기존 테이블 보기](./media/vm-do-ten-things/Python_View_Existing_Tables_Hive_v3.PNG)

각 월의 레코드 수와 trip 테이블에서 팁을 받은 여정과 팁을 받지 않은 여정의 빈도를 살펴보겠습니다.

```python
queryString = """
    select month, count(*) from nyctaxidb.trip group by month;
    """
results = pd.read_sql(queryString,connection)

%matplotlib inline

results.columns = ['month', 'trip_count']
df = results.copy()
df.index = df['month']
df['trip_count'].plot(kind='bar')
```

![월별 레코드 수에 대한 도표](./media/vm-do-ten-things/Exploration_Number_Records_by_Month_v3.PNG)

```python
queryString = """
    SELECT tipped, COUNT(*) AS tip_freq
    FROM
    (
        SELECT if(tip_amount > 0, 1, 0) as tipped, tip_amount
        FROM nyctaxidb.fare
    )tc
    GROUP BY tipped;
    """
results = pd.read_sql(queryString, connection)

results.columns = ['tipped', 'trip_count']
df = results.copy()
df.index = df['tipped']
df['trip_count'].plot(kind='bar')
```

![팁 빈도 도표](./media/vm-do-ten-things/Exploration_Frequency_tip_or_not_v3.PNG)

또한 픽업 위치와 드롭오프 위치 사이의 거리를 계산한 다음 이동 거리와 비교할 수 있습니다.

```python
queryString = """
                select pickup_longitude, pickup_latitude, dropoff_longitude, dropoff_latitude, trip_distance, trip_time_in_secs,
                    3959*2*2*atan((1-sqrt(1-pow(sin((dropoff_latitude-pickup_latitude)
                    *radians(180)/180/2),2)-cos(pickup_latitude*radians(180)/180)
                    *cos(dropoff_latitude*radians(180)/180)*pow(sin((dropoff_longitude-pickup_longitude)*radians(180)/180/2),2)))
                    /sqrt(pow(sin((dropoff_latitude-pickup_latitude)*radians(180)/180/2),2)
                    +cos(pickup_latitude*radians(180)/180)*cos(dropoff_latitude*radians(180)/180)*
                    pow(sin((dropoff_longitude-pickup_longitude)*radians(180)/180/2),2))) as direct_distance
                    from nyctaxidb.trip
                    where month=1
                        and pickup_longitude between -90 and -30
                        and pickup_latitude between 30 and 90
                        and dropoff_longitude between -90 and -30
                        and dropoff_latitude between 30 and 90;
            """
results = pd.read_sql(queryString, connection)
results.head(5)
```

![픽업 및 드롭오프 테이블의 맨 위 행](./media/vm-do-ten-things/Exploration_compute_pickup_dropoff_distance_v2.PNG)

```python
results.columns = ['pickup_longitude', 'pickup_latitude', 'dropoff_longitude',
                   'dropoff_latitude', 'trip_distance', 'trip_time_in_secs', 'direct_distance']
df = results.loc[results['trip_distance'] <= 100]  # remove outliers
df = df.loc[df['direct_distance'] <= 100]  # remove outliers
plt.scatter(df['direct_distance'], df['trip_distance'])
```

![이동 거리까지의 픽업/드롭오프 거리 플롯](./media/vm-do-ten-things/Exploration_direct_distance_trip_distance_v2.PNG)

이제 모델링을 위해 다운샘플링된(1%) 데이터 집합을 준비해 보겠습니다. 기계 학습 리더 모듈에서 이 데이터를 사용할 수 있습니다.

```python
queryString = """
create  table if not exists nyctaxi_downsampled_dataset_testNEW (
medallion string,
hack_license string,
vendor_id string,
rate_code string,
store_and_fwd_flag string,
pickup_datetime string,
dropoff_datetime string,
pickup_hour string,
pickup_week string,
weekday string,
passenger_count int,
trip_time_in_secs double,
trip_distance double,
pickup_longitude double,
pickup_latitude double,
dropoff_longitude double,
dropoff_latitude double,
direct_distance double,
payment_type string,
fare_amount double,
surcharge double,
mta_tax double,
tip_amount double,
tolls_amount double,
total_amount double,
tipped string,
tip_class string
)
row format delimited fields terminated by ','
lines terminated by '\\n'
stored as textfile;
"""
cursor.execute(queryString)
```

이제 조인의 내용을 이전 내부 테이블에 삽입합니다.

```python
queryString = """
insert overwrite table nyctaxi_downsampled_dataset_testNEW
select
t.medallion,
t.hack_license,
t.vendor_id,
t.rate_code,
t.store_and_fwd_flag,
t.pickup_datetime,
t.dropoff_datetime,
hour(t.pickup_datetime) as pickup_hour,
weekofyear(t.pickup_datetime) as pickup_week,
from_unixtime(unix_timestamp(t.pickup_datetime, 'yyyy-MM-dd HH:mm:ss'),'u') as weekday,
t.passenger_count,
t.trip_time_in_secs,
t.trip_distance,
t.pickup_longitude,
t.pickup_latitude,
t.dropoff_longitude,
t.dropoff_latitude,
t.direct_distance,
f.payment_type,
f.fare_amount,
f.surcharge,
f.mta_tax,
f.tip_amount,
f.tolls_amount,
f.total_amount,
if(tip_amount>0,1,0) as tipped,
if(tip_amount=0,0,
if(tip_amount>0 and tip_amount<=5,1,
if(tip_amount>5 and tip_amount<=10,2,
if(tip_amount>10 and tip_amount<=20,3,4)))) as tip_class
from
(
select
medallion,
hack_license,
vendor_id,
rate_code,
store_and_fwd_flag,
pickup_datetime,
dropoff_datetime,
passenger_count,
trip_time_in_secs,
trip_distance,
pickup_longitude,
pickup_latitude,
dropoff_longitude,
dropoff_latitude,
3959*2*2*atan((1-sqrt(1-pow(sin((dropoff_latitude-pickup_latitude)
radians(180)/180/2),2)-cos(pickup_latitude*radians(180)/180)
*cos(dropoff_latitude*radians(180)/180)*pow(sin((dropoff_longitude-pickup_longitude)*radians(180)/180/2),2)))
/sqrt(pow(sin((dropoff_latitude-pickup_latitude)*radians(180)/180/2),2)
+cos(pickup_latitude*radians(180)/180)*cos(dropoff_latitude*radians(180)/180)*pow(sin((dropoff_longitude-pickup_longitude)*radians(180)/180/2),2))) as direct_distance,
rand() as sample_key

from trip
where pickup_latitude between 30 and 90
    and pickup_longitude between -90 and -30
    and dropoff_latitude between 30 and 90
    and dropoff_longitude between -90 and -30
)t
join
(
select
medallion,
hack_license,
vendor_id,
pickup_datetime,
payment_type,
fare_amount,
surcharge,
mta_tax,
tip_amount,
tolls_amount,
total_amount
from fare
)f
on t.medallion=f.medallion and t.hack_license=f.hack_license and t.pickup_datetime=f.pickup_datetime
where t.sample_key<=0.01
"""
cursor.execute(queryString)
```

잠시 후 데이터가 Hadoop 클러스터에 로드된 것을 볼 수 있습니다.

```python
queryString = """
    select * from nyctaxi_downsampled_dataset limit 10;
    """
cursor.execute(queryString)
pd.read_sql(queryString, connection)
```

![테이블에서 데이터의 상위 행](./media/vm-do-ten-things/DownSample_Data_For_Modeling_v2.PNG)

#### <a name="read-data-from-hdi-by-using-azure-machine-learning-studio-classic-reader-module"></a>Azure 기계 학습 스튜디오(클래식): 리더 모듈을 사용하여 HDI에서 데이터 읽기

Azure 기계 학습 스튜디오(클래식)의 판독기 모듈을 사용하여 Hadoop 클러스터의 데이터베이스에 액세스할 수도 있습니다. HDI 클러스터 및 Azure 저장소 계정의 자격 증명을 연결하여 HDI 클러스터의 데이터베이스를 사용하여 기계 학습 모델을 구축할 수 있습니다.

![판독기 모듈 속성](./media/vm-do-ten-things/AML_Reader_Hive.PNG)

그런 다음 점수가 매겨짐된 데이터 집합을 볼 수 있습니다.

![채점된 데이터 세트 보기](./media/vm-do-ten-things/AML_Model_Results.PNG)

### <a name="azure-sql-data-warehouse-and-databases"></a>Azure SQL 데이터 웨어하우스 및 데이터베이스
Azure SQL 데이터 웨어하우스는 엔터프라이즈급 SQL Server 환경을 갖춘 서비스로서의 탄력적 데이터 웨어하우스입니다.

[이 문서의](../../sql-data-warehouse/sql-data-warehouse-get-started-provision.md)지침에 따라 Azure SQL 데이터 웨어하우스를 프로비전할 수 있습니다. SQL 데이터 웨어하우스를 프로비전한 후 [이 연습에서](../team-data-science-process/sqldw-walkthrough.md) SQL 데이터 웨어하우스 내의 데이터를 사용하여 데이터 업로드, 탐색 및 모델링을 수행할 수 있습니다.

#### <a name="azure-cosmos-db"></a>Azure Cosmos DB
Azure Cosmos DB는 클라우드의 NoSQL 데이터베이스입니다. JSON과 같은 문서로 작업하고 문서를 저장하고 쿼리하는 데 사용할 수 있습니다.

DSVM에서 Azure Cosmos DB에 액세스하려면 다음 필수 구성 단계를 사용합니다.

1. Azure 코스모스 DB 파이썬 SDK가 이미 DSVM에 설치되어 있습니다. 업데이트하려면 명령 ```pip install pydocumentdb --upgrade``` 프롬프트에서 실행합니다.
2. Azure [포털에서](https://portal.azure.com)Azure Cosmos DB 계정 및 데이터베이스를 만듭니다.
3. [Microsoft 다운로드 센터에서](https://www.microsoft.com/download/details.aspx?id=53595) Azure Cosmos DB 데이터 마이그레이션 도구를 다운로드하여 선택한 디렉토리로 추출합니다.
4. [공용 Blob에](https://cahandson.blob.core.windows.net/samples/volcano.json) 저장된 JSON 데이터(화산 데이터)를 Azure Cosmos DB로 가져오고 다음 명령 매개 변수를 마이그레이션 도구로 가져옵니다. Azure Cosmos DB 데이터 마이그레이션 도구를 설치한 디렉터리에서 dtui.exe를 사용합니다. 다음 매개변수를 사용 하 고 소스 및 대상 위치를 입력 합니다.
   
    `/s:JsonFile /s.Files:https://cahandson.blob.core.windows.net/samples/volcano.json /t:DocumentDBBulk /t.ConnectionString:AccountEndpoint=https://[DocDBAccountName].documents.azure.com:443/;AccountKey=[[KEY];Database=volcano /t.Collection:volcano1`

데이터를 가져온 후 Jupyter로 이동하여 *DocumentDBSample*이라는 노트북을 열 수 있습니다. Azure Cosmos DB에 액세스하고 몇 가지 기본 쿼리를 수행하는 파이썬 코드가 포함되어 있습니다. Azure Cosmos DB에 대한 자세한 내용은 서비스의 [설명서 페이지를](https://docs.microsoft.com/azure/cosmos-db/)참조하세요.

## <a name="use-power-bi-reports-and-dashboards"></a>Power BI 보고서 및 대시보드 사용 
Power BI 데스크톱의 앞의 Azure Cosmos DB 예제에서 Volcano JSON 파일을 시각화하여 데이터에 대한 시각적 인 통찰력을 얻을 수 있습니다. 자세한 단계는 [Power BI 문서](../../cosmos-db/powerbi-visualize.md)에서 확인할 수 있습니다. 대략적인 단계는 다음과 같습니다.

1. Power BI Desktop을 열고 **Get Data**를 선택합니다. URL을 다음과 `https://cahandson.blob.core.windows.net/samples/volcano.json`같이 지정합니다.
2. 목록으로 가져온 JSON 레코드가 표시됩니다. Power BI가 사용할 수 있도록 목록을 테이블로 변환합니다.
4. 확장(화살표) 아이콘을 선택하여 열을 확장합니다.
5. 위치는 **레코드** 필드입니다. 레코드를 확장하고 좌표만 선택합니다. **좌표는** 목록 열입니다.
6. 새 열을 추가하여 목록 좌표 열을 쉼표로 구분된 **LatLong** 열로 변환합니다. 좌표 리스트 필드의 두 요소를 수식을 ```Text.From([coordinates]{1})&","&Text.From([coordinates]{0})```사용하여 연결합니다.
7. **입면** 열을 소수로 변환하고 **닫기** 및 **적용** 단추를 선택합니다.

이전 단계 대신 다음 코드를 붙여넣기를 할 수 있습니다. Power BI의 고급 편집기에서 쿼리 언어로 데이터 변환을 작성하는 데 사용되는 단계를 스크립팅합니다.

```pqfl
let
    Source = Json.Document(Web.Contents("https://cahandson.blob.core.windows.net/samples/volcano.json")),
    #"Converted to Table" = Table.FromList(Source, Splitter.SplitByNothing(), null, null, ExtraValues.Error),
    #"Expanded Column1" = Table.ExpandRecordColumn(#"Converted to Table", "Column1", {"Volcano Name", "Country", "Region", "Location", "Elevation", "Type", "Status", "Last Known Eruption", "id"}, {"Volcano Name", "Country", "Region", "Location", "Elevation", "Type", "Status", "Last Known Eruption", "id"}),
    #"Expanded Location" = Table.ExpandRecordColumn(#"Expanded Column1", "Location", {"coordinates"}, {"coordinates"}),
    #"Added Custom" = Table.AddColumn(#"Expanded Location", "LatLong", each Text.From([coordinates]{1})&","&Text.From([coordinates]{0})),
    #"Changed Type" = Table.TransformColumnTypes(#"Added Custom",{{"Elevation", type number}})
in
    #"Changed Type"
```

이제 Power BI 데이터 모델에 데이터가 있습니다. Power BI 데스크톱 인스턴스는 다음과 같이 표시되어야 합니다.

![Power BI Desktop](./media/vm-do-ten-things/PowerBIVolcanoData.png)

데이터 모델을 사용하여 보고서 및 시각화 빌드를 시작할 수 있습니다. 이 Power BI [문서의](../../cosmos-db/powerbi-visualize.md#build-the-reports) 단계를 수행하여 보고서를 작성할 수 있습니다.

## <a name="scale-the-dsvm-dynamically"></a>DSVM을 동적으로 확장 
프로젝트의 요구 사항을 충족하도록 DSVM을 확장하고 축소할 수 있습니다. 저녁이나 주말에 VM을 사용할 필요가 없는 경우 [Azure 포털에서](https://portal.azure.com)VM을 종료할 수 있습니다.

> [!NOTE]
> VM의 운영 체제에 대한 종료 버튼만 사용하는 경우 계산 요금이 부과됩니다.  
> 
> 

일부 대규모 분석을 처리해야 하고 더 많은 CPU, 메모리 또는 디스크 용량이 필요할 수 있습니다. 이 경우 CPU 코어, 딥 러닝용 GPU 기반 인스턴스, 메모리 용량 및 컴퓨팅 및 예산 요구 사항을 충족하는 디스크 유형(솔리드 스테이트 드라이브 포함)에서 VM 크기를 선택할 수 있습니다. 시간별 계산 가격과 함께 전체 VM 목록은 [Azure 가상 컴퓨터 가격 책정](https://azure.microsoft.com/pricing/details/virtual-machines/) 페이지에서 확인할 수 있습니다.

마찬가지로 VM 처리 용량에 대한 필요성도 줄어들 수 있습니다. (예: 주요 워크로드를 Hadoop 또는 Spark 클러스터로 이동했습니다.) 그런 다음 [Azure 포털에서](https://portal.azure.com) 클러스터를 축소하고 VM 인스턴스의 설정으로 이동합니다. 

## <a name="add-more-tools"></a>도구 추가
DSVM에 미리 빌드된 도구는 많은 일반적인 데이터 분석 요구 사항을 해결할 수 있습니다. 이렇게 하면 환경을 하나씩 설치하고 구성할 필요가 없으므로 시간을 절약할 수 있습니다. 또한 사용하는 리소스에 대해서만 비용을 지불하기 때문에 비용을 절약할 수 있습니다.

이 문서에서 프로파일린 다른 Azure 데이터 및 분석 서비스를 사용하여 분석 환경을 향상시킬 수 있습니다. 경우에 따라 일부 독점 파트너 도구를 포함하여 추가 도구가 필요할 수 있습니다. 가상 시스템에 대한 모든 관리 액세스 권한을 가하여 필요한 새 도구를 설치할 수 있습니다. 또한 사전 설치되지 않은 추가 패키지를 Python 및 R에 설치할 수 있습니다. 파이썬의 경우 ```conda``` 또는 ```pip```. R의 경우 R ```install.packages()``` 콘솔에서 사용하거나 IDE를 사용하고 **패키지** > 설치 패키지를 선택할 수**있습니다.**

## <a name="deep-learning"></a>딥 러닝

프레임워크 기반 샘플 외에도 DSVM에서 유효성을 검사한 포괄적인 연습 집합을 얻을 수 있습니다. 이러한 연습은 이미지 및 텍스트/언어 이해와 같은 도메인에서 딥 러닝 응용 프로그램 개발을 시작하는 데 도움이 됩니다.   


- [서로 다른 프레임워크에서 신경망 실행](https://github.com/ilkarman/DeepLearningFrameworks): 이 연습에서는 한 프레임워크에서 다른 프레임워크로 코드를 마이그레이션하는 방법을 보여 주며 이 연습에서는 코드를 마이그레이션하는 방법을 보여 주며, 이 연습에서는 코드를 다른 프레임워크로 마이그레이션하는 방법을 보여 주며, 이 연습에서는 코드를 다른 프레임워크로 마이그레이션하는 방법을 보여 주며, 이 연습에서는 코드를 다른 프레임워크로 마이그레이션 또한 프레임워크 전체에서 모델과 런타임 성능을 비교하는 방법을 보여 줍니다. 

- [이미지 내 제품을 검색하는 엔드투엔드 솔루션을 빌드하는 방법 가이드](https://github.com/Azure/cortana-intelligence-product-detection-from-images): 이미지 검색은 이미지 내의 개체를 찾고 분류할 수 있는 기술입니다. 이 기술은 많은 실제 비즈니스 영역에서 엄청난 보상을 가져올 수있는 잠재력을 가지고 있습니다. 예를 들어 소매업체는 이 기술을 사용하여 고객이 선택한 제품을 확인할 수 있습니다. 또한 이 정보는 매장의 제품 재고를 관리하는 데 도움이 됩니다. 

- [오디오에 대한 딥 러닝](https://blogs.technet.microsoft.com/machinelearning/2018/01/30/hearing-ai-getting-started-with-deep-learning-for-audio-on-azure/): 이 자습서에서는 도시 사운드 데이터 [집합에서](https://serv.cusp.nyu.edu/projects/urbansounddataset/urbansound8k.html)오디오 이벤트 감지를 위한 딥 러닝 모델을 학습하는 방법을 보여 주며. 또한 오디오 데이터 로 작업하는 방법에 대한 개요도 제공합니다.

- [텍스트 문서 분류](https://github.com/anargyri/lstm_han): 이 연습에서는 계층적 주의 네트워크및 LSTM(장기 단기 기억) 네트워크라는 두 개의 신경망 아키텍처를 빌드하고 학습하는 방법을 보여 줍니다. 이러한 신경망은 Keras API를 딥 러닝에 사용하여 텍스트 문서를 분류합니다. 케라스는 가장 인기있는 딥 러닝 프레임 워크의 세 가지 프론트 엔드입니다: 마이크로 소프트 코그너티브 툴킷, 텐서 플로우, 그리고 테아노.

## <a name="summary"></a>요약
이 문서에서는 Microsoft 데이터 과학 가상 컴퓨터에서 수행할 수 있는 몇 가지 작업을 설명합니다. DSVM을 효과적인 분석 환경으로 만들기 위해 할 수 있는 일은 더 많습니다.

