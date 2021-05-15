---
title: 'Azure의 SAP: Azure에서 지원되는 SAP 소프트웨어'
description: Azure에 배포하도록 지원되는 SAP 소프트웨어에 대해 설명합니다.
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: SAP
ms.assetid: d7c59cc1-b2d0-4d90-9126-628f9c7a5538
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 12/12/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: dfd560754d92c97b435ff7643f6da4d3e62e8cfd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101673540"
---
# <a name="what-sap-software-is-supported-for-azure-deployments"></a>Azure 배포를 지원하는 SAP 소프트웨어
이 문서에서는 Azure 배포에 지원되는 SAP 소프트웨어 및 필요한 운영 체제 릴리스 또는 DBMS 릴리스가 무엇인지 확인하는 방법에 대해 설명합니다.

현재 SAP 소프트웨어가 지원되는지 여부 및 Azure의 SAP 소프트웨어에서 지원되는 OS와 DBMS 릴리스를 평가하려면 다음에 대한 엑세스 권한이 필요합니다.

- SAP 지원 참고 사항
- SAP 제품 가용성 매트릭스



## <a name="general-restrictions-for-sap-workload"></a>SAP 워크로드에 대한 일반 제한 사항
SAP 워크로드에 사용할 수 있는 Azure IaaS 서비스는 x86-64 또는 x64 하드웨어로 제한됩니다. SAP 워크로드에 적용되는 Sparc 또는 Power CPU 기반 제안은 없습니다. IBM 메인프레임 또는 AS400와 같은 하드웨어 아키텍처 전용 운영 체제에서 애플리케이션을 실행하는 고객 또는 HP UX, Solaris 또는 AIX 운영 체제를 사용하는 고객은 DBMS를 비롯한 SAP 애플리케이션을 다음 운영 체제 중 하나로 변경해야 합니다.

- x86-64 플랫폼용 Windows server 64비트
- x86-64 플랫폼용 SUSE Linux 64비트
- x86-64 플랫폼용 Redhat Linux 64비트
- x86-64 플랫폼용 Oracle Linux 64비트

SAP 소프트웨어와 함께 제공되는 다른 OS 릴리스 또는 Linux 배포는 지원되지 않습니다. 특정 버전 및 사례에 대한 정확한 세부 정보는 이 문서의 뒷부분에 설명되어 있습니다.


