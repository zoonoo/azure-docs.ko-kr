---
title: Azure Security Center 통합을 사용 하 여 Azure VMware 솔루션 Vm 보호
description: Azure Security Center의 단일 대시보드에서 Azure의 기본 보안 도구를 사용 하 여 Azure VMware 솔루션 Vm을 보호 하는 방법에 대해 알아봅니다.
ms.topic: how-to
ms.date: 11/06/2020
ms.openlocfilehash: 53669f2988a7ff7ab1150b155a65c7a187c6f1c8
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/08/2020
ms.locfileid: "94370389"
---
# <a name="protect-your-azure-vmware-solution-vms-with-azure-security-center-integration"></a>Azure Security Center 통합을 사용 하 여 Azure VMware 솔루션 Vm 보호

Azure native security tools는 Azure, Azure VMware 솔루션 및 온-프레미스 Vm (가상 머신)의 하이브리드 환경에 대 한 보안 인프라를 제공 합니다. 이 문서에서는 하이브리드 환경 보안을 위해 Azure tools를 설정 하는 방법을 보여 줍니다. 다양 한 도구를 사용 하 여 다양 한 유형의 위협을 식별 하 고 해결 합니다.

## <a name="azure-native-services"></a>Azure 기본 서비스

다음은 각 Azure native service에 대 한 간략 한 요약입니다.

- **Log Analytics 작업 영역:** Log Analytics 작업 영역은 로그 데이터를 저장 하는 고유한 환경입니다. 각 작업 영역에는 고유한 데이터 리포지토리 및 구성이 있습니다. 데이터 원본 및 솔루션은 특정 작업 영역에 데이터를 저장 하도록 구성 됩니다.
- **Azure Security Center:** Azure Security Center는 통합 인프라 보안 관리 시스템입니다. 데이터 센터의 보안 상태를 강화 하 고 클라우드 또는 온-프레미스에서 하이브리드 작업을 통해 고급 위협 방지를 제공 합니다.
- **Azure 센티널:** Azure 센티널은 클라우드 기본 SIEM (보안 정보 이벤트 관리) 및 대화 충성도 (security orchestration 자동화 된 응답) 솔루션입니다. 환경 전체에서 지능형 보안 분석 및 위협 인텔리전스를 제공 합니다. 이는 경고 검색, 위협 표시 유형, 자동 관리 구하기 및 위협 대응을 위한 단일 솔루션입니다.

## <a name="topology"></a>토폴로지

![Azure 통합 보안의 아키텍처를 보여 주는 다이어그램](media/azure-security-integration/azure-integrated-security-architecture.png)

Log Analytics 에이전트는 Azure, Azure VMware 솔루션 및 온-프레미스 Vm에서 로그 데이터를 수집할 수 있도록 합니다. 로그 데이터는 Azure Monitor 로그에 전송 되 고 Log Analytics 작업 영역에 저장 됩니다. 새 Vm 및 기존 Vm에 대해 Arc 사용 서버 [VM 확장 지원을](../azure-arc/servers/manage-vm-extensions.md) 사용 하 여 Log Analytics 에이전트를 배포할 수 있습니다. 

Log Analytics 작업 영역에서 로그를 수집한 후에는 Azure Security Center를 사용 하 여 Log Analytics 작업 영역을 구성할 수 있습니다. Azure Security Center는 Azure VMware 솔루션 Vm의 취약점 상태를 평가 하 고 위험 취약성에 대 한 경고를 발생 시킵니다. 예를 들어 누락 된 운영 체제 패치, 보안 잘못 된 구성을 평가 하 고 [endpoint protection](../security-center/security-center-services.md)을 평가 합니다.

경고 검색, 위협 표시 유형, 자동 관리 구하기 및 위협 응답을 위해 Azure 센티널을 사용 하 여 Log Analytics 작업 영역을 구성할 수 있습니다. 위의 다이어그램에서 Azure Security Center는 Azure Security Center 커넥터를 사용 하 여 Azure 센티널에 연결 됩니다. Azure Security Center는 Azure 센티널에 환경 취약성을 전달 하 여 인시던트를 만들고 다른 위협에 매핑합니다. 또한 예약 된 규칙 쿼리를 만들어 원치 않는 활동을 검색 하 고이를 인시던트로 변환할 수 있습니다.

