---
title: 특정 사용자가 앱에 액세스할 수 있는 시기 알아보기
description: 매우 중요한 사용자가 Azure AD를 사용하여 사용자를 프로비저닝하도록 구성한 애플리케이션에 액세스할 수 있는 시기를 찾는 방법
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/03/2019
ms.author: mimart
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 48370e2806b70d550bce95ceff3857a79561f247
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79264142"
---
# <a name="check-the-status-of-user-provisioning"></a>사용자 프로비저닝 상태 확인

Azure AD 프로비저닝 서비스는 원본 시스템 및 대상 시스템에 대해 초기 프로비저닝 주기를 실행한 다음 주기적인 증분 주기를 실행합니다. 앱에 대한 프로비저닝을 구성할 때 프로비저닝 서비스의 현재 상태를 확인하고 사용자가 앱에 액세스할 수 있는 시기를 확인할 수 있습니다.

## <a name="view-the-provisioning-progress-bar"></a>프로비저닝 진행률 표시줄 보기

 앱의 **프로비저닝** 페이지에서 Azure AD 프로비전 서비스의 상태를 볼 수 있습니다. 페이지 하단의 **현재 상태** 섹션에는 프로비저닝 주기가 사용자 계정 프로비저닝을 시작했는지 여부를 보여 주며 있습니다. 주기의 진행 률을 확인하고, 프로비전된 사용자 및 그룹 수를 확인하고, 생성된 역할 수를 확인할 수 있습니다.

