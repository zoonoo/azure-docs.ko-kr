---
title: Azure Dev Space로 작업할 때 비밀을 관리하는 방법
services: azure-dev-spaces
ms.date: 12/03/2019
ms.topic: conceptual
description: Azure Dev Spaces를 사용 하 여 응용 프로그램을 개발할 때 실행 또는 빌드 시에 Kubernetes 암호를 사용 하는 방법을 알아봅니다.
keywords: Docker, Kubernetes, Azure, AKS, Azure Container Service, 컨테이너
ms.openlocfilehash: d9dd0de348612bbb3baf5fb351c1c9af1c228c1f
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75438474"
---
# <a name="how-to-manage-secrets-when-working-with-an-azure-dev-space"></a>Azure Dev Space로 작업할 때 비밀을 관리하는 방법

서비스에는 데이터베이스 또는 기타 보안 Azure 서비스에 대한 특정 암호, 연결 문자열 및 기타 비밀이 필요할 수 있습니다. 구성 파일에서 이러한 비밀의 값을 설정하여 코드에서 환경 변수로 사용할 수 있습니다.  이러한 구성 파일은 암호의 보안을 손상 시 키 지 않도록 주의 해 서 처리 해야 합니다.

## <a name="storing-and-using-runtime-secrets"></a>런타임 비밀 저장 및 사용

Azure Dev Spaces은 Azure Dev Spaces 클라이언트 도구에서 생성 된 투구 차트에 비밀을 저장 하기 위한 두 가지 권장 옵션을 제공 합니다. `values.dev.yaml` 파일에서 `azds.yaml`직접 인라인 합니다. `values.yaml`에 암호를 저장 하지 않는 것이 좋습니다.

> [!NOTE]
> 다음 방법에서는 클라이언트 도구에서 생성 된 투구 차트에 비밀을 저장 하 고 사용 하는 방법을 보여 줍니다. 사용자 고유의 투구 차트를 만드는 경우에는 투구 차트를 직접 사용 하 여 비밀을 관리 하 고 저장할 수 있습니다.

### <a name="using-valuesdevyaml"></a>값. dev .yaml 사용

이미 Azure Dev Spaces를 사용 하 여 준비한 프로젝트에서 비밀 키와 값을 정의 하는 `azds.yaml`와 동일한 폴더에 `values.dev.yaml` 파일을 만듭니다. 예:

```yaml
secrets:
  redis:
    port: "6380"
    host: "contosodevredis.redis.cache.windows.net"
    key: "secretkeyhere"
```

`?`를 사용 하 여 `azds.yaml` 파일 참조가 `values.dev.yaml` 참조를 선택 사항으로 확인 합니다. 예:

```yaml
install:
  values:
  - values.dev.yaml?
  - secrets.dev.yaml?
```

추가 비밀 파일이 있는 경우 여기에도 추가할 수 있습니다.

서비스에서 사용자의 암호를 환경 변수로 참조 하도록 업데이트 하거나 확인 합니다. 예:

```javascript
var redisPort = process.env.REDIS_PORT
var host = process.env.REDIS_HOST
var theKey = process.env.REDIS_KEY
```
    
`azds up`를 사용 하 여 업데이트 된 서비스를 실행 합니다.

```console
azds up
```
 
`kubectl`를 사용 하 여 비밀이 만들어졌는지 확인 합니다.

```console
kubectl get secret --namespace default -o yaml 
```

> [!IMPORTANT]
> 암호를 소스 제어에 저장 하지 않는 것이 좋습니다. Git를 사용 하는 경우 `.gitignore` 파일에 `values.dev.yaml`을 추가 하 여 소스 제어에서 비밀 커밋을 방지 합니다.

### <a name="using-azdsyaml"></a>Azds 사용

이미 Azure Dev Spaces를 사용 하 여 준비한 프로젝트에서 구성에서 *$PLACEHOLDER* 구문을 사용 하 여 비밀 키 및 값을 추가 합니다. `azds.yaml`에서 *설정* 합니다. 예:

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
> `azds.yaml`에서 *$PLACEHOLDER* 구문을 사용 하지 않고 직접 비밀 값을 입력할 수 있습니다. 그러나 `azds.yaml`는 소스 제어에 저장 되므로이 방법은 사용 하지 않는 것이 좋습니다.
     
`azds.yaml`와 동일한 폴더에 `.env` 파일을 만들어 *$PLACEHOLDER* 값을 정의 합니다. 예:

```
REDIS_PORT=3333
REDIS_HOST=myredishost
REDIS_KEY=myrediskey
```

> [!IMPORTANT]
> 암호를 소스 제어에 저장 하지 않는 것이 좋습니다. Git를 사용 하는 경우 `.gitignore` 파일에 `.env`을 추가 하 여 소스 제어에서 비밀 커밋을 방지 합니다.

서비스에서 사용자의 암호를 환경 변수로 참조 하도록 업데이트 하거나 확인 합니다. 예:

```javascript
var redisPort = process.env.REDIS_PORT
var host = process.env.REDIS_HOST
var theKey = process.env.REDIS_KEY
```
    
`azds up`를 사용 하 여 업데이트 된 서비스를 실행 합니다.

```console
azds up
```
 
`kubectl`를 사용 하 여 비밀이 만들어졌는지 확인 합니다.

```console
kubectl get secret --namespace default -o yaml 
```

## <a name="using-secrets-as-build-arguments"></a>빌드 인수로 비밀 사용

이전 섹션에서는 컨테이너 런타임에 사용 하기 위해 암호를 저장 하 고 사용 하는 방법을 살펴보았습니다. `azds.yaml`를 사용 하 여 개인 NuGet에 대 한 암호와 같은 컨테이너 빌드 시간에 모든 암호를 사용할 수도 있습니다.

`azds.yaml`에서 `<variable name>: ${secret.<secret name>.<secret key>}` 구문을 사용 하 여 빌드 타임 비밀을 *구성* 에 설정 합니다. 예:

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

위의 예제에서 *mynugetsecret* 는 기존 암호이 고 *pattoken* 는 기존 키입니다.

>[!NOTE]
> 비밀 이름 및 키에 `.` 문자가 포함 될 수 있습니다. 비밀을 빌드 인수로 전달할 때 `\`를 사용 하 여 `.`을 이스케이프 합니다. 예를 들어, *토큰*의 키로 *foo. bar* 라는 암호를 전달 하려면 `MYTOKEN: ${secret.foo\.bar.token}`합니다. 또한 암호는 접두사 및 후 위 텍스트를 사용 하 여 평가할 수 있습니다. `MYURL: eus-${secret.foo\.bar.token}-version1`)을 입력합니다. 또한 부모 및 최상위 공간에서 사용 가능한 암호는 빌드 인수로 전달 될 수 있습니다.

Dockerfile에서 *ARG* 지시어를 사용 하 여 비밀을 사용 하 고 dockerfile에서 나중에 동일한 변수를 사용 합니다. 예:

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
 
