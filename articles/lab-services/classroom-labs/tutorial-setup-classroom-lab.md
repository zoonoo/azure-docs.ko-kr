---
title: Azure Lab Services를 사용하여 클래스룸 랩 설정 | Microsoft Docs
description: 이 자습서에서는 클래스룸에서 사용할 랩을 설정합니다.
services: devtest-lab, lab-services, virtual-machines
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.date: 05/17/2018
ms.author: spelluru
ms.openlocfilehash: a96ba4aec7c23c040921a647cc4986aaf53fb30c
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/01/2018
ms.locfileid: "34651574"
---
# <a name="tutorial-set-up-a-classroom-lab"></a>자습서: 클래스룸 랩 설정 
이 자습서에서는 클래스룸에서 학생이 사용할 가상 머신 집합으로 클래스룸 랩을 설정합니다.  

이 자습서에서는 다음 작업을 수행합니다.

> [!div class="checklist"]
> * 클래스룸 랩 만들기
> * 클래스룸 랩 구성
> * 학생에게 등록 링크 보내기

## <a name="create-a-classroom-lab"></a>클래스룸 랩 만들기

1. [Azure Lab Services 웹 사이트](https://labs.azure.com)로 이동합니다.
2. **새 랩** 창에서 다음 작업을 수행합니다. 
    1. 클래스룸 랩의 **이름**을 지정합니다. 
    2. 클래스룸에서 사용할 가상 머신의 **크기**를 선택합니다.
    3. 가상 머신을 만드는 데 사용할 **이미지**를 선택합니다.
    4. 실험실의 가상 머신에 로그인하기 위한 **기본 자격 증명**을 지정합니다. 
    7. **저장**을 선택합니다.

        ![클래스룸 랩 만들기](../media/tutorial-setup-classroom-lab/new-lab-window.png)
1. 랩의 **홈페이지**가 표시됩니다. 
    
    ![클래스룸 랩 홈페이지](../media/tutorial-setup-classroom-lab/classroom-lab-home-page.png)

## <a name="configure-usage-policy"></a>사용 정책 구성

1. **사용 정책**을 선택합니다. 
2. **사용 정책**의 설정에서 랩을 사용할 수 있는 **사용자 수**를 입력합니다.
3. **저장**을 선택합니다. 

    ![사용 정책](../media/tutorial-setup-classroom-lab/usage-policy-settings.png)

## <a name="set-up-the-template"></a>템플릿 설정 
랩의 템플릿은 모든 사용자의 가상 머신이 만들어지는 기본 가상 머신 이미지입니다. 랩 사용자에게 제공하려는 구성과 정확히 일치하도록 템플릿 가상 머신을 설정합니다. 랩 사용자에게 표시되는 템플릿의 이름 및 설명을 제공할 수 있습니다. 랩 사용자가 템플릿 VM의 인스턴스를 사용할 수 있도록 공용으로 템플릿의 표시 유형을 설정합니다. 

### <a name="set-title-and-description"></a>제목 및 설명 설정
1. **템플릿** 섹션에서 템플릿의 **편집**(연필 아이콘)을 선택합니다. 
2. **사용자 보기** 창에 템플릿의 **제목**을 입력합니다.
3. 템플릿에 대한 **설명**을 입력합니다.
4. **저장**을 선택합니다.

    ![클래스룸 랩 설명](../media/tutorial-setup-classroom-lab/lab-description.png)

### <a name="make-instances-of-the-template-public"></a>템플릿 인스턴스를 공용으로 설정
템플릿의 표시 유형을 **공용**으로 설정하면 Azure Lab Services에서 템플릿을 사용하여 랩에 VM을 만듭니다. 이 과정에서 만들어지는 VM 수는 랩에 허용되는 최대 사용자 수와 같으며, 이 값은 랩의 사용 정책에서 설정할 수 있습니다. 모든 가상 머신은 템플릿과 구성이 동일합니다. 

1. **템플릿** 섹션에서 **표시 유형**을 선택합니다. 
2. **가용성** 페이지에서 **공용**을 선택합니다.
    
    > [!IMPORTANT]
    > 템플릿을 공용으로 사용할 수 있게 되면 템플릿에 대한 액세스를 비공개로 변경할 수 없습니다. 
3. **저장**을 선택합니다.

    ![가용성](../media/tutorial-setup-classroom-lab/public-access.png)

## <a name="send-registration-link-to-students"></a>학생에게 등록 링크 보내기

1. **사용자 등록** 타일을 선택합니다.
2. **사용자 등록** 대화 상자에서 **복사** 단추를 선택합니다. 링크가 클립보드에 복사됩니다. 이메일 편집기에 붙여넣고 학생에게 이메일을 보냅니다. 

    ![학생 등록 ​링크](../media/tutorial-setup-classroom-lab/registration-link.png)

## <a name="next-steps"></a>다음 단계
이 자습서에서는 클래스룸 랩을 만들고 랩을 구성했습니다. 학생이 등록 링크를 사용하여 랩에서 VM에 액세스하는 방법을 알아보려면 다음 자습서로 이동하십시오.

> [!div class="nextstepaction"]
> [클래스룸 랩의 VM에 연결](tutorial-connect-virtual-machine-classroom-lab.md)

