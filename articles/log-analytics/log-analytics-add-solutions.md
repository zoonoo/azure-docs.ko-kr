---
title: 솔루션 갤러리에서 Log Analytics 솔루션 추가 | Microsoft Docs
description: Log Analytics 솔루션은 특정 문제 영역을 중심으로 피벗된 메트릭을 제공하는 논리, 시각화 및 데이터 획득 규칙의 모음입니다.
services: log-analytics
documentationcenter: ''
author: bandersmsft
manager: jwhit
editor: ''

ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/10/2016
ms.author: banders

---
# <a name="add-log-analytics-solutions-from-the-solutions-gallery"></a>솔루션 갤러리에서 Log Analytics 솔루션 추가
Log Analytics 솔루션은 특정 문제 영역을 중심으로 피벗된 메트릭을 제공하는 **논리**, **시각화** 및 **데이터 획득 규칙**의 모음입니다. 이 문서에서는 Log Analytics에서 지원되는 솔루션을 나열하고 솔루션 갤러리를 사용하여 솔루션을 추가 및 제거하는 방법을 보여 줍니다.

솔루션은

* 운영 문제를 보다 빠르게 조사 및 해결하고,
* 다양한 유형의 컴퓨터 데이터를 수집 및 상호 연결하며,
* 용량 계획, 패치 상태 보고 및 보안 감사와 같은 활동으로 문제를 예방하는 데 도움이 되는 보다 심층적인 통찰력을 제공합니다.

> [!NOTE]
> Log Analytics에는 로그 검색 기능이 포함되어 있으므로 이 기능을 사용하기 위해 별도의 솔루션을 설치할 필요가 없습니다. 그러나 솔루션 갤러리에서 솔루션을 추가하여 데이터 시각화, 제안 된 검색 및 정보를 가져올 수 있습니다.
> 
> 

솔루션을 추가한 후에는 데이터가 사용자 인프라의 서버에서 수집되고 OMS 서비스로 전송됩니다. OMS 서비스에서 처리는 일반적으로 몇 분 정도에서 한 시간이 소요됩니다. 서비스에서 데이터를 처리한 후에는 OMS에서 볼 수 있습니다.

더 이상 필요 없는 경우는 솔루션을 쉽게 제거할 수 있습니다. 솔루션을 제거하면 해당 데이터가 OMS로 전송되지 않으므로 일일 할당량(있는 경우)에 의해 사용되는 데이터의 양이 줄어듭니다.

## <a name="solutions-supported-by-the-microsoft-monitoring-agent"></a>Microsoft Monitoring Agent에서 지원하는 솔루션
현재 Microsoft Monitoring Agent를 사용하여 OMS에 연결된 서버는 다음을 포함하여 사용 가능한 솔루션의 대부분을 사용할 수 있습니다.

* Active Directory 평가
* 경고 관리(SCOM 경고 제외)
* 맬웨어 방지
* 변경 내용 추적
* 보안
* SQL 평가
* 시스템 업데이트

그러나 다음 솔루션은 Microsoft Monitoring Agent에서 지원되지 *않으며* SCOM(System Center Operations Manager)이 필요합니다.

* 경고 관리(SCOM 경고 포함)
* 용량 관리
* 구성 평가

SCOM 에이전트를 Log Analytics에 연결하는 방법에 대한 자세한 내용은 [Log Analytics에 Operations Manager 연결](log-analytics-om-agents.md) 을 참조하세요.

### <a name="to-add-a-solution-using-the-solutions-gallery"></a>솔루션 갤러리를 사용하여 솔루션을 추가하려면
1. OMS의 개요 페이지에서 **솔루션 갤러리** 타일을 클릭합니다.    
    ![솔루션 갤러리](./media/log-analytics-add-solutions/sol-gallery.png)
2. OMS 솔루션 갤러리 페이지에서 사용 가능한 각 솔루션에 대해 알아봅니다. OMS에 추가할 솔루션의 이름을 클릭합니다.
3. 선택한 솔루션에 대한 페이지에서 솔루션에 대한 자세한 정보가 표시됩니다. **추가**를 클릭합니다.
4. 추가한 솔루션의 새 타일이 OMS의 개요 페이지에 표시되며 OMS 서비스가 데이터를 처리한 후 새 타일을 사용하여 시작할 수 있습니다.

