---
title: Azure Lab Services에서 공유 이미지 갤러리를 사용 하 여 | Microsoft Docs
description: 사용자를 다른 이미지를 공유할 수 있으며 다른 사용자는 랩에서 VM 템플릿을 만들려면 이미지를 사용할 수 있도록 공유 이미지 갤러리를 사용 하는 랩 계정을 구성 하는 방법에 알아봅니다.
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
ms.openlocfilehash: 8d8b6fffe197d4180b091518dcd1615d0e0b9d19
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "65412845"
---
# <a name="use-a-shared-image-gallery-in-azure-lab-services"></a>Azure Lab Services에서 공유 이미지 갤러리를 사용 합니다.
이 문서에서는 교사/랩 관리자 사람이 다시 사용할 수에 대 한 템플릿 가상 머신 이미지를 저장할 수 하는 방법을 보여 줍니다. 이러한 이미지를 Azure에 저장 됩니다 [공유 이미지 갤러리](../../virtual-machines/windows/shared-image-galleries.md)합니다. 첫 번째 단계로, 랩 관리자는 기존 공유 이미지 갤러리를 랩 계정에 연결합니다. 공유 이미지 갤러리 연결 되 면 랩 계정에 만든 랩 공유 이미지 갤러리에 이미지를 저장할 수 있습니다. 다른 교사는 해당 클래스에 대 한 템플릿을 만들려면 공유 이미지 갤러리에서이 이미지를 선택할 수 있습니다. 

## <a name="prerequisites"></a>필수 조건
- 공유 이미지 갤러리를 사용 하 여 만들기 [Azure PowerShell](../../virtual-machines/windows/shared-images.md) 하거나 [Azure CLI](../../virtual-machines/linux/shared-images.md)합니다.
- 공유 이미지 갤러리의 랩 계정에 연결 합니다. 단계별 지침은 [이미지 갤러리를 공유 하는 연결 또는 분리 하는 방법](how-to-attach-detach-shared-image-gallery.md)합니다.


## <a name="save-an-image-to-the-shared-image-gallery"></a>공유 이미지 갤러리에 이미지 저장
공유 이미지 갤러리를 연결한 후 교사 저장 하거나 다른 교사에서 다시 사용할 수 있도록 공유 이미지 갤러리에 이미지를 업로드할 수 있습니다. 공유 이미지 갤러리에 이미지 업로드에 대 한 지침은 [공유 이미지 갤러리 개요](../../virtual-machines/windows/shared-images.md)합니다. 

> [!NOTE]
> 현재, 클래스 룸 랩 사용자 인터페이스 (UI)는 랩 이미지를 공유 하는 이미지 갤러리에 저장을 지원 하지 않습니다. 

## <a name="use-an-image-from-the-shared-image-gallery"></a>공유 이미지 갤러리에서 이미지를 사용 합니다.
교사/교수 템플릿에 대 한 공유 이미지 갤러리에서 새 랩 만들기 중에 제공 되는 사용자 지정 이미지를 선택할 수 있습니다.

![갤러리에서 사용 하 여 가상 머신 이미지](../media/how-to-use-shared-image-gallery/use-shared-image.png)

## <a name="next-steps"></a>다음 단계
공유 이미지 갤러리에 대 한 자세한 내용은 참조 하세요. [공유 이미지 갤러리](../../virtual-machines/windows/shared-image-galleries.md)합니다.
