---
title: Azure HPC 캐시 필수 구성 조건
description: Azure HPC 캐시 를 사용하기 위한 필수 구성 조건
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 04/03/2020
ms.author: rohogue
ms.openlocfilehash: ab342dba5c8be2ff3793c0eb36926969b3e364e5
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81537290"
---
# <a name="prerequisites-for-azure-hpc-cache"></a>Azure HPC 캐시에 대한 필수 구성 조건

Azure 포털을 사용하여 새 Azure HPC 캐시를 만들기 전에 환경이 이러한 요구 사항을 충족하는지 확인합니다.

## <a name="azure-subscription"></a>Azure 구독

유료 구독을 권장합니다.

## <a name="network-infrastructure"></a>네트워크 인프라

캐시를 사용하기 전에 두 가지 네트워크 관련 필수 구성 조건을 설정해야 합니다.

* Azure HPC 캐시 인스턴스에 대한 전용 서브넷
* 캐시가 저장소 및 기타 리소스에 액세스할 수 있도록 DNS 지원

### <a name="cache-subnet"></a>캐시 서브넷

Azure HPC 캐시에는 다음과 같은 품질이 있는 전용 서브넷이 필요합니다.

* 서브넷에는 사용 가능한 IP 주소가 64개 이상 있어야 합니다.
* 서브넷은 클라이언트 컴퓨터와 같은 관련 서비스에 대해서도 다른 VM을 호스팅할 수 없습니다.
* 여러 Azure HPC 캐시 인스턴스를 사용하는 경우 각 인스턴스에는 고유한 서브넷이 필요합니다.

가장 좋은 방법은 각 캐시에 대해 새 서브넷을 만드는 것입니다. 캐시를 만드는 과정에서 새 가상 네트워크와 서브넷을 만들 수 있습니다.

### <a name="dns-access"></a>DNS 액세스

캐시는 가상 네트워크 외부의 리소스에 액세스하려면 DNS가 필요합니다. 사용 중인 리소스에 따라 사용자 지정된 DNS 서버를 설정하고 해당 서버와 Azure DNS 서버 간에 전달을 구성해야 할 수 있습니다.

* Azure Blob 저장소 끝점 및 기타 내부 리소스에 액세스하려면 Azure 기반 DNS 서버가 필요합니다.
* 온-프레미스 저장소에 액세스하려면 저장소 호스트 이름을 확인할 수 있는 사용자 지정 DNS 서버를 구성해야 합니다.

Blob 저장소에만 액세스해야 하는 경우 캐시에 Azure에서 제공한 기본 DNS 서버를 사용할 수 있습니다. 그러나 다른 리소스에 대한 액세스가 필요한 경우 사용자 지정 DNS 서버를 만들고 Azure 특정 해결 요청을 Azure DNS 서버로 전달하도록 구성해야 합니다.

간단한 DNS 서버를 사용하여 사용 가능한 모든 캐시 마운트 지점 간에 클라이언트 연결을 로드할 수도 있습니다.

