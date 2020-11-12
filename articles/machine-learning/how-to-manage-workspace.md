---
title: 포털에서 작업 영역 만들기
titleSuffix: Azure Machine Learning
description: Azure Portal 또는 Python 용 SDK를 사용 하 여 Azure Machine Learning 작업 영역을 만들고, 확인 하 고, 삭제 하는 방법에 대해 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: sgilley
author: sdgilley
ms.date: 09/30/2020
ms.topic: conceptual
ms.custom: how-to, fasttrack-edit
ms.openlocfilehash: 29c378d40e3a4f92852f433677125a9e8a6d1133
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/12/2020
ms.locfileid: "94540130"
---
# <a name="create-and-manage-azure-machine-learning-workspaces"></a>Azure Machine Learning 작업 영역 만들기 및 관리 

이 문서에서는 Azure Portal 또는 [Python 용 SDK](/python/api/overview/azure/ml/?preserve-view=true&view=azure-ml-py) 를 사용 하 여 [Azure Machine Learning](overview-what-is-azure-ml.md)에 대 한 [**Azure Machine Learning 작업 영역**](concept-workspace.md) 을 만들고, 확인 하 고, 삭제 합니다.

변경 사항이 나 automation 증가에 대 한 요구 사항에 따라 [CLI를 사용](reference-azure-machine-learning-cli.md)하거나 [VS Code 확장을 통해](tutorial-setup-vscode-extension.md)작업 영역을 만들고 삭제할 수도 있습니다.

## <a name="prerequisites"></a>필수 구성 요소

