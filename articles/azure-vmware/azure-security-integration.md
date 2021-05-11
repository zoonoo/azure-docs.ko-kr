---
title: Azure Security Center 통합을 사용하여 Azure VMware Solution VM 보호
description: Azure Security Center 대시보드에서 Azure의 기본 보안 도구를 사용하여 Azure VMware Solution VM을 보호합니다.
ms.topic: how-to
ms.date: 02/12/2021
ms.openlocfilehash: 7292ea4486a61f5b0cfd8f656d2763a3ce655e79
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100578260"
---
# <a name="protect-your-azure-vmware-solution-vms-with-azure-security-center-integration"></a>Azure Security Center 통합을 사용하여 Azure VMware Solution VM 보호

Azure 네이티브 보안 도구는 Azure, Azure VMware Solution 및 온-프레미스 VM(가상 머신)의 하이브리드 환경에 대한 보호를 제공합니다. 이 문서에서는 하이브리드 환경 보안을 위해 Azure 도구를 설정하는 방법을 보여 줍니다. 해당 도구를 사용하여 다양한 위협을 식별하고 해결합니다.

## <a name="azure-native-services"></a>Azure 네이티브 서비스

Azure 네이티브 서비스에 대한 간략한 요약은 다음과 같습니다.

- **Log Analytics 작업 영역:** Log Analytics 작업 영역은 로그 데이터를 저장하는 고유한 환경입니다. 각 작업 영역에는 고유한 데이터 리포지토리 및 구성이 있습니다. 데이터 원본 및 솔루션은 특정 작업 영역에 데이터를 저장하도록 구성됩니다.
- **Azure Security Center:** Azure Security Center는 통합 인프라 보안 관리 시스템입니다. 데이터 센터의 보안을 강화하고 클라우드 또는 온-프레미스에서 하이브리드 워크로드를 통해 고급 위협 방지 기능을 제공합니다.
- **Azure Sentinel:** Azure Sentinel은 클라우드 네이티브 SIEM(보안 정보 및 이벤트 관리) 솔루션입니다. 환경 전체에서 보안 분석, 경고 검색 및 자동화된 위협 응답을 제공합니다.

## <a name="topology"></a>토폴로지

![Azure 통합 보안 아키텍처를 보여 주는 다이어그램.](media/azure-security-integration/azure-integrated-security-architecture.png)

Log Analytics 에이전트는 Azure, Azure VMware Solution 및 온-프레미스 VM에서 로그 데이터를 수집할 수 있도록 합니다. 로그 데이터는 Azure Monitor 로그에 전송되고 Log Analytics 작업 영역에 저장됩니다. 새로운 및 기존의 VM에 대한 Arc 사용 서버 [VM 확장 지원](../azure-arc/servers/manage-vm-extensions.md)을 사용하여 Log Analytics 에이전트를 배포할 수 있습니다. 

Log Analytics 작업 영역에서 로그를 수집한 후에는 Azure Security Center를 사용하여 Log Analytics 작업 영역을 구성할 수 있습니다. Azure Security Center는 Azure VMware Solution VM의 취약점 상태를 평가하고 중요한 취약성에 대한 경고를 발생시킵니다. 예를 들어 누락된 운영 체제 패치, 잘못된 보안 구성과 [엔드포인트 보호](../security-center/security-center-services.md)를 평가합니다.

경고 검색, 위협 표시 유형, 헌팅 및 위협 응답을 위해 Azure Sentinel을 사용하여 Log Analytics 작업 영역을 구성할 수 있습니다. 위의 다이어그램에서 Azure Security Center는 Azure Security Center 커넥터를 사용하여 Azure Sentinel에 연결됩니다. Azure Security Center는 Azure Sentinel에 환경 취약성을 전달하여 인시던트를 만들고 다른 위협에 매핑합니다. 또한 예약된 규칙 쿼리를 만들어서 원치 않는 활동을 검색하고 이를 인시던트로 변환할 수 있습니다.

## <a name="benefits"></a>이점

