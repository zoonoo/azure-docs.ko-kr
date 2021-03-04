---
title: Azure Automation에서 Python 3 패키지 관리
description: 이 문서에서는 Azure Automation에서 Python 3 패키지 (미리 보기)를 관리 하는 방법을 설명 합니다.
services: automation
ms.subservice: process-automation
ms.date: 02/19/2021
ms.topic: conceptual
ms.openlocfilehash: fd4d8ee92b670bc2544619a0dce16a26d9342c13
ms.sourcegitcommit: dac05f662ac353c1c7c5294399fca2a99b4f89c8
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102122037"
---
# <a name="manage-python-3-packages-preview-in-azure-automation"></a>Azure Automation에서 Python 3 패키지 (미리 보기) 관리

Azure Automation를 사용 하면 Azure 및 Linux Hybrid Runbook Worker에서 Python 3 runbook (미리 보기)을 실행할 수 있습니다. Runbook을 간편하게 실행하기 위해 Python 패키지를 사용하여 필요한 모듈을 가져올 수 있습니다. 단일 패키지를 가져오려면 [패키지 가져오기](#import-a-package)를 참조 하세요. 여러 패키지가 포함 된 패키지를 가져오려면 종속성이 [있는 패키지 가져오기](#import-a-package-with-dependencies)를 참조 하세요. 이 문서에서는 Azure Automation에서 Python 3 패키지 (미리 보기)를 관리 하 고 사용 하는 방법을 설명 합니다.

## <a name="import-a-package"></a>패키지 가져오기

Automation 계정에서 **공유 리소스** 아래에 있는 **Python 패키지** 를 선택 합니다. **+ Python 패키지 추가를** 선택 합니다.

:::image type="content" source="media/python-3-packages/add-python-3-package.png" alt-text="Python 3 패키지 페이지의 스크린샷 왼쪽 메뉴에 Python 3 패키지를 표시 하 고 Python 2 패키지를 강조 표시 합니다.":::

**Python 패키지 추가** 페이지에서 **버전** 에 대해 **python 3** 을 선택 하 고 업로드할 로컬 패키지를 선택 합니다. 패키지는 **.whl** 또는 **.tar.gz** 파일일 수 있습니다. 패키지를 선택 하는 경우 **확인** 을 선택 하 여 업로드 합니다.

:::image type="content" source="media/python-3-packages/upload-package.png" alt-text="스크린샷 release.tar.gz 파일이 선택 된 Python 3 패키지 추가 페이지가 표시 됩니다.":::

패키지를 가져온 후에는 **python 3 패키지 (미리 보기)** 탭의 Automation 계정에 있는 python 패키지 페이지에 나열 됩니다. 패키지를 제거 해야 하는 경우 패키지를 선택 하 고 **삭제** 를 클릭 합니다.

:::image type="content" source="media/python-3-packages/python-3-packages-list.png" alt-text="패키지를 가져온 후의 스크린샷에는 Python 3 패키지 페이지가 표시 됩니다.":::

### <a name="import-a-package-with-dependencies"></a>종속성이 있는 패키지 가져오기

다음 Python 스크립트를 Python 3 runbook으로 가져온 다음 실행 하 여 Python 3 패키지 및 해당 종속성을 가져올 수 있습니다.

```cmd
https://github.com/azureautomation/runbooks/blob/master/Utility/Python/import_py3package_from_pypi.py
```

#### <a name="importing-the-script-into-a-runbook"></a>Runbook으로 스크립트 가져오기
Runbook을 가져오는 방법에 대 한 자세한 내용은 [Azure Portal에서 Runbook 가져오기](manage-runbooks.md#import-a-runbook-from-the-azure-portal)를 참조 하세요. 가져오기를 실행 하기 전에 포털에서 액세스할 수 있는 저장소에 파일을 복사 합니다.

**Runbook 가져오기** 페이지는 스크립트 이름과 일치 하도록 runbook 이름을 기본값으로 설정 합니다. 필드에 대 한 액세스 권한이 있는 경우 이름을 변경할 수 있습니다. **Runbook 유형은** 기본적으로 **Python 2** 로 되어 있을 수 있습니다. 이 경우 **Python 3** 으로 변경 해야 합니다.

:::image type="content" source="media/python-3-packages/import-python-3-package.png" alt-text="스크린샷은 Python 3 runbook 가져오기 페이지를 표시 합니다.":::

#### <a name="executing-the-runbook-to-import-the-package-and-dependencies"></a>Runbook을 실행 하 여 패키지 및 종속성 가져오기

Runbook을 만들고 게시 한 후에 실행 하 여 패키지를 가져옵니다. Runbook을 실행 하는 방법에 대 한 자세한 내용은 [Azure Automation에서 Runbook 시작](start-runbooks.md) 을 참조 하세요.

스크립트 ()에는 `import_py3package_from_pypi.py` 다음 매개 변수가 필요 합니다.

| 매개 변수 | 설명 |
|---------------|-----------------|
|subscription_id | Automation 계정의 구독 ID |
| resource_group | Automation 계정이 정의 된 리소스 그룹의 이름입니다. |
| automation_account | Automation 계정 이름 |
| module_name | 가져올 모듈의 이름입니다. `pypi.org` |

Runbook에 매개 변수를 사용 하는 방법에 대 한 자세한 내용은 [runbook 매개 변수 작업](start-runbooks.md#work-with-runbook-parameters)을 참조 하세요.

## <a name="use-a-package-in-a-runbook"></a>Runbook에서 패키지 사용

가져온 패키지를 runbook에서 사용할 수 있습니다. Azure 구독의 모든 리소스 그룹을 나열 하는 다음 코드를 추가 합니다.

```python
import os  
import azure.mgmt.resource  
import automationassets  

def get_automation_runas_credential(runas_connection):  
    from OpenSSL import crypto  
    import binascii  
    from msrestazure import azure_active_directory  
    import adal 

    # Get the Azure Automation RunAs service principal certificate  
    cert = automationassets.get_automation_certificate("AzureRunAsCertificate")  
    pks12_cert = crypto.load_pkcs12(cert)  
    pem_pkey = crypto.dump_privatekey(crypto.FILETYPE_PEM,pks12_cert.get_privatekey())  

    # Get run as connection information for the Azure Automation service principal 
    application_id = runas_connection["ApplicationId"]  
    thumbprint = runas_connection["CertificateThumbprint"]  
    tenant_id = runas_connection["TenantId"]  

    # Authenticate with service principal certificate  
    resource ="https://management.core.windows.net/"  
    authority_url = ("https://login.microsoftonline.com/"+tenant_id)  
    context = adal.AuthenticationContext(authority_url)  
    return azure_active_directory.AdalAuthentication(  
    lambda: context.acquire_token_with_client_certificate(  
            resource,  
            application_id,  
            pem_pkey,  
            thumbprint) 
    ) 

# Authenticate to Azure using the Azure Automation RunAs service principal  
runas_connection = automationassets.get_automation_connection("AzureRunAsConnection")  
azure_credential = get_automation_runas_credential(runas_connection)  

# Intialize the resource management client with the RunAs credential and subscription  
resource_client = azure.mgmt.resource.ResourceManagementClient(  
    azure_credential,  
    str(runas_connection["SubscriptionId"]))  

# Get list of resource groups and print them out  
groups = resource_client.resource_groups.list()  
for group in groups:  
    print(group.name) 
```

## <a name="next-steps"></a>다음 단계

Python Runbook을 준비하려면 [Python Runbook 만들기](learn/automation-tutorial-runbook-textual-python-3.md)를 참조하세요.
