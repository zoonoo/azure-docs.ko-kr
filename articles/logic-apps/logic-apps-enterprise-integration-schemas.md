---
title: "스키마 및 엔터프라이즈 통합 팩 개요 | Microsoft Docs"
description: "엔터프라이즈 통합 팩 및 논리 앱에서 스키마를 사용하는 방법 알아보기"
services: logic-apps
documentationcenter: .net,nodejs,java
author: msftman
manager: anneta
editor: cgronlun
ms.assetid: 56c5846c-5d8c-4ad4-9652-60b07aa8fc3b
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/29/2016
ms.author: deonhe
translationtype: Human Translation
ms.sourcegitcommit: dc8c9eac941f133bcb3a9807334075bfba15de46
ms.openlocfilehash: 2ad6ad4dcab9a7296dfc6fb6227c510daee85222


---
# <a name="learn-about-schemas-and-the-enterprise-integration-pack"></a>스키마 및 엔터프라이즈 통합 팩에 대해 알아보기
## <a name="why-use-a-schema"></a>스키마를 사용하는 이유
스키마를 사용하여 수신한 XML 문서가 유효한지, 예상되는 데이터가 미리 정의된 형식으로 문서에 포함되는지를 확인합니다. 스키마는 B2B 시나리오에서 교환되는 메시지의 유효성을 검사하는 데 사용됩니다.

## <a name="add-a-schema"></a>스키마 추가
Azure 포털에서 다음을 수행합니다.  

1. **추가 서비스**를 선택합니다.  
   !["추가 서비스"를 강조 표시한 Azure Portal의 스크린샷](media/logic-apps-enterprise-integration-schemas/overview-11.png)    
2. 필터 검색 상자에 **통합**을 입력하고 결과 목록에서 **통합 계정**을 선택합니다.     
   ![필터 검색 상자의 스크린샷](media/logic-apps-enterprise-integration-schemas/overview-21.png)  
3. 스키마를 추가할 **통합 계정**을 선택합니다.    
   ![통합 계정 목록의 스크린샷](media/logic-apps-enterprise-integration-schemas/overview-31.png)  
4. **스키마** 타일을 선택합니다.  
   !["스키마"가 강조 표시된 IEP 통합 계정의 스크린샷](media/logic-apps-enterprise-integration-schemas/schema-11.png)  

### <a name="add-a-schema-file-less-than-2-mb"></a>2MB보다 작은 스키마 파일 추가
1. 이전 단계에서 열린 **스키마** 블레이드에서 **추가**를 선택합니다.  
   !["추가" 단추가 강조 표시된 스키마 블레이드의 스크린샷](media/logic-apps-enterprise-integration-schemas/schema-21.png)  
2. 스키마의 이름을 입력합니다. 그런 다음 스키마 파일을 업로드하려면 **스키마** 텍스트 상자 옆의 폴더 아이콘을 선택합니다. 업로드 프로세스가 완료되면 **확인**을 선택합니다.    
   !["작은 파일"이 강조 표시된 "스키마 추가"의 스크린샷](media/logic-apps-enterprise-integration-schemas/schema-31.png)  

### <a name="add-a-schema-file-larger-than-2-mb-up-to-a-maximum-of-8-mb"></a>2MB(최대 8MB)보다 큰 스키마 파일 추가
이 프로세스는 Blob 컨테이너 액세스 수준인 **공용** 또는 **익명 액세스 없음**에 따라 달라 집니다. 이 액세스 수준을 확인하려면 **Azure Storage 탐색기**의 **Blob 컨테이너**에서 원하는 Blob 컨테이너를 선택합니다. **보안**을 선택하고 **액세스 수준** 탭을 선택합니다.

