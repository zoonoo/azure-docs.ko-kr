---
title: Azure NetApp 파일에 대한 자주 묻는 질문 | 마이크로 소프트 문서
description: Azure NetApp 파일에 대해 자주 묻는 질문에 대한 답변입니다.
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
ms.date: 04/03/2020
ms.author: b-juche
ms.openlocfilehash: aebc669a90511e48ddd2a7876553948c04b97710
ms.sourcegitcommit: 67addb783644bafce5713e3ed10b7599a1d5c151
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/05/2020
ms.locfileid: "80667821"
---
# <a name="faqs-about-azure-netapp-files"></a>Azure NetApp 파일에 대한 자주 묻는 질문

이 문서에서는 Azure NetApp 파일에 대한 자주 묻는 질문(FAQ)에 대해 답변합니다. 

## <a name="networking-faqs"></a>네트워킹 자주 묻는 질문

### <a name="does-the-nfs-data-path-go-over-the-internet"></a>NFS 데이터 경로가 인터넷을 통해 이동합니까?  

아니요. NFS 데이터 경로는 인터넷을 통해 이동하지 않습니다. Azure NetApp 파일은 서비스를 사용할 수 있는 Azure 가상 네트워크(VNet)에 배포되는 Azure 네이티브 서비스입니다. Azure NetApp 파일은 위임된 서브넷을 사용하고 VNet에서 직접 네트워크 인터페이스를 프로비전합니다. 

자세한 내용은 [Azure NetApp 파일 네트워크 계획에 대한 지침을](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-network-topologies) 참조하십시오.  

### <a name="can-i-connect-a-vnet-that-i-already-created-to-the-azure-netapp-files-service"></a>이미 만든 VNet을 Azure NetApp 파일 서비스에 연결할 수 있습니까?

예. 만든 VNet을 서비스에 연결할 수 있습니다. 

자세한 내용은 [Azure NetApp 파일 네트워크 계획에 대한 지침을](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-network-topologies) 참조하십시오.  

### <a name="can-i-mount-an-nfs-volume-of-azure-netapp-files-using-dns-fqdn-name"></a>DNS FQDN 이름을 사용하여 Azure NetApp 파일의 NFS 볼륨을 탑재할 수 있습니까?

예, 필요한 DNS 항목을 만드는 경우 할 수 있습니다. Azure NetApp 파일은 프로비저닝된 볼륨에 대한 서비스 IP를 제공합니다. 

> [!NOTE] 
> Azure NetApp 파일은 필요에 따라 서비스에 대한 추가 IP를 배포할 수 있습니다.  DNS 항목은 주기적으로 업데이트해야 할 수 있습니다.

## <a name="security-faqs"></a>보안 FAQ

### <a name="can-the-network-traffic-between-the-azure-vm-and-the-storage-be-encrypted"></a>Azure VM과 저장소 간의 네트워크 트래픽을 암호화할 수 있습니까?

데이터 트래픽(NFSv3, NFSv4.1 또는 SMBv3 클라이언트에서 Azure NetApp 파일 볼륨으로의 트래픽)은 암호화되지 않습니다. 그러나 Azure VM(NFS 또는 SMB 클라이언트 실행)에서 Azure NetApp 파일로의 트래픽은 다른 Azure-VM-VM 트래픽만큼 안전합니다. 이 트래픽은 Azure 데이터 센터 네트워크에 로컬입니다. 

### <a name="can-the-storage-be-encrypted-at-rest"></a>저장소를 미사용 으로 암호화할 수 있습니까?

모든 Azure NetApp 파일 볼륨은 FIPS 140-2 표준을 사용하여 암호화됩니다. 모든 키는 Azure NetApp 파일 서비스에서 관리합니다. 

### <a name="how-are-encryption-keys-managed"></a>암호화 키는 어떻게 관리됩니까? 

Azure NetApp 파일에 대한 키 관리는 서비스에서 처리합니다. 고유한 XTS-AES-256 데이터 암호화 키가 각 볼륨에 대해 생성됩니다. 암호화 키 계층 구조는 모든 볼륨 키를 암호화하고 보호하는 데 사용됩니다. 이러한 암호화 키는 암호화되지 않은 형식으로 표시되거나 보고되지 않습니다. 암호화 키는 볼륨이 삭제되면 즉시 삭제됩니다.

