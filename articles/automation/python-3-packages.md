---
title: Azure Automation에서 Python 3 패키지 관리
description: 이 문서에서는 Azure Automation에서 Python 3 패키지 (미리 보기)를 관리 하는 방법을 설명 합니다.
services: automation
ms.subservice: process-automation
ms.date: 12/22/2020
ms.topic: conceptual
ms.openlocfilehash: 3f39f49ff47b935da7ffc777ee45bd219f5740b5
ms.sourcegitcommit: f7084d3d80c4bc8e69b9eb05dfd30e8e195994d8
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/22/2020
ms.locfileid: "97734304"
---
# <a name="manage-python-3-packages-preview-in-azure-automation"></a>Azure Automation에서 Python 3 패키지 (미리 보기) 관리

Azure Automation를 사용 하면 Azure 및 Linux Hybrid Runbook Worker에서 Python 3 runbook (미리 보기)을 실행할 수 있습니다. Runbook을 간편하게 실행하기 위해 Python 패키지를 사용하여 필요한 모듈을 가져올 수 있습니다. 이 문서에서는 Azure Automation에서 Python 3 패키지 (미리 보기)를 관리 하 고 사용 하는 방법을 설명 합니다.

## <a name="import-packages"></a>패키지 가져오기

Automation 계정에서 **공유 리소스** 아래에 있는 **Python 패키지** 를 선택 합니다. **+ Python 패키지 추가를** 선택 합니다.

:::image type="content" source="media/python-3-packages/add-python-3-package.png" alt-text="Python 3 패키지 페이지의 스크린샷 왼쪽 메뉴에 Python 3 패키지를 표시 하 고 Python 2 패키지를 강조 표시 합니다.":::

**Python 패키지 추가** 페이지에서 **버전** 에 대해 python 3을 선택 하 고 업로드할 로컬 패키지를 선택 합니다. 패키지는 **.whl** 또는 **.tar.gz** 파일일 수 있습니다. 패키지를 선택 하는 경우 **확인** 을 선택 하 여 업로드 합니다.

:::image type="content" source="media/python-3-packages/upload-package.png" alt-text="스크린샷 release.tar.gz 파일이 선택 된 Python 3 패키지 추가 페이지가 표시 됩니다.":::

패키지를 가져온 후에는 **python 3 패키지 (미리 보기)** 탭의 Automation 계정에 있는 python 패키지 페이지에 나열 됩니다. 패키지를 제거 해야 하는 경우 패키지를 선택 하 고 **삭제** 를 클릭 합니다.

:::image type="content" source="media/python-3-packages/python-3-packages-list.png" alt-text="패키지를 가져온 후의 스크린샷에는 Python 3 패키지 페이지가 표시 됩니다.":::

## <a name="import-packages-with-dependencies"></a>종속성이 있는 패키지 가져오기

가져오기 프로세스 중에 Azure Automation는 Python 패키지에 대 한 종속성을 확인 하지 않습니다. 그러나 모든 종속성이 포함 된 패키지를 가져오는 방법이 있습니다.

### <a name="manually-download"></a>수동 다운로드

[Python 3.8](https://www.python.org/downloads/release/python-380/) 및 [pip](https://pip.pypa.io/en/stable/) 가 설치 된 Windows 64 비트 컴퓨터에서 다음 명령을 실행 하 여 패키지 및 해당 종속성을 모두 다운로드 합니다.

```cmd
C:\Python38\Scripts\pip3.8.exe download -d <output dir> <package name>
```

패키지가 다운로드 되 면 Automation 계정으로 가져올 수 있습니다.

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
