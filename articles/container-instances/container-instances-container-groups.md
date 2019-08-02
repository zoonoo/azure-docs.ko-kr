---
title: Azure Container Instances 컨테이너 그룹
description: 다중 컨테이너 그룹의 작동 방식 이해 Azure Container Instances
services: container-instances
author: dlepow
manager: gwallace
ms.service: container-instances
ms.topic: article
ms.date: 03/20/2019
ms.author: danlep
ms.custom: mvc
ms.openlocfilehash: b17004e7821bcac61ca98afdbeaf87644da2a441
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68326045"
---
# <a name="container-groups-in-azure-container-instances"></a>Azure Container Instances의 컨테이너 그룹

Azure Container Instances의 최상위 리소스는 *컨테이너 그룹*입니다. 이 문서에서는 컨테이너 그룹의 정의와 이들이 지원하는 시나리오 유형에 대해 설명합니다.

## <a name="how-a-container-group-works"></a>컨테이너 그룹 작동 방식

컨테이너 그룹은 같은 호스트 컴퓨터에서 예약되어 있는 컨테이너 컬렉션입니다. 컨테이너 그룹의 컨테이너는 수명 주기, 리소스, 로컬 네트워크 및 저장소 볼륨을 공유 합니다. [Kubernetes][kubernetes-pod]의 *pod* 와 비슷합니다.

다음 다이어그램은 여러 컨테이너가 포함된 컨테이너 그룹의 예를 보여줍니다.

![컨테이너 그룹 다이어그램][container-groups-example]

이 예제 컨테이너 그룹은 다음과 같습니다.

* 단일 호스트 컴퓨터에서 예약됩니다.
* DNS 이름 레이블이 할당됩니다.
* 1개의 노출 포트가 있는 단일 공용 IP 주소를 노출합니다.
* 두 개의 컨테이너로 구성됩니다. 하나의 컨테이너는 포트 80을 수신하고, 다른 컨테이너는 포트 5000을 수신합니다.
* 볼륨 탑재로 2개의 Azure 파일 공유가 포함되어 있으며, 각 컨테이너는 공유 중 하나를 로컬에서 탑재합니다.

