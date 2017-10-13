---
title: "Azure Site Recovery를 사용하여 다중 SAP NetWeaver 응용 프로그램 배포 보호 | Microsoft Docs"
description: "이 문서에서는 Azure Site Recovery를 사용하여 SAP NetWeaver 응용 프로그램 배포를 보호하는 방법에 대해 설명합니다."
services: site-recovery
documentationcenter: 
author: mayanknayar
manager: rochakm
editor: 
ms.assetid: 
ms.service: site-recovery
ms.workload: backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/07/2017
ms.author: manayar
ms.openlocfilehash: 951980eeba61e53c983d5b23c301c81eee9528bd
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="protect-a-multi-tier-sap-netweaver-application-deployment-using-azure-site-recovery"></a>Azure Site Recovery를 사용하여 다중 SAP NetWeaver 응용 프로그램 배포 보호 

대부분의 중대형 SAP 배포에는 어떤 형태의 재해 복구 솔루션이 있습니다.  점점 더 많은 핵심 비즈니스 프로세스가 SAP과 같은 응용 프로그램으로 옮겨가면서 강력하고 테스트 가능한 재해 복구 솔루션이 중요성이 높아지고 있습니다.  Azure Site Recovery는 SAP에서 테스트 및 통합되었으며, 경쟁 솔루션보다 총 소유 비용(TCO)은 낮으면서 대부분의 온-프레미스 재해 복구 솔루션 기능을 능가합니다.
Azure Site Recovery를 통해 다음을 수행할 수 있습니다.
* 구성 요소를 Azure에 복사하여 온-프레미스로 실행 중인 SAP NetWeaver 및 비-NetWeaver 프로덕션 응용 프로그램 보호를 활성화합니다.
* 구성 요소를 다른 Azure 데이터 센터에 복사하여 Azure를 실행 중인 SAP NetWeaver 및 비-NetWeaver 프로덕션 응용 프로그램 보호를 활성화합니다.
* Site Recovery를 사용하여 클라우드 마이그레이션을 간소화하여 Azure에 SAP 배포를 마이그레이션합니다.
* SAP 응용 프로그램 테스팅을 위해 온디맨드로 프로덕션 클론을 만들어 SAP 프로젝트 업그레이드, 테스트 및 프로토타입 생성을 단순화합니다.

이 문서에서는 [Azure Site Recovery](site-recovery-overview.md)를 사용하여 SAP NetWeaver 응용 프로그램 배포를 보호하는 방법에 대해 설명합니다. 이 문서에서는 Azure Site Recovery를 사용하여 다른 Azure 데이터 센터에 복제하여 Azure의 3계층 SAP NetWeaver 배포를 보호하기 위한 모범 사례, 지원되는 시나리오 및 구성과 테스트 장애 조치(재해 복구 훈련) 및 실제 장애 조치 모두를 포함한 장애 조치 수행 방법에 대해 설명합니다.


## <a name="prerequisites"></a>필수 조건
시작하기 전에 다음 항목을 이해해야 합니다.

1. [Azure에 가상 컴퓨터 복제](azure-to-azure-walkthrough-enable-replication.md)
2. 방법: [복구 네트워크 디자인](site-recovery-azure-to-azure-networking-guidance.md)
3. [Azure로 테스트 장애 조치 수행](azure-to-azure-walkthrough-test-failover.md)
4. [Azure로 장애 조치 수행](site-recovery-failover.md)
5. 방법: [도메인 컨트롤러 복제](site-recovery-active-directory.md)
6. 방법: [SQL Server 복제](site-recovery-sql.md)

