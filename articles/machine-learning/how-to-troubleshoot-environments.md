---
title: 환경 이미지 문제 해결
titleSuffix: Azure Machine Learning
description: 환경 이미지 빌드 및 패키지 설치와 관련 된 문제를 해결 하는 방법에 대해 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
author: saachigopal
ms.author: sagopal
ms.date: 12/3/2020
ms.topic: troubleshooting
ms.custom: devx-track-python
ms.openlocfilehash: 71061c056b499f79727f70fb855db7a81a65f3bd
ms.sourcegitcommit: 65cef6e5d7c2827cf1194451c8f26a3458bc310a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/19/2021
ms.locfileid: "98572173"
---
# <a name="troubleshoot-environment-image-builds"></a>환경 이미지 빌드 문제 해결

Docker 환경 이미지 빌드 및 패키지 설치와 관련 된 문제를 해결 하는 방법에 대해 알아봅니다.

## <a name="prerequisites"></a>사전 요구 사항

* Azure 구독 [Azure Machine Learning 평가판 또는 유료 버전](https://aka.ms/AMLFree)을 사용해 보세요.
* [Azure Machine Learning SDK](/python/api/overview/azure/ml/install?preserve-view=true&view=azure-ml-py)
* [Azure CLI](/cli/azure/install-azure-cli?preserve-view=true&view=azure-cli-latest)
* [Azure Machine Learning용 CLI 확장](reference-azure-machine-learning-cli.md)
* 로컬로 디버그하려면 로컬 시스템에서 작동하는 Docker가 설치되어 있어야 합니다.

## <a name="docker-image-build-failures"></a>Docker 이미지 빌드 오류
 
대부분의 이미지 빌드 오류에 대해 이미지 빌드 로그에서 근본 원인을 찾을 수 있습니다.
Azure Machine Learning 포털 (20 \_ 이미지 \_ 빌드 \_log.txt) 또는 Azure Container Registry 태스크 실행 로그에서 이미지 빌드 로그를 찾습니다.
 
일반적으로 오류를 로컬에서 재현 하는 것이 더 쉽습니다. 오류 종류를 확인 하 고 다음 중 하나를 수행 합니다 `setuptools` .

- 로컬에서 conda 종속성을 설치 `conda install suspicious-dependency==X.Y.Z` 합니다.
- Pip 종속성을 로컬로 설치 `pip install suspicious-dependency==X.Y.Z` 합니다.
- 전체 환경을 구체화 해 `conda create -f conda-specification.yml` 보세요.

> [!IMPORTANT]
> 로컬 계산 클러스터의 플랫폼과 인터프리터가 원격 계산 클러스터의 플랫폼과 일치 하는지 확인 합니다. 

### <a name="timeout"></a>제한 시간 
 
다음 네트워크 문제로 인해 시간 초과 오류가 발생할 수 있습니다.

- 낮은 인터넷 대역폭
- 서버 문제
- 지정 된 conda 또는 pip 시간 제한 설정으로 다운로드할 수 없는 많은 종속성
 
다음 예와 유사한 메시지는 문제를 표시 합니다.
 
```
('Connection broken: OSError("(104, \'ECONNRESET\')")', OSError("(104, 'ECONNRESET')"))
```
```
ReadTimeoutError("HTTPSConnectionPool(host='****', port=443): Read timed out. (read timeout=15)",)
```

오류 메시지가 표시 되는 경우 다음과 같은 해결 방법 중 하나를 시도해 보세요.
 
- 종속성에 대해 미러, Azure Blob Storage 또는 다른 Python 피드와 같은 다른 원본을 사용해 보세요.
- Conda 또는 pip를 업데이트 합니다. 사용자 지정 Docker 파일을 사용 하는 경우 제한 시간 설정을 업데이트 합니다.
- 일부 pip 버전에는 알려진 문제가 있습니다. 환경 종속성에 특정 버전의 pip를 추가 하는 것이 좋습니다.

### <a name="package-not-found"></a>패키지를 찾을 수 없음

다음 오류는 이미지 빌드 오류에 가장 일반적입니다.

- Conda 패키지를 찾을 수 없습니다.

   ```
   ResolvePackageNotFound: 
   - not-existing-conda-package
   ```

- 지정 된 pip 패키지 또는 버전을 찾을 수 없습니다.

   ```
   ERROR: Could not find a version that satisfies the requirement invalid-pip-package (from versions: none)
   ERROR: No matching distribution found for invalid-pip-package
   ```

- 잘못 된 pip 종속성:

   ```
   ERROR: No matching distribution found for bad-package==0.0 (from good-package==1.0)
   ```

지정 된 원본에 패키지가 있는지 확인 합니다. [Pip 검색](https://pip.pypa.io/en/stable/reference/pip_search/) 을 사용 하 여 pip 종속성을 확인 합니다.

- `pip search azureml-core`

Conda 종속성의 경우 [conda search](https://docs.conda.io/projects/conda/en/latest/commands/search.html)를 사용 합니다.

- `conda search conda-forge::numpy`

추가 옵션을 보려면 다음을 수행 하십시오.
- `pip search -h`
- `conda search -h`

#### <a name="installer-notes"></a>설치 관리자 정보

지정 된 플랫폼과 Python 인터프리터 버전에 필요한 배포가 있는지 확인 합니다.

Pip 종속성의 경우으로 이동 `https://pypi.org/project/[PROJECT NAME]/[VERSION]/#files` 하 여 필요한 버전을 사용할 수 있는지 확인 합니다. 예제를 https://pypi.org/project/azureml-core/1.11.0/#files 보려면로 이동 합니다.

Conda 종속성의 경우 채널 리포지토리에서 패키지를 확인 합니다.
Anaconda, i n c .에서 유지 관리 되는 채널의 경우 [Anaconda 패키지 페이지](https://repo.anaconda.com/pkgs/)를 확인 합니다.

### <a name="pip-package-update"></a>Pip 패키지 업데이트

Pip 패키지를 설치 하거나 업데이트 하는 동안 해결 프로그램은 새 요구 사항을 충족 하기 위해 이미 설치 된 패키지를 업데이트 해야 할 수 있습니다.
Pip 버전 또는 종속성이 설치 된 방법과 관련 된 다양 한 이유로 제거가 실패할 수 있습니다.
가장 일반적인 시나리오는 conda에서 설치한 종속성을 pip에서 제거할 수 없는 것입니다.
이 시나리오의 경우를 사용 하 여 종속성을 제거 하는 것이 좋습니다 `conda remove mypackage` .

```
  Attempting uninstall: mypackage
    Found existing installation: mypackage X.Y.Z
ERROR: Cannot uninstall 'mypackage'. It is a distutils installed project and thus we cannot accurately determine which files belong to it which would lead to only a partial uninstall.
``` 
### <a name="installer-issues"></a>설치 관리자 문제

특정 설치 관리자 버전에는 빌드 실패로 이어질 수 있는 패키지 해결 프로그램에 문제가 있습니다.

사용자 지정 기본 이미지 또는 Dockerfile을 사용 하는 경우 conda 버전 4.5.4 이상을 사용 하는 것이 좋습니다.

Pip 종속성을 설치 하려면 pip 패키지가 필요 합니다. 환경에서 버전을 지정 하지 않으면 최신 버전이 사용 됩니다.
알려진 버전의 pip를 사용 하 여 일시적인 문제 또는 최신 버전의 도구에서 발생 하는 주요 변경 사항을 방지 하는 것이 좋습니다.

다음 메시지가 표시 되는 경우 사용자 환경에서 pip 버전을 고정 하는 것이 좋습니다.

   ```
   Warning: you have pip-installed dependencies in your environment file, but you do not list pip itself as one of your conda dependencies. Conda may not use the correct pip to install your packages, and they may end up in the wrong place. Please add an explicit pip dependency. I'm adding one for you, but still nagging you.
   ```

Pip 하위 프로세스 오류:
   ```
   ERROR: THESE PACKAGES DO NOT MATCH THE HASHES FROM THE REQUIREMENTS FILE. If you have updated the package versions, update the hashes as well. Otherwise, examine the package contents carefully; someone may have tampered with them.
   ```

종속성에 확인할 수 없는 충돌이 있으면 Pip 설치가 무한 루프에서 중단 될 수 있습니다. 로컬에서 작업 하는 경우 pip 버전을 < 20.3으로 다운 그레이드 합니다. YAML 파일에서 만든 conda 환경에서는 conda-대장간이 가장 높은 우선 순위의 채널 인 경우에만이 문제가 발생 합니다. 이 문제를 완화 하려면 conda 사양 파일에서 pip < 20.3 (! = 20.3 또는 = 20.2.4 pin을 다른 버전으로 고정)을 conda 종속성으로 명시적으로 지정 합니다.

## <a name="service-side-failures"></a>서비스 쪽 오류

가능한 서비스 측 오류 문제를 해결 하려면 다음 시나리오를 참조 하십시오.

### <a name="youre-unable-to-pull-an-image-from-a-container-registry-or-the-address-couldnt-be-resolved-for-a-container-registry"></a>컨테이너 레지스트리에서 이미지를 끌어오거나 컨테이너 레지스트리에 대 한 주소를 확인할 수 없습니다.

가능한 문제:
- 컨테이너 레지스트리의 경로 이름이 올바르게 확인 되지 않을 수 있습니다. 이미지 이름이 이중 슬래시를 사용 하 고 Linux 및 Windows 호스트에 대 한 슬래시 방향이 올바른지 확인 합니다.
- 가상 네트워크 뒤의 컨테이너 레지스트리가 [지원 되지 않는 지역](https://docs.microsoft.com/azure/private-link/private-link-overview#availability)에서 개인 끝점을 사용 하는 경우 포털에서 서비스 엔드포인트 (공용 액세스)를 사용 하 여 컨테이너 레지스트리를 구성 하 고 다시 시도 하세요.
- 컨테이너 레지스트리를 가상 네트워크 뒤에 배치한 후에는 작업 영역이 container registry 인스턴스와 통신할 수 있도록 [Azure Resource Manager 템플릿을](https://docs.microsoft.com/azure/machine-learning/how-to-enable-virtual-network#azure-container-registry) 실행 합니다.

### <a name="you-get-a-401-error-from-a-workspace-container-registry"></a>작업 영역 컨테이너 레지스트리에서 401 오류가 발생 합니다.

[Ws.sync_keys ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py#sync-keys--)를 사용 하 여 저장소 키를 다시 동기화 합니다.

### <a name="the-environment-keeps-throwing-a-waiting-for-other-conda-operations-to-finish-error"></a>환경에서 "다른 conda 작업이 완료 될 때까지 기다리는 중 ..."가 계속 throw 됩니다. 메시지가

이미지 빌드가 진행 중일 때 SDK 클라이언트에서 conda을 잠 궜 습니다. 프로세스가 충돌 하거나 사용자에 의해 잘못 취소 된 경우 conda은 잠긴 상태로 유지 됩니다. 이 문제를 해결 하려면 잠금 파일을 수동으로 삭제 합니다. 

### <a name="your-custom-docker-image-isnt-in-the-registry"></a>사용자 지정 Docker 이미지가 레지스트리에 없습니다.

[올바른 태그](https://docs.microsoft.com/azure/machine-learning/how-to-use-environments#create-an-environment) 를 사용 하 고 있는지 확인 `user_managed_dependencies = True` 합니다. `Environment.python.user_managed_dependencies = True` conda를 사용 하지 않도록 설정 하 고 사용자의 설치 된 패키지를 사용 합니다.

### <a name="you-get-one-of-the-following-common-virtual-network-issues"></a>다음과 같은 일반적인 가상 네트워크 문제 중 하나를 얻을 수 있습니다.

- 저장소 계정, 계산 클러스터 및 컨테이너 레지스트리가 모두 가상 네트워크의 동일한 서브넷에 있는지 확인 합니다.
- 컨테이너 레지스트리가 가상 네트워크 뒤에 있는 경우 이미지를 빌드하는 데 직접 사용할 수 없습니다. 이미지를 빌드하려면 계산 클러스터를 사용 해야 합니다.
- 다음을 수행 하는 경우 저장소를 가상 네트워크 뒤에 배치 해야 할 수 있습니다.
    - 추론 또는 private wheel을 사용 합니다.
    - 403 (권한 없음) 서비스 오류를 참조 하세요.
    - Azure Container Registry에서 이미지 세부 정보를 가져올 수 없습니다.

### <a name="the-image-build-fails-when-youre-trying-to-access-network-protected-storage"></a>네트워크로 보호 된 저장소에 액세스 하려고 할 때 이미지 빌드가 실패 합니다.

- Azure Container Registry 작업은 가상 네트워크 뒤에서 작동 하지 않습니다. 사용자에 게 가상 네트워크 뒤의 컨테이너 레지스트리가 있는 경우 계산 클러스터를 사용 하 여 이미지를 만들어야 합니다.
- 저장소는 종속성을 가져오기 위해 가상 네트워크 뒤에 있어야 합니다.

### <a name="you-cant-run-experiments-when-storage-has-network-security-enabled"></a>저장소에서 네트워크 보안을 사용 하는 경우 실험을 실행할 수 없습니다.

기본 Docker 이미지를 사용 하 고 사용자 관리 종속성을 사용 하도록 설정 하는 경우 MicrosoftContainerRegistry 및 AzureFrontDoor [서비스 태그](https://docs.microsoft.com/azure/machine-learning/how-to-enable-virtual-network) 를 사용 하 여 Azure Container Registry 및 해당 종속성을 allowlist 합니다.

 자세한 내용은 [가상 네트워크 사용](https://docs.microsoft.com/azure/machine-learning/how-to-enable-virtual-network#azure-container-registry)을 참조 하세요.

### <a name="you-need-to-create-an-icm"></a>ICM을 만들어야 합니다.

Metastore에 ICM을 만들고 할당 하는 경우 문제를 더 잘 이해할 수 있도록 CSS 지원 티켓을 포함 합니다.

## <a name="next-steps"></a>다음 단계

- [기계 학습 모델을 학습 하 여 꽃 범주화](how-to-train-scikit-learn.md)
- [사용자 지정 Docker 이미지를 사용 하 여 기계 학습 모델 학습](how-to-train-with-custom-image.md)