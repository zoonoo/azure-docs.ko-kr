<properties 
	pageTitle="API 끝점 크기 조정 | Microsoft Azure" 
	description="Azure 기계 학습에서 웹 서비스 끝점 크기 조정" 
	services="machine-learning"
	documentationCenter="" 
	authors="hiteshmadan" 
	manager="padou" 
	editor=""/>

<tags
	ms.service="machine-learning"
	ms.devlang="multiple"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="tbd" 
	ms.date="06/29/2015"
	ms.author="himad"/>


# API 끝점 크기 조정

Azure 기계 학습의 웹 서비스 끝점에는 끝점이 사용되는 속도와 일치하도록 선택할 수 있는 제한 수준이 있습니다.

끝점에서 수행되는 제한 크기를 제어하는 두 단계(제한 수준: 낮음 또는 높음)가 있습니다. 유료 고객만 제한 수준을 높음으로 설정할 수 있습니다. 최대 동시 호출 수: 낮음 제한 수준의 경우 4, 높음 제한 수준의 경우 20-200


동기 API는 일반적으로 낮은 대기 시간을 원하는 경우에 사용됩니다. 여기서 대기 시간은 API가 하나의 요청을 완료하는 데 걸리는 시간을 의미하며 네트워크 지연을 고려하지 않습니다. 대기 시간이 50ms인 API가 있다고 가정합니다. 높음 제한 수준 및 최대 동시 호출 = 20으로 사용 가능한 용량을 완전히 사용하려면 이 API를 초당 20 * 1000 / 50 = 400회 호출해야 합니다. 더욱 확장하여 최대 동시 호출 수를 200으로 설정하면 대기 시간이 50ms일 경우 API를 초당 4000회 호출할 수 있습니다.

최대 동시 호출 수 200에서 지원하는 것보다 높은 부하로 API를 호출하려는 경우 동일한 웹 서비스에서 여러 끝점을 만들고 모든 끝점에 부하를 무작위로 분산해야 합니다.

해당하는 높은 속도로 API를 적중하지 않을 경우 매우 높은 동시성 개수를 사용하는 것은 바람직하지 않습니다. 높은 부하로 구성된 API에 상대적으로 낮은 부하를 배치할 경우 가끔 시간 초과 및/또는 대기 시간 급증이 나타날 수 있습니다.

제한 설정 조정은 동기 API(RRS)의 동작에만 영향을 줍니다. 동기 API에서 503 서비스를 사용할 수 없음 응답이 자주 표시되는 경우 이러한 설정을 조정해야 합니다.

관리 UI를 통해 제한 수준을 설정/해제할 수 있습니다. 높음 제한 수준에 맞게 사용자 지정 동시성 개수를 지정하려면 패치 끝점 API를 사용합니다.

- manage.windowsazure.com 열기
- 기계 학습 탭으로 이동합니다.
- 작업 영역을 클릭합니다.
- 끝점이 있는 웹 서비스로 이동합니다. ![웹 서비스로 이동합니다.](./media/machine-learning-scaling-endpoints/figure-1.png)

- 끝점을 클릭한 다음 구성 탭을 클릭합니다. ![끝점 구성으로 이동합니다.](./media/machine-learning-scaling-endpoints/figure-2.png)


- 제한 수준을 높음으로 변경하고 저장을 클릭합니다.


 

<!---HONumber=Oct15_HO3-->