---
title: "사용 가능한 커넥터 및 API Apps 목록 | Microsoft Docs"
description: "Azure 앱 서비스의 커넥터 및 API 앱 알아보기"
services: logic-apps
documentationcenter: 
author: MandiOhlinger
manager: erikre
editor: cgronlun
ms.assetid: 984a425d-ba64-48cc-90dc-bb624411e0f0
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/01/2016
ms.author: mandia
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 6028fc53661978f63bbac89be6214ffebdc22405


---
# <a name="list-of-connectors-and-api-apps-to-use-in-your-logic-apps"></a>논리 앱에서 사용할 커넥터 및 API 앱 목록
> [!NOTE]
> 이 문서 버전은 논리 앱 2014-12-01-미리 보기 스키마 버전에 적용됩니다. Logic Apps GA(일반 공급) 버전의 경우 [새 커넥터 목록](../connectors/apis-list.md)을 참조합니다.
> 
> 

논리 앱으로 사용하려는 Microsoft에서 만든 사용 가능한 모든 커넥터 및 API 앱에 대해 알아봅니다.

가격 정보 및 각 서비스 계층에 포함된 항목의 목록은 [Azure 앱 서비스 가격](https://azure.microsoft.com/pricing/details/app-service/)을 참조하세요.

> [!NOTE]
> Azure 계정에 등록하기 전에 Logic Apps를 시작하려는 경우 [Logic Apps 평가](https://tryappservice.azure.com/?appservice=logic)로 이동하세요. 앱 서비스에서 단기 시작 논리 앱을 즉시 만들 수 있습니다. 신용 카드는 필요하지 않으며 약정도 필요하지 않습니다.
> 
> 

## <a name="core-connectors"></a>코어 커넥터
다음 표에는 코어 커넥터로 사용할 수 있는 Microsoft에서 만든 사용 가능한 모든 커넥터 및 API 앱이 나열되어 있습니다.

| 이름 | 설명 |
| --- | --- |
| [Bing 번역기](https://azure.microsoft.com/marketplace/partners/bing/microsofttranslator/) |Bing을 사용하여 텍스트를 다른 언어로 번역합니다. |
| [HTTP](app-service-logic-connector-http.md) |HTTP 수신기는 HTTP 서버로서 작업하는 끝점을 열고, 들어오는 HTTP 또는 HTTPS 요청을 수신합니다. HTTP 작업은 API 앱이 필요하지 않고 논리 앱 내에서 고유하게 지원됩니다. |
| [Slack](app-service-logic-connector-slack.md) |Slack에 연결하고 Slack 채널에 메시지를 게시합니다. |

## <a name="enterprise-integration-connectors"></a>엔터프라이즈 통합 커넥터
다음 표에는 엔터프라이즈 통합 커넥터로 사용할 수 있는 Microsoft에서 만든 사용 가능한 모든 커넥터 및 API 앱이 나열되어 있습니다.

| 이름 | 설명 |
| --- | --- |
| [BizTalk 규칙](app-service-logic-use-biztalk-rules.md) |BizTalk 규칙을 사용하여 조직 내에서 비즈니스 논리를 정의하고 제어합니다. 비즈니스 정책은 관련된 응용 프로그램을 다시 컴파일하고 다시 배포하지 않고도 업데이트할 수 있습니다. |
| [BizTalk XPath 추출기](app-service-logic-xpath-extract.md) |선택한 XPath에 따라 XML 콘텐츠에서 데이터를 조회 및 추출합니다. |
| [DB2 커넥터](app-service-logic-connector-db2.md) |Windows 운영 체제를 실행하는 IBM DB2 온-프레미스 및 Azure 가상 컴퓨터에 연결합니다. Web API 및 OData API 작업을 Informix 구조적 쿼리 언어 명령에 매핑할 수 있습니다. <br/><br/>트리거가 없습니다. 작업은 테이블 선택, 삽입, 업데이트, 삭제 및 사용자 지정 문을 포함합니다<br/><br/> 이 커넥터에는 TCP/IP 네트워크를 통해 Informix 서버에 연결하는 DRDA용 Microsoft 클라이언트도 포함되어 있습니다. |
| [파일](app-service-logic-connector-file.md) |이 커넥터를 사용하여 온-프레미스 파일 시스템 또는 네트워크에 연결하고 파일 업로드, 삭제, 나열 등과 같은 다른 파일 작업을 완료할 수 있습니다. |
| [Informix](app-service-logic-connector-informix.md) |Windows 운영 체제를 실행하는 IBM Informix 데이터베이스,  온-프레미스 및 Azure 가상 컴퓨터에 연결합니다. Web API 및 OData API 작업을 Informix 구조적 쿼리 언어 명령에 매핑할 수 있습니다.<br/><br/>트리거가 없습니다. 작업은 테이블 선택, 삽입, 업데이트, 삭제 및 사용자 지정 문을 포함합니다.<br/><br/>온-프레미스를 사용하여 VPN 또는 Azure ExpressRoute를 사용할 수 있습니다. 이 커넥터에는 TCP/IP 네트워크를 통해 Informix 서버에 연결하는 DRDA용 Microsoft 클라이언트도 포함되어 있습니다. |
| [Microsoft SQL Server](app-service-logic-connector-sql.md) |온-프레미스 SQL Server 또는 Azure SQL 데이터베이스에 연결합니다. SQL 데이터베이스 테이블의 항목 생성, 업데이트, 가져오기 및 삭제 작업을 수행할 수 있습니다. |
| MQ |Windows 운영 체제를 실행하는 IBM WebSphere MQ Server 버전 8, 온-프레미스 및 Azure 가상 컴퓨터에 연결합니다. 온-프레미스를 사용하여 VPN 또는 Azure ExpressRoute를 사용할 수 있습니다. 커넥터에는 MQ용 Microsoft 클라이언트도 포함되어 있습니다.<br/><br/>트리거가 없습니다. 작업이 없습니다.<br/><br/>**참고** 현재는 논리 앱과 함께 사용할 수 없습니다. |

## <a name="connectors-as-triggers"></a>트리거로 사용되는 커넥터
일부 커넥터는 논리 앱에 대한 트리거를 제공합니다. 다음 두 가지 유형의 트리거가 있습니다.

1. 폴링 트리거: 이 트리거는 지정된 빈도로 서비스를 폴링하여 새 데이터를 확인합니다. 새 데이터를 사용할 수 있으면 논리 앱의 새 인스턴스가 해당 데이터를 입력으로 사용하여 실행됩니다. 동일한 데이터가 여러 번 사용되지 않도록 트리거는 읽어서 논리 앱으로 전달된 데이터를 정리합니다. 이러한 커넥터의 예로 파일, SQL 및 Azure 저장소가 있습니다.
2. 밀어넣기 트리거: 이 트리거는 끝점에서 데이터를 수신 대기하거나 발생하는 이벤트를 수신 대기합니다. 그런 다음 논리 앱의 새 인스턴스를 트리거합니다. 이러한 커넥터의 예로 HTTP 수신기와 Twitter가 있습니다.

## <a name="connectors-as-actions"></a>작업으로 사용되는 커넥터
커넥터를 논리 앱의 내부에서 작업으로 사용할 수도 있습니다. 작업은 다음 실행에 사용되는 논리 앱 내에서 데이터를 조회하는 데 유용합니다. 예를 들어 주문을 처리할 때 고객에 대한 추가적인 내용은 SQL 데이터베이스에서 데이터를 조회할 필요가 있을 수 있습니다. 또는 대상 위치에서 데이터를 작성, 업데이트 또는 삭제해야 할 수 있습니다. 커넥터가 제공하는 작업을 사용하여 이를 수행할 수 있습니다. 작업은 해당 Swagger 메타데이터에 정의된 대로 API 앱의 작업에 매핑됩니다.

## <a name="create-your-own-connectors-and-api-apps"></a>고유한 커넥터 및 API 앱 만들기
[커넥터 및 API Apps 참조](http://aka.ms/appservicesconnectorreference)  
[Azure App Service API 앱 트리거](../app-service-api/app-service-api-dotnet-triggers.md)  
[논리 앱 참조](https://msdn.microsoft.com/library/azure/dn948510.aspx)

## <a name="more-on-connectors-and-api-apps"></a>커넥터 및 API 앱에 대한 자세한 내용
[커넥터 및 BizTalk API 앱 정의](app-service-logic-what-are-biztalk-api-apps.md)  
[Azure 앱 서비스에서 하이브리드 연결 관리자 사용](app-service-logic-hybrid-connection-manager.md)  
[기본 제공 API 앱 및 커넥터 관리 및 모니터링](app-service-logic-monitor-your-connectors.md)




<!--HONumber=Nov16_HO2-->


