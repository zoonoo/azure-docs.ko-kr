---
title: 개인 끝점 구성
titleSuffix: Azure Machine Learning
description: Azure 개인 링크를 사용 하 여 가상 네트워크에서 Azure Machine Learning 작업 영역에 안전 하 게 액세스할 수 있습니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to, devx-track-azurecli
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 09/30/2020
ms.openlocfilehash: 2953f85a5c21cdd670d6e133d09ffacf06f178ef
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/18/2020
ms.locfileid: "94842705"
---
# <a name="configure-azure-private-link-for-an-azure-machine-learning-workspace"></a>Azure Machine Learning 작업 영역에 대 한 Azure 개인 링크 구성

이 문서에서는 Azure Machine Learning 작업 영역에서 Azure 개인 링크를 사용 하는 방법에 대해 알아봅니다. Azure Machine Learning에 대 한 가상 네트워크를 만드는 방법에 대 한 자세한 내용은 [가상 네트워크 격리 및 개인 정보 개요](how-to-network-security-overview.md) 를 참조 하세요.

Azure 개인 링크를 사용 하면 개인 끝점을 사용 하 여 작업 영역에 연결할 수 있습니다. 프라이빗 엔드포인트는 가상 네트워크 내에 있는 일련의 개인 IP 주소입니다. 그런 다음 개인 IP 주소를 통해서만 발생 하도록 작업 영역에 대 한 액세스를 제한할 수 있습니다. 개인 링크를 사용 하면 데이터 exfiltration의 위험을 줄일 수 있습니다. 프라이빗 엔드포인트에 대한 자세한 내용은 [Azure Private Link](../private-link/private-link-overview.md) 문서를 참조하세요.

> [!IMPORTANT]
> Azure 개인 링크는 작업 영역을 삭제 하거나 계산 리소스를 관리 하는 것과 같은 Azure 제어 평면 (관리 작업)에 영향을 주지 않습니다. 예를 들어 계산 대상을 생성, 업데이트 또는 삭제 합니다. 이러한 작업은 일반적인 공용 인터넷을 통해 수행 됩니다. Azure Machine Learning studio, Api (게시 된 파이프라인 포함)를 사용 하는 등의 데이터 평면 작업 또는 SDK는 개인 끝점을 사용 합니다.
>
> Mozilla Firefox를 사용 하는 경우 작업 영역에 대 한 개인 끝점에 액세스 하는 동안 문제가 발생할 수 있습니다. 이 문제는 HTTPS를 통한 DNS와 관련 된 것일 수 있습니다. 해결 방법으로 Google Chrome의 Microsoft Edge를 사용 하는 것이 좋습니다.

## <a name="prerequisites"></a>전제 조건

