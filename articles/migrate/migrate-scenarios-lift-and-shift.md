---
title: Azure DMS 및 Site Recovery를 사용하여 Azure로 온-프레미스 작업 마이그레이션 | Microsoft Docs
description: Azure Database Migration Service 및 Azure Site Recovery 서비스를 사용하여 온-프레미스 컴퓨터를 마이그레이션할 수 있도록 Azure를 준비하는 방법을 알아봅니다.
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: conceptual
ms.date: 04/21/2018
ms.author: raynew
ms.openlocfilehash: d6fc1af3c5a587ab62b86dcd4189165d96e6e3bc
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2018
ms.locfileid: "32182243"
---
# <a name="scenario-2-lift-and-shift-migration-to-azure"></a>시나리오 2: Azure로 신속한 마이그레이션 전환

Contoso 회사는 Azure로의 마이그레이션을 고려하고 있습니다. 이를 시도해 보기 위해 Azure로 작은 온-프레미스 여행 앱을 평가 및 마이그레이션하려고 합니다. VM에서 실행되는 웹앱 및 두 번째 VM의 SQL Server 데이터베이스가 있는 2계층 여행 앱입니다. 응용 프로그램은 VMware에 배포되고, 환경은 vCenter Server를 통해 관리됩니다. 

[시나리오 1: Azure로의 마이그레이션 평가](migrate-scenarios-assessment.md)에서 DMA(Data Migration Assistant)를 사용하여 앱에 대한 SQL Server 데이터베이스를 평가했습니다. 또한 Azure Migrate 서비스를 사용하여 앱 VM을 평가했습니다. 이제 이 시나리오에서 성공적으로 평가를 완료한 후 Azure DMS(Database Migration Service)를 사용하여 데이터를 Azure SQL 관리되는 인스턴스로, Azure Site Recovery 서비스를 사용하여 온-프레미스 컴퓨터를 Azure VM으로 마이그레이션하려고 합니다.

