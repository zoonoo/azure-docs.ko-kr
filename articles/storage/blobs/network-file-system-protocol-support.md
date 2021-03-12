---
title: Azure Blob storage (미리 보기)의 네트워크 파일 시스템 3.0 지원 | Microsoft Docs
description: 이제 Blob storage는 NFS (네트워크 파일 시스템) 3.0 프로토콜을 지원 합니다. 이 지원을 통해 Linux 클라이언트는 Azure VM (가상 머신) 또는 온-프레미스를 실행 하는 컴퓨터에서 Blob 저장소에 컨테이너를 탑재할 수 있습니다.
author: normesta
ms.subservice: blobs
ms.service: storage
ms.topic: conceptual
ms.date: 02/19/2021
ms.author: normesta
ms.reviewer: yzheng
ms.custom: references_regions
ms.openlocfilehash: b00956a6fb5a79b09602ca1752cc547595f32db6
ms.sourcegitcommit: 94c3c1be6bc17403adbb2bab6bbaf4a717a66009
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/12/2021
ms.locfileid: "103224597"
---
# <a name="network-file-system-nfs-30-protocol-support-in-azure-blob-storage-preview"></a>Azure Blob storage에서 NFS (네트워크 파일 시스템) 3.0 프로토콜 지원 (미리 보기)

이제 Blob storage는 NFS (네트워크 파일 시스템) 3.0 프로토콜을 지원 합니다. 이 지원을 통해 개체 저장소 크기 조정 및 가격으로 Linux 파일 시스템 호환성을 제공 하 고 Linux 클라이언트가 Azure VM (가상 머신) 또는 온-프레미스 컴퓨터에서 Blob 저장소에 컨테이너를 탑재할 수 있습니다. 

> [!NOTE]
> NFS 3.0 Azure Blob storage의 프로토콜 지원은 공개 미리 보기 상태입니다. 오스트레일리아 동부, 대한민국 중부 및 미국 서 부 지역에서 표준 계층 성능을 갖춘 GPV2 storage 계정을 지원 합니다. 미리 보기는 모든 공용 지역에서 프리미엄 성능 계층을 포함 하는 블록 blob도 지원 합니다.

클라우드의 HPC (고성능 컴퓨팅)와 같은 대규모 레거시 워크 로드를 실행 하는 것은 항상 어려운 일입니다. 한 가지 이유는 응용 프로그램에서 NFS 또는 SMB (서버 메시지 블록)와 같은 기존 파일 프로토콜을 사용 하 여 데이터에 액세스 하는 경우가 많기 때문입니다. 또한 계층적 네임 스페이스와 효율적인 메타 데이터 작업을 제공 하는 파일 시스템 대신 플랫 네임 스페이스와 광범위 한 메타 데이터가 있는 개체 저장소에 초점을 맞춘 기본 클라우드 저장소 서비스입니다. 

Blob Storage 이제 계층 구조 네임 스페이스를 지원 하 고, NFS 3.0 프로토콜 지원과 함께 사용할 경우 대규모 클라우드 개체 저장소를 기반으로 레거시 응용 프로그램을 훨씬 더 쉽게 실행할 수 있습니다. 

## <a name="applications-and-workloads-suited-for-this-feature"></a>이 기능에 적합 한 응용 프로그램 및 워크 로드

NFS 3.0 프로토콜 기능은 높은 처리량을 처리 하는 데 가장 적합 하 고, 확장성이 높고, 미디어 처리, 위험 시뮬레이션, genomics 시퀀싱 등의 과도 한 읽기 작업을 수행 하는 데 가장 적합 합니다. 여러 판독기와 많은 스레드를 사용 하는 다른 워크 로드의 경우에는이 기능을 사용 하는 것이 좋습니다 .이 기능에는 높은 대역폭이 필요 합니다. 

## <a name="nfs-30-and-the-hierarchical-namespace"></a>NFS 3.0 및 계층 구조 네임 스페이스

NFS 3.0 프로토콜 지원에는 계층 구조 네임 스페이스에 대 한 blob을 구성 해야 합니다. 저장소 계정을 만들 때 계층적 네임 스페이스를 사용 하도록 설정할 수 있습니다. 계층적 네임 스페이스를 사용 하는 기능은 Azure Data Lake Storage Gen2에서 도입 되었습니다. 컴퓨터의 파일 시스템을 구성 하는 것과 동일한 방식으로 개체 (파일)를 디렉터리 및 하위 디렉터리의 계층 구조로 구성 합니다.  계층적 네임 스페이스는 선형으로 확장 되며 데이터 용량 또는 성능을 저하 하지 않습니다. 계층 구조 네임 스페이스에서 다른 프로토콜이 확장 됩니다. NFS 3.0 프로토콜은 이러한 사용 가능한 프로토콜 중 하나입니다.   

