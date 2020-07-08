---
title: Azure Lab Services에서 공유 이미지 갤러리 사용 | Microsoft Docs
description: 사용자가 다른 사용자와 이미지를 공유하고 다른 사용자가 해당 이미지를 사용하여 랩에서 템플릿 VM을 만들 수 있도록 공유 이미지 갤러리를 사용하도록 랩 계정을 구성하는 방법에 대해 알아봅니다.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 4dc001a5e4c95db608c38f0a7c00190fd1859998
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85445154"
---
# <a name="use-a-shared-image-gallery-in-azure-lab-services"></a>Azure Lab Services에서 공유 이미지 갤러리 사용
이 문서는 교육자/랩 관리자가 템플릿 가상 머신 이미지를 [공유 이미지 갤러리](https://docs.microsoft.com/azure/virtual-machines/windows/shared-image-galleries)에 저장하여 다른 사용자가 랩을 만들 수 있도록 하는 방법을 보여 줍니다. 

## <a name="scenarios"></a>시나리오
이 기능에서 지원하는 몇 가지 시나리오는 다음과 같습니다. 

- 랩 계정 관리자는 공유 이미지 갤러리를 랩 계정에 연결하고, 랩 컨텍스트 외부의 공유 이미지 갤러리에 이미지를 업로드합니다. 그런 다음, 랩 작성자는 공유 이미지 갤러리에서 해당 이미지를 사용하여 랩을 만들 수 있습니다. 
- 랩 계정 관리자는 공유 이미지 갤러리를 랩 계정에 연결합니다. 랩 작성자(교육자)는 랩의 사용자 지정된 이미지를 공유 이미지 갤러리에 저장합니다. 그런 다음, 다른 랩 작성자가 공유 이미지 갤러리에서 이 이미지를 선택하여 랩에 대한 템플릿을 만들 수 있습니다. 

    이미지가 공유 이미지 갤러리에 저장되는 경우 Azure Lab Services는 저장된 이미지를 동일한 [지리](https://azure.microsoft.com/global-infrastructure/geographies/)에서 사용할 수 있는 다른 지역에 복제합니다. 해당 이미지를 동일한 지리의 다른 지역에서 만든 랩에 사용할 수 있습니다. 공유 이미지 갤러리에 이미지를 저장하면 모든 복제된 이미지에 대한 비용을 포함하는 추가 비용이 발생합니다. 이 비용은 Azure Lab Services 사용 비용과는 별개입니다. Shared Image Gallery 가격 책정에 대한 자세한 내용은 [Shared Image Gallery – 청구]( https://docs.microsoft.com/azure/virtual-machines/windows/shared-image-galleries#billing)를 참조하세요.
    
## <a name="prerequisites"></a>사전 요구 사항
- [Azure PowerShell](../virtual-machines/windows/shared-images.md) 또는 [Azure CLI](../virtual-machines/linux/shared-images.md)를 사용하여 공유 이미지 갤러리를 만듭니다.
- 공유 이미지 갤러리를 랩 계정에 연결했습니다. 단계별 지침은 [공유 이미지 갤러리를 연결하거나 분리하는 방법](how-to-attach-detach-shared-image-gallery.md)을 참조하세요.


## <a name="save-an-image-to-the-shared-image-gallery"></a>공유 이미지 갤러리에 이미지 저장
공유 이미지 갤러리가 연결된 후에는 랩 계정 관리자 또는 교육자는 다른 교육자가 다시 사용할 수 있도록 공유 이미지 갤러리에 이미지를 저장할 수 있습니다. 

1. 랩에 대한 **템플릿** 페이지의 도구 모음에서 **공유 이미지 갤러리로 내보내기**를 선택합니다.

    ![이미지 저장 단추](./media/how-to-use-shared-image-gallery/export-to-shared-image-gallery-button.png)
2. **공유 이미지 갤러리로 내보내기** 대화 상자에서 **이미지에 대한 이름**을 입력한 다음, **내보내기**를 선택합니다. 

    ![공유 이미지 갤러리로 내보내기 대화 상자](./media/how-to-use-shared-image-gallery/export-to-shared-image-gallery-dialog.png)
3. **템플릿** 페이지에서 이 작업의 진행률을 볼 수 있습니다. 이 작업에는 시간이 오래 걸릴 수 있습니다. 

    ![내보내기 진행 중](./media/how-to-use-shared-image-gallery/exporting-image-in-progress.png)
4. 내보내기 작업에 성공하면 다음 메시지가 표시됩니다.

    ![내보내기 완료](./media/how-to-use-shared-image-gallery/exporting-image-completed.png)

    공유 이미지 갤러리에 이미지를 저장하면 다른 랩을 만들 때 갤러리에서 해당 이미지를 사용할 수 있습니다. 랩 컨텍스트 외부의 공유 이미지 갤러리에 이미지를 업로드할 수도 있습니다. 자세한 내용은 [공유 이미지 갤러리 개요](../virtual-machines/windows/shared-images.md)를 참조하세요. 

    > [!IMPORTANT]
    > Azure Lab Services [랩의 템플릿 이미지](how-to-use-shared-image-gallery.md#save-an-image-to-the-shared-image-gallery) 를 공유 이미지 갤러리에 저장 하면 이미지가 갤러리에 **특수 이미지**로 업로드 됩니다. [특수 이미지](https://docs.microsoft.com/azure/virtual-machines/windows/shared-image-galleries#generalized-and-specialized-images) 는 컴퓨터 관련 정보와 사용자 프로필을 유지 합니다. Azure Lab Services 외부의 갤러리에 일반화 된 이미지를 직접 직접 업로드할 수 있습니다.    

## <a name="use-an-image-from-the-shared-image-gallery"></a>공유 이미지 갤러리의 이미지 사용
교육자는 새 랩을 만들 때 템플릿의 공유 이미지 갤러리에서 사용할 수 있는 사용자 지정 이미지를 선택할 수 있습니다.

![갤러리에서 가상 머신 이미지 사용](./media/how-to-use-shared-image-gallery/use-shared-image.png)

> [!NOTE]
> Azure Lab Services에서 **일반화** 된 이미지와 **특수** 이미지를 기반으로 하는 템플릿 VM을 만들 수 있습니다. 


## <a name="next-steps"></a>다음 단계
공유 이미지 갤러리에 대한 자세한 내용은 [공유 이미지 갤러리](https://docs.microsoft.com/azure/virtual-machines/windows/shared-image-galleries)를 참조하세요.