- Azure 네이티브 서비스는 Azure, Azure VMware Solution 및 온-프레미스 서비스에서 하이브리드 환경 보안에 사용할 수 있습니다.
- Log Analytics 작업 영역을 사용하여 데이터 또는 로그를 단일 지점으로 수집하고 다른 Azure 네이티브 서비스에 동일한 데이터를 제공할 수 있습니다.
- Azure Security Center는 다음을 비롯한 다양한 기능을 제공합니다.
    - 파일 무결성 모니터링
    - 파일리스 공격 탐지
    - 운영 체제 패치 평가 
    - 잘못된 보안 구성 평가
    - Endpoint Protection 평가
- Azure Sentinel을 사용하여 다음을 수행할 수 있습니다.
    - 온-프레미스와 여러 클라우드에서 모든 사용자, 디바이스, 애플리케이션 및 인프라 전반에 걸쳐 클라우드 규모로 데이터를 수집합니다.
    - 이전에 탐지되지 않은 위협을 탐지합니다.
    - 인공 지능을 통해 위협을 조사하고 대규모로 의심스러운 활동을 헌팅합니다.
    - 일반 작업의 기본 제공 오케스트레이션 및 자동화로 빠르게 인시던트에 대응합니다.

## <a name="create-a-log-analytics-workspace"></a>Log Analytics 작업 영역 만들기

다양한 원본에서 데이터를 수집하려면 Log Analytics 작업 영역이 필요합니다. 자세한 내용은 [Azure Portal의 Log Analytics 작업 영역 만들기](../azure-monitor/logs/quick-create-workspace.md)를 참조하세요. 

## <a name="deploy-security-center-and-configure-azure-vmware-solution-vms"></a>Security Center 배포 및 Azure VMware Solution VM 구성

Azure Security Center는 배포가 필요하지 않은 미리 구성된 도구입니다. Azure Portal에서 **Security Center** 를 검색하여 선택합니다.

### <a name="enable-azure-defender"></a>Azure Defender 사용

Azure Defender는 온-프레미스와 클라우드에서 Azure Security Center의 고급 위협 방지를 하이브리드 워크로드 전반으로 확장합니다. 따라서 Azure VMware Solution VM을 보호하려면 Azure Defender를 사용하도록 설정해야 합니다. 

1. Security Center에서 **시작** 을 선택합니다.

2. **업그레이드** 탭을 선택하고 구독 또는 작업 영역을 선택합니다. 

3. Azure Defender를 사용하도록 설정하려면 **업그레이드** 를 선택합니다.

## <a name="add-azure-vmware-solution-vms-to-security-center"></a>Security Center에 Azure VMware Solution VM 추가

1. Azure Portal에서 **Azure Arc** 를 검색하고 선택합니다.

2. 리소스에서 **서버** 를 선택하고 **+추가** 를 선택합니다.

    :::image type="content" source="media/azure-security-integration/add-server-to-azure-arc.png" alt-text="Azure에 Azure VMware Solution VM을 추가하기 위한 Azure Arc 서버 페이지를 보여 주는 스크린샷.":::

3. **스크립트 생성** 을 선택합니다.
 
    :::image type="content" source="media/azure-security-integration/add-server-using-script.png" alt-text="대화형 스크립트를 사용하여 서버를 추가하기 위한 옵션을 보여 주는 Azure Arc 페이지의 스크린샷."::: 
 
4. **필수 구성 요소** 탭에서 **다음** 을 선택합니다.

5. **리소스 세부 정보** 탭에서 다음 세부 정보를 입력합니다. 
    - Subscription
    - Resource group
    - 지역 
    - 운영 체제
    - 프록시 서버 세부 정보
    
    이후 **다음: 태그** 를 선택합니다.

6. **태그** 탭에서 **다음** 을 선택합니다.

7. **스크립트 다운로드 및 실행** 탭에서 **다운로드** 를 선택합니다.

8. 운영 체제를 지정하고 Azure VMware Solution VM에서 스크립트를 실행합니다.

## <a name="view-recommendations-and-passed-assessments"></a>권장 사항 및 통과된 평가 보기

1. Azure Security Center의 왼쪽 창에서 **인벤토리** 를 선택합니다.

2. 리소스 종류의 경우, **서버 - Azure Arc** 를 선택합니다.
 
     :::image type="content" source="media/azure-security-integration/select-resource-in-security-center.png" alt-text="리소스 종류에서 선택한 서버 - Azure Arc를 표시하는 Azure Security Center 인벤토리 페이지의 스크린샷.":::