> [!div class="mx-imgBorder"]
> ![계층 구조 네임 스페이스](./media/network-protocol-support/hierarchical-namespace-and-nfs-support.png)
  
## <a name="data-stored-as-block-blobs"></a>블록 blob으로 저장 된 데이터

NFS 3.0 프로토콜 지원을 사용 하도록 설정 하면 저장소 계정의 모든 데이터가 블록 blob으로 저장 됩니다. 블록 blob은 많은 양의 읽기-대량 데이터를 효율적으로 처리 하도록 최적화 되어 있습니다. 블록 blob은 블록으로 구성 됩니다. 각 블록은 블록 ID로 식별 됩니다. 블록 blob은 최대 5만 블록을 포함할 수 있습니다. 블록 blob의 각 블록은 계정에서 사용 하는 서비스 버전에 허용 되는 최대 크기까지 다른 크기를 사용할 수 있습니다.

응용 프로그램에서 NFS 3.0 프로토콜을 사용 하 여 요청을 수행 하는 경우 해당 요청은 블록 blob 작업의 조합으로 변환 됩니다. 예를 들어 NFS 3.0에서 읽은 RPC (원격 프로시저 호출) 요청은 [Blob 가져오기](/rest/api/storageservices/get-blob) 작업으로 변환 됩니다. NFS 3.0 쓰기 RPC 요청은 [Get Block list](/rest/api/storageservices/get-block-list), [Put Block](/rest/api/storageservices/put-block)및 [put 블록 목록의](/rest/api/storageservices/put-block-list)조합으로 변환 됩니다.

## <a name="general-workflow-mounting-a-storage-account-container"></a>일반 워크플로: 저장소 계정 컨테이너 탑재

Linux 클라이언트는 Azure VM (가상 머신) 또는 온-프레미스 컴퓨터에서 Blob 저장소에 컨테이너를 탑재할 수 있습니다. 저장소 계정 컨테이너를 탑재 하려면 다음 작업을 수행 해야 합니다.

1. 구독과 함께 NFS 3.0 프로토콜 기능을 등록 합니다.

2. 기능이 등록 되어 있는지 확인 합니다.

3. Azure Virtual Network (VNet)를 만듭니다.

4. 네트워크 보안을 구성 합니다.

5. VNet의 트래픽만 허용 하는 저장소 계정을 만들고 구성 합니다.

6. 저장소 계정에서 컨테이너를 만듭니다.

7. 컨테이너를 탑재 합니다.

단계별 지침은 [NFS (네트워크 파일 시스템) 3.0 프로토콜을 사용 하 여 Blob Storage 탑재 (미리 보기)](network-file-system-protocol-support-how-to.md)를 참조 하세요.

> [!IMPORTANT]
> 이러한 작업을 순서 대로 완료 하는 것이 중요 합니다. 계정에서 NFS 3.0 프로토콜을 사용 하도록 설정 하기 전에 만든 컨테이너를 탑재할 수 없습니다. 또한 계정에서 NFS 3.0 프로토콜을 사용 하도록 설정한 후에는 사용 하지 않도록 설정할 수 없습니다.

## <a name="network-security"></a>네트워크 보안

저장소 계정은 VNet 내에 포함 되어야 합니다. VNet을 통해 클라이언트는 저장소 계정에 안전 하 게 연결할 수 있습니다. 계정에서 데이터를 보호 하는 유일한 방법은 VNet 및 기타 네트워크 보안 설정을 사용 하는 것입니다. 계정 키 인증, AD (Azure Active Directory) 보안 및 Acl (액세스 제어 목록)을 포함 하 여 데이터를 보호 하는 데 사용 되는 다른 도구는 NFS 3.0 프로토콜 지원이 설정 된 계정에서 아직 지원 되지 않습니다. 