이 설명 여행 앱을 사용하여 [시나리오 1](migrate-scenarios-assessment.md) 및 이 시나리오를 차례로 시도해보려는 경우 [github](https://github.com/Microsoft/SmartHotel360)에서 다운로드할 수 있습니다.



**서비스** | **설명** | **비용**
--- | --- | ---
[데이터베이스 관리 서비스](https://docs.microsoft.com/azure/dms/dms-overview) | DMS를 통해 최소한의 가동 중지 시간으로 여러 데이터베이스 원본에서 Azure 데이터 플랫폼으로 원활하게 마이그레이션할 수 있습니다. | 서비스는 현재 공개 미리 보기(2018년 4월) 상태이며 미리 보기 기간 동안 무료로 사용할 수 있습니다.<br/><br/> 공개 미리 보기 DMS는 [지역 수](https://docs.microsoft.com/azure/dms/dms-overview)로 제한됩니다.
[Azure SQL 관리되는 인스턴스](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance) | 관리되는 인스턴스는 Azure 클라우드에서 완벽하게 관리되는 SQL Server 인스턴스를 나타내는 관리되는 데이터베이스 서비스입니다. 최신 버전의 SQL Server 데이터베이스 엔진과 동일한 코드를 공유하고 최신 기능, 성능 향상 및 보안 패치를 포함합니다. | Azure에서 실행하는 Azure SQL Database 관리되는 인스턴스를 사용하면 용량에 따라 요금이 청구됩니다. [자세히 알아보기](https://azure.microsoft.com/pricing/details/sql-database/managed/). 
[Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/) | 서비스는 Azure VM, 온-프레미스 VM 및 물리적 서버에 대한 마이그레이션 및 재해 복구를 오케스트레이션하고 관리합니다.  | Azure로 복제하는 동안 Azure Storage 비용이 청구됩니다.  장애 조치(failover) 발생 시 Azure VM이 생성되고, 요금이 발생합니다. 요금 및 가격 책정에 대해 [자세히 알아보세요](https://azure.microsoft.com/pricing/details/site-recovery/).

이 시나리오에서는 DMS에서 온-프레미스 데이터베이스에 연결하고, Azure에서 Azure SQL 관리되는 인스턴스를 만들고, 데이터베이스를 마이그레이션할 수 있도록 사이트 간 VPN을 설정합니다. Site Recovery의 경우 온-프레미스 VMware 환경을 준비하고, 복제를 설정하고, VM을 Azure로 마이그레이션합니다.  


> [!IMPORTANT]
> SQL 관리되는 인스턴스 제한된 공개 미리 보기에 등록해야 합니다. [등록](https://portal.azure.com#create/Microsoft.SQLManagedInstance)하려면 Azure 구독이 필요합니다. 등록을 완료되는 데 몇 일이 걸릴 수 있으므로 이 시나리오의 배포를 시작하기 전에 수행해야 합니다.

## <a name="architecture"></a>아키텍처

### <a name="site-recovery"></a>Site Recovery

![Site Recovery](media/migrate-scenarios-lift-and-shift/asr-architecture.png)  

### <a name="dms"></a>DMS
![DMS](media/migrate-scenarios-lift-and-shift/dms-architecture.png)  

이 시나리오에서는

- Contoso는 일반적인 엔터프라이즈 조직을 나타내는 가상 이름입니다. Contoso는 2계층 온-프레미스 여행 앱을 평가하고 마이그레이션하려고 합니다.
- Contoso는 온-프레미스 데이터 센터(contoso-datacenter)와 온-프레미스 도메인 컨트롤러(**contosodc1**)를 갖고 있습니다.
- 내부 여행 앱은 두 VM, WEBVM 및 SQLVM에 계층화되어 있으며, VMware ESXi 호스트(**contosohost1.contoso.com**)에 있습니다.
- VMware 환경은 VM에서 실행되는 VCenter Server(**vcenter.contoso.com**)를 통해 관리됩니다.

## <a name="prerequisites"></a>필수 조건

이 시나리오를 실행하려는 경우 요구 사항은 다음과 같습니다.

요구 사항 | 세부 정보
--- | ---
**Azure 구독** | Azure 구독이 아직 없는 경우 [체험 계정](https://azure.microsoft.com/pricing/free-trial/)을 만듭니다.<br/><br/> 무료 계정을 만드는 경우 구독 관리자로서 모든 작업을 수행할 수 있습니다.<br/><br/> 기존 구독을 사용하고 관리자가 아닌 경우 관리자와 협력하여 소유자 또는 기여자 권한을 할당받아야 합니다.<br/><br/> 보다 세부적인 권한이 필요한 경우 [이 문서](../site-recovery/site-recovery-role-based-linked-access-control.md)를 검토합니다. 
**사이트 복구(온-프레미스)** | 5.5, 6.0 또는 6.5 버전을 실행하는 온-프레미스 vCenter 서버<br/><br/> 5.5, 6.0 또는 6.5 버전을 실행하는 ESXi 호스트<br/><br/> ESXi 호스트에서 실행되는 하나 이상의 VMware VM<br/><br/> VM은 [Azure 요구 사항](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#azure-vm-requirements)을 충족해야 합니다.<br/><br/> 지원되는 [네트워크](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#network) 및 [저장소](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#storage) 구성입니다.
**DMS** | DMS의 경우 [호환 가능한 온-프레미스 VPN 장치](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpn-devices)가 필요합니다.<br/><br/> 온-프레미스 VPN 장치를 구성할 수 있어야 합니다. 외부적으로 연결 공용 IPv4 주소가 있어야 하며, NAT 장치 뒤에 주소를 배치할 수 없습니다.<br/><br/> 온-프레미스 SQL Server 데이터베이스에 대한 권한이 있는지 확인합니다.<br/><br/> Windows 방화벽은 원본 데이터베이스 엔진에 액세스할 수 있어야 합니다. [자세히 알아보기](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).<br/><br/> 데이터베이스 컴퓨터 앞에 방화벽이 있는 경우 SMB 포트 445를 통해 데이터베이스 및 파일에 엑세스를 허용하는 규칙을 추가합니다.<br/><br/> 원본 SQL Server와 대상 관리되는 인스턴스를 연결하는 데 사용되는 자격 증명은 sysadmin 서버 역할의 구성원이어야 합니다.<br/><br/> DMS에서 원본 데이터베이스를 백업하는 데 사용할 수 있는 온-프레미스 데이터베이스에서 네트워크 공유가 필요합니다.<br/><br/> 원본 SQL Server 인스턴스를 실행하는 서비스 계정이 네트워크 공유에 대한 쓰기 권한을 갖고 있는지 확인합니다.<br/><br/> 네트워크 공유에 대한 전체 제어 권한을 갖고 있는 Windows 사용자(및 암호)를 메모해 둡니다. Azure Database Migration Service는 이러한 사용자 자격 증명을 가장하여 Azure 저장소 컨테이너에 백업 파일을 업로드합니다.<br/><br/> SQL Server Express 설치 프로세스는 TCP/IP 프로토콜을 기본적으로 **사용 안 함**으로 설정합니다. 활성화되어 있는지 확인합니다.


# <a name="scenario-overview"></a>시나리오 개요

우리가 할 일은 다음과 같습니다.

> [!div class="checklist"]
> * **1단계: 사이트 간 VPN 연결 설정**: DMS에서 VPN 사이트 간 연결을 통해 온-프레미스 데이터베이스에 연결합니다. VPN 연결에 대한 가상 네트워크를 만들고 그 후에 Site Recovery를 위해 이 네트워크를 다시 사용합니다. 사용자가 만드는 네트워크는 Recovery Services 자격 증명 모음을 만드는 지역에 있어야 합니다.
> * **2단계: SQL Azure 관리되는 인스턴스 설정**: 온-프레미스 SQL Server 데이터베이스에서 마이그레이션하는 미리 작성된 관리되는 인스턴스가 필요합니다.
> * **3단계: DMS에 대한 SA URI 설정**: SAS(공유 액세스 서명) URI(Uniform Resource Identifier)는 저장소 개체에 제한된 권한을 부여할 수 있도록 저장소 계정의 리소스에 대한 위임된 액세스를 제공합니다. DMS가 서비스에서 SQL Server 백업 파일을 업로드하는 저장소 계정 컨테이너에 액세스할 수 있도록 SAS URI를 설정합니다.
> * **4단계: DMS 준비**: 데이터베이스 마이그레이션 공급자를 등록하고, 인스턴스를 만든 다음, DMS 프로젝트를 만듭니다.
> * **5단계: Site Recovery를 위한 Azure 준비**: 복제된 데이터를 저장할 Azure 저장소 계정을 만듭니다.
> * **6단계: Site Recovery를 위한 온-프레미스 VMware 준비**: VM 검색 및 에이전트 설치를 위한 계정을 준비하고, 장애 조치(failover) 후 Azure VM에 연결하도록 준비합니다. 
> * **7단계: VM 복제**: Site Recovery 원본 및 대상 환경을 구성하고, 복제 정책을 설정하고, Azure 저장소로 VM 복제를 시작합니다.
> * **8단계: DMS를 사용하여 데이터베이스 마이그레이션**: 데이터베이스 마이그레이션을 실행합니다.
> * **9단계: Site Recovery를 사용하여 VM 마이그레이션**: 장애 조치(failover)를 실행하여 VM을 Azure로 마이그레이션합니다.


## <a name="step-1-set-up-site-to-site-vpn-connection"></a>1단계: 사이트 간 VPN 연결 설정

VPN 사이트 간 연결을 준비하려면 가상 네트워크 및 서브넷을 설정하고, 가상 네트워크에 대한 VPN 게이트웨이를 만들고, Azure에서 온-프레미스 VPN에 연결하는 방법을 알 수 있도록 로컬 네트워크 게이트웨이를 설정합니다. 그런 다음, 사이트 간 연결을 만들고 확인합니다.

### <a name="set-up-a-virtual-network"></a>가상 네트워크 설정

Azure 가상 네트워크를 만들어 온-프레미스 SQL Server에 사이트 간 연결로 DMS를 제공합니다.


1. [Azure Portal](https://portal.azure.com)에서 **리소스 만들기**를 클릭합니다. **네트워킹** > **가상 네트워크**를 선택합니다.
2. **Virtual Network** > **배포 모델 선택**에서 **Resource Manager** > **만들기**를 선택합니다.
3. **가상 네트워크 만들기** > **이름**에서 고유한 네트워크 이름을 입력합니다. 이 시나리오의 경우 **ContosoVNET**입니다.
4. **주소 공간**에서 IP 주소 범위를 추가합니다. 범위는 온-프레미스 주소 공간과 겹치면 안 됩니다.
5. **리소스 그룹**에서 새 그룹을 만들거나 기존 리소스 그룹을 선택합니다. 이 시나리오에서는 **ContosoRG**를 사용합니다.
6. **위치**에서 가상 네트워크를 만들 지역을 선택합니다. **미국 동부 2**를 사용합니다.
7. **서브넷**에서 첫 번째 서브넷 이름 및 주소 범위를 추가합니다. **앱** 서브넷을 만들었습니다.
8. 서비스 엔드포인트를 비활성화합니다.

    ![가상 네트워크 만들기](media/migrate-scenarios-lift-and-shift/vnet-create-network.png)

9. 나중에 네트워크를 쉽게 찾을 수 있도록 **대시보드에 고정**을 선택한 다음, **만들기**를 클릭합니다.
10. 가상 네트워크를 만드는 데 몇 초 정도 걸립니다. 만들어지면 Azure Portal 대시보드에서 확인합니다.
11. 가상 네트워크에서 이러한 통신 포트가 허용되는지 확인합니다. 443, 53, 9354, 445, 12000


### <a name="set-up-subnets"></a>서브넷 설정

Azure 네트워크에 4개의 서브넷이 있습니다.

![서브넷 목록](media/migrate-scenarios-lift-and-shift/vnet-subnet-list.png)

네트워크를 만들었을 때 첫 번째 서브넷(앱)을 설정했습니다. 이제 서브넷의 나머지 부분을 만듭니다. 게이트웨이 서브넷은 온-프레미스 사이트에 VPN 연결을 통해 트래픽을 전송하는 Azure용 VPN 게이트웨이 연결에 사용됩니다.

1. 가상 네트워크의 **설정**에서 **서브넷** > **+서브넷**을 클릭합니다.
2. 이 주소 범위를 사용하여 서브넷 **Datamigration**을 설정한 다음, **확인**을 클릭합니다.

    ![데이터 서브넷](media/migrate-scenarios-lift-and-shift/vnet-subnet-data.png)  


3. **+서브넷**에서 이 주소 범위를 사용하여 서브넷 **ID**를 설정한 다음, **확인**을 클릭합니다.
    ![ID 서브넷](media/migrate-scenarios-lift-and-shift/vnet-subnet-identity.png)

4. **서브넷**에서 **게이트웨이 서브넷**을 클릭한 다음, **확인**을 클릭합니다.
    - 이 서브넷은 VPN 게이트웨이에서 VPN 연결에 사용할 IP 주소를 포함합니다.
    - Azure에서 인식할 수 있도록 이 서브넷의 이름은 자동으로 설정됩니다.
    - 온-프레미스 서브넷에 맞게 자동으로 채워진 주소 범위를 조정합니다. 

    ![게이트웨이 서브넷](media/migrate-scenarios-lift-and-shift/vnet-subnet-gateway.png)
    
### <a name="set-up-a-virtual-network-gateway"></a>가상 네트워크 게이트웨이 설정

1. 포털 페이지의 왼쪽에서 **+** 를 클릭하고 검색에서 'Virtual Network 게이트웨이'를 입력합니다. **결과**에서 **가상 네트워크 게이트웨이**를 클릭합니다.
2. '가상 네트워크 게이트웨이' 페이지 아래쪽에서 **만들기**를 클릭합니다.
3. **가상 네트워크 게이트웨이 만들기** >  **이름**에서 게이트웨이 개체에 대한 이름을 지정합니다.
4. **게이트웨이 유형**에서 **VPN**을 선택합니다.
5. **VPN 유형**에서 **경로 기반**을 선택합니다.
6. **SKU**의 드롭다운에서 게이트웨이 SKU를 선택합니다. 드롭다운에 나열되는 SKU는 선택하는 VPN 유형에 따라 달라집니다. 활성-활성 모드를 사용하지 마십시오. SKU에 대해 [자세히 알아봅니다](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpn-gateway-settings#gwsku).
7. **가상 네트워크** > **가상 네트워크 선택**에서 앞에서 만든 네트워크를 추가합니다. 이 네트워크에 게이트웨이가 추가됩니다.
8. **첫 번째 IP 구성**에서 공용 IP 주소를 지정합니다.  VPN Gateway에는 공용 IP 주소가 있어야 합니다. VPN Gateway가 생성될 때 이 주소는 리소스에 동적으로 할당됩니다. 동적 주소만 현재 지원되지만 IP 주소는 할당된 후 변경되지 않습니다.
    - **게이트웨이 IP 구성 만들기**를 클릭합니다. **공용 IP 주소 선택**에서 **+새로 만들기**를 클릭합니다.
    - **공용 IP 주소 만들기**에서 공용 IP 주소에 대한 이름을 지정합니다(Contoso-Gateway). SKU를 **기본**으로 두고, **확인**을 클릭하여 변경 내용을 저장합니다.
        
    ![가상 네트워크 게이트웨이](media/migrate-scenarios-lift-and-shift/vnet-create-vpn-gateway2.png) 

9. **만들기**를 클릭하여 VPN Gateway를 만들기 시작합니다. 설정이 확인되고 대시보드에 "가상 네트워크 게이트웨이 배포"가 표시됩니다.

    ![가상 네트워크 게이트웨이 유효성 검사](media/migrate-scenarios-lift-and-shift/vnet-vpn-gateway-validate.png)
    
하나의 게이트웨이를 만드는 데 최대 45분이 걸릴 수 있습니다. 완료 상태를 확인하기 위해 포털 페이지를 새로 고쳐야 할 수 있습니다.

### <a name="set-up-a-local-network-gateway"></a>로컬 네트워크 게이트웨이 설정

온-프레미스 사이트를 나타내기 위해 Azure에서 로컬 네트워크 게이트웨이를 설정합니다.

1. 포털에서 **+리소스 만들기**를 클릭합니다.
2. 검색 상자에 **로컬 네트워크 게이트웨이**를 입력한 후 **Enter**를 눌러서 검색합니다. 결과에서 **로컬 네트워크 게이트웨이** > **만들기**를 클릭합니다.
3. **로컬 네트워크 게이트웨이 만들기** > **이름**에서 Azure가 로컬 네트워크 게이트웨이 개체에 대해 사용하는 이름을 지정합니다.
4. **IP 주소**에서 Azure에서 연결하는 온-프레미스 VPN 장치의 공용 IP 주소를 지정합니다. IP 주소는 NAT되지 않아야 하고, Azure에서 연결할 수 있어야 합니다.
5. **주소 공간**에서 VPN 게이트웨이를 통해 온-프레미스 장치에 라우팅되는 로컬 네트워크 주소 범위를 입력합니다. Azure 가상 네트워크의 범위와 겹치지 않는지 확인합니다.
6. **BGP 설정 구성**은 이 시나리오와 관계가 없습니다.
7. **구독**에서 구독이 올바른지 확인합니다.
8. **리소스 그룹**에서 네트워크를 만드는 데 사용한 리소스 그룹을 선택합니다(ContosoRG).
9. **위치**에서 가상 네트워크를 만든 지역을 선택합니다.

    ![로컬 게이트웨이](media/migrate-scenarios-lift-and-shift/vnet-create-local-gateway.png)

4. **만들기**를 클릭하여 로컬 게이트웨이를 만듭니다.

### <a name="configure-your-on-premises-vpn-device"></a>온-프레미스 VPN 장치 구성

다음은 온-프레미스 VPN 장치에서 구성해야 하는 사항입니다.

- 방금 만든 Azure 가상 네트워크 게이트웨이의 공용 IPv4 주소
- 미리 공유한 키(VPN 사이트 간 연결을 만들 때 사용한 키)  

온-프레미스 VPN 장치를 설정하려면:

- 사용하고 있는 온-프레미스 VPN 장치에 따라 VPN 장치 구성 스크립트를 다운로드할 수 있습니다. [자세히 알아보기](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-download-vpndevicescript).
- [더 유용한 리소스](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal#VPNDevice)를 검토합니다.

### <a name="create-the-vpn-connection"></a>VPN 연결 만들기

이제 가상 네트워크 게이트웨이와 온-프레미스 VPN 장치 사이의 사이트 간 VPN 연결을 만듭니다.

1. **가상 네트워크** > **개요** > **연결된 장치**에서 가상 네트워크 페이지를 엽니다. 
2. **연결** > **+추가**를 클릭합니다.
3. **연결 추가** > **이름**에서 연결에 대한 이름을 지정합니다.
4. **연결 형식**에서 **사이트 간(IPSec)** 을 선택합니다.
5. 이 게이트웨이에서 연결하기 때문에 **가상 네트워크 게이트웨이** 값은 고정됩니다.
6. **로컬 네트워크 게이트웨이**에서 앞에서 만든 로컬 네트워크 게이트웨이를 지정합니다.
7. **공유 키**에서 로컬 온-프레미스 VPN 장치에 사용하는 값과 일치하는 키를 지정합니다. 예제에서는 'abc123'을 사용하지만 좀 더 복잡한 항목을 사용할 수 있습니다. 중요한 점은 여기에서 지정한 값이 VPN 장치를 구성하는 경우 지정한 것과 동일한 값이어야 한다는 것입니다.
8. **구독**, **리소스 그룹** 및 **위치**에 대한 값이 고정됩니다.

    ![VPN 연결](media/migrate-scenarios-lift-and-shift/vpn-connection.png) 

4. **확인**을 클릭하여 연결을 만듭니다. 화면에서 "연결 만들기" 플래시가 표시됩니다.
5. 연결이 만들어진 후 가상 네트워크 게이트웨이의 **연결** 페이지에서 볼 수 있습니다. 상태는 알 수 없음 > 연결 중 >성공함으로 이동합니다.

### <a name="verify-the-vpn-connection"></a>VPN 연결 확인

Azure Portal에서 연결로 이동하여 Resource Manager VPN Gateway의 연결 상태를 볼 수 있습니다. 

1. **모든 리소스**를 클릭하고, 가상 네트워크 게이트웨이로 이동합니다.
2. 가상 네트워크 게이트웨이 페이지에서 **연결**을 클릭합니다. 각 연결의 상태를 볼 수 있습니다.
3. 연결의 이름을 클릭하고, **기본 정보**에서 자세한 정보를 봅니다. 연결에 성공하면 상태가 '성공함' 및 '연결됨'으로 표시됩니다.

이제 VPN을 통해 SQL Server VM에 연결할 수 있는지 확인합니다. 원격 데스크톱 연결을 사용하여 VM IP 주소에 연결합니다.



## <a name="step-2-prepare-an-azure-sql-managed-instance"></a>2단계: Azure SQL 관리되는 인스턴스 준비

### <a name="set-up-a-virtual-network"></a>가상 네트워크 설정

이 시나리오의 경우 Azure SQL 관리되는 인스턴스 전용 다른 가상 네트워크를 만들어야 합니다.  다음 요구 사항에 유의하세요.

- 관리되는 인스턴스를 만들려면 전용 서브넷이 필요합니다.
- 서브넷은 비어 있고 다른 클라우드 서비스를 포함하지 않아야 하며, 게이트웨이 서브넷이 아니어야 합니다. 관리되는 인스턴스를 만든 후 서브넷에 리소스를 추가해서는 안 됩니다.
- 서브넷에는 연결된 NSG가 없어야 합니다.
- 서브넷에는 할당된 유일한 경로로 0.0.0.0/0 다음 홉 인터넷을 사용하는 사용자 경로 테이블(UDR)이 있어야 합니다. 
- 선택적 사용자 지정 DNS: VNet에 사용자 지정 DNS을 지정하는 경우 Azure의 재귀 해결자 IP 주소(예: 168.63.129.16)를 목록에 추가해야 합니다. [자세히 알아보기](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-custom-dns).
- 서브넷에는 연결된 서비스 엔드포인트(저장소 또는 SQL)가 없어야 합니다. 서비스 엔드포인트는 가상 네트워크에서 비활성화되어야 합니다.
- 서브넷에는 최소 16개의 IP 주소가 있어야 합니다. 관리되는 인스턴스 서브넷 크기 조정에 대해 [자세히 알아봅니다](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-vnet-configuration#determine-the-size-of-subnet-for-managed-instances).

다음과 같이 가상 네트워크를 설정합니다.

1.  Azure Portal에서 **리소스 만들기**를 클릭합니다. 
2. **네트워킹** > **가상 네트워크**를 선택합니다.
3. **Virtual Network** > **배포 모델 선택**에서 **Resource Manager** > **만들기**를 선택합니다.
4. **가상 네트워크 만들기** > **이름**에서 고유한 네트워크 이름을 입력합니다. 이 시나리오의 경우 **ContosoVNETSQLMI**입니다.
5. **주소 공간**에서 아래 표시된 IP 주소 범위를 추가합니다. 범위는 온-프레미스 및 ContosoVNET 주소 공간과 겹치면 안 됩니다.
6. **리소스 그룹**에서 새 그룹을 만들거나 기존 리소스 그룹을 선택합니다. 이 시나리오에서는 **ContosoRG**를 사용합니다.
7. **위치**에서 가상 네트워크를 만들 지역을 선택합니다. **미국 동부 2**를 사용합니다.
8. **서브넷**에서 첫 번째 서브넷 이름 및 주소 범위를 추가합니다. **ManagedInstances** 서브넷을 만들었습니다.
9. 서비스 엔드포인트를 비활성화합니다.

    ![관리되는 인스턴스 네트워크](media/migrate-scenarios-lift-and-shift/network-mi.png)

10. 네트워크를 배포한 후 DNS 설정을 변경해야 합니다. 이 시나리오에서 DNS는 먼저 정적 개인 IP 주소(172.16.3.4) 및 Azure DNS 확인자 168.63.129.16을 사용하여 ID 서브넷에서 도메인 컨트롤러를 가리킵니다.

    ![관리되는 인스턴스 네트워크](media/migrate-scenarios-lift-and-shift/network-mi2.png)


### <a name="set-up-routing"></a>라우팅 설정

1. Azure Portal의 왼쪽 위 모서리에서 **리소스 만들기**를 클릭합니다. **경로 테이블** > **경로 테이블에 만들기** 페이지를 클릭합니다.
2. **경로 테이블 만들기** > **이름**에서 테이블에 대한 이름을 지정합니다.
3. 구독, 리소스 그룹 및 위치를 선택합니다. BGP를 비활성화로 두고, **만들기**를 클릭합니다.
    ![경로 테이블](media/migrate-scenarios-lift-and-shift/route-table.png)

4. 경로 테이블을 만든 후에 열고, **경로** > **+추가**를 클릭합니다.
5. **경로 추가** > **이름**에서 경로 이름을 지정합니다.
6. **주소 접두사**에서 0.0.0.0/0을 지정합니다.
7. **다음 홉 유형**에서 **인터넷**을 선택합니다. 그런 후 **OK**를 클릭합니다.

     ![경로 테이블](media/migrate-scenarios-lift-and-shift/route-table2.png)


### <a name="apply-the-route-to-the-managed-instance-subnet"></a>관리되는 인스턴스 서브넷에 경로 적용

1. 앞에서 만든 가상 네트워크를 엽니다. **서브넷** > 서브넷 이름을 클릭합니다.
2. **경로 테이블** > 테이블 이름을 클릭합니다. 그런 다음 **Save**를 클릭합니다.

     ![경로 테이블](media/migrate-scenarios-lift-and-shift/route-table3.png)

### <a name="create-a-managed-instance"></a>관리되는 인스턴스 만들기

1. **리소스 만들기**를 클릭하고, **관리되는 인스턴스**를 찾고 **Azure SQL Database 관리되는 인스턴스(미리 보기)** 를 선택합니다.
2. **만들기**를 클릭합니다.
3. **SQL 관리되는 인스턴스**에서 구독을 선택하고, **미리 보기 약관**이 **승인됨**으로 표시되는지 확인합니다.
4. **관리되는 인스턴스 이름**에서 이름을 지정합니다. 
5. 인스턴스에 대한 관리 권한으로 사용자 이름 및 암호를 지정합니다.
6. 인스턴스에 대한 리소스 그룹, 위치 및 가상 네트워크를 선택합니다.
7. **가격 책정 계층**을 클릭하여 계산 및 저장소의 크기를 조정합니다. 기본적으로 인스턴스는 32GB의 저장소 공간을 무료로 가져옵니다(2018년 4월).
8. 저장소, 가상 코어 수를 지정합니다.
9. **적용**을 클릭하여 설정을 저장하고, **만들기**를 클릭하여 관리되는 인스턴스를 배포합니다. 배포 상태를 보려면 **알림** 및 **배포 진행 중**을 클릭합니다.

    ![관리되는 인스턴스](media/migrate-scenarios-lift-and-shift/mi-1.png)

관리되는 인스턴스 배포가 완료되면 ContosoRG 리소스 그룹에 두 개의 새 리소스가 표시됩니다. 관리 되는 인스턴스에 대한 가상 클러스터 및 SQL 관리되는 인스턴스 모두 미국 동부 2 위치에 있습니다.

![관리되는 인스턴스](media/migrate-scenarios-lift-and-shift/managed-instance2.png)


## <a name="step-3-get-a-sas-uri-for-dms"></a>3단계: DMS에 대한 SAS URI 가져오기

DMS에서 데이터베이스를 Azure SQL Database 관리되는 인스턴스로 마이그레이션하는 데 사용되는 백업 파일을 업그레이드하기 위해 저장소 계정 컨테이너에 액세스할 수 있도록 SAS URI를 가져옵니다. 

1. 저장소 탐색기(미리 보기)를 엽니다.
2. 왼쪽 창에서 SAS를 가져오려는 Blob 컨테이너가 들어 있는 저장소 계정을 확장합니다. 저장소 계정의 **Blob 컨테이너**를 확장합니다.
3. 컨테이너를 마우스 오른쪽 단추로 클릭하고, **공유 액세스 서명 가져오기**를 선택합니다.

     ![SAS](media/migrate-scenarios-lift-and-shift/sas-1.png)

4. **공유 액세스 서명**에서 정책, 시작 및 만료 날짜, 표준 시간대, 리소스에 적용할 액세스 수준을 지정합니다. 그런 다음 **Create**를 클릭합니다.

    ![SAS](media/migrate-scenarios-lift-and-shift/sas-2.png)

5. 두 번째 대화 상자는 저장소 리소스에 액세스하는 데 사용할 수 있는 URL 및 쿼리 문자열과 함께 Blob 컨테이너를 표시합니다. **복사**를 선택하여 값을 복사합니다. 안전한 장소에 보관합니다. DMS를 설정할 때 필요합니다.

      ![SAS](media/migrate-scenarios-lift-and-shift/sas-3.png)  

## <a name="step-4-prepare-dms"></a>4단계: DMS 준비

데이터베이스 마이그레이션 공급자를 등록하고, 인스턴스를 만듭니다.

### <a name="register-the-microsoftdatamigration-resource-provider"></a>Microsoft.DataMigration 리소스 공급자 등록

1. 구독에서 **리소스 공급자**를 선택합니다. 
2. "마이그레이션"을 검색한 다음, Microsoft.DataMigration의 오른쪽에서 **등록**을 선택합니다. 


### <a name="create-an-instance"></a>인스턴스 만들기

1. **+ 리소스 만들기**를 선택하고, 'Azure Database Migration Service'를 검색한 다음, 드롭다운 목록에서 **Azure Database Migration Service**를 선택합니다.
2. Azure Database Migration Service(미리 보기) 화면에서 **만들기**를 선택합니다.
3. 서비스, 구독, 리소스 그룹, 가상 네트워크 및 가격 책정 계층의 이름을 지정합니다.
4. **만들기**를 선택하여 서비스를 만듭니다.

    ![DMS](media/migrate-scenarios-lift-and-shift/dms-instance.png)  




## <a name="step-5-prepare-azure-for-the-site-recovery-service"></a>5단계: Site Recovery 서비스를 위한 Azure 준비

### <a name="set-up-a-virtual-network"></a>가상 네트워크 설정

장애 조치(failover) 후 만들어질 때 Azure VM이 배치될 Azure 네트워크 및 복제된 데이터가 저장되는 Azure 저장소 계정이 필요합니다.

- 이 시나리오의 목적을 위해 DMS에 대해 이전에 만든 Azure 네트워크를 사용합니다.
- 사용하는 네트워크는 Site Recovery 자격 증명 모음과 같은 지역에 있어야 합니다.


### <a name="create-an-azure-storage-account"></a>Azure 저장소 계정 만들기

Site Recovery는 VM 데이터를 Azure 저장소에 복제합니다. 온-프레미스에서 Azure로 장애 조치할 때 저장소에서 Azure VM이 만들어집니다.

1. [Azure Portal](https://portal.azure.com) 메뉴에서 **새로 만들기** > **Storage** > **저장소 계정**을 선택합니다.
2. **저장소 계정 만들기**에서 계정의 이름을 입력합니다. 이 자습서에서는 **contosovmsacct1910171607**이라는 이름이 사용됩니다. 이름은 Azure 내에서 고유해야 하며 길이가 3자에서 24자 사이이고 숫자 및 소문자만 포함해야 합니다.
3. **배포 모델**에서 **Resource Manager**를 선택합니다.
4. **계정 종류**에서 **범용**을 선택합니다. **성능**에서 **표준**을 선택합니다. Blob Storage를 선택하지 마세요.
5. **복제**에서 저장소 중복에 대해 기본 **읽기 액세스 지역 중복 저장소**를 선택합니다.
6. **구독**에서 새 저장소 계정을 만들려는 구독을 선택합니다.
7. **리소스 그룹**에서 새 리소스 그룹을 입력합니다. Azure 리소스 그룹은 Azure 리소스가 배포 및 관리되는 논리적 컨테이너입니다. 이 자습서에서는 **ContosoRG**라는 이름이 사용됩니다.
8. **위치**에서 저장소 계정에 대한 지리적 위치를 선택합니다. 저장소 계정은 Recovery Services 자격 증명 모음과 동일한 영역에 있어야 합니다. 이 시나리오의 경우 **미국 동부 2** 지역을 사용합니다.

   ![저장소 계정 만들기](media/migrate-scenarios-lift-and-shift/create-storageacct.png)

9. **만들기**를 선택하여 저장소 계정을 만듭니다.

### <a name="create-a-recovery-services-vault"></a>Recovery Services 자격 증명 모음 만들기

1. Azure Portal에서 **리소스 만들기** > **모니터링 + 관리** > **Backup 및 Site Recovery**를 선택합니다.
2. **이름**에 자격 증명 모음을 식별하기 위한 이름을 입력합니다. 이 자습서에서는 **ContosoVMVault**가 사용됩니다.
3. **리소스 그룹**에서 **contosoRG**라는 기존 리소스 그룹을 선택합니다.
4. **위치**에서 Azure 지역 **미국 동부 2**를 입력합니다.
5. 대시보드에서 자격 증명 모음에 빠르게 액세스하려면 **대시보드에 고정** > **만들기**를 선택합니다.
새 자격 증명 모음이 **대시보드** > **모든 리소스** 및 주 **Recovery Services 자격 증명 모음** 페이지에 표시됩니다.

## <a name="step-6-prepare-on-premises-vmware-for-site-recovery"></a>6단계: Site Recovery를 위해 온-프레미스 VMware 준비

Site Recovery를 위해 VMware를 준비하려면 다음을 수행해야 합니다.

- VM 검색을 자동화하기 위해 vCenter 서버 또는 vSphere ESXi 호스트에서 계정 준비
- VMware VM에 모바일 서비스 자동 설치를 위한 계정 준비
- 장애 조치(failover) 후 Azure VM에 연결하려는 경우 온-프레미스 VM을 준비합니다.


### <a name="prepare-an-account-for-automatic-discovery"></a>자동 검색용 계정 준비

Site Recovery에서는 다음 작업을 위해 VMware 서버에 액세스해야 합니다.

- VM을 자동으로 검색합니다. 최소한 읽기 전용 계정이 필요합니다.
- 복제, 장애 조치 및 장애 복구를 오케스트레이션합니다. 디스크 만들기 및 제거, VM 전원 켜기와 같은 작업을 실행할 수 있는 계정이 필요합니다.

계정을 만들려면 다음을 수행합니다.

1. 전용 계정을 사용하려면 vCenter 수준에서 역할을 만듭니다. 역할에 **Azure_Site_Recovery**와 같은 이름을 지정합니다.
2. 아래 표에 요약된 권한을 역할에 할당합니다.
3. vCenter 서버 또는 vSphere 호스트에서 사용자를 만듭니다. 사용자에게 역할을 할당합니다.

**Task** | **역할/권한** | **세부 정보**
--- | --- | ---
**VM 검색** | 읽기 전용 사용자(최소)<br/><br/> 데이터 센터 개체 –> 자식 개체에 전파, role=Read 전용 | 사용자는 데이터 센터 수준에서 할당되며 데이터 센터의 모든 개체에 대한 액세스 권한이 있습니다.<br/><br/> 액세스를 제한하려는 경우 **자식에 전파** 개체를 사용하여 **액세스 권한 없음** 역할을 자식 개체(vSphere 호스트, 데이터 저장소, VM 및 네트워크)에 할당합니다.
**전체 복제, 장애 조치, 장애 복구** |  필요한 권한이 있는 역할(Azure_Site_Recovery)을 만든 다음 VMware 사용자 또는 그룹에 이 역할을 할당합니다.<br/><br/> 데이터 센터 개체 –> 자식 개체에 전파, role=Azure_Site_Recovery<br/><br/> 데이터 저장소 -&gt; 공간 할당, 데이터 저장소 찾아보기, 낮은 수준 파일 작업, 파일 제거, 가상 머신 파일 업데이트<br/><br/> 네트워크 -> 네트워크 할당<br/><br/> 리소스 -> 리소스 풀에 VM 할당, 전원이 꺼진 VM 마이그레이션, 전원이 켜진 VM 마이그레이션<br/><br/> 태스크 -> 만들기 태스크, 업데이트 태스크<br/><br/> 가상 머신 -&gt; 구성<br/><br/> 가상 머신 -&gt; 상호 작용 -&gt; 질문 응답, 장치 연결, CD 미디어 구성, 플로피 미디어 구성, 전원 끄기, 전원 켜기, VMware 도구 설치<br/><br/> 가상 머신 -&gt; 인벤토리 -&gt; 만들기, 등록, 등록 취소<br/><br/> 가상 머신 -&gt; 프로비전 -&gt; 가상 머신 다운로드 허용, 가상 머신 파일 업로드 허용<br/><br/> 가상 머신 -&gt; 스냅숏 -&gt; 스냅숏 제거 | 사용자는 데이터 센터 수준에서 할당되며 데이터 센터의 모든 개체에 대한 액세스 권한이 있습니다.<br/><br/> 액세스를 제한하려는 경우 **자식에 전파** 개체를 사용하여 **액세스 권한 없음** 역할을 자식 개체(vSphere 호스트, 데이터 저장소, VM 및 네트워크)에 할당합니다.

### <a name="prepare-an-account-for-mobility-service-installation"></a>모바일 서비스 설치를 위한 계정 준비

복제하려는 VM에 모바일 서비스를 설치해야 합니다.

- Site Recovery는 VM에 대한 복제를 활성화할 때 이 구성 요소의 자동 강제 설치 작업을 수행할 수 있습니다.
- 자동 강제 설치를 위해 Site Recovery에서 VM에 액세스하는 데 사용할 계정을 준비해야 합니다.
- Azure 콘솔에서 복제를 설정할 때 이 계정을 지정합니다.
- VM에 설치할 수 있는 권한이 있는 도메인 또는 로컬 계정이 필요합니다.


### <a name="prepare-to-connect-to-azure-vms-after-failover"></a>장애 조치(Failover) 후 Azure VM에 연결할 준비

Azure로 장애 조치(failover) 후 온-프레미스 네트워크의 Azure에서 복제된 VM에 연결할 수 있습니다.

장애 조치 후 RDP를 사용하여 Windows VM에 연결하려면 다음을 수행합니다.

1. 인터넷을 통해 액세스하려면 장애 조치 전에 온-프레미스 VM에서 RDP를 활성화합니다. **공용** 프로필에 대한 TCP 및 UDP 규칙이 추가되었는지와 해당 RDP가 **Windows 방화벽** > **허용되는 앱**에서 모든 프로필에 대해 허용되는지 확인합니다.
2. 사이트 간 VPN을 통해 액세스하려면 온-프레미스 컴퓨터에서 RDP를 활성화합니다. RDP가 **Windows 방화벽** -> **허용되는 앱 및 기능**에서 **도메인 또는 사설** 네트워크에 대해 허용되어야 합니다.
3. 운영 체제의 SAN 정책이 **OnlineAll**로 설정되어 있는지 확인합니다. [자세히 알아보기](https://support.microsoft.com/kb/3031135).
4. 장애 조치를 트리거할 때 VM에 보류 중인 Windows 업데이트가 없어야 합니다. 있는 경우 업데이트가 완료될 때까지 가상 머신에 로그인할 수 없습니다.
5. 장애 조치 후 Microsoft Azure VM에서 **부트 진단**을 확인하여 VM의 스크린샷을 검토합니다. 연결할 수 없는 경우 VM이 실행 중인지 확인하고 해당 [문제 해결 팁](http://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx)(영문)을 검토합니다.



## <a name="step-7-replicate-vms-with-site-recovery"></a>7단계: Site Recovery를 사용하여 VM 복제

### <a name="select-a-replication-goal"></a>복제 목표 선택

1. **Recovery Services 자격 증명 모음**에서 자격 증명 모음 이름인 **ContosoVMVault**를 선택합니다.
2. **시작**에서 Site Recovery를 선택합니다. 그런 다음, **인프라 준비**를 선택합니다.
3. **보호 목표** > **컴퓨터가 있는 위치**에서 **온-프레미스**를 선택합니다.
4. **컴퓨터를 복제할 위치를 선택하세요.** 에서 **Azure**를 선택합니다.
5. **컴퓨터가 가상화된 경우**에서 **예, VMware vSphere 하이퍼바이저 사용**을 선택합니다. 그런 다음 **확인**을 선택합니다.

    ![OVF 템플릿](./media/migrate-scenarios-lift-and-shift/replication-goal.png)


### <a name="confirm-deployment-planning"></a>배포 계획 확인

**배포 계획**의 드롭다운 목록에서 **예, 완료함**을 클릭합니다.

### <a name="set-up-the-source-environment"></a>원본 환경 설정


원본 환경을 설정하려면 온-프레미스 Site Recovery 구성 요소를 호스트할 단일 고가용성 온-프레미스 컴퓨터가 필요합니다. 구성 요소에는 구성 서버 및 프로세스 서버가 포함됩니다.

- 구성 서버는 온-프레미스와 Azure 간의 통신을 조정하여 데이터 복제를 관리합니다.
- 프로세스 서버는 복제 게이트웨이의 역할을 합니다. 복제 데이터를 수신하고 캐싱, 압축 및 암호화를 사용하여 최적화하며 복제 데이터를 Azure 저장소로 전송합니다.
- 또한 프로세스 서버는 복제하려는 VM에 모바일 서비스를 설치하고 온-프레미스 VMware VM의 자동 검색을 수행합니다.


항상 사용 가능한 VMware VM으로 구성 서버를 설정하려면:

- 준비된 OVF(Open Virtualization Format) 템플릿을 다운로드합니다.
- 템플릿을 VMware로 가져와 VM을 만듭니다.
- 구성 서버를 설정하고, 자격 증명 모음에 등록합니다. 

등록이 완료되면 Site Recovery가 온-프레미스 VMware VM을 검색합니다.



#### <a name="download-the-vm-template"></a>VM 템플릿 다운로드

1. 자격 증명 모음에서 **인프라 준비** > **원본**으로 이동합니다.
2. **원본 준비**에서 **+구성 서버**를 선택합니다.

    ![템플릿 다운로드](./media/migrate-scenarios-lift-and-shift/new-cs.png)

4. **서버 추가**에서 **VMware에 대한 구성 서버**가 **서버 형식**에 표시되는지 확인합니다.
2. 구성 서버에 대한 OVF 템플릿을 다운로드합니다.

    ![OVF 다운로드](./media/migrate-scenarios-lift-and-shift/add-cs.png)

  > [!TIP]
  [Microsoft 다운로드 센터](https://aka.ms/asrconfigurationserver)에서 최신 버전의 구성 서버 템플릿을 직접 다운로드할 수 있습니다.

#### <a name="import-the-template-in-vmware"></a>VMware에서 템플릿 가져오기

1. VMWare vSphere 클라이언트를 사용하여 VMware vCenter 서버에 로그인합니다.
2. **파일** 메뉴에서 **OVF 템플릿 배포**를 선택하여 OVF 템플릿 배포 마법사를 시작합니다. 

     ![OVF 템플릿](./media/migrate-scenarios-lift-and-shift/vcenter-wizard.png)

3. **원본 선택**에서 다운로드한 OVF의 위치를 입력합니다.
4. **리뷰 세부 정보**에서 **다음**을 선택합니다.
5. **이름 및 폴더 선택**과 **구성 선택**에서 기본 설정을 그대로 적용합니다.
6. 최상의 성능을 발휘할 수 있도록 **저장소 선택**의 **가상 디스크 형식 선택**에서 **씩 프로비전 Eager Zeroed**를 선택합니다.
7. 마법사 페이지의 나머지 부분에서는 기본 설정을 적용합니다.
8. **완료 준비**에서:

    * 기본 설정을 사용하여 VM을 설정하려면 **배포 후 전원 켜기** > **마침**을 선택합니다.

    * 추가 네트워크 인터페이스를 추가하려면 **배포 후 전원 켜기**의 선택을 취소합니다. 그런 다음, **마침**을 선택합니다. 기본적으로 구성 서버 템플릿은 단일 NIC를 사용하여 배포됩니다. 배포 후 NIC를 추가할 수 있습니다.



#### <a name="register-the-configuration-server"></a>구성 서버 등록 

1. VMWare vSphere 클라이언트 콘솔에서 VM을 켭니다.
2. VM이 Windows Server 2016 설치 환경으로 부팅됩니다. 사용권 계약에 동의하고 관리자 암호를 입력합니다.
3. 설치가 완료되면 VM에 관리자 권한으로 로그인합니다.
4. 처음으로 로그인하면 Azure Site Recovery 구성 도구가 시작됩니다.
5. Site Recovery에 구성 서버를 등록하는 데 사용된 이름을 입력합니다. 그런 후 **다음**을 선택합니다.

    ![OVF 템플릿](./media/migrate-scenarios-lift-and-shift/config-server-register1.png)

6. VM이 Azure에 연결할 수 있는지 도구에서 확인합니다. 연결이 설정되면 **로그인**을 선택하여 Azure 구독에 로그인합니다. 구성 서버를 등록하려는 자격 증명 모음에 자격 증명이 액세스할 수 있어야 합니다.

    ![OVF 템플릿](./media/migrate-scenarios-lift-and-shift/config-server-register2.png)

7. 도구에서 몇 가지 구성 작업을 수행한 후 다시 부팅합니다.
8. 컴퓨터에 다시 로그인합니다. 구성 서버 관리 마법사가 자동으로 시작됩니다.

#### <a name="configure-settings-and-add-the-vmware-server"></a>설정 구성 및 VMware 서버 추가

1. 구성 서버 관리 마법사에서 **연결 설정**을 선택한 다음, 복제 트래픽을 수신할 NIC를 선택합니다. 그런 다음 **저장**을 선택합니다. 구성된 후에는 이 설정을 변경할 수 없습니다.
2. **Recovery Services 자격 증명 모음 선택**에서 Azure 구독을 선택한 다음, 관련 리소스 그룹 및 자격 증명 모음을 선택합니다.

    ![자격 증명 모음](./media/migrate-scenarios-lift-and-shift/cswiz1.png) 

3. **타사 소프트웨어 설치**에서 사용권 계약에 동의합니다. **다운로드 및 설치**를 선택하여 MySQL 서버를 설치합니다.
4. **VMware PowerCLI 설치**를 선택합니다. 이렇게 하기 전에 모든 브라우저 창을 닫아야 합니다. 그런 다음, **계속**을 선택합니다.
5. 계속하기 전에 **어플라이언스 구성 유효성 검사**에서 필수 구성 요소가 확인됩니다.
6. **vCenter Server/vSphere ESXi 서버 구성**에 복제하려는 VM이 있는 vCenter 서버 또는 vSphere 호스트의 FQDN 또는 IP 주소를 지정합니다. 서버가 수신 대기하는 포트를 입력합니다. 자격 증명 모음의 VMware 서버에 사용할 이름을 입력합니다.
7. 구성 서버가 VMware 서버에 연결하는 데 사용할 자격 증명을 입력합니다. Site Recovery는 이러한 자격 증명을 사용하여 복제에 사용 가능한 VMware VM을 자동으로 검색합니다. **추가**를 선택한 다음, **계속**을 선택합니다.

    ![vCenter](./media/migrate-scenarios-lift-and-shift/cswiz2.png)

8. **가상 머신 자격 증명 구성**에서, 복제가 사용되면 컴퓨터에 모바일 서비스를 자동으로 설치하는 데 사용할 사용자 이름 및 암호를 입력합니다. Windows 컴퓨터의 경우 복제하려는 컴퓨터에 대한 로컬 관리자 권한이 필요합니다. 

    ![vCenter](./media/migrate-scenarios-lift-and-shift/cswiz2.png)

7. **구성 완료**를 선택하여 등록을 완료합니다. 
8. 등록이 완료되면 Azure Portal에서 구성 서버 및 VMware 서버가 자격 증명 모음의 **원본** 페이지에 표시되는지 확인합니다. 그런 다음, **확인**을 선택하여 대상 설정을 구성합니다.
9. Site Recovery는 지정한 설정을 사용하여 VMware 서버에 연결하고 VM을 검색합니다.

> [!NOTE]
> 포털에 계정 이름이 표시되는 데 15분 이상 걸릴 수 있습니다. 즉시 업데이트하려면 **구성 서버** > ***서버 이름*** > **서버 새로 고침**을 선택합니다.

### <a name="set-up-the-target-environment"></a>대상 환경 설정

대상 리소스를 선택하고 확인합니다.

1. **인프라 준비** > **대상**에서 사용하려는 Azure 구독을 선택합니다.
2. 대상 배포 모델의 경우 **Azure Resource Manager**를 선택합니다.

3. Site Recovery가 호환되는 Azure 저장소 계정 및 네트워크가 하나 이상 있는지 확인합니다.


### <a name="create-a-replication-policy"></a>복제 정책 만들기

1. **인프라 준비** > **복제 설정** > **복제 정책**에서 **만들기 및 연결**을 클릭합니다.
1. **복제 정책 만들기**에서 **VMwareRepPolicy**라는 정책 이름을 지정합니다.
2. **RPO 임계값**에서 기본값인 60분을 사용합니다. 이 값은 복구 지점을 만드는 빈도를 지정합니다. 연속 복제가 이 제한을 초과하면 경고가 생성됩니다.
3. **복구 지점 보존**에서 각 복구 지점에 대한 보존 기간으로 기본값인 24시간을 사용합니다. 이 자습서에서는 72시간을 사용합니다. 복제된 VM은 하나의 시간대에서 임의의 시점으로 복구할 수 있습니다.
4. **앱 일치 스냅숏 빈도**에서 앱 일치 스냅숏을 만드는 빈도로 기본값인 60분을 사용합니다. **확인**을 선택하여 정책을 만듭니다.

    ![복제 정책 만들기](./media/migrate-scenarios-lift-and-shift/replication-policy.png)

5. 정책은 구성 서버와 자동으로 연결됩니다. 

    ![복제 정책 연결](./media/migrate-scenarios-lift-and-shift/replication-policy2.png)



### <a name="enable-replication"></a>복제 사용

이제 VM 복제를 시작합니다. VM에 대한 복제를 활성화하면 Site Recovery에서 각 VM에 모바일 서비스를 설치합니다. 


1. **응용 프로그램 복제** > **원본**을 선택합니다.
2. **원본**에서 구성 서버를 선택합니다.
3. **컴퓨터 형식**에서 **Virtual Machines**를 선택합니다.
4. **vCenter/vSphere 하이퍼바이저**에서 vSphere 호스트를 관리하는 vCenter Server를 선택하거나 해당 호스트를 선택합니다.
5. 프로세스 서버(구성 서버)를 선택합니다. 그런 다음 **확인**을 선택합니다.

    ![복제 사용](./media/migrate-scenarios-lift-and-shift/enable-replication1.png)

1. **대상**에서 장애 조치(Failover)된 VM을 만들려는 구독 및 리소스 그룹을 선택합니다. 장애 조치 VM에 대해 Azure(클래식 또는 Resource Manager)에서 사용할 배포 모델을 선택합니다.
2. 데이터 복제에 사용할 Azure 저장소 계정을 선택합니다.
3. 장애 조치(failover) 후 Azure VM이 생성될 때 연결될 Azure 네트워크 및 서브넷을 선택합니다.
4. 컴퓨터마다 Azure 네트워크를 선택하려면 **나중에 구성** 을 선택합니다. 네트워크가 없는 경우 **만들어야** 합니다.
5. 가상 네트워크에서 서브넷을 선택합니다. 그런 다음 **확인**을 선택합니다.

    ![복제 사용](./media/migrate-scenarios-lift-and-shift/enable-replication2.png)

6. **Virtual Machines** > **가상 머신 선택**에서 원하는 VM(WEBVM)을 선택합니다. 복제를 활성화할 수 있는 컴퓨터만 선택할 수 있습니다. 

    ![복제 사용](./media/migrate-scenarios-lift-and-shift/enable-replication3.png)

7. 추가하는 VM을 모니터링하려면, **구성 서버** > **마지막 연락**에서 VM을 마지막으로 검색한 시간을 확인합니다. 예약된 검색을 기다리지 않고 VM을 추가하려면 구성 서버를 강조 표시하고(클릭하지 않음) **새로 고침**을 선택합니다. 변경 내용이 적용되어 포털에 표시되는 데 15분 이상 걸릴 수 있습니다.
8. **속성** > **속성 구성**에서 프로세스 서버가 자동으로 컴퓨터에 모바일 서비스를 설치하는 데 사용할 계정을 선택합니다. 

    ![복제 사용](./media/migrate-scenarios-lift-and-shift/enable-replication4.png)

9. **복제 설정** > **복제 설정 구성**에서 올바른 복제 정책이 선택되어 있는지 확인합니다.
10. **복제 사용**을 선택합니다.

    ![복제 사용](./media/migrate-scenarios-lift-and-shift/enable-replication5.png)

11. **설정** > **작업** > **Site Recovery 작업**에서 **보호 사용** 작업의 진행률을 추적합니다. 
12. **보호 완료** 작업이 실행된 후에는 컴퓨터가 장애 조치(failover)를 수행할 준비가 되고, **개요** > **기본 정보**에 표시됩니다.

    ![Essentials](./media/migrate-scenarios-lift-and-shift/essentials.png)


## <a name="step-8-migrate-the-database-with-dms"></a>8단계: DMS를 사용하여 데이터베이스 마이그레이션

DMS 프로젝트를 만들고, 마이그레이션을 실행합니다.


### <a name="create-a-database-migration-project"></a>데이터베이스 마이그레이션 프로젝트 만들기

1. Azure Portal에서 DMS 리소스를 찾아 엽니다.
2. **+ 새 마이그레이션 프로젝트**를 선택합니다.
3. **새 마이그레이션 프로젝트**에서 프로젝트에 대한 이름을 지정합니다.
4. **원본 서버 유형**에서 **SQL Server**를 선택합니다. **대상 서버 유형**에서 **Azure SQL Database 관리되는 인스턴스**를 선택합니다.
5. **만들기**를 클릭하여 프로젝트를 만듭니다.
6. **원본 세부 정보**에서 온-프레미스 원본 SQL Server에 대한 연결 세부 정보를 지정합니다. **저장**을 클릭합니다.
7. **대상 세부 정보**에서 대상에 대한 연결 세부 정보를 지정합니다. 대상은 온-프레미스 데이터베이스가 마이그레이션될 미리 프로비저닝된 Azure SQL Database 관리되는 인스턴스입니다. 그런 다음 **Save**를 클릭합니다.
8. **프로젝트 요약**에서 마이그레이션 프로젝트와 연결된 세부 정보를 검토하고 확인합니다.

    ![DMS 프로젝트](./media/migrate-scenarios-lift-and-shift/dms-project.png)

### <a name="migrate-the-database"></a>데이터베이스 마이그레이션

1. 프로젝트를 만든 후 마이그레이션 마법사가 나타납니다.
2. 원본 및 대상 서버에 대한 자격 증명을 지정하고, **저장**을 클릭합니다. 마법사는 온-프레미스 SQL Server에 로그인하려고 시도합니다.

    ![DMS 마법사](./media/migrate-scenarios-lift-and-shift/dms-wiz1.png)

3. **대상 데이터베이스에 매핑**에서 마이그레이션하려는 원본 데이터베이스를 선택합니다. 그런 다음 **Save**를 클릭합니다.

    ![DMS 마법사](./media/migrate-scenarios-lift-and-shift/dms-wiz2.png)

4. **대상 세부 정보**에서 **대상 세부 정보를 알고 있음**을 선택합니다. 자격 증명과 함께 SQL 관리되는 인스턴스의 DNS 이름을 제공합니다. 그런 다음 **Save**를 클릭합니다.

    ![DMS 마법사](./media/migrate-scenarios-lift-and-shift/dms-wiz3.png)

5. 저장된 프로젝트에서 **+새 작업**을 선택합니다. 그런 다음, **마이그레이션 실행**을 선택합니다.
6. 메시지가 표시되면 원본 및 대상 서버에 대한 자격 증명을 입력합니다. 그런 다음 **Save**를 클릭합니다.
7. **대상 데이터베이스에 매핑**에서 마이그레이션하려는 원본 데이터베이스를 선택합니다. 그런 다음 **Save**
8. **마이그레이션 설정 구성** > **서버 백업 위치**에서 온-프레미스 컴퓨터에서 만든 네트워크 공유를 지정합니다. DMS에서 이 공유에 원본 백업을 사용합니다.  원본 SQL Server 인스턴스를 실행하는 서비스 계정에는 이 공유에 대한 쓰기 권한이 있어야 합니다.
9. DMS에서 복원을 위해 Azure 저장소 컨테이너에 백업 파일을 업로드하기 위해 가장할 수 있는 사용자 이름 및 암호를 지정합니다.
10. Azure SQL Database 관리되는 인스턴스로 마이그레이션하는 데 사용되며 서비스에서 백업 파일을 업로드하는 저장소 계정 컨테이너에 대한 액세스 권한이 있는 DMS를 제공하는 SAS URI를 지정합니다.  그런 다음 **Save**를 클릭합니다.
11. **마이그레이션 요약**에서 마이그레이션 작업에 대한 이름을 지정하고 **마이그레이션을 실행합니다**.
12. 프로젝트 > **개요**에서 마이그레이션 상태를 모니터링합니다. 마이그레이션이 완료되면 대상 데이터베이스가 관리되는 인스턴스에 존재하는지 확인합니다.


## <a name="step-9-migrate-vms-with-site-recovery"></a>9단계: Site Recovery를 사용하여 VM 마이그레이션

전체 마이그레이션을 실행하기 전에 모든 항목이 예상대로 작동하는지 확인하기 위해 테스트 장애 조치(failover)를 실행하는 것이 좋습니다. 테스트 장애 조치(failover)를 실행하면 다음 상황이 발생합니다.

1. 필수 구성 요소 확인은 마이그레이션에 필요한 모든 조건이 충족되었는지 확인하기 위해 실행합니다.
2. 장애 조치(failover)는 데이터를 처리하며 이 데이터에서 Azure VM이 만들어질 수 있습니다. 최신 복구 지점을 선택하는 경우 해당 데이터에서 복구 지점이 만들어집니다.
3. 이전 단계에서 처리한 데이터를 사용하여 Azure VM이 만들어집니다.

### <a name="run-a-test-failover"></a>테스트 장애 조치(failover) 실행

1. **보호된 항목**에서 **복제된 항목** > VM을 클릭합니다.
2. VM 속성에서 **+테스트 장애 조치(failover)** 를 클릭합니다.

    ![테스트 장애 조치](./media/migrate-scenarios-lift-and-shift/test-failover.png)

3. **최신 처리**를 선택하여 사용 가능한 최신 시점으로 VM을 장애 조치(failover)합니다. 타임스탬프가 표시됩니다. 이 옵션을 사용하면 데이터를 처리하는 데 시간을 소비하지 않으므로 낮은 RTO(복구 시간 목표)가 제공됩니다.
2. **테스트 장애 조치(Failover)** 에서 장애 조치(Failover)가 발생한 후에 Azure VM이 연결될 대상 Azure 네트워크를 선택합니다.
3. **확인**을 클릭하여 장애 조치(failover)를 시작합니다. 자격 증명 모음 > **설정** > **작업** > **테스트 장애 조치(failover)** 에서 진행률을 추적할 수 있습니다.
4. 장애 조치가 완료되면 Azure Portal > **Virtual Machines**에서 Azure VM 복제본이 표시됩니다. VM의 크기가 적당하고, VM이 올바른 네트워크에 연결되어 있으며 실행 중인지 확인합니다. 이제 Azure에서 복제된 VM에에 연결할 수 있습니다.
5. 테스트 장애 조치(failover) 중에 만든 Azure VM을 삭제하려면 VM에서 **테스트 장애 조치(failover) 정리**를 클릭합니다. **참고**에서 테스트 장애 조치와 관련된 모든 관측 내용을 기록하고 저장합니다.

### <a name="migrate-the-machines"></a>컴퓨터 마이그레이션

테스트 장애 조치(failover)가 예상대로 작동하는지 확인한 후 Azure로 마이그레이션하기 위한 복구 계획을 만듭니다.

### <a name="create-a-recovery-plan"></a>복구 계획 만들기

1. 자격 증명 모음에서 **복구 계획(Site Recovery)** > **+복구 계획**을 선택합니다.
2. **복구 계획 만들기**에서 계획의 이름을 지정합니다.
3. 원본 구성 서버 및 Azure를 대상으로 선택합니다. **Resource Manager**를 배포 모델로 선택합니다. 원본 위치에는 장애 조치(failover) 및 복구를 사용하도록 설정한 컴퓨터가 있어야 합니다. 
4. **항목 선택**에서 컴퓨터(WEBVM)를 계획에 추가합니다. 그런 후 **OK**를 클릭합니다.
5. **확인**을 클릭하여 계획을 만듭니다.

    ![복구 계획](./media/migrate-scenarios-lift-and-shift/recovery-plan1.png)

### <a name="run-a-failover"></a>장애 조치(Failover) 실행

1. **복구 계획**에서 계획 > **장애 조치(failover)** 를 클릭합니다.
2. **장애 조치(failover)** > **복구 지점**에서 최신 복구 지점을 선택합니다.
3. 암호화 키 설정은 이 시나리오와 관련이 없습니다.
4. **장애 조치(failover)를 시작하기 전에 컴퓨터를 종료합니다.** 를 선택합니다. Site Recovery는 장애 조치(failover)를 트리거하기 전에 원본 가상 머신을 종료하려고 시도합니다. 종료가 실패하더라도 장애 조치는 계속됩니다. **작업** 페이지에서 장애 조치 진행 상황 확인을 수행할 수 있습니다.

    ![장애 조치(failover)](./media/migrate-scenarios-lift-and-shift/failover1.png)

5. Azure VM이 예상대로 Azure에 표시되는지 확인합니다.

    ![장애 조치(failover)](./media/migrate-scenarios-lift-and-shift/failover2.png)

6. **복제된 항목**에서 VM를 마우스 오른쪽 단추로 클릭하고 **마이그레이션 완료**를 클릭합니다. 그러면 마이그레이션 프로세스가 완료되고, VM에 대한 복제가 중지되고, VM에 대한 Site Recovery 청구가 중지됩니다.

    ![장애 조치(failover)](./media/migrate-scenarios-lift-and-shift/failover3.png)

### <a name="update-the-connection-string"></a>연결 문자열 업데이트

마이그레이션 프로세스의 마지막 단계는 SQL MI에서 실행되는 마이그레이션된 데이터베이스를 가리키도록 응용 프로그램의 연결 문자열을 업데이트하는 것입니다.

1. **설정** > **연결 문자열**을 클릭하여 Azure Portal에서 새 연결 문자열을 찾습니다.

    ![장애 조치(failover)](./media/migrate-scenarios-lift-and-shift/failover4.png)  

2. SQL MI에 대한 사용자 이름 및 암호로 이 문자열을 업데이트해야 합니다.
3. 문자열을 구성한 후 응용 프로그램의 web.config 파일에 있는 현재 연결 문자열을 교체해야 합니다.
4. 파일 업데이트 및 저장 후 WEBVM에서 IIS를 다시 시작합니다. cmd 프롬프트에서 IISRESET/RESTART를 사용하여 이를 수행할 수 있습니다.
5. IIS를 다시 시작한 후 응용 프로그램에서 이제 SQL MI에서 실행되는 데이터베이스를 사용합니다.
6. 이 시점에서 SQLVM 컴퓨터 온-프레미스는 종료될 수 있으며 마이그레이션이 완료됩니다.



## <a name="conclusion"></a>결론

이 시나리오에서는 다음과 같은 일을 했습니다.

> [!div class="checklist"]
> * DMS를 사용하여 온-프레미스 데이터베이스를 Azure SQL 관리되는 인스턴스로 마이그레이션했습니다.
> * Azure Site Recovery 서비스를 사용하여 온-프레미스 VM을 Azure VM으로 마이그레이션했습니다.
