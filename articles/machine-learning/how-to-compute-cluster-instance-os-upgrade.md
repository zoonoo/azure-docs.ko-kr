---
title: 컴퓨팅 클러스터 및 인스턴스에 대한 호스트 OS 업그레이드
titleSuffix: Azure Machine Learning
description: 컴퓨팅 클러스터 및 컴퓨팅 인스턴스의 호스트 OS를 Ubuntu 16.04 LTS에서 18.04 LTS로 업그레이드합니다.
services: machine-learning
author: nishankgu
ms.author: nigup
ms.reviewer: larryfr
ms.service: machine-learning
ms.subservice: core
ms.date: 03/03/2021
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: 710a860b1ed87f176b6f42b4963dad17acb323b1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104954057"
---
# <a name="upgrade-compute-instance-and-compute-cluster-host-os"></a>컴퓨팅 인스턴스 및 컴퓨팅 클러스터 호스트 OS를 업그레이드합니다.

Azure Machine Learning __컴퓨팅 클러스터__ 및 __컴퓨팅 인스턴스__ 는 관리되는 컴퓨팅 인프라입니다. 관리되는 서비스인 Microsoft는 설치된 호스트 OS와 패키지 및 소프트웨어 버전을 관리합니다.

컴퓨팅 클러스터 및 컴퓨팅 인스턴스에 대한 호스트 OS는 Ubuntu 16.04 LTS입니다. **2021년 4월 30일** 에 Ubuntu는 16.04에 대한 지원을 종료합니다. __2021년 3월 15일__ 부터 Microsoft는 자동으로 호스트 OS를 Ubuntu 18.04 LTS로 업데이트합니다. 18.04로 업데이트하면 Ubuntu 커뮤니티에서 지속적인 보안 업데이트 및 지원이 보장됩니다. 이 업데이트는 Azure 지역에서 롤아웃되며 __2021년 4월 9일__ 까지 모든 지역에서 사용할 수 있습니다. Ubuntu 16.04 지원 종료에 대한 자세한 내용은 [ubuntu 릴리스 블로그](https://wiki.ubuntu.com/Releases)를 참조하세요.

> [!TIP]
> * 호스트 OS는 모델을 학습하거나 배포할 때 [환경](how-to-use-environments.md)에 대해 지정할 수 있는 OS 버전이 아닙니다. 환경은 Docker 내에서 실행됩니다. Docker는 호스트 OS에서 실행됩니다.
> * 현재 학습 또는 배포를 위해 Ubuntu 16.04 기반 환경을 사용 중인 경우 Ubuntu 18.04 기반 이미지를 사용하도록 전환하는 것이 좋습니다. 자세한 내용은 [환경 사용 방법](how-to-use-environments.md) 및 [Azure Machine Learning 컨테이너 리포지토리](https://github.com/Azure/AzureML-Containers/tree/master/base)를 참조하세요.
> * Ubuntu 18.04를 기반으로 Azure Machine Learning 컴퓨팅 인스턴스를 사용하는 경우 기본 Python 버전은 _Python 3.8_ 입니다.
## <a name="creating-new-resources"></a>새 리소스 만들기

__2021년 4월 9일__ 이후에 생성된 컴퓨팅 클러스터 또는 컴퓨팅 인스턴스는 기본적으로 Ubuntu 18.04 LTS를 호스트 OS로 사용합니다. 다른 호스트 OS를 선택할 수 없습니다.

## <a name="upgrade-existing-resources"></a>기존 리소스 업그레이드

__2021년 3월 15일__ 이전에 생성된 기존 컴퓨팅 클러스터 또는 컴퓨팅 인스턴스가 있는 경우 호스트 OS를 Ubuntu 18.04로 업그레이드하는 작업을 수행해야 합니다. Azure Machine Learning에 액세스하는 지역에 따라, __2021년 4월 9일__ 이후에는 변경 내용이 모든 지역에 롤아웃되도록 다음 작업을 수행하는 것이 좋습니다.

* __Azure Machine Learning 컴퓨팅 클러스터__:

    * 클러스터가 __min nodes = 0__ 으로 구성된 경우 모든 작업이 완료되면 자동으로 업그레이드되고 노드가 0개로 줄어듭니다.
    * __min nodes > 0__ 으로 구성된 경우 최소 노드 수를 0으로 일시적으로 변경하고 클러스터를 0개 노드로 줄일 수 있습니다.

    최소 노드 수를 변경하는 방법에 대한 자세한 내용은 [az ml computetarget update amlcompute](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/computetarget/update#ext_azure_cli_ml_az_ml_computetarget_update_amlcompute) Azure CLI 명령 또는 [AmlCompute.update()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute#update-min-nodes-none--max-nodes-none--idle-seconds-before-scaledown-none-) SDK 참조를 참조하세요.

* __Azure Machine Learning 컴퓨팅 인스턴스__: Ubuntu 18.04를 사용할 새 컴퓨팅 인스턴스를 만들고 이전 인스턴스를 삭제합니다.

    * 데이터 세트의 작업 영역 파일 공유에 저장된 모든 Notebook 파일과 작업 영역 데이터 저장소의 데이터는 새 컴퓨팅 인스턴스에서 액세스할 수 있습니다.
    * 사용자 지정 Conda 환경을 만든 경우 기존 인스턴스에서 해당 환경을 내보내고 새 인스턴스에서 가져올 수 있습니다. Conda 내보내기 및 가져오기에 대한 자세한 내용은 docs.conda.io에서 [Conda 설명서](https://docs.conda.io/)를 참조하세요.

    자세한 내용은 [컴퓨팅 인스턴스의 정의](concept-compute-instance.md) 및 [Azure Machine Learning 컴퓨팅 인스턴스 만들기 및 관리](how-to-create-manage-compute-instance.md) 문서를 참조하세요.

## <a name="check-host-os-version"></a>호스트 OS 버전 확인

호스트 OS 버전을 확인하는 방법에 대한 자세한 내용은 [Ubuntu 버전 확인](https://help.ubuntu.com/community/CheckingYourUbuntuVersion)에서 Ubuntu 커뮤니티 wiki 페이지를 참조하세요.

> [!TIP]
> Wiki에서 `lsb_release -a` 명령을 사용하려면 [컴퓨팅 인스턴스에서 터미널 세션을 사용](how-to-access-terminal.md)하면 됩니다.
## <a name="next-steps"></a>다음 단계

질문이나 궁금한 사항이 있는 경우 [ubuntu18azureml@service.microsoft.com](mailto:ubuntu18azureml@service.microsoft.com)으로 문의하세요.
