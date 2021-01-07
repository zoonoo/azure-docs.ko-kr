---
title: 전용 호스트에 배포
description: 전용 호스트를 사용 하 여 Azure Container Instances 작업에 대 한 실제 호스트 수준 격리 얻기
ms.topic: article
ms.date: 01/17/2020
author: macolso
ms.author: macolso
ms.openlocfilehash: 967d2da61ffdfa9d1723bcab589deb2277d4041e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91825608"
---
# <a name="deploy-on-dedicated-hosts"></a>전용 호스트에 배포

"전용"은 컨테이너를 안전 하 게 실행 하기 위해 격리 된 전용 계산 환경을 제공 하는 ACI (Azure Container Instances) sku입니다. 전용 sku를 사용 하면 각 컨테이너 그룹에서 Azure 데이터 센터에 전용 물리적 서버가 있으므로 조직의 보안 및 규정 준수 요구 사항을 충족 하는 데 도움이 되는 전체 워크 로드 격리를 보장할 수 있습니다. 

전용 sku는 물리적 서버 관점에서 작업을 격리 해야 하는 컨테이너 작업에 적합 합니다.

## <a name="prerequisites"></a>필수 구성 요소

* 전용 sku를 사용 하는 모든 구독에 대 한 기본 제한은 0입니다. 프로덕션 컨테이너 배포에이 sku를 사용 하려는 경우 [Azure 지원 요청][azure-support] 를 만들어 제한을 늘립니다.

## <a name="use-the-dedicated-sku"></a>전용 sku 사용

> [!IMPORTANT]
> 전용 sku를 사용 하는 것은 현재 롤아웃 중인 최신 API 버전 (2019-12-01) 에서만 사용할 수 있습니다. 배포 템플릿에서이 API 버전을 지정 합니다.
>

API 버전 2019-12-01부터 `sku` ACI 배포에 필요한 배포 템플릿의 컨테이너 그룹 속성 섹션 아래에 속성이 있습니다. 현재이 속성은 ACI 용 Azure Resource Manager 배포 템플릿의 일부로 사용할 수 있습니다. [자습서: 리소스 관리자 템플릿을 사용 하 여 다중 컨테이너 그룹 배포](./container-instances-multi-container-group.md)에서 템플릿을 사용 하 여 ACI 리소스를 배포 하는 방법에 대해 자세히 알아보세요. 

`sku`속성은 다음 값 중 하나를 가질 수 있습니다.
* `Standard` -하이퍼바이저 수준 보안을 보장 하는 표준 ACI 배포 선택 
* `Dedicated` -컨테이너 그룹에 대 한 전용 물리적 호스트와 워크 로드 수준 격리에 사용 됨

## <a name="modify-your-json-deployment-template"></a>JSON 배포 템플릿 수정

배포 템플릿에서 다음 속성을 수정 하거나 추가 합니다.
* 에서을 `resources` `apiVersion` 로 설정 `2019-12-01` 합니다.
* 컨테이너 그룹 속성에서 `sku` value를 사용 하 여 속성을 추가 `Dedicated` 합니다.

다음은 전용 sku를 사용 하는 컨테이너 그룹 배포 템플릿의 리소스 섹션에 대 한 예제 코드 조각입니다.

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

다음은 단일 컨테이너 인스턴스를 실행 하는 샘플 컨테이너 그룹을 배포 하는 전체 템플릿입니다.

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

바탕 화면에서 배포 템플릿 파일을 만들고 편집한 경우에는 파일을 끌어서 Cloud Shell 디렉터리에 업로드할 수 있습니다. 

[az group create][az-group-create] 명령을 사용하여 리소스 그룹을 만듭니다.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

[Az deployment group create][az-deployment-group-create] 명령을 사용 하 여 템플릿을 배포 합니다.

```azurecli-interactive
az deployment group create --resource-group myResourceGroup --template-file deployment-template.json
```

몇 초 정도 지나면 Azure에서 초기 응답이 수신됩니다. 배포는 전용 호스트에서 수행 됩니다.

<!-- LINKS - Internal -->
[az-group-create]: /cli/azure/group#az-group-create
[az-deployment-group-create]: /cli/azure/deployment/group#az-deployment-group-create

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