## <a name="benefits"></a>이점

- Azure native services는 azure, Azure VMware 솔루션 및 온-프레미스 서비스에서 하이브리드 환경 보안에 사용할 수 있습니다.
- Log Analytics 작업 영역을 사용 하 여 데이터 또는 로그를 단일 지점으로 수집 하 고 다른 Azure native services에 동일한 데이터를 제공할 수 있습니다.
- Azure Security Center는 파일 무결성 모니터링, fileless 공격 감지, 운영 체제 패치 평가, 보안 잘못 되지 않은 평가 및 endpoint protection 평가와 같은 보안 기능을 제공 합니다.
- Azure 센티널을 사용 하면 다음을 수행할 수 있습니다.
    - 온-프레미스와 여러 클라우드에 있는 모든 사용자, 장치, 응용 프로그램 및 인프라에서 클라우드 규모의 데이터를 수집 합니다.
    - 이전에 감지 되지 않은 위협 검색.
    - 인공 지능을 사용 하 여 위협을 조사 하 고 대규모의 의심 스러운 활동을 사냥 하세요.
    - 일반 작업의 기본 제공 오케스트레이션 및 자동화로 빠르게 인시던트에 대응합니다.

## <a name="create-a-log-analytics-workspace"></a>Log Analytics 작업 영역 만들기

다양 한 원본에서 데이터를 수집 하려면 Log Analytics 작업 영역이 필요 합니다. [Azure Portal에서 Log Analytics 작업 영역 만들기](../azure-monitor/learn/quick-create-workspace.md)의 단계를 참조 하세요. 

## <a name="deploy-security-center-and-configure-azure-vmware-solution-vms"></a>Azure VMware 솔루션 Vm Security Center 배포 및 구성

Azure Security Center는 미리 구성 된 도구 이며 배포를 요구 하지 않습니다. Azure Portal에서 **Security Center** 를 검색 하 여 선택 합니다.

### <a name="enable-azure-defender"></a>Azure Defender 사용

Azure Defender는 온-프레미스와 클라우드 모두에서 하이브리드 워크 로드를 통해 Azure Security Center의 advanced threat protection을 확장 합니다. 따라서 Azure VMware 솔루션 Vm을 보호 하려면 Azure Defender를 사용 하도록 설정 해야 합니다. 

1. Security Center에서 **시작** 을 선택 합니다.

2. **업그레이드** 탭을 선택 하 고 구독 또는 작업 영역을 선택 합니다. 

3. Azure Defender를 사용하도록 설정하려면 **업그레이드** 를 선택합니다.

## <a name="add-azure-vmware-solution-vms-to-security-center"></a>Security Center에 Azure VMware 솔루션 Vm 추가

1. Azure Portal에서 **Azure Arc** 를 검색 하 고 선택 합니다.

2. 리소스에서 **서버** 를 선택 하 고 **+ 추가** 를 선택 합니다.

    :::image type="content" source="media/azure-security-integration/add-server-to-azure-arc.png" alt-text="Azure에 azure VMware 솔루션 VM을 추가 하기 위한 Azure Arc 서버 페이지를 보여 주는 스크린샷":::

3. **스크립트 생성** 을 선택 합니다.
 
    :::image type="content" source="media/azure-security-integration/add-server-using-script.png" alt-text="대화형 스크립트를 사용 하 여 서버를 추가 하기 위한 옵션을 보여 주는 Azure Arc 페이지의 스크린샷"::: 
 
4. **필수 구성 요소** 탭에서 **다음** 을 선택 합니다.

5. **리소스 세부 정보** 탭에서 다음 세부 정보를 입력 합니다. 
    - Subscription
    - 리소스 그룹
    - 지역 
    - 운영 체제
    - 프록시 서버 정보
    
    그런 후 **다음: 태그** 를 선택 합니다.

6. **태그** 탭에서 **다음** 을 선택 합니다.

7. **스크립트 다운로드 및 실행** 탭에서 **다운로드** 를 선택 합니다.

8. 운영 체제를 지정 하 고 Azure VMware 솔루션 VM에서 스크립트를 실행 합니다.

## <a name="view-recommendations-and-passed-assessments"></a>권장 사항 및 통과 된 평가 보기

1. Azure Security Center의 왼쪽 창에서 **인벤토리** 를 선택 합니다.

