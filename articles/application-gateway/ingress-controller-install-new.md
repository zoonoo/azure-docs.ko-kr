---
title: 새 응용 프로그램 게이트웨이를 사용 하 고 있는 응용 프로그램 컨트롤러 만들기
description: 이 문서에서는 새 응용 프로그램 게이트웨이를 사용하여 응용 프로그램 게이트웨이 인서스 컨트롤러를 배포하는 방법에 대한 정보를 제공합니다.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: a0bc6aef1becd53217be0eeb8c865b5c78a5d69f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80239457"
---
# <a name="how-to-install-an-application-gateway-ingress-controller-agic-using-a-new-application-gateway"></a>새 응용 프로그램 게이트웨이를 사용하여 AGIC(응용 프로그램 게이트웨이 인그레스 컨트롤러) 설치 하는 방법

아래 지침은 응용 프로그램 게이트웨이 인그레스 컨트롤러(AGIC)가 기존 구성 요소가 없는 환경에 설치된다고 가정합니다.

## <a name="required-command-line-tools"></a>필수 명령줄 도구

아래의 모든 명령줄 작업에 [Azure Cloud Shell을](https://shell.azure.com/) 사용하는 것이 좋습니다. shell.azure.com 또는 링크를 클릭하여 셸을 시작합니다.

[![포함 시작](https://shell.azure.com/images/launchcloudshell.png "Azure Cloud Shell 시작")](https://shell.azure.com)

또는 다음 아이콘을 사용하여 Azure 포털에서 클라우드 셸을 시작합니다.

![포털 시작](./media/application-gateway-ingress-controller-install-new/portal-launch-icon.png)

[Azure 클라우드 셸에는](https://shell.azure.com/) 이미 필요한 모든 도구가 있습니다. 다른 환경을 사용하도록 선택한 경우 다음 명령줄 도구가 설치되어 있는지 확인하십시오.

* `az`- Azure CLI: [설치 지침](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
* `kubectl`- Kubernetes 명령줄 도구 : [설치 지침](https://kubernetes.io/docs/tasks/tools/install-kubectl)
* `helm`- Kubernetes 패키지 관리자 : [설치 지침](https://github.com/helm/helm/releases/latest)
* `jq`- 명령줄 JSON 프로세서 : [설치 지침](https://stedolan.github.io/jq/download/)


## <a name="create-an-identity"></a>ID 만들기

아래 단계에 따라 AAD(Azure Active Directory) [서비스 주체 개체를](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals#service-principal-object)만듭니다. `appId`에서 `password`및 `objectId` 값을 기록하십시오 .

1. AD 서비스 주체[만들기(RBAC에 대해 자세히 알아보기):](https://docs.microsoft.com/azure/role-based-access-control/overview)
    ```azurecli
    az ad sp create-for-rbac --skip-assignment -o json > auth.json
    appId=$(jq -r ".appId" auth.json)
    password=$(jq -r ".password" auth.json)
    ```
    JSON 출력의 `appId` 및 `password` 값은 다음 단계에서 사용됩니다.


1. 이전 `appId` 명령의 출력에서 새 서비스 `objectId` 주체를 가져옵니다.
    ```azurecli
    objectId=$(az ad sp show --id $appId --query "objectId" -o tsv)
    ```
    이 명령의 출력은 `objectId`아래 Azure 리소스 관리자 템플릿에서 사용할 것입니다.

1. 나중에 Azure 리소스 관리자 템플릿 배포에 사용할 매개 변수 파일을 만듭니다.
    ```bash
    cat <<EOF > parameters.json
    {
      "aksServicePrincipalAppId": { "value": "$appId" },
      "aksServicePrincipalClientSecret": { "value": "$password" },
      "aksServicePrincipalObjectId": { "value": "$objectId" },
      "aksEnableRBAC": { "value": false }
    }
    EOF
    ```
    **RBAC** 지원 클러스터를 배포하려면 `aksEnabledRBAC` 필드를`true`

## <a name="deploy-components"></a>구성 요소 배포
이 단계에서는 구독에 다음 구성 요소를 추가합니다.

- [Azure Kubernetes 서비스](https://docs.microsoft.com/azure/aks/intro-kubernetes)
- [애플리케이션 게이트웨이](https://docs.microsoft.com/azure/application-gateway/overview) v2
- [서브넷 2개가 있는](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) [가상 네트워크](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview)
- [공용 IP 주소](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address)
- [관리되는 ID](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)- [AAD 포드 ID에서](https://github.com/Azure/aad-pod-identity/blob/master/README.md) 사용할 수 있습니다.

1. Azure 리소스 관리자 템플릿을 다운로드하고 필요에 따라 템플릿을 수정합니다.
    ```bash
    wget https://raw.githubusercontent.com/Azure/application-gateway-kubernetes-ingress/master/deploy/azuredeploy.json -O template.json
    ```

1. 을 사용하여 `az cli`Azure 리소스 관리자 템플릿을 배포합니다. 이 경우 최대 5분이 소요될 수 있습니다.
    ```azurecli
    resourceGroupName="MyResourceGroup"
    location="westus2"
    deploymentName="ingress-appgw"

    # create a resource group
    az group create -n $resourceGroupName -l $location

    # modify the template as needed
    az group deployment create \
            -g $resourceGroupName \
            -n $deploymentName \
            --template-file template.json \
            --parameters parameters.json
    ```

1. 배포가 완료되면 배포 출력을 라는 `deployment-outputs.json`파일로 다운로드합니다.
    ```azurecli
    az group deployment show -g $resourceGroupName -n $deploymentName --query "properties.outputs" -o json > deployment-outputs.json
    ```

## <a name="set-up-application-gateway-ingress-controller"></a>애플리케이션 게이트웨이 인그레스 컨트롤러 설정

이전 섹션의 지침과 함께 새 AKS 클러스터와 응용 프로그램 게이트웨이를 만들고 구성했습니다. 이제 샘플 앱과 침투 컨트롤러를 새로운 Kubernetes 인프라에 배포할 준비가 되었습니다.

### <a name="setup-kubernetes-credentials"></a>Kubernetes 자격 증명 설정
다음 단계를 위해 새로운 Kubernetes 클러스터에 연결하는 데 사용할 설정 [kubectl](https://kubectl.docs.kubernetes.io/) 명령이 필요합니다. [클라우드 셸이](https://shell.azure.com/) 이미 설치되었습니다. `kubectl` CLI를 `az` 사용하여 Kubernetes에 대한 자격 증명을 얻습니다.

새로 배포된 AKS에 대한 자격 증명 을[가져옵니다(자세히 보기):](https://docs.microsoft.com/azure/aks/kubernetes-walkthrough#connect-to-the-cluster)
```azurecli
# use the deployment-outputs.json created after deployment to get the cluster name and resource group name
aksClusterName=$(jq -r ".aksClusterName.value" deployment-outputs.json)
resourceGroupName=$(jq -r ".resourceGroupName.value" deployment-outputs.json)

az aks get-credentials --resource-group $resourceGroupName --name $aksClusterName
```

### <a name="install-aad-pod-identity"></a>AAD 포드 ID 설치
  Azure Active Directory 포드 ID는 [ARM(Azure 리소스 관리자)에](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)대한 토큰 기반 액세스를 제공합니다.

  [AAD 포드 ID는](https://github.com/Azure/aad-pod-identity) Kubernetes 클러스터에 다음 구성 요소를 추가합니다.
   * 쿠버네츠 [CRDs](https://kubernetes.io/docs/tasks/access-kubernetes-api/custom-resources/custom-resource-definitions/): `AzureIdentity`, `AzureAssignedIdentity``AzureIdentityBinding`
   * [MIC(Managed Identity Controller)](https://github.com/Azure/aad-pod-identity#managed-identity-controllermic) 구성 요소
   * [NMI(Node Managed Identity)](https://github.com/Azure/aad-pod-identity#node-managed-identitynmi) 구성 요소


클러스터에 AAD 포드 ID를 설치하려면 다음을 수행하십시오.

   - *RBAC 사용 가능* AKS 클러스터

     ```bash
     kubectl create -f https://raw.githubusercontent.com/Azure/aad-pod-identity/master/deploy/infra/deployment-rbac.yaml
     ```

   - *RBAC 사용 안 함* AKS 클러스터

     ```bash
     kubectl create -f https://raw.githubusercontent.com/Azure/aad-pod-identity/master/deploy/infra/deployment.yaml
     ```

### <a name="install-helm"></a>Helm 설치
[헬름은](https://docs.microsoft.com/azure/aks/kubernetes-helm) 쿠베네츠의 패키지 매니저입니다. 우리는 패키지를 설치하는 데 `application-gateway-kubernetes-ingress` 활용합니다 :

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

### <a name="install-ingress-controller-helm-chart"></a>수신 컨트롤러 Helm 차트 설치

1. 위에서 `deployment-outputs.json` 만든 파일을 사용하고 다음 변수를 만듭니다.
    ```bash
    applicationGatewayName=$(jq -r ".applicationGatewayName.value" deployment-outputs.json)
    resourceGroupName=$(jq -r ".resourceGroupName.value" deployment-outputs.json)
    subscriptionId=$(jq -r ".subscriptionId.value" deployment-outputs.json)
    identityClientId=$(jq -r ".identityClientId.value" deployment-outputs.json)
    identityResourceId=$(jq -r ".identityResourceId.value" deployment-outputs.json)
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
    #    secretJSON: <<Generate this value with: "az ad sp create-for-rbac --subscription <subscription-uuid> --sdk-auth | base64 -w0" >>
    
    ################################################################################
    # Specify if the cluster is RBAC enabled or not
    rbac:
        enabled: false # true/false
    
    # Specify aks cluster related information. THIS IS BEING DEPRECATED.
    aksClusterConfiguration:
        apiServerAddress: <aks-api-server-address>
    ```

1. 새로 다운로드한 helm-config.yaml을 편집하고 `appgw` 섹션을 `armAuth`작성하고 .
    ```bash
    sed -i "s|<subscriptionId>|${subscriptionId}|g" helm-config.yaml
    sed -i "s|<resourceGroupName>|${resourceGroupName}|g" helm-config.yaml
    sed -i "s|<applicationGatewayName>|${applicationGatewayName}|g" helm-config.yaml
    sed -i "s|<identityResourceId>|${identityResourceId}|g" helm-config.yaml
    sed -i "s|<identityClientId>|${identityClientId}|g" helm-config.yaml

    # You can further modify the helm config to enable/disable features
    nano helm-config.yaml
    ```

   값
     - `verbosityLevel`: AGIC 로깅 인프라의 세부 수준을 설정합니다. 가능한 값은 [로깅 수준](https://github.com/Azure/application-gateway-kubernetes-ingress/blob/463a87213bbc3106af6fce0f4023477216d2ad78/docs/troubleshooting.md#logging-levels)을 참조하세요.
     - `appgw.subscriptionId`: 응용 프로그램 게이트웨이가 있는 Azure 구독 ID입니다. 예: `a123b234-a3b4-557d-b2df-a0bc12de1234`
     - `appgw.resourceGroup`: 응용 프로그램 게이트웨이가 만들어진 Azure 리소스 그룹의 이름입니다. 예: `app-gw-resource-group`
     - `appgw.name`: 응용 프로그램 게이트웨이의 이름입니다. 예: `applicationgatewayd0f0`
     - `appgw.shared`: 이 부울 플래그는 `false`기본값으로 설정해야 합니다. 공유 `true` 응용 프로그램 [게이트웨이가](https://github.com/Azure/application-gateway-kubernetes-ingress/blob/072626cb4e37f7b7a1b0c4578c38d1eadc3e8701/docs/setup/install-existing.md#multi-cluster--shared-app-gateway)필요한 지 설정합니다.
     - `kubernetes.watchNamespace`: AGIC가 확인해야 할 이름 공간을 지정합니다. 이 값은 단일 문자열 값또는 쉼표로 구분된 네임스페이스 목록일 수 있습니다.
    - `armAuth.type`: `aadPodIdentity` 또는`servicePrincipal`
    - `armAuth.identityResourceID`: Azure 관리 ID의 리소스 ID
    - `armAuth.identityClientId`: ID의 클라이언트 ID입니다. ID에 대한 자세한 내용은 아래를 참조하십시오.
    - `armAuth.secretJSON`: 서비스 주체 보안 원칙 을 `armAuth.type` 선택한 경우에만 `servicePrincipal`필요합니다(설정된 경우). 


   > [!NOTE]
   > 및 ID 만들기 단계 중에 생성된 값이며 다음 명령을 사용하여 다시 가져올 수 있습니다. [Create an Identity](https://github.com/Azure/application-gateway-kubernetes-ingress/blob/072626cb4e37f7b7a1b0c4578c38d1eadc3e8701/docs/setup/install-new.md#create-an-identity) `identityResourceID` `identityClientID`
   > ```azurecli
   > az identity show -g <resource-group> -n <identity-name>
   > ```
   > `<resource-group>`위의 명령에서 응용 프로그램 게이트웨이의 리소스 그룹입니다. `<identity-name>`은 생성된 ID의 이름입니다. 지정된 구독의 모든 ID는 다음을 사용하여 나열할 수 있습니다.`az identity list`


1. Application Gateway 수신 컨트롤러 패키지 설치

    ```bash
    helm install -f helm-config.yaml application-gateway-kubernetes-ingress/ingress-azure
    ```

## <a name="install-a-sample-app"></a>샘플 앱 설치
이제 응용 프로그램 게이트웨이, AKS 및 AGIC가 설치되었으므로 [Azure Cloud Shell을](https://shell.azure.com/)통해 샘플 앱을 설치할 수 있습니다.

```yaml
cat <<EOF | kubectl apply -f -
apiVersion: v1
kind: Pod
metadata:
  name: aspnetapp
  labels:
    app: aspnetapp
spec:
  containers:
  - image: "mcr.microsoft.com/dotnet/core/samples:aspnetapp"
    name: aspnetapp-image
    ports:
    - containerPort: 80
      protocol: TCP

---

apiVersion: v1
kind: Service
metadata:
  name: aspnetapp
spec:
  selector:
    app: aspnetapp
  ports:
  - protocol: TCP
    port: 80
    targetPort: 80

---

apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: aspnetapp
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
spec:
  rules:
  - http:
      paths:
      - path: /
        backend:
          serviceName: aspnetapp
          servicePort: 80
EOF
```

또는 다음을 수행할 수 있습니다.

* 위의 YAML 파일을 다운로드하십시오.

```bash
curl https://raw.githubusercontent.com/Azure/application-gateway-kubernetes-ingress/master/docs/examples/aspnetapp.yaml -o aspnetapp.yaml
```

* YAML 파일을 적용합니다.

```bash
kubectl apply -f aspnetapp.yaml
```


## <a name="other-examples"></a>기타 예
이 [방법 가이드에는](ingress-controller-expose-service-over-http-https.md) HTTP 또는 HTTPS를 통해 응용 프로그램 게이트웨이를 사용하여 인터넷에 AKS 서비스를 노출하는 방법에 대한 자세한 예제가 포함되어 있습니다.
