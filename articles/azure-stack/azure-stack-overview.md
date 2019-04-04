---
title: Azure Stack이란? | Microsoft Docs
description: Azure Stack 데이터 센터에서 Azure 서비스를 실행할 수 있도록 하는 방법에 대해 알아봅니다.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 03/29/2019
ms.author: jeffgilb
ms.reviewer: unknown
ms.custom: ''
ms.lastreviewed: 03/29/2019
ms.openlocfilehash: e5e05ad4f2ae7e718e160916144f03bfb74a2a52
ms.sourcegitcommit: 956749f17569a55bcafba95aef9abcbb345eb929
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2019
ms.locfileid: "58631318"
---
# <a name="azure-stack-overview"></a>Azure Stack 개요

Azure Stack은 온-프레미스 환경에서 응용 프로그램을 실행 하 고 데이터 센터에서 Azure 서비스를 제공 하는 방법을 제공 하는 Azure의 확장입니다. 조직이 일관 된 클라우드 플랫폼을 사용 하 여 비즈니스 의사 결정 기술 제한 사항에 따라 보다는 비즈니스 요구 사항에 따라 기술 결정 자신 있게 가능 합니다.

## <a name="why-use-azure-stack"></a>Azure Stack을 사용 하는 이유

Azure는 최신 응용 프로그램을 빌드하는 개발자를 위한 다양 한 플랫폼을 제공 합니다. 그러나 일부 클라우드 기반 응용 프로그램 대기 시간, 간헐적으로 연결 및 규정의 장애물 때문에 직면합니다. Azure 및 Azure Stack 고객 지향 및 내부 기간 업무 응용 프로그램에 대 한 새로운 하이브리드 클라우드 사용 사례를 잠금 해제 합니다.

- **Edge 및 연결이 끊긴 솔루션**. Azure Stack에서 로컬로 데이터를 처리 하 고 둘 사이에서 일반적인 응용 프로그램 논리를 사용 하 여 추가 분석을 위해 Azure에서 집계 하 여 대기 시간 및 연결 요구 사항을 해결 합니다. Azure에 연결 하지 않고 인터넷에서 연결 되지 않은 Azure Stack도 배포할 수 있습니다. 공장의 think, 선, 및 예로 mine 독 순항 합니다.

- **다양한 규정을 충족하는 클라우드 애플리케이션**. 개발 하 고 유연 하 게 필요한 코드 변경 없이 온-프레미스 규정을 충족 하기 위해 Azure Stack 또는 정책 요구 사항을 사용 하 여 배포를 사용 하 여 Azure에서 응용 프로그램을 배포 합니다. 응용 프로그램 예로 전역 감사, 재무 보고, 환율 거래 업체, 온라인 게임 및 비용 보고 합니다.

- **온-프레미스의 클라우드 애플리케이션 모델**. Azure 서비스, 서버 리스, 컨테이너 및 마이크로 서비스 아키텍처를 업데이트 하 고 기존 응용 프로그램을 확장 하거나 새 빌드를 사용 합니다. 클라우드에서 Azure 전체에서 사용 하 여 일관 된 DevOps 프로세스 및 Azure Stack 온-프레미스 응용 프로그램 현대화 핵심 업무용 응용 프로그램에 대 한 속도입니다.

Azure Stack 제공 하 여 이러한 시나리오를 가능 합니다.

- 통합된 배달 환경을 시작 하 고 특화 된 Azure Stack을 사용 하 여 빠르게 실행 신뢰할 수 있는 하드웨어 파트너 로부터 시스템을 통합 합니다. 배달을 한 후 Azure Stack Nagios 또는 System Center Operations Manager 관리 팩 확장을 통해 모니터링을 통해 데이터 센터에 쉽게 통합 됩니다.

- 유연성 있는 id 관리에 대 한 Active Directory Federation Services (AD FS)를 활용 하 여 Azure Active Directory (Azure AD)를 사용 하 여 하이브리드 환경에서는 Azure 및 Azure Stack 배포 연결이 끊어졌습니다. 

