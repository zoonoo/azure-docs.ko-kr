---
title: 'Azure의 SAP: Azure에서 지원 되는 SAP 소프트웨어'
description: Azure에 배포 하도록 지원 되는 SAP 소프트웨어에 대해 설명 합니다.
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: SAP
ms.assetid: d7c59cc1-b2d0-4d90-9126-628f9c7a5538
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 04/21/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 8b2f4e7a16c967b26b545d1405f973bf8b8afaae
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "82086133"
---
# <a name="what-sap-software-is-supported-for-azure-deployments"></a>Azure 배포를 지원하는 SAP 소프트웨어
이 문서에서는 Azure 배포에 대해 지원 되는 SAP 소프트웨어 및 필요한 운영 체제 릴리스 또는 DBMS 릴리스가 무엇 인지 확인 하는 방법을 설명 합니다.

현재 SAP 소프트웨어가 지원 되는지 여부와 Azure의 SAP 소프트웨어에서 지원 되는 OS 및 DBMS 릴리스를 평가 하는 데에는 다음에 대 한 액세스 권한이 필요 합니다.

- SAP 지원 참고 사항
- SAP 제품 가용성 매트릭스



## <a name="general-restrictions-for-sap-workload"></a>SAP 워크 로드에 대 한 일반적인 제한 사항
SAP 워크 로드에 사용할 수 있는 Azure IaaS 서비스는 x86-64 또는 x64 하드웨어로 제한 됩니다. SAP 워크 로드에 적용 되는 Sparc 또는 전원 CPU 기반 제안은 없습니다. IBM 메인프레임 또는 AS400와 같은 하드웨어 아키텍처를 소유 하는 운영 체제에서 응용 프로그램을 실행 하는 고객 또는 HP UX, Solaris 또는 AIX 운영 체제를 사용 하는 고객은 DBMS를 비롯 한 SAP 응용 프로그램을 다음 운영 체제 중 하나로 변경 해야 합니다.

- X86-64 플랫폼용 Windows server 64 비트
- X86-64 플랫폼용 SUSE linux 64 비트
- X86-64 플랫폼용 Red hat Linux 64 비트
- X86-64 플랫폼용 Oracle Linux 64 비트

SAP 소프트웨어와 함께 다른 OS 릴리스 또는 Linux 배포판은 지원 되지 않습니다. 특정 버전 및 사례에 대 한 정확한 세부 정보는이 문서의 뒷부분에 설명 되어 있습니다.


