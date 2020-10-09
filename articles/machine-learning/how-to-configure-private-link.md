---
title: 개인 끝점 구성
titleSuffix: Azure Machine Learning
description: Azure 개인 링크를 사용 하 여 가상 네트워크에서 Azure Machine Learning 작업 영역에 안전 하 게 액세스할 수 있습니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 09/30/2020
ms.openlocfilehash: 4ba7ec73ac70723e21b6acad571d62d14edd250a
ms.sourcegitcommit: d2222681e14700bdd65baef97de223fa91c22c55
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/07/2020
ms.locfileid: "91828132"
---
# <a name="configure-azure-private-link-for-an-azure-machine-learning-workspace"></a>Azure Machine Learning 작업 영역에 대 한 Azure 개인 링크 구성

이 문서에서는 Azure Machine Learning 작업 영역에서 Azure 개인 링크를 사용 하는 방법에 대해 알아봅니다. Azure Machine Learning에 대 한 가상 네트워크를 만드는 방법에 대 한 자세한 내용은 [가상 네트워크 격리 및 개인 정보 개요](how-to-network-security-overview.md) 를 참조 하세요.

Azure 개인 링크를 사용 하면 개인 끝점을 사용 하 여 작업 영역에 연결할 수 있습니다. 프라이빗 엔드포인트는 가상 네트워크 내에 있는 일련의 개인 IP 주소입니다. 그런 다음 개인 IP 주소를 통해서만 발생 하도록 작업 영역에 대 한 액세스를 제한할 수 있습니다. 개인 링크를 사용 하면 데이터 exfiltration의 위험을 줄일 수 있습니다. 프라이빗 엔드포인트에 대한 자세한 내용은 [Azure Private Link](/azure/private-link/private-link-overview) 문서를 참조하세요.

> [!IMPORTANT]
> Azure 개인 링크는 작업 영역을 삭제 하거나 계산 리소스를 관리 하는 것과 같은 Azure 제어 평면 (관리 작업)에 영향을 주지 않습니다. 예를 들어 계산 대상을 생성, 업데이트 또는 삭제 합니다. 이러한 작업은 일반적인 공용 인터넷을 통해 수행 됩니다. Azure Machine Learning studio, Api (게시 된 파이프라인 포함)를 사용 하는 등의 데이터 평면 작업 또는 SDK는 개인 끝점을 사용 합니다.
>
> Mozilla Firefox를 사용 하는 경우 작업 영역에 대 한 개인 끝점에 액세스 하는 동안 문제가 발생할 수 있습니다. 이 문제는 HTTPS를 통한 DNS와 관련 된 것일 수 있습니다. 해결 방법으로 Google Chrome의 Microsoft Edge를 사용 하는 것이 좋습니다.

## <a name="prerequisites"></a>필수 구성 요소

