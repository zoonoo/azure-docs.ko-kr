---
title: 컨테이너 인스턴스의 진입점 재정의
description: Azure 컨테이너 인스턴스를 배포할 때 컨테이너 이미지의 진입점을 재정의하는 명령줄을 설정합니다.
ms.topic: article
ms.date: 04/15/2019
ms.openlocfilehash: 5898decbf4108d48bb9e84019d659075b18fd043
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107771086"
---
# <a name="set-the-command-line-in-a-container-instance-to-override-the-default-command-line-operation"></a>컨테이너 인스턴스에서 명령줄을 설정하여 기본 명령줄 작업 재정의

컨테이너 인스턴스를 만들 때 선택적으로 컨테이너 이미지에 적용된 기본 명령줄 명령을 재정의하는 명령을 지정합니다. 이 동작은 `docker run`에 대한 `--entrypoint` 명령줄 인수와 유사합니다.

컨테이너 인스턴스에 대한 [환경 변수](container-instances-environment-variables.md) 설정과 마찬가지로 시작 명령줄을 지정하면 작업별 구성으로 각 컨테이너를 동적으로 준비해야 하는 일괄 작업에 유용합니다.

## <a name="command-line-guidelines"></a>명령줄 지침

* 기본적으로 명령줄은 컨테이너에서 *셸 없이 시작하는 단일 프로세스* 를 지정합니다. 예를 들어 명령줄에서 Python 스크립트나 실행 파일을 실행할 수 있습니다. 프로세스에서 추가 매개 변수 또는 인수를 지정할 수 있습니다.

* 여러 명령을 실행하려면 컨테이너 운영 체제에서 지원되는 셸 환경을 설정하여 명령줄을 시작합니다. 예:

  |운영 체제  |기본 셸  |
  |---------|---------|
  |Ubuntu     |   `/bin/bash`      |
  |Alpine     |   `/bin/sh`      |
  |Windows     |    `cmd`     |

  셸의 규칙에 따라 여러 명령을 순서대로 실행합니다.

* 컨테이너 구성에 따라 명령줄 실행 파일 또는 인수에 대한 전체 경로를 설정해야 할 수 있습니다.

* 명령줄이 장기 실행 작업을 지정하는지 또는 한 번 실행 작업을 지정하는지에 따라 컨테이너 인스턴스에 대해 적절한 [다시 시작 정책](container-instances-restart-policy.md)을 설정합니다. 예를 들어 한 번 실행 작업에는 `Never` 또는 `OnFailure`의 다시 시작 정책이 권장됩니다. 

* 컨테이너 이미지에 설정된 기본 진입점에 대한 정보가 필요한 경우 [docker image inspect](https://docs.docker.com/engine/reference/commandline/image_inspect/) 명령을 사용합니다.

## <a name="command-line-syntax"></a>명령줄 구문

명령줄 구문은 인스턴스를 만드는 데 사용되는 Azure API 또는 도구에 따라 달라집니다. 셸 환경을 지정하는 경우 셸의 명령 구문 규칙도 관찰됩니다.

* [az container create][az-container-create] 명령: `--command-line` 매개 변수와 함께 문자열을 전달합니다. 예: `--command-line "python myscript.py arg1 arg2"`).

* [New-AzureRmContainerGroup][new-azurermcontainergroup] Azure PowerShell cmdlet: `-Command` 매개 변수와 함께 문자열을 전달합니다. 예: `-Command "echo hello"`.

* Azure Portal: 컨테이너 구성의 **명령 재정의** 속성에서 따옴표 없이 쉼표로 구분된 문자열 목록을 제공합니다. 예: `python, myscript.py, arg1, arg2`). 

