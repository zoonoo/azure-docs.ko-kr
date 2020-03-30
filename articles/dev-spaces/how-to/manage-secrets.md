---
title: Azure Dev Space로 작업할 때 비밀을 관리하는 방법
services: azure-dev-spaces
ms.date: 12/03/2019
ms.topic: conceptual
description: Azure 개발자 공간으로 응용 프로그램을 개발할 때 실행 시 Kubernetes 비밀을 사용하거나 빌드하는 방법을 알아봅니다.
keywords: Docker, Kubernetes, Azure, AKS, Azure Container Service, 컨테이너
ms.openlocfilehash: d9dd0de348612bbb3baf5fb351c1c9af1c228c1f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75438474"
---
# <a name="how-to-manage-secrets-when-working-with-an-azure-dev-space"></a>Azure Dev Space로 작업할 때 비밀을 관리하는 방법

서비스에는 데이터베이스 또는 기타 보안 Azure 서비스에 대한 특정 암호, 연결 문자열 및 기타 비밀이 필요할 수 있습니다. 구성 파일에서 이러한 비밀의 값을 설정하여 코드에서 환경 변수로 사용할 수 있습니다.  이러한 구성 파일은 비밀의 보안을 손상시키지 않도록 주의하여 처리해야 합니다.

## <a name="storing-and-using-runtime-secrets"></a>런타임 비밀 저장 및 사용

Azure 개발자 공간은 Azure Dev Spaces 클라이언트 도구에서 생성된 헬름 차트에 비밀을 `values.dev.yaml` 저장하기 위한 두 `azds.yaml`가지 권장되고 간소화된 옵션을 제공합니다. `values.yaml`에 비밀을 저장하지 않는 것이 좋습니다.

> [!NOTE]
> 다음 방법에서는 클라이언트 도구에서 생성된 Helm 차트에 대한 비밀을 저장하고 사용하는 방법을 보여 준다. 직접 투구 차트를 만든 경우 Helm 차트를 사용하여 비밀을 관리하고 저장할 수 있습니다.

### <a name="using-valuesdevyaml"></a>값 사용.dev.yaml

Azure 개발자 공백으로 이미 준비한 프로젝트에서 비밀 `values.dev.yaml` 키와 값을 `azds.yaml` 정의하는 것과 동일한 폴더에 파일을 만듭니다. 예를 들어:

```yaml
secrets:
  redis:
    port: "6380"
    host: "contosodevredis.redis.cache.windows.net"
    key: "secretkeyhere"
```

을 `azds.yaml` 사용하여 `values.dev.yaml` 파일 참조를 선택 사항으로 확인합니다. `?` 예를 들어:

```yaml
install:
  values:
  - values.dev.yaml?
  - secrets.dev.yaml?
```

추가 비밀 파일이 있는 경우 여기에 추가할 수도 있습니다.

서비스 업데이트 또는 확인 서비스 환경 변수로 비밀을 참조합니다. 예를 들어:

```javascript
var redisPort = process.env.REDIS_PORT
var host = process.env.REDIS_HOST
var theKey = process.env.REDIS_KEY
```
    
을 사용하여 `azds up`업데이트된 서비스를 실행합니다.

```console
azds up
```
 
비밀이 만들어졌는지 확인하는 데 사용합니다. `kubectl`

```console
kubectl get secret --namespace default -o yaml 
```

> [!IMPORTANT]
> 소스 제어에 비밀을 저장하지 않는 것이 좋습니다. Git을 사용하는 `values.dev.yaml` 경우 `.gitignore` 소스 제어에서 비밀을 커밋하지 않도록 파일에 추가합니다.

### <a name="using-azdsyaml"></a>azds.yaml 사용

Azure 개발자 공간으로 이미 준비한 프로젝트에서 *에서 구성.develop.install.set에서* `azds.yaml` *$PLACEHOLDER* 구문을 사용하여 비밀 키와 값을 추가합니다. 예를 들어:

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
> 에서 `azds.yaml`구문을 사용하지 않고 직접 비밀 값을 입력할 수 *$PLACEHOLDER.* 그러나 이 방법은 소스 `azds.yaml` 제어에 저장되므로 권장되지 않습니다.
     
*$PLACEHOLDER* `.env` 값을 정의하는 것과 `azds.yaml` 동일한 폴더에 파일을 만듭니다. 예를 들어:

```
REDIS_PORT=3333
REDIS_HOST=myredishost
REDIS_KEY=myrediskey
```

> [!IMPORTANT]
> 소스 제어에 비밀을 저장하지 않는 것이 좋습니다. Git을 사용하는 `.env` 경우 `.gitignore` 소스 제어에서 비밀을 커밋하지 않도록 파일에 추가합니다.

서비스 업데이트 또는 확인 서비스 환경 변수로 비밀을 참조합니다. 예를 들어:

```javascript
var redisPort = process.env.REDIS_PORT
var host = process.env.REDIS_HOST
var theKey = process.env.REDIS_KEY
```
    
을 사용하여 `azds up`업데이트된 서비스를 실행합니다.

```console
azds up
```
 
비밀이 만들어졌는지 확인하는 데 사용합니다. `kubectl`

```console
kubectl get secret --namespace default -o yaml 
```

## <a name="using-secrets-as-build-arguments"></a>기밀을 빌드 인수로 사용

이전 섹션에서는 컨테이너 런타임에 사용할 비밀을 저장하고 사용하는 방법을 보여 주어 도보였습니다. 을 사용하여 `azds.yaml`개인 NuGet에 대한 암호와 같은 컨테이너 빌드 시간에 모든 암호를 사용할 수도 있습니다.

에서 `azds.yaml` `<variable name>: ${secret.<secret name>.<secret key>}` 구문을 사용하여 *configurations.develop.build.args에서* 빌드 시간 비밀을 설정합니다. 예를 들어:

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

위의 예에서 *mynugetsecret는* 기존 비밀이며 *pattoken은* 기존 키입니다.

>[!NOTE]
> 비밀 이름과 키에는 `.` 문자가 포함될 수 있습니다. 빌드 `\` 인수로 비밀을 전달할 때 이스케이프하는 `.` 데 사용합니다. 예를 들어, *토큰의*키와 함께 `MYTOKEN: ${secret.foo\.bar.token}` *foo.bar라는* 비밀을 전달하려면 : . 또한 접두사 및 후도 텍스트로 비밀을 평가할 수 있습니다. `MYURL: eus-${secret.foo\.bar.token}-version1`)을 입력합니다. 또한 부모 및 조부모 공간에서 사용할 수 있는 비밀은 빌드 인수로 전달할 수 있습니다.

Dockerfile에서 *ARG* 지시문을 사용하여 비밀을 사용한 다음 나중에 Dockerfile에서 동일한 변수를 사용합니다. 예를 들어:

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
 
