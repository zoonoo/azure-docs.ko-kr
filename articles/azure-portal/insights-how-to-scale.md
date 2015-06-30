<properties 
	pageTitle="웹 사이트를 크기 조정하는 방법" 
	description="Azure에서 호스팅 계획을 확장하는 방법에 대해 알아봅니다." 
	authors="stepsic-microsoft-com" 
	manager="kamrani" 
	editor="" 
	services="application-insights" 
	documentationCenter=""/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="2014-11-04" 
	ms.author="stepsic"/>

# 웹 사이트를 크기 조정하는 방법

Azure 포털 미리 보기에서 웹 호스팅 계획의 인스턴스 수를 수동으로 설정하거나 자동으로 크기 조정되도록 매개 변수를 설정할 수 있습니다. 웹 호스팅 계획의 크기 조정을 구성하기 전에 먼저 인스턴스 크기에 의해 크기 조정이 영향을 받는다는 점을 고려해야 합니다. 크기가 클수록 코어 및 메모리가 많아지므로, 인스턴스 수가 동일할 때 성능이 더 높습니다.

크기 조정은 전체 웹 호스팅 계획에 영향을 미칩니다. 웹 사이트를 만들 때 새 웹 호스팅 계획을 만들거나 기존 웹 호스팅 계획을 사용하는 옵션이 있습니다. 웹 호스팅 계획이 있으면 모든 사이트에서 동일한 인스턴스가 공유되므로 모든 사이트의 크기가 함께 조정됩니다.

## 웹 호스팅 계획 크기 조정

1. [Azure 포털 미리 보기](https://portal.azure.com/)에서 **찾아보기**를 클릭하고 **웹 사이트**를 클릭한 후 웹 사이트 이름을 클릭하여 블레이드를 엽니다.
2. 웹 사이트 블레이드에 있는 **작업** 렌즈의 **크기 조정** 파트에서 웹 호스팅 계획의 상태를 확인할 수 있습니다. 수동으로 크기 조정하는 경우 **해제**이고, 한 개 이상의 성능 메트릭에 의해 크기 조정하는 경우 **성능**이고, 여러 개의 크기 자동 조정 프로필을 사용하도록 설정한 경우 **일정**입니다.  
    ![Scale part](./media/insights-how-to-scale/Insights_ScalePartOff.png)
3. 이 파트를 클릭하면 **크기 조정** 블레이드로 이동합니다. 크기 조정 블레이드 맨 위에서 웹 호스팅 계획의 크기 자동 조정 작업 기록을 볼 수 있습니다.  

    ![Scale blade](./media/insights-how-to-scale/Insights_ScaleBladeDayZero.png)
4. 웹 호스팅 계획을 실행하는 가상 컴퓨터의 수를 **인스턴스** 슬라이더로 수동으로 조정할 수 있습니다.
5. 로드를 기준으로 인스턴스 수가 자동으로 조정되도록 하려면 **자동 크기 조정 모드** 아래에서 **성능**을 선택합니다. 이때 Azure 포털 미리 보기에서 **일정**을 선택할 수 없습니다.  
    ![Scale blade with CPU Percentage](./media/insights-how-to-scale/Insights_ScaleBladeCPU.png) 
6. 성능을 선택하는 경우 2가지 변경 내용이 있습니다.
    - **인스턴스 범위**에서 최대 및 최소 인스턴스 수를 선택할 수 있습니다. 크기 자동 조정은 로드와 관련 없이 항상 이 범위 내에 유지합니다.
    - **대상 메트릭** 섹션에서 성능 메트릭을 정의할 수 있습니다.
7. **CPU 비율** 섹션에서는 웹 호스팅 계획의 전체 인스턴스의 평균 CPU 목표를 설정할 수 있습니다. 평균 CPU가 정의된 최대값을 초과할 때는 강화가 발생합니다.

크기 자동 조정이 설정된 경우 웹 사이트 블레이드의 해당 파트에서 **성능**이 표시되며 차트에 크기 조정 기록이 표시됩니다.

![Scale blade with CPU Percentage](./media/insights-how-to-scale/Insights_ScalePartBladeOn.png) 

Azure 포털 Preview에서 공유 웹 호스팅 계획의 인스턴스 수를 변경할 수 없습니다.

## 고급 크기 조정

Azure 포털 Preview의 새로운 기능으로, CPU 비율 대신 메트릭을 기준으로 크기를 조정할 수 있으며 강화 및 규모 축소의 복합적인 규칙 집합을 사용할 수도 있습니다.

### 다른 성능 메트릭 기반 크기 조정
CPU 외에도 다음을 기반으로 크기 조정할 수 있습니다.

- 평균 메모리 - 인스턴스에 사용되는 평균 메모리 비율이 지정 임계값을 초과하거나 임계값 아래로 떨어지는 경우 인스턴스가 추가되거나 제거됩니다.
- HTTP 큐 크기 또는 디스크 큐 크기 - HTTP 요청 또는 디스크의 큐에 있는 메시지 수가 지정 임계값을 초과하거나 임계값 아래로 떨어지는 경우 인스턴스가 추가되거나 제거됩니다.

다른 메트릭에 따라 크기 조정하는 2가지 방법이 있습니다. 단일 메트릭에 의해서만 크기 조정하려는 경우 **CPU 비율** 슬라이더 옆에 있는 펼침 단추를 선택합니다. 메트릭 세부 정보 블레이드가 열립니다.

![Entry point to scale metrics](./media/insights-how-to-scale/Insights_ScaleMetricChevron.png)

한 번에 여러 개의 메트릭에 의해 크기를 조정하려면 명령 모음에서 **메트릭 추가**를 클릭합니다.

![Add metrics](./media/insights-how-to-scale/Insights_AddMetric.png)

메트릭 세부 정보 블레이드에는 최적의 크기 조정 프로필을 설정하는 데 필요한 모든 컨트롤이 있습니다. 맨 위에서 크기 조정 기준으로 사용할 새로운 메트릭을 선택합니다.

### 여러 단계로 크기 조정

메트릭 그래프 아래에 2개의 섹션, 즉 **Scale up rules** 및 **Scale down rules**가 있습니다. **임의의** 강화 규칙이 충족되는 경우 서비스가 강화됩니다. 반대로, **모든** 규모 축소 규칙이 충족되는 경우에는 서비스 규모가 축소됩니다.

선택하는 각 규칙의 옵션은 다음과 같습니다.

- 조건 - 보다 큼 또는 보다 작음
- Threshold - 동작을 트리거하기 위해 이 메트릭이 통과해야 하는 수치
- Over Past - 이 메트릭의 평균을 계산하는 범위 시간(분)
- Scale up or down by - 크기 조정 작업의 크기
- Cool down - 이전 크기 조정 작업 이후에 이 규칙이 다시 크기 조정하도록 대기해야 하는 시간

![Multiple scale rules](./media/insights-how-to-scale/Insights_MultipleScaleRules.png)

여러 개의 크기 조정 규칙을 사용하면 성능이 변화함에 따라 보다 적극적으로 강화(또는 규모 축소)할 수 있습니다. 예를 들어 2가지 크기 조정 규칙을 정의할 수 있습니다.

1. CPU 비율이 60%를 넘는 경우 1개 인스턴스 강화
2. CPU 비율이 85%를 넘는 경우 3개 인스턴스 강화

이 추가 규칙을 사용하면 크기 조정 작업 이전에 로드가 85%를 넘는 경우 1개가 아니라 2개의 추가 인스턴스를 얻게 됩니다. 

<!--HONumber=46--> 
 