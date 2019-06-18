---
title: Azure Lab Services에서 Vm에 대 한 암호를 설정 합니다. | Microsoft Docs
description: 설정 하 고 Azure Lab Services의 클래스 룸 랩의 virtual machines (Vm)에 대 한 암호를 재설정 하는 방법에 알아봅니다.
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
ms.date: 06/11/2019
ms.author: spelluru
ms.openlocfilehash: 3701c69ff97d840f6cba175df8f02bc55ece498b
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67123185"
---
# <a name="set-or-reset-password-for-virtual-machines-in-classroom-labs"></a>또는 클래스 룸 랩의 가상 머신에 대 한 암호를 다시 설정
이 문서에서는 클래스 룸 랩에 Vm에 액세스 하기 위한 여러 가지 설정과 resettings 암호를 제공 합니다. 

## <a name="lab-owners-teachers"></a>랩 소유자 (교사)
랩 소유자 (교사)를 사용 하는 암호를 설정할 수 Vm에 대 한 랩에서에 **자격 증명 설정** 랩 만들기 마법사의 페이지입니다.

![자격 증명 설정](../media/tutorial-setup-classroom-lab/set-credentials.png)

그런 다음 랩 소유자 암호를 재설정할 수 (필요한 경우)에 **구성 템플릿** 랩 만들기 마법사의 페이지입니다. 

![완료된 템플릿 페이지 구성](../media/tutorial-setup-classroom-lab/configure-template-after-complete.png)

랩 소유자 대시보드에서 랩을 만든 후 암호를 다시 설정할 수도 있습니다. 

![홈 페이지의 메뉴 암호 다시 설정](../media/how-to-set-virtual-machine-passwords/reset-password-menu-dashboard.png)

그런 다음에 새 암호를 입력 합니다 **암호 설정** 페이지에서 선택한 **세트 암호**합니다. 

![홈 페이지의 메뉴 암호 다시 설정](../media/how-to-set-virtual-machine-passwords/set-password.png)

## <a name="lab-users-students"></a>랩 사용자 (학생)
랩을 만든 때. 동시에, 랩 소유자 설정 하거나 해제할 수 있도록 학생 들이 Vm에 대 한 자신의 암호를 설정 합니다. 랩 소유자 (교사)이이 옵션을 사용 하도록 설정할 경우 랩 사용자 (학생) 옵션 학생 처음으로 VM에 로그인 할 때 VM에 대 한 암호를 설정 해야 합니다. 에 **Windows VM**, 키를 누릅니다 **CTRL + ALT + END**를 선택 하 고 **암호 변경**합니다. 

## <a name="next-steps"></a>다음 단계
옵션에 대해 알아보려면 다른 학생 사용 하면 (랩 소유자로) 수 구성, 다음 문서를 참조 하세요. [학생 사용 구성](how-to-configure-student-usage.md)합니다.