3. 리소스 이름을 선택합니다. 리소스의 보안 상태 세부 정보를 보여 주는 페이지가 열립니다.

4. **권장 사항 목록** 에서 **권장 사항**, **통과된 평가** 및 **사용할 수 없는 평가** 탭을 선택하여 해당 세부 정보를 확인합니다.

    :::image type="content" source="media/azure-security-integration/view-recommendations-assessments.png" alt-text="보안 추천 및 평가를 보여 주는 Azure Security Center의 스크린샷.":::

## <a name="deploy-an-azure-sentinel-workspace"></a>Azure Sentinel 작업 영역 배포

Azure Sentinel은 Log Analytics 작업 영역의 상위에 위치하고 있습니다. Azure Sentinel을 온보딩하는 첫 번째 단계는 해당 목적으로 사용하려는 Log Analytics 작업 영역을 선택하는 것입니다.

1. Azure Portal에서 **Azure Sentinel** 을 검색하고 선택합니다.

2. Azure Sentinel 작업 영역 페이지에서 **+추가** 를 선택합니다.

3. Log Analytics 작업 영역을 선택하고 **추가** 를 선택합니다.

## <a name="enable-data-collector-for-security-events-on-azure-vmware-solution-vms"></a>Azure VMware Solution VM에서 보안 이벤트에 대한 데이터 수집기 사용

이제 데이터 소스(이 경우 보안 이벤트)와 Azure Sentinel을 연결할 준비가 되었습니다.

1. Azure Sentinel 작업 영역 페이지에서 구성된 작업 영역을 선택합니다.

2. 구성 아래에서 **데이터 커넥터** 를 선택합니다.

3. 커넥터 이름 열 아래 목록에서 **보안 이벤트** 를 선택하고 **커넥터 페이지 열기** 를 선택합니다.

4. 커넥터 페이지에서 스트림 할 이벤트를 선택한 다음 **변경 내용 적용** 을 선택합니다.

    :::image type="content" source="media/azure-security-integration/select-events-you-want-to-stream.png" alt-text="스트림 할 이벤트를 선택할 수 있는 Azure Sentinel의 보안 이벤트 페이지 스크린샷.":::

## <a name="connect-azure-sentinel-with-azure-security-center"></a>Azure Security Center를 사용하여 Azure Sentinel 연결  

1. Azure Sentinel 작업 영역 페이지에서 구성된 작업 영역을 선택합니다.

2. 구성 아래에서 **데이터 커넥터** 를 선택합니다.

3. 목록에서 **Azure Security Center** 를 선택한 다음 **커넥터 페이지 열기** 를 선택합니다.

    :::image type="content" source="media/azure-security-integration/connect-security-center-with-azure-sentinel.png" alt-text="Azure Security Center를 Azure Sentinel과 연결하기 위한 선택 항목을 보여 주는 Azure Sentinel의 데이터 커넥터 페이지의 스크린샷.":::

4. **연결** 을 선택하여 Azure Security Center를 Azure Sentinel과 연결합니다.

5. **인시던트 만들기** 를 사용하도록 설정하여 Azure Security Center에 대한 인시던트를 생성합니다.

## <a name="create-rules-to-identify-security-threats"></a>보안 위협을 식별하는 규칙 만들기

데이터 원본을 Azure Sentinel에 연결한 후 검색된 위협에 대한 경고를 생성하는 규칙을 만들 수 있습니다. 다음 예제에서 잘못된 암호로 Windows 서버에 로그인하려고 시도하는 규칙을 만들어보겠습니다.

1. Azure Sentinel 개요 페이지의 구성에서 **분석** 을 선택합니다.

2. 구성에서 **분석** 을 선택합니다.

3. **+만들기** 를 선택하고 드롭다운에서 **예약된 쿼리 규칙** 을 선택합니다.

4. **기본** 탭에서 필수 정보를 입력합니다.

    - 이름
    - 설명
    - 전술
    - 심각도
    - 상태

    **다음: 규칙 논리 설정 >** 을 선택합니다.