자세히 알아보려면 [Blob 저장소에 대 한 네트워크 보안 권장 사항](security-recommendations.md#networking)을 참조 하세요.

## <a name="supported-network-connections"></a>지원 되는 네트워크 연결

클라이언트는 공용 또는 [개인 끝점](../common/storage-private-endpoints.md)을 통해 연결할 수 있으며 다음 네트워크 위치 중 하나에서 연결할 수 있습니다.

- 저장소 계정에 대해 구성 하는 VNet입니다. 

  이 문서에서는 VNet을 *기본 vnet* 으로 지칭 합니다. 자세히 알아보려면 [가상 네트워크에서 액세스 권한 부여](../common/storage-network-security.md#grant-access-from-a-virtual-network)를 참조 하세요.

- 기본 VNet과 동일한 지역에 있는 피어 링 VNet입니다.

  이 피어 링 VNet에 대 한 액세스를 허용 하도록 저장소 계정을 구성 해야 합니다. 자세히 알아보려면 [가상 네트워크에서 액세스 권한 부여](../common/storage-network-security.md#grant-access-from-a-virtual-network)를 참조 하세요.

- [VPN Gateway](../../vpn-gateway/vpn-gateway-about-vpngateways.md) 또는 [express 경로 게이트웨이](../../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md)를 사용 하 여 기본 VNet에 연결 된 온-프레미스 네트워크입니다. 

  자세히 알아보려면 [온-프레미스 네트워크에서 액세스 구성](../common/storage-network-security.md#configuring-access-from-on-premises-networks)을 참조 하세요.

- 피어 링 네트워크에 연결 된 온-프레미스 네트워크

  [게이트웨이 전송](/azure/architecture/reference-architectures/hybrid-networking/vnet-peering#gateway-transit)과 함께 [VPN Gateway](../../vpn-gateway/vpn-gateway-about-vpngateways.md) 또는 [express 경로 게이트웨이](../../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md) 를 사용 하 여이 작업을 수행할 수 있습니다. 

> [!IMPORTANT]
> 온-프레미스 네트워크에서 연결 하는 경우 클라이언트가 포트 111 및 2048를 통해 나가는 통신을 허용 하는지 확인 합니다. NFS 3.0 프로토콜은 이러한 포트를 사용 합니다.

## <a name="azure-storage-features-not-yet-supported"></a>아직 지원 되지 않는 Azure Storage 기능

계정에서 NFS 3.0 프로토콜을 사용 하도록 설정 하는 경우 다음 Azure Storage 기능이 지원 되지 않습니다. 

- AD (Azure Active Directory) 보안

- POSIX와 유사한 Acl (access control 목록)

- 기존 저장소 계정에서 NFS 3.0 지원을 사용 하도록 설정 하는 기능

- 저장소 계정에서 NFS 3.0 지원을 사용 하지 않도록 설정 하는 기능 (설정 후)

- REST Api 또는 Sdk를 사용 하 여 blob에 쓸 수 있습니다. 
  
## <a name="nfs-30-features-not-yet-supported"></a>NFS 3.0 기능이 아직 지원 되지 않음

다음 NFS 3.0 기능은 아직 지원 되지 않습니다.

- UDP를 통한 NFS 3.0. TCP를 통한 NFS 3.0만 지원 됩니다.

- NLM (네트워크 잠금 관리자)를 사용 하 여 파일을 잠급니다. 탑재 명령은 매개 변수를 포함 해야 합니다 `-o nolock` .

- 하위 디렉터리를 탑재 합니다. 루트 디렉터리 (컨테이너)만 탑재할 수 있습니다.

- 탑재 목록 (예: 명령 사용 `showmount -a` )

- 내보내기 나열 (예: 명령 사용 `showmount -e` )

- 하드 링크

- 컨테이너를 읽기 전용으로 내보내기

## <a name="nfs-30-clients-not-yet-supported"></a>NFS 3.0 클라이언트는 아직 지원 되지 않음

다음 NFS 3.0 클라이언트는 아직 지원 되지 않습니다.

- NFS 용 Windows 클라이언트

## <a name="pricing"></a>가격 책정

미리 보기 중에 저장소 계정에 저장 된 데이터는 월별 GB 당 blob 저장소 요금이 청구 되는 것과 동일한 용량 속도로 청구 됩니다. 

미리 보기 동안에는 트랜잭션이 청구 되지 않습니다. 트랜잭션에 대 한 가격은 변경 될 수 있으며 일반 공급 될 때 결정 됩니다.

## <a name="next-steps"></a>다음 단계

- 시작 하려면 [NFS (네트워크 파일 시스템) 3.0 프로토콜을 사용 하 여 Blob 저장소 탑재 (미리 보기)](network-file-system-protocol-support-how-to.md)를 참조 하세요.

- 성능을 최적화 하려면 [Azure Blob storage (미리 보기)에서 NFS (네트워크 파일 시스템) 3.0 성능 고려 사항을](network-file-system-protocol-support-performance.md)참조 하세요.
