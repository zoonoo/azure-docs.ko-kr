---
title: 브라우저를 통해 가상 머신에 연결-Azure | Microsoft Docs
description: 브라우저를 통해 가상 머신에 연결 하는 방법을 알아봅니다.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: tanmayeekamath
manager: femila
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/09/2019
ms.author: takamath
ms.openlocfilehash: 4549ee0f50c06040b70307c96b5ca96cd4162214
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "74974298"
---
# <a name="connect-to-your-virtual-machines-through-a-browser"></a>브라우저를 통해 가상 컴퓨터에 연결 

DevTest Labs는 브라우저를 통해 가상 컴퓨터에 연결할 수 있도록 하는 [Azure 방호](https://docs.microsoft.com/azure/bastion/)와 통합 됩니다. DevTest Labs에서이 기능을 사용 하도록 설정 하는 방법에 대 한 자세한 내용은 [lab virtual machines에서 브라우저 연결 사용](enable-browser-connection-lab-virtual-machines.md)을 참조 하세요.

*브라우저 연결* 을 사용 하도록 설정 하면 랩 사용자는 브라우저를 통해 가상 컴퓨터에 액세스할 수 있습니다.  

## <a name="create-a-lab-virtual-machine"></a>랩 가상 컴퓨터 만들기

먼저,이 가상 네트워크에는 요새가 구성 된 가상 네트워크 내에서 랩 가상 머신을 만들어야 합니다. AzureBastionSubnet가 아닌, 만든 두 번째 **서브넷** 을 선택 합니다. **고급 설정** 탭으로 이동 하 여 가상 컴퓨터를 만드는 동안 가상 네트워크를 선택할 수 있습니다.

![가상 머신 만들기](./media/connect-virtual-machine-through-browser/create-virtual-machine.png)

## <a name="launch-virtual-machine-in-a-browser"></a>브라우저에서 가상 컴퓨터 시작

가상 머신을 만든 후 브라우저 *연결* 단추를 클릭 하 고 컴퓨터에 대 한 사용자 이름 및 암호를 입력 하 여 브라우저에서 시작할 수 있습니다.  

![브라우저에서 시작](./media/connect-virtual-machine-through-browser/browser-connect.png)

## <a name="next-steps"></a>다음 단계

[Azure DevTest Labs에서 랩에 VM 추가](devtest-lab-add-vm.md)
