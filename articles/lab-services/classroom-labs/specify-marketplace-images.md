---
title: Azure 랩 서비스에서 랩에 대한 마켓플레이스 이미지 지정
description: 이 문서에서는 랩 작성자가 Azure Lab Services의 랩 계정에서 랩을 만드는 데 사용할 수 있는 마켓플레이스 이미지를 지정하는 방법을 보여 주며 이 문서에서는
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/14/2020
ms.author: spelluru
ms.openlocfilehash: ad56041f853d030e3a286610fe4872bffecaee12
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77444668"
---
# <a name="specify-marketplace-images-available-to-lab-creators"></a>랩 작성자에 사용할 수 있는 Marketplace 이미지 지정
랩 계정 소유자는 랩 작성자가 랩 계정에서 랩을 만들 때 사용할 수 있는 Marketplace 이미지를 지정할 수 있습니다. 

## <a name="select-images-available-for-labs"></a>랩에 사용할 수 있는 이미지 선택
왼쪽 메뉴에서 **Marketplace 이미지**를 선택합니다. 기본적으로 이미지의 전체 목록이 표시됩니다(사용 및 사용 안 함으로 설정된 이미지 모두 포함). 상단의 드롭다운 목록에서 **Enabled only**/**사용 만 사용 만 비활성화 된 옵션을** 선택하여 목록을 필터링하여 활성화 / 비활성화 된 이미지만 볼 수 있습니다. 
    
![Marketplace 이미지 페이지](../media/tutorial-setup-lab-account/marketplace-images-page.png)

다음 조건을 만족하는 Marketplace 이미지만 목록에 표시됩니다.
    
- 단일 VM을 만듭니다.
- Azure Resource Manager를 사용하여 VM을 프로비전합니다.
- 추가 라이선스 플랜을 구입하지 않아도 됩니다.

## <a name="disable-images-for-a-lab"></a>랩용 이미지 비활성화 
랩에 대한 단일 이미지를 비활성화하려면 **다음을 선택합니다. (타원)** 마지막 열에서 이미지 **비활성화를**선택합니다. 

![하나의 이미지를 사용하지 않도록 설정](../media/tutorial-setup-lab-account/disable-one-image.png) 

또는 이미지 이름 앞에 있는 확인란을 선택하고 도구 모음에서 **선택한 이미지 비활성화를** 선택합니다. 

동시에 여러 이미지를 사용하지 않도록 설정하려면 이미지 이름 앞에 있는 확인란을 선택하고 도구 모음에서 **선택한 이미지 비활성화를** 선택합니다. 

![여러 이미지를 사용하지 않도록 설정](../media/tutorial-setup-lab-account/disable-multiple-images.png) 


## <a name="enable-images-for-a-lab"></a>랩용 이미지 사용
비활성화된 이미지를 **사용하려면... (타원)** 마지막 열에서 이미지 **활성화를**선택합니다. 또는 이미지 이름 앞에 있는 확인란을 선택하고 도구 모음에서 **선택한 이미지 활성화를** 선택합니다. 

여러 이미지를 동시에 사용하지 않도록 설정하려면 이미지 이름 앞에 있는 확인란을 선택하고 도구 모음에서 **선택한 이미지 활성화를** 선택합니다. 

## <a name="next-steps"></a>다음 단계
다음 문서를 참조하세요.

- [랩 소유자 권한으로 랩 만들기 및 관리](how-to-manage-classroom-labs.md)
- [랩 소유자 권한으로 템플릿 설정 및 게시](how-to-create-manage-template.md)
- [랩 소유자 권한으로 랩 사용 구성 및 제어](how-to-configure-student-usage.md)
- [랩 사용자 권한으로 클래스룸 랩 액세스](how-to-use-classroom-lab.md)
