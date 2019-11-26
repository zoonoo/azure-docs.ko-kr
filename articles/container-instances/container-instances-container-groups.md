---
title: Introduction to container groups
description: Learn about container groups in Azure Container Instances, a collection of instances that share a lifecycle and resources such as storage and network
ms.topic: article
ms.date: 11/01/2019
ms.custom: mvc
ms.openlocfilehash: 9fbf9fea7da0896ee6c0e248d18e18d52798fbd7
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/25/2019
ms.locfileid: "74482130"
---
# <a name="container-groups-in-azure-container-instances"></a>Azure Container Instances의 컨테이너 그룹

Azure Container Instances의 최상위 리소스는 *컨테이너 그룹*입니다. 이 문서에서는 컨테이너 그룹의 정의와 이들이 지원하는 시나리오 유형에 대해 설명합니다.

## <a name="what-is-a-container-group"></a>What is a container group?

컨테이너 그룹은 같은 호스트 컴퓨터에서 예약되어 있는 컨테이너 컬렉션입니다. The containers in a container group share a lifecycle, resources, local network, and storage volumes. It's similar in concept to a *pod* in [Kubernetes][kubernetes-pod].

다음 다이어그램은 여러 컨테이너가 포함된 컨테이너 그룹의 예를 보여줍니다.

![컨테이너 그룹 다이어그램][container-groups-example]

이 예제 컨테이너 그룹은 다음과 같습니다.

* 단일 호스트 컴퓨터에서 예약됩니다.
* DNS 이름 레이블이 할당됩니다.
* 1개의 노출 포트가 있는 단일 공용 IP 주소를 노출합니다.
* 두 개의 컨테이너로 구성됩니다. 하나의 컨테이너는 포트 80을 수신하고, 다른 컨테이너는 포트 5000을 수신합니다.
* 볼륨 탑재로 2개의 Azure 파일 공유가 포함되어 있으며, 각 컨테이너는 공유 중 하나를 로컬에서 탑재합니다.