자동 프로비저닝을 처음 구성할 때 페이지 하단의 **현재 상태** 섹션에는 초기 프로비저닝 주기의 상태가 표시됩니다. 이 섹션은 증분 주기가 실행될 때마다 업데이트됩니다. 다음 세부 정보가 표시됩니다.
- 현재 실행 중이거나 마지막으로 완료된 프로비저닝 주기(초기 또는 증분) 유형입니다.
- 프로비저닝 주기의 백분율을 보여 주는 **진행률 표시줄입니다.** 백분율은 프로비전된 페이지 수를 반영합니다. 각 페이지에는 여러 사용자 또는 그룹이 포함될 수 있으므로 백분율은 프로비저닝된 사용자, 그룹 또는 역할 의 수와 직접적인 관련이 없습니다.
- 뷰를 업데이트하는 데 사용할 수 있는 **새로 고침** 단추입니다.
- 커넥터 데이터 저장소의 **사용자** 및 **그룹** 수입니다. 개체가 프로비저닝 범위에 추가될 때마다 개수가 증가합니다. 사용자가 커넥터 데이터 저장소에서 개체를 제거하지 않으므로 사용자가 일시 삭제되거나 하드 삭제된 경우 개수가 다운되지 않습니다. CDS가 [재설정된](https://docs.microsoft.com/graph/api/synchronization-synchronizationjob-restart?view=graph-rest-beta&tabs=http) 후 첫 번째 동기화를 다시 계산합니다. 
- 개별 사용자에 대한 프로비저닝 상태를 포함하여 사용자 프로비저닝 서비스에서 실행되는 모든 작업에 대한 자세한 내용은 Azure AD 프로비저닝 로그를 여는 **조회 감사 로그** 링크입니다(아래 [사용 프로비저닝 로그](#use-provisioning-logs-to-check-a-users-provisioning-status) 섹션 참조).

프로비저닝 주기가 완료되면 **현재까지의 통계** 섹션에는 현재까지 프로비전된 사용자 및 그룹의 누적 수와 마지막 주기의 완료 날짜 및 기간이 표시됩니다. **활동 ID는** 가장 최근의 프로비저닝 주기를 고유하게 식별합니다. **작업 ID는** 프로비저닝 작업에 대한 고유 식별자이며 테넌트의 앱과 관련이 있습니다.

프로비저닝 진행 상황은 Azure Active **Directory &gt; 엔터프라이즈 앱 &gt; \[응용\] &gt; 프로그램 이름 프로비저닝** 탭의 Azure 포털에서 볼 수 있습니다.

![프로비저닝 페이지 진행률 표시줄](./media/application-provisioning-when-will-provisioning-finish-specific-user/provisioning-progress-bar-section.png)

## <a name="use-provisioning-logs-to-check-a-users-provisioning-status"></a>프로비저닝 로그를 사용하여 사용자의 프로비저닝 상태 확인

선택한 사용자의 프로비저닝 상태를 보려면 Azure AD의 [프로비저닝 로그(미리 보기)를](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context) 참조하십시오. 사용자 프로비저닝 서비스에서 실행되는 모든 작업은 Azure AD 프로비저닝 로그에 기록됩니다. 여기에는 원본 및 대상 시스템에 대한 모든 읽기 및 쓰기 작업과 각 작업 중에 읽거나 쓴 사용자 데이터가 포함됩니다.

**활동** 섹션에서 **Azure Active Directory** &gt; **엔터프라이즈 앱** &gt; **프로비저닝 로그(미리 보기)를** 선택하여 Azure 포털의 프로비저닝 로그에 액세스할 수 있습니다. 소스 시스템 또는 대상 시스템에서 사용자 이름 또는 식별자의 이름을 기반으로 프로비저닝 데이터를 검색할 수 있습니다. 자세한 내용은 [프로비저닝 로그(미리 보기)를](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context)참조하십시오. 

프로비저닝 로그는 다음을 포함하여 프로비저닝 서비스에서 수행하는 모든 작업을 기록합니다.

* 프로비저닝 범위에 있는 할당된 사용자에 대해 Azure AD 쿼리
* 해당 사용자의 존재에 대해 대상 앱 쿼리
* 시스템 간의 사용자 개체 비교
* 비교를 기반으로 대상 시스템에서 사용자 계정 추가, 업데이트 또는 비활성화

Azure 포털에서 프로비저닝 로그를 읽는 방법에 대한 자세한 내용은 [프로비저닝 보고 가이드를](check-status-user-account-provisioning.md)참조하십시오.

## <a name="how-long-will-it-take-to-provision-users"></a>사용자를 프로비전하는 데 걸리는 시간은 어느 정도인가요?
응용 프로그램과 함께 자동 사용자 프로비전을 사용하는 경우 Azure AD는 일반적으로 40분마다 정기적으로 예약된 시간 간격으로 [사용자 및 그룹 할당과](../manage-apps/assign-user-or-group-access-portal.md) 같은 항목을 기반으로 앱에서 사용자 계정을 자동으로 프로비저닝하고 업데이트합니다.

지정된 사용자가 프로비전되는 데 걸리는 시간은 주로 프로비저닝 작업이 초기 주기를 실행하고 있는지 또는 증분 주기를 실행하는지에 따라 달라집니다.

- **초기 주기의**경우 작업 시간은 프로비저닝 범위의 사용자 및 그룹 수, 원본 시스템의 총 사용자 및 그룹 수를 비롯한 여러 요인에 따라 달라집니다. Azure AD와 앱 간의 첫 번째 동기화는 Azure AD 디렉터리의 크기와 프로비저닝 범위에 있는 사용자 수에 따라 20분에서 몇 시간이 걸릴 수 있습니다. 초기 주기 성능에 영향을 주는 요인의 포괄적인 목록은 이 섹션의 후반부에 요약되어 있습니다.

- 초기 **주기 이후의 증분 주기의** 경우 프로비저닝 서비스가 초기 주기 이후 두 시스템의 상태를 나타내는 워터마크를 저장하므로 작업 시간이 더 빠르며(예: 10분 이내) 후속 동기화의 성능이 향상됩니다. 작업 시간은 해당 프로비저닝 주기에서 검색된 변경 수에 따라 달라집니다. 사용자 또는 그룹 구성원 자격 변경이 5,000회 미만인 경우 작업은 단일 증분 프로비저닝 주기 내에 완료될 수 있습니다. 

다음 표에는 일반적인 프로비저닝 시나리오의 동기화 시간이 요약되어 있습니다. 이러한 시나리오에서 원본 시스템은 Azure AD이고, 대상 시스템은 SaaS 애플리케이션입니다. 동기화 시간은 SaaS 응용 프로그램 ServiceNow, Workplace, Salesforce 및 G Suite에 대한 동기화 작업의 통계 분석에서 파생됩니다.


| 범위 구성 | 범위 내 사용자, 그룹 및 멤버 | 초기 사이클 시간 | 증분 주기 시간 |
| -------- | -------- | -------- | -------- |
| 할당된 사용자 및 그룹만 동기화 |  1,000 미만 |  30분 미만 | 30분 미만 |
| 할당된 사용자 및 그룹만 동기화 |  1,000 - 10,000 | 142 - 708분 | 30분 미만 |
| 할당된 사용자 및 그룹만 동기화 |   10,000 - 100,000 | 1,170 - 2,340분 | 30분 미만 |
| Azure AD의 모든 사용자 및 그룹 동기화 |  1,000 미만 | 30분 미만  | 30분 미만 |
| Azure AD의 모든 사용자 및 그룹 동기화 |  1,000 - 10,000 | 30 - 120분 | 30분 미만 |
| Azure AD의 모든 사용자 및 그룹 동기화 |  10,000 - 100,000  | 713 - 1,425분 | 30분 미만 |
| Azure AD의 모든 사용자 동기화|  1,000 미만  | 30분 미만 | 30분 미만 |
| Azure AD의 모든 사용자 동기화 | 1,000 - 10,000  | 43 - 86분 | 30분 미만 |


**동기화 할당된 사용자 및 그룹만**구성의 경우 다음 수식을 사용하여 대략적인 최소 및 최대 예상 **초기 주기** 시간을 결정할 수 있습니다.

    Minimum minutes =  0.01 x [Number of assigned users, groups, and group members]
    Maximum minutes = 0.08 x [Number of assigned users, groups, and group members] 
    
**초기 주기를**완료하는 데 걸리는 시간에 영향을 주는 요인 요약:

- 프로비저닝 범위에 속하는 사용자 및 그룹의 총 수

- 원본 시스템(Azure AD)에 있는 사용자, 그룹 및 그룹 멤버의 총 수

- 프로비저닝 범위의 사용자가 대상 응용 프로그램의 기존 사용자와 일치하는지 또는 처음으로 만들어야 하는지 여부입니다. 모든 사용자가 처음으로 생성되는 동기화 작업은 모든 사용자가 기존 사용자와 일치하는 동기화 작업보다 약 *두 배 정도 오래* 소요됩니다.

- [프로비저닝 로그의](check-status-user-account-provisioning.md)오류 수입니다. 많은 오류가 있고 프로비저닝 서비스가 격리 상태로 전환된 경우 성능이 저하됩니다. 

- 대상 시스템에서 구현된 요청 비율 한도 및 제한. 일부 대상 시스템은 대규모 동기화 작업 중 성능에 영향을 줄 수 있는 요청 속도 제한 및 제한을 구현합니다. 이러한 조건에서 너무 많은 요청을 너무 빠르게 받는 앱은 응답 속도가 느려지거나 연결을 닫을 수도 있습니다. 성능을 개선하려면 커넥터가 앱이 처리할 수 있는 속도보다 빠르게 앱 요청이 전송되지 않도록 조정해야 합니다. Microsoft에서 빌드한 프로비저닝 커넥터는 이러한 조정 작업을 수행합니다. 

- 할당된 그룹 수 및 크기. 할당된 그룹 동기화가 사용자 동기화보다 시간이 오래 걸립니다. 할당된 그룹 수와 크기는 둘 다 성능에 영향을 줍니다. 애플리케이션에 [그룹 개체 동기화가 사용되는 매핑](customize-application-attributes.md#editing-group-attribute-mappings)이 있는 경우 그룹 이름 및 멤버 자격과 같은 그룹 속성이 사용자와 함께 동기화됩니다. 이러한 추가 동기화 때문에 사용자 객체만 동기화하는 것보다 시간이 오래 걸립니다.

- 성능이 문제가 되고 테넌트의 대다수 사용자 및 그룹을 프로비전하려고 하면 범위 지정 필터를 사용합니다. 범위 지정 필터를 사용하면 특정 특성 값에 따라 사용자를 필터링하여 프로비저닝 서비스가 Azure AD에서 추출하는 데이터를 미세 조정할 수 있습니다. 범위 지정 필터에 대한 자세한 내용은 [범위 지정 필터를 사용한 특성 기반 애플리케이션 프로비전](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계
[Azure Active Directory를 사용하여 SaaS 애플리케이션의 사용자를 자동으로 프로비전 및 프로비전 해제](user-provisioning.md)
