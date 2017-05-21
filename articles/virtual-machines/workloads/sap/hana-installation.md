---
title: "Azure(큰 인스턴스)에서 SAP HANA 설치 | Microsoft Docs"
description: "Azure(큰 인스턴스)에서 SAP HANA를 설치하는 방법"
services: virtual-machines-linux
documentationcenter: 
author: hermanndms
manager: timlt
editor: 
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 12/01/2016
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 54b5b8d0040dc30651a98b3f0d02f5374bf2f873
ms.openlocfilehash: b791be369016dd52d95ec727e46fd8b554c09047
ms.contentlocale: ko-kr
ms.lasthandoff: 04/28/2017


---
# <a name="how-to-install-and-configure-sap-hana-large-instances-on-azure"></a>Azure(큰 인스턴스)에서 SAP HANA를 설치하고 구성하는 방법

SAP HANA의 설치는 사용자가 진행하며 Azure(큰 인스턴스) 서버에서 새 SAP HANA가 제공된 후, Azure VNet 및 HANA 큰 인스턴스 단위 간에 연결이 설정된 후에 작업을 시작할 수 있습니다. SAP 정책에 따라 SAP HANA의 설치는 SAP HANA 설치 인증 시험인 Certified SAP Technology Associate를 통과한 인증된 SAP HANA 설치 담당자 또는 SAP 인증 SI(시스템 통합업체)가 수행해야 합니다.

## <a name="first-steps-after-receiving-the-hana-large-instance-units"></a>HANA 큰 인스턴스 단위를 받은 후 첫 번째 단계

