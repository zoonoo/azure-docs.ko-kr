---
title: Azure NetApp 파일에 대 한 Faq | Microsoft Docs
description: Azure NetApp 파일에 대 한 자주 묻는 질문을 답변 합니다.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/14/2019
ms.author: b-juche
ms.openlocfilehash: 6f1ca3398678b59a81e5c22b51b36a1f5505d4c2
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "65806388"
---
# <a name="faqs-about-azure-netapp-files"></a>Azure NetApp 파일에 대 한 Faq

이 문서는 Azure NetApp 파일에 대 한 질문과 대답 (Faq)을 답변합니다. 

## <a name="networking-faqs"></a>네트워킹 Faq

### <a name="does-the-nfs-data-path-go-over-the-internet"></a>NFS 데이터 경로 인터넷을 통해 이동?  

아니요. NFS 데이터 경로 인터넷을 통해 이동 하지 않습니다. Azure NetApp 파일은 서비스를 사용할 수 있는 Azure Virtual Network (VNet)에 배포 되는 기본 Azure 서비스입니다. Azure NetApp 위임 된 서브넷을 사용 하 여 파일과 VNet에 직접 네트워크 인터페이스를 프로 비전 합니다. 

참조 [NetApp Azure Files에 대 한 지침 계획 네트워크](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-network-topologies) 세부 정보에 대 한 합니다.  

### <a name="can-i-connect-a-vnet-that-i-already-created-to-the-azure-netapp-files-service"></a>Azure NetApp 파일 서비스를 이미 만든 VNet을 연결할 수 있습니까?

예, 서비스 사용자가 만든 Vnet을 연결할 수 있습니다. 

참조 [NetApp Azure Files에 대 한 지침 계획 네트워크](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-network-topologies) 세부 정보에 대 한 합니다.  

### <a name="can-i-mount-an-nfs-volume-of-azure-netapp-files-using-dns-fqdn-name"></a>DNS FQDN 이름을 사용 하 여 Azure NetApp 파일 NFS 볼륨을 탑재할 수 있나요?

예, 있습니다 수 필요한 DNS 항목을 만든 경우. Azure NetApp 파일 프로 비전된 된 볼륨에 대 한 서비스 IP를 제공합니다. 

> [!NOTE] 
> 필요에 따라 azure NetApp 파일 서비스에 대 한 추가 Ip를 배포할 수 있습니다.  DNS 항목을 정기적으로 업데이트 해야 합니다.

## <a name="security-faqs"></a>보안 FAQ

### <a name="can-the-network-traffic-between-the-azure-vm-and-the-storage-be-encrypted"></a>Azure VM 및 저장소 간의 네트워크 트래픽을 암호화할 수 있습니다.

데이터 트래픽 (NetApp Azure Files 볼륨 NFSv3 또는 SMBv3 클라이언트에서 트래픽) 암호화 되지 않습니다. 그러나 Azure NetApp 파일 (SMB 또는 NFS 클라이언트를 실행) 하는 Azure VM에서 트래픽을 안전 하 게 다른 VM에 Azure VM 트래픽을 유지 됩니다. 이 트래픽은 Azure 데이터 센터 네트워크에 로컬입니다. 

### <a name="can-the-storage-be-encrypted-at-rest"></a>저장소는 미사용 시 암호화할 수 있습니다?

모든 Azure NetApp 파일 볼륨은 FIPS 140-2 표준을 사용 하 여 암호화 됩니다. 모든 키는 Azure NetApp 파일 서비스에서 관리 됩니다. 

### <a name="how-are-encryption-keys-managed"></a>암호화 키 관리 하는 방법 

Azure NetApp 파일에 대 한 키 관리 서비스에 의해 처리 됩니다.  현재 사용자가 관리 키 (Bring Your 고유 키)는 지원 되지 않습니다.

### <a name="can-i-configure-the-nfs-export-policy-rules-to-control-access-to-the-azure-netapp-files-service-mount-target"></a>Azure NetApp 파일 서비스 탑재 대상에 대 한 액세스를 제어 하려면 NFS 내보내기 정책 규칙을 구성할 수 있나요?


예, 단일 NFS 내보내기 정책에서 최대 5 개의 규칙을 구성할 수 있습니다.

