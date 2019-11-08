---
title: 제한 된 평가판 프로젝트를 Azure로 이동
titleSuffix: Azure Cognitive Services
description: Custom Vision에서 제한 된 평가판 프로젝트가 있나요? 이 문서에서는 마이그레이션 스크립트를 사용 하 여 Azure로 이동 하는 방법을 보여 줍니다.
services: cognitive-services
author: anrothMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 02/19/2019
ms.author: anroth
ms.openlocfilehash: 0a2b48a6c046150f6e685ecda0c0d765342e0194
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73818947"
---
# <a name="how-to-move-your-limited-trial-project-to-azure"></a>제한 된 평가판 프로젝트를 Azure로 이동 하는 방법

Custom Vision Service Azure로의 이동이 완료 되 면 Azure 외부의 제한 된 평가판 프로젝트에 대 한 지원이 종료 됩니다. 이 문서에서는 Custom Vision Api를 사용 하 여 제한 된 평가판 프로젝트를 Azure 리소스에 복사 하는 방법을 보여 줍니다.

[Custom Vision 웹 사이트](https://customvision.ai) 에서 제한 된 평가판 프로젝트를 볼 수 있는 지원은 2019 년 3 월 25 일에 종료 되었습니다. 이 문서에서는 이제 GitHub의 [마이그레이션 python 스크립트](https://github.com/Azure-Samples/custom-vision-move-project) 를 사용 하 여 Custom Vision api를 사용 하 여 프로젝트를 Azure 리소스에 복제 하는 방법을 보여 줍니다.

제한 된 평가판 사용 중단 프로세스의 주요 기한을 비롯 한 자세한 내용은 [릴리스 정보](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/release-notes#february-25-2019) 를 참조 하거나, 제한 된 평가판 프로젝트 소유자에 게 보내는 전자 메일 통신을 참조 하세요.

[마이그레이션 스크립트](https://github.com/Azure-Samples/custom-vision-move-project) 를 사용 하면 현재 반복에서 모든 태그, 영역 및 이미지를 다운로드 한 다음 업로드 하 여 프로젝트를 다시 만들 수 있습니다. 그러면 새 구독에 새 프로젝트가 표시 된 후 학습을 수행할 수 있습니다.

## <a name="prerequisites"></a>필수 조건

- [Custom Vision 웹 사이트](https://customvision.ai)에 로그인 하는 데 사용 하려는 AAD (Microsoft 계정 또는 Azure Active Directory) 계정과 연결 된 유효한 Azure 구독이 필요 합니다. 
    - Azure 계정이 없는 경우 무료 [계정을 만듭니다](https://azure.microsoft.com/free/) .
    - 구독 및 리소스의 Azure 개념에 대 한 소개는 [azure 개발자 가이드](https://docs.microsoft.com/azure/guides/developer/azure-developer-guide#manage-your-subscriptions)를 참조 하세요.
-  [Python](https://www.python.org/downloads/)
- [Pip](https://pip.pypa.io/en/stable/installing/)

## <a name="create-custom-vision-resources-in-the-azure-portal"></a>Azure Portal에서 Custom Vision 리소스 만들기

Azure에서 Custom Vision Service를 사용 하려면 [Azure Portal](https://portal.azure.com/?microsoft_azure_marketplace_ItemHideKey=microsoft_azure_cognitiveservices_customvision#create/Microsoft.CognitiveServicesCustomVision)에 Custom Vision 학습 및 예측 리소스를 만들어야 합니다. 

여러 프로젝트를 단일 리소스에 연결할 수 있습니다. [가격 책정 및 제한](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/limits-and-quotas) 에 대 한 자세한 정보를 확인할 수 있습니다. Custom Vision Service를 무료로 사용 하려면 Azure Portal에서 F0 계층을 선택할 수 있습니다. 

> [!NOTE]
> Custom Vision 프로젝트를 Azure 리소스로 이동 하면 해당 Azure 리소스의 기본 [사용 권한이]( https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal) 상속 됩니다. 조직의 다른 사용자가 프로젝트가 속한 Azure 리소스의 소유자 인 경우 [Custom Vision 웹 사이트](https://customvision.ai)에서 프로젝트에 액세스할 수 있습니다. 마찬가지로 리소스를 삭제 하면 프로젝트가 삭제 됩니다.  

## <a name="find-your-limited-trial-project-information"></a>제한 된 평가판 프로젝트 정보 찾기

프로젝트를 이동 하려면 마이그레이션하려는 프로젝트에 대 한 _프로젝트 ID_ 및 _학습 키_ 가 필요 합니다. 이 정보가 없는 경우 [https://www.customvision.ai/projects](https://www.customvision.ai/projects) 를 방문 하 여 각 프로젝트에 대 한 ID 및 키를 가져옵니다. 

## <a name="use-the-python-sample-code-to-copy-your-project-to-azure"></a>Python 샘플 코드를 사용 하 여 Azure에 프로젝트 복사

"원본" 자료로 제한 된 평가판 키와 프로젝트 ID를 사용 하 고 "대상"으로 만든 새 Azure 리소스의 키를 사용 하 여 [샘플 코드 지침](https://github.com/Azure-Samples/custom-vision-move-project)을 따릅니다.

기본적으로 제한 된 평가판 프로젝트는 모두 미국 중부 중부 Azure 지역에서 호스팅됩니다.

## <a name="next-steps"></a>다음 단계

이제 프로젝트가 Azure 리소스로 이동 되었습니다. 사용자가 작성 한 모든 응용 프로그램에서 학습 및 예측 키를 업데이트 해야 합니다.

[Custom Vision 웹 사이트](https://customvision.ai)에서 프로젝트를 보려면 Azure Portal에 로그인 하는 데 사용한 것과 동일한 계정으로 로그인 합니다. 프로젝트가 표시 되지 않는 경우 [Custom Vision 웹 사이트](https://customvision.ai) 의 동일한 디렉터리에 리소스가 Azure Portal 있는 디렉터리와 같은지 확인 하십시오. Azure Portal 및 CustomVision.ai 모두에서 화면의 오른쪽 위 모퉁이에 있는 드롭다운 사용자 메뉴에서 디렉터리를 선택할 수 있습니다.