> [!NOTE]
> Multi-container groups currently support only Linux containers. For Windows containers, Azure Container Instances only supports deployment of a single instance. While we are working to bring all features to Windows containers, you can find current platform differences in the service [Overview](container-instances-overview.md#linux-and-windows-containers).

## <a name="deployment"></a>배포

Here are two common ways to deploy a multi-container group: use a [Resource Manager template][resource-manager template] or a [YAML file][yaml-file]. A Resource Manager template is recommended when you need to deploy additional Azure service resources (for example, an [Azure Files share][azure-files]) when you deploy the container instances. Due to the YAML format's more concise nature, a YAML file is recommended when your deployment includes only container instances. For details on properties you can set, see the [Resource Manager template reference](/azure/templates/microsoft.containerinstance/containergroups) or [YAML reference](container-instances-reference-yaml.md) documentation.

To preserve a container group's configuration, you can export the configuration to a YAML file by using the Azure CLI command [az container export][az-container-export]. Export allows you to store your container group configurations in version control for "configuration as code." 또는 YAML에서 새 구성을 개발할 때 내보낸 파일을 시작점으로 사용할 수 있습니다.



## <a name="resource-allocation"></a>리소스 할당

Azure Container Instances allocates resources such as CPUs, memory, and optionally [GPUs][gpus] (preview) to a container group by adding the [resource requests][resource-requests] of the instances in the group. Taking CPU resources as an example, if you create a container group with two instances, each requesting 1 CPU, then the container group is allocated 2 CPUs.

### <a name="resource-usage-by-instances"></a>Resource usage by instances

Each container instance is allocated the resources specified in its resource request. However, the resource usage by a container instance in a group depends on how you configure its optional [resource limit][resource-limits] property.

* If you don't specify a resource limit, the instance's maximum resource usage is the same as its resource request.

* If you specify a resource limit for an instance, you can adjust the instance's resource usage for its workload, either reducing or increasing usage relative to the resource request. The maximum resource limit you can set is the total resources allocated to the group.
    
    For example, in a group with two instances requesting 1 CPU, one of your containers might run a workload that requires more CPUs to run than the other.

    In this scenario, you could set a resource limit of 0.5 CPU for one instance, and a limit of 2 CPUs for the second. This configuration limits the first container's resource usage to 0.5 CPU, allowing the second container to use up to the full 2 CPUs if available.

For more information, see the [ResourceRequirements][resource-requirements] property in the container groups REST API.

### <a name="minimum-and-maximum-allocation"></a>Minimum and maximum allocation

* Allocate a **minimum** of 1 CPU and 1 GB of memory to a container group. Individual container instances within a group can be provisioned with less than 1 CPU and 1 GB of memory. 

* For the **maximum** resources in a container group, see the [resource availability][region-availability] for Azure Container Instances in the deployment region.

## <a name="networking"></a>네트워킹

컨테이너 그룹은 IP 주소와 해당 IP 주소에서 포트 네임스페이스를 공유합니다. 외부 클라이언트가 그룹 내 컨테이너에 도달하게 지원하려면 IP 주소와 컨테이너에서 해당 포트를 공개해야 합니다. Because containers within the group share a port namespace, port mapping isn't supported. Containers within a group can reach each other via localhost on the ports that they have exposed, even if those ports aren't exposed externally on the group's IP address.

Optionally deploy container groups into an [Azure virtual network][virtual-network] (preview) to allow containers to communicate securely with other resources in the virtual network.

## <a name="storage"></a>스토리지

컨테이너 그룹 내에서 탑재할 외부 볼륨을 지정할 수 있습니다. 해당 볼륨을 그룹의 개별 컨테이너 내에 있는 특정 경로에 매핑할 수 있습니다.

## <a name="common-scenarios"></a>일반적인 시나리오

다중 컨테이너 그룹은 단일 기능 작업을 적은 수의 컨테이너 이미지로 나누려는 경우에 유용합니다. 이러한 이미지는 다른 팀에서 제공될 수 있으며 별도의 리소스 요구 사항을 가질 수 있습니다.

사용 예는 다음과 같습니다.

* 웹 애플리케이션을 처리하는 컨테이너와 원본 제어에서 최신 콘텐츠를 풀링하는 컨테이너
* 애플리케이션 컨테이너 및 로깅 컨테이너. 로깅 컨테이너는 주 애플리케이션에서 출력한 로그 및 메트릭을 수집하여 장기 스토리지로 기록합니다.
* 애플리케이션 컨테이너 및 모니터링 컨테이너. 모니터링 컨테이너는 애플리케이션이 올바르게 실행 중이고 응답하고 있는지 확인하기 위해 애플리케이션에 정기적으로 요청을 보내고, 그렇지 않은 경우 경고를 올립니다.
* A front-end container and a back-end container. The front end might serve a web application, with the back end running a service to retrieve data. 

## <a name="next-steps"></a>다음 단계

Azure Resource Manager 템플릿을 통해 다중 컨테이너 그룹 배포 방법을 알아봅니다.

> [!div class="nextstepaction"]
> [컨테이너 그룹 배포][resource-manager template]

<!-- IMAGES -->
[container-groups-example]: ./media/container-instances-container-groups/container-groups-example.png

<!-- LINKS - External -->
[dcos-pod]: https://dcos.io/docs/1.10/deploying-services/pods/
[kubernetes-pod]: https://kubernetes.io/docs/concepts/workloads/pods/pod/

<!-- LINKS - Internal -->
[resource-manager template]: container-instances-multi-container-group.md
[yaml-file]: container-instances-multi-container-yaml.md
[region-availability]: container-instances-region-availability.md
[resource-requests]: /rest/api/container-instances/containergroups/createorupdate#resourcerequests
[resource-limits]: /rest/api/container-instances/containergroups/createorupdate#resourcelimits
[resource-requirements]: /rest/api/container-instances/containergroups/createorupdate#resourcerequirements
[azure-files]: container-instances-volume-azure-files.md
[virtual-network]: container-instances-vnet.md
[gpus]: container-instances-gpu.md
[az-container-export]: /cli/azure/container#az-container-export
