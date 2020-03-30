---
title: Azure HPC 캐시 및 Azure NetApp 파일 사용
description: Azure HPC 캐시를 사용하여 Azure NetApp 파일로 저장된 데이터에 대한 액세스를 개선하는 방법
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: rohogue
ms.openlocfilehash: 38f9d0338ce4c47024d670e6d3ee89a97faecc91
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80238690"
---
# <a name="use-azure-hpc-cache-with-azure-netapp-files"></a>Azure NetApp 파일과 함께 Azure HPC 캐시 사용

[Azure NetApp 파일을 Azure](https://azure.microsoft.com/services/netapp/) HPC 캐시의 저장소 대상으로 사용할 수 있습니다. 이 문서에서는 두 서비스가 함께 작동하는 방법을 설명하고 설정에 대한 팁을 제공합니다.

Azure NetApp 파일은 ONTAP 운영 체제와 Microsoft Azure의 확장성 및 속도를 결합합니다. 이 조합을 통해 사용자는 코드를 다시 작성하지 않고도 설정된 워크플로를 클라우드로 이동할 수 있습니다.

Azure HPC 캐시 구성 요소를 추가하면 하나의 집계된 네임스페이스에 여러 Azure NetApp 파일 볼륨을 표시하여 파일 액세스를 향상시킬 수 있습니다. 다른 서비스 지역에 있는 볼륨에 대해 에지 캐싱을 제공할 수 있습니다. 또한 낮은 계층 서비스 수준에서 생성된 볼륨에 대한 요청 시 성능을 향상하여 비용을 절감할 수 있습니다.

## <a name="overview"></a>개요

Azure NetApp 파일 시스템을 Azure HPC 캐시가 있는 백 엔드 저장소로 사용하려면 이 프로세스를 따르십시오.

1. [아래의 시스템 계획의](#plan-your-azure-netapp-files-system)지침에 따라 Azure NetApp 파일 시스템 및 볼륨을 만듭니다.
1. 파일 액세스가 필요한 지역에서 Azure HPC 캐시를 만듭니다. (Azure [HPC 캐시 만들기의](hpc-cache-create.md)지침을 사용하십시오.)
1. Azure NetApp 파일 볼륨을 가리키는 캐시의 [저장소 대상을 정의합니다.](#create-storage-targets-in-the-cache) 볼륨에 액세스하는 데 사용되는 각 고유 IP 주소에 대해 하나의 캐시 저장소 대상을 만듭니다.
1. 클라이언트가 Azure NetApp 파일 볼륨을 직접 탑재하는 대신 Azure [HPC 캐시를 마운트할](#mount-storage-targets) 수 있도록 합니다.

## <a name="plan-your-azure-netapp-files-system"></a>Azure NetApp 파일 시스템 계획

Azure NetApp 파일 시스템을 계획할 때이 섹션의 항목에 주의 하 고 Azure HPC 캐시와 원활 하 게 통합할 수 있는지 확인 합니다.

또한 Azure HPC 캐시에 사용할 볼륨을 만들기 전에 [Azure NetApp 파일 설명서를](../azure-netapp-files/index.yml) 읽어보십시오.

### <a name="nfs-client-access-only"></a>NFS 클라이언트 액세스만

Azure HPC 캐시는 현재 NFS 액세스만 지원합니다. SMB ACL 또는 POSIX 모드 비트 볼륨에는 사용할 수 없습니다.

### <a name="exclusive-subnet-for-azure-netapp-files"></a>Azure NetApp 파일에 대한 전용 서브넷

Azure NetApp 파일은 볼륨에 대해 단일 위임된 서브넷을 사용합니다. 다른 리소스는 해당 서브넷을 사용할 수 없습니다. 또한 가상 네트워크에서 하나의 서브넷만 Azure NetApp 파일에 사용할 수 있습니다. [Azure NetApp 파일 네트워크 계획에 대한 지침에서](../azure-netapp-files/azure-netapp-files-network-topologies.md)자세히 알아보십시오.

### <a name="delegated-subnet-size"></a>위임된 서브넷 크기

Azure HPC 캐시와 함께 사용할 Azure NetApp 파일 시스템을 만들 때 위임된 서브넷의 최소 크기를 사용합니다.

netmask /28로 지정된 최소 크기는 16개의 IP 주소를 제공합니다. 실제로 Azure NetApp 파일은 볼륨 액세스에 사용할 수 있는 IP 주소 중 3개만 사용합니다. 즉, 모든 볼륨을 다루기 위해 Azure HPC 캐시에 세 개의 저장소 대상만 만들면 됩니다.

위임된 서브넷이 너무 큰 경우 Azure NetApp 파일 볼륨이 단일 Azure HPC 캐시 인스턴스에서 처리할 수 있는 것보다 더 많은 IP 주소를 사용할 수 있습니다. 단일 캐시에는 최대 10개의 저장소 대상이 있을 수 있습니다.

Azure NetApp 파일 설명서의 빠른 시작 예제는 위임된 서브넷에 대해 10.7.0.0/16을 사용하며, 이 서브넷은 너무 큽습니다.

### <a name="capacity-pool-service-level"></a>용량 풀 서비스 수준

용량 풀에 대한 서비스 수준을 선택할 때는 워크플로를 고려하십시오. Azure NetApp 파일 볼륨에 데이터를 자주 다시 쓰는 경우 쓰기 저장 시간이 느린 경우 캐시의 성능이 제한될 수 있습니다. 쓰기가 자주 발생하는 볼륨에 대해 높은 서비스 수준을 선택합니다.

서비스 수준이 낮은 볼륨은 캐시가 콘텐츠를 미리 채우는 동안 작업 시작 시 약간의 지연이 나타날 수도 있습니다. 캐시가 실행되고 좋은 작업 파일 집합으로 실행되면 지연이 눈에 띄지 않게됩니다.

생성 후 변경할 수 없으므로 용량 풀 서비스 수준을 미리 계획하는 것이 중요합니다. 다른 용량 풀에서 새 볼륨을 만들어야 하며 데이터를 복사해야 합니다.

볼륨의 저장소 할당량과 액세스 권한을 방해하지 않고 용량 풀의 크기를 변경할 수 있습니다.

## <a name="create-storage-targets-in-the-cache"></a>캐시에 스토리지 대상 만들기

Azure NetApp 파일 시스템을 설정하고 Azure HPC 캐시를 만든 후 파일 시스템 볼륨을 가리키는 캐시의 저장소 대상을 정의합니다.

Azure NetApp 파일 볼륨에서 사용하는 각 IP 주소에 대해 하나의 저장소 대상을 만듭니다. IP 주소는 볼륨의 마운트 지침 페이지에 나열됩니다.

여러 볼륨이 동일한 IP 주소를 공유하는 경우 모든 저장소 대상에 대해 하나의 저장소 대상을 사용할 수 있습니다.  

Azure [NetApp 파일 설명서의 탑재 지침을](../azure-netapp-files/azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md) 따라 사용할 IP 주소를 찾습니다.

또한 Azure CLI를 통해 IP 주소를 찾을 수 있습니다.

```azurecli
az netappfiles volume list -g ${RESOURCE_GROUP} --account-name ${ANF_ACCOUNT} --pool-name ${POOL} --query "[].mountTargets[].ipAddress" | grep -Ee '[0-9]+[.][0-9]+[.][0-9]+[.][0-9]+' | tr -d '"' | tr -d , | sort | uniq
```

Azure NetApp 파일 시스템의 내보내기 이름에는 단일 경로 구성 요소가 있습니다. 해당 내보내기는 파일 액세스를 제공하지 ``/`` 않으므로 Azure NetApp Files에서 루트 내보내기에 대한 저장소 대상을 만들지 마십시오.

이러한 저장소 대상에 대한 가상 네임스페이스 경로에는 특별한 제한이 없습니다.

## <a name="mount-storage-targets"></a>스토리지 대상 마운트

클라이언트 컴퓨터는 Azure NetApp 파일 볼륨을 직접 탑재하는 대신 캐시를 탑재해야 합니다. [Azure HPC 캐시 마운트의](hpc-cache-mount.md)지침을 따릅니다.

## <a name="next-steps"></a>다음 단계

* [Azure NetApp 파일](../azure-netapp-files/index.yml) 설정 및 사용에 대해 자세히 알아보기
* Azure NetApp 파일을 사용하도록 Azure HPC 캐시 시스템을 계획하고 설정하는 데 도움이 되면 지원 팀에 [문의하십시오.](hpc-cache-support-ticket.md)
