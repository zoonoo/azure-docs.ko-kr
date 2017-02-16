---
title: "파트너 및 엔터프라이즈 통합 팩에 대해 알아보기 | Microsoft Docs"
description: "엔터프라이즈 통합 팩 및 논리 앱에서 파트너를 사용하는 방법 알아보기"
services: logic-apps
documentationcenter: .net,nodejs,java
author: msftman
manager: anneta
editor: cgronlun
ms.assetid: b179325c-a511-4c1b-9796-f7484b4f6873
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/08/2016
ms.author: deonhe
translationtype: Human Translation
ms.sourcegitcommit: dc8c9eac941f133bcb3a9807334075bfba15de46
ms.openlocfilehash: 549dd716df6ee64892ef33ffe4d42751d8662ccb


---
# <a name="learn-about-partners-and-enterprise-integration-pack"></a>파트너 및 엔터프라이즈 통합 팩에 대해 알아보기
## <a name="overview"></a>개요
파트너를 만들기 전에 함께 사용자는 업무를 수행하려는 조직과 함께 서로가 전송한 메시지의 유효성을 식별하고 검사하는 데 유용한 정보를 공유해야 합니다. 다음을 논의하면 비즈니스 관계를 시작할 준비가 되고 통합 계정에 *파트너* 를 만들 수 있습니다.

## <a name="what-is-a-partner"></a>파트너란?
파트너는 B2B(기업 간) 메시징 및 트랜잭션에 참여하는 엔터티입니다. 

## <a name="how-are-partners-used"></a>파트너는 어떻게 사용하나요?
파트너는 규약을 작성하는 데 사용됩니다. 규약은 파트너 간에 교환될 메시지에 대한 세부 정보를 정의합니다. 

규약을 만들기 전에 통합 계정에 둘 이상의 파트너를 추가해야 합니다. 규약에 대한 파트너 중 하나는 조직이어야 합니다. 사용자 조직을 나타내는 파트너는 **호스트 파트너**라고 합니다. 두 번째 파트너는 조직이 메시지를 교환하는 다른 조직을 나타냅니다. 두 번째 파트너는 **게스트 파트너**라고 합니다. 게스트 파트너는 다른 회사 또는 조직 내 다른 부서일 수 있습니다.  

파트너를 추가하면 해당 파트너를 사용하여 규약을 만듭니다. 

수신 및 송신 설정은 호스트 파트너의 관점에서 시작됩니다. 예를 들어 규약의 수신 설정은 호스트 파트너가 게스트 파트너로부터 받은 메시지를 수신하는 방법을 결정합니다. 마찬가지로, 규약의 송신 설정은 호스트 파트너가 게스트 파트너에게 메시지를 보내는 방법을 나타냅니다.

## <a name="how-to-create-a-partner"></a>파트너를 만드는 방법
Azure 포털에서 다음을 수행합니다.  

1. **찾아보기**를 선택합니다.  
   ![](./media/logic-apps-enterprise-integration-overview/overview-1.png)    
2. 필터 검색 상자에 **통합**을 입력하고 결과 목록에서 **통합 계정**을 선택합니다.     
   ![](./media/logic-apps-enterprise-integration-overview/overview-2.png)  
3. 파트너를 추가할 **통합 계정**을 선택합니다.  
   ![](./media/logic-apps-enterprise-integration-overview/overview-3.png)  
4. **파트너** 타일을 선택합니다.  
   ![](./media/logic-apps-enterprise-integration-partners/partner-1.png)  
5. 열린 파트너 블레이드에서 **추가** 단추를 선택합니다.  
   ![](./media/logic-apps-enterprise-integration-partners/partner-2.png)  
6. 파트너의 **이름**을 입력한 다음 **한정자**를 선택하고 마지막으로 **값**을 입력합니다. 값은 앱에 들어오는 문서를 식별하는 데 사용됩니다.  
   ![](./media/logic-apps-enterprise-integration-partners/partner-3.png)  
7. *벨* 알림 아이콘을 선택하여 파트너 생성 프로세스의 진행률을 확인합니다.  
   ![](./media/logic-apps-enterprise-integration-partners/partner-4.png)  
8. **파트너** 타일을 선택합니다. 타일을 새로 고쳐서 증가된 파트너 수를 확인해야 합니다. 새 파트너를 반영하는 작업이 성공적으로 추가되었습니다.    
   ![](./media/logic-apps-enterprise-integration-partners/partner-5.png)  
9. 파트너 타일을 선택한 후에 새로 추가된 파트너가 파트너 블레이드에 표시됩니다.    
   ![](./media/logic-apps-enterprise-integration-partners/partner-6.png)  

## <a name="how-to-edit-a-partner"></a>파트너 편집 방법
다음 단계를 수행하여 통합 계정에 이미 존재하는 파트너를 편집합니다.  

1. **파트너** 타일을 선택합니다.  
2. 파트너 블레이드가 열리면 편집할 파트너를 선택합니다.  
3. **파트너 업데이트** 타일에서 필요한 내용을 변경합니다.  
4. 변경 사항이 만족스러우면 **저장** 링크를 선택하거나 **취소** 링크를 선택하여 변경 내용을 삭제합니다.  
   ![](./media/logic-apps-enterprise-integration-partners/edit-1.png)  

## <a name="how-to-delete-a-partner"></a>파트너 삭제 방법
1. **파트너** 타일을 선택합니다.  
2. 파트너 블레이드가 열리면 편집할 파트너를 선택합니다.  
3. **삭제** 링크를 선택합니다.    
   ![](./media/logic-apps-enterprise-integration-partners/delete-1.png)   

## <a name="next-steps"></a>다음 단계
* [규약에 대해 자세히 알아보기](../logic-apps/logic-apps-enterprise-integration-agreements.md "엔터프라이즈 통합 규약에 대해 알아보기")  




<!--HONumber=Jan17_HO3-->


