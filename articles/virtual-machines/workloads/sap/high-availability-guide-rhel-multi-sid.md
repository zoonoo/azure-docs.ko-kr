---
title: RHEL 다중 SID 가이드의 SAP NW에 대 한 Azure Vm 고가용성 Microsoft Docs
description: Red Hat Enterprise Linux의 SAP NetWeaver에 대한 Azure Virtual Machines 고가용성
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 02/26/2020
ms.author: radeltch
ms.openlocfilehash: 1c52e7e30ac02b14356284f0506824b5a7d0188a
ms.sourcegitcommit: 96dc60c7eb4f210cacc78de88c9527f302f141a9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/27/2020
ms.locfileid: "77652049"
---
# <a name="high-availability-for-sap-netweaver-on-azure-vms-on-red-hat-enterprise-linux-for-sap-applications-multi-sid-guide"></a>SAP 응용 프로그램에 대 한 Red Hat Enterprise Linux Azure Vm의 SAP NetWeaver에 대 한 고가용성-다중 SID 가이드

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md

[anf-azure-doc]:https://docs.microsoft.com/azure/azure-netapp-files/
[anf-avail-matrix]:https://azure.microsoft.com/global-infrastructure/services/?products=storage&regions=all
[anf-register]:https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-register
[anf-sap-applications-azure]:https://www.netapp.com/us/media/tr-4746.pdf

[2002167]: https://launchpad.support.sap.com/#/notes/2002167
[2009879]: https://launchpad.support.sap.com/#/notes/2009879
[1928533]: https://launchpad.support.sap.com/#/notes/1928533
[2015553]: https://launchpad.support.sap.com/#/notes/2015553
[2178632]: https://launchpad.support.sap.com/#/notes/2178632
[2191498]: https://launchpad.support.sap.com/#/notes/2191498
[2243692]: https://launchpad.support.sap.com/#/notes/2243692
[1999351]: https://launchpad.support.sap.com/#/notes/1999351
[1410736]:https://launchpad.support.sap.com/#/notes/1410736

[sap-swcenter]:https://support.sap.com/en/my-support/software-downloads.html

[template-multisid-xscs]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs-md%2Fazuredeploy.json

[sap-hana-ha]:sap-hana-high-availability-rhel.md
[glusterfs-ha]:high-availability-guide-rhel-glusterfs.md

이 문서에서는 SAP 응용 프로그램에 대 한 Red Hat Enterprise Linux를 사용 하 여 Azure Vm의 두 노드 클러스터에 다중 SID를 사용 하는 여러 SAP NetWeaver 시스템을 배포 하는 방법을 설명 합니다.  

예제 구성, 설치 명령 등 세 가지 SAP NetWeaver 7.50 시스템은 단일 노드 고가용성 클러스터에 배포 됩니다. SAP 시스템 Sid는 다음과 같습니다.
* **N w 1**: ascs 인스턴스 번호 **00** 및 가상 호스트 이름 **msnw1ascs**; ERS 인스턴스 번호 **02** 및 가상 호스트 이름 **msnw1ers**.  
* **N w 2**: ascs 인스턴스 번호 **10** 및 가상 호스트 이름 **msnw2ascs**; ERS 인스턴스 번호 **12** 및 가상 호스트 이름 **msnw2ers**.  
* **NW3**: ascs 인스턴스 번호 **20** 및 가상 호스트 이름 **msnw3ascs**; ERS 인스턴스 번호 **22** 및 가상 호스트 이름 **msnw3ers**.  

이 문서에서는 데이터베이스 계층과 SAP NFS 공유의 배포에 대해 다루지 않습니다. 이 문서의 예제에서는 볼륨이 이미 배포 된 것으로 가정 하 여 NFS 공유에 [Azure NetApp Files](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-volumes) 볼륨 **sapMSID** 를 사용 합니다. 또한 Azure NetApp Files 볼륨이 NFSv3 프로토콜을 사용 하 여 배포 되 고 SAP systems N W 1, N W 2 및 NW3의 ASCS 및 ERS 인스턴스에 대 한 클러스터 리소스에 대해 다음 파일 경로가 존재 한다고 가정 합니다.  