HANA 큰 인스턴스를 받고 인스턴스에 대한 액세스 및 연결을 설정한 후 **첫 번째 단계**는 사용자의 OS 공급자에 인스턴스의 OS를 등록하는 것입니다. 여기에는 배포해야 하는 SUSE SMT 인스턴스에 SUSE Linux OS를 등록하는 것이 포함됩니다. 또는 연결해야 하는 Red Hat Subscription Manager에 RedHat OS를 등록해야 합니다. 이 [문서](https://docs.microsoft.com/azure/virtual-machines/linux/sap-hana-overview-architecture?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)의 설명도 참조하세요. 이 단계는 앞으로 OS를 패치할 수 있도록 하는 데도 필요합니다. 고객에게 책임이 있는 작업입니다. 

**두 번째 단계**는 특정 OS 릴리스/버전의 새 패치 및 수정을 확인하는 것입니다. HANA 큰 인스턴스의 패치 수준이 최신 상태인지 확인하세요. OS 패치/릴리스의 타이밍과 Microsoft가 배포할 수 있는 이미지의 변경 사항에 따라 최신 패치가 포함되지 않을 수 있습니다. 따라서 이 단계는 HANA 큰 인스턴스 단위를 인수하고 Linux 배포자에 등록된 OS를 설치한 후 보안, 기능, 가용성 및 성능과 관련한 패치가 특정 Linux 공급 업체에 의해 릴리스되었는지, 적용해야 하는지 여부를 확인하기 위해 필수적인 단계입니다.

**세 번째 단계**는 특정 OS 릴리스/버전에 SAP HANA를 설치 및 구성하기 위해 관련 SAP Note를 확인하는 것입니다. 개별 설치 시나리오에 따라 SAP Note 또는 구성에 대한 권장 사항이나 변경 사항이 달라지므로 Microsoft에서 항상 HANA 큰 인스턴스 단위를 완벽하게 구성할 수 있는 것은 아닙니다. 따라서 고객으로, SAP Note(최소한 아래 나열된 내용)를 읽고, 아직 수행되지 않은 구성 설정을 적용하는 데 필요한 OS 릴리스/버전의 구성을 확인하기 위해 이 단계가 필요합니다.

구체적으로 다음 매개 변수를 확인하고 결과적으로 조정하세요.

- net.core.rmem_max = 16777216
- net.core.wmem_max = 16777216
- net.core.rmem_default = 16777216
- net.core.wmem_default = 16777216
- net.core.optmem_max = 16777216
- net.ipv4.tcp_rmem = 65536 16777216 16777216
- net.ipv4.tcp_wmem = 65536 16777216 16777216

SLES12 SP1 및 RHEL 7.2부터 이러한 매개 변수를 /etc/sysctl.d 디렉터리의 구성 파일에 설정해야 합니다. 예를 들어 이름이 91-NetApp-HANA.conf인 구성 파일을 만들어야 합니다. 이전 SLES 및 RHEL 릴리스의 경우 /etc/sysctl.conf에서 이러한 매개 변수를 설정해야 합니다.

모든 RHEL 릴리스와 SLES12부터는 
- sunrpc.tcp_slot_table_entries = 128

매개 변수를 /etc/modprobe.d/sunrpc-local.conf에서 설정해야 합니다. 파일이 없으면 다음 항목을 추가하여 먼저 생성해야 합니다. 
- options sunrpc tcp_max_slot_table_entries=128

**네 번째 단계**는 HANA 큰 인스턴스 단위의 시스템 시간을 확인하는 것입니다. 인스턴스는 HANA 큰 인스턴스 스탬프가 있는 Azure 지역의 위치를 나타내는 시스템 표준 시간대로 배포됩니다. 소유한 인스턴스의 시스템 시간이나 표준 시간대를 자유롭게 변경할 수 있습니다. 이렇게 하고 더 많은 인스턴스를 테넌트에 주문하면 새로 전달된 인스턴스의 표준 시간대를 적용해야 합니다. Microsoft 작업에서는 인계 후 인스턴스에서 사용자가 설정한 시스템 표준 시간대에 대해 모릅니다. 따라서 새로 배포된 인스턴스는 변경한 동일한 표준 시간대로 설정되지 않을 수 있습니다. 따라서 인계된 인스턴스의 표준 시간대를 확인하고 필요할 경우 적용하는 것은 고객의 책임입니다. 

**다섯 번째 단계**는 etc/hosts를 확인하는 것입니다. 블레이드를 인계하면 다양한 용도로 서로 다른 IP 주소가 할당됩니다(다음 섹션 참조). etc/hosts를 확인하세요. 기존 테넌트에 단위가 추가된 경우 새로 배포된 시스템의 etc/hosts가 이전에 제공된 시스템의 IP 주소로 올바르게 유지되지 않을 수 있습니다. 따라서 새로 배포된 인스턴스에서 상호 작용하고 테넌트에 이전에 배포된 단위의 이름을 확인할 수 있도록 올바른 설정을 확인하는 것은 고객의 책임입니다. 

## <a name="networking"></a>네트워킹
Azure VNet을 디자인할 때 권장 사항을 따르고 다음 문서에 설명된 대로 Azure VNet을 HANA 큰 인스턴스에 연결한다고 가정합니다.

- [Azure에서 SAP HANA(큰 인스턴스) 개요 및 아키텍처](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)
- [Azure의 SAP HANA(큰 인스턴스) 인프라 및 연결](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

단일 단위의 네트워킹에 대해 알아야 할 몇 가지 세부 사항이 있습니다. 모든 HANA 큰 인스턴스 단위에는 2개 또는 3개의 단위 NIC 포트에 할당된 2개 또는 3개의 IP 주소가 포함되어 있습니다. HANA 확장 구성 및 HANA 시스템 복제 시나리오에는 3개의 IP 주소가 사용됩니다. 단위의 NIC에 할당된 IP 주소 중 하나가 [Azure에서 SAP HANA(큰 인스턴스) 개요 및 아키텍처](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)에 설명된 서버 IP 풀을 벗어납니다.

할당된 두 IP 주소가 있는 단위에 대한 배포는 다음과 같습니다.

- eth0.xx에는 Microsoft에 제출한 서버 IP 풀 주소 범위를 벗어나는 IP 주소가 할당되어 있어야 합니다. 이 IP 주소는 OS의 /etc/hosts에서 유지 관리에 사용됩니다.
- eth1.xx에는 NFS와의 통신에 사용되는 IP 주소가 할당되어 있어야 합니다. 따라서 이러한 주소는 테넌트 내에서 인스턴스 트래픽에 대한 인스턴스를 허용하기 위해 etc/hosts에서 유지 관리할 필요가 **없습니다**.

HANA 시스템 복제 또는 HANA 확장 배포의 경우 두 개의 IP 주소가 할당된 블레이드 구성은 적합하지 않습니다. 두 개의 IP 주소만 할당하고 그러한 구성을 배포하려는 경우 Microsoft Service Management에 문의하여 할당된 세 번째 VLAN에서 세 번째 IP 주소를 얻으세요. 3개의 NIC 포트에 3개의 IP 주소가 할당된 HANA 큰 인스턴스 단위에는 다음이 적용됩니다.

- eth0.xx에는 Microsoft에 제출한 서버 IP 풀 주소 범위를 벗어나는 IP 주소가 할당되어 있어야 합니다. 따라서 이 IP 주소는 OS의 /etc/hosts에서 유지 관리에 사용됩니다.
- eth1.xx에는 NFS 저장소와의 통신에 사용되는 IP 주소가 할당되어 있어야 합니다. 따라서 이 주소 유형은 etc/hosts에서 유지 관리되지 않아야 합니다.
- eth2.xx는 다른 인스턴스 간의 통신을 위해 etc/hosts에서 유지 관리되도록 독점적으로 사용되어야 합니다. 또한 HANA가 노드 간 구성에 사용하는 IP 주소로, 확장 HANA 구성에서 유지 관리해야 하는 IP 주소도 됩니다.



## <a name="storage"></a>저장소

Azure(큰 인스턴스)에서 SAP HANA에 대한 저장소 레이아웃은 SAP 권장 모범 사례를 통해 Azure Service Management의 SAP HANA에 의해 구성됩니다. [SAP HANA 저장소 요구 사항](http://go.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html) 백서를 참조하세요. 이 문서를 읽고 HANA 큰 인스턴스 단위를 살펴보면 단위에 HANA/data에 대해 상당히 많은 디스크 볼륨이 있고 HANA/log/backup 볼륨이 있다는 것을 알게 될 것입니다. HANA/data 크기가 너무 큰 이유는 고객으로서 우리가 제공하는 저장소 스냅숏이 동일한 디스크 볼륨을 사용하고 있기 때문입니다. 따라서 수행하는 저장소 스냅숏이 많을수록 더 많은 공간이 필요합니다. HANA/log/backup 볼륨은 데이터베이스 백업을 넣는 볼륨이 아니라 HANA 트랜잭션 로그의 백업 볼륨으로 활용하는 볼륨으로 간주됩니다. 향후 버전의 저장소 스냅숏 셀프 서비스에서는 HANA 큰 인스턴스 인프라에서 제공하는 재해 복구 기능을 옵션으로 추가하려는 경우 이 특정 볼륨을 대상으로 스냅숏을 자주 생성하고 재해 복구 사이트로 더 자주 복제할 수 있습니다. 자세한 내용은 [Azure의 SAP HANA(큰 인스턴스) 고가용성 및 재해 복구](hana-overview-high-availability-disaster-recovery.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)를 참조하세요. 

제공된 저장소 외에도 1TB 단위로 추가 저장소 용량을 구입할 수 있습니다. 이 추가 저장소는 HANA 큰 인스턴스에 새 볼륨으로 추가될 수 있습니다.

Microsoft Service Management로 온보딩하는 동안 고객은 <SID> ADM 사용자 및 sapsys 그룹(예: 1000,500)에 대한 사용자 ID(UID) 및 그룹 ID(GID)를 지정합니다. SAP HANA 시스템을 설치하는 동안 이 동일한 값이 사용됩니다.

큰 인스턴스 스탬프의 저장소 컨트롤러와 노드는 NTP 서버와 동기화됩니다. Azure(큰 인스턴스) 장치의 SAP HANA와 Azure VM을 NTP 서버에 대해 동기화할 경우 Azure 또는 큰 인스턴스 스탬프에서 인프라 및 계산 장치 간에 중대한 시간 변동이 발생하지 않게 됩니다.

아래 사용된 저장소에 대해 SAP HANA를 최적화하려면 다음 SAP HANA 구성 매개 변수도 설정해야 합니다.

- max_parallel_io_requests 128
- async_read_submit on
- async_write_submit_active on
- async_write_submit_blocks all
 
SAP HANA 1.0 버전 SPS12까지는 [SAP Note #2267798 - SAP HANA 데이터베이스의 구성](https://launchpad.support.sap.com/#/notes/2267798)에 설명된 대로 SAP HANA 데이터베이스 설치 중에 이러한 매개 변수를 설정할 수 있습니다.

hdbparam 프레임워크를 사용하여 SAP HANA 데이터베이스 설치 후 매개 변수를 구성할 수도 있습니다. 

SAP HANA 2.0에서는 hdbparam 프레임워크가 사용되지 않습니다. 따라서 SQL 명령을 사용하여 매개 변수를 설정해야 합니다. 자세한 내용은 [SAP Note #2399079: HANA 2에서 hdbparam 제거](https://launchpad.support.sap.com/#/notes/2399079)를 참조하세요.


## <a name="operating-system"></a>운영 체제

[SUSE Linux Enterprise Server 12 SP1 for SAP Applications](https://www.suse.com/products/sles-for-sap/hana)는 Azure(큰 인스턴스)에서 SAP HANA에 대해 설치되는 Linux 배포판입니다. 이 특정 배포판은 SAP 관련 기능을 &quot;바로 사용할 수 있게&quot; 제공합니다(SLES에서 SAP을 효과적으로 실행하기 위해 미리 설정된 매개 변수 포함).

SLES의 SAP HANA 배포와 관련된 몇 가지 유용한 리소스(고가용성 설정, SAP 작업과 관련된 보안 강화 등)에 대해서는 SUSE 웹 사이트의 [리소스 라이브러리/백서](https://www.suse.com/products/sles-for-sap/resource-library#white-papers) 및 SCN(SAP Community Network)의 [SUSE의 SAP](https://wiki.scn.sap.com/wiki/display/ATopics/SAP+on+SUSE)을 참조하세요.

유용한 추가 SLES 관련 링크:

- [SUSE Linux의 SAP HANA 사이트](https://wiki.scn.sap.com/wiki/display/ATopics/SAP+on+SUSE)
- [SAP 모범 사례: 복제 대기 – SUSE Linux Enterprise 12의 SAP NetWeaver](https://www.suse.com/docrepcontent/container.jsp?containerId=9113)
- [ClamSAP - SAP에 대한 SLES 바이러스 보호](http://scn.sap.com/community/linux/blog/2014/04/14/clamsap--suse-linux-enterprise-server-integrates-virus-protection-for-sap)(SLES 12 for SAP Applications 포함)

SLES 12 SP1의 SAP HANA 구현에 적용할 수 있는 SAP Support Notes:

- [SAP Support Note #1944799 – SLES 운영 체제 설치에 대한 SAP HANA 지침](http://go.sap.com/documents/2016/05/e8705aae-717c-0010-82c7-eda71af511fa.html)
- [SAP Support Note #2205917 – SLES 12 for SAP Applications를 위한 SAP HANA DB 권장 OS 설정](https://launchpad.support.sap.com/#/notes/2205917/E)
- [SAP Support Note #1984787 – SUSE Linux Enterprise Server 12: 설치 참고](https://launchpad.support.sap.com/#/notes/1984787)
- [SAP Support Note #171356 – Linux의 SAP 소프트웨어: 일반 정보](https://launchpad.support.sap.com/#/notes/1984787)
- [SAP Support Note #1391070 – Linux UUID 솔루션](https://launchpad.support.sap.com/#/notes/1391070)

## <a name="time-synchronization"></a>시간 동기화

SAP은 SAP 시스템을 구성하는 다양한 구성 요소의 시간 차이에 매우 민감합니다. 오랫 동안 SAP(Basis)으로 작업해왔다면 오류 제목 ZDATE\_LARGE\_TIME\_DIFF를 갖는 SAP ABAP 짧은 덤프가 친숙할 것입니다. 이러한 짧은 덤프는 다른 서버 또는 VM의 시스템 시간이 너무 큰 차이를 보일 때 나타나기 때문입니다.

Azure(큰 인스턴스)에 있는 SAP HANA의 경우 Azure에서 수행되는 시간 동기화가 큰 인스턴스 스탬프의 계산 장치에 적용되지 않습니다. Azure는 시스템 시간이 제대로 동기화되도록 하므로 Azure(VM)에서 SAP 응용 프로그램을 실행하는 경우는 해당되지 않습니다. 결과적으로 Azure VM에서 실행되는 SAP 응용 프로그램 서버와 HANA 큰 인스턴스에서 실행되는 SAP HANA 데이터베이스 인스턴스에서 사용할 수 있는 별도의 시간 서버를 설정해야 합니다. 큰 인스턴스 스탬프의 저장소 인프라는 NTP 서버와 시간 동기화됩니다.



