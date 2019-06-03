---
title: Azure Container Registry-질문과 대답
description: Azure Container Registry 서비스와 관련 된 자주 묻는 질문에 대 한 답변
services: container-registry
author: sajayantony
manager: jeconnoc
ms.service: container-instances
ms.topic: article
ms.date: 5/13/2019
ms.author: sajaya
ms.openlocfilehash: 1400c023e43179a9c8490334e262711486c75a2d
ms.sourcegitcommit: c05618a257787af6f9a2751c549c9a3634832c90
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/30/2019
ms.locfileid: "66417935"
---
# <a name="frequently-asked-questions-about-azure-container-registry"></a>Azure Container Registry에 대 한 질문과 대답

이 문서는 질문과 대답 및 Azure Container Registry에 대 한 알려진된 문제를 해결합니다.

## <a name="resource-management"></a>리소스 관리

- [Resource Manager 템플릿을 사용 하 여 Azure container registry를 만들 수 있나요?](#can-i-create-an-azure-container-registry-using-a-resource-manager-template)
- [보안 취약성 검사가 ACR의 이미지는 무엇입니까?](#is-there-security-vulnerability-scanning-for-images-in-acr)
- [Azure Container Registry를 사용 하 여 Kubernetes를 어떻게 구성 하나요?](#how-do-i-configure-kubernetes-with-azure-container-registry)
- [컨테이너 레지스트리에 대 한 관리자 자격 증명 가져오기](#how-do-i-get-admin-credentials-for-a-container-registry)
- [Resource Manager 템플릿에서 관리자 자격 증명 가져오기](#how-do-i-get-admin-credentials-in-a-resource-manager-template)
- [Azure CLI 또는 Azure PowerShell을 사용 하 여 복제를 삭제 하지만 사용할 수 없음 상태로 복제 삭제 실패](#delete-of-replication-fails-with-forbidden-status-although-the-replication-gets-deleted-using-the-azure-cli-or-azure-powershell)

### <a name="can-i-create-an-azure-container-registry-using-a-resource-manager-template"></a>Resource Manager 템플릿을 사용 하 여 Azure Container Registry를 만들 수 있나요?

예. 다음과 같습니다 [템플릿으로](https://github.com/Azure/azure-cli/blob/master/src/command_modules/azure-cli-acr/azure/cli/command_modules/acr/template.json) 레지스트리를 만드는 데 사용할 수 있는 합니다.

### <a name="is-there-security-vulnerability-scanning-for-images-in-acr"></a>보안 취약성 검사가 ACR의 이미지는 무엇입니까?

예. 설명서를 참조 하세요 [Twistlock](https://www.twistlock.com/2016/11/07/twistlock-supports-azure-container-registry/) 하 고 [바다색](http://blog.aquasec.com/image-vulnerability-scanning-in-azure-container-registry)합니다.

### <a name="how-do-i-configure-kubernetes-with-azure-container-registry"></a>Azure Container Registry를 사용 하 여 Kubernetes를 어떻게 구성 하나요?

에 대 한 설명서를 참조 하세요 [Kubernetes](http://kubernetes.io/docs/user-guide/images/#using-azure-container-registry-acr) 단계와 [Azure Kubernetes Service](container-registry-auth-aks.md)합니다.

### <a name="how-do-i-get-admin-credentials-for-a-container-registry"></a>컨테이너 레지스트리에 대 한 관리자 자격 증명 가져오기

> [!IMPORTANT]
> 관리 사용자 계정이 주로 테스트 목적을 위해 레지스트리를 액세스 하는 단일 사용자를 위해 설계 되었습니다. 관리자 계정 자격 증명을 여러 사용자와 공유하지 않는 것이 좋습니다. 헤드리스 시나리오의 경우 사용자 및 서비스 주체는 개별 ID를 사용하는 것이 좋습니다. 참조 [인증 개요](container-registry-authentication.md)합니다.

관리자 자격 증명을 가져오기 전에 레지스트리의 관리 사용자를 사용할 수 있는지를 확인 합니다.

Azure CLI를 사용 하 여 자격 증명을 가져오려면:

```azurecli
az acr credential show -n myRegistry
```

Azure Powershell을 사용 합니다.

```powershell
Invoke-AzureRmResourceAction -Action listCredentials -ResourceType Microsoft.ContainerRegistry/registries -ResourceGroupName myResourceGroup -ResourceName myRegistry
```

### <a name="how-do-i-get-admin-credentials-in-a-resource-manager-template"></a>Resource Manager 템플릿에서 관리자 자격 증명 가져오기

> [!IMPORTANT]
> 관리 사용자 계정이 주로 테스트 목적을 위해 레지스트리를 액세스 하는 단일 사용자를 위해 설계 되었습니다. 관리자 계정 자격 증명을 여러 사용자와 공유하지 않는 것이 좋습니다. 헤드리스 시나리오의 경우 사용자 및 서비스 주체는 개별 ID를 사용하는 것이 좋습니다. 참조 [인증 개요](container-registry-authentication.md)합니다.

관리자 자격 증명을 가져오기 전에 레지스트리의 관리 사용자를 사용할 수 있는지를 확인 합니다.

첫 번째 암호를 가져오려면:

```json
{
    "password": "[listCredentials(resourceId('Microsoft.ContainerRegistry/registries', 'myRegistry'), '2017-10-01').passwords[0].value]"
}
```

두 번째 암호를 가져오려면:

```json
{
    "password": "[listCredentials(resourceId('Microsoft.ContainerRegistry/registries', 'myRegistry'), '2017-10-01').passwords[1].value]"
}
```

### <a name="delete-of-replication-fails-with-forbidden-status-although-the-replication-gets-deleted-using-the-azure-cli-or-azure-powershell"></a>Azure CLI 또는 Azure PowerShell을 사용 하 여 복제를 삭제 하지만 사용할 수 없음 상태로 복제 삭제 실패

사용자 레지스트리에 대 한 권한이 있지만 구독에서 판독기 수준 권한이 없는 경우 오류가 표시 됩니다. 이 문제를 해결 하려면 구독에 대 한 읽기 권한자 권한이 사용자에 게 할당 합니다.


```azurecli  
az role assignment create --role "Reader" --assignee user@contoso.com --scope /subscriptions/<subscription_id> 
```

## <a name="registry-operations"></a>레지스트리 작업

- [Docker 레지스트리 HTTP API V2를 액세스 하려면 어떻게 해야 합니까?](#how-do-i-access-docker-registry-http-api-v2)
- [리포지토리에서 태그에서 참조 되지 않는 모든 매니페스트를 삭제 하려면 어떻게 해야 하나요?](#how-do-i-delete-all-manifests-that-are-not-referenced-by-any-tag-in-a-repository)
- [왜 레지스트리 할당량 사용 현황 보고서는 줄어들지 않습니다 이미지를 삭제 한 후?](#why-does-the-registry-quota-usage-not-reduce-after-deleting-images)
- [저장소 할당량 변경 유효성을 검사 하는 방법](#how-do-i-validate-storage-quota-changes)
- [어떻게 인증을 수행 내 레지스트리를 사용 하 여 컨테이너에서 CLI를 실행 하는 경우?](#how-do-i-authenticate-with-my-registry-when-running-the-cli-in-a-container)
- [Azure Container Registry는 TLS v1.2만 구성 및 TLS v1.2를 사용 하도록 설정 하는 방법을 제공 하나요?](#does-azure-container-registry-offer-tls-v12-only-configuration-and-how-to-enable-tls-v12)
- [Azure Container Registry 콘텐츠 신뢰를 지원 하나요?](#does-azure-container-registry-support-content-trust)
- [레지스트리 리소스를 관리할 수 있는 권한이 없는 끌어오기 또는 밀어넣기 이미지에 대 한 액세스를 부여 수행 하는 방법](#how-do-i-grant-access-to-pull-or-push-images-without-permission-to-manage-the-registry-resource)
- [레지스트리에 대 한 자동 이미지 격리 사용 하도록 설정 하는 방법](#how-do-i-enable-automatic-image-quarantine-for-a-registry)

### <a name="how-do-i-access-docker-registry-http-api-v2"></a>Docker 레지스트리 HTTP API V2를 액세스 하려면 어떻게 해야 합니까?

ACR는 Docker 레지스트리 HTTP API V2를 지원합니다. Api에 액세스할 수 있습니다 `https://<your registry login server>/v2/`합니다. 예제: `https://mycontainerregistry.azurecr.io/v2/`

### <a name="how-do-i-delete-all-manifests-that-are-not-referenced-by-any-tag-in-a-repository"></a>리포지토리에서 태그에서 참조 되지 않는 모든 매니페스트를 삭제 하려면 어떻게 해야 하나요?

Bash의 경우:

```bash
az acr repository show-manifests -n myRegistry --repository myRepository --query "[?tags[0]==null].digest" -o tsv  | xargs -I% az acr repository delete -n myRegistry -t myRepository@%
```

Powershell:

```powershell
az acr repository show-manifests -n myRegistry --repository myRepository --query "[?tags[0]==null].digest" -o tsv | %{ az acr repository delete -n myRegistry -t myRepository@$_ }
```

참고: 추가할 수 있습니다 `-y` delete 명령에 확인을 건너뜁니다.

자세한 내용은 [Azure Container Registry에 컨테이너 이미지 삭제](container-registry-delete.md)합니다.

### <a name="why-does-the-registry-quota-usage-not-reduce-after-deleting-images"></a>왜 레지스트리 할당량 사용 현황 보고서는 줄어들지 않습니다 이미지를 삭제 한 후?

기본 계층 다른 컨테이너 이미지에서 참조 되는 경우이 상황이 발생할 수 있습니다. 참조가 없는 이미지를 삭제 하면 몇 분 안에 레지스트리 사용량을 업데이트 합니다.

### <a name="how-do-i-validate-storage-quota-changes"></a>저장소 할당량 변경 유효성을 검사 하는 방법

다음 docker 파일을 사용 하 여 1GB 레이어를 사용 하 여 이미지를 만듭니다. 이렇게 하면 이미지에 레지스트리의 이미지별 공유 되지 않는 계층입니다.

```dockerfile
FROM alpine
RUN dd if=/dev/urandom of=1GB.bin  bs=32M  count=32
RUN ls -lh 1GB.bin
```

빌드하고 docker CLI를 사용 하 여 레지스트리에 이미지를 푸시하십시오.

```bash
docker build -t myregistry.azurecr.io/1gb:latest .
docker push myregistry.azurecr.io/1gb:latest
```

Azure 포털의 저장소 사용량이 증가 걸리거나 CLI를 사용 하 여 쿼리할 수 있어야 합니다.

```bash
az acr show-usage -n myregistry
```

Azure CLI 또는 포털을 사용 하 여 이미지를 삭제 하 고 잠시 후에 업데이트 된 사용량을 확인 합니다.

```bash
az acr repository delete -n myregistry --image 1gb
```

### <a name="how-do-i-authenticate-with-my-registry-when-running-the-cli-in-a-container"></a>어떻게 인증을 수행 내 레지스트리를 사용 하 여 컨테이너에서 CLI를 실행 하는 경우?

Docker 소켓을 탑재 하 여 Azure CLI 컨테이너를 실행 해야 합니다.

```bash
docker run -it -v /var/run/docker.sock:/var/run/docker.sock azuresdk/azure-cli-python:dev
```

컨테이너에 설치 `docker`:

```bash
apk --update add docker
```

레지스트리를 통해 인증할 수 있습니다.

```azurecli
az acr login -n MyRegistry
```

### <a name="does-azure-container-registry-offer-tls-v12-only-configuration-and-how-to-enable-tls-v12"></a>Azure Container Registry는 TLS v1.2만 구성 및 TLS v1.2를 사용 하도록 설정 하는 방법을 제공 하나요?

예. 모든 최근 docker 클라이언트를 사용 하 여 TLS를 사용 하도록 설정 (18.03.0 버전 이상). 

### <a name="does-azure-container-registry-support-content-trust"></a>Azure Container Registry 콘텐츠 신뢰를 지원 하나요?

예를 사용할 수 있습니다 신뢰할 수 있는 이미지, Azure Container Registry에서 이후 합니다 [Docker 공증](https://docs.docker.com/notary/getting_started/) 통합 되었으며 및 사용할 수 있습니다. 자세한 내용은 참조 하세요 [Azure Container Registry의 콘텐츠 신뢰](container-registry-content-trust.md)합니다.


####  <a name="where-is-the-file-for-the-thumbprint-located"></a>여기서는 파일에 있는 지문?

아래 `~/.docker/trust/tuf/myregistry.azurecr.io/myrepository/metadata`:

* 에 저장 됩니다 (위임 역할)를 제외한 모든 역할의 인증서 및 공개 키를 `root.json`입니다.
* 공개 키 및 위임 역할의 인증서는 부모 역할의 JSON 파일에 저장 됩니다 (예를 들어 `targets.json` 에 대 한는 `targets/releases` 역할).

Docker 및 공증 클라이언트가 수행한 전체 TUF 검토 한 후 해당 공개 키 및 인증서를 확인 하는 것이 좋습니다.

### <a name="how-do-i-grant-access-to-pull-or-push-images-without-permission-to-manage-the-registry-resource"></a>레지스트리 리소스를 관리할 수 있는 권한이 없는 끌어오기 또는 밀어넣기 이미지에 대 한 액세스를 부여 수행 하는 방법

ACR 지원 [사용자 지정 역할](container-registry-roles.md) 다양 한 수준의 권한 제공 합니다. 특히 `AcrPull` 고 `AcrPush` 역할에 사용자가 Azure에서 레지스트리 리소스를 관리할 수 있는 권한이 없는 이미지를 끌어오기 및/또는 푸시 하도록 허용 합니다.

* Azure Portal: 레지스트리에 액세스 제어 (IAM)-> 추가-> (선택 `AcrPull` 또는 `AcrPush` 역할에 대 한).
* Azure CLI: 다음 명령을 실행 하 여 레지스트리 리소스 ID를 찾습니다.

  ```azurecli
  az acr show -n myRegistry
  ```
  
  할당할 수 있습니다 합니다 `AcrPull` 나 `AcrPush` 사용자 역할 (다음 예제에서는 `AcrPull`):

  ```azurecli
    az role assignment create --scope resource_id --role AcrPull --assignee user@example.com
    ```

  또는 해당 응용 프로그램 ID로 식별 되는 서비스 주체에 역할을 할당 합니다.

  ```
  az role assignment create --scope resource_id --role AcrPull --assignee 00000000-0000-0000-0000-000000000000
  ```

책임자는 인증 하 고 이미지를 레지스트리에 액세스 수 있습니다.

* 레지스트리로 인증 합니다.
    
  ```azurecli
  az acr login -n myRegistry 
  ```

* 목록 리포지토리에:

  ```azurecli
  az acr repository list -n myRegistry
  ```

 이미지를 끌어옵니다.
    
  ```azurecli
  docker pull myregistry.azurecr.io/hello-world
  ```

만 사용 하 여 합니다 `AcrPull` 또는 `AcrPush` 역할 책임자 Azure에서 레지스트리 리소스를 관리할 수 있는 권한이 없습니다. 예를 들어 `az acr list` 또는 `az acr show -n myRegistry` 레지스트리에 표시 되지 않습니다.

### <a name="how-do-i-enable-automatic-image-quarantine-for-a-registry"></a>레지스트리에 대 한 자동 이미지 격리 사용 하도록 설정 하는 방법

이미지 격리는 현재 ACR의 미리 보기 기능입니다. 보안 검사에 성공 했는지는 이미지만 일반 사용자에 게 표시 되도록 레지스트리 격리 모드를 사용할 수 있습니다. 자세한 내용은 참조는 [ACR GitHub 리포지토리](https://github.com/Azure/acr/tree/master/docs/preview/quarantine)합니다.

## <a name="diagnostics"></a>진단

- [오류와 함께 docker 풀 실패: net/http: (Client.Timeout 헤더를 기다리는 동안 초과) 연결을 기다리는 동안 요청이 취소](#docker-pull-fails-with-error-nethttp-request-canceled-while-waiting-for-connection-clienttimeout-exceeded-while-awaiting-headers)
- [docker 밀어넣기 성공 하지만 오류와 함께 docker 풀 실패: 무단: 인증 필요](#docker-push-succeeds-but-docker-pull-fails-with-error-unauthorized-authentication-required)
- [Docker 디먼의 디버그 로그를 가져오고 사용 하도록 설정](#enable-and-get-the-debug-logs-of-the-docker-daemon) 
- [새 사용자 권한 아닐 효과적인 업데이트 한 후 즉시](#new-user-permissions-may-not-be-effective-immediately-after-updating)
- [인증 정보 직접 REST API 호출에 올바른 형식으로 제공 되지 않습니다.](#authentication-information-is-not-given-in-the-correct-format-on-direct-rest-api-calls)
- [왜 Azure 포털을 나열 하지 않습니다 모든 리포지토리 또는 태그?](#why-does-the-azure-portal-not-list-all-my-repositories-or-tags)
- [Windows에서 http 추적을 어떻게 수집 하나요?](#how-do-i-collect-http-traces-on-windows)

### <a name="docker-pull-fails-with-error-nethttp-request-canceled-while-waiting-for-connection-clienttimeout-exceeded-while-awaiting-headers"></a>오류와 함께 docker 풀 실패: net/http: (Client.Timeout 헤더를 기다리는 동안 초과) 연결을 기다리는 동안 요청이 취소

 - 이 오류는 일시적인 문제 이면 다시 시도 성공 합니다.
 - 경우 `docker pull` docker 데몬과 문제가 있을 수 있습니다 지속적으로 실패 합니다. 문제는 일반적으로 docker 디먼을 다시 시작 하 여 완화할 수 있습니다. 
 - Docker 디먼을 다시 시작 후이 문제를 확인 합니다. 계속 하면이 문제는 컴퓨터와 일부 네트워크 연결 문제를 수 있습니다. 일반 네트워크 컴퓨터에서 정상 상태 인지를 확인 하려면 명령을 같은 사용해 `ping www.bing.com`합니다.
 - 모든 docker 클라이언트 작업에 다시 시도 메커니즘을 항상 있어야 합니다.

### <a name="docker-push-succeeds-but-docker-pull-fails-with-error-unauthorized-authentication-required"></a>docker 밀어넣기 성공 하지만 오류와 함께 docker 풀 실패: 무단: 인증 필요

Docker 데몬, Red Hat 버전을 사용 하 여이 오류가 발생할 수 있는 `--signature-verification` 기본적으로 사용 됩니다. 다음 명령을 실행 하 여 Red Hat Enterprise Linux (RHEL) 또는 Fedora docker 디먼 옵션을 확인할 수 있습니다.

```bash
grep OPTIONS /etc/sysconfig/docker
```

예를 들어, Fedora 28 Server 다음 docker 디먼 옵션이 있습니다.

```
OPTIONS='--selinux-enabled --log-driver=journald --live-restore'
```

사용 하 여 `--signature-verification=false` 누락 된 경우 `docker pull` 유사한 오류로 인해 실패 합니다.

```bash
Trying to pull repository myregistry.azurecr.io/myimage ...
unauthorized: authentication required
```

오류를 해결하려면:
1. 추가 옵션 `--signature-verification=false` docker 디먼 구성 파일에 `/etc/sysconfig/docker`입니다. 예를 들면 다음과 같습니다.

  ```
  OPTIONS='--selinux-enabled --log-driver=journald --live-restore --signature-verification=false'
  ```
2. 다음 명령을 실행 하 여 docker 디먼 서비스를 다시 시작 합니다.

  ```bash
  sudo systemctl restart docker.service
  ```

세부 정보 `--signature-verification` 실행 하 여 찾을 수 있습니다 `man dockerd`합니다.

### <a name="enable-and-get-the-debug-logs-of-the-docker-daemon"></a>Docker 디먼의 디버그 로그를 가져오고 사용 하도록 설정  

시작 `dockerd` 사용 하 여는 `debug` 옵션입니다. 먼저 docker 디먼 구성 파일을 만듭니다 (`/etc/docker/daemon.json`) 존재 하지 않습니다을 추가 하는 경우는 `debug` 옵션:

```json
{   
    "debug": true   
}
```

그런 다음 디먼이 다시 시작 합니다. 예를 들어 Ubuntu 14.04의 경우:

```bash
sudo service docker restart
```

세부 정보에서 확인할 수 있습니다 합니다 [Docker 설명서](https://docs.docker.com/engine/admin/#enable-debugging)합니다. 

 * 시스템에 따라 서로 다른 위치에 로그를 생성할 수 있습니다. Ubuntu 14.04, 예를 들어 있기 `/var/log/upstart/docker.log`합니다.   
참조 [Docker 설명서](https://docs.docker.com/engine/admin/#read-the-logs) 세부 정보에 대 한 합니다.    

 * Docker에 대 한 Windows에 대 한 로그는 %LOCALAPPDATA%/docker/ 생성 됩니다. 그러나 모든 디버그 정보를 아직 없을 수도 있습니다.   

   전체 디먼 로그에 액세스 하려면 몇 가지 추가 단계가 필요할 수 있습니다.

    ```console
    docker run --privileged -it --rm -v /var/run/docker.sock:/var/run/docker.sock -v /usr/local/bin/docker:/usr/local/bin/docker alpine sh

    docker run --net=host --ipc=host --uts=host --pid=host -it --security-opt=seccomp=unconfined --privileged --rm -v /:/host alpine /bin/sh
    chroot /host
    ```
    실행 하는 VM의 모든 파일에 액세스할 수 있는 이제 `dockerd`합니다. 로그를 `/var/log/docker.log`입니다.

### <a name="new-user-permissions-may-not-be-effective-immediately-after-updating"></a>새 사용자 권한 아닐 효과적인 업데이트 한 후 즉시

새 권한을 부여할 때 서비스 주체를 변경 하려면 (새 역할) 권한이 수 하지 즉시 적용 됩니다. 두 가지 가능한 이유가 있습니다.

* Azure Active Directory 역할 할당 지연입니다. 일반적으로 빠르며 있지만 전파 지연이 발생 하는 분이 걸릴 수 있습니다.
* ACR 토큰 서버에 권한 지연입니다. 최대 10 분 정도 걸릴 수 있습니다. 문제를 완화 하려면 `docker logout` 인증 되 고 다시 동일한 사용자 1 분 후 및:

  ```bash
  docker logout myregistry.azurecr.io
  docker login myregistry.azurecr.io
  ```

현재 ACR 사용자가 홈 복제 삭제를 지원 하지 않습니다. 해결 방법은 홈 복제를 포함 하는 템플릿에서 만들지만 추가 하 여 해당 만들기를 건너뛸 `"condition": false` 아래와 같이:

```json
{
    "name": "[concat(parameters('acrName'), '/', parameters('location'))]",
    "condition": false,
    "type": "Microsoft.ContainerRegistry/registries/replications",
    "apiVersion": "2017-10-01",
    "location": "[parameters('location')]",
    "properties": {},
    "dependsOn": [
        "[concat('Microsoft.ContainerRegistry/registries/', parameters('acrName'))]"
     ]
},
```

### <a name="authentication-information-is-not-given-in-the-correct-format-on-direct-rest-api-calls"></a>인증 정보 직접 REST API 호출에 올바른 형식으로 제공 되지 않습니다.

발생할 수 있습니다는 `InvalidAuthenticationInfo` 오류를 사용 하 여 특히 합니다 `curl` 옵션을 사용 하 여 도구 `-L`, `--location` (리디렉션)을 합니다.
예를 들어, 사용 하 여 blob을 가져오는 `curl` 사용 하 여 `-L` 옵션 및 기본 인증:

```bash
curl -L -H "Authorization: basic $credential" https://$registry.azurecr.io/v2/$repository/blobs/$digest
```

다음과 같은 응답이 발생할 수 있습니다.

```xml
<?xml version="1.0" encoding="utf-8"?>
<Error><Code>InvalidAuthenticationInfo</Code><Message>Authentication information is not given in the correct format. Check the value of Authorization header.
RequestId:00000000-0000-0000-0000-000000000000
Time:2019-01-01T00:00:00.0000000Z</Message></Error>
```

근본 원인을 일부 `curl` 구현 원래 요청에서 헤더를 사용 하 여 리디렉션을 수행 합니다.

문제를 해결 하려면 리디렉션을 헤더 없이 수동으로 수행 해야 합니다. 응답 헤더를 사용 하 여 인쇄를 `-D -` 옵션을 `curl` 한 다음 추출:는 `Location` 헤더:

```bash
redirect_url=$(curl -s -D - -H "Authorization: basic $credential" https://$registry.azurecr.io/v2/$repository/blobs/$digest | grep "^Location: " | cut -d " " -f2 | tr -d '\r')
curl $redirect_url
```

### <a name="why-does-the-azure-portal-not-list-all-my-repositories-or-tags"></a>왜 Azure 포털을 나열 하지 않습니다 모든 리포지토리 또는 태그? 

Microsoft Edge 브라우저를 사용 하는 경우에 최대 100 리포지토리 또는 나열 된 태그를 볼 수 있습니다. 레지스트리의 리포지토리 또는 태그를 100 개 있으면 모두 나열 하려면 Firefox 또는 Chrome 브라우저를 사용 하는 것이 좋습니다.

### <a name="how-do-i-collect-http-traces-on-windows"></a>Windows에서 http 추적을 어떻게 수집 하나요?

#### <a name="prerequisites"></a>필수 조건

- Fiddler에서 암호 해독 https를 사용 합니다.  <https://docs.telerik.com/fiddler/Configure-Fiddler/Tasks/DecryptHTTPS>
- Docker ui 통해 프록시를 사용 하도록 Docker를 사용 하도록 설정 합니다. <https://docs.docker.com/docker-for-windows/#proxies>
- 완료 되 면 revert를 사용 해야 합니다.  Docker 사용이 작동 하지 않습니다 및 fiddler 실행 되지 않습니다.

#### <a name="windows-containers"></a>Windows 컨테이너

127.0.0.1: 8888 Docker 프록시 구성

#### <a name="linux-containers"></a>Linux 컨테이너

Docker의 ip를 vm 가상 스위치 찾기:

```powershell
(Get-NetIPAddress -InterfaceAlias "*Docker*" -AddressFamily IPv4).IPAddress
```

이전 명령에서 포트 8888 (예를 들어 10.0.75.1:8888)을 출력 하도록 Docker 프록시 구성

## <a name="tasks"></a>태스크

- [실행 취소를 일괄 처리 하는 있나요?](#how-do-i-batch-cancel-runs)
- [Az acr 빌드 명령에서.git 폴더 포함지 않습니다는 방법](#how-do-i-include-the-git-folder-in-az-acr-build-command)

### <a name="how-do-i-batch-cancel-runs"></a>실행 취소를 일괄 처리 하는 있나요?

다음 명령은 지정된 된 레지스트리에서 실행 중인 모든 작업을 취소합니다.

```azurecli
az acr task list-runs -r $myregistry --run-status Running --query '[].runId' -o tsv \
| xargs -I% az acr task cancel-run -r $myregistry --run-id %
```

### <a name="how-do-i-include-the-git-folder-in-az-acr-build-command"></a>Az acr 빌드 명령에서.git 폴더 포함지 않습니다는 방법

로컬 소스 폴더를 전달 하는 경우는 `az acr build` 명령인은 `.git` 폴더는 기본적으로 업로드 된 패키지에서 제외 됩니다. 만들 수는 `.dockerignore` 다음 설정 사용 하 여 파일입니다. 아래에 있는 모든 파일을 복원 하는 명령을 알려줍니다 `.git` 업로드 된 패키지의 합니다. 

```
!.git/**
```

이 설정에도 적용 됩니다는 `az acr run` 명령입니다.

## <a name="cicd-integration"></a>CI/CD 통합

- [CircleCI](https://github.com/Azure/acr/blob/master/docs/integration/CircleCI.md)
- [GitHub 작업](https://github.com/Azure/acr/blob/master/docs/integration/github-actions/github-actions.md)


## <a name="next-steps"></a>다음 단계

* [자세한](container-registry-intro.md) Azure Container Registry에 대 한 합니다.
