---
title: 브라우저를 통해 가상 컴퓨터에 연결 - Azure | 마이크로 소프트 문서
description: 브라우저를 통해 가상 컴퓨터에 연결하는 방법을 알아봅니다.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74974298"
---
# <a name="connect-to-your-virtual-machines-through-a-browser"></a>브라우저를 통해 가상 컴퓨터에 연결 

DevTest Labs는 브라우저를 통해 가상 컴퓨터에 연결할 수 있는 [Azure Bastion과](https://docs.microsoft.com/azure/bastion/)통합됩니다. DevTest Labs에서 이 기능을 활성화하는 방법에 대한 자세한 내용은 [랩 가상 컴퓨터에서 브라우저 연결 활성화를](enable-browser-connection-lab-virtual-machines.md)참조하십시오.

브라우저 *연결을* 사용하도록 설정하면 랩 사용자는 브라우저를 통해 가상 컴퓨터에 액세스할 수 있습니다.  

## <a name="create-a-lab-virtual-machine"></a>랩 가상 컴퓨터 만들기

먼저 Bastion이 구성된 가상 네트워크 내에서 랩 가상 컴퓨터를 만들어야 합니다. AzureBastionSubnet이 아니라 만든 두 번째 **서브넷을** 선택합니다. **고급 설정** 탭으로 이동하여 가상 컴퓨터를 만드는 동안 가상 네트워크를 선택할 수 있습니다.

![가상 머신 만들기](./media/connect-virtual-machine-through-browser/create-virtual-machine.png)

## <a name="launch-virtual-machine-in-a-browser"></a>브라우저에서 가상 컴퓨터 실행

가상 컴퓨터를 만든 후에는 *브라우저 연결* 버튼을 클릭하고 컴퓨터의 사용자 이름과 암호를 입력하여 브라우저에서 시작할 수 있습니다.  

![브라우저에서 시작](./media/connect-virtual-machine-through-browser/browser-connect.png)

## <a name="next-steps"></a>다음 단계

[Azure DevTest Labs에서 랩에 VM 추가](devtest-lab-add-vm.md)