1. Blob 보안 액세스 수준이 **공용**인 경우 다음 단계를 수행합니다.  
   !["Blob 컨테이너", "보안" 및 "공용"이 강조 표시된 Azure Storage 탐색기의 스크린샷](media/logic-apps-enterprise-integration-schemas/blob-public.png)  
   
    a. 스키마를 저장소에 업로드하고 URI를 복사합니다.  
    ![URI가 강조 표시된 저장소 계정의 스크린샷](media/logic-apps-enterprise-integration-schemas/schema-blob.png)  
   
    b. **스키마 추가**에서 **큰 파일**을 선택하고 **콘텐츠 URI** 텍스트 상자에 URI를 제공합니다.  
    !["추가" 단추 및 " 큰 파일"이 강조 표시된 스키마의 스크린샷](media/logic-apps-enterprise-integration-schemas/schema-largefile.png)  
2. Blob 보안 액세스 수준이 **익명 액세스 없음**인 경우 다음 단계를 수행합니다.  
   !["Blob 컨테이너", "보안" 및 "익명 액세스 없음"이 강조 표시된 Azure Storage 탐색기의 스크린샷](media/logic-apps-enterprise-integration-schemas/blob-1.png)  
   
    a. 스키마를 저장소에 업로드합니다.  
    ![저장소 계정의 스크린샷](media/logic-apps-enterprise-integration-schemas/blob-3.png)
   
    b. 스키마에 대한 공유 액세스 서명을 생성합니다.  
    ![공유 액세스 서명 탭이 강조 표시된 저장소 계정의 스크린샷](media/logic-apps-enterprise-integration-schemas/blob-2.png)
   
    c. **스키마 추가**에서 **큰 파일**을 선택하고 **콘텐츠 URI** 텍스트 상자에 공유 액세스 서명 URI를 제공합니다.  
    !["추가" 단추 및 " 큰 파일"이 강조 표시된 스키마의 스크린샷](media/logic-apps-enterprise-integration-schemas/schema-largefile.png)  
3. EIP 통합 계정의 **스키마** 블레이드에서 이제 새로 추가된 스키마가 표시됩니다.  
   !["스키마" 및 새 스키마가 강조 표시된 EIP 통합 계정의 스크린샷](media/logic-apps-enterprise-integration-schemas/schema-41.png)

## <a name="edit-schemas"></a>스키마 편집
1. **스키마** 타일을 선택합니다.  
2. 열린 **스키마** 블레이드에서 편집하려는 스키마를 선택합니다.
3. **스키마** 블레이드에서 **편집**을 선택합니다.  
   ![스키마 블레이드의 스크린샷](media/logic-apps-enterprise-integration-schemas/edit-12.png)    
4. 열린 파일 선택 대화 상자를 사용하여 편집하려는 스키마 파일을 선택합니다.
5. 파일 선택에서 **열기**를 선택합니다.  
   ![파일 선택의 스크린샷](media/logic-apps-enterprise-integration-schemas/edit-31.png)  
6. 성공적으로 업로드되었다는 알림을 받게 됩니다.  

## <a name="delete-schemas"></a>스키마 삭제
1. **스키마** 타일을 선택합니다.  
2. 열린 **스키마** 블레이드에서 삭제하려는 스키마를 선택합니다.  
3. **스키마** 블레이드에서 **삭제**를 선택합니다.
   ![스키마 블레이드의 스크린샷](media/logic-apps-enterprise-integration-schemas/delete-12.png)  
4. 선택을 확인하려면 **예**를 선택합니다.  
   !["삭제 스키마" 확인 메시지의 스크린샷](media/logic-apps-enterprise-integration-schemas/delete-21.png)  
5. 마지막으로, **스키마** 블레이드에서 스키마 목록을 새로 고치면 삭제한 스키마는 더 이상 나열되지 않습니다.  
   !["스키마"가 강조 표시된 EIP 통합 계정의 스크린샷](media/logic-apps-enterprise-integration-schemas/delete-31.png)    

## <a name="next-steps"></a>다음 단계
* [엔터프라이즈 통합 팩에 대해 자세히 알아보기](logic-apps-enterprise-integration-overview.md "엔터프라이즈 통합 팩에 대해 알아보기")  




<!--HONumber=Jan17_HO3-->


