<properties
	pageTitle="기계 학습 웹 서비스용 Excel 추가 기능 | Microsoft Azure"
	description="코드를 작성하지 않고 Excel에서 직접 Azure 기계 학습 웹 서비스에 액세스하는 방법입니다."
	services="machine-learning"
	documentationCenter=""
	authors="tedway"
	manager="paulettm"
	editor="cgronlun"
    tags=""/>

<tags
	ms.service="machine-learning"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
	ms.workload="data-services"
	ms.date="08/10/2015"
	ms.author="tedway;garye" />

# Azure 기계 학습 웹 서비스용 Excel 추가 기능

Excel을 사용하면 코드를 작성할 필요 없이 쉽게 직접 웹 서비스를 호출할 수 있습니다.

## 기존 웹 서비스를 사용하는 단계

1. 여기서 샘플 Excel 파일을 열거나 웹 서비스 **DASHBOARD** 탭에서 엽니다.
2. 웹 서비스를 클릭하여 선택합니다(이 예제의 경우 "Titanic Survivor Predictor (Excel Add-in Sample) [Score]").

    ![웹 서비스 선택][01]

3. 이렇게 하면 **Predict** 섹션으로 이동합니다. Excel에서 셀 하나를 선택하고 **Use sample data**를 클릭합니다. 필요에 따라 Excel에서 데이터를 편집합니다.

	![Predict 섹션][02]

4. 데이터를 강조 표시하고 입력 데이터 범위 아이콘을 클릭합니다.
5. **Output** 아래에 출력을 배치할 셀 번호를 입력합니다.
6. **Predict**를 클릭합니다.

## 새 웹 서비스를 추가하는 단계

1. 웹 서비스를 게시([이 페이지](machine-learning-walkthrough-5-publish-web-service.md)에 게시 방법이 설명되어 있음)하거나 기존 웹 서비스를 찾습니다.
2. 웹 서비스가 생겼으면 기계 학습 스튜디오의 왼쪽 창에서 **WEB SERVICES** 섹션을 클릭한 다음 사용할 웹 서비스를 선택합니다.
3. 웹 서비스의 **DASHBOARD** 탭에서 웹 서비스의 API 키를 복사합니다.
4. Excel에서 **Web Services** 섹션으로 이동합니다(**Predict** 섹션에 있는 경우 뒤로 화살표를 클릭하여 웹 서비스 목록으로 이동).

	![Web Services 섹션][03]

5. **Add Web Service**를 클릭합니다.
6. 키를 **API key** 텍스트 상자에 붙여넣습니다.
7. 웹 서비스의 **DASHBOARD** 탭에서 **API help page** 링크를 클릭합니다.
8. **Request** 아래에서 요청 URI를 복사하여 **URL** 텍스트 상자에 붙여넣습니다.
9. **추가**를 클릭합니다.

	![URL 및 API 키][04]

10.	웹 서비스를 사용하려면 위의 지침 "기존 웹 서비스를 사용하는 단계"를 따릅니다.

## 통합 문서 공유

통합 문서를 저장하면 추가한 웹 서비스의 API 키도 저장됩니다. 즉, 신뢰할 수 있는 사용자와만 통합 문서를 공유해야 합니다.


[01]: ./media/machine-learning-excel-add-in-for-web-services/image1.png
[02]: ./media/machine-learning-excel-add-in-for-web-services/image2.png
[03]: ./media/machine-learning-excel-add-in-for-web-services/image3.png
[04]: ./media/machine-learning-excel-add-in-for-web-services/image4.png

<!---HONumber=August15_HO8-->