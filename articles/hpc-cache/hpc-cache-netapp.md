---
title: Azure HPC 캐시 및 Azure NetApp Files 사용
description: Azure HPC 캐시를 사용 하 여 Azure NetApp Files 저장 된 데이터에 대 한 액세스를 개선 하는 방법
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 10/30/2019
ms.author: v-erkel
ms.openlocfilehash: e955ddc14bb2b0a7abc0dc815c6955247568876b
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/20/2020
ms.locfileid: "86497015"
---
# <a name="use-azure-hpc-cache-with-azure-netapp-files"></a>Azure NetApp Files에서 Azure HPC 캐시 사용

[Azure NetApp Files](https://azure.microsoft.com/services/netapp/) 를 Azure HPC 캐시의 저장소 대상으로 사용할 수 있습니다. 이 문서에서는 두 서비스를 함께 사용 하는 방법을 설명 하 고 설정에 대 한 팁을 제공 합니다.

Azure NetApp Files는 ONTAP 운영 체제를 Microsoft Azure 확장성 및 속도와 결합 합니다. 이러한 조합을 통해 사용자는 코드를 다시 작성 하지 않고도 설정 된 워크플로를 클라우드로 전환할 수 있습니다.

Azure HPC 캐시 구성 요소를 추가 하면 하나의 집계 된 네임 스페이스에 여러 Azure NetApp Files 볼륨을 제시 하 여 파일 액세스를 향상 시킬 수 있습니다. 다른 서비스 지역에 있는 볼륨에 대 한에 지 캐싱을 제공할 수 있습니다. 또한 낮은 계층 서비스 수준에서 생성 된 볼륨에 대 한 요청 시 성능을 향상 시켜 비용을 절감할 수 있습니다.

## <a name="overview"></a>개요

Azure HPC 캐시를 사용 하 여 Azure NetApp Files 시스템을 백 엔드 저장소로 사용 하려면이 프로세스를 수행 합니다.

1. [아래의 시스템 계획](#plan-your-azure-netapp-files-system)에 있는 지침에 따라 Azure NetApp Files 시스템과 볼륨을 만듭니다.
1. 파일 액세스가 필요한 지역에 Azure HPC 캐시를 만듭니다. ( [AZURE HPC 캐시 만들기](hpc-cache-create.md)의 지침을 사용 합니다.)
1. 캐시에서 Azure NetApp Files 볼륨을 가리키는 [저장소 대상을 정의](#create-storage-targets-in-the-cache) 합니다. 볼륨에 액세스 하는 데 사용 되는 각각의 고유한 IP 주소에 대해 하나의 캐시 저장소 대상을 만듭니다.
1. 클라이언트가 Azure NetApp Files 볼륨을 직접 탑재 하는 대신 [AZURE HPC 캐시를 탑재](#mount-storage-targets) 하도록 합니다.

## <a name="plan-your-azure-netapp-files-system"></a>Azure NetApp Files 시스템 계획

Azure NetApp Files 시스템을 계획 하는 경우이 섹션의 항목을 주의 하 여 Azure HPC 캐시와 원활 하 게 통합할 수 있는지 확인 합니다.

또한 Azure HPC 캐시에서 사용할 볼륨을 만들기 전에 [Azure NetApp Files 설명서](../azure-netapp-files/index.yml) 를 참조 하세요.

### <a name="nfs-client-access-only"></a>NFS 클라이언트 액세스만

Azure HPC 캐시는 현재 NFS 액세스만 지원 합니다. SMB ACL 또는 POSIX 모드 비트 볼륨에서 사용할 수 없습니다.

### <a name="exclusive-subnet-for-azure-netapp-files"></a>Azure NetApp Files에 대 한 배타 서브넷

Azure NetApp Files는 해당 볼륨에 대해 위임 된 단일 서브넷을 사용 합니다. 다른 리소스는 해당 서브넷을 사용할 수 없습니다. 또한 Azure NetApp Files에는 가상 네트워크의 서브넷을 하나만 사용할 수 있습니다. [Azure NetApp Files 네트워크 계획에 대 한 지침](../azure-netapp-files/azure-netapp-files-network-topologies.md)을 자세히 알아보세요.

### <a name="delegated-subnet-size"></a>위임 된 서브넷 크기

Azure HPC 캐시에서 사용할 Azure NetApp Files 시스템을 만들 때 위임 된 서브넷의 최소 크기를 사용 합니다.

넷마스크/28로 지정 된 최소 크기는 16 개의 IP 주소를 제공 합니다. 실제로 볼륨 액세스를 위해 사용 가능한 IP 주소를 세 개만 사용 Azure NetApp Files 합니다. 즉, 모든 볼륨을 포함 하기 위해 Azure HPC 캐시에 저장소 대상을 세 개 만들어야 합니다.

위임 된 서브넷이 너무 크면 단일 Azure HPC 캐시 인스턴스가 처리할 수 있는 것 보다 더 많은 IP 주소를 Azure NetApp Files 볼륨에서 사용할 수 있습니다. 단일 캐시에는 최대 10 개의 저장소 대상이 있을 수 있습니다.

Azure NetApp Files 설명서의 빠른 시작 예제에서는 위임 된 서브넷에 대해 10.7.0.0/16을 사용 하 여 너무 많은 서브넷을 제공 합니다.

### <a name="capacity-pool-service-level"></a>용량 풀 서비스 수준

용량 풀의 서비스 수준을 선택할 때 워크플로를 고려 합니다. Azure NetApp Files 볼륨에 데이터를 자주 쓰는 경우 쓰기 저장 시간이 느려지는 경우 캐시의 성능이 제한 될 수 있습니다. 쓰기를 자주 수행 하는 볼륨에 대 한 높은 서비스 수준을 선택 합니다.

서비스 수준이 낮은 볼륨에도 캐시에서 콘텐츠를 미리 채우는 동안 작업 시작 시 약간의 지연이 표시 될 수 있습니다. 캐시가 정상적으로 작동 하는 파일 집합을 사용 하 여 실행 되 면 지연 시간이 그러나 그 영향은 됩니다.

용량 풀 서비스 수준을 만든 후에는 변경할 수 없으므로 미리 계획 해야 합니다. 새 볼륨을 다른 용량 풀에 만들어야 하 고 데이터를 복사 해야 합니다.

볼륨의 저장소 할당량과 용량 풀의 크기는 액세스를 방해 하지 않고 변경할 수 있습니다.

## <a name="create-storage-targets-in-the-cache"></a>캐시에 저장소 대상 만들기

Azure NetApp Files 시스템을 설정 하 고 Azure HPC 캐시를 만든 후 파일 시스템 볼륨을 가리키는 캐시의 저장소 대상을 정의 합니다.

Azure NetApp Files 볼륨에서 사용 하는 각 IP 주소에 대해 저장소 대상을 하나씩 만듭니다. IP 주소는 볼륨의 탑재 명령 페이지에 나열 됩니다.

여러 볼륨이 동일한 IP 주소를 공유 하는 경우 모든 볼륨에 대해 하나의 저장소 대상을 사용할 수 있습니다.  

[Azure NetApp Files 설명서의 탑재 지침](../azure-netapp-files/azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md) 에 따라 사용할 IP 주소를 찾습니다.

또한 Azure CLI를 사용 하 여 IP 주소를 찾을 수 있습니다.

```azurecli
az netappfiles volume list -g ${RESOURCE_GROUP} --account-name ${ANF_ACCOUNT} --pool-name ${POOL} --query "[].mountTargets[].ipAddress" | grep -Ee '[0-9]+[.][0-9]+[.][0-9]+[.][0-9]+' | tr -d '"' | tr -d , | sort | uniq
```

Azure NetApp Files 시스템의 내보내기 이름에는 단일 경로 구성 요소가 있습니다. ``/``내보내기는 파일 액세스를 제공 하지 않기 때문에 Azure NetApp Files의 루트 내보내기에 대 한 저장소 대상을 만들려고 하지 마십시오.

이러한 저장소 대상에 대 한 가상 네임 스페이스 경로에 대 한 특별 한 제한은 없습니다.

## <a name="mount-storage-targets"></a>저장소 대상 탑재

클라이언트 컴퓨터는 Azure NetApp Files 볼륨을 직접 탑재 하는 대신 캐시를 탑재 해야 합니다. [AZURE HPC 캐시 탑재](hpc-cache-mount.md)의 지침을 따릅니다.

## <a name="next-steps"></a>다음 단계

* 설정 및 사용에 대 한 자세한 내용은 [Azure NetApp Files](../azure-netapp-files/index.yml) 를 참조 하세요.
* Azure NetApp Files를 사용 하도록 Azure HPC 캐시 시스템을 계획 하 고 설정 하는 방법에 대 한 자세한 내용은 [지원 담당자에 게 문의 하세요](hpc-cache-support-ticket.md).
