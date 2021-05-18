---
title: Azure Dev Space로 작업할 때 비밀을 관리하는 방법
services: azure-dev-spaces
ms.date: 12/03/2019
ms.topic: conceptual
description: Azure Dev Spaces를 사용하여 애플리케이션을 개발할 때 런타임 또는 빌드 타임에 Kubernetes 비밀을 사용하는 방법을 알아봅니다.
keywords: Docker, Kubernetes, Azure, AKS, Azure Container Service, 컨테이너
ms.custom: devx-track-js
ms.openlocfilehash: 8791480f420dfd76d5291ce82e8ebf7412a41326
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "91972971"
---
# <a name="how-to-manage-secrets-when-working-with-an-azure-dev-space"></a>Azure Dev Space로 작업할 때 비밀을 관리하는 방법

[!INCLUDE [Azure Dev Spaces deprecation](../../../includes/dev-spaces-deprecation.md)]

서비스에는 데이터베이스 또는 기타 보안 Azure 서비스에 대한 특정 암호, 연결 문자열 및 기타 비밀이 필요할 수 있습니다. 구성 파일에서 이러한 비밀의 값을 설정하여 코드에서 환경 변수로 사용할 수 있습니다.  비밀의 보안이 손상되지 하도록 구성 파일을 주의해서 처리해야 합니다.

## <a name="storing-and-using-runtime-secrets"></a>런타임 비밀 저장 및 사용

Azure Dev Spaces에서는 Azure Dev Spaces 클라이언트 도구에서 생성된 Helm 차트에 비밀을 저장하기 위한 두 가지 간소화된 권장 옵션을 제공합니다. 즉, `values.dev.yaml` 파일에 저장하거나 `azds.yaml`에 직접 인라인으로 저장합니다. `values.yaml`에는 비밀을 저장하지 않는 것이 좋습니다.

> [!NOTE]
> 다음 방법은 클라이언트 도구에서 생성된 Helm 차트의 비밀을 저장하고 사용하는 방법을 보여 줍니다. 사용자 고유의 Helm 차트를 만드는 경우 Helm 차트를 직접 사용하여 비밀을 관리하고 저장할 수 있습니다.

### <a name="using-valuesdevyaml"></a>values.dev.yaml 사용

Azure Dev Spaces를 사용하여 미리 준비한 프로젝트에서 `azds.yaml`과 동일한 폴더에 `values.dev.yaml` 파일을 만들어 비밀 키와 값을 정의합니다. 예를 들면 다음과 같습니다.

```yaml
secrets:
  redis:
    port: "6380"
    host: "contosodevredis.redis.cache.windows.net"
    key: "secretkeyhere"
```

`?`를 사용하여 `azds.yaml` 파일이 `values.dev.yaml`을 선택 사항으로 참조하는지 확인합니다. 예를 들면 다음과 같습니다.

```yaml
install:
  values:
  - values.dev.yaml?
  - secrets.dev.yaml?
```

추가 비밀 파일이 있는 경우 해당 파일도 여기에서 추가할 수 있습니다.

서비스에서 비밀을 환경 변수로 참조하도록 업데이트하거나 확인합니다. 예를 들면 다음과 같습니다.

```javascript
var redisPort = process.env.REDIS_PORT
var host = process.env.REDIS_HOST
var theKey = process.env.REDIS_KEY
```
    
`azds up`을 사용하여 업데이트된 서비스를 실행합니다.

```console
azds up
```
 
`kubectl`을 사용하여 비밀이 생성되었는지 확인합니다.

```console
kubectl get secret --namespace default -o yaml 
```

> [!IMPORTANT]
> 원본 제어에는 비밀을 저장하지 않는 것이 좋습니다. Git을 사용하는 경우 원본 제어에서 비밀이 커밋되지 않도록 `.gitignore` 파일에 `values.dev.yaml`을 추가합니다.

### <a name="using-azdsyaml"></a>azds.yaml 사용

Azure Dev Spaces를 사용하여 미리 준비한 프로젝트에서 `azds.yaml`의 *configurations.develop.install.set* 아래에 *$PLACEHOLDER* 구문을 사용하여 비밀 키와 값을 추가합니다. 예를 들면 다음과 같습니다.

