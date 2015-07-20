<properties 
	pageTitle="Excel에서 기계 학습 웹 서비스 사용 | Microsoft Azure" 
	description="Excel에서 Azure 기계 학습 웹 서비스 사용" 
	services="machine-learning" 
	documentationCenter="" 
	authors="LuisCabrer" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/18/2015" 
	ms.author="tedway"/>


# Excel에서 Azure 기계 학습 웹 서비스 사용 #

 Azure 기계 학습 스튜디오를 사용하면 코드를 작성할 필요 없이 Excel에서 직접 웹 서비스를 쉽게 호출할 수 있습니다.

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

## 단계

1. 웹 서비스를 게시합니다. [이 페이지](machine-learning-walkthrough-5-publish-web-service.md)에서는 게시 방법을 설명합니다. 현재 Excel 통합 문서 기능은 단일 출력(즉, 단일 점수 매기기 레이블)이 있는 요청/응답 서비스에 대해서만 지원됩니다. 
 
2. 웹 서비스를 만든 후 스튜디오의 왼쪽에서 **웹 서비스** 섹션을 클릭한 다음 Excel에서 사용할 웹 서비스를 선택합니다.

3. 웹 서비스의 **대시보드** 탭은 **요청/응답** 서비스의 행입니다. 이 서비스에 단일 출력이 있는 경우 해당 행에 **Excel 통합 문서 다운로드** 링크가 표시됩니다.

	![][1]
 
4. **Excel 통합 문서 다운로드**를 클릭하고 Excel에서 엽니다.

5. 보안 경고가 표시되면 **편집 사용** 단추를 클릭합니다.

	![][2]

6. 보안 경고가 표시됩니다. **콘텐츠 사용** 단추를 클릭하여 스프레드시트에서 매크로를 실행합니다.

	![][3]
 
7. 매크로가 활성화되면 테이블이 생성됩니다. 파란색 열은 RRS 웹 서비스의 입력 또는 **매개 변수**로 필요합니다. RRS 서비스의 출력인 **예측 값**은 녹색입니다. 주어진 행의 모든 열이 채워지면 통합 문서에서 점수 매기기 API를 자동으로 호출하고 점수가 매겨진 결과를 표시합니다.

	![][4]

7. 둘 이상의 행에 대한 점수를 매기려면 두 번째 행을 데이터로 채웁니다. 그러면 예측 값이 생성됩니다. 여러 행을 한 번에 붙여 넣을 수도 있습니다.

8. 이제 예측 값에서 Excel 기능(그래프, 파워 맵, 조건부 서식 등)을 사용합니다.


## 통합 문서 공유

매크로를 작동하려면 액세스 키가 스프레드시트의 일부여야 합니다. 즉, 신뢰할 수 있는 엔터티/개인과만 통합 문서를 공유해야 합니다.

## 자동 업데이트

RRS 호출은 다음 두 가지 상황에서 수행됩니다.

1. 행의 모든 **매개 변수**에 콘텐츠가 처음으로 채워진 경우
    
2. 모든 **매개 변수**가 있는 행에서 **매개 변수** 중 하나 이상이 변경될 때마다

[1]: ./media/machine-learning-consuming-from-excel/excellink.png
[2]: ./media/machine-learning-consuming-from-excel/enableeditting.png
[3]: ./media/machine-learning-consuming-from-excel/enablecontent.png
[4]: ./media/machine-learning-consuming-from-excel/sampletable.png
 

<!---HONumber=July15_HO2-->