---
title: Azure Arc 지원 Kubernetes 클러스터를 위한 Azure RBAC
services: azure-arc
ms.service: azure-arc
ms.date: 04/05/2021
ms.topic: article
author: shashankbarsin
ms.author: shasb
description: Azure Arc 지원 Kubernetes 클러스터에서 권한 부여 검사를 위해 Azure RBAC 사용
ms.openlocfilehash: bd8029cb2772a6f6bd9821abe6acf69c9c08599d
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106450796"
---
# <a name="azure-rbac-for-azure-arc-enabled-kubernetes-clusters"></a>Azure Arc 지원 Kubernetes 클러스터를 위한 Azure RBAC

Kubernetes [ClusterRoleBinding 및 RoleBinding](https://kubernetes.io/docs/reference/access-authn-authz/rbac/#rolebinding-and-clusterrolebinding) 개체 형식은 Kubernetes에서 기본적으로 권한 부여를 정의하는 데 도움이 됩니다. Azure RBAC를 사용하면 Azure에서 Azure Active Directory 및 역할 할당을 사용하여 클러스터에 대한 권한 부여 확인을 제어할 수 있습니다. 즉, 이제 Azure 역할 할당을 사용하여 배포, Pod 및 서비스와 같은 Kubernetes 개체를 읽고, 쓰고, 삭제할 수 있는 사람을 세부적으로 제어할 수 있습니다.

이 기능에 대한 개념적인 개요는 [Azure RBAC - Azure Arc 지원 Kubernetes](conceptual-azure-rbac.md) 문서를 참조하세요.

[!INCLUDE [preview features note](./includes/preview/preview-callout.md)]

## <a name="prerequisites"></a>필수 구성 요소

- 2\.16.0 이상 버전의 [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli)를 설치하거나 업그레이드합니다.

- 1\.1.0 이상 버전의 `connectedk8s` Azure CLI 확장을 설치합니다.

    ```azurecli
    az extension add --name connectedk8s
    ```
    
    `connectedk8s` 확장이 이미 설치되어 있는 경우 다음 명령을 사용하여 최신 버전으로 업데이트할 수 있습니다. 

    ```azurecli
    az extension update --name connectedk8s
    ```

- 기존 Azure Arc 지원 Kubernetes 연결 클러스터.
    - 아직 클러스터를 연결하지 않은 경우 [빠른 시작](quickstart-connect-cluster.md)을 사용하세요.
    - [에이전트](agent-upgrade.md#manually-upgrade-agents)를 버전 >= 1.1.0으로 업그레이드합니다.

> [!NOTE]
> 이 기능은 사용자가 클러스터의 `apiserver`에 액세스할 수 없는 클라우드 공급자(예: Elastic Kubernetes Service 또는 Google Kubernetes Engine)의 관리되는 Kubernetes 제품에 대해 설정할 수 없습니다. Azure Kubernetes Service(AKS) 클러스터의 경우 이 [기능은 고유하게 제공되며](../../aks/manage-azure-rbac.md) AKS 클러스터를 Azure Arc에 연결할 필요가 없습니다.

## <a name="set-up-azure-ad-applications"></a>Azure AD 애플리케이션 설정

### <a name="create-server-application"></a>서버 애플리케이션 만들기

1. 새 Azure AD 애플리케이션을 만들고 이후 단계에서 `serverApplicationId`로 사용되는 해당 `appId` 값을 가져옵니다.

    ```azurecli
    az ad app create --display-name "<clusterName>Server" --identifier-uris "https://<clusterName>Server" --query appId -o tsv
    ```

1. 애플리케이션 그룹 멤버 자격 클레임을 업데이트합니다.

    ```azurecli
    az ad app update --id <serverApplicationId> --set groupMembershipClaims=All
    ```

1. 서비스 주체를 만들고 나중에 클러스터에서 이 기능을 사용하도록 설정할 때 `serverApplicationSecret`으로 필요한 해당 `password` 필드 값을 가져옵니다.

    ```azurecli
    az ad sp create --id <serverApplicationId>
    az ad sp credential reset --name <serverApplicationId> --credential-description "ArcSecret" --query password -o tsv
    ```

1. 애플리케이션 API 권한 부여:

    ```azurecli
    az ad app permission add --id <serverApplicationId> --api 00000003-0000-0000-c000-000000000000 --api-permissions e1fe6dd8-ba31-4d61-89e7-88639da4683d=Scope
    az ad app permission grant --id <serverApplicationId> --api 00000003-0000-0000-c000-000000000000
    ```

    > [!NOTE]
    > * 이 단계는 Azure 테넌트 관리자로 실행해야 합니다.
    > * 프로덕션에서 이 기능을 사용하려면 각 클러스터에 대해 서로 다른 서버 애플리케이션을 만드는 것이 좋습니다.

### <a name="create-client-application"></a>클라이언트 애플리케이션 만들기

1. 새 Azure AD 애플리케이션을 만들고 이후 단계에서 `clientApplicationId`로 사용되는 해당 'appId' 값을 가져옵니다.

    ```azurecli
    az ad app create --display-name "<clusterName>Client" --native-app --reply-urls "https://<clusterName>Client" --query appId -o tsv
    ```

2. 이 클라이언트 애플리케이션의 서비스 주체를 만듭니다.

    ```azurecli
    az ad sp create --id <clientApplicationId>
    ```

3. 서버 애플리케이션에 대해 `oAuthPermissionId`를 가져옵니다.

    ```azurecli
    az ad app show --id <serverApplicationId> --query "oauth2Permissions[0].id" -o tsv
    ```

4. 클라이언트 애플리케이션에 대해 필요한 권한을 부여합니다.

    ```azurecli
    az ad app permission add --id <clientApplicationId> --api <serverApplicationId> --api-permissions <oAuthPermissionId>=Scope
    az ad app permission grant --id <clientApplicationId> --api <serverApplicationId>
    ```

## <a name="create-a-role-assignment-for-the-server-application"></a>서버 애플리케이션에 대한 역할 할당 만들기

요청을 수행하는 사용자에게 해당 요청에 포함된 Kubernets 개체에 대한 권한이 부여되었는지 확인할 수 있는 `Microsoft.Authorization/*/read` 권한이 서버 애플리케이션에 필요합니다.

1. 다음 콘텐츠가 포함된 accessCheck.json이라는 파일을 만듭니다.

    ```json
    {
      "Name": "Read authorization",
      "IsCustom": true,
      "Description": "Read authorization",
      "Actions": ["Microsoft.Authorization/*/read"],
      "NotActions": [],
      "DataActions": [],
      "NotDataActions": [],
      "AssignableScopes": [
        "/subscriptions/<subscription-id>"
      ]
    }
    ```

    `<subscription-id>`를 실제 구독 ID로 바꿉니다.

2. 다음 명령을 실행하여 새 사용자 지정 역할을 만듭니다.

    ```azurecli
    az role definition create --role-definition ./accessCheck.json
    ```

3. 위 명령의 출력에서 이후 단계에서 `roleId`로 사용되는 `id` 필드의 값을 저장합니다.

4. 담당자로서 위에서 만든 역할을 사용하여 서버 애플리케이션에서 역할 할당을 만듭니다.

    ```azurecli
    az role assignment create --role <roleId> --assignee <serverApplicationId> --scope /subscriptions/<subscription-id>
    ```

## <a name="enable-azure-rbac-on-cluster"></a>클러스터에서 Azure RBAC 사용

1. 다음 명령을 실행하여 Arc 지원 Kubernetes 클러스터에서 Azure RBAC를 사용하도록 설정합니다.

    ```console
    az connectedk8s enable-features -n <clusterName> -g <resourceGroupName> --features azure-rbac --app-id <serverApplicationId> --app-secret <serverApplicationSecret>
    ```
    
    > [!NOTE]
    > 1. 위 명령을 실행하기 전 컴퓨터의 `kubeconfig` 파일이 Azure RBAC 기능을 사용하도록 설정할 클러스터를 가리키는지 확인합니다.
    > 2. Azure RBAC 대신 Kubernetes 네이티브 ClusterRoleBinding 및 RoleBinding 개체를 사용하여 권한 부여 검사를 진행 중인 쉼표로 구분된 사용자 이름/이메일/oid 목록에 대해 위 명령과 함께 `--skip-azure-rbac-list`를 사용합니다.

### <a name="for-a-generic-cluster-where-there-is-no-reconciler-running-on-apiserver-specification"></a>apiserver 사양으로 실행되는 조정자가 없는 일반 클러스터의 경우:

1. 클러스터의 모든 마스터 노드에 SSH로 연결하고 다음 단계를 실행합니다.

    1. 편집 모드에서 `apiserver` 매니페스트 열기:
        
        ```console
        sudo vi /etc/kubernetes/manifests/kube-apiserver.yaml
        ```

    1. `volumes` 아래에 다음 사양 추가:
        
        ```yml
        - name: azure-rbac
          secret:
            secretName: azure-arc-guard-manifests
        ```

    1. `volumeMounts` 아래에 다음 사양 추가:

        ```yml
        - mountPath: /etc/guard
          name: azure-rbac
          readOnly: true
        ```

    1. 다음 `apiserver` 인수 추가:

        ```yml
        - --authentication-token-webhook-config-file=/etc/guard/guard-authn-webhook.yaml
        - --authentication-token-webhook-cache-ttl=5m0s
        - --authorization-webhook-cache-authorized-ttl=5m0s
        - --authorization-webhook-config-file=/etc/guard/guard-authz-webhook.yaml
        - --authorization-webhook-version=v1
        - --authorization-mode=Node,Webhook,RBAC
        ```
    
        Kubernetes 클러스터 버전이 1.19.0 이상이면 다음 `apiserver argument`도 함께 설정해야 합니다.

        ```yml
        - --authentication-token-webhook-version=v1
        ```

    1. `apiserver` Pod를 업데이트하기 위해 편집기를 저장하고 종료합니다.


### <a name="for-a-cluster-created-using-cluster-api"></a>Cluster API를 사용하여 만든 클러스터의 경우

1. 인증 및 권한 부여 웹후크 구성 파일 `from the workload cluster`가 포함된 가드 비밀을 컴퓨터에 복사합니다.

    ```console
    kubectl get secret azure-arc-guard-manifests -n kube-system -o yaml > azure-arc-guard-manifests.yaml
    ```

1. `azure-arc-guard-manifests.yaml` 파일에 있는 `namespace` 필드를 워크로드 클러스터 만들기를 위해 사용자 지정 리소스를 적용하려는 관리 클러스터 내의 네임스페이스로 변경합니다.

1. 이 매니페스트를 적용합니다.

    ```console
    kubectl apply -f azure-arc-guard-manifests.yaml
    ```

1. `kubectl edit kcp <clustername>-control-plane`을 실행하여 `KubeadmControlPlane` 개체를 편집합니다.
    
    1. 다음 코드 조각을 `files:` 아래에 추가합니다.
    
        ```console
        - contentFrom:
            secret:
              key: guard-authn-webhook.yaml
              name: azure-arc-guard-manifests
          owner: root:root
          path: /etc/kubernetes/guard-authn-webhook.yaml
          permissions: "0644"
        - contentFrom:
            secret:
              key: guard-authz-webhook.yaml
              name: azure-arc-guard-manifests
          owner: root:root
          path: /etc/kubernetes/guard-authz-webhook.yaml
          permissions: "0644"
        ```

    1. 다음 코드 조각을 `apiServer:` -> `extraVolumes:` 아래에 추가합니다.
    
        ```console
        - hostPath: /etc/kubernetes/guard-authn-webhook.yaml
            mountPath: /etc/guard/guard-authn-webhook.yaml
            name: guard-authn
            readOnly: true
        - hostPath: /etc/kubernetes/guard-authz-webhook.yaml
            mountPath: /etc/guard/guard-authz-webhook.yaml
            name: guard-authz
            readOnly: true
        ```

    1. 다음 코드 조각을 `apiServer:` -> `extraArgs:` 아래에 추가합니다.
    
        ```console
        authentication-token-webhook-cache-ttl: 5m0s
        authentication-token-webhook-config-file: /etc/guard/guard-authn-webhook.yaml
        authentication-token-webhook-version: v1
        authorization-mode: Node,Webhook,RBAC
        authorization-webhook-cache-authorized-ttl: 5m0s
        authorization-webhook-config-file: /etc/guard/guard-authz-webhook.yaml
        authorization-webhook-version: v1
        ```

    1. `KubeadmControlPlane` 개체를 업데이트하기 위해 저장하고 종료합니다. 이러한 변경 사항이 워크로드 클러스터에 적용될 때까지 기다립니다.


## <a name="create-role-assignments-for-users-to-access-the-cluster"></a>사용자가 클러스터에 액세스하기 위한 역할 할당 만들기

Azure Arc 지원 Kubernetes 리소스 소유자는 기본 제공 역할 또는 사용자 지정 역할을 사용하여 다른 사용자에게 Kubernetes 클러스터 액세스 권한을 부여할 수 있습니다.

### <a name="built-in-roles"></a>기본 제공 역할

| 역할 | Description |
|---|---|
| Azure Arc Kubernetes 뷰어 | 네임스페이스에 있는 대부분의 개체를 볼 수 있는 읽기 전용 액세스 권한을 허용합니다. 이 역할은 비밀 보기를 허용하지 않습니다. 비밀에 대한 `read` 권한은 네임스페이스에 있는 `ServiceAccount` 자격 증명에 대한 액세스를 허용하고, 이 권한은 또한 해당 `ServiceAccount`를 사용하여 API 액세스를 허용하는 권한 상승 형태를 갖기 때문입니다. |
| Azure Arc Kubernetes 작성자 | 네임스페이스에 있는 대부분의 개체에 대해 읽기/쓰기 액세스 권한을 허용합니다. 이 역할은 보기 또는 수정 역할 또는 역할 바인딩을 허용하지 않습니다. 그러나 이 역할을 사용하여 네임스페이스에서 `ServiceAccount`로 Pod를 실행하고 비밀에 액세스할 수 있으므로, 이를 사용하여 네임스페이스의 모든 `ServiceAccount`의 API 액세스 수준을 얻을 수 있습니다. |
| Azure Arc Kubernetes 관리자 | 관리자 액세스 권한을 허용합니다. RoleBinding을 사용하여 네임스페이스 내에서 부여됩니다. RoleBinding에 사용되는 경우 네임스페이스 내에서 역할 및 역할 바인딩 만들기 기능을 포함하여 네임스페이스에 있는 대부분의 리소스에 대한 읽기/쓰기 액세스 권한을 허용합니다. 이 역할은 리소스 할당량 또는 네임스페이스 자체에 대한 쓰기 권한을 허용하지 않습니다. |
| Azure Arc Kubernetes 클러스터 관리자 | 슈퍼 사용자 액세스를 허용하여 모든 리소스에 대한 모든 작업을 실행할 수 있습니다. ClusterRoleBinding에 사용될 경우 클러스터 및 모든 네임스페이스에 있는 모든 리소스에 대해 전체 제어 권한을 부여합니다. RoleBinding에서 사용될 경우 네임스페이스 자체를 포함하여 역할 바인딩의 네임스페이스에 있는 모든 리소스에 대한 전체 제어 권한을 부여합니다.|

Azure Portal에서 클러스터 리소스의 `Access Control (IAM)` 블레이드에서 Arc 지원 Kubernetes 클러스터로 범위가 지정된 역할 할당을 만들 수 있습니다. 다음과 같이 Azure CLI 명령을 사용할 수도 있습니다.

```azurecli
az role assignment create --role "Azure Arc Kubernetes Cluster Admin" --assignee <AZURE-AD-ENTITY-ID> --scope $ARM_ID
```

여기서 `AZURE-AD-ENTITY-ID`는 사용자 이름(예: testuser@mytenant.onmicrosoft.com) 또는 서비스 주체의 `appId`일 수도 있습니다.

다음은 클러스터 내의 특정 네임스페이스로 범위가 지정된 역할 할당을 만드는 예제입니다.

```azurecli
az role assignment create --role "Azure Arc Kubernetes Viewer" --assignee <AZURE-AD-ENTITY-ID> --scope $ARM_ID/namespaces/<namespace-name>
```

> [!NOTE]
> Azure Portal 또는 CLI를 사용하여 클러스터로 범위가 지정된 역할 할당을 만들 수 있지만, 네임스페이스로 범위가 지정된 역할 할당은 CLI를 사용해서만 만들 수 있습니다.

### <a name="custom-roles"></a>사용자 지정 역할

역할 할당의 용도에 따라 고유 역할 할당을 만들도록 선택할 수 있습니다.

사용자의 배포 읽기만 허용하는 역할 할당에 대한 아래 예제를 진행하세요. 자세한 내용은 [역할 정의를 생성하는 데 사용할 수 있는 데이터 작업의 전체 목록](../../role-based-access-control/resource-provider-operations.md#microsoftkubernetes)을 참조하세요.

아래 JSON 개체를 custom-role.json이라는 파일에 복사합니다. `<subscription-id>` 자리 표시자를 실제 구독 ID로 바꿉니다. 아래 사용자 지정 역할에는 데이터 작업 중 하나가 사용되며, 이를 사용해서 역할 할당이 생성되는 범위(클러스터/네임스페이스)의 모든 배포를 볼 수 있습니다.

```json
{
    "Name": "Arc Deployment Viewer",
    "Description": "Lets you view all deployments in cluster/namespace.",
    "Actions": [],
    "NotActions": [],
    "DataActions": [
        "Microsoft.Kubernetes/connectedClusters/apps/deployments/read"
    ],
    "NotDataActions": [],
    "assignableScopes": [
        "/subscriptions/<subscription-id>"
    ]
}
```

1. `custom-role.json`을 저장한 폴더에서 아래 명령을 실행하여 역할 정의를 만듭니다.

    ```bash
    az role definition create --role-definition @custom-role.json
    ```

1. 이 사용자 지정 역할 정의를 사용하여 역할 할당을 만듭니다.

    ```bash
    az role assignment create --role "Arc Deployment Viewer" --assignee <AZURE-AD-ENTITY-ID> --scope $ARM_ID/namespaces/<namespace-name>
    ```

## <a name="configure-kubectl-with-user-credentials"></a>사용자 자격 증명으로 kubectl 구성

클러스터에 액세스하는 데 필요한 `kubeconfig` 파일은 두 가지 방법으로 가져올 수 있습니다.
1. Azure Arc 지원 Kubernetes 클러스터의 [클러스터 연결](cluster-connect.md) 기능(`az connectedk8s proxy`)을 사용합니다.
1. 클러스터 관리자는 `kubeconfig` 파일을 다른 모든 사용자와 공유합니다.

### <a name="if-you-are-accessing-cluster-using-cluster-connect-feature"></a>클러스터 연결 기능을 사용하여 클러스터에 액세스하는 경우

다음 명령을 실행하여 프록시 프로세스를 시작합니다.

```console
az connectedk8s proxy -n <clusterName> -g <resourceGroupName>
```

프록시 프로세스가 실행된 후 콘솔에서 또 다른 탭을 열어서 [클러스터에 요청 전송을 시작](#sending-requests-to-cluster)할 수 있습니다.

### <a name="if-cluster-admin-shared-the-kubeconfig-file-with-you"></a>클러스터 관리자가 `kubeconfig` 파일을 사용자에게 공유한 경우 

1. 다음 명령을 실행하여 사용자에 대해 자격 증명을 설정합니다.

    ```console
    kubectl config set-credentials <testuser>@<mytenant.onmicrosoft.com> \
    --auth-provider=azure \
    --auth-provider-arg=environment=AzurePublicCloud \
    --auth-provider-arg=client-id=<clientApplicationId> \
    --auth-provider-arg=tenant-id=<tenantId> \
    --auth-provider-arg=apiserver-id=<serverApplicationId>
    ```

1. 이전에 만든 `kubeconfig` 파일을 엽니다. `contexts`에서 클러스터와 연결된 컨텍스트가 이전 단계에서 만든 사용자 자격 증명을 가리키는지 확인합니다.

1. 사용자 구성 아래에서 **config-mode** 설정을 추가합니다.
  
    ```console
    name: testuser@mytenant.onmicrosoft.com
    user:
        auth-provider:
        config:
            apiserver-id: $SERVER_APP_ID
            client-id: $CLIENT_APP_ID
            environment: AzurePublicCloud
            tenant-id: $TENANT_ID
            config-mode: "1"
        name: azure
    ```

## <a name="sending-requests-to-cluster"></a>클러스터에 요청 전송

1. `kubectl` 명령을 실행합니다. 예:
  * `kubectl get nodes` 
  * `kubectl get pods`

1. 브라우저 기반 인증을 묻는 메시지가 표시되면 디바이스 로그인 URL `https://microsoft.com/devicelogin`을 복사하고 웹 브라우저로 엽니다.

1. 콘솔에 출력된 코드를 입력하고, 터미널에서 코드를 복사하여 디바이스 인증 입력 프롬프트에 붙여넣습니다.

1. 사용자 이름(testuser@mytenant.onmicrosoft.com) 및 연결된 암호를 입력합니다.

1. 이와 비슷한 오류 메시지가 표시되면 요청된 리소스에 액세스하도록 권한이 부여되지 않은 것입니다.

    ```console
    Error from server (Forbidden): nodes is forbidden: User "testuser@mytenant.onmicrosoft.com" cannot list resource "nodes" in API group "" at the cluster scope: User doesn't have access to the resource in Azure. Update role assignment to allow access.
    ```

    이 사용자가 리소스에 액세스하도록 권한을 부여하려면 관리자가 새 역할 할당을 만들어야 합니다.


## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [클러스터 연결](cluster-connect.md)을 사용하여 클러스터에 안전하게 연결