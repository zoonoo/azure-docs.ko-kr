---
title: 컨테이너 인스턴스에서 진입점 재정의
description: Azure 컨테이너 인스턴스를 배포할 때 컨테이너 이미지의 진입점을 재정의하는 명령줄 설정
ms.topic: article
ms.date: 04/15/2019
ms.openlocfilehash: d9554603f78a07fa44af51d8f39a91e1b3c39f70
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79247125"
---
# <a name="set-the-command-line-in-a-container-instance-to-override-the-default-command-line-operation"></a>컨테이너 인스턴스에서 명령줄을 설정하여 기본 명령줄 작업을 재정의합니다.

컨테이너 인스턴스를 만들 때 선택적으로 컨테이너 이미지에 적용된 기본 명령줄 명령을 재정의하는 명령을 지정합니다. 이 동작은 명령줄 인수와 `--entrypoint` `docker run`유사합니다.

컨테이너 인스턴스에 대한 [환경 변수](container-instances-environment-variables.md) 설정과 마찬가지로 시작 명령줄을 지정하는 것은 작업별 구성을 사용하여 각 컨테이너를 동적으로 준비해야 하는 일괄 처리 작업에 유용합니다.

## <a name="command-line-guidelines"></a>명령줄 지침

* 기본적으로 명령줄은 컨테이너에 *셸 없이 시작하는 단일 프로세스를* 지정합니다. 예를 들어 명령줄에서 Python 스크립트 또는 실행 파일을 실행할 수 있습니다. 프로세스는 추가 매개 변수 또는 인수를 지정할 수 있습니다.

* 여러 명령을 실행하려면 컨테이너 운영 체제에서 지원되는 셸 환경을 설정하여 명령줄을 시작합니다. 예제:

  |운영 체제  |기본 셸  |
  |---------|---------|
  |Ubuntu     |   `/bin/bash`      |
  |알파인     |   `/bin/sh`      |
  |Windows     |    `cmd`     |

  셸의 규칙을 따라 여러 명령을 결합하여 순서대로 실행합니다.

* 컨테이너 구성에 따라 명령줄 실행 수 또는 인수에 대한 전체 경로를 설정해야 할 수 있습니다.

* 명령줄이 장기 실행 작업 또는 실행 한 번 작업을 지정하는지 여부에 따라 컨테이너 인스턴스에 대한 적절한 [다시 시작 정책을](container-instances-restart-policy.md) 설정합니다. 예를 들어 실행 한 `Never` 번 `OnFailure` 작업에 대 한 다시 시작 정책 또는 권장 됩니다. 

* 컨테이너 이미지에서 설정된 기본 진입점에 대한 정보가 필요한 경우 [docker 이미지 검사](https://docs.docker.com/engine/reference/commandline/image_inspect/) 명령을 사용합니다.

## <a name="command-line-syntax"></a>명령줄 구문

명령줄 구문은 인스턴스를 만드는 데 사용되는 Azure API 또는 도구에 따라 다릅니다. 셸 환경을 지정하는 경우 셸의 명령 구문 규칙도 준수합니다.

* [az 컨테이너 만들기][az-container-create] 명령: 매개 `--command-line` 변수를 사용하여 문자열을 전달합니다. 예: `--command-line "python myscript.py arg1 arg2"`).

* [새 AzureRm컨테이너 그룹][new-azurermcontainergroup] Azure PowerShell cmdlet: 매개 `-Command` 변수를 사용 하 고 문자열을 전달 합니다. 예: `-Command "echo hello"`.

* Azure 포털: Command는 컨테이너 구성의 속성을 **재정의하여** 따옴표 없이 쉼표로 구분된 문자열 목록을 제공합니다. 예: `python, myscript.py, arg1, arg2`). 

