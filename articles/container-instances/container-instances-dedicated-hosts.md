---
title: 전용 호스트에 배포
description: 전용 호스트를 사용하여 Azure Container Instances 워크로드의 실질적인 호스트 수준 격리 실현
ms.topic: article
ms.date: 01/17/2020
author: macolso
ms.author: macolso
ms.openlocfilehash: 72ad05eea9232e7a0d6ac24d1d0d22a971a7f6a5
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107790836"
---
# <a name="deploy-on-dedicated-hosts"></a>전용 호스트에 배포

‘전용’은 컨테이너를 안전하게 실행하기 위한 격리된 전용 컴퓨팅 환경을 제공하는 ACI(Azure Container Instances) SKU입니다. 전용 SKU를 사용하면 각 컨테이너 그룹이 Azure 데이터 센터의 전용 물리적 서버를 갖게 되고 완전한 워크로드 격리가 보장되어 조직의 보안 및 규정 준수 요구 사항을 충족하는 데 도움이 됩니다. 

전용 SKU는 물리적 서버 관점에서 워크로드 격리가 필요한 컨테이너 워크로드에 적합합니다.

## <a name="prerequisites"></a>사전 요구 사항

> [!NOTE]
> 일부 현재 제한 사항 때문에 승인되지 않는 제한 증가 요청이 있을 수 있습니다.

* 모든 구독에서 전용 SKU를 사용하기 위한 기본 제한은 0입니다. 프로덕션 컨테이너 배포에 이 SKU를 사용하려면 [Azure 지원 요청][azure-support]을 만들어 제한을 늘리세요.

## <a name="use-the-dedicated-sku"></a>전용 SKU 사용

> [!IMPORTANT]
> 전용 SKU 사용은 현재 출시 중인 최신 API 버전(2019-12-01)에서만 사용할 수 있습니다. 배포 템플릿에 이 API 버전을 지정하세요.
>

API 버전 2019-12-01부터 ACI 배포에 필요한 배포 템플릿의 컨테이너 그룹 속성 섹션 아래에 `sku` 속성이 있습니다. 현재 이 속성은 ACI용 Azure Resource Manager 배포 템플릿의 일부로 사용할 수 있습니다. 템플릿을 사용하여 ACI 리소스를 배포하는 방법에 대한 자세한 내용은 [Tutorial: Deploy a multi-container group using a Resource Manager template](./container-instances-multi-container-group.md)(자습서: 에서 Resource Manager 템플릿을 사용하여 다중 컨테이너 그룹 배포)을 참조하세요. 

`sku` 속성 값은 다음 중 하나일 수 있습니다.
* `Standard` - 하이퍼바이저 수준 보안을 여전히 보장하는 표준 ACI 배포 선택 항목 
* `Dedicated` - 컨테이너 그룹에 전용 물리적 호스트를 사용하는 워크로드 수준 격리에 사용됨

## <a name="modify-your-json-deployment-template"></a>JSON 배포 템플릿 수정

배포 템플릿에서 다음 속성을 수정하거나 추가합니다.
* `resources` 아래에서 `apiVersion`를 `2019-12-01`로 설정합니다.
* 컨테이너 그룹 속성 아래에 `Dedicated` 값을 사용하여 `sku` 속성을 추가합니다.

다음은 전용 SKU를 사용하는 컨테이너 그룹 배포 템플릿의 리소스 섹션을 위한 예제 코드 조각입니다.

```json
[...]
"resources": [
    {
        "name": "[parameters('containerGroupName')]",
        "type": "Microsoft.ContainerInstance/containerGroups",
        "apiVersion": "2019-12-01",
        "location": "[resourceGroup().location]",    
        "properties": {
            "sku": "Dedicated",
            "containers": {
                [...]
            }
        }
    }
]
```

다음은 단일 컨테이너 인스턴스를 실행하는 샘플 컨테이너 그룹을 배포하는 전체 템플릿입니다.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "containerGroupName": {
        "type": "string",
        "defaultValue": "myContainerGroup",
        "metadata": {
          "description": "Container Group name."
        }
      }
    },
    "resources": [
        {
            "name": "[parameters('containerGroupName')]",
            "type": "Microsoft.ContainerInstance/containerGroups",
            "apiVersion": "2019-12-01",
            "location": "[resourceGroup().location]",
            "properties": {
                "sku": "Dedicated",
                "containers": [
                    {
                        "name": "container1",
                        "properties": {
                            "image": "nginx",
                            "command": [
                                "/bin/sh",
                                "-c",
                                "while true; do echo `date`; sleep 1000000; done"
                            ],
                            "ports": [
                                {
                                    "protocol": "TCP",
                                    "port": 80
                                }
                            ],
                            "environmentVariables": [],
                            "resources": {
                                "requests": {
                                    "memoryInGB": 1.0,
                                    "cpu": 1
                                }
                            }
                        }
                    }
                ],
                "restartPolicy": "Always",
                "ipAddress": {
                    "ports": [
                        {
                            "protocol": "TCP",
                            "port": 80
                        }
                    ],
                    "type": "Public"
                },
                "osType": "Linux"
            },
            "tags": {}
        }
    ]
}
```

## <a name="deploy-your-container-group"></a>컨테이너 그룹 배포

데스크톱에서 배포 템플릿 파일을 만들고 편집했으면 파일을 끌어서 Cloud Shell 디렉터리로 업로드할 수 있습니다. 

[az group create][az-group-create] 명령을 사용하여 리소스 그룹을 만듭니다.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

[az deployment group create][az-deployment-group-create] 명령을 사용하여 템플릿을 배포합니다.

```azurecli-interactive
az deployment group create --resource-group myResourceGroup --template-file deployment-template.json
```

몇 초 정도 지나면 Azure에서 초기 응답이 수신됩니다. 전용 호스트에서 성공적인 배포가 이루어집니다.

<!-- LINKS - Internal -->
[az-group-create]: /cli/azure/group#az_group_create
[az-deployment-group-create]: /cli/azure/deployment/group#az_deployment_group_create

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
