---
title: Azure Lab 서비스에서 공유 이미지 갤러리 사용 | 마이크로 소프트 문서
description: 사용자가 다른 사용자와 이미지를 공유할 수 있고 다른 사용자가 이미지를 사용하여 랩에서 템플릿 VM을 만들 수 있도록 공유 이미지 갤러리를 사용하도록 랩 계정을 구성하는 방법에 대해 알아봅니다.
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
ms.openlocfilehash: c611ecdb5a2534f7368e533e3e19e6e3f96de57f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78190452"
---
# <a name="use-a-shared-image-gallery-in-azure-lab-services"></a>Azure 랩 서비스에서 공유 이미지 갤러리 사용
이 문서에서는 교사/랩 관리자가 템플릿 가상 컴퓨터 이미지를 다른 사용자가 다시 사용할 수 있도록 저장하는 방법을 보여 주었습니다. 이러한 이미지는 Azure [공유 이미지 갤러리에](../../virtual-machines/windows/shared-image-galleries.md)저장됩니다. 첫 번째 단계로 랩 관리자는 기존 공유 이미지 갤러리를 랩 계정에 연결합니다. 공유 이미지 갤러리가 연결되면 랩 계정에 생성된 랩은 공유 이미지 갤러리에 이미지를 저장할 수 있습니다. 다른 교사는 공유 이미지 갤러리에서 이 이미지를 선택하여 수업에 대한 템플릿을 만들 수 있습니다. 

> [!NOTE]
> 현재 Azure Lab Services는 공유 이미지 갤러리에서 **일반화된** VM 이미지(특수이미지가 아님)만을 기반으로 템플릿 VM 생성을 지원합니다. 

## <a name="prerequisites"></a>사전 요구 사항
- [Azure PowerShell](../../virtual-machines/windows/shared-images.md) 또는 [Azure CLI를](../../virtual-machines/linux/shared-images.md)사용하여 공유 이미지 갤러리를 만듭니다.
- 공유 이미지 갤러리를 랩 계정에 연결했습니다. 단계별 지침은 공유 이미지 [갤러리를 연결하거나 분리하는 방법을](how-to-attach-detach-shared-image-gallery.md)참조하십시오.


## <a name="save-an-image-to-the-shared-image-gallery"></a>공유 이미지 갤러리에 이미지 저장
공유 이미지 갤러리를 연결한 후 랩 계정 관리자 또는 교사는 이미지를 공유 이미지 갤러리에 저장하여 다른 교사가 다시 사용할 수 있도록 할 수 있습니다. 

1. 랩의 **템플릿** 페이지에서 도구 모음에서 **공유 이미지 갤러리로 내보내기를** 선택합니다.

    ![이미지 저장 버튼](../media/how-to-use-shared-image-gallery/export-to-shared-image-gallery-button.png)
2. 공유 **이미지 갤러리로내보내기** 대화 상자에서 **이미지의 이름을**입력한 다음 **내보내기를**선택합니다. 

    ![공유 이미지 갤러리 대화 상자로 내보내기](../media/how-to-use-shared-image-gallery/export-to-shared-image-gallery-dialog.png)
3. **템플릿** 페이지에서 이 작업의 진행 률을 확인할 수 있습니다. 이 작업은 시간이 걸릴 수 있습니다. 

    ![진행 중인 내보내기](../media/how-to-use-shared-image-gallery/exporting-image-in-progress.png)
4. 내보내기 작업이 성공하면 다음 메시지가 표시됩니다.

    ![내보내기 완료](../media/how-to-use-shared-image-gallery/exporting-image-completed.png)

랩의 컨텍스트 외부의 공유 이미지 갤러리에 이미지를 업로드할 수도 있습니다. 자세한 내용은 [공유 이미지 갤러리 개요를](../../virtual-machines/windows/shared-images.md)참조하십시오. 

## <a name="use-an-image-from-the-shared-image-gallery"></a>공유 이미지 갤러리의 이미지 사용
교사/교수는 새 랩을 만드는 동안 템플릿에 대한 공유 이미지 갤러리에서 사용할 수 있는 사용자 지정 이미지를 선택할 수 있습니다.

![갤러리에서 가상 시스템 이미지 사용](../media/how-to-use-shared-image-gallery/use-shared-image.png)

## <a name="next-steps"></a>다음 단계
공유 이미지 갤러리에 대한 자세한 내용은 [공유 이미지 갤러리를](../../virtual-machines/windows/shared-image-galleries.md)참조하십시오.
