---
title: "Azure에서 VMware VM 복제를 위한 다중 테넌트 지원(CSP 프로그램) | Microsoft Docs"
description: "다중 테넌트 환경에서 Azure Site Recovery를 배포하여 Azure Portal을 사용하는 CSP 프로그램을 통해 온-프레미스 VMware VM(가상 컴퓨터)에서 Azure로 복제, 장애 조치(failover) 및 복구를 오케스트레이션하는 방법에 대해 설명합니다."
services: site-recovery
documentationcenter: 
author: mayanknayar
manager: rochakm
editor: 
ms.assetid: 
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/23/2017
ms.author: manayar
ms.openlocfilehash: 97edbe67c25036dc1156f0f0ca5431a617d7a004
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="multi-tenant-support-in-azure-site-recovery-for-replicating-vmware-virtual-machines-to-azure-through-csp"></a>CSP를 통해 VMware 가상 컴퓨터를 Azure에 복제하기 위해 Azure Site Recovery에서 다중 테넌트 지원

Azure Site Recovery는 테넌트 구독을 위해 다중 테넌트 환경을 지원합니다. 또한 Microsoft CSP(클라우드 솔루션 공급자) 프로그램을 통해 생성 및 관리되는 테넌트 구독에 대한 다중 테넌트도 지원합니다. 이 문서는 다중 테넌트 VMware 및 Azure 연결 시나리오를 구현하고 관리하기 위한 지침에 대해 자세히 설명합니다. CSP를 통한 테넌트 구독 생성 및 관리도 설명합니다.

이 지침은 VMware 가상 컴퓨터를 Azure에 복제하기 위해 기존 설명서의 내용도 상당 부분을 인용합니다. 자세한 내용은 [Site Recovery를 사용하여 Azure에 VMware 가상 컴퓨터 복제](site-recovery-vmware-to-azure.md)를 참조하세요.

## <a name="multi-tenant-environments"></a>다중 테넌트 환경
세 가지 주요 다중 테넌트 모델이 있습니다.

* **HSP(공유 호스팅 서비스 공급자)**: 파트너는 물리적 인프라를 소유하고 공유 리소스(vCenter, 데이터 센터, 실제 저장소 등)를 사용하여 동일한 인프라에서 다중 테넌트의 VM을 호스팅합니다. 파트너는 재해 복구 관리를 관리 서비스로 제공할 수 있으며 테넌트는 재해 복구를 셀프 서비스 솔루션으로 소유할 수 있습니다.

* **전용 호스팅 서비스 공급자**: 파트너는 물리적 인프라를 소유하지만 전용 리소스(여러 vCenter, 실제 데이터 저장소 등)를 사용하여 별도의 인프라에서 각 테넌트의 VM을 호스팅합니다. 파트너는 재해 복구 관리를 관리 서비스로 제공할 수 있으며 테넌트는 이를 셀프 서비스 솔루션으로 소유할 수 있습니다.

* **MSP(관리형 서비스 공급자)**: 고객은 VM을 호스팅하는 실제 인프라를 소유하고 파트너는 재해 복구를 사용하고 관리합니다.

## <a name="shared-hosting-multi-tenant-guidance"></a>공유 호스팅 다중 테넌트 지침
이 섹션에서는 공유 호스팅 시나리오를 자세히 다룹니다. 다른 두 가지 시나리오는 공유 호스팅 시나리오의 하위 집합이며 동일한 원칙을 사용합니다. 차이점은 공유 호스팅 지침의 끝에 설명되어 있습니다.

다중 테넌트 시나리오의 기본 요구 사항은 다양한 테넌트를 격리하는 것입니다. 한 테넌트에서 다른 테넌트가 호스팅한 것을 확인할 수 없어야 합니다. 이 요구 사항은 셀프 서비스 환경에서는 중요할 수 있으나 파트너 관리형 환경에서는 그만큼 중요하지 않습니다. 이 지침에서는 테넌트 격리가 필요한 것으로 가정합니다.