### <a name="does-azure-netapp-files-support-network-security-groups"></a>Azure NetApp Files 네트워크 보안 그룹을 지원 하나요?

아니요, 현재 적용할 수 없습니다 네트워크 보안 그룹 Azure NetApp 파일 또는 서비스에서 만든 네트워크 인터페이스의 위임 된 서브넷입니다.

### <a name="can-i-use-azure-iam-with-azure-netapp-files"></a>NetApp Azure Files를 사용 하 여 Azure IAM을 사용할 수 있나요?

예, Azure NetApp Files Azure IAM을 사용 하 여 RBAC 기능을 지원합니다.

## <a name="performance-faqs"></a>성능 Faq

### <a name="what-should-i-do-to-optimize-or-tune-azure-netapp-files-performance"></a>최적화 하거나 Azure NetApp 파일 성능을 조정 하려면 어떻게 해야 합니까?

성능 요구 사항에 따라 다음 작업을 수행할 수 있습니다. 
- 가상 머신에 적절 하 게 크기가 있는지 확인 합니다.
- VM에 대 한 가속된 네트워킹을 사용 합니다.
- 원하는 서비스 수준 및 용량 풀의 크기를 선택 합니다.
- 용량 및 성능에 대 한 원하는 할당량 크기를 사용 하 여 볼륨을 만듭니다.

### <a name="how-do-i-convert-throughput-based-service-levels-of-azure-netapp-files-to-iops"></a>Iops 처리량에 따라 서비스 수준의 Azure NetApp 파일을 변환 하는 방법

다음 수식을 사용 하 여 iops MB/s를 변환할 수 있습니다.  

`IOPS = (MBps Throughput / KB per IO) * 1024`

### <a name="how-do-i-change-the-service-level-of-a-volume"></a>볼륨의 서비스 수준을 변경 하려면 어떻게 해야 하나요?

볼륨의 서비스 수준 변경은 현재 지원 되지 않습니다.

### <a name="how-do-i-monitor-azure-netapp-files-performance"></a>Azure NetApp 파일 성능을 모니터링 하려면 어떻게 해야 하나요?

Azure NetApp 파일에는 볼륨 성능 메트릭을 제공합니다. 또한 Azure NetApp 파일에 대 한 사용 메트릭을 모니터링 하는 것에 대 한 Azure Monitor를 사용할 수 있습니다.  참조 [NetApp Azure Files에 대 한 메트릭을](azure-netapp-files-metrics.md) Azure NetApp 파일에 대 한 성능 메트릭 목록에 대 한 합니다.

## <a name="nfs-faqs"></a>NFS Faq

### <a name="i-want-to-have-a-volume-mounted-automatically-when-an-azure-vm-is-started-or-rebooted--how-do-i-configure-my-host-for-persistent-nfs-volumes"></a>Azure VM을 시작 하거나 다시 부팅할 때 자동으로 탑재 된 볼륨이 생성 하려고 합니다.  영구 NFS 볼륨에 대 한 호스트를 구성 하려면 어떻게 해야 하나요?

NFS 볼륨을 자동으로 VM 시작 또는 다시 부팅 시 탑재에 대 한 항목을 추가 합니다 `/etc/fstab` 호스트 파일에 있습니다. 

예: `$ANFIP:/$FILEPATH      /$MOUNTPOINT    nfs bg,rw,hard,noatime,nolock,rsize=65536,wsize=65536,vers=3,tcp,_netdev 0 0`

- $ANFIP  
    볼륨 속성 블레이드에서 찾을 NetApp Azure Files 볼륨의 IP 주소
- $FILEPATH  
    NetApp Azure Files 볼륨의 내보내기 경로
- $MOUNTPOINT  
    NFS 내보내기 탑재 하는 데 사용 하는 Linux 호스트에서 만든 디렉터리

### <a name="why-does-the-df-command-on-nfs-client-not-show-the-provisioned-volume-size"></a>왜 DF 명령 NFS 클라이언트에서 표시 되지 않습니다 프로 비전 된 볼륨 크기는?

DF에서 보고 하는 볼륨 크기는 최대 크기가 NetApp Azure Files 볼륨 증가할 수 있습니다. DF 명령 NetApp Azure Files 볼륨의 크기가 할당량 또는 볼륨의 크기를 반영 합니다.  Azure portal 또는 API를 통해 Azure NetApp 파일 볼륨 크기 또는 할당량을 가져올 수 있습니다.

