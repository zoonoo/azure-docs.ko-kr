---
title: 방화벽 뒤에 Azure Machine Learning 사용
titleSuffix: Azure Machine Learning
description: Azure 방화벽 뒤에 Azure Machine Learning 안전 하 게 사용 합니다. Azure Machine Learning가 제대로 작동 하도록 방화벽을 통해 허용 해야 하는 호스트에 대해 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 04/27/2020
ms.openlocfilehash: 2c5fe9d46ef408775cc4b99d4820a652a91683c4
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "82196323"
---
# <a name="use-azure-machine-learning-workspace-behind-azure-firewall"></a>Azure 방화벽 뒤에 Azure Machine Learning 작업 영역 사용

이 문서에는 Azure Machine Learning에서 사용할 Azure 방화벽 구성에 대 한 정보가 포함 되어 있습니다.

Azure 방화벽은 Azure Machine Learning 작업 영역 및 공용 인터넷에 대 한 액세스를 제어 하는 데 사용할 수 있습니다. 올바르게 구성 되지 않은 경우 방화벽에서 작업 영역을 사용 하 여 문제를 일으킬 수 있습니다.

## <a name="network-rules"></a>네트워크 규칙

방화벽에서이 문서의 주소로 들어오고 나가는 트래픽을 허용 하는 네트워크 규칙을 만듭니다.

> [!TIP]
> 네트워크 규칙을 추가 하는 경우 __프로토콜__ 을 any로 설정 하 고 포트를 `*`로 설정 합니다.
>
> Azure 방화벽 구성에 대 한 자세한 내용은 [Azure 방화벽 배포 및 구성](../firewall/tutorial-firewall-deploy-portal.md#configure-a-network-rule)을 참조 하세요.

## <a name="microsoft-hosts"></a>Microsoft 호스트

이 섹션의 호스트는 Microsoft에서 소유 하며 작업 영역의 적절 한 기능을 제공 하는 데 필요한 서비스를 제공 합니다.

| **호스트 이름** | **목적** |
| ---- | ---- |
| **\*. batchai.core.windows.net** | 클러스터 학습 |
| **ml.azure.com** | Azure Machine Learning Studio |
| **\*. azureml.ms** | Azure Machine Learning Api에서 사용 |
| **\*. experiments.azureml.net** | Azure Machine Learning에서 실행 되는 실험에서 사용|
| **\*. modelmanagement.azureml.net** | 모델을 등록 하 고 배포 하는 데 사용 됩니다.|
| **mlworkspace.azure.ai** | 작업 영역을 볼 때 Azure Portal에서 사용 됩니다. |
| **\*. aether.ms** | Azure Machine Learning 파이프라인을 실행할 때 사용 됩니다. |
| **\*. instances.azureml.net** | Azure Machine Learning 계산 인스턴스 |
| **windows.net** | Azure Blob Storage |
| **vault.azure.net** | Azure Key Vault |
| **microsoft.com** | 기본 docker 이미지 |
| **azurecr.io** | Azure Container Registry |

## <a name="python-hosts"></a>Python 호스트

이 섹션의 호스트는 Python 패키지를 설치 하는 데 사용 됩니다. 개발, 학습 및 배포 중에 필요 합니다. 

| **호스트 이름** | **목적** |
| ---- | ---- |
| **anaconda.com** | Conda 패키지를 설치할 때 사용 됩니다. |
| **pypi.org** | Pip 패키지를 설치할 때 사용 됩니다. |

## <a name="r-hosts"></a>R 호스트

이 섹션의 호스트는 R 패키지를 설치 하는 데 사용 됩니다. 개발, 학습 및 배포 중에 필요 합니다.

> [!IMPORTANT]
> 내부적으로 Azure Machine Learning 용 R SDK는 Python 패키지를 사용 합니다. 따라서 방화벽을 통해 Python 호스트도 허용 해야 합니다.

| **호스트 이름** | **목적** |
| ---- | ---- |
| **cloud.r-project.org** | CRAN 패키지를 설치할 때 사용 됩니다. |

다음 단계

* [[Azure 방화벽 배포 및 구성](../firewall/tutorial-firewall-deploy-portal.md)]
* [Azure Virtual Network 내에서 Azure ML 실험 및 유추 작업 보호](how-to-enable-virtual-network.md)