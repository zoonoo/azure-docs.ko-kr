---
title: Azure Lab Services |에서 공유 이미지 갤러리 사용 Microsoft Docs
description: 사용자가 다른 사용자와 이미지를 공유 하 고 다른 사용자가 해당 이미지를 사용 하 여 랩에서 템플릿 VM을 만들 수 있도록 공유 이미지 갤러리를 사용 하도록 랩 계정을 구성 하는 방법에 대해 알아봅니다.
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
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/29/2020
ms.locfileid: "78190452"
---
# <a name="use-a-shared-image-gallery-in-azure-lab-services"></a>Azure Lab Services에서 공유 이미지 갤러리 사용
이 문서에서는 교사/랩 관리자가 템플릿 가상 머신 이미지를 다른 사용자가 다시 사용할 수 있도록 저장 하는 방법을 보여 줍니다. 이러한 이미지는 Azure [공유 이미지 갤러리](../../virtual-machines/windows/shared-image-galleries.md)에 저장 됩니다. 첫 번째 단계로 랩 관리자는 기존 공유 이미지 갤러리를 랩 계정에 연결 합니다. 공유 이미지 갤러리가 연결 되 면 랩 계정에서 생성 된 랩에서 공유 이미지 갤러리에 이미지를 저장할 수 있습니다. 다른 교사는 공유 이미지 갤러리에서이 이미지를 선택 하 여 해당 클래스에 대 한 템플릿을 만들 수 있습니다. 

> [!NOTE]
> 현재 Azure Lab Services에서는 공유 이미지 갤러리에서 **일반화** 된 VM 이미지 (특수 이미지 아님)만 기반으로 하는 템플릿 vm 만들기를 지원 합니다. 

## <a name="prerequisites"></a>필수 조건
- [Azure PowerShell](../../virtual-machines/windows/shared-images.md) 또는 [Azure CLI](../../virtual-machines/linux/shared-images.md)를 사용 하 여 공유 이미지 갤러리를 만듭니다.
- 공유 이미지 갤러리를 랩 계정에 연결 했습니다. 단계별 지침은 [공유 이미지 갤러리를 연결 하거나 분리 하는 방법](how-to-attach-detach-shared-image-gallery.md)을 참조 하세요.


## <a name="save-an-image-to-the-shared-image-gallery"></a>공유 이미지 갤러리에 이미지 저장
공유 이미지 갤러리가 연결 된 후에는 랩 계정 관리자 또는 교사가 다른 교사가 다시 사용할 수 있도록 공유 이미지 갤러리에 이미지를 저장할 수 있습니다. 

1. 랩에 대 한 **템플릿** 페이지의 도구 모음에서 **공유 이미지 갤러리로 내보내기를** 선택 합니다.

    ![이미지 저장 단추](../media/how-to-use-shared-image-gallery/export-to-shared-image-gallery-button.png)
2. **공유 이미지 갤러리에 내보내기** 대화 상자에서 **이미지의 이름을**입력 한 다음 **내보내기**를 선택 합니다. 

    ![공유 이미지 갤러리로 내보내기 대화 상자](../media/how-to-use-shared-image-gallery/export-to-shared-image-gallery-dialog.png)
3. **템플릿** 페이지에서이 작업의 진행률을 볼 수 있습니다. 이 작업은 시간이 오래 걸릴 수 있습니다. 

    ![내보내기 진행 중](../media/how-to-use-shared-image-gallery/exporting-image-in-progress.png)
4. 내보내기 작업에 성공 하면 다음 메시지가 표시 됩니다.

    ![내보내기 완료](../media/how-to-use-shared-image-gallery/exporting-image-completed.png)

랩 컨텍스트 외부에서 공유 이미지 갤러리에 이미지를 업로드할 수도 있습니다. 자세한 내용은 [공유 이미지 갤러리 개요](../../virtual-machines/windows/shared-images.md)를 참조 하세요. 

## <a name="use-an-image-from-the-shared-image-gallery"></a>공유 이미지 갤러리의 이미지 사용
교사/교수는 새 랩을 만들 때 템플릿의 공유 이미지 갤러리에서 사용할 수 있는 사용자 지정 이미지를 선택할 수 있습니다.

![갤러리에서 가상 컴퓨터 이미지 사용](../media/how-to-use-shared-image-gallery/use-shared-image.png)

## <a name="next-steps"></a>다음 단계
공유 이미지 갤러리에 대 한 자세한 내용은 [공유 이미지 갤러리](../../virtual-machines/windows/shared-image-galleries.md)를 참조 하세요.