## <a name="to-configure-solutions"></a>솔루션을 구성하려면
1. 몇 가지 솔루션을 구성해야 합니다. 예를 들어 자동화, Azure Site Recovery 및 백업을 사용하려면 먼저 구성해야 합니다.
2. 이러한 솔루션에 대해 개요 페이지에서 해당 타일을 클릭합니다.  
    ![솔루션 구성](./media/log-analytics-add-solutions/configure-additional.png)
3. 그런 다음 필요한 정보와 함께 솔루션을 구성하고 **저장**을 클릭합니다.  
    ![솔루션 구성](./media/log-analytics-add-solutions/configure.png)

### <a name="to-remove-a-solution-using-the-solutions-gallery"></a>솔루션 갤러리를 사용하여 솔루션을 제거하려면
1. OMS의 개요 페이지에서 **설정** 타일을 클릭합니다.
2. 설정 페이지의 솔루션 탭에서 제거할 솔루션에 대해 **제거** 를 클릭합니다.
3. 확인 대화 상자에서 **예** 를 클릭하여 솔루션을 제거합니다.

## <a name="data-collection-details-for-oms-features-and-solutions"></a>OMS 기능 및 솔루션에 대한 데이터 수집 정보
다음 표에서는 데이터 수집 방법 및 OMS 기능과 솔루션에 대해 데이터가 수집되는 방식에 대한 기타 세부 정보를 보여 줍니다. 직접 에이전트와 SCOM 에이전트는 근본적으로는 동일하지만, 직접 에이전트에는 OMS 작업 영역에 연결하고 프로기를 통해 전달할 수 있는 추가적인 기능이 포함됩니다. SCOM 에이전트를 사용하는 경우 OMS 에이전트와의 통신을 위해 OMS 에이전트로서 대상이 되어야 합니다. 이 표의 SCOM 에이전트는 SCOM에 연결된 OMS 에이전트입니다. 기존 SCOM 환경을 OMS에 연결하기 위한 자세한 내용은 [Log Analytics에 Operations Manager 연결](log-analytics-om-agents.md)을 참조하세요.

> [!NOTE]
> 사용하는 에이전트 유형에 따라 다음 조건을 통해 OMS에 데이터를 보내는 방식이 결정됩니다.
> 
> 

* 직접 에이전트 또는 SCOM 연결 OMS 에이전트를 사용합니다.
* SCOM이 필요한 경우 솔루션에 대한 SCOM 에이전트 데이터가 항상 SCOM 관리 그룹을 통해 OMS로 전송됩니다.  또한 SCOM이 필요한 경우 솔루션에서 SCOM 에이전트만 사용됩니다.
* SCOM이 필요하지 않으며 표에서 SCOM 에이전트 데이터가 관리 그룹을 통해 OMS로 전송된다고 나타날 경우 SCOM 에이전트 데이터는 항상 관리 그룹을 통해 OMS로 보내집니다. 직접 에이전트는 관리 그룹을 건너뛰고 데이터를 직접 OMS로 보냅니다.
* SCOM 에이전트 데이터가 관리 그룹을 사용하여 전송되지 않는 경우 데이터가 관리 그룹을 건너뛰고 직접 OMS로 보내집니다.

