---
title: Azure Virtual Machines 고가용성 Azure NetApp 파일을 사용 하 여 Red Hat Enterprise Linux에서 SAP NetWeaver에 대 한 | Microsoft Docs
description: Red Hat Enterprise Linux의 SAP NetWeaver에 대한 Azure Virtual Machines 고가용성
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-windows
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 06/14/2019
ms.author: radeltch
ms.openlocfilehash: 8679cfe54c8fb2c88b312f67ea9b2d7115cc479e
ms.sourcegitcommit: 837dfd2c84a810c75b009d5813ecb67237aaf6b8
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2019
ms.locfileid: "67503579"
---
# <a name="azure-virtual-machines-high-availability-for-sap-netweaver-on-red-hat-enterprise-linux-with-azure-netapp-files-for-sap-applications"></a>Azure Virtual Machines 고가용성 SAP 응용 프로그램에 대 한 Azure NetApp 파일을 사용 하 여 Red Hat Enterprise Linux에서 SAP NetWeaver에 대 한

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

이 문서에서는 가상 컴퓨터를 배포, 가상 컴퓨터를 구성, 클러스터 프레임 워크 및 고가용성 SAP NetWeaver 7.50 시스템을 설치 하는 방법을 설명를 사용 하 여 [Azure NetApp 파일](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-introduction/)합니다.
그리고 예제 구성, 설치 명령 등을 소개합니다. ASCS 인스턴스 번호 00, ERS 인스턴스 번호는 01, 기본 응용 프로그램 인스턴스 (PA) 02 이며 응용 프로그램 인스턴스 AAS ()는 03 경우 SAP 시스템 ID QAS 사용 됩니다. 

데이터베이스 계층은이 문서에서 자세히 적용 되지 않습니다.  

다음 SAP Note 및 문서를 먼저 읽어 보세요.

* [Azure NetApp 파일 설명서][anf-azure-doc] 
* SAP Note [1928533], 다음 항목을 포함합니다.
  * SAP 소프트웨어 배포에 지원되는 Azure VM 크기 목록
  * Azure VM 크기에 대한 중요한 용량 정보
  * 지원되는 SAP 소프트웨어 및 운영 체제(OS)와 데이터베이스 조합
  * Microsoft Azure에서 Windows 및 Linux에 필요한 SAP 커널 버전

