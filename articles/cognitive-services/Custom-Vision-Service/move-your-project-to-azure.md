---
title: 제한 된 평가판 프로젝트를 Azure로 이동
titlesuffix: Azure Cognitive Services
description: 제한 된 평가판 프로젝트를 Azure로 이동 하는 방법에 알아봅니다.
services: cognitive-services
author: anrothMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 02/19/2019
ms.author: anroth
ms.openlocfilehash: a9f49af54f391b159f8b3d626fffc36635f5e51f
ms.sourcegitcommit: 1516779f1baffaedcd24c674ccddd3e95de844de
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/26/2019
ms.locfileid: "56821312"
---
# <a name="how-to-move-your-limited-trial-project-to-azure-using-the-customvisionai-site"></a>제한 된 평가판 프로젝트가 CustomVision.ai 사이트를 사용 하 여 Azure로 이동 하는 방법


Custom Vision Service에서 이제 그대로 [Azure 미리 보기](https://azure.microsoft.com/services/preview/), Azure 외부에서 제한 된 평가판 프로젝트에 대 한 지원이 종료 됩니다. 이 문서를 사용 하는 방법을 설명 합니다 [Custom Vision 웹 사이트](https://customvision.ai) Azure 리소스와 연결할 제한 된 평가판 프로젝트를 이동 합니다. 

> [!NOTE]
> Custom Vision 프로젝트는 Azure 리소스를 이동할 때 이러한 기본 상속 [권한을]( https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal) 해당 Azure 리소스의 합니다. 프로젝트에 액세스 할 수는 조직의 다른 사용자가 프로젝트에 Azure 리소스의 소유자 인 경우는 [웹 사이트 사용자 지정 비전](https://customvision.ai)합니다. 마찬가지로, 리소스를 삭제 해도 프로젝트 개체는 삭제 됩니다.  


Azure 구독 및 리소스의 개념에 대 한 소개를 참조 합니다 [Azure 개발자 가이드.](https://docs.microsoft.com/azure/guides/developer/azure-developer-guide#manage-your-subscriptions)


## <a name="prerequisites"></a>필수 조건

동일한 Microsoft 계정 또는 Azure Active Directory (AAD) 계정에 로그인 하는 데 관련 된 유효한 Azure 구독이 필요 합니다 [웹 사이트 사용자 지정 비전](https://customvision.ai)합니다. 

Azure 계정이 없는 경우 [계정을 만들](https://azure.microsoft.com/free/) 무료입니다.


## <a name="create-custom-vision-resources-in-the-azure-portal"></a>Azure portal에서 사용자 지정 비전 리소스 만들기
Custom Vision Service에서 Azure를 사용 하려면에서 사용자 지정 비전 교육 및 예측 리소스를 만들 해야 합니다 [Azure portal](https://portal.azure.com/?microsoft_azure_marketplace_ItemHideKey=microsoft_azure_cognitiveservices_customvision#create/Microsoft.CognitiveServicesCustomVision)합니다. 

 이 사용 하 여 프로젝트를 이동할 [웹 사이트 사용자 지정 비전](https://customvision.ai) 환경을 만들어야 리소스 미국 중남부 지역에서 미국 중남부에서에서 호스트 되므로 모든 제한 된 평가판 프로젝트입니다. 

여러 프로젝트를 단일 리소스에 연결할 수 있습니다. 에 대 한 자세한 정보 [가격 책정 및 제한](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/limits-and-quotas) 를 사용할 수 있습니다. 을 계속 무료로 Custom Vision Service를 사용 하려면 Azure portal에서 F0 계층을 선택할 수 있습니다. 


## <a name="move-your-limited-trial-project-to-an-azure-resource"></a>Azure 리소스에 제한 된 평가판 프로젝트 이동

1.  웹 브라우저에서로 이동 합니다 [Custom Vision 웹 사이트](https://customvision.ai) 선택한 __로그인__합니다. Azure 계정에 마이그레이션하려면 프로젝트를 엽니다. 
2.  화면의 오른쪽 위 모퉁이에서 기어 아이콘을 클릭 하 여 프로젝트에 대 한 설정 페이지를 엽니다. 

    ![프로젝트 설정이 프로젝트 페이지의 오른쪽 위에 있는 기어 아이콘을 되었습니다.](./media/move-your-project-to-azure/settings-icon.png)


3. 클릭할 __Azure로 이동할__합니다.

    ![프로젝트 설정 페이지의 왼쪽 아래에 Azure 단추에 이동이 됩니다.](./media/move-your-project-to-azure/move-to-azure.jpg)


4. 드롭다운 목록에서를 __Azure로 이동할__ 단추, 프로젝트를 이동 하려는 Azure 리소스를 선택 합니다. 클릭 __이동__합니다. 

5. Custom Vision Service에 대 한 이전에 만든 Azure 리소스에 표시 되지 않는 경우 다른 디렉터리의 수도 있습니다. 프로젝트에 다른 디렉터리에서 리소스를 이동 하려면 아래 지침을 따릅니다. 

    ![프로젝트 마이그레이션 기간입니다.](./media/move-your-project-to-azure/Project_Migration_Window.jpg)


## <a name="move-project-to-another-azure-directory"></a>프로젝트를 다른 Azure 디렉터리 이동 

> [!NOTE]
> Azure portal 및 CustomVision.ai 모두에서 화면의 오른쪽 위 모서리에 있는 드롭다운 사용자 메뉴에서 디렉터리를 선택할 수 있습니다.   


1. Azure 리소스는 디렉터리를 식별 합니다. Azure portal 메뉴 표시줄의 오른쪽 위에 있는 사용자 이름 아래에 나열 된 디렉터리를 찾을 수 있습니다. 

    ![Azure portal 메뉴 표시줄의 오른쪽 위에 있는 사용자 이름을 디렉터리에 나열 됩니다. .](./media/move-your-project-to-azure/identify_directory.jpg)

2. 사용자 지정 비전 교육 리소스의 리소스 ID를 찾습니다. 사용자 지정 비전 교육 리소스를 열고 "리소스 관리" 섹션에서 "속성"을 선택 하 여 Azure portal에서 찾을 수 있습니다. 리소스 ID가 있을 수 있습니다. 

    ![사용자 지정 비전 교육 리소스를 열고 "리소스 관리" 섹션에서 "속성"을 선택 하 여 Azure portal의 리소스 ID를 찾습니다.](./media/move-your-project-to-azure/resource_ID_azure_portal.jpg)


3. 또는 사용자 지정 비전 웹 사이트에서 직접 사용자 지정 비전 리소스의 리소스 ID를 찾을 수 있습니다 [설정 페이지]( https://www.customvision.ai/projects#/settings)합니다. Azure 리소스는 동일한 디렉터리로 전환 해야 합니다.

    ![Custom Vision 웹 사이트 설정 페이지에서 각 리소스에 대 한 리소스 ID 나열 됩니다.](./media/move-your-project-to-azure/resource_ID_CVS_portal.jpg)

4. 리소스 ID를가지고 Azure 리소스에 제한 된 평가판에서 전환 하려는 사용자 지정 비전 프로젝트를 반환 합니다. 미리 알림을 찾을 원래 디렉터리 전환 해야 합니다. 제공 된 지침을 따릅니다 [위에](#move-your-limited-trial-project-to-an-azure-resource) 프로젝트 설정 페이지를 열고 선택 __Azure로 이동할__합니다. 


5. Azure 창으로 이동, "다른 Azure 디렉터리에 이동?"에 대 한 상자를 선택 합니다. 프로젝트를 이동 하 고 프로젝트를 이동 하는 리소스의 리소스 ID를 입력 하려는 디렉터리를 선택 합니다. 클릭 __이동__합니다. 



5. 프로젝트는 이제 다른 디렉터리에서를 기억 합니다. 프로젝트를 찾으려면 프로젝트에 있는 Custom Vision 웹 포털에서 동일한 디렉터리로 전환 해야 합니다. Azure portal에서와 [Custom Vision 웹 사이트](https://customvision.ai), 화면의 오른쪽 위 모서리에서 계정 드롭다운 메뉴에서 디렉터리를 선택할 수 있습니다. 

## <a name="next-steps"></a>다음 단계

이제 프로젝트를 Azure 리소스에 옮겨졌습니다. 작성 한 응용 프로그램에 키를 학습 및 예측을 업데이트 해야 합니다.