| 데이터 형식 | 플랫폼 | 직접 에이전트 | SCOM 에이전트 | Azure 저장소 | SCOM 필요? | 관리 그룹을 통해 전송되는 SCOM 에이전트 데이터 | 수집 빈도 |
| --- | --- | --- | --- | --- | --- | --- | --- |
| AD 평가 |Windows |![예](./media/log-analytics-add-solutions/oms-bullet-green.png) |![예](./media/log-analytics-add-solutions/oms-bullet-green.png) |![아니요](./media/log-analytics-add-solutions/oms-bullet-red.png) |![아니요](./media/log-analytics-add-solutions/oms-bullet-red.png) |![예](./media/log-analytics-add-solutions/oms-bullet-green.png) |7 일 |
| AD 복제 상태 |Windows |![예](./media/log-analytics-add-solutions/oms-bullet-green.png) |![예](./media/log-analytics-add-solutions/oms-bullet-green.png) |![아니요](./media/log-analytics-add-solutions/oms-bullet-red.png) |![아니요](./media/log-analytics-add-solutions/oms-bullet-red.png) |![아니요](./media/log-analytics-add-solutions/oms-bullet-red.png) |5일 |
| 경고(Nagios) |Linux |![예](./media/log-analytics-add-solutions/oms-bullet-green.png) |![아니요](./media/log-analytics-add-solutions/oms-bullet-red.png) |![아니요](./media/log-analytics-add-solutions/oms-bullet-red.png) |![아니요](./media/log-analytics-add-solutions/oms-bullet-red.png) |![아니요](./media/log-analytics-add-solutions/oms-bullet-red.png) |도착 시 |
| 경고(Zabbix) |Linux |![예](./media/log-analytics-add-solutions/oms-bullet-green.png) |![아니요](./media/log-analytics-add-solutions/oms-bullet-red.png) |![아니요](./media/log-analytics-add-solutions/oms-bullet-red.png) |![아니요](./media/log-analytics-add-solutions/oms-bullet-red.png) |![아니요](./media/log-analytics-add-solutions/oms-bullet-red.png) |1분 |
| 경고(Operations Manager) |Windows |![아니요](./media/log-analytics-add-solutions/oms-bullet-red.png) |![예](./media/log-analytics-add-solutions/oms-bullet-green.png) |![아니요](./media/log-analytics-add-solutions/oms-bullet-red.png) |![예](./media/log-analytics-add-solutions/oms-bullet-green.png) |![예](./media/log-analytics-add-solutions/oms-bullet-green.png) |3분 |
| 맬웨어 방지 |Windows |![예](./media/log-analytics-add-solutions/oms-bullet-green.png) |![예](./media/log-analytics-add-solutions/oms-bullet-green.png) |![아니요](./media/log-analytics-add-solutions/oms-bullet-red.png) |![아니요](./media/log-analytics-add-solutions/oms-bullet-red.png) |![예](./media/log-analytics-add-solutions/oms-bullet-green.png) |매시간 |
| 용량 관리 |Windows |![아니요](./media/log-analytics-add-solutions/oms-bullet-red.png) |![예](./media/log-analytics-add-solutions/oms-bullet-green.png) |![아니요](./media/log-analytics-add-solutions/oms-bullet-red.png) |![예](./media/log-analytics-add-solutions/oms-bullet-green.png) |![예](./media/log-analytics-add-solutions/oms-bullet-green.png) |매시간 |
| 변경 내용 추적 |Windows |![예](./media/log-analytics-add-solutions/oms-bullet-green.png) |![예](./media/log-analytics-add-solutions/oms-bullet-green.png) |![아니요](./media/log-analytics-add-solutions/oms-bullet-red.png) |![아니요](./media/log-analytics-add-solutions/oms-bullet-red.png) |![예](./media/log-analytics-add-solutions/oms-bullet-green.png) |매시간 |
| 변경 내용 추적 |Linux |![예](./media/log-analytics-add-solutions/oms-bullet-green.png) |![아니요](./media/log-analytics-add-solutions/oms-bullet-red.png) |![아니요](./media/log-analytics-add-solutions/oms-bullet-red.png) |![아니요](./media/log-analytics-add-solutions/oms-bullet-red.png) |![아니요](./media/log-analytics-add-solutions/oms-bullet-red.png) |매시간 |
| 구성 평가(레거시 관리자) |Windows |![아니요](./media/log-analytics-add-solutions/oms-bullet-red.png) |![예](./media/log-analytics-add-solutions/oms-bullet-green.png) |![아니요](./media/log-analytics-add-solutions/oms-bullet-red.png) |![예](./media/log-analytics-add-solutions/oms-bullet-green.png) |![예](./media/log-analytics-add-solutions/oms-bullet-green.png) |하루에 2회 |
| ETW |Windows |![아니요](./media/log-analytics-add-solutions/oms-bullet-red.png) |![아니요](./media/log-analytics-add-solutions/oms-bullet-red.png) |![예](./media/log-analytics-add-solutions/oms-bullet-green.png) |![아니요](./media/log-analytics-add-solutions/oms-bullet-red.png) |![아니요](./media/log-analytics-add-solutions/oms-bullet-red.png) |5분 |
| IIS 로그 |Windows |![예](./media/log-analytics-add-solutions/oms-bullet-green.png) |![예](./media/log-analytics-add-solutions/oms-bullet-green.png) |![예](./media/log-analytics-add-solutions/oms-bullet-green.png) |![아니요](./media/log-analytics-add-solutions/oms-bullet-red.png) |![아니요](./media/log-analytics-add-solutions/oms-bullet-red.png) |5분 |
| Key Vault |Windows |![아니요](./media/log-analytics-add-solutions/oms-bullet-red.png) |![아니요](./media/log-analytics-add-solutions/oms-bullet-red.png) |![예](./media/log-analytics-add-solutions/oms-bullet-green.png) |![아니요](./media/log-analytics-add-solutions/oms-bullet-red.png) |![아니요](./media/log-analytics-add-solutions/oms-bullet-red.png) |10분 |
| 네트워크 응용 프로그램 게이트웨이 |Windows |![아니요](./media/log-analytics-add-solutions/oms-bullet-red.png) |![아니요](./media/log-analytics-add-solutions/oms-bullet-red.png) |![예](./media/log-analytics-add-solutions/oms-bullet-green.png) |![아니요](./media/log-analytics-add-solutions/oms-bullet-red.png) |![아니요](./media/log-analytics-add-solutions/oms-bullet-red.png) |10분 |
| 네트워크 보안 그룹 |Windows |![아니요](./media/log-analytics-add-solutions/oms-bullet-red.png) |![아니요](./media/log-analytics-add-solutions/oms-bullet-red.png) |![예](./media/log-analytics-add-solutions/oms-bullet-green.png) |![아니요](./media/log-analytics-add-solutions/oms-bullet-red.png) |![아니요](./media/log-analytics-add-solutions/oms-bullet-red.png) |10분 |
| Office 365 |Windows |![아니요](./media/log-analytics-add-solutions/oms-bullet-red.png) |![아니요](./media/log-analytics-add-solutions/oms-bullet-red.png) |![아니요](./media/log-analytics-add-solutions/oms-bullet-red.png) |![아니요](./media/log-analytics-add-solutions/oms-bullet-red.png) |![아니요](./media/log-analytics-add-solutions/oms-bullet-red.png) |알림 시 |
| 성능 카운터 |Windows |![예](./media/log-analytics-add-solutions/oms-bullet-green.png) |![예](./media/log-analytics-add-solutions/oms-bullet-green.png) |![아니요](./media/log-analytics-add-solutions/oms-bullet-red.png) |![아니요](./media/log-analytics-add-solutions/oms-bullet-red.png) |![아니요](./media/log-analytics-add-solutions/oms-bullet-red.png) |예약된 대로, 최소 10초 |
| 성능 카운터 |Linux |![예](./media/log-analytics-add-solutions/oms-bullet-green.png) |![아니요](./media/log-analytics-add-solutions/oms-bullet-red.png) |![아니요](./media/log-analytics-add-solutions/oms-bullet-red.png) |![아니요](./media/log-analytics-add-solutions/oms-bullet-red.png) |![아니요](./media/log-analytics-add-solutions/oms-bullet-red.png) |예약된 대로, 최소 10초 |
| 서비스 패브릭 |Windows |![아니요](./media/log-analytics-add-solutions/oms-bullet-red.png) |![아니요](./media/log-analytics-add-solutions/oms-bullet-red.png) |![예](./media/log-analytics-add-solutions/oms-bullet-green.png) |![아니요](./media/log-analytics-add-solutions/oms-bullet-red.png) |![아니요](./media/log-analytics-add-solutions/oms-bullet-red.png) |5분 |
| SQL 평가 |Windows |![예](./media/log-analytics-add-solutions/oms-bullet-green.png) |![예](./media/log-analytics-add-solutions/oms-bullet-green.png) |![아니요](./media/log-analytics-add-solutions/oms-bullet-red.png) |![아니요](./media/log-analytics-add-solutions/oms-bullet-red.png) |![예](./media/log-analytics-add-solutions/oms-bullet-green.png) |7 일 |
| SurfaceHub |Windows |![예](./media/log-analytics-add-solutions/oms-bullet-green.png) |![아니요](./media/log-analytics-add-solutions/oms-bullet-red.png) |![아니요](./media/log-analytics-add-solutions/oms-bullet-red.png) |![아니요](./media/log-analytics-add-solutions/oms-bullet-red.png) |![아니요](./media/log-analytics-add-solutions/oms-bullet-red.png) |도착 시 |
| Syslog |Linux |![예](./media/log-analytics-add-solutions/oms-bullet-green.png) |![아니요](./media/log-analytics-add-solutions/oms-bullet-red.png) |![아니요](./media/log-analytics-add-solutions/oms-bullet-red.png) |![아니요](./media/log-analytics-add-solutions/oms-bullet-red.png) |![아니요](./media/log-analytics-add-solutions/oms-bullet-red.png) |Azure 저장소: 10분, 에이전트: 도착 시 |
| 시스템 업데이트 |Windows |![예](./media/log-analytics-add-solutions/oms-bullet-green.png) |![예](./media/log-analytics-add-solutions/oms-bullet-green.png) |![아니요](./media/log-analytics-add-solutions/oms-bullet-red.png) |![아니요](./media/log-analytics-add-solutions/oms-bullet-red.png) |![예](./media/log-analytics-add-solutions/oms-bullet-green.png) |하루에 최소 2회 및 업데이트를 설치하고 15분 후 |
| Windows 보안 이벤트 로그 |Windows |![예](./media/log-analytics-add-solutions/oms-bullet-green.png) |![예](./media/log-analytics-add-solutions/oms-bullet-green.png) |![예](./media/log-analytics-add-solutions/oms-bullet-green.png) |![아니요](./media/log-analytics-add-solutions/oms-bullet-red.png) |![아니요](./media/log-analytics-add-solutions/oms-bullet-red.png) |Azure 저장소: 10분, 에이전트: 도착 시 |
| Windows 방화벽 로그 |Windows |![예](./media/log-analytics-add-solutions/oms-bullet-green.png) |![예](./media/log-analytics-add-solutions/oms-bullet-green.png) |![아니요](./media/log-analytics-add-solutions/oms-bullet-red.png) |![아니요](./media/log-analytics-add-solutions/oms-bullet-red.png) |![아니요](./media/log-analytics-add-solutions/oms-bullet-red.png) |도착 시 |
| Windows 이벤트 로그 |Windows |![예](./media/log-analytics-add-solutions/oms-bullet-green.png) |![예](./media/log-analytics-add-solutions/oms-bullet-green.png) |![예](./media/log-analytics-add-solutions/oms-bullet-green.png) |![아니요](./media/log-analytics-add-solutions/oms-bullet-red.png) |![예](./media/log-analytics-add-solutions/oms-bullet-green.png) |Azure 저장소: 1분, 에이전트: 도착 시 |
| 실시간 데이터 |Windows(2012 R2/8.1 이상) |![예](./media/log-analytics-add-solutions/oms-bullet-green.png) |![예](./media/log-analytics-add-solutions/oms-bullet-green.png) |![아니요](./media/log-analytics-add-solutions/oms-bullet-red.png) |![아니요](./media/log-analytics-add-solutions/oms-bullet-red.png) |![아니요](./media/log-analytics-add-solutions/oms-bullet-red.png) |1분마다 |

