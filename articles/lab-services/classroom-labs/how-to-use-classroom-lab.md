---
title: Azure Lab Services의 클래스룸 랩에 액세스하는 방법 | Microsoft Docs
description: 이 자습서에서는 교수가 설정한 클래스룸 랩의 가상 머신에 액세스합니다.
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
ms.date: 02/07/2019
ms.author: spelluru
ms.openlocfilehash: bc5c12d4bb92edaafcc9808da8c48106a6e0cbd5
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/13/2019
ms.locfileid: "59548044"
---
# <a name="how-to-access-a-classroom-lab-in-azure-lab-services"></a>Azure Lab Services의 클래스룸 랩에 액세스하는 방법
이 문서에서는 클래스룸 랩에 액세스하고, 랩의 VM에 연결하고 VM을 중지하는 방법을 설명합니다. 

## <a name="register-to-a-lab"></a>랩에 등록
1. 교수/강사로부터 받은 **등록 URL**로 이동합니다. 
2. 학교 계정을 사용하여 서비스에 로그인하여 등록을 완료합니다. 
3. 등록 후에는 액세스할 수 있는 랩의 가상 머신이 보이는지 확인합니다. 
2. 가상 머신이 준비될 때까지 기다렸다가 VM을 **시작**합니다. 이 프로세스는 다소 시간이 걸립니다.  

    ![VM 시작](../media/tutorial-connect-vm-in-classroom-lab/start-vm.png)


## <a name="view-all-the-classroom-labs"></a>모든 클래스룸 랩 보기
랩에 등록한 후 다음 단계를 수행하여 모든 클래스 룸 랩을 볼 수 있습니다. 

1. [https://labs.azure.com](https://labs.azure.com)으로 이동합니다. Internet Explorer 11은 아직 지원되지 않습니다. 
2. 랩에 등록하는 데 사용한 사용자 계정을 사용하여 서비스에 로그인합니다. 
3. 액세스 권한이 있는 모든 랩이 표시되는지 확인합니다. 

    ![모든 랩 보기](../media/how-to-use-classroom-lab/all-labs.png)

## <a name="connect-to-the-virtual-machine-in-a-classroom-lab"></a>클래스룸 랩에서 가상 머신에 연결

1. VM을 아직 시작하지 않은 경우에는 타일에서 **시작**을 선택합니다. 
2. 액세스하려는 랩의 가상 머신을 나타내는 타일에서 **연결**을 선택합니다. 
3. 다음 단계 중 하나를 수행합니다. 
   1. **Windows** 가상 머신의 경우 **RDP** 파일을 하드 디스크에 저장합니다. RDP 파일을 열어 가상 머신에 연결합니다. 강사/교수로부터 받은 **사용자 이름** 및 **암호**를 사용하여 머신에 로그인합니다. 
   3. **Linux** 가상 머신의 경우 **가상 머신에 연결** 대화 상자에서 SSH 연결 문자열을 복사하고 저장합니다. SSH 터미널(예: [Putty](https://www.putty.org/))에서 이 연결 문자열을 사용하여 가상 머신에 연결합니다.

## <a name="stop-the-virtual-machine-in-a-classroom-lab"></a>클래스룸 랩의 가상 머신 중지

VM을 중지하려면 타일에서 **중지**를 선택합니다. VM이 중지되면 타일의 **시작** 단추를 사용할 수 있게 됩니다. 

## <a name="next-steps"></a>다음 단계
다음 문서를 참조하세요.

- [관리자 권한으로 랩 계정 만들기 및 관리](how-to-manage-lab-accounts.md)
- [랩 소유자 권한으로 랩 만들기 및 관리](how-to-manage-classroom-labs.md)
- [랩 소유자 권한으로 템플릿 설정 및 게시](how-to-create-manage-template.md)
- [랩 소유자 권한으로 랩 사용 구성 및 제어](how-to-configure-student-usage.md)
 