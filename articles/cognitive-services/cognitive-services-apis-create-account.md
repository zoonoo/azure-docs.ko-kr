---
title: Azure Portal에서 Cognitive Services API 계정 만들기 | Microsoft Docs
description: Azure Portal에서 Microsoft Cognitive Services API를 만드는 방법입니다.
services: cognitive-services
documentationcenter: ''
author: garyericson
manager: cgronlun
editor: ''
ms.assetid: b6176bb2-3bb6-4ebf-84d1-3598ee6e01c6
ms.service: cognitive-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/01/2018
ms.author: garye
ms.reviewer: gibattag
ms.openlocfilehash: 3697dd0628f0028cb486139e92c032f0d757c6ed
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35374487"
---
# <a name="create-a-cognitive-services-apis-account-in-the-azure-portal"></a>Azure Portal에서 Cognitive Services API 계정 만들기

Microsoft Cognitive Service API를 사용하려면 먼저 Azure Portal에서 계정을 만들어야 합니다.

1. [Azure 포털](http://portal.azure.com)에 로그인합니다.

2. **+ 리소스 만들기**를 클릭합니다.

3. Azure Marketplace에서 **AI + Cognitive Services**를 선택하고 사용 가능한 API 목록을 검색합니다. Cognitive Services API의 전체 목록을 보려면 **모두 보기**를 클릭합니다. 계속 진행하려면 선택한 API를 클릭합니다.

    ![Cognitive Services API 선택](media/cognitive-services-apis-create-account/select-cognitive-services-apis.png)

4. **만들기** 페이지에서 다음 정보를 제공합니다.

   - **계정 이름:** 계정의 이름입니다. 설명이 포함된 이름(예: *AFaceAPIAccount*)을 사용하는 것이 좋습니다.

   - **구독:** 참가자 이상의 권한이 있는 사용 가능한 Azure 구독 중 하나를 선택합니다.

   - **API 형식:** 사용하려는 Cognitive Services API를 선택합니다. 사용 가능한 다양한 Cognitive Services API에 대한 자세한 내용은 [Cognitive Services](https://azure.microsoft.com/services/cognitive-services/) 사이트를 참조하세요.

   - **가격 책정 계층:** Cognitive Services 계정의 비용은 실제 사용량 및 선택한 옵션에 따라 다릅니다. 각 API의 가격 책정에 대한 자세한 내용은 [가격 책정 페이지](https://azure.microsoft.com/pricing/details/cognitive-services/)를 참조하세요.

   - **리소스 그룹:** 리소스 그룹은 동일한 수명 주기, 권한 및 정책을 공유하는 리소스의 컬렉션입니다. 리소스 그룹에 대한 자세한 내용은 [포털을 통해 Azure 리소스 관리](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-portal)를 참조하세요.

   - **리소스 그룹 위치:** 선택된 API가 전역(특정 위치에 바인딩되지 않음)인 경우에만 필요합니다. 그렇지만 API가 전역이고 특정 위치에 바인딩되지 않은 경우 Cognitive Services API 계정과 연결된 메타데이터가 상주하는 리소스 그룹의 위치를 지정해야 합니다. 이 위치는 계정의 런타임 가용성에 영향을 주지 않습니다. 리소스 그룹에 대한 자세한 내용은 [포털을 통해 Azure 리소스 관리](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-portal)를 참조하세요.

   - **온라인 서비스 사용 약관에 대한 명시적 승인**: 계정을 만들려면 구독 소유자 또는 참가자([Azure 역할 기반 액세스 제어](https://docs.microsoft.com/azure/role-based-access-control/overview)에서 정의됨)가 [온라인 서비스 사용 약관](https://www.microsoft.com/en-us/Licensing/product-licensing/products.aspx)에서 Cognitive Services에 적용되는 사용 약관을 명시적으로 승인해야 합니다. 

     구독 소유자는 [Azure Portal을 사용하여 리소스 정책 할당 및 관리](../azure-policy/assign-policy-definition.md) 문서에 따라 “허용되는 리소스 종류 없음” 정책 정의를 할당하고 **Microsoft.CognitiveServices/accounts**를 대상 리소스 종류로 지정하여 [Azure 정책](../azure-policy/azure-policy-introduction.md)을 통해 특정 리소스 그룹 또는 구독과 관련해서 Cognitive Services 계정을 만들지 못하도록 설정할 수 있습니다.

     계정 생성이 비활성화된 경우 계정을 만들 때 다음 오류가 표시됩니다.

     ![계정 만들기 오류](media/cognitive-services-apis-create-account/error-message.png)

5. Azure Portal 대시보드에 계정을 고정하려면 **대시보드에 고정**을 클릭합니다.

6. **만들기**를 클릭하여 계정을 만듭니다.

Cognitive Services 계정이 성공적으로 배포되면 대시보드에서 타일을 클릭하여 계정 정보를 확인합니다.

**개요** 섹션의 **끝점 URL**과 **키** 섹션의 키를 사용하여 응용 프로그램에서 API 호출을 시작할 수 있습니다.

![계정 정보 표시](media/cognitive-services-apis-create-account/display-account.png)

![계정 키 표시](media/cognitive-services-apis-create-account/account-keys.png)

### <a name="next-steps"></a>다음 단계

사용 가능한 모든 Microsoft Cognitive Services에 대한 자세한 내용은 [Cognitive Services](https://azure.microsoft.com/services/cognitive-services/)를 참조하세요.

몇 가지 예제 Cognitive Services API 사용에 대한 빠른 시작 가이드는 다음을 참조하세요.

 - [Computer Vision C# 빠른 시작](/computer-vision/quickstarts/csharp.md)
 - [텍스트 분석 및 Python](/text-analytics/quickstarts/python.md)
 - [Face API 및 JavaScript](/face/quickstarts/javascript.md)
