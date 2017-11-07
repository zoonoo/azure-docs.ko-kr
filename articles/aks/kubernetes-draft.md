---
title: "AKS 및 Azure Container Registry에서 Draft 사용 | Microsoft Docs"
description: "AKS 및 Azure Container Registry에서 Draft 사용"
services: container-service
documentationcenter: 
author: neilpeterson
manager: timlt
editor: 
tags: draft, helm, aks, azure-container-service
keywords: "Docker, 컨테이너, 마이크로 서비스, Kubernetes, Draft, Azure"
ms.service: container-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/24/2017
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 3e607a6ce5662f6ff597fafbcec8c864f25ff54c
ms.sourcegitcommit: c5eeb0c950a0ba35d0b0953f5d88d3be57960180
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/24/2017
---
# <a name="use-draft-with-azure-container-service-aks"></a>Azure Container Service(AKS)에서 Draft 사용

Draft는 Kubernetes 클러스터에서 코드를 패키지화하고 실행하는 데 도움이 되는 오픈 소스 도구입니다. Draft는 개발 반복 주기(코드 개발 중이지만 버전 제어에 커밋하기 전)를 목표로 합니다. Draft를 사용하면 코드가 변경될 때 Kubernetes에 응용 프로그램을 신속하게 재배포할 수 있습니다. Draft에 대한 자세한 내용은 [Github의 Draft 문서](https://github.com/Azure/draft/tree/master/docs)를 참조하세요.

이 문서에서는 AKS에서 Kubernetes 클러스터와 함께 Draft를 사용하는 방법에 대해 자세히 설명합니다.

## <a name="prerequisites"></a>필수 조건

이 문서에서는 AKS 클러스터를 만들고 클러스터와의 kubectl 연결을 설정했다고 가정합니다. 이러한 항목이 필요한 경우 [AKS 빠른 시작](./kubernetes-walkthrough.md)을 참조하세요.

ACR(Azure Container Registry)에서 개인 Docker 레지스트리도 필요합니다. ACR 인스턴스를 배포하는 지침은 [Azure Container Registry 빠른 시작](../container-registry/container-registry-get-started-azure-cli.md)을 참조하세요.

## <a name="install-helm"></a>Helm 설치

Helm CLI는 개발 시스템에서 실행되는 클라이언트로, Helm 차트가 있는 응용 프로그램을 시작, 중지 및 관리할 수 있습니다.

Mac에서 Helm CLI를 설치하려면 `brew`를 사용합니다. 추가 설치 옵션에 대해서는 [Helm 설치](https://github.com/kubernetes/helm/blob/master/docs/install.md)를 참조하세요.

```console
brew install kubernetes-helm
```

출력:

```
==> Downloading https://homebrew.bintray.com/bottles/kubernetes-helm-2.6.2.sierra.bottle.1.tar.gz
######################################################################## 100.0%
==> Pouring kubernetes-helm-2.6.2.sierra.bottle.1.tar.gz
==> Caveats
Bash completion has been installed to:
  /usr/local/etc/bash_completion.d
==> Summary
🍺  /usr/local/Cellar/kubernetes-helm/2.6.2: 50 files, 132.4MB
```

## <a name="install-draft"></a>Draft 설치

Draft CLI는 개발 시스템에서 실행되는 클라이언트로, Kubernetes 클러스터에 코드를 신속하게 배포할 수 있습니다.

Mac에서 Draft CLI를 설치하려면 `brew`를 사용합니다. 추가 설치 옵션에 대해서는 [Draft 설치 가이드](https://github.com/Azure/draft/blob/master/docs/install.md)를 참조하세요.

```console
brew install draft
```

출력:

```
==> Installing draft from azure/draft
==> Downloading https://azuredraft.blob.core.windows.net/draft/draft-v0.7.0-darwin-amd64.tar.gz
Already downloaded: /Users/neilpeterson/Library/Caches/Homebrew/draft-0.7.0.tar.gz
==> /usr/local/Cellar/draft/0.7.0/bin/draft init --client-only
🍺  /usr/local/Cellar/draft/0.7.0: 6 files, 61.2MB, built in 1 second
```

## <a name="configure-draft"></a>Draft 구성

Draft를 구성할 때 컨테이너 레지스트리를 지정해야 합니다. 이 예제에서는 Azure Container Registry를 사용합니다.

다음 명령을 실행하여 ACR 인스턴스의 이름과 로그인 서버 이름을 가져옵니다. 명령을 ACR 인스턴스가 있는 리소스 그룹의 이름으로 업데이트합니다.

```console
az acr list --resource-group <resource group> --query "[].{Name:name,LoginServer:loginServer}" --output table
```

ACR 인스턴스 암호도 필요합니다.

ACR 암호를 반환하려면 다음 명령을 실행합니다. ACR 인스턴스 이름으로 명령을 업데이트합니다.

```console
az acr credential show --name <acr name> --query "passwords[0].value" --output table
```

`draft init` 명령으로 Draft를 초기화합니다.

```console
draft init
```

이 프로세스 중에 컨테이너 레지스트리 자격 증명을 묻는 메시지가 나타납니다. Azure Container Registry를 사용하는 경우 레지스트리 URL은 ACR 로그인 서버 이름이고 사용자 이름은 ACR 인스턴스 이름이며 암호는 ACR 암호입니다.

```console
1. Enter your Docker registry URL (e.g. docker.io/myuser, quay.io/myuser, myregistry.azurecr.io): <ACR Login Server>
2. Enter your username: <ACR Name>
3. Enter your password: <ACR Password>
```

완료되면 Draft가 Kubernetes 클러스터에서 구성되고 사용할 준비가 됩니다.

```
Draft has been installed into your Kubernetes Cluster.
Happy Sailing!
```

## <a name="run-an-application"></a>응용 프로그램 실행

Draft 리포지토리에는 Draft 데모를 보여 주는 데 사용할 수 있는 몇 가지 샘플 응용 프로그램이 포함되어 있습니다. 리포지토리의 복제된 복사본을 만듭니다.

```console
git clone https://github.com/Azure/draft
```

Java 예제 디렉터리로 변경합니다.

```console
cd draft/examples/java/
```

`draft create` 명령을 사용하여 프로세스를 시작합니다. 이 명령은 Kubernetes 클러스터에서 응용 프로그램을 실행하는 데 사용되는 아티팩트를 만듭니다. 이러한 항목에는 Dockerfile, Helm 차트 포함 및 Draft 구성 파일인 `draft.toml` 파일이 포함됩니다.

```console
draft create
```

출력:

```
--> Draft detected the primary language as Java with 92.205567% certainty.
--> Ready to sail
```

Kubernetes 클러스터에서 응용 프로그램을 실행하려면 `draft up` 명령을 사용합니다. 이 명령은 Kubernetes 클러스터에 응용 프로그램 코드 및 구성 파일을 업로드합니다. 그런 다음 Dockerfile을 실행하여 컨테이너 이미지를 만들고 이미지를 컨테이너 레지스트리에 푸시한 다음 마지막으로 Helm 차트를 실행하여 응용 프로그램을 시작합니다.

```console
draft up
```

출력:

```
Draft Up Started: 'open-jaguar'
open-jaguar: Building Docker Image: SUCCESS ⚓  (28.0342s)
open-jaguar: Pushing Docker Image: SUCCESS ⚓  (7.0647s)
open-jaguar: Releasing Application: SUCCESS ⚓  (4.5056s)
open-jaguar: Build ID: 01BW3VVNZYQ5NQ8V1QSDGNVD0S
```

## <a name="test-the-application"></a>응용 프로그램 테스트

응용 프로그램을 테스트하려면 `draft connect` 명령을 사용합니다. 이 명령은 안전한 로컬 연결을 허용하는 Kubernetes Pod로 연결을 위임합니다. 완료되면 제공된 URL에서 응용 프로그램에 액세스할 수 있습니다.

경우에 따라 컨테이너 이미지를 다운로드하고 응용 프로그램을 시작하는 데 몇 분이 걸릴 수 있습니다. 응용 프로그램에 액세스할 때 오류가 발생하면 연결을 다시 시도합니다.

```console
draft connect
```

출력:

```
Connecting to your app...SUCCESS...Connect to your app on localhost:46143
Starting log streaming...
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
== Spark has ignited ...
>> Listening on 0.0.0.0:4567
```

응용 프로그램 테스트가 끝나면 `Control+C`를 사용하여 프록시 연결을 중지합니다.

## <a name="expose-application"></a>응용 프로그램 공개

Kubernetes에서 응용 프로그램을 테스트할 때 인터넷에서 응용 프로그램을 사용할 수 있도록 하는 것이 좋습니다. 이 작업은 [수신 컨트롤러](https://kubernetes.io/docs/concepts/services-networking/ingress/) 또는 [LoadBalancer](https://kubernetes.io/docs/concepts/services-networking/service/#type-loadbalancer) 유형의 Kubernetes 서비스를 사용하여 수행할 수 있습니다. 이 문서는 Kubernetes 서비스를 사용하여 자세히 설명합니다.


먼저, `LoadBalancer` 유형의 서비스를 만들도록 지정하기 위해 Draft 팩을 업데이트해야 합니다. 이렇게 하려면 `values.yaml` 파일에서 서비스 유형을 업데이트합니다.

```console
vi chart/java/values.yaml
```

`service.type` 속성을 찾고 값을 `ClusterIP`에서 `LoadBalancer`로 업데이트합니다.

```yaml
replicaCount: 2
image:
  repository: openjdk
  tag: 8-jdk-alpine
  pullPolicy: IfNotPresent
service:
  name: java
  type: LoadBalancer
  externalPort: 80
  internalPort: 4567
resources:
  limits:
    cpu: 100m
    memory: 128Mi
  requests:
    cpu: 100m
    memory: 128Mi
  ```

`draft up`을 실행하여 응용 프로그램을 다시 실행합니다.

```console
draft up
```

서비스가 공인 IP 주소를 반환하는 데 몇 분이 걸릴 수 있습니다. 진행 상황을 모니터링하려면 조사식과 함께 `kubectl get service` 명령을 사용합니다.

```console
kubectl get service -w
```

처음에는 서비스에 대한 *EXTERNAL-IP*가 `pending`으로 표시됩니다.

```
deadly-squid-java   10.0.141.72   <pending>     80:32150/TCP   14m
```

EXTERNAL-IP 주소가 `pending`에서 `IP address`로 변경되면 `Control+C`를 사용하여 kubectl 조사식 프로세스를 중지합니다.

```
deadly-squid-java   10.0.141.72   52.175.224.118   80:32150/TCP   17m
```

응용 프로그램을 보려면 외부 IP 주소로 이동합니다.

```console
curl 52.175.224.118
```

출력:

```
Hello World, I'm Java
```

## <a name="iterate-on-the-application"></a>응용 프로그램 반복

이제 Draft가 구성되었고 응용 프로그램이 Kubernetes에서 실행 중이므로 코드 반복에 대해 설정되었습니다. 업데이트된 코드를 테스트할 때마다 `draft up` 명령을 실행하여 실행 중인 응용 프로그램을 업데이트합니다.

이 예제에서는 Java Hello World 응용 프로그램을 업데이트합니다.

```console
vi src/main/java/helloworld/Hello.java
```

Hello World 텍스트를 업데이트합니다.

```java
package helloworld;

import static spark.Spark.*;

public class Hello {
    public static void main(String[] args) {
        get("/", (req, res) -> "Hello World, I'm Java - Draft Rocks!");
    }
}
```

`draft up` 명령을 실행하여 응용 프로그램을 재배포합니다.

```console
draft up
```

출력

```
Draft Up Started: 'deadly-squid'
deadly-squid: Building Docker Image: SUCCESS ⚓  (18.0813s)
deadly-squid: Pushing Docker Image: SUCCESS ⚓  (7.9394s)
deadly-squid: Releasing Application: SUCCESS ⚓  (6.5005s)
deadly-squid: Build ID: 01BWK8C8X922F5C0HCQ8FT12RR
```

마지막으로 응용 프로그램을 보고 업데이트를 확인합니다.

```console
curl 52.175.224.118
```

출력:

```
Hello World, I'm Java - Draft Rocks!
```

## <a name="next-steps"></a>다음 단계

Draft 사용에 대한 자세한 내용은 Github의 Draft 문서를 참조하세요.

> [!div class="nextstepaction"]
> [Draft 설명서](https://github.com/Azure/draft/tree/master/docs)