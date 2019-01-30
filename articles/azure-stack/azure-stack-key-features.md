---
title: Azure Stack의 기능 및 개념 키 | Microsoft Docs
description: Azure Stack의 주요 기능 및 개념에 대해 알아봅니다.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: 09ca32b7-0e81-4a27-a6cc-0ba90441d097
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/14/2019
ms.author: jeffgilb
ms.reviewer: unknown
ms.lastreviewed: 01/14/2019
ms.openlocfilehash: b07d8b115b966b9decdfa7379a908da4f9f2ee74
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/30/2019
ms.locfileid: "55243912"
---
# <a name="key-features-and-concepts-in-azure-stack"></a>Azure Stack의 주요 기능 및 개념
Microsoft Azure Stack을 처음 접하는 경우 이러한 용어와 기능 설명은 도움이 될 수 있습니다.

## <a name="personas"></a>가상 사용자
Microsoft Azure Stack, 연산자 및 사용자에 대 한 사용자가 다양 한 두 가지 있습니다.

* Azure Stack **연산자** 제안, 계획, 서비스, 할당량 관리 및 가격 책정을 사용자 테 넌 트에 대 한 리소스를 제공 하 여 Azure Stack을 구성할 수 있습니다. 연산자는 또한 용량을 관리 하 고 경고에 응답 합니다.  
* Azure Stack **사용자** (테 넌 트 라고도 함) 연산자를 제공 하는 서비스를 사용 합니다. 사용자 프로 비전, 모니터링 및 서비스는 구독에 웹 앱, 저장소 및 가상 머신과 같은 관리할 수 있습니다.

## <a name="portal"></a>포털
Microsoft Azure Stack과 상호 작용의 기본 메서드에서 관리 포털, 사용자 포털 및 PowerShell 됩니다.

Azure Stack 포털은 각각 별도 Azure Resource Manager의 인스턴스에서 지원 됩니다. 운영자는 Azure Stack을 관리 하 고 제품 테 넌 트를 만들어야 하는 등 작업을 수행 하는 관리 포털을 사용 합니다. 가상 머신, 저장소 계정 및 web apps와 같은 클라우드 리소스의 소비에 대 한 셀프 서비스 환경을 제공 하는 사용자 포털 (테 넌 트 포털 라고도 함). 자세한 내용은 [Azure Stack 관리자 및 사용자 포털을 사용 하 여](azure-stack-manage-portals.md)입니다.

## <a name="identity"></a>ID 
Azure Stack을 id 공급자로 Azure Active Directory (Azure AD) 또는 Active Directory Federation Services (AD FS)를 사용합니다.  

### <a name="azure-active-directory"></a>Azure Active Directory
Azure AD는 Microsoft의 클라우드 기반, 다중 테 넌 트 id 공급자입니다. 대부분의 하이브리드 id 저장소로 Azure AD를 사용합니다.

### <a name="active-directory-federation-services"></a>Active Directory Federation Services
Active Directory Federation Services (AD FS) Azure Stack의 연결이 끊긴된 배포에 사용 하도록 선택할 수 있습니다. Azure Stack 리소스 공급자 및 기타 응용 프로그램을 Azure AD와 마찬가지로 AD FS 사용 하 여 비슷하게를 작동 합니다. Azure Stack에 자체 Active Directory 인스턴스 및 Active Directory Graph API를 포함합니다. Azure Stack 개발 키트에는 다음 AD FS 시나리오를 지원합니다.

- AD FS를 사용 하 여 배포에 로그인 합니다.
- Key Vault의 암호를 사용 하 여 가상 머신 만들기
- 암호 저장/액세스에 대 한 자격 증명 모음 만들기
- 구독에 사용자 지정 RBAC 역할 만들기
- 리소스에 대 한 RBAC 역할에 사용자 할당
- Azure PowerShell을 통해 시스템 차원의 RBAC 역할 만들기
- Azure PowerShell을 통해 사용자로 로그인
- 서비스를 만들려면 보안 주체 사용 하 여 Azure PowerShell에 로그인


## <a name="regions-services-plans-offers-and-subscriptions"></a>지역, 서비스, 계획, 제품 및 구독
Azure Stack에서 서비스는 지역, 구독, 제품 및 계획을 사용 하 여 테 넌 트에 전달 됩니다. 테넌트는 여러 제품을 구독할 수 있습니다. 제안에는 하나 이상의 계획이 있을 수 있으며 계획에는 하나 이상의 서비스가 있을 수 있습니다.

![](media/azure-stack-key-features/image4.png)

