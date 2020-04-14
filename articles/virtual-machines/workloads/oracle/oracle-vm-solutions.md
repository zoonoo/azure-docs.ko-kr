---
title: Azure 가상 머신의 오라클 솔루션 | 마이크로 소프트 문서
description: Microsoft Azure에서 Oracle 가상 시스템 이미지의 지원되는 구성 및 제한 사항에 대해 알아봅니다.
services: virtual-machines-linux
documentationcenter: ''
author: mimckitt
manager: gwallace
tags: azure-resource-management
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 05/23/2019
ms.author: mimckitt
ms.custom: mimckitt
ms.openlocfilehash: 0a2374a4c3526b77a25f9fa8faa94c9cb0d4c4ea
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/13/2020
ms.locfileid: "81263236"
---
# <a name="oracle-vm-images-and-their-deployment-on-microsoft-azure"></a>오라클 VM 이미지 및 Microsoft Azure에 배포

이 문서에서는 Azure Marketplace에서 Oracle이 게시한 가상 시스템 이미지를 기반으로 하는 Oracle 솔루션에 대한 정보를 다룹니다. 오라클 클라우드 인프라와 크로스 클라우드 애플리케이션 솔루션에 관심이 있는 경우 [Microsoft Azure 및 Oracle 클라우드 인프라를 통합하는 Oracle 응용 프로그램 솔루션을](oracle-oci-overview.md)참조하십시오.

현재 사용할 수 있는 이미지 목록을 가져오려면 다음 명령을 실행합니다.

```azurecli-interactive
az vm image list --publisher oracle -o table --all
```

2019년 5월부터 다음 이미지를 사용할 수 있습니다.

```bash
Offer                   Publisher    Sku                     Urn                                                          Version
----------------------  -----------  ----------------------  -----------------------------------------------------------  -------------
Oracle-Database-Ee      Oracle       12.1.0.2                Oracle:Oracle-Database-Ee:12.1.0.2:12.1.20170220             12.1.20170220
Oracle-Database-Ee      Oracle       12.2.0.1                Oracle:Oracle-Database-Ee:12.2.0.1:12.2.20180725             12.2.20180725
Oracle-Database-Ee      Oracle       18.3.0.0                Oracle:Oracle-Database-Ee:18.3.0.0:18.3.20181213             18.3.20181213
Oracle-Database-Se      Oracle       12.1.0.2                Oracle:Oracle-Database-Se:12.1.0.2:12.1.20170220             12.1.20170220
Oracle-Database-Se      Oracle       12.2.0.1                Oracle:Oracle-Database-Se:12.2.0.1:12.2.20180725             12.2.20180725
Oracle-Database-Se      Oracle       18.3.0.0                Oracle:Oracle-Database-Se:18.3.0.0:18.3.20181213             18.3.20181213
Oracle-Linux            Oracle       6.10                    Oracle:Oracle-Linux:6.10:6.10.20190506                       6.10.20190506
Oracle-Linux            Oracle       6.8                     Oracle:Oracle-Linux:6.8:6.8.20190506                         6.8.20190506
Oracle-Linux            Oracle       6.9                     Oracle:Oracle-Linux:6.9:6.9.20190506                         6.9.20190506
Oracle-Linux            Oracle       7.3                     Oracle:Oracle-Linux:7.3:7.3.20190506                         7.3.20190506
Oracle-Linux            Oracle       7.4                     Oracle:Oracle-Linux:7.4:7.4.20190506                         7.4.20190506
Oracle-Linux            Oracle       7.5                     Oracle:Oracle-Linux:7.5:7.5.20181207                         7.5.20181207
Oracle-Linux            Oracle       7.5                     Oracle:Oracle-Linux:7.5:7.5.20190506                         7.5.20190506
Oracle-Linux            Oracle       7.6                     Oracle:Oracle-Linux:7.6:7.6.20181207                         7.6.20181207
Oracle-Linux            Oracle       7.6                     Oracle:Oracle-Linux:7.6:7.6.20190506                         7.6.20190506
Oracle-WebLogic-Server  Oracle       Oracle-WebLogic-Server  Oracle:Oracle-WebLogic-Server:Oracle-WebLogic-Server:12.1.2  12.1.2
```

