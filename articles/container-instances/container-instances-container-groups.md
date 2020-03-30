---
title: 컨테이너 그룹 소개
description: 수명 주기 및 저장소 및 네트워크와 같은 리소스를 공유하는 인스턴스 컬렉션인 Azure 컨테이너 인스턴스에서 컨테이너 그룹에 대해 알아봅니다.
ms.topic: article
ms.date: 11/01/2019
ms.custom: mvc
ms.openlocfilehash: 73781418321c3932bf3e0190b646dcd3bb178195
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79247216"
---
# <a name="container-groups-in-azure-container-instances"></a>Azure Container Instances의 컨테이너 그룹

Azure Container Instances의 최상위 리소스는 *컨테이너 그룹*입니다. 이 문서에서는 컨테이너 그룹의 정의와 이들이 지원하는 시나리오 유형에 대해 설명합니다.

## <a name="what-is-a-container-group"></a>컨테이너 그룹이란 무엇입니까?

컨테이너 그룹은 같은 호스트 컴퓨터에서 예약되어 있는 컨테이너 컬렉션입니다. 컨테이너 그룹의 컨테이너는 수명 주기, 리소스, 로컬 네트워크 및 저장소 볼륨을 공유합니다. 개념이 [Kubernetes의][kubernetes-pod] *포드와* 비슷합니다.

다음 다이어그램은 여러 컨테이너가 포함된 컨테이너 그룹의 예를 보여줍니다.

![컨테이너 그룹 다이어그램][container-groups-example]

이 예제 컨테이너 그룹은 다음과 같습니다.

* 단일 호스트 컴퓨터에서 예약됩니다.
* DNS 이름 레이블이 할당됩니다.
* 1개의 노출 포트가 있는 단일 공용 IP 주소를 노출합니다.
* 두 개의 컨테이너로 구성됩니다. 하나의 컨테이너는 포트 80을 수신하고, 다른 컨테이너는 포트 5000을 수신합니다.
* 볼륨 탑재로 2개의 Azure 파일 공유가 포함되어 있으며, 각 컨테이너는 공유 중 하나를 로컬에서 탑재합니다.

