---
title: Azure NetApp 파일(SMB)이 있는 Windows에서 SAP NW용 Azure VM HA | 마이크로 소프트 문서
description: SAP 응용 프로그램에 대한 Azure NetApp 파일(SMB)이 있는 Windows의 Azure VM에서 SAP NetWeaver용 고가용성
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 5e514964-c907-4324-b659-16dd825f6f87
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 10/29/2019
ms.author: radeltch
ms.openlocfilehash: b41db629c5308348f632b3dc51c75822ba361c60
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77591356"
---
# <a name="high-availability-for-sap-netweaver-on-azure-vms-on-windows-with-azure-netapp-filessmb-for-sap-applications"></a>SAP 응용 프로그램에 대한 Azure NetApp 파일(SMB)이 있는 Windows의 Azure VM에서 SAP NetWeaver용 고가용성

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md

[anf-azure-doc]:https://docs.microsoft.com/azure/azure-netapp-files/
[anf-avail-matrix]:https://azure.microsoft.com/global-infrastructure/services/?products=storage&regions=all
[anf-register]:https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-register
[anf-sap-applications-azure]:https://www.netapp.com/us/media/tr-4746.pdf

[2205917]:https://launchpad.support.sap.com/#/notes/2205917
[1944799]:https://launchpad.support.sap.com/#/notes/1944799
[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2191498]:https://launchpad.support.sap.com/#/notes/2191498
[2243692]:https://launchpad.support.sap.com/#/notes/2243692
[1984787]:https://launchpad.support.sap.com/#/notes/1984787
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[1410736]:https://launchpad.support.sap.com/#/notes/1410736

[sap-swcenter]:https://support.sap.com/en/my-support/software-downloads.html

[suse-ha-guide]:https://www.suse.com/products/sles-for-sap/resource-library/sap-best-practices/
[suse-drbd-guide]:https://www.suse.com/documentation/sle-ha-12/singlehtml/book_sleha_techguides/book_sleha_techguides.html
[suse-ha-12sp3-relnotes]:https://www.suse.com/releasenotes/x86_64/SLE-HA/12-SP3/

[template-multisid-xscs]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs-md%2Fazuredeploy.json
[template-converged]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-converged-md%2Fazuredeploy.json
[template-file-server]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-file-server-md%2Fazuredeploy.json

[sap-hana-ha]:sap-hana-high-availability.md
[nfs-ha]:high-availability-guide-suse-nfs.md

