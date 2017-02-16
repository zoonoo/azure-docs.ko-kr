---
title: "Azure Logic Apps에서 통합 계정 만들기, 연결, 삭제 또는 이동 | Microsoft Docs"
description: "통합 계정을 만들고 논리 앱에 연결하는 방법"
services: logic-apps
documentationcenter: .net,nodejs,java
author: MandiOhlinger
manager: anneta
editor: 
ms.assetid: d3ad9e99-a9ee-477b-81bf-0881e11e632f
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/27/2017
ms.author: mandia
translationtype: Human Translation
ms.sourcegitcommit: dca762854f22721de76f69cbc28c0377d563fe28
ms.openlocfilehash: e26271a1291fc76e3ea8f93a7aa9ab8c811ff604


---

# <a name="what-is-an-integration-account"></a>통합 계정이란?
통합 계정을 사용하면 엔터프라이즈 통합 앱에서 스키마, 맵, 인증서, 파트너 및 규약을 포함한 아티팩트를 관리할 수 있습니다. 만든 통합 앱은 모두 통합 계정을 사용하여 이러한 스키마, 맵, 인증 등에 액세스합니다.

## <a name="create-an-integration-account"></a>통합 계정 만들기
1. **찾아보기**를 선택합니다.   
   ![](./media/logic-apps-enterprise-integration-accounts/account-1.png)  
2. 필터 검색 상자에서 **통합**을 입력하고 결과 목록에서 **통합 계정**을 선택합니다.     
   ![](./media/logic-apps-enterprise-integration-accounts/account-2.png)  
3. 페이지 위쪽에 있는 메뉴에서 *추가* 단추를 선택합니다.      
   ![](./media/logic-apps-enterprise-integration-accounts/account-3.png)  
4. **이름**을 입력하고 사용하려는 **구독**을 선택한 후 새 **리소스 그룹**을 만들거나 기존 리소스 그룹을 선택하고 통합 계정을 호스트할 **위치**를 선택하고 **가격 책정 계층**을 선택한 후 **만들기** 단추를 선택합니다.   
   
   이 시점에서 선택한 위치에서 통합 계정이 프로비전됩니다. 1분 내에 완료해야 합니다.    
   ![](./media/logic-apps-enterprise-integration-accounts/account-4.png)  
5. 페이지를 새로 고칩니다. 새 통합 계정이 나열되어 있습니다.  
   ![](./media/logic-apps-enterprise-integration-accounts/account-5.png) 

그런 다음 방금 만든 통합 계정을 논리 앱에 연결합니다. 

## <a name="link-an-integration-account-to-a-logic-app"></a>논리 앱에 통합 계정 연결
논리 앱에서 통합 계정의 맵, 스키마, 규약 및 다른 아티팩트에 액세스하려면 통합 계정을 논리 앱에 연결해야 합니다.

#### <a name="prereqs"></a>선행 조건
* 통합 계정
* 논리 앱

> [!NOTE] 
> 시작하기 전에 통합 계정과 논리 앱이 **동일한 Azure 위치**에 있어야 합니다.

1. 논리 앱의 메뉴에서 **설정** 링크를 선택합니다.  
   ![](./media/logic-apps-enterprise-integration-accounts/linkaccount-1.png)   
2. [설정] 블레이드에서 **통합 계정** 항목을 선택합니다.  
   ![](./media/logic-apps-enterprise-integration-accounts/linkaccount-2.png)   
3. **통합 계정 선택** 드롭다운 목록 상자에서 논리 앱에 연결하려는 통합 계정을 선택합니다.  
   ![](./media/logic-apps-enterprise-integration-accounts/linkaccount-3.png)   
4. 작업을 저장합니다.  
   ![](./media/logic-apps-enterprise-integration-accounts/linkaccount-4.png)   
5. 통합 계정이 논리 앱에 연결되어 있고 이제는 통합 계정의 모든 아티팩트를 논리 앱에서 사용할 수 있음을 나타내는 알림이 표시됩니다.  
   ![](./media/logic-apps-enterprise-integration-accounts/linkaccount-5.png)   

통합 계정이 논리 앱에 연결되었으므로 논리 앱 내에서 B2B 커넥터를 사용할 수 있습니다. 일반적인 B2B 커넥터 일부에는 XML 유효성 검사, 플랫 파일 인코딩/디코딩이 포함되어 있습니다.  

## <a name="how-to-delete-an-integration-account"></a>통합 계정 삭제 방법
1. **찾아보기**를 선택합니다.  
   ![](./media/logic-apps-enterprise-integration-overview/overview-1.png)    
2. 필터 검색 상자에서 **통합**을 입력하고 결과 목록에서 **통합 계정**을 선택합니다.     
   ![](./media/logic-apps-enterprise-integration-overview/overview-2.png)  
3. 삭제하려는 **통합 계정**을 선택합니다.  
   ![](./media/logic-apps-enterprise-integration-overview/overview-3.png)  
4. 메뉴에 있는 **삭제** 링크를 선택합니다.   
   ![](./media/logic-apps-enterprise-integration-accounts/delete.png)  
5. 선택 내용을 확인합니다.    

## <a name="how-to-move-an-integration-account"></a>통합 계정 이동 방법
통합 계정을 새 구독 및 새 리소스 그룹에 쉽게 이동할 수 있습니다. 통합 계정을 이동하는 경우 다음 단계를 따릅니다.

> [!IMPORTANT]
> 통합 계정을 이동한 후에 새 리소스 ID를 사용할 수 있도록 모든 스크립트를 업데이트해야 합니다.

1. **찾아보기**를 선택합니다.  
   ![](./media/logic-apps-enterprise-integration-overview/overview-1.png)    
2. 필터 검색 상자에서 **통합**을 입력하고 결과 목록에서 **통합 계정**을 선택합니다.     
   ![](./media/logic-apps-enterprise-integration-overview/overview-2.png)  
3. 삭제하려는 **통합 계정**을 선택합니다.  
   ![](./media/logic-apps-enterprise-integration-overview/overview-3.png)  
4. 메뉴에 있는 **이동** 링크를 선택합니다.   
   ![](./media/logic-apps-enterprise-integration-accounts/move.png)  
5. 선택 내용을 확인합니다.    

## <a name="next-steps"></a>다음 단계
* [규약에 대해 자세히 알아보기](../logic-apps/logic-apps-enterprise-integration-agreements.md "엔터프라이즈 통합 규약에 대해 알아보기")  




<!--HONumber=Jan17_HO5-->


