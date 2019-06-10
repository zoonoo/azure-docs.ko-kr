---
title: Azure virtual machines에서 oracle 솔루션 | Microsoft Docs
description: 지원 되는 구성 및 Microsoft Azure에서 Oracle 가상 머신 이미지의 제한 사항에 알아봅니다.
services: virtual-machines-linux
documentationcenter: ''
author: romitgirdhar
manager: jeconnoc
tags: azure-resource-management
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 05/23/2019
ms.author: rogirdh
ms.custom: seodec18
ms.openlocfilehash: b62b35320ba1f4473e9b3a039d181d6a2fb58257
ms.sourcegitcommit: 7042ec27b18f69db9331b3bf3b9296a9cd0c0402
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/06/2019
ms.locfileid: "66743622"
---
# <a name="oracle-vm-images-and-their-deployment-on-microsoft-azure"></a>Oracle VM 이미지 및 Microsoft Azure에 배포

이 문서에서는 Azure Marketplace의 Oracle에서 게시 하는 가상 머신 이미지를 기반으로 하는 Oracle 솔루션에 대 한 정보를 다룹니다. Oracle 클라우드 인프라를 사용 하 여 클라우드 간 응용 프로그램 솔루션에 관심이 있는 경우 참조 [Microsoft Azure 및 Oracle 클라우드 Infrastructure를 통합 하는 Oracle 응용 프로그램 솔루션](oracle-oci-overview.md)합니다.

현재 사용할 수 있는 이미지 목록을 가져오려면 다음 명령을 실행합니다.

```azurecli-interactive
az vm image list --publisher oracle -o table --all
```

다음 이미지는 2019 년 5 월을 기준으로 사용할 수 있습니다.

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

