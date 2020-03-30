---
title: YAML 참조 - ACR 작업
description: 작업 속성, 단계 유형, 단계 속성 및 기본 제공 변수를 포함하여 YAML로 ACR 작업에 대한 작업을 정의하기 위한 참조입니다.
ms.topic: article
ms.date: 10/23/2019
ms.openlocfilehash: 9558f698b4a9dbca46431fc02ced6ae30de29121
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79246982"
---
# <a name="acr-tasks-reference-yaml"></a>ACR 작업 참조: YAML

ACR 작업의 다단계 작업 정의는 컨테이너 빌드, 테스트 및 패치에 중점을 둔 컨테이너 중심의 컴퓨팅 기본 형식을 제공합니다. 이 문서에서는 다단계 작업을 정의하는 YAML 파일에 대한 명령, 매개 변수, 속성 및 구문을 다룹니다.

이 문서에는 ACR 작업에 대한 다단계 작업 YAML 파일을 만들기 위한 참조가 포함되어 있습니다. ACR 작업에 대한 소개를 보려면 [ACR 작업 개요](container-registry-tasks-overview.md)를 참조하세요.

## <a name="acr-taskyaml-file-format"></a>acr-task.yaml 파일 형식

ACR 작업은 표준 YAML 구문의 다단계 작업 선언을 지원합니다. YAML 파일에서 작업의 단계를 정의합니다. 그런 다음 [az acr run][az-acr-run] 명령에 파일을 전달하여 수동으로 작업을 실행할 수 있습니다. 또는 파일을 사용하여 Git 커밋 또는 기본 이미지 업데이트에서 자동으로 트리거되는 [az acr 작업을 사용하여 작업을 만듭니다.][az-acr-task-create] 이 문서에서는 `acr-task.yaml`을 단계가 포함된 파일로 참조하지만, ACR 작업은 [지원되는 확장명](#supported-task-filename-extensions)을 가진 유효한 파일 이름을 모두 지원합니다.

최상위 `acr-task.yaml` 기본 형식은 **작업 속성**, **단계 유형** 및 **단계 속성**입니다.

* [작업 속성](#task-properties)은 작업 실행 전반에 걸쳐 모든 단계에 적용됩니다. 다음과 같은 여러 전역 작업 속성이 있습니다.
  * `version`
  * `stepTimeout`
  * `workingDirectory`
* [작업 단계 유형](#task-step-types)은 작업에서 수행할 수 있는 동작 유형을 나타냅니다. 다음 세 가지 단계 유형이 있습니다.
  * `build`
  * `push`
  * `cmd`
* [작업 단계 속성](#task-step-properties)은 개별 단계에 적용되는 매개 변수입니다. 다음과 같은 몇 가지 단계 속성이 있습니다.
  * `startDelay`
  * `timeout`
  * `when`
  * 기타 등등

몇 가지 일반적인 단계 속성을 포함하여 `acr-task.yaml` 파일의 기본 형식은 다음과 같습니다. 사용 가능한 모든 단계 속성 또는 단계 유형 사용법의 전체 목록은 아니지만 기본 파일 형식에 대한 빠른 개요를 제공합니다.

```yml
version: # acr-task.yaml format version.
stepTimeout: # Seconds each step may take.
steps: # A collection of image or container actions.
  - build: # Equivalent to "docker build," but in a multi-tenant environment
  - push: # Push a newly built or retagged image to a registry.
    when: # Step property that defines either parallel or dependent step execution.
  - cmd: # Executes a container, supports specifying an [ENTRYPOINT] and parameters.
    startDelay: # Step property that specifies the number of seconds to wait before starting execution.
```

### <a name="supported-task-filename-extensions"></a>지원되는 작업 파일 이름 확장명

ACR 작업에는 `.yaml`을 포함하여 몇 가지 파일 이름 확장명이 예약되어 있으며, 작업 파일로 처리됩니다. 다음 목록에 ‘없는’ 확장명은 ACR 작업에서 Dockerfile로 간주됩니다. .yaml, .yml, .toml, .json, .sh, .bash, .zsh, .ps1, .ps, .cmd, .bat, .ts, .js, .php, .py, .rb, .lua**

YAML은 현재 ACR 작업에서 지원되는 유일한 파일 형식입니다. 다른 파일 이름 확장명은 향후 지원을 위해 예약되었습니다.

## <a name="run-the-sample-tasks"></a>샘플 작업 실행

이 문서의 다음 섹션에서 참조되는 몇 가지 샘플 작업 파일이 있습니다. 샘플 작업은 공용 GitHub 리포지토리인 [Azure-Samples/acr-tasks][acr-tasks]에 있습니다. Azure CLI 명령 [az acr run][az-acr-run]을 사용하여 실행할 수 있습니다. 샘플 명령은 다음과 유사합니다.

```azurecli
az acr run -f build-push-hello-world.yaml https://github.com/Azure-Samples/acr-tasks.git
```

샘플 명령의 형식은 Azure CLI에서 기본 레지스트리를 구성했다고 가정하므로 `--registry` 매개 변수를 생략합니다. 기본 레지스트리를 구성하려면  값을 사용하는  매개 변수와 함께 [az configure`--defaults``acr=REGISTRY_NAME`][az-configure] 명령을 사용합니다.

예를 들어, “myregistry”라는 기본 레지스트리를 사용하여 Azure CLI를 구성하려면 다음 명령을 사용합니다.

```azurecli
az configure --defaults acr=myregistry
```

## <a name="task-properties"></a>작업 속성

작업 속성은 일반적으로 `acr-task.yaml` 파일의 맨 위에 나타나며 작업 단계의 전체 실행 에 적용되는 전역 속성입니다. 이러한 전역 속성 중 일부는 개별 단계에서 재정의할 수 있습니다.

| 속성 | Type | Optional | 설명 | 재정의 지원 여부 | 기본값 |
| -------- | ---- | -------- | ----------- | ------------------ | ------------- |
| `version` | 문자열 | yes | ACR 작업 서비스에서 구문 분석한 `acr-task.yaml` 파일의 버전입니다. ACR 작업은 이전 버전과의 호환성을 유지하려고 하지만, 이 값을 사용하면 ACR 작업이 정의된 버전 내에서 호환성을 유지할 수 있습니다. 지정되지 않은 경우 기본값은 최신 버전으로 설정됩니다. | 예 | None |
| `stepTimeout` | int(초) | yes | 단계를 실행할 수 있는 최대 시간(초)입니다. 작업에 속성을 지정하면 모든 단계의 기본 `timeout` 속성을 설정합니다. 단계에 `timeout` 속성을 지정하면 작업에서 제공하는 속성을 재정의합니다. | yes | 600(10분) |
| `workingDirectory` | 문자열 | yes | 런타임 동안 컨테이너의 작업 디렉터리입니다. 작업에 속성을 지정하면 모든 단계의 기본 `workingDirectory` 속성을 설정합니다. 단계에 지정하면 작업에서 제공하는 속성을 재정의합니다. | yes | `/workspace` |
| `env` | [string, string, ...] | yes |  작업에 대 한 `key=value` 환경 변수를 정의 하는 형식의 문자열배열입니다. 작업에 속성을 지정하면 모든 단계의 기본 `env` 속성을 설정합니다. 단계에 지정하면 작업에서 상속된 모든 환경 변수를 재정의합니다. | None |
| `secrets` | [비밀, 비밀,...] | yes | [비밀](#secret) 개체의 배열입니다. | None |
| `networks` | [네트워크, 네트워크, ...] | yes | [네트워크](#network) 개체의 배열입니다. | None |

### <a name="secret"></a>secret

secret 개체에는 다음과 같은 속성이 있습니다.

| 속성 | Type | Optional | 설명 | 기본값 |
| -------- | ---- | -------- | ----------- | ------- |
| `id` | 문자열 | 예 | 비밀의 식별자입니다. | None |
| `keyvault` | 문자열 | yes | Azure 키 볼트 보안 검색비밀 URL입니다. | None |
| `clientID` | 문자열 | yes | Azure 리소스에 대해 [사용자가 할당한 관리 ID의 클라이언트 ID입니다.](container-registry-tasks-authentication-managed-identity.md) | None |

### <a name="network"></a>네트워크

네트워크 개체에는 다음과 같은 속성이 있습니다.

| 속성 | Type | Optional | 설명 | 기본값 |
| -------- | ---- | -------- | ----------- | ------- | 
| `name` | 문자열 | 예 | 네트워크의 이름입니다. | None |
| `driver` | 문자열 | yes | 네트워크를 관리할 드라이버입니다. | None |
| `ipv6` | bool | yes | IPv6 네트워킹이 활성화되어 있는지 여부입니다. | `false` |
| `skipCreation` | bool | yes | 네트워크 생성을 건너뛸지 여부입니다. | `false` |
| `isDefault` | bool | yes | 네트워크가 Azure 컨테이너 레지스트리와 함께 제공되는 기본 네트워크인지 여부 | `false` |

## <a name="task-step-types"></a>작업 단계 유형

ACR 작업은 세 가지 단계 유형을 지원합니다. 각 단계 유형은 여러 가지 속성을 지원하며, 각 단계 유형에 대한 섹션에서 자세히 설명합니다.

| 단계 유형 | 설명 |
| --------- | ----------- |
| [`build`](#build) | 익숙한 `docker build` 구문을 사용하여 컨테이너 이미지를 빌드합니다. |
| [`push`](#push) | 새로 빌드되었거나 태그가 변경된 이미지를 컨테이너 레지스트리로 `docker push`하는 작업을 실행합니다. Azure Container Registry, 기타 프라이빗 레지스트리 및 공용 Docker 허브가 지원됩니다. |
| [`cmd`](#cmd) | 컨테이너의 `[ENTRYPOINT]`에 전달된 매개 변수를 사용하여 컨테이너를 명령으로 실행합니다. 단계 `cmd` 유형은 에 `env` `detach`대한 및 기타 `docker run` 친숙한 명령 옵션과 같은 매개 변수를 지원하므로 동시 컨테이너 실행을 통해 단위 및 기능 테스트를 사용할 수 있습니다. |

## <a name="build"></a>build

컨테이너 이미지를 빌드합니다. `build` 단계 유형은 클라우드에서 `docker build`를 첫 번째 클래스 기본 형식으로 실행하는 보안 수단인 다중 테넌트를 나타냅니다.

### <a name="syntax-build"></a>구문: build

```yml
version: v1.1.0
steps:
  - [build]: -t [imageName]:[tag] -f [Dockerfile] [context]
    [property]: [value]
```

`build` 단계 형식은 다음 테이블의 매개 변수를 지원합니다. 또한 `build` 단계 형식은 빌드 시간 변수를 설정하는 [docker build](https://docs.docker.com/engine/reference/commandline/build/) 명령(예: `--build-arg`)의 모든 빌드 옵션을 지원합니다.

| 매개 변수 | 설명 | Optional |
| --------- | ----------- | :-------: |
| `-t` &#124; `--image` | 빌드된 이미지의 정규화된 `image:tag`를 정의합니다.<br /><br />기능 테스트와 같은 내부 작업 유효성 검사에 이미지가 사용될 수도 있으므로 일부 이미지는 레지스트리로 `push`할 필요가 없습니다. 그러나 작업 실행 내에서 이미지를 인스턴스화하려면 이미지를 참조하기 위해 이름이 필요합니다.<br /><br />달리 `az acr build`ACR 태스크를 실행하는 것은 기본 푸시 동작을 제공하지 않습니다. ACR 작업을 사용한 기본 시나리오에서는 이미지를 빌드하고 유효성을 검사한 다음, 푸시하는 기능을 가정합니다. 빌드된 이미지를 선택적으로 푸시하는 방법은 [push](#push)를 참조하세요. | yes |
| `-f` &#124; `--file` | `docker build`에 전달된 Dockerfile을 지정합니다. 지정하지 않으면 컨텍스트 루트의 기본 Dockerfile이 가정됩니다. Dockerfile을 지정하려면 컨텍스트 루트를 기준으로 파일 이름을 전달합니다. | yes |
| `context` | `docker build`에 전달된 루트 디렉터리입니다. 각 작업의 루트 디렉터리는 공유 [workingDirectory](#task-step-properties)로 설정되며, Git clone된 관련 디렉터리의 루트를 포함합니다. | 예 |

### <a name="properties-build"></a>속성: build

`build` 단계 유형은 다음 속성을 지원합니다. 이 문서의 작업 [단계 속성](#task-step-properties) 섹션에서 이러한 속성에 대한 자세한 내용을 찾을 수 있습니다.

| | | |
| -------- | ---- | -------- |
| `detach` | bool | Optional |
| `disableWorkingDirectoryOverride` | bool | Optional |
| `entryPoint` | 문자열 | Optional |
| `env` | [string, string, ...] | Optional |
| `expose` | [string, string, ...] | Optional |
| `id` | 문자열 | Optional |
| `ignoreErrors` | bool | Optional |
| `isolation` | 문자열 | Optional |
| `keep` | bool | Optional |
| `network` | object | Optional |
| `ports` | [string, string, ...] | Optional |
| `pull` | bool | Optional |
| `repeat` | int | Optional |
| `retries` | int | Optional |
| `retryDelay` | int(초) | Optional |
| `secret` | object | Optional |
| `startDelay` | int(초) | Optional |
| `timeout` | int(초) | Optional |
| `when` | [string, string, ...] | Optional |
| `workingDirectory` | 문자열 | Optional |

### <a name="examples-build"></a>예: build

#### <a name="build-image---context-in-root"></a>이미지 빌드 - 루트의 컨텍스트

```azurecli
az acr run -f build-hello-world.yaml https://github.com/AzureCR/acr-tasks-sample.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/build-hello-world.yaml -->
[!code-yml[task](~/acr-tasks/build-hello-world.yaml)]

#### <a name="build-image---context-in-subdirectory"></a>이미지 빌드 - 하위 디렉터리의 컨텍스트

```yml
version: v1.1.0
steps:
  - build: -t $Registry/hello-world -f hello-world.dockerfile ./subDirectory
```

## <a name="push"></a>push

빌드되었거나 태그가 변경된 하나 이상의 이미지를 컨테이너 레지스트리로 푸시합니다. Azure Container Registry 등의 프라이빗 레지스트리 또는 공용 Docker 허브로 푸시할 수 있습니다.

### <a name="syntax-push"></a>구문: push

`push` 단계 유형은 이미지 컬렉션을 지원합니다. YAML 컬렉션 구문은 인라인 및 중첩 형식을 지원합니다. 단일 이미지 푸시는 일반적으로 인라인 구문을 사용하여 나타냅니다.

```yml
version: v1.1.0
steps:
  # Inline YAML collection syntax
  - push: ["$Registry/hello-world:$ID"]
```

가독성을 높이려면 여러 이미지를 푸시할 때 중첩 구문을 사용합니다.

```yml
version: v1.1.0
steps:
  # Nested YAML collection syntax
  - push:
    - $Registry/hello-world:$ID
    - $Registry/hello-world:latest
```

### <a name="properties-push"></a>속성: push

`push` 단계 유형은 다음 속성을 지원합니다. 이 문서의 작업 [단계 속성](#task-step-properties) 섹션에서 이러한 속성에 대한 자세한 내용을 찾을 수 있습니다.

| | | |
| -------- | ---- | -------- |
| `env` | [string, string, ...] | Optional |
| `id` | 문자열 | Optional |
| `ignoreErrors` | bool | Optional |
| `startDelay` | int(초) | Optional |
| `timeout` | int(초) | Optional |
| `when` | [string, string, ...] | Optional |

### <a name="examples-push"></a>예: push

#### <a name="push-multiple-images"></a>여러 이미지 푸시

```azurecli
az acr run -f build-push-hello-world.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/build-push-hello-world.yaml -->
[!code-yml[task](~/acr-tasks/build-push-hello-world.yaml)]

#### <a name="build-push-and-run"></a>빌드, 푸시 및 실행

```azurecli
az acr run -f build-run-hello-world.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/build-run-hello-world.yaml -->
[!code-yml[task](~/acr-tasks/build-run-hello-world.yaml)]

## <a name="cmd"></a>cmd

`cmd` 단계 유형은 컨테이너를 실행합니다.

### <a name="syntax-cmd"></a>구문: cmd

```yml
version: v1.1.0
steps:
  - [cmd]: [containerImage]:[tag (optional)] [cmdParameters to the image]
```

### <a name="properties-cmd"></a>속성: cmd

`cmd` 단계 유형은 다음 속성을 지원합니다.

| | | |
| -------- | ---- | -------- |
| `detach` | bool | Optional |
| `disableWorkingDirectoryOverride` | bool | Optional |
| `entryPoint` | 문자열 | Optional |
| `env` | [string, string, ...] | Optional |
| `expose` | [string, string, ...] | Optional |
| `id` | 문자열 | Optional |
| `ignoreErrors` | bool | Optional |
| `isolation` | 문자열 | Optional |
| `keep` | bool | Optional |
| `network` | object | Optional |
| `ports` | [string, string, ...] | Optional |
| `pull` | bool | Optional |
| `repeat` | int | Optional |
| `retries` | int | Optional |
| `retryDelay` | int(초) | Optional |
| `secret` | object | Optional |
| `startDelay` | int(초) | Optional |
| `timeout` | int(초) | Optional |
| `when` | [string, string, ...] | Optional |
| `workingDirectory` | 문자열 | Optional |

이 문서의 [작업 단계 속성](#task-step-properties) 섹션에서 이러한 속성의 세부 정보를 확인할 수 있습니다.

### <a name="examples-cmd"></a>예: cmd

#### <a name="run-hello-world-image"></a>hello-world 이미지 실행

이 명령은 Docker 허브의 [hello-world](https://hub.docker.com/_/hello-world/) 이미지를 참조하는 `hello-world.yaml` 작업 파일을 실행합니다.

```azurecli
az acr run -f hello-world.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/hello-world.yaml -->
[!code-yml[task](~/acr-tasks/hello-world.yaml)]

#### <a name="run-bash-image-and-echo-hello-world"></a>bash 이미지 실행 및 “hello world” 에코

이 명령은 Docker 허브의 [bash](https://hub.docker.com/_/bash/) 이미지를 참조하는 `bash-echo.yaml` 작업 파일을 실행합니다.

```azurecli
az acr run -f bash-echo.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/bash-echo.yaml -->
[!code-yml[task](~/acr-tasks/bash-echo.yaml)]

#### <a name="run-specific-bash-image-tag"></a>특정 bash 이미지 태그 실행

특정 이미지 버전을 실행하려면 `cmd`에 태그를 지정합니다.

이 명령은 Docker 허브의 [bash:3.0](https://hub.docker.com/_/bash/) 이미지를 참조하는 `bash-echo-3.yaml` 작업 파일을 실행합니다.

```azurecli
az acr run -f bash-echo-3.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/bash-echo-3.yaml -->
[!code-yml[task](~/acr-tasks/bash-echo-3.yaml)]

#### <a name="run-custom-images"></a>사용자 지정 이미지 실행

`cmd` 단계 유형은 표준 `docker run` 형식을 사용하여 이미지를 참조합니다. 앞에 레지스트리가 없는 이미지는 docker.io에서 발생하는 것으로 간주됩니다. 앞의 예제를 동일한 의미로 다음과 같이 나타낼 수 있습니다.

```yml
version: v1.1.0
steps:
  - cmd: docker.io/bash:3.0 echo hello world
```

표준 `docker run` 이미지 참조 규칙을 `cmd` 사용하면 개인 레지스트리 또는 공용 Docker Hub에서 이미지를 실행할 수 있습니다. ACR 작업을 실행 중인 동일한 레지스트리에서 이미지를 참조하는 경우에는 레지스트리 자격 증명을 지정할 필요가 없습니다.

* Azure 컨테이너 레지스트리의 이미지를 실행합니다. 다음 예제에서는 다음과 같은 `myregistry`레지스트리와 사용자 지정 `myimage:mytag`이미지가 있다고 가정합니다.

    ```yml
    version: v1.1.0
    steps:
        - cmd: myregistry.azurecr.io/myimage:mytag
    ```

* Run 변수 또는 별칭으로 레지스트리 참조 일반화

    파일에서 레지스트리 이름을 하드 코딩하는 `acr-task.yaml` 대신 [Run 변수](#run-variables) 또는 [별칭을](#aliases)사용하여 이식성을 높일 수 있습니다. 변수 `Run.Registry` 또는 `$Registry` 별칭은 런타임에 작업이 실행 중인 레지스트리의 이름으로 확장됩니다.

    예를 들어 모든 Azure 컨테이너 레지스트리에서 작동하도록 이전 작업을 일반화하려면 이미지 이름의 $Registry 변수를 참조하십시오.

    ```yml
    version: v1.1.0
    steps:
      - cmd: $Registry/myimage:mytag
    ```

## <a name="task-step-properties"></a>작업 단계 속성

각 단계 유형은 해당 유형에 적합한 몇 가지 속성을 지원합니다. 다음 표에서는 사용 가능한 모든 단계 속성을 정의합니다. 모든 단계 유형이 모든 속성을 지원하는 것은 아닙니다. 각 단계 유형에 사용 가능한 속성을 보려면 [cmd](#cmd), [build](#build) 및 [push](#push) 단계 유형 참조 섹션을 참조하세요.

| 속성 | Type | Optional | 설명 | 기본값 |
| -------- | ---- | -------- | ----------- | ------- |
| `detach` | bool | yes | 실행할 때 컨테이너를 분리해야 하는지 여부입니다. | `false` |
| `disableWorkingDirectoryOverride` | bool | yes | 재정의 `workingDirectory` 기능을 사용하지 않도록 설정할 지 여부입니다. 컨테이너의 작업 `workingDirectory` 디렉터리를 완벽하게 제어하려면 이 방법을 함께 사용합니다. | `false` |
| `entryPoint` | 문자열 | yes | 단계 컨테이너의 `[ENTRYPOINT]`을 재정의합니다. | None |
| `env` | [string, string, ...] | yes | 단계의 환경 변수를 정의하는 `key=value` 형식의 문자열 배열입니다. | None |
| `expose` | [string, string, ...] | yes | 컨테이너에서 노출되는 포트의 배열입니다. |  None |
| [`id`](#example-id) | 문자열 | yes | 작업 내의 단계를 고유하게 식별합니다. 작업의 다른 단계에서 `when`을 사용한 종속성 검사 등을 위해 단계 `id`를 참조할 수 있습니다.<br /><br />`id`는 실행 중인 컨테이너의 이름이기도 합니다. 작업의 다른 컨테이너에서 실행 중인 프로세스가 `id`를 해당 DNS 호스트 이름으로 참조하거나 docker 로그 [id] 등으로 액세스하기 위해 참조할 수 있습니다. | `acb_step_%d`을 `%d` 통해 YAML 파일에서 단계 하향식의 0기반 인덱스는 어디에 있습니다. |
| `ignoreErrors` | bool | yes | 컨테이너 실행 중에 오류가 발생했는지 여부에 관계없이 단계를 성공한 것으로 표시할지 여부입니다. | `false` |
| `isolation` | 문자열 | yes | 컨테이너의 격리 수준입니다. | `default` |
| `keep` | bool | yes | 실행 후 단계 컨테이너를 유지할지 여부입니다. | `false` |
| `network` | object | yes | 컨테이너가 실행되는 네트워크를 식별합니다. | None |
| `ports` | [string, string, ...] | yes | 컨테이너에서 호스트로 게시되는 포트의 배열입니다. |  None |
| `pull` | bool | yes | 캐싱 동작을 방지하기 위해 컨테이너를 실행하기 전에 강제로 끌어올지 여부입니다. | `false` |
| `privileged` | bool | yes | 권한 모드에서 컨테이너를 실행할지 여부입니다. | `false` |
| `repeat` | int | yes | 컨테이너의 실행을 반복하는 재시도 수입니다. | 0 |
| `retries` | int | yes | 컨테이너가 실행에 실패할 경우 시도할 재시도 횟수입니다. 컨테이너의 종료 코드가 0이 아닌 경우에만 다시 시도합니다. | 0 |
| `retryDelay` | int(초) | yes | 컨테이너 실행의 재시도 사이의 초 지연입니다. | 0 |
| `secret` | object | yes | Azure 리소스에 대한 Azure 키 자격 증명 모음 보안 또는 [관리되는 ID를](container-registry-tasks-authentication-managed-identity.md)식별합니다. | None |
| `startDelay` | int(초) | yes | 컨테이너의 실행을 지연하는 시간(초)입니다. | 0 |
| `timeout` | int(초) | yes | 종료되기 전에 단계를 실행할 수 있는 최대 시간(초)입니다. | 600 |
| [`when`](#example-when) | [string, string, ...] | yes | 작업 내의 다른 하나 이상 단계에 대한 단계의 종속성을 구성합니다. | None |
| `user` | 문자열 | yes | 컨테이너의 사용자 이름 또는 UID | None |
| `workingDirectory` | 문자열 | yes | 단계의 작업 디렉터리를 설정합니다. 기본적으로, ACR 작업은 루트 디렉터리를 작업 디렉터리로 만듭니다. 그러나 빌드에 여러 단계가 있는 경우 동일한 작업 디렉터리를 지정하여 이전 단계가 이후 단계와 아티팩트를 공유할 수 있습니다. | `/workspace` |

### <a name="examples-task-step-properties"></a>예: 작업 단계 속성

#### <a name="example-id"></a>예: id

두 개의 이미지를 빌드하고, 기능 테스트 이미지를 인스턴스화합니다. 각 단계는 고유 `id`로 식별되며, 작업에 포함된 다른 단계의 `when` 속성에서 참조됩니다.

```azurecli
az acr run -f when-parallel-dependent.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/when-parallel-dependent.yaml -->
[!code-yml[task](~/acr-tasks/when-parallel-dependent.yaml)]

#### <a name="example-when"></a>예: when

`when` 속성은 작업 내의 다른 단계에 대한 단계의 종속성을 지정합니다. 다음 두 개의 매개 변수 값을 지원합니다.

* `when: ["-"]` - 다른 단계에 대한 종속성이 없음을 나타냅니다. `when: ["-"]`를 지정하는 단계는 즉시 실행을 시작하며, 동시 단계 실행이 가능합니다.
* `when: ["id1", "id2"]` - 단계가 `id` “id1” 및 `id` “id2”인 단계에 종속됨을 나타냅니다. 이 단계는 “id1” 및 “id2” 단계가 둘 다 완료될 때까지 실행되지 않습니다.

`when`이 지정되지 않은 단계는 `acr-task.yaml` 파일의 이전 단계 완료에 종속됩니다.

`when`을 사용하지 않는 순차 단계 실행:

```azurecli
az acr run -f when-sequential-default.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/when-sequential-default.yaml -->
[!code-yml[task](~/acr-tasks/when-sequential-default.yaml)]

`when`을 사용한 순차 단계 실행:

```azurecli
az acr run -f when-sequential-id.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/when-sequential-id.yaml -->
[!code-yml[task](~/acr-tasks/when-sequential-id.yaml)]

병렬 이미지는 다음을 빌드합니다.

```azurecli
az acr run -f when-parallel.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/when-parallel.yaml -->
[!code-yml[task](~/acr-tasks/when-parallel.yaml)]

병렬 이미지 빌드 및 종속 테스트:

```azurecli
az acr run -f when-parallel-dependent.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/when-parallel-dependent.yaml -->
[!code-yml[task](~/acr-tasks/when-parallel-dependent.yaml)]

## <a name="run-variables"></a>Run 변수

ACR 작업에는 실행 시 작업 단계에서 사용할 수 있는 기본 변수 집합이 포함되어 있습니다. 이러한 변수는 `{{.Run.VariableName}}` 형식을 사용하여 액세스할 수 있습니다. 여기서 `VariableName`는 다음 중 하나입니다.

* `Run.ID`
* `Run.SharedVolume`
* `Run.Registry`
* `Run.RegistryName`
* `Run.Date`
* `Run.OS`
* `Run.Architecture`
* `Run.Commit`
* `Run.Branch`
* `Run.TaskName`

변수 이름은 일반적으로 설명이 자명합니다. 일반적으로 사용되는 변수에 대한 세부 정보는 다음과 같습니다. YAML 버전에서는 `v1.1.0`대부분의 실행 변수 대신 축약된 미리 정의된 [작업 별칭을](#aliases) 사용할 수 있습니다. 예를 들어 `{{.Run.Registry}}`에서 별칭을 `$Registry` 사용합니다.

### <a name="runid"></a>Run.ID

을 통해 `az acr task create` `az acr run`생성된 작업의 실행, 실행 또는 트리거 기반 실행은 고유 ID를 가며 각 실행. ID는 현재 실행 중인 Run을 나타냅니다.

일반적으로 이미지에 다음과 같은 고유 태그를 지정하는 데 사용됩니다.

```yml
version: v1.1.0
steps:
    - build: -t $Registry/hello-world:$ID .
```

### <a name="runregistry"></a>Run.Registry

레지스트리의 정규화된 서버 이름입니다. 일반적으로 작업을 실행 중인 레지스트리를 참조하는 데 사용됩니다.

```yml
version: v1.1.0
steps:
  - build: -t $Registry/hello-world:$ID .
```

### <a name="runregistryname"></a>실행.레지스트리 이름

컨테이너 레지스트리의 이름입니다. 일반적으로 레지스트리에서 Azure CLI 명령을 실행하는 `cmd` 단계와 같은 정규화된 서버 이름이 필요하지 않은 작업 단계에서 사용됩니다.

```yml
version 1.1.0
steps:
# List repositories in registry
- cmd: az login --identity
- cmd: az acr repository list --name $RegistryName
```

### <a name="rundate"></a>Run.Date

실행이 시작된 현재 UTC 시간입니다.

### <a name="runcommit"></a>실행.커밋

GitHub 리포지토리에 대한 커밋에 의해 트리거된 작업의 경우 커밋 식별자입니다.

### <a name="runbranch"></a>실행.분기

GitHub 리포지토리에 대한 커밋에 의해 트리거된 작업의 경우 분기 이름입니다.

## <a name="aliases"></a>별칭

`v1.1.0`이제 ACR 작업은 실행할 때 작업 단계에 사용할 수 있는 별칭을 지원합니다. 별칭은 bash 및 기타 명령 셸에서 지원되는 별칭(명령 바로 가기)과 개념이 비슷합니다. 

별칭을 사용하면 단일 단어를 입력하여 명령 또는 명령 그룹(옵션 및 파일 이름 포함)을 시작할 수 있습니다.

ACR 작업은 미리 정의된 여러 별칭과 만드는 사용자 지정 별칭을 지원합니다.

### <a name="predefined-aliases"></a>미리 정의된 별칭

다음 작업 별칭은 실행 변수 대신 사용할 수 [있습니다.](#run-variables)

| Alias | 변수 실행 |
| ----- | ------------ |
| `ID` | `Run.ID` |
| `SharedVolume` | `Run.SharedVolume` |
| `Registry` | `Run.Registry` |
| `RegistryName` | `Run.RegistryName` |
| `Date` | `Run.Date` |
| `OS` | `Run.OS` |
| `Architecture` | `Run.Architecture` |
| `Commit` | `Run.Commit` |
| `Branch` | `Run.Branch` |

작업 단계에서 는 이 예제와 `$` 같이 지시문이 있는 별칭 앞에 옵니다.

```yml
version: v1.1.0
steps:
  - build: -t $Registry/hello-world:$ID -f hello-world.dockerfile .
```

### <a name="image-aliases"></a>이미지 별칭

다음 별칭은 각각 MCR(Microsoft 컨테이너 레지스트리)에서 안정적인 이미지를 가리킵니다. 지시문을 사용하지 않고 작업 `cmd` 파일의 섹션에서 각 파일을 참조할 수 있습니다.

| Alias | 이미지 |
| ----- | ----- |
| `acr` | `mcr.microsoft.com/acr/acr-cli:0.1` |
| `az` | `mcr.microsoft.com/acr/azure-cli:a80af84` |
| `bash` | `mcr.microsoft.com/acr/bash:a80af84` |
| `curl` | `mcr.microsoft.com/acr/curl:a80af84` |

다음 예제 작업은 여러 별칭을 사용하여 실행 레지스트리의 리포지토리에서 `samples/hello-world` 7일 이상 오래된 이미지 태그를 [제거합니다.](container-registry-auto-purge.md)

```yml
version: v1.1.0
steps:
  - cmd: acr tag list --registry $RegistryName --repository samples/hello-world
  - cmd: acr purge --registry $RegistryName --filter samples/hello-world:.* --ago 7d
```

### <a name="custom-alias"></a>사용자 지정 별칭

YAML 파일에서 사용자 지정 별칭을 정의하고 다음 예제와 같이 사용합니다. 별칭에는 숫자 문자만 포함될 수 있습니다. 별칭을 확장하는 기본 지시문은 `$` 문자입니다.

```yml
version: v1.1.0
alias:
  values:
    repo: myrepo
steps:
  - build: -t $Registry/$repo/hello-world:$ID -f Dockerfile .
```

사용자 지정 별칭 정의를 위해 원격 또는 로컬 YAML 파일에 연결할 수 있습니다. 다음 예제에서는 Azure Blob 저장소의 YAML 파일에 대한 링크입니다.

```yml
version: v1.1.0
alias:
  src:  # link to local or remote custom alias files
    - 'https://link/to/blob/remoteAliases.yml?readSasToken'
[...]
```

## <a name="next-steps"></a>다음 단계

다단계 작업에 대한 개요는 [ACR 작업에서 다단계 빌드, 테스트 및 패치 작업 실행](container-registry-tasks-multi-step.md)을 참조하세요.

한 단계 빌드의 경우 [ACR 작업 개요](container-registry-tasks-overview.md)를 참조하세요.



<!-- IMAGES -->

<!-- LINKS - External -->
[acr-tasks]: https://github.com/Azure-Samples/acr-tasks

<!-- LINKS - Internal -->
[az-acr-run]: /cli/azure/acr#az-acr-run
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-configure]: /cli/azure/reference-index#az-configure