> [!NOTE]
> 다중 컨테이너 그룹은 현재 Linux 컨테이너만 지원합니다. Windows 컨테이너의 경우 Azure 컨테이너 인스턴스는 단일 컨테이너 인스턴스의 배포만 지원합니다. 모든 기능을 Windows 컨테이너로 가져오기 위해 노력하고 있지만 서비스 [개요에서](container-instances-overview.md#linux-and-windows-containers)현재 플랫폼 의 차이점을 확인할 수 있습니다.

## <a name="deployment"></a>배포

다음은 다중 컨테이너 그룹을 배포하는 두 가지 일반적인 방법, 즉 [리소스 관리자 템플릿][resource-manager template] 또는 [YAML 파일을][yaml-file]사용합니다. 컨테이너 인스턴스를 배포할 때 추가 Azure 서비스 리소스(예: [Azure Files 공유)를][azure-files]배포해야 하는 경우 리소스 관리자 템플릿을 권장합니다. YAML 형식의 간결한 특성으로 인해 배포에 컨테이너 인스턴스만 포함되는 경우 YAML 파일을 권장합니다. 설정할 수 있는 속성에 대한 자세한 내용은 [리소스 관리자 템플릿 참조](/azure/templates/microsoft.containerinstance/containergroups) 또는 [YAML 참조](container-instances-reference-yaml.md) 설명서를 참조하십시오.

컨테이너 그룹의 구성을 유지 하려면 Azure CLI 명령 [az 컨테이너 내보내기를][az-container-export]사용 하 여 YAML 파일로 구성을 내보낼 수 있습니다. 내보내기를 사용하면 "코드로 구성"에 대한 버전 제어에 컨테이너 그룹 구성을 저장할 수 있습니다. 또는 YAML에서 새 구성을 개발할 때 내보낸 파일을 시작점으로 사용할 수 있습니다.



## <a name="resource-allocation"></a>리소스 할당

Azure 컨테이너 인스턴스는 그룹의 인스턴스에 대한 리소스 요청을 추가하여 CPU, 메모리 및 선택적으로 [GPU(미리][gpus] 보기)와 같은 [리소스를][resource-requests] 다중 컨테이너 그룹에 할당합니다. 예를 들어 CPU 리소스를 예로 들면 두 개의 컨테이너 인스턴스가 있는 컨테이너 그룹을 만들면 각각 1개의 CPU를 요청하는 경우 컨테이너 그룹에 2개의 CPU가 할당됩니다.

### <a name="resource-usage-by-container-instances"></a>컨테이너 인스턴스별 리소스 사용량

그룹의 각 컨테이너 인스턴스는 리소스 요청에 지정된 리소스를 할당합니다. 그러나 선택적 [리소스 제한][resource-limits] 속성을 구성 하는 경우 그룹에서 컨테이너 인스턴스에서 사용 하는 최대 리소스다를 수 있습니다. 컨테이너 인스턴스의 리소스 제한은 필수 [리소스 요청][resource-requests] 속성보다 크거나 같아야 합니다.

* 리소스 제한을 지정하지 않으면 컨테이너 인스턴스의 최대 리소스 사용량이 리소스 요청과 동일합니다.

* 컨테이너 인스턴스에 대한 제한을 지정하면 인스턴스의 최대 사용량이 설정한 한도까지 요청보다 클 수 있습니다. 이에 따라 그룹의 다른 컨테이너 인스턴스에 의한 리소스 사용량이 감소할 수 있습니다. 컨테이너 인스턴스에 대해 설정할 수 있는 최대 리소스 제한은 그룹에 할당된 총 리소스입니다.
    
예를 들어 각각 1개의 CPU를 요청하는 두 개의 컨테이너 인스턴스가 있는 그룹에서 컨테이너 중 하나가 다른 CPU보다 더 많은 CPU를 실행해야 하는 워크로드를 실행할 수 있습니다.

이 시나리오에서는 컨테이너 인스턴스에 대 한 2 CPU의 리소스 제한을 설정할 수 있습니다. 이 구성을 사용하면 컨테이너 인스턴스가 사용 가능한 경우 최대 2개의 CPU를 사용할 수 있습니다.

### <a name="minimum-and-maximum-allocation"></a>최소 및 최대 할당

* **최소** CPU 1GB와 1GB의 메모리를 컨테이너 그룹에 할당합니다. 그룹 내의 개별 컨테이너 인스턴스는 CPU 가 1GB 미만이고 메모리가 1GB 미만으로 프로비저닝될 수 있습니다. 

* 컨테이너 그룹의 **최대** 리소스에 대한 자세한 내용은 배포 지역의 Azure 컨테이너 인스턴스에 대한 [리소스 가용성을][region-availability] 참조하세요.

## <a name="networking"></a>네트워킹

컨테이너 그룹은 외부 IP 주소, 해당 IP 주소의 하나 이상의 포트 및 완전히 정규화된 도메인 이름(FQDN)이 있는 DNS 레이블을 공유할 수 있습니다. 외부 클라이언트가 그룹 내 컨테이너에 도달하게 지원하려면 IP 주소와 컨테이너에서 해당 포트를 공개해야 합니다. 그룹 내의 컨테이너는 포트 네임스페이스를 공유하므로 포트 매핑은 지원되지 않습니다. 컨테이너 그룹의 IP 주소와 FQDN컨테이너 그룹이 삭제되면 해제됩니다. 

컨테이너 그룹 내에서 컨테이너 인스턴스는 그룹의 IP 주소 또는 컨테이너에서 외부에 표시되지 않더라도 모든 포트에서 localhost를 통해 서로 연결할 수 있습니다.

컨테이너가 가상 네트워크의 다른 리소스와 안전하게 통신할 수 있도록 컨테이너 그룹을 [Azure 가상 네트워크에][virtual-network] 배포합니다.

## <a name="storage"></a>스토리지

컨테이너 그룹 내에서 탑재할 외부 볼륨을 지정할 수 있습니다. 지원되는 볼륨은 다음과 같습니다.
* [Azure 파일 공유][azure-files]
* [비밀][secret]
* [빈 디렉터리][empty-directory]
* [복제 된 git 리포지토리][volume-gitrepo]

해당 볼륨을 그룹의 개별 컨테이너 내에 있는 특정 경로에 매핑할 수 있습니다. 

## <a name="common-scenarios"></a>일반적인 시나리오

다중 컨테이너 그룹은 단일 기능 작업을 적은 수의 컨테이너 이미지로 나누려는 경우에 유용합니다. 이러한 이미지는 다른 팀에서 제공될 수 있으며 별도의 리소스 요구 사항을 가질 수 있습니다.

사용 예는 다음과 같습니다.

* 웹 애플리케이션을 처리하는 컨테이너와 원본 제어에서 최신 콘텐츠를 풀링하는 컨테이너
* 애플리케이션 컨테이너 및 로깅 컨테이너. 로깅 컨테이너는 주 애플리케이션에서 출력한 로그 및 메트릭을 수집하여 장기 스토리지로 기록합니다.
* 애플리케이션 컨테이너 및 모니터링 컨테이너. 모니터링 컨테이너는 애플리케이션이 올바르게 실행 중이고 응답하고 있는지 확인하기 위해 애플리케이션에 정기적으로 요청을 보내고, 그렇지 않은 경우 경고를 올립니다.
* 프런트 엔드 컨테이너 및 백 엔드 컨테이너. 프런트 엔드는 백 엔드가 데이터를 검색하는 서비스를 실행하는 웹 응용 프로그램을 제공할 수 있습니다. 

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
[secret]: container-instances-volume-secret.md
[volume-gitrepo]: container-instances-volume-gitrepo.md
[gpus]: container-instances-gpu.md
[empty-directory]: container-instances-volume-emptydir.md
[az-container-export]: /cli/azure/container#az-container-export