### <a name="what-nfs-version-does-azure-netapp-files-support"></a>Azure NetApp Files NFS 버전을 지원 하나요?

현재 azure NetApp 파일 NFSv3를 지원합니다.

### <a name="how-do-i-enable-root-squashing"></a>루트 squash 병합을 어떻게 사용 하나요?

루트 압축 (squash) 현재 지원 되지 않습니다.

## <a name="smb-faqs"></a>SMB Faq

### <a name="does-azure-netapp-files-support-azure-active-directory"></a>Azure Active Directory를 Azure NetApp Files 지원 하나요?

아니요, 지원 하지 않습니다.  NetApp 파일을 azure Active Directory Domain Services (AD에 자신의 항목만 표시), NetApp Azure Files를 사용 하 여 기존 Active Directory 도메인 컨트롤러를 사용할 수 있는 지원 합니다. 도메인 컨트롤러 가상 컴퓨터 또는 ExpressRoute 통해 온-프레미스에서 Azure에 있을 수 있습니다.

### <a name="is-an-active-directory-connection-required-for-smb-access"></a>SMB 액세스에 필요한 Active Directory 연결 되어 있습니까? 

예, SMB 볼륨을 배포 하기 전에 Active Directory 연결을 만들어야 합니다. 지정 된 도메인 컨트롤러는 성공적인 연결에 대 한 Azure NetApp 파일의 위임 된 서브넷에서 액세스할 수 있어야 합니다.  참조 [SMB 볼륨을 만들](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-volumes#create-an-smb-volume) 세부 정보에 대 한 합니다. 

### <a name="how-many-active-directory-connections-are-supported"></a>Active Directory 연결 지원 되나요?

현재 NetApp 파일을 azure 구독 당 하나의 Active Directory 연결을 지원합니다. 또한 Active Directory 연결 된 단일 NetApp 계정과 관련이; 계정에서 공유 되지 않습니다. 

### <a name="what-versions-of-windows-ad-are-supported"></a>어떤 버전의 Windows AD 지원 되나요?

NetApp 파일을 azure Active Directory Domain Services의 Windows Server 2016 2008r2SP1 버전을 지원합니다.

## <a name="capacity-management-faqs"></a>용량 관리 Faq

### <a name="how-do-i-monitor-usage-for-capacity-pool-and-volume-of-azure-netapp-files"></a>용량 풀 및 Azure NetApp 파일의 볼륨에 대 한 사용량을 모니터링 하려면 어떻게 해야 하나요? 

NetApp 파일을 azure에 용량 풀 및 볼륨 사용 메트릭을 제공합니다. 또한 Azure NetApp 파일에 대 한 사용량을 모니터링 하려면 Azure Monitor를 사용할 수 있습니다. 참조 [NetApp Azure Files에 대 한 메트릭을](azure-netapp-files-metrics.md) 세부 정보에 대 한 합니다. 

### <a name="can-i-manage-azure-netapp-files-through-azure-storage-explorer"></a>Azure Storage 탐색기를 통해 Azure NetApp 파일을 관리할 수 있나요?

아니요. Azure Storage 탐색기에서 azure NetApp 파일 지원 되지 않습니다.

## <a name="data-migration-and-protection-faqs"></a>데이터 마이그레이션 및 보호 Faq

### <a name="how-do-i-migrate-data-to-azure-netapp-files"></a>데이터를 Azure NetApp Files로 마이그레이션하려면 어떻게 하나요?
NetApp의 azure Files는 SMB 및 NFS 볼륨을 제공합니다.  데이터 서비스를 마이그레이션하려면 모든 파일 복사 도구를 사용할 수 있습니다. 

NetApp SaaS 기반 솔루션을 제공 [NetApp 클라우드 동기화](https://cloud.netapp.com/cloud-sync-service)합니다.  솔루션을 사용 하면 NFS를 복제할 수 있습니다 또는 SMB 데이터를 Azure NetApp 파일 NFS 내보내기 또는 SMB 공유 합니다. 

또한 데이터를 복사 하는 다양 한 무료 도구를 사용할 수 있습니다. NFS를 사용할 수 있습니다 작업 도구와 같은 [rsync](https://rsync.samba.org/examples.html) 를 복사 하 여 Azure NetApp Files 볼륨에 원본 데이터를 동기화 합니다. SMB에 대 한 워크 로드를 사용할 수 있습니다 [robocopy](https://docs.microsoft.com/windows-server/administration/windows-commands/robocopy) 동일한 방식으로 합니다.  이러한 도구 파일 또는 폴더 권한을 복제할 수도 있습니다. 

데이터 마이그레이션 온-프레미스에서 Azure NetApp 파일에 대 한 요구 사항은 다음과 같습니다. 

- 대상 Azure 지역에서에서 사용할 수 있는 Azure NetApp 파일 확인 합니다.
- 원본 및 Azure NetApp 파일 대상 볼륨 IP 주소 간의 네트워크 연결의 유효성을 검사 합니다. ExpressRoute를 통해 온-프레미스와 Azure NetApp 파일 서비스 간에 데이터 전송을 사용할 수 있습니다.
- 대상 Azure NetApp 파일 볼륨을 만듭니다.
- 기본 파일 복사 도구를 사용 하 여 대상 볼륨에 원본 데이터를 전송 합니다.

### <a name="how-do-i-create-a-copy-of-an-azure-netapp-files-volume-in-another-azure-region"></a>다른 Azure 지역의 Azure NetApp 파일 볼륨의 복사본 만들기
    
NetApp의 azure Files는 SMB 및 NFS 볼륨을 제공합니다.  Azure 지역 간에 데이터를 복제 하는 파일 기반 복사 도구를 사용할 수 있습니다. 

NetApp 기반 SaaS 솔루션을 제공 [NetApp 클라우드 동기화](https://cloud.netapp.com/cloud-sync-service)합니다.  솔루션을 사용 하면 NFS를 복제할 수 있습니다 또는 SMB 데이터를 Azure NetApp 파일 NFS 내보내기 또는 SMB 공유 합니다. 

또한 데이터를 복사 하는 다양 한 무료 도구를 사용할 수 있습니다. NFS를 사용할 수 있습니다 작업 도구와 같은 [rsync](https://rsync.samba.org/examples.html) 를 복사 하 여 Azure NetApp Files 볼륨에 원본 데이터를 동기화 합니다. SMB에 대 한 워크 로드를 사용할 수 있습니다 [robocopy](https://docs.microsoft.com/windows-server/administration/windows-commands/robocopy) 동일한 방식으로 합니다.  이러한 도구 파일 또는 폴더 권한을 복제할 수도 있습니다. 

다른 Azure 지역에 Azure NetApp Files 볼륨을 복제 하기 위한 요구 사항은 다음과 같습니다. 
- 대상 Azure 지역에서에서 사용할 수 있는 Azure NetApp 파일 확인 합니다.
- 각 지역 내의 Vnet 간 네트워크 연결의 유효성을 검사 합니다. 현재 전역 Vnet 간에 피어 링도 지원 되지 않습니다.  ExpressRoute 회로 사용 하 여 연결 하거나 S2S VPN 연결을 사용 하 여 Vnet 간의 연결을 설정할 수 있습니다. 
- 대상 Azure NetApp 파일 볼륨을 만듭니다.
- 기본 파일 복사 도구를 사용 하 여 대상 볼륨에 원본 데이터를 전송 합니다.

### <a name="is-migration-with-azure-data-box-supported"></a>지원 되는 Azure Data Box를 사용 하 여 마이그레이션이?

아니요. Azure Data Box Azure NetApp 파일을 현재 지원 하지 않습니다. 

### <a name="is-migration-with-azure-importexport-service-supported"></a>지원 되는 Azure Import/Export 서비스를 사용 하 여 마이그레이션이?

아니요. Azure Import/Export 서비스에서는 Azure NetApp 파일을 현재 지원 하지 않습니다.

## <a name="next-steps"></a>다음 단계  

- [Microsoft Azure ExpressRoute FAQs](https://docs.microsoft.com/azure/expressroute/expressroute-faqs)
- [Microsoft Azure Virtual Network FAQ](https://docs.microsoft.com/azure/virtual-network/virtual-networks-faq)
- [Azure 지원 요청을 만드는 방법](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)
- [Azure Data Box](https://docs.microsoft.com/azure/databox-family/)