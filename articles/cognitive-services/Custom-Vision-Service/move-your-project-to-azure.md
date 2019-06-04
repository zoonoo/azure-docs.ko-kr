---
title: 제한된 평가판 프로젝트를 Azure로 이동
titlesuffix: Azure Cognitive Services
description: 제한된 평가판 프로젝트를 Azure로 이동 하는 방법에 대해 알아봅니다.
services: cognitive-services
author: anrothMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 02/19/2019
ms.author: anroth
ms.openlocfilehash: 6fac6531ea0a39796de13f95aee33b30dc91f131
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/18/2019
ms.locfileid: "59274453"
---
# <a name="how-to-move-your-limited-trial-project-to-azure"></a>제한된 평가판 프로젝트를 Azure로 이동 하는 방법

Custom Vision Service를 Azure로 이동이 완료되면, Azure 외부에서 제한된 평가판 프로젝트에 대한 지원이 종료 됩니다. 이 문서는 Custom Vision Api를 사용하는여 Azure 리소스에 제한된 평가판 프로젝트를 복사하는 방법을 보여 줍니다.

[Custom Vision 웹 사이트](https://customvision.ai)에서 제한된 평가판 프로젝트 보기에 대한 지원은 2019 년 3 월 25 일에 종료 되었습니다. 이 문서 에서는 GitHub의 [마이그레이션 python 스크립트](https://github.com/Azure-Samples/custom-vision-move-project)를 통해 Custom Vision Api를 사용하여 Azure 리소스에 프로젝트를 복제하는 방법을 설명합니다.

제한된 평가판 사용 중단 프로세스에서 키 마감일을 비롯한 자세한 내용은 [릴리스 노트](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/release-notes#february-25-2019)를 참고하거나 제한된 평가판 프로젝트의 소유자에게 전자 메일을 전송합니다.

[마이그레이션 스크립트](https://github.com/Azure-Samples/custom-vision-move-project)는 현재 반복에서 모든 지역, 태그와 이미지를 다운로드한 다음 업로드하여 프로젝트를 다시 생성합니다. 그것은 학습 시킬수 있는 새 구독에 새 프로젝트를 남기게 됩니다.

## <a name="prerequisites"></a>필수 조건

- [Custom Vision 웹 사이트](https://customvision.ai)에 로그온하기 원하는 Microsoft 계정 또는 Azure Active Directory (AAD) 계정에 로그인하는데 관련된 유효한 Azure 구독이 필요 합니다. 
    - Azure 계정이 없는 경우 무료 [계정을 생성합니다](https://azure.microsoft.com/free/).
    - Azure 구독 및 리소스의 개념에 대한 소개는 [Azure 개발자 가이드.](https://docs.microsoft.com/azure/guides/developer/azure-developer-guide#manage-your-subscriptions)를 참조합니다.
- [Python](https://www.python.org/downloads/)
- [Pip](https://pip.pypa.io/en/stable/installing/)

## <a name="create-custom-vision-resources-in-the-azure-portal"></a>Azure portal에서 Custom Vision 리소스 만들기

Azure에서 Custom Vision Service를 사용 하려면, [Azure portal](https://portal.azure.com/?microsoft_azure_marketplace_ItemHideKey=microsoft_azure_cognitiveservices_customvision#create/Microsoft.CognitiveServicesCustomVision)에서 Custom Vision 학습 및 예측 리소스를 만들어야 합니다.

여러 프로젝트를 단일 리소스에 연결할 수 있습니다. [가격 책정 및 제한](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/limits-and-quotas)에 대한 자세한 정보를 사용할 수 있습니다. Custom Vision Service를 계속 무료로 사용하려면, Azure portal에서 F0 계층을 선택할 수 있습니다. 

> [!NOTE]
> Azure 리소스로 Custom Vision 프로젝트를 이동하면, 해당 Azure 리소스의 [권한을]( https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal) 기본 상속합니다. 조직의 다른 사용자가 프로젝트가 속하는 Azure 리소스의 소유자 인 경우, [Custom Vision 웹 사이트](https://customvision.ai)의 프로젝트에 액세스 할 수 있습니다. 마찬가지로, 리소스를 삭제하면 프로젝트 개체는 삭제 됩니다.  

## <a name="find-your-limited-trial-project-information"></a>제한된 평가판 프로젝트 정보 찾기

프로젝트를 이동하기 위해, 마이그레이션하려는 프로젝트에 대한 _프로젝트 ID_ 및 _학습 키_ 가 필요합니다. 이 정보가 없는 경우, 각 프로젝트에 대한 키 및 ID를 가져오기 위해 [ https://limitedtrial.customvision.ai/projects ](https://limitedtrial.customvision.ai/projects) 방문합니다. 

## <a name="use-the-python-sample-code-to-copy-your-project-to-azure"></a>Python 샘플 코드를 사용하여 Azure에 프로젝트 복사

제한된 평가판 키와 프로젝트 ID를 "source" 자료로 사용하고 "destination"으로 만든 새 Azure 리소스의 키를 사용하여 [샘플 코드 지침](https://github.com/Azure-Samples/custom-vision-move-project) 따라합니다.

기본적으로 모든 제한된 평가판 프로젝트는 미국 중남부 Azure 지역에 호스트 됩니다.

## <a name="next-steps"></a>다음 단계

이제 프로젝트를 Azure 리소스에 옮겨졌습니다. 작성한 응용 프로그램에 학습 및 예측 키를 업데이트 해야 합니다.

[Custom Vision 웹 사이트](https://customvision.ai)에서 프로젝트를 보기 위해서는, Azure portal에 로그인 하는 동일한 계정을 사용하여 로그인 합니다. 프로젝트에 표시 되지 않는 경우, Azure portal에서 리소스가 위치한 디렉토리와 [Custom Vision 웹 사이트](https://customvision.ai)에서 동일한 디렉터리에 있는지 확인 하십시오. Azure portal 및 CustomVision.ai 모두에서 화면의 오른쪽 위 모서리에 있는 드롭다운 사용자 메뉴에서 디렉터리를 선택할 수 있습니다.
