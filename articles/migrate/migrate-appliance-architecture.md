---
title: Azure 마이그레이션 어플라이언스 아키텍처
description: 서버 평가 및 마이그레이션에 사용되는 Azure 마이그레이션 어플라이언스에 대한 개요를 제공합니다.
ms.topic: conceptual
ms.date: 03/23/2020
ms.openlocfilehash: d55d123bb056b46b5e78dd8ac836eeaf9b42fe70
ms.sourcegitcommit: 0553a8b2f255184d544ab231b231f45caf7bbbb0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2020
ms.locfileid: "80389021"
---
# <a name="azure-migrate-appliance-architecture"></a>Azure 마이그레이션 어플라이언스 아키텍처

이 문서에서는 Azure 마이그레이션 어플라이언스 아키텍처 및 프로세스에 대해 설명합니다. Azure 마이그레이션 어플라이언스는 Azure로 마이그레이션하기 위한 VM 및 물리적 서버를 검색하기 위해 온-프레미스에 배포되는 경량 어플라이언스입니다. 

## <a name="deployment-scenarios"></a>배포 시나리오

Azure 마이그레이션 어플라이언스는 다음 시나리오에서 사용됩니다.

**시나리오** | **도구** | **사용 대상** 
--- | --- | ---
**VM웨어 VM 평가** | Azure 마이그레이션:서버 평가 | VMware VM을 알아보십시오.<br/><br/> 컴퓨터 앱 및 종속성을 검색합니다.<br/><br/> 컴퓨터 메타데이터 및 성능 메타데이터를 수집하고 Azure로 보냅니다.
**VM웨어 VM 마이그레이션(에이전트 없는)** | Azure 마이그레이션:서버 마이그레이션 | VM웨어 VM 자세히 알아보기<br/><br/>  [에이전트 없는 마이그레이션을](server-migrate-overview.md)통해 VMware VM을 복제합니다.
**하이퍼 V VM 평가** | Azure 마이그레이션:서버 평가 | 하이퍼 VM을 검색합니다.<br/><br/> 컴퓨터 메타데이터 및 성능 메타데이터를 수집하고 Azure로 보냅니다.
**물리적 기계** |  Azure 마이그레이션:서버 평가 |  물리적 서버를 검색합니다.<br/><br/> 컴퓨터 메타데이터 및 성능 메타데이터를 수집하고 Azure로 보냅니다.

## <a name="appliance-components"></a>어플라이언스 부품

어플라이언스에는 여러 구성 요소가 있습니다.

- **관리 앱**: 어플라이언스 배포 중에 사용자 입력을 위한 웹 앱입니다. Azure로 마이그레이션할 컴퓨터를 평가할 때 사용됩니다.
- **검색 에이전트**: 에이전트는 컴퓨터 구성 데이터를 수집합니다. Azure로 마이그레이션할 컴퓨터를 평가할 때 사용됩니다. 
- **평가 에이전트**: 에이전트가 성능 데이터를 수집합니다. Azure로 마이그레이션할 컴퓨터를 평가할 때 사용됩니다.
- **DRA 에이전트**: VM 복제를 오케스트레이션하고 복제된 컴퓨터와 Azure 간의 통신을 조정합니다. 에이전트 없는 마이그레이션을 사용하여 VMware VM을 Azure로 복제할 때만 사용됩니다.
- **게이트웨이**: 복제된 데이터를 Azure로 보냅니다. 에이전트 없는 마이그레이션을 사용하여 VMware VM을 Azure로 복제할 때만 사용됩니다.
- **자동 업데이트 서비스**: 어플라이언스 구성 요소를 업데이트합니다(24시간마다 실행).



## <a name="appliance-deployment"></a>어플라이언스 배포

- Azure 마이그레이션 어플라이언스는 [하이퍼 V](how-to-set-up-appliance-hyper-v.md) 또는 [VMware용](how-to-set-up-appliance-vmware.md) 템플릿을 사용하거나 [VMware/Hyper-V](deploy-appliance-script.md)및 물리적 서버에 대한 PowerShell 스크립트 설치 [관리자를](how-to-set-up-appliance-physical.md)사용하여 설정할 수 있습니다. 
- 어플라이언스 지원 요구 사항 및 배포 전제 조건은 [어플라이언스 지원 매트릭스에](migrate-appliance.md)요약되어 있습니다.


## <a name="appliance-registration"></a>어플라이언스 등록

어플라이언스 설정 중에 Azure 마이그레이션에 어플라이언스를 등록하고 테이블에 요약된 작업이 발생합니다.

