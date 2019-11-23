---
title: Azure Spring Cloud에서 Config 서버 설정 | Microsoft Docs
description: 이 자습서에서는 Azure Portal에서 Azure Spring Cloud용 Spring Cloud Config 서버를 설정하는 방법을 알아봅니다.
ms.service: spring-cloud
ms.topic: tutorial
ms.author: jeconnoc
author: jpconnock
ms.date: 10/18/2019
ms.openlocfilehash: 6742e1a5924fdcd1fe00f49ac790209a907d1bac
ms.sourcegitcommit: 2d3740e2670ff193f3e031c1e22dcd9e072d3ad9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/16/2019
ms.locfileid: "74132783"
---
# <a name="tutorial-set-up-a-spring-cloud-config-server-for-your-service"></a>자습서: 서비스용 Spring Cloud Config 서버 설정

이 자습서에서는 Spring Cloud Config 서버를 Azure Spring Cloud 서비스에 연결하는 방법을 보여 줍니다.

Spring Cloud Config는 분산 시스템에서 표면화된 구성에 대한 서버 및 클라이언트 쪽 지원을 제공합니다. Config 서버를 사용하면 모든 환경에서 애플리케이션에 대한 외부 속성을 관리할 수 있습니다. 자세한 내용은 [Spring Cloud Config 서버 참조](https://spring.io/projects/spring-cloud-config)를 참조하세요.

## <a name="prerequisites"></a>필수 조건
* Azure 구독. Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다. 
* 이미 프로비저닝되어 실행되는 Azure Spring Cloud 서비스.  [이 빠른 시작](spring-cloud-quickstart-launch-app-cli.md)을 완료하여 Azure Spring Cloud 서비스를 프로비저닝하고 시작합니다.

## <a name="restriction"></a>제한

git 백 엔드에서 __Config 서버__를 사용하는 경우 몇 가지 제한 사항이 있습니다. __Config 서버__ 및 __서비스 검색__에 액세스하기 위해 일부 속성이 애플리케이션 환경에 자동으로 삽입됩니다. 또한 **Config 서버** 파일에서 이러한 속성을 구성하는 경우 충돌 및 예기치 않은 동작이 발생할 수 있습니다. 속성은 다음과 같습니다. 

```yaml
eureka.client.service-url.defaultZone
eureka.client.tls.keystore
server.port
spring.cloud.config.tls.keystore
spring.application.name
```
> [!CAUTION]
> 위의 속성을 __Config 서버__ 애플리케이션 파일에 배치하지 __않는__ 것이 가장 좋습니다.

## <a name="create-your-config-server-files"></a>Config 서버 파일 만들기

Azure Spring Cloud는 Config 서버 파일을 저장하기 위해 Azure DevOps, GitHub, GitLab 및 Bitbucket을 지원합니다. 리포지토리가 준비되면 아래 지침에 따라 구성 파일을 만들고 여기에 저장합니다.

또한 일부 구성 가능한 속성은 일부 유형에만 사용할 수 있습니다. 다음 하위 섹션에는 각 리포지토리 유형의 속성이 나와 있습니다.

### <a name="public-repository"></a>퍼블릭 리포지토리

퍼블릭 리포지토리를 사용하는 경우 구성 가능한 속성이 더욱 제한됩니다.

퍼블릭 `Git` 리포지토리를 설정하는 데 사용되는 모든 구성 가능한 속성은 아래에 나와 있습니다.

> [!NOTE]
> 현재 지원되는 유일한 명명 규칙은 하이픈("-")을 사용하여 단어를 구분하는 것입니다. 예를 들어 `default-label`은 사용하지만, `defaultLabel`은 사용하지 않습니다.

| 자산        | 필수 | 기능                                                      |
| :-------------- | -------- | ------------------------------------------------------------ |
| `uri`           | `yes`    | Config 서버 백 엔드로 사용되는 `Git` 리포지토리의 `uri`이며, `http://`, `https://`, `git@` 또는 `ssh://`로 시작해야 합니다. |
| `default-label` | `no`     | `Git` 리포지토리의 기본 레이블이며, 리포지토리의 `branch name`, `tag name` 또는 `commit-id`여야 합니다. |
| `search-paths`  | `no`     | `Git` 리포지토리의 하위 디렉터리를 검색하는 데 사용되는 문자열 배열입니다. |

------

### <a name="private-repository-with-ssh-authentication"></a>SSH 인증을 사용하는 프라이빗 리포지토리

`Ssh`를 사용하는 프라이빗 `Git` 리포지토리를 설정하는 데 사용되는 모든 구성 가능한 속성은 아래에 나와 있습니다.

> [!NOTE]
> 현재 지원되는 유일한 명명 규칙은 하이픈("-")을 사용하여 단어를 구분하는 것입니다. 예를 들어 `default-label`은 사용하지만, `defaultLabel`은 사용하지 않습니다.

| 자산                   | 필수 | 기능                                                      |
| :------------------------- | -------- | ------------------------------------------------------------ |
| `uri`                      | `yes`    | Config 서버 백 엔드로 사용되는 `Git` 리포지토리의 `uri`이며, `http://`, `https://`, `git@` 또는 `ssh://`로 시작해야 합니다. |
| `default-label`            | `no`     | `Git` 리포지토리의 기본 레이블이며, 리포지토리의 `branch name`, `tag name` 또는 `commit-id`여야 합니다. |
| `search-paths`             | `no`     | `Git` 리포지토리의 하위 디렉터리를 검색하는 데 사용되는 문자열 배열입니다. |
| `private-key`              | `no`     | `Git` 리포지토리에 액세스하기 위한 `Ssh` 프라이빗 키이며, `uri`가 `git@` 또는 `ssh://`로 시작하는 경우에 __필요합니다__. |
| `host-key`                 | `no`     | Git 리포지토리 서버의 호스트 키이며, `host-key-algorithm`에 포함된 알고리즘 접두사를 포함하지 않아야 합니다. |
| `host-key-algorithm`       | `no`     | 호스트 키 알고리즘은 `ssh-dss`, `ssh-rsa`, `ecdsa-sha2-nistp256`, `ecdsa-sha2-nistp384` 또는 `ecdsa-sha2-nistp521`여야 합니다. `host-key`가 있는 경우에만 필요합니다. |
| `strict-host-key-checking` | `no`     | 프라이빗 `host-key`를 활용하는 경우 config 서버가 시작에 실패하는지 여부를 나타냅니다. `true`(기본값) 또는 `false`여야 합니다. |

-----

### <a name="private-repository-with-basic-authentication"></a>기본 인증을 사용하는 프라이빗 리포지토리

기본 인증을 사용하는 프라이빗 Git 리포지토리를 설정하는 데 사용되는 모든 구성 가능한 속성은 아래에 나와 있습니다.

> [!NOTE]
> 현재 지원되는 유일한 명명 규칙은 하이픈("-")을 사용하여 단어를 구분하는 것입니다. 예를 들어 `defaultLabel`이 아닌 `default-label`을 사용합니다.

| 자산        | 필수 | 기능                                                      |
| :-------------- | -------- | ------------------------------------------------------------ |
| `uri`           | `yes`    | Config 서버 백 엔드로 사용되는 `Git` 리포지토리의 `uri`이며, `http://`, `https://`, `git@` 또는 `ssh://`로 시작해야 합니다. |
| `default-label` | `no`     | `Git` 리포지토리의 기본 레이블이며, 리포지토리의 `branch name`, `tag name` 또는 `commit-id`여야 합니다. |
| `search-paths`  | `no`     | `Git` 리포지토리의 하위 디렉터리를 검색하는 데 사용되는 문자열 배열입니다. |
| `username`      | `no`     | `Git` 리포지토리 서버에 액세스하는 데 사용되는 `username`이며, `Git` 리포지토리 서버에서 `Http Basic Authentication`을 지원하는 데 __필요합니다__. |
| `password`      | `no`     | `Git` 리포지토리 서버에 액세스하는 데 사용되는 암호이며, `Git` 리포지토리 서버에서 `Http Basic Authentication`을 지원하는 데 __필요합니다__. |

> [!NOTE]
> 많은 `Git` 리포지토리 서버는 `HTTP Basic Authentication`에 대한 암호 대신 토큰 사용을 지원합니다. GitHub와 같은 일부 리포지토리는 토큰을 무기한으로 유지할 수 있도록 허용합니다. 그러나 Azure DevOps를 비롯한 일부 Git 리포지토리 서버는 몇 시간 안에 토큰을 강제로 만료시킵니다. 토큰이 만료되는 리포지토리는 Azure Spring Cloud에서 토큰 기반 인증을 사용하면 안 됩니다.

### <a name="git-repositories-with-pattern"></a>패턴을 사용하는 Git 리포지토리

패턴을 사용하는 Git 리포지토리를 설정하는 데 사용되는 모든 구성 가능한 속성은 아래에 나와 있습니다.

> [!NOTE]
> 현재 지원되는 유일한 명명 규칙은 하이픈("-")을 사용하여 단어를 구분하는 것입니다. 예를 들어 `defaultLabel`이 아닌 `default-label`을 사용합니다.

| 자산                           | 필수         | 기능                                                      |
| :--------------------------------- | ---------------- | ------------------------------------------------------------ |
| `repos`                            | `no`             | 지정된 이름을 가진 `Git` 리포지토리의 설정으로 구성된 맵입니다. |
| `repos."uri"`                      | `repos`가 있는 경우 `yes` | Config 서버 백 엔드로 사용되는 `Git` 리포지토리의 `uri`이며, `http://`, `https://`, `git@` 또는 `ssh://`로 시작해야 합니다. |
| `repos."name"`                     | `repos`가 있는 경우 `yes` | 하나의 `Git` 리포지토리를 식별하는 이름이며, `repos`가 있는 경우에만 __필요합니다__. 위 예제의 경우 `team-A`, `team-B`입니다. |
| `repos."pattern"`                  | `no`             | 애플리케이션 이름을 매칭하는 데 사용되는 문자열 배열입니다. 각 패턴의 경우 와일드 카드가 포함된 `{application}/{profile}` 형식을 사용합니다. |
| `repos."default-label"`            | `no`             | `Git` 리포지토리의 기본 레이블이며, 리포지토리의 `branch name`, `tag name` 또는 `commit-id`여야 합니다. |
| `repos."search-paths`"             | `no`             | `Git` 리포지토리의 하위 디렉터리를 검색하는 데 사용되는 문자열 배열입니다. |
| `repos."username"`                 | `no`             | `Git` 리포지토리 서버에 액세스하는 데 사용되는 `username`이며, `Git` 리포지토리 서버에서 `Http Basic Authentication`을 지원하는 데 __필요합니다__. |
| `repos."password"`                 | `no`             | `Git` 리포지토리 서버에 액세스하는 데 사용되는 암호이며, `Git` 리포지토리 서버에서 `Http Basic Authentication`을 지원하는 데 __필요합니다__. |
| `repos."private-key"`              | `no`             | `Git` 리포지토리에 액세스하기 위한 `Ssh` 프라이빗 키이며, `uri`가 `git@` 또는 `ssh://`로 시작하는 경우에 __필요합니다__. |
| `repos."host-key"`                 | `no`             | Git 리포지토리 서버의 호스트 키이며, `host-key-algorithm`에 포함된 알고리즘 접두사를 포함하지 않아야 합니다. |
| `repos."host-key-algorithm"`       | `no`             | 호스트 키 알고리즘은 `ssh-dss`, `ssh-rsa`, `ecdsa-sha2-nistp256`, `ecdsa-sha2-nistp384` 또는 `ecdsa-sha2-nistp521`여야 합니다. `host-key`가 있는 경우에만 __필요합니다__. |
| `repos."strict-host-key-checking"` | `no`             | 프라이빗 `host-key`를 활용하는 경우 config 서버가 시작에 실패하는지 여부를 나타냅니다. `true`(기본값) 또는 `false`여야 합니다. |

## <a name="attaching-your-config-server-repository-to-azure-spring-cloud"></a>Azure Spring Cloud에 Config 서버 리포지토리 연결

이제 구성 파일을 리포지토리에 저장했으므로 Azure Spring Cloud를 연결해야 합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

1. Azure Spring Cloud **개요** 페이지로 이동합니다.

1. 구성할 서비스를 선택합니다.

1. 서비스의 왼쪽 메뉴에 있는 **설정** 제목 아래의 **구성 서버** 탭을 선택합니다.

![창 스크린샷](media/spring-cloud-tutorial-config-server/portal-config-server.png)

### <a name="input-repository-information-directly-to-the-azure-portal"></a>Azure Portal에 직접 리포지토리 정보 입력

#### <a name="default-repository"></a>기본 리포지토리

* 퍼블릭 리포지토리: **기본 리포지토리** 섹션에서 **Uri** 섹션에 리포지토리 URI를 붙여넣습니다.  **레이블**을 `config`로 설정합니다. **인증** 설정이 **공용**인지 확인한 다음, **적용**을 선택하여 완료합니다. 

* 프라이빗 리포지토리: Azure Spring Cloud는 기본 암호/토큰 기반 인증 및 SSH를 지원합니다.

    * 기본 인증: **기본 리포지토리** 섹션에서 **Uri** 섹션에 리포지토리 URI를 붙여넣은 다음, **인증**을 클릭합니다. **인증 유형**을 **기본**으로 선택하고 사용자 이름 및 암호/토큰을 입력하여 Azure Spring Cloud에 대한 액세스 권한을 부여합니다. **확인**을 클릭하고 **적용**을 클릭하여 Config 서버 설정을 완료합니다.

    ![창 스크린샷](media/spring-cloud-tutorial-config-server/basic-auth.png)
    
    > [!CAUTION]
    > GitHub와 같은 일부 Git 리포지토리 서버는 `personal-token` 또는 `access-token`을 암호처럼 **기본 인증**에 사용합니다. 이러한 종류의 토큰은 절대 만료되지 않으므로 Azure Spring Cloud에서 암호로 사용할 수 있습니다. 그러나 BitBucket이나 Azure DevOps와 같은 다른 Git 리포지토리 서버의 경우 `access-token`이 1~2시간 후에 만료됩니다. 즉, Azure Spring Cloud에서 이러한 리포지토리 서버를 사용할 때 해당 옵션을 사용할 수 없습니다.

    * SSH: **기본 리포지토리** 섹션에서 **Uri** 섹션에 리포지토리 URI를 붙여넣은 다음, **인증**을 클릭합니다. **인증 유형**으로 **SSH**를 선택하고 **프라이빗 키**를 입력합니다. 선택 사항으로 **호스트 키** 및 **호스트 키 알고리즘**을 지정할 수 있습니다. config 서버 리포지토리에 공개 키를 포함해야 합니다. **확인**을 클릭하고 **적용**을 클릭하여 Config 서버 설정을 완료합니다.

    ![창 스크린샷](media/spring-cloud-tutorial-config-server/ssh-auth.png)

#### <a name="pattern-repository"></a>패턴 리포지토리

선택 사항인 **패턴 리포지토리**를 사용하여 서비스를 구성하려면 **기본 리포지토리**와 같은 방법으로 **URI** 및 **인증**을 지정합니다. 패턴의 **이름**을 포함한 다음, **적용**을 클릭하여 인스턴스에 연결해야 합니다. 

### <a name="enter-repository-information-into-a-yaml-file"></a>YAML 파일에 리포지토리 정보 입력

리포지토리 설정을 사용하여 YAML 파일을 작성한 경우 로컬 머신에서 Azure Spring Cloud로 직접 YAML 파일을 가져올 수 있습니다. 기본 인증을 사용하는 프라이빗 리포지토리의 간단한 YAML 파일은 다음과 같습니다.

```yml
spring:
    cloud:
        config:
            server:
                git:
                    uri: https://github.com/azure-spring-cloud-samples/config-server-repository.git
                    username: <username>
                    password: <password/token>

```

**설정 가져오기** 단추를 클릭한 다음, 프로젝트 디렉터리에서 `.yml` 파일을 선택합니다. **가져오기**를 클릭하면 **알림**에서 `async` 작업이 팝업됩니다. 1-2분 후 성공 여부가 보고됩니다.

![창 스크린샷](media/spring-cloud-tutorial-config-server/local-yml-success.png)


Azure Portal에 YAML 파일의 정보가 표시되는 것이 보입니다. **적용**을 클릭하여 완료합니다. 


## <a name="delete-your-app-configuration"></a>앱 구성 삭제

구성 파일이 저장되면 **구성** 탭에 **앱 구성 삭제** 단추가 표시됩니다. 이 단추를 사용하면 기존 설정이 완전히 지워집니다. GitHub에서 Azure DevOps로 이동하는 것과 같이 Config 서버를 다른 원본에 연결하려면 이 작업을 수행해야 합니다.



## <a name="next-steps"></a>다음 단계

이 자습서에서는 Config 서버를 사용하도록 설정하고 구성하는 방법을 알아보았습니다. 애플리케이션을 관리하는 방법에 대해 자세히 알아보려면 앱을 수동으로 크기 조정하는 방법에 대한 자습서로 계속 진행하세요.

> [!div class="nextstepaction"]
> [Azure Spring Cloud 애플리케이션을 수동으로 크기 조정하는 방법 알아보기](spring-cloud-tutorial-scale-manual.md)