2. 리소스 종류에 대해 **서버-Azure Arc** 를 선택 합니다.
 
     :::image type="content" source="media/azure-security-integration/select-resource-in-security-center.png" alt-text="리소스 유형에서 선택한 서버-Azure Arc를 표시 하는 Azure Security Center Inventory 페이지의 스크린샷":::

3. 리소스의 이름을 선택 합니다. 리소스의 보안 상태 세부 정보를 표시 하는 페이지가 열립니다.

4. **권장 사항 목록** 에서 **권장 사항** , **통과 된 평가** 및 **사용할 수 없는 평가** 탭을 선택 하 여 이러한 세부 정보를 확인 합니다.

    :::image type="content" source="media/azure-security-integration/view-recommendations-assessments.png" alt-text="보안 권장 사항 및 평가를 보여 주는 Azure Security Center의 스크린샷":::

## <a name="deploy-an-azure-sentinel-workspace"></a>Azure 센티널 작업 영역 배포

Azure 센티널은 Log Analytics 작업 영역을 기반으로 빌드됩니다. Azure 센티널을 등록 하는 첫 번째 단계는 해당 목적으로 사용 하려는 Log Analytics 작업 영역을 선택 하는 것입니다.

1. Azure Portal에서 **Azure 센티널** 를 검색 하 고 선택 합니다.

2. Azure 센티널 작업 영역 페이지에서 **+ 추가** 를 선택 합니다.

3. Log Analytics 작업 영역을 선택 하 고 **추가** 를 선택 합니다.

## <a name="enable-data-collector-for-security-events-on-azure-vmware-solution-vms"></a>Azure VMware 솔루션 Vm에서 보안 이벤트에 대 한 데이터 수집기 사용

이제 데이터 소스 (이 경우 보안 이벤트)와 Azure 센티널을 연결할 준비가 되었습니다.

1. Azure 센티널 작업 영역 페이지에서 구성 된 작업 영역을 선택 합니다.

2. 구성 아래에서 **데이터 커넥터** 를 선택 합니다.

3. 커넥터 이름 열의 목록에서 **보안 이벤트** 를 선택 하 고 **커넥터 페이지 열기** 를 선택 합니다.

4. 커넥터 페이지에서 스트리밍할 이벤트를 선택한 다음 **변경 내용 적용** 을 선택 합니다.

    :::image type="content" source="media/azure-security-integration/select-events-you-want-to-stream.png" alt-text="스트리밍할 이벤트를 선택할 수 있는 Azure 센티널의 보안 이벤트 페이지 스크린샷":::

## <a name="connect-azure-sentinel-with-azure-security-center"></a>Azure Security Center를 사용 하 여 Azure 센티널 연결  

1. Azure 센티널 작업 영역 페이지에서 구성 된 작업 영역을 선택 합니다.

2. 구성 아래에서 **데이터 커넥터** 를 선택 합니다.

3. 목록에서 **Azure Security Center** 을 선택한 다음 **커넥터 페이지 열기** 를 선택 합니다.

    :::image type="content" source="media/azure-security-integration/connect-security-center-with-azure-sentinel.png" alt-text="Azure 센티널에서 Azure Security Center 연결 하기 위한 선택 항목을 보여 주는 Azure 센티널의 데이터 커넥터 페이지 스크린샷.":::

4. **연결** 을 선택 하 여 Azure Security Center를 Azure 센티널과 연결 합니다.

5. **인시던트 만들기** 를 사용 하 여 Azure Security Center에 대 한 인시던트를 생성 합니다.

## <a name="create-rules-to-identify-security-threats"></a>보안 위협을 식별 하는 규칙 만들기

데이터 원본을 Azure 센티널에 연결한 후 검색 된 위협에 따라 경고를 생성 하는 규칙을 만들 수 있습니다. 다음 예제에서는 잘못 된 암호를 사용 하 여 Windows server에 로그인 하려는 시도를 식별 하는 규칙을 만듭니다.

1. Azure 센티널 개요 페이지의 구성에서 **분석** 을 선택 합니다.

2. 구성 아래에서 **Analytics** 를 선택 합니다.

3. **+ 만들기** 를 선택 하 고 드롭다운에서 **예약 된 쿼리 규칙** 을 선택 합니다.

