---
title: "Logic Apps에서 온-프레미스 데이터 게이트웨이와 함께 파일 시스템 커넥터 사용 | Microsoft Docs"
description: "Logic Apps를 사용하면 워크플로의 일부로 온-프레미스 파일 시스템에 쉽게 연결할 수 있습니다."
services: logic-apps
documentationcenter: dev-center-name
author: derek1ee
manager: erikre
ms.service: logic-apps
ms.devlang: wdl
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/15/2016
ms.author: deli
translationtype: Human Translation
ms.sourcegitcommit: 9b2ae8a06cfa0cf686b7cae50d3b6903e536ee73
ms.openlocfilehash: fc81b6ca60af48ddfd8862d5f6620db537646ae7


---
# <a name="use-file-system-connector-with-on-premises-data-gateway"></a>온-프레미스 데이터 게이트웨이와 함께 파일 시스템 커넥터 사용

하이브리드 클라우드 연결이 Logic Apps의 핵심입니다. 온-프레미스 데이터 게이트웨이를 사용하면 Logic Apps에서 온-프레미스에 있는 데이터를 관리하고 리소스에 안전하게 액세스할 수 있습니다. 이 문서에서는 간단한 시나리오 “Dropbox에 업로드된 파일을 파일 공유에 복사한 다음 전자 메일 전송”를 사용하여 온-프레미스 파일 시스템에 연결하는 방법을 설명합니다.

## <a name="prerequisites"></a>필수 조건
- 최신 [온-프레미스 데이터 게이트웨이](https://www.microsoft.com/en-us/download/details.aspx?id=53127)를 설치하고 구성합니다.

 최신 온-프레미스 데이터 게이트웨이 1.15.6150.1 이상 버전이 아직 없으면 설치합니다. [이 문서](http://aka.ms/logicapps-gateway).에서 자세한 내용을 찾을 수 있습니다. 나머지 단계를 계속하기 전에 게이트웨이를 온-프레미스 컴퓨터에 설치해야 합니다.

## <a name="use-file-system-connector"></a>파일 시스템 커넥터 사용

1. Dropbox "파일을 만들 때" 트리거를 만든 다음, 지원되는 모든 파일 커넥터 작업을 둘러봅니다. 이 작업은 검색에 "파일 시스템"을 입력하는 것만큼 간단합니다.

 ![파일 커넥터 검색](./media/app-service-logic-use-file-connector/search-file-connector.png)

2. "파일 만들기"를 선택하고 연결을 만듭니다.
 - 기존 연결이 없으면 새로 만들라는 메시지가 표시됩니다.
 - “온-프레미스 데이터 게이트웨이 통해 연결" 옵션을 선택하면 추가 필드가 표시됩니다.
 - 루트 폴더를 지정합니다. 루트 폴더는 온-프레미스 데이터 게이트웨이가 설치된 시스템의 로컬 폴더이거나 시스템에서 액세스할 수 있는 네트워크 공유일 수 있습니다.
 - 게이트웨이에 대한 사용자 이름과 암호를 입력합니다.
 - 이전 단계에서 설치한 게이트웨이를 선택합니다.
    
 > [!NOTE]
 > 루트 폴더는 모든 파일 관련 작업에 대한 상대 경로에 사용되는 기본 상위 폴더입니다.

 ![연결 구성](./media/app-service-logic-use-file-connector/create-file.png)

3. 모든 세부 정보를 제공한 후 "만들기"를 클릭합니다. Logic Apps는 연결을 구성한 후 제대로 작동하는지 테스트합니다. 모든 항목을 확인하면 이전에 선택한 카드에 대한 옵션이 표시되고 파일 커넥터를 사용할 준비가 됩니다.

4. 파일을 Dropbox에 업로드하고 온-프레미스에 있는 파일 공유의 루트 폴더에 복사합니다.

 ![파일 작업 만들기](./media/app-service-logic-use-file-connector/create-file-filled.png)

5. 파일이 복사되면 관련 사용자가 알 수 있도록 전자 메일을 보내보겠습니다. 다른 커넥터와 마찬가지로, 이전 작업의 출력은 "동적 콘텐츠" 선택에서 사용할 수 있습니다.
 - 전자 메일 받는 사람, 제목 및 본문을 지정합니다. "동적 콘텐츠" 선택을 사용하여 파일 커넥터에서 전자 메일을 보다 풍부하게 만들 수 있는 출력을 선택합니다.

 ![전자 메일 보내기 작업](./media/app-service-logic-use-file-connector/send-email.png)

6. 논리 앱을 저장하고 Dropbox에 파일을 업로드하여 테스트합니다. 온-프레미스 파일 공유에 파일이 복사되는 것을 볼 수 있으며, 해당 작업에 대한 전자 메일 알림이 수신됩니다.
 > [!TIP]
 > [Logic Apps 모니터링](app-service-logic-monitor-your-logic-apps.md) 방법을 확인합니다.

7. 모든 작업이 끝났으므로 이제 파일 시스템 커넥터를 사용하여 Logic Apps 작업을 수행합니다. 제공되는 다른 기능을 알아볼 수 있습니다.
    - 파일 만들기
    - 폴더의 파일 나열
    - 파일 첨부
    - 파일 삭제
    - 파일 콘텐츠 가져오기
    - 경로를 사용하여 파일 콘텐츠 가져오기
    - 파일 메타데이터 가져오기
    - 경로를 사용하여 파일 메타데이터 가져오기
    - 루트 폴더의 파일 나열
    - 파일 업데이트

## <a name="next-steps"></a>다음 단계
- [엔터프라이즈 통합 팩](app-service-logic-enterprise-integration-overview.md)에 대해 알아보기 
- Logic Apps로의 [온-프레미스 연결](app-service-logic-gateway-connection.md) 만들기



<!--HONumber=Nov16_HO3-->


