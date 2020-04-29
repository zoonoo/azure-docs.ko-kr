---
title: Azure Lab Services에서 랩에 대 한 마켓플레이스 이미지 지정
description: 이 문서에서는 랩 작성자가 Azure Lab Services의 랩 계정에서 랩을 만드는 데 사용할 수 있는 Marketplace 이미지를 지정 하는 방법을 보여 줍니다.
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
ms.date: 04/10/2020
ms.author: spelluru
ms.openlocfilehash: a64dee6da521764a38fc60bee06545f6a561c297
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81257704"
---
# <a name="specify-marketplace-images-available-to-lab-creators"></a>랩 작성자에 사용할 수 있는 Marketplace 이미지 지정
랩 계정 소유자는 랩 작성자가 랩 계정에서 랩을 만들 때 사용할 수 있는 Marketplace 이미지를 지정할 수 있습니다. 

## <a name="select-images-available-for-labs"></a>랩에 사용할 수 있는 이미지 선택
왼쪽 메뉴에서 **Marketplace 이미지**를 선택합니다. 기본적으로 이미지의 전체 목록이 표시됩니다(사용 및 사용 안 함으로 설정된 이미지 모두 포함). 맨 위에 있는 드롭다운 목록에서 **사용 하도록 설정 된**/옵션만 사용 옵션을 선택 하 여 사용/사용**안 함 이미지만** 표시 되도록 목록을 필터링 할 수 있습니다. 
    
![Marketplace 이미지 페이지](../media/tutorial-setup-lab-account/marketplace-images-page.png)

다음 조건을 만족하는 Marketplace 이미지만 목록에 표시됩니다.
    
- 단일 VM을 만듭니다.
- Azure Resource Manager를 사용하여 VM을 프로비전합니다.
- 추가 라이선스 플랜을 구입하지 않아도 됩니다.

## <a name="disable-images-for-a-lab"></a>랩에 대 한 이미지 사용 안 함 
랩에 대 한 단일 이미지를 사용 하지 않도록 설정 **하려면 ...을 선택 합니다. (줄임표)** 마지막 열에서 **이미지 사용 안 함**을 선택 합니다. 

![하나의 이미지를 사용하지 않도록 설정](../media/tutorial-setup-lab-account/disable-one-image.png) 

또는 이미지 이름 앞에 있는 확인란을 선택 하 고 도구 모음에서 **선택한 이미지 사용 안 함** 을 선택 합니다. 

동시에 여러 이미지를 사용 하지 않도록 설정 하려면 이미지 이름 앞의 확인란을 선택 하 고 도구 모음에서 **선택한 이미지 사용 안 함** 을 선택 합니다. 

![여러 이미지를 사용하지 않도록 설정](../media/tutorial-setup-lab-account/disable-multiple-images.png) 


## <a name="enable-images-for-a-lab"></a>랩에 대해 이미지 사용
비활성화 된 이미지를 사용 하도록 설정 **하려면 ...를 선택 합니다. (줄임표)** 마지막 열에서 **이미지 사용**을 선택 합니다. 또는 이미지 이름 앞에 있는 확인란을 선택 하 고 도구 모음에서 **선택한 이미지 사용** 을 선택 합니다. 

동시에 여러 이미지를 사용 하지 않도록 설정 하려면 이미지 이름 앞에 있는 확인란을 선택 하 고 도구 모음에서 **선택한 이미지 사용** 을 선택 합니다. 

## <a name="enable-images-at-the-time-of-lab-creation"></a>랩 생성 시 이미지 사용
랩을 만들 때 더 많은 이미지를 사용 하도록 설정할 수 있습니다. 

1. **랩 계정 소유자** 자격 증명을 사용 하 여 [Azure Lab Services 웹 사이트](https://labs.azure.com) 에 로그인 합니다.
2. 기본 가상 머신 이미지 또는 아래쪽 화살표를 선택 합니다. 
3. **추가 이미지 옵션 사용**을 선택 합니다. 

    ![추가 이미지 옵션 사용](../media/specify-marketplace-images/enable-more-images-menu.png)
4. 이전 섹션의 지침에 따라 선택한 이미지를 사용 하도록 설정 합니다. 
5. 이전 단계에서 선택한 이미지를 보려면 **새 랩** 창을 닫은 후 다시 열어야 할 수도 있습니다. 



## <a name="next-steps"></a>다음 단계
다음 문서를 참조하세요.

- [랩 소유자 권한으로 랩 만들기 및 관리](how-to-manage-classroom-labs.md)
- [랩 소유자 권한으로 템플릿 설정 및 게시](how-to-create-manage-template.md)
- [랩 소유자 권한으로 랩 사용 구성 및 제어](how-to-configure-student-usage.md)
- [랩 사용자 권한으로 클래스룸 랩 액세스](how-to-use-classroom-lab.md)