Azure 가상 네트워크의 [리소스에 대한 이름 확인에서](https://docs.microsoft.com/azure/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances)Azure 가상 네트워크 및 DNS 서버 구성에 대해 자세히 알아봅니다.

## <a name="permissions"></a>사용 권한

캐시를 만들기 전에 이러한 권한 관련 필수 구성 조건을 확인하십시오.

* 캐시 인스턴스는 가상 네트워크 인터페이스(NIC)를 만들 수 있어야 합니다. 캐시를 만드는 사용자는 NIC를 만들기 위해 구독에 충분한 권한이 있어야 합니다.

* Blob 저장소를 사용하는 경우 Azure HPC 캐시는 저장소 계정에 액세스하기 위한 권한 부여가 필요합니다. 역할 기반 액세스 제어(RBAC)를 사용하여 Blob 저장소에 대한 캐시 액세스 권한을 부여합니다. 저장소 계정 참여자 및 저장소 Blob 데이터 참여자라는 두 가지 역할이 필요합니다.

  저장소 대상 [추가의](hpc-cache-add-storage.md#add-the-access-control-roles-to-your-account) 지침에 따라 역할을 추가합니다.

## <a name="storage-infrastructure"></a>스토리지 인프라

캐시는 Azure Blob 컨테이너 또는 NFS 하드웨어 저장소 내보내기를 지원합니다. 캐시를 만든 후 저장소 대상을 추가합니다.

각 저장소 유형에는 특정 필수 구성 조건이 있습니다.

### <a name="blob-storage-requirements"></a>Blob 스토리지 요구 사항

캐시와 함께 Azure Blob 저장소를 사용하려면 호환되는 저장소 계정과 빈 Blob 컨테이너 또는 Azure HPC 캐시 형식의 데이터로 채워진 컨테이너가 [필요합니다.](hpc-cache-ingest.md)

저장소 대상을 추가하기 전에 계정을 만듭니다. 대상을 추가할 때 새 컨테이너를 만들 수 있습니다.

호환되는 저장소 계정을 만들려면 다음 설정을 사용합니다.

* 성능: **표준**
* 계정 종류 : **StorageV2 (범용 v2)**
* 복제: **로컬 중복 저장소(LRS)**
* 액세스 계층(기본값): **핫**

캐시와 동일한 위치에 저장소 계정을 사용하는 것이 좋습니다.

또한 위의 [사용 권한에](#permissions)언급된 대로 Azure 저장소 계정에 대한 캐시 응용 프로그램 액세스 권한을 부여해야 합니다. 저장소 대상 [추가의](hpc-cache-add-storage.md#add-the-access-control-roles-to-your-account) 절차를 수행하여 캐시에 필요한 액세스 역할을 제공합니다. 저장소 계정 소유자가 아닌 경우 소유자가 이 단계를 수행해야 합니다.

### <a name="nfs-storage-requirements"></a>NFS 스토리지 요구 사항
<!-- linked from configuration.md -->

NFS 스토리지 시스템(예: 온-프레미스 하드웨어 NAS 시스템)을 사용하는 경우 이러한 요구 사항을 충족하는지 확인합니다. 이러한 설정을 확인하려면 저장소 시스템(또는 데이터 센터)에 대해 네트워크 관리자 또는 방화벽 관리자와 협력해야 할 수 있습니다.

> [!NOTE]
> 캐시에 NFS 저장소 시스템에 대한 액세스 가 부족하면 저장소 대상 생성이 실패합니다.

자세한 내용은 [문제 해결 NAS 구성 및 NFS 저장소 대상 문제에](troubleshoot-nas.md)포함되어 있습니다.

* **네트워크 연결:** Azure HPC 캐시는 캐시 서브넷과 NFS 시스템의 데이터 센터 간에 고대역폭 네트워크 액세스가 필요합니다. [ExpressRoute](https://docs.microsoft.com/azure/expressroute/) 또는 이와 유사한 액세스를 권장합니다. VPN을 사용하는 경우 큰 패킷이 차단되지 않도록 TCP MSS를 1350으로 고정하도록 구성해야 할 수 있습니다. VPN 설정 문제 해결에 대한 추가 도움말에 대한 [VPN 패킷 크기 제한을](troubleshoot-nas.md#adjust-vpn-packet-size-restrictions) 읽어보십시오.

* **포트 액세스:** 캐시는 저장소 시스템의 특정 TCP/UDP 포트에 액세스해야 합니다. 저장소 유형에 따라 포트 요구 사항이 다릅니다.

  저장소 시스템의 설정을 확인하려면 이 절차를 따르십시오.

  * 저장소 `rpcinfo` 시스템에 명령을 내서 필요한 포트를 확인합니다. 아래 명령은 포트를 나열하고 관련 결과를 테이블에 서식을 지정합니다. 시스템의 IP 주소를 *<storage_IP>* 용어 대신 사용합니다.)

    NFS 인프라가 설치된 Linux 클라이언트에서 이 명령을 내릴 수 있습니다. 클러스터 서브넷 내에서 클라이언트를 사용하는 경우 서브넷과 저장소 시스템 간의 연결을 확인하는 데도 도움이 될 수 있습니다.

    ```bash
    rpcinfo -p <storage_IP> |egrep "100000\s+4\s+tcp|100005\s+3\s+tcp|100003\s+3\s+tcp|100024\s+1\s+tcp|100021\s+4\s+tcp"| awk '{print $4 "/" $3 " " $5}'|column -t
    ```

  ``rpcinfo`` 쿼리에서 반환되는 모든 포트가 Azure HPC 캐시의 서브넷에서 무제한 트래픽을 허용하는지 확인합니다.

  * `rpcinfo` 명령을 사용할 수 없는 경우 일반적으로 사용되는 포트에서 인바운드 및 아웃바운드 트래픽을 허용하는지 확인합니다.

    | 프로토콜 | 포트  | 서비스  |
    |----------|-------|----------|
    | TCP/UDP  | 111   | rpcbind  |
    | TCP/UDP  | 2049  | NFS      |
    | TCP/UDP  | 4045  | nlockmgr |
    | TCP/UDP  | 4046  | 장착 된   |
    | TCP/UDP  | 4047  | 상태   |

    일부 시스템은 이러한 서비스에 대해 서로 다른 포트 번호를 사용하며, 스토리지 시스템의 설명서를 참조하십시오.

  * 방화벽 설정을 확인하여 이러한 모든 필수 포트에서 트래픽을 허용하는지 확인합니다. Azure에서 사용되는 방화벽과 데이터 센터의 온-프레미스 방화벽을 확인해야 합니다.

* **디렉터리 액세스:** 저장소 `showmount` 시스템에서 명령을 사용하도록 설정합니다. Azure HPC Cache는 이 명령을 사용하여 저장소 대상 구성이 유효한 내보내기를 가리키는지 확인하고 여러 마운트가 동일한 하위 디렉터리(파일 충돌 위험)에 액세스하지 않도록 합니다.

  > [!NOTE]
  > NFS 스토리지 시스템에서 NetApp의 ONTAP 9.2 운영 체제를 사용하는 경우 을 ** `showmount`사용하지 마십시오. ** 도움이 [드리면 Microsoft 서비스 및 지원에 문의하십시오.](hpc-cache-support-ticket.md)

  NFS 저장소 대상 [문제 해결 문서에서](troubleshoot-nas.md#enable-export-listing)디렉터리 목록 액세스에 대해 자세히 알아봅니다.

* **루트** 액세스(읽기/쓰기): 캐시가 백 엔드 시스템에 사용자 ID 0으로 연결됩니다. 저장소 시스템에서 다음 설정을 확인합니다.
  
  * `no_root_squash`을 사용하도록 설정합니다. 이 옵션을 사용하면 원격 루트 사용자가 루트가 소유한 파일에 액세스할 수 있습니다.

  * 내보내기 정책이 캐시의 서브넷의 루트 액세스에 대한 제한을 포함하지 않는지 확인합니다.

  * 저장소에 다른 내보내기의 하위 디렉터리인 내보내기가 있는 경우 캐시에 경로의 가장 낮은 세그먼트에 대한 루트 액세스 권한이 있는지 확인합니다. 자세한 내용은 NFS 저장소 대상 문제 해결 문서에서 [디렉터리 경로에](troubleshoot-nas.md#allow-root-access-on-directory-paths) 대한 루트 액세스를 읽어보십시오.

* NFS 백 엔드 스토리지는 호환되는 하드웨어/소프트웨어 플랫폼이어야 합니다. 자세한 내용은 Azure HPC 캐시 팀에 문의하십시오.

## <a name="next-steps"></a>다음 단계

* Azure 포털에서 [Azure HPC 캐시 인스턴스 만들기](hpc-cache-create.md)
