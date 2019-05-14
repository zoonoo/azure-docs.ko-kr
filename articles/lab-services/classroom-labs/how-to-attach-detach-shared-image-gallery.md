---
title: 연결 또는 분리 Azure Lab Services에서 공유 이미지 갤러리 | Microsoft Docs
description: Azure Lab Services의 랩에는 공유 이미지 갤러리를 연결 하는 방법에 알아봅니다.
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
ms.date: 05/07/2019
ms.author: spelluru
ms.openlocfilehash: de4e9fb4b15f4c346926fe46f23255c668204c2e
ms.sourcegitcommit: 300cd05584101affac1060c2863200f1ebda76b7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/08/2019
ms.locfileid: "65413883"
---
# <a name="attach-or-detach-a-shared-image-gallery-in-azure-lab-services"></a>연결 또는 Azure Lab Services에서 공유 이미지 갤러리를 분리 합니다.
교사/랩 관리자는 Azure에서 템플릿 VM 이미지를 저장할 수 [공유 이미지 갤러리](../../virtual-machines/windows/shared-image-galleries.md) 사람이 다시 사용할 수 있도록 합니다. 첫 번째 단계로, 랩 관리자는 기존 공유 이미지 갤러리를 랩 계정에 연결합니다. 공유 이미지 갤러리 연결 되 면 랩 계정에 만든 랩 공유 이미지 갤러리에 이미지를 저장할 수 있습니다. 다른 교사는 해당 클래스에 대 한 템플릿을 만들려면 공유 이미지 갤러리에서이 이미지를 선택할 수 있습니다. 

이 문서에서는 연결 또는 랩 계정에 공유 이미지 갤러리를 분리 하는 방법을 보여 줍니다. 

## <a name="configure-at-the-time-of-lab-account-creation"></a>랩 계정 만들기의 시 구성
랩 계정을 만들려는 경우에 랩 계정에 공유 이미지 갤러리를 연결할 수 있습니다. 드롭다운 목록에서 기존 공유 이미지 갤러리를 선택 하거나 새로 만들 수 있습니다. 를 만들고 공유 이미지 갤러리 랩 계정에 연결 하려면 선택 **새로 만들기**갤러리에 대 한 이름을 입력 하 고 입력 **확인**합니다. 

![랩 계정 생성 시 공유 이미지 갤러리 구성](../media/how-to-use-shared-image-gallery/new-lab-account.png)

## <a name="configure-after-the-lab-account-is-created"></a>랩 계정을 만든 후 구성
랩 계정을 만든 후에 다음 작업을 수행할 수 있습니다.

- 만들기 및 공유 이미지 갤러리를 연결 합니다.
- 공유 이미지 갤러리 랩 계정에 연결
- 랩 계정에서 공유 이미지 갤러리를 분리 합니다.

## <a name="create-and-attach-a-shared-image-gallery"></a>만들기 및 공유 이미지 갤러리를 연결 합니다.
1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. 왼쪽 메뉴에서 **모든 서비스**를 선택합니다. **DEVOPS** 섹션에서 **랩 서비스**를 선택합니다. **랩 서비스** 옆에 있는 별표(`*`)를 선택하면 왼쪽 메뉴의 **즐겨찾기** 섹션에 추가됩니다. 다음부터는 **즐겨찾기** 아래에서 **랩 서비스**를 선택합니다.

    ![모든 서비스 -> 랩 서비스](../media/tutorial-setup-lab-account/select-lab-accounts-service.png)
3. 랩 계정을 확인 하려면 선택 합니다 **랩 계정을** 페이지입니다. 
4. 선택 **공유 이미지 갤러리** 선택 고 왼쪽된 메뉴에서 **+ 만들기** 도구 모음에서 합니다.  

    ![공유 이미지 갤러리 단추 만들기](../media/how-to-use-shared-image-gallery/new-shared-image-gallery-button.png)