* Azure 구독 Azure 구독이 없는 경우 시작하기 전에 체험 계정을 만듭니다. 지금 [Azure Machine Learning 평가판 또는 유료 버전](https://aka.ms/AMLFree)을 사용해 보세요.
* Python SDK를 사용 하는 경우 [sdk를 설치](/python/api/overview/azure/ml/install?preserve-view=true&view=azure-ml-py)합니다.

## <a name="create-a-workspace"></a>작업 영역 만들기

# <a name="python"></a>[Python](#tab/python)

* **기본 사양입니다.** 기본적으로 리소스 그룹 뿐만 아니라 종속 리소스도 자동으로 생성 됩니다. 이 코드에서는 이라는 작업 영역 `myworkspace` 및에 라는 리소스 그룹을 만듭니다 `myresourcegroup` `eastus2` .
    
    ```python
    from azureml.core import Workspace
    
    ws = Workspace.create(name='myworkspace',
                   subscription_id='<azure-subscription-id>',
                   resource_group='myresourcegroup',
                   create_resource_group=True,
                   location='eastus2'
                   )
    ```
    `create_resource_group`작업 영역에 사용 하려는 기존 Azure 리소스 그룹이 있는 경우 False로 설정 합니다.

* <a name="create-multi-tenant"></a>**다중 테 넌 트.**  계정이 여러 개인 경우 사용 하려는 Azure Active Directory의 테 넌 트 ID를 추가 합니다.  **Azure Active Directory, 외부 id** 의 [Azure Portal](https://portal.azure.com) 에서 테 넌 트 ID를 찾습니다.

    ```python
    from azureml.core.authentication import InteractiveLoginAuthentication
    from azureml.core import Workspace
    
    interactive_auth = InteractiveLoginAuthentication(tenant_id="my-tenant-id")
    ws = Workspace.create(name='myworkspace',
                subscription_id='<azure-subscription-id>',
                resource_group='myresourcegroup',
                create_resource_group=True,
                location='eastus2',
                auth=interactive_auth
                )
    ```

* **[소 버린 클라우드](reference-machine-learning-cloud-parity.md)**. 소 버린 클라우드에서 작업 하는 경우 Azure에 인증 하려면 추가 코드가 필요 합니다.

    ```python
    from azureml.core.authentication import InteractiveLoginAuthentication
    from azureml.core import Workspace
    
    interactive_auth = InteractiveLoginAuthentication(cloud="<cloud name>") # for example, cloud="AzureUSGovernment"
    ws = Workspace.create(name='myworkspace',
                subscription_id='<azure-subscription-id>',
                resource_group='myresourcegroup',
                create_resource_group=True,
                location='eastus2',
                auth=interactive_auth
                )
    ```

* **기존 Azure 리소스를 사용** 합니다.  Azure 리소스 ID 형식으로 기존 Azure 리소스를 사용 하는 작업 영역을 만들 수도 있습니다. Azure Portal 또는 SDK를 사용 하 여 특정 Azure 리소스 Id를 찾습니다. 이 예에서는 리소스 그룹, 저장소 계정, key vault, App Insights 및 container registry가 이미 있는 것으로 가정 합니다.

   ```python
   import os
   from azureml.core import Workspace
   from azureml.core.authentication import ServicePrincipalAuthentication

   service_principal_password = os.environ.get("AZUREML_PASSWORD")

   service_principal_auth = ServicePrincipalAuthentication(
      tenant_id="<tenant-id>",
      username="<application-id>",
      password=service_principal_password)

                        auth=service_principal_auth,
                             subscription_id='<azure-subscription-id>',
                             resource_group='myresourcegroup',
                             create_resource_group=False,
                             location='eastus2',
                             friendly_name='My workspace',
                             storage_account='subscriptions/<azure-subscription-id>/resourcegroups/myresourcegroup/providers/microsoft.storage/storageaccounts/mystorageaccount',
                             key_vault='subscriptions/<azure-subscription-id>/resourcegroups/myresourcegroup/providers/microsoft.keyvault/vaults/mykeyvault',
                             app_insights='subscriptions/<azure-subscription-id>/resourcegroups/myresourcegroup/providers/microsoft.insights/components/myappinsights',
                             container_registry='subscriptions/<azure-subscription-id>/resourcegroups/myresourcegroup/providers/microsoft.containerregistry/registries/mycontainerregistry',
                             exist_ok=False)
   ```

자세한 내용은 [작업 영역 SDK 참조](/python/api/azureml-core/azureml.core.workspace.workspace?preserve-view=true&view=azure-ml-py)를 참조 하세요.

구독에 액세스 하는 데 문제가 있는 경우 [Azure Machine Learning 노트북의 인증](https://aka.ms/aml-notebook-auth) 뿐만 아니라 [Azure Machine Learning 리소스 및 워크플로에 대 한 인증 설정](how-to-setup-authentication.md)을 참조 하세요.

# <a name="portal"></a>[포털](#tab/azure-portal)

1. Azure 구독에 대한 자격 증명을 사용하여 [Azure Portal](https://portal.azure.com/)에 로그인합니다. 

1. Azure Portal의 왼쪽 위 모서리에서 **+ 리소스 만들기** 를 선택합니다.

      ![새 리소스 만들기](./media/how-to-manage-workspace/create-workspace.gif)

1. 검색 창을 사용하여 **Machine Learning** 을 찾습니다.

1. **Machine Learning** 을 선택합니다.

1. **Machine Learning** 창에서 **만들기** 를 선택하여 시작합니다.

1. 새 작업 영역을 구성하려면 다음 정보를 제공하세요.

   필드|Description 
   ---|---
   작업 영역 이름 |작업 영역을 식별하는 고유한 이름을 입력합니다. 이 예제에서는 **docs-ws** 를 사용합니다. 이름은 리소스 그룹 전체에서 고유해야 합니다. 다른 사용자가 만든 작업 영역과 구별되고 기억하기 쉬운 이름을 사용하세요. 작업 영역 이름은 대/소문자를 구분하지 않습니다.
   Subscription |사용할 Azure 구독을 선택합니다.
   Resource group | 구독의 기존 리소스 그룹을 사용하거나 이름을 입력하여 새 리소스 그룹을 만듭니다. 리소스 그룹은 Azure 솔루션에 관련된 리소스를 보유합니다. 이 예에서는 **docs-aml** 을 사용합니다. 기존 리소스 그룹을 사용 하려면 *참가자* 또는 *소유자* 역할이 필요 합니다.  액세스에 대 한 자세한 내용은 [Azure Machine Learning 작업 영역에 대 한 액세스 관리](how-to-assign-roles.md)를 참조 하세요.
   지역 | 사용자의 작업 영역을 만들 사용자 및 데이터 리소스에 가장 가까운 Azure 지역을 선택 합니다.

    ![작업 영역 구성](./media/how-to-manage-workspace/create-workspace-form.png)

1. 작업 영역 구성을 마쳤으면 **검토 + 만들기** 를 선택 합니다. 필요에 따라 [네트워킹](#networking) 및 [고급](#advanced) 섹션을 사용 하 여 작업 영역에 대 한 추가 설정을 구성 합니다.

1. 설정을 검토 하 고 추가 변경 또는 수정 작업을 수행 합니다. 설정에 만족 하는 경우 **만들기** 를 선택 합니다.

   > [!Warning] 
   > 클라우드에서 작업 영역을 만드는 데 몇 분 정도 걸릴 수 있습니다.

   프로세스가 완료되면 배포 성공 메시지가 표시됩니다. 
 
 1. 새 작업 영역을 보려면 **리소스로 이동** 을 선택합니다.
 
---

### <a name="networking"></a>네트워킹  

> [!IMPORTANT]  
> 작업 영역에서 개인 끝점 및 가상 네트워크를 사용 하는 방법에 대 한 자세한 내용은 [네트워크 격리 및 개인 정보](how-to-network-security-overview.md)를 참조 하세요.


# <a name="python"></a>[Python](#tab/python)

Azure Machine Learning Python SDK는 작업 영역에서 사용할 수 있는 [PrivateEndpointConfig](/python/api/azureml-core/azureml.core.privateendpointconfig?preserve-view=true&view=azure-ml-py) 클래스를 제공 합니다 [. create ()](/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py#create-name--auth-none--subscription-id-none--resource-group-none--location-none--create-resource-group-true--sku--basic---tags-none--friendly-name-none--storage-account-none--key-vault-none--app-insights-none--container-registry-none--adb-workspace-none--cmk-keyvault-none--resource-cmk-uri-none--hbi-workspace-false--default-cpu-compute-target-none--default-gpu-compute-target-none--private-endpoint-config-none--private-endpoint-auto-approval-true--exist-ok-false--show-output-true-&preserve-view=true) 를 사용 하 여 개인 끝점으로 작업 영역을 만들 수 있습니다. 이 클래스에는 기존 가상 네트워크가 필요 합니다.

# <a name="portal"></a>[포털](#tab/azure-portal)

1. 기본 네트워크 구성은 공용 인터넷에서 액세스할 수 있는 __공용 끝점__ 을 사용 하는 것입니다. 만든 Azure Virtual Network 작업 영역에 대 한 액세스를 제한 하려면 __연결 방법__ 으로 __개인 끝점__ (미리 보기)을 선택 하 고 __+ 추가__ 를 사용 하 여 끝점을 구성 하면 됩니다.   

   :::image type="content" source="media/how-to-manage-workspace/select-private-endpoint.png" alt-text="개인 끝점 선택":::  

1. __개인 끝점 만들기__ 양식에서 사용할 위치, 이름 및 가상 네트워크를 설정 합니다. 사설 DNS 영역에서 끝점을 사용 하려면 __개인 DNS 영역과 통합__ 을 선택 하 고 __사설 DNS 영역__ 필드를 사용 하 여 영역을 선택 합니다. __확인__ 을 선택 하 여 끝점을 만듭니다.   

   :::image type="content" source="media/how-to-manage-workspace/create-private-endpoint.png" alt-text="개인 끝점 만들기":::   

1. 네트워킹 구성을 완료 한 후 __검토 + 만들기__ 를 선택 하거나 __고급__ 구성 (선택 사항)으로 이동할 수 있습니다.

---

> [!IMPORTANT]  
> Azure Machine Learning 작업 영역에서 개인 끝점을 사용 하는 것은 현재 공개 미리 보기 상태입니다. 이 미리 보기는 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.     
> 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

### <a name="multiple-workspaces-with-private-endpoint"></a>개인 끝점을 사용 하는 여러 작업 영역

개인 끝점을 만들 때 __privatelink.api.azureml.ms__ 이라는 새 사설 DNS 영역이 만들어집니다. 여기에는 가상 네트워크에 대 한 링크가 포함 됩니다. 동일한 리소스 그룹에서 개인 끝점을 사용 하 여 여러 작업 영역을 만드는 경우 첫 번째 개인 끝점에 대 한 가상 네트워크만 DNS 영역에 추가할 수 있습니다. 추가 작업 영역/개인 끝점에서 사용 하는 가상 네트워크에 대 한 항목을 추가 하려면 다음 단계를 사용 합니다.

1. [Azure Portal](https://portal.azure.com)에서 작업 영역을 포함 하는 리소스 그룹을 선택 합니다. 그런 다음 __privatelink.api.azureml.ms__ 이라는 사설 DNS 영역 리소스를 선택 합니다.
2. __설정__ 에서 __가상 네트워크 링크__ 를 선택 합니다.
3. __추가__ 를 선택합니다. __가상 네트워크 링크 추가__ 페이지에서 고유한 __링크 이름을__ 제공한 다음 추가할 __가상 네트워크__ 를 선택 합니다. __확인__ 을 선택 하 여 네트워크 링크를 추가 합니다.

자세한 내용은 [Azure 개인 끝점 DNS 구성](../private-link/private-endpoint-dns.md)을 참조 하세요.

### <a name="vulnerability-scanning"></a>취약성 검색

Azure Security Center는 하이브리드 클라우드 워크로드에 통합 보안 관리 및 고급 위협 방지를 제공합니다. Azure Security Center에서 리소스를 검색 하 고 권장 사항을 따를 수 있도록 해야 합니다. 자세한 내용은  [Azure Container Registry 이미지 검색 Security Center](../security-center/defender-for-container-registries-introduction.md) 및 [Security Center와 Azure Kubernetes Services 통합](../security-center/defender-for-kubernetes-introduction.md)을 참조 하세요.

### <a name="advanced"></a>고급

기본적으로 작업 영역에 대 한 메타 데이터는 Microsoft에서 유지 관리 하는 Azure Cosmos DB 인스턴스에 저장 됩니다. 이 데이터는 Microsoft에서 관리 하는 키를 사용 하 여 암호화 됩니다.

Microsoft에서 작업 영역에 대해 수집 하는 데이터를 제한 하려면 포털에서 __높은 비즈니스 영향 작업 영역__ 을 선택 하거나 `hbi_workspace=true ` Python에서 설정 합니다. 이 설정에 대 한 자세한 내용은 [미사용 암호화](concept-data-encryption.md#encryption-at-rest)를 참조 하세요.

> [!IMPORTANT]  
> 높은 비즈니스 영향을 선택 하는 작업은 작업 영역을 만들 때만 수행할 수 있습니다. 작업 영역을 만든 후에는이 설정을 변경할 수 없습니다.   

#### <a name="use-your-own-key"></a>사용자 고유의 키 사용

데이터 암호화를 위해 고유한 키를 제공할 수 있습니다. 이렇게 하면 Azure 구독에 메타 데이터를 저장 하는 Azure Cosmos DB 인스턴스가 만들어집니다.

[!INCLUDE [machine-learning-customer-managed-keys.md](../../includes/machine-learning-customer-managed-keys.md)]

다음 단계를 사용 하 여 사용자 고유의 키를 제공 합니다.

> [!IMPORTANT]  
> 이러한 단계를 수행 하기 전에 먼저 다음 작업을 수행 해야 합니다.   
>
> 1. 구독에 대 한 참가자 권한으로 Id 및 액세스 관리에서 __Machine Learning 앱__ 에 권한을 부여 합니다.  
> 1. [고객 관리 키 구성](../cosmos-db/how-to-setup-cmk.md) 의 단계에 따라 다음을 수행 합니다.
>     * Azure Cosmos DB 공급자 등록
>     * Azure Key Vault 만들기 및 구성
>     * 키 생성
>   
>     Azure Cosmos DB 인스턴스를 수동으로 만들 필요는 없으며 작업 영역을 만드는 동안 생성 됩니다. 이 Azure Cosmos DB 인스턴스는이 패턴을 기반으로 하는 이름을 사용 하 여 별도의 리소스 그룹에 만들어집니다 `<your-workspace-resource-name>_<GUID>` .   
>   
> 작업 영역을 만든 후에는이 설정을 변경할 수 없습니다. 작업 영역에서 사용 하는 Azure Cosmos DB를 삭제 하는 경우 해당 작업 영역을 사용 하는 작업 영역도 삭제 해야 합니다.

# <a name="python"></a>[Python](#tab/python)

`cmk_keyvault`및 `resource_cmk_uri` 를 사용 하 여 고객 관리 키를 지정 합니다.

```python
from azureml.core import Workspace
   ws = Workspace.create(name='myworkspace',
               subscription_id='<azure-subscription-id>',
               resource_group='myresourcegroup',
               create_resource_group=True,
               location='eastus2'
               cmk_keyvault='subscriptions/<azure-subscription-id>/resourcegroups/myresourcegroup/providers/microsoft.keyvault/vaults/<keyvault-name>', 
               resource_cmk_uri='<key-identifier>'
               )

```

# <a name="portal"></a>[포털](#tab/azure-portal)

1. __고객 관리 키__ 를 선택 하 고 __키를 선택 하려면 클릭__ 하십시오 .를 선택 합니다.

    :::image type="content" source="media/how-to-manage-workspace/advanced-workspace.png" alt-text="고객 관리형 키":::

1. __Azure Key Vault에서 키 선택__ 양식에서 기존 Azure Key Vault, 포함 된 키 및 키의 버전을 선택 합니다. 이 키는 Azure Cosmos DB에 저장 된 데이터를 암호화 하는 데 사용 됩니다. 마지막으로, __선택__ 단추를 사용 하 여이 키를 사용 합니다.

   :::image type="content" source="media/how-to-manage-workspace/select-key-vault.png" alt-text="키 선택":::

---

### <a name="download-a-configuration-file"></a>구성 파일 다운로드

[계산 인스턴스](tutorial-1st-experiment-sdk-setup.md#azure)를 만들려는 경우에는이 단계를 건너뜁니다.  계산 인스턴스가이 파일의 복사본을 이미 만들었습니다.

# <a name="python"></a>[Python](#tab/python)

로컬 환경에서이 작업 영역을 참조 하는 코드를 사용 하려면 ( `ws` ) 다음 구성 파일을 작성 합니다.

```python
ws.write_config()
```

# <a name="portal"></a>[포털](#tab/azure-portal)

로컬 환경에서 이 작업 영역을 참조하는 코드를 사용하려면 작업 영역의 **개요** 섹션에서 **config.json 다운로드** 를 선택합니다.  

   ![config.json 다운로드](./media/how-to-manage-workspace/configure.png)

---

Python 스크립트 또는 Jupyter Notebook을 사용하여 파일을 디렉터리 구조에 배치합니다. 동일한 디렉터리, *.azureml* 이라는 하위 디렉터리 또는 부모 디렉터리에 있을 수 있습니다. 계산 인스턴스를 만들 때이 파일은 VM의 올바른 디렉터리에 추가 됩니다.

## <a name="connect-to-a-workspace"></a>작업 영역에 연결

Python 코드에서 작업 영역에 연결 하는 작업 영역 개체를 만듭니다.  이 코드는 구성 파일의 내용을 읽고 작업 영역을 찾습니다.  아직 인증 하지 않은 경우 로그인 하 라는 메시지가 표시 됩니다.

```python
from azureml.core import Workspace

ws = Workspace.from_config()
```

* <a name="connect-multi-tenant"></a>**다중 테 넌 트.**  계정이 여러 개인 경우 사용 하려는 Azure Active Directory의 테 넌 트 ID를 추가 합니다.  **Azure Active Directory, 외부 id** 의 [Azure Portal](https://portal.azure.com) 에서 테 넌 트 ID를 찾습니다.

    ```python
    from azureml.core.authentication import InteractiveLoginAuthentication
    from azureml.core import Workspace
    
    interactive_auth = InteractiveLoginAuthentication(tenant_id="my-tenant-id")
    ws = Workspace.from_config(auth=interactive_auth)
    ```

* **[소 버린 클라우드](reference-machine-learning-cloud-parity.md)**. 소 버린 클라우드에서 작업 하는 경우 Azure에 인증 하려면 추가 코드가 필요 합니다.

    ```python
    from azureml.core.authentication import InteractiveLoginAuthentication
    from azureml.core import Workspace
    
    interactive_auth = InteractiveLoginAuthentication(cloud="<cloud name>") # for example, cloud="AzureUSGovernment"
    ws = Workspace.from_config(auth=interactive_auth)
    ```
    
구독에 액세스 하는 데 문제가 있는 경우 [Azure Machine Learning 노트북의 인증](https://aka.ms/aml-notebook-auth) 뿐만 아니라 [Azure Machine Learning 리소스 및 워크플로에 대 한 인증 설정](how-to-setup-authentication.md)을 참조 하세요.

## <a name="find-a-workspace"></a><a name="view"></a>작업 영역 찾기

사용할 수 있는 모든 작업 영역 목록이 표시 됩니다.

# <a name="python"></a>[Python](#tab/python)

[Azure Portal의 구독 페이지](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)에서 구독을 찾습니다.  ID를 복사 하 고 아래 코드에서 사용 하 여 해당 구독에 사용할 수 있는 모든 작업 영역을 확인 합니다.

```python
from azureml.core import Workspace

Workspace.list('<subscription-id>')
```

# <a name="portal"></a>[포털](#tab/azure-portal)

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.

1. 위쪽 검색 필드에 **Machine Learning** 을 입력 합니다.  

1. **Machine Learning** 을 선택합니다.

   ![Azure Machine Learning 작업 영역 검색](./media/how-to-manage-workspace/find-workspaces.png)

1. 검색된 작업 영역 목록을 살펴봅니다. 구독, 리소스 그룹 및 위치를 기준으로 필터링 할 수 있습니다.  

1. 해당 속성을 표시할 작업 영역을 선택 합니다.

---


## <a name="delete-a-workspace"></a>작업 영역 삭제

작업 영역이 더 이상 필요 하지 않으면 삭제 합니다.  

# <a name="python"></a>[Python](#tab/python)

작업 영역을 삭제 합니다 `ws` .

```python
ws.delete(delete_dependent_resources=False, no_wait=False)
```

기본 작업은 작업 영역에 연결 된 리소스 (예: container registry, storage 계정, key vault 및 application insights)를 삭제 하지 않습니다.  `delete_dependent_resources`이러한 리소스도 삭제 하려면 True로 설정 합니다.

# <a name="portal"></a>[포털](#tab/azure-portal)

[Azure Portal](https://portal.azure.com/)에서 삭제 하려는 작업 영역의 맨 위에 있는 **삭제** 를 선택 합니다.

:::image type="content" source="./media/how-to-manage-workspace/delete-workspace.png" alt-text="작업 영역 삭제":::

---

## <a name="clean-up-resources"></a>리소스 정리

[!INCLUDE [aml-delete-resource-group](../../includes/aml-delete-resource-group.md)]

## <a name="troubleshooting"></a>문제 해결

### <a name="resource-provider-errors"></a>리소스 공급자 오류

[!INCLUDE [machine-learning-resource-provider](../../includes/machine-learning-resource-provider.md)]

### <a name="moving-the-workspace"></a>작업 영역 이동

> [!WARNING]
> Azure Machine Learning 작업 영역을 다른 구독으로 이동하거나 소유하는 구독을 새 테넌트로 이동하는 것은 지원되지 않습니다. 이렇게 하면 오류가 발생할 수 있습니다.

### <a name="deleting-the-azure-container-registry"></a>Azure Container Registry 삭제

Azure Machine Learning 작업 영역에서는 일부 작업에 ACR(Azure Container Registry)을 사용합니다. 먼저 필요한 경우 ACR 인스턴스를 자동으로 만듭니다.

[!INCLUDE [machine-learning-delete-acr](../../includes/machine-learning-delete-acr.md)]

## <a name="examples"></a>예제

작업 영역을 만드는 예는 다음과 같습니다.
* Azure Portal를 사용 하 여 [작업 영역 및 계산 인스턴스 만들기](tutorial-1st-experiment-sdk-setup.md)
* Python SDK를 사용 하 여 [사용자 환경에서 작업 영역 만들기](tutorial-1st-experiment-sdk-setup-local.md)

## <a name="next-steps"></a>다음 단계

작업 영역이 있으면 [모델을 학습 하 고 배포](tutorial-train-models-with-aml.md)하는 방법을 알아봅니다.