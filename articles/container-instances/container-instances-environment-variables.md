---
title: Azure Container Instances에서 환경 변수 설정
description: Azure Container Instances에서 실행하는 컨테이너에서 환경 변수를 설정하는 방법 알아보기
services: container-instances
author: mmacy
manager: jeconnoc
ms.service: container-instances
ms.topic: article
ms.date: 06/07/2018
ms.author: marsma
ms.openlocfilehash: bc30352f50344031f8356d2be1b800dd035f12ad
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/07/2018
ms.locfileid: "34830465"
---
# <a name="set-environment-variables"></a>환경 변수 설정

컨테이너 인스턴스에서 환경 변수를 설정하면 컨테이너가 실행하는 응용 프로그램 또는 스크립트의 동적 구성을 제공할 수 있습니다. 컨테이너에서 환경 변수를 설정하려면 컨테이너 인스턴스를 만들 때 지정합니다. [Azure CLI](#azure-cli-example), [Azure PowerShell](#azure-powershell-example) 및 [Azure Portal](#azure-portal-example)로 컨테이너를 시작할 때 환경 변수를 설정할 수 있습니다.

예를 들어 [microsoft/aci-wordcount][aci-wordcount] 컨테이너 이미지를 실행하는 경우 다음 환경 변수를 지정하여 해당 동작을 수정할 수 있습니다.

*NumWords*: STDOUT으로 전송된 단어 수입니다.

*MinLength*: 계산되는 단어의 최소 문자 수입니다. 숫자가 높을수록 "of" 및 "the"와 같은 일반적인 단어를 무시합니다.

환경 변수로 비밀을 전달해야 하는 경우 Azure Container Instances는 Windows와 Linux 컨테이너 모두에 대한 [보안 값](#secure-values)을 지원합니다.

## <a name="azure-cli-example"></a>Azure CLI 예제

[microsoft/aci-wordcount][aci-wordcount] 컨테이너의 기본 출력을 보려면 이 [az container create][az-container-create] 명령으로 먼저 실행합니다(지정된 환경 변수 없음).

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer1 \
    --image microsoft/aci-wordcount:latest \
    --restart-policy OnFailure
```

출력을 수정하려면 *NumWords* 및 *MinLength* 변수에 대한 값을 지정하여 추가된 `--environment-variables` 인수로 두 번째 컨테이너를 시작합니다.

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer2 \
    --image microsoft/aci-wordcount:latest \
    --restart-policy OnFailure \
    --environment-variables NumWords=5 MinLength=8
```

두 컨테이너의 상태가 *종료됨*으로 표시되면([az container show][az-container-show]를 사용하여 상태 확인) [az container logs][az-container-logs]로 해당 로그를 표시하여 출력을 봅니다.

```azurecli-interactive
az container logs --resource-group myResourceGroup --name mycontainer1
az container logs --resource-group myResourceGroup --name mycontainer2
```

컨테이너의 출력은 환경 변수를 설정하여 두 번째 컨테이너의 스크립트 동작을 수정한 방법을 보여줍니다.

```console
azureuser@Azure:~$ az container logs --resource-group myResourceGroup --name mycontainer1
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

azureuser@Azure:~$ az container logs --resource-group myResourceGroup --name mycontainer2
[('CLAUDIUS', 120),
 ('POLONIUS', 113),
 ('GERTRUDE', 82),
 ('ROSENCRANTZ', 69),
 ('GUILDENSTERN', 54)]
```

## <a name="azure-powershell-example"></a>Azure PowerShell 예제

PowerShell에서 환경 변수를 설정하는 것은 CLI와 유사하지만 `-EnvironmentVariable` 명령줄 인수를 사용합니다.

먼저 이 [New-AzureRmContainerGroup][new-azurermcontainergroup] 명령을 사용하여 해당 기본 구성에서 [microsoft/aci-wordcount][aci-wordcount] 컨테이너를 시작합니다.

```azurepowershell-interactive
New-AzureRmContainerGroup `
    -ResourceGroupName myResourceGroup `
    -Name mycontainer1 `
    -Image microsoft/aci-wordcount:latest
```

이제 다음 [New-AzureRmContainerGroup][new-azurermcontainergroup] 명령을 실행합니다. 이 명령은 배열 변수, `envVars`를 채운 후 *NumWords* 및 *MinLength* 환경 변수를 지정합니다.

```azurepowershell-interactive
$envVars = @{NumWords=5;MinLength=8}
New-AzureRmContainerGroup `
    -ResourceGroupName myResourceGroup `
    -Name mycontainer2 `
    -Image microsoft/aci-wordcount:latest `
    -RestartPolicy OnFailure `
    -EnvironmentVariable $envVars
```

두 컨테이너의 상태가 *종료됨*이 되면([Get-AzureRmContainerInstanceLog][azure-instance-log]를 사용하여 상태 확인) [Get-AzureRmContainerInstanceLog][azure-instance-log] 명령을 사용하여 해당 로그를 끌어옵니다.

```azurepowershell-interactive
Get-AzureRmContainerInstanceLog -ResourceGroupName myResourceGroup -ContainerGroupName mycontainer1
Get-AzureRmContainerInstanceLog -ResourceGroupName myResourceGroup -ContainerGroupName mycontainer2
```

각 컨테이너의 출력은 환경 변수를 설정하여 컨테이너에서 실행되는 스크립트를 수정한 방법을 보여줍니다.

```console
PS Azure:\> Get-AzureRmContainerInstanceLog -ResourceGroupName myResourceGroup -ContainerGroupName mycontainer1
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

Azure:\
PS Azure:\> Get-AzureRmContainerInstanceLog -ResourceGroupName myResourceGroup -ContainerGroupName mycontainer2
[('CLAUDIUS', 120),
 ('POLONIUS', 113),
 ('GERTRUDE', 82),
 ('ROSENCRANTZ', 69),
 ('GUILDENSTERN', 54)]

Azure:\
```

## <a name="azure-portal-example"></a>Azure Portal 예제

Azure Portal에서 컨테이너를 시작할 때 환경 변수를 설정하려면 컨테이너를 만들 때 **구성** 페이지에서 지정합니다.

포털로 배포할 때 현재 세 개의 변수로 제한되며, 이 `"variableName":"value"` 형식으로 입력해야 합니다.

예제를 보려면 *NumWords* 및 *MinLength* 변수를 사용하여 [microsoft/aci-wordcount][aci-wordcount] 컨테이너를 시작합니다.

1. **구성**에서 **재시작 정책**을 *실패 시*로 설정합니다.
2. 첫 번째 변수에 `"NumWords":"5"`를 입력하고, **추가 환경 변수 추가** 아래에서 **예**를 선택하고, 두 번째 변수에 `"MinLength":"8"`을 입력합니다. **확인**을 선택하여 확인한 다음, 컨테이너를 배포합니다.

![환경 변수 사용 단추 및 텍스트 상자를 표시하는 포털 페이지][portal-env-vars-01]

컨테이너의 로그를 보려면 **설정**에서 **컨테이너**, **로그**를 차례로 선택합니다. 이전 CLI 및 PowerShell 섹션에서 표시된 출력과 유사하게 스크립트의 동작이 환경 변수에서 수정된 방법을 볼 수 있습니다. 각각이 8개 문자의 최소 길이로 5개의 단어만 표시됩니다.

![컨테이너 로그 출력을 표시하는 포털][portal-env-vars-02]

## <a name="secure-values"></a>보안 값
보안 값이 있는 개체는 응용 프로그램에 대한 암호 또는 키와 같은 중요한 정보를 저장하는 데 사용됩니다. 환경 변수에 대한 보안 값을 사용하면 컨테이너의 이미지에 포함하는 것보다 더 안전하고 더 유연적입니다. 또 다른 옵션은 [Azure Container Instances에서 비밀 볼륨 탑재](container-instances-volume-secret.md)에서 설명한 비밀 볼륨을 사용하는 것입니다.

보안 값이 포함된 보안 환경 변수는 사용자 컨테이너의 속성에서 보안 값을 노출하지 않으므로 값은 사용자 컨테이너 내에서만 액세스할 수 있습니다. 예를 들어 Azure Portal 또는 Azure CLI에서 본 컨테이너 속성은 보안 값이 포함된 환경 변수에서 표시되지 않습니다.

변수의 유형에 대한 일반 `value` 대신 `secureValue` 속성을 지정하여 보안 환경 변수를 설정합니다. 다음 YAML에서 정의된 두 개의 변수는 두 가지의 변수 유형을 설명합니다.

### <a name="yaml-deployment"></a>YAML 배포

다음 코드 조각이 포함된 `secure-env.yaml` 파일을 만듭니다.

```yaml
apiVersion: 2018-06-01
location: westus
name: securetest
properties:
  containers:
  - name: mycontainer
    properties:
      environmentVariables:
        - "name": "SECRET"
          "secureValue": "my-secret-value"
        - "name": "NOTSECRET"
          "value": "my-exposed-value"
      image: nginx
      ports: []
      resources:
        requests:
          cpu: 1.0
          memoryInGB: 1.5
  osType: Linux
  restartPolicy: Always
tags: null
type: Microsoft.ContainerInstance/containerGroups
```

다음 명령을 실행하여 YAML이 포함된 컨테이너 그룹을 배포합니다.

```azurecli-interactive
az container create --resource-group myRG --name securetest -f secure-env.yaml
```

### <a name="verify-environment-variables"></a>환경 변수 확인

다음 명령을 실행하여 컨테이너의 환경 변수에 대해 쿼리합니다.

```azurecli-interactive
az container show --resource-group myRG --name securetest --query 'containers[].environmentVariables`
```

이 컨테이너에 대한 세부 정보가 포함된 JSON 응답은 비보안 환경 변수와 보안 환경 변수의 키만 만 표시합니다.

```json
  "environmentVariables": [
    {
      "name": "NOTSECRET",
      "value": "my-exposed-value"
    },
    {
      "name": "SECRET"
    }
```

실행 중인 컨테이너 내에서 명령을 실행할 수 있도록 하는 `exec` 명령으로 보안 환경 변수가 설정된 것을 검토할 수 있습니다. 

다음 명령을 실행하여 컨테이너를 통해 대화형 Bash 세션을 시작합니다.
```azurecli-interactive
az container exec --resource-group myRG --name securetest --exec-command "/bin/bash"
```

사용자 컨테이너 내에서 다음 Bash 명령을 사용하여 환경 변수를 인쇄합니다.
```bash
echo $SECRET
```

## <a name="next-steps"></a>다음 단계

여러 컨테이너로 큰 데이터 집합을 처리하는 일괄 처리와 같은 작업 기반 시나리오는 런타임 시 사용자 지정 환경 변수를 활용할 수 있습니다. 작업 기반 컨테이너 실행에 대한 자세한 내용은 [Azure Container Instances에서 컨테이너화된 작업 실행](container-instances-restart-policy.md)을 참조하세요.

<!-- IMAGES -->
[portal-env-vars-01]: ./media/container-instances-environment-variables/portal-env-vars-01.png
[portal-env-vars-02]: ./media/container-instances-environment-variables/portal-env-vars-02.png

<!-- LINKS - External -->
[aci-wordcount]: https://hub.docker.com/r/microsoft/aci-wordcount/

<!-- LINKS Internal -->
[az-container-create]: /cli/azure/container#az-container-create
[az-container-logs]: /cli/azure/container#az-container-logs
[az-container-show]: /cli/azure/container#az-container-show
[azure-cli-install]: /cli/azure/
[azure-instance-log]: /powershell/module/azurerm.containerinstance/get-azurermcontainerinstancelog
[azure-powershell-install]: /powershell/azure/install-azurerm-ps
[new-azurermcontainergroup]: /powershell/module/azurerm.containerinstance/new-azurermcontainergroup
[portal]: https://portal.azure.com