Azure 전용 HSM을 사용하는 사용자 관리 키(사용자 고유 키 가져오기)에 대한 지원은 미국 동부, 미국 서부 2 및 미국 중남부 지역에서 제어된 기준으로 사용할 수 있습니다.  에서 액세스를 요청할 **anffeedback@microsoft.com**수 있습니다. 용량을 사용할 수 있게 되면 요청이 승인됩니다.

### <a name="can-i-configure-the-nfs-export-policy-rules-to-control-access-to-the-azure-netapp-files-service-mount-target"></a>Azure NetApp 파일 서비스 마운트 대상에 대한 액세스를 제어하도록 NFS 내보내기 정책 규칙을 구성할 수 있습니까?


예. 단일 NFS 내보내기 정책에서 최대 5개의 규칙을 구성할 수 있습니다.

### <a name="does-azure-netapp-files-support-network-security-groups"></a>Azure NetApp 파일이 네트워크 보안 그룹을 지원합니까?

아니요, 현재 Azure NetApp 파일의 위임된 서브넷 또는 서비스에서 만든 네트워크 인터페이스에 네트워크 보안 그룹을 적용할 수 없습니다.

### <a name="can-i-use-azure-iam-with-azure-netapp-files"></a>Azure NetApp 파일과 함께 Azure IAM을 사용할 수 있습니까?

예. Azure NetApp 파일은 Azure IAM을 통해 RBAC 기능을 지원합니다.

## <a name="performance-faqs"></a>성능 자주 묻는 질문

### <a name="what-should-i-do-to-optimize-or-tune-azure-netapp-files-performance"></a>Azure NetApp 파일 성능을 최적화하거나 조정하려면 어떻게 해야 합니까?

성능 요구 사항에 따라 다음 작업을 수행할 수 있습니다. 
- 가상 컴퓨터의 크기가 적절한지 확인합니다.
- VM에 대한 가속 네트워킹을 활성화합니다.
- 용량 풀에 대해 원하는 서비스 수준과 크기를 선택합니다.
- 용량 및 성능에 대해 원하는 할당량 크기로 볼륨을 만듭니다.

### <a name="how-do-i-convert-throughput-based-service-levels-of-azure-netapp-files-to-iops"></a>Azure NetApp 파일의 처리량 기반 서비스 수준을 IOPS로 변환하려면 어떻게 해야 합니까?

다음 수식을 사용하여 MB/s를 IOPS로 변환할 수 있습니다.  

`IOPS = (MBps Throughput / KB per IO) * 1024`

### <a name="how-do-i-change-the-service-level-of-a-volume"></a>볼륨의 서비스 수준을 변경하려면 어떻게 해야 합니까?

볼륨의 서비스 수준을 변경하는 것은 현재 지원되지 않습니다.

### <a name="how-do-i-monitor-azure-netapp-files-performance"></a>Azure NetApp 파일 성능을 모니터링하려면 어떻게 해야 합니까?

Azure NetApp 파일은 볼륨 성능 메트릭을 제공합니다. Azure NetApp 파일에 대한 사용 메트릭을 모니터링하기 위해 Azure 모니터를 사용할 수도 있습니다.  Azure NetApp 파일의 성능 메트릭 목록은 [Azure NetApp 파일에 대한 메트릭을](azure-netapp-files-metrics.md) 참조하십시오.

## <a name="nfs-faqs"></a>NFS 자주 묻는 질문

### <a name="i-want-to-have-a-volume-mounted-automatically-when-an-azure-vm-is-started-or-rebooted--how-do-i-configure-my-host-for-persistent-nfs-volumes"></a>Azure VM을 시작하거나 재부팅할 때 볼륨을 자동으로 탑재하려고 합니다.  영구 NFS 볼륨에 대해 호스트를 구성하려면 어떻게 해야 합니까?

NFS 볼륨이 VM 시작 또는 재부팅 시 자동으로 마운트하려면 `/etc/fstab` 호스트의 파일에 항목을 추가합니다. 

자세한 내용은 [Windows 또는 Linux 가상 컴퓨터용 볼륨 마운트 또는 마운트 해제를](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md) 참조하십시오.  

