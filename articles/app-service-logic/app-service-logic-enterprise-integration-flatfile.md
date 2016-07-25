<properties 
	pageTitle="엔터프라이즈 통합 팩 및 논리 앱을 사용하여 플랫 파일을 인코딩 또는 디코딩하는 방법 배우기 | Microsoft Azure 앱 서비스 | Microsoft Azure" 
	description="엔터프라이즈 통합 팩 및 논리 앱의 기능을 사용하여 플랫 파일 인코딩 또는 디코딩" 
	services="app-service\logic" 
	documentationCenter=".net,nodejs,java"
	authors="msftman" 
	manager="erikre" 
	editor="cgronlun"/>

<tags 
	ms.service="app-service-logic" 
	ms.workload="integration" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/08/2016" 
	ms.author="deonhe"/>

# 플랫 파일과 엔터프라이즈 통합

## 개요

XML 콘텐츠를 인코딩하는 논리 앱 내에서 플랫 파일 인코딩 커넥터를 사용합니다. B2B 시나리오에서 비즈니스 파트너에게 보내기 전에 XML 콘텐츠를 인코딩하려 할 수 있습니다. 만든 논리 앱은 HTTP 요청 트리거를 포함하여 다른 응용 프로그램 또는 다양한 [커넥터](../connectors/apis-list.md)에서도 다양한 원본의 XML 콘텐츠를 가져올 수 있습니다. 논리 앱의 기능에 대한 자세한 내용은 [논리 앱 설명서](./app-service-logic-what-are-logic-apps.md "논리 앱에 대해 자세히 알아보기")를 확인합니다.

## 플랫 파일 인코딩 커넥터를 만드는 방법

다음 단계를 수행하여 논리 앱을 만들고 플랫 파일 인코딩 커넥터를 논리 앱을 추가합니다.

1. 논리 앱을 만들고 XML 데이터를 인코딩하는 데 사용할 스키마를 포함하는 [통합 계정에 연결](./app-service-logic-enterprise-integration-accounts.md "논리 앱에 통합 계정을 연결하는 방법 알아보기")합니다.
2. **요청 - HTTP 요청을 받은 경우** 트리거를 논리 앱에 추가합니다. ![](./media/app-service-logic-enterprise-integration-flatfile/flatfile-1.png)
3. 다음을 수행하여 플랫 파일 인코딩 작업을 추가합니다.
-  **더하기** 기호를 선택합니다.
-  더하기 기호를 선택한 후에 표시되는 **작업 추가** 링크를 선택합니다.
-  사용하려는 작업에 대해 모든 작업을 필터링하기 위해 검색 상자에 *플랫*을 입력합니다.
-  목록에서 **플랫 파일 인코딩** 작업을 선택합니다. ![](./media/app-service-logic-enterprise-integration-flatfile/flatfile-2.png)
6. 팝업된 플랫 파일 인코딩 제어에서 **콘텐츠** 텍스트 상자를 선택합니다. ![](./media/app-service-logic-enterprise-integration-flatfile/flatfile-3.png)
7. 본문 태그를 인코딩하려는 콘텐츠로 선택합니다. 본문 태그는 콘텐츠 필드를 채웁니다. ![](./media/app-service-logic-enterprise-integration-flatfile/flatfile-4.png)
8. **스키마 이름** 목록 상자를 선택하고 위에 있는 입력 *콘텐츠*를 인코딩하는 데 사용할 스키마를 선택합니다. ![](./media/app-service-logic-enterprise-integration-flatfile/flatfile-5.png)
9. 작업을 저장합니다. ![](./media/app-service-logic-enterprise-integration-flatfile/flatfile-6.png)

이 시점에서 플랫 파일 인코딩 커넥터의 설정이 완료됩니다. 실제 응용 프로그램에서는 SalesForce와 같은 LOB 응용 프로그램에 인코딩한 데이터를 저장하거나 인코딩된 데이터를 거래 파트너에게 전송할 수 있습니다. 제공된 다른 커넥터 중 하나를 사용하여 Salesforce 또는 거래 파트너에게 인코딩 작업의 출력을 보내는 동작을 쉽게 추가할 수 있습니다.

이제 HTTP 끝점에 요청하고 요청 본문에 XML 콘텐츠를 포함하여 커넥터를 테스트할 수 있습니다.

## 플랫 파일 디코딩 커넥터를 만드는 방법

### 필수 요소
다음 단계를 완료하려면 통합 계정에 이미 업로드된 스키마 파일이 있어야 합니다.

1. **요청 - HTTP 요청을 받은 경우** 트리거를 논리 앱에 추가합니다. ![](./media/app-service-logic-enterprise-integration-flatfile/flatfile-1.png)
2. 다음을 수행하여 플랫 파일 인코딩 작업을 추가합니다.
-  **더하기** 기호를 선택합니다.
-  더하기 기호를 선택한 후에 표시되는 **작업 추가** 링크를 선택합니다.
-  사용하려는 작업에 대해 모든 작업을 필터링하기 위해 검색 상자에 *플랫*을 입력합니다.
-  목록에서 **플랫 파일 디코딩** 작업을 선택합니다. ![](./media/app-service-logic-enterprise-integration-flatfile/flatfile-2.png)
- **콘텐츠** 제어를 선택합니다. 그러면 이전 단계에서 콘텐츠로 디코딩하는 데 사용할 수 있는 콘텐츠의 목록이 생성됩니다. 들어오는 HTTP 요청의 *본문*을 콘텐츠로 디코딩하는 데 사용할 수 있습니다. **콘텐츠** 제어에 직접 디코딩할 콘텐츠를 입력할 수도 있습니다. 이 예제에서는 *본문*을 선택하여 디코딩 단계 중 1단계에서 들어오는 HTTP 요청의 본문을 사용합니다.
- *본문* 태그를 선택합니다. 이제 본문 태그는 콘텐츠 제어에 위치합니다.
- 콘텐츠를 디코딩하는 데 사용할 스키마의 이름을 선택합니다. 이 예제에서는 *OrderFile*을 선택합니다. OrderFile은 이전 특정 시점에 내 통합 계정에 업로드한 스키마의 이름입니다. ![](./media/app-service-logic-enterprise-integration-flatfile/flatfile-decode-1.png)
- 메뉴에서 **저장** 링크를 선택하여 작업을 저장합니다. ![](./media/app-service-logic-enterprise-integration-flatfile/flatfile-6.png)

이 시점에서 플랫 파일 디코딩 커넥터의 설정이 완료됩니다. 실제 응용 프로그램에서는 SalesForce와 같은 LOB 응용 프로그램에 디코딩한 데이터를 저장할 수 있습니다. Salesforce에 디코딩 작업의 출력을 보내는 작업을 쉽게 추가할 수 있습니다.

이제 HTTP 끝점에 요청하고 요청 본문에 디코딩하려는 XML 콘텐츠를 포함하여 커넥터를 테스트할 수 있습니다.

## 자세한 정보
- [엔터프라이즈 통합 팩에 대해 자세히 알아보기](./app-service-logic-enterprise-integration-overview.md "엔터프라이즈 통합 팩에 대해 알아보기")

<!---HONumber=AcomDC_0713_2016-->