이러한 이미지는 "사용자 라이선스가 필요"한 것으로 간주되며, 따라서 VM을 실행하는 데 발생한 계산, 스토리지 및 네트워킹 비용만 청구됩니다.  Oracle 소프트웨어를 사용하는 데 적절한 라이선스가 있으며 현재 Oracle과의 지원 계약이 체결되어 있는 것으로 가정합니다. Oracle에서는 온-프레미스에서 Azure로의 라이선스 이동을 보장합니다. 라이선스 이동에 대한 자세한 내용은 게시된 [Oracle 및 Microsoft](https://www.oracle.com/technetwork/topics/cloud/faq-1963009.html) 참고를 참조하세요. 

또한 개인은 솔루션이 Azure에서 처음부터 만들거나 온-프레미스 환경에서 업로드한 사용자 지정 이미지를 기반으로 하도록 선택할 수도 있습니다.

## <a name="support-for-jd-edwards"></a>JD Edwards 지원
Oracle 지원 참고 사항에 따라 [Doc ID 2178595.1](https://support.oracle.com/epmos/faces/DocumentDisplay?_afrLoop=573435677515785&id=2178595.1&_afrWindowMode=0&_adf.ctrl-state=o852dw7d_4), JD Edwards EnterpriseOne 버전 9.2 이상에서 지원 됩니다 **모든 공용 클라우드 제품** 충족 하는 특정 `Minimum Technical Requirements` (MTR).  OS 및 소프트웨어 애플리케이션 호환성에 대한 해당 MTR 사양을 충족하는 사용자 지정 이미지를 만들어야 합니다. 

## <a name="oracle-database-vm-images"></a>Oracle 데이터베이스 VM 이미지
Oracle은 Oracle Linux 기반 가상 머신 이미지의 Azure에서 Oracle DB 12.1 Standard 및 Enterprise Edition을 실행하도록 지원합니다.  Azure에서 Oracle DB의 프로덕션 워크로드에 대한 최상의 성능을 위해 VM 이미지 크기를 적절히 조정하고 Premium Storage에서 지원되는 관리 디스크를 사용해야 합니다. Oracle에서 게시한 VM 이미지를 사용하여 Azure에서 Oracle DB를 신속하게 실행하는 방법에 대한 지침은 [Oracle DB 빠른 시작 연습](oracle-database-quick-create.md)을 참조하세요.

### <a name="attached-disk-configuration-options"></a>연결된 디스크 구성 옵션

연결된 디스크는 Azure Blob Storage 서비스에 의존합니다. 각 표준 디스크는 이론적으로 최대 초당 500개의 입력/출력 작업(IOPS)을 할 수 있습니다. 프리미엄 디스크 제품은 고성능 데이터베이스 워크로드에 적합하며 디스크당 최대 5000개의 IOPS를 제공할 수 있습니다. 성능 요구 사항을 충족 하는 경우 단일 디스크를 사용할 수 있습니다. 그러나 데이터베이스 데이터, 분산 후 Oracle 자동 저장소 관리 (ASM) 사용 하 여 여러 연결 된 디스크를 사용 하는 경우 효과적으로 IOPS 성능을 향상할 수 있습니다. Oracle ASM 관련 정보는 [Oracle 자동 저장소 개요](https://www.oracle.com/technetwork/database/index-100339.html)를 참조하세요. 설치 하 고 Linux Azure VM에서 Oracle ASM를 구성 하는 방법의 예제를 참조 합니다 [설치 및 구성 Oracle Automated Storage Management](configure-oracle-asm.md) 자습서입니다.

## <a name="oracle-real-application-cluster-oracle-rac"></a>Oracle RAC(Oracle Real Application Cluster)
Oracle RAC는 온-프레미스 다중 노드 클러스터 구성에서 단일 노드의 오류를 완화하도록 고안되었습니다. 이 기능은 하이퍼스케일(hyper-scale) 공용 클라우드 환경에 네이티브가 아닌 두 가지 온-프레미스 기술인 네트워크 멀티캐스트 및 공유 디스크에 의존합니다. 세 번째 해야 Azure에서 Oracle RAC를 필요로 하는 데이터베이스 솔루션을 하는 경우 이러한 기술을 사용 하려면 타사 소프트웨어. Oracle RAC에 대 한 자세한 내용은 참조는 [FlashGrid SkyCluster 페이지](https://www.flashgrid.io/oracle-rac-in-azure/)합니다.

## <a name="high-availability-and-disaster-recovery-considerations"></a>높은 가용성 및 재해 복구 고려 사항
Azure에서 Oracle 데이터베이스를 사용할 때는 가동 중지 시간을 방지 하기 위해 높은 가용성 및 재해 복구 솔루션을 구현 하는 일을 담당 합니다. 

고가용성 및 재해 복구 Oracle Database Enterprise Edition에 대 한 (않고 Oracle RAC)를 사용 하 여 Azure에서 수행할 수 있습니다 [Data Guard, Active Data Guard](https://www.oracle.com/technetwork/articles/oem/dataguardoverview-083155.html), 또는 [Oracle GoldenGate](https://www.oracle.com/technetwork/middleware/goldengate), 두 개 있는 두 데이터베이스를 사용 하 여 가상 컴퓨터를 구분 합니다. 두 가상 머신은 영구적인 개인 IP 주소를 통해 서로 액세스할 수 있도록 동일한 [가상 네트워크](https://azure.microsoft.com/documentation/services/virtual-network/)에 있어야 합니다.  또한 Azure가 개별 장애 도메인 및 업그레이드 도메인에 가상 머신을 배치할 수 있도록 동일한 가용성 집합에 VM을 배치하는 것이 좋습니다. 지리적 중복을 포함 하려는, 서로 다른 두 지역 간에 복제 하 여 VPN Gateway를 사용 하 여 두 인스턴스를 연결 하는 두 개의 데이터베이스를 설정 합니다.

이 자습서 [Azure에서 Oracle Data Guard 구현](configure-oracle-dataguard.md) Azure에서 기본 설치 절차를 안내 합니다.  

Oracle 데이터 가드로, 가상 머신에서 주 데이터베이스, 또 다른 가상 머신에서 보조(대기) 데이터 베이스에서 고가용성을 얻을 수 있으며, 양 데이터베이스 간에 단방향 복제를 설정할 수 있습니다. 결과는 해당 데이터 베이스의 복사본에 대한 읽기 엑세스입니다. Oracle 골든 게이트로, 두 개의 데이터베이스 간의 양방향 복제를 구성할 수 있습니다. 이러한 도구를 사용하여 데이터베이스에 대한 고가용성 솔루션을 설정하는 방법을 알아보려면 Oracle 웹 사이트에서 [Active Data Guard](https://www.oracle.com/technetwork/database/features/availability/data-guard-documentation-152848.html) 및 [GoldenGate](https://docs.oracle.com/goldengate/1212/gg-winux/index.html) 설명서를 참조하세요. 데이터베이스의 복사본에 대한 읽기-쓰기 권한이 필요한 경우 [Oracle Active Data Guard](https://www.oracle.com/uk/products/database/options/active-data-guard/overview/index.html)를 사용할 수 있습니다.

이 자습서 [Azure에서 Oracle GoldenGate 구현](configure-oracle-golden-gate.md) Azure에서 기본 설치 절차를 안내 합니다.

HA 및 DR 솔루션이 Azure에서 설계를 가져야 하는 것 외에도 데이터베이스를 복원할 수 있는 백업 전략을 해야 합니다. 이 자습서 [는 Oracle 데이터베이스 Backup 및 복구](oracle-backup-recovery.md) 일관 된 백업 설정에 대 한 기본 절차를 단계별로 안내 합니다.

## <a name="oracle-weblogic-server-virtual-machine-images"></a>Oracle WebLogic Server 가상 머신 이미지

* **클러스터링은 Enterprise Edition에서만 지원됩니다.** WebLogic Server Enterprise Edition을 사용할 때만 WebLogic 클러스터링을 사용하도록 허가됩니다. WebLogic Server Standard Edition으로 클러스터링을 사용하지 마십시오.
* **UDP 멀티 캐스트는 지원되지 않습니다.** Azure는 UDP 유니캐스트를 지원하지만 멀티 캐스팅 및 브로드캐스팅은 지원하지 않습니다. WebLogic 서버는 Azure UDP 유니캐스 기능에 의존할 수 있습니다. UDP 유니캐스트에 의존 하는 최상의 결과 WebLogic 클러스터 크기를 정적 또는 유지 관리 되는 서버 수가 10 개를 사용 하 여 유지 되도록 하는 것이 좋습니다.
* **WebLogic Server는 T3 액세스(예: Enterprise JavaBeans를 사용할 때)에 대해 공용 및 개인 포트가 동일한 것으로 예상합니다.** 서비스 계층 (EJB) 응용 프로그램 이라는 가상 네트워크에 둘 이상의 Vm으로 구성 된 WebLogic Server 클러스터에서 실행 되 고 있는 다중 계층 시나리오를 고려해 보겠습니다 *SLWLS*합니다. 클라이언트 계층은 서비스 계층에서 EJB 호출을 시도 하는 간단한 Java 프로그램을 실행 동일한 가상 네트워크에서 다른 서브넷에 있습니다. 부하 분산 서비스 계층에 필요한 이기 때문에 WebLogic Server 클러스터의 가상 컴퓨터에 대해 생성 될 공용 부하 분산 된 끝점이 필요 합니다. 지정한 프라이빗 포트가 공용 포트와 다른 경우(예: 7006:7008), 다음과 같은 오류가 발생합니다.

       [java] javax.naming.CommunicationException [Root exception is java.net.ConnectException: t3://example.cloudapp.net:7006:

       Bootstrap to: example.cloudapp.net/138.91.142.178:7006' over: 't3' got an error or timed out]

   이는 모든 원격 T3 액세스에 대해, WebLogic Server가  부하 분산 장치 포트 및 WebLogic 관리 서버가 동일한 것으로 간주하기 때문입니다. 앞의 경우 클라이언트는 포트 7006(부하 분산 장치 포트)에 액세스하고 관리되는 서버는 7008(프라이빗 포트)에서 수신 대기합니다. 이 제한은 T3 액세스에만 적용가능하며 HTTP에는 적용할 수 없습니다.

   이 문제를 방지하려면, 다음 해결 방법중 하나를 사용합니다.

  * T3 액세스 전용 부하 분산 엔드포인트에 대해 동일한 프라이빗 및 공용 포트 번호를 사용합니다.
  * WebLogic Server를 시작하는 경우 다음 JVM 매개변수를 포함합니다.

    ```
    -Dweblogic.rjvm.enableprotocolswitch=true
    ```

관련 정보는 <https://support.oracle.com>에서 KB 문서 **860340.1**을 참조하세요.

* **동적 클러스터링 및 부하 분산 제한** WebLogic 서버에서 동적 클러스터를 사용하고 Azure에서 단일, 공용 부하 분산 엔드포인트를 통해 노출한다고 가정해 보세요. 이렇게 하려면 각 관리 서버 (범위에서 할당 되지 동적으로)에 대 한 고정 된 포트 번호를 사용 하 고 컴퓨터 관리자가 추적 하는 것 보다 더 많은 관리 서버를 시작 하지 않습니다. 즉, 서버가 둘 이상의 관리 되는 가상 머신 당)입니다. 구성 결과 많은 WebLogic 서버가 보다 가상 머신이 시작 되는 경우 (즉, 여기서 여러 WebLogic Server의 인스턴스가 동일한 가상 머신을 공유), 인스턴스 WebLogic 서버 중 하나 이상이 가능한 됩니다 주어진된 포트 번호를 바인딩하려면. 가상 머신에서 나머지 실패합니다.

   관리되는 서버에 고유한 포트 번호를 할당하도록 관리 서버를 구성하면 이 구성에 필요한 것처럼 Azure가 단일 공용 포트에서 다수의 프라이빗 포트로 매핑을 지원하지 않기 때문에 부하 분산이 불가능합니다.
* **가상 머신에서 WebLogic 서버의 여러 인스턴스.** 배포의 요구 사항에 따라 좋습니다 동일한 가상 머신에서 WebLogic 서버의 여러 인스턴스를 실행 중인 가상 컴퓨터가 충분히 큰 경우. 예를 들면 두 개의 코어를 갖고 있는 중간 크기의 가상 머신에서 WebLogic Server의 두 개의 인스턴스 실행을 선택할 수 있습니다. 그러나 WebLogic Server의 여러 인스턴스를 실행 하는 단지 하나의 가상 컴퓨터를 사용 하는 경우에 해당 될 수 있는 아키텍처에 단일 실패 지점을 도입 하지 않는 것이 좋습니다. 두 개 이상의 가상 컴퓨터를 사용 하 여 더 나은 방법은, 수 및 각 가상 머신에 WebLogic Server의 여러 인스턴스를 실행할 수 있습니다. WebLogic Server의 각 인스턴스는 동일한 클러스터의 일부가 해질 수 있습니다. 그러나 현재 수 없는 Azure 부하 분산 장치에 분산 되도록 고유한 부하 분산 서버에 필요 하므로 동일한 가상 머신 내의 WebLogic 서버 배포에 의해 노출 되는 부하 분산 끝점에 Azure를 사용 하려면 가상 머신입니다.

## <a name="oracle-jdk-virtual-machine-images"></a>Oracle JDK 가상 머신 이미지
* **JDK 6 및7 최신 업데이트** Java의 최신 공용 지원 버전(현재 Java 8) 사용이 권장되지만, Azure 또한 JDK 6 및 7 이미지를 사용할 수 있게 합니다. 이는 JDK 8으로 아직 업그레이드 될 준비가 되지 않은 레거시 애플리케이션을 위한 것입니다. 이전 JDK 이미지에 대한 업데이트가 일반 대중에게 더 이상 제공되지 않지만, Oracle과 Microsoft 사이의 파트너 관계로 인하여 Azure에 의해 제공되는 JDK 6 및 7 이미지들은 Oracle이 지원하는 고객중 오직 선택 그룹의 고객들을 위한 가장 최신의 비-공용 업데이트를 포함합니다. JDK 이미지의 최신 버전은 JDK 6 및 7의 업데이트된 릴리스 시간이 지난 후 제공될 것입니다.

   JDK 6 및 7 이미지와 virtual machines 및에서 파생 되는 이미지에서 사용할 수 있는 JDK은 Azure 내 에서만 사용할 수 있습니다.
* **64비트 JDK.** Azure가 제공하는 Oracle WebLogic Sever 가상 머신 이미지 및 Oracle JDK 가상 머신 이미지는 64비트 버전 Windows Server와 JDK 모두를 포함합니다.

## <a name="next-steps"></a>다음 단계
이제 Microsoft Azure에서 가상 머신 이미지를 기준으로 현재 Oracle 솔루션의 개요입니다. 다음 단계는 Azure에서 첫 번째 Oracle 데이터베이스를 배포 하는 것입니다.

> [!div class="nextstepaction"]
> [Azure에서 Oracle 데이터베이스 만들기](oracle-database-quick-create.md)
