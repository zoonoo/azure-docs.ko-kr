---
title: Azure CLI를 사용하여 Log Analytics 작업 영역 만들기 | Microsoft Docs
description: Azure CLI를 사용하여 온-프레미스 환경에서 관리 솔루션 및 데이터 수집이 가능하도록 Log Analytics 작업 영역을 만드는 방법에 대해 알아봅니다.
services: log-analytics
documentationcenter: log-analytics
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptal
ms.date: 10/02/2018
ms.author: magoedte
ms.component: ''
ms.openlocfilehash: 2d3d48db5a1eb329d854d1c062d23dfaf63ad9fa
ms.sourcegitcommit: ada7419db9d03de550fbadf2f2bb2670c95cdb21
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2018
ms.locfileid: "50963135"
---
# <a name="create-a-log-analytics-workspace-with-azure-cli-20"></a>Azure CLI 2.0으로 Log Analytics 작업 영역 만들기

명령줄 또는 스크립트에서 Azure 리소스를 만들고 관리하는 데 Azure CLI 2.0이 사용됩니다. 이 빠른 시작에서는 Azure CLI 2.0을 사용하여 Azure에 Log Analytics 작업 영역을 배포하는 방법을 보여줍니다. 이것은 자체 데이터 리포지토리, 데이터 원본 및 솔루션이 있는 고유한 환경입니다.  이 문서에 설명된 단계는 다음 원본에서 데이터를 수집하려는 경우에 필요합니다.

* 구독의 Azure 리소스  
* System Center Operations Manager에서 모니터링하는 온-프레미스 컴퓨터  
* System Center Configuration Manager의 데이터 수집  
* Azure Storage에서 진단 또는 로그 데이터  
 
Azure VM 및 사용자 환경의 Windows 또는 Linux VM 등 다른 소스의 경우 다음 항목을 참조하세요.

* [Azure 가상 머신에서 데이터 수집](log-analytics-quick-collect-azurevm.md)
* [하이브리드 Linux 컴퓨터에서 데이터 수집](log-analytics-quick-collect-linux-computer.md)
* [하이브리드 Windows 컴퓨터에서 데이터 수집](log-analytics-quick-collect-windows-computer.md)

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 을 만듭니다.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

CLI를 로컬로 설치하여 사용하도록 선택한 경우 이 빠른 시작에서는 Azure CLI 버전 2.0.30 이상을 실행해야 합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 2.0 설치](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)를 참조하세요.

## <a name="create-a-workspace"></a>작업 영역 만들기
[az group deployment create](https://docs.microsoft.com/cli/azure/group/deployment?view=azure-cli-latest#az-group-deployment-create)를 사용하여 작업 영역을 만듭니다. 다음 예제에서는 로컬 머신의 Resource Manager 템플릿을 사용하여 *eastus* 위치의 리소스 그룹 *Lab*에 *TestWorkspace*라는 작업 영역을 생성합니다. JSON 템플릿은 작업 영역의 이름만 사용자에게 입력을 요청하도록 구성되며, 환경에서 표준 구성으로 사용될수 있는 다른 매개 변수에 대해서는 기본값을 지정합니다. 또는 조직에서 공유 액세스에 대한 Azure 저장소 계정에 템플릿을 저장할 수 있습니다. 템플릿 작업에 대한 자세한 내용은 [Azure Resource Manager 템플릿과 Azure CLI를 사용하여 리소스 배포](../azure-resource-manager/resource-group-template-deploy-cli.md)를 참조하세요.

다음 매개 변수는 기본값을 설정합니다.

* 위치 - 기본값은 미국 동부
* sku - 2018년 4월 가격 책정 모델에서 배포된 새로운 GB당 가격 책정 계층이 기본값

>[!WARNING]
>새 2018년 4월 가격 책정 모델을 선택한 구독에서 Log Analytics 작업 영역을 만들거나 구성할 때 유효한 유일한 Log Analytics 가격 책정 계층은 **PerGB2018**입니다. 
>

### <a name="create-and-deploy-template"></a>템플릿 만들기 및 배포

1. 다음 JSON 구문을 파일에 복사하여 붙여넣습니다.

    ```json
    {
    "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "workspaceName": {
            "type": "String",
            "metadata": {
              "description": "Specifies the name of the workspace."
            }
        },
        "location": {
            "type": "String",
            "allowedValues": [
              "eastus",
              "westus"
            ],
            "defaultValue": "eastus",
            "metadata": {
              "description": "Specifies the location in which to create the workspace."
            }
        },
        "sku": {
            "type": "String",
            "allowedValues": [
              "Standalone",
              "PerNode",
              "PerGB2018"
            ],
            "defaultValue": "PerGB2018",
            "metadata": {
            "description": "Specifies the service tier of the workspace: Standalone, PerNode, Per-GB"
        }
          }
    },
    "resources": [
        {
            "type": "Microsoft.OperationalInsights/workspaces",
            "name": "[parameters('workspaceName')]",
            "apiVersion": "2017-03-15-preview",
            "location": "[parameters('location')]",
            "properties": {
                "sku": {
                    "Name": "[parameters('sku')]"
                },
                "features": {
                    "searchVersion": 1
                }
            }
          }
       ]
    }
    ```

2. 요구 사항을 충족하도록 템플릿을 편집합니다.  지원되는 속성 및 값은 [Microsoft.OperationalInsights/workspaces 템플릿](https://docs.microsoft.com/azure/templates/microsoft.operationalinsights/workspaces) 참조를 검토하세요. 
3. 이 파일을 로컬 폴더에 **deploylaworkspacetemplate.json**으로 저장합니다.   
4. 이제 이 템플릿을 배포할 수 있습니다. 템플릿이 포함된 폴더에서 다음 명령을 사용합니다.

    ```azurecli
    azure group deployment create --resource-group <my-resource-group> --name <my-deployment-name> --template-file deploylaworkspacetemplate.json
    ```

배포가 완료될 때까지 몇 분 정도 걸릴 수 있습니다. 완료되면 다음과 유사하게 결과가 포함된 메시지가 표시됩니다.

![배포가 완료되었을 때 결과 예](media/log-analytics-quick-create-workspace-cli/template-output-01.png)

## <a name="next-steps"></a>다음 단계
이제 사용 가능한 작업 영역이 있으므로 모니터링 원격 분석의 컬렉션을 구성하고, 해당 데이터를 분석하는 로그 검색을 실행하고, 추가 데이터 및 분석 정보를 제공하는 관리 솔루션을 추가할 수 있습니다.  

* Azure 진단 또는 Azure Storage를 사용하여 Azure 리소스의 데이터 수집을 사용하려면 [Log Analytics에서 사용할 Azure 서비스 로그 및 메트릭 수집](log-analytics-azure-storage.md)을 참조하세요.  
* [System Center Operations Manager를 데이터 원본으로 추가](log-analytics-om-agents.md)하여 Operations Manager 관리 그룹을 보고하는 에이전트에서 데이터를 수집하고 Log Analytics 작업 영역에 저장합니다.  
* [Configuration Manager](log-analytics-sccm.md)를 연결하여 계층에서 컬렉션의 멤버인 컴퓨터를 가져옵니다.  
* [관리 솔루션](../monitoring/monitoring-solutions.md)을 사용할 수 있는지 및 작업 영역에서 솔루션을 추가하거나 제거하는 방법을 검토합니다.
