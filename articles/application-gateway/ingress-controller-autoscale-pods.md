---
title: Azure 응용 프로그램 게이트웨이 메트릭을 사용 하 고 자동 크기 조정 AKS 포드
description: 이 문서에서는 응용 프로그램 게이트웨이 메트릭 및 Azure Kubernetes 메트릭 어댑터를 사용하여 AKS 백 엔드 포드를 확장하는 방법에 대한 지침을 제공합니다.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: 1169ed0e9a2b970ee0e30d73ea20c87001b62786
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80239445"
---
# <a name="autoscale-your-aks-pods-using-application-gateway-metrics-beta"></a>응용 프로그램 게이트웨이 메트릭(베타)을 사용하여 AKS 포드자동 크기 조정

들어오는 트래픽이 증가함에 따라 수요에 따라 응용 프로그램을 확장하는 것이 중요합니다.

다음 자습서에서는 응용 프로그램 게이트웨이의 `AvgRequestCountPerHealthyHost` 메트릭을 사용하여 응용 프로그램을 확장하는 방법을 설명합니다. `AvgRequestCountPerHealthyHost`은 특정 백 엔드 풀및 백 엔드 HTTP 설정 조합으로 전송된 평균 요청을 측정합니다.

다음 두 가지 구성 요소를 사용할 예정입니다.

* [`Azure Kubernetes Metric Adapter`](https://github.com/Azure/azure-k8s-metrics-adapter)- 메트릭 어댑터를 사용하여 메트릭 서버를 통해 응용 프로그램 게이트웨이 메트릭을 노출합니다. Azure Kubernetes 메트릭 어댑터는 응용 프로그램 게이트웨이 침투 컨트롤러와 유사한 Azure 에서 오픈 소스 프로젝트입니다. 
* [`Horizontal Pod Autoscaler`](https://docs.microsoft.com/azure/aks/concepts-scale#horizontal-pod-autoscaler)- HPA를 사용하여 애플리케이션 게이트웨이 메트릭을 사용하고 확장을 위한 배포를 대상으로 합니다.

## <a name="setting-up-azure-kubernetes-metric-adapter"></a>Azure Kubernetes 메트릭 어댑터 설정

1. 먼저 Azure AAD 서비스 주체를 만들고 `Monitoring Reader` 응용 프로그램 게이트웨이의 리소스 그룹을 통해 액세스 권한을 할당합니다. 

    ```azurecli
        applicationGatewayGroupName="<application-gateway-group-id>"
        applicationGatewayGroupId=$(az group show -g $applicationGatewayGroupName -o tsv --query "id")
        az ad sp create-for-rbac -n "azure-k8s-metric-adapter-sp" --role "Monitoring Reader" --scopes applicationGatewayGroupId
    ```

1. 이제 위에서 만든 [`Azure Kubernetes Metric Adapter`](https://github.com/Azure/azure-k8s-metrics-adapter) AAD 서비스 주체를 사용하여 배포합니다.

    ```bash
    kubectl create namespace custom-metrics
    # use values from service principle created above to create secret
    kubectl create secret generic azure-k8s-metrics-adapter -n custom-metrics \
        --from-literal=azure-tenant-id=<tenantid> \
        --from-literal=azure-client-id=<clientid> \
        --from-literal=azure-client-secret=<secret>
    kubectl apply -f kubectl apply -f https://raw.githubusercontent.com/Azure/azure-k8s-metrics-adapter/master/deploy/adapter.yaml -n custom-metrics
    ```

1. 우리는 이름으로 `appgw-request-count-metric` `ExternalMetric` 자원을 만들 것입니다. 이 리소스는 메트릭 어댑터에 `AvgRequestCountPerHealthyHost` `myApplicationGateway` `myResourceGroup` 리소스 그룹의 리소스에 대한 메트릭을 노출하도록 지시합니다. `filter` 필드를 사용하여 응용 프로그램 게이트웨이에서 특정 백 엔드 풀 및 백 엔드 HTTP 설정을 대상으로 지정할 수 있습니다.

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

이제 메트릭 서버에 요청을 하여 새 메트릭이 노출되고 있는지 확인할 수 있습니다.
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

## <a name="using-the-new-metric-to-scale-up-the-deployment"></a>새 메트릭을 사용하여 배포 확장

메트릭 서버를 통해 `appgw-request-count-metric` 노출할 수 있게 되면 [`Horizontal Pod Autoscaler`](https://docs.microsoft.com/azure/aks/concepts-scale#horizontal-pod-autoscaler) 대상 배포를 확장하는 데 사용할 준비가 됩니다.

다음 예제에서는 샘플 배포를 `aspnet`대상으로 합니다. 포드당 200> `appgw-request-count-metric` 최대 `10` 포드까지 확장할 것입니다.

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

아파치 벤치와 같은 부하 테스트 도구를 사용하여 설정을 테스트하십시오.
```bash
ab -n10000 http://<applicaiton-gateway-ip-address>/
```

## <a name="next-steps"></a>다음 단계
- [**문제 해결 인슈어컨트롤러 문제**](ingress-controller-troubleshoot.md): 인그레스 컨트롤러의 문제 해결.