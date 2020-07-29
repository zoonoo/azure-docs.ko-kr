---
title: Init 컨테이너 실행
description: Azure Container Instances에서 init 컨테이너를 실행 하 여 응용 프로그램 컨테이너가 실행 되기 전에 컨테이너 그룹에서 설치 작업을 수행할 수 있습니다.
ms.topic: article
ms.date: 06/01/2020
ms.openlocfilehash: 5a729263ee632eb9227694ec8684eb6889c6324b
ms.sourcegitcommit: 845a55e6c391c79d2c1585ac1625ea7dc953ea89
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/05/2020
ms.locfileid: "85954284"
---
# <a name="run-an-init-container-for-setup-tasks-in-a-container-group"></a>컨테이너 그룹의 설치 작업에 대해 init 컨테이너 실행

Azure Container Instances는 컨테이너 그룹의 *init 컨테이너* 를 지원 합니다. 응용 프로그램 컨테이너 또는 컨테이너를 시작 하기 전에 완료 될 때까지 컨테이너를 초기화 합니다. [Kubernetes init 컨테이너](https://kubernetes.io/docs/concepts/workloads/pods/init-containers/)와 마찬가지로 하나 이상의 init 컨테이너를 사용 하 여 계정 설정, 설치 스크립트 실행 또는 데이터베이스 구성과 같은 앱 컨테이너에 대 한 초기화 논리를 수행 합니다.

이 문서에서는 Azure Resource Manager 템플릿을 사용 하 여 init 컨테이너를 사용 하 여 컨테이너 그룹을 구성 하는 방법을 보여 줍니다.

## <a name="things-to-know"></a>알아야 할 사항

* **Api 버전** -init 컨테이너를 배포 하려면 Azure Container Instances api 버전 2019-12-01 이상이 필요 합니다. `initContainers` [Yaml 파일](container-instances-multi-container-yaml.md) 또는 [리소스 관리자 템플릿에서](container-instances-multi-container-group.md)속성을 사용 하 여 배포 합니다.
* **실행 순서** -Init 컨테이너는 템플릿에 지정 된 순서에 따라 실행 되 고 다른 컨테이너 앞에 실행 됩니다. 기본적으로 컨테이너 그룹당 최대 59 개의 init 컨테이너를 지정할 수 있습니다. 하나 이상의 비 init 컨테이너가 그룹에 있어야 합니다.
* **호스트 환경** -Init 컨테이너는 그룹의 나머지 컨테이너와 동일한 하드웨어에서 실행 됩니다.
* **리소스** -init 컨테이너에 대 한 리소스를 지정 하지 않습니다. 여기에는 Cpu 및 컨테이너 그룹에 사용할 수 있는 메모리와 같은 총 리소스가 부여 됩니다. Init 컨테이너가 실행 되는 동안에는 다른 컨테이너가 그룹에서 실행 되지 않습니다.
* **지원 되는 속성** -Init 컨테이너는 볼륨, 암호 및 관리 되는 id와 같은 그룹 속성을 사용할 수 있습니다. 그러나 컨테이너 그룹에 대해 구성 된 경우에는 포트 또는 IP 주소를 사용할 수 없습니다. 
* **다시 시작 정책** -그룹의 다음 컨테이너가 시작 되기 전에 각 init 컨테이너가 성공적으로 종료 되어야 합니다. Init 컨테이너가 성공적으로 종료 되지 않으면 다시 시작 작업은 그룹에 대해 구성 된 [다시 시작 정책](container-instances-restart-policy.md) 에 따라 달라 집니다.

    |그룹의 정책  |Init의 정책  |
    |---------|---------|
    |항상     |OnFailure         |
    |OnFailure     |OnFailure         |
    |Never     |Never         |
* **요금** -컨테이너 그룹은 init 컨테이너의 첫 번째 배포에서 요금이 발생 합니다.

## <a name="resource-manager-template-example"></a>Resource Manager 템플릿 예제

먼저 다음 JSON을 `azuredeploy.json`이라는 새 파일에 복사합니다. 템플릿은 하나의 init 컨테이너와 두 개의 응용 프로그램 컨테이너를 사용 하 여 컨테이너 그룹을 설정 합니다.

* *Init1* 컨테이너는 Docker 허브에서 [busybox](https://hub.docker.com/_/busybox) 이미지를 실행 합니다. 60 초 동안 대기한 다음 [emptyDir 볼륨](container-instances-volume-emptydir.md)의 파일에 명령줄 문자열을 씁니다.
* 두 응용 프로그램 컨테이너 모두 Microsoft `aci-wordcount` 컨테이너 이미지를 실행 합니다.
    * *Hamlet* 컨테이너는 셰익스피어의 play *hamlet*에서 단어 주파수를 계산 하는 기본 구성으로 wordcount 앱을 실행 합니다.
    * *Juliet* 앱 컨테이너는 셰익스피어의 *Romeo 및 juliet*대신 wordcount 앱을 실행 하기 위해 emptDir 볼륨에서 명령줄 문자열을 읽습니다.

이미지 사용에 대 한 자세한 내용 및 예제는 `aci-wordcount` [컨테이너 인스턴스에서 환경 변수 설정](container-instances-environment-variables.md)을 참조 하세요.

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
                "sku": "Standard",
                "initContainers": [
                {
                    "name": "init1",
                    "properties": {
                        "image": "busybox",
                        "environmentVariables": [],
                        "volumeMounts": [
                            {
                                "name": "emptydir1",
                                "mountPath": "/mnt/emptydir"
                            }
                        ],
                         "command": [
                            "/bin/sh",
                            "-c",
                            "sleep 60; echo python wordcount.py http://shakespeare.mit.edu/romeo_juliet/full.html > /mnt/emptydir/command_line.txt"
                        ]
                    }
                }
            ], 
                "containers": [
                    {
                        "name": "hamlet",
                        "properties": {
                            "image": "mcr.microsoft.com/azuredocs/aci-wordcount",
                            "volumeMounts": [
                            {
                                "name": "emptydir1",
                                "mountPath": "/mnt/emptydir"
                            }
                        ],
                            "environmentVariables": [
                             {
                                "name": "NumWords",
                                "value": "3"
                             },
                             {  "name": "MinLength",
                                "value": "5"
                             }
                            ],
                            "resources": {
                                "requests": {
                                    "memoryInGB": 1.0,
                                    "cpu": 1
                                }
                            }
                        }
                    },
                    {
                        "name": "juliet",
                        "properties": {
                            "image": "mcr.microsoft.com/azuredocs/aci-wordcount",
                            "volumeMounts": [
                            {
                                "name": "emptydir1",
                                "mountPath": "/mnt/emptydir"
                            }
                            ],
                            "command": [
                                "/bin/sh",
                                "-c",
                                "$(cat /mnt/emptydir/command_line.txt)"
                            ],
                            "environmentVariables": [
                             {
                                "name": "NumWords",
                                "value": "3"
                             },
                             {  "name": "MinLength",
                                "value": "5"
                             }
                            ],
                            "resources": {
                                "requests": {
                                    "memoryInGB": 1.0,
                                    "cpu": 1
                                }
                            }
                        }
                    }
                ],
                "restartPolicy": "OnFailure",
                "osType": "Linux",
                "volumes": [
                    {
                        "name": "emptydir1",
                        "emptyDir": {}
                    }
                ]           
            },
            "tags": {}
        }
    ]
}
```

## <a name="deploy-the-template"></a>템플릿 배포

[az group create][az-group-create] 명령을 사용하여 리소스 그룹을 만듭니다.

```azurecli
az group create --name myResourceGroup --location eastus
```

[Az deployment group create][az-deployment-group-create] 명령을 사용 하 여 템플릿을 배포 합니다.

```azurecli
az deployment group create \
  --resource-group myResourceGroup \
  --template-file azuredeploy.json