## <a name="log-analytics-preview-solutions-and-features"></a>Log Analytics 미리 보기 솔루션 및 기능
서비스를 실행하고 DevOps 방식을 따라 고객과 연계하여 기능 및 솔루션을 개발할 수 있습니다.

비공개 미리 보기 중 소규모 고객 그룹에게 기능 또는 솔루션의 구현에 대한 액세스를 제공하여 피드백을 얻고 개선할 수 있습니다. 이 초기 구현에는 최소한의 기능 및 운영 기능이 있습니다.

우리의 목표는 작동하고 작동하지 않는 항목을 찾을 수 있도록 작업을 빠르게 시도하는 것입니다. 비공개 미리 보기 고객의 의견이 공개 미리 보기에 대한 준비가 되었다고 알릴 때까지 이 프로세스를 통해 반복합니다.

공개 미리 보기 동안 모든 사용자가 기능 또는 솔루션을 사용할 수 있도록 하여 더 많은 피드백을 받고 확장성 및 효율성의 유효성을 검사합니다. 이 단계 동안:

* 미리 보기 기능은 설정 탭에 나타나고 모든 사용자가 사용할 수 있습니다.
* 갤러리를 통해 또는 게시된 스크립트를 사용하여 미리 보기 솔루션을 추가할 수 있습니다.