* Resource Manager 템플릿 또는 YAML 파일 또는 Azure SDK 중 하나: 명령줄 속성을 문자열 배열로 지정합니다. 예: Resource Manager 템플릿의 JSON 배열 `["python", "myscript.py", "arg1", "arg2"]`입니다. 

  [Dockerfile](https://docs.docker.com/engine/reference/builder/) 구문에 익숙하다면 이 형식은 CMD 명령의 *exec* 형식과 유사합니다.

### <a name="examples"></a>예

|    |  Azure CLI   | 포털 | 템플릿 | 
| ---- | ---- | --- | --- |
| **단일 명령** | `--command-line "python myscript.py arg1 arg2"` | **명령 재정의**: `python, myscript.py, arg1, arg2` | `"command": ["python", "myscript.py", "arg1", "arg2"]` |
| **다중 명령** | `--command-line "/bin/bash -c 'mkdir test; touch test/myfile; tail -f /dev/null'"` |**명령 재정의**: `/bin/bash, -c, mkdir test; touch test/myfile; tail -f /dev/null` | `"command": ["/bin/bash", "-c", "mkdir test; touch test/myfile; tail -f /dev/null"]` |

## <a name="azure-cli-example"></a>Azure CLI 예제

예를 들어 셰익스피어의 *Hamlet* 에서 텍스트를 분석하여 가장 자주 발생하는 단어를 찾는 [microsoft/aci-wordcount][aci-wordcount] 컨테이너 이미지의 동작을 수정합니다. *Hamlet* 을 분석하는 대신 다른 텍스트 원본을 가리키는 명령줄을 설정할 수 있습니다.

기본 텍스트를 분석할 때 [microsoft/aci-wordcount][aci-wordcount] 컨테이너의 출력을 보려면 다음 [az container create][az-container-create] 명령을 사용하여 실행합니다. 시작 명령줄이 지정되지 않았으므로 기본 컨테이너 명령이 실행됩니다. 설명을 위해 이 예에서는 [환경 변수](container-instances-environment-variables.md)를 설정하여 길이가 5자 이상인 상위 3개 단어를 찾습니다.

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer1 \
    --image mcr.microsoft.com/azuredocs/aci-wordcount:latest \
    --environment-variables NumWords=3 MinLength=5 \
    --restart-policy OnFailure
```

컨테이너의 상태가 *종료됨* 으로 표시되면([az container show][az-container-show]를 사용하여 상태 확인) [az container logs][az-container-logs]로 해당 로그를 표시하여 출력을 봅니다.

```azurecli-interactive
az container logs --resource-group myResourceGroup --name mycontainer1
```

출력:

```console
[('HAMLET', 386), ('HORATIO', 127), ('CLAUDIUS', 120)]
```

이제 다른 명령줄을 지정하여 다른 텍스트를 분석하는 두 번째 예제 컨테이너를 설정합니다. *wordcount.py* 컨테이너에서 실행하는 Python 스크립트는 URL을 인수로 받아들이고 기본값 대신 해당 페이지의 콘텐츠를 처리합니다.

예를 들어 *Romeo and Juliet* 에서 5자 이상인 상위 3개 단어를 확인하려면 다음을 수행합니다.

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer2 \
    --image mcr.microsoft.com/azuredocs/aci-wordcount:latest \
    --restart-policy OnFailure \
    --environment-variables NumWords=3 MinLength=5 \
    --command-line "python wordcount.py http://shakespeare.mit.edu/romeo_juliet/full.html"
```

다시 한번 컨테이너가 *Terminated* 가 되면 컨테이너의 로그를 표시하여 출력을 봅니다.

```azurecli-interactive
az container logs --resource-group myResourceGroup --name mycontainer2
```

출력:

```console
[('ROMEO', 177), ('JULIET', 134), ('CAPULET', 119)]
```

## <a name="next-steps"></a>다음 단계

여러 컨테이너로 큰 데이터 세트를 처리하는 일괄 처리와 같은 작업 기반 시나리오는 런타임 시 사용자 지정 명령줄을 활용할 수 있습니다. 작업 기반 컨테이너 실행에 대한 자세한 내용은 [다시 시작 정책으로 컨테이너화된 작업 실행](container-instances-restart-policy.md)을 참조하세요.

<!-- LINKS - External -->
[aci-wordcount]: https://hub.docker.com/_/microsoft-azuredocs-aci-wordcount

<!-- LINKS Internal -->
[az-container-create]: /cli/azure/container#az_container_create
[az-container-logs]: /cli/azure/container#az_container_logs
[az-container-show]: /cli/azure/container#az_container_show
[new-azurermcontainergroup]: /powershell/module/azurerm.containerinstance/new-azurermcontainergroup
[portal]: https://portal.azure.com