고객 관리 키를 사용 하 여 개인 링크를 사용 하도록 설정 된 작업 영역을 사용 하려는 경우 지원 티켓을 사용 하 여이 기능을 요청 해야 합니다. 자세한 내용은 [할당량 관리 및 늘리기](how-to-manage-quotas.md#private-endpoint-and-private-dns-quota-increases)를 참조 하세요.

## <a name="limitations"></a>제한 사항

개인 링크로 Azure Machine Learning 작업 영역을 사용 하는 것은 Azure Government 지역 또는 Azure 중국 21Vianet 지역에서 사용할 수 없습니다.

## <a name="create-a-workspace-that-uses-a-private-endpoint"></a>개인 끝점을 사용 하는 작업 영역 만들기

다음 방법 중 하나를 사용 하 여 개인 끝점이 있는 작업 영역을 만듭니다.

> [!TIP]
> 필요한 경우 Azure Resource Manager 템플릿이 새 가상 네트워크를 만들 수 있습니다. 다른 방법에는 모두 기존 가상 네트워크가 필요 합니다.

# <a name="resource-manager-template"></a>[Resource Manager 템플릿](#tab/azure-resource-manager)

의 Azure Resource Manager 템플릿은 [https://github.com/Azure/azure-quickstart-templates/tree/master/201-machine-learning-advanced](https://github.com/Azure/azure-quickstart-templates/tree/master/201-machine-learning-advanced) 개인 끝점 및 가상 네트워크를 사용 하 여 작업 영역을 만드는 쉬운 방법을 제공 합니다.

개인 끝점을 포함 하 여이 템플릿을 사용 하는 방법에 대 한 자세한 내용은 [Azure Resource Manager 템플릿을 사용 하 여 Azure Machine Learning에 대 한 작업 영역 만들기](how-to-create-workspace-template.md)를 참조 하세요.

# <a name="python"></a>[Python](#tab/python)

Azure Machine Learning Python SDK는 작업 영역에서 사용할 수 있는 [PrivateEndpointConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.privateendpointconfig?view=azure-ml-py) 클래스를 제공 합니다 [. create ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py#create-name--auth-none--subscription-id-none--resource-group-none--location-none--create-resource-group-true--sku--basic---tags-none--friendly-name-none--storage-account-none--key-vault-none--app-insights-none--container-registry-none--adb-workspace-none--cmk-keyvault-none--resource-cmk-uri-none--hbi-workspace-false--default-cpu-compute-target-none--default-gpu-compute-target-none--private-endpoint-config-none--private-endpoint-auto-approval-true--exist-ok-false--show-output-true-) 를 사용 하 여 개인 끝점으로 작업 영역을 만들 수 있습니다. 이 클래스에는 기존 가상 네트워크가 필요 합니다.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[Machine learning의 Azure CLI 확장](reference-azure-machine-learning-cli.md) 은 [az ml workspace create](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/workspace?view=azure-cli-latest#ext_azure_cli_ml_az_ml_workspace_create) 명령을 제공 합니다. 이 명령에 대 한 다음 매개 변수를 사용 하 여 개인 네트워크가 있는 작업 영역을 만들 수 있지만 기존 가상 네트워크가 필요 합니다.

* `--pe-name`: 만든 개인 끝점의 이름입니다.
* `--pe-auto-approval`: 작업 영역에 대 한 개인 끝점 연결을 자동으로 승인 해야 하는지 여부입니다.
* `--pe-resource-group`: 개인 끝점을 만들 리소스 그룹입니다. 는 가상 네트워크를 포함 하는 그룹과 같아야 합니다.
* `--pe-vnet-name`: 개인 끝점을 만들 기존 가상 네트워크입니다.
* `--pe-subnet-name`: 개인 끝점을 만들 서브넷의 이름입니다. 기본값은 `default`입니다.

# <a name="portal"></a>[포털](#tab/azure-portal)

Azure Machine Learning studio의 __네트워킹__ 탭에서는 개인 끝점을 구성할 수 있습니다. 그러나 기존 가상 네트워크가 필요 합니다. 자세한 내용은 [포털에서 작업 영역 만들기](how-to-manage-workspace.md)를 참조 하세요.

---

## <a name="using-a-workspace-over-a-private-endpoint"></a>개인 끝점에서 작업 영역 사용

작업 영역에 대 한 통신은 가상 네트워크 에서만 허용 되므로 작업 영역을 사용 하는 모든 개발 환경은 가상 네트워크의 구성원 이어야 합니다. 예를 들어 가상 네트워크의 가상 머신이 있습니다.

> [!IMPORTANT]
> 일시적인 연결 중단을 방지 하려면 개인 링크를 사용 하도록 설정한 후 작업 영역에 연결 하는 컴퓨터에서 DNS 캐시를 플러시하는 것이 좋습니다. 

Azure Virtual Machines에 대 한 자세한 내용은 [Virtual Machines 설명서](/azure/virtual-machines/)를 참조 하세요.


## <a name="next-steps"></a>다음 단계

* Azure Machine Learning 작업 영역을 보호 하는 방법에 대 한 자세한 내용은 [가상 네트워크 격리 및 개인 정보 개요](how-to-network-security-overview.md) 문서를 참조 하세요.

* 가상 네트워크에서 사용자 지정 DNS 솔루션을 사용 하려는 경우 [사용자 지정 dns 서버에서 작업 영역을 사용 하는 방법](how-to-custom-dns.md)을 참조 하세요.