테넌트의 구독인 예제 계층은 각각 다양한 계획 및 서비스를 제안합니다.

### <a name="regions"></a>영역
Azure Stack 지역 크기 조정 및 관리의 기본 요소 이며 조직에는 각 지역에서 사용할 수 있는 리소스를 사용 하 여 여러 지역 있을 수 있습니다. 지역에서 사용할 수 있는 다른 서비스 제품을 있을 수도 있습니다. Azure Stack Development Kit를 단일 지역만 지원에 자동으로 이름이 지정 *로컬*합니다.

### <a name="services"></a>Services
Microsoft Azure Stack에는 다양 한 서비스 및 virtual machines, SQL Server 데이터베이스, SharePoint, Exchange 및 기타 등의 응용 프로그램을 제공 하는 공급자 수 있습니다.

### <a name="plans"></a>요금제
계획은 하나 이상의 서비스가 모인 그룹입니다. 공급자로서 테넌트에게 제공할 계획을 만듭니다. 차례로 테넌트는 제안을 구독하여 포함된 계획 및 서비스를 사용합니다.

클라우드 용량을 관리할 수 있도록 할당량 설정을 사용하여 계획에 추가된 각 서비스를 구성할 수 있습니다. 할당량은 VM, RAM 및 CPU 제한과 같은 제한 사항을 포함할 수 있으며 사용자 구독에 대해 적용됩니다. 할당량은 위치로 식별될 수 있습니다. 예를 들어 지역 A에서에서 계산 서비스를 포함 하는 계획에 두 개의 가상 머신, 4GB RAM 및 10 개의 CPU 코어 할당량이 있을 수 있습니다.

제품을 만들면, 서비스 관리자 포함 될 수 있습니다는 **기본 계획**합니다. 이러한 기본 계획은 테넌트가 해당 제안을 구독할 때 기본적으로 포함됩니다. 사용자가 구독 (및 구독을 만들) 때 사용자 (사용 하 여 해당 할당량) 해당 기본 계획에 지정 된 모든 리소스 공급자에 액세스할 수 있습니다.

서비스 관리자는 제안에 **추가 기능 계획**을 포함할 수 있습니다. 추가 기능 계획은 구독에서 기본적으로 포함되지 않습니다. 추가 기능 계획은 추가 계획 (할당량) 구독 소유자는 해당 구독에 추가할 수 있는 제품에서 사용할 수 있습니다.

### <a name="offers"></a>제품
제안은 공급자가 구매(구독)할 테넌트에 제공하는 하나 이상의 계획으로 이루어진 그룹입니다. 제품 a는 계획을 포함할 수 있습니다 예를 들어, 계산 서비스와 저장소 및 네트워크 서비스의 집합을 포함 하는 계획 B의 집합을 포함 합니다.

제안은 기본 계획의 집합과 함께 제공되고 서비스 관리자는 테넌트가 해당 구독에 추가할 수 있는 추가 기능 계획을 만들 수 있습니다.

### <a name="subscriptions"></a>구독
구독은 테넌트가 제안을 구입하는 방법입니다. 구독은 제안과 테넌트의 조합입니다. 테넌트에는 여러 제안에 대한 구독이 있을 수 있습니다. 각 구독은 하나의 제안에 적용됩니다. 테넌트의 구독은 액세스할 수 있는 계획/서비스를 결정합니다.

구독에 공급자를 구성 하 고 클라우드 리소스 및 서비스에 액세스할 수 있습니다.

관리자에 대 한 기본 공급자 구독을 배포 하는 동안 만들어집니다. 이 구독은 Azure Stack 관리, 배포 리소스 공급자를 추가, 테 넌 트에 대 한 계획 및 제품은 만들기를 사용할 수 있습니다. 고객 워크 로드 및 응용 프로그램을 실행 하려면 미사용 해야 합니다. 두 개의 추가 구독 보완 1804 버전부터, 기본 공급자 구독 계량 구독 및 사용량 구독 합니다. 이러한 추가 코어 인프라, 추가 리소스 공급자 및 워크 로드 관리를 분리를 지원 합니다.  

## <a name="azure-resource-manager"></a>Azure 리소스 관리자
Azure Resource Manager를 사용 하 여 템플릿에 기반 하 고 선언적인 모델에서 인프라 리소스를 사용 하 여 작업할 수 있습니다.   배포 및 관리 솔루션 구성 요소를 사용할 수 있는 단일 인터페이스를 제공 합니다. 전체 정보 및 지침은 [Azure 리소스 관리자 개요](../azure-resource-manager/resource-group-overview.md)를 참조하세요.