- 하이브리드 환경에 걸쳐 개발자 생산성을 극대화 하 고 일반적인 DevOps를 사용 하도록 설정 하는 Azure와 일관 된 응용 프로그램 개발 환경에 가깝습니다.

- Azure 서비스 제공에서 온-프레미스 하이브리드 클라우드 컴퓨팅 기능을 사용 하 여 합니다. 배포 하 고 Azure와 같은 관리 환경 및 도구를 사용 하 여 Azure IaaS 및 PaaS 서비스를 작동 하는 Azure 및 Azure Stack에서 일반적인 운영 방침을 채택 합니다. Microsoft은 Azure stack에서 새 Azure 서비스를 기존 서비스 및 추가 Azure Marketplace 응용 프로그램 및 이미지에 대 한 업데이트를 포함 하 여 지속적인 Azure 혁신을 제공 합니다.

## <a name="azure-stack-architecture"></a>Azure Stack 아키텍처
신뢰할 수 있는 하드웨어 파트너 하 고 데이터 센터에 바로 배달 하는 azure Stack 통합된 시스템에서 빌드된 4-16 서버 랙에서 구성 됩니다. 배달을 한 후 솔루션 공급자 통합된 시스템을 배포 하 고 Azure Stack 솔루션에는 비즈니스 요구 사항을 충족 하는지 확인 하를 사용 하 여 작동 합니다. 필요한 모든 전원 및 냉각 하 여 데이터 센터를 준비 해야, 테두리 연결 및 기타 필요한 데이터 센터 통합 요구 사항 진행에서 됩니다. 

> Azure Stack 데이터 센터 통합 환경에 대 한 자세한 내용은 참조 하세요. [Azure Stack 데이터 센터 통합](azure-stack-customer-journey.md)합니다.

Azure Stack 업계 표준 하드웨어에서 작성 되 고 이미 Azure 구독 관리를 위해 사용 하는 것과 동일한 도구를 사용 하 여 관리 됩니다. 결과적으로, 또는 Azure에 연결 되어 있는지 여부를 일관 된 DevOps 프로세스를 적용할 수 있습니다. 

Azure Stack 아키텍처를 사용 하면 원격 위치에 일시적인 연결, 인터넷에서 연결이 끊어진 가장자리에 대 한 Azure 서비스를 제공할 수 있습니다. Azure Stack에서 로컬로 데이터를 처리 하 고 추가 처리 및 분석을 위해 Azure에서 집계 하는 하이브리드 솔루션을 만들 수 있습니다. 마지막으로, Azure Stack에서 온-프레미스 설치 이므로 특정 규정 또는 정책 요구 사항을 코드를 변경 하지 않고도 클라우드 응용 프로그램 온-프레미스를 배포 하는 유연 하 게 충족할 수 있습니다. 

## <a name="deployment-options"></a>배포 옵션

### <a name="production-or-evaluation-environments"></a>프로덕션 또는 평가판 환경
Azure Stack 프로덕션 사용에 대 한 Azure Stack 통합 시스템 및는 Azure Stack 개발 키트 ASDK () Azure Stack을 평가 하는 것에 대 한 요구 사항을 충족 하기 위해 두 가지 배포 옵션으로 제공 됩니다.

- **Azure Stack 통합 시스템**. Azure Stack 통합된 시스템 솔루션을 만드는 Microsoft 및 하드웨어 파트너의 파트너 관계를 통해 제공 되는 클라우드 주도적 혁신 및 컴퓨팅 관리의 간편함을 제공 합니다. Azure Stack은 통합된 하드웨어 및 소프트웨어 시스템으로 제공되므로 클라우드에서 혁신할 수있는 기능과 함께 필요한 유연성과 제어 기능을 사용할 수 있습니다. Azure Stack 통합 시스템 4-16 노드에서 크기 및 하드웨어 파트너와 Microsoft에서 지원 됩니다. Azure Stack 통합 시스템을 사용하여 새로운 시나리오를 만들고 프로덕션 워크로드에 맞는 새로운 솔루션을 배포합니다.