```

Init 컨테이너가 있는 그룹에서 init 컨테이너 또는 컨테이너를 완료 하는 데 걸리는 시간으로 인해 배포 시간이 증가 합니다.


## <a name="view-container-logs"></a>컨테이너 로그 보기

Init 컨테이너가 성공적으로 실행 되었는지 확인 하려면 [az container logs][az-container-logs] 명령을 사용 하 여 앱 컨테이너의 로그 출력을 확인 합니다. `--container-name` 인수는 로그를 가져올 컨테이너를 지정합니다. 이 예제에서는 다양 한 명령 출력을 표시 하는 *hamlet* 및 *juliet* 컨테이너의 로그를 가져옵니다.

```azurecli
az container logs \
  --resource-group myResourceGroup \
  --name myContainerGroup \
  --container-name hamlet
```

출력:

```console
[('HAMLET', 386), ('HORATIO', 127), ('CLAUDIUS', 120)]
```

```azurecli
az container logs \
  --resource-group myResourceGroup \
  --name myContainerGroup \
  --container-name juliet
```

출력:

```console
[('ROMEO', 177), ('JULIET', 134), ('CAPULET', 119)]
```

## <a name="next-steps"></a>다음 단계

초기화 컨테이너는 응용 프로그램 컨테이너에 대 한 설치 및 초기화 작업을 수행 하는 데 도움이 됩니다. 작업 기반 컨테이너를 실행 하는 방법에 대 한 자세한 내용은 [컨테이너 화 된 작업을 다시 시작 정책으로 실행](container-instances-restart-policy.md)을 참조 하세요.

Azure Container Instances는 응용 프로그램 컨테이너의 동작을 수정 하는 다른 옵션을 제공 합니다. 다음은 이러한 템플릿의 예입니다.

* [컨테이너 인스턴스에서 환경 변수 설정](container-instances-environment-variables.md)
* [컨테이너 인스턴스에서 명령줄을 설정 하 여 기본 명령줄 작업을 재정의 합니다.](container-instances-start-command.md)


[az-group-create]: /cli/azure/group#az-group-create
[az-deployment-group-create]: /cli/azure/deployment/group#az-deployment-group-create
[az-container-logs]: /cli/azure/container#az-container-logs
