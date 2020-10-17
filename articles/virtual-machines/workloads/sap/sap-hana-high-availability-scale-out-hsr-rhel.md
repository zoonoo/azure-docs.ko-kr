---
title: RHEL에서 HSR 및 Pacemaker를 사용 하 여 확장 SAP HANA | Microsoft Docs
description: RHEL에서 HSR 및 Pacemaker를 사용 하 여 확장 SAP HANA
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
ms.date: 10/16/2020
ms.author: radeltch
ms.openlocfilehash: 520a7649942fc5186d32020853b98297ef8b34d7
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/17/2020
ms.locfileid: "92152112"
---
# <a name="high-availability-of-sap-hana-scale-out-system-on-red-hat-enterprise-linux"></a>Red Hat Enterprise Linux에서 SAP HANA 스케일 아웃 시스템의 고가용성 

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md

[anf-azure-doc]:https://docs.microsoft.com/azure/azure-netapp-files/
[anf-avail-matrix]:https://azure.microsoft.com/global-infrastructure/services/?products=netapp&regions=all 
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
[1900823]:https://launchpad.support.sap.com/#/notes/1900823
[2292690]:https://launchpad.support.sap.com/#/notes/2292690
[2455582]:https://launchpad.support.sap.com/#/notes/2455582
[2593824]:https://launchpad.support.sap.com/#/notes/2593824
[2009879]:https://launchpad.support.sap.com/#/notes/2009879

[sap-swcenter]:https://support.sap.com/en/my-support/software-downloads.html

[sap-hana-ha]:sap-hana-high-availability.md
[nfs-ha]:high-availability-guide-suse-nfs.md


이 문서에서는 Azure Red Hat Enterprise Linux Vm (가상 머신)에서 HSR (HANA system replication) 및 Pacemaker를 사용 하 여 스케일 아웃 구성에서 항상 사용 가능한 SAP HANA 시스템을 배포 하는 방법을 설명 합니다. 제공 된 아키텍처의 공유 파일 시스템은 [Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-introduction.md) 에서 제공 하며 NFS를 통해 탑재 됩니다.  

예제 구성, 설치 명령 등에서 HANA 인스턴스는 **03** 이 고 HANA 시스템 ID는 **h n 1**입니다. 예제는 HANA 2.0 SP4 및 SAP 7.6 용 Red Hat Enterprise Linux을 기반으로 합니다. 

시작 하기 전에 다음 SAP note 및 백서를 참조 하세요.

* SAP Note [1928533] 에는 다음이 포함 됩니다.  
  * SAP 소프트웨어 배포에 지원 되는 Azure VM 크기 목록
  * Azure VM 크기에 대한 중요한 용량 정보
  * 지원되는 SAP 소프트웨어 및 운영 체제(OS)와 데이터베이스 조합
  * Microsoft Azure의 Windows 및 Linux에 필요한 SAP 커널 버전