- **Azure Stack 개발 키트**. 합니다 [Azure Stack 개발 키트 (ASDK)](./asdk/asdk-what-is.md) 평가 하 고 Azure Stack에 대해 알아보기에 사용할 수 있는 Azure Stack의 무료, 단일 노드 배포입니다. 도구 및 Api를 사용 하 여 앱을 빌드하는 개발자 환경으로 Azure를 사용 하 여 일치 하는 ASDK도 사용할 수 있습니다. 그러나는 ASDK 프로덕션 환경으로 사용할 수 없습니다 및 통합된 시스템을 전체 프로덕션 배포에 비해 다음과 같은 제한이 있습니다.

    - ASDK를 단일 Azure Active Directory (Azure AD) 또는 Active Directory Federation Services (AD FS) id 공급자와만 연결할 수 있습니다.
    - 없기 때문에 Azure Stack 구성 요소를 단일 호스트 컴퓨터에 배포 된, 제한 된 물리적 리소스 테 넌 트 리소스에 대해 사용할 수 있습니다. 이 구성은 규모와 성능 평가용 아닙니다.
    - 네트워크 시나리오는 단일 호스트 및 NIC 배포 요구 사항으로 인해 제한됩니다.

### <a name="connection-models"></a>연결 모델
하거나 Azure Stack을 배포 하도록 선택할 수 있습니다 **연결 된** 인터넷 (및 Azure) 또는 **끊어진** 에서. 이 선택 정의 id 저장소 (Azure AD 또는 AD FS) 및 청구 모델에 대해 사용할 수 있는 옵션 (사용 하 여 기준으로 요금을 지불 청구 또는 용량 기반 청구).

> 자세한 내용은 참조에 대 한 고려 사항 [연결 된](azure-stack-connected-deployment.md) 하 고 [연결이 끊긴](azure-stack-disconnected-deployment.md) 배포 모델. 

### <a name="identity-provider"></a>ID 공급자 
Azure Stack Azure Stack identities를 설정 하는 id 공급자로 Azure Active Directory (Azure AD) 또는 Active Directory Federation Services (AD FS)를 사용 합니다. 

> [!IMPORTANT]
> 이 주요 의사 결정 지점! 선택 하면 Azure AD 또는 AD FS id 공급자로 배포 시에 적용 해야 하는 한 번만 결정 합니다. 전체 시스템을 다시 배포 하지 않고이 작업을 나중에 변경할 수 없습니다.

Azure AD는 Microsoft의 클라우드 기반, 다중 테 넌 트 id 공급자입니다. 인터넷에 연결 된 배포를 사용 하 여 대부분의 하이브리드 id 저장소로 Azure AD를 사용합니다. 그러나 Active Directory Federation Services (AD FS) Azure Stack의 연결이 끊긴된 배포에 사용 하도록 선택할 수 있습니다. Azure Stack 리소스 공급자 및 기타 응용 프로그램을 Azure AD와 마찬가지로 AD FS 사용 하 여 비슷하게를 작동 합니다. Azure Stack에 자체 Active Directory 인스턴스 및 Active Directory Graph API를 포함합니다. 

> Azure Stack id 고려 사항에 자세히 알아보십시오 [Azure Stack에 대 한 id 개요](azure-stack-identity-overview.md)합니다.

## <a name="how-is-azure-stack-managed"></a>Azure Stack은 관리 하는 방법
Azure Stack 통합된 시스템 배포 나 ASDK 설치에 배포한 후 관리 포털, 사용자 포털 및 PowerShell Azure Stack을 사용 하 여 상호 작용의 기본 메서드에서 합니다. Azure Stack 포털은 각각 별도 Azure Resource Manager의 인스턴스에서 지원 됩니다. **Azure Stack 운영자** Azure Stack 관리 제품의 경우 테 넌 트를 만들어야 하는 등 작업을 수행 하 고 통합된 된 시스템의 상태 및 모니터 상태를 유지 관리에 관리 포털을 사용 합니다. 가상 머신, 저장소 계정 및 web apps와 같은 클라우드 리소스의 소비에 대 한 셀프 서비스 환경을 제공 하는 사용자 포털 (테 넌 트 포털 라고도 함). 

