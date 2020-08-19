---
title: Azure Lab Services에 대 한 Cost management 가이드
description: Lab Services에 대 한 비용을 볼 수 있는 다양 한 방법을 이해 합니다.
author: rbest
ms.author: rbest
ms.date: 08/16/2020
ms.topic: article
ms.openlocfilehash: 98ce4d5e82d65d911984dc45615253ddcae33ae1
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88589876"
---
# <a name="cost-management-for-azure-lab-services"></a>Azure Lab Services에 대 한 Cost management

Cost management는 비용 예측 및 비용 분석의 두 가지 고유한 영역으로 나눌 수 있습니다.  랩을 설정할 때 랩의 초기 구조가 예상 된 예산에 맞는지 확인 하는 데 드는 비용 예측이 발생 합니다.  비용 분석은 일반적으로 해당 월의 끝에 비용을 분석 하 고 다음 달에 필요한 작업을 결정 하는 데 발생 합니다.

## <a name="estimate-the-lab-costs"></a>랩 비용 예측

각 랩 대시보드에는 해당 월에 발생 하는 랩의 비용에 대 한 대략적인 추정치를 제공 하는 **비용 & 청구** 섹션이 있습니다.  비용 예상치는 시간당 예상 비용을 기준으로 최대 사용자 수를 사용 하 여 시간 사용량을 요약 합니다.  [일정](how-to-create-schedules.md)을 포함 하 여 랩에 대 한 가장 정확한 예상 값을 얻으려면 대시보드는 예상 비용을 반영 합니다.  

이러한 예상 비용은 모두 포함 되지 않을 수 있습니다. 일부 리소스는 포함 되지 않습니다.  템플릿 준비 비용은 비용 예상치로 팩터링 되지 않습니다.  템플릿을 만드는 데 필요한 시간이 상당히 다를 수 있습니다. 템플릿의 실행 비용은 시간당 전체 랩 비용과 동일 합니다. 갤러리를 여러 실습 간에 공유할 수 있으므로 [공유 이미지 갤러리](how-to-use-shared-image-gallery.md) 비용은 랩 대시보드에 포함 되지 않습니다.  마지막으로, 랩 작성자가 컴퓨터를 시작할 때 발생 하는 시간이이 예상치에서 제외 됩니다.

> [!div class="mx-imgBorder"]
> ![대시보드 비용 예측](./media/cost-management-guide/dashboard-cost-estimation.png)

## <a name="analyze-previous-months-usage"></a>이전 달 사용량 분석

