---
title: "VM을 만드는 Azure DevTest Labs 수식 관리 | Microsoft 문서"
description: "Azure DevTest Labs 수식을 업데이트하고 제거하는 방법 알아보기"
services: devtest-lab,virtual-machines
documentationcenter: na
author: tomarcher
manager: douge
editor: 
ms.assetid: 841dd95a-657f-4d80-ba26-59a9b5104fe4
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/07/2017
ms.author: tarcher
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: a087df444c5c88ee1dbcf8eb18abf883549a9024
ms.openlocfilehash: fd334dc8cd22990456888146f41389054f2e8b29
ms.lasthandoff: 03/15/2017


---
# <a name="manage-azure-devtest-labs-formulas"></a>Azure DevTest Labs 수식 관리

[!INCLUDE [devtest-lab-formula-definition](../../includes/devtest-lab-formula-definition.md)]

[Azure DevTest Labs 수식 만들기](devtest-lab-create-formulas.md#create-a-formula) 문서는 Azure DevTest Labs에서 수식을 만드는 과정을 안내합니다. 수식이 있다면 이 문서에서 수식을 관리하는 방법을 배울 수 있습니다.

## <a name="modify-a-formula"></a>수식 수정
수식을 수정하려면 다음 단계를 수행합니다.

1. [Azure 포털](http://go.microsoft.com/fwlink/p/?LinkID=525040)에 로그인합니다.
2. **추가 서비스**를 선택한 후 목록에서 **DevTest Labs**을 선택합니다.
3. 랩 목록에서 원하는 랩을 탭합니다.  
4. 랩의 블레이드에서 **수식(재사용 가능 기준)**을 선택합니다.
   
    ![수식 메뉴](./media/devtest-lab-manage-formulas/lab-settings-formulas.png)
5. **Lab formulas** (랩 수식) 블레이드에서 수정할 수식을 탭합니다.
6. **수식 업데이트** 블레이드에서 원하는 내용을 편집하고 **업데이트**를 선택합니다.

## <a name="delete-a-formula"></a>수식 삭제
수식을 삭제하려면 다음 단계를 수행합니다.

1. [Azure 포털](http://go.microsoft.com/fwlink/p/?LinkID=525040)에 로그인합니다.
2. **추가 서비스**를 선택한 후 목록에서 **DevTest Labs**을 선택합니다.
3. 랩 목록에서 원하는 랩을 탭합니다.  
4. 랩의 **설정** 블레이드에서 **수식**을 선택합니다.
   
    ![수식 메뉴](./media/devtest-lab-manage-formulas/lab-settings-formulas.png)
5. **Lab formulas** (랩 수식) 블레이드에서 삭제할 수식 오른쪽의 줄임표를 클릭합니다.
   
    ![수식 메뉴](./media/devtest-lab-manage-formulas/lab-formulas-blade.png)
6. 수식의 상황에 맞는 메뉴에서 **삭제**를 선택합니다.
   
    ![수식 상황에 맞는 메뉴](./media/devtest-lab-manage-formulas/formula-delete-context-menu.png)
7. 삭제 확인 대화 상자에서 **예** 를 탭합니다.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="related-blog-posts"></a>관련 블로그 게시물
* [사용자 지정 이미지 또는 수식?](https://blogs.msdn.microsoft.com/devtestlab/2016/04/06/custom-images-or-formulas/)

## <a name="next-steps"></a>다음 단계
VM을 만들 때 사용할 수식을 만들었으면 다음 단계는 [VM을 랩에 추가](devtest-lab-add-vm-with-artifacts.md)하는 것입니다.