### <a name="resource-groups"></a>리소스 그룹
리소스 그룹은 컬렉션인 리소스, 서비스 및 응용 프로그램-각 리소스에 가상 머신, 가상 네트워크, 공용 Ip, 저장소 계정 및 웹 사이트와 같은 형식이 고 합니다. 각 리소스는 리소스 그룹에 포함 될 하 고 리소스 그룹을 통해 논리적으로 하므로 리소스를 구성 같은 워크 로드 또는 위치 해야 합니다. Azure Stack에서 계획 및 제품과 같은 리소스는 리소스 그룹에도 관리 됩니다.

와 달리 [Azure](../azure-resource-manager/resource-group-move-resources.md), 리소스 그룹 간에 Azure Stack 리소스를 이동할 수 없습니다. Azure Stack 관리 포털에서 리소스 또는 리소스 그룹의 속성을 볼 때 합니다 *이동* 단추는 흐리게 표시를 사용할 수 없습니다. 또한 사용 합니다 **리소스 그룹 변경** 또는 **구독을 변경할** 작업 리소스 그룹 또는 리소스 그룹 항목 속성에서 지원 되지 않습니다. 시도한 모든 이동 작업이 실패 합니다.
 
### <a name="azure-resource-manager-templates"></a>Azure 리소스 관리자 템플릿
Azure Resource Manager로 응용 프로그램의 배포 및 구성을 정의 하는 JSON 형식으로 템플릿을 만들 수 있습니다. 이 템플릿은 Azure 리소스 관리자 템플릿이며 배포를 정의하는 선언적 방법을 제공합니다. 서식 파일을 사용하여 수명 주기 내내 애플리케이션을 반복적으로 배포하며 안심하고 일관된 상태로 리소스가 배포할 수 있습니다.

## <a name="resource-providers-rps"></a>리소스 공급자 (Rp)
리소스 공급자는 모든 Azure 기반 IaaS및 PaaS 서비스에 대한 기초를 형성하는 웹 서비스입니다. Azure Resource Manager 서비스에 대 한 액세스를 제공 하는 다양 한 Rp에 의존 합니다.

기본 4 개 Rp 가지가 있습니다. 네트워크, 저장소, 계산 및 KeyVault 합니다. 각각의 이러한 RP는 해당하는 리소스를 구성하고 제어하도록 돕습니다. 서비스 관리자는 새 사용자 지정 리소스 공급자를 추가할 수도 있습니다.

### <a name="compute-rp"></a>Compute RP
CRP(Compute 리소스 공급자)를 사용하면 Azure Stack 테넌트가 고유의 가상 머신을 만들 수 있습니다. CRP는 Virtual Machine을 확장할 뿐만 아니라 가상 머신을 만드는 기능을 포함합니다. Virtual Machine 확장 서비스는 Windows 및 Linux 가상 머신에 IaaS 기능을 제공합니다.  예를 들어 Linux 가상 머신 프로 비전 배포 VM을 구성 하는 동안 Bash 스크립트를 실행 하는 CRP를 사용할 수 있습니다.

### <a name="network-rp"></a>네트워크 RP
NRP(네트워크 리소스 공급자)는 사설 클라우드를 위한 일련의 SDN (소프트웨어 정의된 네트워킹) 및 NFV(네트워크 기능 가상화) 기능을 제공합니다.  소프트웨어 부하 분산 장치 공용 Ip, 네트워크 보안 그룹, 가상 네트워크와 같은 리소스를 만들려면 NRP를 사용할 수 있습니다.

### <a name="storage-rp"></a>저장소 RP
저장소 RP는 네 가지 일관 된 Azure 저장소 서비스를 제공 합니다: blob, 테이블, 큐 및 계정 관리 합니다. 또한 스토리지 클라우드 관리 서비스를 제공하여 Azure에 일관된 Storage 서비스의 서비스 공급자 관리를 용이하게 합니다. Azure Storage는 Azure Blob가 있는 문서 및 미디어 파일, Azure 테이블이 있는 구조화된 NoSQL 기반 데이터 등 구조화되지 않은 많은 양의 데이터를 저장하고 검색하도록 유연성을 제공합니다. Azure Storage에 대 한 자세한 내용은 참조 하세요. [Microsoft Azure Storage 소개](../storage/common/storage-introduction.md)합니다.