### <a name="what-should-i-know-about-preview-features-and-solutions?"></a>미리 보기 기능 및 솔루션에 대해 무엇을 알아야 하나요?
새로운 기능 및 솔루션을 소개하게 되어 흥분을 감추지 못하며 이를 개발하기 위해 고객과 함께 작업하고자 합니다.

미리 보기 기능 및 솔루션은 모든 사용자에게 적합하지 않으므로 비공개 미리 보기에 참가를 요청하거나 공개 미리 보기를 사용하기 전에 개발 중인 것과 작업하는 데 문제가 없는지 확인하세요.

포털을 통해 미리 보기 기능을 사용할 때 기능이 미리 보기 상태임을 알리는 경고가 표시됩니다.

#### <a name="for-both-*private*-and-*public*-preview"></a>*비공개* 및 *공개* 미리 보기의 경우
다음이 공개 및 비공개 미리 보기 모두에 적용됩니다.

* 항상 올바르게 작동하지 않을 수 있습니다.
  * 사소한 불편에서 전혀 작동하지 않는 것에 이르는 문제 범위
* 미리 보기의 경우 시스템/환경에 부정적인 영향을 줄 수 있습니다.
  * OMS를 사용하는 시스템에 부정적인 영향이 발생하는 것을 방지하려고 노력하지만 때때로 예기치 않은 상황이 발생합니다.