이러한 이미지는 "사용자 라이선스가 필요"한 것으로 간주되며, 따라서 VM을 실행하는 데 발생한 컴퓨팅, 스토리지 및 네트워킹 비용만 청구됩니다.  Oracle 소프트웨어를 사용하는 데 적절한 라이선스가 있으며 현재 Oracle과의 지원 계약이 체결되어 있는 것으로 가정합니다. Oracle에서는 온-프레미스에서 Azure로의 라이선스 이동을 보장합니다. 라이선스 이동에 대한 자세한 내용은 게시된 [Oracle 및 Microsoft](https://www.oracle.com/technetwork/topics/cloud/faq-1963009.html) 참고를 참조하세요. 

또한 개인은 솔루션이 Azure에서 처음부터 만들거나 온-프레미스 환경에서 업로드한 사용자 지정 이미지를 기반으로 하도록 선택할 수도 있습니다.

## <a name="oracle-database-vm-images"></a>오라클 데이터베이스 VM 이미지
오라클은 오라클 리눅스를 기반으로 하는 가상 머신 이미지에서 Azure에서 오라클 데이터베이스 12.1 이상 표준 및 엔터프라이즈 버전 실행을 지원합니다.  Azure에서 Oracle Database의 프로덕션 워크로드에 대한 최상의 성능을 위해 VM 이미지의 크기를 적절히 조정하고 프리미엄 SSD 또는 울트라 SSD 관리 디스크를 사용해야 합니다. Oracle 게시된 VM 이미지를 사용하여 Azure에서 Oracle 데이터베이스를 빠르게 가동하고 실행하는 방법에 대한 지침은 [Oracle 데이터베이스 빠른 시작 연습에서 사용해 보십시오.](oracle-database-quick-create.md)

### <a name="attached-disk-configuration-options"></a>연결된 디스크 구성 옵션

연결된 디스크는 Azure Blob Storage 서비스에 의존합니다. 각 표준 디스크는 이론적으로 최대 초당 500개의 입력/출력 작업(IOPS)을 할 수 있습니다. 프리미엄 디스크 제품은 고성능 데이터베이스 워크로드에 적합하며 디스크당 최대 5000개의 IOPS를 제공할 수 있습니다. 성능 요구 사항을 충족하는 경우 단일 디스크를 사용할 수 있습니다. 그러나 연결된 디스크를 여러 개 사용하고 데이터베이스 데이터를 분산한 다음 ORACLE 자동 저장소 관리(ASM)를 사용하는 경우 효과적인 IOPS 성능을 향상시킬 수 있습니다. Oracle ASM 관련 정보는 [Oracle 자동 스토리지 개요](https://www.oracle.com/technetwork/database/index-100339.html)를 참조하세요. Linux Azure VM에서 Oracle ASM을 설치하고 구성하는 방법에 대한 예는 Oracle 자동 저장소 관리 관리 자습서의 [설치 및 구성 자습서를](configure-oracle-asm.md) 참조하십시오.

### <a name="shared-storage-configuration-options"></a>공유 저장소 구성 옵션

Azure NetApp 파일은 클라우드의 데이터베이스와 같은 고성능 워크로드를 실행하는 핵심 요구 사항을 충족하도록 설계되었으며,
- VM 네이티브 NFS 클라이언트 또는 Oracle dNFS를 통해 오라클 워크로드를 실행하기 위한 Azure 네이티브 공유 NFS 스토리지 서비스
- IOPS 요구의 실제 범위를 반영하는 확장 가능한 성능 계층
- 짧은 대기 시간
- 일반적으로 미션 크리티컬 엔터프라이즈 워크로드(예: SAP 및 Oracle)에서 요구되는 고가용성, 높은 내구성 및 확장성
- 가장 공격적인 RTO 및 RPO SLA를 달성하기 위해 빠르고 효율적인 백업 및 복구

Azure NetApp 파일은 Azure 데이터 센터 환경에서 실행되는 NetApp® ONTAP® 올플래시 시스템을 기반으로 하므로 이러한 기능이 가능합니다. 그 결과 다른 Azure 저장소 옵션과 마찬가지로 프로비전하고 사용할 수 있는 이상적인 데이터베이스 저장소 기술이 생성됩니다. Azure NetApp 파일 NFS 볼륨을 배포하고 액세스하는 방법에 대한 자세한 내용은 [Azure NetApp 파일 설명서를](https://docs.microsoft.com/azure/azure-netapp-files/) 참조하십시오. [Azure NetApp 파일에서 Oracle](https://www.netapp.com/us/media/tr-4780.pdf) 데이터베이스를 운영하기 위한 모범 사례 권장 사항은 Azure NetApp 파일을 사용하는 Azure 배포 모범 사례 가이드를 참조하십시오.


## <a name="licensing-oracle-database--software-on-azure"></a>Azure에서 소프트웨어에 & 오라클 데이터베이스 라이선스
Microsoft Azure는 오라클 데이터베이스를 실행하기 위한 권한이 부여된 클라우드 환경입니다. 오라클 코어 팩터 테이블은 클라우드에서 Oracle 데이터베이스에 라이선스를 부여할 때 적용되지 않습니다. 대신 엔터프라이즈 에디션 데이터베이스에 사용하도록 설정된 하이퍼 스레딩 기술이 있는 VM을 사용하는 경우 하이퍼스레딩이 활성화된 경우 두 개의 vCPU를 Oracle Processor 라이선스 1개와 동일한 것으로 계산합니다(정책 문서에 명시된 대로). 정책 세부 정보는 [여기에서](http://www.oracle.com/us/corporate/pricing/cloud-licensing-070579.pdf)확인할 수 있습니다.
오라클 데이터베이스는 일반적으로 더 높은 메모리와 IO가 필요합니다. 이러한 이유로 이러한 워크로드에 [메모리 최적화 VM이](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-memory) 권장됩니다. 워크로드를 추가로 최적화하기 위해 [제한된 코어 vCPU는](https://docs.microsoft.com/azure/virtual-machines/linux/constrained-vcpu) 높은 메모리, 스토리지 및 I/O 대역폭이 필요하지만 코어 수가 많지 않은 Oracle Database 워크로드에 권장됩니다.

오라클 소프트웨어 및 워크로드를 온-프레미스에서 Microsoft Azure로 마이그레이션할 때 오라클은 [Azure FAQ에서 오라클에](https://www.oracle.com/cloud/technologies/oracle-azure-faq.html) 명시된 대로 라이선스 이동성을 제공합니다.


## <a name="oracle-real-application-cluster-oracle-rac"></a>Oracle RAC(Oracle Real Application Cluster)
오라클 실제 애플리케이션 클러스터(Oracle RAC)는 온-프레미스 멀티 노드 클러스터 구성에서 단일 노드의 오류를 완화하도록 설계되었습니다. 이 기능은 하이퍼스케일(hyper-scale) 퍼블릭 클라우드 환경에 네이티브가 아닌 두 가지 온-프레미스 기술인 네트워크 멀티캐스트 및 공유 디스크에 의존합니다. 데이터베이스 솔루션에 Azure의 Oracle RAC가 필요한 경우 이러한 기술을 사용하도록 설정하려면 타사 소프트웨어가 필요합니다. 오라클 RAC에 대한 자세한 내용은 [플래시그리드 스카이클러스터 페이지를](https://www.flashgrid.io/oracle-rac-in-azure/)참조하십시오.

## <a name="high-availability-and-disaster-recovery-considerations"></a>높은 가용성 및 재해 복구 고려 사항
Azure에서 Oracle 데이터베이스를 사용하는 경우 가동 중지 시간을 방지하기 위해 고가용성 및 재해 복구 솔루션을 구현해야 합니다. 

오라클 데이터베이스 엔터프라이즈 에디션의 고가용성 및 재해 복구(Oracle RAC에 의존하지 않고)는 [데이터 가드, 액티브 데이터 가드](https://www.oracle.com/database/technologies/high-availability/dataguard.html)또는 Oracle [GoldenGate를](https://www.oracle.com/technetwork/middleware/goldengate)사용하여 Azure에서 두 개의 별도 가상 머신에 두 개의 데이터베이스를 사용하여 달성할 수 있습니다. 두 가상 머신은 영구적인 개인 IP 주소를 통해 서로 액세스할 수 있도록 동일한 [가상 네트워크](https://azure.microsoft.com/documentation/services/virtual-network/)에 있어야 합니다.  또한 Azure가 개별 장애 도메인 및 업그레이드 도메인에 가상 머신을 배치할 수 있도록 동일한 가용성 집합에 VM을 배치하는 것이 좋습니다. 지리적 중복성을 원한다면 두 데이터베이스를 설정하여 두 개의 서로 다른 지역 간에 복제하고 두 인스턴스를 VPN 게이트웨이에 연결합니다.

[Azure에서 Oracle 데이터 가드 구현](configure-oracle-dataguard.md) 자습서에서는 Azure의 기본 설정 절차를 안내합니다.  

Oracle 데이터 가드로, 가상 머신에서 주 데이터베이스, 또 다른 가상 머신에서 보조(대기) 데이터 베이스에서 고가용성을 얻을 수 있으며, 양 데이터베이스 간에 단방향 복제를 설정할 수 있습니다. 결과는 해당 데이터 베이스의 복사본에 대한 읽기 엑세스입니다. Oracle 골든 게이트로, 두 개의 데이터베이스 간의 양방향 복제를 구성할 수 있습니다. 이러한 도구를 사용하여 데이터베이스에 대한 고가용성 솔루션을 설정하는 방법을 알아보려면 Oracle 웹 사이트에서 [Active Data Guard](https://www.oracle.com/database/technologies/high-availability/dataguard.html) 및 [GoldenGate](https://docs.oracle.com/goldengate/1212/gg-winux/index.html) 설명서를 참조하세요. 데이터베이스의 복사본에 대한 읽기-쓰기 권한이 필요한 경우 [Oracle Active Data Guard](https://www.oracle.com/uk/products/database/options/active-data-guard/overview/index.html)를 사용할 수 있습니다.

[Azure에서 Oracle GoldenGate 구현](configure-oracle-golden-gate.md) 자습서에서는 Azure의 기본 설정 절차를 안내합니다.

Azure에서 HA 및 DR 솔루션을 설계하는 것 외에도 데이터베이스를 복원하는 백업 전략이 있어야 합니다. [오라클 데이터베이스를 백업하고 복구하는](oracle-backup-recovery.md) 자습서에서는 일관된 백업을 설정하기 위한 기본 절차를 안내합니다.


## <a name="support-for-jd-edwards"></a>JD Edwards 지원
오라클 지원 노트 [Doc ID 2178595.1에](https://support.oracle.com/epmos/faces/DocumentDisplay?_afrLoop=573435677515785&id=2178595.1&_afrWindowMode=0&_adf.ctrl-state=o852dw7d_4)따르면 JD Edwards EnterpriseOne 버전 9.2 이상은 특정(MTR)을 `Minimum Technical Requirements` 충족하는 **모든 퍼블릭 클라우드 오퍼링에서** 지원됩니다.  OS 및 소프트웨어 애플리케이션 호환성에 대한 해당 MTR 사양을 충족하는 사용자 지정 이미지를 만들어야 합니다. 


## <a name="oracle-weblogic-server-virtual-machine-images"></a>Oracle WebLogic Server 가상 머신 이미지

* **클러스터링은 Enterprise Edition에서만 지원됩니다.** Oracle WebLogic Server의 엔터프라이즈 버전을 사용하는 경우에만 WebLogic 클러스터링을 사용할 수 있습니다. 오라클 웹로직 서버 표준 에디션과 함께 클러스터링을 사용하지 마십시오.
* **UDP 멀티 캐스트는 지원되지 않습니다.**  Azure는 UDP 유니캐스트를 지원하지만 멀티 캐스팅 및 브로드캐스팅은 지원하지 않습니다. 오라클 웹로직 서버는 Azure UDP 유니캐스트 기능을 사용할 수 있습니다. UDP 유니캐스트에 의존하는 최상의 결과를 얻으려면 WebLogic 클러스터 크기를 정전기로 유지하거나 관리되는 서버가 10개 이하로 유지하는 것이 좋습니다.
* **Oracle WebLogic Server는 공개 및 개인 포트가 T3 액세스에 대해 동일할 것으로 예상합니다(예: 엔터프라이즈 JavaBeans 사용 시).** *SLWLS라는*가상 네트워크에서 둘 이상의 VM으로 구성된 Oracle WebLogic Server 클러스터에서 EJB(서비스 계층) 응용 프로그램이 실행되는 다중 계층 시나리오를 생각해 보십시오. 클라이언트 계층은 동일한 가상 네트워크의 다른 서브넷에 있으며 서비스 계층에서 EJB를 호출하려는 간단한 Java 프로그램을 실행합니다. 서비스 계층을 로드해야 하므로 Oracle WebLogic Server 클러스터의 가상 시스템에 대해 공용 로드 균형 끝점을 만들어야 합니다. 지정한 프라이빗 포트가 공용 포트와 다른 경우(예: 7006:7008), 다음과 같은 오류가 발생합니다.

       [java] javax.naming.CommunicationException [Root exception is java.net.ConnectException: t3://example.cloudapp.net:7006:

       Bootstrap to: example.cloudapp.net/138.91.142.178:7006' over: 't3' got an error or timed out]

   이는 모든 원격 T3 액세스의 경우 Oracle WebLogic Server는 로드 밸러터 포트와 WebLogic 관리 서버 포트가 동일할 것으로 예상하기 때문입니다. 앞의 경우 클라이언트는 포트 7006(부하 분산 장치 포트)에 액세스하고 관리되는 서버는 7008(프라이빗 포트)에서 수신 대기합니다. 이 제한은 T3 액세스에만 적용가능하며 HTTP에는 적용할 수 없습니다.

   이 문제를 방지하려면, 다음 해결 방법중 하나를 사용합니다.

  * T3 액세스 전용 부하 분산 엔드포인트에 대해 동일한 프라이빗 및 공용 포트 번호를 사용합니다.
  * Oracle WebLogic 서버를 시작할 때 다음 JVM 매개 변수를 포함합니다.

    ```
    -Dweblogic.rjvm.enableprotocolswitch=true
    ```

관련 정보는 <https://support.oracle.com>에서 KB 문서 **860340.1**을 참조하세요.

* **동적 클러스터링 및 부하 분산 제한** Oracle WebLogic Server에서 동적 클러스터를 사용하고 Azure의 공용 로드 균형 이량적 인 단일 끝점을 통해 노출한다고 가정합니다. 이 작업은 관리되는 각 서버에 대해 고정 포트 번호를 사용하고(범위에서 동적으로 할당되지 않음) 관리자가 추적하는 컴퓨터보다 더 많은 관리되는 서버를 시작하지 않는 한 수행할 수 있습니다. 즉, 가상 시스템당 관리되는 서버가 하나만 있습니다. 구성으로 인해 가상 시스템(즉, 여러 Oracle WebLogic Server Server 인스턴스가 동일한 가상 컴퓨터를 공유하는 경우)보다 더 많은 Oracle WebLogic Server 서버가 시작되는 경우 Oracle WebLogic Server의 인스턴스 중 하나 이상이 지정된 포트 번호에 바인딩할 수 없습니다. 해당 가상 컴퓨터의 다른 사용자가 실패합니다.

   관리되는 서버에 고유한 포트 번호를 할당하도록 관리 서버를 구성하면 이 구성에 필요한 것처럼 Azure가 단일 공용 포트에서 다수의 프라이빗 포트로 매핑을 지원하지 않기 때문에 부하 분산이 불가능합니다.
* **가상 머신에서 Oracle WebLogic Server의 여러 인스턴스.** 배포 의 요구 사항에 따라 가상 시스템이 충분히 큰 경우 동일한 가상 컴퓨터에서 Oracle WebLogic Server의 여러 인스턴스를 실행하는 것이 좋습니다. 예를 들어 두 개의 코어가 포함된 중간 크기의 가상 컴퓨터에서 Oracle WebLogic Server의 두 인스턴스를 실행하도록 선택할 수 있습니다. 그러나 Oracle WebLogic Server의 여러 인스턴스를 실행하는 가상 컴퓨터를 하나만 사용하는 경우 아키텍처에 단일 오류 지점이 도입되지 않는 것이 좋습니다. 두 개 이상의 가상 컴퓨터를 사용하는 것이 더 나은 방법일 수 있으며 각 가상 컴퓨터는 Oracle WebLogic Server의 여러 인스턴스를 실행할 수 있습니다. Oracle WebLogic Server의 각 인스턴스는 여전히 동일한 클러스터의 일부일 수 있습니다. 그러나 Azure 로드 밸런서에는 로드 균형 서버가 고유한 가상 시스템 간에 분산되어야 하기 때문에 동일한 가상 시스템 내에서 이러한 Oracle WebLogic Server 배포에 의해 노출되는 엔드포인트를 로드 밸런서하는 데 Azure를 사용할 수 없습니다.

## <a name="oracle-jdk-virtual-machine-images"></a>Oracle JDK 가상 머신 이미지
* **JDK 6 및7 최신 업데이트**  Java의 최신 공용 지원 버전(현재 Java 8) 사용이 권장되지만, Azure 또한 JDK 6 및 7 이미지를 사용할 수 있게 합니다. 이는 JDK 8으로 아직 업그레이드 될 준비가 되지 않은 레거시 애플리케이션을 위한 것입니다. 이전 JDK 이미지에 대한 업데이트가 일반 대중에게 더 이상 제공되지 않지만, Oracle과 Microsoft 사이의 파트너 관계로 인하여 Azure에 의해 제공되는 JDK 6 및 7 이미지들은 Oracle이 지원하는 고객중 오직 선택 그룹의 고객들을 위한 가장 최신의 비-공용 업데이트를 포함합니다. JDK 이미지의 최신 버전은 JDK 6 및 7의 업데이트된 릴리스 시간이 지난 후 제공될 것입니다.

   JDK 6 및 7 이미지에서 사용할 수 있는 JDK및 이 이미지에서 파생된 가상 컴퓨터 및 이미지는 Azure 내에서만 사용할 수 있습니다.
* **64비트 JDK.** Azure가 제공하는 Oracle WebLogic Sever 가상 머신 이미지 및 Oracle JDK 가상 머신 이미지는 64비트 버전 Windows Server와 JDK 모두를 포함합니다.

## <a name="next-steps"></a>다음 단계
이제 Microsoft Azure의 가상 시스템 이미지를 기반으로 하는 현재 Oracle 솔루션에 대한 개요가 있습니다. 다음 단계는 Azure에 첫 번째 Oracle 데이터베이스를 배포하는 것입니다.

> [!div class="nextstepaction"]
> [Azure에서 Oracle 데이터베이스 만들기](oracle-database-quick-create.md)