## <a name="you-start-here"></a>여기에서 시작
사용자의 시작 지점은 [SAP support note #1928533](https://launchpad.support.sap.com/#/notes/1928533)입니다. 이 SAP note를 위에서 아래로 이동 하면 지원 되는 소프트웨어 및 Vm의 여러 영역이 표시 됩니다.

첫 번째 섹션에는 일반적으로 Azure Vm의 SAP 소프트웨어에서 지원 되는 운영 릴리스에 대 한 최소 요구 사항이 나열 되어 있습니다. 이러한 최소 요구 사항에 도달 하지 않고 이러한 운영 체제의 이전 릴리스를 실행 하는 경우 OS 릴리스를 해당 릴리스 또는 최신 릴리스로 업그레이드 해야 합니다. 일반적으로 Azure는 이러한 운영 체제 중 일부의 이전 릴리스를 지원 합니다. 그러나 나열 된 것과 같은 제한 또는 최소 릴리스는 실행 된 테스트 및 자격을 기반으로 하며 더 이상 다시 확장 되지 않습니다. 


> [!NOTE]
>최신 OS 릴리스가 필요한 몇 가지 특정 VM 유형, HANA Large 인스턴스 또는 SAP 워크 로드가 있습니다. 이와 같은 사례는 문서 전체에서 언급 됩니다. 이러한 사례는 SAP note 또는 다른 SAP 게시에서 명확 하 게 문서화 됩니다.

다음 섹션에는 지원 되는 릴리스와 지원 되는 SAP 커널에서 지원 되는 일반적인 SAP 플랫폼이 나와 있습니다. 최소 커널 릴리스가 필요한 및 지원 되는 NetWeaver/ABAP 또는 Java 스택을 나열 합니다. 최신 ABAP 스택은 Azure에서 지원 되지만, Azure에 대 한 변경 내용이 최신 스택 개발의 시작 부분에서 구현 되었으므로 최소 커널 릴리스가 필요 하지 않습니다.

다음을 확인 해야 합니다.

- 실행 하는 SAP 응용 프로그램에는 지정 된 최소 릴리스가 적용 됩니다. 그렇지 않은 경우 새 대상 릴리스를 정의 하 고, SAP 제품 가용성 매트릭스를 확인 하 고, 새 대상 릴리스에서 지원 되는 운영 체제 빌드 및 DBMS 조합을 확인 해야 합니다. 따라서 올바른 운영 체제 릴리스 및 DBMS 릴리스를 선택할 수 있습니다.
- Azure로 이동에서 SAP 커널을 업데이트 해야 하는지 여부
- SAP 지원 패키지를 업데이트 해야 하는지 여부입니다. 특히 최신 DBMS 릴리스로 이동 해야 하는 경우에 필요할 수 있는 패키지를 지원 합니다.


다음 섹션에서는 Windows 및 Linux 용 Azure의 SAP에서 지원 되는 다른 SAP 제품 및 DBMS 릴리스에 대해 자세히 설명 합니다. 

> [!NOTE]
> 다른 DBMS의 최소 릴리스는 신중 하 게 선택 되어 있으며, 일반적으로 Azure에서 다른 DBMS 공급 업체가 지 원하는 DBMS 릴리스의 전체 스펙트럼을 반영 하지 않을 수 있습니다. 이러한 최소 릴리스를 정의 하기 위해 많은 SAP 워크 로드 관련 고려 사항을 고려 했습니다. 이전 DBMS 릴리스를 테스트 하 고 한정할 필요가 없습니다. 

> [!NOTE]
> 나열 된 최소 릴리스는 이전 버전의 운영 체제 및 데이터베이스 릴리스를 나타냅니다. 최신 운영 체제 릴리스 및 데이터베이스 릴리스를 사용 하는 것이 좋습니다. 많은 경우에 최신 운영 체제 및 데이터베이스 릴리스가 공용 클라우드에서 실행 하는 데 사용 되는 사용 사례를 고려 하 고 조정 된 코드를 사용 하 여 공용 클라우드 또는 보다 구체적으로 Azure에서 실행 되도록 최적화 합니다.

## <a name="oracle-dbms-support"></a>Oracle DBMS 지원
Azure에서 지원 되는 운영 체제, Oracle DBMS 릴리스 및 Oracle 기능은 [SAP support note #2039619](https://launchpad.support.sap.com/#/notes/2039619)에 구체적으로 나와 있습니다. 이러한 메모의 핵심은 다음과 같이 요약할 수 있습니다.

- NetWeaver에 대해 인증 된 Azure Vm에서 지원 되는 최소 Oracle 릴리스는 Oracle 11g Release 2 2010 3 (11.2.0.4)입니다.
- 게스트 운영 체제는 Windows 및 Oracle Linux 한정 됩니다. OS 및 관련 된 최소 DBMS 릴리스의 정확한 릴리스는 다음 항목에 나와 있습니다.
- Oracle Linux에 대 한 지원은 Oracle DBMS 클라이언트로도 확장 됩니다. 즉, ABAP 또는 Java 스택의 대화 상자 인스턴스와 같은 모든 SAP 구성 요소가 Oracle Linux 에서도 실행 되어야 합니다. Oracle DBMS에 연결 되지 않는 이러한 SAP 시스템 내의 SAP 구성 요소만 다른 Linux 운영 체제를 실행할 수 있습니다.
- Oracle RAC는 지원 되지 않습니다. 
- 일부 경우에는 Oracle ASM이 지원 됩니다. 자세한 내용은 다음 항목을 참조 하십시오.
- 유니코드가 아닌 SAP 시스템은 Windows 게스트 OS에서 실행 되는 응용 프로그램 서버 에서만 지원 됩니다. DBMS의 게스트 운영 체제는 Oracle Linux 또는 Windows 일 수 있습니다. 이 제한에 대 한 이유는 SAP PAM (제품 가용성 매트릭스)을 확인할 때 드러납니다. Oracle Linux의 경우 SAP에서 유니코드가 아닌 SAP 커널을 릴리스되지 않았습니다.

대상 Azure 인프라에서 지원 되는 DBMS 릴리스를 알고 있어야 합니다. OS 릴리스 및 DBMS가 실행 하려는 SAP 제품 릴리스에서 지원 되는지 여부에 대 한 SAP 제품 가용성 매트릭스를 확인 해야 합니다. 


## <a name="sap-hana-support"></a>지원 SAP HANA
Azure에서 HANA 데이터베이스를 실행 하는 데 사용할 수 있는 두 가지 서비스가 있습니다.

- Azure Virtual Machines
- [HANA Large Instances](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)

SAP HANA를 실행 하기 위해 SAP는 NetWeaver 또는 다른 SAP 응용 프로그램 및 DBMS를 실행 하는 것 보다 더 강력 하 고 더 강력한 조건을 충족 해야 합니다. 따라서 SAP HANA DBMS를 실행 하는 데 적합 한 Azure Vm의 수가 적습니다. SAP HANA에 대해 지원 되는 지원 되는 Azure 인프라 목록은 [SAP HANA 하드웨어 디렉터리](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)라고 하는에서 찾을 수 있습니다. 

> [!NOTE]
> 문자 ' '로 시작 하는 단위는 [HANA Large Instances](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) 단위입니다. 

> [!NOTE]
> SAP에는 SAP HANA 주 릴리스에 종속 된 특정 인증이 없습니다. [Hana 인증 IaaS 플랫폼](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)의 열 **인증 시나리오** 와는 달리,이 열은 **hana 주 또는 부 릴리스 인증에 대 한 문을 수행 하지 않습니다**. 특정 단위에 대 한 인증 된 운영 체제 릴리스가 HANA 1.0 릴리스부터도 지원 되는 한 HANA 1.0 및 HANA 2.0에 사용할 수 있는 모든 단위가 나열 되어 있다고 가정 해야 합니다. 

SAP HANA 사용 하는 경우에는 일반적인 NetWeaver 사례 보다 다른 최소 OS 릴리스가 적용 될 수 있습니다. 각 단위에 대해 지원 되는 운영 체제는 달라질 수 있으므로 개별적으로 체크 아웃 해야 합니다. 각 단위를 클릭 하 여이 작업을 수행 합니다. 자세한 정보가 표시 됩니다. 나열 된 세부 정보 중 하나는이 특정 단위에 대해 지원 되는 운영 체제입니다.

> [!NOTE]
> Azure HANA Large Instance 장치는 Azure Vm과 비교 하 여 지원 되는 운영 체제에서 더 제한적입니다. 반면에 Azure Vm은 최신 운영 릴리스를 최소 릴리스로 적용할 수 있습니다. Linux 커널에서 변경 해야 하는 더 큰 VM 단위 중 일부에는 특히 그렇습니다.

Azure 인프라에 대해 지원 되는 OS를 알고 있으면 대상으로 하는 Azure 단위에서 지원 되는 정확한 SAP HANA 릴리스 및 패치 수준에 대 한 [SAP support note #2235581](https://launchpad.support.sap.com/#/notes/2235581) 를 확인 해야 합니다. 

> [!IMPORTANT]
> 지원 되는 정확한 SAP HANA 릴리스와 패치 수준을 확인 하는 단계는 매우 중요 합니다. 많은 경우에 특정 OS 릴리스 지원은 SAP HANA 실행 파일의 특정 패치 수준에 따라 달라 집니다.

대상 Azure 인프라에서 실행할 수 있는 특정 HANA 릴리스를 알고 있으므로 SAP 제품 가용성 매트릭스를 확인 하 여 필터링 한 HANA 릴리스를 지 원하는 SAP 제품 릴리스에 제한이 있는지 확인 해야 합니다.


## <a name="certified-azure-vms-and-hana-large-instance-units-and-business-transaction-throughput"></a>인증 된 Azure Vm 및 HANA Large Instance 단위 및 비즈니스 트랜잭션 처리량
지원 되는 운영 체제 릴리스를 평가 하는 것 외에도, DBMS 릴리스 및 종속 된 Azure 인프라 단위에 대 한 SAP 소프트웨어 릴리스를 확인 하는 것 외에도 SAP에서 ' SAP ' 단위로 표현 된 비즈니스 트랜잭션 처리량으로 이러한 단위를 한정할 필요가 있습니다. 모든 SAP 크기 조정은 SAPS 계산에 따라 달라 집니다. 일반적으로 인프라 공급자의 도움으로 기존 SAP 시스템을 평가 하 여 단위의 SAPS 계산 합니다. 응용 프로그램 계층 뿐만 아니라 DBMS 계층의 경우입니다. 새 기능이 생성 되는 경우 SAP를 사용 하는 크기 조정 연습을 통해 응용 프로그램 계층 및 DBMS 계층에 필요한 SAPS 수를 표시할 수 있습니다. 인프라 공급자는 Microsoft가 NetWeaver 및/또는 HANA 인증 된 다른 단위의 SAP 처리량 특성화를 제공 하는 것을 의무 없지만.

Azure Vm의 경우 이러한 SAPS 처리량 번호는 [SAP support note #1928533](https://launchpad.support.sap.com/#/notes/1928533)에 설명 되어 있습니다. Azure HANA Large Instance 유닛의 경우 SAPS 처리량 번호는 [SAP support note](https://launchpad.support.sap.com/#/notes/2316233) 에 설명 되어 있습니다 #2316233

[SAP support note #1928533](https://launchpad.support.sap.com/#/notes/1928533)를 살펴보면 다음과 같은 주의 사항을 적용 합니다.

- **M 시리즈 azure vm 및 Mv2 시리즈 azure Vm의 경우 다른 AZURE vm 유형에 비해 다른 최소 OS 릴리스가 적용**됩니다. 최신 OS 릴리스를 위한 요구 사항은 특정 Azure VM 유형에 서 실행 중인 운영 체제를 사용 하도록 설정 하거나 해당 VM 유형에 서 SAP 워크 로드의 성능 및 처리량을 최적화 하기 위해 운영 체제 릴리스에서 제공 해야 하는 다양 한 운영 체제 공급 업체의 변경 사항을 기반으로 합니다.
- 서로 다른 VM 유형을 지정 하는 두 개의 테이블이 있습니다. 두 번째 테이블은 Azure standard Storage만 지 원하는 Azure VM 형식에 대 한 SAPS 처리량을 지정 합니다. 메모의 두 번째 테이블에 지정 된 단위에 대 한 DBMS 배포가 지원 되지 않습니다.


## <a name="other-sap-products-supported-on-azure"></a>Azure에서 지원 되는 기타 SAP 제품
일반적으로 Azure와 같은 하이퍼 규모 클라우드의 상태를 사용 하는 경우 SAP 소프트웨어의 대부분은 Azure에서 기능적 문제 없이 실행 되어야 합니다. 그럼에도 불구 하 고 사설 클라우드 시각화와 반대 되는 SAP는 다른 hyerpscale 클라우드 공급자에 대해 여러 SAP 제품에 대 한 지원을 명시적으로 표현 합니다. 따라서 다양 한 SAP 제품에 대 한 Azure 지원을 나타내는 다양 한 SAP 지원 정보가 있습니다. 

비즈니스 개체 BI 플랫폼의 경우 [sap support note #2145537](https://launchpad.support.sap.com/#/notes/2145537) Azure에서 지원 되는 Sap 비즈니스 개체 제품의 목록을 제공 합니다. 나열 되거나 지원 되지 않는 것으로 보이는 구성 요소 또는 소프트웨어 릴리스 및 OS 릴리스 조합에 대 한 질문이 있는 경우, 나열 된 최소 릴리스 보다 최신 버전의 구성 요소에 대 한 질문이 있는 경우 지원 되는 구성 요소에 대 한 SAP 지원 요청을 열어야 합니다.

비즈니스 개체 Data Services의 경우 [sap support note #22288344](https://launchpad.support.sap.com/#/notes/2288344) 는 Azure에서 실행 되는 sap Data Services의 최소 지원을 설명 합니다. 

> [!NOTE]
> SAP 지원 정보에 표시 된 것 처럼 SAP PAM에서 Azure에서 지원 되는 올바른 지원 패키지 수준을 확인 해야 합니다.

AKS (Azure Kubernetes Services)의 SAP Datahub/Vora 지원은 [sap support note](https://launchpad.support.sap.com/#/notes/2464722) 에 자세히 설명 되어 있습니다 #2464722

Sap BPC 10.1 SP08에 대 한 지원은 [sap support note](https://launchpad.support.sap.com/#/notes/2451795) 에 설명 되어 #2451795

Azure의 SAP Hybris Commerce Platform에 대 한 지원은 [Hybris 설명서](https://help.sap.com/viewer/a74589c3a81a4a95bf51d87258c0ab15/1811/en-US/8c71300f866910149b40c88dfc0de431.html)에 자세히 설명 되어 있습니다. SAP Hybris Commerce Platform에 대해 지원 되는 DBMS에 따라 다음과 같이 나열 됩니다.

- Windows 운영 체제 플랫폼의 SQL Server 및 Oracle 동일한 최소 릴리스가 SAP NetWeaver 적용 됩니다. 자세한 내용은 [SAP support note #1928533](https://launchpad.support.sap.com/#/notes/1928533) 를 참조 하세요.
- Red Hat 및 SUSE Linux에서 SAP HANA 합니다. [이 문서](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-supported-product-on-azure#sap-hana-support)앞부분에서 설명한 대로 인증 된 VM 유형 SAP HANA 필요 합니다. SAP (Hybris) 상거래 플랫폼은 OLTP 작업으로 간주 됩니다.
- SQL Azure DB as SAP (Hybris) 상거래 플랫폼 버전 1811




## <a name="next-steps"></a>다음 단계
[SAP NetWeaver에 대 한 Azure Virtual Machines 계획 및 구현](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide) 의 다음 단계를 참조 하십시오.

