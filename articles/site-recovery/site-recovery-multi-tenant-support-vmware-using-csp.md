---
title: Azure에서 VMware VM 복제를 위한 다중 테넌트 지원(CSP 프로그램) | Microsoft Docs
description: 다중 테넌트 환경에서 Azure Site Recovery를 배포하여 Azure Portal을 사용하는 CSP 프로그램을 통해 온-프레미스 VMware VM(가상 머신)에서 Azure로 복제, 장애 조치(failover) 및 복구를 오케스트레이션하는 방법에 대해 설명합니다.
services: site-recovery
documentationcenter: ''
author: mayanknayar
manager: rochakm
editor: ''
ms.assetid: ''
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/27/2018
ms.author: manayar
ms.openlocfilehash: 532dd399d2d5fcbab616744dd02f4a95078cbb1b
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/07/2018
ms.locfileid: "29767618"
---
# <a name="multi-tenant-support-in-azure-site-recovery-for-replicating-vmware-virtual-machines-to-azure-through-csp"></a>CSP를 통해 VMware 가상 머신을 Azure에 복제하기 위해 Azure Site Recovery에서 다중 테넌트 지원

Azure Site Recovery는 테넌트 구독을 위해 다중 테넌트 환경을 지원합니다. 또한 Microsoft CSP(클라우드 솔루션 공급자) 프로그램을 통해 생성 및 관리되는 테넌트 구독에 대한 다중 테넌트도 지원합니다. 이 문서는 다중 테넌트 VMware 및 Azure 연결 시나리오를 구현하고 관리하기 위한 지침에 대해 자세히 설명합니다. 테넌트 구독을 생성하고 관리하는 방법에 대한 자세한 내용은 [CSP를 사용하여 다중 테넌트 관리](site-recovery-manage-multi-tenancy-with-csp.md)를 참조하세요.

이 지침은 VMware 가상 머신을 Azure에 복제하기 위해 기존 설명서의 내용도 상당 부분을 인용합니다. 자세한 내용은 [Site Recovery를 사용하여 Azure에 VMware 가상 머신 복제](site-recovery-vmware-to-azure.md)를 참조하세요.

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

위 다이어그램에 표시된 것과 같이 각 고객은 별도의 관리 서버를 포함합니다. 이 구성은 테넌트별 VM에 테넌트 액세스를 제한하고 테넌트를 격리합니다. VMware 가상 컴퓨터 복제 시나리오는 VM을 검색하고 에이전트를 설치하기 위해 계정을 관리하는 구성 서버를 사용합니다. vCenter 액세스 제어를 통해 VM 검색을 제한하는 것 외에도 동일한 원칙이 다중 테넌트 환경에 적용됩니다.

데이터 격리 요구 사항은 모든 민감한 인프라 정보(예: 액세스 자격 증명)를 테넌트에 공개하지 않는 상태로 유지하는 것이 필요합니다. 이러한 이유로 관리 서버의 모든 구성 요소는 파트너가 독점적으로 통제하도록 하는 것이 좋습니다. 관리 서버 구성 요소는 다음과 같습니다.
* 구성 서버(CS)
* 프로세스 서버(PS)
* 마스터 대상 서버(MT)

스케일 아웃 PS 역시 파트너가 통제합니다.

### <a name="every-cs-in-the-multi-tenant-scenario-uses-two-accounts"></a>다중 테넌트 시나리오의 모든 CS는 두 개의 계정을 사용합니다.

- **vCenter 액세스 계정**: 이 계정을 사용하여 테넌트 VM을 검색합니다. 다음 섹션에 설명되는 것처럼 vCenter 액세스 권한이 이 계정에 할당됩니다. 우발적인 액세스 누수를 방지하기 위해 파트너가 이러한 자격 증명을 구성 도구에 직접 입력하는 것이 좋습니다.

- **가상 컴퓨터 액세스 계정**: 이 계정을 사용하여 자동 푸시를 통해 테넌트 VM에 모바일 에이전트를 설치합니다. 일반적으로 테넌트가 파트너에게 제공하거나 파트너가 직접 관리할 수 있는 도메인 계정입니다. 테넌트가 파트너와 세부 사항을 직접 공유하지 않으려는 경우, 파트너가 제한된 기간 동안만 CS에 액세스하여 자격 증명을 입력할 수 있도록 하거나 아니면 파트너의 지원을 받아 모바일 에이전트를 수동으로 설치할 수 있습니다.

### <a name="requirements-for-a-vcenter-access-account"></a>vCenter 액세스 계정에 대한 요구 사항

이전 섹션에서 설명했듯이 특별한 역할이 할당된 계정으로 CS를 구성해야 합니다. 각 vCenter 개체에 대한 vCenter 액세스 계정에 역할 할당이 적용되고 자식 개체에게는 전파되지 않도록 해야 합니다. 액세스 전파가 다른 개체에도 우발적으로 액세스할 수 있기 때문에 이 구성을 사용하면 테넌트 격리가 보장됩니다.

![자식 개체로 전파 옵션](./media/site-recovery-multi-tenant-support-vmware-using-csp/assign-permissions-without-propagation.png)

대안으로 데이터 센터 개체에서 사용자 계정 및 역할을 할당하고 자식 개체에 전파하는 것이 있습니다. 그러면 특정 테넌트에 액세스하지 않아야 하는 계정에 모든 개체(예: 다른 테넌트의 VM)에 대한 *액세스 없음* 역할이 부여됩니다. 모든 새로운 자식 개체에게는 부모로부터 상속된 액세스도 자동으로 부여되므로 이 구성은 번거롭고 우발적으로 액세스 제어를 공개합니다. 따라서 첫 번째 방법을 사용하는 것이 좋습니다.

vCenter 계정 액세스 절차는 다음과 같습니다.

1. 미리 정의된 *읽기 전용* 역할을 복제하여 새 역할을 만들고 편리한 이름을 부여합니다(예: 이 예제에서 표시된 것처럼 Azure_Site_Recovery).

2. 이 역할에 다음 권한을 할당합니다.

    * **데이터 저장소**: 공간 할당, 데이터 저장소 찾아보기, 낮은 수준 파일 작업, 파일 제거, 가상 머신 파일 업데이트

    * **네트워크**: 네트워크 할당

    * **리소스**: 리소스 풀에 VM 할당, 전원이 꺼진 VM 마이그레이션, 전원이 켜진 VM 마이그레이션

    * **작업**: 만들기 작업, 업데이트 작업

    * **가상 머신**:
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
>| 관리 서버 | Azure_Site_Recovery | CS 컴퓨터 외부에서 모든 구성 요소(CS, PS 및 MT)에 대한 액세스를 포함합니다. |
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

## <a name="next-steps"></a>다음 단계
Azure Site Recovery 배포를 관리하는 역할 기반 액세스 제어에 대해 [자세히 알아봅니다](site-recovery-role-based-linked-access-control.md).

[CSP를 사용하여 다중 테넌트 관리](site-recovery-manage-multi-tenancy-with-csp.md)