> [!NOTE]
> 다중 컨테이너 그룹은 현재 Linux 컨테이너만 지원 합니다. Windows 컨테이너의 경우 Azure Container Instances는 단일 인스턴스의 배포만 지원 합니다. Windows 컨테이너에 모든 기능을 제공 하기 위해 작업 하는 동안 서비스 [개요](container-instances-overview.md#linux-and-windows-containers)에서 현재 플랫폼 차이를 찾을 수 있습니다.

## <a name="deployment"></a>배포

다중 컨테이너 그룹을 배포 하는 두 가지 일반적인 방법은 [리소스 관리자 템플릿][resource-manager template] 또는 [yaml 파일][yaml-file]을 사용 하는 것입니다. 컨테이너 인스턴스를 배포할 때 추가 Azure 서비스 리소스 (예: [Azure Files 공유][azure-files])를 배포 해야 하는 경우 리소스 관리자 템플릿을 권장 합니다. YAML 형식의 보다 간결한 특성으로 인해 배포에 컨테이너 인스턴스만 포함 된 경우에는 YAML 파일이 권장 됩니다.

컨테이너 그룹의 구성을 유지 하려면 Azure CLI 명령 [az container export][az-container-export]를 사용 하 여 해당 구성을 yaml 파일로 내보낼 수 있습니다. 내보내기를 사용 하면 컨테이너 그룹 구성을 "코드로 구성"을 위한 버전 제어로 저장할 수 있습니다. 또는 YAML에서 새 구성을 개발할 때 내보낸 파일을 시작점으로 사용할 수 있습니다.

## <a name="resource-allocation"></a>리소스 할당

Azure Container Instances는 그룹의 인스턴스에 대 한 [리소스 요청][resource-requests] 을 추가 하 여 cpu, 메모리, 선택적으로 [gpu][gpus] (미리 보기) 등의 리소스를 컨테이너 그룹에 할당 합니다. CPU 리소스를 예로 들어 두 개의 인스턴스를 사용 하 여 1 개의 CPU를 요청 하는 컨테이너 그룹을 만드는 경우 컨테이너 그룹에 2 개의 cpu가 할당 됩니다.

컨테이너 그룹에 사용할 수 있는 최대 리소스는 배포에 사용 되는 [Azure 지역][region-availability] 에 따라 달라 집니다.

### <a name="container-resource-requests-and-limits"></a>컨테이너 리소스 요청 및 제한

* 기본적으로 그룹의 컨테이너 인스턴스는 그룹의 요청 된 리소스를 공유 합니다. 각각 1 개의 CPU를 요청 하는 두 개의 인스턴스가 있는 그룹에서 전체 그룹은 2 개의 Cpu에 액세스할 수 있습니다. 각 인스턴스는 Cpu를 2 개까지 사용할 수 있으며 인스턴스는 실행 되는 동안 CPU 리소스를 경쟁할 수 있습니다.

* 그룹의 인스턴스에의 한 리소스 사용을 제한 하려면 선택적으로 인스턴스에 대 한 [리소스 제한을][resource-limits] 설정 합니다. 1 개의 CPU를 요청 하는 두 개의 인스턴스가 있는 그룹에서 컨테이너 중 하나를 실행 하는 데 더 많은 Cpu가 필요할 수 있습니다.

  이 시나리오에서는 한 인스턴스에 대해 0.5 CPU의 리소스 제한을 설정 하 고 두 번째에 Cpu 제한인 2 개를 설정할 수 있습니다. 이 구성은 첫 번째 컨테이너의 리소스 사용량을 0.5 CPU로 제한 하 여 두 번째 컨테이너가 사용 가능한 경우 최대 2 개의 Cpu까지 사용할 수 있도록 합니다.

자세한 내용은 REST API 컨테이너 그룹의 [ResourceRequirements][resource-requirements] 속성을 참조 하세요.

### <a name="minimum-and-maximum-allocation"></a>최소 및 최대 할당

* **최소** 1 개의 CPU 및 1gb의 메모리를 컨테이너 그룹에 할당 합니다. 그룹 내의 개별 컨테이너 인스턴스는 1 개 미만의 CPU와 1gb의 메모리로 프로 비전 할 수 있습니다. 

* 컨테이너 그룹의 **최대** 리소스는 배포 지역에서 Azure Container Instances에 대 한 [리소스 가용성][region-availability] 을 참조 하세요.

## <a name="networking"></a>네트워킹

컨테이너 그룹은 IP 주소와 해당 IP 주소에서 포트 네임스페이스를 공유합니다. 외부 클라이언트가 그룹 내 컨테이너에 도달하게 지원하려면 IP 주소와 컨테이너에서 해당 포트를 공개해야 합니다. 그룹 내의 컨테이너는 포트 네임 스페이스를 공유 하기 때문에 포트 매핑이 지원 되지 않습니다. 그룹 내의 컨테이너는 해당 포트가 그룹의 IP 주소에서 외부에 노출 되지 않는 경우에도 노출 된 포트의 localhost를 통해 서로 연결할 수 있습니다.

필요에 따라 컨테이너 그룹을 [Azure virtual network][virtual-network] (미리 보기)에 배포 하 여 컨테이너가 가상 네트워크의 다른 리소스와 안전 하 게 통신할 수 있도록 합니다.

## <a name="storage"></a>저장 공간

컨테이너 그룹 내에서 탑재할 외부 볼륨을 지정할 수 있습니다. 해당 볼륨을 그룹의 개별 컨테이너 내에 있는 특정 경로에 매핑할 수 있습니다.

## <a name="common-scenarios"></a>일반적인 시나리오

다중 컨테이너 그룹은 단일 기능 작업을 적은 수의 컨테이너 이미지로 나누려는 경우에 유용합니다. 이러한 이미지는 다른 팀에서 제공될 수 있으며 별도의 리소스 요구 사항을 가질 수 있습니다.

사용 예는 다음과 같습니다.

* 웹 애플리케이션을 처리하는 컨테이너와 원본 제어에서 최신 콘텐츠를 풀링하는 컨테이너
* 애플리케이션 컨테이너 및 로깅 컨테이너. 로깅 컨테이너는 주 애플리케이션에서 출력한 로그 및 메트릭을 수집하여 장기 저장소로 기록합니다.
* 애플리케이션 컨테이너 및 모니터링 컨테이너. 모니터링 컨테이너는 애플리케이션이 올바르게 실행 중이고 응답하고 있는지 확인하기 위해 애플리케이션에 정기적으로 요청을 보내고, 그렇지 않은 경우 경고를 올립니다.
* 프런트 엔드 컨테이너와 백 엔드 컨테이너 프런트 엔드는 데이터를 검색 하기 위해 백 엔드가 서비스를 실행 하는 웹 응용 프로그램을 제공할 수 있습니다. 

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
