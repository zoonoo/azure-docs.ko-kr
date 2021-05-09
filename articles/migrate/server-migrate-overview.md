---
title: Azure Migrate 서버 마이그레이션을 사용하여 VMware 마이그레이션 옵션을 선택
description: Azure Migrate 서버 마이그레이션을 사용하여 VMware VM을 Azure로 마이그레이션하기 위한 옵션에 대한 개요 제공
author: anvar-ms
ms.author: anvar
ms.manager: bsiva
ms.topic: conceptual
ms.date: 06/08/2020
ms.openlocfilehash: 7446b2050fdd7bbc7704953c053da0629231191c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101715125"
---
# <a name="select-a-vmware-migration-option"></a>VMware 마이그레이션 옵션 선택

Azure Migrate 서버 마이그레이션 도구를 사용하여 VMware VM을 Azure로 마이그레이션할 수 있습니다. 이 도구는 VMware VM 마이그레이션을 위해 다음 두 가지 옵션을 제공합니다.

- 에이전트 없는 복제를 통한 마이그레이션. VM에 아무 것도 설치하지 않고 해당 VM을 마이그레이션합니다.
- 복제용 에이전트를 통한 마이그레이션. VM에 복제용 에이전트를 설치합니다.


## <a name="compare-migration-methods"></a>마이그레이션 방법 비교

선택한 비교를 활용하여 어떤 방법을 사용할지 결정합니다. [에이전트 없는](migrate-support-matrix-vmware-migration.md#agentless-migration) 마이그레이션 및 [에이전트 기반](migrate-support-matrix-vmware-migration.md#agent-based-migration) 마이그레이션에 대한 전체 지원 요구 사항도 검토할 수 있습니다.

**설정** | **에이전트 없음** | **에이전트 기반**
--- | --- | ---
**Azure 권한** | Azure Migrate 프로젝트를 만들고, Azure Migrate 어플라이언스를 배포할 때 만든 Azure AD 앱을 등록하기 위한 권한이 필요합니다. | Azure 구독에 대한 기여자 권한이 필요합니다. 
**복제** | vCenter Server에서 최대 500대의 VM을 동시에 복제할 수 있습니다. 포털에서 복제할 머신을 한 번에 10대까지 선택할 수 있습니다. 더 많은 머신을 복제하려면 10대 일괄 처리를 추가합니다.| 복제 어플라이언스의 크기를 스케일링하면 복제 용량을 늘릴 수 있습니다.
**어플라이언스 배포** | [Azure Migrate 어플라이언스](migrate-appliance.md)는 온-프레미스에 배포됩니다. | [Azure Migrate 복제 어플라이언스](migrate-replication-appliance.md)는 온-프레미스에 배포됩니다.
**Site Recovery 호환성** | 호환 가능합니다. | Site Recovery를 사용하여 머신 복제를 설정한 경우 Azure Migrate 서버 마이그레이션으로 복제할 수 없습니다.
**대상 디스크** | 관리 디스크 | 관리 디스크
**디스크 제한** | OS 디스크: 2TB<br/><br/> 데이터 디스크: 32TB<br/><br/> 최대 디스크: 60 | OS 디스크: 2TB<br/><br/> 데이터 디스크: 32TB<br/><br/> 최대 디스크: 63
**통과 디스크** | 지원되지 않음 | 지원됨
**UEFI 부팅** | 지원됨. | 지원됨.

## <a name="compare-deployment-steps"></a>배포 단계 비교

제한 사항 검토 후 각 솔루션 배포에 포함되는 단계를 이해하면 어떤 옵션을 선택할지 결정하는 데 도움이 될 수 있습니다.

**Task** | **세부 정보** |**에이전트 없음** | **에이전트 기반**
--- | --- | --- | ---
**Azure Migrate 어플라이언스 배포** | VMware VM에서 실행되는 경량 어플라이언스입니다.<br/><br/> 해당 어플라이언스는 머신을 검색 및 평가하고 에이전트 없는 마이그레이션을 사용하여 머신을 마이그레이션하는 데 사용됩니다. | 필수 요소.<br/><br/> 평가를 위해 어플라이언스를 이미 설정한 경우 에이전트 없는 마이그레이션에 동일한 어플라이언스를 사용할 수 있습니다. | 필수 아님.<br/><br/> 평가를 위해 어플라이언스를 설정한 경우 해당 어플라이언스를 그대로 두거나 평가가 완료된 경우 제거할 수 있습니다.
**서버 평가 도구 사용** | Azure Migrate: 서버 평가 도구를 사용하여 머신을 평가합니다. | 평가는 선택 사항입니다. | 평가는 선택 사항입니다.
**서버 마이그레이션 도구 사용** | Azure Migrate 서버 마이그레이션 도구를 Azure Migrate 프로젝트에 추가합니다. | 필수 | 필수
**마이그레이션을 위해 VMware 준비** | VMware 서버 및 VM에 대한 설정을 구성합니다. | 필수 | 필수
**VM에 Mobility Service 설치** | Mobility Service는 복제하려는 각 VM에서 실행됩니다. | 필요 없음 | 필수
**복제 어플라이언스 배포** | [복제 어플라이언스](migrate-replication-appliance.md)는 에이전트 기반 마이그레이션에 사용되며 VM에서 실행되는 Mobility Service와 서버 마이그레이션을 연결합니다. | 필요 없음 | 필수
**VM 복제**. VM 복제를 사용하도록 설정. | 복제 설정을 구성하고 복제할 VM을 선택 | 필수 | 필수
**테스트 마이그레이션 실행** | 테스트 마이그레이션을 실행하여 모든 것이 예상대로 작동하는지 확인합니다. | 필수 | 필수
**전체 마이그레이션 실행** | VM 마이그레이션. | 필수 | 필수



## <a name="next-steps"></a>다음 단계

에이전트 없는 마이그레이션을 사용하여 [VMware VM을 마이그레이션](tutorial-migrate-vmware.md)합니다.



