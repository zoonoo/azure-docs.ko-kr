---
title: 질문과 대답
description: Azure 컨테이너 레지스트리 서비스와 관련하여 자주 묻는 질문에 대한 답변
author: sajayantony
ms.topic: article
ms.date: 07/02/2019
ms.author: sajaya
ms.openlocfilehash: c0d51c9c31e4e6859eaedce371efeafaa5fd4f46
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78403218"
---
# <a name="frequently-asked-questions-about-azure-container-registry"></a>Azure 컨테이너 레지스트리에 대해 자주 묻는 질문

이 문서에서는 Azure 컨테이너 레지스트리에 대한 자주 묻는 질문과 알려진 문제를 다룹니다.

## <a name="resource-management"></a>리소스 관리

- [리소스 관리자 템플릿을 사용하여 Azure 컨테이너 레지스트리를 만들 수 있습니까?](#can-i-create-an-azure-container-registry-using-a-resource-manager-template)
- [ACR에서 이미지에 대한 보안 취약점 검색이 있습니까?](#is-there-security-vulnerability-scanning-for-images-in-acr)
- [Azure 컨테이너 레지스트리를 통해 Kubernetes를 구성하려면 어떻게 해야 합니까?](#how-do-i-configure-kubernetes-with-azure-container-registry)
- [컨테이너 레지스트리에 대한 관리자 자격 증명을 받으려면 어떻게 해야 합니까?](#how-do-i-get-admin-credentials-for-a-container-registry)
- [리소스 관리자 템플릿에서 관리자 자격 증명을 받으려면 어떻게 해야 합니까?](#how-do-i-get-admin-credentials-in-a-resource-manager-template)
- [Azure CLI 또는 Azure PowerShell을 사용하여 복제가 삭제되더라도 금지된 상태에서 복제 삭제가 실패합니다.](#delete-of-replication-fails-with-forbidden-status-although-the-replication-gets-deleted-using-the-azure-cli-or-azure-powershell)
- [방화벽 규칙이 성공적으로 업데이트되었지만 적용되지는 않습니다.](#firewall-rules-are-updated-successfully-but-they-do-not-take-effect)

### <a name="can-i-create-an-azure-container-registry-using-a-resource-manager-template"></a>리소스 관리자 템플릿을 사용하여 Azure 컨테이너 레지스트리를 만들 수 있습니까?

예. 다음은 [레지스트리를](https://github.com/Azure/azure-quickstart-templates/tree/master/101-container-registry) 만드는 데 사용할 수 있는 템플릿입니다.

### <a name="is-there-security-vulnerability-scanning-for-images-in-acr"></a>ACR에서 이미지에 대한 보안 취약점 검색이 있습니까?

예. Azure 보안 [센터,](https://docs.microsoft.com/azure/security-center/azure-container-registry-integration) [트위스트록](https://www.twistlock.com/2016/11/07/twistlock-supports-azure-container-registry/) 및 [아쿠아의](https://blog.aquasec.com/image-vulnerability-scanning-in-azure-container-registry)설명서를 참조하십시오.

### <a name="how-do-i-configure-kubernetes-with-azure-container-registry"></a>Azure 컨테이너 레지스트리를 통해 Kubernetes를 구성하려면 어떻게 해야 합니까?

[Kubernetes](https://kubernetes.io/docs/user-guide/images/#using-azure-container-registry-acr) 및 [Azure Kubernetes 서비스에](../aks/cluster-container-registry-integration.md)대한 단계에 대한 설명서를 참조하십시오.

### <a name="how-do-i-get-admin-credentials-for-a-container-registry"></a>컨테이너 레지스트리에 대한 관리자 자격 증명을 받으려면 어떻게 해야 합니까?

> [!IMPORTANT]
> 관리자 사용자 계정은 주로 테스트 목적으로 단일 사용자가 레지스트리에 액세스할 수 있도록 설계되었습니다. 관리자 계정 자격 증명을 여러 사용자와 공유하지 않는 것이 좋습니다. 헤드리스 시나리오의 경우 사용자 및 서비스 주체는 개별 ID를 사용하는 것이 좋습니다. [인증 개요를](container-registry-authentication.md)참조하십시오.

관리자 자격 증명을 받기 전에 레지스트리의 관리자 사용자가 활성화되어 있는지 확인합니다.

Azure CLI를 사용하여 자격 증명을 얻으려면 다음을 수행하십시오.

```azurecli
az acr credential show -n myRegistry
```

Azure 전원 셸 사용:

```powershell
Invoke-AzureRmResourceAction -Action listCredentials -ResourceType Microsoft.ContainerRegistry/registries -ResourceGroupName myResourceGroup -ResourceName myRegistry
```

### <a name="how-do-i-get-admin-credentials-in-a-resource-manager-template"></a>리소스 관리자 템플릿에서 관리자 자격 증명을 받으려면 어떻게 해야 합니까?

> [!IMPORTANT]
> 관리자 사용자 계정은 주로 테스트 목적으로 단일 사용자가 레지스트리에 액세스할 수 있도록 설계되었습니다. 관리자 계정 자격 증명을 여러 사용자와 공유하지 않는 것이 좋습니다. 헤드리스 시나리오의 경우 사용자 및 서비스 주체는 개별 ID를 사용하는 것이 좋습니다. [인증 개요를](container-registry-authentication.md)참조하십시오.

관리자 자격 증명을 받기 전에 레지스트리의 관리자 사용자가 활성화되어 있는지 확인합니다.

첫 번째 암호를 얻으려면 다음을 수행하십시오.

```json
{
    "password": "[listCredentials(resourceId('Microsoft.ContainerRegistry/registries', 'myRegistry'), '2017-10-01').passwords[0].value]"
}
```

두 번째 암호를 얻으려면 다음을 수행하십시오.

```json
{
    "password": "[listCredentials(resourceId('Microsoft.ContainerRegistry/registries', 'myRegistry'), '2017-10-01').passwords[1].value]"
}
```

### <a name="delete-of-replication-fails-with-forbidden-status-although-the-replication-gets-deleted-using-the-azure-cli-or-azure-powershell"></a>Azure CLI 또는 Azure PowerShell을 사용하여 복제가 삭제되더라도 금지된 상태에서 복제 삭제가 실패합니다.

이 오류는 사용자가 레지스트리에 대한 사용 권한이 있지만 구독에 대한 Reader 수준 권한이 없는 경우에 나타납니다. 이 문제를 해결하려면 사용자에게 구독에 대한 판독기 권한을 할당합니다.


```azurecli  
az role assignment create --role "Reader" --assignee user@contoso.com --scope /subscriptions/<subscription_id> 
```

### <a name="firewall-rules-are-updated-successfully-but-they-do-not-take-effect"></a>방화벽 규칙이 성공적으로 업데이트되었지만 적용되지는 않습니다.

방화벽 규칙 변경 내용을 전파하는 데 는 다소 시간이 걸립니다. 방화벽 설정을 변경한 후 몇 분 동안 기다렸다가 이 변경 사항을 확인하십시오.


## <a name="registry-operations"></a>레지스트리 작업

- [Docker 레지스트리 HTTP API V2에 액세스하려면 어떻게 해야 합니까?](#how-do-i-access-docker-registry-http-api-v2)
- [리포지토리의 태그에서 참조하지 않는 모든 매니페스트를 삭제하려면 어떻게 해야 합니까?](#how-do-i-delete-all-manifests-that-are-not-referenced-by-any-tag-in-a-repository)
- [이미지를 삭제한 후 레지스트리 할당량 사용량이 줄어들지 않는 이유는 무엇입니까?](#why-does-the-registry-quota-usage-not-reduce-after-deleting-images)
- [저장소 할당량 변경의 유효성을 검사하려면 어떻게 해야 합니까?](#how-do-i-validate-storage-quota-changes)
- [컨테이너에서 CLI를 실행할 때 레지스트리를 통해 인증하려면 어떻게 해야 합니까?](#how-do-i-authenticate-with-my-registry-when-running-the-cli-in-a-container)
- [TLS 1.2를 활성화 하는 방법?](#how-to-enable-tls-12)
- [Azure 컨테이너 레지스트리가 콘텐츠 트러스트를 지원합니까?](#does-azure-container-registry-support-content-trust)
- [레지스트리 리소스를 관리할 수 있는 권한 없이 이미지를 가져오거나 푸시할 수 있는 액세스 권한을 부여하려면 어떻게 해야 합니까?](#how-do-i-grant-access-to-pull-or-push-images-without-permission-to-manage-the-registry-resource)
- [레지스트리에 대한 자동 이미지 격리를 사용하려면 어떻게 해야 합니까?](#how-do-i-enable-automatic-image-quarantine-for-a-registry)

### <a name="how-do-i-access-docker-registry-http-api-v2"></a>Docker 레지스트리 HTTP API V2에 액세스하려면 어떻게 해야 합니까?

ACR은 도커 레지스트리 HTTP API V2를 지원합니다. API는 에서 `https://<your registry login server>/v2/`액세스할 수 있습니다. 예: `https://mycontainerregistry.azurecr.io/v2/`

### <a name="how-do-i-delete-all-manifests-that-are-not-referenced-by-any-tag-in-a-repository"></a>리포지토리의 태그에서 참조하지 않는 모든 매니페스트를 삭제하려면 어떻게 해야 합니까?

당신이 강타에있는 경우 :

```azurecli
az acr repository show-manifests -n myRegistry --repository myRepository --query "[?tags[0]==null].digest" -o tsv  | xargs -I% az acr repository delete -n myRegistry -t myRepository@%
```

파워쉘의 경우:

```azurecli
az acr repository show-manifests -n myRegistry --repository myRepository --query "[?tags[0]==null].digest" -o tsv | %{ az acr repository delete -n myRegistry -t myRepository@$_ }
```

참고: 삭제 `-y` 명령에 추가하여 확인을 건너뛸 수 있습니다.

자세한 내용은 [Azure 컨테이너 레지스트리의 컨테이너 이미지 삭제를](container-registry-delete.md)참조하십시오.

### <a name="why-does-the-registry-quota-usage-not-reduce-after-deleting-images"></a>이미지를 삭제한 후 레지스트리 할당량 사용량이 줄어들지 않는 이유는 무엇입니까?

이 상황은 기본 레이어가 여전히 다른 컨테이너 이미지에서 참조되는 경우에 발생할 수 있습니다. 참조가 없는 이미지를 삭제하면 레지스트리 사용량이 몇 분 안에 업데이트됩니다.

### <a name="how-do-i-validate-storage-quota-changes"></a>저장소 할당량 변경의 유효성을 검사하려면 어떻게 해야 합니까?

다음 도커 파일을 사용하여 1GB 레이어로 이미지를 만듭니다. 이렇게 하면 이미지에 레지스트리의 다른 이미지에서 공유되지 않는 레이어가 있습니다.

```dockerfile
FROM alpine
RUN dd if=/dev/urandom of=1GB.bin  bs=32M  count=32
RUN ls -lh 1GB.bin
```

docker CLI를 사용하여 이미지를 빌드하고 레지스트리로 푸시합니다.

```bash
docker build -t myregistry.azurecr.io/1gb:latest .
docker push myregistry.azurecr.io/1gb:latest
```

Azure 포털에서 저장소 사용량이 증가했거나 CLI를 사용하여 사용량을 쿼리할 수 있습니다.

```azurecli
az acr show-usage -n myregistry
```

Azure CLI 또는 포털을 사용하여 이미지를 삭제하고 몇 분 안에 업데이트된 사용량을 확인합니다.

```azurecli
az acr repository delete -n myregistry --image 1gb
```

### <a name="how-do-i-authenticate-with-my-registry-when-running-the-cli-in-a-container"></a>컨테이너에서 CLI를 실행할 때 레지스트리를 통해 인증하려면 어떻게 해야 합니까?

Docker 소켓을 장착하여 Azure CLI 컨테이너를 실행해야 합니다.

```bash
docker run -it -v /var/run/docker.sock:/var/run/docker.sock azuresdk/azure-cli-python:dev
```

컨테이너에 설치 `docker`:

```bash
apk --update add docker
```

그런 다음 레지스트리를 통해 인증합니다.

```azurecli
az acr login -n MyRegistry
```

### <a name="how-to-enable-tls-12"></a>TLS 1.2를 활성화 하는 방법?

최신 도커 클라이언트(버전 18.03.0 이상)를 사용하여 TLS 1.2를 활성화합니다. 

> [!IMPORTANT]
> 2020년 1월 13일부터 Azure Container Registry는 TLS 1.2를 사용하기 위해 서버 및 애플리케이션의 모든 보안 연결이 필요합니다. TLS 1.0 및 1.1에 대한 지원이 중단됩니다.

### <a name="does-azure-container-registry-support-content-trust"></a>Azure 컨테이너 레지스트리가 콘텐츠 트러스트를 지원합니까?

예. [Docker 공증인이](https://docs.docker.com/notary/getting_started/) 통합되어 있고 활성화할 수 있으므로 Azure 컨테이너 레지스트리에서 신뢰할 수 있는 이미지를 사용할 수 있습니다. 자세한 내용은 [Azure 컨테이너 레지스트리의 콘텐츠 트러스트를](container-registry-content-trust.md)참조하십시오.


####  <a name="where-is-the-file-for-the-thumbprint-located"></a>지문에 대한 파일은 어디에 있습니까?

아래 `~/.docker/trust/tuf/myregistry.azurecr.io/myrepository/metadata`:

* 위임 역할을 제외한 모든 역할의 공개 키 및 `root.json`인증서가 에 저장됩니다.
* 위임 역할의 공개 키와 인증서는 상위 역할의 JSON 파일에 `targets.json` 저장됩니다(예: `targets/releases` 역할).

Docker 및 공증인 클라이언트에서 수행한 전체 TUF 확인 후 이러한 공개 키 및 인증서를 확인하는 것이 좋습니다.

### <a name="how-do-i-grant-access-to-pull-or-push-images-without-permission-to-manage-the-registry-resource"></a>레지스트리 리소스를 관리할 수 있는 권한 없이 이미지를 가져오거나 푸시할 수 있는 액세스 권한을 부여하려면 어떻게 해야 합니까?

ACR은 서로 다른 수준의 권한을 제공하는 [사용자 지정 역할을](container-registry-roles.md) 지원합니다. 특히 `AcrPull` 역할을 `AcrPush` 통해 사용자는 Azure에서 레지스트리 리소스를 관리할 수 있는 권한 없이 이미지를 가져오거나 푸시할 수 있습니다.

* Azure 포털: 레지스트리-> 액세스 제어(IAM) -> `AcrPull` `AcrPush` 추가(역할 선택 또는 역할)입니다.
* Azure CLI: 다음 명령을 실행 하여 레지스트리의 리소스 ID를 찾습니다.

  ```azurecli
  az acr show -n myRegistry
  ```
  
  그런 다음 사용자에게 `AcrPull` 또는 `AcrPush` 역할을 할당할 수 있습니다(다음 예제에서는 사용). `AcrPull`

  ```azurecli
  az role assignment create --scope resource_id --role AcrPull --assignee user@example.com
  ```

  또는 응용 프로그램 ID로 식별된 서비스 원칙에 역할을 할당합니다.

  ```azurecli
  az role assignment create --scope resource_id --role AcrPull --assignee 00000000-0000-0000-0000-000000000000
  ```

그런 다음 수인자는 레지스트리에서 이미지를 인증하고 액세스할 수 있습니다.

* 레지스트리를 인증하려면 다음을 수행하십시오.
    
  ```azurecli
  az acr login -n myRegistry 
  ```

* 리포지토리를 나열하려면 다음을 수행합니다.

  ```azurecli
  az acr repository list -n myRegistry
  ```

* 이미지를 가져오려면 다음을 수행합니다.

  ```bash
  docker pull myregistry.azurecr.io/hello-world
  ```

`AcrPull` 또는 `AcrPush` 역할만 사용하면 지정인은 Azure에서 레지스트리 리소스를 관리할 수 있는 권한이 없습니다. 예를 들어 `az acr list` `az acr show -n myRegistry` 레지스트리가 표시되지 않습니다.

### <a name="how-do-i-enable-automatic-image-quarantine-for-a-registry"></a>레지스트리에 대한 자동 이미지 격리를 사용하도록 설정하려면 어떻게 해야 합니까?

이미지 격리는 현재 ACR의 미리 보기 기능입니다. 레지스트리의 격리 모드를 활성화하여 보안 검사를 성공적으로 통과한 이미지만 일반 사용자에게 표시되도록 할 수 있습니다. 자세한 내용은 [ACR GitHub 리포지토리를](https://github.com/Azure/acr/tree/master/docs/preview/quarantine)참조하십시오.

## <a name="diagnostics-and-health-checks"></a>진단 및 상태 확인

- [상태 확인`az acr check-health`](#check-health-with-az-acr-check-health)
- [도커 풀 오류: net/http: 연결을 기다리는 동안 취소된 요청(헤더를 기다리는 동안 Client.Timeout초과)](#docker-pull-fails-with-error-nethttp-request-canceled-while-waiting-for-connection-clienttimeout-exceeded-while-awaiting-headers)
- [docker 푸시가 성공하지만 도커 풀에 오류가 발생하지 않음: 무단: 인증 필요](#docker-push-succeeds-but-docker-pull-fails-with-error-unauthorized-authentication-required)
- [`az acr login`성공하지만, 도커 명령오류가 발생하면 무단: 인증이 필요합니다.](#az-acr-login-succeeds-but-docker-fails-with-error-unauthorized-authentication-required)
- [도커 데몬의 디버그 로그 사용 및 받기](#enable-and-get-the-debug-logs-of-the-docker-daemon) 
- [새 사용자 권한은 업데이트 후 즉시 적용되지 않을 수 있습니다.](#new-user-permissions-may-not-be-effective-immediately-after-updating)
- [직접 REST API 호출에서 인증 정보가 올바른 형식으로 제공되지 않습니다.](#authentication-information-is-not-given-in-the-correct-format-on-direct-rest-api-calls)
- [Azure 포털에 내 모든 리포지토리 또는 태그가 나열되지 않는 이유는 무엇입니까?](#why-does-the-azure-portal-not-list-all-my-repositories-or-tags)
- [Azure 포털이 리포지토리 또는 태그를 가져오지 못하는 이유는 무엇입니까?](#why-does-the-azure-portal-fail-to-fetch-repositories-or-tags)
- [끌어오기 또는 푸시 요청이 허용되지 않는 작업으로 실패하는 이유는 무엇입니까?](#why-does-my-pull-or-push-request-fail-with-disallowed-operation)
- [Windows에서 http 추적을 수집하려면 어떻게 해야 합니까?](#how-do-i-collect-http-traces-on-windows)

### <a name="check-health-with-az-acr-check-health"></a>상태 확인`az acr check-health`

일반적인 환경 및 레지스트리 문제를 해결하려면 [Azure 컨테이너 레지스트리의 상태 확인을](container-registry-check-health.md)참조하십시오.

### <a name="docker-pull-fails-with-error-nethttp-request-canceled-while-waiting-for-connection-clienttimeout-exceeded-while-awaiting-headers"></a>도커 풀 오류: net/http: 연결을 기다리는 동안 취소된 요청(헤더를 기다리는 동안 Client.Timeout초과)

 - 이 오류가 일시적인 문제인 경우 다시 시도가 성공합니다.
 - 계속 `docker pull` 실패하면 Docker 데몬에 문제가 있을 수 있습니다. Docker 데몬을 다시 시작하여 일반적으로 문제를 완화할 수 있습니다. 
 - Docker 데몬을 다시 시작한 후에도 이 문제가 계속 표시되면 컴퓨터의 일부 네트워크 연결 문제일 수 있습니다. 컴퓨터의 일반 네트워크가 정상인지 확인하려면 다음 명령을 실행하여 끝점 연결을 테스트합니다. 이 `az acr` 연결 검사 명령을 포함하는 최소 버전은 2.2.9입니다. 이전 버전을 사용하는 경우 Azure CLI를 업그레이드합니다.
 
  ```azurecli
  az acr check-health -n myRegistry
  ```

 - 모든 Docker 클라이언트 작업에 항상 다시 시도 메커니즘이 있어야 합니다.

### <a name="docker-pull-is-slow"></a>도커 당기기가 느립니다.
[이](http://www.azurespeed.com/Azure/Download) 도구를 사용하여 컴퓨터 네트워크 다운로드 속도를 테스트합니다. 컴퓨터 네트워크가 느린 경우 레지스트리와 동일한 지역에서 Azure VM을 사용하는 것이 좋습니다. 이렇게 하면 일반적으로 네트워크 속도가 빨라집니다.

### <a name="docker-push-is-slow"></a>도커 푸시가 느립니다.
[이](http://www.azurespeed.com/Azure/Upload) 도구를 사용하여 컴퓨터 네트워크 업로드 속도를 테스트합니다. 컴퓨터 네트워크가 느린 경우 레지스트리와 동일한 지역에서 Azure VM을 사용하는 것이 좋습니다. 이렇게 하면 일반적으로 네트워크 속도가 빨라집니다.

### <a name="docker-push-succeeds-but-docker-pull-fails-with-error-unauthorized-authentication-required"></a>Docker 푸시가 성공했지만 도커 풀에 오류가 발생하지 않음: 무단: 인증필요

이 오류는 Docker 데몬의 Red Hat 버전에서 발생할 수 있으며 `--signature-verification` 기본적으로 활성화되어 있습니다. 다음 명령을 실행 하 여 레드 햇 엔터프라이즈 리눅스 (RHEL) 또는 페도라에 대 한 Docker 데몬 옵션을 확인할 수 있습니다.

```bash
grep OPTIONS /etc/sysconfig/docker
```

예를 들어, Fedora 28 Server에는 다음과 같은 도커 데몬 옵션이 있습니다.

`OPTIONS='--selinux-enabled --log-driver=journald --live-restore'`

누락된 `--signature-verification=false` `docker pull` 경우 다음과 유사한 오류로 실패합니다.

```output
Trying to pull repository myregistry.azurecr.io/myimage ...
unauthorized: authentication required
```

오류를 해결하려면:
1. Docker 데몬 구성 파일에 `--signature-verification=false` 옵션을 `/etc/sysconfig/docker`추가합니다. 예를 들어:
   
   `OPTIONS='--selinux-enabled --log-driver=journald --live-restore --signature-verification=false'`
   
2. 다음 명령을 실행 하여 Docker 데몬 서비스를 다시 시작합니다.
   
   ```bash
   sudo systemctl restart docker.service
   ```

에 `--signature-verification` 대한 자세한 내용은 `man dockerd`를 실행하여 찾을 수 있습니다.

### <a name="az-acr-login-succeeds-but-docker-fails-with-error-unauthorized-authentication-required"></a>az acr 로그인이 성공하지만 도커가 오류로 실패 : 무단 : 인증이 필요합니다

예를 들어 `docker push myregistry.azurecr.io/myimage:latest`레지스트리 리소스 이름이 대문자이거나 대/소문자와 같은 `myRegistry`대/소문자인 경우에도 모든 소문자 서버 URL을 사용해야 합니다.

### <a name="enable-and-get-the-debug-logs-of-the-docker-daemon"></a>Docker 데몬의 디버그 로그 사용 및 받기  

`debug` 옵션으로 시작합니다. `dockerd` 먼저 Docker 데몬 구성 파일`/etc/docker/daemon.json`()이 존재하지 않는 경우 `debug` 만들고 옵션을 추가합니다.

```json
{   
    "debug": true   
}
```

그런 다음 데몬을 다시 시작합니다. 예를 들어, 우분투 14.04와 함께:

```bash
sudo service docker restart
```

자세한 내용은 Docker [설명서에서](https://docs.docker.com/engine/admin/#enable-debugging)찾을 수 있습니다. 

 * 로그는 시스템에 따라 다른 위치에서 생성될 수 있습니다. 예를 들어, 우분투 14.04의 `/var/log/upstart/docker.log`경우.   
자세한 내용은 [Docker 설명서를](https://docs.docker.com/engine/admin/#read-the-logs) 참조하십시오.    

 * Windows용 도커의 경우 로그는 %LOCALAPPDATA%/도커/에서 생성됩니다. 그러나 아직 모든 디버그 정보가 포함되어 있지 않을 수 있습니다.   

   전체 데몬 로그에 액세스하려면 몇 가지 추가 단계가 필요할 수 있습니다.

    ```console
    docker run --privileged -it --rm -v /var/run/docker.sock:/var/run/docker.sock -v /usr/local/bin/docker:/usr/local/bin/docker alpine sh

    docker run --net=host --ipc=host --uts=host --pid=host -it --security-opt=seccomp=unconfined --privileged --rm -v /:/host alpine /bin/sh
    chroot /host
    ```
    이제 실행 `dockerd`중인 VM의 모든 파일에 액세스할 수 있습니다. 로그는 에 `/var/log/docker.log`있습니다.

### <a name="new-user-permissions-may-not-be-effective-immediately-after-updating"></a>새 사용자 권한은 업데이트 후 즉시 적용되지 않을 수 있습니다.

서비스 주체에 새 권한(새 역할)을 부여하면 변경 이 즉시 적용되지 않을 수 있습니다. 가능한 두 가지 이유는 다음과 같습니다.

* Azure Active Directory 역할 할당 지연입니다. 일반적으로 는 빠르지만 전파 지연으로 인해 몇 분이 걸릴 수 있습니다.
* ACR 토큰 서버에 대한 권한 지연입니다. 이 경우 최대 10분이 걸릴 수 있습니다. 완화하려면 1분 `docker logout` 후에 동일한 사용자로 다시 인증할 수 있습니다.

  ```bash
  docker logout myregistry.azurecr.io
  docker login myregistry.azurecr.io
  ```

현재 ACR은 사용자가 홈 복제 삭제를 지원하지 않습니다. 해결 방법은 템플릿에 홈 복제 만들기를 포함하지만 아래와 `"condition": false` 같이 추가하여 생성을 건너 뛰는 것입니다.

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

### <a name="authentication-information-is-not-given-in-the-correct-format-on-direct-rest-api-calls"></a>직접 REST API 호출에서 인증 정보가 올바른 형식으로 제공되지 않습니다.

특히 `-L`옵션과 `InvalidAuthenticationInfo` `--location` 함께 `curl` 도구를 사용하여 (리디렉션을 따르기 위해) 오류가 발생할 수 있습니다.
예를 들어 옵션 및 기본 `curl` `-L` 인증을 사용하여 Blob을 가져옵니다.

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

근본 원인은 일부 `curl` 구현이 원래 요청의 헤더로 리디렉션을 따르기 때문에 입니다.

문제를 해결하려면 헤더 없이 수동으로 리디렉션을 따라야 합니다. 다음 `-D -` 헤더를 추출하는 옵션으로 `curl` 응답 헤더를 `Location` 인쇄합니다.

```bash
redirect_url=$(curl -s -D - -H "Authorization: basic $credential" https://$registry.azurecr.io/v2/$repository/blobs/$digest | grep "^Location: " | cut -d " " -f2 | tr -d '\r')
curl $redirect_url
```

### <a name="why-does-the-azure-portal-not-list-all-my-repositories-or-tags"></a>Azure 포털에 내 모든 리포지토리 또는 태그가 나열되지 않는 이유는 무엇입니까? 

Microsoft Edge/IE 브라우저를 사용하는 경우 최소 100개의 리포지토리 또는 태그를 볼 수 있습니다. 레지스트리에 100개 이상의 리포지토리 또는 태그가 있는 경우 Firefox 또는 Chrome 브라우저를 사용하여 모두 나열하는 것이 좋습니다.

### <a name="why-does-the-azure-portal-fail-to-fetch-repositories-or-tags"></a>Azure 포털이 리포지토리 또는 태그를 가져오지 못하는 이유는 무엇입니까?

브라우저가 저장소 또는 태그를 서버에 가져오는 요청을 보내지 못할 수 있습니다. 다음과 같은 여러 가지 이유가 있을 수 있습니다.

* 네트워크 연결 부족
* 방화벽
* 광고 차단기
* DNS 오류

네트워크 관리자에게 문의하거나 네트워크 구성 및 연결을 확인하십시오. Azure `az acr check-health -n yourRegistry` CLI를 사용하여 실행하여 환경이 컨테이너 레지스트리에 연결할 수 있는지 확인합니다. 또한 브라우저에서 시크릿 또는 비공개 세션을 시도하여 오래된 브라우저 캐시 나 쿠키를 방지 할 수도 있습니다.

### <a name="why-does-my-pull-or-push-request-fail-with-disallowed-operation"></a>끌어오기 또는 푸시 요청이 허용되지 않는 작업으로 실패하는 이유는 무엇입니까?

다음은 작업이 허용되지 않는 몇 가지 시나리오입니다.
* 클래식 레지스트리는 더 이상 지원되지 않습니다. [az acr 업데이트](https://docs.microsoft.com/cli/azure/acr?view=azure-cli-latest#az-acr-update) 또는 Azure 포털을 사용하여 지원되는 [SCO로](https://aka.ms/acr/skus) 업그레이드하십시오.
* 이미지 또는 리포지토리는 삭제하거나 업데이트할 수 없도록 잠겨 있을 수 있습니다. [az acr 표시 리포지토리](https://docs.microsoft.com/azure/container-registry/container-registry-image-lock) 명령을 사용하여 현재 특성을 볼 수 있습니다.
* 이미지가 격리된 경우 일부 작업은 허용되지 않습니다. [검역소](https://github.com/Azure/acr/tree/master/docs/preview/quarantine)에 대해 자세히 알아보십시오.

### <a name="how-do-i-collect-http-traces-on-windows"></a>Windows에서 http 추적을 수집하려면 어떻게 해야 합니까?

#### <a name="prerequisites"></a>사전 요구 사항

- 바이올린에서 https 해독을 활성화합니다.<https://docs.telerik.com/fiddler/Configure-Fiddler/Tasks/DecryptHTTPS>
- Dockerui를 통해 프록시를 사용하도록 설정합니다.<https://docs.docker.com/docker-for-windows/#proxies>
- 완료되면 되돌려야 합니다.  Docker는 이 사용 이 사용 이 사용 및 바이올린이 실행되지 않는 작업으로 작동하지 않습니다.

#### <a name="windows-containers"></a>Windows 컨테이너

Docker 프록시를 127.0.0.1:8888로 구성

#### <a name="linux-containers"></a>Linux 컨테이너

Docker vm 가상 스위치의 IP 찾기:

```powershell
(Get-NetIPAddress -InterfaceAlias "*Docker*" -AddressFamily IPv4).IPAddress
```

Docker 프록시를 이전 명령 및 포트 8888의 출력으로 구성합니다(예: 10.0.75.1:8888).

## <a name="tasks"></a>작업

- [실행을 일괄 취소하려면 어떻게 해야 합니까?](#how-do-i-batch-cancel-runs)
- [az acr 빌드 명령에 .git 폴더를 포함하려면 어떻게 해야 합니까?](#how-do-i-include-the-git-folder-in-az-acr-build-command)
- [태스크가 소스 트리거에 대한 GitLab을 지원합니까?](#does-tasks-support-gitlab-for-source-triggers)
- [태스크가 지원하는 git 리포지토리 관리 서비스는 무엇입니까?](#what-git-repository-management-service-does-tasks-support)

### <a name="how-do-i-batch-cancel-runs"></a>실행을 일괄 취소하려면 어떻게 해야 합니까?

다음 명령은 지정된 레지스트리에서 실행 중인 모든 작업을 취소합니다.

```azurecli
az acr task list-runs -r $myregistry --run-status Running --query '[].runId' -o tsv \
| xargs -I% az acr task cancel-run -r $myregistry --run-id %
```

### <a name="how-do-i-include-the-git-folder-in-az-acr-build-command"></a>az acr 빌드 명령에 .git 폴더를 포함하려면 어떻게 해야 합니까?

로컬 소스 폴더를 `az acr build` 명령으로 전달하면 `.git` 기본적으로 업로드된 패키지에서 폴더가 제외됩니다. 다음 설정을 `.dockerignore` 사용하여 파일을 만들 수 있습니다. 업로드된 패키지의 모든 `.git` 파일을 복원하라는 명령을 알려줍니다. 

`!.git/**`

이 설정은 `az acr run` 명령에도 적용됩니다.

### <a name="does-tasks-support-gitlab-for-source-triggers"></a>태스크가 소스 트리거에 대한 GitLab을 지원합니까?

현재 소스 트리거에 대한 GitLab을 지원하지 않습니다.

### <a name="what-git-repository-management-service-does-tasks-support"></a>태스크가 지원하는 git 리포지토리 관리 서비스는 무엇입니까?

| Git 서비스 | 소스 컨텍스트 | 수동 빌드 | 커밋 트리거를 통한 자동 빌드 |
|---|---|---|---|
| GitHub | https://github.com/user/myapp-repo.git#mybranch:myfolder | yes | yes |
| Azure Repos | https://dev.azure.com/user/myproject/_git/myapp-repo#mybranch:myfolder | yes | yes |
| GitLab | https://gitlab.com/user/myapp-repo.git#mybranch:myfolder | yes | 예 |
| BitBucket | https://user@bitbucket.org/user/mayapp-repo.git#mybranch:myfolder | yes | 예 |

## <a name="run-error-message-troubleshooting"></a>오류 메시지 문제 해결 실행

| 오류 메시지 | 문제 해결 가이드 |
|---|---|
|VM에 대한 액세스가 구성되지 않으므로 구독을 찾을 수 없습니다.|ACR 태스크에서 사용하는 `az login --identity` 경우 이러한 일이 발생할 수 있습니다. 이는 일시적인 오류이며 관리되는 ID의 역할 할당이 전파되지 않은 경우에 발생합니다. 작업을 다시 시도하기 전에 몇 초 를 기다리는 중입니다.|

## <a name="cicd-integration"></a>CI/CD 통합

- [서클시 (동그라미)](https://github.com/Azure/acr/blob/master/docs/integration/CircleCI.md)
- [GitHub 작업](https://github.com/Azure/acr/blob/master/docs/integration/github-actions/github-actions.md)

## <a name="next-steps"></a>다음 단계

* Azure 컨테이너 레지스트리에 대해 [자세히 알아보세요.](container-registry-intro.md)