아키텍처는 다음 다이어그램에서 나타납니다.

![하나의 vCenter를 사용한 공유 HSP](./media/site-recovery-multi-tenant-support-vmware-using-csp/shared-hosting-scenario.png)  
**하나의 vCenter를 사용한 공유 호스팅 시나리오**

위 다이어그램에 표시된 것과 같이 각 고객은 별도의 관리 서버를 포함합니다. 이 구성은 테넌트별 VM에 테넌트 액세스를 제한하고 테넌트를 격리합니다. VMware 가상 컴퓨터 복제 시나리오는 VM을 검색하고 에이전트를 설치하기 위해 계정을 관리하는 구성 서버를 사용합니다. vCenter 액세스 제어를 통해 VM 검색을 제한하는 것 외에도 다중 테넌트 환경에 대해 동일한 원칙을 따릅니다.

데이터 격리 요구 사항은 모든 민감한 인프라 정보(예: 액세스 자격 증명)를 테넌트에 공개하지 않는 상태로 유지하는 것이 필요합니다. 이러한 이유로 관리 서버의 모든 구성 요소는 파트너가 독점적으로 통제하도록 하는 것이 좋습니다. 관리 서버 구성 요소는 다음과 같습니다.
* 구성 서버(CS)
* 프로세스 서버(PS)
* 마스터 대상 서버(MT) 

스케일 아웃 PS 역시 파트너가 통제합니다.

### <a name="every-cs-in-the-multi-tenant-scenario-uses-two-accounts"></a>다중 테넌트 시나리오의 모든 CS는 두 개의 계정을 사용합니다.

- **vCenter 액세스 계정**: 이 계정을 사용하여 테넌트 VM을 검색합니다. 다음 섹션에 설명되는 것처럼 vCenter 액세스 권한이 이 계정에 할당됩니다. 우발적인 액세스 누수를 방지하기 위해 파트너가 이러한 자격 증명을 구성 도구에 직접 입력하는 것이 좋습니다.

- **가상 컴퓨터 액세스 계정**: 이 계정을 사용하여 자동 푸시를 통해 테넌트 VM에 모바일 에이전트를 설치합니다. 일반적으로 도메인 계정으로, 테넌트가 파트너에게 제공하거나 아니면 파트너가 직접 관리할 수 있습니다. 테넌트가 파트너와 세부 사항을 직접 공유하지 않으려는 경우, 파트너가 제한된 기간 동안만 CS에 액세스하여 자격 증명을 입력할 수 있도록 하거나 아니면 파트너의 지원을 받아 모바일 에이전트를 수동으로 설치할 수 있습니다.

### <a name="requirements-for-a-vcenter-access-account"></a>vCenter 액세스 계정에 대한 요구 사항

이전 섹션에서 설명했듯이 특별한 역할이 할당된 계정으로 CS를 구성해야 합니다. 각 vCenter 개체에 대한 vCenter 액세스 계정에 역할 할당이 적용되고 자식 개체에게는 전파되지 않도록 해야 합니다. 액세스 전파가 다른 개체에도 우발적으로 액세스할 수 있기 때문에 이 구성을 사용하면 테넌트 격리가 보장됩니다.

![자식 개체로 전파 옵션](./media/site-recovery-multi-tenant-support-vmware-using-csp/assign-permissions-without-propagation.png)

대안으로 데이터 센터 개체에서 사용자 계정 및 역할을 할당하고 자식 개체에 전파하는 것이 있습니다. 그러면 특정 테넌트에 액세스하지 않아야 하는 계정에 모든 개체(예: 다른 테넌트의 VM)에 대한 *액세스 없음* 역할이 부여됩니다. 모든 새로운 자식 개체에게는 부모로부터 상속된 액세스도 자동으로 부여되므로 이 구성은 번거롭고 우발적으로 액세스 제어를 공개합니다. 따라서 첫 번째 방법을 사용하는 것이 좋습니다.

