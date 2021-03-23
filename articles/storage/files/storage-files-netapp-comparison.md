---
title: Azure Files 및 Azure NetApp Files 비교 | Microsoft Docs
description: Azure Files 및 Azure NetApp Files 비교
author: jeffpatt24
services: storage
ms.service: storage
ms.subservice: files
ms.topic: conceptual
ms.date: 3/19/2021
ms.author: jeffpatt
ms.openlocfilehash: 8ebd667b9f3f4ec1a6db4481e42282b3d8bae97d
ms.sourcegitcommit: 2c1b93301174fccea00798df08e08872f53f669c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/22/2021
ms.locfileid: "104776351"
---
# <a name="azure-files-and-azure-netapp-files-comparison"></a>Azure Files 및 Azure NetApp Files 비교

이 문서에서는 Azure Files와 Azure NetApp Files 비교를 제공 합니다. 

클라우드 파일 저장소가 필요한 대부분의 작업은 Azure Files 또는 Azure NetApp Files에서 잘 작동 합니다. 작업에 가장 적합 한 항목을 확인 하려면이 문서에 제공 된 정보를 검토 하세요. 자세한 내용은 [Azure Files](https://docs.microsoft.com/azure/storage/files/) 및 [Azure NetApp Files](https://docs.microsoft.com/azure/azure-netapp-files/) 설명서를 참조 하세요.

## <a name="features"></a>기능

| 범주 | Azure 파일 | Azure NetApp Files |
|---------|-------------------------|---------|
| Description | [Azure Files](https://azure.microsoft.com/services/storage/files/) 는 완전히 관리 되 고 항상 사용 가능한 서비스 이며 내부 데이터 업데이트를 사용 하 여 임의 액세스 작업에 최적화 되어 있습니다.<br><br> Azure Files는 Azure Blob과 같은 다른 서비스와 동일한 Azure storage 플랫폼에서 빌드됩니다. | [Azure NetApp Files](https://azure.microsoft.com/services/netapp/) 는 고급 데이터 관리 기능을 필요로 하는 가장 까다로운 고성능의 대기 시간이 짧은 워크 로드를 처리할 수 있는 완전히 관리 되는 고가용성의 엔터프라이즈급 NAS 서비스입니다. 이 기능을 사용 하면가 없는 "마이그레이션할 수 있고"로 간주 되는 워크 로드를 마이그레이션할 수 있습니다.<br><br>  ANF는 일관 된 Azure 환경을 위해 Azure 데이터 센터 내에서 실행 되는 ONTAP 저장소 OS와 성능 같은 온-프레미스를 사용 하 여 NetApp의 완전 한 기능 금속을 기반으로 합니다. |
| 프로토콜 | Premium<br><ul><li>SMB 2.1, 3.0</li><li>NFS 4.1 (미리 보기)</li></ul><br>Standard<br><ul><li>SMB 2.1, 3.0</li><li>REST</li></ul><br> 자세히 알아보려면 [사용 가능한 파일 공유 프로토콜](https://docs.microsoft.com/azure/storage/files/storage-files-compare-protocols)을 참조 하세요. | 모든 계층<br><ul><li>SMB 1, 2.x, 3(sp3)</li><li>NFS 3.0, 4.1</li><li>이중 프로토콜 액세스 (NFSv3/SMB)</li></ul><br> 자세히 알아보려면 [NFS](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-volumes), [SMB](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-volumes-smb)또는 [이중 프로토콜](https://docs.microsoft.com/azure/azure-netapp-files/create-volumes-dual-protocol) 볼륨을 만드는 방법을 참조 하세요. |
| 지역 가용성 | Premium<br><ul><li>30 개 이상의 지역</li></ul><br>Standard<br><ul><li>모든 지역</li></ul><br> 자세한 내용은 [지역별 사용 가능한 제품](https://azure.microsoft.com/global-infrastructure/services/?products=storage)을 참조하세요. | 모든 계층<br><ul><li>25 개 이상의 지역</li></ul><br> 자세한 내용은 [지역별 사용 가능한 제품](https://azure.microsoft.com/global-infrastructure/services/?products=storage)을 참조하세요. |
| 중복 | Premium<br><ul><li>LRS</li><li>ZRS</li></ul><br>Standard<br><ul><li>LRS</li><li>ZRS</li><li>GRS</li><li>GZRS</li></ul><br> 자세히 알아보려면 [중복성](https://docs.microsoft.com/azure/storage/files/storage-files-planning#redundancy)을 참조 하세요. | 모든 계층<br><ul><li>기본 제공 로컬 HA</li><li>[지역 간 복제](https://docs.microsoft.com/azure/azure-netapp-files/cross-region-replication-introduction)</li></ul> |
| SLA (Service-Level 계약)<br><br> Azure Files 및 Azure NetApp Files에 대 한 Sla는 다르게 계산 됩니다. | [Azure Files에 대 한 SLA](https://azure.microsoft.com/support/legal/sla/storage/) | [Azure NetApp Files에 대 한 SLA](https://azure.microsoft.com/support/legal/sla/netapp) |  
| Identity-Based 인증 및 권한 부여 | SMB<br><ul><li>AD DS(Active Directory Domain Services)</li><li>Azure AD DS(Azure Active Directory Domain Services)</li></ul><br> 확인 기반 인증은 SMB 프로토콜을 사용 하는 경우에만 지원 됩니다. 자세히 알아보려면 [FAQ](https://docs.microsoft.com/azure/storage/files/storage-files-faq#security-authentication-and-access-control)를 참조 하세요. | SMB<br><ul><li>AD DS(Active Directory Domain Services)</li><li>Azure AD DS(Azure Active Directory Domain Services)</li></ul><br> NFS/SMB 이중 프로토콜<ul><li>추가/i s 통합</li></ul><br>NFSv3/NFSv 4.1<ul><li>추가/v s 통합 (출시)</li><li>NFS 확장 그룹 (출시)</li></ul><br> 자세히 알아보려면 [FAQ](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-faqs)를 참조 하세요. |
| 암호화 | SMB<br><ul><li>사용자 또는 Microsoft 관리 키를 사용 하 여 미사용 암호화 (AES 256)</li><li>AES 256 또는 RC4를 사용 하는 Kerberos 암호화-HMAC</li><li>전송 중 암호화</li></ul><br>NFS<br><ul><li>사용자 또는 Microsoft 관리 키를 사용 하 여 미사용 암호화 (AES 256)</li></ul><br>REST<br><ul><li>사용자 또는 Microsoft 관리 키를 사용 하 여 미사용 암호화 (AES 256)</li><li>전송 중 암호화</li></ul><br> 자세히 알아보려면 [보안](https://docs.microsoft.com/azure/storage/files/storage-files-compare-protocols#security)을 참조 하세요. | 모든 프로토콜<br><ul><li>Microsoft에서 관리 하는 키를 사용 하 여 미사용 암호화 (AES 256) </li></ul><br>NFS 4.1<ul><li>AES 256에서 Kerberos를 사용 하 여 전송 암호화</li></ul><br> 자세히 알아보려면 [보안 FAQ](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-faqs#security-faqs)를 참조 하세요. |
| 액세스 옵션 | <ul><li>인터넷</li><li>보안 VNet 액세스</li><li>VPN Gateway</li><li>ExpressRoute</li><li>Azure 파일 동기화</li></ul><br> 자세히 알아보려면 [네트워크 고려 사항](https://docs.microsoft.com/azure/storage/files/storage-files-networking-overview)을 참조 하세요. | <ul><li>보안 VNet 액세스</li><li>VPN Gateway</li><li>ExpressRoute</li><li>[전역 파일 캐시](https://cloud.netapp.com/global-file-cache/azure)</li><li>[HPC Cache](https://docs.microsoft.com/azure/hpc-cache/hpc-cache-overview)</li></ul><br> 자세히 알아보려면 [네트워크 고려 사항](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-network-topologies)을 참조 하세요. |
| 데이터 보호  | <ul><li>증분 스냅숏</li><li>파일/디렉터리 사용자 자동 복원</li><li>새 위치로 복원</li><li>내부 되돌리기</li><li>공유 수준 일시 삭제</li><li>Azure Backup 통합</li></ul><br> 자세한 내용은 [데이터 보호 기능 향상 Azure Files](https://azure.microsoft.com/blog/azure-files-enhances-data-protection-capabilities/)를 참조 하세요. | <ul><li>스냅숏 (255/볼륨)</li><li>파일/디렉터리 사용자 자동 복원</li><li>새 볼륨으로 복원</li><li>내부 되돌리기</li><li>[영역 간 복제](https://docs.microsoft.com/azure/azure-netapp-files/cross-region-replication-introduction) (공개 미리 보기)</li><li>Azure NetApp Files 백업 (미리 보기)</li></ul><br> 자세한 내용은 [Azure NetApp Files 스냅숏이 작동 하는 방법](https://docs.microsoft.com/azure/azure-netapp-files/snapshots-introduction)을 참조 하세요. |
| 마이그레이션 도구  | <ul><li>Azure Data Box</li><li>Azure 파일 동기화</li><li>스토리지 마이그레이션 서비스</li><li>AzCopy</li><li>Robocopy</li></ul><br> 자세히 알아보려면 [Azure 파일 공유로 마이그레이션](https://docs.microsoft.com/azure/storage/files/storage-files-migration-overview)을 참조 하세요. | <ul><li>[전역 파일 캐시](https://cloud.netapp.com/global-file-cache/azure)</li><li>[Cloudsync](https://cloud.netapp.com/cloud-sync-service), [xcp](https://xcp.netapp.com/)</li><li>스토리지 마이그레이션 서비스</li><li>AzCopy</li><li>Robocopy</li><li>응용 프로그램 기반 (예: HSR, Data Guard, AOAG)</li></ul> |
| 계층 | <ul><li>Premium</li><li>트랜잭션 최적화</li><li>핫</li><li>쿨</li></ul><br> 자세히 알아보려면 [저장소 계층](https://docs.microsoft.com/azure/storage/files/storage-files-planning#storage-tiers)을 참조 하세요. | <ul><li>Ultra</li><li>Premium</li><li>Standard</li></ul><br> 모든 계층은 최소 대기 시간 (밀리초)을 제공 합니다.<br><br> 자세히 알아보려면 [서비스 수준](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-service-levels) 및 [성능 고려 사항](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-performance-considerations)을 참조 하세요. |
| 가격 책정 | [Azure Files 가격 책정](https://azure.microsoft.com/pricing/details/storage/files/) | [Azure NetApp Files 가격 책정](https://azure.microsoft.com/pricing/details/netapp/) |

## <a name="scalability-and-performance"></a>확장성 및 성능

| 범주 | Azure 파일 | Azure NetApp Files |
|---------|---------|---------|
| 최소 공유/볼륨 크기 | Premium<br><ul><li>100GiB</li></ul><br>Standard<br><ul><li>GiB 1</li></ul> | 모든 계층<br><ul><li>100 GiB (최소 용량 풀 크기: 4 TiB)</li></ul> |
| 최대 공유/볼륨 크기 | Premium<br><ul><li>100TiB</li></ul><br>Standard<br><ul><li>100TiB</li></ul> | 모든 계층<br><ul><li>100 TiB (500-TiB capacity pool limit)</li></ul><br>Azure NetApp 계정 당 최대 12.5 PiB |
| 최대 공유/볼륨 IOPS | Premium<br><ul><li>최대 10만 개</li></ul><br>Standard<br><ul><li>최대 10k</li></ul> | Ultra 및 Premium<br><ul><li>최대 450k </li></ul><br>Standard<br><ul><li>최대 320k</li></ul> |
| 최대 공유/볼륨 처리량 | Premium<br><ul><li>최대 10 GiB/s</li></ul><br>Standard<br><ul><li>최대 300 MiB/s</li></ul> | Ultra 및 Premium<br><ul><li>최대 4.5 GiB/s</li></ul><br>Standard<br><ul><li>최대 3.2 GiB/s</li></ul> |
| 최대 파일 크기 | Premium<br><ul><li>4TiB</li></ul><br>Standard<br><ul><li>1TiB</li></ul> | 모든 계층<br><ul><li>16TiB</li></ul> |
| 파일당 최대 IOPS | Premium<br><ul><li>최대 8000</li></ul><br>Standard<br><ul><li>1,000</li></ul> | 모든 계층<br><ul><li>최대 볼륨 제한</li></ul> |
| 파일당 최대 처리량 | Premium<br><ul><li>300 MiB/s (SMB 다중 채널을 사용 하는 최대 1 GiB/s)</li></ul><br>Standard<br><ul><li>60 MiB/s</li></ul> | 모든 계층<br><ul><li>최대 볼륨 제한</li></ul> |
| SMB 다중 채널 | 예 ([미리 보기](https://docs.microsoft.com/azure/storage/files/storage-files-smb-multichannel-performance)) | 예 |
| 대기 시간 | 단일 밀리초 최소 대기 시간 (작은 IO의 경우 2ms ~ 3ms) | 최소 밀리초의 최소 대기 시간 (임의 IO에 대 한 1ms <)<br><br>자세히 알아보려면 [성능 벤치 마크](https://docs.microsoft.com/azure/azure-netapp-files/performance-benchmarks-linux)를 참조 하세요. |

확장성 및 성능 목표에 대 한 자세한 내용은 [Azure Files](https://docs.microsoft.com/azure/storage/files/storage-files-scale-targets#azure-files-scale-targets) 및 [Azure NetApp Files](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-resource-limits)를 참조 하세요.

## <a name="next-steps"></a>다음 단계
* [Azure Files 설명서](https://docs.microsoft.com/azure/storage/files/)
* [Azure NetApp Files 설명서](https://docs.microsoft.com/azure/azure-netapp-files/)
