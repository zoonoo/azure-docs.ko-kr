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
ms.openlocfilehash: 6fac6531ea0a39796de13f95aee33b30dc91f131
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/08/2019
ms.locfileid: "59274453"
---
# <a name="how-to-move-your-limited-trial-project-to-azure"></a>제한 된 평가판 프로젝트를 Azure로 이동 하는 방법

Custom Vision Service에 Azure로 이동 함으로써 완료 되 면 Azure 외부에서 제한 된 평가판 프로젝트에 대 한 지원이 종료 됩니다. 이 문서는 Custom Vision Api를 사용 하 여 Azure 리소스에 제한 된 평가판 프로젝트를 복사 하는 방법을 보여 줍니다.

제한 된 평가판 프로젝트 보기에 대 한 지원 합니다 [Custom Vision 웹 사이트](https://customvision.ai) 2019 년 3 월 25 일에 종료 되었습니다. 이 문서 이제 하는 방법을 사용 하 여 사용자 지정 비전 Api를 사용 하 여는 [마이그레이션 python 스크립트](https://github.com/Azure-Samples/custom-vision-move-project) GitHub에서) Azure 리소스에 프로젝트를 복제 합니다.

제한 된 평가판 사용 중단 프로세스에서 키 마감일을 비롯 한 자세한 내용은을 참조 하세요 합니다 [릴리스](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/release-notes#february-25-2019) 또는 제한 된 평가판 프로젝트의 소유자에 게 전송 되는 전자 메일 통신 합니다.

합니다 [마이그레이션 스크립트](https://github.com/Azure-Samples/custom-vision-move-project) 현재 반복에서 이미지 및 다운로드 한 후 모든 지역, 태그를 업로드 하 여 프로젝트를 다시 할 수 있습니다. 이 학습 시킬 수 있는 새 구독에 새 프로젝트를 사용 하 여 없게 됩니다.

## <a name="prerequisites"></a>필수 조건

- Microsoft 계정 또는 Azure Active Directory (AAD) 계정에 로그인 하는 데 관련 된 유효한 Azure 구독이 필요 합니다 [웹 사이트 사용자 지정 비전](https://customvision.ai)합니다. 
    - Azure 계정이 없는 경우 [계정을 만들](https://azure.microsoft.com/free/) 무료입니다.
    - Azure 구독 및 리소스의 개념에 대 한 소개를 참조 합니다 [Azure 개발자 가이드.](https://docs.microsoft.com/azure/guides/developer/azure-developer-guide#manage-your-subscriptions)합니다.
-  [Python](https://www.python.org/downloads/)
- [Pip](https://pip.pypa.io/en/stable/installing/)

## <a name="create-custom-vision-resources-in-the-azure-portal"></a>Azure portal에서 사용자 지정 비전 리소스 만들기

Custom Vision Service에서 Azure를 사용 하려면에서 사용자 지정 비전 교육 및 예측 리소스를 만들 해야 합니다 [Azure portal](https://portal.azure.com/?microsoft_azure_marketplace_ItemHideKey=microsoft_azure_cognitiveservices_customvision#create/Microsoft.CognitiveServicesCustomVision)합니다. 

여러 프로젝트를 단일 리소스에 연결할 수 있습니다. 에 대 한 자세한 정보 [가격 책정 및 제한](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/limits-and-quotas) 를 사용할 수 있습니다. 을 계속 무료로 Custom Vision Service를 사용 하려면 Azure portal에서 F0 계층을 선택할 수 있습니다. 

> [!NOTE]
> Azure 리소스에 사용자 지정 비전 프로젝트를 이동 하면 기본 상속 [권한을]( https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal) 해당 Azure 리소스의 합니다. 프로젝트에 액세스 할 수는 조직의 다른 사용자가 프로젝트에 Azure 리소스의 소유자 인 경우는 [웹 사이트 사용자 지정 비전](https://customvision.ai)합니다. 마찬가지로, 리소스를 삭제 해도 프로젝트 개체는 삭제 됩니다.  

## <a name="find-your-limited-trial-project-information"></a>제한 된 평가판 프로젝트 정보 찾기

프로젝트를 이동 해야 합니다 _프로젝트 ID_ 및 _학습 키_ 마이그레이션하려는 프로젝트에 대 한 합니다. 이 정보가 없는 경우 방문 [ https://limitedtrial.customvision.ai/projects ](https://limitedtrial.customvision.ai/projects) 각 프로젝트에 대 한 키 및 ID를 가져옵니다. 

## <a name="use-the-python-sample-code-to-copy-your-project-to-azure"></a>Python 샘플 코드를 사용 하 여 Azure에 프로젝트를 복사할

에 따라 합니다 [샘플 코드 지침](https://github.com/Azure-Samples/custom-vision-move-project)제한 평가판 키를 사용 하 여을 "source" 자료 "대상"으로 만든 새 Azure 리소스의 키와 프로젝트 ID입니다.

기본적으로 모든 제한 된 평가판 프로젝트는 미국 중남부 Azure 지역에 호스트 됩니다.

## <a name="next-steps"></a>다음 단계

이제 프로젝트를 Azure 리소스에 옮겨졌습니다. 작성 한 응용 프로그램에 키를 학습 및 예측을 업데이트 해야 합니다.

프로젝트를 볼 수는 [Custom Vision 웹 사이트](https://customvision.ai), Azure portal에 로그인 하는 데 동일한 계정을 사용 하 여 로그인 합니다. 프로젝트에 표시 되지 않는 경우의 동일한 디렉터리에 있는지 확인 하십시오는 [Custom Vision 웹 사이트](https://customvision.ai) 디렉터리로 Azure portal에서 리소스 위치입니다. Azure portal 및 CustomVision.ai 모두에서 화면의 오른쪽 위 모서리에 있는 드롭다운 사용자 메뉴에서 디렉터리를 선택할 수 있습니다.