<properties 
	pageTitle="끝점 만들기" 
	description="Azure 기계 학습에서 웹 서비스 끝점 만들기" 
	services="machine-learning" 
	authors="hiteshmadan" 
	manager="padou" 
	editor="cgronlun"/>

<tags
	ms.service="machine-learning"
	ms.devlang="multiple"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="tbd" 
	ms.date="02/19/2015"
	ms.author="himad"/>


# 끝점 만들기

Azure 기계 학습을 사용하면 게시된 웹 서비스에 대한 여러 끝점을 만들 수 있습니다. 각 끝점은 해당 웹 서비스의 다른 끝점과 독립적으로 개별적으로 처리, 제한 및 관리됩니다. 각 끝점에 대한 고유한 URL 및 권한 부여 키가 있습니다.

이를 통해 Azure 기계 학습 사용자는 향후 해당 고객에게 판매할 수 있는 웹 서비스를 만들 수 있습니다. 각 끝점을 이 웹 서비스를 만든 실험에 연결되어 있는 동안 고유한 학습된 모델로 개별적으로 사용자 지정할 수 있습니다. 또한 사용자 지정을 덮어쓰지 않고 실험의 모든 업데이트를 끝점에 선택적으로 적용할 수 있습니다.

## 끝점 만들기 단계
- manage.windowsazure.com을 열고 왼쪽 열에서 기계 학습을 클릭합니다. 관심 있는 웹 서비스가 있는 작업 영역을 클릭합니다.
![Navigate to workspace](./media/machine-learning-create-endpoint/figure-1.png)


- "웹 서비스" 탭을 클릭합니다.
![Navigate to web services](./media/machine-learning-create-endpoint/figure-2.png)


- 사용 가능한 끝점 목록을 확인할 웹 서비스를 클릭합니다.
![Navigate to endpoint](./media/machine-learning-create-endpoint/figure-3.png)


- 맨 아래에 있는 끝점 추가 단추를 클릭합니다. 이름 및 설명을 입력하고 이 웹 서비스에 이름이 같은 다른 끝점이 없는지 확인합니다. 특별한 요구 사항이 없는 경우 제한 수준을 해당 기본값으로 그대로 둡니다.
제한에 대해 자세히 알아보려면 [API 끝점 확장](machine-learning-scaling-endpoints.md) 페이지를 참조하세요.
![Create endpoint](./media/machine-learning-create-endpoint/figure-4.png)


끝점이 만들어지면 동기 API, 일괄 처리 API 및 Excel 워크시트를 통해 끝점을 사용할 수 있습니다.

<!--HONumber=49-->