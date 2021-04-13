---
title: '빠른 시작: Python을 사용하여 Purview 계정 만들기'
description: Python을 사용하여 Azure Purview 계정을 만듭니다.
author: nayenama
ms.author: nayenama
ms.service: purview
ms.subservice: purview-data-catalog
ms.devlang: python
ms.topic: quickstart
ms.date: 04/02/2021
ms.openlocfilehash: f8ac611d25507913d6d5f2e2dd289ea52ce4ae9f
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/05/2021
ms.locfileid: "106387213"
---
# <a name="quickstart-create-a-purview-account-using-python"></a>빠른 시작: Python을 사용하여 Purview 계정 만들기

이 빠른 시작에서는 Python을 사용하여 Purview 계정을 만듭니다. 

## <a name="prerequisites"></a>필수 구성 요소

* 활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* 사용자 고유의 [Azure Active Directory 테넌트](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

* 계정에는 구독에서 리소스를 만들 수 있는 권한이 있어야 합니다.

* 모든 애플리케이션에서 **Storage 계정** 및 **EventHub 네임스페이스** 를 만들지 못하도록 차단하는 **Azure Policy** 가 있는 경우 태그를 사용하여 정책 예외를 만들어야 합니다. 이 예외는 Purview 계정을 만드는 과정에서 입력할 수 있습니다. 가장 중요한 이유는 만든 각 Purview 계정에 대해 관리되는 리소스 그룹을 만들고 이 리소스 그룹 내에 Storage 계정 및 EventHub 네임스페이스를 만들어야 하기 때문입니다. 자세한 내용은 [카탈로그 포털 만들기](create-catalog-portal.md)를 참조하세요.


## <a name="install-the-python-package"></a>Python 패키지 설치

1. 관리자 권한으로 터미널 또는 명령 프롬프트를 엽니다. 
2. 먼저, Azure 관리 리소스에 대한 Python 패키지를 설치합니다.

    ```python
    pip install azure-mgmt-resource
    ```
3. Purview에 대한 Python 패키지를 설치하려면 다음 명령을 실행합니다.

    ```python
    pip install azure-mgmt-purview
    ```

    [Purview용 Python SDK](https://github.com/Azure/azure-sdk-for-python)는 Python 2.7, 3.3, 3.4, 3.5, 3.6 및 3.7을 지원합니다.

4. Azure ID 인증을 위한 Python 패키지를 설치하려면 다음 명령을 실행합니다.

    ```python
    pip install azure-identity
    ```
    > [!NOTE] 
    > "azure-identity" 패키지는 일부 공통 종속성에서 "azure-cli"와 충돌할 수 있습니다. 인증 문제가 발생하면 "azure-cli" 및 해당 종속성을 제거하거나 "azure cli" 패키지를 설치하지 않고 정리된 머신을 사용하여 작동하도록 합니다.
    
## <a name="create-a-purview-client"></a>purview 클라이언트 만들기

1. **purview.py** 라는 파일을 만듭니다. 다음 문을 추가하여 네임스페이스에 대한 참조를 추가합니다.

    ```python
    from azure.identity import ClientSecretCredential 
    from azure.mgmt.resource import ResourceManagementClient
    from azure.mgmt.purview import PurviewManagementClient
    from azure.mgmt.purview.models import *
    from datetime import datetime, timedelta
    import time
    ```

2. **Main** 메서드에 PurviewManagementClient 클래스의 인스턴스를 만드는 다음 코드를 추가합니다. 이 개체를 사용하여 purview 계정을 만들고, purview 계정을 삭제하고, 이름 가용성 및 다른 리소스 공급자 작업을 확인할 수 있습니다.
 
 ```python
    def main():
    
    # Azure subscription ID
    subscription_id = '<subscription ID>'
    
    # This program creates this resource group. If it's an existing resource group, comment out the code that creates the resource group
    rg_name = '<resource group>'

    # The purview name. It must be globally unique.
    purview_name = '<purview account name>'

    # Location name, where Purview account must be created.
    location = '<location name>'    

    # Specify your Active Directory client ID, client secret, and tenant ID
    credentials = ClientSecretCredential(client_id='<service principal ID>', client_secret='<service principal key>', tenant_id='<tenant ID>') 
    # resource_client = ResourceManagementClient(credentials, subscription_id)
    purview_client = PurviewManagementClient(credentials, subscription_id)
    ```

## <a name="create-a-purview-account"></a>purview 계정 만들기

**Main** 메서드에 **purview 계정** 을 만드는 다음 코드를 추가합니다. 리소스 그룹이 이미 있는 경우 첫 번째 `create_or_update` 문을 주석으로 처리합니다.

```python
    # create the resource group
    # comment out if the resource group already exits
    resource_client.resource_groups.create_or_update(rg_name, rg_params)

    #Create a purview
    identity = Identity(type= "SystemAssigned")
    sku = AccountSku(name= 'Standard', capacity= 4)
    purview_resource = Account(identity=identity,sku=sku,location =location )
       
    try:
        pa = (purview_client.accounts.begin_create_or_update(rg_name, purview_name, purview_resource)).result()
        print("location:", pa.location, " Purview Account Name: ", pa.name, " Id: " , pa.id ," tags: " , pa.tags)  
    except:
        print("Error")
        print_item(pa)
 
    while (getattr(pa,'provisioning_state')) != "Succeeded" :
        pa = (purview_client.accounts.get(rg_name, purview_name))  
        print(getattr(pa,'provisioning_state'))
        if getattr(pa,'provisioning_state') != "Failed" :
            print("Error in creating Purview account")
            break
        time.sleep(30)      
        
```

이제 프로그램이 실행될 때 **main** 메서드를 호출하기 위해 다음 문을 추가합니다.

```python
# Start the main method
main()
```

## <a name="full-script"></a>전체 스크립트

전체 Python 코드는 다음과 같습니다.

```python
    
    from azure.identity import ClientSecretCredential 
    from azure.mgmt.resource import ResourceManagementClient
    from azure.mgmt.purview import PurviewManagementClient
    from azure.mgmt.purview.models import *
    from datetime import datetime, timedelta
    import time
    
     # Azure subscription ID
    subscription_id = '<subscription ID>'
    
    # This program creates this resource group. If it's an existing resource group, comment out the code that creates the resource group
    rg_name = '<resource group>'

    # The purview name. It must be globally unique.
    purview_name = '<purview account name>'

    # Specify your Active Directory client ID, client secret, and tenant ID
    credentials = ClientSecretCredential(client_id='<service principal ID>', client_secret='<service principal key>', tenant_id='<tenant ID>') 
    # resource_client = ResourceManagementClient(credentials, subscription_id)
    purview_client = PurviewManagementClient(credentials, subscription_id)
    
    # create the resource group
    # comment out if the resource group already exits
    resource_client.resource_groups.create_or_update(rg_name, rg_params)

    #Create a purview
    identity = Identity(type= "SystemAssigned")
    sku = AccountSku(name= 'Standard', capacity= 4)
    purview_resource = Account(identity=identity,sku=sku,location ="southcentralus" )
       
    try:
        pa = (purview_client.accounts.begin_create_or_update(rg_name, purview_name, purview_resource)).result()
        print("location:", pa.location, " Purview Account Name: ", purview_name, " Id: " , pa.id ," tags: " , pa.tags) 
    except:
        print("Error in submitting job to create account")
        print_item(pa)
 
    while (getattr(pa,'provisioning_state')) != "Succeeded" :
        pa = (purview_client.accounts.get(rg_name, purview_name))  
        print(getattr(pa,'provisioning_state'))
        if getattr(pa,'provisioning_state') != "Failed" :
            print("Error in creating Purview account")
            break
        time.sleep(30)    

# Start the main method
main()
```

## <a name="run-the-code"></a>코드 실행

애플리케이션을 빌드하고 시작한 다음, 파이프라인 실행을 확인합니다.

콘솔에서 데이터 팩터리, 연결된 서비스, 데이터 세트, 파이프라인 및 파이프라인 실행 만들기에 대한 진행 상황을 출력합니다. 데이터를 읽고/쓴 크기가 있는 복사 작업 실행 세부 정보가 표시될 때까지 기다립니다. 그런 다음, [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/)와 같은 도구를 사용하여 변수에 지정한 대로 Blob이 "inputBlobPath"에서 "outputBlobPath"로 복사되었는지 확인합니다.

샘플 출력은 다음과 같습니다.

```console
location: southcentralus  Purview Account Name:  purviewpython7  Id:  /subscriptions/8c2c7b23-848d-40fe-b817-690d79ad9dfd/resourceGroups/Demo_Catalog/providers/Microsoft.Purview/accounts/purviewpython7  tags:  None
Creating
Creating
Succeeded
```

## <a name="verify-the-output"></a>출력 확인

Azure Portal에서 **Purview 계정** 페이지로 이동하고 위의 코드를 사용하여 만든 계정을 확인합니다. 

## <a name="delete-purview-account"></a>Purview 계정 삭제

purview 계정을 삭제하려면 프로그램에 다음 코드를 추가합니다.

```python
pa = purview_client.accounts.begin_delete(rg_name, purview_name).result()
```

## <a name="next-steps"></a>다음 단계

이 자습서의 코드는 purview 계정을 만들고 purview 계정을 삭제합니다. 이제 python SDK를 다운로드하고 Purview 계정에 대해 수행할 수 있는 다른 리소스 공급자 작업에 대해 알아볼 수 있습니다.

다음 문서로 계속 진행하여 사용자가 Azure Purview 계정에 액세스할 수 있도록 허용하는 방법을 알아보세요. 

> [!div class="nextstepaction"]
> [Azure Purview 계정에 사용자 추가](catalog-permissions.md)
