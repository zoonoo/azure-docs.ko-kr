---
title: "Azure 용어집- Azure 사전 | Microsoft Docs"
description: "Azure 플랫폼에 대한 클라우드 용어를 이해하려면 Azure 용어집을 사용합니다. 이 간단한 Azure 사전에서 Azure의 일반적인 클라우드 용어에 대한 정의를 제공합니다."
keywords: "Azure 사전, 클라우드 용어, Azure 용어집, 용어 정의, 클라우드 용어"
services: na
documentationcenter: na
author: MonicaRush
manager: jhubbard
editor: 
ms.assetid: d7ac12f7-24b5-4bcd-9e4d-3d76fbd8d297
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/03/2016
ms.author: monicar
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: 1842d55e2275560581cf2766cdf74e0939e68c98
ms.lasthandoff: 04/03/2017


---
# <a name="microsoft-azure-glossary-a-dictionary-of-cloud-terminology-on-the-azure-platform"></a>Microsoft Azure 용어집: Azure 플랫폼의 클라우드 용어 사전
Microsoft Azure 용어집은 Azure 플랫폼용 클라우드 용어의 간단한 사전입니다.

## <a name="find-service-definitions-and-other-cloud-terms"></a>서비스 정의 및 기타 클라우드 용어 찾기
* Azure 서비스 및 해당 AWS 서비스의 정의는 [Microsoft Azure 및 Amazon Web Services 비교](https://azure.microsoft.com/campaigns/azure-vs-aws/mapping/)를 참조하세요.
* 일반 업계 클라우드 용어는 [클라우드 컴퓨팅 용어](https://azure.microsoft.com/overview/cloud-computing-dictionary/)를 참조하세요.

Azure 용어집은 위의 두 참조와 함께 Azure 및 클라우드 업계에 대한 종단 간 분류법을 제공합니다.  

## <a name="azure-glossary-list"></a>Azure 용어집 목록
### <a name="account"></a>계정
Azure 구독에 액세스하고 관리하는 데 사용하는 회사, 학교 또는 개인 Microsoft 계정입니다.  
[Azure 구독과 Azure Active Directory의 연관 관계](active-directory/active-directory-how-subscriptions-associated-directory.md)

### <a name="availability-set"></a>가용성 집합
응용 프로그램 중복성 및 안정성을 제공하기 위해 함께 관리되는 가상 컴퓨터의 컬렉션입니다. 가용성 집합을 사용하면 계획되거나 계획되지 않은 유지 관리 이벤트 중에 하나 이상의 가상 컴퓨터를 사용할 수 있도록 합니다.  
[Windows 가상 컴퓨터의 가용성 관리](virtual-machines/windows/manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) 또는 [Linux 가상 컴퓨터의 가용성 관리](virtual-machines/linux/manage-availability.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)를 참조하세요.

### <a name="classic-model"></a>Azure 클래식 배포 모델
Azure의 리소스를 배포하는 데 사용하는 두 [배포 모델](resource-manager-deployment-model.md) 중 하나입니다(새 모델은 Azure Resource Manager임). 일부 Azure 리소스는 둘 중 하나의 모델에서 배포할 수 있지만 다른 리소스는 두 모델 모두에서 배포할 수 있습니다. 리소스가 배포될 수 있는 모델에 대한 개별 Azure 리소스 세부 정보를 안내합니다.

### <a name="cli"></a>Azure CLI(명령줄 인터페이스)
Windows, OSX 및 Linux PC의 Azure 서비스를 관리하는 데 사용할 수 있는 [명령줄 인터페이스](cli-install-nodejs.md) 입니다.

### <a name="powershell"></a>Azure PowerShell
Windows PC의 명령줄을 통해 Azure 서비스를 관리하는 [명령줄 인터페이스](/powershell/azureps-cmdlets-docs) 입니다. 일부 서비스 또는 서비스 기능은 PowerShell 또는 CLI를 통해서만 관리할 수 있습니다. 리소스가 배포될 수 있는 모델에 대한 각 개별 Azure 리소스 세부 정보의 지침입니다.   
[Azure PowerShell 설치 및 구성 방법](/powershell/azureps-cmdlets-docs)

### <a name="arm-model"></a>Azure Resource Manager 배포 모델
Microsoft Azure의 리소스를 배포하는 데 사용하는 두 [배포 모델](resource-manager-deployment-model.md) 중 하나입니다(다른 모델은 클래식 배포 모델임). 일부 Azure 리소스는 둘 중 하나의 모델에서 배포할 수 있지만 다른 리소스는 두 모델 모두에서 배포할 수 있습니다. 리소스가 배포될 수 있는 모델에 대한 개별 Azure 리소스 세부 정보를 안내합니다.

### <a name="fault-domain"></a>장애 도메인
동시에 실패할 가능성이 있는 가용성 집합의 가상 컴퓨터 컬렉션입니다. 예를 들어 랙에서 공통된 전원 및 네트워크 스위치를 공유하는 컴퓨터 그룹입니다. Azure에서 가용성 집합의 가상 컴퓨터는 여러 장애 도메인에서 자동으로 분리됩니다.  
[Windows 가상 컴퓨터의 가용성 관리](virtual-machines/windows/manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) 또는 [Linux 가상 컴퓨터의 가용성 관리](virtual-machines/linux/manage-availability.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)를 참조하세요.  

### <a name="geo"></a>지역
일반적으로 두 개 이상의 영역을 포함하는 데이터 상주에 대해 정의된 경계입니다. 경계는 국경 내에 있거나 초과될 수 있으며 세금 규정의 영향을 받습니다. 모든 지역에는 하나 이상의 하위 지역이 있습니다. 지역의 예로 아시아 태평양 및 일본이 있습니다. *지리*라고도 합니다.  
[Azure 지역](best-practices-availability-paired-regions.md)

### <a name="geo-replication"></a>지역에서 복제
지역 쌍 내의 Blob, 테이블 및 큐와 같은 콘텐츠를 자동으로 복제하는 프로세스입니다.  
[Azure SQL 데이터베이스에 대한 활성 지역 복제](sql-database/sql-database-geo-replication-overview.md)

### <a name="image"></a>이미지
개수에 관계없이 여러 가상 컴퓨터를 만드는 데 사용할 수 있는 운영 체제 및 응용 프로그램 구성을 포함하는 파일입니다. Azure에는 두 가지 유형의 이미지, 즉 VM 이미지와 OS 이미지가 있습니다. VM 이미지에는 운영 체제 및 이미지를 만들 때 가상 컴퓨터에 연결된 모든 디스크가 포함됩니다. OS 이미지에는 데이터 디스크 구성 없이 일반화된 운영 체제만 포함되어 있습니다.  
[Powershell 또는 CLI로 Azure의 Windows 가상 컴퓨터 이미지 이동 및 선택](virtual-machines/windows/cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

### <a name="limits"></a>제한
만들 수 있는 리소스 수 또는 도달할 수 있는 성능 벤치마크입니다. 제한은 일반적으로 구독, 서비스 및 제공 사항에 연결됩니다.  
[Azure 구독 및 서비스 제한, 할당량 및 제약 조건](azure-subscription-service-limits.md)

### <a name="load-balancer"></a>부하 분산 장치
들어오는 트래픽을 네트워크의 컴퓨터 간에 분산시키는 리소스입니다. Azure에서 부하 분산 장치는 부하 분산 장치 집합에 정의된 가상 컴퓨터에 트래픽을 분산시킵니다. [부하 분산 장치](load-balancer/load-balancer-overview.md) 는 인터넷에 연결되거나 내부에 있을 수 있습니다.  

### <a name="offer"></a>제안
Azure 구독에 적용되는 가격 책정, 신용 및 관련 조건입니다.  
[Azure 제안 세부 정보 페이지](https://azure.microsoft.com/support/legal/offer-details/)

### <a name="portal"></a>포털
Azure 서비스를 배포하고 관리하는 데 사용하는 보안 웹 포털입니다.  포털에는 두 가지, 즉 [Azure portal](http://portal.azure.com/) 및 [클래식 포털](http://manage.windowsazure.com/)이 있습니다. 일부 서비스는 두 포털 모두에서 사용할 수 있는 반면 다른 서비스는 둘 중 하나의 포털에서만 사용할 수 있습니다. [Azure 포털 가용성 차트](https://azure.microsoft.com/features/azure-portal/availability/) 에 포털 중 하나에서 사용할 수 있는 서비스가 나열되어 있습니다.  

### <a name="region"></a>지역
국경을 넘지 않으면서 하나 이상의 데이터 센터를 포함하는 지역 내의 영역입니다. 가격 책정, 지역 서비스 및 제공 사항 유형은 지역 수준에서 노출됩니다. 지역은 일반적으로 최대 수백 킬로미터까지도 떨어질 수 있는 다른 지역과 쌍으로 연결되어 지역 쌍을 형성합니다. 지역 쌍은 재해 복구 및 고가용성 시나리오에 대한 메커니즘으로 사용할 수 있습니다. 일반적으로 *위치*라고도 합니다.  
[Azure 지역](best-practices-availability-paired-regions.md)

### <a name="resource"></a>리소스
Azure 솔루션에 속한 항목입니다. 각 Azure 서비스를 사용하면 데이터베이스 또는 가상 컴퓨터와 같은 다양한 유형의 리소스를 배포할 수 있습니다.   
또한 [Azure Resource Manager 개요](azure-resource-manager/resource-group-overview.md)를 참조하세요.

### <a name="resource-group"></a>리소스 그룹
리소스 관리자에서 응용 프로그램에 관련된 리소스를 보유하는 컨테이너입니다. 리소스 그룹에는 모든 응용 프로그램에 대한 리소스 또는 논리적으로 그룹화된 리소스만 포함될 수 있습니다. 사용자의 조직에 가장 적합한 내용에 따라 어떻게 리소스 그룹에 리소스를 할당할지 결정할 수 있습니다.  
또한 [Azure Resource Manager 개요](azure-resource-manager/resource-group-overview.md)를 참조하세요.

### <a name="arm-template"></a>리소스 관리자 템플릿
하나 이상의 Azure 리소스를 선언적으로 정의하고 배포된 리소스 간의 종속성을 정의하는 JSON 파일입니다. 템플릿은 리소스를 일관되고 반복적으로 배포하는 데 사용할 수 있습니다.  
[Azure Resource Manager 템플릿 작성](resource-group-authoring-templates.md)

### <a name="resource-provider"></a>리소스 공급자
리소스 관리자를 통해 배포하고 관리할 수 있는 리소스를 제공하는 서비스입니다. 각 리소스 공급자는 배포된 리소스로 작업하기 위한 작업을 제공합니다. 리소스 공급자는 Azure 포털, Azure PowerShell 및 몇 가지 프로그래밍 SDK를 통해 액세스할 수 있습니다.  
또한 [Azure Resource Manager 개요](azure-resource-manager/resource-group-overview.md)를 참조하세요.

### <a name="role"></a>역할
사용자, 그룹 및 서비스에 할당할 수 있는 액세스를 제어하는 방법입니다. 역할은 Azure 리소스에서 만들기, 관리 및 읽기와 같은 작업을 수행할 수 있습니다.  
[RBAC: 기본 제공 역할](active-directory/role-based-access-built-in-roles.md)

### <a name="sla"></a>SLA(서비스 수준 계약)
작동 시간 및 연결에 대한 Microsoft의 정책을 설명하는 계약입니다. 각 Azure 서비스에는 특정 SLA가 있습니다.  
[서비스 수준 계약](https://azure.microsoft.com/support/legal/sla/)

### <a name="storage-account"></a>저장소 계정
저장소 계정은 Azure 저장소의 Azure Blob, 큐, 테이블 및 파일 서비스에 대한 액세스 권한을 제공합니다. 저장소 계정은 Azure 저장소 데이터 개체에 고유한 네임스페이스를 제공합니다.  
[Azure 저장소 계정 정보](storage/storage-create-storage-account.md)

### <a name="subscription"></a>구독
Azure 서비스를 받을 수 있도록 하는, 고객과 Microsoft와의 계약입니다. 구독 가격 책정 및 관련 조건은 구독에 대해 선택된 제공 사항에 따라 제어됩니다. [Microsoft 온라인 정기가입 계약](https://azure.microsoft.com/support/legal/subscription-agreement/)을 참조하세요.  
[Azure 구독과 Azure Active Directory의 연관 관계](active-directory/active-directory-how-subscriptions-associated-directory.md)

### <a name="tag"></a>태그
관리 또는 청구에 대한 요구 사항에 따라 리소스를 분류할 수 있도록 하는 인덱싱 용어입니다. 리소스 그룹 및 리소스의 복잡한 컬렉션이 있고 이러한 자산에 가장 적합한 방식으로 시각화할 필요가 있을 때 태그를 사용할 수 있습니다. 예를 들어 조직에서 비슷한 역할을 제공하거나 동일한 부서에 속한 리소스를 태그로 지정할 수 있습니다.  
[태그를 사용하여 Azure 리소스 구성](resource-group-using-tags.md)

### <a name="update-domain"></a>업데이트 도메인
동시에 업데이트되는 가용성 집합에 있는 가상 컴퓨터의 컬렉션입니다. 동일한 업데이트 도메인의 가상 컴퓨터는 계획된 유지 관리 동안 함께 다시 시작됩니다. Azure는 한 번에 둘 이상의 업데이트 도메인을 다시 시작하지 않습니다. 또한 업그레이드 도메인이라고도 합니다.  
[Windows 가상 컴퓨터의 가용성 관리](virtual-machines/windows/manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) 또는 [Linux 가상 컴퓨터의 가용성 관리](virtual-machines/linux/manage-availability.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)를 참조하세요.  

### <a name="vm"></a>가상 컴퓨터
운영 체제를 실행하는 물리적 컴퓨터의 소프트웨어 구현입니다. 같은 하드웨어에서 복수의 가상 컴퓨터가 동시에 실행될 수 있습니다. Azure에서 가상 컴퓨터는 다양한 크기로 사용할 수 있습니다.  
[가상 컴퓨터 설명서](https://azure.microsoft.com/documentation/services/virtual-machines/)

### <a name="vm-extension"></a>가상 컴퓨터 확장
다른 프로그램의 작동을 돕거나 실행 중인 컴퓨터를 조작할 수 있는 기능을 제공하는 동작 또는 기능을 구현하는 리소스입니다. 예를 들어 Azure 가상 컴퓨터에서 VM 액세스 확장을 사용하여 원격 액세스 값을 다시 설정하거나 수정할 수 있습니다.  
[가상 컴퓨터 확장 및 기능 정보(Windows)](virtual-machines/windows/extensions-features.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) 또는 [가상 컴퓨터 확장 및 기능 정보(Linux)](virtual-machines/linux/extensions-features.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)를 참조하세요.

### <a name="vnet"></a>가상 네트워크
다른 모든 Azure 테넌트에서 격리된 Azure 리소스 간의 연결을 제공하는 네트워크입니다. 다른 Azure 가상 네트워크에 [Azure VPN Gateway](vpn-gateway/vpn-gateway-about-vpngateways.md)를 통해 연결되고 [여러 옵션](vpn-gateway/vpn-gateway-plan-design.md)을 사용하여 온-프레미스 네트워크에 연결될 수 있습니다. 사용자는 이 네트워크 내부의 IP 주소 블록, DNS 설정, 보안 정책 및 경로 테이블을 완벽하게 제어할 수 있습니다.  
[가상 네트워크 개요](virtual-network/virtual-networks-overview.md)  

### <a name="see-also"></a>**참고 항목**
* [Azure 시작](https://azure.microsoft.com/get-started/)
* [클라우드 리소스 센터](https://azure.microsoft.com/resources/)  
* [비즈니스 응용 프로그램을 위한 Azure](https://azure.microsoft.com/overview/business-apps-on-azure/)
* [데이터 센터의 Azure](https://azure.microsoft.com/overview/business-apps-on-azure/)


