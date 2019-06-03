---
title: Azure Container Instances 컨테이너 그룹
description: 어떻게 다중 컨테이너 그룹 이해 Azure Container Instances에서 작동
services: container-instances
author: dlepow
manager: jeconnoc
ms.service: container-instances
ms.topic: article
ms.date: 03/20/2019
ms.author: danlep
ms.custom: mvc
ms.openlocfilehash: cba57875daf9b570d274ec8c4e9c4146af0dc045
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/27/2019
ms.locfileid: "65072842"
---
# <a name="container-groups-in-azure-container-instances"></a>Azure Container Instances의 컨테이너 그룹

Azure Container Instances의 최상위 리소스는 *컨테이너 그룹*입니다. 이 문서에서는 컨테이너 그룹의 정의와 이들이 지원하는 시나리오 유형에 대해 설명합니다.

## <a name="how-a-container-group-works"></a>컨테이너 그룹 작동 방식

컨테이너 그룹은 같은 호스트 컴퓨터에서 예약되어 있는 컨테이너 컬렉션입니다. 컨테이너 그룹의 컨테이너 수명 주기, 리소스, 로컬 네트워크 및 저장소 볼륨을 공유합니다. 개념상에서 비슷하지만 *pod* 에 [Kubernetes][kubernetes-pod]합니다.

다음 다이어그램은 여러 컨테이너가 포함된 컨테이너 그룹의 예를 보여줍니다.

![컨테이너 그룹 다이어그램][container-groups-example]

이 예제 컨테이너 그룹은 다음과 같습니다.

* 단일 호스트 컴퓨터에서 예약됩니다.
* DNS 이름 레이블이 할당됩니다.
* 1개의 노출 포트가 있는 단일 공용 IP 주소를 노출합니다.
* 두 개의 컨테이너로 구성됩니다. 하나의 컨테이너는 포트 80을 수신하고, 다른 컨테이너는 포트 5000을 수신합니다.
* 볼륨 탑재로 2개의 Azure 파일 공유가 포함되어 있으며, 각 컨테이너는 공유 중 하나를 로컬에서 탑재합니다.