vCenter 계정 액세스 절차는 다음과 같습니다.

1. 미리 정의된 *읽기 전용* 역할을 복제하여 새 역할을 만들고 편리한 이름을 부여합니다(예: 이 예제에서 표시된 것처럼 Azure_Site_Recovery).

2. 이 역할에 다음 권한을 할당합니다.

    * **데이터 저장소**: 공간 할당, 데이터 저장소 찾아보기, 낮은 수준 파일 작업, 파일 제거, 가상 컴퓨터 파일 업데이트

    * **네트워크**: 네트워크 할당

    * **리소스**: 리소스 풀에 VM 할당, 전원이 꺼진 VM 마이그레이션, 전원이 켜진 VM 마이그레이션

    * **작업**: 만들기 작업, 업데이트 작업

    * **가상 컴퓨터**: 
        * 구성 > 모두
        * 상호 작용 -> 질문 응답, 장치 연결, CD 미디어 구성, 플로피 미디어 구성, 전원 끄기, 전원 켜기, VMware 도구 설치
        * 인벤토리 > 기존 항목에서 만들기, 새로 만들기, 등록, 등록 취소
        * 프로비전 -> 가상 컴퓨터 다운로드 허용, 가상 컴퓨터 파일 업로드 허용
        * 스냅숏 관리 > 스냅숏 제거

    ![역할 편집 대화 상자](./media/site-recovery-multi-tenant-support-vmware-using-csp/edit-role-permissions.png)

3. 다음과 같이 다양한 개체에 대해 vCenter 계정(CS 테넌트에 사용)에 액세스 수준을 할당합니다.

>| Object | 역할 | 설명 |
>| --- | --- | --- |
>| vCenter | 읽기 전용 | 여러 개체를 관리하기 위해 vCenter 액세스를 허용하는 데만 필요합니다. 계정이 테넌트에 제공되지 않거나 vCenter의 모든 관리 작업에 사용되는 경우 이 권한을 제거할 수 있습니다. |
>| 데이터 센터 | Azure_Site_Recovery |  |
>| 호스트 및 호스트 클러스터 | Azure_Site_Recovery | 따라서 장애 조치(failover) 전 및 장애 복구(failback) 후에 액세스할 수 있는 호스트만 테넌트 VM을 가지도록 개체 수준에서 액세스가 이루어지는지 다시 확인합니다. |
>| 데이터 저장소 및 데이터 저장소 클러스터 | Azure_Site_Recovery | 앞과 동일합니다. |
>| 네트워크 | Azure_Site_Recovery |  |
>| 관리 서버 | Azure_Site_Recovery | CS 컴퓨터 외부에 있는 경우 모든 구성 요소(CS, PS 및 MT)에 대한 액세스를 포함합니다. |
>| 테넌트 VM | Azure_Site_Recovery | 또한 특정 테넌트의 새로운 테넌트 VM이 이 액세스를 수행하는지, Azure Portal을 통해 이 테넌트를 검색할 수 없는지 확인합니다. |

이제 vCenter 계정 액세스가 완료되었습니다. 이 단계는 장애 복구 작업을 완료하는 최소 권한 요구 사항을 충족합니다. 이러한 액세스 권한은 기존 정책과 함께 사용할 수도 있습니다. 이전에 설명한 2단계의 역할 권한을 포함하도록 기존 권한을 수정합니다.

장애 복구 기능 없이 장애 조치 상태가 될 때까지 재해 복구 작업을 제한하려면 위의 절차에 따릅니다. 단, *Azure_Site_Recovery* 역할을 vCenter 액세스 계정에 할당하는 대신 *읽기 전용* 역할만 해당 계정에 할당합니다. 이 권한 집합은 VM 복제 및 장애 조치(Failover)를 허용하지만 장애 복구(failback)는 허용하지 않습니다. 위 프로세스의 나머지 모든 것들은 그대로 유지됩니다. 테넌트를 격리하고 VM 검색을 제한하기 위해 모든 권한은 계속 개체 수준에서만 할당되며 자식 개체에는 전파되지 않습니다.