* 데이터 손실.손상이 발생할 수 있습니다.
* 문제를 해결하기 위해 진단 로그 또는 다른 데이터를 수집하도록 요청할 수 있습니다.
* 기능 또는 솔루션은 제거될 수 있습니다(일시적으로 또는 영구적으로).
  * 미리 보기 중 알게 된 것을 기반으로 기능 또는 솔루션을 공개하지 않도록 결정할 수 있습니다.
* 미리 보기는 작동하지 않거나 모든 구성으로 테스트되지 않을 수 있으며 다음을 제한할 수도 있습니다.
  * 사용할 수 있는 운영 체제(예: 미리 보기 동안 기능은 Linux에만 적용될 수 있음)
  * 사용할 수 있는 에이전트(MMA, SCOM)의 유형(예: 미리 보기 동안 기능은 SCOM과 함께 작동할 수 없음)  
* 미리 보기 솔루션 및 기능은 서비스 수준 계약에서 다루지 않습니다.
* 미리 보기 기능의 사용은 사용 요금을 부과합니다.
* 기능 또는 기능에 필요한 기능/유용한 솔루션은 누락되거나 불완전할 수 있습니다
* 기능/솔루션은 모든 지역에서 사용하지 못할 수도 있습니다.
* 기능/솔루션을 지역화할 수 없습니다.
* 기능/솔루션은 사용할 수 있는 고객 또는 장치 수에 제한이 있을 수 있습니다.
* 구성 작업을 수행하고 솔루션/기능을 사용하려면 스크립트를 사용해야 할 수 있습니다.
* 사용자 인터페이스(UI)는 불완전하고 매일 변경될 수 있습니다.
* 공개 미리 보기는 프로덕션/중요 시스템에 적합하지 않을 수 있습니다.

#### <a name="for-*private*-preview"></a>*비공개* 미리 보기의 경우
위의 항목 외에도 다음은 비공개 미리 보기에 해당되는 것입니다.

* 더 나은 기능/솔루션을 만들 수 있도록 경험에 대한 의견을 제공해 주기를 기대합니다.
* 설문 조사, 전화 또는 전자 메일을 사용하여 피드백을 받을 수도 있습니다.
* 항상 올바르게 작동하지 않습니다.
* 참여에 대한 비밀 유지 계약(NDA)을 요구하거나 기밀 콘텐츠를 포함할 수 있습니다.
  * 블로그, 트윗 또는 제 3자와 소통하기 전에 미리 보기에 대한 프로그램 관리자 책임을 확인하여 공개에 대한 제한을 이해하세요.
* 프로덕션/중요 시스템에서 실행하지 마십시오.

### <a name="how-do-i-get-access-to-private-preview-features-and-solutions?"></a>비공개 미리 보기 기능 및 솔루션에 대한 액세스를 어떻게 받습니까?
미리 보기에 따라 여러 가지 방법을 통해 비공개 미리 보기에 고객을 초대합니다.

* 월별 고객 설문 조사에 응답하고 연락할 권한을 제공하면 비공개 미리 보기에 초대될 가능성이 향상됩니다.
* Microsoft 계정 팀은 사용자를 지정할 수 있습니다.
* 트위터 [msopsmgmt](https://twitter.com/msopsmgmt)
* 자세한 공유 커뮤니티 이벤트에 따라 등록할 수 있습니다. 밋업, 회의 및 온라인 커뮤니티에서 찾아보세요.

## <a name="next-steps"></a>다음 단계
* [로그를 검색](log-analytics-log-searches.md) 하여 솔루션에서 수집한 자세한 정보를 확인합니다.

<!--HONumber=Oct16_HO2-->