이 문서에서는 [Azure NetApp 파일에서](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-introduction/) [SMB를](https://docs.microsoft.com/windows/win32/fileio/microsoft-smb-protocol-and-cifs-protocol-overview) 사용하여 가상 컴퓨터를 배포하고, 구성하고, 클러스터 프레임워크를 설치하고, Windows VM에 가용성이 높은 SAP NetWeaver 7.50 시스템을 설치하는 방법에 대해 설명합니다.  

데이터베이스 계층은 이 문서에서 자세히 다루지 않습니다. Azure [가상 네트워크가](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) 이미 생성되었다고 가정합니다.  

다음 SAP Note 및 문서를 먼저 읽어 보세요.

* [Azure NetApp 파일 설명서][anf-azure-doc] 
* SAP 주 [1928533][1928533], 포함:  
  * SAP 소프트웨어 배포에 지원되는 Azure VM 크기 목록
  * Azure VM 크기에 대한 중요한 용량 정보
  * 지원되는 SAP 소프트웨어 및 운영 체제(OS)와 데이터베이스 조합
  * 마이크로소프트 Azure에서 Windows용 필수 SAP 커널 버전
* SAP Note [2015553][2015553]는 Azure에서 SAP을 지원하는 SAP 소프트웨어 배포에 대한 필수 구성 요소를 나열합니다.
* SAP Note [2178632][2178632]는 Azure에서 SAP에 대해 보고된 모든 모니터링 메트릭에 대한 자세한 정보를 포함하고 있습니다.
* SAP Note [1999351][1999351]은 SAP용 Azure 고급 모니터링 확장을 위한 추가 문제 해결 정보를 포함하고 있습니다.
* SAP Note [2287140은](https://launchpad.support.sap.com/#/notes/2287140) SMB 3.x 프로토콜의 SAP 지원 CA 기능에 대한 필수 구성 조건을 나열합니다.
* SAP Note [2802770은](https://launchpad.support.sap.com/#/notes/2802770) Windows 2012 및 2016에서 느리게 실행되는 SAP 트랜잭션 AL11에 대한 문제 해결 정보를 제공합니다.
* SAP 주 [1911507에는](https://launchpad.support.sap.com/#/notes/1911507) SMB 3.0 프로토콜을 사용 하 여 Windows 서버에서 파일 공유에 대 한 투명 장애 조치 기능에 대 한 정보가 있습니다.
* SAP Note [662452에는](https://launchpad.support.sap.com/#/notes/662452) 데이터 액세스 중에 파일 시스템 성능/오류를 해결하기 위해 권장 사항(8.3 이름 생성 비활성화)이 있습니다.
* [Azure에서 SAP ASCS/SCS 인스턴스의 Windows 장애 조치(Failover) 클러스터 및 파일 공유에 SAP NetWeaver 고가용성 설치](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-installation-wsfc-file-share) 
* [SAP NetWeaver에 대한 Azure Virtual Machines 고가용성 아키텍처 및 시나리오](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-architecture-scenarios)
* [ASCS 클러스터 구성에 프로브 포트 추가](sap-high-availability-installation-wsfc-file-share.md)
* [장애 조치 클러스터에 (A)SCS 인스턴스 설치](https://www.sap.com/documents/2017/07/f453332f-c97c-0010-82c7-eda71af511fa.html)
* [Azure NetApp Files에 대한 SMB 볼륨 만들기](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-volumes-smb#requirements-for-active-directory-connections)
* [Azure NetApp 파일을 사용하여 Microsoft Azure의 NetApp SAP 응용 프로그램][anf-sap-applications-azure]

## <a name="overview"></a>개요

SAP는 Windows 장애 조치(Failover) 클러스터에 SAP ASCS/SCS 인스턴스를 클러스터링하기 위한 새로운 접근 방법을 개발했습니다. 이것은 클러스터 공유 디스크의 대안이 됩니다. 클러스터 공유 디스크를 사용하는 대신 SMB 파일 공유를 사용하여 SAP 전역 호스트 파일을 배포할 수 있습니다. Azure NetApp 파일은 활성 디렉터리를 사용하여 NTFS ACL을 사용하여 SMBv3(NFS와 함께)를 지원합니다. Azure NetApp 파일은 PaaS 서비스이기 때문에 자동으로 고가용성을 제공합니다. 이러한 기능을 사용하면 Azure NetApp 파일이 SAP 글로벌에 대한 SMB 파일 공유를 호스팅하는 데 유용합니다.  
[Azure Active Directory(AD) 도메인 서비스와](https://docs.microsoft.com/azure/active-directory-domain-services/overview) [AD DS(활성 디렉터리 도메인 서비스)가](https://docs.microsoft.com/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview) 모두 지원됩니다. Azure NetApp 파일에서 기존 Active Directory 도메인 컨트롤러를 사용할 수 있습니다. 도메인 컨트롤러는 Azure에서 가상 컴퓨터로 사용할 수 있으며 ExpressRoute 또는 S2S VPN을 통해 온프레미스에 있을 수 있습니다. 이 문서에서는 Azure VM에서 도메인 컨트롤러를 사용합니다.  
SAP Netweaver 중앙 서비스에 대한 고가용성(HA)에는 공유 스토리지가 필요합니다. Windows에서 이를 달성하기 위해 지금까지 SOFS 클러스터를 빌드하거나 SIOS와 같은 클러스터 공유 디스크를 사용해야 했습니다. 이제 Azure NetApp 파일에 배포된 공유 저장소를 사용하여 SAP Netweaver HA를 달성할 수 있습니다. 공유 저장소에 Azure NetApp 파일을 사용하면 SOFS 또는 SIOS가 필요하지 않습니다.  

> [!NOTE]
> 파일 공유를 사용한 SAP ASCS/SCS 인스턴스의 클러스터링은 SAP 커널 7.49 이상이 적용된 SAP NetWeaver 7.40 이상에서 지원됩니다.  

![SAP 공유를 가진 SAP ASCS/SCS HA 아키텍처](./media/virtual-machines-shared-sap-high-availability-guide/high-availability-windows-azure-netapp-files-smb.png)

SMB 파일 공유의 전제 조건은 다음과 같습니다.
* SMB 3.0 이상의 프로토콜
* Active Directory 사용자 그룹 및 컴퓨터$ 컴퓨터 개체에 대한 Active Directory 액세스 제어 목록(ACL)을 설정할 수 있습니다.
* 파일 공유는 HA를 사용하도록 설정해야 합니다.

이 참조 아키텍처에서 SAP 중앙 서비스에 대한 공유는 Azure NetApp 파일에서 제공됩니다.

![SAP 공유를 가진 SAP ASCS/SCS HA 아키텍처](./media/virtual-machines-shared-sap-high-availability-guide/high-availability-windows-azure-netapp-files-smb-detail.png)

## <a name="create-and-mount-smb-volume-for-azure-netapp-files"></a>Azure NetApp 파일에 대한 SMB 볼륨 생성 및 마운트

Azure NetApp 파일 사용에 대한 준비로 다음 단계를 수행합니다.  

1. [Azure NetApp 파일에 등록하는](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-register) 단계에 따라  
2. NetApp 계정 만들기에 설명된 단계에 따라 Azure [NetApp 계정 만들기](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-netapp-account)  
3. 용량 풀 설정의 지침에 따라 [용량 풀 설정](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-set-up-capacity-pool)
4. Azure NetApp 파일 리소스는 위임된 서브넷에 있어야 합니다. [Azure NetApp 파일에 서브넷 을 위임하는](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-delegate-subnet) 방법에 따라 위임된 서브넷을 만듭니다.  

> [!IMPORTANT]
> SMB 볼륨을 만들기 전에 Active Directory 연결을 만들어야 합니다. Active [Directory 연결에 대한 요구 사항을](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-volumes-smb#requirements-for-active-directory-connections)검토합니다.  

5. 활성 디렉터리 연결 만들기에 설명된 대로 [Active Directory 연결 만들기](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-volumes-smb#create-an-active-directory-connection)  
6. SMB Azure NetApp 파일 SMB 볼륨 추가의 지침에 따라 [SMB](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-volumes-smb#add-an-smb-volume) Azure NetApp 파일 볼륨 만들기  
7. Windows 가상 머신에 SMB 볼륨을 탑재합니다.

> [!TIP]
> Azure [Portal에서 Azure](https://portal.azure.com/#home) NetApp 파일 개체로 탐색한 다음 **볼륨** 블레이드를 클릭한 다음 **명령**마운트를 클릭하는 경우 Azure NetApp 파일 볼륨을 탑재하는 방법에 대한 지침을 찾을 수 있습니다.  

## <a name="prepare-the-infrastructure-for-sap-ha-by-using-a-windows-failover-cluster"></a>Windows 장애 조치 클러스터를 사용하여 SAP HA에 대한 인프라 준비 

1. [필요한 DNS IP 주소 설정](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-infrastructure-wsfc-shared-disk#b22d7b3b-4343-40ff-a319-097e13f62f9e)  
2. [SAP 가상 시스템에 대한 정적 IP 주소를 설정합니다.](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-infrastructure-wsfc-shared-disk#84c019fe-8c58-4dac-9e54-173efd4b2c30)
3. [Azure 내부 로드 밸러버에 대한 정적 IP 주소를 설정합니다.](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-infrastructure-wsfc-shared-disk#7a8f3e9b-0624-4051-9e41-b73fff816a9e)
4. [Azure 내부 부하 분산 장치의 기본 ASCS/SCS 부하 분산 규칙 설정](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-infrastructure-wsfc-shared-disk#f19bd997-154d-4583-a46e-7f5a69d0153c)
5. [Azure 내부 로드 밸런서에 대한 ASCS/SCS 기본 부하 분산 규칙을 변경합니다.](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-infrastructure-wsfc-shared-disk#fe0bd8b5-2b43-45e3-8295-80bee5415716)
6. [도메인에 Windows 가상 컴퓨터를 추가합니다.](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-infrastructure-wsfc-shared-disk#e69e9a34-4601-47a3-a41c-d2e11c626c0c)
7. [SAP ASCS/SCS 인스턴스의 두 클러스터 노드에 레지스트리 항목 추가](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-infrastructure-wsfc-shared-disk#661035b2-4d0f-4d31-86f8-dc0a50d78158)
8. [SAP ASCS/SCS 인스턴스에 대한 Windows 서버 장애 조치 클러스터 설정](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-infrastructure-wsfc-shared-disk#0d67f090-7928-43e0-8772-5ccbf8f59aab)
9. Windows Server 2016을 사용하는 경우 Azure [클라우드 감시를](https://docs.microsoft.com/windows-server/failover-clustering/deploy-cloud-witness)구성하는 것이 좋습니다.


## <a name="install-sap-ascs-instance-on-both-nodes"></a>두 노드모두에 SAP ASCS 인스턴스 설치

SAP에서 다음 소프트웨어가 필요합니다.
   * SAP 소프트웨어 프로비저닝 관리자(SWPM) 설치 도구 버전 SPS25 이상.
   * SAP 커널 7.49 이상
   * 클러스터된 SAP ASCS/SCS [인스턴스에](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-installation-wsfc-shared-disk#a97ad604-9094-44fe-a364-f89cb39bf097)대해 가상 호스트 이름(클러스터 네트워크 이름)을 만듭니다.

> [!NOTE]
> 파일 공유를 사용한 SAP ASCS/SCS 인스턴스의 클러스터링은 SAP 커널 7.49 이상이 적용된 SAP NetWeaver 7.40 이상에서 지원됩니다.  

### <a name="install-an-ascsscs-instance-on-the-first-ascsscs-cluster-node"></a>첫 번째 ASCS/SCS 클러스터 노드에 ASCS/SCS 인스턴스 설치

1. 첫 번째 클러스터 노드에 SAP ASCS/SCS 인스턴스를 설치합니다. SAP SWPM 설치 도구를 시작한 다음 다음 다음 으로 이동합니다: **제품** > **DBMS** > 설치 > 애플리케이션 서버 ABAP(또는 Java) > ASCS/SCS 인스턴스 > 첫 번째 클러스터 노드를 > 고가용성 시스템입니다.  

2. SWPM에서 클러스터 공유 구성으로 **파일 공유 클러스터를** 선택합니다.  
3. 단계 **SAP 시스템 클러스터 매개 변수에서**메시지가 표시 되면 이미 **파일 공유 호스트**이름으로 만든 Azure NetApp 파일 SMB 공유에 대한 호스트 이름을 입력합니다.  이 예제에서 SMB 공유 호스트 이름은 **anfsmb-9562입니다.** 

> [!IMPORTANT]
> 필수 조건 검사기 SWPM의 결과 연속 가용성 기능 조건이 충족되지 않는 경우 [Windows에 더 이상 존재하지 않는 공유 폴더에 액세스하려고 할 때 지연 된 오류 메시지의](https://support.microsoft.com/help/2820470/delayed-error-message-when-you-try-to-access-a-shared-folder-that-no-l)지침에 따라 해결할 수 있습니다.  

> [!TIP]
> 필수 조건 검사기 SWPM의 결과에 스왑 크기 조건이 충족되지 않은 경우 내 컴퓨터>시스템 속성>성능 설정> 고급> 가상 메모리> 변경으로 이동하여 SWAP 크기를 조정할 수 있습니다.  

4. PowerShell을 사용하여 SAP `SAP-SID-IP` 클러스터 리소스인 프로브 포트를 구성합니다. [구성 프로브 포트에](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-installation-wsfc-shared-disk#10822f4f-32e7-4871-b63a-9b86c76ce761)설명된 대로 SAP ASCS/SCS 클러스터 노드 중 하나에서 이 구성을 실행합니다.

### <a name="install-an-ascsscs-instance-on-the-second-ascsscs-cluster-node"></a>두 번째 ASCS/SCS 클러스터 노드에 ASCS/SCS 인스턴스 설치

1. 두 번째 클러스터 노드에 SAP ASCS/SCS 인스턴스를 설치합니다. SAP SWPM 설치 도구를 시작한 다음 ASCS/SCS 인스턴스를 > 고가용성 시스템 > 고가용성 시스템 > 설치 > 애플리케이션 서버 ABAP(또는 Java)> 추가 클러스터 노드를 > **제품** > **DBMS로** 이동합니다.  

### <a name="install-a-dbms-instance-and-sap-application-servers"></a>DBMS 인스턴스 및 SAP 애플리케이션 서버 설치

다음을 설치하여 SAP 설치를 완료합니다.

   * DBMS 인스턴스  
   * 기본 SAP 응용 프로그램 서버  
   * 추가 SAP 응용 프로그램 서버  

## <a name="test-the-sap-ascsscs-instance-failover"></a>SAP ASCS/SCS 인스턴스 장애 조치 테스트 

### <a name="fail-over-from-cluster-node-a-to-cluster-node-b-and-back"></a>클러스터 노드 A에서 클러스터 노드 B로 장애 조치 및 다시 장애 조치
이 테스트 시나리오에서는 클러스터 노드 sapascs1을 노드 A로, 클러스터 노드 sapascs2를 노드 B로 참조합니다.

1. 클러스터 리소스가 노드 A. ![그림 1에서 실행되고 있는지 확인: 장애 조치 테스트 이전 노드 A에서 실행 중인 Windows Server 장애 조치 클러스터 리소스](./media/virtual-machines-shared-sap-high-availability-guide/high-availability-windows-azure-netapp-files-smb-figure-1.png)  

2. 클러스터 노드 A를 다시 시작합니다. SAP 클러스터 리소스는 클러스터 노드 ![B. 그림 2: 장애 조치 테스트 후 노드 B에서 실행되는 Windows Server 장애 조치 클러스터 리소스로 이동합니다.](./media/virtual-machines-shared-sap-high-availability-guide/high-availability-windows-azure-netapp-files-smb-figure-2.png)  


## <a name="lock-entry-test"></a>잠금 항목 테스트

1.SAP 큐 복제 서버(ERS)가 활성 상태인지 확인  
2. SAP 시스템에 로그온하고 트랜잭션 SU01을 실행하고 변경 모드에서 사용자 ID를 엽니다. 그렇게 하면 SAP 잠금 항목이 생성됩니다.  
3. SAP 시스템에 로그인할 때 트랜잭션 ST12로 이동하여 잠금 항목을 표시합니다.  
4. 클러스터 노드 A에서 클러스터 노드 B로 ASCS 리소스를 장애 조치합니다.  
5. SAP ASCS/SCS 클러스터 리소스 장애 조치 전에 생성된 잠금 항목이 유지되는지 확인합니다.  

![그림 3: 장애 조치 테스트 후 잠금 항목이 유지됩니다.](./media/virtual-machines-shared-sap-high-availability-guide/high-availability-windows-azure-netapp-files-smb-figure-3.png)  

자세한 내용은 [ERS를 통해 ASCS에서 큐 장애 조치(Failover)에 대한 문제 해결을 참조하세요.](https://wiki.scn.sap.com/wiki/display/SI/Troubleshooting+for+Enqueue+Failover+in+ASCS+with+ERS)
## <a name="next-steps"></a>다음 단계

* [SAP용 Azure Virtual Machines 계획 및 구현][planning-guide]
* [SAP용 Azure Virtual Machines 배포][deployment-guide]
* [SAP용 Azure Virtual Machines DBMS 배포][dbms-guide]
* SAP의 고가용성 설정 및 재해 복구 계획 수립 방법 알아보기 
* Azure에서 HANA(큰 인스턴스)를 [참조하세요.](hana-overview-high-availability-disaster-recovery.md)
* Azure VM에서 SAP HANA의 재해 복구를 계획하고 고가용성을 설정하는 방법을 알아보려면 [Azure VM(Virtual Machines)의 SAP HANA 고가용성][sap-hana-ha]을 참조하세요.
