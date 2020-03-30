---
title: Azure Lab 서비스에서 공유 이미지 갤러리연결 또는 분리 | 마이크로 소프트 문서
description: 이 문서에서는 Azure Lab Services의 강의실 랩에 공유 이미지 갤러리를 연결하는 방법에 대해 설명합니다.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79284318"
---
# <a name="attach-or-detach-a-shared-image-gallery-in-azure-lab-services"></a>Azure Lab 서비스에서 공유 이미지 갤러리 연결 또는 분리
교사/랩 관리자는 다른 사용자가 다시 사용할 수 있도록 Azure [공유 이미지 갤러리에](../../virtual-machines/windows/shared-image-galleries.md) 템플릿 VM 이미지를 저장할 수 있습니다. 첫 번째 단계로 랩 관리자는 기존 공유 이미지 갤러리를 랩 계정에 연결합니다. 공유 이미지 갤러리가 연결되면 랩 계정에 생성된 랩은 공유 이미지 갤러리에 이미지를 저장할 수 있습니다. 다른 교사는 공유 이미지 갤러리에서 이 이미지를 선택하여 수업에 대한 템플릿을 만들 수 있습니다. 

이미지가 공유 이미지 갤러리에 저장되면 Azure Lab Services는 저장된 이미지를 동일한 [지역에서](https://azure.microsoft.com/global-infrastructure/geographies/)사용할 수 있는 다른 지역으로 복제합니다. 동일한 지역의 다른 지역에서 만든 랩에서 이미지를 사용할 수 있습니다. 이미지를 공유 이미지 갤러리에 저장하면 복제된 모든 이미지에 대한 비용이 포함되는 추가 비용이 발생합니다. 이 비용은 Azure 랩 서비스 사용 비용과 는 별개입니다. 공유 이미지 갤러리 가격에 대한 자세한 내용은 [공유 이미지 갤러리 - 청구]( https://docs.microsoft.com/azure/virtual-machines/windows/shared-image-galleries#billing)를 참조하십시오.

이 문서에서는 공유 이미지 갤러리를 랩 계정에 연결하거나 분리하는 방법을 보여 주었습니다. 

> [!NOTE]
> 현재 Azure Lab Services는 공유 이미지 갤러리에서 **일반화된** VM 이미지(특수이미지가 아님)만을 기반으로 템플릿 VM 생성을 지원합니다. 


## <a name="configure-at-the-time-of-lab-account-creation"></a>랩 계정 생성 시 구성
랩 계정을 만들 때 공유 이미지 갤러리를 랩 계정에 연결할 수 있습니다. 드롭다운 목록에서 기존 공유 이미지 갤러리를 선택하거나 새 이미지 갤러리를 만들 수 있습니다. 공유 이미지 갤러리를 만들고 랩 계정에 첨부하려면 **새 만들기를**선택하고 갤러리의 이름을 입력하고 **확인을**입력합니다. 

![랩 계정 생성 시 공유 이미지 갤러리 구성](../media/how-to-use-shared-image-gallery/new-lab-account.png)

## <a name="configure-after-the-lab-account-is-created"></a>랩 계정을 만든 후 구성
랩 계정을 만든 후 다음 작업을 수행할 수 있습니다.

- 공유 이미지 갤러리 만들기 및 첨부
- 랩 계정에 공유 이미지 갤러리 첨부
- 랩 계정에서 공유 이미지 갤러리 분리

## <a name="create-and-attach-a-shared-image-gallery"></a>공유 이미지 갤러리 만들기 및 첨부
1. [Azure 포털에](https://portal.azure.com)로그인합니다.
2. 왼쪽 메뉴에서 **모든 서비스**를 선택합니다. **DEVOPS** 섹션에서 **랩 서비스**를 선택합니다. **랩 서비스** 옆에 있는 별표(`*`)를 선택하면 왼쪽 메뉴의 **즐겨찾기** 섹션에 추가됩니다. 다음부터는 **즐겨찾기** 아래에서 **랩 서비스**를 선택합니다.

    ![모든 서비스 -> 랩 서비스](../media/tutorial-setup-lab-account/select-lab-accounts-service.png)
3. 랩 계정을 선택하여 **랩 계정** 페이지를 확인합니다. 
4. 왼쪽 메뉴에서 **공유 이미지 갤러리를** 선택하고 도구 모음에서 **+ 만들기를** 선택합니다.  

    ![공유 이미지 갤러리 버튼 만들기](../media/how-to-use-shared-image-gallery/new-shared-image-gallery-button.png)
5. 공유 **이미지 갤러리 만들기** 창에서 갤러리의 **이름을** 입력하고 **확인을**입력합니다. 

    ![공유 이미지 갤러리 창 만들기](../media/how-to-use-shared-image-gallery/create-shared-image-gallery-window.png)

    Azure Lab Services는 공유 이미지 갤러리를 만들고 랩 계정에 연결합니다. 이 랩 계정에서 만든 모든 랩은 첨부된 공유 이미지 갤러리에 액세스할 수 있습니다. 

    ![첨부된 이미지 갤러리](../media/how-to-use-shared-image-gallery/image-gallery-in-list.png)

    아래쪽 창에는 공유 이미지 갤러리에 이미지가 표시됩니다. 이 새로운 갤러리에는 이미지가 없습니다. 갤러리에 이미지를 업로드하면 이 페이지에 이미지가 표시됩니다.     

    첨부된 공유 이미지 갤러리의 모든 이미지는 기본적으로 활성화됩니다. 목록에서 선택한 이미지를 선택하고 선택한 이미지 사용 또는 선택한 이미지 **사용 안 함** 버튼을 사용하여 **선택한 이미지를** 사용하거나 비활성화할 수 있습니다.

## <a name="attach-an-existing-shared-image-gallery"></a>기존 공유 이미지 갤러리 첨부
다음 절차에서는 기존 공유 이미지 갤러리를 랩 계정에 연결하는 방법을 보여 주며, 이 절차에서는 기존 공유 이미지 갤러리를 첨부하는 방법을 보여 주시면 됩니다. 

1. 랩 **계정** 페이지에서 왼쪽 메뉴에서 **공유 이미지 갤러리를** 선택하고 도구 모음에서 **첨부를** 선택합니다. 

    ![공유 이미지 갤러리 - 버튼 추가](../media/how-to-use-shared-image-gallery/sig-attach-button.png)
5. 기존 **공유 이미지 갤러리 첨부** 페이지에서 공유 이미지 갤러리를 선택하고 **확인을**선택합니다.

    ![기존 갤러리 선택](../media/how-to-use-shared-image-gallery/select-image-gallery.png)
6. 다음 화면이 표시됩니다. 

    ![목록의 내 갤러리](../media/how-to-use-shared-image-gallery/my-gallery-in-list.png)
    
    이 예제에서는 공유 이미지 갤러리에 아직 이미지가 없습니다.

    Azure Lab Services ID는 랩에 연결된 공유 이미지 갤러리에 기여자로 추가됩니다. 교사/IT 관리자가 가상 컴퓨터 이미지를 공유 이미지 갤러리에 저장할 수 있습니다. 이 랩 계정에서 만든 모든 랩은 첨부된 공유 이미지 갤러리에 액세스할 수 있습니다. 

    첨부된 공유 이미지 갤러리의 모든 이미지는 기본적으로 활성화됩니다. 목록에서 선택한 이미지를 선택하고 선택한 이미지 사용 또는 선택한 이미지 **사용 안 함** 버튼을 사용하여 **선택한 이미지를** 사용하거나 비활성화할 수 있습니다. 

## <a name="detach-a-shared-image-gallery"></a>공유 이미지 갤러리 분리
하나의 공유 이미지 갤러리만 랩에 연결할 수 있습니다. 다른 공유 이미지 갤러리를 첨부하려면 새 이미지 갤러리를 연결하기 전에 현재 갤러리를 분리합니다. 랩에서 공유 이미지 갤러리를 분리하려면 도구 모음에서 **분리를** 선택하고 분리 작업을 확인합니다. 

![랩 계정에서 공유 이미지 갤러리 분리](../media/how-to-use-shared-image-gallery/detach.png)

## <a name="next-steps"></a>다음 단계
랩 이미지를 공유 이미지 갤러리에 저장하거나 공유 이미지 갤러리의 이미지를 사용하여 VM을 만드는 방법에 대해 알아보려면 [공유 이미지 갤러리를 사용하는 방법을](how-to-use-shared-image-gallery.md)참조하세요.

일반적으로 공유 이미지 갤러리에 대한 자세한 내용은 [공유 이미지 갤러리를](../../virtual-machines/windows/shared-image-galleries.md)참조하십시오.
