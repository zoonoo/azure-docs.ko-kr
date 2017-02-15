---
title: "VM을 만드는 Azure DevTest Labs 수식 관리 | Microsoft 문서"
description: "Azure DevTest Labs 수식을 만들고 업데이트하고 제거하는 방법과 이 수식을 사용하여 새 VM을 만드는 방법에 대해 알아봅니다."
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
ms.date: 11/25/2016
ms.author: tarcher
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: f7ea1762e3a3ffe8d3c0c6af307b846dc1f3c696


---
# <a name="manage-devtest-labs-formulas-to-create-vms"></a>VM을 만드는 DevTest Labs 수식 관리
Azure DevTest Labs에 포함된 수식은 VM(가상 컴퓨터)을 만드는 데 사용되는 기본 속성 값의 목록입니다. 수식을 통해 VM을 만들 때, 기본값을 그대로 사용하거나 수정할 수 있습니다. [사용자 지정 이미지](devtest-lab-create-template.md) 및 [마켓플레이스 이미지](devtest-lab-configure-marketplace-images.md)와 마찬가지로 수식은 신속한 VM 프로비전을 위한 메커니즘을 제공합니다.  

이 문서에서는 다음 작업을 수행하는 방법을 알아봅니다.

* [수식 만들기](#create-a-formula)
* [수식을 사용하여 VM 프로비전](#use-a-formula-to-provision-a-vm)
* [수식 수정](#modify-a-formula)
* [수식 삭제](#delete-a-formula)

> [!NOTE]
> [사용자 지정 이미지](devtest-lab-create-template.md)와 마찬가지로 수식을 사용하면 VHD 파일에서 기본 이미지를 만들 수 있습니다. 그런 다음 기본 이미지를 사용하여 새 VM을 프로비전할 수 있습니다. 사용자의 특정 환경에 적합한 것을 결정하는 데 도움이 필요하면 [DevTest Lab에서 사용자 지정 이미지와 수식 비교](devtest-lab-comparing-vm-base-image-types.md)문서를 참조하세요.
> 
> 

## <a name="create-a-formula"></a>수식 만들기
DevTest Lab *사용자* 권한이 있으면 수식을 기준으로 사용하여 VM을 만들 수 있습니다. 수식을 만드는 방법은 두 가지입니다. 

* 기준에서 - 수식의 모든 특성을 정의하려는 경우에 사용합니다.
* 기존 랩 VM을 통해 - 기존 VM의 설정을 기반으로 수식을 만들려는 경우에 사용합니다.

### <a name="create-a-formula-from-a-base"></a>기준에서 수식 만들기
다음 단계에서는 사용자 지정 이미지, 마켓플레이스 이미지 또는 다른 수식에서 수식을 만드는 과정을 안내합니다.

1. [Azure 포털](http://go.microsoft.com/fwlink/p/?LinkID=525040)에 로그인합니다.
2. **추가 서비스**를 선택한 후 목록에서 **DevTest Labs**을 선택합니다.
3. 랩 목록에서 원하는 랩을 탭합니다.  
4. 랩의 블레이드에서 **수식(재사용 가능 기준)**을 선택합니다.
   
    ![수식 메뉴](./media/devtest-lab-manage-formulas/lab-settings-formulas.png)
5. **랩 수식** 블레이드에서 **+ 추가**를 선택합니다.
   
    ![수식 추가](./media/devtest-lab-manage-formulas/add-formula.png)
6. **기준 선택** 블레이드에서 수식을 만들 때 사용할 기준(사용자 지정 이미지, 마켓플레이스 이미지 또는 수식)을 선택합니다.
   
    ![기본 목록](./media/devtest-lab-manage-formulas/base-list.png)
7. **Create formula** (수식 만들기) 블레이드에서 다음 값을 지정합니다.
   
   * **수식 이름** - 수식의 이름을 입력합니다. 이 값은 VM을 만들 때 기본 이미지 목록에 표시됩니다. 이름 입력 시에 유효성이 검사되고, 유효하지 않으면, 유효한 이름에 필요한 사항을 알려주는 메시지가 표시됩니다.
   * **설명** - 수식에 대한 의미 있는 설명을 입력합니다. 이 값은 VM을 만들 때 수식의 상황에 맞는 메뉴에서 볼 수 있습니다.
   * **사용자 이름** - 관리자 권한이 부여된 사용자 이름을 입력합니다.
   * **암호** - 지정된 사용자에 대해 사용하려는 암호와 연결된 값을 입력하거나 드롭다운 목록에서 선택합니다.  
   * **이미지** - 이 필드는 이전 블레이드에서 선택한 기본 이미지의 이름을 표시합니다. 
   * **가상 컴퓨터 크기** - 만들려는 VM의 프로세서 코어, RAM 크기 및 하드 드라이브 크기를 지정하는 미리 정의된 항목 중 하나를 선택합니다.
   * **가상 네트워크** - 원하는 가상 네트워크를 선택합니다.
   * **서브넷** - 원하는 서브넷을 지정합니다.
   * **공용 IP 주소** - 선택한 서브넷에 공용 IP 주소를 허용하도록 랩 정책이 설정되어 있으면 **예** 또는 **아니요**를 선택하여 IP 주소를 공용으로 할 것인지 지정합니다. 그렇지 않으면 이 옵션이 비활성화되고 **아니요**가 선택됩니다.
   * **아티팩트** - 기준 이미지에 추가할 아티팩트를 선택하고 구성합니다. 보안 문자열 값은 수식과 함께 저장되지 않습니다. 따라서 보안 문자열에 해당하는 아티팩트 매개 변수는 표시되지 않습니다. 
     
       ![Create formula](./media/devtest-lab-manage-formulas/create-formula.png)
8. **만들기** 를 탭하여 수식을 만듭니다.

### <a name="create-a-formula-from-a-vm"></a>VM에서 수식 만들기
다음 단계는 기존 VM을 기반으로 수식을 만드는 과정을 안내합니다. 

> [!NOTE]
> VM에서 수식을 만들려면 2016년 3월 30일 이후에 VM을 만들었어야 합니다. 
> 
> 

1. [Azure 포털](http://go.microsoft.com/fwlink/p/?LinkID=525040)에 로그인합니다.
2. **추가 서비스**를 선택한 후 목록에서 **DevTest Labs**을 선택합니다.
3. 랩 목록에서 원하는 랩을 탭합니다.  
4. 랩의 **개요** 블레이드에서 수식을 만들 VM을 선택합니다.
   
    ![랩 VM](./media/devtest-lab-manage-formulas/my-vms.png)
5. VM 블레이드에서 **수식 만들기(재사용 가능 기준)**를 선택합니다.
   
    ![Create formula](./media/devtest-lab-manage-formulas/create-formula-menu.png)
6. **수식 만들기** 블레이드에서 새 수식의 **이름** 및 **설명** 을 입력합니다.
   
    ![수식 만들기 블레이드](./media/devtest-lab-manage-formulas/create-formula-blade.png)
7. **확인** 을 선택하여 수식을 만듭니다.

## <a name="use-a-formula-to-provision-a-vm"></a>수식을 사용하여 VM 프로비전
수식을 만들면 해당 수식에 따라 VM을 만들 수 있습니다. [아티팩트를 사용하여 VM 추가](devtest-lab-add-vm-with-artifacts.md#add-a-vm-with-artifacts) 섹션에서 이 과정을 안내합니다.

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




<!--HONumber=Nov16_HO3-->


