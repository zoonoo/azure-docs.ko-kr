---
title: 기존 응용 프로그램 게이트웨이를 사용하여 인서스 컨트롤러 만들기
description: 이 문서에서는 기존 응용 프로그램 게이트웨이를 사용하여 응용 프로그램 게이트웨이 인서스 컨트롤러를 배포하는 방법에 대한 정보를 제공합니다.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: 949f1b3ee3db72e1c541c3dd4c5f74f364f1b514
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/22/2020
ms.locfileid: "81869889"
---
# <a name="install-an-application-gateway-ingress-controller-agic-using-an-existing-application-gateway"></a>기존 응용 프로그램 게이트웨이를 사용하여 응용 프로그램 게이트웨이 인그레스 컨트롤러(AGIC) 설치

응용 프로그램 게이트웨이 침투 컨트롤러(AGIC)는 Kubernetes 클러스터 내의 포드입니다.
AGIC는 Kubernetes [침투](https://kubernetes.io/docs/concepts/services-networking/ingress/) 리소스를 모니터링하고 Kubernetes 클러스터의 상태에 따라 응용 프로그램 게이트웨이 구성을 만들고 적용합니다.

## <a name="outline"></a>개요:
- [필수 구성 요소](#prerequisites)
- [AZURE 리소스 관리자 인증(ARM)](#azure-resource-manager-authentication)
    - 옵션 1: [Aad-pod-ID를 설정하고](#set-up-aad-pod-identity) ARM에서 Azure ID를 만듭니다.
    - 옵션 2: [서비스 주체 사용](#using-a-service-principal)
- [투구를 사용하여 인그레스 컨트롤러 설치](#install-ingress-controller-as-a-helm-chart)
- [다중 클러스터 / 공유 응용 프로그램 게이트웨이](#multi-cluster--shared-application-gateway): 하나 이상의 AKS 클러스터 및/또는 다른 Azure 구성 요소 간에 응용 프로그램 게이트웨이가 공유되는 환경에서 AGIC를 설치합니다.

## <a name="prerequisites"></a>사전 요구 사항
이 문서에서는 다음 도구 및 인프라가 이미 설치되어 있다고 가정합니다.
- [고급 네트워킹을](https://docs.microsoft.com/azure/aks/configure-azure-cni) 지원하는 [AKS](https://azure.microsoft.com/services/kubernetes-service/)
- AKS와 동일한 가상 네트워크의 [애플리케이션 게이트웨이 v2](https://docs.microsoft.com/azure/application-gateway/create-zone-redundant)
- AKS 클러스터에 설치된 [AAD 포드 ID](https://github.com/Azure/aad-pod-identity)
- [클라우드 셸은](https://shell.azure.com/) `az` CLI가 `kubectl`있고 `helm` 설치되는 Azure 셸 환경입니다. 이러한 도구는 아래 명령에 필요합니다.

AGIC를 설치하기 전에 __응용 프로그램 게이트웨이의 구성을 백업하십시오.__
  1. Azure 포털을 사용하여 인스턴스로 이동 [Azure portal](https://portal.azure.com/) `Application Gateway`
  2. 클릭한 시간 `Export template``Download`

다운로드 한 zip 파일에는 필요한 경우 앱 게이트웨이를 복원하는 데 사용할 수있는 JSON 템플릿, bash 및 PowerShell 스크립트가 있습니다.

## <a name="install-helm"></a>Helm 설치
[헬름은](https://docs.microsoft.com/azure/aks/kubernetes-helm) 쿠베네츠의 패키지 매니저입니다. 우리는 `application-gateway-kubernetes-ingress` 패키지를 설치하는 데 활용할 것입니다.
[클라우드 쉘을](https://shell.azure.com/) 사용하여 투구를 설치합니다.

1. [투구를](https://docs.microsoft.com/azure/aks/kubernetes-helm) 설치하고 다음을 `application-gateway-kubernetes-ingress` 실행하여 투구 패키지를 추가합니다.

    - *RBAC 사용 가능* AKS 클러스터

    ```bash
    kubectl create serviceaccount --namespace kube-system tiller-sa
    kubectl create clusterrolebinding tiller-cluster-rule --clusterrole=cluster-admin --serviceaccount=kube-system:tiller-sa
    helm init --tiller-namespace kube-system --service-account tiller-sa
    ```

    - *RBAC 사용 안 함* AKS 클러스터

    ```bash
    helm init
    ```

1. AGIC 리포지토리를 추가합니다.
    ```bash
    helm repo add application-gateway-kubernetes-ingress https://appgwingress.blob.core.windows.net/ingress-azure-helm-package/
    helm repo update
    ```

## <a name="azure-resource-manager-authentication"></a>Azure 리소스 관리자 인증

AGIC는 Kubernetes API 서버 및 Azure 리소스 관리자와 통신합니다. 이러한 API에 액세스하려면 ID가 필요합니다.

## <a name="set-up-aad-pod-identity"></a>AAD 포드 ID 설정

[AAD 포드 ID는](https://github.com/Azure/aad-pod-identity) AKS에서 실행되는 AGIC와 유사한 컨트롤러입니다. Azure Active Directory ID를 Kubernetes 포드에 바인딩합니다. Kubernetes 포드의 응용 프로그램이 다른 Azure 구성 요소와 통신할 수 있도록 ID가 필요합니다. 여기서 는 [ARM에](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)HTTP 요청을 하려면 AGIC 포드에 대한 권한 부여가 필요합니다.

[AAD 포드 ID 설치 지침에](https://github.com/Azure/aad-pod-identity#deploy-the-azure-aad-identity-infra) 따라 이 구성 요소를 AKS에 추가합니다.

다음으로 Azure ID를 만들고 사용 권한 ARM을 제공해야 합니다.
[Cloud Shell을](https://shell.azure.com/) 사용하여 다음 명령을 모두 실행하고 ID를 만듭니다.

1. **AKS 노드와 동일한 리소스 그룹에서**Azure ID를 만듭니다. 올바른 리소스 그룹을 선택하는 것이 중요합니다. 아래 명령에 필요한 리소스 그룹은 AKS 포털 창에서 참조하는 리소스 그룹이 *아닙니다.* 가상 시스템의 리소스 그룹입니다. `aks-agentpool` 일반적으로 해당 리소스 `MC_` 그룹은 AKS의 이름으로 시작하고 포함합니다. 예를 들어 다음과 같은 경우를 예로 들 수 있습니다.`MC_resourceGroup_aksABCD_westus`

    ```azurecli
    az identity create -g <agent-pool-resource-group> -n <identity-name>
    ```

1. 아래의 역할 할당 명령에 대 `principalId` 한 우리는 새로 만든된 ID에 대 한 얻을 필요가:

    ```azurecli
    az identity show -g <resourcegroup> -n <identity-name>
    ```

1. 응용 프로그램 `Contributor` 게이트웨이에 대한 ID 액세스 권한을 부여합니다. 이를 위해서는 다음과 같이 보이는 응용 프로그램 게이트웨이의 ID가 필요합니다.`/subscriptions/A/resourceGroups/B/providers/Microsoft.Network/applicationGateways/C`

    다음을 통해 구독에서 응용 프로그램 게이트웨이 아이디 목록을 가져옵니다.`az network application-gateway list --query '[].id'`

    ```azurecli
    az role assignment create \
        --role Contributor \
        --assignee <principalId> \
        --scope <App-Gateway-ID>
    ```

1. 응용 프로그램 `Reader` 게이트웨이 리소스 그룹에 대한 ID 액세스 권한을 부여합니다. 리소스 그룹 ID는 다음과 `/subscriptions/A/resourceGroups/B`같습니다. 다음을 통해 모든 리소스 그룹을 얻을 수 있습니다.`az group list --query '[].id'`

    ```azurecli
    az role assignment create \
        --role Reader \
        --assignee <principalId> \
        --scope <App-Gateway-Resource-Group-ID>
    ```

## <a name="using-a-service-principal"></a>서비스 주체 사용
또한 Kubernetes 비밀을 통해 ARM에 대한 AGIC 액세스를 제공할 수도 있습니다.

1. Active Directory 서비스 보안 주체를 만들고 base64로 인코딩합니다. JSON Blob을 Kubernetes에 저장하려면 base64 인코딩이 필요합니다.

```azurecli
az ad sp create-for-rbac --sdk-auth | base64 -w0
```

2. 파일에 base64 인코딩된 JSON Blob을 `helm-config.yaml` 추가합니다. 자세한 `helm-config.yaml` 내용은 다음 섹션에 있습니다.
```yaml
armAuth:
    type: servicePrincipal
    secretJSON: <Base64-Encoded-Credentials>
```

## <a name="install-ingress-controller-as-a-helm-chart"></a>헬름 차트로 인그레스 컨트롤러 설치
처음 몇 단계에서는 Kubernetes 클러스터에 헬름의 틸러를 설치합니다. [클라우드 쉘을](https://shell.azure.com/) 사용하여 AGIC 헬름 패키지를 설치합니다.

1. 투구 `application-gateway-kubernetes-ingress` 리포지토리를 추가하고 투구 업데이트를 수행합니다.

    ```bash
    helm repo add application-gateway-kubernetes-ingress https://appgwingress.blob.core.windows.net/ingress-azure-helm-package/
    helm repo update
    ```

1. 다운로드 helm-config.yaml, AGIC를 구성합니다:
    ```bash
    wget https://raw.githubusercontent.com/Azure/application-gateway-kubernetes-ingress/master/docs/examples/sample-helm-config.yaml -O helm-config.yaml
    ```
    또는 아래YAML 파일을 복사하십시오. 
    
    ```yaml
    # This file contains the essential configs for the ingress controller helm chart

    # Verbosity level of the App Gateway Ingress Controller
    verbosityLevel: 3
    
    ################################################################################
    # Specify which application gateway the ingress controller will manage
    #
    appgw:
        subscriptionId: <subscriptionId>
        resourceGroup: <resourceGroupName>
        name: <applicationGatewayName>
    
        # Setting appgw.shared to "true" will create an AzureIngressProhibitedTarget CRD.
        # This prohibits AGIC from applying config for any host/path.
        # Use "kubectl get AzureIngressProhibitedTargets" to view and change this.
        shared: false
    
    ################################################################################
    # Specify which kubernetes namespace the ingress controller will watch
    # Default value is "default"
    # Leaving this variable out or setting it to blank or empty string would
    # result in Ingress Controller observing all acessible namespaces.
    #
    # kubernetes:
    #   watchNamespace: <namespace>
    
    ################################################################################
    # Specify the authentication with Azure Resource Manager
    #
    # Two authentication methods are available:
    # - Option 1: AAD-Pod-Identity (https://github.com/Azure/aad-pod-identity)
    armAuth:
        type: aadPodIdentity
        identityResourceID: <identityResourceId>
        identityClientID:  <identityClientId>
    
    ## Alternatively you can use Service Principal credentials
    # armAuth:
    #    type: servicePrincipal
    #    secretJSON: <<Generate this value with: "az ad sp create-for-rbac --sdk-auth | base64 -w0" >>
    
    ################################################################################
    # Specify if the cluster is RBAC enabled or not
    rbac:
        enabled: false # true/false
    
    # Specify aks cluster related information. THIS IS BEING DEPRECATED.
    aksClusterConfiguration:
        apiServerAddress: <aks-api-server-address>
    ```

1. helm-config.yaml을 편집하고 에 대한 `appgw` `armAuth`값을 입력합니다.
    ```bash
    nano helm-config.yaml
    ```

    > [!NOTE] 
    > `<identity-resource-id>` 및 `<identity-client-id>` 이전 섹션에서 설정한 Azure AD ID의 속성입니다. 다음 명령을 실행 하 여이 `az identity show -g <resourcegroup> -n <identity-name>`정보를 `<resourcegroup>` 검색할 수 있습니다.

1. 이전 단계의 `application-gateway-kubernetes-ingress` `helm-config.yaml` 구성으로 헬름 차트 설치

    ```bash
    helm install -f <helm-config.yaml> application-gateway-kubernetes-ingress/ingress-azure
    ```

    또는 한 단계에서 `helm-config.yaml` helm 명령과 Helm 명령을 결합할 수 있습니다.
    ```bash
    helm install ./helm/ingress-azure \
         --name ingress-azure \
         --namespace default \
         --debug \
         --set appgw.name=applicationgatewayABCD \
         --set appgw.resourceGroup=your-resource-group \
         --set appgw.subscriptionId=subscription-uuid \
         --set appgw.shared=false \
         --set armAuth.type=servicePrincipal \
         --set armAuth.secretJSON=$(az ad sp create-for-rbac --sdk-auth | base64 -w0) \
         --set rbac.enabled=true \
         --set verbosityLevel=3 \
         --set kubernetes.watchNamespace=default \
         --set aksClusterConfiguration.apiServerAddress=aks-abcdefg.hcp.westus2.azmk8s.io
    ```

1. 새로 만든 포드의 로그를 확인하여 제대로 시작되었는지 확인합니다.

이 [방법 가이드를](ingress-controller-expose-service-over-http-https.md) 참조하여 Azure 응용 프로그램 게이트웨이를 사용하여 HTTP 또는 HTTPS를 통해 인터넷에 AKS 서비스를 노출하는 방법을 이해합니다.



## <a name="multi-cluster--shared-application-gateway"></a>멀티 클러스터 / 공유 애플리케이션 게이트웨이
기본적으로 AGIC는 연결된 응용 프로그램 게이트웨이의 전체 소유권을 가정합니다. AGIC 버전 0.8.0 이상은 단일 응용 프로그램 게이트웨이를 다른 Azure 구성 요소와 공유할 수 있습니다. 예를 들어, AKS 클러스터뿐만 아니라 가상 머신 스케일 집합에서 호스팅되는 앱에 동일한 응용 프로그램 게이트웨이를 사용할 수 있습니다.

이 설정을 활성화하기 전에 __응용 프로그램 게이트웨이의 구성을 백업하십시오.__
  1. Azure 포털을 사용하여 인스턴스로 이동 [Azure portal](https://portal.azure.com/) `Application Gateway`
  2. 클릭한 시간 `Export template``Download`

다운로드 한 zip 파일에는 응용 프로그램 게이트웨이를 복원하는 데 사용할 수있는 JSON 템플릿, bash 및 PowerShell 스크립트가 있습니다.

### <a name="example-scenario"></a>예제 시나리오
두 웹 사이트의 트래픽을 관리하는 가상의 응용 프로그램 게이트웨이를 살펴보겠습니다.
  - `dev.contoso.com`- 응용 프로그램 게이트웨이 및 AGIC를 사용하여 새로운 AKS에서 호스팅
  - `prod.contoso.com`- Azure [가상 시스템 규모 집합에서](https://azure.microsoft.com/services/virtual-machine-scale-sets/) 호스팅됨

기본 설정을 사용하면 AGIC는 가리키는 응용 프로그램 게이트웨이의 100% 소유권을 가정합니다. AGIC는 앱 게이트웨이의 모든 구성을 덮어씁니다. Kubernetes 수신에서 정의하지 `prod.contoso.com` 않고 응용 프로그램 게이트웨이에서 수동으로 수신기를 만들면 AGIC는 몇 초 `prod.contoso.com` 내에 구성을 삭제합니다.

AGIC를 설치하고 가상 `prod.contoso.com` 머신 스케일 세트 머신에서 서비스를 제공하려면 AGIC를 `dev.contoso.com` 구성에만 제한해야 합니다. 이것은 다음 [CRD를](https://kubernetes.io/docs/concepts/extend-kubernetes/api-extension/custom-resources/)인스턴스화하여 촉진됩니다.

```bash
cat <<EOF | kubectl apply -f -
apiVersion: "appgw.ingress.k8s.io/v1"
kind: AzureIngressProhibitedTarget
metadata:
  name: prod-contoso-com
spec:
  hostname: prod.contoso.com
EOF
```

위의 명령은 개체를 `AzureIngressProhibitedTarget` 만듭니다. 이렇게 하면 AGIC(버전 0.8.0 이상)에 대한 `prod.contoso.com` 응용 프로그램 게이트웨이 구성의 존재를 인식하고 해당 호스트 이름과 관련된 구성을 변경하지 않도록 명시적으로 지시합니다.


### <a name="enable-with-new-agic-installation"></a>새로운 AGIC 설치로 사용 가능
AGIC(버전 0.8.0 이상)를 응용 프로그램 게이트웨이 구성의 하위 `helm-config.yaml` 집합으로 제한하려면 템플릿을 수정합니다.
섹션 `appgw:` 아래에 키를 추가하고 `shared` `true`로 설정합니다.

```yaml
appgw:
    subscriptionId: <subscriptionId>    # existing field
    resourceGroup: <resourceGroupName>  # existing field
    name: <applicationGatewayName>      # existing field
    shared: true                        # <<<<< Add this field to enable shared Application Gateway >>>>>
```

투구 변경 사항 적용:
  1. CRD가 `AzureIngressProhibitedTarget` 다음과 함께 설치되었는지 확인합니다.
      ```bash
      kubectl apply -f https://raw.githubusercontent.com/Azure/application-gateway-kubernetes-ingress/ae695ef9bd05c8b708cedf6ff545595d0b7022dc/crds/AzureIngressProhibitedTarget.yaml
      ```
  2. 투구 업데이트:
      ```bash
      helm upgrade \
          --recreate-pods \
          -f helm-config.yaml \
          ingress-azure application-gateway-kubernetes-ingress/ingress-azure
      ```

결과적으로 AKS는 `AzureIngressProhibitedTarget` 다음과 같은 새 인스턴스를 갖게 됩니다. `prohibit-all-targets`
```bash
kubectl get AzureIngressProhibitedTargets prohibit-all-targets -o yaml
```

이름에서 `prohibit-all-targets`알 수 있듯이 개체는 AGIC가 모든 호스트 및 경로에 대한 구성을 변경할 *수* 없습니다.
Helm `appgw.shared=true` 설치는 AGIC를 배포하지만 응용 프로그램 게이트웨이를 변경하지는 않습니다.


### <a name="broaden-permissions"></a>권한 확대
헬름과 `appgw.shared=true` 기본 `prohibit-all-targets` 은 AGIC가 어떤 구성을 적용하지 못하도록 차단합니다.

다음을 통해 AGIC 권한 확대
1. 특정 설정으로 새 `AzureIngressProhibitedTarget` 만들기:
    ```bash
    cat <<EOF | kubectl apply -f -
    apiVersion: "appgw.ingress.k8s.io/v1"
    kind: AzureIngressProhibitedTarget
    metadata:
      name: your-custom-prohibitions
    spec:
      hostname: your.own-hostname.com
    EOF
    ```

2. 사용자 지정 금지를 만든 후에만 너무 광범위한 기본 금지를 삭제할 수 있습니다.

    ```bash
    kubectl delete AzureIngressProhibitedTarget prohibit-all-targets
    ```

### <a name="enable-for-an-existing-agic-installation"></a>기존 AGIC 설치 사용
클러스터에 이미 작업 중인 AKS, 응용 프로그램 게이트웨이 및 구성된 AGIC가 있다고 가정해 보겠습니다. 우리는 에 대한 `prod.contosor.com` Ingress을 가지고 성공적으로 AKS에서 트래픽을 제공하고 있습니다. 기존 응용 `staging.contoso.com` 프로그램 게이트웨이에 추가하되 [VM에서](https://azure.microsoft.com/services/virtual-machines/)호스트해야 합니다. 기존 응용 프로그램 게이트웨이를 다시 사용하고 에 대한 `staging.contoso.com`수신기 및 백 엔드 풀을 수동으로 구성할 예정입니다. 그러나 [포털,](https://portal.azure.com) [ARM API](https://docs.microsoft.com/rest/api/resources/) 또는 [테라폼을](https://www.terraform.io/)통해 응용 프로그램 게이트웨이 구성을 수동으로 조정하면 AGIC의 전체 소유권 가정과 충돌합니다. 변경 내용을 적용한 직후 AGIC는 변경 내용을 덮어쓰거나 삭제합니다.

AGIC가 구성의 하위 집합을 변경하는 것을 금지할 수 있습니다.

1. `AzureIngressProhibitedTarget` 개체 만들기:
    ```bash
    cat <<EOF | kubectl apply -f -
    apiVersion: "appgw.ingress.k8s.io/v1"
    kind: AzureIngressProhibitedTarget
    metadata:
      name: manually-configured-staging-environment
    spec:
      hostname: staging.contoso.com
    EOF
    ```

2. 새로 만든 객체 보기:
    ```bash
    kubectl get AzureIngressProhibitedTargets
    ```

3. 포털을 통해 응용 프로그램 게이트웨이 구성 수정 - 수신기, 라우팅 규칙, 백 엔드 등을 추가합니다. `manually-configured-staging-environment`() 만든 새 개체는 AGIC가 와 관련된 응용 `staging.contoso.com`프로그램 게이트웨이 구성을 덮어쓰는 것을 금지합니다.