> 관리 포털을 사용 하 여 Azure Stack을 관리 하는 방법에 대 한 자세한 내용은 참조를 사용 합니다 [Azure Stack 관리 포털 빠른 시작](azure-stack-manage-portals.md)합니다.

Azure Stack 운영자를 제공할 수 있습니다 다양 한 서비스 및 응용 프로그램의 경우와 같은 [virtual machines](azure-stack-tutorial-tenant-vm.md)를 [웹 응용 프로그램](azure-stack-app-service-overview.md)고가용성 [SQL Server](azure-stack-tutorial-sql.md), 및 [MySQL Server](azure-stack-tutorial-mysql.md) 데이터베이스입니다. 사용할 수도 있습니다 [Azure Stack 빠른 시작 Azure Resource Manager 템플릿](https://github.com/Azure/AzureStack-QuickStart-Templates) Exchange, SharePoint 등을 배포 합니다. 

관리 포털을 사용 하면 [서비스를 제공할 Azure Stack 구성](azure-stack-plan-offer-quota-overview.md) 계획, 할당량, 제품 및 구독을 사용 하 여 테 넌 트에 있습니다. 테 넌 트 사용자는 여러 제품을 구독할 수 있습니다. 제안에는 하나 이상의 계획이 있을 수 있으며 계획에는 하나 이상의 서비스가 있을 수 있습니다. 연산자는 또한 용량을 관리 하 고 경고에 응답 합니다. 

Azure Stack 구성 된 경우는 **Azure Stack 사용자** (테 넌 트 라고도 함) 연산자를 제공 하는 서비스를 사용 합니다. 사용자 프로 비전, 모니터링 및 서비스는 구독에 웹 앱, 저장소 및 가상 머신과 같은 관리할 수 있습니다.

> Azure Stack, where, 일반적인 운영자 업무를 사용 하 여 새로운 계정을 포함 하 여 관리 하는 방법에 대 한 자세한 도움말을 봅니다 방법과 사용자에 게 알릴 내용 검토 [Azure Stack 관리 기본 사항](azure-stack-manage-basics.md)합니다.

## <a name="resource-providers"></a>리소스 공급자 
리소스 공급자는 모든 Azure Stack IaaS에 대 한 기초를 형성 하는 웹 서비스 및 PaaS 서비스입니다. Azure Resource Manager 서비스에 대 한 액세스를 제공 하는 다양 한 리소스 공급자에 의존 합니다. 각 리소스 공급자를 사용 하 여 구성 하 고 해당 하는 리소스를 제어할 수 있습니다. 서비스 관리자는 새 사용자 지정 리소스 공급자를 추가할 수도 있습니다. 

### <a name="foundational-resource-providers"></a>기본 리소스 공급자 
기본 IaaS 리소스 공급자 세 가지가 있습니다. Compute, Network 및 저장소:

- **계산**합니다. Compute 리소스 공급자는 자신의 가상 컴퓨터를 만들려면 Azure Stack 테 넌 트를 허용 합니다. Compute 리소스 공급자 가상 머신 확장 뿐만 아니라 가상 컴퓨터를 만드는 기능을 포함 합니다. Virtual Machine 확장 서비스는 Windows 및 Linux 가상 머신에 IaaS 기능을 제공합니다.  예를 들어 Linux 가상 머신을 프로 비전 배포 VM을 구성 하는 동안 Bash 스크립트를 실행 하도록 계산 리소스 공급자를 사용할 수 있습니다.
- **네트워크 리소스 공급자**합니다. 네트워크 리소스 공급자는 일련의 사설 클라우드를 위한 소프트웨어 정의 네트워킹 (SDN) 및 네트워크 기능 가상화 (NFV) 기능을 제공합니다. 소프트웨어 부하 분산 장치, 공용 Ip, 네트워크 보안 그룹 및 가상 네트워크와 같은 리소스를 만들려면 네트워크 리소스 공급자를 사용할 수 있습니다.
- **Storage 리소스 공급자**합니다. 저장소 리소스 공급자는 Azure와 일관 된 4 개의 저장소 서비스를 제공 합니다. [blob](https://docs.microsoft.com/azure/storage/common/storage-introduction#blob-storage), [큐](https://docs.microsoft.com/azure/storage/common/storage-introduction#queue-storage), [테이블](https://docs.microsoft.com/azure/storage/common/storage-introduction#table-storage), 및 관리를 제공 하는 key Vault 계정 관리 및 암호, 암호, 인증서 등의 감사 합니다. 저장소 리소스 공급자도 Azure consistent Storage 서비스의 서비스 공급자 관리를 용이 하 게 저장소 클라우드 관리 서비스를 제공 합니다. Azure Storage는 Azure Blob가 있는 문서 및 미디어 파일, Azure 테이블이 있는 구조화된 NoSQL 기반 데이터 등 구조화되지 않은 많은 양의 데이터를 저장하고 검색하도록 유연성을 제공합니다. 

### <a name="optional-resource-providers"></a>선택적 리소스 공급자
배포 하 고 Azure Stack과 함께 사용할 수 있는 세 가지 선택적 PaaS 리소스 공급자 가지가 있습니다. App Service, SQL Server 및 MySQL Server 리소스 공급자:

- **App Service**합니다. [Azure App Service on Azure Stack](azure-stack-app-service-overview.md) 는 Microsoft Azure의 플랫폼-as a service (PaaS) 제품을 Azure Stack에 사용할 수 있습니다. 서비스에 내부 또는 외부 고객에 게 웹, API 및 Azure Functions를 만들 수 있도록 모든 플랫폼 또는 장치에 대 한 응용 프로그램입니다. 
- **SQL Server**. 사용 된 [SQL Server 리소스 공급자](azure-stack-sql-resource-provider.md) 서비스로 Azure Stack의 SQL database를 제공 합니다. 리소스 공급자를 설치 하 고 하나 이상의 SQL Server 인스턴스에 연결 후 하 고 사용자가 클라우드 네이티브 앱, SQL을 사용 하는 웹 사이트 및 SQL을 사용 하는 다른 워크 로드에 대 한 데이터베이스를 만들 수 있습니다.
- **MySQL Server**. 사용 된 [MySQL Server 리소스 공급자](azure-stack-mysql-resource-provider-deploy.md) MySQL 데이터베이스 서비스를 Azure Stack으로 노출 합니다. MySQL 리소스 공급자는 Windows Server 2016 Server Core 가상 머신 (VM)에서 서비스로 실행 됩니다.

## <a name="providing-high-availability"></a>고가용성 제공
Azure에서 다중 VM 프로덕션 시스템의 고가용성을 달성 하려면 Vm에 배치 되는 [가용성 집합](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy) 여러 장애 도메인과 업데이트 도메인 간에 분산 하는 합니다. Azure Stack의 소규모 배율 단위의 단일 노드로 가용성 집합의 장애 도메인 정의 됩니다.  

Azure stack 인프라 장애에 복원 력이 이미 상태인 (장애 조치 클러스터링) 기술의 여전히 일부 가동 중지 시간이 발생 Vm에 대 한 영향을 받는 실제 서버의 하드웨어 오류가 발생 하는 경우. Azure Stack 있는 가용성 Azure를 사용 하 여 일치 하는 최대 3 개의 장애 도메인을 사용 하 여 집합을 지원 합니다.

- **장애 도메인**합니다. Vm 가용성 집합에 배치는 여러 장애 도메인 (Azure Stack 노드)에 해당 작업을 최대한 균등 하 게 분산 하 여 물리적으로 서로 격리 됩니다. 하드웨어 오류가 있으면 실패 한 장애 도메인에서 Vm는 다른 장애 도메인에 다시 시작 되지만, 동일한 가용성 집합의 다른 Vm에서 별도 장애 도메인에서 유지 가능한 경우. 하드웨어 상태가 다시 온라인 상태가 되 면 고가용성을 유지 하기 위해 Vm 부하가 다시 분산 됩니다. 
 
- **업데이트 도메인**합니다. 업데이트 도메인은 가용성 집합의 고가용성을 제공 하는 다른 Azure 개념입니다. 업데이트 도메인은 동시에 유지 관리를 거칠 수 있습니다 기본 하드웨어의 논리적 그룹입니다. 동일한 업데이트 도메인에 있는 Vm 계획 된 유지 관리 동안 함께 다시 시작 됩니다. 테 넌 트 Vm 가용성 집합 내의 만들 때 Azure 플랫폼에 자동으로 분산 Vm이 도메인을 업데이트 합니다. Azure Stack에서 Vm은 라이브 클러스터에 있는 다른 온라인 호스트 간에 마이그레이션되는 해당 기본 호스트를 업데이트 하기 전에 합니다. 호스트 업데이트 중에 가동 중지 시간 없이 테 넌 트 이므로 Azure Stack의 업데이트 도메인 기능 Azure 사용 하 여 템플릿 호환성을 위해서만 존재 합니다. 

## <a name="role-based-access-control"></a>역할 기반 액세스 제어
구독, 리소스 그룹 또는 개별 리소스 수준에서 역할을 할당 하 여 시스템 액세스 권한이 있는 사용자, 그룹 및 서비스 권한을 부여 하려면 역할 기반 액세스 제어 (RBAC)를 사용할 수 있습니다. 각 역할은 사용자, 그룹 또는 서비스가 Microsoft Azure Stack 리소스에 가진 액세스 수준을 정의합니다.

Azure Stack RBAC는 모든 리소스 유형에 적용 되는 세 가지 기본 역할: 소유자, 참가자 및 판독기입니다. 소유자는 액세스 권한을 다른 사용자에게 위임할 수 있는 권한을 포함하여 모든 리소스에 대한 전체 액세스 권한을 보유합니다. 참여자는 모든 유형의 Azure 리소스를 만들고 관리할 수 있으나 다른 사용자에게 액세스 권한을 부여할 수 없습니다. 판독기에서 기존 리소스를 보기만 할 수 있습니다. RBAC 역할은 나머지 특정 Azure 리소스 관리를 허용 합니다. 예를 들어, Virtual Machine 참여자 역할은 가상 머신 만들기 및 관리를 허용하지만 가상 머신이 연결되는 가상 네트워크 또는 서브넷의 관리는 허용하지 않습니다.

> 참조 [Access Control](azure-stack-manage-permissions.md) 자세한 내용은 합니다. 

## <a name="reporting-usage-data"></a>사용 현황 데이터를 보고합니다.
Microsoft Azure Stack 모든 리소스 공급자에서 사용 현황 데이터를 집계 및 전송 azure 처리를 위해 Azure에서 수집 합니다. REST API를 통해 Azure Stack에서 수집한 사용량 데이터를 볼 수 있습니다. Azure에 일관된 테넌트 API 뿐만 아니라 테넌트 구독 전체에서 사용 현황을 가져오는 공급자 및 위임된 공급자 API가 있습니다. 이 데이터는 대금 청구 또는 차지백을 위한 외부 도구 또는 서비스와 통합하는 데 사용될 수 있습니다. Azure에서 사용량을 처리 한 후에 Azure 청구 포털에서 볼 수 있습니다.

> 에 대해 자세히 알아보세요 [Azure에 Azure Stack 사용 현황 데이터를 보고](azure-stack-usage-reporting.md)합니다.

## <a name="next-steps"></a>다음 단계

[Azure Stack 및 전역 Azure를 비교 합니다.](compare-azure-azure-stack.md)

[관리 기본 사항](azure-stack-manage-basics.md)

[빠른 시작: Azure Stack 관리 포털 사용](azure-stack-manage-portals.md)