---
title: "큰 데이터 파일을 읽고 쓰는 방법 | Microsoft Docs"
description: "Azure ML 실험에서 큰 파일을 읽고 쓰는 방법을 알아봅니다."
services: machine-learning
author: hning86
ms.author: haining
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 09/10/2017
ms.openlocfilehash: fb3158ef786ad73440a59c07b38476a98ced0768
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="persisting-changes-and-dealing-with-large-files"></a>변경 내용을 유지하고 큰 파일 처리

## <a name="execution-isolation-portability-and-reproducibility"></a>실행 격리, 이식성 및 재현성
Azure ML 실험 서비스를 사용하면 로컬 컴퓨터 또는 로컬 컴퓨터의 Docker 컨테이너 같은 로컬 실행 대상과 원격 컴퓨터의 Docker 컨테이너 또는 HDInsight 클러스터 같은 원격 실행 대상을 포함하여 여러 실행 대상을 구성할 수 있습니다. 자세한 내용은 [실험 실행 구성](experiment-execution-configuration.md) 문서를 참조하세요. 실행이 발생할 수 있도록 프로젝트 폴더가 해당 계산 대상으로 복사되어야 합니다. 로컬 임시 폴더가 이 목적에 사용되는 로컬 실행의 경우에도 마찬가지입니다. 

이 디자인의 목적은 실행 격리, 재현성 및 이식성을 보장하는 것입니다. 동일한 계산 대상 또는 다른 계산 대상에서 동일한 스크립트를 두 번 실행할 때 동일한 결과가 보장됩니다. 첫 번째 실행에서 변경한 내용이 두 번째 실행에 영향을 미치지 않아야 합니다. 이 디자인에서는 계산 대상이 완료된 후 실행된 작업에 대한 선호도 없이 계산 대상을 상태 비저장 계산 리소스로 처리할 수 있습니다.

## <a name="challenges"></a>과제
이 디자인은 이식성과 재현성이라는 이점을 제공하지만 몇 가지 고유의 과제가 있습니다.
### <a name="persisting-state-changes"></a>상태 변경 내용 유지
스크립트에서 계산 컨텍스트의 상태를 수정하더라도 변경 내용이 다음 실행 동안 유지되지 않을 뿐 아니라 자동으로 클라이언트 컴퓨터에 다시 전파되지도 않습니다. 

좀 더 구체적으로 말하자면, 스크립트에서 하위 폴더를 만들거나 파일을 작성하더라도 사용자는 프로젝트 디렉터리 사후 실행에서 해당 폴더 또는 파일을 찾을 수 없습니다. 이 하위 폴더 또는 파일은 어디서 발생하더라도 계산 대상 환경의 임시 폴더에 남아 있습니다. 이 하위 폴더 또는 파일을 디버깅 용도로 사용할 수는 있겠지만 그 존재에 대한 종속성을 만들 수는 없습니다.

### <a name="dealing-with-large-files-in-project-folder"></a>프로젝트 폴더의 큰 파일 처리

프로젝트 폴더에 큰 파일이 들어 있는 경우 각 실행이 시작될 때마다 프로젝트 폴더가 대상 계산 환경에 복사될 때 대기 시간이 발생합니다. 실행이 로컬에서 발생하더라도 이는 여전히 피해야 하는 불필요한 디스크 트래픽입니다. 이러한 이유로 현재는 최대 프로젝트 크기가 25MB로 제한되어 있습니다.

## <a name="option-1-use-the-outputs-folder"></a>옵션 1: 출력 폴더 사용
스크립트에서 파일을 생성하고 사용자는 이러한 파일이 실험 실행마다 변경될 것으로 예상하고 파일 기록을 유지하려는 경우에 적합한 옵션입니다. 일반적인 사용 사례로는 모델 교육, 데이터 집합 만들기 또는 모델 교육 실행의 일환으로 이미지 파일로 그래프 만들기를 들 수 있습니다. 사용자는 실행 간에 이러한 출력을 비교하고, 뒤로 돌아가서 이전 실행에서 생성된 출력 파일(예: 모델)을 선택하고, 후속 작업(예: 점수 매기기)에 사용할 수 있습니다.