* 리소스 관리자 템플릿 또는 YAML 파일 또는 Azure SDK 중 하나: 명령줄 속성을 문자열 배열로 지정합니다. 예: 리소스 관리자 `["python", "myscript.py", "arg1", "arg2"]` 템플릿의 JSON 배열입니다. 

  [Dockerfile](https://docs.docker.com/engine/reference/builder/) 구문에 익숙한 경우 이 형식은 CMD 명령의 *exec* 형식과 유사합니다.

### <a name="examples"></a>예

|    |  Azure CLI   | 포털 | 템플릿 | 
| ---- | ---- | --- | --- |
| 단일 명령 | `--command-line "python myscript.py arg1 arg2"` | **명령 재정의**:`python, myscript.py, arg1, arg2` | `"command": ["python", "myscript.py", "arg1", "arg2"]` |
| 여러 명령 | `--command-line "/bin/bash -c 'mkdir test; touch test/myfile; tail -f /dev/null'"` |**명령 재정의**:`/bin/bash, -c, mkdir test; touch test/myfile; tail -f /dev/null` | `"command": ["/bin/bash", "-c", "mkdir test; touch test/myfile; tail -f /dev/null"]` |

## <a name="azure-cli-example"></a>Azure CLI 예제

예를 들어 셰익스피어의 *햄릿의* 텍스트를 분석하여 가장 자주 발생하는 단어를 찾기 위해 [microsoft/aci-wordcount][aci-wordcount] 컨테이너 이미지의 동작을 수정합니다. *Hamlet을*분석하는 대신 다른 텍스트 원본을 가리키는 명령줄을 설정할 수 있습니다.

기본 텍스트를 분석할 때 [microsoft/aci-wordcount][aci-wordcount] 컨테이너의 출력을 보려면 다음 [az 컨테이너 만들기][az-container-create] 명령을 사용하여 실행합니다. 시작 명령줄이 지정되지 않으므로 기본 컨테이너 명령이 실행됩니다. 예를 들어 이 예제에서는 [환경 변수를](container-instances-environment-variables.md) 설정하여 5자 이상의 상위 3개 단어를 찾습니다.

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer1 \
    --image mcr.microsoft.com/azuredocs/aci-wordcount:latest \
    --environment-variables NumWords=3 MinLength=5 \
    --restart-policy OnFailure
```

컨테이너의 상태가 *종료됨으로* 표시되면(az [컨테이너 표시를][az-container-show] 사용하여 상태를 확인), az 컨테이너 로그가 있는 로그를 표시하여 [출력을 확인합니다.][az-container-logs]

```azurecli-interactive
az container logs --resource-group myResourceGroup --name mycontainer1
```

출력:

```console
[('HAMLET', 386), ('HORATIO', 127), ('CLAUDIUS', 120)]
```

이제 다른 명령줄을 지정하여 다른 텍스트를 분석하는 두 번째 예제 컨테이너를 설정합니다. 컨테이너에서 실행되는 Python 스크립트는 *wordcount.py*URL을 인수로 받아들이고 기본값 대신 해당 페이지의 콘텐츠를 처리합니다.

예를 들어 *로미오와 줄리엣에서*5자 이상의 상위 3개 단어를 결정합니다.

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

출력:

```console
[('ROMEO', 177), ('JULIET', 134), ('CAPULET', 119)]
```

## <a name="next-steps"></a>다음 단계

여러 컨테이너가 있는 대규모 데이터 집합을 일괄 처리하는 것과 같은 작업 기반 시나리오는 런타임시 사용자 지정 명령줄의 이점을 활용할 수 있습니다. 작업 기반 컨테이너 실행에 대한 자세한 내용은 [다시 시작 정책을 통해 컨테이너화된 작업 실행을](container-instances-restart-policy.md)참조하십시오.

<!-- LINKS - External -->
[aci-wordcount]: https://hub.docker.com/_/microsoft-azuredocs-aci-wordcount

<!-- LINKS Internal -->
[az-container-create]: /cli/azure/container#az-container-create
[az-container-logs]: /cli/azure/container#az-container-logs
[az-container-show]: /cli/azure/container#az-container-show
[new-azurermcontainergroup]: /powershell/module/azurerm.containerinstance/new-azurermcontainergroup
[portal]: https://portal.azure.com