```yaml
configurations:
  develop:
    ...
    install:
      set:
        secrets:
          redis:
            port: "$REDIS_PORT"
            host: "$REDIS_HOST"
            key: "$REDIS_KEY"
```

> [!NOTE]
> `azds.yaml`에서는 *$PLACEHOLDER* 구문을 사용하지 않고 비밀 값을 직접 입력할 수 있습니다. 그러나 `azds.yaml`은 원본 제어에 저장되므로 이 방법은 사용하지 않는 것이 좋습니다.
     
`azds.yaml`과 동일한 폴더에 `.env` 파일을 만들어 *$PLACEHOLDER* 값을 정의합니다. 예를 들면 다음과 같습니다.

```
REDIS_PORT=3333
REDIS_HOST=myredishost
REDIS_KEY=myrediskey
```

> [!IMPORTANT]
> 원본 제어에는 비밀을 저장하지 않는 것이 좋습니다. Git을 사용하는 경우 원본 제어에서 비밀이 커밋되지 않도록 `.gitignore` 파일에 `.env`을 추가합니다.

서비스에서 비밀을 환경 변수로 참조하도록 업데이트하거나 확인합니다. 예를 들면 다음과 같습니다.

```javascript
var redisPort = process.env.REDIS_PORT
var host = process.env.REDIS_HOST
var theKey = process.env.REDIS_KEY
```
    
`azds up`을 사용하여 업데이트된 서비스를 실행합니다.

```console
azds up
```
 
`kubectl`을 사용하여 비밀이 생성되었는지 확인합니다.

```console
kubectl get secret --namespace default -o yaml 
```

## <a name="using-secrets-as-build-arguments"></a>빌드 인수로 비밀 사용

이전 섹션에서는 컨테이너 런타임에 사용할 비밀을 저장하고 사용하는 방법을 살펴보았습니다. `azds.yaml`을 사용하여 프라이빗 NuGet의 암호와 같은 모든 비밀을 컨테이너 빌드 타임에 사용할 수도 있습니다.

`azds.yaml`의 *configurations.develop.build.args* 에서 `<variable name>: ${secret.<secret name>.<secret key>}` 구문을 사용하여 빌드 타임 비밀을 설정합니다 예를 들면 다음과 같습니다.

```yaml
configurations:
  develop:
    build:
      dockerfile: Dockerfile.develop
      useGitIgnore: true
      args:
        BUILD_CONFIGURATION: ${BUILD_CONFIGURATION:-Debug}
        MYTOKEN: ${secret.mynugetsecret.pattoken}
```

위의 예제에서 *mynugetsecret* 은 기존 비밀이고 *pattoken* 은 기존 키입니다.

>[!NOTE]
> 비밀 이름과 키는 `.` 문자를 포함할 수 있습니다. 빌드 인수로 비밀을 전달하는 경우 `\`를 사용하여 `.`를 이스케이프합니다. 예를 들어 키가 *token* 인 *foo.bar* 라는 비밀을 전달하려면 `MYTOKEN: ${secret.foo\.bar.token}`을 사용합니다. 또한 접두사 및 후위 텍스트를 사용하여 비밀을 평가할 수 있습니다. 예: `MYURL: eus-${secret.foo\.bar.token}-version1`. 부모 및 최상위 공간에서 사용 가능한 비밀을 빌드 인수로 전달할 수도 있습니다.

Dockerfile에서 *ARG* 지시문을 통해 비밀을 사용한 다음, 나중에 Dockerfile에서 동일한 변수를 사용합니다. 예를 들면 다음과 같습니다.

```dockerfile
...
ARG MYTOKEN
...
ARG NUGET_EXTERNAL_FEED_ENDPOINTS="{'endpointCredentials': [{'endpoint':'PRIVATE_NUGET_ENDPOINT', 'password':'${MYTOKEN}'}]}"
...
```

이러한 변경 내용으로 클러스터에서 실행되는 서비스를 업데이트합니다. 명령줄에서 다음 명령을 실행합니다.

```
azds up
```

## <a name="next-steps"></a>다음 단계

이러한 방법을 사용하여 이제 안전하게 데이터베이스, Azure Redis Cache에 연결하거나 보안 Azure 서비스에 액세스할 수 있습니다.
 