기본적으로 사용자는 루트 디렉터리에 상대적인 `outputs`라는 폴더에 파일을 쓸 수 있습니다. 이 폴더는 실험 서비스에서 특별히 취급하는 특수 폴더입니다. 실행하는 동안 스크립트가 여기에 만드는 모델 파일, 데이터 파일, 그려진 이미지 파일 등의 모든 것은(통틀어 _아티팩트_라고 함) 실행이 완료된 후 실험 계정과 연결된 Azure Blob Storage 계정에 복사됩니다. 이러한 것들은 실행 기록 레코드의 일부가 됩니다.

다음은 `outputs` 폴더에 모델을 저장하는 방법에 대한 간단한 예제입니다.
```python
import os
import pickle

# m is a scikit-learn model. 
# we serialize it into a mode.plk file under the ./outputs folder.
with open(os.path.join('.', 'outputs', 'model.pkl'), 'wb') as f:    
    pickle.dump(m, f)
```
Azure ML Workbench에서 특정 실행의 실행 세부 정보 페이지 **출력 파일** 섹션으로 이동한 후 아티팩트를 선택하고 **다운로드** 단추를 클릭하여 _아티팩트_를 다운로드할 수 있습니다. CLI 창에서 `az ml asset download` 명령을 사용할 수도 있습니다.

보다 완전한 예제를 보려면 _아이리스 분류_ 샘플 프로젝트의 `iris_sklearn.py` Python 스크립트를 참조하세요.

## <a name="option-2-use-the-shared-folder"></a>옵션 2: 공유 폴더 사용
각 실행에 대한 파일 기록을 보관할 필요가 없는 경우 다음과 같은 시나리오에 독립 실행 간에 액세스할 수 있는 공유 폴더를 사용하면 매우 좋습니다. 
- 스크립트가 교육 또는 테스트 데이터로 csv 파일이나 텍스트 또는 이미지 파일의 디렉터리 같은 로컬 파일의 데이터를 로드해야 합니다. 
- 스크립트가 원시 데이터를 처리하고, 텍스트/이미지 파일의 기능화된 교육 데이터처럼 후속 교육 실행에 사용되는 중간 결과를 작성합니다. 
- 스크립트가 모델을 산출하면 후속 점수 매기기 스크립트가 모델을 선택하고 평가에 사용해야 합니다. 

공유 폴더는 사용자가 선택하는 동일한 계산 대상에 로컬로 상주합니다. 따라서 이 옵션은 이 공유 폴더를 참조하는 모든 스크립트 실행이 동일한 계산 대상에서 실행되고 계산 대상이 실행 간에 재활용되지 않는 경우에만 유효합니다.

공유 폴더 기능을 사용하면 환경 변수 `AZUREML_NATIVE_SHARE_DIRECTORY`에서 식별하는 특수 폴더에서 읽거나 쓸 수 있습니다. 

### <a name="example"></a>예제
다음은 이 공유 폴더를 사용하여 텍스트 파일을 읽고 쓰는 샘플 Python 코드입니다.
```python
import os

# write to the shared folder
with open(os.environ['AZUREML_NATIVE_SHARE_DIRECTORY'] + 'test.txt', 'wb') as f:
    f.write(“Hello World”)

# read from the shared folder
with open(os.environ['AZUREML_NATIVE_SHARE_DIRECTORY'] + 'test.txt', 'r') as f:
    text = file.read()
```

보다 완전한 예제를 보려면 _아이리스 분류_ 샘플 프로젝트의 `iris_sklearn_shared_folder.py` 파일을 참조하세요.

