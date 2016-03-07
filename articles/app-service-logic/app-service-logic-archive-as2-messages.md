<properties 
   pageTitle="AS2 커넥터 메시지 보관 | Microsoft Azure 앱 서비스" 
   description="Azure 앱 서비스에서 AS2 커넥터 메시지를 보관 또는 저장하는 방법" 
   services="app-service\logic" 
   documentationCenter=".net,nodejs,java" 
   authors="rajram" 
   manager="dwrede" 
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration" 
   ms.date="02/18/2016"
   ms.author="rajram"/>


# AS2 커넥터 메시지 보관 개요

>[AZURE.NOTE] 이 문서 버전은 논리 앱 2014-12-01-미리 보기 스키마 버전에 적용됩니다.

[AS2 커넥터](app-service-logic-connector-as2.md)는 메시지 보관 기능을 노출합니다. 보관은 패키지 설정의 일부인 **Azure Blob 컨테이너**에 메시지를 저장하는 것입니다.

보관은 메시지와 승인(MDN) 둘 다에 대해 두 지점에서 노출됩니다.

1. **수신/디코드 트리거**: API 앱 인스턴스에서 메시지를 수신하는 즉시 메시지가 보관됩니다. 
2. **인코드/보내기 동작**: 모든 처리가 완료된 후 파트너에게 전송되기 직전에 인코드된 메시지가 보관됩니다. 

## 방법: 메시지의 보관된 URL 검색

AS2 커넥터 API 앱 인스턴스를 찾아 ‘추적'을 클릭합니다. 필터 매개 변수를 사용하여 추적 정보를 좁힙니다. 메시지가 보기에 표시되면 해당 자세한 보기를 클릭하여 표시합니다. 메시지의 보관된 URL이 이 자세한 보기에 표시됩니다. ![][1]

## 방법: 보관된 메시지 검색

위의 검색된 URL을 사용하여 Azure Blob 저장소에서 보관된 메시지를 검색합니다.


<!--Image references-->
[1]: ./media/app-service-logic-archive-as2-messages/Tracking.jpg
 

<!---HONumber=AcomDC_0224_2016-->