5. 에 **공유 이미지 갤러리 만들기** 창 입력을 **이름** 갤러리에 대 한 enter **확인**합니다. 

    ![공유 이미지 갤러리 창을 만듭니다.](../media/how-to-use-shared-image-gallery/create-shared-image-gallery-window.png)

    Azure Lab Services 공유 이미지 갤러리를 만들고 랩 계정에 연결 합니다. 모든 랩이 랩 계정에서 만든 연결 된 공유 이미지 갤러리에 액세스할 수 있으며 

    ![연결 된 이미지 갤러리](../media/how-to-use-shared-image-gallery/image-gallery-in-list.png)

    아래쪽 창에서 공유 이미지 갤러리에서 이미지 표시 됩니다. 이 새 갤러리에는 없는 이미지가 있습니다. 갤러리에 이미지를 업로드할 때이 페이지에서 확인할 수 있습니다.     

    연결 된 공유 이미지 갤러리의 모든 이미지는 기본적으로 활성화 됩니다. 목록에서 선택 하 고 사용 하 여 선택한 이미지를 사용 하지 않도록 설정 하거나 설정할 수 있습니다 합니다 **선택한 이미지를 사용 하도록 설정** 하거나 **선택한 이미지를 사용 하지 않도록 설정** 단추입니다.

## <a name="attach-an-existing-shared-image-gallery"></a>기존 공유 이미지 갤러리를 연결 합니다.
다음 절차는 랩 계정에 기존 공유 이미지 갤러리를 연결 하는 방법을 보여 줍니다. 

1. 에 **랩 계정을** 페이지에서 **공유 이미지 갤러리** 선택 고 왼쪽된 메뉴에서 **연결** 도구 모음에서 합니다. 

    ![공유 갤러리 이미지-단추를 추가 합니다.](../media/how-to-use-shared-image-gallery/sig-attach-button.png)
5. 에 **기존 공유 이미지 갤러리를 첨부할** 페이지, 공유 이미지 갤러리를 선택 하 고, 선택 **확인**합니다.

    ![기존 갤러리를 선택 합니다.](../media/how-to-use-shared-image-gallery/select-image-gallery.png)
6. 다음과 같은 화면이 표시 됩니다. 

    ![목록에 내 갤러리](../media/how-to-use-shared-image-gallery/my-gallery-in-list.png)
    
    이 예제의 경우 이미지가 없습니다 공유 이미지 갤러리에서 아직

    Azure Lab Services id는 랩에 연결 된 공유 이미지 갤러리에 contributor로 추가 됩니다. 교사 수 있도록 공유 이미지 갤러리에 이미지를 IT 관리자에 가상 머신을 저장 하는 /입니다. 모든 랩이 랩 계정에서 만든 연결 된 공유 이미지 갤러리에 액세스할 수 있으며 

    연결 된 공유 이미지 갤러리의 모든 이미지는 기본적으로 활성화 됩니다. 목록에서 선택 하 고 사용 하 여 선택한 이미지를 사용 하지 않도록 설정 하거나 설정할 수 있습니다 합니다 **선택한 이미지를 사용 하도록 설정** 하거나 **선택한 이미지를 사용 하지 않도록 설정** 단추입니다. 

## <a name="save-an-image-to-the-shared-image-gallery"></a>공유 이미지 갤러리에 이미지 저장
공유 이미지 갤러리를 연결한 후 랩 계정 관리자 또는 교사 수 저장 하거나 다른 교사에서 다시 사용할 수 있도록 공유 이미지 갤러리에 이미지를 업로드 합니다. 공유 이미지 갤러리에 이미지 업로드에 대 한 지침은 [공유 이미지 갤러리 개요](../../virtual-machines/windows/shared-images.md)합니다. 

> [!NOTE]
> 현재, 클래스 룸 랩 사용자 인터페이스 (UI)는 랩 이미지를 공유 하는 이미지 갤러리에 저장을 지원 하지 않습니다. 

## <a name="detach-a-shared-image-gallery"></a>공유 이미지 갤러리를 분리 합니다.
랩에 하나의 공유 이미지 갤러리를 연결할 수 있습니다. 다른 공유 이미지 갤러리를 연결 하려는 경우 새 연결 하기 전에 현재의 분리 합니다. 랩에서 공유 이미지 갤러리를 분리 하려면 선택 **분리** 도구 모음에서 분리 작업을 확인 합니다. 

![랩 계정에서 공유 이미지 갤러리를 분리 합니다.](../media/how-to-use-shared-image-gallery/detach.png)

## <a name="next-steps"></a>다음 단계
공유 이미지 갤러리에 랩 이미지를 저장 하거나 공유 이미지 갤러리에서 이미지를 사용 하 여 VM을 만드는 방법에 대 한 자세한 참조 [공유 이미지 갤러리를 사용 하는 방법을](how-to-use-shared-image-gallery.md)합니다.

에 대 한 자세한 정보는 일반적 이미지 갤러리를 공유, 참조 [공유 이미지 갤러리](../../virtual-machines/windows/shared-image-galleries.md)합니다.