#### <a name="blob-storage"></a>Blob 저장소
Blob 저장소는 데이터 집합을 저장합니다. Blob은 문서, 미디어 파일 또는 애플리케이션 설치 프로그램 등 모든 종류의 텍스트 또는 이진 데이터일 수 있습니다. 테이블 저장소는 구조화된 데이터 세트를 저장합니다. 테이블 저장소는 신속한 개발과 대량 데이터에 대한 빠른 액세스를 가능하게 하는 NoSQL 키-특성 데이터 저장소입니다. 큐 저장소는 워크플로 처리 및 클라우드 서비스 구성 요소 사이의 통신을 위한 안정적인 메시징을 제공합니다.

모든 blob 컨테이너에서 구성 됩니다. 컨테이너를 통해 유용하게 개체 그룹에 보안 정책을 할당할 수도 있습니다. 저장소 계정 컨테이너를 개수에 관계 없이 포함할 수 있습니다 하 고 컨테이너에 blob 저장소 계정의 500 TB 용량 제한까지 개수에 관계 없이 포함 될 수 있습니다. Blob 저장소에는 블록 Blob, 추가 Blob 및 페이지 Blob(디스크)의 세 가지 Blob 유형이 있습니다. 블록 Blob은 클라우드 개체 스트리밍 및 저장을 위해 최적화되며 문서, 미디어 파일, 백업 등을 저장하는 데 적합합니다. 추가 Blob은 블록 Blob과 유사하지만 추가 작업에 최적화되어 있습니다. 새 블록을 끝에 추가해야만 추가 Blob을 업데이트할 수 있습니다. 추가 Blob은 Blob 끝에만 새 데이터를 써야 하는 로깅과 같은 시나리오에 적합합니다. 페이지 blob은 IaaS 디스크를 나타내기 위해 최적화 되어 및 지원 임의 쓰기를 최대 1TB 일 수 있습니다. Azure 가상 머신 네트워크에 추가된 IaaS 디스크는 페이지 Blob으로 저장된 VHD입니다.

#### <a name="table-storage"></a>테이블 저장소
Table storage는 Microsoft의 NoSQL 키/특성 저장소 – 쉽게 전통적인 관계형 데이터베이스에서 다른 스키마 없이 디자인 되어 있습니다. 데이터 부족 스키마에 저장 되므로 응용 프로그램 진화에 필요에 따라 데이터에 맞게 쉽습니다. 테이블 저장소는 쉽게 사용할 수 있어, 개발자가 신속하게 애플리케이션을 만들 수 있습니다. 테이블 저장소는 키-특성 저장소입니다. 다시 말해서, 테이블의 모든 값이 입력된 속성 이름을 사용하여 저장됩니다. 이 속성 이름은 선택 조건을 필터링하고 지정하는 데 사용할 수 있습니다. 속성 모음과 해당 값은 함께 엔터티를 구성합니다. 테이블 저장소 부족 스키마 이후 두 엔터티가 같은 테이블의 여러 컬렉션 속성에 포함 될 수 있습니다 하 고 이러한 속성은 다른 유형일 수 있습니다. 테이블 저장소를 사용하여 웹 응용 프로그램의 사용자 데이터, 주소록, 장치 정보 및 서비스에 필요한 다른 유형의 메타데이터와 같은 유연한 데이터 세트를 저장할 수 있습니다. 테이블에 저장할 수 있는 엔터티 수에는 제한이 없으며, 저장소 계정에 포함할 수 있는 테이블의 수에는 저장소 계정의 최대 용량 한도까지 제한이 없습니다.

#### <a name="queue-storage"></a>Queue Storage
Azure Queue storage는 애플리케이션 구성 요소 간에 클라우드 메시징을 제공합니다. 규모를 고려하여 애플리케이션을 디자인할 때는 애플리케이션 구성 요소를 개별적으로 확장할 수 있도록 각 구성 요소를 분리하는 경우가 많습니다. 큐 저장소는 클라우드, 데스크톱, 온-프레미스 서버 또는 모바일 디바이스에서 실행 중인지와 관계 없이 응용 프로그램 구성 요소 간에 통신을 위한 비동기 메시징을 제공합니다. 큐 저장소는 또한 비동기 작업 관리와 프로세스 워크플로 작성을 지원합니다.

### <a name="keyvault"></a>KeyVault
KeyVault RP 관리 및 암호, 암호, 인증서 등의 감사를 제공 합니다. 예를 들어 테 넌 트 VM 배포 중 관리자 암호 또는 키를 제공 하는 KeyVault RP를 사용할 수 있습니다.

