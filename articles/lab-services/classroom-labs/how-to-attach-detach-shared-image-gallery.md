---
title: Azure Lab Services |에서 공유 이미지 갤러리 연결 또는 분리 Microsoft Docs
description: 이 문서에서는 Azure Lab Services의 클래스 룸 랩에 공유 이미지 갤러리를 연결 하는 방법을 설명 합니다.
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
ms.date: 02/24/2020
ms.author: spelluru
ms.openlocfilehash: 00dbef7b4453ffcb54020340bde51f55827759a0
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/29/2020
ms.locfileid: "78190520"
---
# <a name="attach-or-detach-a-shared-image-gallery-in-azure-lab-services"></a>Azure Lab Services에서 공유 이미지 갤러리 연결 또는 분리
교사/랩 관리자는 다른 사용자가 다시 사용할 수 있도록 Azure [공유 이미지 갤러리](../../virtual-machines/windows/shared-image-galleries.md) 에 템플릿 VM 이미지를 저장할 수 있습니다. 첫 번째 단계로 랩 관리자는 기존 공유 이미지 갤러리를 랩 계정에 연결 합니다. 공유 이미지 갤러리가 연결 되 면 랩 계정에서 생성 된 랩에서 공유 이미지 갤러리에 이미지를 저장할 수 있습니다. 다른 교사는 공유 이미지 갤러리에서이 이미지를 선택 하 여 해당 클래스에 대 한 템플릿을 만들 수 있습니다. 