* volume sapMSID (nfs://10.42.0.4/sapmnt<b>n w 1</b>)
* volume sapMSID (nfs://10.42.0.4/usrsap<b>n w 1</b>ascs)
* 볼륨 sapMSID (nfs://10.42.0.4/usrsap<b>n w 1</b>sys)
* volume sapMSID (nfs://10.42.0.4/usrsap<b>n w 1</b>ers)
* volume sapMSID (nfs://10.42.0.4/sapmnt<b>n w 2</b>)
* volume sapMSID (nfs://10.42.0.4/usrsap<b>n w 2</b>ascs)
* 볼륨 sapMSID (nfs://10.42.0.4/usrsap<b>n w 2</b>sys)
* volume sapMSID (nfs://10.42.0.4/usrsap<b>n w 2</b>ers)
* volume sapMSID (nfs://10.42.0.4/sapmnt<b>NW3</b>)
* volume sapMSID (nfs://10.42.0.4/usrsap<b>NW3</b>ascs)
* 볼륨 sapMSID (nfs://10.42.0.4/usrsap<b>NW3</b>sys)
* volume sapMSID (nfs://10.42.0.4/usrsap<b>NW3</b>ers)

시작 하기 전에 먼저 다음 SAP 참고 사항 및 백서를 참조 하세요.

* SAP Note [1928533], 다음 항목을 포함합니다.
  * SAP 소프트웨어 배포에 지원되는 Azure VM 크기 목록
  * Azure VM 크기에 대한 중요한 용량 정보
  * 지원되는 SAP 소프트웨어 및 운영 체제(OS)와 데이터베이스 조합
  * Microsoft Azure에서 Windows 및 Linux에 필요한 SAP 커널 버전
* [Azure NetApp Files 설명서][anf-azure-doc]
* SAP Note [2015553]는 Azure에서 SAP을 지원하는 SAP 소프트웨어 배포에 대한 필수 구성 요소를 나열합니다.
* SAP Note [2002167]에는 Red Hat Enterprise Linux에 권장되는 OS 설정이 있습니다.
* SAP Note [2009879]에는 Red Hat Enterprise Linux용 SAP HANA 지침이 있습니다.
* SAP Note [2178632]는 Azure에서 SAP에 대해 보고된 모든 모니터링 메트릭에 대한 자세한 정보를 포함하고 있습니다.
* SAP Note [2191498]는 Azure에서 Linux에 필요한 SAP Host Agent 버전을 포함하고 있습니다.
* SAP Note [2243692]는 Azure에서 Linux의 SAP 라이선스에 대한 정보를 포함하고 있습니다.
* SAP Note [1999351]은 SAP용 Azure 고급 모니터링 확장을 위한 추가 문제 해결 정보를 포함하고 있습니다.
* [SAP Community WIKI](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes)는 Linux에 필요한 모든 SAP Note를 포함하고 있습니다.
* [Linux에서 SAP 용 Azure Virtual Machines 계획 및 구현][planning-guide]
* [Linux에서 SAP 용 Azure Virtual Machines 배포][deployment-guide]
* [Linux에서 SAP 용 Azure Virtual Machines DBMS 배포][dbms-guide]
* [pacemaker 클러스터의 SAP Netweaver](https://access.redhat.com/articles/3150081)
* 일반 RHEL 설명서
  * [High Availability Add-On Overview](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_overview/index)(고가용성 추가 기능 개요)
  * [High Availability Add-On Administration](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_administration/index)(고가용성 추가 기능 관리)
  * [High Availability Add-On Reference](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_reference/index)(고가용성 추가 기능 참조)
  * [RHEL 7.5에서 독립 실행형 리소스를 사용하여 SAP Netweaver용 ASCS/ERS 구성](https://access.redhat.com/articles/3569681)
  * [Pacemaker의 RHEL에서 독립 실행형 큐에 넣기 서버 2 (ENSA2)를 사용 하 여 SAP S/4HANA ASCS/ERS 구성](https://access.redhat.com/articles/3974941)
* Azure 관련 RHEL 설명서:
  * [Support Policies for RHEL High Availability Clusters - Microsoft Azure Virtual Machines as Cluster Members](https://access.redhat.com/articles/3131341)(RHEL 고가용성 클러스터용 지원 정책 - Microsoft Azure Virtual Machines(클러스터 멤버))
  * [Installing and Configuring a Red Hat Enterprise Linux 7.4 (and later) High-Availability Cluster on Microsoft Azure](https://access.redhat.com/articles/3252491)(Microsoft Azure에서 Red Hat Enterprise Linux 7.4 이상 고가용성 클러스터 설치 및 구성)
* [Azure NetApp Files를 사용 하 여 Microsoft Azure에서 NetApp SAP 응용 프로그램][anf-sap-applications-azure]

## <a name="overview"></a>개요

장애 조치 (failover)가 발생 하는 경우 클러스터에 참여 하는 가상 머신은 모든 리소스를 실행할 수 있도록 크기를 조정 해야 합니다. 각 SAP SID는 다중 SID 고가용성 클러스터에서 서로 독립적인 장애 조치 (failover) 될 수 있습니다.  

고가용성을 위해 SAP NetWeaver에는 항상 사용 가능한 공유가 필요 합니다. 이 설명서에서는 [AZURE NETAPP FILES NFS 볼륨](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-volumes)에 배포 된 SAP 공유에 대 한 예제를 제공 합니다. 또한 여러 SAP 시스템에서 사용할 수 있는 고가용성 [GlusterFS 클러스터](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-glusterfs)에 대 한 공유를 호스트할 수 있습니다.  

![SAP NetWeaver 고가용성 개요](./media/high-availability-guide-rhel/ha-rhel-multi-sid.png)

> [!IMPORTANT]
> Azure Vm에서 Red Hat Linux를 게스트 운영 체제로 사용 하는 SAP ASCS/ERS의 다중 SID 클러스터링에 대 한 지원은 동일한 클러스터에서 **5 개의** sap sid로 제한 됩니다. 각 새 SID는 복잡성이 증가 합니다. 동일한 클러스터에서 SAP 큐에서 복제 서버 1과 큐에 넣기 복제 서버 2를 혼합 하 여 **사용할 수 없습니다**. 다중 SID 클러스터링은 하나의 Pacemaker 클러스터에서 서로 다른 Sid를 사용 하 여 여러 SAP ASCS/ERS 인스턴스를 설치 하는 방법을 설명 합니다. 현재 다중 SID 클러스터링은 ASCS/ERS에 대해서만 지원 됩니다.  

> [!TIP]
> SAP ASCS/ERS의 다중 SID 클러스터링은 더 복잡 한 솔루션입니다. 구현 하는 것이 더 복잡 합니다. 또한 유지 관리 작업 (예: OS 패치)을 실행할 때 더 높은 관리 노력이 수반 됩니다. 실제 구현을 시작 하기 전에 배포 및 Vm, NFS 탑재, Vip, 부하 분산 장치 구성 등의 관련 된 모든 구성 요소를 신중 하 게 계획 해야 합니다.  

Sap NetWeaver ASCS, sap NetWeaver SCS 및 SAP NetWeaver ERS는 가상 호스트 이름 및 가상 IP 주소를 사용 합니다. Azure에서는 가상 IP 주소를 사용하려면 부하 분산 장치가 필요합니다. [표준 부하 분산 장치](https://docs.microsoft.com/azure/load-balancer/quickstart-load-balancer-standard-public-portal)를 사용 하는 것이 좋습니다.  

다음 목록에서는 세 가지 SAP 시스템을 사용 하는이 다중 SID 클러스터 예제에 대 한 (A) SCS 및 ERS 부하 분산 장치 구성을 보여 줍니다. 각 Sid에 대 한 각 ASCS 및 ERS 인스턴스에 대해 별도의 프런트 엔드 IP, 상태 프로브 및 부하 분산 규칙이 필요 합니다. ASCS/ASCS 클러스터의 일부인 모든 Vm을 단일 ILB의 백 엔드 풀 하나에 할당 합니다.  

### <a name="ascs"></a>(A)SCS

* 프런트 엔드 구성
  * N W 1에 대 한 IP 주소: 10.3.1.50
  * N W 2에 대 한 IP 주소: 10.3.1.52
  * NW3에 대 한 IP 주소: 10.3.1.54

* 프로브 포트
  * 포트 620<strong>&lt;nr&gt;</strong>, 따라서 N W 1, N W 2 및 NW3 프로브 포트 620**00**, 620**10** 및 620**20**
* 부하 분산 규칙-각 인스턴스, 즉 N W 1/ASCS, N W 2/ASCS 및 NW3/ASCS에 대해 하나씩 만듭니다.
  * 표준 Load Balancer 사용 하는 경우 **HA 포트** 를 선택 합니다.
  * 기본 Load Balancer 사용 하는 경우 다음 포트에 대 한 부하 분산 규칙을 만듭니다.
    * 32<strong>&lt;nr&gt;</strong> TCP
    * 36<strong>&lt;nr&gt;</strong> TCP
    * 39<strong>&lt;nr&gt;</strong> TCP
    * 81<strong>&lt;nr&gt;</strong> TCP
    * 5<strong>&lt;nr&gt;</strong>13 TCP
    * 5<strong>&lt;nr&gt;</strong>14 TCP
    * 5<strong>&lt;nr&gt;</strong>16 TCP

### <a name="ers"></a>ERS

* 프런트 엔드 구성
  * N W 1 10.3.1.51에 대 한 IP 주소
  * N W 2 10.3.1.53에 대 한 IP 주소
  * NW3 10.3.1.55에 대 한 IP 주소

* 프로브 포트
  * 포트 621<strong>&lt;nr&gt;</strong>, 따라서 N W 1, N w 2 및 N3 프로브 포트인 621**02**, 621**12** 및 621**22**
* 부하 분산 규칙-각 인스턴스, 즉 N W 1/ERS, N W 2/ERS 및 NW3/ERS에 대해 하나를 만듭니다.
  * 표준 Load Balancer 사용 하는 경우 **HA 포트** 를 선택 합니다.
  * 기본 Load Balancer 사용 하는 경우 다음 포트에 대 한 부하 분산 규칙을 만듭니다.
    * 32<strong>&lt;nr&gt;</strong> TCP
    * 33<strong>&lt;nr&gt;</strong> TCP
    * 5<strong>&lt;nr&gt;</strong>13 TCP
    * 5<strong>&lt;nr&gt;</strong>14 TCP
    * 5<strong>&lt;nr&gt;</strong>16 TCP

* 백 엔드 구성
  * (A)SCS/ERS 클러스터의 일부분이어야 하는 모든 가상 머신의 주 네트워크 인터페이스에 연결됨

> [!Note]
> 공용 IP 주소가 없는 Vm이 내부 (공용 IP 주소 없음) 표준 Azure 부하 분산 장치의 백 엔드 풀에 배치 되는 경우 공용 끝점으로의 라우팅을 허용 하기 위해 추가 구성을 수행 하지 않는 한 아웃 바운드 인터넷 연결이 없습니다. 아웃 바운드 연결을 설정 하는 방법에 대 한 자세한 내용은 [SAP 고가용성 시나리오에서 Azure 표준 Load Balancer를 사용 하 여 Virtual Machines에 대 한 공용 끝점 연결](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-standard-load-balancer-outbound-connections)을 참조 하세요.  

> [!IMPORTANT]
> Azure Load Balancer 뒤에 배치 되는 Azure Vm에서 TCP 타임 스탬프를 사용 하도록 설정 하지 마세요. TCP 타임 스탬프를 사용 하도록 설정 하면 상태 프로브가 실패 합니다. **Tcp_timestamps** 매개 변수를 **0**으로 설정 합니다. 자세한 내용은 [Load Balancer 상태 프로브](https://docs.microsoft.com/azure/load-balancer/load-balancer-custom-probe-overview)를 참조 하세요.

## <a name="sap-shares"></a>SAP 공유

SAP NetWeaver에는 전송, 프로필 디렉터리 등에 대 한 공유 저장소가 필요 합니다. 항상 사용 가능한 SAP 시스템의 경우 항상 사용 가능한 공유를 사용 하는 것이 중요 합니다. SAP 공유에 대 한 아키텍처를 결정 해야 합니다. 한 가지 옵션은 [Azure NetApp FILES NFS 볼륨](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-volumes)에 공유를 배포 하는 것입니다.  Azure NetApp Files를 사용 하 여 SAP NFS 공유에 대 한 고가용성을 제공 합니다.

또 다른 옵션은 [Sap NetWeaver에 대 한 Red Hat Enterprise Linux에서 Azure vm에 대 한 GlusterFS](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-glusterfs)을 빌드하는 것입니다 .이는 여러 sap 시스템 간에 공유할 수 있습니다. 

## <a name="deploy-the-first-sap-system-in-the-cluster"></a>클러스터에 첫 번째 SAP 시스템 배포

이제 SAP 공유에 대 한 아키텍처를 결정 했으므로 해당 설명서에 따라 클러스터에 첫 번째 SAP 시스템을 배포 합니다.

* Azure NetApp Files NFS 볼륨을 사용 하는 경우 [sap 응용 프로그램에 대해 Azure NetApp Files를 사용 하 여 Red Hat Enterprise Linux에서 Sap NetWeaver에 대 한 Azure vm 고가용성](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-netapp-files) 을 따르세요.  
* GlusterFS 클러스터를 사용 하는 경우 [SAP NetWeaver에 대 한 Red Hat Enterprise Linux에서 Azure vm의 GlusterFS](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-glusterfs)를 따르세요.  

위에 나열 된 문서에서는 필요한 인프라를 준비 하 고, 클러스터를 빌드하고, SAP 응용 프로그램을 실행 하기 위해 OS를 준비 하는 단계를 안내 합니다.  

> [!TIP]
> 클러스터에 추가 SAP Sid를 추가 하기 전에 첫 번째 시스템이 배포 된 후 클러스터의 장애 조치 (failover) 기능을 항상 테스트 합니다. 이렇게 하면 클러스터에 추가 SAP 시스템의 복잡성을 추가 하기 전에 클러스터 기능이 작동 한다는 것을 알 수 있습니다.   

## <a name="deploy-additional-sap-systems-in-the-cluster"></a>클러스터에 추가 SAP 시스템 배포

이 예제에서는 system **n w 1** 가 클러스터에 이미 배포 되어 있다고 가정 합니다. **N w 2** 및 **NW3**클러스터에서 배포 하는 방법을 보여 줍니다. 

다음 항목에는 접두사 **[A]** (모든 노드에 적용됨), **[1]** (노드 1에만 적용됨), **[2]** (노드 2에만 적용됨) 접두사가 표시되어 있습니다.

### <a name="prerequisites"></a>사전 요구 사항 

> [!IMPORTANT]
> 클러스터에 추가 SAP 시스템을 배포 하기 위한 지침을 수행 하기 전에 첫 번째 시스템 배포 중에만 필요한 단계를 설명 하는 지침에 따라 클러스터의 첫 번째 SAP 시스템을 배포 합니다.  

이 문서에서는 다음을 가정 합니다.
* Pacemaker 클러스터가 이미 구성 되어 실행 되 고 있습니다.  
* 하나 이상의 SAP 시스템 (ASCS/ERS 인스턴스)이 이미 배포 되어 클러스터에 실행 되 고 있습니다.  
* 클러스터 장애 조치 (failover) 기능이 테스트 되었습니다.  
* 모든 SAP 시스템에 대 한 NFS 공유를 배포 합니다.  

### <a name="prepare-for-sap-netweaver-installation"></a>SAP NetWeaver 설치 준비

1. 새로 배포한 시스템 (즉, **n w 2**, **NW3**)에 대 한 구성을 기존 Azure Load Balancer에 추가 합니다. 지침에 따라 [Azure Portal를 통해 수동으로 배포 Azure Load Balancer](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-netapp-files#deploy-linux-manually-via-azure-portal)합니다. 구성에 대 한 IP 주소, 상태 프로브 포트, 부하 분산 규칙을 조정 합니다.  

2. **[A]** 추가 SAP 시스템에 대 한 이름 확인을 설정 합니다. DNS 서버를 사용 하거나 모든 노드에서 `/etc/hosts`를 수정할 수 있습니다. 이 예제에서는 `/etc/hosts` 파일을 사용 하는 방법을 보여 줍니다.  사용자 환경에 대 한 IP 주소 및 호스트 이름을 조정 합니다. 

    ```
    sudo vi /etc/hosts
    # IP address of the load balancer frontend configuration for NW2 ASCS
    10.3.1.52 msnw2ascs
    # IP address of the load balancer frontend configuration for NW3 ASCS
    10.3.1.54 msnw3ascs
    # IP address of the load balancer frontend configuration for NW2 ERS
    10.3.1.53 msnw2ers
    # IP address of the load balancer frontend configuration for NW3 ERS
    10.3.1.55 msnw3ers
   ```

3. **[A]** 클러스터에 배포 하는 추가 **n w 2** 및 **NW3** SAP 시스템에 대 한 공유 디렉터리를 만듭니다. 

    ```
    sudo mkdir -p /sapmnt/NW2
    sudo mkdir -p /usr/sap/NW2/SYS
    sudo mkdir -p /usr/sap/NW2/ASCS10
    sudo mkdir -p /usr/sap/NW2/ERS12
    sudo mkdir -p /sapmnt/NW3
    sudo mkdir -p /usr/sap/NW3/SYS
    sudo mkdir -p /usr/sap/NW3/ASCS20
    sudo mkdir -p /usr/sap/NW3/ERS22

    
    sudo chattr +i /sapmnt/NW2
    sudo chattr +i /usr/sap/NW2/SYS
    sudo chattr +i /usr/sap/NW2/ASCS10
    sudo chattr +i /usr/sap/NW2/ERS12
    sudo chattr +i /sapmnt/NW3
    sudo chattr +i /usr/sap/NW3/SYS
    sudo chattr +i /usr/sap/NW3/ASCS20
    sudo chattr +i /usr/sap/NW3/ERS22
   ```

4. **[A]** 클러스터에 배포 하는 추가 sap 시스템에 대 한/Sapmnt/SID 및/usr/sap/SID/SYS 파일 시스템의 탑재 항목을 추가 합니다. 이 예제에서는 **n w 2** 및 **NW3**입니다.  

   클러스터에 배포 하는 추가 SAP 시스템의 파일 시스템을 사용 하 여 파일 `/etc/fstab`를 업데이트 합니다.  

   * Azure NetApp Files 사용 하는 경우 [여기](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-netapp-files#prepare-for-sap-netweaver-installation) 에 있는 지침을 따르세요.  
   * GlusterFS 클러스터를 사용 하는 경우 [여기](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel#prepare-for-sap-netweaver-installation) 에 있는 지침을 따르세요.  

### <a name="install-ascs--ers"></a>ASCS/ERS 설치

1. 클러스터에 배포 하는 추가 SAP 시스템의 ASCS 인스턴스에 대 한 가상 IP 및 상태 프로브 클러스터 리소스를 만듭니다. 여기에 나와 있는 예제는 **n w 2** 및 **NW3** ascs에 대 한 것 이며, NFSv3 프로토콜을 사용 하는 Azure NetApp Files 볼륨에서 NFS를 사용 합니다.  

    ```
    sudo pcs resource create fs_NW2_ASCS Filesystem device='10.42.0.4:/sapMSIDR/usrsapNW2ascs' \
    directory='/usr/sap/NW2/ASCS10' fstype='nfs' force_unmount=safe \
    op start interval=0 timeout=60 op stop interval=0 timeout=120 op monitor interval=200 timeout=40 \
     --group g-NW2_ASCS

    sudo pcs resource create vip_NW2_ASCS IPaddr2 \
    ip=10.3.1.52 cidr_netmask=24 \
     --group g-NW2_ASCS
    
    sudo pcs resource create nc_NW2_ASCS azure-lb port=62010 \
     --group g-NW2_ASCS

    sudo pcs resource create fs_NW3_ASCS Filesystem device='10.42.0.4:/sapMSIDR/usrsapNW3ascs' \
    directory='/usr/sap/NW3/ASCS20' fstype='nfs' force_unmount=safe \
    op start interval=0 timeout=60 op stop interval=0 timeout=120 op monitor interval=200 timeout=40 \
    --group g-NW3_ASCS

    sudo pcs resource create vip_NW3_ASCS IPaddr2 \
    ip=10.3.1.54 cidr_netmask=24 \
    --group g-NW3_ASCS

    sudo pcs resource create nc_NW3_ASCS azure-lb port=62020 \
    --group g-NW3_ASCS
    ```

   클러스터 상태가 양호 이며 모든 리소스가 시작 되었는지 확인 합니다. 리소스가 실행되는 노드는 중요하지 않습니다.  

2. **[1]** SAP NetWeaver ASCS 설치  

   ASCS에 대 한 부하 분산 장치 프런트 엔드 구성의 IP 주소에 매핑되는 가상 호스트 이름을 사용 하 여 SAP NetWeaver ASCS를 루트로 설치 합니다. 예를 들어 시스템 **n w 2**의 경우 가상 호스트 이름은 <b>msnw2ascs</b>, <b>10.3.1.52</b> 및 부하 분산 장치 프로브에 사용한 인스턴스 번호 (예: <b>10</b>)입니다. 시스템 **NW3**경우 가상 호스트 이름은 <b>msnw3ascs</b>, <b>10.3.1.54</b> 및 부하 분산 장치 프로브에 사용한 인스턴스 번호 (예: <b>20</b>)입니다. 각 SAP SID에 대해 ASCS를 설치한 클러스터 노드를 적어둡니다.  

   sapinst 매개 변수 SAPINST_REMOTE_ACCESS_USER를 사용하면 루트 권한이 없는 사용자의 sapinst 연결을 허용할 수 있습니다. 매개 변수 SAPINST_USE_HOSTNAME를 사용 하 여 가상 호스트 이름을 사용 하 여 SAP를 설치할 수 있습니다.  

    ```
    # Allow access to SWPM. This rule is not permanent. If you reboot the machine, you have to run the command again
    sudo firewall-cmd --zone=public --add-port=4237/tcp
    sudo swpm/sapinst SAPINST_REMOTE_ACCESS_USER=sapadmin SAPINST_USE_HOSTNAME=virtual_hostname
    ```

   /Usr/sap/**sid**/ASCS**instance #** 에서 하위 폴더를 만들지 못한 경우에는 소유자를 **SID**adm으로 설정 하 고 sapsys를 ascs**인스턴스 #** 로 설정 하 고 다시 시도 하세요.

3. **[1]** 클러스터에 배포 하는 추가 SAP 시스템의 ERS 인스턴스에 대 한 가상 IP 및 상태 프로브 클러스터 리소스를 만듭니다. 여기에 나와 있는 예제는 **n w 2** 및 **NW3** ERS에 대 한 것 이며, NFSv3 프로토콜을 사용 하는 Azure NetApp Files 볼륨에서 NFS를 사용 합니다.  

    ```
    sudo pcs resource create fs_NW2_AERS Filesystem device='10.42.0.4:/sapMSIDR/usrsapNW2ers' \
    directory='/usr/sap/NW2/ERS12' fstype='nfs' force_unmount=safe \
    op start interval=0 timeout=60 op stop interval=0 timeout=120 op monitor interval=200 timeout=40 \
     --group g-NW2_AERS

    sudo pcs resource create vip_NW2_AERS IPaddr2 \
    ip=10.3.1.53 cidr_netmask=24 \
     --group g-NW2_AERS

    sudo pcs resource create nc_NW2_AERS azure-lb port=62112 \
     --group g-NW2_AERS

    sudo pcs resource create fs_NW3_AERS Filesystem device='10.42.0.4:/sapMSIDR/usrsapNW3ers' \
    directory='/usr/sap/NW3/ERS22' fstype='nfs' force_unmount=safe \
    op start interval=0 timeout=60 op stop interval=0 timeout=120 op monitor interval=200 timeout=40 \
     --group g-NW3_AERS

    sudo pcs resource create vip_NW3_AERS IPaddr2 \
    ip=10.3.1.55 cidr_netmask=24 \
     --group g-NW3_AERS

    sudo pcs resource create nc_NW3_AERS azure-lb port=62122 \
     --group g-NW3_AERS
   ```

   클러스터 상태가 양호 이며 모든 리소스가 시작 되었는지 확인 합니다.  

   그런 다음, 새로 만든 ERS 그룹의 리소스가 동일한 SAP 시스템의 ASCS 인스턴스가 설치 된 클러스터 노드와 반대 되는 클러스터 노드에서 실행 되 고 있는지 확인 합니다.  예를 들어 N W 2 ASCS가 `rhelmsscl1`에 설치 된 경우에는 `rhelmsscl2`에서 N W 2 ERS 그룹이 실행 중인지 확인 합니다.  그룹의 클러스터 리소스 중 하나에 대해 다음 명령을 실행 하 여 N W 2 ERS 그룹을 `rhelmsscl2`로 마이그레이션할 수 있습니다. 

    ```
      pcs resource move fs_NW2_AERS rhelmsscl2
    ```

4. **[2]** SAP NetWeaver ERS 설치

   해당 ERS에 대 한 부하 분산 장치 프런트 엔드 구성의 IP 주소에 매핑되는 가상 호스트 이름을 사용 하 여 다른 노드에 SAP NetWeaver ERS를 루트로 설치 합니다. 예를 들어 system **n w 2**의 경우 가상 호스트 이름은 <b>msnw2ers</b>, <b>10.3.1.53</b> 및 부하 분산 장치 프로브에 사용한 인스턴스 번호 (예: <b>12</b>)가 됩니다. 시스템 **NW3**경우 가상 호스트 이름 <b>msnw3ers</b>, <b>10.3.1.55</b> 및 부하 분산 장치 프로브에 사용한 인스턴스 번호 (예: <b>22</b>). 

   sapinst 매개 변수 SAPINST_REMOTE_ACCESS_USER를 사용하면 루트 권한이 없는 사용자의 sapinst 연결을 허용할 수 있습니다. 매개 변수 SAPINST_USE_HOSTNAME를 사용 하 여 가상 호스트 이름을 사용 하 여 SAP를 설치할 수 있습니다.  

    ```
    # Allow access to SWPM. This rule is not permanent. If you reboot the machine, you have to run the command again
    sudo firewall-cmd --zone=public --add-port=4237/tcp
    sudo swpm/sapinst SAPINST_REMOTE_ACCESS_USER=sapadmin SAPINST_USE_HOSTNAME=virtual_hostname
    ```

   > [!NOTE]
   > SWPM SP 20 PL 05 이상을 사용합니다. 그 이전 버전은 권한을 올바르게 설정하지 않으므로 설치가 실패합니다.

   /Usr/sap/**n w 2**/ers**인스턴스 #** 에서 하위 폴더를 만들지 못한 경우에는 소유자를 **sid**adm으로 설정 하 고 그룹을 ERS**인스턴스 #** 폴더의 sapsys로 설정 하 고 다시 시도 하세요.

   새로 배포 된 SAP 시스템의 ERS 그룹을 다른 클러스터 노드로 마이그레이션하려는 경우에는 해당 그룹에 대 한 위치 제약 조건을 제거 해야 합니다. 다음 명령을 실행 하 여 제약 조건을 제거할 수 있습니다. 예는 SAP systems **n w 2** and **NW3**에 대해 제공 됩니다. 명령에서 사용한 것과 같은 리소스에 대 한 임시 제약 조건을 제거 하 여 ERS 클러스터 그룹을 이동 해야 합니다.

    ```
      pcs resource clear fs_NW2_AERS
      pcs resource clear fs_NW3_AERS
    ```

5. **[1]** 새로 설치 된 SAP 시스템에 대 한 ASCS/SCS 및 ERS 인스턴스 프로필을 조정 합니다. 아래에 표시 된 예제는 N W 2에 대 한 것입니다. 클러스터에 추가 된 모든 SAP 인스턴스에 대 한 ASCS/SCS 및 ERS 프로필을 조정 해야 합니다.  
 
   * ASCS/SCS 프로필

      ```
      sudo vi /sapmnt/NW2/profile/NW2_ASCS10_msnw2ascs
   
      # Change the restart command to a start command
      #Restart_Program_01 = local $(_EN) pf=$(_PF)
      Start_Program_01 = local $(_EN) pf=$(_PF)

      # Add the keep alive parameter
      enque/encni/set_so_keepalive = true
      ```

   * ERS 프로필

      ```
      sudo vi /sapmnt/NW2/profile/NW2_ERS12_msnw2ers
   
      # Change the restart command to a start command
      #Restart_Program_00 = local $(_ER) pf=$(_PFL) NR=$(SCSID)
      Start_Program_00 = local $(_ER) pf=$(_PFL) NR=$(SCSID)
   
      # remove Autostart from ERS profile
      # Autostart = 1
      ```

6. **[A]** /usr/sap/sapservices 파일 업데이트

   Sapinit 시작 스크립트를 통해 인스턴스의 시작을 방지 하려면 Pacemaker에서 관리 하는 모든 인스턴스가 `/usr/sap/sapservices` 파일에서 주석 처리 되어야 합니다.  아래에 표시 된 예제는 SAP systems **n w 2** 및 **NW3**에 대 한 것입니다.  

   ```
    # On the node where ASCS was installed, comment out the line for the ASCS instacnes
    #LD_LIBRARY_PATH=/usr/sap/NW2/ASCS10/exe:$LD_LIBRARY_PATH; export LD_LIBRARY_PATH; /usr/sap/NW2/ASCS10/exe/sapstartsrv pf=/usr/sap/NW2/SYS/profile/NW2_ASCS10_msnw2ascs -D -u nw2adm
    #LD_LIBRARY_PATH=/usr/sap/NW3/ASCS20/exe:$LD_LIBRARY_PATH; export LD_LIBRARY_PATH; /usr/sap/NW3/ASCS20/exe/sapstartsrv pf=/usr/sap/NW3/SYS/profile/NW3_ASCS20_msnw3ascs -D -u nw3adm

    # On the node where ERS was installed, comment out the line for the ERS instacnes
    #LD_LIBRARY_PATH=/usr/sap/NW2/ERS12/exe:$LD_LIBRARY_PATH; export LD_LIBRARY_PATH; /usr/sap/NW2/ERS12/exe/sapstartsrv pf=/usr/sap/NW2/ERS12/profile/NW2_ERS12_msnw2ers -D -u nw2adm
    #LD_LIBRARY_PATH=/usr/sap/NW3/ERS22/exe:$LD_LIBRARY_PATH; export LD_LIBRARY_PATH; /usr/sap/NW3/ERS22/exe/sapstartsrv pf=/usr/sap/NW3/ERS22/profile/NW3_ERS22_msnw3ers -D -u nw3adm
   ```

7. **[1]** 새로 설치 된 sap 시스템에 대 한 sap 클러스터 리소스를 만듭니다.  

   ENSA1 (큐에 넣기 서버 1 아키텍처)를 사용 하는 경우 다음과 같이 SAP systems **n w 2** 및 **NW3** 에 대 한 리소스를 정의 합니다.

    ```
     sudo pcs property set maintenance-mode=true

    sudo pcs resource create rsc_sap_NW2_ASCS10 SAPInstance \
    InstanceName=NW2_ASCS10_msnw2ascs START_PROFILE="/sapmnt/NW2/profile/NW2_ASCS10_msnw2ascs" \
    AUTOMATIC_RECOVER=false \
    meta resource-stickiness=5000 migration-threshold=1 failure-timeout=60 \
    op monitor interval=20 on-fail=restart timeout=60 \
    op start interval=0 timeout=600 op stop interval=0 timeout=600 \
     --group g-NW2_ASCS

    sudo pcs resource create rsc_sap_NW2_ERS12 SAPInstance \
    InstanceName=NW2_ERS12_msnw2ers START_PROFILE="/sapmnt/NW2/profile/NW2_ERS12_msnw2ers" \
    AUTOMATIC_RECOVER=false IS_ERS=true \
    op monitor interval=20 on-fail=restart timeout=60 op start interval=0 timeout=600 op stop interval=0 timeout=600 \
     --group g-NW2_AERS

    sudo pcs constraint colocation add g-NW2_AERS with g-NW2_ASCS -5000
    sudo pcs constraint location rsc_sap_NW2_ASCS10 rule score=2000 runs_ers_NW2 eq 1
    sudo pcs constraint order g-NW2_ASCS then g-NW2_AERS kind=Optional symmetrical=false

    sudo pcs resource create rsc_sap_NW3_ASCS20 SAPInstance \
    InstanceName=NW3_ASCS20_msnw3ascs START_PROFILE="/sapmnt/NW3/profile/NW3_ASCS20_msnw3ascs" \
    AUTOMATIC_RECOVER=false \
    meta resource-stickiness=5000 migration-threshold=1 failure-timeout=60 \
    op monitor interval=20 on-fail=restart timeout=60 \
    op start interval=0 timeout=600 op stop interval=0 timeout=600 \
     --group g-NW3_ASCS

    sudo pcs resource create rsc_sap_NW3_ERS22 SAPInstance \
    InstanceName=NW3_ERS22_msnw3ers START_PROFILE="/sapmnt/NW3/profile/NW2_ERS22_msnw3ers" \
    AUTOMATIC_RECOVER=false IS_ERS=true \
    op monitor interval=20 on-fail=restart timeout=60 op start interval=0 timeout=600 op stop interval=0 timeout=600 \
     --group g-NW3_AERS

    sudo pcs constraint colocation add g-NW3_AERS with g-NW3_ASCS -5000
    sudo pcs constraint location rsc_sap_NW3_ASCS20 rule score=2000 runs_ers_NW3 eq 1
    sudo pcs constraint order g-NW3_ASCS then g-NW3_AERS kind=Optional symmetrical=false

    sudo pcs property set maintenance-mode=false
    ```

   Sap는 복제를 비롯 하 여 SAP NW 7.52에 대 한 지원 서비스를 도입 했습니다. ABAP Platform 1809부터 시작 하 여 큐에 넣기 서버 2가 기본적으로 설치 됩니다. 큐에 넣기 서버 2 지원에 대해서는 SAP note [2630416](https://launchpad.support.sap.com/#/notes/2630416) 을 참조 하세요.
   [ENSA2](https://help.sap.com/viewer/cff8531bc1d9416d91bb6781e628d4e0/1709%20001/en-US/6d655c383abf4c129b0e5c8683e7ecd8.html)(큐에 넣기 서버 2 아키텍처)를 사용 하는 경우 다음과 같이 SAP systems **n w 2** 및 **NW3** 에 대 한 리소스를 정의 합니다.

    ```
     sudo pcs property set maintenance-mode=true

    sudo pcs resource create rsc_sap_NW2_ASCS10 SAPInstance \
    InstanceName=NW2_ASCS10_msnw2ascs START_PROFILE="/sapmnt/NW2/profile/NW2_ASCS10_msnw2ascs" \
    AUTOMATIC_RECOVER=false \
    meta resource-stickiness=5000 migration-threshold=1 failure-timeout=60 \
    op monitor interval=20 on-fail=restart timeout=60 \
    op start interval=0 timeout=600 op stop interval=0 timeout=600 \
     --group g-NW2_ASCS

    sudo pcs resource create rsc_sap_NW2_ERS12 SAPInstance \
    InstanceName=NW2_ERS12_msnw2ers START_PROFILE="/sapmnt/NW2/profile/NW2_ERS12_msnw2ers" \
    AUTOMATIC_RECOVER=false IS_ERS=true \
    op monitor interval=20 on-fail=restart timeout=60 op start interval=0 timeout=600 op stop interval=0 timeout=600 \
     --group g-NW2_AERS

    sudo pcs constraint colocation add g-NW2_AERS with g-NW2_ASCS -5000
    sudo pcs constraint order g-NW2_ASCS then g-NW2_AERS kind=Optional symmetrical=false
    sudo pcs constraint order start g-NW2_ASCS then stop g-NW2_AERS symmetrical=false

    sudo pcs resource create rsc_sap_NW3_ASCS20 SAPInstance \
    InstanceName=NW3_ASCS20_msnw3ascs START_PROFILE="/sapmnt/NW3/profile/NW3_ASCS20_msnw3ascs" \
    AUTOMATIC_RECOVER=false \
    meta resource-stickiness=5000 migration-threshold=1 failure-timeout=60 \
    op monitor interval=20 on-fail=restart timeout=60 \
    op start interval=0 timeout=600 op stop interval=0 timeout=600 \
     --group g-NW3_ASCS

    sudo pcs resource create rsc_sap_NW3_ERS22 SAPInstance \
    InstanceName=NW3_ERS22_msnw3ers START_PROFILE="/sapmnt/NW3/profile/NW2_ERS22_msnw3ers" \
    AUTOMATIC_RECOVER=false IS_ERS=true \
    op monitor interval=20 on-fail=restart timeout=60 op start interval=0 timeout=600 op stop interval=0 timeout=600 \
     --group g-NW3_AERS

    sudo pcs constraint colocation add g-NW3_AERS with g-NW3_ASCS -5000
    sudo pcs constraint order g-NW3_ASCS then g-NW3_AERS kind=Optional symmetrical=false
    sudo pcs constraint order start g-NW3_ASCS then stop g-NW3_AERS symmetrical=false

    sudo pcs property set maintenance-mode=false
    ```

   이전 버전에서 업그레이드 하 고 큐에 넣기 서버 2로 전환 하는 경우 SAP note [2641019](https://launchpad.support.sap.com/#/notes/2641019)을 참조 하세요. 

   > [!NOTE]
   > 위의 구성에서 시간 제한은 단지 예 이며 특정 SAP 설정에 맞게 조정 해야 할 수 있습니다. 

   클러스터 상태가 정상이며 모든 리소스가 시작되었는지 확인합니다. 리소스가 실행되는 노드는 중요하지 않습니다.
   다음 예에서는 SAP systems **n w 2** 및 **NW3** 가 클러스터에 추가 된 후의 클러스터 리소스 상태를 보여 줍니다. 

    ```
     sudo pcs status

    Online: [ rhelmsscl1 rhelmsscl2 ]

    Full list of resources:

    rsc_st_azure   (stonith:fence_azure_arm):      Started rhelmsscl1
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl2
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl2
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl2
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started rhelmsscl2
    Resource Group: g-NW2_ASCS
        fs_NW2_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW2_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW2_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW2_ASCS10 (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW2_AERS
        fs_NW2_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW2_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW2_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW2_ERS12  (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW3_ASCS
        fs_NW3_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW3_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW3_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW3_ASCS20 (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW3_AERS
        fs_NW3_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW3_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW3_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW3_ERS22  (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    ```

8. **[A]** 두 노드에 ascs 및 ERS에 대 한 방화벽 규칙을 추가 합니다.  아래 예제에서는 SAP systems **n w 2** 및 **NW3**둘 다에 대 한 방화벽 규칙을 보여 줍니다.  

   ```
    # NW2 - ASCS
    sudo firewall-cmd --zone=public --add-port=62010/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=62010/tcp
    sudo firewall-cmd --zone=public --add-port=3210/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=3210/tcp
    sudo firewall-cmd --zone=public --add-port=3610/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=3610/tcp
    sudo firewall-cmd --zone=public --add-port=3910/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=3910/tcp
    sudo firewall-cmd --zone=public --add-port=8110/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=8110/tcp
    sudo firewall-cmd --zone=public --add-port=51013/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=51013/tcp
    sudo firewall-cmd --zone=public --add-port=51014/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=51014/tcp
    sudo firewall-cmd --zone=public --add-port=51016/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=51016/tcp
    # NW2 - ERS
    sudo firewall-cmd --zone=public --add-port=62112/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=62112/tcp
    sudo firewall-cmd --zone=public --add-port=3312/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=3312/tcp
    sudo firewall-cmd --zone=public --add-port=51213/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=51213/tcp
    sudo firewall-cmd --zone=public --add-port=51214/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=51214/tcp
    sudo firewall-cmd --zone=public --add-port=51216/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=51216/tcp
    # NW3 - ASCS
    sudo firewall-cmd --zone=public --add-port=62020/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=62020/tcp
    sudo firewall-cmd --zone=public --add-port=3220/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=3220/tcp
    sudo firewall-cmd --zone=public --add-port=3620/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=3620/tcp
    sudo firewall-cmd --zone=public --add-port=3920/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=3920/tcp
    sudo firewall-cmd --zone=public --add-port=8120/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=8120/tcp
    sudo firewall-cmd --zone=public --add-port=52013/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=52013/tcp
    sudo firewall-cmd --zone=public --add-port=52014/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=52014/tcp
    sudo firewall-cmd --zone=public --add-port=52016/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=52016/tcp
    # NW3 - ERS
    sudo firewall-cmd --zone=public --add-port=62122/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=62122/tcp
    sudo firewall-cmd --zone=public --add-port=3322/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=3322/tcp
    sudo firewall-cmd --zone=public --add-port=52213/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=52213/tcp
    sudo firewall-cmd --zone=public --add-port=52214/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=52214/tcp
    sudo firewall-cmd --zone=public --add-port=52216/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=52216/tcp
   ```

### <a name="proceed-with-the-sap-installation"></a>SAP 설치를 진행 합니다. 

다음을 수행 하 여 SAP 설치를 완료 합니다.

* [SAP NetWeaver 응용 프로그램 서버 준비](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-netapp-files#2d6008b0-685d-426c-b59e-6cd281fd45d7)
* [DBMS 인스턴스 설치](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-netapp-files#install-database)
* [기본 SAP 응용 프로그램 서버 설치](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-netapp-files#sap-netweaver-application-server-installation)
* 하나 이상의 추가 SAP 응용 프로그램 인스턴스 설치

## <a name="test-the-multi-sid-cluster-setup"></a>다중 SID 클러스터 설정 테스트

다음 테스트는 Red Hat의 모범 사례 가이드에서 테스트 사례의 하위 집합입니다. 사용자 편의를 위해 포함 되었습니다. 클러스터 테스트의 전체 목록은 다음 설명서를 참조 하세요.

* NFS 볼륨 Azure NetApp Files 사용 하는 경우 sap [응용 프로그램에 대 한 Azure NetApp Files를 사용 하 여 RHEL에서 Sap NetWeaver에 대 한 Azure vm 고가용성](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-netapp-files) 을 따릅니다.
* 항상 사용 가능한 `GlusterFS`사용 하는 경우 sap [응용 프로그램에 대 한 RHEL ON Sap NetWeaver의 Azure vm 고가용성](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel)을 따릅니다.  

항상 Red Hat 모범 사례 가이드를 읽고 추가 되었을 수 있는 모든 추가 테스트를 수행 합니다.  
표시 되는 테스트는 3 개의 SAP 시스템이 설치 된 다중 SID 클러스터의 두 노드에 있습니다.  

1. ASCS 인스턴스를 수동으로 마이그레이션합니다. 이 예에서는 SAP 시스템 NW3 ASCS 인스턴스를 마이그레이션하는 방법을 보여 줍니다.

   테스트 시작 전 리소스 상태:

   ```
    Online: [ rhelmsscl1 rhelmsscl2 ]

    Full list of resources:

    rsc_st_azure   (stonith:fence_azure_arm):      Started rhelmsscl1
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl2
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl2
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl2
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started rhelmsscl2
    Resource Group: g-NW2_ASCS
        fs_NW2_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl2
        vip_NW2_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl2
        nc_NW2_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl2
        rsc_sap_NW2_ASCS10 (ocf::heartbeat:SAPInstance):   Started rhelmsscl2
    Resource Group: g-NW2_AERS
        fs_NW2_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW2_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW2_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW2_ERS12  (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW3_ASCS
        fs_NW3_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl2
        vip_NW3_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl2
        nc_NW3_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl2
        rsc_sap_NW3_ASCS20 (ocf::heartbeat:SAPInstance):   Started rhelmsscl2
    Resource Group: g-NW3_AERS
        fs_NW3_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW3_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW3_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW3_ERS22  (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
   ```

   NW3 ASCS 인스턴스를 마이그레이션하려면 루트로 다음 명령을 실행 합니다.

   ```
    pcs resource move rsc_sap_NW3_ASCS200
    # Clear temporary migration constraints
    pcs resource clear rsc_sap_NW3_ASCS20

    # Remove failed actions for the ERS that occurred as part of the migration
    pcs resource cleanup rsc_sap_NW3_ERS22
   ```

   테스트 후 리소스 상태:

   ```
    Online: [ rhelmsscl1 rhelmsscl2 ]

    Full list of resources:

    rsc_st_azure   (stonith:fence_azure_arm):      Started rhelmsscl1
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl2
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl2
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl2
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started rhelmsscl2
    Resource Group: g-NW2_ASCS
        fs_NW2_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl2
        vip_NW2_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl2
        nc_NW2_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl2
        rsc_sap_NW2_ASCS10 (ocf::heartbeat:SAPInstance):   Started rhelmsscl2
    Resource Group: g-NW2_AERS
        fs_NW2_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW2_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW2_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW2_ERS12  (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW3_ASCS
        fs_NW3_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW3_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW3_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW3_ASCS20 (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW3_AERS
        fs_NW3_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl2
        vip_NW3_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl2
        nc_NW3_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl2
        rsc_sap_NW3_ERS22  (ocf::heartbeat:SAPInstance):   Started rhelmsscl2
   ```

1. 노드 작동 중단 시뮬레이트

   테스트 시작 전 리소스 상태:

   ```
    Online: [ rhelmsscl1 rhelmsscl2 ]

    Full list of resources:

    rsc_st_azure   (stonith:fence_azure_arm):      Started rhelmsscl1
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl2
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl2
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl2
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started rhelmsscl2
    Resource Group: g-NW2_ASCS
        fs_NW2_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW2_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW2_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW2_ASCS10 (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW2_AERS
        fs_NW2_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl2
        vip_NW2_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl2
        nc_NW2_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl2
        rsc_sap_NW2_ERS12  (ocf::heartbeat:SAPInstance):   Started rhelmsscl2
    Resource Group: g-NW3_ASCS
        fs_NW3_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW3_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW3_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW3_ASCS20 (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW3_AERS
        fs_NW3_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl2
        vip_NW3_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl2
        nc_NW3_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl2
        rsc_sap_NW3_ERS22  (ocf::heartbeat:SAPInstance):   Started rhelmsscl2
   ```

   하나 이상의 ASCS 인스턴스가 실행 되 고 있는 노드의 루트로 다음 명령을 실행 합니다. 이 예제에서는 N W 1, N W 2 및 NW3의 ASCS 인스턴스가 실행 되는 `rhelmsscl1`에서 명령을 실행 했습니다.  

   ```
   echo c > /proc/sysrq-trigger
   ```

   테스트 후의 상태와 노드가 충돌 한 후 다시 시작 된 후에는 다음과 같이 표시 됩니다.

   ```
    Full list of resources:

    rsc_st_azure    (stonith:fence_azure_arm):      Started rhelmsscl2
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl2
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl2
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl2
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started rhelmsscl2
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW2_ASCS
        fs_NW2_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl2
        vip_NW2_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl2
        nc_NW2_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl2
        rsc_sap_NW2_ASCS10 (ocf::heartbeat:SAPInstance):   Started rhelmsscl2
    Resource Group: g-NW2_AERS
        fs_NW2_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW2_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW2_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW2_ERS12  (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW3_ASCS
        fs_NW3_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl2
        vip_NW3_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl2
        nc_NW3_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl2
        rsc_sap_NW3_ASCS20 (ocf::heartbeat:SAPInstance):   Started rhelmsscl2
    Resource Group: g-NW3_AERS
        fs_NW3_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW3_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW3_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW3_ERS22  (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
   ```

   실패 한 리소스에 대 한 메시지가 있는 경우 실패 한 리소스의 상태를 정리 합니다. 다음은 그 예입니다.

   ```
   pcs resource cleanup rsc_sap_NW1_ERS02
   ```

## <a name="next-steps"></a>다음 단계

* [SAP 용 Azure Virtual Machines 계획 및 구현][planning-guide]
* [SAP 용 Azure Virtual Machines 배포][deployment-guide]
* [SAP 용 Azure Virtual Machines DBMS 배포][dbms-guide]
* Azure Vm에서 SAP HANA의 고가용성을 설정 하 고 재해 복구를 계획 하는 방법에 대 한 자세한 내용은 [azure Virtual Machines (vm)의 SAP HANA 고가용성][sap-hana-ha] 을 참조 하세요.
