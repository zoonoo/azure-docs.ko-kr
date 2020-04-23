---
title: 전용 호스트에 배포
description: 전용 호스트를 사용하여 Azure 컨테이너 인스턴스 워크로드에 대한 진정한 호스트 수준 격리 달성
ms.topic: article
ms.date: 01/17/2020
author: dkkapur
ms.author: dekapur
ms.openlocfilehash: a614d6b5d0cf5c6c1df5ffcb90e56960d6b8a2a9
ms.sourcegitcommit: 75089113827229663afed75b8364ab5212d67323
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/22/2020
ms.locfileid: "82025036"
---
# <a name="deploy-on-dedicated-hosts"></a>전용 호스트에 배포

"전용"은 안전하게 실행되는 컨테이너를 위해 격리되고 전용된 계산 환경을 제공하는 Azure 컨테이너 인스턴스(ACI) sku입니다. 전용 sku를 사용하면 각 컨테이너 그룹에 Azure 데이터 센터에 전용 물리적 서버가 있으므로 조직의 보안 및 규정 준수 요구 사항을 충족하는 데 도움이 되는 전체 워크로드 격리가 보장됩니다. 

전용 sku는 물리적 서버 관점에서 워크로드 격리가 필요한 컨테이너 워크로드에 적합합니다.

## <a name="prerequisites"></a>사전 요구 사항

* 전용 sku를 사용하는 구독의 기본 제한은 0입니다. 프로덕션 컨테이너 배포에 이 sku를 사용하려면 Azure [Support 요청을][azure-support] 만들어 제한을 늘립니다.

## <a name="use-the-dedicated-sku"></a>전용 스쿠 사용

> [!IMPORTANT]
> 전용 sku를 사용하는 것은 현재 출시중인 최신 API 버전(2019-12-01)에서만 사용할 수 있습니다. 배포 템플릿에서 이 API 버전을 지정합니다.
>

API 버전 2019-12-01부터 ACI `sku` 배포에 필요한 배포 템플릿의 컨테이너 그룹 속성 섹션 아래에 속성이 있습니다. 현재 이 속성을 ACI에 대한 Azure 리소스 관리자 배포 템플릿의 일부로 사용할 수 있습니다. [자습서: 리소스 관리자 템플릿을 사용하여 다중 컨테이너 그룹을 배포하는 방법에](https://docs.microsoft.com/azure/container-instances/container-instances-multi-container-group)대해 자세히 알아보기 

속성은 `sku` 다음 값 중 하나를 가질 수 있습니다.
* `Standard`- 하이퍼바이저 수준의 보안을 보장하는 표준 ACI 배포 선택 
* `Dedicated`- 컨테이너 그룹에 대한 전용 물리적 호스트와 워크로드 수준 격리에 사용

## <a name="modify-your-json-deployment-template"></a>JSON 배포 템플릿 수정

배포 템플릿에서 다음 속성을 수정하거나 추가합니다.
* 에서 `resources`으로 `apiVersion` `2019-12-01`설정합니다.
* 컨테이너 그룹 속성 아래에 `sku` 값이 `Dedicated`있는 속성을 추가합니다.

다음은 전용 sku를 사용하는 컨테이너 그룹 배포 템플릿의 리소스 섹션에 대한 예제 코드 입니다.

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

데스크톱에서 배포 템플릿 파일을 만들고 편집한 경우 파일을 끌어서 Cloud Shell 디렉터리에 업로드할 수 있습니다. 

[az group create][az-group-create] 명령을 사용하여 리소스 그룹을 만듭니다.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

[az group deployment create][az-group-deployment-create] 명령을 사용하여 템플릿을 배포합니다.

```azurecli-interactive
az group deployment create --resource-group myResourceGroup --template-file deployment-template.json
```

몇 초 정도 지나면 Azure에서 초기 응답이 수신됩니다. 성공적인 배포는 전용 호스트에서 이루어집니다.

<!-- LINKS - Internal -->
[az-group-create]: /cli/azure/group#az-group-create
[az-group-deployment-create]: /cli/azure/group/deployment#az-group-deployment-create

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