## <a name="high-availability-for-azure-stack"></a>Azure Stack에 대 한 고가용성
Azure에서 다중 VM 프로덕션 시스템의 고가용성을 달성 하려면 Vm에 배치 되는 [가용성 집합](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy) 여러 장애 도메인과 업데이트 도메인 간에 분산 하는 합니다. Azure Stack의 소규모 배율 단위의 단일 노드로 가용성 집합의 장애 도메인 정의 됩니다.  

Azure stack 인프라 장애에 복원 력이 이미 상태인 (장애 조치 클러스터링) 기술의 여전히 일부 가동 중지 시간이 발생 Vm에 대 한 영향을 받는 실제 서버의 하드웨어 오류가 발생 하는 경우. Azure Stack 있는 가용성 Azure를 사용 하 여 일치 하는 최대 3 개의 장애 도메인을 사용 하 여 집합을 지원 합니다.

- **장애 도메인**합니다. Vm 가용성 집합에 배치는 여러 장애 도메인 (Azure Stack 노드)에 해당 작업을 최대한 균등 하 게 분산 하 여 물리적으로 서로 격리 됩니다. 하드웨어 오류가 있으면 실패 한 장애 도메인에서 Vm는 다른 장애 도메인에 다시 시작 되지만, 동일한 가용성 집합의 다른 Vm에서 별도 장애 도메인에서 유지 가능한 경우. 하드웨어 상태가 다시 온라인 상태가 되 면 고가용성을 유지 하기 위해 Vm 부하가 다시 분산 됩니다. 
 
- **업데이트 도메인**합니다. 업데이트 도메인은 가용성 집합의 고가용성을 제공 하는 다른 Azure 개념입니다. 업데이트 도메인은 동시에 유지 관리를 거칠 수 있습니다 기본 하드웨어의 논리적 그룹입니다. 동일한 업데이트 도메인에 있는 Vm 계획 된 유지 관리 동안 함께 다시 시작 됩니다. 테 넌 트 Vm 가용성 집합 내의 만들 때 Azure 플랫폼에 자동으로 분산 Vm이 도메인을 업데이트 합니다. Azure Stack에서 Vm은 라이브 클러스터에 있는 다른 온라인 호스트 간에 마이그레이션되는 해당 기본 호스트를 업데이트 하기 전에 합니다. 호스트 업데이트 중에 가동 중지 시간 없이 테 넌 트 이므로 Azure Stack의 업데이트 도메인 기능 Azure 사용 하 여 템플릿 호환성을 위해서만 존재 합니다. 

## <a name="role-based-access-control-rbac"></a>역할 기반 Access Control(RBAC)
구독, 리소스 그룹 또는 개별 리소스 수준에서 역할을 할당하여 시스템 액세스에 권한이 있는 사용자, 그룹 및 서비스를 허용하도록 RBAC를 사용할 수 있습니다. 각 역할은 사용자, 그룹 또는 서비스가 Microsoft Azure Stack 리소스에 가진 액세스 수준을 정의합니다.

Azure RBAC에는 모든 리소스 유형에 적용되는 3가지 기본 역할이 있습니다. 소유자, 참가자 및 판독기입니다. 소유자는 액세스 권한을 다른 사용자에게 위임할 수 있는 권한을 포함하여 모든 리소스에 대한 전체 액세스 권한을 보유합니다. 참여자는 모든 유형의 Azure 리소스를 만들고 관리할 수 있으나 다른 사용자에게 액세스 권한을 부여할 수 없습니다. 읽기 권한자는 기존 Azure 리소스만 볼 수 있습니다. Azure의 나머지 RBAC 역할은 특정 Azure 리소스의 관리를 허용합니다. 예를 들어, Virtual Machine 참여자 역할은 가상 머신 만들기 및 관리를 허용하지만 가상 머신이 연결되는 가상 네트워크 또는 서브넷의 관리는 허용하지 않습니다.

## <a name="usage-data"></a>사용 데이터
Microsoft Azure Stack 모든 리소스 공급자에서 사용 현황 데이터를 집계 및 전송 azure 처리를 위해 Azure에서 수집 합니다. REST API를 통해 Azure Stack에서 수집한 사용량 데이터를 볼 수 있습니다. Azure에 일관된 테넌트 API 뿐만 아니라 테넌트 구독 전체에서 사용 현황을 가져오는 공급자 및 위임된 공급자 API가 있습니다. 이 데이터는 대금 청구 또는 차지백을 위한 외부 도구 또는 서비스와 통합하는 데 사용될 수 있습니다. Azure에서 사용량을 처리 한 후에 Azure 청구 포털에서 볼 수 있습니다.

## <a name="next-steps"></a>다음 단계
[관리 기본 사항](azure-stack-manage-basics.md)

