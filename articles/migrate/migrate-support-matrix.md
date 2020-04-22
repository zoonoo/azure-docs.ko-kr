---
title: Azure 마이그레이션 지원 매트릭스
description: Azure 마이그레이션 서비스에 대한 지원 설정 및 제한 사항에 대한 요약을 제공합니다.
ms.topic: conceptual
ms.date: 04/19/2020
ms.author: raynew
ms.openlocfilehash: 44a971894f53a3f31c068b3c3ed4912bc7e00dab
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81680706"
---
# <a name="azure-migrate-support-matrix"></a>Azure 마이그레이션 지원 매트릭스

Azure 마이그레이션 [서비스를](migrate-overview.md) 사용하여 컴퓨터를 평가하고 Microsoft Azure 클라우드로 마이그레이션할 수 있습니다. 이 문서에서는 Azure 마이그레이션 시나리오 및 배포에 대한 일반적인 지원 설정 및 제한 사항에 대해 간략히 요약합니다.

## <a name="supported-assessmentmigration-scenarios"></a>지원되는 평가/마이그레이션 시나리오

이 표에는 지원되는 검색, 평가 및 마이그레이션 시나리오가 요약되어 있습니다.

**배포** | **세부 정보** 
--- | --- 
**앱별 검색** | VMware VM에서 실행되는 앱, 역할 및 기능을 검색할 수 있습니다. 현재 이 기능은 검색으로만 제한됩니다. 평가는 현재 기계 수준에 있습니다. 아직 앱, 역할 또는 기능별 평가를 제공하지 않습니다. 
**온-프레미스 평가** | VMware VM, Hyper-VM 및 물리적 서버에서 실행되는 온-프레미스 워크로드 및 데이터를 평가합니다. Azure 마이그레이션 서버 평가 및 Microsoft 데이터 마이그레이션 도우미(DMA)를 사용 하 여 평가 뿐만 아니라 다른 도구 및 ISV 제품입니다.
**온-프레미스 에서 Azure로 마이그레이션** | 물리적 서버, VM웨어 VM, 하이퍼 VM, 물리적 서버 및 클라우드 기반 VMS에서 실행되는 워크로드 및 데이터를 Azure로 마이그레이션합니다. Azure 마이그레이션 서버 평가 및 Azure 데이터베이스 마이그레이션 서비스(DMS) 및 기타 도구 및 ISV 오퍼링을 사용하여 마이그레이션합니다.

> [!NOTE]
> 현재 ISV 도구는 Azure 정부에서 Azure 마이그레이션에 데이터를 보낼 수 없습니다. 통합 된 Microsoft 도구를 사용하거나 파트너 도구를 독립적으로 사용할 수 있습니다.

## <a name="supported-tools"></a>지원되는 도구

특정 도구 지원은 표에 요약되어 있습니다.

