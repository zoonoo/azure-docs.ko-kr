---
title: "항공 이미지 분류 | Microsoft Docs"
description: "항공 이미지 분류 실제 시나리오에 대한 지침을 제공합니다."
author: mawah
ms.author: mawah
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.topic: article
ms.service: machine-learning
services: machine-learning
ms.date: 09/15/2017
ms.openlocfilehash: e6b673527d77550d4213e6d742156ccc525f6b44
ms.sourcegitcommit: 9ae92168678610f97ed466206063ec658261b195
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/17/2017
---
# <a name="aerial-image-classification"></a>항공 이미지 분류

이 예제는 Azure Machine Learning Workbench를 사용해 이미지 분류 모델의 분산된 교육 및 운영을 조정하는 방법을 보여줍니다. 미리 교육된 CNTK 모델을 사용하여 이미지를 기능화하고 파생된 기능을 사용하여 분류자를 교육하도록 [Microsoft Machine Learning for Apache Spark (MMLSpark)](https://github.com/Azure/mmlspark) 패키지를 사용합니다. 그런 후 교육된 모델을 그대로 클라우드에 있는 큰 이미지 집합에 적용합니다. 이 단계는 [Azure HDInsight Spark](https://azure.microsoft.com/en-us/services/hdinsight/apache-spark/) 클러스터에서 수행되어 작업자 노드를 추가 또는 제거하여 교육 및 운영 속도를 변경할 수 있게 해 줍니다.

우리가 보여준 전이 교육의 양식은 심층 신경망을 재교육하거나 미세 조정하는 것에 비해 주된 장점이 있습니다. GPU 컴퓨팅을 필요로 하지 않으며, 본질적으로 빠르고 임의로 확장할 수 있으며, 매개 변수도 적게 사용합니다. 따라서 이 메서드는 사용자 지정 사용 사례인 경우 흔히 사용되듯이 교육 샘플이 거의 없는 경우에 매우 적합합니다. 많은 사용자가 훨씬 큰 비용을 들여 처음부터 교육되는 심층 신경망을 피할 수 있어 전이 교육이 성능이 뛰어난 모델을 생성한다고 보고합니다.

이 예에 사용된 Spark 클러스터는 40개의 작업자 노드가 있으며 운영하는 데 시간당 40불의 비용이 듭니다. 이 연습을 완료하려면 약 2시간 가량 걸립니다. 연습을 마치면 정리 지침에 따라 만든 리소스를 제거하여 더 이상 비용이 발생하지 않게 합니다.

## <a name="link-to-the-gallery-github-repository"></a>갤러리 GitHub 리포지토리에 연결

이 실제 시나리오에 대한 공용 GitHub 리포지토리에는 이 예제에 필요한 코드 샘플을 포함한 모든 자료가 들어 있습니다.

[https://github.com/Azure/MachineLearningSamples-AerialImageClassification](https://github.com/Azure/MachineLearningSamples-AerialImageClassification)

## <a name="use-case-description"></a>사용 사례 설명

이 시나리오에서는 224미터 x 224미터 평면도 항공 이미지에 보이는 토지 유형을 분류할 기계 교육 모델을 교육합니다. 토지 사용 분류 모델은 정기적으로 수집된 항공 이미지를 사용하여 도시화, 삼림 벌채, 습지 유실 및 기타 주요 환경적 경향을 추적하는 데 사용할 수 있습니다. 미국의 이미지를 기반으로 교육 및 유효성 검사 이미지 집합을 준비했습니다. National Agriculture Imagery Program and land는 미국에서 게시하는 라벨을 사용합니다. National Land Cover Database. 각 토지의 이미지가 클래스를 사용하는 예제는 다음과 같습니다.

![각 토지가 라벨을 사용하는 예제](media/scenario-aerial-image-classification/example-labels.PNG)

분류자 모델을 교육하고 유효성 검사를 한 후, 우리는 이러한 모델이 도시 개발에서 추세를 연구하는 데 어떻게 사용될 수 있는지 보여주기 위해 Microsoft의 뉴 잉글랜드 연구 및 개발(NERD) 센터가 위치한 메사추세츠 미들섹스 카운티의 항공 사진에 적용할 것입니다.

전이 학습을 사용하는 이미지 분류자를 생성하기 위해 데이터 과학자는 흔히 다양한 메서드로 여러 모델을 생성한 후 가장 성능이 좋은 모델을 선택합니다. 데이터 과학자는 Azure Machine Learning Workbench를 활용하여 여러 다른 컴퓨팅 환경에 걸쳐 교육을 조정하고, 여러 모델의 성능을 추적 및 비교하여, 선택한 모델을 클라우드에 있는 큰 데이터 집합에 적용할 수 있습니다.

## <a name="scenario-structure"></a>시나리오 구조

이 예제에서는 이미지 데이터와 미리 교육된 모델이 Azure 저장소 계정에 들어 있습니다. Azure HDInsight Spark 클러스터는 MMLSpark를 사용하여 이러한 파일을 읽고 이미지 분류 모델과 생성합니다. 교육된 모델 및 예측은 저장소 계정에 기록되어, 로컬에서 실행되는 Jupyter 노트북으로 분석하고 시각화할 수 있습니다. Azure Machine Learning Workbench는 스크립트를 Spark 클러스터에 원격 실행하는 것을 조정합니다. 또한 사용자가 가장 성능이 좋은 모델을 선택할 수 있도록 다양한 방법을 사용하여 교육한 여러 모델에 대한 정확도 메트릭을 추적합니다.

![항공 이미지 분류 실제 시나리오에 대한 계통도](media/scenario-aerial-image-classification/scenario-schematic.PNG)

이 단계별 지침은 데이터 전송 및 종속성 설치를 포함하여 Azure Storage 계정 및 Spark 클러스터 생성과 준비하는 과정을 안내합니다. 그런 다음 교육 작업을 시작하고 결과 모델의 성능을 비교하는 방법을 설명합니다. 마지막으로, 선택한 모델을 Spark 클러스터의 큰 이미지 집합에 적용하여 로컬에서 예측 결과 분석하는 방법을 보여 줍니다.


## <a name="set-up-the-execution-environment"></a>실행 환경 설정

다음 지침에서는 이 예제에 대한 실행 환경을 설정하는 과정을 안내합니다.

### <a name="prerequisites"></a>필수 조건
- [Azure 계정](https://azure.microsoft.com/en-us/free/)(평가판 사용 가능)
    - 이 샘플은 40 작업자 노드(총 168 코어)로 HDInsight Spark 클러스터를 만듭니다. 사용자 계정에 사용 가능한 충분한 코어가 있는지 Azure Portal에서 구독에 대한 "사용 + 할당량" 탭을 검토하여 확인합니다.
    - 사용할 수 있는 코어가 적다면 HDInsight 클러스터 템플릿을 수정하여 프로비전된 사용자 수를 줄일 수 있습니다. 이에 대한 지침은 "HDInsight Spark 클러스터 만들기" 섹션 아래에 있습니다.
- [Azure Machine Learning Workbench](./overview-what-is-azure-ml.md)
    - [빠른 시작 설치 및 만들기](quickstart-installation.md)에 따라 Azure Machine Learning Workbench를 설치하고 Experimentation and Model Management Accounts(실험 및 모델 관리 계정)을 만듭니다.
- [AzCopy](https://docs.microsoft.com/en-us/azure/storage/common/storage-use-azcopy), Azure 저장소 계정 간의 파일 전송을 조정하기 위한 유틸리티
    - AzCopy 실행 파일이 있는 폴더가 시스템의 PATH 환경 변수에 있는지 확인합니다. (환경 변수를 수정하는 방법에 대한 지침은 [여기](https://support.microsoft.com/en-us/help/310519/how-to-manage-environment-variables-in-windows-xp)에 있습니다). 이 예제는 Windows 10 PC에서 테스트 되었습니다; Azure 데이터 과학 가상 컴퓨터를 포함하여 모든 Windows 컴퓨터에서 실행할 수 있어야 합니다. 이 예제를 macOS에서 실행할 경우 약간의 수정이 필요할 수도 있습니다.(예: 파일 경로 변경)

### <a name="set-up-azure-resources"></a>Azure 리소스 설정

이 예제는 관련 파일을 호스트하기 위해 HDInsight Spark 클러스터 및 Azure 저장소 계정이 필요합니다. 새 Azure 리소스 그룹에 이러한 리소스를 만들려면 다음 지침을 따릅니다.

#### <a name="create-a-new-workbench-project"></a>새 워크벤치 프로젝트 만들기

이 예제를 템플릿으로 사용하여 새 프로젝트를 만듭니다.
1.  Azure Machine Learning Workbench 열기
2.  **프로젝트** 페이지에서 **+** 기호를 클릭하고 **새 프로젝트**를 선택합니다.
3.  **새 프로젝트 만들기** 창에서 새 프로젝트에 대한 정보를 입력합니다.
4.  **Search Project Templates** 검색 상자에서 "Aerial Image Classification(항공 이미지 분류)"을 입력하고 템플릿을 선택합니다.
5.  **만들기**
 
#### <a name="create-the-resource-group"></a>리소스 그룹 만들기

1. Azure Machine Learning Workbench 프로젝트에서 File -> Open Command Prompt를 클릭하여 명령줄 인터페이스(CLI)를 엽니다.
1. 명령줄 인터페이스에서 다음 명령을 실행하여 Azure 계정에 로그인합니다.

    ```
    az login
    ```

    URL을 방문하고 제공된 임시 코드를 입력해야 하며 웹 사이트가 Azure 계정 자격 증명을 요청합니다.
    
1. 로그인 완료되면 CLI 돌아가 다음 명령을 실행하여 Azure 계정에 사용할 수 있는 Azure 구독을 확인합니다.

    ```
    az account list
    ```

    이 명령은 Azure 계정과 연결된 모든 구독을 나열합니다. 사용하려는 구독 ID를 찾습니다. 다음 명령에서 표시된 위치에 구독 ID를 기입한 다음 명령을 실행하여 활성 구독을 설정합니다.

    ```
    az account set --subscription [subscription ID]
    ```

1. 이 예제에서 만든 Azure 리소스는 Azure 리소스 그룹에 함께 저장됩니다. 고유한 리소스 그룹 이름을 선택하고 두 명령을 실행하여 Azure 리소스 그룹을 만듭니다.

    ```
    set AZURE_RESOURCE_GROUP=[resource group name]
    az group create --location eastus --name %AZURE_RESOURCE_GROUP%
    ```

#### <a name="create-the-storage-account"></a>저장소 계정 만들기

이제 HDInsight Spark에서 액세스해야 하는 프로젝트 파일을 호스트하는 저장소 계정을 만듭니다.

1. 고유한 저장소 계정 이름을 선택하 고 다음 `set` 명령의 표시된 위치에 쓴 다음 두 명령을 실행하여 Azure Storage 계정을 만듭니다.

    ```
    set STORAGE_ACCOUNT_NAME=[storage account name]
    az storage account create --name %STORAGE_ACCOUNT_NAME% --resource-group %AZURE_RESOURCE_GROUP% --sku Standard_LRS
    ```

1. 다음 명령을 실행하여 저장소 계정 키를 검색합니다.

    ```
    az storage account keys list --resource-group %AZURE_RESOURCE_GROUP% --account-name %STORAGE_ACCOUNT_NAME%
    ```

    다음 명령에서 `key1` 값을 저장소 키로 기록한 다음 명령을 실행하여 값을 저장합니다.
    ```
    set STORAGE_ACCOUNT_KEY=[storage account key]
    ```
1. 원하는 텍스트 편집기에서 Azure Machine Learning Workbench 프로젝트의 하위 디렉터리 "Code"로부터 `settings.cfg` 파일을 로드하고 표시된 대로 저장소 계정 이름과 키를 삽입합니다. 파일의 수정된 줄은 다음과 유사하게 나타납니다.
    ```
    [Settings]
        # Credentials for the Azure Storage account
        storage_account_name = storacctname
        storage_account_key = kpI...88 chars total...Q==
    ```
    `settings.cfg` 파일을 저장하고 닫습니다.
1. [AzCopy](http://aka.ms/downloadazcopy) 유틸리티가 없으면 다운로드하여 설치합니다. "AzCopy" 입력 후 Enter 키를 눌러 설명을 표시하여 시스템 경로에 AzCopy 실행 파일이 있는지 확인합니다.
1. 다음 명령을 실행하여 예제 데이터, 미리 학습된 모델 및 모델 학습 스크립트를 모두 저장소 계정의 적절한 위치에 복사합니다.

    ```
    AzCopy /Source:https://mawahsparktutorial.blob.core.windows.net/test /SourceSAS:"?sv=2017-04-17&ss=bf&srt=sco&sp=rwl&se=2037-08-25T22:02:55Z&st=2017-08-25T14:02:55Z&spr=https,http&sig=yyO6fyanu9ilAeW7TpkgbAqeTnrPR%2BpP1eh9TcpIXWw%3D" /Dest:https://%STORAGE_ACCOUNT_NAME%.blob.core.windows.net/test /DestKey:%STORAGE_ACCOUNT_KEY% /S
    AzCopy /Source:https://mawahsparktutorial.blob.core.windows.net/train /SourceSAS:"?sv=2017-04-17&ss=bf&srt=sco&sp=rwl&se=2037-08-25T22:02:55Z&st=2017-08-25T14:02:55Z&spr=https,http&sig=yyO6fyanu9ilAeW7TpkgbAqeTnrPR%2BpP1eh9TcpIXWw%3D" /Dest:https://%STORAGE_ACCOUNT_NAME%.blob.core.windows.net/train /DestKey:%STORAGE_ACCOUNT_KEY% /S
    AzCopy /Source:https://mawahsparktutorial.blob.core.windows.net/middlesexma2016 /SourceSAS:"?sv=2017-04-17&ss=bf&srt=sco&sp=rwl&se=2037-08-25T22:02:55Z&st=2017-08-25T14:02:55Z&spr=https,http&sig=yyO6fyanu9ilAeW7TpkgbAqeTnrPR%2BpP1eh9TcpIXWw%3D" /Dest:https://%STORAGE_ACCOUNT_NAME%.blob.core.windows.net/middlesexma2016 /DestKey:%STORAGE_ACCOUNT_KEY% /S
    AzCopy /Source:https://mawahsparktutorial.blob.core.windows.net/pretrainedmodels /SourceSAS:"?sv=2017-04-17&ss=bf&srt=sco&sp=rwl&se=2037-08-25T22:02:55Z&st=2017-08-25T14:02:55Z&spr=https,http&sig=yyO6fyanu9ilAeW7TpkgbAqeTnrPR%2BpP1eh9TcpIXWw%3D" /Dest:https://%STORAGE_ACCOUNT_NAME%.blob.core.windows.net/pretrainedmodels /DestKey:%STORAGE_ACCOUNT_KEY% /S
    ```

    파일 전송은 최대 20분이 예상됩니다. 기다리는 동안 다음 섹션으로 진행할 수 있습니다. 워크벤치를 통해 다른 명령줄 인터페이스를 열어 임시 변수를 재정의해야 할 수 있습니다.

#### <a name="create-the-hdinsight-spark-cluster"></a>HDInsight Spark 클러스터 만들기

권장되는 HDInsight 클러스터 만들기 방법은 이 프로젝트의 "Code\01_Data_Acquisition_and_Understanding\01_HDInsight_Spark_Provisioning" 하위 폴더에 있는 HDInsight Spark 클러스터 Resource Manager 템플릿을 사용하는 것입니다. 

1. HDInsight Spark 클러스터 템플릿은 이 프로젝트의 하위 폴더 "Code\01_Data_Acquisition_and_Understanding\01_HDInsight_Spark_Provisioning"에 있는 "template.json" 파일입니다. 기본적으로 템플릿은 40 작업자 노드가 있는 Spark 클러스터를 만듭니다. 이 숫자를 조정해야 할 경우, 원하는 텍스트 편집기에서 템플릿을 열고 모든 “40” 인스턴스를 원하는 작업자 노드 수로 바꿉니다.
    - 선택한 작업자 노드 수 작으면 메모리 부족 오류가 발생할 수 있습니다. 메모리 오류를 방지하기 위해 이 문서의 뒷부분에 설명된 대로 사용 가능한 데이터의 하위 집합에서 교육 및 연산화 스크립트를 실행할 수 있습니다.
2. HDInsight 클러스터에 고유한 이름 및 암호를 선택하고 다음 명령의 표시된 위치에 씁니다. 다음 명령을 실행하여 클러스터를 만듭니다.

    ```
    set HDINSIGHT_CLUSTER_NAME=[HDInsight cluster name]
    set HDINSIGHT_CLUSTER_PASSWORD=[HDInsight cluster password]
    az group deployment create --resource-group %AZURE_RESOURCE_GROUP% --name hdispark --template-file "Code\01_Data_Acquisition_and_Understanding\01_HDInsight_Spark_Provisioning\template.json" --parameters storageAccountName=%STORAGE_ACCOUNT_NAME%.blob.core.windows.net storageAccountKey=%STORAGE_ACCOUNT_KEY% clusterName=%HDINSIGHT_CLUSTER_NAME% clusterLoginPassword=%HDINSIGHT_CLUSTER_PASSWORD%
    ```

클러스터 배포는 최대 30분이 걸릴 수 있습니다(프로비전 및 스크립트 동작 실행 포함).

### <a name="prepare-the-azure-machine-learning-workbench-execution-environment"></a>Azure Machine Learning Workbench 실행 환경 준비

#### <a name="register-the-hdinsight-cluster-as-an-azure-machine-learning-workbench-compute-target"></a>Machine Learning Workbench에서 계산 대상으로 HDInsight 클러스터 등록

HDInsight 클러스터를 만들고 나면 다음과 같이 클러스터를 프로젝트의 계산 대상으로 등록합니다.

1.  Azure Machine Learning 명령줄 인터페이스에서 다음 명령을 실행합니다.

    ```
    az ml computetarget attach --name myhdi --address %HDINSIGHT_CLUSTER_NAME%-ssh.azurehdinsight.net --username sshuser --password %HDINSIGHT_CLUSTER_PASSWORD% -t cluster
    ```

    이 명령은 두 파일(`myhdi.runconfig` 및 `myhdi.compute`)을 프로젝트의 `aml_config` 폴더에 추가합니다.

1. 원하는 텍스트 편집기에서 `myhdi.compute` 파일을 엽니다. `yarnDeployMode: cluster` 줄을 수정하여 `yarnDeployMode: client`를 읽은 다음 저장하고 파일을 닫습니다.
1. 다음 명령을 실행하여 환경을 사용하도록 준비합니다.
   ```
   az ml experiment prepare -c myhdi
   ```

#### <a name="install-local-dependencies"></a>로컬 종속성 설치

Azure Machine Learning Workbench에서 CLI를 열고 다음 명령을 실행하여 로컬 실행에 필요한 종속성을 설치합니다.

```
pip install matplotlib azure-storage==0.36.0 pillow scikit-learn
```

## <a name="data-acquisition-and-understanding"></a>데이터 취득 및 이해

이 시나리오에서는 [NAIP(National Agriculture Imagery Program)](https://www.fsa.usda.gov/programs-and-services/aerial-photography/imagery-programs/naip-imagery/)에서 공개 제공하는 1미터 해상도 항공 이미지 데이터를 사용합니다. 원본 NAIP 데이터에서 잘라낸 224 x 224 픽셀 PNG 파일 집합을 생성하고 [National Land Cover Database](https://www.mrlc.gov/nlcd2011.php)의 토지 사용 레이블에 따라 정렬했습니다. 레이블이 "Developed"(개발됨)인 샘플 이미지가 전체 크기로 표시됩니다.

![개발된 토지 샘플 타일](media/scenario-aerial-image-classification/sample-tile-developed.png)

~44k 및 11k 이미지의 클래스 분산 집합을 각각 모델 학습과 유효성 검사에 사용합니다. Microsoft NERD(New England Research and Development) 센터가 있는 Middlesex County(MA) 타일 ~67k 이미지 집합에서의 모델 배포를 설명합니다. 이러한 이미지 집합의 구성 방식에 대한 자세한 내용은 [상세 이미지 분류 GIT 리포지토리](https://github.com/Azure/Embarrassingly-Parallel-Image-Classification)를 참조하세요.

![매사추세츠 주 미들섹스 카운티의 위치](media/scenario-aerial-image-classification/middlesex-ma.png)

설치 중에, 만들어진 저장소 계정에 이 예에서 사용된 항공 이미지 집합이 전송되었습니다. 학습, 유효성 검사 및 연산화 이미지는 모두 평방미터당 1픽셀 해상도의 224 x 224 픽셀 PNG 파일입니다. 학습 및 유효성 검사 이미지는 토지 사용 레이블에 따라 하위 폴더에 구성되었습니다. 연산화 이미지의 토지 사용 레이블은 알 수 없으며 대부분의 경우 모호합니다. 이 이미지 중 상당수에는 여러 토지 유형이 포함되어 있습니다. 이러한 이미지 집합의 구성 방식에 대한 자세한 내용은 [상세 이미지 분류 GIT 리포지토리](https://github.com/Azure/Embarrassingly-Parallel-Image-Classification)를 참조하세요.

Azure Storage 계정에서 예제 이미지를 보려면(선택 사항)
1. [Azure 포털](https://portal.azure.com) 에 로그인합니다.
1. 화면 맨 위의 검색 표시줄에서 저장소 계정의 이름을 검색합니다. 검색 결과에서 저장소 계정을 클릭합니다.
2. 저장소 계정의 기본 창에서 "Blob" 링크를 클릭합니다.
3. 이름이 "train"인 컨테이너를 클릭합니다. 그러면 토지 사용에 따라 명령된 디렉터리 목록이 표시됩니다.
4. 이 디렉터리 중 하나를 클릭하여 그 안에 들어 있는 이미지 목록을 로드합니다.
5. 이미지를 클릭하고 다운로드하여 봅니다.
6. 원하는 경우 이름이 "test" 및 "middlesexma2016"인 컨테이너를 클릭하여 그 콘텐츠도 함께 확인합니다.

## <a name="modeling"></a>모델링

### <a name="training-models-with-mmlspark"></a>MMLSpark를 통한 모델 교육
워크벤치 프로젝트의 "Code\02_Modeling" 하위 폴더에 있는 `run_mmlspark.py` 스크립트를 사용하여 이미지 분류를 위해 [MMLSpark](https://github.com/Azure/mmlspark) 모델을 교육합니다. 이 스크립트는 먼저 ImageNet 데이터 집합(AlexNet 또는 18레이어 ResNet)에서 미리 학습된 이미지 분류자 DNN을 사용하여 학습 집합 이미지를 특성화합니다. 그런 다음 특성화된 이미지를 사용하여 이미지를 분류하도록 MMLSpark 모델을 교육합니다(임의 포리스트 또는 논리적 회귀 모델). 그러면 테스트 이미지 집합이 특성화되고 학습된 모델과 함께 채점됩니다. 테스트 집합의 모델 예측 정확도가 계산되어 Azure Machine Learning Workbench 실행 기록 특징에 기록됩니다. 마지막으로, 학습된 MMLSpark 모델 및 테스트 집합에 대한 예측이 BLOB Storage에 저장됩니다.

학습된 모델, 미리 학습된 모델 형식 및 MMLSpark 모델 형식에 대해 고유한 출력 모델 이름을 선택합니다. 다음 명령 템플릿의 표시된 위치에 선택을 기입한 다음 Azure ML 명령줄 인터페이스에서 명령을 실행하여 재교육을 시작합니다.

```
az ml experiment submit -c myhdi Code\02_Modeling\run_mmlspark.py --config_filename Code/settings.cfg --output_model_name [unique model name, alphanumeric characters only] --pretrained_model_type {alexnet,resnet18} --mmlspark_model_type {randomforest,logisticregression}
```

추가 `--sample_frac` 매개 변수를 사용하여 사용 가능한 데이터의 하위 집합으로 모델을 교육 및 테스트할 수 있습니다. 작은 샘플 조각을 사용하면 런타임과 메모리 요구 사항은 줄어들지만 학습된 모델의 정확도는 떨어집니다. 이 항목과 다른 매개 변수에 대한 자세한 내용을 보려면 `python Code\02_Modeling\run_mmlspark.py -h`를 실행하세요.

다양한 입력 매개 변수를 사용하여 이 스크립트를 여러 차례 실행하는 것이 좋습니다. 그런 다음 결과 모델의 성능을 Azure Machine Learning Workbench 실행 기록 기능에서 비교할 수 있습니다.

### <a name="comparing-model-performance-using-the-workbench-run-history-feature"></a>워크벤치 실행 기록 기능을 사용한 모델 성능 비교

유형의 학습 실행을 두 차례 이상 실행한 후에는 왼쪽 메뉴 표시줄에 있는 시계 아이콘을 클릭하여 워크벤치의 실행 기록 기능으로 이동할 수 있습니다. 왼쪽의 스크립트 목록에서 `run_mmlspark.py`를 선택합니다. 창에 모든 실행에 대한 테스트 집합 정확도 비교가 로드됩니다. 자세한 내용을 보려면 아래로 스크롤하여 개별 실행의 이름을 클릭합니다.

## <a name="deployment"></a>배포

HDInsight에서 원격 실행을 사용하여 학습된 모델 중 하나를 Middlesex County, MA 타일의 항공 이미지에 적용하려면 원하는 모델의 이름을 다음 명령에 삽입하고 실행합니다.

```
az ml experiment submit -c myhdi Code\03_Deployment\batch_score_spark.py --config_filename Code/settings.cfg --output_model_name [trained model name chosen earlier]
```

추가 `--sample_frac` 매개 변수를 사용하여 사용 가능한 데이터의 하위 집합으로 모델을 연산화할 수 있습니다. 작은 샘플 조각을 사용하면 런타임과 메모리 요구 사항은 줄어들지만 예측 완성도는 떨어집니다. 이 항목과 다른 매개 변수에 대한 자세한 내용을 보려면 `python Code\03_Deployment\batch_score_spark -h`를 실행하세요.

이 스크립트는 저장소 계정에 모델의 예측을 기록합니다. 다음 섹션에 설명된 대로 예측을 조사할 수 있습니다.

## <a name="visualization"></a>시각화

워크벤치 프로젝트의 "Code\04_Result_Analysis" 하위 폴더에 있는 "Model prediction analysis" Jupyter 노트북은 모델 예측을 시각화합니다. 다음과 같이 노트북을 로드하여 실행합니다.
1. 워크벤치에서 프로젝트를 열고 왼쪽 메뉴의 폴더("Files") 아이콘을 클릭하여 디렉터리 목록을 로드합니다.
2. "Code\04_Result_Analysis" 하위 폴더로 이동하고 이름이 "Model prediction analysis"인 노트북을 클릭합니다. 노트북의 미리 보기 렌더링이 표시됩니다.
3. "Start Notebook Server"를 클릭하여 노트북을 로드합니다.
4. 첫 번째 셀에서 표시된 위치에 결과를 분석하고자 하는 모델의 이름을 입력합니다.
5. "셀 -> 모두 실행"을 클릭하여 노트북의 모든 셀을 실행합니다.
6. 노트북을 따라 읽어가며 제시된 분석과 시각화에 대한 자세한 내용을 확인합니다.

## <a name="cleanup"></a>정리
이 예제를 완료한 후에는 Azure 명령줄 인터페이스에서 다음 명령을 실행하여, 만든 모든 리소스를 삭제하는 것이 좋습니다.

  ```
  az group delete --name %AZURE_RESOURCE_GROUP%
  ```

## <a name="references"></a>참조

- [상세 병렬 이미지 분류 리포지토리](https://github.com/Azure/Embarrassingly-Parallel-Image-Classification)
   - 무료로 제공되는 이미지 및 레이블을 통해 데이터 집합 구성 설명
- [MMLSpark](https://github.com/Azure/mmlspark) GitHub 리포지토리
   - MMLSpark를 통한 모델 교육 및 평가의 추가적인 예제 제공

## <a name="conclusions"></a>결론

Azure Machine Learning Workbench를 통해 데이터 과학자들은 원격 컴퓨터 대상에 코드를 간편하게 배포할 수 있습니다. 이 예제에서는 HDInsight 클러스터에서 원격 실행을 위해 로컬 코드가 배포되었습니다. Azure Machine Learning Workbench의 실행 기록 기능은 여러 모델의 성능을 추적하고 가장 정확한 모델을 밝히는 데 도움이 되었습니다. 워크벤치의 Jupyter 노트북 기능은 대화형 그래픽 환경에서 모델의 예측을 시각화하는 데 도움이 되었습니다.

## <a name="next-steps"></a>다음 단계
이 예제에 대해 자세히 알아보려면
- Azure Machine Learning Workbench의 실행 기록 기능에서 기어 기호를 클릭하여 표시할 그래프와 메트릭을 선택합니다.
- `run_logger`를 호출하는 문에 대한 예제 스크립트를 살펴봅니다. 각각의 메트릭이 어떻게 기록되는지 확인합니다.
- `blob_service`를 호출하는 문에 대한 예제 스크립트를 살펴봅니다. 학습된 모델과 예측이 클라우드에서 어떻게 저장 및 검색되는지 확인합니다.
- BLOB Storage 계정에서 만든 컨테이너의 내용을 탐색합니다. 어떤 스크립트나 명령이 각 파일 그룹의 생성을 담당하는지 확인합니다.
- 학습 스크립트를 수정하여 다른 MMLSpark 모델 형식을 교육하거나 모델 하이퍼 매개 변수를 변경합니다. 실행 기록 기능을 사용하여 변경에 따라 모델의 정확도가 높아졌는지 또는 낮아졌는지 판단합니다.
