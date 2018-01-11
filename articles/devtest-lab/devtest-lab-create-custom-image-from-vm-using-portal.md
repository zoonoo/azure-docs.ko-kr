---
title: "VHD에서 Azure DevTest Labs 사용자 지정 이미지 만들기 | Microsoft Docs"
description: "Azure Portal을 사용하여 프로비저닝된 VM에서 Azure DevTest Labs에 사용자 지정 이미지를 만드는 방법에 대해 알아봅니다."
services: devtest-lab,virtual-machines
documentationcenter: na
author: craigcaseyMSFT
manager: douge
editor: 
ms.assetid: 
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/10/2017
ms.author: v-craic
ms.openlocfilehash: ce854024df5c6974873cf3574bf2de282946f6d0
ms.sourcegitcommit: 85012dbead7879f1f6c2965daa61302eb78bd366
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/02/2018
---
# <a name="create-a-custom-image-from-a-vm"></a>VM에서 사용자 지정 이미지 만들기

[!INCLUDE [devtest-lab-custom-image-definition](../../includes/devtest-lab-custom-image-definition.md)]

## <a name="step-by-step-instructions"></a>단계별 지침

프로비저닝된 VM에서 사용자 지정 이미지를 만든 다음, 사용자 지정 이미지를 사용하여 동일한 VM을 만들 수 있습니다. 다음 단계에서는 VM에서 사용자 지정 이미지를 만드는 방법을 보여 줍니다.

1. [Azure 포털](http://go.microsoft.com/fwlink/p/?LinkID=525040)에 로그인합니다.

1. **추가 서비스**를 선택한 후 목록에서 **DevTest Labs**을 선택합니다.

1. 랩 목록에서 원하는 랩을 탭합니다.  

1. 랩의 블레이드에서 **내 가상 머신**를 선택합니다.
 
1. **내 가상 머신** 블레이드에서 사용자 지정 이미지를 만들 VM을 선택합니다.

1. VM의 블레이드에서 **사용자 지정 이미지 만들기(VHD)**를 선택합니다.

    ![사용자 지정 이미지 만들기 메뉴 항목](./media/devtest-lab-create-template/create-custom-image.png)

1. **이미지 만들기** 블레이드에서 사용자 지정 이미지에 대한 이름 및 설명을 입력합니다. 이 정보는 VM을 만들 때 기본 목록에 표시됩니다.

    ![사용자 지정 이미지 만들기 블레이드](./media/devtest-lab-create-template/create-custom-image-blade.png)

1. sysprep이 VM에서 실행되었는지 여부를 선택합니다. sysprep이 VM에서 실행되지 않은 경우 이 사용자 지정 이미지에서 VM을 만들 때 sysprep을 실행할지를 지정합니다.

1. 완료했으면 **확인** 을 선택하여 사용자 지정 이미지를 만듭니다.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="related-blog-posts"></a>관련 블로그 게시물

- [사용자 지정 이미지 또는 수식?](https://blogs.msdn.microsoft.com/devtestlab/2016/04/06/custom-images-or-formulas/)
- [Azure DevTest Labs 간의 사용자 지정 이미지 복사](http://www.visualstudiogeeks.com/blog/DevOps/How-To-Move-CustomImages-VHD-Between-AzureDevTestLabs#copying-custom-images-between-azure-devtest-labs)

##<a name="next-steps"></a>다음 단계

- [랩에 VM 추가](devtest-lab-add-vm.md)