### <a name="why-does-the-df-command-on-nfs-client-not-show-the-provisioned-volume-size"></a>NFS 클라이언트의 DF 명령에 프로비저닝된 볼륨 크기가 표시되지 않는 이유는 무엇입니까?

DF에 보고된 볼륨 크기는 Azure NetApp 파일 볼륨이 증가할 수 있는 최대 크기입니다. DF 명령의 Azure NetApp 파일 볼륨 크기는 볼륨의 할당량 또는 크기를 반영하지 않습니다.  Azure Portal 또는 API를 통해 Azure NetApp 파일 볼륨 크기 또는 할당량을 얻을 수 있습니다.

### <a name="what-nfs-version-does-azure-netapp-files-support"></a>Azure NetApp 파일은 어떤 NFS 버전을 지원합니까?

Azure NetApp 파일은 NFSv3 및 NFSv4.1을 지원합니다. NFS 버전을 사용하여 [볼륨을 만들](azure-netapp-files-create-volumes.md) 수 있습니다. 

### <a name="how-do-i-enable-root-squashing"></a>루트 스쿼시를 사용하려면 어떻게 해야 합니까?

루트 스쿼시는 현재 지원되지 않습니다.

## <a name="smb-faqs"></a>중소기업 자주 묻는 질문

### <a name="is-an-active-directory-connection-required-for-smb-access"></a>SMB 액세스에 Active Directory 연결이 필요합니까? 

예. SMB 볼륨을 배포하기 전에 Active Directory 연결을 만들어야 합니다. 성공적인 연결을 위해서는 Azure NetApp 파일의 위임된 서브넷에서 지정된 도메인 컨트롤러에 액세스할 수 있어야 합니다.  자세한 내용은 [SMB 볼륨 만들기를](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-volumes-smb) 참조하십시오. 

### <a name="how-many-active-directory-connections-are-supported"></a>지원되는 Active Directory 연결수는 몇 개입니까?

Azure NetApp 파일은 AD 연결이 다른 NetApp 계정에 있더라도 단일 *리전에서*여러 Active Directory(AD) 연결을 지원하지 않습니다. 그러나 AD 연결이 다른 지역에 있는 한 단일 *구독에*여러 AD 연결을 가질 수 있습니다. 단일 리전에서 여러 개의 AD 연결이 필요한 경우 별도의 구독을 사용하여 이를 수행할 수 있습니다. 

AD 연결은 NetApp 계정당 구성됩니다. AD 연결은 생성된 NetApp 계정을 통해서만 볼 수 있습니다.

### <a name="does-azure-netapp-files-support-azure-active-directory"></a>Azure NetApp 파일이 Azure 활성 디렉터리를 지원합니까? 

