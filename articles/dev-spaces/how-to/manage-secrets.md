---
title: Azure Dev Space로 작업할 때 비밀을 관리하는 방법
services: azure-dev-spaces
ms.date: 12/03/2019
ms.topic: conceptual
description: Azure에서 컨테이너 및 마이크로 서비스가 있는 Kubernetes 개발 환경을 빠르게 만듭니다.
keywords: Docker, Kubernetes, Azure, AKS, Azure Container Service, 컨테이너
ms.openlocfilehash: b184f72dfbbfe093443ab8a9b79bafbece3a3d51
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/03/2019
ms.locfileid: "74790179"
---
# <a name="how-to-manage-secrets-when-working-with-an-azure-dev-space"></a>Azure Dev Space로 작업할 때 비밀을 관리하는 방법

서비스에는 데이터베이스 또는 기타 보안 Azure 서비스에 대한 특정 암호, 연결 문자열 및 기타 비밀이 필요할 수 있습니다. 구성 파일에서 이러한 비밀의 값을 설정하여 코드에서 환경 변수로 사용할 수 있습니다.  이러한 항목은 비밀의 보안을 손상시키지 하도록 주의해서 처리되어야 합니다.

Azure Dev Spaces은 Azure Dev Spaces 클라이언트 도구에서 생성 된 투구 차트에 비밀을 저장 하기 위한 두 가지 권장 옵션을 제공 합니다. `values.dev.yaml` 파일에서 `azds.yaml`직접 인라인 합니다. `values.yaml`에 암호를 저장 하지 않는 것이 좋습니다. 이 문서에 정의 된 클라이언트 도구에 의해 생성 된 투구 차트를 사용 하는 두 가지 방법 중 하나를 사용 하 여 사용자 고유의 투구 차트를 만드는 경우 투구 차트를 직접 사용 하 여 비밀을 관리 하 고 저장할 수 있습니다.

## <a name="method-1-valuesdevyaml"></a>방법 1: values.dev.yaml
1. Azure Dev Spaces에 대해 활성화된 프로젝트로 VS Code를 엽니다.
2. 다음 예제와 같이 기존 _azds_ 와 동일한 폴더에 라는 파일을 추가 하 고 비밀 키와 값을 _정의 합니다._

    ```yaml
    secrets:
      redis:
        port: "6380"
        host: "contosodevredis.redis.cache.windows.net"
        key: "secretkeyhere"
    ```
     
3. _azds_ 파일이 있는 경우 _이 파일을_ 이미 참조 합니다. 다른 파일 이름을 선호 하는 경우에는 install. values 섹션을 업데이트 합니다.

    ```yaml
    install:
      values:
      - values.dev.yaml?
      - secrets.dev.yaml?
    ```
 
4. 다음 예제와 같이 환경 변수로 이러한 비밀을 참조하도록 서비스 코드를 수정합니다.

    ```
    var redisPort = process.env.REDIS_PORT
    var host = process.env.REDIS_HOST
    var theKey = process.env.REDIS_KEY
    ```
    
5. 이러한 변경 내용으로 클러스터에서 실행되는 서비스를 업데이트합니다. 명령줄에서 다음 명령을 실행합니다.

    ```
    azds up
    ```
 
6. (선택 사항) 명령줄에서 이러한 비밀이 만들어졌는지 확인합니다.

      ```
      kubectl get secret --namespace default -o yaml 
      ```

7. 원본 제어에서 비밀을 커밋하지 않도록 _.gitignore_ 파일에 _values.dev.yaml_을 추가해야 합니다.
 
 
## <a name="method-2-azdsyaml"></a>방법 2: azds
1.  _azds.yaml_의 yaml 섹션 구성/개발/설치 아래에서 비밀을 설정합니다. 해당 위치에서 비밀 값을 직접 입력할 수도 있지만 _azds.yaml_이 원본 제어로 확인되므로 권장되지 않습니다. 대신 "$PLACEHOLDER" 구문을 사용하여 자리 표시자를 추가합니다.

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
     
2.  _azds.yaml_과 동일한 폴더에 _.env_ 파일을 만듭니다. 표준 키=값 표기법을 사용하여 비밀을 입력합니다. 원본 제어로 _.env_ 파일을 커밋하지 마십시오. (Git 기반 버전 제어 시스템의 소스 제어에서 생략 하려면 _.gitignore_ 파일에 추가 합니다.) 다음 예제에서는 _env_ 파일을 보여 줍니다.

    ```
    REDIS_PORT=3333
    REDIS_HOST=myredishost
    REDIS_KEY=myrediskey
    ```
2.  다음 예제와 같이 코드에서 이러한 비밀을 참조하도록 서비스 원본 코드를 수정합니다.

    ```
    var redisPort = process.env.REDIS_PORT
    var host = process.env.REDIS_HOST
    var theKey = process.env.REDIS_KEY
    ```
 
3.  이러한 변경 내용으로 클러스터에서 실행되는 서비스를 업데이트합니다. 명령줄에서 다음 명령을 실행합니다.

    ```
    azds up
    ```

4.  (선택 사항) kubectl에서 비밀을 봅니다.

    ```
    kubectl get secret --namespace default -o yaml
    ```

## <a name="passing-secrets-as-build-arguments"></a>빌드 인수로 암호 전달

이전 섹션에서는 컨테이너 실행 시 사용할 암호를 전달 하는 방법을 살펴보았습니다. `azds.yaml`를 사용 하 여 개인 NuGet에 대 한 암호와 같은 컨테이너 빌드 시간에 암호를 전달할 수도 있습니다.

`azds.yaml`에서 `<variable name>: ${secret.<secret name>.<secret key>}` 구문을 사용 하 여 빌드 타임 비밀을 *구성* 에 설정 합니다. 다음은 그 예입니다.

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
> 비밀 이름 및 키에 `.` 문자가 포함 될 수 있습니다. 비밀을 빌드 인수로 전달할 때 `\`를 사용 하 여 `.`을 이스케이프 합니다. 예를 들어, *토큰*의 키로 *foo. bar* 라는 암호를 전달 하려면 `MYTOKEN: ${secret.foo\.bar.token}`합니다. 또한 암호는 접두사 및 후 위 텍스트를 사용 하 여 평가할 수 있습니다. 예: `MYURL: eus-${secret.foo\.bar.token}-version1` 또한 부모 및 최상위 공간에서 사용 가능한 암호는 빌드 인수로 전달 될 수 있습니다.

Dockerfile에서 *ARG* 지시어를 사용 하 여 비밀을 사용 하 고 dockerfile에서 나중에 동일한 변수를 사용 합니다. 다음은 그 예입니다.

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
 