비용 분석은 이전 달 사용량을 검토 하 여 랩에 대 한 조정을 결정 하는 데 사용 됩니다.  과거의 비용 [분석은 구독 비용 분석](https://docs.microsoft.com/azure/cost-management-billing/costs/quick-acm-cost-analysis)에서 찾을 수 있습니다.  Azure Portal 위쪽 검색 필드에 "구독"을 입력 한 다음 구독 옵션을 선택할 수 있습니다.  

> [!div class="mx-imgBorder"]
> ![구독 검색](./media/cost-management-guide/subscription-search.png)

검토할 특정 구독을 선택 합니다.

> [!div class="mx-imgBorder"]
> ![구독 선택](./media/cost-management-guide/subscription-select.png)

 왼쪽 창에서 **Cost Management**의 "비용 분석"을 선택 합니다.

> [!div class="mx-imgBorder"]
> ![구독 비용 분석](./media/cost-management-guide/subscription-cost-analysis.png)

이 대시보드는 일정에 따라 다양 한 파일 형식으로 내보내는 기능을 포함 하 여 심층 비용 분석을 가능 하 게 합니다.  Cost Management에는 다양 한 기능이 포함 되어 있습니다. 자세한 내용은 [Cost Management 청구 개요](https://docs.microsoft.com/azure/cost-management-billing/cost-management-billing-overview) 를 참조 하세요.

리소스 유형별로 필터링: `microsoft.labservices/labaccounts` Lab Services와 관련 된 비용을 표시 합니다.

## <a name="understand-the-usage"></a>사용법 이해

다음은 비용 분석의 샘플입니다.

> [!div class="mx-imgBorder"]
> ![구독 비용 분석](./media/cost-management-guide/cost-analysis.png)

기본적으로 리소스, 리소스 종류, 위치, 리소스 그룹 이름, 태그, 비용의 6 개 열이 있습니다.  리소스 열에는 랩 계정, 랩 이름 및 VM에 대 한 정보가 포함 됩니다.  랩 계정/랩 이름/기본값을 포함 하는 행은 랩의 비용으로, 두 번째 및 세 번째 행에서 볼 수 있습니다.  사용 되는 Vm은 랩 계정/랩 이름/기본/v m 이름에 따라 비용이 청구 됩니다.  이 예에서는 두 번째 행을 사용 하 여 첫 번째 행을 추가 하 고 "aaalab/dockerlab"으로 시작 하는 경우 "aaalab" 랩 계정에서 랩 "dockerlab"의 총 비용을 제공 합니다.

공유 이미지 갤러리 정보를 가져오려면 리소스 종류를로 변경 합니다 `Microsoft.Compute/Galleries` . 그러면 이미지 갤러리에 대 한 전체 비용이 제공 됩니다.  갤러리의 저장 위치에 따라 공유 이미지 갤러리가 비용에 표시 되지 않을 수 있습니다.

> [!NOTE]
> 공유 이미지 갤러리가 랩 계정에 연결 되어 있습니다.  즉, 여러 랩에서 동일한 이미지를 사용할 수 있습니다.

## <a name="separating-costs"></a>비용 구분

일부 대학에서는 여러 클래스를 분리 하는 방법으로 랩 계정 및 리소스 그룹을 사용 했습니다.  각 클래스에는 고유한 랩 계정 및 리소스 그룹이 있습니다. 비용 분석 창에서 리소스 그룹 이름을 기반으로 하는 필터를 클래스에 대 한 적절 한 리소스 그룹 이름으로 추가 하면 해당 클래스에 대 한 비용도 표시 됩니다.  이렇게 하면 비용을 볼 때 서로 다른 클래스 사이에 더 명확 하 게 경계 수 있습니다.  비용 분석의 [예약 된 내보내기](https://docs.microsoft.com/azure/cost-management-billing/costs/tutorial-export-acm-data) 기능을 사용 하면 각 클래스의 비용을 별도의 파일로 다운로드할 수 있습니다.

## <a name="managing-costs"></a>비용 관리

클래스 유형에 따라 컴퓨터를 사용 하는 학생이 없어도 Vm이 실행 되는 것을 줄이기 위해 비용을 관리 하는 방법이 있습니다.

### <a name="maximize-cost-control-with-auto-shutdown-settings"></a>자동 종료 설정을 사용 하 여 비용 제어 최대화

자동 종료 비용 제어 기능을 사용 하면 랩 내에서 가상 컴퓨터 사용 시간이 낭비 되는 것을 방지할 수 있습니다. 다음 세 가지 자동 종료 및 연결 끊기 기능의 조합은 사용자가 실수로 가상 컴퓨터를 실행 하는 대부분의 경우를 포착 합니다.

> [!div class="mx-imgBorder"]
> ![구독 비용 분석](./media/cost-management-guide/auto-shutdown-disconnect.png)

이러한 설정은 랩 계정 수준과 랩 수준에서 구성할 수 있습니다. 설정이 랩 계정 수준에서 설정 된 경우 랩 계정 내의 모든 랩에 적용 됩니다. 이러한 설정은 모든 새 랩 계정에 대해 기본적으로 설정 되어 있습니다. 

#### <a name="details-about-auto-shutdown-settings"></a>자동 종료 설정에 대 한 세부 정보

* OS가 유휴 상태를 하다 고 판단 가상 머신에서 사용자의 연결을 자동으로 끊습니다 (Windows 전용).

    > [!NOTE]
    > 이 설정은 Windows 가상 컴퓨터에만 사용할 수 있습니다.

    설정이 켜져 있으면 Windows OS가 세션을 유휴 상태로 하다 고 판단 때 (템플릿 가상 컴퓨터 포함) 사용자가 랩의 모든 컴퓨터에서 연결이 끊어집니다. [유휴의 WINDOWS OS 정의](https://docs.microsoft.com/windows/win32/taskschd/task-idle-conditions#detecting-the-idle-state) 는 다음 두 가지 기준을 사용 합니다. 

    * 사용자 부재 – 키보드 또는 마우스 입력이 없습니다.
    * 리소스 사용 부족-모든 프로세서 및 모든 디스크가 특정 시간 (%) 동안 유휴 상태 였습니다.

    사용자는 연결을 끊기 전에 가상 컴퓨터 내부에 다음과 같은 메시지를 표시 합니다. 

    > [!div class="mx-imgBorder"]
    > ![구독 비용 분석](./media/cost-management-guide/idle-timer-expired.png)
    
    사용자의 연결이 끊어질 때 가상 컴퓨터가 계속 실행 되 고 있습니다. 사용자가 로그인 하 여 가상 컴퓨터에 다시 연결 하는 경우에는 연결 끊기 이전에 열려 있거나 저장 되지 않은 windows 또는 파일이 계속 남아 있습니다. 이 상태에서 가상 머신이 실행 되 고 있기 때문에 여전히 활성 및 발생 비용으로 계산 됩니다. 
    
    연결이 끊긴 Windows 가상 머신을 자동으로 종료 하려면 **가상 머신이 유휴 상태일 때 사용자 연결 끊기 사용자** 의 조합을 사용 하 고 **사용자가 설정의 연결을 끊을 때 가상 머신을 종료** 합니다.

    예를 들어 다음과 같이 설정을 구성 합니다.
    
    * 가상 컴퓨터가 유휴 상태일 때 사용자 연결 끊기 – 유휴 상태가 검색 된 후 15 분
    * 사용자가 연결을 끊은 후 5 분 후에 가상 컴퓨터를 종료 합니다.
    
    Windows 가상 머신은 사용자가 사용을 중지 한 후 자동으로 20 분 후에 종료 됩니다. 
    
    > [!div class="mx-imgBorder"]
    > ![구독 비용 분석](./media/cost-management-guide/vm-idle-diagram.png)
* 사용자가 연결을 끊을 때 자동으로 가상 컴퓨터 종료 (Windows & Linux)
    
    이 설정은 Windows 및 Linux 가상 컴퓨터를 모두 지원 합니다. 이 설정이 켜져 있으면 다음과 같은 경우 자동 종료가 수행 됩니다.
    
    * Windows의 경우 RDP (원격 데스크톱) 연결의 연결이 끊깁니다.
    * Linux의 경우 SSH 연결의 연결이 끊깁니다.
    
    > [!NOTE]
    > [특정 배포 및 Linux 버전만](https://docs.microsoft.com/azure/virtual-machines/extensions/diagnostics-linux#supported-linux-distributions) 지원 됩니다.
    
    가상 컴퓨터가 자동으로 종료 되기 전에 사용자가 다시 연결 될 때까지 대기 해야 하는 기간을 지정할 수 있습니다. 
* 시작 되었지만 사용자가 연결 하지 않는 가상 컴퓨터를 자동으로 종료 합니다.
     
    랩 내에서 사용자는 가상 컴퓨터를 시작할 수는 있지만 연결할 수는 없습니다. 예를 들면 다음과 같습니다.
    
    * 랩의 일정은 클래스 세션의 모든 가상 컴퓨터를 시작 하지만 일부 학생은 표시 되지 않고 컴퓨터에 연결 되지 않습니다.  
    * 사용자가 가상 컴퓨터를 시작 하지만 연결을 잊어버린 경우 
    
    "사용자가 연결 하지 않을 때 가상 컴퓨터 종료" 설정은 이러한 경우를 포착 하 고 가상 컴퓨터를 자동으로 종료 합니다.  
    
연결을 끊을 때 Vm의 자동 종료를 구성 하 고 사용 하도록 설정 하는 방법에 대 한 자세한 내용은 다음 문서를 참조 하세요.

* [랩 계정의 연결 해제 설정에서 VM의 자동 종료 구성](how-to-configure-lab-accounts.md)
* [연결 해제 시 VM 자동 종료 사용](how-to-enable-shutdown-disconnect.md)

### <a name="quota-vs-scheduled-time"></a>할당량 및 예약 된 시간

[할당량 시간](classroom-labs-concepts.md#quota) 및 [예약 된 시간](classroom-labs-concepts.md#schedules) 을 이해 하면 랩 소유자가 교수 및 학생의 요구에 더 잘 맞도록 랩을 구성할 수 있습니다.  예약 된 시간은 모든 학생 Vm이 시작 되 고에 연결할 수 있는 설정 된 시간입니다.  일반적으로 예약 됨은 모든 학생이 자신의 VM을 보유 하는 경우에 사용 되며, 교수의 지침을 사용 하는 경우에는 클래스 시간 처럼 해당 날짜에 설정 된 시간에 수행 됩니다.  단점은 학생이 VM에 로그인 하지 않더라도 모든 학생 Vm이 시작 되 고 비용이 발생는 것입니다.  할당량 시간은 각 학생에 게 할당 된 시간으로, 신중 하 게 사용할 수 있으며 독립적인 조사에 자주 사용 됩니다. 학생이 VM을 시작할 때까지 Vm이 시작 되지 않습니다.  

랩에서는 할당량 시간, 예약 된 시간 또는 둘의 조합을 사용할 수 있습니다. 클래스에 예약 된 시간이 필요 하지 않은 경우 Vm을 가장 효과적으로 사용 하는 데 할당량 시간만 사용 합니다.

### <a name="scheduled-event---stop-only"></a>예약 된 이벤트-중지만

일정에서 중지 전용 이벤트 유형을 추가할 수 있습니다. 그러면 특정 시간에 모든 컴퓨터가 중지 됩니다.  일부 랩 소유자는 학생이 사용 중인 VM을 종료 하지 않는 경우 비용 및 할당량 사용량을 줄이기 위해 매일 자정에 중지 전용 이벤트를 설정 했습니다.  이 유형의 이벤트에 대 한 단점은 학생 들이 VM을 사용 하는 경우에도 모든 Vm이 종료 된다는 것입니다.

### <a name="other-costs-related-to-labs"></a>랩 관련 기타 비용 

랩 서비스에는 없지만 랩 서비스에 연결 될 수 있는 비용은 있습니다.  공유 이미지 갤러리는 실습에 연결할 수 있지만 Lab services 비용에는 표시 되지 않으며 비용은 있습니다.  전체 비용을 줄일 수 있도록 이미지에 저장소를 상속 하는 비용이 있으므로 사용 하지 않는 모든 이미지를 갤러리에서 제거 해야 합니다.  랩을 제거할 때 랩에서 VNet (가상 네트워크)에서 다른 Azure 리소스에 연결할 수 있으며 VNet 및 기타 리소스를 제거 해야 합니다.

## <a name="conclusion"></a>결론

위의 정보를 통해 사용 비용 및 도구를 사용 하 여 초과 비용을 줄이는 방법에 대해 더 잘 이해 하 게 됩니다.