## <a name="other-multi-tenant-environments"></a>기타 다중 테넌트 환경

이전 섹션에서는 공유 호스팅 솔루션을 위한 다중 테넌트 환경을 설정하는 방법을 설명했습니다. 기타 두 가지 주요 솔루션은 전용 호스팅 및 관리형 서비스입니다. 이러한 솔루션의 아키텍처는 다음 섹션에 설명되어 있습니다.

### <a name="dedicated-hosting-solution"></a>전용 호스팅 솔루션

다음 다이어그램에 표시된 것처럼 전용 호스팅 솔루션의 아키텍처 차이점은 각 테넌트의 인프라가 해당 테넌트에 대해서만 설정된다는 것입니다. 테넌트가 별도의 vCenter를 통해 격리되기 때문에 호스팅 공급자는 공유 호스팅에 대해 제공된 CSP 단계를 계속 수행해야 하지만 테넌트 격리에 대해 우려하지 않아도 됩니다. CSP 설치는 변경되지 않고 유지됩니다.

![architecture-shared-hsp](./media/site-recovery-multi-tenant-support-vmware-using-csp/dedicated-hosting-scenario.png)  
**여러 vCenter를 사용한 전용 호스팅 시나리오**

### <a name="managed-service-solution"></a>관리형 서비스 솔루션

다음 다이어그램에 나와 있는 것처럼 관리 서비스 솔루션의 아키텍처 차이점은 각 테넌트의 인프라가 다른 테넌트의 인프라와 물리적으로도 분리된다는 점입니다. 이 시나리오는 일반적으로 테넌트가 인프라를 소유하고 재해 복구를 관리할 솔루션 공급자가 필요한 경우 존재합니다. 다시 말해, 테넌트가 다른 인프라를 통해 물리적으로 격리되기 때문에 파트너는 공유 호스팅에 대해 제공된 CSP 단계를 계속 수행해야 하지만 테넌트 격리에 대해 우려하지 않아도 됩니다. CSP 프로비저닝은 그대로 유지됩니다.

![architecture-shared-hsp](./media/site-recovery-multi-tenant-support-vmware-using-csp/managed-service-scenario.png)  
**여러 vCenter를 사용하는 관리 서비스 시나리오**