## <a name="supported-scenarios"></a>지원되는 시나리오
Azure Site Recovery를 통해 다음 시나리오에서의 재해 복구 솔루션을 구현할 수 있습니다.
* [여기](https://aka.ms/asr-a2a-architecture)에서 설계한 대로 한 Azure 데이터 센터에서 실행되는 SAP 시스템을 다른 Azure 데이터 센터에 복제(Azure - Azure DR) 
* [여기](https://aka.ms/asr-v2a-architecture)에서 설계한 대로 VMWare(또는 물리적) 서버에서 온-프레미스로 실행 중인 SAP 시스템을 Azure 데이터 센터의 DR 사이트로 복제(VMware-Azure DR). 몇 가지 추가적인 구성 요소 필요
* [여기](https://aka.ms/asr-h2a-architecture)에서 설계한 대로 Hyper-V 온-프레미스로 실행 중인 SAP 시스템을 Azure 데이터 센터의 DR 사이트로 복제(Hyper-V 대 Azure DR). 몇 가지 추가적인 구성 요소 필요

이 문서에서는 Azure Site Recovery의 SAP 재해 복구 기능을 보여 주기 위해 첫 번째 사례인 Azure-t-Azure DR을 사용합니다. Azure Site Recovery 복제는 여러 응용 프로그램에 사용되므로 여기서 설명한 프로세스가 다른 시나리오에서도 유효할 수 있습니다.

### <a name="required-foundation-services"></a>필요한 기반 서비스
이 문서 전체에서는 다음 기본 서비스와 함께 배포되었다고 가정합니다.
* ExpressRoute 또는 사이트 간 가상 사설망(VPN)
* Azure에서 하나 이상의 Active Directory 도메인 컨트롤러와 DNS 서버 실행됨

Azure Site Recovery를 배포하기 전에 위의 인프라를 구축하는 것이 좋습니다.


## <a name="typical-sap-application-deployment"></a>일반 SAP 응용 프로그램 배포
일반적으로 대형 SAP 고객은 6~20개의 개별 SAP 응용 프로그램을 배포합니다.  이러한 응용 프로그램 대부분은 SAP NetWeaver ABAP 또는 Java 엔진을 기반으로 합니다.  이러한 핵심 NetWeaver 응용 프로그램을 지원하는 것은 더 적은 규모의 많은 특정 비 NetWeaver SAP 독립 실행형 엔진이며 보통은 비 SAP 응용 프로그램입니다.  

수평 실행되는 모든 SAP 응용 프로그램을 인벤토리하고 배포 모드(2계층 또는 3계층), 버전, 패치, 크기, 변동 속도, 디스크 지속성 요구 사항을 판단하는 것이 매우 중요합니다.

![배포 패턴](./media/site-recovery-sap/sap-typical-deployment.png)

SAP 데이터베이스 지속성 계층은 SQL Server AlwaysOn, Oracle DataGuard 또는 HANA System Replication 등과 같은 기본 DBMS 도구를 통해 보호되어야 합니다. 클라이언트 계층도 Azure Site Recovery로 보호되지는 않지만 DR 데이터 센터에 대한 DNS Propagation 지연, 보안 및 원격 액세스 등과 같이 이 계층에 영향을 미치는 항목을 고려하는 것이 중요합니다.

Azure Site Recovery는 (A)SCS를 포함한 응용 프로그램 계층에 권장됩니다. 비 NetWeaver SAP 응용 프로그램 및 비 SAM 응용 프로그램 같은 기타 응용 프로그램은 전체 SAP 배포 환경의 일부를 구성하며 역시 Azure Site Recovery로 보호되어야 합니다.

## <a name="replicate-virtual-machines"></a>가상 컴퓨터 복제
[이 지침](azure-to-azure-walkthrough-enable-replication.md)에 따라 모든 SAP 응용 프로그램 가상 컴퓨터를 Azure DR 데이터 센터로 복제합니다.

고정 IP를 사용하는 경우 계산 및 네트워크 설정의 네트워크 인터페이스 카드 섹션에서 가상 컴퓨터가 사용할 IP를 지정합니다.

![대상 IP](./media/site-recovery-sap/sap-static-ip.png)


## <a name="creating-a-recovery-plan"></a>복구 계획 만들기
복구 계획을 사용하면 다중 계층 응용 프로그램에서 다양한 계층의 장애 조치를 시퀀싱하여 응용 프로그램의 일관성을 유지할 수 있습니다. 다중 계층 웹 응용 프로그램에 대한 복구 계획을 만드는 동안 [여기](site-recovery-create-recovery-plans.md)서 설명한 단계를 수행합니다.

### <a name="adding-scripts-to-the-recovery-plan"></a>복구 계획에 스크립트 추가
장애 조치/테스트 장애 조치 후에 응용 프로그램이 올바르게 작동하도록 Azure 가상 컴퓨터에서 일부 작업을 수행해야 할 수 있습니다. [이 문서](site-recovery-create-recovery-plans.md#add-scripts)에서 설명한 대로 복구 계획에서 해당 스크립트를 추가하여 DNS 항목 업데이트, 바인딩 및 연결 변경 등과 같은 사후 장애 조치 작업을 자동화할 수 있습니다.

### <a name="dns-update"></a>DNS 업데이트
DNS가 동적 DNS 업데이트를 위해 구성된 경우 일반적으로 가상 컴퓨터를 시작할 때 새 IP로 DNS를 업데이트합니다. 가상 컴퓨터의 새 IP로 DNS를 업데이트하는 명시적인 단계를 추가하려면 복구 계획 그룹의 사후 작업으로 [DNS의 IP를 업데이트하는 스크립트](https://aka.ms/asr-dns-update)를 추가합니다.  

## <a name="example-azure-to-azure-deployment"></a>Azure-Azure 배포 예제
다음 다이어그램에서는 Azure Site Recovery Azure-Azure DR 시나리오를 설명합니다.
* 기본 데이터 센터는 싱가포르에 있으며(Azure 동남아시아) DR 데이터 센터는 홍콩입니다(Azure 동아시아).  이 시나리오에서는 싱가포르에서 동기 모드로 SQL Server AlwaysOn을 실행하는 두 VM을 통해 로컬 고가용성을 제공합니다.
* 파일 공유 ASCS를 사용하여 SAP 단일 실패 지점에서 HA를 제공할 수 있습니다. 파일 공유 ASCS에는 클러스터 공유 디스크가 필요하지 않으며 SIOS 같은 응용 프로그램이 필요하지 않습니다.
* DBMS 계층에 대한 DR 보호는 비동기 복제를 통해 수행합니다.
* 이 시나리오에서는 프로덕션의 정확한 복제본인 DR 솔루션을 설명하는 용어인 “대칭 DR”을 나타내므로 DR SQL Server 솔루션에 로컬 고가용성이 적용됩니다. 데이터베이스 계층에 대칭 DR을 반드시 사용해야 하는 것은 아니며 많은 고객들은 클라우드 배포의 유연성을 활용하여 DR 이벤트 후 신속히 로컬 고가용성 노드를 구축합니다.
* 이 다이어그램에서는 Azure Site Recovery를 통해 복제된 응용 프로그램 서버 계층과 SAP NetWeaver ASCS를 나타냅니다.

![복제 시나리오](./media/site-recovery-sap/sap-replication-scenario.png)

## <a name="doing-a-test-failover"></a>테스트 장애 조치 수행
[이 지침](azure-to-azure-walkthrough-test-failover.md)에 따라 테스트 장애 조치를 수행합니다.

1.  Azure Portal로 이동하여 Recovery Services 자격 증명 모음을 선택합니다.
2.  SAP 응용 프로그램에 대한 복구 계획을 클릭합니다.
3.  '테스트 장애 조치'를 클릭합니다.
4.  복구 지점 및 Azure 가상 네트워크를 선택하여 테스트 장애 조치 프로세스를 시작합니다.
5.  보조 환경이 가동 중이면 유효성 검사를 수행할 수 있습니다.
6.  유효성 검사가 완료되면 '테스트 장애 조치 정리'를 클릭하고 장애 조치 환경을 정리합니다.

## <a name="doing-a-failover"></a>장애 조치 수행
장애 조치를 수행할 때 [이 지침](site-recovery-failover.md)을 따릅니다.

1.  Azure Portal로 이동하여 Recovery Services 자격 증명 모음을 선택합니다.
2.  SAP 응용 프로그램에 대해 만든 복구 계획을 클릭합니다.
3.  '장애 조치'를 클릭합니다.
4.  복구 지점을 선택하여 장애 조치 프로세스를 시작합니다.

## <a name="next-steps"></a>다음 단계
[이 백서](http://aka.ms/asr-sap)에서는 Azure Site Recovery를 사용한 SAP NetWeaver 배포에서의 재해 복구 솔루션 구축에 대해 자세히 알아봅니다. 또한 다양한 SAP 아키텍처의 권장 사항에 대해 설명하고, Azure의 SAP에서 지원되는 응용 프로그램 및 VM 유형을 나열하며, 재해 복구 솔루션에서 가능한 테스트 계획에 대해 설명합니다.

Site Recovery를 사용하여 [다른 워크로드를 복제](site-recovery-workload.md)하는 것에 대해 알아봅니다.