**작업** | **세부 정보** | **권한을**
--- | --- | ---
**소스 공급자 등록** | 이러한 리소스 공급자는 어플라이언스 설정 중에 선택한 구독에 등록됩니다: Microsoft.OffAzure, Microsoft.Migrate 및 Microsoft.KeyVault.<br/><br/> 리소스 공급자를 등록하면 구독이 리소스 공급자에서 작동하도록 구성됩니다. | 리소스 공급자를 등록하려면 구독에 대한 기여자 또는 소유자 역할이 필요합니다.
**Azure AD 앱 통신 만들기** | Azure Migrate는 어플라이언스에서 실행되는 에이전트와 Azure에서 실행되는 해당 서비스 간의 통신(인증 및 권한 부여)을 위해 Azure Active Directory(Azure AD) 앱을 만듭니다.<br/><br/> 이 앱에는 Azure 리소스 관리자 호출또는 리소스에 대한 RBAC 액세스를 만들 수 있는 권한이 없습니다. | 앱을 만들려면 Azure 마이그레이션에 대한 [이러한 권한이](tutorial-prepare-vmware.md#assign-permissions-to-register-the-appliance) 필요합니다.
**Azure AD 앱-키 자격 증명 모음 만들기** | 이 응용 프로그램은 Azure에 VMware VM의 에이전트없는 마이그레이션을 위해 만들어집니다.<br/><br/> 에이전트 없는 마이그레이션을 위해 사용자의 구독에서 생성된 키 자격 증명 모음에 액세스하는 데만 사용됩니다.<br/><br/> 어플라이언스에서 검색이 시작될 때 Azure 키 자격 증명 모음(고객의 테넌트에서 생성됨)에 RBAC 액세스 권한이 있습니다. | 앱을 만들려면 Azure 마이그레이션에 대한 [이러한 권한이](tutorial-prepare-vmware.md#assign-permissions-to-register-the-appliance) 필요합니다.



## <a name="collected-data"></a>수집된 데이터

모든 배포 시나리오에 대해 클라이언트에서 수집한 데이터는 [어플라이언스 지원 매트릭스에](migrate-appliance.md)요약되어 있습니다.

## <a name="discovery-and-collection-process"></a>검색 및 수집 프로세스

![Architecture](./media/migrate-appliance-architecture/architecture.png)

어플라이언스는 다음 프로세스를 사용하여 vCenter 서버 및 Hyper-V 호스트/클러스터와 통신합니다.

1. **검색 시작**:
    - Hyper-V 어플라이언스에서 검색을 시작하면 WinRM 포트 5985(HTTP) 및 5986(HTTPS)의 Hyper-V 호스트와 통신합니다.
    - VMware 어플라이언스에서 검색을 시작하면 기본적으로 TCP 포트 443의 vCenter 서버와 통신합니다. vCenter 서버가 다른 포트에서 수신절되는 경우 어플라이언스 웹 앱에서 구성할 수 있습니다.
2. **메타데이터 및 성능 데이터 수집:**
    - 어플라이언스는 CIM(공통 정보 모델) 세션을 사용하여 포트 5985 및 5986의 Hyper-V 호스트에서 하이퍼 V VM 데이터를 수집합니다.
    - 어플라이언스는 기본적으로 포트 443과 통신하여 vCenter 서버에서 VM웨어 VM 데이터를 수집합니다.
3. **데이터 보내기**: 어플라이언스는 수집된 데이터를 Azure 마이그레이션 서버 평가로 보내고 Azure는 SSL 포트 443을 통해 서버 마이그레이션을 마이그레이션합니다. 어플라이언스는 인터넷을 통해 Azure에 연결하거나 공용/Microsoft 피어링과 함께 ExpressRoute를 사용할 수 있습니다.
    - 성능 데이터의 경우 어플라이언스는 실시간 사용률 데이터를 수집합니다.
        - 성능 데이터는 VMware의 경우 20초마다, 하이퍼-V의 경우 30초마다 각 성능 메트릭에 대해 수집됩니다.
        - 수집된 데이터는 롤업되어 10분 동안 단일 데이터 포인트를 만듭니다.
        - 최대 사용률 값은 모든 20/30초 데이터 포인트에서 선택되고 평가 계산을 위해 Azure로 전송됩니다.
        - 평가 속성에 지정된 백분위수 값(50/90/95/99)에 따라 10분 포인트는 오름차순으로 정렬되고 적절한 백분위수 값은 평가를 계산하는 데 사용됩니다.
    - 서버 마이그레이션의 경우 어플라이언스는 VM 데이터 수집을 시작하고 이를 Azure로 복제합니다.
4. **평가 및 마이그레이션**: 이제 Azure 마이그레이션 서버 평가를 사용하여 어플라이언스에서 수집한 메타데이터에서 평가를 만들 수 있습니다. 또한 Azure 마이그레이션 서버 마이그레이션을 사용하여 에이전트 없는 VM 복제를 오케스트레이션하기 위해 VMware VM 마이그레이션을 시작할 수도 있습니다.





## <a name="appliance-upgrades"></a>어플라이언스 업그레이드

어플라이언스에서 실행 중인 Azure 마이그레이션 에이전트가 업데이트되면 어플라이언스가 업그레이드됩니다. 기본적으로 어플라이언스에서 자동 업데이트가 활성화되어 있기 때문에 이 작업은 자동으로 수행됩니다. 이 기본 설정을 변경하여 에이전트를 수동으로 업데이트할 수 있습니다.

HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\AzureAppliance "자동 업데이트" 키를 0(DWORD)으로 설정하여 레지스트리에서 자동 업데이트를 끕니다.

 

## <a name="next-steps"></a>다음 단계

어플라이언스 지원 매트릭스를 [검토합니다.](migrate-appliance.md)

