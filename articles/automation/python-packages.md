---
title: Azure Automation에서 Python 2 패키지 관리
description: 이 문서에서는 Azure Automation에서 Python 2 패키지를 관리하는 방법을 설명합니다.
services: automation
ms.subservice: process-automation
ms.date: 02/25/2019
ms.topic: conceptual
ms.openlocfilehash: 9f52dfd92d430abffe5857d231898dd4b0e7745e
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81679913"
---
# <a name="manage-python-2-packages-in-azure-automation"></a>Azure Automation에서 Python 2 패키지 관리

Azure Automation을 사용하면 Linux Hybrid Runbook Worker 및 Azure에서 Python 2 Runbook을 실행할 수 있습니다. Runbook을 간편하게 실행하기 위해 Python 패키지를 사용하여 필요한 모듈을 가져올 수 있습니다. 이 문서에서는 Azure Automation에서 Python 패키지를 관리하고 사용하는 방법에 대해 설명합니다.

## <a name="import-packages"></a>패키지 가져오기

자동화 계정에서 **공유 리소스**에서 Python **2 패키지를** 선택합니다. **+ Python 2 패키지 추가**를 클릭합니다.

![Python 패키지 추가](media/python-packages/add-python-package.png)

Python 2 패키지 추가 페이지에서 업로드할 로컬 패키지를 선택합니다. 패키지는 **.whl** 또는 **.tar.gz** 파일일 수 있습니다. 패키지를 선택하면 **확인을** 클릭하여 업로드합니다.

![Python 패키지 추가](media/python-packages/upload-package.png)

패키지를 가져오면 자동화 계정의 Python 2 패키지 페이지에 나열됩니다. 패키지를 제거해야 하는 경우 패키지를 선택하고 **삭제를**클릭합니다.

![패키지 목록](media/python-packages/package-list.png)

## <a name="import-packages-with-dependencies"></a>종속성이 있는 패키지 가져오기

Azure 자동화는 가져오기 프로세스 중에 파이썬 패키지에 대한 종속성을 해결하지 않습니다. 모든 종속성이 있는 패키지를 가져오는 방법에는 두 가지가 있습니다. 자동화 계정으로 패키지를 가져오려면 다음 단계 중 하나만 사용해야 합니다.

### <a name="manually-download"></a>수동으로 다운로드

[python2.7](https://www.python.org/downloads/release/latest/python2) 및 [pip가](https://pip.pypa.io/en/stable/) 설치된 Windows 64비트 컴퓨터에서 다음 명령을 실행하여 패키지 및 모든 종속성을 다운로드합니다.

```cmd
C:\Python27\Scripts\pip2.7.exe download -d <output dir> <package name>
```

패키지를 다운로드하면 자동화 계정으로 가져올 수 있습니다.

### <a name="runbook"></a>Runbook

[파이피에서 파이피의 파이썬 2 패키지를 갤러리의 Azure Automation 계정으로](https://gallery.technet.microsoft.com/scriptcenter/Import-Python-2-packages-57f7d509) 가져와 자동화 계정으로 가져옵니다. 실행 설정이 **Azure로** 설정되어 있는지 확인하고 매개 변수를 사용하여 Runbook을 시작합니다. Runbook에서 자동화 계정이 작동하려면 실행 계정으로 실행해야 합니다. 각 매개 변수에 대해 다음 목록 및 이미지에서 볼 수 있는 스위치로 시작하십시오.

* -s \<구독ID\>
* -g \<자원그룹\>
* \<-자동화계정\>
* -m \<모듈패키지\>

![패키지 목록](media/python-packages/import-python-runbook.png)

Runbook을 사용하면 다운로드할 패키지를 지정할 수 있습니다. 예를 들어 매개 `Azure` 변수를 사용하면 모든 Azure 모듈과 모든 종속성(약 105개)이 다운로드됩니다.

Runbook이 완료되면 자동화 계정의 **공유 리소스** 에서 Python **2 패키지를** 확인하여 패키지가 올바르게 가져왔는지 확인할 수 있습니다.

## <a name="use-a-package-in-a-runbook"></a>Runbook에서 패키지 사용

패키지를 가져온 경우 Runbook에서 사용할 수 있습니다. 다음 예제에서는 [Azure 자동화 유틸리티 패키지를](https://github.com/azureautomation/azure_automation_utility)사용합니다. 이 패키지가 있으면 Azure Automation에서 Python을 더욱 쉽게 사용할 수 있습니다. 패키지를 사용하려면 GitHub 리포지토리의 지침을 따르고 Runbook에 추가합니다. 예를 들어 Run `from azure_automation_utility import get_automation_runas_credential` As 계정을 검색하기 위한 함수를 가져오는 데 사용할 수 있습니다.

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

파이썬 2 런북으로 시작하려면 [내 첫 번째 파이썬 2 런북을](automation-first-runbook-textual-python2.md)참조하십시오.
