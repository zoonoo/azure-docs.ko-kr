---
title: 시작 명령줄을 사용 하 여 Azure Container Instances에서
description: Azure container instance를 배포할 때 컨테이너 이미지에 구성 된 진입점 재정의
services: container-instances
author: dlepow
ms.service: container-instances
ms.topic: article
ms.date: 04/15/2019
ms.author: danlep
ms.openlocfilehash: da94a4c79694f511d41e5c8dda8c786fc7049726
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2019
ms.locfileid: "64569644"
---
# <a name="set-the-command-line-in-a-container-instance-to-override-the-default-command-line-operation"></a>기본 명령줄 작업을 재정의 하려면 컨테이너 인스턴스에서 명령줄 설정

컨테이너 인스턴스를 만들 때 필요에 따라 명령을 컨테이너 이미지에 포함 된 기본 명령줄 명령 재정의 지정 합니다. 이 동작은 비슷합니다는 `--entrypoint` 명령줄 인수를 `docker run`입니다.

같은 설정을 [환경 변수](container-instances-environment-variables.md) container instances에 대 한 시작 명령줄을 지정 하는 유용 일괄 처리 작업에 대 한 작업별 구성으로 동적으로 각 컨테이너를 준비 해야 합니다.

## <a name="command-line-guidelines"></a>명령줄 지침

* 기본적으로 명령줄 지정는 *없이 셸을 시작 하는 프로세스를 단일* 컨테이너에서. 예를 들어 Python 스크립트 또는 실행 파일을 명령줄 실행 될 수 있습니다. 

* 여러 명령을 실행 하려면 컨테이너 운영 체제에서 지원 되는 셸 환경을 설정 하 여 명령줄을 시작 합니다. 예제:

  |운영 체제  |기본 셸  |
  |---------|---------|
  |Ubuntu     |   `/bin/bash`      |
  |Alpine     |   `/bin/sh`      |
  |Windows     |    `cmd`     |

  셸의 순서로 실행할 여러 명령을 결합 하는 규칙을 따릅니다.

* 컨테이너 구성에 따라 전체 경로 명령줄 실행 파일 또는 인수를 설정 해야 합니다.

* 적절 한 설정 [다시 시작 정책](container-instances-restart-policy.md) 여부 명령줄 지정 장기 실행 작업 또는 한 번 실행 작업에 따라 컨테이너 인스턴스의 경우입니다. 예를 들어, 다시 시작 정책을 `Never` 또는 `OnFailure` 한 번 실행 작업에 대 한 것이 좋습니다. 

* 사용 하 여 컨테이너 이미지에 대 한 설정 기본 진입점에 대 한 정보를 해야 하는 경우는 [docker 이미지 검사](https://docs.docker.com/engine/reference/commandline/image_inspect/) 명령입니다.

## <a name="command-line-syntax"></a>명령줄 구문

명령줄 구문 Azure API 또는 인스턴스를 만드는 데 사용 되는 도구에 따라 달라 집니다. 셸 환경에서 지정 하는 경우 셸의 명령 구문 표기 규칙도 확인 합니다.

* [az 컨테이너 만들기] [ az-container-create] 명령: 문자열을 전달 합니다 `--command-line` 매개 변수입니다. 예: `--command-line "python myscript.py arg1 arg2"`).

* [새 AzureRmContainerGroup] [ new-azurermcontainergroup] Azure PowerShell cmdlet: 문자열을 전달 합니다 `-Command` 매개 변수입니다. 예: `-Command "echo hello"`.

* Azure Portal: 에 **명령 재정의** 컨테이너 구성의 속성 따옴표 없는 문자열의 쉼표로 구분 된 목록을 제공 합니다. 예: `python, myscript.py, arg1, arg2`). 

