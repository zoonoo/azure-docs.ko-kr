---
title: 'Azure에서 SAP: Azure에서 지원되는 SAP 소프트웨어'
description: Azure에서 배포할 수 있도록 지원되는 SAP 소프트웨어에 대해 설명합니다.
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
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2020
ms.locfileid: "82086133"
---
# <a name="what-sap-software-is-supported-for-azure-deployments"></a>Azure 배포에 지원되는 SAP 소프트웨어
이 문서에서는 Azure 배포에 지원되는 SAP 소프트웨어와 필요한 운영 체제 릴리스 또는 DBMS 릴리스에 대해 설명하는 방법을 설명합니다.

현재 SAP 소프트웨어가 지원되는지 여부와 Azure의 SAP 소프트웨어에서 지원되는 OS 및 DBMS 릴리스를 평가하면 다음에 액세스해야 합니다.

- SAP 지원 노트
- SAP 제품 가용성 매트릭스



## <a name="general-restrictions-for-sap-workload"></a>SAP 워크로드에 대한 일반 제한 사항
SAP 워크로드에 사용할 수 있는 Azure IaaS 서비스는 x86-64 또는 x64 하드웨어로 제한됩니다. SAP 워크로드에 적용되는 Sparc 또는 Power CPU 기반 오퍼가 없습니다. IBM 메인프레임 또는 AS400과 같은 하드웨어 아키텍처를 독점하는 운영 체제에서 응용 프로그램을 실행하거나 HP-UX, Solaris 또는 AIX를 사용 중인 고객은 DBMS를 포함한 SAP 응용 프로그램을 다음 운영 체제 중 하나로 변경해야 합니다.

- x86-64 플랫폼을 위한 윈도우 서버 64비트
- x86-64 플랫폼을위한 SUSE 리눅스 64비트
- x86-64 플랫폼에 대 한 레드 햇 리눅스 64비트
- x86-64 플랫폼을 위한 오라클 리눅스 64비트

SAP 소프트웨어와 함께 다른 OS 릴리스 또는 Linux 배포판은 지원되지 않습니다. 특정 버전 및 서비스 케이스에 대한 정확한 세부 정보는 문서의 후반부에서 설명합니다.