[Azure Active Directory(AD) 도메인 서비스와](https://docs.microsoft.com/azure/active-directory-domain-services/overview) [AD DS(활성 디렉터리 도메인 서비스)가](https://docs.microsoft.com/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview) 모두 지원됩니다. Azure NetApp 파일에서 기존 Active Directory 도메인 컨트롤러를 사용할 수 있습니다. 도메인 컨트롤러는 Azure에서 가상 컴퓨터로 상주하거나 ExpressRoute 또는 S2S VPN을 통해 온프레미스에 있을 수 있습니다. 현재 Azure NetApp 파일은 [Azure Active Directory에](https://azure.microsoft.com/resources/videos/azure-active-directory-overview/) 대한 AD 조인을 지원하지 않습니다.

Azure Active Directory 도메인 서비스와 함께 Azure NetApp 파일을 사용하는 `OU=AADDC Computers` 경우 조직 단위 경로는 NetApp 계정에 대해 Active Directory를 구성할 때입니다.

### <a name="what-versions-of-windows-server-active-directory-are-supported"></a>어떤 버전의 Windows 서버 활성 디렉터리가 지원됩니까?

Azure NetApp 파일은 Windows Server 2008r2SP1-2019 버전의 Active Directory 도메인 서비스를 지원합니다.

### <a name="why-does-the-available-space-on-my-smb-client-not-show-the-provisioned-size"></a>SMB 클라이언트의 사용 가능한 공간에 프로비저닝된 크기가 표시되지 않는 이유는 무엇입니까?

SMB 클라이언트에서 보고하는 볼륨 크기는 Azure NetApp 파일 볼륨이 증가할 수 있는 최대 크기입니다. SMB 클라이언트에 표시된 Azure NetApp 파일 볼륨의 크기는 볼륨의 할당량 또는 크기를 반영하지 않습니다. Azure Portal 또는 API를 통해 Azure NetApp 파일 볼륨 크기 또는 할당량을 얻을 수 있습니다.

<!--
### Does Azure NetApp Files support Kerberos encryption?

Yes, by default, Azure NetApp Files supports both AES-128 and AES-256 encryption for traffic between the service and the targeted Active Directory domain controllers. See [Create an SMB volume for Azure NetApp Files](azure-netapp-files-create-volumes-smb.md) for requirements. 
-->

<!--
### Does Azure NetApp Files support LDAP signing? 

Yes, Azure NetApp Files supports LDAP signing by default. This functionality enables secure LDAP lookups between the Azure NetApp Files service and the user-specified [Active Directory Domain Services domain controllers](https://docs.microsoft.com/windows/win32/ad/active-directory-domain-services). For more information, see [ADV190023 | Microsoft Guidance for Enabling LDAP Channel Binding and LDAP Signing](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/ADV190023).
--> 

## <a name="capacity-management-faqs"></a>용량 관리 자주 묻는 질문

### <a name="how-do-i-monitor-usage-for-capacity-pool-and-volume-of-azure-netapp-files"></a>Azure NetApp 파일의 용량 풀 및 볼륨에 대한 사용량을 모니터링하려면 어떻게 해야 합니까? 

Azure NetApp 파일은 용량 풀 및 볼륨 사용 메트릭을 제공합니다. Azure 모니터를 사용하여 Azure NetApp 파일의 사용량을 모니터링할 수도 있습니다. 자세한 내용은 [Azure NetApp 파일에 대한 메트릭을](azure-netapp-files-metrics.md) 참조하십시오. 

### <a name="can-i-manage-azure-netapp-files-through-azure-storage-explorer"></a>Azure 저장소 탐색기를 통해 Azure NetApp 파일을 관리할 수 있습니까?

아니요. Azure NetApp 파일은 Azure 저장소 탐색기에서 지원되지 않습니다.

### <a name="how-do-i-determine-if-a-directory-is-approaching-the-limit-size"></a>디렉터리가 제한 크기에 가까워지고 있는지 확인하려면 어떻게 해야 합니까?

클라이언트의 `stat` 명령을 사용하여 디렉터리가 최대 크기 제한(320MB)에 가까워지고 있는지 확인할 수 있습니다.

320MB 디렉터리에서 블록 수는 655360이며 각 블록 크기는 512바이트입니다.  (즉, 320x1024x1024/512.)  

예제:

    [makam@cycrh6rtp07 ~]$ stat bin
    File: 'bin'
    Size: 4096            Blocks: 8          IO Block: 65536  directory

    [makam@cycrh6rtp07 ~]$ stat tmp
    File: 'tmp'
    Size: 12288           Blocks: 24         IO Block: 65536  directory
 
    [makam@cycrh6rtp07 ~]$ stat tmp1
    File: 'tmp1'
    Size: 4096            Blocks: 8          IO Block: 65536  directory

## <a name="data-migration-and-protection-faqs"></a>데이터 마이그레이션 및 보호 FAQ

### <a name="how-do-i-migrate-data-to-azure-netapp-files"></a>Azure NetApp 파일로 데이터를 마이그레이션하려면 어떻게 해야 합니까?
Azure NetApp 파일은 NFS 및 SMB 볼륨을 제공합니다.  모든 파일 기반 복사 도구를 사용하여 데이터를 서비스로 마이그레이션할 수 있습니다. 

넷앱은 SaaS 기반 솔루션인 [넷앱 클라우드 동기화를](https://cloud.netapp.com/cloud-sync-service)제공합니다.  이 솔루션을 사용하면 NFS 또는 SMB 데이터를 Azure NetApp 파일 NFS 내보내기 또는 SMB 공유에 복제할 수 있습니다. 

다양한 무료 도구를 사용하여 데이터를 복사할 수도 있습니다. NFS의 경우 [rsync와](https://rsync.samba.org/examples.html) 같은 워크로드 도구를 사용하여 원본 데이터를 Azure NetApp 파일 볼륨으로 복사하고 동기화할 수 있습니다. SMB의 경우 동일한 방식으로 워크로드 [로보카를](https://docs.microsoft.com/windows-server/administration/windows-commands/robocopy) 사용할 수 있습니다.  이러한 도구는 파일 또는 폴더 권한을 복제할 수도 있습니다. 

온-프레미스에서 Azure NetApp 파일로의 데이터 마이그레이션에 대한 요구 사항은 다음과 같습니다. 

- 대상 Azure 지역에서 Azure NetApp 파일을 사용할 수 있는지 확인합니다.
- 원본과 Azure NetApp 파일 대상 볼륨 IP 주소 간의 네트워크 연결을 검증합니다. 온-프레미스와 Azure NetApp 파일 서비스 간의 데이터 전송은 ExpressRoute를 통해 지원됩니다.
- 대상 Azure NetApp 파일 볼륨을 만듭니다.
- 기본 파일 복사 도구를 사용하여 원본 데이터를 대상 볼륨으로 전송합니다.

### <a name="how-do-i-create-a-copy-of-an-azure-netapp-files-volume-in-another-azure-region"></a>다른 Azure 지역에서 Azure NetApp 파일 볼륨의 복사본을 만들려면 어떻게 해야 합니까?
    
Azure NetApp 파일은 NFS 및 SMB 볼륨을 제공합니다.  모든 파일 기반 복사 도구를 사용하여 Azure 지역 간에 데이터를 복제할 수 있습니다. 

넷앱은 SaaS 기반 솔루션인 [넷앱 클라우드 동기화를](https://cloud.netapp.com/cloud-sync-service)제공합니다.  이 솔루션을 사용하면 NFS 또는 SMB 데이터를 Azure NetApp 파일 NFS 내보내기 또는 SMB 공유에 복제할 수 있습니다. 

다양한 무료 도구를 사용하여 데이터를 복사할 수도 있습니다. NFS의 경우 [rsync와](https://rsync.samba.org/examples.html) 같은 워크로드 도구를 사용하여 원본 데이터를 Azure NetApp 파일 볼륨으로 복사하고 동기화할 수 있습니다. SMB의 경우 동일한 방식으로 워크로드 [로보카를](https://docs.microsoft.com/windows-server/administration/windows-commands/robocopy) 사용할 수 있습니다.  이러한 도구는 파일 또는 폴더 권한을 복제할 수도 있습니다. 

Azure NetApp 파일 볼륨을 다른 Azure 지역으로 복제하기 위한 요구 사항은 다음과 같습니다. 
- 대상 Azure 지역에서 Azure NetApp 파일을 사용할 수 있는지 확인합니다.
- 각 리전의 VNet 간의 네트워크 연결을 검증합니다. 현재 VNet 간의 전역 피어링은 지원되지 않습니다.  ExpressRoute 회로와 연결하거나 S2S VPN 연결을 사용하여 VNet 간의 연결을 설정할 수 있습니다. 
- 대상 Azure NetApp 파일 볼륨을 만듭니다.
- 기본 파일 복사 도구를 사용하여 원본 데이터를 대상 볼륨으로 전송합니다.

### <a name="is-migration-with-azure-data-box-supported"></a>Azure 데이터 상자를 사용하여 마이그레이션이 지원되고 있습니까?

아니요. Azure 데이터 상자는 현재 Azure NetApp 파일을 지원하지 않습니다. 

### <a name="is-migration-with-azure-importexport-service-supported"></a>Azure 가져오기/내보내기 서비스를 사용 하 고 마이그레이션이 지원 되 나요?

아니요. Azure 가져오기/내보내기 서비스는 현재 Azure NetApp 파일을 지원하지 않습니다.

## <a name="next-steps"></a>다음 단계  

- [마이크로소프트 Azure 익스프레스루트 자주 묻는 질문](https://docs.microsoft.com/azure/expressroute/expressroute-faqs)
- [마이크로소프트 Azure 가상 네트워크 자주 묻는 질문](https://docs.microsoft.com/azure/virtual-network/virtual-networks-faq)
- [Azure 지원 요청을 만드는 방법](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)
- [Azure 데이터 상자](https://docs.microsoft.com/azure/databox-family/)
- [Azure NetApp 파일에 대한 SMB 성능에 대한 자주 묻는 질문](azure-netapp-files-smb-performance.md)