공유 이미지 갤러리에 이미지를 저장 하는 경우 Azure Lab Services는 저장 된 이미지를 동일한 [지리](https://azure.microsoft.com/global-infrastructure/geographies/)에서 사용할 수 있는 다른 지역에 복제 합니다. 이를 통해 동일한 지리에서 다른 지역에 생성 된 랩에서 이미지를 사용할 수 있습니다. 공유 이미지 갤러리에 이미지를 저장 하면 모든 복제 된 이미지에 대 한 비용을 포함 하는 추가 비용이 발생 합니다. 이 비용은 Azure Lab Services 사용 비용과는 별개입니다. 공유 이미지 갤러리 가격에 대 한 자세한 내용은 [공유 이미지 갤러리 – 청구]( https://docs.microsoft.com/azure/virtual-machines/windows/shared-image-galleries#billing)를 참조 하세요.

이 문서에서는 공유 이미지 갤러리를 랩 계정에 연결 하거나 분리 하는 방법을 보여 줍니다. 

> [!NOTE]
> 현재 Azure Lab Services에서는 공유 이미지 갤러리에서 **일반화** 된 VM 이미지 (특수 이미지 아님)만 기반으로 하는 템플릿 vm 만들기를 지원 합니다. 


## <a name="configure-at-the-time-of-lab-account-creation"></a>랩 계정 생성 시 구성
랩 계정을 만들 때 공유 이미지 갤러리를 랩 계정에 연결할 수 있습니다. 드롭다운 목록에서 기존 공유 이미지 갤러리를 선택 하거나 새로 만들 수 있습니다. 공유 이미지 갤러리를 만들어 랩 계정에 첨부 하려면 **새로 만들기**를 선택 하 고, 갤러리의 이름을 입력 하 고, **확인**을 입력 합니다. 

![랩 계정을 만들 때 공유 이미지 갤러리 구성](../media/how-to-use-shared-image-gallery/new-lab-account.png)

## <a name="configure-after-the-lab-account-is-created"></a>랩 계정이 만들어진 후 구성
랩 계정을 만든 후에는 다음 작업을 수행할 수 있습니다.

- 공유 이미지 갤러리 만들기 및 첨부
- 공유 이미지 갤러리를 랩 계정에 연결
- 랩 계정에서 공유 이미지 갤러리 분리

## <a name="create-and-attach-a-shared-image-gallery"></a>공유 이미지 갤러리 만들기 및 첨부
1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. 왼쪽 메뉴에서 **모든 서비스**를 선택합니다. **DEVOPS** 섹션에서 **랩 서비스**를 선택합니다. `*`랩 서비스 **옆에 있는 별표(** )를 선택하면 왼쪽 메뉴의 **즐겨찾기** 섹션에 추가됩니다. 다음부터는 **즐겨찾기** 아래에서 **랩 서비스**를 선택합니다.

    ![모든 서비스 -> 랩 서비스](../media/tutorial-setup-lab-account/select-lab-accounts-service.png)
3. 랩 계정을 선택 하 여 **랩 계정** 페이지를 표시 합니다. 
4. 왼쪽 메뉴에서 **공유 이미지 갤러리** 를 선택 하 고 도구 모음에서 **+ 만들기** 를 선택 합니다.  

    ![공유 이미지 갤러리 만들기 단추](../media/how-to-use-shared-image-gallery/new-shared-image-gallery-button.png)
5. **공유 이미지 갤러리 만들기** 창에서 갤러리의 **이름을** 입력 하 고 **확인**을 입력 합니다. 

    ![공유 이미지 갤러리 만들기 창](../media/how-to-use-shared-image-gallery/create-shared-image-gallery-window.png)

    Azure Lab Services 공유 이미지 갤러리를 만들고 랩 계정에 연결 합니다. 이 랩 계정에서 만든 모든 랩에서는 연결 된 공유 이미지 갤러리에 액세스할 수 있습니다. 

    ![연결 된 이미지 갤러리](../media/how-to-use-shared-image-gallery/image-gallery-in-list.png)

    아래쪽 창에 공유 이미지 갤러리의 이미지가 표시 됩니다. 이 새 갤러리에는 이미지가 없습니다. 갤러리에 이미지를 업로드 하는 경우이 페이지에 표시 됩니다.     

    연결 된 공유 이미지 갤러리의 모든 이미지는 기본적으로 사용 하도록 설정 되어 있습니다. 선택 된 이미지를 목록에서 선택 하 고 선택 된 이미지 **사용** 또는 **선택한 이미지 사용 안 함** 단추를 사용 하 여 선택한 이미지를 사용 하거나 사용 하지 않도록 설정할 수 있습니다.

## <a name="attach-an-existing-shared-image-gallery"></a>기존 공유 이미지 갤러리 연결
다음 절차에서는 기존 공유 이미지 갤러리를 랩 계정에 연결 하는 방법을 보여 줍니다. 

1. **랩 계정** 페이지의 왼쪽 메뉴에서 **공유 이미지 갤러리** 를 선택 하 고 도구 모음에서 **연결** 을 선택 합니다. 

    ![공유 이미지 갤러리-추가 단추](../media/how-to-use-shared-image-gallery/sig-attach-button.png)
5. **기존 공유 이미지 갤러리 연결** 페이지에서 공유 이미지 갤러리를 선택 하 고 **확인**을 선택 합니다.

    ![기존 갤러리를 선택 합니다.](../media/how-to-use-shared-image-gallery/select-image-gallery.png)
6. 다음 화면이 표시 됩니다. 

    ![목록의 내 갤러리](../media/how-to-use-shared-image-gallery/my-gallery-in-list.png)
    
    이 예제에서는 공유 이미지 갤러리에 아직 이미지가 없습니다.

    Azure Lab Services id는 랩에 연결 된 공유 이미지 갤러리에 참가자로 추가 됩니다. 이를 통해 교사/IT 관리자는 가상 머신 이미지를 공유 이미지 갤러리에 저장할 수 있습니다. 이 랩 계정에서 만든 모든 랩에서는 연결 된 공유 이미지 갤러리에 액세스할 수 있습니다. 

    연결 된 공유 이미지 갤러리의 모든 이미지는 기본적으로 사용 하도록 설정 되어 있습니다. 선택 된 이미지를 목록에서 선택 하 고 선택 된 이미지 **사용** 또는 **선택한 이미지 사용 안 함** 단추를 사용 하 여 선택한 이미지를 사용 하거나 사용 하지 않도록 설정할 수 있습니다. 

## <a name="detach-a-shared-image-gallery"></a>공유 이미지 갤러리 분리
공유 이미지 갤러리 하나만 랩에 연결할 수 있습니다. 다른 공유 이미지 갤러리를 연결 하려면 새 이미지 갤러리를 연결 하기 전에 현재 항목을 분리 합니다. 랩에서 공유 이미지 갤러리를 분리 하려면 도구 모음에서 **분리** 를 선택 하 고 분리 작업을 확인 합니다. 

![랩 계정에서 공유 이미지 갤러리 분리](../media/how-to-use-shared-image-gallery/detach.png)

## <a name="next-steps"></a>다음 단계
공유 이미지 갤러리에 랩 이미지를 저장 하거나 공유 이미지 갤러리의 이미지를 사용 하 여 VM을 만드는 방법에 대해 알아보려면 [공유 이미지 갤러리를 사용 하는 방법](how-to-use-shared-image-gallery.md)을 참조 하세요.

일반적으로 공유 이미지 갤러리에 대 한 자세한 내용은 [공유 이미지 갤러리](../../virtual-machines/windows/shared-image-galleries.md)를 참조 하세요.