* Resource Manager 템플릿 또는 YAML 파일 또는 Azure Sdk 중 하나: 문자열의 배열로 명령줄 속성을 지정 합니다. 예: JSON 배열 `["python", "myscript.py", "arg1", "arg2"]` Resource Manager 템플릿에서 합니다. 

  익숙한 경우 [Dockerfile](https://docs.docker.com/engine/reference/builder/) 구문 형식이으로 비슷합니다는 *exec* CMD 명령의 형식입니다.

### <a name="examples"></a>예

|    |  Azure CLI   | 포털 | Template | 
| ---- | ---- | --- | --- |
| 단일 명령 | `--command-line "python myscript.py arg1 arg2"` | **명령 재정의**: `python, myscript.py, arg1, arg2` | `"command": ["python", "myscript.py", "arg1", "arg2"]` |
| 여러 명령 | `--command-line "/bin/bash -c 'mkdir test; touch test/myfile; tail -f /dev/null'"` |**명령 재정의**: `/bin/bash, -c, mkdir test; touch test/myfile; tail -f /dev/null` | `"command": ["/bin/bash", "-c", "mkdir test; touch test/myfile; tail -f /dev/null"]` |

## <a name="azure-cli-example"></a>Azure CLI 예제

예를 들어의 동작을 수정 합니다 [microsoft/aci-wordcount] [ aci-wordcount] 셰익스피어의 텍스트를 분석 하 고 컨테이너 이미지를 *Hamlet* 가장 자주 찾으려고 단어를 수행 합니다. 분석 하는 대신 *Hamlet*를 다른 텍스트 원본을 가리키는 명령줄을 설정할 수 있습니다.

출력을 확인 하는 [microsoft/aci-wordcount] [ aci-wordcount] 다음을 사용 하 여 실행 기본 텍스트를 분석 하는 경우 컨테이너 [az 컨테이너 만들기] [ az-container-create] 명령입니다. 기본 컨테이너 명령 실행 시작 명령줄이 지정 됩니다. 이해를 돕기 위해가 예제이에서는 설정 [환경 변수](container-instances-environment-variables.md) 최소 5 개의 문자로 된 상위 3 개 단어를 찾으려고 합니다.

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer1 \
    --image mcr.microsoft.com/azuredocs/aci-wordcount:latest \
    --environment-variables NumWords=3 MinLength=5 \
    --restart-policy OnFailure
```

컨테이너의 상태가으로 표시 되 면 *Terminated* (사용 하 여 [az container show] [ az-container-show] 상태를 확인 하려면)을 사용 하 여 로그를 표시 [az 컨테이너 로그] [ az-container-logs] 출력을 볼 수 있습니다.

```azurecli-interactive
az container logs --resource-group myResourceGroup --name mycontainer1
```

출력

```console
[('HAMLET', 386), ('HORATIO', 127), ('CLAUDIUS', 120)]
```

이제 다른 명령줄을 지정 하 여 다양 한 텍스트를 분석 하려면 두 번째 예제에서는 컨테이너를 설정 합니다. 컨테이너에서 실행 되는 Python 스크립트 *wordcount.py*URL을 인수로 받아들이고 기본값 대신 해당 페이지의 콘텐츠를 처리 합니다.

예를 들어 위쪽을 확인 하려면 5 적어도 3 개 단어 문자에서 긴 *Romeo and Juliet*:

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer2 \
    --image mcr.microsoft.com/azuredocs/aci-wordcount:latest \
    --restart-policy OnFailure \
    --environment-variables NumWords=3 MinLength=5 \
    --command-line "python wordcount.py http://shakespeare.mit.edu/romeo_juliet/full.html"
```

다시 한번 컨테이너가 *Terminated*가 되면 컨테이너의 로그를 표시하여 출력을 봅니다.

```azurecli-interactive
az container logs --resource-group myResourceGroup --name mycontainer2
```

출력

```console
[('ROMEO', 177), ('JULIET', 134), ('CAPULET', 119)]
```

## <a name="next-steps"></a>다음 단계

런타임 시 사용자 지정 명령 줄에서 여러 컨테이너를 사용 하 여 큰 데이터 집합을 처리 하는 일괄 처리 등의 작업 기반 시나리오를 활용할 수 있습니다. 작업 기반 컨테이너를 실행 하는 방법에 대 한 자세한 내용은 참조 하세요. [다시 시작 정책을 사용 하 여 컨테이너 화 된 작업을 실행할](container-instances-restart-policy.md)합니다.

<!-- LINKS - External -->
[aci-wordcount]: https://hub.docker.com/_/microsoft-azuredocs-aci-wordcount

<!-- LINKS Internal -->
[az-container-create]: /cli/azure/container#az-container-create
[az-container-logs]: /cli/azure/container#az-container-logs
[az-container-show]: /cli/azure/container#az-container-show
[new-azurermcontainergroup]: /powershell/module/azurerm.containerinstance/new-azurermcontainergroup
[portal]: https://portal.azure.com