* SAP Note [2015553]: AZURE에서 SAP 지원 sap 소프트웨어 배포에 대 한 필수 구성 요소를 나열 합니다.
* SAP Note [2002167] Red Hat Enterprise Linux에 대해 권장 되는 OS 설정
* SAP Note [2009879]에는 Red Hat Enterprise Linux용 SAP HANA 지침이 있습니다.
* SAP Note [2178632]: AZURE에서 sap에 대해 보고 된 모든 모니터링 메트릭에 대 한 자세한 정보를 포함 합니다.
* SAP Note [2191498]: Azure에서 Linux에 필요한 Sap Host Agent 버전을 포함 합니다.
* SAP Note [2243692]: Azure에서 LINUX의 sap 라이선스에 대 한 정보 포함
* SAP Note [1999351]: Sap 용 Azure 고급 모니터링 확장에 대 한 추가 문제 해결 정보가 포함 되어 있습니다.
* SAP Note [1900823]: SAP HANA 저장소 요구 사항에 대 한 정보를 포함 합니다.
* [Sap Community Wiki](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes): Linux에 필요한 모든 SAP note를 포함 합니다.
* [Linux에서 SAP용 Azure Virtual Machines 계획 및 구현][planning-guide]
* [Linux에서 SAP용 Azure Virtual Machines 배포][deployment-guide]
* [Linux에서 SAP용 Azure Virtual Machines DBMS 배포][dbms-guide]
* [네트워크 요구 사항 SAP HANA](https://www.sap.com/documents/2016/08/1cd2c2fb-807c-0010-82c7-eda71af511fa.html)
* 일반 RHEL 설명서
  * [High Availability Add-On Overview](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_overview/index)(고가용성 추가 기능 개요)
  * [High Availability Add-On Administration](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_administration/index)(고가용성 추가 기능 관리)
  * [High Availability Add-On Reference](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_reference/index)(고가용성 추가 기능 참조)
  * [Red Hat Enterprise Linux 네트워킹 가이드](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/networking_guide)
  * [NFS 공유의 HANA 파일 시스템을 사용 하 여 Pacemaker 클러스터에서 SAP HANA Scale-Out 시스템 복제를 구성 어떻게 할까요?](https://access.redhat.com/solutions/5423971)
* Azure 특정 RHEL 설명서:
  * [Install SAP HANA on Red Hat Enterprise Linux for Use in Microsoft Azure](https://access.redhat.com/public-cloud/microsoft-azure)(Microsoft Azure에서 사용할 용도로 Red Hat Enterprise Linux에 SAP HANA 설치)
  * [SAP HANA Scale-Out 및 시스템 복제를 위한 Red Hat Enterprise Linux 솔루션](https://access.redhat.com/solutions/4386601)
* [Azure NetApp Files를 사용하는 Microsoft Azure의 NetApp SAP 애플리케이션][anf-sap-applications-azure]
* [Azure NetApp Files 설명서][anf-azure-doc] 


## <a name="overview"></a>개요

Hana 확장 설치에 대해 HANA 고가용성을 구현 하는 한 가지 방법은 HANA 시스템 복제를 구성 하 고 자동 장애 조치 (failover)를 허용 하도록 Pacemaker 클러스터를 사용 하 여 솔루션을 보호 하는 것입니다. 활성 노드에 오류가 발생 하면 클러스터는 HANA 리소스를 다른 사이트로 장애 조치 (failover) 합니다.  
표시 된 구성에는 각 사이트에 세 개의 HANA 노드가 표시 되 고, 분할 된 두뇌 시나리오를 방지 하기 위한 과반수 maker 노드가 표시 됩니다. 추가 Vm을 HANA DB 노드로 포함 하도록 지침을 적용할 수 있습니다.  

제공 된 아키텍처의 HANA 공유 파일 시스템 `/hana/shared` 은 [Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-introduction.md)에 의해 제공 됩니다. 동일한 HANA 시스템 복제 사이트의 각 HANA 노드에 NFSv 4.1을 통해 탑재 됩니다. 파일 시스템 `/hana/data` 및 `/hana/log` 은 로컬 파일 시스템 이며 HANA DB 노드 간에 공유 되지 않습니다. SAP HANA는 비공유 모드로 설치 됩니다. 

> [!TIP]
> 권장 SAP HANA 저장소 구성은 [Azure vm 저장소 구성 SAP HANA](./hana-vm-operations-storage.md)을 참조 하세요.   

[![HSR 및 Pacemaker 클러스터를 사용 하 여 확장 SAP HANA](./media/sap-hana-high-availability-rhel/sap-hana-high-availability-scale-out-hsr-rhel.png)](./media/sap-hana-high-availability-rhel/sap-hana-high-availability-scale-out-hsr-rhel-detail.png#lightbox)

위의 다이어그램에서 3 개의 서브넷은 단일 Azure 가상 네트워크 내에 표시 되며 SAP HANA 네트워크 권장 사항에 따라 다음과 같이 표시 됩니다. 
* 클라이언트 통신의 경우- `client` 10.23.0.0/24  
* 내부 HANA 노드 간 통신- `inter` 10.23.1.128/26  
* HANA 시스템 복제의 경우- `hsr` 10.23.1.192/26  

`/hana/data`및 `/hana/log` 는 로컬 디스크에 배포 되므로 저장소에 대 한 통신을 위해 별도의 서브넷 및 별도의 가상 네트워크 카드를 배포할 필요가 없습니다.  

Azure NetApp 볼륨은 별도의 서브넷에 배포 됩니다 ([Azure NetApp Files에 위임 됨] ( https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-delegate-subnet: `anf` 10.23.1.0/26).   

## <a name="set-up-the-infrastructure"></a>인프라 설정

다음 지침에서는 `client` , 및 라는 세 개의 azure 네트워크 서브넷이 있는 azure virtual network 라는 리소스 그룹을 이미 만들었다고 가정 합니다. `inter` `hsr`

### <a name="deploy-linux-virtual-machines-via-the-azure-portal"></a>Azure Portal을 통해 Linux 가상 컴퓨터 배포
1. Azure Vm을 배포 합니다.  
이 문서에 나와 있는 구성의 경우 7 개의 가상 머신을 배포 합니다. 
   - hana 복제 사이트 1에 대 한 HANA DB 노드로 사용할 3 개의 가상 머신: **hana-s1-db1**, **hana-s1-db2** 및 hana- **s1-db3**  
   - hana 복제 사이트 2에 대 한 HANA DB 노드로 사용할 3 개의 가상 머신 ( **db1**, **hana-s2-db2** 및 hana- **s2-db3**  
   - *과반수 작성자*역할을 하는 작은 가상 머신: **hana-s-mm**

   SAP DB HANA 노드로 배포 된 Vm은 [SAP HANA 하드웨어 디렉터리](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)에 게시 된 대로 sap에 대해 sap에서 인증 되어야 합니다. HANA DB 노드를 배포할 때 [가속화 된 네트워크](../../../virtual-network/create-vm-accelerated-networking-cli.md) 가 선택 되어 있는지 확인 합니다.  
  
   과반수 작성자 노드의 경우이 VM이 SAP HANA 리소스를 실행 하지 않으므로 작은 VM을 배포할 수 있습니다. 대다수 작성자 VM은 분할 된 두뇌 시나리오에서 클러스터 노드 수를 홀수 개 얻기 위해 클러스터 구성에 사용 됩니다. 이 예제에서는 과반수 maker VM이 서브넷에 하나의 가상 네트워크 인터페이스만 필요 `client` 합니다.        

   및에 대 한 로컬 관리 디스크를 배포 `/hana/data` `/hana/log` 합니다. 및에 대 한 최소 권장 저장소 구성은 `/hana/data` `/hana/log` [SAP HANA Azure vm 저장소 구성](./hana-vm-operations-storage.md)에 설명 되어 있습니다.

   가상 네트워크 서브넷의 각 VM에 대 한 기본 네트워크 인터페이스를 배포 `client` 합니다.  
   Azure Portal를 통해 VM을 배포 하는 경우 네트워크 인터페이스 이름이 자동으로 생성 됩니다. 편의를 위해이 지침에서는 자동으로 생성 된 기본 네트워크 인터페이스를 참조 합니다 .이는 `client` Azure 가상 네트워크 서브넷에 **hana-db1**, **hana-** db3, hana- **s1-** 등으로 연결 됩니다.  


   > [!IMPORTANT]
   > 선택한 OS가 사용 중인 특정 VM 형식에 대 한 SAP HANA에 대해 SAP 인증 되는지 확인 합니다. 이러한 유형에 대 한 SAP HANA 인증 VM 유형 및 OS 릴리스 목록은 [SAP HANA 인증 된 IaaS 플랫폼](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) 사이트로 이동 합니다. 나열 된 VM 형식에 대 한 세부 정보를 클릭 하 여 해당 형식에 대해 지원 되는 SAP HANA OS 릴리스의 전체 목록을 가져옵니다.  
  

2. 가상 네트워크 서브넷에서 각 HANA DB 가상 머신에 대해 하나씩 6 개의 네트워크 인터페이스를 만듭니다 `inter` (이 예에서는 hana-s1- **db1** **, hana** **-s2-db1**, **hana-s2**- **db3**, hana- **s2-db3**-s-s-s-s-간).  

3. 6 개의 네트워크 인터페이스를 만듭니다. 각 HANA DB 가상 컴퓨터에 대 한 하나는 `hsr` 가상 네트워크 서브넷에 있습니다 (이 예에서는 hana-s1- **db1-hsr** **, hana**-db3-hsr, hana- **db1-hsr**, **hana**- **hana-s1-db3-hsr**-hsr, hana- **s2-db3-hsr**).  

4. 새로 만든 가상 네트워크 인터페이스를 해당 가상 컴퓨터에 연결 합니다.  

    a. [Azure Portal](https://portal.azure.com/#home)의 가상 머신으로 이동 합니다.  

    b. 왼쪽 창에서 **Virtual Machines**을 선택 합니다. 가상 컴퓨터 이름 (예: **db1**)을 필터링 한 다음 가상 컴퓨터를 선택 합니다.  

    다. **개요** 창에서 **중지** 를 선택 하 여 가상 컴퓨터의 할당을 취소 합니다.  

    d. **네트워킹**을 선택 하 고 네트워크 인터페이스를 연결 합니다. **네트워크 인터페이스 연결** 드롭다운 목록에서 및 서브넷에 대해 이미 생성 된 네트워크 인터페이스를 선택 합니다 `inter` `hsr` .  
    
    e. **저장**을 선택합니다. 
 
    f. 나머지 가상 머신에 대해 b ~ e 단계를 반복 합니다 (이 예제에서는  **hana-s1-db2**, hana- **db3**, **hana-s2-db1**, **hana-s2** - **db3**).
 
    g. 지금은 가상 컴퓨터를 중지 됨 상태로 둡니다. 다음에는 새로 연결 된 모든 네트워크 인터페이스에 대해 [가속화 된 네트워킹](../../../virtual-network/create-vm-accelerated-networking-cli.md) 을 사용 하도록 설정 합니다.  

5. `inter`다음 단계를 수행 하 여 및 서브넷에 대 한 추가 네트워크 인터페이스에 대해 가속화 된 네트워킹을 사용 하도록 설정 합니다 `hsr` .  

    a. [Azure Portal](https://portal.azure.com/#home)에서 [Azure Cloud Shell](https://azure.microsoft.com/features/cloud-shell/) 를 엽니다.  

    b. 다음 명령을 실행 하 여 및 서브넷에 연결 된 추가 네트워크 인터페이스에 대 한 가속화 된 네트워킹을 사용 하도록 설정 `inter` `hsr` 합니다.  

    ```
    az network nic update --id /subscriptions/your subscription/resourceGroups/your resource group/providers/Microsoft.Network/networkInterfaces/hana-s1-db1-inter --accelerated-networking true
    az network nic update --id /subscriptions/your subscription/resourceGroups/your resource group/providers/Microsoft.Network/networkInterfaces/hana-s1-db2-inter --accelerated-networking true
    az network nic update --id /subscriptions/your subscription/resourceGroups/your resource group/providers/Microsoft.Network/networkInterfaces/hana-s1-db3-inter --accelerated-networking true
    az network nic update --id /subscriptions/your subscription/resourceGroups/your resource group/providers/Microsoft.Network/networkInterfaces/hana-s2-db1-inter --accelerated-networking true
    az network nic update --id /subscriptions/your subscription/resourceGroups/your resource group/providers/Microsoft.Network/networkInterfaces/hana-s2-db2-inter --accelerated-networking true
    az network nic update --id /subscriptions/your subscription/resourceGroups/your resource group/providers/Microsoft.Network/networkInterfaces/hana-s2-db3-inter --accelerated-networking true
    
    az network nic update --id /subscriptions/your subscription/resourceGroups/your resource group/providers/Microsoft.Network/networkInterfaces/hana-s1-db1-hsr --accelerated-networking true
    az network nic update --id /subscriptions/your subscription/resourceGroups/your resource group/providers/Microsoft.Network/networkInterfaces/hana-s1-db2-hsr --accelerated-networking true
    az network nic update --id /subscriptions/your subscription/resourceGroups/your resource group/providers/Microsoft.Network/networkInterfaces/hana-s1-db3-hsr --accelerated-networking true
    az network nic update --id /subscriptions/your subscription/resourceGroups/your resource group/providers/Microsoft.Network/networkInterfaces/hana-s2-db1-hsr --accelerated-networking true
    az network nic update --id /subscriptions/your subscription/resourceGroups/your resource group/providers/Microsoft.Network/networkInterfaces/hana-s2-db2-hsr --accelerated-networking true
    az network nic update --id /subscriptions/your subscription/resourceGroups/your resource group/providers/Microsoft.Network/networkInterfaces/hana-s2-db3-hsr --accelerated-networking true
    ```

7. HANA DB 가상 머신 시작

### <a name="deploy-azure-load-balancer"></a>Azure Load Balancer 배포

1. 표준 부하 분산 장치를 사용 하는 것이 좋습니다. 표준 부하 분산 장치를 배포 하려면 다음 구성 단계를 따르세요.
   1. 먼저 프런트 엔드 IP 풀을 만듭니다.

      1. 부하 분산 장치를 열고, **프런트 엔드 IP 풀**을 선택하고, **추가**를 선택합니다.
      1. 새 프런트 엔드 IP 풀의 이름을 입력합니다(예: **hana-frontend**).
      1. **할당** 을 **정적** 으로 설정 하 고 IP 주소를 입력 합니다 (예: **10.23.0.18**).
      1. **확인**을 선택합니다.
      1. 새 프런트 엔드 IP 풀을 만든 후, 풀 IP 주소를 적어 둡니다.

   1. 다음으로 백 엔드 풀을 만들고 모든 클러스터 Vm을 백 엔드 풀에 추가 합니다.

      1. 부하 분산 장치를 열고, **백 엔드 풀**을 선택한 다음, **추가**를 클릭합니다.
      1. 새 백 엔드 풀의 이름 입력합니다(예: **hana-backend**).
      1. **가상 머신 추가**를 선택합니다.
      1. **가상 머신**을 선택합니다.
      1. SAP HANA 클러스터의 가상 머신 및 서브넷에 대 한 IP 주소를 선택 합니다 `client` .
      1. **추가**를 선택합니다.

   1. 다음으로, 상태 프로브를 만듭니다.

      1. 부하 분산 장치를 열고, **상태 프로브**를 선택한 다음, **추가**를 선택합니다.
      1. 새 상태 프로브의 이름을 입력합니다(예: **hana-hp**).
      1. 프로토콜 및 포트 625**03**으로 **TCP**를 선택합니다. 5로 설정된 **간격** 값, 2로 설정된 **비정상 임계값** 값을 유지합니다.
      1. **확인**을 선택합니다.

   1. 다음으로 부하 분산 규칙을 만듭니다.
   
      1. 부하 분산 장치를 열고, **부하 분산 규칙**을 선택한 다음, **추가**를 선택합니다.
      1. 새 부하 분산 장치 규칙의 이름을 입력합니다(예: **hana-lb**).
      1. 이전에 만든 프런트 엔드 IP 주소, 백 엔드 풀 및 상태 프로브를 선택합니다(예: **hana-frontend**, **hana-backend** 및 **hana-hp**).
      1. **HA 포트**를 선택합니다.
      1. **유휴 상태 시간 제한**을 30분으로 증가시킵니다.
      1. **부동 IP를 사용하도록 설정**했는지 확인합니다.
      1. **확인**을 선택합니다.

   > [!IMPORTANT]
   > 부동 IP는 부하 분산 시나리오의 NIC 보조 IP 구성에서 지원 되지 않습니다. 자세한 내용은 [Azure 부하 분산 장치 제한](https://docs.microsoft.com/azure/load-balancer/load-balancer-multivip-overview#limitations)을 참조 하세요. VM에 대 한 추가 IP 주소가 필요한 경우 두 번째 NIC를 배포 합니다.    
   
   > [!Note]
   > 공용 IP 주소가 없는 VM이 내부(공용 IP 주소 없음) 표준 Azure 부하 분산 장치의 백 엔드 풀에 배치되는 경우 퍼블릭 엔드포인트로 라우팅을 허용하기 위해 추가 구성을 수행하지 않는 한 아웃바운드 인터넷 연결이 없습니다. 아웃바운드 연결을 설정하는 방법에 대한 자세한 내용은 [SAP 고가용성 시나리오에서 Azure 표준 Load Balancer를 사용하는 Virtual Machines에 대한 퍼블릭 엔드포인트 연결](./high-availability-guide-standard-load-balancer-outbound-connections.md)을 참조하세요.  


   > [!IMPORTANT]
   > Azure Load Balancer 뒤에 배치되는 Azure VM에서 TCP 타임스탬프를 사용하도록 설정하면 안 됩니다. TCP 타임스탬프를 사용하도록 설정하면 상태 프로브에 오류가 발생합니다. 매개 변수 **net.ipv4.tcp_timestamps**를 **0**으로 설정합니다. 자세한 내용은 [Load Balancer 상태 프로브](../../../load-balancer/load-balancer-custom-probe-overview.md)를 참조하세요.
   > 또한 SAP 참고 [2382421](https://launchpad.support.sap.com/#/notes/2382421)을 참조하세요.  

### <a name="deploy-the-azure-netapp-files-infrastructure"></a>Azure NetApp Files 인프라 배포 

파일 시스템용 ANF 볼륨을 배포 `/hana/shared` 합니다. `/hana/shared`각 HANA 시스템 복제 사이트 마다 별도의 볼륨이 필요 합니다. 자세한 내용은 [Azure NetApp Files 인프라 설정](./sap-hana-scale-out-standby-netapp-files-rhel.md#set-up-the-azure-netapp-files-infrastructure)을 참조 하세요.

이 예제에서는 다음 Azure NetApp Files 볼륨을 사용 했습니다. 

* volume **h n 1**(nfs://10.23.1.7/**h n 1**-s1)
* volume **h n 1**-s2 (nfs://10.23.1.7/**h n 1**-s2)

## <a name="operating-system-configuration-and-preparation"></a>운영 체제 구성 및 준비

다음 섹션의 지침에는 다음 약어 중 하나가 접두사로 추가 됩니다.
* **[A]**: 모든 노드에 적용 가능
* **[AH]**: 모든 HANA DB 노드에 적용 가능
* **[M]**: 과반수 작성자 노드에 적용 가능
* **[AH1]**: 사이트 1의 모든 HANA DB 노드에 적용 가능
* **[AH2]**: 사이트 2의 모든 HANA DB 노드에 적용 가능
* **[1]**: HANA DB 노드 1, 사이트 1에만 적용 됩니다.
* **[2]**: HANA DB 노드 1, 사이트 2에만 적용 가능


다음 단계를 수행 하 여 OS를 구성 하 고 준비 합니다.

1. **[A]** 가상 컴퓨터에서 호스트 파일을 유지 관리 합니다. 모든 서브넷에 대 한 항목을 포함 합니다. 이 예제의에 추가 된 항목은 다음과 같습니다 `/etc/hosts` .  

    ```
     # Client subnet
     10.23.0.11 hana-s1-db1
     10.23.0.12 hana-s1-db1
     10.23.0.13 hana-s1-db2
     10.23.0.14 hana-s2-db1
     10.23.0.15 hana-s2-db2
     10.23.0.16 hana-s2-db3
     10.23.0.17 hana-s-mm
     # Internode subnet
     10.23.1.138 hana-s1-db1-inter
     10.23.1.139 hana-s1-db2-inter
     10.23.1.140 hana-s1-db3-inter
     10.23.1.141 hana-s2-db1-inter
     10.23.1.142 hana-s2-db2-inter
     10.23.1.143 hana-s2-db3-inter
     # HSR subnet
     10.23.1.202 hana-s1-db1-hsr
     10.23.1.203 hana-s1-db2-hsr
     10.23.1.204 hana-s1-db3-hsr
     10.23.1.205 hana-s2-db1-hsr
     10.23.1.206 hana-s2-db2-hsr
     10.23.1.207 hana-s2-db3-hsr
    ```


2. **[A]** NFS 클라이언트 패키지를 설치 합니다.  

    ```yum install nfs-utils ```


3. **[AH]** HANA 구성의 경우 red Hat.  

    <https://access.redhat.com/solutions/2447641>다음 SAP 참고 사항에서 및에 설명 된 대로 RHEL를 구성 합니다.  
   - [2292690 - SAP HANA DB: RHEL 7에 대한 권장 OS 설정](https://launchpad.support.sap.com/#/notes/2292690)
   - [2777782-SAP HANA DB: RHEL 8에 권장 되는 OS 설정](https://launchpad.support.sap.com/#/notes/2777782)
   - [2455582-Linux: GCC 6.x로 컴파일된 SAP 응용 프로그램 실행](https://launchpad.support.sap.com/#/notes/2455582)
   - [2593824-Linux: GCC 4.x로 컴파일된 SAP 응용 프로그램 실행](https://launchpad.support.sap.com/#/notes/2593824) 
   - [2886607-Linux: GCC 4.x로 컴파일된 SAP 응용 프로그램 실행](https://launchpad.support.sap.com/#/notes/2886607)

## <a name="prepare-the-file-systems"></a>파일 시스템 준비
### <a name="mount-the-shared-file-systems"></a>공유 파일 시스템 탑재

이 예제에서는 공유 HANA 파일 시스템이 Azure NetApp Files에 배포 되 고 NFSv4를 통해 탑재 됩니다.  

1. **[AH]** HANA 데이터베이스 볼륨의 탑재 위치를 만듭니다.  

    ```
    mkdir -p /hana/shared
    ```

2. **[AH]** NFS 도메인 설정을 확인 합니다. 도메인이 기본 Azure NetApp Files 도메인으로 구성 되었는지 확인 하 고, 즉, **`defaultv4iddomain.com`** 매핑이 없음으로 설정 되었는지 확인 합니다. **nobody**  
   이 단계는 Azure NetAppFiles NFSv 4.1을 사용 하는 경우에만 필요 합니다.  

    > [!IMPORTANT]
    > VM의 `/etc/idmapd.conf`에서 NFS 도메인을 Azure NetApp Files의 기본 도메인 구성( **`defaultv4iddomain.com`** )과 일치하도록 설정해야 합니다. NFS 클라이언트(예: VM)의 도메인 구성과 NFS 서버(예: Azure NetApp 구성)가 일치하지 않는 경우 VM에 탑재된 Azure NetApp 볼륨의 파일에 대한 사용 권한이 `nobody`로 표시됩니다.  

    ```
    sudo cat /etc/idmapd.conf
    # Example
    [General]
    Domain = defaultv4iddomain.com
    [Mapping]
    Nobody-User = nobody
    Nobody-Group = nobody
    ```

3. **[AH]** 확인 `nfs4_disable_idmapping` . **Y**로 설정되어야 합니다. `nfs4_disable_idmapping`이 있는 디렉터리 구조를 만들려면 mount 명령을 실행합니다. 커널/드라이버용으로 액세스가 예약되어 있기 때문에 /sys/modules 아래에 디렉터리를 수동으로 만들 수 없습니다.  
   이 단계는 Azure NetAppFiles NFSv 4.1을 사용 하는 경우에만 필요 합니다.  

    ```
    # Check nfs4_disable_idmapping 
    cat /sys/module/nfs/parameters/nfs4_disable_idmapping
    # If you need to set nfs4_disable_idmapping to Y
    mkdir /mnt/tmp
    mount 10.9.0.4:/HN1-shared /mnt/tmp
    umount  /mnt/tmp
    echo "Y" > /sys/module/nfs/parameters/nfs4_disable_idmapping
    # Make the configuration permanent
    echo "options nfs nfs4_disable_idmapping=Y" >> /etc/modprobe.d/nfs.conf
    ```

   매개 변수를 변경 하는 방법에 대 한 자세한 내용은 `nfs4_disable_idmapping` 을 참조 하십시오 https://access.redhat.com/solutions/1749883 .

4. **[AH1]** SITE1 HANA DB Vm에 공유 Azure NetApp Files 볼륨을 탑재 합니다.  

    ```
    sudo mount -o rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys 10.23.1.7:/HN1-shared-s1 /hana/shared
    ```

5. **[AH2]** SITE2 HANA DB Vm에 공유 Azure NetApp Files 볼륨을 탑재 합니다.  

    ```
    sudo mount -o rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys 10.23.1.7:/HN1-shared-s2 /hana/shared
    ```


10. **[AH]** 해당 `/hana/shared/` 파일 시스템이 NFS 프로토콜 버전 **NFSv4**를 사용 하는 모든 HANA DB vm에 탑재 되어 있는지 확인 합니다.  

    ```
    sudo nfsstat -m
    # Verify that flag vers is set to 4.1 
    # Example from SITE 1, hana-s1-db1
    /hana/shared from 10.23.1.7:/HN1-shared-s1
     Flags: rw,noatime,vers=4.1,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.23.0.11,local_lock=none,addr=10.23.1.7
    # Example from SITE 2, hana-s2-db1
    /hana/shared from 10.23.1.7:/HN1-shared-s2
     Flags: rw,noatime,vers=4.1,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.23.0.14,local_lock=none,addr=10.23.1.7
    ```

### <a name="prepare-the-data-and-log-local-file-systems"></a>데이터 및 로그 로컬 파일 시스템 준비
제공 된 구성에서 파일 시스템 `/hana/data` 및는 `/hana/log` 관리 디스크에 배포 되 고 각 HANA DB VM에 로컬로 연결 됩니다. 각 HANA DB 가상 머신에서 로컬 데이터 및 로그 볼륨을 만드는 단계를 실행 해야 합니다. 

**LVM (논리 볼륨 관리자)** 을 사용 하 여 디스크 레이아웃을 설정 합니다. 다음 예에서는 각 HANA 가상 컴퓨터에 두 개의 볼륨을 만드는 데 사용 되는 세 개의 데이터 디스크가 연결 되어 있다고 가정 합니다.

1. **[AH]** 사용 가능한 모든 디스크 나열:
    ```
    ls /dev/disk/azure/scsi1/lun*
    ```

   예제 출력:

    ```
    /dev/disk/azure/scsi1/lun0  /dev/disk/azure/scsi1/lun1  /dev/disk/azure/scsi1/lun2 
    ```

2. **[AH]** 사용 하려는 모든 디스크에 대 한 실제 볼륨을 만듭니다.
    ```
    sudo pvcreate /dev/disk/azure/scsi1/lun0
    sudo pvcreate /dev/disk/azure/scsi1/lun1
    sudo pvcreate /dev/disk/azure/scsi1/lun2
    ```

3. **[AH]** 데이터 파일에 대 한 볼륨 그룹을 만듭니다. 로그 파일에 대해 한 볼륨 그룹, SAP HANA의 공유 디렉터리에 대해 한 볼륨을 사용합니다.
    ```
    sudo vgcreate vg_hana_data_HN1 /dev/disk/azure/scsi1/lun0 /dev/disk/azure/scsi1/lun1
    sudo vgcreate vg_hana_log_HN1 /dev/disk/azure/scsi1/lun2
    ```

4. **[AH]** 논리 볼륨을 만듭니다. 
   `-i` 스위치 없이 `lvcreate`를 사용하는 경우 선형 볼륨이 만들어집니다. 더 나은 I/O 성능을 위해 스트라이프 볼륨을 만들고 [SAP HANA VM 스토리지 구성](./hana-vm-operations-storage.md)에 설명된 값에 스트라이프 크기를 정렬하는 것이 좋습니다. `-i` 인수는 기본 실제 볼륨의 수여야 하며 `-I` 인수는 스트라이프 크기입니다. 이 문서에서는 2개의 물리적 볼륨이 데이터 볼륨에 사용되므로 `-i` 스위치 인수가 **2**로 설정됩니다. 데이터 볼륨의 스트라이프 크기는 **256 KiB**입니다. 로그 볼륨에는 하나의 실제 볼륨이 사용되므로 `-i` 또는 `-I` 스위치는 로그 볼륨 명령에 명시적으로 사용되지 않습니다.  

   > [!IMPORTANT]
   > 스위치를 사용 `-i` 하 여 각 데이터 또는 로그 볼륨에 둘 이상의 실제 볼륨을 사용 하는 경우 기본 실제 볼륨의 수로 설정 합니다. 스트라이프 볼륨을 만들 때 `-I` 스위치를 사용하여 스트라이프 크기를 지정합니다.  
   > 스트라이프 크기 및 디스크 수를 비롯한 권장되는 스토리지 구성은 [SAP HANA VM 스토리지 구성](./hana-vm-operations-storage.md)을 참조하세요.  

    ```
    sudo lvcreate -i 2 -I 256 -l 100%FREE -n hana_data vg_hana_data_HN1
    sudo lvcreate -l 100%FREE -n hana_log vg_hana_log_HN1
    sudo mkfs.xfs /dev/vg_hana_data_HN1/hana_data
    sudo mkfs.xfs /dev/vg_hana_log_HN1/hana_log
    ```

5. **[AH]** 탑재 디렉터리를 만들고 모든 논리 볼륨의 UUID를 복사 합니다.
    ```
    sudo mkdir -p /hana/data/HN1
    sudo mkdir -p /hana/log/HN1
    # Write down the ID of /dev/vg_hana_data_HN1/hana_data and /dev/vg_hana_log_HN1/hana_log
    sudo blkid
    ```

6. **[AH]** `fstab` 논리 볼륨에 대 한 항목을 만들고 탑재 합니다.
    ```
    sudo vi /etc/fstab
    ```

   `/etc/fstab` 파일에서 다음 줄을 삽입합니다.

    ```
    /dev/disk/by-uuid/UUID of /dev/mapper/vg_hana_data_HN1-hana_data /hana/data/HN1 xfs  defaults,nofail  0  2
    /dev/disk/by-uuid/UUID of /dev/mapper/vg_hana_log_HN1-hana_log /hana/log/HN1 xfs  defaults,nofail  0  2
    ```

   새 볼륨을 탑재합니다.

    ```
    sudo mount -a
    ```

## <a name="installation"></a>설치  

이 예제에서는 Azure Vm에서 HSR를 사용 하 여 스케일 아웃 구성에 SAP HANA를 배포 하기 위한 HANA 2.0 SP4를 사용 했습니다.  

### <a name="prepare-for-hana-installation"></a>HANA 설치 준비

1. **[AH]** HANA 설치 전에 루트 암호를 설정 합니다. 설치가 완료 된 후 루트 암호를 사용 하지 않도록 설정할 수 있습니다. Execute as `root` 명령 `passwd` 입니다.  

2. **[1, 2]** 에 대 한 사용 권한 변경 `/hana/shared` 
    ```
    chmod 775 /hana/shared
    ```

3. **[1]** 암호를 입력 하 라는 메시지가 표시 되지 않고,이 사이트의 Hana DB vm ( **hana-s1-db2** 및 hana- **s1-db3)** 을 통해 SSH를 통해 로그인 할 수 있는지 확인 합니다.  
   이 경우에는 [키 기반 인증 사용](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/6/html/deployment_guide/s2-ssh-configuration-keypairs)에 설명 된 대로 exchange ssh 키를 사용 합니다.  
    ```
    ssh root@hana-s1-db2
    ssh root@hana-s1-db3
    ```

4. **[2]** 암호를 입력 하 라는 메시지가 표시 되지 않고 **SSH를 통해** **이 사이트의 hana DB** vm에 로그인 할 수 있는지 확인 합니다.  
   이 경우에는 [키 기반 인증 사용](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/6/html/deployment_guide/s2-ssh-configuration-keypairs)에 설명 된 대로 exchange ssh 키를 사용 합니다.  
    ```
    ssh root@hana-s2-db2
    ssh root@hana-s2-db3
    ```

5. **[AH]** HANA 2.0 s p 4에 필요한 추가 패키지를 설치 합니다. 자세한 내용은 RHEL 7에 대 한 SAP Note [2593824](https://launchpad.support.sap.com/#/notes/2593824) 을 참조 하세요. 

    ```
    # If using RHEL 7
    yum install libgcc_s1 libstdc++6 compat-sap-c++-7 libatomic1
    # If using RHEL 8
    yum install libatomic libtool-ltdl.x86_64
    ```


6. **[A]** HANA 설치를 방해 하지 않도록 일시적으로 방화벽을 사용 하지 않도록 설정 합니다. HANA 설치가 완료 된 후에 다시 사용 하도록 설정할 수 있습니다. 
    ```
    # Execute as root
    systemctl stop firewalld
    systemctl disable firewalld
    ```

### <a name="hana-installation-on-the-first-node-on-each-site"></a>각 사이트의 첫 번째 노드에 HANA 설치

1. **[1]** [SAP HANA 2.0 설치 및 업데이트 가이드](https://help.sap.com/viewer/2c1988d620e04368aa4103bf26f17727/2.0.04/en-US/7eb0167eb35e4e2885415205b8383584.html)의 지침에 따라 SAP HANA를 설치 합니다. 다음 지침에서는 사이트 1의 첫 번째 노드에 SAP HANA 설치를 보여 줍니다.   

   a. **hdblcm** `root` HANA 설치 소프트웨어 디렉터리에서 hdblcm 프로그램을 시작 합니다. `internal_network`매개 변수를 사용 하 고 내부 HANA 노드 간 통신에 사용 되는 서브넷에 대 한 주소 공간을 전달 합니다.  

    ```
    ./hdblcm --internal_network=10.23.1.128/26
    ```

   b. 프롬프트에서 다음 값을 입력 합니다.

     * **작업 선택**: **1** (설치의 경우)을 입력 합니다.
     * **설치용 추가 구성 요소**: **2, 3** 입력
     * 설치 경로: Enter 키를 누릅니다 (기본값은/hana/shared).
     * **로컬 호스트 이름**: 기본값을 적용 하려면 enter 키를 누릅니다.
     * **시스템에 호스트를 추가**하 시겠습니까?: **n** 을 입력 합니다.
     * **SAP HANA 시스템 ID**: **h n 1** 을 입력 합니다.
     * **인스턴스 번호** [00]: **03** 을 입력 합니다.
     * **로컬 호스트 작업자 그룹** [기본값]: enter 키를 눌러 기본값을 적용 합니다.
     * **시스템 사용량 선택/인덱스 [4] 입력**: **4** (사용자 지정)를 입력 합니다.
     * **데이터 볼륨의 위치** [/hana/data/HN1]: enter 키를 눌러 기본값을 적용 합니다.
     * **로그 볼륨의 위치** [/hana/log/HN1]: enter 키를 눌러 기본값을 적용 합니다.
     * **최대 메모리 할당 제한** [n]: **n** 을 입력 합니다.
     * 호스트 hana-s1-db1 [hana-s1-db1] **에 대 한 인증서 호스트 이름** : 기본값을 적용 하려면 enter 키를 누릅니다.
     * **SAP 호스트 에이전트 사용자 (sapadm) 암호**: 암호를 입력 합니다.
     * **SAP 호스트 에이전트 사용자 확인 (sapadm) 암호**: 암호를 입력 합니다.
     * **Hn1adm (시스템 관리자) 암호**: 암호를 입력 합니다.
     * **시스템 관리자 홈 디렉터리** [/usr/sap/HN1/home]: 기본값을 적용 하려면 enter 키를 누릅니다.
     * **시스템 관리자 로그인 셸** [/bin/sh]: enter 키를 눌러 기본값을 적용 합니다.
     * **시스템 관리자 사용자 ID** [1001]: enter 키를 눌러 기본값을 적용 합니다.
     * **사용자 그룹의 ID (sapsys)** [79]에 대해 enter 키를 눌러 기본값을 적용 합니다.
     * 시스템 **데이터베이스 사용자 (시스템) 암호**: 시스템 암호를 입력 합니다.
     * **시스템 데이터베이스 사용자 (시스템) 암호 확인**: 시스템 암호 입력
     * **컴퓨터를 다시 부팅 한 후 시스템을 다시 시작** 하려면 [n]: **n** 을 입력 합니다. 
     * **계속 하 시겠습니까 (y/n)**: 요약의 유효성을 검사 하 고 모든 항목이 양호 하면 **y** 를 입력 합니다.

2. **[2]** 이전 단계를 반복 하 여 사이트 2의 첫 번째 노드에 SAP HANA를 설치 합니다.   

3. **[1, 2]** 확인 global.ini  

   global.ini를 표시 하 고 내부 SAP HANA 노드 간 통신에 대 한 구성이 준비 되었는지 확인 합니다. **통신** 섹션을 확인 합니다. 서브넷에 대 한 주소 공간이 있어야 하 `inter` 고 `listeninterface` 로 설정 되어야 합니다 `.internal` . **Internal_hostname_resolution** 섹션을 확인 합니다. 서브넷에 속하는 HANA 가상 컴퓨터의 IP 주소가 있어야 합니다 `inter` .  

   ```
     sudo cat /usr/sap/HN1/SYS/global/hdb/custom/config/global.ini
     # Example from SITE1 
     [communication]
     internal_network = 10.23.1.128/26
     listeninterface = .internal
     [internal_hostname_resolution]
     10.23.1.138 = hana-s1-db1
     10.23.1.139 = hana-s1-db2
     10.23.1.140 = hana-s1-db3
   ```

4. **[1, 2]** `global.ini` SAP note [2080991](https://launchpad.support.sap.com/#/notes/0002080991)에 설명 된 대로 공유 되지 않는 환경에서 설치를 준비 합니다.  

   ```
    sudo vi /usr/sap/HN1/SYS/global/hdb/custom/config/global.ini
    [persistence]
    basepath_shared = no
   ```

4. **[1, 2]** SAP HANA을 다시 시작 하 여 변경 내용을 활성화 합니다.  

   ```
    sudo -u hn1adm /usr/sap/hostctrl/exe/sapcontrol -nr 03 -function StopSystem
    sudo -u hn1adm /usr/sap/hostctrl/exe/sapcontrol -nr 03 -function StartSystem
   ```

6. **[1, 2]** 클라이언트 인터페이스가 통신을 위해 서브넷의 IP 주소를 사용 하는지 확인 `client` 합니다.  

    ```
    # Execute as hn1adm
    /usr/sap/HN1/HDB03/exe/hdbsql -u SYSTEM -p "password" -i 03 -d SYSTEMDB 'select * from SYS.M_HOST_INFORMATION'|grep net_publicname
    # Expected result - example from SITE 2
    "hana-s2-db1","net_publicname","10.23.0.14"
   ```

   구성을 확인 하는 방법에 대 한 자세한 내용은 SAP Note [2183363-SAP HANA 내부 네트워크 구성](https://launchpad.support.sap.com/#/notes/2183363)을 참조 하세요.  

7. **[AH]** HANA 설치 오류를 방지 하려면 데이터 및 로그 디렉터리에 대 한 사용 권한을 변경 하십시오.  

   ```
    sudo chmod o+w -R /hana/data /hana/log
   ```

8. **[1]** 보조 HANA 노드를 설치 합니다. 이 단계의 예제 지침은 사이트 1에 대 한 것입니다.  
   a. 로 상주 **hdblcm** 프로그램을 시작 `root` 합니다.    
    ```
     cd /hana/shared/HN1/hdblcm
     ./hdblcm 
    ```

   b. 프롬프트에서 다음 값을 입력 합니다.

     * **작업 선택**: 호스트 추가의 경우 **2** 를 입력 합니다.
     * **쉼표로 구분 된 호스트 이름 입력**: hana-s1-db2, hana-s1-db3
     * **설치용 추가 구성 요소**: **2, 3** 입력
     * **Root 사용자 이름 입력 [root]**: enter 키를 눌러 기본값을 적용 합니다.
     * **' Hana-s1-db2 ' [1]에 대 한 역할 선택**의 경우: 1 (작업자의 경우)
     * **' Hana-s1-db2 ' 호스트에 대 한 호스트 장애 조치 (Failover) 그룹 입력 [기본값]**: enter 키를 눌러 기본값을 적용 합니다.
     * **' Hana-s1-db2 ' 호스트에 대 한 저장소 파티션 번호 입력 [<<assign automatically>>]**: enter 키를 눌러 기본값을 적용 합니다.
     * **' Hana-s1-db2 ' 호스트에 대 한 작업자 그룹 입력 [기본값]**: enter 키를 눌러 기본값을 적용 합니다.
     * **' Hana-s1-db3 ' [1]**: 1 (작업자의 경우)에 대 한 역할을 선택 합니다.
     * **' Hana-s1-db3 ' 호스트에 대 한 호스트 장애 조치 (Failover) 그룹 입력 (기본값)**: enter 키를 눌러 기본값을 적용 합니다.
     * **' Hana-db3 ' 호스트에 대 한 저장소 파티션 번호 입력 [<<assign automatically>>]**: enter 키를 눌러 기본값을 적용 합니다.
     * **' Hana-db3 ' 호스트에 대 한 작업자 그룹 입력 (기본값)**: enter 키를 눌러 기본값을 적용 합니다.
     * **Hn1adm (시스템 관리자) 암호**: 암호를 입력 합니다.
     * **SAP 호스트 에이전트 사용자 (sapadm) 암호 입력**에 대해 암호를 입력 합니다.
     * **SAP 호스트 에이전트 사용자 확인 (sapadm) 암호**: 암호를 입력 합니다.
     * **호스트 hana-s1-d b 2에 대 한 인증서 호스트 이름** [hana-s1-db2]: enter 키를 눌러 기본값을 적용 합니다.
     * 호스트 hana-s1-db3 [hana-s1-db3] **에 대 한 인증서 호스트 이름** : 기본값을 적용 하려면 enter 키를 누릅니다.
     * **계속 하 시겠습니까 (y/n)**: 요약의 유효성을 검사 하 고 모든 항목이 양호 하면 **y** 를 입력 합니다.

9. **[2]** 사이트 2에 보조 SAP HANA 노드를 설치 하는 이전 단계를 반복 합니다.   

## <a name="configure-sap-hana-20-system-replication"></a>SAP HANA 2.0 시스템 복제 구성

1. **[1]** 사이트 1에서 시스템 복제 구성:

   **H n 1**adm으로 데이터베이스를 백업 합니다.

    ```
    hdbsql -d SYSTEMDB -u SYSTEM -p "passwd" -i 03 "BACKUP DATA USING FILE ('initialbackupSYS')"
    hdbsql -d HN1 -u SYSTEM -p "passwd" -i 03 "BACKUP DATA USING FILE ('initialbackupHN1')"
    ```

   시스템 PKI 파일을 보조 사이트에 복사합니다.

    ```
    scp /usr/sap/HN1/SYS/global/security/rsecssfs/data/SSFS_HN1.DAT hana-s2-db1:/usr/sap/HN1/SYS/global/security/rsecssfs/data/
    scp /usr/sap/HN1/SYS/global/security/rsecssfs/key/SSFS_HN1.KEY  hana-s2-db1:/usr/sap/HN1/SYS/global/security/rsecssfs/key/
    ```

   기본 사이트를 만듭니다.

    ```
    hdbnsutil -sr_enable --name=HANA_S1
    ```

2. **[2]** 사이트 2에서 시스템 복제 구성:
    
   두 번째 사이트를 등록 하 여 시스템 복제를 시작 합니다. 다음 명령을 <hanasid\>adm으로 실행합니다.

    ```
    sapcontrol -nr 03 -function StopWait 600 10
    hdbnsutil -sr_register --remoteHost=hana-s1-db1 --remoteInstance=03 --replicationMode=sync --name=HANA_S2
    sapcontrol -nr 03 -function StartSystem
    ```

3. **[1]** 복제 상태 확인

   복제 상태를 확인하고 모든 데이터베이스가 동기화될 때까지 기다립니다.

    ```
    sudo su - hn1adm -c "python /usr/sap/HN1/HDB03/exe/python_support/systemReplicationStatus.py"
    # | Database | Host          | Port  | Service Name | Volume ID | Site ID | Site Name | Secondary     | Secondary | Secondary | Secondary | Secondary     | Replication | Replication | Replication    |
    # |          |               |       |              |           |         |           | Host          | Port      | Site ID   | Site Name | Active Status | Mode        | Status      | Status Details |
    # | -------- | ------------- | ----- | ------------ | --------- | ------- | --------- | ------------- | --------- | --------- | --------- | ------------- | ----------- | ----------- | -------------- |
    # | HN1      | hana-s1-db3   | 30303 | indexserver  |         5 |       1 | HANA_S1   | hana-s2-db3   |     30303 |         2 | HANA_S2   | YES           | SYNC        | ACTIVE      |                |
    # | SYSTEMDB | hana-s1-db1   | 30301 | nameserver   |         1 |       1 | HANA_S1   | hana-s2-db1   |     30301 |         2 | HANA_S2   | YES           | SYNC        | ACTIVE      |                |
    # | HN1      | hana-s1-db1   | 30307 | xsengine     |         2 |       1 | HANA_S1   | hana-s2-db1   |     30307 |         2 | HANA_S2   | YES           | SYNC        | ACTIVE      |                |
    # | HN1      | hana-s1-db1   | 30303 | indexserver  |         3 |       1 | HANA_S1   | hana-s2-db1   |     30303 |         2 | HANA_S2   | YES           | SYNC        | ACTIVE      |                |
    # | HN1      | hana-s1-db2   | 30303 | indexserver  |         4 |       1 | HANA_S1   | hana-s2-db2   |     30303 |         2 | HANA_S2   | YES           | SYNC        | ACTIVE      |                |
    #
    # status system replication site "2": ACTIVE
    # overall system replication status: ACTIVE
    #
    # Local System Replication State
    #   
    # mode: PRIMARY
    # site id: 1
    # site name: HANA_S1
    ```

4. **[1, 2]** hana 시스템 복제 가상 네트워크 인터페이스를 통해 전달 된 경우 hana 시스템 복제에 대 한 통신이 되도록 hana 구성을 변경 합니다.   
   - 두 사이트에서 HANA 중지
    ```
    sudo -u hn1adm /usr/sap/hostctrl/exe/sapcontrol -nr 03 -function StopSystem HDB
    ```

   - global.ini 편집 하 여 HANA 시스템 복제에 대 한 호스트 매핑을 추가 합니다. 서브넷의 IP 주소를 사용 `hsr` 합니다.  
    ```
    sudo vi /usr/sap/HN1/SYS/global/hdb/custom/config/global.ini
    #Add the section
    [system_replication_hostname_resolution]
    10.23.1.202 = hana-s1-db1
    10.23.1.203 = hana-s1-db2
    10.23.1.204 = hana-s1-db3
    10.23.1.205 = hana-s2-db1
    10.23.1.206 = hana-s2-db2
    10.23.1.207 = hana-s2-db3
    ```

   - 두 사이트에서 HANA 시작
   ```
    sudo -u hn1adm /usr/sap/hostctrl/exe/sapcontrol -nr 03 -function StartSystem HDB
   ```

   자세한 내용은 [시스템 복제에 대 한 호스트 이름 확인](https://help.sap.com/viewer/eb3777d5495d46c5b2fa773206bbfb46/1.0.12/en-US/c0cba1cb2ba34ec89f45b48b2157ec7b.html)을 참조 하세요.  

5. **[AH]** 방화벽을 다시 사용 하도록 설정 합니다.  
   - 방화벽 다시 사용
       ```
       # Execute as root
       systemctl start firewalld
       systemctl enable firewalld
       ```

   - 필요한 방화벽 포트를 엽니다. HANA 인스턴스 번호에 대 한 포트를 조정 해야 합니다.  

       > [!IMPORTANT]
       > HANA 노드 통신과 클라이언트 트래픽을 허용 하는 방화벽 규칙을 만듭니다. 필수 포트 목록은 [모든 SAP 제품의 TCP/IP 포트](https://help.sap.com/viewer/ports)에 나열되어 있습니다. 다음 명령은 예제 일 뿐입니다. 이 시나리오에서는 시스템 번호 03이 사용 됩니다.

       ```
        # Execute as root
        sudo firewall-cmd --zone=public --add-port=30301/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30301/tcp
        sudo firewall-cmd --zone=public --add-port=30303/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30303/tcp
        sudo firewall-cmd --zone=public --add-port=30306/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30306/tcp
        sudo firewall-cmd --zone=public --add-port=30307/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30307/tcp
        sudo firewall-cmd --zone=public --add-port=30313/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30313/tcp
        sudo firewall-cmd --zone=public --add-port=30315/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30315/tcp
        sudo firewall-cmd --zone=public --add-port=30317/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30317/tcp
        sudo firewall-cmd --zone=public --add-port=30340/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30340/tcp
        sudo firewall-cmd --zone=public --add-port=30341/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30341/tcp
        sudo firewall-cmd --zone=public --add-port=30342/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30342/tcp
        sudo firewall-cmd --zone=public --add-port=1128/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=1128/tcp
        sudo firewall-cmd --zone=public --add-port=1129/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=1129/tcp
        sudo firewall-cmd --zone=public --add-port=40302/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=40302/tcp
        sudo firewall-cmd --zone=public --add-port=40301/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=40301/tcp
        sudo firewall-cmd --zone=public --add-port=40307/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=40307/tcp
        sudo firewall-cmd --zone=public --add-port=40303/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=40303/tcp
        sudo firewall-cmd --zone=public --add-port=40340/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=40340/tcp
        sudo firewall-cmd --zone=public --add-port=50313/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=50313/tcp
        sudo firewall-cmd --zone=public --add-port=50314/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=50314/tcp
        sudo firewall-cmd --zone=public --add-port=30310/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30310/tcp
        sudo firewall-cmd --zone=public --add-port=30302/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30302/tcp
       ```

## <a name="create-a-pacemaker-cluster"></a>Pacemaker 클러스터 만들기

[Azure의 Red Hat Enterprise Linux에서 Pacemaker 설정](high-availability-guide-rhel-pacemaker.md) 단계에 따라 이 HANA 서버용 기본 Pacemaker 클러스터를 만듭니다.
클러스터의 과반수 maker를 비롯 한 모든 가상 컴퓨터를 포함 합니다.  

> [!IMPORTANT]
> `quorum expected-votes`두 노드 클러스터가 아니므로 2로 설정 하지 마십시오.  
> `concurrent-fencing`노드 펜스가 deserialize 되도록 클러스터 속성을 사용 하도록 설정 했는지 확인 합니다.   

## <a name="create-file-system-resources"></a>파일 시스템 리소스 만들기

1. **[1, 2]** 두 복제 사이트에서 SAP HANA를 중지 합니다. <sid adm으로 실행 \> 합니다.  

    ```
    sapcontrol -nr 03 -function StopSystem
    ```

2. **[AH]** `/hana/shared`모든 HANA DB vm에 설치 하기 위해 일시적으로 탑재 된 파일 시스템의 탑재를 해제 합니다. 파일 시스템을 사용 하는 프로세스 및 세션을 중지 한 후에 탑재를 해제할 수 있습니다. 
 
    ```
    umount /hana/shared 
    ```

3. **[1]** `/hana/shared` 사용 안 함 상태의에 대 한 파일 시스템 클러스터 리소스를 만듭니다. `--disabled`탑재를 사용 하도록 설정 하기 전에 위치 제약 조건을 정의 해야 하기 때문에 옵션을 사용 하 여 리소스를 만듭니다.  

    ```
    # /hana/shared file system for site 1
    pcs resource create fs_hana_shared_s1 --disabled ocf:heartbeat:Filesystem device=10.23.1.7:/HN1-shared-s1  directory=/hana/shared \
    fstype=nfs options='defaults,rw,hard,timeo=600,rsize=262144,wsize=262144,proto=tcp,intr,noatime,sec=sys,vers=4.1,lock,_netdev' op monitor interval=20s on-fail=fence timeout=40s OCF_CHECK_LEVEL=20 \
    op start interval=0 timeout=120 op stop interval=0 timeout=120

    # /hana/shared file system for site 2   
    pcs resource create fs_hana_shared_s2 --disabled ocf:heartbeat:Filesystem device=10.23.1.7:/HN1-shared-s1 directory=/hana/shared \
    fstype=nfs options='defaults,rw,hard,timeo=600,rsize=262144,wsize=262144,proto=tcp,intr,noatime,sec=sys,vers=4.1,lock,_netdev' op monitor interval=20s on-fail=fence timeout=40s OCF_CHECK_LEVEL=20 \
    op start interval=0 timeout=120 op stop interval=0 timeout=120

    # clone the /hana/shared file system resources for both site1 and site2
    pcs resource clone fs_hana_shared_s1 meta clone-node-max=1 interleave=true
    pcs resource clone fs_hana_shared_s2 meta clone-node-max=1 interleave=true
    ```
 
   `OCF_CHECK_LEVEL=20` 모니터 작업에서 파일 시스템에 대 한 읽기/쓰기 테스트를 수행할 수 있도록 특성은 모니터 작업에 추가 됩니다. 이 특성이 없으면 모니터 작업은 파일 시스템이 탑재 되어 있는지만 확인 합니다. 이는 연결이 끊어질 때 파일 시스템에 액세스할 수 없는 경우에도 탑재 된 상태를 유지할 수 있기 때문에 문제가 될 수 있습니다.  

   `on-fail=fence` 특성은 모니터 작업에도 추가 됩니다. 이 옵션을 사용 하면 노드에서 모니터 작업이 실패 하는 경우 해당 노드가 즉시 친 됩니다. 이 옵션을 사용 하지 않으면 실패 한 리소스에 종속 된 모든 리소스를 중지 하 고 실패 한 리소스를 다시 시작한 다음 실패 한 리소스에 종속 된 모든 리소스를 시작 하는 것이 기본 동작입니다. SAPHana 리소스가 실패 한 리소스에 종속 되는 경우에는이 동작에 오랜 시간이 걸릴 수 있지만 완전히 실패할 수도 있습니다. HANA 이진 파일을 보유 하는 NFS 공유에 액세스할 수 없는 경우 SAPHana 리소스를 성공적으로 중지할 수 없습니다.  

4. **[1]** 노드 특성을 구성 하 고 확인 합니다. 복제 사이트 1의 모든 SAP HANA DB 노드에는 특성이 할당 되 `S1` 고 복제 사이트 2의 모든 SAP HANA db 노드에는 특성이 할당 됩니다 `S2` .  

    ```
    # HANA replication site 1
    pcs node attribute hana-s1-db1 NFS_SID_SITE=S1
    pcs node attribute hana-s1-db2 NFS_SID_SITE=S1
    pcs node attribute hana-s1-db3 NFS_SID_SITE=S1
    # HANA replication site 2
    pcs node attribute hana-s2-db1 NFS_SID_SITE=S2
    pcs node attribute hana-s2-db2 NFS_SID_SITE=S2
    pcs node attribute hana-s2-db3 NFS_SID_SITE=S2
    #To verify the attribute assignment to nodes execute
    pcs node attribute
    ```

5. **[1]** NFS 파일 시스템이 탑재 되는 위치를 결정 하는 제약 조건을 구성 하 고 파일 시스템 리소스를 사용 하도록 설정 합니다.  
    ```
    # Configure the constraints
    pcs constraint location fs_hana_shared_s1-clone rule resource-discovery=never score=-INFINITY NFS_SID_SITE ne S1
    pcs constraint location fs_hana_shared_s2-clone rule resource-discovery=never score=-INFINITY NFS_SID_SITE ne S2
    # Enable the file system resources
    pcs resource enable fs_hana_shared_s1
    pcs resource enable fs_hana_shared_s2
    ```

   파일 시스템 리소스를 사용 하도록 설정 하면 클러스터에서 파일 시스템을 탑재 합니다 `/hana/shared` .
 
6. **[AH]** ANF 볼륨이 `/hana/shared` 두 사이트의 모든 HANA DB vm에 탑재 되어 있는지 확인 합니다.

    ```
    sudo nfsstat -m
    # Verify that flag vers is set to 4.1 
    # Example from SITE 1, hana-s1-db1
    /hana/shared from 10.23.1.7:/HN1-shared-s1
     Flags: rw,noatime,vers=4.1,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.23.0.11,local_lock=none,addr=10.23.1.7
    # Example from SITE 2, hana-s2-db1
    /hana/shared from 10.23.1.7:/HN1-shared-s2
     Flags: rw,noatime,vers=4.1,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.23.0.14,local_lock=none,addr=10.23.1.7
    ```

7. **[1]** 특성 리소스를 구성 합니다. `true`에 대 한 NFS 탑재를 탑재 하는 경우 특성을로 설정할 제약 조건을 구성 합니다 `hana/shared` .  

    ```
    # Configure the attribure resources
    pcs resource create hana_nfs_s1_active ocf:pacemaker:attribute active_value=true inactive_value=false name=hana_nfs_s1_active
    pcs resource create hana_nfs_s2_active ocf:pacemaker:attribute active_value=true inactive_value=false name=hana_nfs_s2_active
    # Clone the attribure resources
    pcs resource clone hana_nfs_s1_active meta clone-node-max=1 interleave=true
    pcs resource clone hana_nfs_s2_active meta clone-node-max=1 interleave=true
    # Configure the constraints, which will set the attribute values
    pcs constraint order fs_hana_shared_s1-clone then hana_nfs_s1_active-clone
    pcs constraint order fs_hana_shared_s2-clone then hana_nfs_s2_active-clone
    ```

   > [!TIP]
   > 구성에 NFS가 탑재 된 다른 파일 시스템이 포함 된 경우에는 `hana/shared` `sequential=false` 파일 시스템 간에 정렬 종속성이 없도록 옵션을 포함 합니다. 모든 NFS 탑재 파일 시스템은 해당 특성 리소스 보다 먼저 시작 해야 하지만 서로를 기준으로 하 여 시작할 필요는 없습니다. 자세한 내용은 [HANA 파일 시스템이 NFS 공유 인 경우 pacemaker 클러스터에서 SAP HANA Scale-Out HSR 어떻게 할까요? 구성을](https://access.redhat.com/solutions/5423971)참조 하세요.  

8. **[1]** pacemaker를 유지 관리 모드로 전환 하 여 HANA 클러스터 리소스 만들기를 준비 합니다.  
    ```
    pcs property set maintenance-mode=true
    ```

## <a name="create-sap-hana-cluster-resources"></a>SAP HANA 클러스터 리소스 만들기

1. **[A]** 과반수 maker를 포함 하 여 모든 클러스터 노드에 HANA 확장 리소스 에이전트를 설치 합니다.    

    ```
    yum install -y resource-agents-sap-hana-scaleout 
    ```


   > [!NOTE]
   > OS 릴리스에 대해 지원 되는 최소 버전의 패키지에 대 한 [클러스터의 SAP HANA 관리-RHEL HA 클러스터에 대 한 지원 정책](https://access.redhat.com/articles/3397471) 을 참조 하세요 `resource-agents-sap-hana-scaleout` .  

2. **[1, 2]** HANA "시스템 복제 후크"를 설치 합니다. 후크는 각 시스템 복제 사이트의 하나의 HANA DB 노드에 설치 해야 합니다. SAP HANA 여전히 작동 중단 되어야 합니다.        

   1. 후크 준비 `root` 
    ```
     mkdir -p /hana/shared/myHooks
     cp /usr/share/SAPHanaSR-ScaleOut/SAPHanaSR.py /hana/shared/myHooks
     chown -R hn1adm:sapsys /hana/shared/myHooks
    ```

   2. 조정해 `global.ini`
    ```
    # add to global.ini
    [ha_dr_provider_SAPHanaSR]
    provider = SAPHanaSR
    path = /hana/shared/myHooks
    execution_order = 1
    
    [trace]
    ha_dr_saphanasr = info
    ```

3. **[AH]** 클러스터 <sid adm의 클러스터 노드에 sudoers 구성이 필요 합니다 \> . 이 예에서는 새 파일을 만들어 수행 합니다. 로 명령을 실행 `root` 합니다.    
    ``` 
    cat << EOF > /etc/sudoers.d/20-saphana
    # SAPHanaSR-ScaleOut needs for srHook
     Cmnd_Alias SOK = /usr/sbin/crm_attribute -n hana_hn1_glob_srHook -v SOK -t crm_config -s SAPHanaSR
     Cmnd_Alias SFAIL = /usr/sbin/crm_attribute -n hana_hn1_glob_srHook -v SFAIL -t crm_config -s SAPHanaSR
     hn1adm ALL=(ALL) NOPASSWD: SOK, SFAIL
     EOF
    ```

4. **[1, 2]** 두 복제 사이트에서 SAP HANA를 시작 합니다. <sid adm으로 실행 \> 합니다.  

    ```
    sapcontrol -nr 03 -function StartSystem 
    ```

5. **[1]** 후크 설치를 확인 합니다. \>활성 HANA 시스템 복제 사이트에서 <sid adm으로 실행 합니다.   

    ```
    cdtrace
     awk '/ha_dr_SAPHanaSR.*crm_attribute/ \
     { printf "%s %s %s %s\n",$2,$3,$5,$16 }' nameserver_*

     # Example entries
     # 2020-07-21 22:04:32.364379 ha_dr_SAPHanaSR SFAIL
     # 2020-07-21 22:04:46.905661 ha_dr_SAPHanaSR SFAIL
     # 2020-07-21 22:04:52.092016 ha_dr_SAPHanaSR SFAIL
     # 2020-07-21 22:04:52.782774 ha_dr_SAPHanaSR SFAIL
     # 2020-07-21 22:04:53.117492 ha_dr_SAPHanaSR SFAIL
     # 2020-07-21 22:06:35.599324 ha_dr_SAPHanaSR SOK
    ```

6. **[1]** HANA 클러스터 리소스를 만듭니다. 로 다음 명령을 실행 합니다 `root` .    
   1. 클러스터가 이미 유지 관리 모드 인지 확인 합니다.  
    
   2. 다음으로 HANA 토폴로지 리소스를 만듭니다.  
      RHEL **7.x** 클러스터를 빌드하는 경우 다음 명령을 사용 합니다.  
      ```
      pcs resource create SAPHanaTopology_HN1_HDB03 SAPHanaTopologyScaleOut \
       SID=HN1 InstanceNumber=03 \
       op start timeout=600 op stop timeout=300 op monitor interval=10 timeout=600

      pcs resource clone SAPHanaTopology_HN1_HDB03 meta clone-node-max=1 interleave=true
      ```

      RHEL **8gb** 클러스터를 빌드하는 경우 다음 명령을 사용 합니다.  
      ```
      pcs resource create SAPHanaTopology_HN1_HDB03 SAPHanaTopology \
       SID=HN1 InstanceNumber=03 meta clone-node-max=1 interleave=true \
       op methods interval=0s timeout=5 \
       op start timeout=600 op stop timeout=300 op monitor interval=10 timeout=600

      pcs resource clone SAPHanaTopology_HN1_HDB03 meta clone-node-max=1 interleave=true
      ```

   3. 다음으로 HANA 인스턴스 리소스를 만듭니다.  
      > [!NOTE]
      > 이 문서에는 Microsoft에서 더 이상 사용 하지 않는 용어 *종속*용어에 대 한 참조가 포함 되어 있습니다. 소프트웨어에서 용어를 제거 하는 경우이 문서에서 제거 합니다.  
 
      RHEL **7.x** 클러스터를 빌드하는 경우 다음 명령을 사용 합니다.    
      ```
      pcs resource create SAPHana_HN1_HDB03 SAPHanaController \
       SID=HN1 InstanceNumber=03 PREFER_SITE_TAKEOVER=true DUPLICATE_PRIMARY_TIMEOUT=7200 AUTOMATED_REGISTER=false \
       op start interval=0 timeout=3600 op stop interval=0 timeout=3600 op promote interval=0 timeout=3600 \
       op monitor interval=60 role="Master" timeout=700 op monitor interval=61 role="Slave" timeout=700
     
      pcs resource master msl_SAPHana_HN1_HDB03 SAPHana_HN1_HDB03 \
       meta master-max="1" clone-node-max=1 interleave=true
      ```

      RHEL **8gb** 클러스터를 빌드하는 경우 다음 명령을 사용 합니다.  
      ```
      pcs resource create SAPHana_HN1_HDB03 SAPHanaController \
       SID=HN1 InstanceNumber=03 PREFER_SITE_TAKEOVER=true DUPLICATE_PRIMARY_TIMEOUT=7200 AUTOMATED_REGISTER=false \
       op demote interval=0s timeout=320 op methods interval=0s timeout=5 \
       op start interval=0 timeout=3600 op stop interval=0 timeout=3600 op promote interval=0 timeout=3600 \
       op monitor interval=60 role="Master" timeout=700 op monitor interval=61 role="Slave" timeout=700
     
      pcs resource promotable SAPHana_HN1_HDB03 \
       meta master-max="1" clone-node-max=1 interleave=true
      ```
      > [!IMPORTANT]
      > 철저 한 장애 조치 (failover) 테스트를 수행 하는 동안 AUTOMATED_REGISTER를 **아니요**로 설정 하 여 실패 한 주 인스턴스가 보조로 자동으로 등록 되는 것을 방지 하는 것이 좋습니다. 장애 조치 (failover) 테스트가 성공적으로 완료 되 면 AUTOMATED_REGISTER를 **예**로 설정 하 여 인수 시스템 복제를 자동으로 다시 시작할 수 있도록 합니다. 

   4. 가상 IP 및 연결 된 리소스를 만듭니다.  
      ```
      pcs resource create vip_HN1_03 ocf:heartbeat:IPaddr2 ip=10.23.0.18 op monitor interval="10s" timeout="20s"
      sudo pcs resource create nc_HN1_03 azure-lb port=62503
      sudo pcs resource group add g_ip_HN1_03 nc_HN1_03 vip_HN1_03
      ```

   5. 클러스터 제약 조건 만들기  
      RHEL **7.x** 클러스터를 빌드하는 경우 다음 명령을 사용 합니다.  
      ```
      #Start HANA topology, before the HANA instance
      pcs constraint order SAPHanaTopology_HN1_HDB03-clone then msl_SAPHana_HN1_HDB03

      pcs constraint colocation add g_ip_HN1_03 with master msl_SAPHana_HN1_HDB03 4000
      #HANA resources are only allowed to run on a node, if the node's NFS file systems are mounted. The constraint also avoids the majority maker node
      pcs constraint location SAPHanaTopology_HN1_HDB03-clone rule resource-discovery=never score=-INFINITY hana_nfs_s1_active ne true and hana_nfs_s2_active ne true
      ```
 
      RHEL **8gb** 클러스터를 빌드하는 경우 다음 명령을 사용 합니다.  
      ```
      #Start HANA topology, before the HANA instance
      pcs constraint order SAPHanaTopology_HN1_HDB03-clone then SAPHana_HN1_HDB03-clone

      pcs constraint colocation add g_ip_HN1_03 with master SAPHana_HN1_HDB03-clone 4000
      #HANA resources are only allowed to run on a node, if the node's NFS file systems are mounted. The constraint also avoids the majority maker node
      pcs constraint location SAPHanaTopology_HN1_HDB03-clone rule resource-discovery=never score=-INFINITY hana_nfs_s1_active ne true and hana_nfs_s2_active ne true
      ```

7. **[1]** 클러스터를 유지 관리 모드에서 제외 합니다. 클러스터 상태가 정상이며 모든 리소스가 시작되었는지 확인합니다.  
    ```
    sudo pcs property set maintenance-mode=false
    #If there are failed cluster resources, you may need to run the next command
    pcs resource cleanup
    ```
  
   > [!NOTE]
   > 위의 구성에서 시간 제한은 단지 예제이며 특정 HANA 설정에 맞게 조정해야 할 수 있습니다. 예를 들어 SAP HANA 데이터베이스를 시작하는 데 시간이 더 오래 걸리는 경우 시작 시간 제한을 늘려야 할 수 있습니다.
  
## <a name="test-sap-hana-failover"></a>테스트 SAP HANA 장애 조치 

1. 테스트를 시작 하기 전에 클러스터를 확인 하 고 시스템 복제 상태를 SAP HANA 합니다.  

   a. 실패 한 클러스터 작업이 없는지 확인 합니다.  
     ```
     #Verify that there are no failed cluster actions
     pcs status
     # Example
     #Stack: corosync
     #Current DC: hana-s-mm (version 1.1.19-8.el7_6.5-c3c624ea3d) - partition with quorum
     #Last updated: Thu Sep 24 06:00:20 2020
     #Last change: Thu Sep 24 05:59:17 2020 by root via crm_attribute on hana-s1-db1
     #
     #7 nodes configured
     #45 resources configured
     #
     #Online: [ hana-s-mm hana-s1-db1 hana-s1-db2 hana-s1-db3 hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
     #
     #Active resources:
     #
     #rsc_st_azure    (stonith:fence_azure_arm):      Started hana-s-mm
     #Clone Set: fs_hana_shared_s1-clone [fs_hana_shared_s1]
     #    Started: [ hana--s1-db1 hana-s1-db2 hana-s1-db3 ]
     #Clone Set: fs_hana_shared_s2-clone [fs_hana_shared_s2]
     #    Started: [ hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
     #Clone Set: hana_nfs_s1_active-clone [hana_nfs_s1_active]
     #    Started: [ hana-s1-db1 hana-s1-db2 hana-s1-db3 ]
     #Clone Set: hana_nfs_s2_active-clone [hana_nfs_s2_active]
     #    Started: [ hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
     #Clone Set: SAPHanaTopology_HN1_HDB03-clone [SAPHanaTopology_HN1_HDB03]
     #    Started: [ hana-s1-db1 hana-s1-db2 hana-s1-db3 hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
     #Master/Slave Set: msl_SAPHana_HN1_HDB03 [SAPHana_HN1_HDB03]
     #    Masters: [ hana-s1-db1 ]
     #    Slaves: [ hana-s1-db2 hana-s1-db3 hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
     #Resource Group: g_ip_HN1_03
     #    nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hana-s1-db1
     #    vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hana-s1-db1
     ```

   b. SAP HANA 시스템 복제가 동기화 되었는지 확인

      ```
      # Verify HANA HSR is in sync
      sudo su - hn1adm -c "python /usr/sap/HN1/HDB03/exe/python_support/systemReplicationStatus.py"
      #| Database | Host        | Port  | Service Name | Volume ID | Site ID | Site Name | Secondary     | Secondary| Secondary | Secondary | Secondary     | Replication | Replication | Replication    |
      #|          |             |       |              |           |         |           | Host          | Port     | Site ID   | Site Name | Active Status | Mode        | Status      | Status Details |
      #| -------- | ----------- | ----- | ------------ | --------- | ------- | --------- | ------------- | -------- | --------- | --------- | ------------- | ----------- | ----------- | -------------- |
      #| HN1      | hana-s1-db3 | 30303 | indexserver  |         5 |       2 | HANA_S1   | hana-s2-db3 |     30303  |         1 | HANA_S2   | YES           | SYNC        | ACTIVE      |                |
      #| HN1      | hana-s1-db2 | 30303 | indexserver  |         4 |       2 | HANA_S1   | hana-s2-db2 |     30303  |         1 | HANA_S2   | YES           | SYNC        | ACTIVE      |                |  
      #| SYSTEMDB | hana-s1-db1 | 30301 | nameserver   |         1 |       2 | HANA_S1   | hana-s2-db1 |     30301  |         1 | HANA_S2   | YES           | SYNC        | ACTIVE      |                |
      #| HN1      | hana-s1-db1 | 30307 | xsengine     |         2 |       2 | HANA_S1   | hana-s2-db1 |     30307  |         1 | HANA_S2   | YES           | SYNC        | ACTIVE      |                |
      #| HN1      | hana-s1-db1 | 30303 | indexserver  |         3 |       2 | HANA_S1   | hana-s2-db1 |     30303  |         1 | HANA_S2   | YES           | SYNC        | ACTIVE      |                |

      #status system replication site "1": ACTIVE
      #overall system replication status: ACTIVE

      #Local System Replication State
      #~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

      #mode: PRIMARY
      #site id: 1
      #site name: HANA_S1
      ```

2. 노드가 NFS 공유에 대 한 액세스를 잃으면 () 장애 시나리오에 대 한 클러스터 구성을 확인 `/hana/shared` 합니다.  

   SAP HANA 리소스 에이전트는 `/hana/shared` 장애 조치 (failover) 중에 작업을 수행 하기 위해에 저장 된 이진 파일에 종속 됩니다. 파일 시스템 `/hana/shared` 은 제공 된 구성에서 NFS를 통해 탑재 됩니다. 실행할 수 있는 테스트는 `/hana/shared` 파일 시스템을 *읽기 전용*으로 다시 탑재 하는 것입니다. 이 접근 방식은 `/hana/shared` 활성 시스템 복제 사이트에 대 한 액세스가 손실 된 경우 클러스터가 장애 조치 (failover) 되는 것을 확인 합니다.  

   **예상 결과**: 읽기 전용으로 다시 탑재 하는 경우 파일 시스템에 `/hana/shared` 대 한 읽기/쓰기 작업을 수행 하는 모니터링 작업이 실패 하 고 HANA 리소스 장애 조치 (failover)가 트리거됩니다. *Read only* HANA 노드가 NFS 공유에 액세스할 수 없는 경우에도 동일한 결과가 예상 됩니다.  
     
   또는를 실행 하 여 클러스터 리소스의 상태를 확인할 수 있습니다 `crm_mon` `pcs status` . 테스트 시작 전 리소스 상태:
      ```
      # Output of crm_mon
      #7 nodes configured
      #45 resources configured

      #Online: [ hana-s-mm hana-s1-db1 hana-s1-db2 hana-s1-db3 hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
      #
      #Active resources:

      #rsc_st_azure    (stonith:fence_azure_arm):      Started hana-s-mm
      # Clone Set: fs_hana_shared_s1-clone [fs_hana_shared_s1]
      #    Started: [ hana-s1-db1 hana-s1-db2 hana-s1-db3 ]
      # Clone Set: fs_hana_shared_s2-clone [fs_hana_shared_s2]
      #     Started: [ hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
      # Clone Set: hana_nfs_s1_active-clone [hana_nfs_s1_active]
      #     Started: [ hana-s1-db1 hana-s1-db2 hana-s1-db3 ]
      # Clone Set: hana_nfs_s2_active-clone [hana_nfs_s2_active]
      #     Started: [ hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
      # Clone Set: SAPHanaTopology_HN1_HDB03-clone [SAPHanaTopology_HN1_HDB03]
      #     Started: [ hana-s1-db1 hana-s1-db2 hana-s1-db3 hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
      # Master/Slave Set: msl_SAPHana_HN1_HDB03 [SAPHana_HN1_HDB03]
      #     Masters: [ hana-s1-db1 ]
      #     Slaves: [ hana-s1-db2 hana-s1-db3 hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
      # Resource Group: g_ip_HN1_03
      #     nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hana-s1-db1
      #     vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hana-s1-db1
      ```

   `/hana/shared`주 복제 사이트 vm 중 하나에서 실패를 시뮬레이트하려면 다음 명령을 실행 합니다.
      ```
      # Execute as root 
      mount -o ro /hana/shared
      # Or if the above command returns an error
      sudo mount -o ro 10.23.1.7/HN1-shared-s1 /hana/shared
      ```
  
   클러스터 구성에 따라에 대 한 액세스를 잃은 HANA VM을 `/hana/shared` 다시 시작 하거나 중지 해야 합니다. 클러스터 리소스는 다른 HANA 시스템 복제 사이트로 마이그레이션됩니다.  
         
   클러스터가 VM에서 시작 되지 않은 경우 다시 시작 되 면 다음을 실행 하 여 클러스터를 시작 합니다. 

      ```
      # Start the cluster 
      pcs cluster start
      ```
   
   클러스터가 시작 되 면 파일 시스템이 `/hana/shared` 자동으로 탑재 됩니다.     
   AUTOMATED_REGISTER = "false"를 설정 하는 경우 보조 사이트에서 SAP HANA 시스템 복제를 구성 해야 합니다. 이 경우 다음 명령을 실행 하 여 SAP HANA를 보조로 다시 구성할 수 있습니다.   

      ```
      # Execute on the secondary 
      su - hn1adm
      # Make sure HANA is not running on the secondary site. If it is started, stop HANA
      sapcontrol -nr 03 -function StopWait 600 10
      # Register the HANA secondary site
      hdbnsutil -sr_register --name=HANA_S1 --remoteHost=hana-s2-db1 --remoteInstance=03 --replicationMode=sync
      # Switch back to root and cleanup failed resources
      pcs resource cleanup SAPHana_HN1_HDB03
      ```

   테스트 후의 리소스 상태입니다. 

      ```
      # Output of crm_mon
      #7 nodes configured
      #45 resources configured
    
      #Online: [ hana-s-mm hana-s1-db1 hana-s1-db2 hana-s1-db3 hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
      
      #Active resources:
    
      #rsc_st_azure    (stonith:fence_azure_arm):      Started hana-s-mm
      # Clone Set: fs_hana_shared_s1-clone [fs_hana_shared_s1]
      #    Started: [ hana-s1-db1 hana-s1-db2 hana-s1-db3 ]
      # Clone Set: fs_hana_shared_s2-clone [fs_hana_shared_s2]
      #     Started: [ hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
      # Clone Set: hana_nfs_s1_active-clone [hana_nfs_s1_active]
      #     Started: [ hana-s1-db1 hana-s1-db2 hana-s1-db3 ]
      # Clone Set: hana_nfs_s2_active-clone [hana_nfs_s2_active]
      #     Started: [ hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
      # Clone Set: SAPHanaTopology_HN1_HDB03-clone [SAPHanaTopology_HN1_HDB03]
      #     Started: [ hana-s1-db1 hana-s1-db2 hana-s1-db3 hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
      # Master/Slave Set: msl_SAPHana_HN1_HDB03 [SAPHana_HN1_HDB03]
      #     Masters: [ hana-s2-db1 ]
      #     Slaves: [ hana-s1-db1 hana-s1-db2 hana-s1-db3 hana-s2-db2 hana-s2-db3 ]
      # Resource Group: g_ip_HN1_03
      #     nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hana-s2-db1
      #     vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hana-s2-db1
      ```


[RHEL에서 Azure vm의 SAP HANA에 대 한 HA](./sap-hana-high-availability-rhel.md#test-the-cluster-setup)에 설명 된 테스트를 수행 하 여 SAP HANA 클러스터 구성을 철저히 테스트 하는 것이 좋습니다.


## <a name="next-steps"></a>다음 단계

* [SAP용 Azure Virtual Machines 계획 및 구현][planning-guide]
* [SAP용 Azure Virtual Machines 배포][deployment-guide]
* [SAP용 Azure Virtual Machines DBMS 배포][dbms-guide]
* Azure Vm에서 SAP HANA의 고가용성을 설정 하 고 재해 복구를 계획 하는 방법에 대 한 자세한 내용은 [azure Virtual Machines (vm)의 SAP HANA 고가용성][sap-hana-ha]을 참조 하세요.