---
title: 계산 클러스터 및 인스턴스에 대 한 호스트 OS 업그레이드
titleSuffix: Azure Machine Learning
description: 계산 클러스터 및 계산 인스턴스의 호스트 OS를 Ubuntu 16.04 LTS에서 18.04 LTS로 업그레이드 합니다.
services: machine-learning
author: nishankgu
ms.author: nigup
ms.reviewer: larryfr
ms.service: machine-learning
ms.subservice: core
ms.date: 03/03/2021
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: 5f853c19015a70d596c32532a9c280c785b4597b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104609548"
---
# <a name="upgrade-compute-instance-and-compute-cluster-host-os"></a>계산 인스턴스 및 계산 클러스터 호스트 OS를 업그레이드 합니다.

Azure Machine Learning __계산 클러스터__ 및 __계산 인스턴스__ 는 관리 되는 계산 인프라입니다. 관리 서비스인 Microsoft는 설치 된 호스트 OS와 패키지 및 소프트웨어 버전을 관리 합니다.

계산 클러스터 및 계산 인스턴스에 대 한 호스트 OS는 Ubuntu 16.04 LTS입니다. **2021 년 4 월 30** 일부 터 Ubuntu는 16.04에 대 한 지원을 종료 합니다. __2021 년 3 월 15__ 일부 터 Microsoft에서 자동으로 호스트 OS를 Ubuntu 18.04 lts로 업데이트 합니다. 18.04로 업데이트 하면 Ubuntu 커뮤니티에서 지속적인 보안 업데이트 및 지원이 보장 됩니다. Ubuntu에서 16.04에 대 한 지원을 종료 하는 방법에 대 한 자세한 내용은 [ubuntu 릴리스 블로그](https://wiki.ubuntu.com/Releases)를 참조 하세요.

> [!TIP]
> * 호스트 OS는 모델을 학습 하거나 배포할 때 [환경](how-to-use-environments.md) 에 대해 지정할 수 있는 os 버전이 아닙니다. 환경은 Docker 내에서 실행 됩니다. Docker는 호스트 OS에서 실행 됩니다.
> * 현재 교육 또는 배포를 위해 Ubuntu 16.04 기반 환경을 사용 중인 경우 Ubuntu 18.04 기반 이미지를 사용 하도록 전환 하는 것이 좋습니다. 자세한 내용은 [환경 사용 방법](how-to-use-environments.md) 및 [Azure Machine Learning 컨테이너 리포지토리](https://github.com/Azure/AzureML-Containers/tree/master/base)를 참조 하세요.
> * Ubuntu 18.04을 기반으로 Azure Machine Learning 계산 인스턴스를 사용 하는 경우 기본 Python 버전은 _python 3.8_ 입니다.
## <a name="creating-new-resources"></a>새 리소스 만들기

__2021 년 3 월 15 일__ 이후에 생성 된 계산 클러스터 또는 계산 인스턴스는 기본적으로 Ubuntu 18.04 lts를 호스트 OS로 사용 합니다. 다른 호스트 OS를 선택할 수 없습니다.

## <a name="upgrade-existing-resources"></a>기존 리소스 업그레이드

__2021 년 3 월 15 일__ 이전에 만든 기존 계산 클러스터 또는 계산 인스턴스가 있는 경우 호스트 OS를 Ubuntu 18.04로 업그레이드 하려면 작업을 수행 해야 합니다.

* __계산 클러스터 Azure Machine Learning__:

    * 클러스터가 __min nodes = 0__ 으로 구성 된 경우 모든 작업이 완료 되 면 자동으로 업그레이드 되 고 0 개 노드로 줄어듭니다.
    * __Min nodes에서 0 >__ 하는 경우 최소 노드를 0으로 일시적으로 변경 하 고 클러스터를 0 노드로 줄일 수 있습니다.

    최소 노드를 변경 하는 방법에 대 한 자세한 내용은 [az ml computetarget update amlcompute](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/computetarget/update#ext_azure_cli_ml_az_ml_computetarget_update_amlcompute) Azure CLI 명령 또는 [amlcompute. update ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute#update-min-nodes-none--max-nodes-none--idle-seconds-before-scaledown-none-) SDK 참조를 참조 하세요.

* __Azure Machine Learning 계산 인스턴스__: 새 계산 인스턴스 (Ubuntu 18.04를 사용 하는)를 만들고 이전 인스턴스를 삭제 합니다.

    * 데이터 집합의 작업 영역 파일 공유에 저장 된 모든 노트북은 새 계산 인스턴스에서 액세스할 수 있습니다.
    * 사용자 지정 conda 환경을 만든 경우 기존 인스턴스에서 해당 환경을 내보내고 새 인스턴스에서 가져올 수 있습니다. Conda 내보내기 및 가져오기에 대 한 자세한 내용은 docs.conda.io에서 [conda 설명서](https://docs.conda.io/) 를 참조 하세요.

    자세한 내용은 [compute 인스턴스 정의](concept-compute-instance.md) 및 [Azure Machine Learning 계산 인스턴스 만들기 및 관리](how-to-create-manage-compute-instance.md) 문서를 참조 하세요.

## <a name="check-host-os-version"></a>호스트 OS 버전 확인

호스트 OS 버전을 확인 하는 방법에 대 한 자세한 내용은 ubuntu [버전 확인](https://help.ubuntu.com/community/CheckingYourUbuntuVersion)의 ubuntu 커뮤니티 wiki 페이지를 참조 하세요.

> [!TIP]
> `lsb_release -a`Wiki에서 명령을 사용 하려면 [계산 인스턴스에서 터미널 세션을 사용할](how-to-access-terminal.md)수 있습니다.
## <a name="next-steps"></a>다음 단계

질문이 나 궁금한 사항이 있는 경우에 문의 하세요 [ubuntu18azureml@service.microsoft.com](mailto:ubuntu18azureml@service.microsoft.com) .