* SAP Note [2015553]는 Azure에서 SAP을 지원하는 SAP 소프트웨어 배포에 대한 필수 구성 요소를 나열합니다.
* SAP Note [2002167]에는 Red Hat Enterprise Linux에 권장되는 OS 설정이 있습니다.
* SAP Note [2009879]에는 Red Hat Enterprise Linux용 SAP HANA 지침이 있습니다.
* SAP Note [2178632]는 Azure에서 SAP에 대해 보고된 모든 모니터링 메트릭에 대한 자세한 정보를 포함하고 있습니다.
* SAP Note [2191498]는 Azure에서 Linux에 필요한 SAP Host Agent 버전을 포함하고 있습니다.
* SAP Note [2243692]는 Azure에서 Linux의 SAP 라이선스에 대한 정보를 포함하고 있습니다.
* SAP Note [1999351]은 SAP용 Azure 고급 모니터링 확장을 위한 추가 문제 해결 정보를 포함하고 있습니다.
* [SAP Community WIKI](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes)는 Linux에 필요한 모든 SAP Note를 포함하고 있습니다.
* [Azure Virtual Machines 계획 및 Linux에서 SAP 용 구현][planning-guide]
* [Linux에서 SAP 용 azure Virtual Machines 배포][deployment-guide]
* [Linux에서 SAP 용 azure Virtual Machines DBMS 배포][dbms-guide]
* [pacemaker 클러스터의 SAP Netweaver](https://access.redhat.com/articles/3150081)
* 일반 RHEL 설명서
  * [High Availability Add-On Overview](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_overview/index)(고가용성 추가 기능 개요)
  * [High Availability Add-On Administration](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_administration/index)(고가용성 추가 기능 관리)
  * [고가용성 추가 기능 참조](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_reference/index)
  * [RHEL 7.5에서 독립 실행형 리소스를 사용하여 SAP Netweaver용 ASCS/ERS 구성](https://access.redhat.com/articles/3569681)
  * [RHEL에서 Pacemaker에 독립 실행형 큐에 넣기 서버 2 (ENSA2)를 사용 하 여 SAP S/4HANA ASCS/ERS 구성 ](https://access.redhat.com/articles/3974941)
* Azure 특정 RHEL 설명서:
  * [Support Policies for RHEL High Availability Clusters - Microsoft Azure Virtual Machines as Cluster Members](https://access.redhat.com/articles/3131341)(RHEL 고가용성 클러스터용 지원 정책 - Microsoft Azure Virtual Machines(클러스터 멤버))
  * [Installing and Configuring a Red Hat Enterprise Linux 7.4 (and later) High-Availability Cluster on Microsoft Azure](https://access.redhat.com/articles/3252491)(Microsoft Azure에서 Red Hat Enterprise Linux 7.4 이상 고가용성 클러스터 설치 및 구성)
* [NetApp Azure Files를 사용 하 여 Microsoft Azure의 NetApp SAP 응용 프로그램][anf-sap-applications-azure]

## <a name="overview"></a>개요

높은 availability(HA) 중앙 서비스가 SAP Netweaver에 대 한 공유 저장소가 필요 합니다.
Red Hat Linux에서이 위해 지금 필요 했기 별도 항상 사용 가능한 GlusterFS 클러스터를 구축 합니다. 

이제 목표를 달성할 수 SAP Netweaver HA NetApp 파일을 Azure에 배포 된 공유 저장소를 사용 하 여입니다. 공유 저장소가 필요 하지 않습니다.에 대 한 Azure NetApp 파일을 사용 하 여 추가 [GlusterFS 클러스터](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-glusterfs)합니다. Pacemaker는 고가용성 SAP Netweaver 중앙 services(ASCS/SCS) 여전히 필요 합니다.

![SAP NetWeaver 고가용성 개요](./media/high-availability-guide-rhel/high-availability-guide-rhel-anf.png)

SAP NetWeaver ASCS, SAP NetWeaver SCS, SAP NetWeaver ERS 및 SAP HANA 데이터베이스는 가상 호스트 이름 및 가상 IP 주소를 사용합니다. Azure에서는 가상 IP 주소를 사용하려면 부하 분산 장치가 필요합니다. 다음 목록에는 (A) SCS 및 ERS에 대 한 별도 프런트 Ip 사용 하 여 load balancer의 구성을 보여 줍니다.

> [!IMPORTANT]
> 다중 SID 클러스터링의 SAP ASCS/ERS Red Hat Linux를 사용 하 여 Azure Vm에서 게스트 운영 체제가 **지원 되지 않습니다**합니다. 다중 SID 클러스터링 하나의 Pacemaker 클러스터에서 다른 Sid 사용 하 여 여러 SAP ASCS/ERS 인스턴스 설치를 설명 합니다.

### <a name="ascs"></a>(A)SCS

* 프런트 엔드 구성
  * IP 주소 192.168.14.9
* 백 엔드 구성
  * (A)SCS/ERS 클러스터의 일부분이어야 하는 모든 가상 머신의 주 네트워크 인터페이스에 연결됨
* 프로브 포트
  * 포트 620<strong>&lt;nr&gt;</strong>
* 부하 분산 규칙
  * 32<strong>&lt;nr&gt;</strong> TCP
  * 36<strong>&lt;nr&gt;</strong> TCP
  * 39<strong>&lt;nr&gt;</strong> TCP
  * 81<strong>&lt;nr&gt;</strong> TCP
  * 5<strong>&lt;nr&gt;</strong>13 TCP
  * 5<strong>&lt;nr&gt;</strong>14 TCP
  * 5<strong>&lt;nr&gt;</strong>16 TCP

### <a name="ers"></a>ERS

* 프런트 엔드 구성
  * IP 주소 192.168.14.10
* 백 엔드 구성
  * (A)SCS/ERS 클러스터의 일부분이어야 하는 모든 가상 머신의 주 네트워크 인터페이스에 연결됨
* 프로브 포트
  * 포트 621<strong>&lt;nr&gt;</strong>
* 부하 분산 규칙
  * 32<strong>&lt;nr&gt;</strong> TCP
  * 33<strong>&lt;nr&gt;</strong> TCP
  * 5<strong>&lt;nr&gt;</strong>13 TCP
  * 5<strong>&lt;nr&gt;</strong>14 TCP
  * 5<strong>&lt;nr&gt;</strong>16 TCP

## <a name="setting-up-the-azure-netapp-files-infrastructure"></a>Azure NetApp 파일 인프라를 설정 

SAP NetWeaver에는 전송 및 프로필 디렉터리에 대한 공유 저장소가 필요합니다.  Azure NetApp 파일 인프라에 대 한 설치를 진행 하기 전에 숙지 합니다 [NetApp Azure Files 설명서][anf-azure-doc]합니다. 선택한 Azure 지역에 Azure NetApp 파일 제공 하는 경우를 확인 합니다. 다음 링크를 Azure 지역에 따라 NetApp 파일을 Azure의 가용성을 보여 줍니다. [Azure 지역에 따라 가용성 파일을 azure NetApp][anf-avail-matrix]합니다.

Azure NetApp 파일은 몇 가지 사용 가능한 [Azure 지역](https://azure.microsoft.com/global-infrastructure/services/?products=netapp)합니다. NetApp Azure Files를 배포 하기 전에 Azure NetApp 파일을 다음으로 온 보 딩을 요청 합니다 [Azure NetApp 파일 지침에 대 한 등록][anf-register]합니다. 

### <a name="deploy-azure-netapp-files-resources"></a>NetApp 파일 Azure 리소스 배포  

단계를 이미 배포한 가정 [Azure Virtual Network](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview)합니다. 동일한 Azure Virtual Network 또는 피어 링 된 Azure Virtual Network에서 Azure NetApp 파일 리소스 및 Azure NetApp 파일 리소스를 탑재할 Vm에 배포 되어야 합니다.  

1. 아직 수행 하지 않은, 경우 요청 [NetApp 파일을 Azure에 온 보 딩](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-register)합니다.  
2. 다음 선택한 Azure 지역에서 NetApp 계정을 만들려면 합니다 [NetApp 계정을 만드는 지침은](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-netapp-account)합니다.  
3. 다음 Azure NetApp Files 용량 풀을 설정 합니다 [NetApp 파일 Azure 용량 풀을 설정 하는 방법에 대 한 지침](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-set-up-capacity-pool)합니다.  
이 문서에서 제공 하는 SAP Netweaver 아키텍처는 단일 Azure NetApp Files 용량 풀, 프리미엄 SKU를 사용 합니다. Azure에서 SAP Netweaver 응용 프로그램 워크 로드에 대 한 Azure NetApp 파일 Premium SKU를 권장합니다.  
4. 에 설명 된 대로 Azure NetApp 파일에 서브넷을 위임 합니다 [지침은 위임할 Azure NetApp 파일에 서브넷](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-delegate-subnet)합니다.  

5. 다음 Azure NetApp 파일 볼륨을 배포 하는 [볼륨을 만들려면 Azure NetApp 파일에 대 한 지침](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-volumes)합니다. 지정된 된 Azure NetApp 파일의 볼륨에 배포할 [서브넷](https://docs.microsoft.com/rest/api/virtualnetwork/subnets)합니다. Azure NetApp 파일 리소스 및 Azure Vm을 동일한 Azure Virtual Network 또는 피어 링 된 Azure Virtual Network 수 있어야 하는 점을 염두에 두십시오. 두 개의 Azure NetApp 파일 볼륨 사용 하 여이 예제의: sap<b>QAS</b> 및 transSAP 합니다. 해당 탑재 지점에 탑재 된 파일 경로 /usrsap<b>qas</b>/sapmnt<b>QAS</b>, /usrsap<b>qas</b>/usrsap<b>QAS</b>sys 등  

   1. 볼륨 sap<b>QAS</b> (nfs://192.168.24.5/usrsap<b>qas</b>/sapmnt<b>QAS</b>)
   2. 볼륨 sap<b>QAS</b> (nfs://192.168.24.5/usrsap<b>qas</b>/usrsap<b>QAS</b>ascs)
   3. 볼륨 sap<b>QAS</b> (nfs://192.168.24.5/usrsap<b>qas</b>/usrsap<b>QAS</b>sys)
   4. 볼륨 sap<b>QAS</b> (nfs://192.168.24.5/usrsap<b>qas</b>/usrsap<b>QAS</b>ers)
   5. 볼륨 transSAP (nfs://192.168.24.4/transSAP)
   6. 볼륨 sap<b>QAS</b> (nfs://192.168.24.5/usrsap<b>qas</b>/usrsap<b>QAS</b>pa)
   7. 볼륨 sap<b>QAS</b> (nfs://192.168.24.5/usrsap<b>qas</b>/usrsap<b>QAS</b>aas)
  
이 예제에서는 Azure NetApp 파일 사용할 수 있는 방법을 보여 주기 위해 모든 SAP Netweaver 파일 시스템에 대 한 Azure NetApp 파일을 사용 했습니다. NFS를 통해 탑재 하지 않아도 되는 SAP 파일 시스템으로 배포할 수도 있습니다 [Azure 디스크 저장소](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#premium-ssd) 합니다. 이 예제의 <b>a-e</b> Azure NetApp 파일에 있어야 합니다 및 <b>f g</b> (즉, r/sap/<b>QAS</b>/D<b>02</b>,r/sap/<b>QAS </b>/D<b>03</b>) Azure 디스크 저장소로 배포 될 수 있습니다. 

### <a name="important-considerations"></a>중요 고려 사항

SAP Netweaver 고가용성 SUSE 아키텍처에 대 한 Azure NetApp Files를 고려할 때 다음 중요 사항을 고려해 야 합니다.

- 최소 용량 풀 4 TiB는입니다. 용량 풀 크기 TiB 4의 배수 여야 합니다.
- 최소 볼륨이 100gib
- NetApp Azure Files 볼륨 탑재 될 모든 virtual machines와 azure NetApp 파일이 동일한 Azure Virtual Network 또는 이어야 합니다 [가상 네트워크 피어 링](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) 동일한 지역에 있습니다. 동일한 지역에서 VNET 피어 링을 통해 azure NetApp 파일 액세스는 이제 지원 됩니다. 전역 피어 링을 통해 azure NetApp 액세스가 아직 지원 되지 않습니다.
- 선택한 가상 네트워크에 Azure NetApp 파일에 위임 된 서브넷에 있어야 합니다.
- Azure NetApp 파일에는 현재 NFSv3만 지원 
- Azure NetApp 파일 제공 [정책 내보내기](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-configure-export-policy): 허용 된 클라이언트 액세스 형식 (읽기 및 쓰기, 읽기 전용 등)를 제어할 수 있습니다. 
- Azure NetApp 파일 기능은 아직 영역을 인식 합니다. 현재 Azure NetApp 파일 기능은 Azure 지역에서 모든 가용성 영역에서 배포 되지 않습니다. 일부 Azure 지역에서 잠재적인 대기 시간 영향에 주의 합니다. 

## <a name="setting-up-ascs"></a>(A)SCS 설정

이 예제에서는 리소스를 배포 된 수동으로 통해 합니다 [Azure portal](https://portal.azure.com/#home)합니다.

### <a name="deploy-linux-manually-via-azure-portal"></a>Azure Portal을 통해 Linux를 수동으로 배포

먼저 Azure NetApp 파일 볼륨을 만드는 해야 합니다. Vm을 배포 합니다. 그런 다음, 부하 분산 장치를 만들고 백 엔드 풀의 가상 머신을 사용합니다.

1. 부하 분산 장치(내부) 만들기  
   1. 프런트 엔드 IP 주소 만들기
      1. ASCS에 대 한 IP 주소 192.168.14.9
         1. 부하 분산 장치 열기, 프런트 엔드 IP 풀 선택 및 추가 클릭
         1. 새 프런트 엔드 IP 풀의 이름을 입력 합니다 (예를 들어 **프런트 엔드입니다. QAS 합니다. ASCS**)
         1. 정적으로 할당을 설정 하 고 IP 주소를 입력 (예를 들어 **192.168.14.9**)
         1. 확인 클릭
      1. ASCS ERS에 대 한 IP 주소 192.168.14.10
         * ERS에 대 한 IP 주소를 만들려면 "a"에서 위 단계 반복 (예를 들어 **192.168.14.10** 고 **프런트 엔드입니다. QAS 합니다. ERS**)
   1. 백 엔드 풀 만들기
      1. ASCS에 대한 백 엔드 풀 만들기
         1. 부하 분산 장치를 열고 백 엔드 풀을 선택한 다음 추가 클릭
         1. 새 백 엔드 풀의 이름을 입력 합니다 (예를 들어 **백 엔드입니다. QAS**)
         1. 가상 머신 추가 클릭
         1. ASCS에 대 한 이전에 만든 가용성 집합 선택 
         1. (A)SCS 클러스터의 가상 머신 선택
         1. 확인 클릭
   1. 상태 프로브 만들기
      1. 포트: 620**00**(ASCS용)
         1. 부하 분산 장치를 열고 상태 프로브를 선택한 다음 추가 클릭
         1. 새 상태 프로브의 이름 입력 (예를 들어 **상태입니다. QAS 합니다. ASCS**)
         1. 프로토콜로 TCP를 선택하고, 620**00** 포트를 선택한 다음, 간격은 5, 비정상 임계값은 2로 유지
         1. 확인 클릭
      1. 포트: 621**01** ASCS ERS에 대 한
            * "C" ERS에 대 한 상태 프로브를 만들려면 아래에서 위 단계 반복 (예: 621**01** 고 **상태입니다. QAS 합니다. ERS**)
   1. 부하 분산 규칙
      1. TCP: 32**00**(ASCS용)
         1. 부하 분산 장치를 열고, 부하 분산 규칙을 선택 및 추가 클릭
         1. 새 부하 분산 장치 규칙의 이름을 입력 합니다 (예를 들어 **lb 합니다. QAS 합니다. ASCS.3200**)
         1. ASCS, 백 엔드 풀 및 상태 프로브를 이전에 만든 프런트 엔드 IP 주소를 선택 합니다. (예를 들어 **프런트 엔드입니다. QAS 합니다. ASCS**)
         1. 프로토콜로 **TCP**를 유지하고. 포트로 **3200** 입력
         1. 유휴 상태 시간 제한을 30분으로 증가
         1. **부동 IP를 사용하도록 설정**
         1. 확인 클릭
      1. ASCS에 대한 추가 포트
         * 36 포트에 대 한 "d"에서 위의 단계를 반복**00**, 39**00**, 81**00**, 5**00**13, 5**00**14, 5**00**16 및 ASCS 용 TCP
      1. ASCS ERS에 대한 추가 포트
         * 포트 32에 대 한 "d"에서 위의 단계를 반복**01**, 33**01**, 5**01**13, 5**01**14, 5**01**16 및 TCP ASCS ERS에 대 한


> [!IMPORTANT]
> Azure Load Balancer 뒤에 배치 하는 Azure Vm에서 TCP 타임 스탬프를 사용 하지 마십시오. TCP 타임 스탬프를 사용 하도록 설정 하면 상태 프로브 실패 합니다. 매개 변수를 설정 **net.ipv4.tcp_timestamps** 하 **0**합니다. 자세한 내용은 참조 하십시오 [부하 분산 장치 상태 프로브](https://docs.microsoft.com/azure/load-balancer/load-balancer-custom-probe-overview)합니다.

### <a name="create-pacemaker-cluster"></a>Pacemaker 클러스터 만들기

[Azure의 Red Hat Enterprise Linux에서 Pacemaker 설정](high-availability-guide-rhel-pacemaker.md) 단계에 따라 이 (A)SCS 서버용 기본 Pacemaker 클러스터를 만듭니다.

### <a name="prepare-for-sap-netweaver-installation"></a>SAP NetWeaver 설치 준비

다음 항목에는 접두사 **[A]** (모든 노드에 적용됨), **[1]** (노드 1에만 적용됨), **[2]** (노드 2에만 적용됨) 접두사가 표시되어 있습니다.

1. **[A]** 호스트 이름 확인 설정

   DNS 서버를 사용하거나 모든 노드의 /etc/hosts를 수정할 수 있습니다. 이 예에서는 /etc/hosts 파일 사용 방법을 보여줍니다.
   다음 명령에서 IP 주소와 호스트 이름 바꾸기

    ```
    sudo vi /etc/hosts
    ```

   다음 줄을 /etc/hosts에 삽입합니다. 환경에 맞게 IP 주소와 호스트 이름 변경

    ```
    # IP address of cluster node 1
    192.168.14.5    anftstsapcl1
    # IP address of cluster node 2
    192.168.14.6     anftstsapcl2
    # IP address of the load balancer frontend configuration for SAP Netweaver ASCS
    192.168.14.9    anftstsapvh
    # IP address of the load balancer frontend configuration for SAP Netweaver ERS
    192.168.14.10    anftstsapers
    ```

1. **[1]**  NetApp Azure Files 볼륨에 있는 SAP 만들 디렉터리입니다.  
   Vm 중 하나에서 일시적으로 Azure NetApp 파일 볼륨을 탑재 하 고 SAP 디렉터리 (파일 경로)를 만듭니다.  

    ```
     #mount temporarily the volume
     sudo mkdir -p /saptmp
     sudo mount -t nfs -o rw,hard,rsize=65536,wsize=65536,vers=3,tcp 192.168.24.5:/sapQAS /saptmp
     # create the SAP directories
     sudo cd /saptmp
     sudo mkdir -p sapmntQAS
     sudo mkdir -p usrsapQASascs
     sudo mkdir -p usrsapQASers
     sudo mkdir -p usrsapQASsys
     sudo mkdir -p usrsapQASpas
     sudo mkdir -p usrsapQASaas
     # unmount the volume and delete the temporary directory
     sudo cd ..
     sudo umount /saptmp
     sudo rmdir /saptmp

1. **[A]** Create the shared directories

   ```
   sudo mkdir -p /sapmnt/QAS sudo mkdir -p /usr/sap/trans sudo mkdir -p /usr/sap/QAS/SYS sudo mkdir -p /usr/sap/QAS/ASCS00 sudo mkdir -p /usr/sap/QAS/ERS01
   
   sudo chattr +i /sapmnt/QAS sudo chattr +i /usr/sap/trans sudo chattr +i /usr/sap/QAS/SYS sudo chattr +i /usr/sap/QAS/ASCS00 sudo chattr +i /usr/sap/QAS/ERS01
   ```

1. **[A]** Install NFS client and other requirements

   ```
   sudo yum-y nfs 유틸리티 리소스 에이전트 리소스-에이전트-sap 설치
   ```

1. **[A]** Check version of resource-agents-sap

   Make sure that the version of the installed resource-agents-sap package is at least 3.9.5-124.el7
   ```
   sudo yum 정보 리소스 에이전트-sap
   
   # <a name="loaded-plugins-langpacks-product-id-search-disabled-repos"></a>플러그 인을 로드: langpacks, 제품 id, 검색-사용 안 함-리포지토리
   # <a name="repodata-is-over-2-weeks-old-install-yum-cron-or-run-yum-makecache-fast"></a>Repodata 2 주 동안입니다. Yum cron를 설치 하 시겠습니까? 실행 또는: yum makecache 빠른
   # <a name="installed-packages"></a>설치 된 패키지
   # <a name="name---------resource-agents-sap"></a>이름: 리소스 에이전트 sap
   # <a name="arch---------x8664"></a>Arch        : x86_64
   # <a name="version------395"></a>버전: 3.9.5
   # <a name="release------124el7"></a>릴리스: 124.el7
   # <a name="size---------100-k"></a>크기: 100,000 개
   # <a name="repo---------installed"></a>리포지토리: 설치
   # <a name="from-repo----rhel-sap-for-rhel-7-server-rpms"></a>리포지토리에서: rhel-sap-for-rhel-7-server-rpms
   # <a name="summary------sap-cluster-resource-agents-and-connector-script"></a>요약: SAP 클러스터 리소스 에이전트 및 커넥터 스크립트
   # <a name="url----------httpsgithubcomclusterlabsresource-agents"></a>URL         : https://github.com/ClusterLabs/resource-agents
   # <a name="license------gplv2"></a>라이선스: GPLv2+
   # <a name="description--the-sap-resource-agents-and-connector-script-interface-with"></a>설명: SAP 리소스 에이전트 및 사용 하 여 커넥터 스크립트 인터페이스
   #          <a name="-pacemaker-to-allow-sap-instances-to-be-managed-in-a-cluster"></a>: Pacemaker 클러스터를 관리할 수 있는 SAP 인스턴스를 허용 하려면
   #          <a name="-environment"></a>: 환경입니다.
   ```


1. **[A]** Add mount entries

   ```
   sudo vi /etc/fstab
   
   # <a name="add-the-following-lines-to-fstab-save-and-exit"></a>Fstab에 다음 줄을 추가, 저장 하 고, 종료
    192.168.24.5:/sapQAS/sapmntQAS/sapmnt QAS nfs rw 어려운 rsize = 65536, wsize = 65536, vers 3 192.168.24.5:/sapQAS/usrsapQASsys /usr/sap/QAS/SYS nfs rw 어려운 rsize = 65536, wsize = = 65536, vers 3 192.168.24.4:/transSAP /usr/sap/trans nfs rw 어려운 rsize = = 65536, wsize = 65536, vers = 3
   ```

   Mount the new shares

   ```
   sudo mount -a  
   ```

1. **[A]** Configure SWAP file

   ```
   sudo vi /etc/waagent.conf
   
   # <a name="set-the-property-resourcediskenableswap-to-y"></a>Y로 ResourceDisk.EnableSwap 속성 설정
   # <a name="create-and-use-swapfile-on-resource-disk"></a>만들고 리소스 디스크에서 스왑 파일을 사용 합니다.
   ResourceDisk.EnableSwap=y
   
   # <a name="set-the-size-of-the-swap-file-with-property-resourcediskswapsizemb"></a>ResourceDisk.SwapSizeMB 속성을 사용 하 여 스왑 파일의 크기를 설정 합니다.
   # <a name="the-free-space-of-resource-disk-varies-by-virtual-machine-size-make-sure-that-you-do-not-set-a-value-that-is-too-big-you-can-check-the-swap-space-with-command-swapon"></a>리소스 디스크의 사용 가능한 공간 가상 머신 크기에 따라 다릅니다. 너무 큰 값을 설정 하지 않으면 있는지 확인 합니다. 명령 swapon 사용 하 여 스왑 공간을 확인할 수 있습니다.
   # <a name="size-of-the-swapfile"></a>스왑 파일의 크기입니다.
   ResourceDisk.SwapSizeMB=2000
   ```

   Restart the Agent to activate the change

   ```
   sudo 서비스 waagent 다시 시작
   ```

1. **[A]** RHEL configuration

   Configure RHEL as described in SAP Note [2002167]

### Installing SAP NetWeaver ASCS/ERS

1. **[1]** Create a virtual IP resource and health-probe for the ASCS instance

   ```
   sudo pc 노드 대기 anftstsapcl2
   
   sudo pc 리소스 만들기 fs_QAS_ASCS Filesystem device='192.168.24.5:/sapQAS/usrsapQASascs' \
     디렉터리 = '/ usr/sap/QAS/ASCS00' fstype 이라는 'nfs' = \
     -g QAS_ASCS 그룹
   
   sudo pc 리소스 만들기 vip_QAS_ASCS IPaddr2 \
     ip=192.168.14.9 cidr_netmask=24 \
     -g QAS_ASCS 그룹
   
   sudo pcs resource create nc_QAS_ASCS azure-lb port=62000 \
     -g QAS_ASCS 그룹
   ```

   Make sure that the cluster status is ok and that all resources are started. It is not important on which node the resources are running.

   ```
   sudo pc 상태
   
   # <a name="node-anftstsapcl2-standby"></a>노드 anftstsapcl2: 대기
   # <a name="online--anftstsapcl1-"></a>온라인: [anftstsapcl1]
   #
   # <a name="full-list-of-resources"></a>리소스의 전체 목록:
   #
   # <a name="rscstazure----stonithfenceazurearm------started-anftstsapcl1"></a>rsc_st_azure    (stonith:fence_azure_arm):      시작된 anftstsapcl1
   #  <a name="resource-group-g-qasascs"></a>리소스 그룹: g QAS_ASCS
   #      <a name="fsqasascs--------ocfheartbeatfilesystem----started-anftstsapcl1"></a>fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    시작된 anftstsapcl1
   #      <a name="ncqasascs--------ocfheartbeatazure-lb------started-anftstsapcl1"></a>nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      시작된 anftstsapcl1
   #      <a name="vipqasascs-------ocfheartbeatipaddr2-------started-anftstsapcl1"></a>vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       시작된 anftstsapcl1
   ```

1. **[1]** Install SAP NetWeaver ASCS  

   Install SAP NetWeaver ASCS as root on the first node using a virtual hostname that maps to the IP address of the load balancer frontend configuration for the ASCS, for example <b>anftstsapvh</b>, <b>192.168.14.9</b> and the instance number that you used for the probe of the load balancer, for example <b>00</b>.

   You can use the sapinst parameter SAPINST_REMOTE_ACCESS_USER to allow a non-root user to connect to sapinst.

   ```
   # <a name="allow-access-to-swpm-this-rule-is-not-permanent-if-you-reboot-the-machine-you-have-to-run-the-command-again"></a>SWPM에 대 한 액세스를 허용 합니다. 이 규칙은 영구적입니다. 컴퓨터를 다시 부팅 하는 경우 명령을 다시 실행 해야 합니다.
   sudo firewall-cmd --zone=public  --add-port=4237/tcp
   
   sudo <swpm>/sapinst SAPINST_REMOTE_ACCESS_USER=sapadmin SAPINST_USE_HOSTNAME=<virtual_hostname>
   ```

   If the installation fails to create a subfolder in /usr/sap/**QAS**/ASCS**00**, try setting the owner and group of the ASCS**00** folder and retry.

   ```
   sudo chown qasadm /usr/sap/QAS/ASCS00 sudo chgrp sapsys /usr/sap/QAS/ASCS00
   ```

1. **[1]** Create a virtual IP resource and health-probe for the ERS instance

   ```
   sudo pc 노드 unstandby anftstsapcl2 sudo pc 노드 대기 anftstsapcl1
   
   sudo pc 리소스 만들기 fs_QAS_AERS Filesystem device='192.168.24.5:/sapQAS/usrsapQASers' \
     디렉터리 = '/ usr/sap/QAS/ERS01' fstype 이라는 'nfs' = \
    -g QAS_AERS 그룹

   sudo pc 리소스 만들기 vip_QAS_AERS IPaddr2 \
     ip=192.168.14.10 cidr_netmask=24 \
    -g QAS_AERS 그룹
   
   sudo pcs resource create nc_QAS_AERS azure-lb port=62101 \
    -g QAS_AERS 그룹
   ```
 
   Make sure that the cluster status is ok and that all resources are started. It is not important on which node the resources are running.

   ```
   sudo pc 상태
   
   # <a name="node-anftstsapcl1-standby"></a>노드 anftstsapcl1: 대기
   # <a name="online--anftstsapcl2-"></a>온라인: [anftstsapcl2]
   #
   # <a name="full-list-of-resources"></a>리소스의 전체 목록:
   #
   # <a name="rscstazure----stonithfenceazurearm------started-anftstsapcl2"></a>rsc_st_azure    (stonith:fence_azure_arm):      시작된 anftstsapcl2
   #  <a name="resource-group-g-qasascs"></a>리소스 그룹: g QAS_ASCS
   #      <a name="fsqasascs--------ocfheartbeatfilesystem----started-anftstsapcl2"></a>fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    시작된 anftstsapcl2
   #      <a name="ncqasascs--------ocfheartbeatazure-lb------started-anftstsapcl2"></a>nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Anftstsapcl2를 시작 합니다. <
   #      <a name="vipqasascs-------ocfheartbeatipaddr2-------started-anftstsapcl2"></a>vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       시작된 anftstsapcl2
   #  <a name="resource-group-g-qasaers"></a>리소스 그룹: g QAS_AERS
   #      <a name="fsqasaers--------ocfheartbeatfilesystem----started-anftstsapcl2"></a>fs_QAS_AERS        (ocf::heartbeat:Filesystem):    시작된 anftstsapcl2
   #      <a name="ncqasaers--------ocfheartbeatazure-lb------started-anftstsapcl2"></a>nc_QAS_AERS        (ocf::heartbeat:azure-lb):      시작된 anftstsapcl2
   #      <a name="vipqasaers-------ocfheartbeatipaddr2-------started-anftstsapcl2"></a>vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       시작된 anftstsapcl2
   ```

1. **[2]** Install SAP NetWeaver ERS  

   Install SAP NetWeaver ERS as root on the second node using a virtual hostname that maps to the IP address of the load balancer frontend configuration for the ERS, for example <b>anftstsapers</b>, <b>192.168.14.10</b> and the instance number that you used for the probe of the load balancer, for example <b>01</b>.

   You can use the sapinst parameter SAPINST_REMOTE_ACCESS_USER to allow a non-root user to connect to sapinst.

   ```
   # <a name="allow-access-to-swpm-this-rule-is-not-permanent-if-you-reboot-the-machine-you-have-to-run-the-command-again"></a>SWPM에 대 한 액세스를 허용 합니다. 이 규칙은 영구적입니다. 컴퓨터를 다시 부팅 하는 경우 명령을 다시 실행 해야 합니다.
   sudo firewall-cmd --zone=public  --add-port=4237/tcp

   sudo <swpm>/sapinst SAPINST_REMOTE_ACCESS_USER=sapadmin SAPINST_USE_HOSTNAME=<virtual_hostname>
   ```

   If the installation fails to create a subfolder in /usr/sap/**QAS**/ERS**01**, try setting the owner and group of the ERS**01** folder and retry.

   ```
   sudo chown qaadm /usr/sap/QAS/ERS01 sudo chgrp sapsys /usr/sap/QAS/ERS01
   ```

1. **[1]** Adapt the ASCS/SCS and ERS instance profiles

   * ASCS/SCS profile

   ```
   sudo vi /sapmnt/QAS/profile/QAS_ASCS00_anftstsapvh
   
   # <a name="change-the-restart-command-to-a-start-command"></a>시작 명령에 다시 시작 명령을 변경합니다
   #<a name="restartprogram01--local-en-pfpf"></a>Restart_Program_01 로컬 $(_EN) pf=$(_PF) =
   Start_Program_01 로컬 $(_EN) pf=$(_PF) =
   
   # <a name="add-the-keep-alive-parameter"></a>Keep alive 매개 변수를 추가 합니다.
   enque/encni/set_so_keepalive = true
   ```

   * ERS profile

   ```
   sudo vi /sapmnt/QAS/profile/QAS_ERS01_anftstsapers
   
   # <a name="change-the-restart-command-to-a-start-command"></a>시작 명령에 다시 시작 명령을 변경합니다
   #<a name="restartprogram00--local-er-pfpfl-nrscsid"></a>Restart_Program_00 로컬 $(_ER) pf=$(_PFL) NR=$(SCSID) =
   Start_Program_00 로컬 $(_ER) pf=$(_PFL) NR=$(SCSID) =
   
   # <a name="remove-autostart-from-ers-profile"></a>ERS 프로필에서 자동 시작 제거
   # <a name="autostart--1"></a>Autostart = 1
   ```


1. **[A]** Configure Keep Alive

   The communication between the SAP NetWeaver application server and the ASCS/SCS is routed through a software load balancer. The load balancer disconnects inactive connections after a configurable timeout. To prevent this, you need to set a parameter in the SAP NetWeaver ASCS/SCS profile and change the Linux system settings. Read [SAP Note 1410736][1410736] for more information.

   The ASCS/SCS profile parameter enque/encni/set_so_keepalive was already added in the last step.

   ```
   # <a name="change-the-linux-system-configuration"></a>Linux 시스템 구성을 변경합니다
   sudo sysctl net.ipv4.tcp_keepalive_time=120
   ```

1. **[A]** Update the /usr/sap/sapservices file

   To prevent the start of the instances by the sapinit startup script, all instances managed by Pacemaker must be commented out from /usr/sap/sapservices file. Do not comment out the SAP HANA instance if it will be used with HANA SR.

   ```
   sudo vi /usr/sap/sapservices
   
   # <a name="on-the-node-where-you-installed-the-ascs-comment-out-the-following-line"></a>다음 줄을 주석으로 ASCS 설치한 노드에서
   # <a name="ldlibrarypathusrsapqasascs00exeldlibrarypath-export-ldlibrarypath-usrsapqasascs00exesapstartsrv-pfusrsapqassysprofileqasascs00anftstsapvh--d--u-qasadm"></a>LD_LIBRARY_PATH=/usr/sap/QAS/ASCS00/exe:$LD_LIBRARY_PATH; export LD_LIBRARY_PATH; /usr/sap/QAS/ASCS00/exe/sapstartsrv pf=/usr/sap/QAS/SYS/profile/QAS_ASCS00_anftstsapvh -D -u qasadm
   
   # <a name="on-the-node-where-you-installed-the-ers-comment-out-the-following-line"></a>다음 줄을 주석으로 노드에 ERS 설치 위치
   # <a name="ldlibrarypathusrsapqasers01exeldlibrarypath-export-ldlibrarypath-usrsapqasers01exesapstartsrv-pfusrsapqasers01profileqasers01anftstsapers--d--u-qasadm"></a>LD_LIBRARY_PATH = / usr/sap/QAS/ERS01/exe: $LD_LIBRARY_PATH; LD_LIBRARY_PATH; 내보내기 /usr/sap/QAS/ERS01/exe/sapstartsrv pf = / usr/sap/QAS/ERS01/프로필/QAS_ERS01_anftstsapers-D-u qasadm
   ```

1. **[1]** Create the SAP cluster resources

   If using enqueue server 1 architecture (ENSA1), define the resources as follows:

   ```
   sudo pc 설정 유지 관리 모드 = true
   
    sudo pc 리소스 만들기 rsc_sap_QAS_ASCS00 SAPInstance \
    InstanceName=QAS_ASCS00_anftstsapvh START_PROFILE="/sapmnt/QAS/profile/QAS_ASCS00_anftstsapvh" \
    AUTOMATIC_RECOVER = false \
    meta 리소스 캠페인이 5000 마이그레이션 임계값 = 1 = \
    -g QAS_ASCS 그룹
   
    sudo pcs resource create rsc_sap_QAS_ERS01 SAPInstance \
    InstanceName QAS_ERS01_anftstsapers START_PROFILE = = "/ sapmnt/QAS/프로필/QAS_ERS01_anftstsapers" \
    AUTOMATIC_RECOVER=false IS_ERS=true \
    -g QAS_AERS 그룹
      
    sudo pc 제약 조건 공동 g-QAS_AERS g-QAS_ASCS-5000 sudo pc 제약 조건 위치 rsc_sap_QAS_ASCS00 규칙 점수를 사용 하 여 추가 2000 runs_ers_QAS eq 1 sudo pc 제약 조건을 순서 g QAS_ASCS 다음 g QAS_AERS 종류 = 대칭 Optional = = false
    
    sudo pc 노드 unstandby anftstsapcl1 sudo pc 속성 설정 유지 관리 모드 = false
    ```

   SAP introduced support for enqueue server 2, including replication, as of SAP NW 7.52. Starting with ABAP Platform 1809, enqueue server 2 is installed by default. See SAP note [2630416](https://launchpad.support.sap.com/#/notes/2630416) for enqueue server 2 support.
   If using enqueue server 2 architecture ([ENSA2](https://help.sap.com/viewer/cff8531bc1d9416d91bb6781e628d4e0/1709%20001/en-US/6d655c383abf4c129b0e5c8683e7ecd8.html)), install resource agent resource-agents-sap-4.1.1-12.el7.x86_64 or newer and define the resources as follows:

    ```
    sudo pc 설정 유지 관리 모드 = true
    
    sudo pc 리소스 만들기 rsc_sap_QAS_ASCS00 SAPInstance \
    InstanceName=QAS_ASCS00_anftstsapvh START_PROFILE="/sapmnt/QAS/profile/QAS_ASCS00_anftstsapvh" \
    AUTOMATIC_RECOVER = false \
    meta 리소스 캠페인이 = 5000 \
    -g QAS_ASCS 그룹
   
    sudo pcs resource create rsc_sap_QAS_ERS01 SAPInstance \
    InstanceName QAS_ERS01_anftstsapers START_PROFILE = = "/ sapmnt/QAS/프로필/QAS_ERS01_anftstsapers" \
    AUTOMATIC_RECOVER=false IS_ERS=true \
    -g QAS_AERS 그룹
      
    sudo pc 제약 조건 공동 g-QAS_AERS g-QAS_ASCS-5000 sudo pc 제약 조건 순서 g QAS_ASCS 다음 g QAS_AERS 종류를 사용 하 여 추가 옵션 대칭 = = false
   
    sudo pc 노드 unstandby anftstsapcl1 sudo pc 속성 설정 유지 관리 모드 = false
    ```

   If you are upgrading from an older version and switching to enqueue server 2, see SAP note [2641322](https://launchpad.support.sap.com/#/notes/2641322). 

   Make sure that the cluster status is ok and that all resources are started. It is not important on which node the resources are running.

    ```
    sudo pc 상태
    
    # <a name="online--anftstsapcl1-anftstsapcl2-"></a>온라인: [anftstsapcl1 anftstsapcl2]
    #
    # <a name="full-list-of-resources"></a>리소스의 전체 목록:
    #
    # <a name="rscstazure----stonithfenceazurearm------started-anftstsapcl2"></a>rsc_st_azure    (stonith:fence_azure_arm):      시작된 anftstsapcl2
    #  <a name="resource-group-g-qasascs"></a>리소스 그룹: g QAS_ASCS
    #      <a name="fsqasascs--------ocfheartbeatfilesystem----started-anftstsapcl2"></a>fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    시작된 anftstsapcl2
    #      <a name="ncqasascs--------ocfheartbeatazure-lb------started-anftstsapcl2"></a>nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      시작된 anftstsapcl2
    #      <a name="vipqasascs-------ocfheartbeatipaddr2-------started-anftstsapcl2"></a>vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       시작된 anftstsapcl2
    #      <a name="rscsapqasascs00-ocfheartbeatsapinstance---started-anftstsapcl2"></a>rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   시작된 anftstsapcl2
    #  <a name="resource-group-g-qasaers"></a>리소스 그룹: g QAS_AERS
    #      <a name="fsqasaers--------ocfheartbeatfilesystem----started-anftstsapcl1"></a>fs_QAS_AERS        (ocf::heartbeat:Filesystem):    시작된 anftstsapcl1
    #      <a name="ncqasaers--------ocfheartbeatazure-lb------started-anftstsapcl1"></a>nc_QAS_AERS        (ocf::heartbeat:azure-lb):      시작된 anftstsapcl1
    #      <a name="vipqasaers-------ocfheartbeatipaddr2-------started-anftstsapcl1"></a>vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       시작된 anftstsapcl1
    #      <a name="rscsapqasers01--ocfheartbeatsapinstance---started-anftstsapcl1"></a>rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   시작된 anftstsapcl1
   ```

1. **[A]** Add firewall rules for ASCS and ERS on both nodes
   Add the firewall rules for ASCS and ERS on both nodes.
   ```
   # <a name="probe-port-of-ascs"></a>ASCS의 프로브 포트
   sudo 방화벽-cmd-영역 = public-추가 포트 62000/tcp--= 영구 sudo 방화벽-cmd-영역 = public-추가 포트 62000/tcp sudo 방화벽-cmd-= 영역 = public-추가 포트 3200/tcp--= 영구 sudo 방화벽-cmd-영역 = public-추가 포트 3200/tcp sudo = 방화벽-cmd-영역 공용-추가 포트 = = 3600/tcp-영구 sudo 방화벽-cmd-영역 공용-추가 포트 = = 3600/tcp sudo 방화벽-cmd-영역 = public-추가 포트 3900/tcp--= 영구 sudo 방화벽-cmd-영역 = public-추가 포트 3900/tcp sudo = 방화벽-cmd-영역 = public-추가 포트 8100/tcp--= 영구 sudo 방화벽-cmd-영역 = public-추가 포트 8100/tcp sudo 방화벽-cmd-= 영역 공용-추가 포트 = = 50013/tcp-영구 sudo 방화벽-cmd-영역 공용-추가 포트 = 50013/tcp sudo = 방화벽-cmd-영역 공용-추가 포트 = 50014/tcp--= 영구 sudo 방화벽-cmd-영역 공용-추가 포트 = 50014/tcp sudo 방화벽-cmd-= 영역 = public-추가 포트 50016/tcp--= 영구 sudo 방화벽-cmd-영역 공용-추가 포트 = = 50016/tcp
   # <a name="probe-port-of-ers"></a>ERS의 프로브 포트
   sudo 방화벽-cmd-영역 = public-추가 포트 62101/tcp--= 영구 sudo 방화벽-cmd-영역 공용-추가 포트 = 62101/tcp sudo 방화벽-cmd-= 영역 공용-추가 포트 = 3301/tcp--= 영구 sudo 방화벽-cmd-영역 공용-추가 포트 = 3301/tcp sudo = 방화벽-cmd-영역 = public-추가 포트 50113/tcp--= 영구 sudo 방화벽-cmd-영역 공용-추가 포트 = 50113/tcp sudo 방화벽-cmd-= 영역 공용-추가 포트 = 50114/tcp--= 영구 sudo 방화벽-cmd-영역 공용-추가 포트 = 50114/tcp sudo = 방화벽-cmd-영역 공용-추가 포트 = = 50116/tcp-영구 sudo 방화벽-cmd-영역 공용-추가 포트 = = 50116/tcp
   ```

## <a name="2d6008b0-685d-426c-b59e-6cd281fd45d7"></a>SAP NetWeaver application server preparation

   Some databases require that the database instance installation is executed on an application server. Prepare the application server virtual machines to be able to use them in these cases.  

   The steps bellow assume that you install the application server on a server different from the ASCS/SCS and HANA servers. Otherwise some of the steps below (like configuring host name resolution) are not needed.  

   The following items are prefixed with either **[A]** - applicable to both PAS and AAS, **[P]** - only applicable to PAS or **[S]** - only applicable to AAS.  

1. **[A]** Setup host name resolution
   You can either use a DNS server or modify the /etc/hosts on all nodes. This example shows how to use the /etc/hosts file.
   Replace the IP address and the hostname in the following commands:  

   ```
   sudo vi /etc/hosts
   ```

   Insert the following lines to /etc/hosts. Change the IP address and hostname to match your environment.

   ```
   # <a name="ip-address-of-the-load-balancer-frontend-configuration-for-sap-netweaver-ascs"></a>SAP NetWeaver ASCS 용 부하 분산 장치 프런트 엔드 구성의 IP 주소
   192.168.14.9 anftstsapvh
   # <a name="ip-address-of-the-load-balancer-frontend-configuration-for-sap-netweaver-ascs-ers"></a>SAP NetWeaver ASCS ERS 용 부하 분산 장치 프런트 엔드 구성의 IP 주소
   192.168.14.10 anftstsapers 192.168.14.7 anftstsapa01 192.168.14.8 anftstsapa02
   ```

1. **[A]** Create the sapmnt directory
   Create the sapmnt directory.
   ```
   sudo mkdir -p /sapmnt/QAS sudo mkdir -p /usr/sap/trans

   sudo chattr + i/sapmnt QAS sudo chattr + i /usr/sap/trans
   ```

1. **[A]** Install NFS client and other requirements  

   ```
   sudo yum-y uuidd nfs 유틸리티를 설치 합니다.
   ```

1. **[A]** Add mount entries  

   ```
   sudo vi /etc/fstab
   
   # <a name="add-the-following-lines-to-fstab-save-and-exit"></a>Fstab에 다음 줄을 추가, 저장 하 고, 종료
   192.168.24.5:/sapQAS/sapmntQAS /sapmnt/QAS nfs rw,hard,rsize=65536,wsize=65536,vers=3 192.168.24.4:/transSAP /usr/sap/trans nfs rw,hard,rsize=65536,wsize=65536,vers=3
   ```

   Mount the new shares

   ```
   sudo mount -a
   ```

1. **[P]** Create and mount the PAS directory  

   ```
   sudo mkdir-p /usr/sap/QAS/D02 sudo chattr + i /usr/sap/QAS/D02
   
   sudo vi /etc/fstab
   # <a name="add-the-following-line-to-fstab"></a>Fstab에 다음 줄을 추가 합니다.
   92.168.24.5:/sapQAS/usrsapQASpas /usr/sap/QAS/D02 nfs rw 어려운 rsize = 65536, wsize = 65536, vers = 3
   
   # <a name="mount"></a>탑재
   sudo mount -a
   ```

1. **[S]** Create and mount the AAS directory  

   ```
   sudo mkdir-p /usr/sap/QAS/D03 sudo chattr + i /usr/sap/QAS/D03
   
   sudo vi /etc/fstab
   # <a name="add-the-following-line-to-fstab"></a>Fstab에 다음 줄을 추가 합니다.
   92.168.24.5:/sapQAS/usrsapQASaas /usr/sap/QAS/D03 nfs rw 어려운 rsize = 65536, wsize = 65536, vers = 3
   
   # <a name="mount"></a>탑재
   sudo mount -a
   ```


1. **[A]** Configure SWAP file
 
   ```
   sudo vi /etc/waagent.conf
   
   # <a name="set-the-property-resourcediskenableswap-to-y"></a>Y로 ResourceDisk.EnableSwap 속성 설정
   # <a name="create-and-use-swapfile-on-resource-disk"></a>만들고 리소스 디스크에서 스왑 파일을 사용 합니다.
   ResourceDisk.EnableSwap=y
   
   # <a name="set-the-size-of-the-swap-file-with-property-resourcediskswapsizemb"></a>ResourceDisk.SwapSizeMB 속성을 사용 하 여 스왑 파일의 크기를 설정 합니다.
   # <a name="the-free-space-of-resource-disk-varies-by-virtual-machine-size-make-sure-that-you-do-not-set-a-value-that-is-too-big-you-can-check-the-swap-space-with-command-swapon"></a>리소스 디스크의 사용 가능한 공간 가상 머신 크기에 따라 다릅니다. 너무 큰 값을 설정 하지 않으면 있는지 확인 합니다. 명령 swapon 사용 하 여 스왑 공간을 확인할 수 있습니다.
   # <a name="size-of-the-swapfile"></a>스왑 파일의 크기입니다.
   ResourceDisk.SwapSizeMB=2000
   ```

   Restart the Agent to activate the change

   ```
   sudo 서비스 waagent 다시 시작
   ```

## Install database

In this example, SAP NetWeaver is installed on SAP HANA. You can use every supported database for this installation. For more information on how to install SAP HANA in Azure, see [High availability of SAP HANA on Azure VMs on Red Hat Enterprise Linux][sap-hana-ha]. For a list of supported databases, see [SAP Note 1928533][1928533].

1. Run the SAP database instance installation

   Install the SAP NetWeaver database instance as root using a virtual hostname that maps to the IP address of the load balancer frontend configuration for the database.

   You can use the sapinst parameter SAPINST_REMOTE_ACCESS_USER to allow a non-root user to connect to sapinst.

   ```
   sudo <swpm>/sapinst SAPINST_REMOTE_ACCESS_USER sapadmin =
   ```

## SAP NetWeaver application server installation

Follow these steps to install an SAP application server.

1. Prepare application server

   Follow the steps in the chapter [SAP NetWeaver application server preparation](high-availability-guide-rhel.md#2d6008b0-685d-426c-b59e-6cd281fd45d7) above to prepare the application server.

1. Install SAP NetWeaver application server

   Install a primary or additional SAP NetWeaver applications server.

   You can use the sapinst parameter SAPINST_REMOTE_ACCESS_USER to allow a non-root user to connect to sapinst.

   ```
   sudo <swpm>/sapinst SAPINST_REMOTE_ACCESS_USER sapadmin =
   ```

1. Update SAP HANA secure store

   Update the SAP HANA secure store to point to the virtual name of the SAP HANA System Replication setup.

   Run the following command to list the entries as \<sapsid>adm

   ```
   hdbuserstore 목록
   ```

   This should list all entries and should look similar to
   ```
   DATA FILE       : /home/qasadm/.hdb/anftstsapa01/SSFS_HDB.DAT KEY FILE        : /home/qasadm/.hdb/anftstsapa01/SSFS_HDB.KEY
   
   기본 키 ENV: 192.168.14.4:30313 사용자: SAPABAP1   DATABASE: QAS
   ```

   The output shows that the IP address of the default entry is pointing to the virtual machine and not to the load balancer's IP address. This entry needs to be changed to point to the virtual hostname of the load balancer. Make sure to use the same port (**30313** in the output above) and database name (**QAS** in the output above)!

   ```
   su - qasadm hdbuserstore SET DEFAULT qasdb:30313@QAS SAPABAP1 <password of ABAP schema>
   ```

## Test the cluster setup

1. Manually migrate the ASCS instance

   Resource state before starting the test:

   ```
    rsc_st_azure    (stonith:fence_azure_arm):      Anftstsapcl1 리소스 그룹을 시작: g QAS_ASCS fs_QAS_ASCS (ocf::heartbeat:Filesystem):    Anftstsapcl1 nc_QAS_ASCS 시작 (ocf::heartbeat:azure-lb):      시작된 anftstsapcl1 vip_QAS_ASCS (ocf::heartbeat:IPaddr2):       Anftstsapcl1 rsc_sap_QAS_ASCS00 시작 (ocf::heartbeat:SAPInstance):   Anftstsapcl1 리소스 그룹을 시작: g QAS_AERS fs_QAS_AERS (ocf::heartbeat:Filesystem):    Anftstsapcl2 nc_QAS_AERS 시작 (ocf::heartbeat:azure-lb):      시작된 anftstsapcl2 vip_QAS_AERS (ocf::heartbeat:IPaddr2):       Started anftstsapcl2      rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   시작된 anftstsapcl2
   ```

   Run the following commands as root to migrate the ASCS instance.

   ```
   [root@anftstsapcl1 ~]# pcs resource move rsc_sap_QAS_ASCS00
   
   [root@anftstsapcl1 ~]# pcs resource clear rsc_sap_QAS_ASCS00
   
   # <a name="remove-failed-actions-for-the-ers-that-occurred-as-part-of-the-migration"></a>마이그레이션의 일부로 발생 한 ERS에 대 한 실패 한 작업 제거
   [root@anftstsapcl1 ~]# pcs resource cleanup rsc_sap_QAS_ERS01
   ```

   Resource state after the test:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Anftstsapcl1 리소스 그룹을 시작: g QAS_ASCS fs_QAS_ASCS (ocf::heartbeat:Filesystem):    Anftstsapcl2 nc_QAS_ASCS 시작 (ocf::heartbeat:azure-lb):      시작된 anftstsapcl2 vip_QAS_ASCS (ocf::heartbeat:IPaddr2):       Anftstsapcl2 rsc_sap_QAS_ASCS00 시작 (ocf::heartbeat:SAPInstance):   Anftstsapcl2 리소스 그룹을 시작: g QAS_AERS fs_QAS_AERS (ocf::heartbeat:Filesystem):    Anftstsapcl1 nc_QAS_AERS 시작 (ocf::heartbeat:azure-lb):      시작된 anftstsapcl1 vip_QAS_AERS (ocf::heartbeat:IPaddr2):       Started anftstsapcl1      rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   시작된 anftstsapcl1
   ```

1. Simulate node crash

   Resource state before starting the test:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Anftstsapcl1 리소스 그룹을 시작: g QAS_ASCS fs_QAS_ASCS (ocf::heartbeat:Filesystem):    Anftstsapcl2 nc_QAS_ASCS 시작 (ocf::heartbeat:azure-lb):      시작된 anftstsapcl2 vip_QAS_ASCS (ocf::heartbeat:IPaddr2):       Anftstsapcl2 rsc_sap_QAS_ASCS00 시작 (ocf::heartbeat:SAPInstance):   Anftstsapcl2 리소스 그룹을 시작: g QAS_AERS fs_QAS_AERS (ocf::heartbeat:Filesystem):    Anftstsapcl1 nc_QAS_AERS 시작 (ocf::heartbeat:azure-lb):      시작된 anftstsapcl1 vip_QAS_AERS (ocf::heartbeat:IPaddr2):       Started anftstsapcl1      rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   시작된 anftstsapcl1
   ```

   Run the following command as root on the node where the ASCS instance is running

   ```
   [root@anftstsapcl2 ~]# echo b > /proc/sysrq-trigger
   ```

   The status after the node is started again should look like this.

   ```
   온라인: [anftstsapcl1 anftstsapcl2]
   
   리소스의 전체 목록:
   
   rsc_st_azure    (stonith:fence_azure_arm):      Anftstsapcl1 리소스 그룹을 시작: g QAS_ASCS fs_QAS_ASCS (ocf::heartbeat:Filesystem):    Anftstsapcl1 nc_QAS_ASCS 시작 (ocf::heartbeat:azure-lb):      시작된 anftstsapcl1 vip_QAS_ASCS (ocf::heartbeat:IPaddr2):       Anftstsapcl1 rsc_sap_QAS_ASCS00 시작 (ocf::heartbeat:SAPInstance):   Anftstsapcl1 리소스 그룹을 시작: g QAS_AERS fs_QAS_AERS (ocf::heartbeat:Filesystem):    Anftstsapcl2 nc_QAS_AERS 시작 (ocf::heartbeat:azure-lb):      시작된 anftstsapcl2 vip_QAS_AERS (ocf::heartbeat:IPaddr2):       Started anftstsapcl2      rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   시작된 anftstsapcl2
   
   실패 한 작업의 경우:
   * rsc_sap_QAS_ERS01_monitor_11000 anftstsapcl1에 '실행 중' (7): 호출 = 45, 상태 = 완료 exitreason = ',
   ```

   Use the following command to clean the failed resources.

   ```
   [root@anftstsapcl1 ~]# pcs resource cleanup rsc_sap_QAS_ERS01
   ```

   Resource state after the test:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Anftstsapcl1 리소스 그룹을 시작: g QAS_ASCS fs_QAS_ASCS (ocf::heartbeat:Filesystem):    Anftstsapcl1 nc_QAS_ASCS 시작 (ocf::heartbeat:azure-lb):      시작된 anftstsapcl1 vip_QAS_ASCS (ocf::heartbeat:IPaddr2):       Anftstsapcl1 rsc_sap_QAS_ASCS00 시작 (ocf::heartbeat:SAPInstance):   Anftstsapcl1 리소스 그룹을 시작: g QAS_AERS fs_QAS_AERS (ocf::heartbeat:Filesystem):    Anftstsapcl2 nc_QAS_AERS 시작 (ocf::heartbeat:azure-lb):      시작된 anftstsapcl2 vip_QAS_AERS (ocf::heartbeat:IPaddr2):       Started anftstsapcl2      rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   시작된 anftstsapcl2
   ```

1. Kill message server process

   Resource state before starting the test:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Anftstsapcl1 리소스 그룹을 시작: g QAS_ASCS fs_QAS_ASCS (ocf::heartbeat:Filesystem):    Anftstsapcl1 nc_QAS_ASCS 시작 (ocf::heartbeat:azure-lb):      시작된 anftstsapcl1 vip_QAS_ASCS (ocf::heartbeat:IPaddr2):       Anftstsapcl1 rsc_sap_QAS_ASCS00 시작 (ocf::heartbeat:SAPInstance):   Anftstsapcl1 리소스 그룹을 시작: g QAS_AERS fs_QAS_AERS (ocf::heartbeat:Filesystem):    Anftstsapcl2 nc_QAS_AERS 시작 (ocf::heartbeat:azure-lb):      시작된 anftstsapcl2 vip_QAS_AERS (ocf::heartbeat:IPaddr2):       Started anftstsapcl2      rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   시작된 anftstsapcl2
   ```
   
   Run the following commands as root to identify the process of the message server and kill it.

   ```
   [root@anftstsapcl1 ~]# pgrep ms.sapQAS | xargs kill -9
   ```

   If you only kill the message server once, it will be restarted by `sapstart`. If you kill it often enough, Pacemaker will eventually move the ASCS instance to the other node. Run the following commands as root to clean up the resource state of the ASCS and ERS instance after the test.

   ```
   [root@anftstsapcl1 ~]# pcs resource cleanup rsc_sap_QAS_ASCS00 [root@anftstsapcl1 ~]# pcs resource cleanup rsc_sap_QAS_ERS01
   ```

   Resource state after the test:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Anftstsapcl1 리소스 그룹을 시작: g QAS_ASCS fs_QAS_ASCS (ocf::heartbeat:Filesystem):    Anftstsapcl2 nc_QAS_ASCS 시작 (ocf::heartbeat:azure-lb):      시작된 anftstsapcl2 vip_QAS_ASCS (ocf::heartbeat:IPaddr2):       Anftstsapcl2 rsc_sap_QAS_ASCS00 시작 (ocf::heartbeat:SAPInstance):   Anftstsapcl2 리소스 그룹을 시작: g QAS_AERS fs_QAS_AERS (ocf::heartbeat:Filesystem):    Anftstsapcl1 nc_QAS_AERS 시작 (ocf::heartbeat:azure-lb):      시작된 anftstsapcl1 vip_QAS_AERS (ocf::heartbeat:IPaddr2):       Started anftstsapcl1      rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   시작된 anftstsapcl1
   ```

1. Kill enqueue server process

   Resource state before starting the test:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Anftstsapcl1 리소스 그룹을 시작: g QAS_ASCS fs_QAS_ASCS (ocf::heartbeat:Filesystem):    Anftstsapcl2 nc_QAS_ASCS 시작 (ocf::heartbeat:azure-lb):      시작된 anftstsapcl2 vip_QAS_ASCS (ocf::heartbeat:IPaddr2):       Anftstsapcl2 rsc_sap_QAS_ASCS00 시작 (ocf::heartbeat:SAPInstance):   Anftstsapcl2 리소스 그룹을 시작: g QAS_AERS fs_QAS_AERS (ocf::heartbeat:Filesystem):    Anftstsapcl1 nc_QAS_AERS 시작 (ocf::heartbeat:azure-lb):      시작된 anftstsapcl1 vip_QAS_AERS (ocf::heartbeat:IPaddr2):       Started anftstsapcl1      rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   시작된 anftstsapcl1
   ```

   Run the following commands as root on the node where the ASCS instance is running to kill the enqueue server.

   ```
   [root@anftstsapcl2 ~]# pgrep en.sapQAS | xargs kill -9
   ```

   The ASCS instance should immediately fail over to the other node. The ERS instance should also fail over after the ASCS instance is started. Run the following commands as root to clean up the resource state of the ASCS and ERS instance after the test.

   ```
   [root@anftstsapcl2 ~]# pcs resource cleanup rsc_sap_QAS_ASCS00 [root@anftstsapcl2 ~]# pcs resource cleanup rsc_sap_QAS_ERS01
   ```

   Resource state after the test:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Anftstsapcl1 리소스 그룹을 시작: g QAS_ASCS fs_QAS_ASCS (ocf::heartbeat:Filesystem):    Anftstsapcl1 nc_QAS_ASCS 시작 (ocf::heartbeat:azure-lb):      시작된 anftstsapcl1 vip_QAS_ASCS (ocf::heartbeat:IPaddr2):       Anftstsapcl1 rsc_sap_QAS_ASCS00 시작 (ocf::heartbeat:SAPInstance):   Anftstsapcl1 리소스 그룹을 시작: g QAS_AERS fs_QAS_AERS (ocf::heartbeat:Filesystem):    Anftstsapcl2 nc_QAS_AERS 시작 (ocf::heartbeat:azure-lb):      시작된 anftstsapcl2 vip_QAS_AERS (ocf::heartbeat:IPaddr2):       Started anftstsapcl2      rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   시작된 anftstsapcl2
   ```

1. Kill enqueue replication server process

   Resource state before starting the test:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Anftstsapcl1 리소스 그룹을 시작: g QAS_ASCS fs_QAS_ASCS (ocf::heartbeat:Filesystem):    Anftstsapcl1 nc_QAS_ASCS 시작 (ocf::heartbeat:azure-lb):      시작된 anftstsapcl1 vip_QAS_ASCS (ocf::heartbeat:IPaddr2):       Anftstsapcl1 rsc_sap_QAS_ASCS00 시작 (ocf::heartbeat:SAPInstance):   Anftstsapcl1 리소스 그룹을 시작: g QAS_AERS fs_QAS_AERS (ocf::heartbeat:Filesystem):    Anftstsapcl2 nc_QAS_AERS 시작 (ocf::heartbeat:azure-lb):      시작된 anftstsapcl2 vip_QAS_AERS (ocf::heartbeat:IPaddr2):       Started anftstsapcl2      rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   시작된 anftstsapcl2
   ```

   Run the following command as root on the node where the ERS instance is running to kill the enqueue replication server process.

   ```
   [root@anftstsapcl2 ~]# pgrep er.sapQAS | xargs kill -9
   ```

   If you only run the command once, `sapstart` will restart the process. If you run it often enough, `sapstart` will not restart the process and the resource will be in a stopped state. Run the following commands as root to clean up the resource state of the ERS instance after the test.

   ```
   [root@anftstsapcl2 ~]# pcs resource cleanup rsc_sap_QAS_ERS01
   ```

   Resource state after the test:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Anftstsapcl1 리소스 그룹을 시작: g QAS_ASCS fs_QAS_ASCS (ocf::heartbeat:Filesystem):    Anftstsapcl1 nc_QAS_ASCS 시작 (ocf::heartbeat:azure-lb):      시작된 anftstsapcl1 vip_QAS_ASCS (ocf::heartbeat:IPaddr2):       Anftstsapcl1 rsc_sap_QAS_ASCS00 시작 (ocf::heartbeat:SAPInstance):   Anftstsapcl1 리소스 그룹을 시작: g QAS_AERS fs_QAS_AERS (ocf::heartbeat:Filesystem):    Anftstsapcl2 nc_QAS_AERS 시작 (ocf::heartbeat:azure-lb):      시작된 anftstsapcl2 vip_QAS_AERS (ocf::heartbeat:IPaddr2):       Started anftstsapcl2      rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   시작된 anftstsapcl2
   ```

1. Kill enqueue sapstartsrv process

   Resource state before starting the test:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Anftstsapcl1 리소스 그룹을 시작: g QAS_ASCS fs_QAS_ASCS (ocf::heartbeat:Filesystem):    Anftstsapcl1 nc_QAS_ASCS 시작 (ocf::heartbeat:azure-lb):      시작된 anftstsapcl1 vip_QAS_ASCS (ocf::heartbeat:IPaddr2):       Anftstsapcl1 rsc_sap_QAS_ASCS00 시작 (ocf::heartbeat:SAPInstance):   Anftstsapcl1 리소스 그룹을 시작: g QAS_AERS fs_QAS_AERS (ocf::heartbeat:Filesystem):    Anftstsapcl2 nc_QAS_AERS 시작 (ocf::heartbeat:azure-lb):      시작된 anftstsapcl2 vip_QAS_AERS (ocf::heartbeat:IPaddr2):       Started anftstsapcl2      rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   시작된 anftstsapcl2
   ```

   Run the following commands as root on the node where the ASCS is running.

   ```
   [root@anftstsapcl1 ~]# pgrep -fl ASCS00.*sapstartsrv
   # <a name="59545-sapstartsrv"></a>59545 sapstartsrv
   
   [root@anftstsapcl1 ~]# kill -9 59545
   ```

   The sapstartsrv process should always be restarted by the Pacemaker resource agent as part of the monitoring. Resource state after the test:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Anftstsapcl1 리소스 그룹을 시작: g QAS_ASCS fs_QAS_ASCS (ocf::heartbeat:Filesystem):    Anftstsapcl1 nc_QAS_ASCS 시작 (ocf::heartbeat:azure-lb):      시작된 anftstsapcl1 vip_QAS_ASCS (ocf::heartbeat:IPaddr2):       Anftstsapcl1 rsc_sap_QAS_ASCS00 시작 (ocf::heartbeat:SAPInstance):   Anftstsapcl1 리소스 그룹을 시작: g QAS_AERS fs_QAS_AERS (ocf::heartbeat:Filesystem):    Anftstsapcl2 nc_QAS_AERS 시작 (ocf::heartbeat:azure-lb):      시작된 anftstsapcl2 vip_QAS_AERS (ocf::heartbeat:IPaddr2):       Started anftstsapcl2      rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   시작된 anftstsapcl2
   ```

## Next steps

* [Azure Virtual Machines planning and implementation for SAP][planning-guide]
* [Azure Virtual Machines deployment for SAP][deployment-guide]
* [Azure Virtual Machines DBMS deployment for SAP][dbms-guide]
* To learn how to establish high availability and plan for disaster recovery of SAP HANA on Azure (large instances), see [SAP HANA (large instances) high availability and disaster recovery on Azure](hana-overview-high-availability-disaster-recovery.md).
* To learn how to establish high availability and plan for disaster recovery of SAP HANA on Azure VMs, see [High Availability of SAP HANA on Azure Virtual Machines (VMs)][sap-hana-ha]
