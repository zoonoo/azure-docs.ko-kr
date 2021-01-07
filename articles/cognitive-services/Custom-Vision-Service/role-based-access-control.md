---
title: Azure 역할 기반 액세스 제어-Custom Vision
titleSuffix: Azure Cognitive Services
description: 이 문서에서는 Custom Vision 프로젝트에 대 한 Azure 역할 기반 액세스 제어를 구성 하는 방법을 보여 줍니다.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 09/11/2020
ms.author: pafarley
ms.openlocfilehash: d265b6698a87c1c651dff1413cf48dd4984cd606
ms.sourcegitcommit: 9706bee6962f673f14c2dc9366fde59012549649
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/13/2020
ms.locfileid: "94616895"
---
# <a name="azure-role-based-access-control"></a>Azure 역할 기반 액세스 제어

Custom Vision은 Azure 리소스에 대한 개별 액세스를 관리하는 권한 부여 시스템인 Azure RBAC(Azure 역할 기반 액세스 제어)를 지원합니다. Azure RBAC를 사용 하 여 Custom Vision 프로젝트에 다른 팀 멤버에 게 다른 수준의 사용 권한을 할당 합니다. Azure RBAC에 대 한 자세한 내용은 [AZURE rbac 설명서](../../role-based-access-control/index.yml)를 참조 하세요.

## <a name="add-role-assignment-to-custom-vision-resource"></a>Custom Vision 리소스에 역할 할당 추가

Azure RBAC는 Custom Vision 리소스에 할당할 수 있습니다. Azure 리소스에 대 한 액세스 권한을 부여 하려면 역할 할당을 추가 합니다.
1. [Azure Portal](https://ms.portal.azure.com/)에서 **모든 서비스** 를 선택합니다. 
1. 그런 다음 **Cognitive Services** 를 선택 하 고 특정 Custom Vision 학습 리소스로 이동 합니다.
   > [!NOTE]
   > 또한 전체 리소스 그룹, 구독 또는 관리 그룹에 대 한 Azure RBAC를 설정할 수 있습니다. 원하는 범위 수준을 선택한 다음 원하는 항목으로 이동 하 여이 작업을 수행 합니다 (예: **리소스 그룹** 을 선택 하 고 원하는 리소스 그룹을 클릭 하 여 클릭).
1. 왼쪽 탐색 창에서 **액세스 제어 (IAM)** 를 선택 합니다.
1. **역할 할당** 탭을 선택 하 여이 범위에 대 한 역할 할당을 확인 합니다.
1. **추가**  ->  **역할 할당** 추가를 선택 합니다.
1. **역할** 드롭다운 목록에서 추가 하려는 역할을 선택 합니다.
1. **선택** 목록에서 사용자, 그룹, 서비스 주체 또는 관리 ID르 선택합니다. 목록에 보안 주체가 표시 되지 않으면 선택 상자를 입력 하 여 표시 이름, 전자 메일 주소 및 개체 식별자에 대 한 디렉터리를 검색할 수 있습니다.
1. **저장** 을 선택하여 역할을 할당합니다.

몇 분 이내에 선택한 범위에서 대상에 선택 된 역할이 할당 됩니다.

## <a name="custom-vision-role-types"></a>Custom Vision 역할 유형

다음 표를 사용 하 여 Custom Vision 리소스에 대 한 액세스 요구 사항을 확인 합니다.

|역할  |사용 권한  |
|---------|---------|
|`Cognitive Services Custom Vision Contributor`     | 프로젝트를 만들거나 편집 하거나 삭제 하는 기능을 포함 하 여 프로젝트에 대 한 모든 권한을 제공 합니다.        |
|`Cognitive Services Custom Vision Trainer`     | 프로젝트를 만들거나 삭제 하는 기능을 제외한 모든 권한 교육자는 프로젝트를 보고 편집 하며 모델을 학습, 게시, 게시 취소 또는 내보낼 수 있습니다.        |
|`Cognitive Services Custom Vision Labeler`     | 학습 이미지를 업로드, 편집 또는 삭제 하 고 태그를 만들거나 추가, 제거 또는 삭제할 수 있습니다. Labelers는 프로젝트를 볼 수 있지만 학습 이미지 및 태그 이외의 항목을 업데이트할 수는 없습니다.         |
|`Cognitive Services Custom Vision Deployment`     | 모델을 게시, 게시 취소 또는 내보낼 수 있습니다. 배포자는 프로젝트를 볼 수 있지만 프로젝트, 학습 이미지 또는 태그를 업데이트할 수는 없습니다.        |
|`Cognitive Services Custom Vision Reader`     | 프로젝트를 볼 수 있습니다. 판독기는 변경할 수 없습니다.        |