고객 관리 키를 사용 하 여 개인 링크를 사용 하도록 설정 된 작업 영역을 사용 하려는 경우 지원 티켓을 사용 하 여이 기능을 요청 해야 합니다. 자세한 내용은 [할당량 관리 및 늘리기](how-to-manage-quotas.md#private-endpoint-and-private-dns-quota-increases)를 참조 하세요.

## <a name="limitations"></a>제한 사항

개인 링크로 Azure Machine Learning 작업 영역을 사용 하는 것은 Azure Government 지역 또는 Azure 중국 21Vianet 지역에서 사용할 수 없습니다.

## <a name="create-a-workspace-that-uses-a-private-endpoint"></a>개인 끝점을 사용 하는 작업 영역 만들기

다음 방법 중 하나를 사용 하 여 개인 끝점이 있는 작업 영역을 만듭니다. 이러한 각 방법에 __는 기존 가상 네트워크가 필요 합니다__.

> [!TIP]
> 작업 영역, 개인 끝점 및 가상 네트워크를 동시에 만들려면 [Azure Resource Manager 템플릿을 사용 하 여 Azure Machine Learning에 대 한 작업 영역 만들기](how-to-create-workspace-template.md)를 참조 하세요.

# <a name="python"></a>[Python](#tab/python)

Azure Machine Learning Python SDK는 작업 영역에서 사용할 수 있는 [PrivateEndpointConfig](/python/api/azureml-core/azureml.core.privateendpointconfig?view=azure-ml-py) 클래스를 제공 합니다 [. create ()](/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py#create-name--auth-none--subscription-id-none--resource-group-none--location-none--create-resource-group-true--sku--basic---tags-none--friendly-name-none--storage-account-none--key-vault-none--app-insights-none--container-registry-none--adb-workspace-none--cmk-keyvault-none--resource-cmk-uri-none--hbi-workspace-false--default-cpu-compute-target-none--default-gpu-compute-target-none--private-endpoint-config-none--private-endpoint-auto-approval-true--exist-ok-false--show-output-true-) 를 사용 하 여 개인 끝점으로 작업 영역을 만들 수 있습니다. 이 클래스에는 기존 가상 네트워크가 필요 합니다.

```python
from azureml.core import Workspace
from azureml.core import PrivateEndPointConfig

pe = PrivateEndPointConfig(name='myprivateendpoint', vnet_name='myvnet', vnet_subnet_name='default')
ws = Workspace.create(name='myworkspace',
    subscription_id='<my-subscription-id>',
    resource_group='myresourcegroup',
    location='eastus2',
    private_endpoint_config=pe,
    private_endpoint_auto_approval=True,
    show_output=True)
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[Machine learning의 Azure CLI 확장](reference-azure-machine-learning-cli.md) 은 [az ml workspace create](/cli/azure/ext/azure-cli-ml/ml/workspace?view=azure-cli-latest#ext_azure_cli_ml_az_ml_workspace_create) 명령을 제공 합니다. 이 명령에 대 한 다음 매개 변수를 사용 하 여 개인 네트워크가 있는 작업 영역을 만들 수 있지만 기존 가상 네트워크가 필요 합니다.

* `--pe-name`: 만든 개인 끝점의 이름입니다.
* `--pe-auto-approval`: 작업 영역에 대 한 개인 끝점 연결을 자동으로 승인 해야 하는지 여부입니다.
* `--pe-resource-group`: 개인 끝점을 만들 리소스 그룹입니다. 는 가상 네트워크를 포함 하는 그룹과 같아야 합니다.
* `--pe-vnet-name`: 개인 끝점을 만들 기존 가상 네트워크입니다.
* `--pe-subnet-name`: 개인 끝점을 만들 서브넷의 이름입니다. 기본값은 `default`입니다.

# <a name="portal"></a>[포털](#tab/azure-portal)

Azure Machine Learning studio의 __네트워킹__ 탭에서는 개인 끝점을 구성할 수 있습니다. 그러나 기존 가상 네트워크가 필요 합니다. 자세한 내용은 [포털에서 작업 영역 만들기](how-to-manage-workspace.md)를 참조 하세요.

---

## <a name="add-a-private-endpoint-to-a-workspace"></a>작업 영역에 개인 끝점 추가

다음 방법 중 하나를 사용 하 여 개인 끝점을 기존 작업 영역에 추가 합니다.

> [!IMPORTANT]
>
> 에서 개인 끝점을 만들 기존 가상 네트워크가 있어야 합니다. 또한 개인 끝점을 추가 하기 전에 [개인 끝점에 대 한 네트워크 정책을 사용 하지 않도록 설정](../private-link/disable-private-endpoint-network-policy.md) 해야 합니다.

> [!WARNING]
>
> 이 작업 영역에 연결 된 기존 계산 대상이 있는 경우 해당 가상 네트워크와는 다른 가상 네트워크를 tha는 안 됩니다. 개인 끝점은에서 생성 되지만 작동 하지 않습니다.

# <a name="python"></a>[Python](#tab/python)

```python
from azureml.core import Workspace
from azureml.core import PrivateEndPointConfig

pe = PrivateEndPointConfig(name='myprivateendpoint', vnet_name='myvnet', vnet_subnet_name='default')
ws = Workspace.from_config()
ws.add_private_endpoint(private_endpoint_config=pe, private_endpoint_auto_approval=True, show_output=True)
```

이 예제에 사용 된 클래스 및 메서드에 대 한 자세한 내용은 [PrivateEndpointConfig](/python/api/azureml-core/azureml.core.privateendpointconfig?view=azure-ml-py) 및 [Workspace.add_private_endpoint](/python/api/azureml-core/azureml.core.workspace(class)?view=azure-ml-py#add-private-endpoint-private-endpoint-config--private-endpoint-auto-approval-true--location-none--show-output-true--tags-none-)를 참조 하세요.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[Machine learning의 Azure CLI 확장](reference-azure-machine-learning-cli.md) 은 [az ml workspace private-endpoint add](/cli/azure/ext/azure-cli-ml/ml/workspace/private-endpoint?view=azure-cli-latest#ext_azure_cli_ml_az_ml_workspace_private_endpoint_add) 명령을 제공 합니다.

```azurecli
az ml workspace private-endpoint add -w myworkspace  --pe-name myprivateendpoint --pe-auto-approval true --pe-vnet-name myvnet
```

# <a name="portal"></a>[포털](#tab/azure-portal)

포털의 Azure Machine Learning 작업 영역에서 __개인 끝점 연결__ 을 선택 하 고 __+ 개인 끝점__ 을 선택 합니다. 필드를 사용 하 여 새 개인 끝점을 만듭니다.

* __지역을__ 선택 하는 경우 가상 네트워크와 동일한 지역을 선택 합니다. 
* __리소스 종류__ 를 선택 하는 경우 __MachineLearningServices/작업 영역__ 을 사용 합니다. 
* __리소스__ 를 작업 영역 이름으로 설정 합니다.

마지막으로 __만들기__ 를 선택 하 여 개인 끝점을 만듭니다.

---

## <a name="remove-a-private-endpoint"></a>개인 끝점 제거

다음 방법 중 하나를 사용 하 여 작업 영역에서 개인 끝점을 제거 합니다.

# <a name="python"></a>[Python](#tab/python)

[Workspace.delete_private_endpoint_connection](/python/api/azureml-core/azureml.core.workspace(class)?view=azure-ml-py#delete-private-endpoint-connection-private-endpoint-connection-name-) 를 사용 하 여 개인 끝점을 제거 합니다.

```python
from azureml.core import Workspace

ws = Workspace.from_config()
# get the connection name
_, _, connection_name = ws.get_details()['privateEndpointConnections'][0]['id'].rpartition('/')
ws.delete_private_endpoint_connection(private_endpoint_connection_name=connection_name)
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[Machine learning의 Azure CLI 확장](reference-azure-machine-learning-cli.md) 은 [az ml workspace private-endpoint delete](/cli/azure/ext/azure-cli-ml/ml/workspace/private-endpoint?view=azure-cli-latest#ext_azure_cli_ml_az_ml_workspace_private_endpoint_delete) 명령을 제공 합니다.

# <a name="portal"></a>[포털](#tab/azure-portal)

포털의 Azure Machine Learning 작업 영역에서 __개인 끝점 연결__ 을 선택 하 고 제거 하려는 끝점을 선택 합니다. 마지막으로 __제거__ 를 선택 합니다.

---

## <a name="using-a-workspace-over-a-private-endpoint"></a>개인 끝점에서 작업 영역 사용

작업 영역에 대 한 통신은 가상 네트워크 에서만 허용 되므로 작업 영역을 사용 하는 모든 개발 환경은 가상 네트워크의 구성원 이어야 합니다. 예를 들어 가상 네트워크의 가상 머신이 있습니다.

> [!IMPORTANT]
> 일시적인 연결 중단을 방지 하려면 개인 링크를 사용 하도록 설정한 후 작업 영역에 연결 하는 컴퓨터에서 DNS 캐시를 플러시하는 것이 좋습니다. 

Azure Virtual Machines에 대 한 자세한 내용은 [Virtual Machines 설명서](../virtual-machines/index.yml)를 참조 하세요.


## <a name="next-steps"></a>다음 단계

* Azure Machine Learning 작업 영역을 보호 하는 방법에 대 한 자세한 내용은 [가상 네트워크 격리 및 개인 정보 개요](how-to-network-security-overview.md) 문서를 참조 하세요.

* 가상 네트워크에서 사용자 지정 DNS 솔루션을 사용 하려는 경우 [사용자 지정 dns 서버에서 작업 영역을 사용 하는 방법](how-to-custom-dns.md)을 참조 하세요.