## <a name="you-start-here"></a>여기에서 시작하세요
시작 포인트는 [SAP support note #1928533](https://launchpad.support.sap.com/#/notes/1928533)입니다. 이 SAP note를 처음부터 끝까지 살펴보면 지원되는 소프트웨어 및 VM의 여러 영역을 확인할 수 있습니다.

첫 번째 섹션에는 일반적으로 Azure Vm의 SAP 소프트웨어에서 지원되는 운영 릴리스에 대한 최소 요구 사항이 나열되어 있습니다. 이러한 최소 요구 사항에 도달하지 못하고 이러한 운영 체제의 이전 릴리스를 실행하는 경우 OS 릴리스를 최소 릴리스 또는 최신 릴리스로 업그레이드해야 합니다. 일반적으로 Azure는 이러한 운영 체제 중 일부의 이전 릴리스를 지원합니다. 그러나 목록에 있는 제한 또는 최소 릴리스는 실행된 테스트 및 자격을 기반으로 하며 더 이상 다시 확장되지 않습니다. 


> [!NOTE]
>최신 OS 릴리스가 필요한 몇 가지 특정 VM 유형, HANA 대규모 인스턴스 또는 SAP 워크로드가 있습니다. 이와 같은 사례는 문서 전반에 걸쳐 언급됩니다. 이와 같은 사례는 SAP note 또는 다른 SAP 게시에서 명확하게 문서화되어 있습니다.

다음 섹션에는 지원 릴리스가 지원되는 일반 SAP 플랫폼 및 더 중요하게는 지원 SAP 커널이 나열되어 있습니다. 최소 커널 릴리스가 필요한 지원 NetWeaver/ABAP 또한 Java stacks이 나열됩니다. Azure에서 최신 ABAP 스택이 지원되지만, Azure에 대한 변경 사항이 최신 스택 개발 시작부터 구현되었기 때문에 최소 커널 릴리스가 필요하지 않습니다.

다음을 확인하세요:

- 실행하는 SAP 애플리케이션에는 명시된 최소 릴리스가 적용됩니다. 그렇지 않은 경우 새 대상 릴리스를 정의하고, SAP 제품 가용성 매트릭스, 새 대상 릴리스에서 지원되는 운영 체제 빌드 및 DBMS 조합을 확인해야 합니다. 따라서 올바른 운영 체제 릴리스 및 DBMS 릴리스를 선택할 수 있습니다.
- Azure로 이동하며 SAP 커널을 업데이트해야 하는지 여부
- SAP 지원 패키지를 업데이트해야 하는지 여부 특히 최신 DBMS 릴리스로 이동해야 하는 경우 필요한 패키지를 지원합니다.


다음 섹션에서는 Windows 및 Linux 용 Azure의 SAP에서 지원하는 다른 SAP 제품 및 DBMS 릴리스에 대해 자세히 설명합니다. 

> [!NOTE]
> 다른 DBMS의 최소 릴리스는 신중하게 선택되었으며, 일반적으로 다른 DBMS 공급업체가 Azure에서 지원하는 DBMS 릴리스의 전체 스펙트럼을 반영하지 않을 수 있습니다. 이러한 최소 릴리스를 정의하기 위해 많은 SAP 워크로드 관련 고려사항이 고려되었습니다. 이전 DBMS 릴리스를 테스트하고 검증할 필요가 없습니다. 

> [!NOTE]
> 나열된 최소 릴리스는 이전 버전의 운영 체제 및 데이터베이스 릴리스를 나타냅니다. 최신 운영 체제 릴리스 및 데이터베이스 릴리스를 사용하는 것을 권장합니다. 많은 경우 최신 운영 체제 및 데이터베이스 릴리스는 퍼블릭 클라우드의 실행되는 사용 사례를 고려하고 퍼블릭 클라우드 또는 구체적으로 Azure에서 실행하는 데 최적화되도록 코드를 조정했습니다.

## <a name="oracle-dbms-support"></a>Oracle DBMS 지원
Azure에서 지원되는 운영 체제, Oracle DBMS 릴리스 및 Oracle 기능은 [SAP support note #2039619](https://launchpad.support.sap.com/#/notes/2039619)에 구체적으로 나와있습니다. 이 참고 사항의 핵심은 다음과 같이 요약할 수 있습니다.

- NetWeaver용 인증된 Azure VM에서 지원되는 최소 Oracle 릴리스는 Oracle 11g Release 2 Patchset 3 (11.2.0.4)입니다.
- 게스트 운영 체제는 Windows 및 Oracle Linux 한정입니다. OS의 정확한 릴리스 및 관련된 최소 DBMS 릴리스는 다음 참고 사항에 나와있습니다.
- Oracle Linux에 대한 지원은 Oracle DBMS 클라이언트로도 확장됩니다. 즉, ABAP 또는 Java Stack의 대화 상자 인스턴스와 같은 모든 SAP 구성 요소가 Oracle Linux 에서도 실행되어야 합니다. Oracle DBMS에 연결되지 않는 SAP 시스템 내의 SAP 구성 요소만이 다른 Linux 운영 체제를 실행할 수 있습니다.
- Oracle RAC는 지원되지 않습니다. 
- Oracle ASM은 일부 경우에 지원됩니다. 자세한 설명은 아래 참고 사항에 나와있습니다.
- 비 유니코드 SAP 시스템은 Windows 게스트 OS에서 실행되는 애플리케이션 서버에서만 지원됩니다. DBMS의 게스트 운영 체제는 Oracle Linux 또는 Windows일 수 있습니다. 이러한 제한의 이유는 SAP PAM(제품 가용성 매트릭스) 확인 시 나타납니다. Oracle Linux의 경우 SAP에서 유니코드가 아닌 SAP 커널을 릴리스하지 않았습니다.

대상 Azure 인프라에서 지원되는 DBMS 릴리스를 알고 OS 릴리스 및 필수 DBMS가 실행하려는 SAP 제품 릴리스를 지원받는지 여부에 대해 SAP 제품 가용성 메트릭스를 확인해야 합니다. 

## <a name="oracle-linux"></a>Oracle Linux
Oracle Linux에 대한 가장 중요한 질문은 SAP에서 Oracle Linux의 필수 Red Hat 커널이 지원되는지 여부입니다. 자세한 내용은 [SAP support note #1565179](https://launchpad.support.sap.com/#/notes/1565179)를 참조하세요.

## <a name="other-database-than-sap-hana"></a>SAP HANA 이외의 데이터베이스
SAP 워크로드에 대한 비 HANA 데이터베이스 지원은 [SAP support note #1928533](https://launchpad.support.sap.com/#/notes/1928533)에 설명되어 있습니다.


## <a name="sap-hana-support"></a>SAP HANA는 다음을 지원합니다.
Azure에서 HANA 데이터베이스를 실행하는 데 사용할 수 있는 두 가지 서비스가 있습니다.

- Azure Virtual Machines
- [Hana 대규모 인스턴스](./hana-overview-architecture.md)

SAP HANA를 실행하는 경우 SAP는 NetWeaver 또는 다른 SAP 애플리케이션 및 DBMS를 실행하는 경우보다 더 많고 강력한 조건을 충족해야 합니다. 따라서 SAP HANA DBMS 실행에 적합한 Azure VM의 수가 적습니다. SAP HANA에 지원되는 지원 Azure 인프라 목록은 [SAP HANA 하드웨어 디렉터리](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)에서 찾을 수 있습니다. 

> [!NOTE]
> 문자 'S'로 시작하는 단위는 [HANA 대규모 인스턴스](./hana-overview-architecture.md)단위입니다. 

> [!NOTE]
> SAP는 SAP HANA 주 릴리스에 종속된 특정 인증이 없습니다. 일반적인 의견과 달리 [Hana 인증 IaaS 플랫폼](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)의 열 **인증 시나리오** 는 **인증된 HANA 주 릴리스 또는 부 릴리스** 에 대한 문이 없습니다. 특정 단위에 대한 인증 운영 체제 릴리스가 HANA 1.0 릴리스에서 지원되는 한 HANA 1.0 및 HANA 2.0에 사용할 수 있는 모든 단위가 나열되어 있다고 가정해야 합니다. 

SAP HANA의 경우 일반 NetWeaver 사례와 다른 최소 OS 릴리스가 적용될 수 있습니다. 각 단위에 지원되는 운영 체제는 다를 수 있으므로 개별적으로 체크아웃해야 합니다. 각 단위를 클릭하여 이 작업을 수행합니다. 자세한 정보가 표시됩니다. 나열된 세부 정보 중 하나는 이 특정 단위에 지원되는 운영 체제입니다.

> [!NOTE]
> Azure HANA Large Instance 단위는 Azure VM에 비해 지원되는 운영 체제에서 더 제한적입니다. 반면에 Azure VM은 최신 운영 릴리스를 최소 릴리스로 적용할 수 있습니다. 이는 특히 Linux 커널을 변경해야 하는 일부 대규모 VM 단위에 해당됩니다.

Azure 인프라에 대해 지원되는 OS를 알고 있으면 대상으로 하는 Azure 단위에서 지원되는 정확한 SAP HANA 릴리스 및 패치 수준에 대한 [SAP support note #2235581](https://launchpad.support.sap.com/#/notes/2235581)를 확인해야 합니다. 

> [!IMPORTANT]
> SAP HANA의 정확한 릴리스 및 지원되는 패치 수준을 확인하는 단계는 매우 중요합니다. 많은 경우 특정 OS 릴리스 지원은 SAP HANA 실행 파일의 특정 패치 수준에 따라 달라 집니다.

대상 Azure 인프라에서 실행할 수 있는 특정 HANA 릴리스를 알고 있으므로 SAP 제품 가용성 매트릭스를 통해 필터링한 HANA 릴리스를 지원하는 SAP 제품 릴리스에 제한이 있는지 확인해야 합니다.


## <a name="certified-azure-vms-and-hana-large-instance-units-and-business-transaction-throughput"></a>인증된 Azure VM와 HANA 대규모 인스턴스 장치 및 비즈니스 트랜잭션 처리량
지원되는 운영 체제 릴리스, DBMS 릴리스 및 Azure 인프라 장치용 종속된 SAP 소프트웨어 릴리스에 대한 평가 외에도, SAP에서 제공하는 ‘SAP’ 장치의 비즈니스 트랜잭션 처리량으로 이러한 단위를 검증해야 합니다. 모든 SAP 크기 조정은 SAPS 계산에 따라 달라집니다. 기존 SAP 시스템을 평가하면 인프라 공급자의 도움을 받아 장치의 SAPS를 계산할 수 있습니다. 애플리케이션 계층 뿐만 아니라 DBMS 계층. 새 기능이 생성되는 경우 SAP를 사용하는 크기 조정 연습을 통해 애플리케이션 계층 및 DBMS 계층에 필요한 SAPS 수를 표시할 수 있습니다. 인프라 공급자는 Microsoft가 NetWeaver 및/또는 HANA 인증된 다른 장치의 SAP 처리량 특성을 제공해야 합니다.

Azure VM의 경우 이러한 SAPS 처리량 번호는 [SAP support note #1928533](https://launchpad.support.sap.com/#/notes/1928533)에 설명되어 있습니다. Azure HANA 대규모 인스턴스 장치의 경우 SAPS 처리량 숫자는 [SAP support note #2316233](https://launchpad.support.sap.com/#/notes/2316233)에 설명되어 있습니다.

[SAP support note #1928533](https://launchpad.support.sap.com/#/notes/1928533)를 살펴보면 다음과 같은 설명이 적용됩니다.

- **M 시리즈 Azure VM 및 Mv2-Series Azure VM의 경우 다른 AZURE VM 유형과 다른 최소 OS 릴리스가 적용됩니다.** 최신 OS 릴리스에 대한 요구 사항은 특정 Azure VM 유형에서 운영 체제를 실행하거나 해당 VM 유형에서 SAP 워크로드의 성능 및 처리량을 최적화하기 위해 다양한 운영 체제 공급 업체가 운영 체제 릴리스에서 제공해야 했던 변경 사항을 기반으로 합니다.
- 서로 다른 VM 유형을 지정하는 두 개의 테이블이 있습니다. 두 번째 테이블은 Azure Standard Storage만 지원하는 Azure VM 형식에 대한 SAPS 처리량을 지정합니다. 참고 사항의 두 번째 테이블에 지정된 장치에 대한 DBMS 배포는 지원되지 않습니다.


## <a name="other-sap-products-supported-on-azure"></a>Azure에서 지원되는 기타 SAP 제품
일반적으로 Azure와 같은 하이퍼스케일 클라우드 상태를 사용하는 경우 대부분의 SAP 소프트웨어는 Azure에서 기능적 문제 없이 실행되어야 합니다. 그럼에도 불구하고 프라이빗 클라우드 시각화와는 달리 SAP는 다른 하이퍼스케일 클라우드 공급자에 대한 여러 SAP 제품 지원을 명시적으로 표현합니다. 그 결과 다양한 SAP 제품에 대한 Azure 지원을 나타내는 다양한 SAP 지원 참고 사항이 있습니다. 

비즈니스 개체 BI 플랫폼의 경우 [sap support note #2145537](https://launchpad.support.sap.com/#/notes/2145537)는 Azure에서 지원하는 SAP 비즈니스 개체 제품의 목록을 제공합니다. 나열 및 지원되지 않는 것으로 보이는 구성 요소 또는 최소 릴리스 보다 최신 버전의 구성 요소에 대한 질문이 있는 경우 지원을 요청하는 구성 요소에 대해 SAP 지원 요청을 열어야 합니다.

비즈니스 개체 Data Services의 경우 [SAP support note #22288344](https://launchpad.support.sap.com/#/notes/2288344)는 Azure에서 실행되는 SAP Data Services의 최소 지원을 설명합니다. 

> [!NOTE]
> SAP 지원 참고 사항에 표시된 것처럼 SAP PAM을 체크인하여 Azure에서 지원할 올바른 지원 패키지 수준을 확인해야 합니다.

SAP Datahub/Vora support in Azure Kubernetes Services (AKS) is detailed in [SAP support note #2464722](https://launchpad.support.sap.com/#/notes/2464722)

SAP BPC 10.1 SP08에 대한 지원은 [SAP support note #2451795](https://launchpad.support.sap.com/#/notes/2451795)에 설명되어 있습니다.

Azure의 SAP Hybris 상거래 플랫폼에 대한 지원은 [Hybris 설명서](https://help.sap.com/viewer/a74589c3a81a4a95bf51d87258c0ab15/1811/en-US/8c71300f866910149b40c88dfc0de431.html)에 자세히 설명되어 있습니다. SAP Hybris 상거래 설명서용 지원되는 DBMS는 다음과 같이 나열됩니다.

- Windows 운영 체제 플랫폼의 SQL Server 및 Oracle 동일한 최소 릴리스가 SAP NetWeaver에 적용됩니다. 자세한 내용은 [SAP support note #1928533](https://launchpad.support.sap.com/#/notes/1928533)를 참조하세요.
- Red Hat 및 SUSE Linux의 SAP HANA SAP HANA 인증 VM 유형은 [이 문서](#sap-hana-support) 앞부분에서 설명한 대로 필요합니다. SAP (Hybris) 상거래 플랫폼은 OLTP 워크로드로 간주됩니다.
- SAP (Hybris) 상거래 플랫폼 버전 1811의 SQL Azure DB




## <a name="next-steps"></a>다음 단계
[SAP NetWeaver에 대한 Azure Virtual Machines 계획 및 구현](./planning-guide.md)에서 다음 단계 읽기
