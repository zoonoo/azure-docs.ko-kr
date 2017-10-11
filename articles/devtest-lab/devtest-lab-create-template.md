---
title: "VHD 파일에서 Azure DevTest Labs 사용자 지정 이미지 만들기 | Microsoft Docs"
description: "Azure Portal을 사용하여 VHD 파일에서 Azure DevTest Labs에 사용자 지정 이미지를 만드는 방법에 대해 알아봅니다."
services: devtest-lab,virtual-machines
documentationcenter: na
author: tomarcher
manager: douge
editor: 
ms.assetid: b795bc61-7c28-40e6-82fc-96d629ee0568
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/10/2017
ms.author: tarcher
ms.openlocfilehash: 9983ea9b847f44ed18a6169a4bdb224b63626a64
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/11/2017
---
# <a name="create-a-custom-image-from-a-vhd-file"></a>VHD 파일에서 사용자 지정 이미지 만들기

[!INCLUDE [devtest-lab-create-custom-image-from-vhd-selector](../../includes/devtest-lab-create-custom-image-from-vhd-selector.md)]

[!INCLUDE [devtest-lab-custom-image-definition](../../includes/devtest-lab-custom-image-definition.md)]

[!INCLUDE [devtest-lab-upload-vhd-options](../../includes/devtest-lab-upload-vhd-options.md)]

## <a name="step-by-step-instructions"></a>단계별 지침

다음 단계는 Azure Portal을 사용하여 VHD 파일에서 사용자 지정 이미지를 만드는 과정을 안내합니다.

1. [Azure 포털](http://go.microsoft.com/fwlink/p/?LinkID=525040)에 로그인합니다.

1. **추가 서비스**를 선택한 후 목록에서 **DevTest Labs**을 선택합니다.

1. 랩 목록에서 원하는 랩을 탭합니다.  

1. 랩의 블레이드에서 **구성**을 선택합니다. 

1. 랩의 **구성** 블레이드에서 **사용자 지정 이미지(VHD)**를 선택합니다.

1. **사용자 지정 이미지** 블레이드에서 **+추가**를 선택합니다.

    ![사용자 지정 이미지 추가](./media/devtest-lab-create-template/add-custom-image.png)

1. 사용자 지정 이미지의 이름을 입력합니다. 이 이름은 VM을 만들 때 기본 이미지 목록에 표시됩니다.

1. 사용자 지정 이미지에 대한 설명을 입력합니다. 이 설명은 VM을 만들 때 기본 이미지 목록에 표시됩니다.

1. **VHD**를 선택합니다.

1. **VHD** 블레이드에서 원하는 VHD 파일을 선택합니다.

1. **확인**을 선택하여 **VHD** 블레이드를 닫습니다.

1. **OS 구성**을 선택합니다.

1. **OS 구성** 탭에서 **Windows** 또는 **Linux**를 선택합니다.

1. **Windows** 가 선택되면 확인란을 통해 컴퓨터에서 *Sysprep* 이 실행되었는지를 지정합니다. 

1. **확인**을 선택하여 **OS 구성** 블레이드를 닫습니다.

1. **확인** 을 선택하여 사용자 지정 이미지를 만듭니다.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="related-blog-posts"></a>관련 블로그 게시물

- [사용자 지정 이미지 또는 수식?](https://blogs.msdn.microsoft.com/devtestlab/2016/04/06/custom-images-or-formulas/)
- [Azure DevTest Labs 간의 사용자 지정 이미지 복사](http://www.visualstudiogeeks.com/blog/DevOps/How-To-Move-CustomImages-VHD-Between-AzureDevTestLabs#copying-custom-images-between-azure-devtest-labs)

##<a name="next-steps"></a>다음 단계

- [랩에 VM 추가](./devtest-lab-add-vm-with-artifacts.md)
