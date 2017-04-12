---
title: "Azure Logic Apps에서 온-프레미스 SAP 시스템에 연결 | Microsoft Docs"
description: "온-프레미스 데이터 게이트웨이를 사용하여 논리 앱 워크플로에서 온-프레미스 SAP 시스템에 연결"
services: logic-apps
documentationcenter: 
author: padmavc
manager: anneta
ms.service: logic-apps
ms.devlang: wdl
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/01/2017
ms.author: padmavc
translationtype: Human Translation
ms.sourcegitcommit: 72ac4936c656847fa07f1c1a37d6ddbf4ec1acf4
ms.openlocfilehash: 62b30acf324a5ed6a1b817157c86575d83b4104e


---
# <a name="use-the-sap-connector-in-your-logic-app"></a>논리 앱에서 SAP 커넥터 사용 

온-프레미스 데이터 게이트웨이를 사용하면 온-프레미스에 있는 데이터를 관리하고 리소스에 안전하게 액세스할 수 있습니다. 이 항목에서는 온-프레미스 SAP 시스템에 연결하여 HTTP를 통해 IDOC를 요청하고 다시 응답을 보내는 방법을 설명합니다.    

 > [!NOTE]
> 현재 제한 사항:
 > - 요청이 90초를 초과하면 Logic Apps가 시간 초과됩니다. 이 시나리오에서는 요청이 차단될 수 있습니다. 
 > - 파일 선택에 사용 가능한 모든 필드가 표시되지는 않습니다. 이 시나리오에서는 경로를 수동으로 추가할 수 있습니다.

## <a name="prerequisites"></a>필수 조건
- 최신 [온-프레미스 데이터 게이트웨이](https://www.microsoft.com/download/details.aspx?id=53127) 버전 1.15.6150.1 이상을 설치하고 구성합니다. [논리 앱에서 온-프레미스 데이터 게이트웨이에 연결하는 방법](http://aka.ms/logicapps-gateway)에 해당 단계가 나와 있습니다. 계속 진행하기 전에 게이트웨이를 온-프레미스 컴퓨터에 설치해야 합니다.

- 데이터 게이트웨이를 설치한 컴퓨터에서 최신 SAP 클라이언트 라이브러리를 다운로드하여 설치합니다. 다음 SAP 버전 중 하나를 사용할 수 있습니다. 
    - SAP Server
        - SAP ECC 6.0 Unicode
        - SAP ECC 6.0 Unicode, EHP 4.0
        - SAP ECC 6.0, EHP 7.0 및 모든 이전 EHP 버전
 
    - SAP Client
        - SAP RFC SDK 7.20 UNICODE
        - SAP .NET Connector(NCo) 3.0

## <a name="add-the-sap-connector"></a>SAP 커넥터 추가

SAP 커넥터에는 작업 기능이 있습니다. 그러나 트리거는 없습니다. 따라서 워크플로가 시작될 때 다른 트리거를 사용하세요. 

1. 요청/응답 트리거를 추가하고 **새 단계**를 선택합니다.
2. **작업 추가**를 선택하고 검색 필드에 `SAP`를 입력하여 SAP 커넥터를 선택합니다.    
 ![SAP 응용 프로그램 서버 또는 SAP 메시지 서버 선택](media/logic-apps-using-sap-connector/picture1.png)

3. SAP 설치 프로그램에 따라 **SAP** [응용 프로그램 서버](https://wiki.scn.sap.com/wiki/display/ABAP/ABAP+Application+Server) 또는 [메시지 서버](http://help.sap.com/saphelp_nw70/helpdata/en/40/c235c15ab7468bb31599cc759179ef/frameset.htm)를 선택합니다. 기존 연결이 없으면 새로 만들라는 메시지가 표시됩니다. 
    1. **온-프레미스 데이터 게이트웨이를 통해 연결**을 선택하고 SAP 시스템에 대한 세부 정보를 입력합니다.   
 ![SAP에 연결 문자열 추가](media/logic-apps-using-sap-connector/picture2.png)  
    2. 기존 **게이트웨이**를 선택합니다. 또는 **게이트웨이 설치**를 선택하여 새 게이트웨이를 설치합니다.    
 ![새 게이트웨이 설치](media/logic-apps-using-sap-connector/install-gateway.png)
  
    3. 모든 세부 정보를 입력한 후 **만들기**를 선택합니다. Logic Apps에서 연결을 구성하고 테스트하여 제대로 작동하는지 확인합니다.

4. SAP 연결의 이름을 입력합니다.

5. 이제 여러 다른 SAP 옵션을 사용할 수 있습니다. 파일 선택을 사용하여 IDOC 범주를 찾습니다. 또는 경로를 수동으로 입력하고 **본문** 필드에서 HTTP 응답을 선택합니다.     
 ![SAP 작업](media/logic-apps-using-sap-connector/picture3.png)

6. 새 작업을 추가하여 HTTP 응답을 만듭니다. 응답 메시지는 SAP 출력에서 가져옵니다.

7. 논리 앱을 저장합니다. HTTP 트리거 URL을 통해 IDOC를 전송하여 테스트합니다. IDOC가 전송되면 논리 앱의 응답을 기다립니다.   

  > [!TIP]
  > [Logic Apps 모니터링](../logic-apps/logic-apps-monitor-your-logic-apps.md) 방법을 확인합니다.

이제 SAP 커넥터가 논리 앱에 추가되었으므로 다른 기능을 살펴보겠습니다.

  - BAPI
  - RFC

## <a name="next-steps"></a>다음 단계
- [엔터프라이즈 통합 팩](../logic-apps/logic-apps-enterprise-integration-overview.md)에서 유효성 검사, 변환을 수행하는 방법과 기타 BizTalk 유사 함수에 대해서도 알아봅니다. 
- Logic Apps로의 [온-프레미스 연결](../logic-apps/logic-apps-gateway-connection.md) 만들기



<!--HONumber=Feb17_HO1-->