## <a name="you-start-here"></a>여기에서 시작합니다.
당신을 위한 출발점은 [SAP 지원 노트 #1928533.](https://launchpad.support.sap.com/#/notes/1928533) 이 SAP 노트를 위에서 아래로 살펴보면 지원되는 소프트웨어 및 VM의 여러 영역이 표시됩니다.

첫 번째 섹션에는 일반적으로 Azure VM에서 SAP 소프트웨어로 지원되는 운영 릴리스에 대한 최소 요구 사항이 나열되어 있습니다. 이러한 최소 요구 사항에 도달하지 않고 이러한 운영 체제의 이전 릴리스를 실행하지 않는 경우 OS 릴리스를 최소 릴리스 또는 최신 릴리스로 업그레이드해야 합니다. 일반적으로 Azure는 이러한 운영 체제 중 일부의 이전 릴리스를 지원하는 것이 맞습니다. 그러나 나열된 제한 또는 최소 릴리스는 실행된 테스트 및 자격에 따라 지이며 더 이상 확장되지 않을 것입니다. 


> [!NOTE]
>최신 OS 릴리스가 필요한 몇 가지 특정 VM 유형, HANA 대형 인스턴스 또는 SAP 워크로드가 있습니다. 이와 같은 사례는 문서 전체에서 언급됩니다. 이와 같은 사례는 SAP 노트 또는 기타 SAP 간행물에 명확하게 문서화되어 있습니다.

다음 섹션에는 지원되는 릴리스와 함께 지원되는 일반적인 SAP 플랫폼과 지원되는 SAP 커널이 더 중요합니다. 최소 커널 릴리스가 필요한 NetWeaver/ABAP 또는 Java 스택이 지원되고 있습니다. 최신 ABAP 스택은 Azure에서 지원되지만 Azure에 대한 변경 사항이 최신 스택 개발 시작부터 구현되었기 때문에 최소 커널 릴리스가 필요하지 않습니다.

다음을 확인해야 합니다.

- 실행 중인 SAP 응용 프로그램이 명시된 최소 릴리스의 적용을 받는지 여부입니다. 그렇지 않은 경우 새 대상 릴리스를 정의하고, SAP 제품 가용성 매트릭스를 체크 인해야 하며, 새 대상 릴리스에서 어떤 운영 체제 빌드 및 DBMS 조합이 지원되는지 확인해야 합니다. 따라서 올바른 운영 체제 릴리스 및 DBMS 릴리스를 선택할 수 있습니다.
- Azure로 이동하여 SAP 커널을 업데이트해야 하는지 여부
- SAP 지원 패키지를 업데이트해야 하는지 여부입니다. 특히 최신 DBMS 릴리스로 이동해야 하는 경우에 필요할 수 있는 기본 지원 패키지


다음 섹션에서는 Windows 및 Linux용 Azure에서 SAP에서 지원하는 다른 SAP 제품 및 DBMS 릴리스에 대해 자세히 설명합니다. 

> [!NOTE]
> 다른 DBMS의 최소 릴리스는 신중하게 선택되며 일반적으로 Azure에서 서로 다른 DBMS 공급업체가 지원하는 DBMS 릴리스의 전체 스펙트럼을 항상 반영하지 않을 수 있습니다. 이러한 최소 릴리스를 정의하기 위해 많은 SAP 워크로드 관련 고려 사항이 고려되었습니다. 이전 DBMS 릴리스를 테스트하고 검증하는 방법은 없습니다. 

> [!NOTE]
> 나열된 최소 릴리스는 이전 버전의 운영 체제 및 데이터베이스 릴리스를 나타냅니다. 가장 최근의 운영 체제 릴리스 및 데이터베이스 릴리스를 사용하는 것이 좋습니다. 많은 경우, 최신 운영 체제 및 데이터베이스 릴리스는 퍼블릭 클라우드에서 실행되는 사용 사례를 고려하여 퍼블릭 클라우드 또는 보다 구체적으로 Azure에서 실행하도록 최적화하기 위해 조정된 코드를 적용했습니다.

## <a name="oracle-dbms-support"></a>오라클 DBMS 지원
운영 체제, 오라클 DBMS 릴리스 및 Azure에서 지원되는 오라클 기능은 [#2039619 SAP 지원 노트에](https://launchpad.support.sap.com/#/notes/2039619)특별히 나열되어 있습니다. 그 노트의 본질은 다음과 같이 요약 될 수있다 :

- NetWeaver 인증을 받은 Azure VM에서 지원되는 최소 오라클 릴리스는 오라클 11g 릴리스 2 패치셋 3(11.2.0.4)입니다.
- 게스트 운영 체제로 만 윈도우와 오라클 리눅스 자격. OS 및 관련 최소 DBMS 릴리스의 정확한 릴리스가 메모에 나열되어 있습니다.
- 오라클 리눅스의 지원은 오라클 DBMS 클라이언트에도 확장됩니다. 즉, ABAP 또는 Java 스택의 대화 상자 인스턴스와 같은 모든 SAP 구성 요소도 Oracle Linux에서 실행해야 합니다. Oracle DBMS에 연결하지 않는 SAP 시스템 내의 SAP 구성 요소만 다른 Linux 운영 체제를 실행할 수 있습니다.
- 오라클 RAC가 지원되지 않음 
- 오라클 ASM은 일부 사례에 대해 지원됩니다. 세부 사항은 메모에 나열되어 있습니다.
- 유니코드가 아닌 SAP 시스템은 Windows 게스트 OS에서 실행되는 응용 프로그램 서버에서만 지원됩니다. DBMS의 게스트 운영 체제는 오라클 리눅스 또는 윈도우 가 될 수 있습니다. 이러한 제한에 대한 이유는 SAP 제품 가용성 매트릭스(PAM)를 확인할 때 명백합니다. 오라클 리눅스의 경우, SAP는 비 유니코드 SAP 커널을 출시하지 않았습니다.

대상 Azure 인프라에서 지원되는 DBMS 릴리스를 알면 실행하려는 SAP 제품 릴리스와 필요한 DBMS가 지원되는지 여부에 대해 SAP 제품 가용성 매트릭스를 확인해야 합니다. 


## <a name="sap-hana-support"></a>SAP 하나 지원
Azure에는 HANA 데이터베이스를 실행하는 데 사용할 수 있는 두 가지 서비스가 있습니다.

- Azure Virtual Machines
- [HANA 대형 인스턴스](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)

SAP HANA를 실행하기 위해 SAP는 NetWeaver 또는 기타 SAP 응용 프로그램 및 DBMS를 실행하는 것보다 인프라가 충족해야 하는 조건이 점점 더 강합니다. 따라서 적은 수의 Azure VM이 SAP HANA DBMs를 실행할 수 있습니다. SAP HANA에 지원되는 지원되는 Azure 인프라 목록은 소위 [SAP HANA 하드웨어 디렉토리에서](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)찾을 수 있습니다. 

> [!NOTE]
> 문자 'S'로 시작하는 단위는 [HANA 대형 인스턴스](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) 단위입니다. 

> [!NOTE]
> SAP는 SAP HANA 주요 릴리스에 종속된 특정 인증이 없습니다. 일반적인 의견과는 달리, [HANA 인증 IaaS 플랫폼의](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)열 **인증 시나리오는** **HANA 주요 또는 부버전 인증에 대한 진술을 하지 않습니다.** 특정 장치에 대한 인증된 운영 체제 릴리스가 HANA 1.0 릴리스에서도 지원되는 한 HANA 1.0 및 HANA 2.0에 사용할 수 있는 모든 장치를 가정해야 합니다. 

SAP HANA의 사용에 대 한, 다른 최소 OS 릴리스 일반 NetWeaver 의 경우 보다 적용 될 수 있습니다. 각 장치에 대해 지원되는 운영 체제는 다를 수 있으므로 개별적으로 체크 아웃해야 합니다. 각 단위를 클릭하여 이렇게 합니다. 자세한 내용이 표시됩니다. 나열된 세부 정보 중 하나는 이 특정 장치에 대해 지원되는 다른 운영 체제입니다.

> [!NOTE]
> Azure HANA 대형 인스턴스 단위는 Azure VM에 비해 지원되는 운영 체제에서 더 제한적입니다. 반면에 Azure VM은 최신 운영 릴리스를 최소 릴리스로 적용할 수 있습니다. 이는 Linux 커널을 변경해야 하는 일부 대형 VM 장치에 특히 해당됩니다.

Azure 인프라에 대해 지원되는 OS를 알고 있으므로 대상을 대상으로 하는 Azure 단위에서 지원되는 정확한 SAP HANA 릴리스 및 패치 수준에 대한 [SAP 지원 참고 #2235581](https://launchpad.support.sap.com/#/notes/2235581) 확인해야 합니다. 

> [!IMPORTANT]
> 지원되는 정확한 SAP HANA 릴리스 및 패치 수준을 확인하는 단계는 매우 중요합니다. 많은 경우에 특정 OS 릴리스의 지원은 SAP HANA 실행 서비스의 특정 패치 수준에 따라 달라집니다.

대상 Azure 인프라에서 실행할 수 있는 특정 HANA 릴리스를 알고 있으므로 SAP 제품 가용성 매트릭스를 확인하여 필터링한 HANA 릴리스를 지원하는 SAP 제품 릴리스에 제한이 있는지 확인해야 합니다.


## <a name="certified-azure-vms-and-hana-large-instance-units-and-business-transaction-throughput"></a>인증된 Azure VM 및 HANA 대형 인스턴스 단위 및 비즈니스 트랜잭션 처리량
지원되는 운영 체제 릴리스, DBMS 릴리스 및 Azure 인프라 단위에 대한 종속 지원 SAP 소프트웨어 릴리스를 평가하는 것 외에도 SAP에서 'SAP' 단위로 표현되는 비즈니스 트랜잭션 처리량별로 이러한 단위를 한정할 필요가 있습니다. 모든 SAP 크기 조정은 SAPS 계산에 따라 다릅니다. 기존 SAP 시스템을 평가하면 일반적으로 인프라 공급자의 도움을 받아 단위의 SAPS를 계산할 수 있습니다. 응용 프로그램 계층뿐만 아니라 DBMS 계층에 대한. 새 기능이 만들어지는 다른 경우 SAP를 사용 하 여 크기 조정 연습 응용 프로그램 계층 및 DBMS 계층에 필요한 SAPS 번호를 나타낼 수 있습니다. 인프라 공급자인 Microsoft는 NetWeaver 및/또는 HANA 인증을 받은 다른 단위의 SAP 처리량 특성화를 제공해야 합니다.

Azure VM의 경우 이러한 SAPS 처리량 번호는 [#1928533 SAP 지원 노트에](https://launchpad.support.sap.com/#/notes/1928533)문서화되어 있습니다. Azure HANA 대형 인스턴스 단위의 경우 SAPS 처리량 번호는 [SAP 지원 노트 #2316233](https://launchpad.support.sap.com/#/notes/2316233)

#1928533 [SAP 지원 노트를](https://launchpad.support.sap.com/#/notes/1928533)살펴보면 다음 설명이 적용됩니다.

- **M-시리즈 Azure VM 및 Mv2 시리즈 Azure VM의 경우 다른 Azure VM 유형과 다른 최소 OS 릴리스가 적용됩니다.** 최신 OS 릴리스에 대한 요구 사항은 다른 운영 체제 공급업체가 운영 체제 릴리스에서 특정 Azure VM 유형에서 실행 중인 운영 체제를 사용하도록 설정하거나 해당 VM 유형에 대한 SAP 워크로드의 성능 및 처리량을 최적화하기 위해 제공해야 하는 변경 사항을 기반으로 합니다.
- 다른 VM 형식을 지정하는 두 개의 테이블이 있습니다. 두 번째 표는 Azure 표준 저장소만 지원하는 Azure VM 형식에 대한 SAPS 처리량을 지정합니다. 메모의 두 번째 테이블에 지정된 단위에 DBMS 배포가 지원되지 않습니다.


## <a name="other-sap-products-supported-on-azure"></a>Azure에서 지원되는 기타 SAP 제품
일반적으로 Azure와 같은 하이퍼스케일 클라우드 의 상태는 대부분의 SAP 소프트웨어가 Azure에서 기능적인 문제 없이 실행되어야 한다고 가정합니다. 그럼에도 불구하고 SAP는 프라이빗 클라우드 시각화와 는 반대로 다른 hyerpscale 클라우드 공급자를 위해 명시적으로 다양한 SAP 제품에 대한 지원을 표현하고 있습니다. 따라서 다른 SAP 제품에 대한 Azure 지원을 나타내는 다른 SAP 지원 메모가 있습니다. 

비즈니스 개체 BI 플랫폼의 경우 [SAP 지원 #2145537](https://launchpad.support.sap.com/#/notes/2145537) Azure에서 지원되는 SAP 비즈니스 개체 제품 목록을 제공합니다. 나열되거나 지원되지 않는 것 같고 나열된 최소 릴리스보다 최신인 소프트웨어 릴리스 및 OS 릴리스의 구성 요소 또는 조합에 대한 질문이 있는 경우 지원을 요청하는 구성 요소에 대해 SAP 지원 요청을 열어야 합니다.

비즈니스 개체 데이터 서비스의 경우 [SAP 지원 #22288344](https://launchpad.support.sap.com/#/notes/2288344) Azure에서 실행되는 SAP 데이터 서비스의 최소 지원을 설명합니다. 

> [!NOTE]
> SAP 지원 노트에 표시된 대로 SAP PAM을 체크 인하여 Azure에서 지원할 올바른 지원 패키지 수준을 확인해야 합니다.

Azure Kubernetes 서비스(AKS)의 SAP Datahub/Vora 지원은 [SAP 지원 노트 #2464722](https://launchpad.support.sap.com/#/notes/2464722) 자세히 설명합니다.

SAP BPC 10.1 SP08에 대한 지원은 [#2451795 SAP 지원 노트에](https://launchpad.support.sap.com/#/notes/2451795) 설명되어 있습니다.

Azure의 SAP Hybris 상거래 플랫폼에 대한 지원은 [Hybris 설명서에서](https://help.sap.com/viewer/a74589c3a81a4a95bf51d87258c0ab15/1811/en-US/8c71300f866910149b40c88dfc0de431.html)자세히 설명합니다. SAP Hybris 상거래 플랫폼에 지원되는 DBMS를 사용하면 다음과 같은 목록이 나열됩니다.

- Windows 운영 체제 플랫폼에서 SQL 서버 및 오라클. SAP NetWeaver와 동일한 최소 릴리스가 적용됩니다. 자세한 내용은 [SAP 지원 #1928533](https://launchpad.support.sap.com/#/notes/1928533) 참조
- 레드 햇과 수즈 리눅스에 SAP HANA. SAP HANA 인증 VM 유형은 [이 문서의](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-supported-product-on-azure#sap-hana-support)앞에서 설명한 대로 필요합니다. SAP(하이브리스) 커머스 플랫폼은 OLTP 워크로드로 간주됩니다.
- Sql Azure DB SAP(하이브리스) 상거래 플랫폼 버전 1811




## <a name="next-steps"></a>다음 단계
[SAP NetWeaver에 대한 Azure 가상 시스템 계획 및 구현의](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide) 다음 단계를 읽어보십시오.

