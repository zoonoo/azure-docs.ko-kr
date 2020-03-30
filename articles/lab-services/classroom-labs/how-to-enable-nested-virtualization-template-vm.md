---
title: Azure 랩 서비스의 템플릿 VM에서 중첩 가상화 활성화 | 마이크로 소프트 문서
description: 내부에 여러 VM이 있는 템플릿 VM을 만드는 방법에 대해 알아봅니다.  즉, Azure Lab 서비스의 템플릿 VM에서 중첩된 가상화를 사용하도록 설정합니다.
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
ms.date: 10/04/2019
ms.author: spelluru
ms.openlocfilehash: 59b32834369f76d39bb4a253dad4ec541e7ef999
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79502019"
---
# <a name="enable-nested-virtualization-on-a-template-virtual-machine-in-azure-lab-services"></a>Azure Lab 서비스의 템플릿 가상 머신에서 중첩가상화 사용

현재 Azure Lab Services를 사용하면 랩에서 하나의 템플릿 가상 컴퓨터를 설정하고 각 사용자가 단일 복사본을 사용할 수 있도록 할 수 있습니다. 네트워킹, 보안 또는 IT 수업을 가르치는 교수인 경우 각 학생에게 네트워크를 통해 여러 가상 컴퓨터가 서로 통신할 수 있는 환경을 제공해야 할 수 있습니다.

중첩된 가상화를 사용하면 랩의 템플릿 가상 시스템 내에서 다중 VM 환경을 만들 수 있습니다. 템플릿을 게시하면 랩의 각 사용자에게 여러 VM이 있는 가상 시스템이 설정됩니다.  이 문서에서는 Azure Lab Services의 템플릿 컴퓨터에서 중첩된 가상화를 설정하는 방법을 설명합니다.

## <a name="what-is-nested-virtualization"></a>중첩 된 가상화란 무엇입니까?

중첩된 가상화를 사용하면 가상 시스템 내에서 가상 컴퓨터를 만들 수 있습니다. 중첩된 가상화는 하이퍼-V를 통해 수행되며 Windows VM에서만 사용할 수 있습니다.

중첩 가상화에 대한 자세한 내용은 다음 문서를 참조하십시오.

- [Azure에 중첩된 가상화](https://azure.microsoft.com/blog/nested-virtualization-in-azure/)
- [Azure VM에서 중첩된 가상화를 사용하는 방법](../../virtual-machines/windows/nested-virtualization.md)

## <a name="considerations"></a>고려 사항

중첩된 가상화를 사용하여 랩을 설정하기 전에 고려해야 할 몇 가지 사항은 다음과 같습니다.

- 새 랩을 만들 때 가상 컴퓨터 크기에 대해 **중간(중첩 가상화)** 또는 **대형(중첩 가상화)** 크기를 선택합니다. 이러한 가상 컴퓨터 크기는 중첩가상화를 지원합니다.
- 호스트 와 클라이언트 가상 시스템 모두에 대해 좋은 성능을 제공하는 크기를 선택합니다.  가상화를 사용할 때 선택한 크기는 한 대의 컴퓨터뿐만 아니라 호스트와 동시에 실행되어야 하는 모든 클라이언트 컴퓨터에도 적합해야 합니다.
- 클라이언트 가상 시스템은 Azure 가상 네트워크의 DNS 서버와 같은 Azure 리소스에 액세스할 수 없습니다.
- 호스트 가상 머신은 클라이언트 컴퓨터가 인터넷에 연결될 수 있도록 설정해야 합니다.
- 클라이언트 가상 컴퓨터는 독립 컴퓨터로 라이선스가 부여됩니다. Microsoft 운영 체제 및 제품에 대한 라이선스에 대한 자세한 내용은 Microsoft [라이선스를](https://www.microsoft.com/licensing/default) 참조하십시오. 템플릿 컴퓨터를 설정하기 전에 사용 중인 다른 소프트웨어에 대한 라이선스 계약을 확인합니다.

## <a name="enable-nested-virtualization-on-a-template-vm"></a>템플릿 VM에서 중첩된 가상화 사용

이 문서에서는 랩 계정 및 랩을 만들었다고 가정합니다.  새 랩 계정을 만드는 자세한 내용은 [랩 계정을 설정하는 자습서를](tutorial-setup-lab-account.md)참조하십시오. 랩을 만드는 방법에 대한 자세한 내용은 [강의실 랩 자습서 설정을](tutorial-setup-classroom-lab.md)참조하십시오.

>[!IMPORTANT]
>랩을 만들 때 가상 컴퓨터 크기에 대해 **큰(중첩된 가상화)** 또는 **중간(중첩된 가상화)을** 선택합니다.  중첩된 가상화는 그렇지 않으면 작동하지 않습니다.  

템플릿 컴퓨터에 연결하려면 [교실 템플릿 만들기 및 관리를](how-to-create-manage-template.md)참조하십시오.

### <a name="using-script-to-enable-nested-virtualization"></a>스크립트를 사용하여 중첩 가상화 사용

Windows Server 2016 또는 Windows Server 2019를 사용하여 중첩된 가상화에 대한 자동화된 설정을 사용하려면 [스크립트를 사용하여 Azure Lab 서비스의 템플릿 가상 컴퓨터에서 중첩된 가상화 활성화를](how-to-enable-nested-virtualization-template-vm-using-script.md)참조하십시오. 랩 서비스 하이퍼 [V 스크립트의 스크립트를](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Scripts/HyperV) 사용하여 Hyper-V 역할을 설치합니다.  또한 하이퍼-V 가상 시스템이 인터넷에 액세스할 수 있도록 스크립트도 네트워킹을 설정합니다.

### <a name="using-windows-tools-to-enable-nested-virtualization"></a>Windows 도구를 사용하여 중첩 가상화 사용

Windows 역할 및 관리 도구를 사용하여 Windows Server 2016 또는 Windows Server 2019에 대한 가상화를 중첩한 설정은 [Azure Lab Services의 템플릿 가상 컴퓨터에 중첩된 가상화 활성화를 참조하십시오.](how-to-enable-nested-virtualization-template-vm-ui.md)  또한 Hyper-V 가상 시스템이 인터넷에 액세스할 수 있도록 네트워킹을 설정하는 방법도 설명합니다.