## <a name="csp-program-overview"></a>CSP 프로그램 개요
[CSP 프로그램](https://partner.microsoft.com/en-US/cloud-solution-provider)은 Office 365, Enterprise Mobility Suite 및 Microsoft Azure 등 모든 Microsoft 클라우드 서비스를 파트너에게 제공하기 위해 더 나은 협력 사례를 발전시키고 있습니다. CSP를 통해 파트너는 고객과 종단 간 관계를 보유하게 되고 기본 관계 접점이 될 수 있습니다. 파트너는 고객을 위해 Azure 구독을 배포하고 이러한 구독과 고객의 부가 가치 사용자 지정 옵션을 결합할 수 있습니다.

파트너는 Azure Site Recovery와 함께 CSP를 통해 고객을 위한 완전한 재해 복구 솔루션을 직접 관리할 수 있습니다. 또는 CSP를 사용하여 Site Recovery 환경을 설정할 수 있으며 고객은 셀프 서비스 방식으로 고유한 재해 복구 요구를 관리할 수 있습니다. 두 시나리오 모두에서 파트너는 Site Recovery와 고객 간의 매개체입니다. 파트너가 고객 관계 서비스를 제공하고 Site Recovery 사용에 대해 고객에게 비용을 청구합니다.

## <a name="create-and-manage-tenant-accounts"></a>테넌트 계정 만들기 및 관리

### <a name="step-0-prerequisite-check"></a>단계 0: 필수 구성 요소 확인

VM 필수 구성 요소는 [Azure Site Recovery 설명서](site-recovery-vmware-to-azure.md)에 설명된 것과 동일합니다. 이러한 필수 구성 요소 외에도 이전에 언급된 액세스 제어는 CSP를 통한 테넌트 관리를 진행하기 전에 배치되어야 합니다. 각 테넌트에 대해, 테넌트 VM 및 테넌트의 vCenter와 통신할 수 있는 별도의 관리 서버를 만듭니다. 파트너만이 이 서버에 대한 액세스 권한이 있습니다.

### <a name="step-1-create-a-tenant-account"></a>1단계: 테넌트 계정 만들기

1. [Microsoft 파트너 센터](https://partnercenter.microsoft.com/)를 통해 CSP 계정에 로그인합니다. 
 
2. **대시보드** 메뉴에서 **고객**을 선택합니다.

    ![Microsoft 파트너 센터 고객 링크](./media/site-recovery-multi-tenant-support-vmware-using-csp/csp-dashboard-display.png)

3. 열린 페이지에서 **고객 추가** 단추를 클릭합니다.

    ![[고객 추가] 단추](./media/site-recovery-multi-tenant-support-vmware-using-csp/add-new-customer.png)

4. **새 고객** 페이지에서 테넌트에 대한 계정 세부 정보를 모두 입력하고 **다음: 구독**을 클릭합니다.

    ![[계정 정보] 페이지](./media/site-recovery-multi-tenant-support-vmware-using-csp/customer-add-filled.png)

5. 구독 선택 페이지에서 **Microsoft Azure** 확인란을 선택합니다. 지금 또는 나중에 다른 구독을 추가할 수 있습니다.

    ![[Microsoft Azure 구독] 확인란](./media/site-recovery-multi-tenant-support-vmware-using-csp/azure-subscription-selection.png)

6. **검토** 페이지에서 테넌트 세부 정보를 확인한 후 **제출**을 클릭합니다.

    ![[검토] 페이지](./media/site-recovery-multi-tenant-support-vmware-using-csp/customer-summary-page.png)  

    테넌트 계정을 만들면 확인 페이지가 나타나며 여기에 구독에 대한 기본 계정 및 암호 정보가 표시됩니다. 

7. 정보를 저장하고 나중에 필요하면 Azure Portal 로그인 페이지를 통해 암호를 변경합니다.  
 
    이 정보를 테넌트와 있는 그대로 공유하거나 필요할 경우 별도의 계정을 만들고 공유할 수 있습니다.

### <a name="step-2-access-the-tenant-account"></a>2단계: 테넌트 계정 액세스

“1단계: 테넌트 계정 만들기”에 설명된 대로 Microsoft 파트너 센터 대시보드를 통해 테넌트 구독에 액세스할 수 있습니다. 

1. **고객** 페이지로 이동한 후 테넌트 계정의 이름을 클릭합니다.

2. 테넌트 계정의 **구독** 페이지에서 기존 계정 구독을 모니터링하고 필요에 따라 구독을 더 추가할 수 있습니다. 테넌트의 재해 복구 작업을 관리하려면 **모든 리소스(Azure Portal)**를 선택합니다.

    ![모든 리소스 링크](./media/site-recovery-multi-tenant-support-vmware-using-csp/all-resources-select.png)  
    
    **모든 리소스**를 클릭하면 테넌트의 Azure 구독에 대한 액세스 권한이 부여됩니다. Azure Portal의 맨 위 오른쪽에 있는 Azure Active Directory 링크를 클릭하여 액세스를 확인할 수 있습니다.

    ![Azure Active Directory 링크](./media/site-recovery-multi-tenant-support-vmware-using-csp/aad-admin-display.png)

이제 Azure Portal을 통해 테넌트에 대한 모든 사이트 복구 작업을 수행하고 재해 복구 작업을 관리할 수 있습니다. 관리 재해 복구를 위해 CSP를 통해 테넌트 구독에 액세스하려면 이전에 설명된 과정을 따릅니다.

### <a name="step-3-deploy-resources-to-the-tenant-subscription"></a>3단계: 리소스를 테넌트 구독에 배포
1. Azure Portal에서 리소스 그룹을 만들고 일반 프로세스에 대한 Recovery Services 자격 증명 모음을 배포합니다. 
 
2. 자격 증명 모음 등록 키를 다운로드합니다.

3. 자격 증명 모음 등록 키를 사용하여 테넌트용 CS를 등록합니다.

4. 두 가지 액세스 계정 즉, vCenter 액세스 계정과 VM 액세스 계정에 대한 자격 증명을 입력합니다.

    ![관리자 구성 서버 계정](./media/site-recovery-multi-tenant-support-vmware-using-csp/config-server-account-display.png)

### <a name="step-4-register-site-recovery-infrastructure-to-the-recovery-services-vault"></a>4단계: Site Recovery 인프라를 Recovery Services 자격 증명 모음에 등록
1. Azure Portal의 앞에서 만든 자격 증명 모음에서 vCenter 서버를 “3단계: 리소스를 테넌트 구독에 배포”에 등록한 CS에 등록합니다. 이를 위해 vCenter 액세스 계정을 사용합니다.
2. 일반 프로세스에 대한 Site Recovery를 위해 “준비 인프라” 프로세스를 완료합니다.
3. 이제 VM을 복제할 준비가 되었습니다. **복제** 옵션 아래에 있는 **가상 컴퓨터 선택** 블레이드에 테넌트의 VM만 표시되는지 확인합니다.

    ![가상 컴퓨터 선택 블레이드의 테넌트 VM 목록](./media/site-recovery-multi-tenant-support-vmware-using-csp/tenant-vm-display.png)

### <a name="step-5-assign-tenant-access-to-the-subscription"></a>5단계: 구독에 테넌트 액세스 할당

셀프 서비스 재해 복구의 경우 “1단계: 테넌트 계정 만들기” 섹션의 6단계에서 설명한 것처럼 테넌트에 계정 정보를 제공합니다. 파트너가 재해 복구 인프라를 설정한 후 이 작업을 수행합니다. 재해 복구 형식이 관리형 또는 셀프 서비스 형태인지 관계없이 파트너는 CSP 포털을 통해 테넌트 구독을 액세스해야 합니다. 파트너 소유 자격 증명 모음을 설정하고 테넌트 구독에 인프라를 등록합니다.

파트너는 다음을 수행하여 CSP 포털을 통해 새 사용자를 테넌트 구독에 추가할 수도 있습니다.

1. 테넌트의 CSP 구독 페이지로 이동하고 **사용자 및 라이선스** 옵션을 선택합니다.

    ![테넌트의 CSP 구독 페이지](./media/site-recovery-multi-tenant-support-vmware-using-csp/users-and-licences.png)

    이제 관련 세부 정보를 입력하거나 권한을 선택하거나 사용자 목록을 CSV 파일로 업로드하여 새 사용자를 만들 수 있습니다.

2. 새 사용자를 만든 후 Azure Portal로 돌아가 **구독** 블레이드에서 관련 구독을 선택합니다.

3. 열리는 새 블레이드에서 **액세스 제어(IAM)**를 선택하고 **추가**를 클릭하여 관련 액세스 수준을 가진 사용자를 추가합니다.      
    CSP 포털을 통해 만들어진 사용자는 액세스 수준을 클릭하면 열리는 블레이드에 자동으로 표시됩니다.

    ![사용자 추가](./media/site-recovery-multi-tenant-support-vmware-using-csp/add-user-subscription.png)

    대부분의 관리 작업은 *참여자* 역할로 충분합니다. 이 액세스 수준을 가진 사용자는 액세스 수준 변경(*소유자* 수준 액세스가 필요)을 제외하고 구독에 관한 모든 작업을 수행할 수 있습니다. 또한 필요에 따라 액세스 수준을 미세 조정할 수 있습니다.
