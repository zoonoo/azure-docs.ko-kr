---
title: Azure Application Gateway 메트릭을 사용하여 AKS Pod 자동 크기 조정
description: 이 문서에서는 Application Gateway 메트릭 및 Azure Kubernetes 메트릭 어댑터를 사용하여 AKS 백 엔드 Pod의 크기를 조정하는 방법에 대한 지침을 제공합니다.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: how-to
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: a8f015085baa8fffa6f208e9d8dd749e397c76c3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "93397436"
---
# <a name="autoscale-your-aks-pods-using-application-gateway-metrics-beta"></a>Application Gateway 메트릭(베타)을 사용하여 AKS Pod 자동 크기 조정

들어오는 트래픽이 증가함에 따라 수요를 기반으로 애플리케이션을 스케일 업하는 것이 중요해집니다.

다음 자습서에서는 Application Gateway의 `AvgRequestCountPerHealthyHost` 메트릭을 사용하여 애플리케이션을 스케일 업하는 방법을 설명합니다. `AvgRequestCountPerHealthyHost`는 특정 백 엔드 풀과 백 엔드 HTTP 설정 조합으로 전송된 평균 요청을 측정합니다.

다음 두 가지 구성 요소를 사용할 것입니다.

* [`Azure Kubernetes Metric Adapter`](https://github.com/Azure/azure-k8s-metrics-adapter) - 메트릭 어댑터를 사용하여 메트릭 서버를 통해 Application Gateway 메트릭을 노출합니다. Azure Kubernetes 메트릭 어댑터는 Application Gateway 수신 컨트롤러와 유사한 Azure의 오픈 소스 프로젝트입니다. 
* [`Horizontal Pod Autoscaler`](../aks/concepts-scale.md#horizontal-pod-autoscaler) - HPA를 사용하여 Application Gateway 메트릭을 사용하고 크기 조정을 위한 배포를 대상으로 합니다.

## <a name="setting-up-azure-kubernetes-metric-adapter"></a>Azure Kubernetes 메트릭 어댑터 설정

1. 먼저 Azure AAD 서비스 주체를 만들고 Application Gateway의 리소스 그룹에 대한 `Monitoring Reader` 액세스 권한을 할당합니다. 

    ```azurecli
        applicationGatewayGroupName="<application-gateway-group-id>"
        applicationGatewayGroupId=$(az group show -g $applicationGatewayGroupName -o tsv --query "id")
        az ad sp create-for-rbac -n "azure-k8s-metric-adapter-sp" --role "Monitoring Reader" --scopes applicationGatewayGroupId
    ```

1. 이제 위에서 만든 AAD 서비스 주체를 사용하여 [`Azure Kubernetes Metric Adapter`](https://github.com/Azure/azure-k8s-metrics-adapter)를 배포합니다.

    ```bash
    kubectl create namespace custom-metrics
    # use values from service principal created above to create secret
    kubectl create secret generic azure-k8s-metrics-adapter -n custom-metrics \
        --from-literal=azure-tenant-id=<tenantid> \
        --from-literal=azure-client-id=<clientid> \
        --from-literal=azure-client-secret=<secret>
    kubectl apply -f kubectl apply -f https://raw.githubusercontent.com/Azure/azure-k8s-metrics-adapter/master/deploy/adapter.yaml -n custom-metrics
    ```

1. 이름이 `appgw-request-count-metric`인 `ExternalMetric` 리소스를 만듭니다. 이 리소스는 `myResourceGroup` 리소스 그룹의 `myApplicationGateway` 리소스에 대한 `AvgRequestCountPerHealthyHost` 메트릭을 노출하도록 메트릭 어댑터에 지시합니다. `filter` 필드를 사용하여 Application Gateway에서 특정 백 엔드 풀 및 백 엔드 HTTP 설정을 대상으로 지정할 수 있습니다.

    ```yaml
    apiVersion: azure.com/v1alpha2
    kind: ExternalMetric
    metadata:
    name: appgw-request-count-metric
    spec:
        type: azuremonitor
        azure:
            resourceGroup: myResourceGroup # replace with your application gateway's resource group name
            resourceName: myApplicationGateway # replace with your application gateway's name
            resourceProviderNamespace: Microsoft.Network
            resourceType: applicationGateways
        metric:
            metricName: AvgRequestCountPerHealthyHost
            aggregation: Average
            filter: BackendSettingsPool eq '<backend-pool-name>~<backend-http-setting-name>' # optional
    ```

이제 메트릭 서버에 요청하여 새 메트릭이 노출되는지 확인할 수 있습니다.
```bash
kubectl get --raw "/apis/external.metrics.k8s.io/v1beta1/namespaces/default/appgw-request-count-metric"
# Sample Output
# {
#   "kind": "ExternalMetricValueList",
#   "apiVersion": "external.metrics.k8s.io/v1beta1",
#   "metadata":
#     {
#       "selfLink": "/apis/external.metrics.k8s.io/v1beta1/namespaces/default/appgw-request-count-metric",
#     },
#   "items":
#     [
#       {
#         "metricName": "appgw-request-count-metric",
#         "metricLabels": null,
#         "timestamp": "2019-11-05T00:18:51Z",
#         "value": "30",
#       },
#     ],
# }
```

## <a name="using-the-new-metric-to-scale-up-the-deployment"></a>새 메트릭을 사용하여 배포 스케일 업

메트릭 서버를 통해 `appgw-request-count-metric`을 노출할 수 있게 되면 [`Horizontal Pod Autoscaler`](../aks/concepts-scale.md#horizontal-pod-autoscaler)를 사용하여 대상 배포를 스케일 업할 준비가 된 것입니다.

다음 예에서는 샘플 배포 `aspnet`을 대상으로 합니다. `appgw-request-count-metric`이 Pod당 200개 초과인 경우 최대 `10`개까지 Pod를 스케일 업합니다.

대상 배포 이름을 바꾸고 다음 자동 크기 조정 구성을 적용합니다.
```yaml
apiVersion: autoscaling/v2beta1
kind: HorizontalPodAutoscaler
metadata:
  name: deployment-scaler
spec:
  scaleTargetRef:
    apiVersion: extensions/v1beta1
    kind: Deployment
    name: aspnet # replace with your deployment's name
  minReplicas: 1
  maxReplicas: 10
  metrics:
  - type: External
    external:
      metricName: appgw-request-count-metric
      targetAverageValue: 200
```

Apache Bench와 같은 부하 테스트 도구를 사용하여 설정을 테스트합니다.
```bash
ab -n10000 http://<applicaiton-gateway-ip-address>/
```

## <a name="next-steps"></a>다음 단계
- [**수신 컨트롤러 문제 해결**](ingress-controller-troubleshoot.md): 수신 컨트롤러 관련 문제를 해결합니다.