이 기능을 사용하려면 `aml_config` 폴더의 대상 실행 컨텍스트를 나타내는 `.compute` 파일에서 몇 가지 간단한 구성 작업을 수행해야 합니다. 이 폴더의 실제 경로는 사용자가 선택하는 계산 대상과 구성하는 값에 따라 달라질 수 있습니다.

### <a name="configure-local-compute-context"></a>로컬 계산 컨텍스트 구성

로컬 계산 컨텍스트에서 이 기능을 사용하려면 _로컬_ 환경(일반적으로 `local.compute`)을 나타내는 `.compute` 파일에 다음 줄을 추가합니다.
```
# local.runconfig
...
nativeSharedDirectory: ~/.azureml/share
...
```

`~/.azureml/share`는 기본 폴더 경로입니다. 스크립트 실행에서 액세스할 수 있는 로컬 절대 경로로 변경할 수 있습니다. 실험 계정 이름, 작업 영역 이름 및 프로젝트 이름이 기본 디렉터리에 자동으로 추가되어 공유 디렉터리의 전체 경로를 구성합니다. 예를 들어 앞의 기본값을 사용하는 경우 다음 경로에 파일을 쓰고 파일을 검색할 수 있습니다.

```
# on Windows
C:\users\<username>\.azureml\share\<exp_acct_name>\<workspace_name>\<proj_name>\

# on macOS
/Users/<username>/.azureml/share/<exp_acct_name>/<workspace_name>/<proj_name>/
```

### <a name="configure-docker-compute-context-local-or-remote"></a>Docker 계산 컨텍스트(로컬 또는 원격) 구성
Docker 계산 컨텍스트에서 이 기능을 사용하려면 로컬 또는 원격 Docker _.compute_ 파일에 다음 두 줄을 추가해야 합니다.

```
# docker.compute
...
sharedVolumes: true
nativeSharedDirectory: ~/.azureml/share
...
```
>[!IMPORTANT]
>`AZUREML_NATIVE_SHARE_DIRECTORY` 환경 변수를 사용하여 공유 폴더에 액세스하는 경우 `sharedVolume`을 `true`로 설정해야 합니다. 그렇지 않으면 실행이 실패합니다.

Docker 컨테이너에서 실행되는 코드는 이 공유 폴더가 항상 `/azureml-share/`로 표시됩니다. Docker 컨테이너에 표시되는 이 폴더 경로는 구성할 수 없습니다. 그리고 코드에서 이 폴더 이름에 종속성을 만들면 안 됩니다. 그 대신 항상 환경 변수 이름 `AZUREML_NATIVE_SHARE_DIRECTORY`를 사용하여 이 폴더의 주소를 표시합니다. 이 경로는 Docker 호스트 컴퓨터/계산 컨텍스트의 로컬 폴더에 매핑됩니다. 이 로컬 폴더의 기본 디렉터리는 `.compute` 파일에서 구성 가능한 `nativeSharedDirectory` 설정 값입니다. 위의 기본값을 사용 는 경우 호스트 컴퓨터의 공유 폴더 로컬 경로는 다음과 같습니다.
```
# Windows
C:\users\<username>\.azureml\share\<exp_acct_name>\<workspace_name>\<proj_name>\

# macOS
/Users/<username>/.azureml/share/<exp_acct_name>/<workspace_name>/<proj_name>/

# Ubuntu Linux
/home/<username>/.azureml/share/<exp_acct_name>/<workspace_name>/<proj_name>/
```

>[!TIP]
>로컬 디스크의 공유 폴더 경로는 로컬 계산 컨텍스트와 로컬 Docker 계산 컨텍스트 간에 동일합니다. 즉, 로컬 실행과 로컬 Docker 실행 간에도 파일을 공유할 수 있습니다.