5. **규칙 논리 설정** 탭에서 필요한 정보를 입력합니다.

    - 규칙 쿼리(예제 쿼리 여기 표시)
    
        ```
        SecurityEvent
        |where Activity startswith '4625'
        |summarize count () by IpAddress,Computer
        |where count_ > 3
        ```
        
    - 맵 엔터티
    - 쿼리 예약
    - 경고 임계값
    - 이벤트 그룹화
    - 표시 안 함

    **다음** 을 선택합니다.

6. **인시던트 설정** 탭에서 **이 분석 규칙에 의해 트리거되는 경고에서 인시던트 만들기** 를 사용하도록 설정하고 **다음: 자동화된 응답 >** 을 선택합니다.
 
    :::image type="content" source="media/azure-security-integration/create-new-analytic-rule-wizard.png" alt-text="Azure Sentinel에서 새 규칙을 만들기 위한 분석 규칙 마법사의 스크린샷으로, 활성화되면 이 규칙에 의해 트리거되는 경고에서 인시던트 만들기를 표시합니다.":::

7. **다음: 검토 >** 를 선택합니다.

8. **검토 및 만들기** 탭에서 정보를 검토하고 **만들기** 를 선택합니다.

세 번째로 Windows 서버에 로그인하지 못한 후에 생성된 규칙은 실패한 모든 시도에 대한 인시던트를 트리거합니다.

## <a name="view-alerts"></a>경고 보기

Azure Sentinel을 사용하여 생성된 인시던트를 볼 수 있습니다. Azure Sentinel 내에서 인시던트를 할당하고, 해결한 후에 종료까지 할 수 있습니다.

1. Azure Sentinel 개요 페이지로 이동합니다.

2. 위협 관리에서 **인시던트** 를 선택합니다.

3. 인시던트를 선택합니다. 그런 다음 해결을 위해 팀에 인시던트를 할당할 수 있습니다.

    :::image type="content" source="media/azure-security-integration/assign-incident.png" alt-text="선택된 인시던트가 있는 Azure Sentinel 인시던트 페이지 및 해결을 위한 인시던트를 할당하는 옵션의 스크린샷.":::

    문제를 해결한 후에는 종료할 수 있습니다.

## <a name="hunt-security-threats-with-queries"></a>쿼리를 사용하여 보안 위협 헌팅

쿼리를 만들거나 Azure Sentinel에서 사용 가능한 미리 정의된 쿼리를 사용하여 사용자 환경에서 위협을 식별할 수 있습니다. 다음 단계에서는 미리 정의된 쿼리를 실행합니다.

1. Azure Sentinel 개요 페이지로 이동합니다.

2. 위협 관리에서 **헌팅** 을 선택합니다. 미리 정의된 쿼리 목록이 나타납니다.

3. 쿼리를 선택한 다음 **쿼리 실행** 을 선택합니다.

4. **결과 보기** 를 선택하여 결과를 확인합니다.

### <a name="create-a-new-query"></a>새 쿼리 만들기

1.  위협 관리에서 **헌팅**, 그리고 **+새 쿼리** 를 차례로 선택합니다.

    :::image type="content" source="media/azure-security-integration/create-new-query.png" alt-text="강조 표시된 + 새 쿼리가 있는 Azure Sentinel 헌팅 페이지의 스크린샷.":::

2. 다음 정보를 입력하여 사용자 지정 쿼리를 만듭니다.

    - 이름
    - 설명
    - 사용자 지정 쿼리
    - 매핑 입력
    - 전술
    
3. **만들기** 를 선택합니다. 그런 다음 만들어진 쿼리를 선택하고 **쿼리를 실행** 하고 **결과 보기** 를 할 수 있습니다.

## <a name="next-steps"></a>다음 단계

이제 Azure VMware Solution VM을 보호하는 방법을 살펴보았으므로 다음 사항에 대해 알아볼 수 있습니다.

- [Azure Defender 대시보드](../security-center/azure-defender-dashboard.md) 사용
- [Azure Sentinel에서 고급 다단계 공격 감지](../azure-monitor/logs/quick-create-workspace.md).
- [Azure VMware Solution VM의 수명 주기 관리](lifecycle-management-of-azure-vmware-solution-vms.md).
