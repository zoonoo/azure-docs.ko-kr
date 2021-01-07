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
ms.openlocfilehash: b452c24b4b2ed6021910f267b9941f3829acccd8
ms.sourcegitcommit: a89a517622a3886b3a44ed42839d41a301c786e0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/22/2020
ms.locfileid: "97733335"
---
# <a name="troubleshoot-environment-image-builds"></a>환경 이미지 빌드 문제 해결
Docker 환경 이미지 빌드 및 패키지 설치와 관련 된 문제를 해결 하는 방법에 대해 알아봅니다.

## <a name="prerequisites"></a>필수 구성 요소

* **Azure 구독**. [Azure Machine Learning 평가판 또는 유료 버전](https://aka.ms/AMLFree)을 사용해 보세요.
* [Azure Machine Learning SDK](/python/api/overview/azure/ml/install?preserve-view=true&view=azure-ml-py)
* [Azure CLI](/cli/azure/install-azure-cli?preserve-view=true&view=azure-cli-latest)
* [Azure Machine Learning용 CLI 확장](reference-azure-machine-learning-cli.md)
* 로컬로 디버그하려면 로컬 시스템에서 작동하는 Docker가 설치되어 있어야 합니다.

## <a name="docker-image-build-failures"></a>Docker 이미지 빌드 오류
 
대부분의 이미지 빌드 오류에 대해 이미지 빌드 로그에서 근본 원인을 찾을 수 있습니다.
이미지 빌드 로그는 Azure Machine Learning 포털 (20 \_ 이미지 \_ 빌드 \_log.txt) 또는 ACR 작업 실행 로그에서 찾을 수 있습니다.
 
대부분의 경우 오류를 로컬에서 재현 하는 것이 더 쉽습니다. 오류 종류를 확인 하 고 다음 중 하나를 수행 합니다 `setuptools` .

- 로컬에서 conda 종속성 설치 `conda install suspicious-dependency==X.Y.Z`
- 로컬 pip 종속성 설치 `pip install suspicious-dependency==X.Y.Z`
- 전체 환경을 구체화 해 보세요. `conda create -f conda-specification.yml`

> [!IMPORTANT]
> 로컬 계산의 플랫폼과 인터프리터가 원격의 플랫폼과 일치 하는지 확인 합니다. 

### <a name="timeout"></a>제한 시간 
 
여러 네트워크 문제에 대 한 시간 제한 문제가 발생할 수 있습니다.
- 낮은 인터넷 대역폭
- 서버 문제
- 지정 된 conda 또는 pip 시간 제한 설정으로 다운로드할 수 없는 높은 종속성
 
아래와 유사한 메시지는 문제를 표시 합니다.
 
```
('Connection broken: OSError("(104, \'ECONNRESET\')")', OSError("(104, 'ECONNRESET')"))
```
```
ReadTimeoutError("HTTPSConnectionPool(host='****', port=443): Read timed out. (read timeout=15)",)
```

가능한 해결 방법:
 
- 미러, blob 저장소 또는 기타 python 피드와 같은 사용 가능한 경우 종속성에 대해 다른 원본을 사용해 보세요.
- Conda 또는 pip를 업데이트 합니다. 사용자 지정 docker 파일을 사용 하는 경우 제한 시간 설정을 업데이트 합니다.
- 일부 pip 버전에는 알려진 문제가 있습니다. 환경 종속성에 특정 버전의 pip를 추가 하는 것이 좋습니다.

### <a name="package-not-found"></a>패키지를 찾을 수 없음

이는 이미지 빌드 실패의 가장 일반적인 경우입니다.

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

- 잘못 된 pip 종속성
        ```
        ERROR: No matching distribution found for bad-backage==0.0 (from good-package==1.0)
        ```

지정 된 원본에 패키지가 있는지 확인 합니다. Pip [검색](https://pip.pypa.io/en/stable/reference/pip_search/) 을 사용 하 여 pip 종속성을 확인 합니다.

`pip search azureml-core`

Conda 종속성의 경우 [conda search](https://docs.conda.io/projects/conda/en/latest/commands/search.html)를 사용 합니다.

`conda search conda-forge::numpy`

추가 옵션:
- `pip search -h`
- `conda search -h`

#### <a name="installer-notes"></a>설치 관리자 정보

지정 된 플랫폼과 Python 인터프리터 버전에 필요한 배포가 있는지 확인 합니다.

Pip 종속성의 경우으로 이동 `https://pypi.org/project/[PROJECT NAME]/[VERSION]/#files` 하 여 필요한 버전을 사용할 수 있는지 확인 합니다. 예를 들어 https://pypi.org/project/azureml-core/1.11.0/#files

Conda 종속성의 경우 채널 리포지토리에서 패키지를 확인 합니다.
Anaconda, i n c .에서 유지 관리 되는 채널의 경우 [여기](https://repo.anaconda.com/pkgs/)를 확인 하세요.

### <a name="pip-package-update"></a>Pip 패키지 업데이트

Pip 패키지를 설치 하거나 업데이트 하는 동안 확인자는 새 요구 사항을 충족 하기 위해 이미 설치 된 패키지를 업데이트 해야 할 수 있습니다.
Pip 버전 또는 종속성 설치 방법과 관련 된 다양 한 이유로 제거가 실패할 수 있습니다.
가장 일반적인 시나리오는 conda에서 설치한 종속성을 pip에서 제거할 수 없는 것입니다.
이 시나리오의 경우를 사용 하 여 종속성을 제거 하는 것이 좋습니다 `conda remove mypackage` .

```
  Attempting uninstall: mypackage
    Found existing installation: mypackage X.Y.Z
ERROR: Cannot uninstall 'mypackage'. It is a distutils installed project and thus we cannot accurately determine which files belong to it which would lead to only a partial uninstall.
``` 
### <a name="installer-issues"></a>설치 관리자 문제

특정 설치 관리자 버전에는 빌드 실패로 이어질 수 있는 패키지 해결 프로그램에 문제가 있습니다.

사용자 지정 기본 이미지 또는 dockerfile을 사용 하는 경우 conda version 4.5.4 이상을 사용 하는 것이 좋습니다.

Pip 패키지는 pip 종속성을 설치 하는 데 필요 하며 환경에 버전을 지정 하지 않으면 최신 버전이 사용 됩니다.
최신 버전의 도구에 의해 발생할 수 있는 일시적인 문제 또는 주요 변경 내용을 방지 하려면 알려진 pip 버전을 사용 하는 것이 좋습니다.

아래 메시지가 표시 되 면 환경에서 pip 버전을 고정 하는 것이 좋습니다.

`Warning: you have pip-installed dependencies in your environment file, but you do not list pip itself as one of your conda dependencies. Conda may not use the correct pip to install your packages, and they may end up in the wrong place. Please add an explicit pip dependency. I'm adding one for you, but still nagging you.`

`Pip subprocess error:
ERROR: THESE PACKAGES DO NOT MATCH THE HASHES FROM THE REQUIREMENTS FILE. If you have updated the package versions, update the hashes as well. Otherwise, examine the package contents carefully; someone may have tampered with them.`

또한 종속성에 확인할 수 없는 충돌이 있는 경우 pip 설치가 무한 루프에서 중단 될 수 있습니다. 로컬로 작업 하는 경우 pip 버전을 < 20.3으로 다운 그레이드 합니다. YAML 파일에서 만든 conda 환경에서이 문제는 conda-대장간이 가장 높은 우선 순위의 채널 인 경우에만 표시 됩니다. 이 문제를 완화 하려면 conda 사양 파일에서 pip < 20.3 (! = 20.3 또는 = 20.2.4 pin을 다른 버전으로 고정)을 conda 종속성으로 명시적으로 지정 합니다.

## <a name="service-side-failures"></a>서비스 쪽 오류

### <a name="unable-to-pull-image-from-mcraddress-could-not-be-resolved-for-container-registry"></a>Container Registry에 대해 MCR/Address에서 이미지를 끌어올 수 없습니다.
가능한 문제:
- 컨테이너 레지스트리의 경로 이름이 제대로 확인 되지 않을 수 있습니다. 이미지 이름에 이중 슬래시를 사용 하 고 Linux 및 Windows 호스트에서 슬래시 방향이 올바른지 확인 합니다.
- Vnet 뒤의 ACR이 [지원 되지 않는 지역](https://docs.microsoft.com/azure/private-link/private-link-overview#availability)에서 개인 끝점을 사용 하는 경우 포털에서 서비스 엔드포인트 (공용 액세스)를 사용 하 여 vnet 뒤에 acr을 구성 하 고 다시 시도 합니다.
- VNet 뒤에 ACR을 넣은 후 [ARM 템플릿이](https://docs.microsoft.com/azure/machine-learning/how-to-enable-virtual-network#azure-container-registry) 실행 되는지 확인 합니다. 이렇게 하면 작업 영역에서 ACR 인스턴스와 통신할 수 있습니다.

### <a name="401-error-from-workspace-acr"></a>401 작업 영역 ACR에서 오류 발생
[Ws.sync_keys ()를](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py#sync-keys--) 사용 하 여 저장소 키 다시 동기화

### <a name="environment-keeps-throwing-waiting-for-other-conda-operations-to-finish-error"></a>환경에서 "다른 Conda 작업이 완료 될 때까지 기다리는 중 ..."가 계속 throw 됩니다. 메시지가
이미지 빌드가 진행 중일 때 SDK 클라이언트에서 conda을 잠 궜 습니다. 프로세스가 충돌 하거나 사용자가 잘못 취소 한 경우에는 conda가 잠긴 상태로 유지 됩니다. 이 문제를 해결 하려면 잠금 파일을 수동으로 삭제 합니다. 

### <a name="custom-docker-image-not-in-registry"></a>레지스트리에 없는 사용자 지정 docker 이미지
[올바른 태그](https://docs.microsoft.com/azure/machine-learning/how-to-use-environments#create-an-environment) 를 사용 하 고 있는지 확인 `user_managed_dependencies = True` 합니다. `Environment.python.user_managed_dependencies = True` Conda를 사용 하지 않도록 설정 하 고 사용자의 설치 된 패키지를 사용 합니다.

### <a name="common-vnet-issues"></a>일반적인 VNet 문제

1. 저장소 계정, 계산 클러스터 및 Azure Container Registry 모두 가상 네트워크의 동일한 서브넷에 있는지 확인 합니다.
2. ACR이 VNet 뒤에 있는 경우 이미지를 빌드하는 데 직접 사용할 수 없습니다. 계산 클러스터는 이미지를 작성 하는 데 사용 해야 합니다.
3. 저장소는 다음과 같은 경우 VNet 뒤에 배치 해야 할 수 있습니다.
    - 추론 또는 private wheel 사용
    - 403 (권한 없음) 서비스 오류 표시
    - ACR/MCR에서 이미지 세부 정보를 가져올 수 없습니다.

### <a name="image-build-fails-when-trying-to-access-network-protected-storage"></a>네트워크로 보호 된 저장소에 액세스 하려고 할 때 이미지 빌드가 실패 함
- ACR 작업은 VNet 뒤에서 작동 하지 않습니다. 사용자에 게 VNet 뒤의 ACR가 있는 경우 계산 클러스터를 사용 하 여 이미지를 만들어야 합니다.
- 저장소에서 종속성을 끌어올 수 있으려면 저장소는 VNet 뒤에 있어야 합니다. 

### <a name="cannot-run-experiments-when-storage-has-network-security-enabled"></a>저장소에서 네트워크 보안을 사용 하는 경우 실험을 실행할 수 없습니다.
기본 Docker 이미지를 사용 하 고 사용자 관리 종속성을 사용 하도록 설정 하는 경우 MicrosoftContainerRegistry 및 AzureFrontDoor [서비스 태그](https://docs.microsoft.com/azure/machine-learning/how-to-enable-virtual-network) 를 사용 하 여 mcr 및 해당 종속성을 allowlist 합니다.

 자세한 내용은 [VNET 사용](https://docs.microsoft.com/azure/machine-learning/how-to-enable-virtual-network#azure-container-registry) 을 참조 하세요.

### <a name="creating-an-icm"></a>ICM 만들기

Metastore에 ICM을 만들거나 할당할 때 문제를 더 잘 이해할 수 있도록 CSS 지원 티켓을 포함 하세요.

## <a name="next-steps"></a>다음 단계

- [기계 학습 모델을 학습 하 여 꽃 범주화](how-to-train-scikit-learn.md)
- [사용자 지정 Docker 이미지를 사용 하 여 기계 학습 모델 학습](how-to-train-with-custom-image.md)