**도구** | **평가** | **마이그레이션** 
--- | --- | ---
Azure Migrate 서버 평가 | [VMware VM,](tutorial-prepare-vmware.md) [하이퍼 VM](tutorial-prepare-hyper-v.md)및 [물리적 서버를 평가합니다.](tutorial-prepare-physical.md) |  사용할 수 없음(NA)
Azure Migrate 서버 마이그레이션 | 해당 없음 | [VM웨어 VM,](tutorial-migrate-vmware.md) [하이퍼 VM](tutorial-migrate-hyper-v.md)및 [물리적 서버를 마이그레이션합니다.](tutorial-migrate-physical-virtual-machines.md)
[Carbonite](https://www.carbonite.com/data-protection-resources/resource/Datasheet/carbonite-migrate-for-microsoft-azure) | 해당 없음 | VMware VM, 하이퍼 VM, 물리적 서버, 퍼블릭 클라우드 워크로드를 마이그레이션합니다. 
[클라우드화](https://www.cloudamize.com/platform#tab-0)| VMware VM, 하이퍼 VM, 물리적 서버, 퍼블릭 클라우드 워크로드를 평가합니다. | 해당 없음
[Corent Technology](https://go.microsoft.com/fwlink/?linkid=2084928) | VMware VM, 하이퍼 VM, 물리적 서버, 퍼블릭 클라우드 워크로드를 평가하고 마이그레이션합니다. |  VMware VM, 하이퍼 VM, 물리적 서버, 퍼블릭 클라우드 워크로드를 마이그레이션합니다.
[Device 42](https://go.microsoft.com/fwlink/?linkid=2097158) | VMware VM, 하이퍼 VM, 물리적 서버, 퍼블릭 클라우드 워크로드를 평가합니다.| 해당 없음
[DMA](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017) | 온-프레미스 SQL Server 데이터베이스를 평가합니다. | 해당 없음
[DMS](https://docs.microsoft.com/azure/dms/dms-overview) | 해당 없음 | SQL 서버, 오라클, MySQL, 포스트그레SQL, 몽고DB를 마이그레이션합니다. 
[Lakeside](https://go.microsoft.com/fwlink/?linkid=2104908) | 가상 데스크톱 인프라(VDI) 평가 | 해당 없음
[Movere](https://go.microsoft.com/fwlink/?linkid=2109528) | VMWare VM, 하이퍼 VM, 젠 VM, 물리적 컴퓨터, 워크스테이션(VDI 포함), 퍼블릭 클라우드 워크로드 평가 | 해당 없음
[랙웨어](https://go.microsoft.com/fwlink/?linkid=2102735) | 해당 없음 | VMWare VM, 하이퍼 VM, 젠 VM, KVM VM, 물리적 컴퓨터, 퍼블릭 클라우드 워크로드 마이그레이션 
[Turbonomic](https://go.microsoft.com/fwlink/?linkid=2094295)  | VMware VM, 하이퍼 VM, 물리적 서버, 퍼블릭 클라우드 워크로드를 평가합니다. | 해당 없음
[UnifyCloud](https://go.microsoft.com/fwlink/?linkid=2097195) | VMware VM, 하이퍼 VM, 물리적 서버, 공용 클라우드 워크로드 및 SQL Server 데이터베이스를 평가합니다. | 해당 없음
[웹 앱 마이그레이션 도우미](https://appmigration.microsoft.com/) | 웹 앱 평가 | 웹 앱을 마이그레이션합니다.


## <a name="azure-migrate-projects"></a>Azure 마이그레이션 프로젝트

**지원** | **세부 정보**
--- | ---
Subscription | 구독에 여러 Azure 마이그레이션 프로젝트를 가질 수 있습니다.
Azure 권한 | Azure 마이그레이션 프로젝트를 만들려면 구독에서 기여자 또는 소유자 권한이 필요합니다.
VMware VM  | 단일 프로젝트에서 최대 35,000개의 VMware VM을 평가합니다.
Hyper-V VM    | 단일 프로젝트에서 최대 35,000개의 Hyper-V VM을 평가합니다.

프로젝트에는 평가 한도까지 VMware VM과 Hyper-VVM이 모두 포함될 수 있습니다.

## <a name="azure-permissions"></a>Azure 권한

Azure 마이그레이션을 위해 Azure를 사용하려면 컴퓨터 평가 및 마이그레이션을 시작하기 전에 이러한 권한이 필요합니다.

**Task** | **사용 권한** | **세부 정보**
--- | --- | ---
Azure Migrate 프로젝트 만들기 | Azure 계정에는 프로젝트를 만들 수 있는 권한이 필요합니다. | [VMware,](tutorial-prepare-vmware.md#assign-permissions-to-create-project) [Hyper-V](tutorial-prepare-hyper-v.md#assign-permissions-to-create-project)또는 물리적 서버에 대해 [설정합니다.](tutorial-prepare-physical.md#assign-permissions-to-create-project)
Azure Migrate 어플라이언스 등록| Azure Migrate는 간단한 [Azure 마이그레이션 어플라이언스를](migrate-appliance.md) 사용하여 Azure 마이그레이션 서버 평가를 사용하여 컴퓨터를 평가하고 Azure 마이그레이션 서버 마이그레이션을 사용하여 VM웨어 VM의 [에이전트 없는 마이그레이션을](server-migrate-overview.md) 실행합니다. 이 어플라이언스는 컴퓨터를 검색하고 메타데이터 및 성능 데이터를 Azure Migrate로 보냅니다.<br/><br/> 등록 하는 동안 등록 공급자 (Microsoft.OffAzure, Microsoft.Migrate 및 Microsoft.KeyVault) 응용 프로그램에서 선택 된 구독에 등록 됩니다., 구독 리소스 공급자와 함께 작동 되도록. 등록하려면 구독에 기여자 또는 소유자 액세스가 필요합니다.<br/><br/> **VMware**-온보딩 하는 동안 Azure 마이그레이션 두 개의 Azure Active Directory (Azure AD) 앱을 만듭니다. 첫 번째 앱은 어플라이언스 에이전트와 Azure 마이그레이션 서비스 간에 통신합니다. 앱에는 Azure 리소스 관리 호출을 하거나 리소스에 대한 RBAC 액세스 권한을 가질 수 있는 권한이 없습니다. 두 번째 앱은 에이전트 없는 VMware 마이그레이션에 대해서만 사용자 구독에서 만든 Azure 키 볼트에 액세스합니다. 에이전트 없는 마이그레이션에서 Azure Migrate는 키 볼트를 만들어 구독의 복제 저장소 계정에 대한 액세스 키를 관리합니다. 어플라이언스에서 검색을 시작할 때 Azure 키 자격 증명 모음(고객 테넌트)에서 RBAC 액세스 권한이 있습니다.<br/><br/> **하이퍼-V**-온보딩 중. Azure 마이그레이션은 하나의 Azure AD 앱을 만듭니다. 앱은 어플라이언스 에이전트와 Azure 마이그레이션 서비스 간에 통신합니다. 앱에는 Azure 리소스 관리 호출을 하거나 리소스에 대한 RBAC 액세스 권한을 가질 수 있는 권한이 없습니다. | [VMware,](tutorial-prepare-vmware.md#assign-permissions-to-register-the-appliance) [Hyper-V](tutorial-prepare-hyper-v.md#assign-permissions-to-register-the-appliance)또는 물리적 서버에 대해 [설정합니다.](tutorial-prepare-physical.md#assign-permissions-to-register-the-appliance)
VMware 에이전트 없는 마이그레이션을 위한 키 자격 증명 모음 만들기 | 에이전트가 없는 Azure 마이그레이션 서버 마이그레이션을 사용하여 VMware VM을 마이그레이션하기 위해 Azure Migrate는 키 자격 증명 모음을 만들어 구독의 복제 저장소 계정에 대한 액세스 키를 관리합니다. 볼트를 만들려면 Azure Migrate 프로젝트가 있는 리소스 그룹에 사용 권한(소유자 또는 기여자 및 사용자 액세스 관리자)을 설정합니다. | 사용 권한을 [설정합니다.](tutorial-prepare-vmware.md#assign-permissions-to-create-a-key-vault)

## <a name="supported-geographies-public-cloud"></a>지원되는 지역(퍼블릭 클라우드)

퍼블릭 클라우드의 여러 지역에서 Azure 마이그레이션 프로젝트를 만들 수 있습니다. 이러한 지역에서만 프로젝트를 만들 수 있지만 다른 대상 위치에 대한 컴퓨터를 평가하거나 마이그레이션할 수 있습니다. 프로젝트 지역은 검색된 메타데이터를 저장하는 데만 사용됩니다.

**지리** | **메타데이터 스토리지 위치**
--- | ---
아시아 태평양 | 동아시아 또는 동남 아시아
오스트레일리아 | 오스트레일리아 동부 또는 오스트레일리아 남동부
브라질 | 브라질 남부
Canada | 캐나다 중부 또는 캐나다 동부
유럽 | 북유럽 또는 서유럽
프랑스 | 프랑스 중부
인도 | 인도 중부 또는 인도 남부
일본 |  일본 동부 또는 일본 서부
한국 | 한국중부또는한국남부
United Kingdom | 영국 남부 또는 영국 서부
미국 | 미국 중부 또는 미국 서부 2


## <a name="supported-geographies-azure-government"></a>지원되는 지역(Azure 정부)

**Task** | **지리** | **세부 정보**
--- | --- | ---
프로젝트 만들기 | 미국 | 메타데이터는 미국 정부 애리조나, 미국 정부 버지니아에 저장됩니다.
대상 평가 | 미국 | 대상 지역: 미국 정부 애리조나, 미국 정부 버지니아, 미국 정부 텍사스
대상 복제 | 미국 | 대상 지역: 미국 법무부 중부, 미국 DoD 동부, 미국 정부 애리조나, 미국 정부 아이오와, 미국 정부 텍사스, 미국 정부 버지니아


## <a name="vmware-assessment-and-migration"></a>VM웨어 평가 및 마이그레이션

VMware VM에 대한 Azure 마이그레이션 서버 평가 및 서버 마이그레이션 지원 매트릭스를 [검토합니다.](migrate-support-matrix-vmware.md)

## <a name="hyper-v-assessment-and-migration"></a>Hyper-V 평가 및 마이그레이션

하이퍼 VM에 대한 Azure 마이그레이션 서버 평가 및 서버 마이그레이션 지원 매트릭스를 [검토합니다.](migrate-support-matrix-hyper-v.md)



## <a name="azure-migrate-versions"></a>Azure Migrate 버전

Azure Migrate 서비스에는 다음과 같은 두 가지 버전이 있습니다.

- **현재 버전**: 이 버전을 사용하여 새 Azure 마이그레이션 프로젝트를 만들고, 온-프레미스 평가를 검색하고, 평가 및 마이그레이션을 오케스트레이션할 수 있습니다. [자세히 알아보기](whats-new.md).
- **이전 버전**: 이전 버전의 Azure 마이그레이션을 사용하는 고객의 경우(온-프레미스 VMware VM에 대한 평가만 지원됨) 이제 현재 버전을 사용해야 합니다. 이전 버전에서는 새 Azure 마이그레이션 프로젝트를 만들거나 새 검색을 수행할 수 없습니다.

## <a name="next-steps"></a>다음 단계

- [VMware VM에서](tutorial-assess-vmware.md) 마이그레이션을 평가합니다.
- 마이그레이션을 위해 [하이퍼 V VM을 평가합니다.](tutorial-assess-hyper-v.md)