4. **일반** 탭에서 필요한 정보를 입력 합니다.

    - Name
    - 설명
    - 공학적
    - 심각도
    - 상태

    **다음: 규칙 논리 >설정** 을 선택 합니다.

5. **규칙 논리 설정** 탭에서 필요한 정보를 입력 합니다.

    - 규칙 쿼리 (예제 쿼리 표시)
    
        ```
        SecurityEvent
        |where Activity startswith '4625'
        |summarize count () by IpAddress,Computer
        |where count_ > 3
        ```
        
    - 엔터티 매핑
    - 쿼리 예약
    - 경고 임계값
    - 이벤트 그룹화
    - 표시 안 함

    **다음** 을 선택합니다.

6. **인시던트 설정** 탭에서 **이 분석 규칙에 의해 트리거되는 경고에서 인시던트 만들기** 를 사용 하도록 설정 하 고 **다음: 자동화 된 응답 >** 을 선택 합니다.
 
    :::image type="content" source="media/azure-security-integration/create-new-analytic-rule-wizard.png" alt-text="Azure 센티널에 새 규칙을 만들기 위한 분석 규칙 마법사의 스크린샷 사용 하도록 설정 된이 분석 규칙에 따라 트리거되는 경고에서 인시던트 만들기를 보여 줍니다.":::

7. **다음: 검토 >** 를 선택 합니다.

8. **검토 및 만들기** 탭에서 정보를 검토 하 고 **만들기** 를 선택 합니다.

세 번째로 Windows server에 로그인 하지 못한 후에 생성 된 규칙은 실패 한 모든 시도에 대해 인시던트를 트리거합니다.

## <a name="view-generated-alerts"></a>생성 된 경고 보기

Azure 센티널을 사용 하 여 생성 된 인시던트를 볼 수 있습니다. 인시던트를 할당 하 고 해결 된 후에도 Azure 센티널 내에서 종료할 수 있습니다.

1. Azure 센티널 개요 페이지로 이동 합니다.

2. 위협 관리에서 **인시던트** 를 선택 합니다.

3. 인시던트를 선택 합니다. 그런 다음 해결을 위해 팀에 인시던트를 할당할 수 있습니다.

    :::image type="content" source="media/azure-security-integration/assign-incident.png" alt-text="인시던트가 선택 된 Azure 센티널 인시던트 페이지의 스크린샷 및 해결을 위한 인시던트를 할당 하는 옵션입니다.":::

    문제를 해결 한 후에는 종료할 수 있습니다.

## <a name="hunt-security-threats-with-queries"></a>쿼리를 사용 하 여 보안 위협 구하기

쿼리를 만들거나 Azure 센티널에서 사용 가능한 미리 정의 된 쿼리를 사용 하 여 사용자 환경에서 위협을 식별할 수 있습니다. 다음 단계에서는 미리 정의 된 쿼리를 실행 합니다.

1. Azure 센티널 개요 페이지로 이동 합니다.

2. 위협 관리에서 **구하기** 를 선택 합니다. 미리 정의 된 쿼리 목록이 표시 됩니다.

3. 쿼리를 선택한 다음 **쿼리 실행** 을 선택 합니다.

4. 결과 **보기** 를 선택 하 여 결과를 확인 합니다.

### <a name="create-a-new-query"></a>새 쿼리 만들기

1.  위협 관리에서 **구하기** , **+ 새 쿼리** 를 차례로 선택 합니다.

    :::image type="content" source="media/azure-security-integration/create-new-query.png" alt-text="+ 새 쿼리가 강조 표시 된 Azure 센티널 구하기 페이지의 스크린샷":::

2. 다음 정보를 입력 하 여 사용자 지정 쿼리를 만듭니다.

    - Name
    - 설명
    - 사용자 지정 쿼리
    - 매핑 입력
    - 공학적
    
3. **만들기** 를 선택합니다. 그런 다음 생성 된 쿼리를 선택 하 고 **쿼리를 실행** 하 고 결과를 **볼** 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [Azure Defender 대시보드](../security-center/azure-defender-dashboard.md)사용 방법에 대해 알아봅니다.
- [Azure Defender](../security-center/azure-defender.md)에서 제공 하는 모든 보호 범위를 살펴보세요.
- [Azure 센티널의 Advanced 다단계 공격 감지](../azure-monitor/learn/quick-create-workspace.md)에 대해 알아봅니다.
