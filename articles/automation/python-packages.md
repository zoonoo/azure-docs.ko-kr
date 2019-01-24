---
title: Azure Automation에서 Python 2 패키지 관리
description: 이 문서에서는 Azure Automation에서 Python 2 패키지를 관리하는 방법을 설명합니다.
services: automation
ms.service: automation
ms.subservice: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 09/11/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: de0998dffeac54db5311bbcde1c9499488b23556
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/22/2019
ms.locfileid: "54434975"
---
# <a name="manage-python-2-packages-in-azure-automation"></a>Azure Automation에서 Python 2 패키지 관리

Azure Automation을 사용하면 Linux Hybrid Runbook Worker 및 Azure에서 Python 2 Runbook을 실행할 수 있습니다. Runbook을 간편하게 실행하기 위해 Python 패키지를 사용하여 필요한 모듈을 가져올 수 있습니다. 이 문서에서는 Azure Automation에서 Python 패키지를 관리하고 사용하는 방법을 설명합니다.

## <a name="import-packages"></a>패키지 가져오기

Automation 계정의 **공유 리소스** 아래에서 **Python 2 패키지**를 선택합니다. **+ Python 2 패키지 추가**를 클릭합니다.

![Python 패키지 추가](media/python-packages/add-python-package.png)

**Python 2 패키지 추가** 페이지에서 업로드할 로컬 패키지를 선택합니다. 패키지는 `.whl` 파일 또는 `.tar.gz` 파일일 수 있습니다. 패키지를 선택한 다음 **확인**을 클릭하여 업로드합니다.

![Python 패키지 추가](media/python-packages/upload-package.png)

가져온 패키지는 Automation 계정의 **Python 2 패키지** 페이지에 나열됩니다. 패키지를 제거해야 하는 경우 해당 패키지를 선택하고 패키지 페이지에서 **삭제**를 선택합니다.

![패키지 목록](media/python-packages/package-list.png)

## <a name="use-a-package-in-a-runbook"></a>Runbook에서 패키지 사용

가져온 패키지는 Runbook에서 사용할 수 있습니다. 다음 예제에서는 [ Azure Automation 유틸리티 패키지](https://github.com/azureautomation/azure_automation_utility)를 사용합니다. 이 패키지가 있으면 Azure Automation에서 Python을 더욱 쉽게 사용할 수 있습니다. 이 패키지를 사용하려면 GitHub 리포지토리의 지침에 따라 `from azure_automation_utility import get_automation_runas_credential` 등의 명령(실행 계정을 검색하는 함수를 가져오는 명령)을 사용하여 Runbook에 패키지를 추가합니다.

```python
import azure.mgmt.resource
import automationassets
from azure_automation_utility import get_automation_runas_credential

# Authenticate to Azure using the Azure Automation RunAs service principal
runas_connection = automationassets.get_automation_connection("AzureRunAsConnection")
azure_credential = get_automation_runas_credential()

# Intialize the resource management client with the RunAs credential and subscription
resource_client = azure.mgmt.resource.ResourceManagementClient(
    azure_credential,
    str(runas_connection["SubscriptionId"]))

# Get list of resource groups and print them out
groups = resource_client.resource_groups.list()
for group in groups:
    print group.name
```

## <a name="develop-and-test-runbooks-offline"></a>오프라인에서 Runbook 개발 및 테스트

오프라인에서 Python 2 Runbook을 개발하고 테스트하려는 경우 GitHub의 [Azure Automation python 에뮬레이트된 자산](https://github.com/azureautomation/python_emulated_assets) 모듈을 사용할 수 있습니다. 이 모듈에서는 자격 증명, 변수, 연결, 인증서 등의 공유 리소스를 참조할 수 있습니다.

## <a name="next-steps"></a>다음 단계

Python 2 Runbook 사용을 시작하려면 [내 첫 번째 Python 2 Runbook](automation-first-runbook-textual-python2.md)을 참조하세요.
