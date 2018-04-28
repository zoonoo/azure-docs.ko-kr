---
title: Azure Container Instances에서 환경 변수 설정
description: Azure Container Instances에서 환경 변수를 설정하는 방법 알아보기
services: container-instances
author: david-stanford
manager: jeconnoc
ms.service: container-instances
ms.topic: article
ms.date: 03/13/2018
ms.author: dastanfo
ms.openlocfilehash: 37fde41b6dc2ea0a4d3b4b38a0e3df81a297c125
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2018
---
# <a name="set-environment-variables"></a>환경 변수 설정

컨테이너 인스턴스에서 환경 변수를 설정하면 컨테이너가 실행하는 응용 프로그램 또는 스크립트의 동적 구성을 제공할 수 있습니다.

현재 CLI 및 PowerShell에서 환경 변수를 설정할 수 있습니다. 두 경우 모두 명령에 대한 플래그를 사용하여 환경 변수를 설정합니다. ACI에서 환경 변수를 설정하는 것은 `--env` 명령줄 인수를 `docker run`으로 설정하는 것과 비슷합니다. 예를 들어 microsoft/aci-wordcount 컨테이너 이미지를 사용하는 경우 다음 환경 변수를 지정하여 동작을 수정할 수 있습니다.

*NumWords*: STDOUT으로 전송된 단어 수입니다.

*MinLength*: 계산되는 단어의 최소 문자 수입니다. 숫자가 높을수록 "of" 및 "the"와 같은 일반적인 단어를 무시합니다.

## <a name="azure-cli-example"></a>Azure CLI 예제

컨테이너의 기본 출력을 보려면 다음 명령을 실행합니다.

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer1 \
    --image microsoft/aci-wordcount:latest \
    --restart-policy OnFailure
```

컨테이너의 환경 변수에 `NumWords=5` 및 `MinLength=8`을 지정하면 컨테이너 로그에 다른 출력이 표시됩니다.

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer2 \
    --image microsoft/aci-wordcount:latest \
    --restart-policy OnFailure \
    --environment-variables NumWords=5 MinLength=8
```

컨테이너 상태가 *종료됨*으로 표시되면(상태를 확인하려면 [az container show][az-container-show] 사용) 로그를 표시하여 출력을 확인합니다.  환경 변수 없이 컨테이너의 출력을 보려면 --name을 mycontainer2 대신 mycontainer1으로 설정합니다.

```azurecli-interactive
az container logs --resource-group myResourceGroup --name mycontainer2
```

## <a name="azure-powershell-example"></a>Azure PowerShell 예제

컨테이너의 기본 출력을 보려면 다음 명령을 실행합니다.

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer1 \
    --image microsoft/aci-wordcount:latest \
    --restart-policy OnFailure
```

컨테이너의 환경 변수에 `NumWords=5` 및 `MinLength=8`을 지정하면 컨테이너 로그에 다른 출력이 표시됩니다.

```azurepowershell-interactive
$envVars = @{NumWord=5;MinLength=8}
New-AzureRmContainerGroup `
    -ResourceGroupName myResourceGroup `
    -Name mycontainer2 `
    -Image microsoft/aci-wordcount:latest `
    -RestartPolicy OnFailure `
    -EnvironmentVariable $envVars
```

컨테이너 상태가 *종료됨*으로 표시되면(상태를 확인하려면 [Get-AzureRmContainerInstanceLog][azure-instance-log] 사용) 로그를 표시하여 출력을 확인합니다. 환경 변수 없이 컨테이너 로그를 보려면 ContainerGroupName을 mycontainer2 대신 mycontainer1으로 설정합니다.

```azurepowershell-interactive
Get-AzureRmContainerInstanceLog `
    -ResourceGroupName myResourceGroup `
    -ContainerGroupName mycontainer2
```

## <a name="example-output-without-environment-variables"></a>환경 변수 없는 예제 출력

```bash
[('the', 990),
 ('and', 702),
 ('of', 628),
 ('to', 610),
 ('I', 544),
 ('you', 495),
 ('a', 453),
 ('my', 441),
 ('in', 399),
 ('HAMLET', 386)]
```

## <a name="example-output-with-environment-variables"></a>환경 변수가 있는 예제 출력

```bash
[('CLAUDIUS', 120),
 ('POLONIUS', 113),
 ('GERTRUDE', 82),
 ('ROSENCRANTZ', 69),
 ('GUILDENSTERN', 54)]
```

## <a name="next-steps"></a>다음 단계

컨테이너의 입력을 사용자 지정하는 방법을 알아보았으니, 이번에는 완료될 때까지 실행되는 컨테이너 출력을 유지하는 방법을 알아보세요.
> [!div class="nextstepaction"]
> [Azure Container Instances를 사용하여 Azure 파일 공유 탑재](container-instances-mounting-azure-files-volume.md)

<!-- LINKS Internal -->
[azure-cloud-shell]: ../cloud-shell/overview.md
[azure-cli-install]: /cli/azure/
[azure-powershell-install]: /powershell/azure/install-azurerm-ps
[azure-instance-log]: /powershell/module/azurerm.containerinstance/get-azurermcontainerinstancelog
[az-container-show]: /cli/azure/container?view=azure-cli-latest#az_container_show