> [!NOTE]
> 다중 컨테이너 그룹에는 현재 Linux 컨테이너로 지원합니다. Windows 컨테이너에 대 한 Azure Container Instances는 단일 인스턴스를 배포만 지원합니다. Windows 컨테이너에 모든 기능을 제공 하는 중, 하는 동안 서비스에서 현재 플랫폼의 차이점을 찾을 수 있습니다 [개요](container-instances-overview.md#linux-and-windows-containers)합니다.

## <a name="deployment"></a>배포

다중 컨테이너 그룹을 배포 하는 두 가지 일반적인 방법은 다음과 같습니다: 사용 된 [Resource Manager 템플릿을] [ resource-manager template] 또는 [YAML 파일][yaml-file]합니다. 추가 Azure 서비스 리소스를 배포 해야 하는 경우 Resource Manager 템플릿을 것이 좋습니다 (예를 들어, 한 [Azure 파일 공유][azure-files]) container instances를 배포 하는 경우. YAML 형식의 더 간결한 특성상 YAML 파일을이 배포에만 컨테이너 인스턴스를 포함 하는 경우 좋습니다.

컨테이너 그룹의 구성, 유지 하기 위해 구성을 내보낼 수 있습니다는 YAML 파일에 Azure CLI 명령을 사용 하 여 [az 컨테이너 내보내기][az-container-export]합니다. 내보내기 "코드로 구성"에 대 한 버전 제어에서 컨테이너 그룹 구성을 저장 하도록 허용 또는 YAML에서 새 구성을 개발할 때 내보낸 파일을 시작점으로 사용할 수 있습니다.

## <a name="resource-allocation"></a>리소스 할당

Azure Container Instances는 Cpu, 메모리 같은 리소스를 할당 하 고 선택적으로 [Gpu] [ gpus] (미리 보기) 컨테이너 그룹에 추가 하 여 합니다 [리소스 요청] [ resource-requests] 그룹의 인스턴스. 각 요청 1 두 인스턴스를 사용 하 여 컨테이너 그룹을 만든 경우 예를 들어 CPU 리소스를 차지 CPU, 다음 컨테이너 그룹 2 개 Cpu를 할당 됩니다.

컨테이너 그룹에 대 한 사용 가능한 최대 리소스에 종속 된 [Azure 지역] [ region-availability] 배포에 사용 합니다.

### <a name="container-resource-requests-and-limits"></a>컨테이너 리소스 요청 및 제한

* 기본적으로 그룹의 container instances를 그룹의 요청 된 리소스를 공유 합니다. 두 개의 그룹에 각 요청 1 인스턴스 CPU, 전체적으로 그룹에 2 개 Cpu에 대 한 액세스. 각 인스턴스 2 개 Cpu를 사용할 수 있습니다 하 고 실행 되는 동안 CPU 리소스에 대 한 인스턴스 경쟁할 수 있습니다.

* 그룹의 인스턴스에 의해 리소스 사용량을 최소화 하려면 필요에 따라 설정 된 [리소스 제한] [ resource-limits] 인스턴스에 대 한 합니다. 두 인스턴스를 사용 하 여 그룹의 1 요청 CPU, 컨테이너 중 하나에 다른 실행 하려면 더 많은 Cpu를 필요할 수 있습니다.

  이 시나리오에서는 0.5 리소스 한도 설정할 수 있습니다 하나 인스턴스 및 두 번째 2 개 Cpu 제한에 대 한 CPU입니다. 이 구성은 0.5로 첫 번째 컨테이너의 리소스 사용량을 제한 CPU, 두 번째 컨테이너를 사용할 수 있는 경우 전체 2 Cpu를 사용할 수 있습니다.

자세한 내용은 참조는 [ResourceRequirements] [ resource-requirements] REST API를 그룹화 하는 컨테이너의 속성입니다.

### <a name="minimum-and-maximum-allocation"></a>최소 및 최대 할당

* 할당을 **최소** 1의 CPU 및 1GB의 메모리로 컨테이너 그룹에 있습니다. 1 보다 작은 그룹 내의 개별 컨테이너 인스턴스를 프로 비전 할 수 CPU 및 메모리 1gb로 줄어듭니다. 

* 에 대 한 합니다 **최대** 컨테이너 그룹에 리소스를 참조 합니다 [리소스 가용성] [ region-availability] 배포 지역에 Azure Container Instances에 대 한 합니다.

## <a name="networking"></a>네트워킹

컨테이너 그룹은 IP 주소와 해당 IP 주소에서 포트 네임스페이스를 공유합니다. 외부 클라이언트가 그룹 내 컨테이너에 도달하게 지원하려면 IP 주소와 컨테이너에서 해당 포트를 공개해야 합니다. 그룹 내의 컨테이너가 포트 네임 스페이스를 공유 하므로 포트 매핑은 지원 되지 않습니다. 컨테이너 그룹 내에서 해당 포트 그룹의 IP 주소에 외부적으로 노출 되지 않습니다 하는 경우에 이들이 노출 하는 포트의 localhost를 통해 서로 연결할 수 있습니다.

선택적으로 컨테이너 그룹 배포를 [Azure 가상 네트워크] [ virtual-network] (미리 보기) 컨테이너를 가상 네트워크의 다른 리소스와 안전 하 게 통신할 수 있도록 합니다.

## <a name="storage"></a>Storage

컨테이너 그룹 내에서 탑재할 외부 볼륨을 지정할 수 있습니다. 해당 볼륨을 그룹의 개별 컨테이너 내에 있는 특정 경로에 매핑할 수 있습니다.

## <a name="common-scenarios"></a>일반적인 시나리오

다중 컨테이너 그룹은 단일 기능 작업을 적은 수의 컨테이너 이미지로 나누려는 경우에 유용합니다. 이러한 이미지는 다른 팀에서 제공될 수 있으며 별도의 리소스 요구 사항을 가질 수 있습니다.

사용 예는 다음과 같습니다.

* 웹 애플리케이션을 처리하는 컨테이너와 원본 제어에서 최신 콘텐츠를 풀링하는 컨테이너
* 애플리케이션 컨테이너 및 로깅 컨테이너. 로깅 컨테이너는 주 애플리케이션에서 출력한 로그 및 메트릭을 수집하여 장기 저장소로 기록합니다.
* 애플리케이션 컨테이너 및 모니터링 컨테이너. 모니터링 컨테이너는 애플리케이션이 올바르게 실행 중이고 응답하고 있는지 확인하기 위해 애플리케이션에 정기적으로 요청을 보내고, 그렇지 않은 경우 경고를 올립니다.
* 프런트 엔드 컨테이너 및 백 엔드 컨테이너. 프런트 엔드 데이터를 검색할 서비스를 실행 하는 백 엔드를 사용 하 여 웹 응용 프로그램을 제공할 수 있습니다. 

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