이러한 폴더에 직접 입력 데이터를 배치할 수 있으며, 해당 컴퓨터의 로컬 또는 Docker 실행에서 이러한 폴더를 선택할 수 있을 것으로 예상할 수 있습니다. 또한 로컬 또는 Docker 실행에서 이 폴더에 파일을 쓸 수 있으며, 해당 폴더에서 파일이 지속되어 실행 주기 동안 유지될 것으로 예상할 수 있습니다.

Azure ML 실행 서비스의 구성 파일에 대한 자세한 내용은 [실행 구성 파일](experiment-execution-configuration-reference.md) 문서를 참조하세요.

>[!NOTE]
>`AZUREML_NATIVE_SHARE_DIRECTORY` 환경 변수는 HDInsight 계산 컨텍스트에서 지원되지 않습니다. 그러나 연결된 Blob 저장소에 파일을 쓰고 읽을 때 명시적으로 절대 WASB 경로를 사용하면 간단하게 동일한 결과를 얻을 수 있습니다.

## <a name="option-3-use-external-durable-storage"></a>옵션 3: 외부 영구 저장소 사용

물론 자유롭게 외부 영구 저장소를 사용하여 실행 중에 상태를 유지할 수 있습니다. 이 옵션은 다음과 같은 시나리오에 유용합니다.
- 입력 데이터가 이미 대상 계산 환경에서 액세스할 수 있는 영구 저장소에 저장되어 있습니다.
- 이러한 파일을 실행 기록 레코드에 포함할 필요가 없습니다.
- 여러 계산 환경에서 실행 간에 이러한 파일을 공유해야 합니다.
- 이러한 파일이 계산 컨텍스트 자체를 유지해야 합니다.

Python/PySpark 코드에서 Azure blob 저장소를 사용하는 것을 예로 들 수 있습니다.

다음은 Python 코드에서 Azure blob 저장소를 사용하는 간단한 예제입니다.
```python
from azure.storage.blob import BlockBlobService
import glob
import os

ACCOUNT_NAME = "<your blob storage account name>"
ACCOUNT_KEY = "<account key>"
CONTAINER_NAME = "<container name>"

blob_service = BlockBlobService(account_name=ACCOUNT_NAME, account_key=ACCOUNT_KEY)

## Create a new container if necessary, or use an existing one
my_service.create_container(CONTAINER_NAME, fail_on_exist=False, public_access=PublicAccess.Container)

# df is a pandas DataFrame
df.to_csv('mydata.csv', sep='\t', index=False)

# Export the mydata.csv file to blob storage.
for name in glob.iglob('mydata.csv'):
    blob_service.create_blob_from_path(CONTAINER_NAME, 'single_file.csv', name)
```

다음은 임의의 Azure Blob Storage를 HDI Spark 런타임에 연결하는 간단한 예제입니다.
```python
def attach_storage_container(spark, account, key):
    config = spark._sc._jsc.hadoopConfiguration()
    setting = "fs.azure.account.key." + account + ".blob.core.windows.net"
    if not config.get(setting):
        config.set(setting, key)
 
attach_storage_container(spark, "<storage account name>", "<storage key>”)
```

## <a name="conclusion"></a>결론
Azure ML은 전체 프로젝트 폴더를 대상 계산 컨텍스트로 복사하여 스크립트를 실행하므로 큰 입력, 출력 및 중간 파일에 특별히 주의해야 합니다. `AZUREML_NATIVE_SHARE_DIRECTORY` 환경 변수를 통해 액세스 가능한 공유 폴더인 특수 `outputs` 폴더 또는 큰 파일 트랜잭션을 위한 외부 영구 저장소를 사용할 수 있습니다. 

## <a name="next-steps"></a>다음 단계
- [실험 실행 구성](experiment-execution-configuration-reference.md)을 검토합니다.
- [아이리스 분류](tutorial-classifying-iris-part-1.md) 자습서 프로젝트에서 어떤 방식으로 `outputs` 폴더를 사용하여 학습된 모델을 유지하는지 살펴봅니다.