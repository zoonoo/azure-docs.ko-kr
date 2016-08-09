<properties 
	pageTitle="맵 엔터프라이즈 통합 팩 개요 | Microsoft Azure 앱 서비스 | Microsoft Azure" 
	description="엔터프라이즈 통합 팩 및 논리 앱에서 맵을 사용하는 방법 알아보기" 
	services="app-service\logic" 
	documentationCenter=".net,nodejs,java"
	authors="msftman" 
	manager="erikre" 
	editor="cgronlun"/>

<tags 
	ms.service="logic-apps" 
	ms.workload="integration" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/08/2016" 
	ms.author="deonhe"/>

# 맵 및 엔터프라이즈 통합 팩에 대해 알아보기

## 개요
엔터프라이즈 통합에서는 맵을 사용하여 XML 데이터의 형식을 변환합니다.

## 맵이란?
맵은 문서에서 어떤 데이터를 다른 형식으로 변환해야 하는지를 정의하는 XML 문서입니다.

## 맵을 사용하는 이유
날짜에 YYYMMDD 형식을 사용하는 고객에게서 정기적으로 B2B 주문 또는 송장을 받는 경우를 가정해 보겠습니다. 그러나 조직에서 날짜를 MMDDYYY 형식으로 저장합니다. 고객 작업 데이터베이스에서 주문 또는 송장 세부 정보를 저장하기 전에 맵을 사용하여 YYYMMDD 날짜 형식을 MMDDYYY로 *변환*할 수 있습니다.

## 맵 업로드 방법
Azure 포털에서 다음을 수행합니다.
1. **찾아보기**를 선택합니다. ![](./media/app-service-logic-enterprise-integration-overview/overview-1.png)
2. 필터 검색 상자에 **통합**을 입력하고 결과 목록에서 **통합 계정**을 선택합니다. ![](./media/app-service-logic-enterprise-integration-overview/overview-2.png)
3. 맵을 추가할 **통합 계정**을 선택합니다. ![](./media/app-service-logic-enterprise-integration-overview/overview-3.png)
4.  **맵** 타일을 선택합니다. ![](./media/app-service-logic-enterprise-integration-maps/map-1.png)
5. 열린 맵 블레이드에서 **추가** 단추를 선택합니다. ![](./media/app-service-logic-enterprise-integration-maps/map-2.png)
6. 맵의 **이름**을 입력한 다음 맵 파일을 업로드하고 **맵** 텍스트 상자의 오른쪽에 있는 폴더 아이콘을 선택합니다. 업로드 프로세스가 완료되면 **확인** 단추를 선택합니다. ![](./media/app-service-logic-enterprise-integration-maps/map-3.png)
7. 맵은 통합 계정에 추가되고 있습니다. 맵 파일을 추가하는 데 성공했는지 여부를 나타내는 알림이 화면에 표시됩니다. 알림을 받고 **맵** 타일을 선택하면 맵 블레이드에서 새로 추가된 맵이 표시됩니다. ![](./media/app-service-logic-enterprise-integration-maps/map-4.png)

## 맵 편집 방법
맵을 편집하려면 원하는 변경 내용을 포함한 새 맵 파일을 업로드해야 합니다. 먼저 맵을 다운로드하고 편집할 수 있습니다.

다음 단계를 수행하여 기존 맵을 대체하는 새 맵을 업로드합니다.
1. **맵** 타일을 선택합니다.
2. 맵 블레이드가 열리면 편집할 맵을 선택합니다.
3. **맵** 블레이드에서 **업데이트** 링크를 선택합니다. ![](./media/app-service-logic-enterprise-integration-maps/edit-1.png)
4. 열린 파일 선택 대화 상자를 사용하여 업로드하려는 맵 파일을 선택한 다음 파일 선택에서 **열기**를 선택합니다. ![](./media/app-service-logic-enterprise-integration-maps/edit-2.png)
5. 맵을 업로드한 후에 알림 팝업을 받게 됩니다.

## 맵 삭제 방법
1. **맵** 타일을 선택합니다.
2. 맵 블레이드가 열리면 삭제할 맵을 선택합니다.
3. **삭제** 링크를 선택합니다. ![](./media/app-service-logic-enterprise-integration-maps/delete.png)
4. 정말로 맵을 삭제하려는지를 확인합니다. ![](./media/app-service-logic-enterprise-integration-maps/delete-confirmation-1.png)

## 다음 단계
- [엔터프라이즈 통합 팩에 대해 자세히 알아보기](./app-service-logic-enterprise-integration-overview.md "엔터프라이즈 통합 팩에 대해 알아보기")
- [규약에 대해 자세히 알아보기](./app-service-logic-enterprise-integration-agreements.md "엔터프라이즈 통합 규약에 대해 알아보기")
- [변환에 대해 자세히 알아보기](./app-service-logic-enterprise-integration-transform.md "엔터프라이즈 통합 변환에 대해 알아보기")

<!---HONumber=AcomDC_0727_2016-->