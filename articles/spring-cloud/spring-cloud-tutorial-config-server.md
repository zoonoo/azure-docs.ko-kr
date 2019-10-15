---
title: Azure Spring Cloud에서 Config 서버 설정 | Microsoft Docs
description: 이 자습서에서는 Azure Portal에서 Azure Spring Cloud용 Spring Cloud Config 서버를 설정하는 방법을 알아봅니다.
services: spring-cloud
ms.service: spring-cloud
ms.topic: tutorial
ms.reviewer: jeconnoc
ms.author: v-vasuke
author: v-vasuke
ms.date: 08/08/2019
ms.openlocfilehash: f959d216859ded54de52de474ee3150f91bcfff1
ms.sourcegitcommit: d773b5743cb54b8cbcfa5c5e4d21d5b45a58b081
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/08/2019
ms.locfileid: "72038655"
---
# <a name="tutorial-set-up-a-spring-cloud-config-server-for-your-service"></a>자습서: 서비스용 Spring Cloud Config 서버 설정

이 자습서에서는 Spring Cloud Config 서버를 Azure Spring Cloud 서비스에 연결하는 방법을 보여 줍니다.

Spring Cloud Config는 분산 시스템에서 표면화된 구성에 대한 서버 및 클라이언트 쪽 지원을 제공합니다. Config 서버를 사용하면 모든 환경에서 애플리케이션에 대한 외부 속성을 관리할 수 있습니다. 자세한 내용은 [Spring Cloud Config 서버 참조](https://spring.io/projects/spring-cloud-config)를 참조하세요.

## <a name="prerequisites"></a>필수 조건
* Azure 구독. Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다. 
* 이미 프로비저닝되어 실행되는 Azure Spring Cloud 서비스.  [이 빠른 시작](spring-cloud-quickstart-launch-app-cli.md)을 완료하여 Azure Spring Cloud 서비스를 프로비저닝하고 시작합니다.


## <a name="restriction"></a>제한

git 백 엔드에서 __Config 서버__를 사용하는 경우 몇 가지 제한 사항이 있습니다. __Config 서버__ 및 __서비스 검색__에 액세스하기 위해 일부 속성이 애플리케이션 환경에 자동으로 삽입됩니다. 또한 **Config 서버** 파일에서 이러한 속성을 구성하는 경우 충돌 및 예기치 않은 동작이 발생할 수 있습니다.  속성은 다음과 같습니다. 

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

Azure Spring Cloud는 Config 서버 파일을 저장하기 위해 Azure DevOps, GitHub, GitLab 및 Bitbucket을 지원합니다.  리포지토리가 준비되면 아래 지침에 따라 구성 파일을 만들고 여기에 저장합니다.

또한 일부 구성 가능한 속성은 일부 유형에만 사용할 수 있습니다. 다음 하위 섹션에는 각 리포지토리 유형의 속성이 나와 있습니다.


### <a name="public-repository"></a>퍼블릭 리포지토리

퍼블릭 리포지토리를 사용하는 경우 구성 가능한 속성이 더욱 제한됩니다.

퍼블릭 `Git` 리포지토리를 설정하는 데 사용되는 모든 구성 가능한 속성은 아래에 나와 있습니다.

> [!NOTE]
> 지금은 하이픈("-")만 구분된 단어 명명 규칙을 지원합니다. 예를 들어 `default-label`은 사용하지만, `defaultLabel`은 사용하지 않습니다.

| 자산        | 필수 | 기능                                                      |
| :-------------- | -------- | ------------------------------------------------------------ |
| `uri`           | `yes`    | Config 서버 백 엔드로 사용되는 `Git` 리포지토리의 `uri`이며, `http://`, `https://`, `git@` 또는 `ssh://`로 시작해야 합니다. |
| `default-label` | `no`     | `Git` 리포지토리의 기본 레이블은 리포지토리의 `branch name`, `tag name` 또는 `commit-id`여야 합니다. |
| `search-paths`  | `no`     | `Git` 리포지토리의 하위 디렉터리를 검색하는 데 사용되는 문자열 배열입니다. |

------

### <a name="private-repository-with-ssh-authentication"></a>SSH 인증을 사용하는 프라이빗 리포지토리

`Ssh`를 사용하는 프라이빗 `Git` 리포지토리를 설정하는 데 사용되는 모든 구성 가능한 속성은 아래에 나와 있습니다.

> [!NOTE]
> 지금은 하이픈("-")만 구분된 단어 명명 규칙을 지원합니다. 예를 들어 `default-label`은 사용하지만, `defaultLabel`은 사용하지 않습니다.

| 자산                   | 필수 | 기능                                                      |
| :------------------------- | -------- | ------------------------------------------------------------ |
| `uri`                      | `yes`    | Config 서버 백 엔드로 사용되는 `Git` 리포지토리의 `uri`이며, `http://`, `https://`, `git@` 또는 `ssh://`로 시작해야 합니다. |
| `default-label`            | `no`     | `Git` 리포지토리의 기본 레이블은 리포지토리의 `branch name`, `tag name` 또는 `commit-id`여야 합니다. |
| `search-paths`             | `no`     | `Git` 리포지토리의 하위 디렉터리를 검색하는 데 사용되는 문자열 배열입니다. |
| `private-key`              | `no`     | `Git` 리포지토리에 액세스하기 위한 `Ssh` 프라이빗 키이며, `uri`가 `git@` 또는 `ssh://`로 시작하는 경우에 __필요합니다__. |
| `host-key`                 | `no`     | Git 리포지토리 서버의 호스트 키이며, `host-key-algorithm`에 포함된 알고리즘 접두사를 포함하지 않아야 합니다. |
| `host-key-algorithm`       | `no`     | 호스트 키 알고리즘이며, `ssh-dss`, `ssh-rsa`, `ecdsa-sha2-nistp256`, `ecdsa-sha2-nistp384` 및 `ecdsa-sha2-nistp521` 중 하나여야 합니다. `host-key`가 있는 경우에만 필요합니다. |
| `strict-host-key-checking` | `no`     | 프라이빗 `host-key`를 활용하는 경우 Config 서버가 시작되지 않을지 여부를 나타내며, `true`(기본값) 또는 `false`여야 합니다. |

-----

### <a name="private-repository-with-basic-authentication"></a>기본 인증을 사용하는 프라이빗 리포지토리

기본 인증을 사용하는 프라이빗 Git 리포지토리를 설정하는 데 사용되는 모든 구성 가능한 속성은 아래에 나와 있습니다.

> [!NOTE]
> 지금은 하이픈("-")만 구분된 단어 명명 규칙을 지원합니다. 예를 들어 `default-label`은 사용하지만, `defaultLabel`은 사용하지 않습니다.

| 자산        | 필수 | 기능                                                      |
| :-------------- | -------- | ------------------------------------------------------------ |
| `uri`           | `yes`    | Config 서버 백 엔드로 사용되는 `Git` 리포지토리의 `uri`이며, `http://`, `https://`, `git@` 또는 `ssh://`로 시작해야 합니다. |
| `default-label` | `no`     | `Git` 리포지토리의 기본 레이블은 리포지토리의 `branch name`, `tag name` 또는 `commit-id`여야 합니다. |
| `search-paths`  | `no`     | `Git` 리포지토리의 하위 디렉터리를 검색하는 데 사용되는 문자열 배열입니다. |
| `username`      | `no`     | `Git` 리포지토리 서버에 액세스하는 데 사용되는 `username`이며, `Git` 리포지토리 서버에서 `Http Basic Authentication`을 지원하는 데 __필요합니다__. |
| `password`      | `no`     | `Git` 리포지토리 서버에 액세스하는 데 사용되는 암호이며, `Git` 리포지토리 서버에서 `Http Basic Authentication`을 지원하는 데 __필요합니다__. |

> [!NOTE]
> GitHub와 같은 일부 `Git` 리포지토리 서버는 `HTTP Basic Authentication`의 암호로 "개인 토큰" 또는 "액세스 토큰"을 지원합니다. 여기서도 이러한 토큰을 암호로 사용할 수 있으며, "개인 토큰" 또는 "액세스 토큰"은 만료되지 않습니다. 그러나 BitBucket 및 Azure DevOps와 같은 Git 리포지토리 서버의 경우 토큰이 1~2시간 후에 만료되므로 Azure Spring Cloud에서는 이 옵션을 사용할 수 없습니다.

### <a name="git-repositories-with-pattern"></a>패턴을 사용하는 Git 리포지토리

패턴을 사용하는 Git 리포지토리를 설정하는 데 사용되는 모든 구성 가능한 속성은 아래에 나와 있습니다.

> [!NOTE]
> 지금은 하이픈("-")만 구분된 단어 명명 규칙을 지원합니다. 예를 들어 `default-label`은 사용하지만, `defaultLabel`은 사용하지 않습니다.

| 자산                           | 필수         | 기능                                                      |
| :--------------------------------- | ---------------- | ------------------------------------------------------------ |
| `repos`                            | `no`             | 맵은 지정된 이름의 `Git` 리포지토리 설정으로 구성됩니다. |
| `repos."uri"`                      | `repos`가 있는 경우 `yes` | Config 서버 백 엔드로 사용되는 `Git` 리포지토리의 `uri`이며, `http://`, `https://`, `git@` 또는 `ssh://`로 시작해야 합니다. |
| `repos."name"`                     | `repos`가 있는 경우 `yes` | 하나의 `Git` 리포지토리를 식별하는 이름이며, `repos`가 있는 경우에만 __필요합니다__. 위 예제의 경우 `team-A`, `team-B`입니다. |
| `repos."pattern"`                  | `no`             | 애플리케이션 이름을 일치시키는 데 사용되는 문자열 배열이며, 각 패턴마다 와일드카드가 있는 `{application}/{profile}` 형식을 사용합니다. |
| `repos."default-label"`            | `no`             | `Git` 리포지토리의 기본 레이블은 리포지토리의 `branch name`, `tag name` 또는 `commit-id`여야 합니다. |
| `repos."search-paths`"             | `no`             | `Git` 리포지토리의 하위 디렉터리를 검색하는 데 사용되는 문자열 배열입니다. |
| `repos."username"`                 | `no`             | `Git` 리포지토리 서버에 액세스하는 데 사용되는 `username`이며, `Git` 리포지토리 서버에서 `Http Basic Authentication`을 지원하는 데 __필요합니다__. |
| `repos."password"`                 | `no`             | `Git` 리포지토리 서버에 액세스하는 데 사용되는 암호이며, `Git` 리포지토리 서버에서 `Http Basic Authentication`을 지원하는 데 __필요합니다__. |
| `repos."private-key"`              | `no`             | `Git` 리포지토리에 액세스하기 위한 `Ssh` 프라이빗 키이며, `uri`가 `git@` 또는 `ssh://`로 시작하는 경우에 __필요합니다__. |
| `repos."host-key"`                 | `no`             | Git 리포지토리 서버의 호스트 키이며, `host-key-algorithm`에 포함된 알고리즘 접두사를 포함하지 않아야 합니다. |
| `repos."host-key-algorithm"`       | `no`             | 호스트 키 알고리즘이며, `ssh-dss`, `ssh-rsa`, `ecdsa-sha2-nistp256`, `ecdsa-sha2-nistp384` 및 `ecdsa-sha2-nistp521` 중 하나여야 합니다. `host-key`가 있는 경우에만 __필요합니다__. |
| `repos."strict-host-key-checking"` | `no`             | 프라이빗 `host-key`를 활용하는 경우 Config 서버가 시작되지 않을지 여부를 나타내며, `true`(기본값) 또는 `false`여야 합니다. |

### <a name="import-applicationyml-file-from-spring-cloud-config"></a>Spring Cloud Config에서 `application.yml` 파일 가져오기

일부 기본 Config 서버 설정은 [Spring Cloud Config](https://spring.io/projects/spring-cloud-config) 웹 사이트에서 직접 가져올 수 있습니다. 이 작업은 Azure Portal에서 직접 수행할 수 있으므로 이제 Config 서버 파일 또는 리포지토리를 준비하는 단계를 수행할 필요가 없습니다.

## <a name="attaching-your-config-server-repository-to-azure-spring-cloud"></a>Azure Spring Cloud에 Config 서버 리포지토리 연결

이제 구성 파일을 리포지토리에 저장했으므로 Azure Spring Cloud를 연결해야 합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

1. Azure Spring Cloud **개요** 페이지로 이동합니다.

1. 왼쪽 메뉴에서 **설정** 제목 아래의 **Config 서버** 탭으로 이동합니다.

### <a name="public-repository"></a>퍼블릭 리포지토리

퍼블릭 리포지토리이면 **퍼블릭** 단추를 클릭하고 URL을 붙여넣기만 하면 됩니다.

### <a name="private-repository"></a>프라이빗 리포지토리

Azure Spring Cloud는 SSH 인증을 지원합니다. 퍼블릭 키를 리포지토리에 추가하려면 Azure Portal의 지침을 따릅니다. 그런 다음, 프라이빗 키를 구성 파일에 포함시켜야 합니다.

**적용**을 클릭하여 Config 서버 설정을 완료합니다.


## <a name="delete-your-app-configuration"></a>앱 구성 삭제

구성 파일이 저장되면 **구성** 탭에 **앱 구성 삭제** 단추가 표시됩니다. 이 단추를 사용하면 기존 설정이 완전히 지워집니다. GitHub에서 Azure DevOps로 이동하는 것과 같이 Config 서버를 다른 원본에 연결하려면 이 작업을 수행해야 합니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 Config 서버를 사용하도록 설정하고 구성하는 방법을 알아보았습니다. 애플리케이션을 관리하는 방법에 대해 자세히 알아보려면 앱을 수동으로 크기 조정하는 방법에 대한 자습서로 계속 진행하세요.

> [!div class="nextstepaction"]
> [Azure Spring Cloud 애플리케이션을 수동으로 크기 조정하는 방법 알아보기](spring-cloud-tutorial-scale-manual.md)

