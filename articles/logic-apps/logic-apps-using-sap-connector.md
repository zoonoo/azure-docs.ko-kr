---
title: "Azure Logic Apps에서 온-프레미스 데이터 게이트웨이와 함께 SAP 커넥터 사용 | Microsoft Docs"
description: "Logic Apps를 사용하면 워크플로의 일부로 온-프레미스 SAP에 쉽게 연결할 수 있습니다."
services: logic-apps
documentationcenter: dev-center-name
author: padmavc
manager: anneta
ms.service: logic-apps
ms.devlang: wdl
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/10/2017
ms.author: padmavc
translationtype: Human Translation
ms.sourcegitcommit: 12584e9aa9c1400aba040320bd3a5f3ba3017470
ms.openlocfilehash: a483f527d15372c94fe7fe813dc49c3d6423e1e3


---
# <a name="get-started-with-the-sap-connector"></a>SAP 커넥터 시작 

하이브리드 클라우드 연결이 Logic Apps의 핵심입니다. 온-프레미스 데이터 게이트웨이를 사용하면 Logic Apps에서 온-프레미스에 있는 데이터를 관리하고 리소스에 안전하게 액세스할 수 있습니다. 이 문서에서는 HTTP를 통해 IDOC를 요청하고 다시 응답을 보내는 간단한 시나리오를 통해 온-프레미스 SAP 시스템에 연결하는 방법을 설명합니다.    

 > [!NOTE]
 > 이 SAP 커넥터는 다음 SAP 시스템을 지원합니다. 현재 Logic Apps는 90초가 지난 요청을 차단하는 시간 초과 제한이 있습니다. 현재 파일 선택기에 표시되는 필드 수에 제한이 있습니다(경로 수동으로 추가 가능).
 >
 >

## <a name="prerequisites"></a>필수 조건
- 최신 [온-프레미스 데이터 게이트웨이](https://www.microsoft.com/en-us/download/details.aspx?id=53127)를 설치하고 구성합니다.  

    최신 온-프레미스 데이터 게이트웨이 1.15.6150.1 이상 버전이 아직 없으면 설치합니다. [이 문서](http://aka.ms/logicapps-gateway).에서 자세한 내용을 찾을 수 있습니다. 나머지 단계를 계속하기 전에 게이트웨이를 온-프레미스 컴퓨터에 설치해야 합니다.

- 유니버설 게이트웨이를 설치한 컴퓨터에서 최신 SAP 클라이언트 라이브러리를 다운로드하여 설치

## <a name="use-sap-connector"></a>SAP 커넥터 사용

1. HTTP 요청 트리거를 만든 후 검색 필드에 “SAP”를 입력하여 SAP 커넥터 작업을 선택합니다.    
 ![SAP 검색](media/logic-apps-using-sap-connector/picture1.png)

2. "SAP"(SAP 설정에 따라 ApplicationHost 또는 MessagingHost)를 선택하고 유니버설 게이트웨이를 만들어서 그에 대한 연결을 만듭니다.
 - 기존 연결이 없으면 새로 만들라는 메시지가 표시됩니다.
 - “온-프레미스 데이터 게이트웨이 통해 연결” 옵션을 선택하면 추가 필드가 표시됩니다.
 - 연결 이름 문자열 지정
 - 이전 단계에서 설치한 게이트웨이를 선택하거나 “게이트웨이 설치”를 선택하여 새 게이트웨이를 설치합니다.   
 ![SAP에 연결 문자열 추가](media/logic-apps-using-sap-connector/picture2.png)   
  
  > [!NOTE]
  > 두 가지 종류의 SAP 연결이 있습니다. 하나는 [응용 프로그램 호스트](https://wiki.scn.sap.com/wiki/display/ABAP/ABAP+Application+Server)용이고 다른 하나는 [메시지 호스트](http://help.sap.com/saphelp_nw70/helpdata/en/40/c235c15ab7468bb31599cc759179ef/frameset.htm)용입니다.
  >
  >

3. 모든 세부 정보를 제공한 후 "만들기"를 클릭합니다. Logic Apps가 연결을 구성한 후 제대로 작동하는지 테스트합니다. 모든 검사가 완료되면 이전에 선택한 카드에 대한 옵션이 표시됩니다. 파일 선택기를 사용하여 올바른 IDOC 범주를 찾아보거나 경로에 수동으로 입력하고 본문 필드에서 HTTP 응답을 선택합니다.    
 ![SAP 작업](media/logic-apps-using-sap-connector/picture3.png)

4. 새 작업을 추가하여 HTTP 응답을 만듭니다. 응답 메시지는 SAP 출력에서 와야 합니다.

5. 논리 앱을 저장하고 HTTP 트리거 URL을 통해 IDOC를 전송하여 테스트합니다.

6. IDOC가 전송되면 논리 앱의 응답을 기다립니다.   

  > [!TIP]
  > [Logic Apps 모니터링](../logic-apps/logic-apps-monitor-your-logic-apps.md) 방법을 확인합니다.
  >
  >

7. 모든 작업이 끝났으므로 이제 SAP 커넥터를 사용하여 논리 앱 작업을 수행합니다. 제공되는 다른 기능을 알아볼 수 있습니다.
  - BAPI
  - RFC

## <a name="next-steps"></a>다음 단계
- [엔터프라이즈 통합 팩](../logic-apps/logic-apps-enterprise-integration-overview.md)에 대해 알아보기 
- Logic Apps로의 [온-프레미스 연결](../logic-apps/logic-apps-gateway-connection.md) 만들기


<!--HONumber=Jan17_HO3-->


