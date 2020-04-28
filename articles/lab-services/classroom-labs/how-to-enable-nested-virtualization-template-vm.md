---
title: Azure Lab Services의 템플릿 VM에서 중첩 된 가상화 사용 Microsoft Docs
description: 내에서 여러 Vm을 사용 하 여 템플릿 VM을 만드는 방법을 알아봅니다.  즉, Azure Lab Services의 템플릿 VM에서 중첩 된 가상화를 사용 하도록 설정 합니다.
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
ms.openlocfilehash: 3c954c4689281838ea8c61c932cdcc3b74bac442
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "82184676"
---
# <a name="enable-nested-virtualization-on-a-template-virtual-machine-in-azure-lab-services"></a>Azure Lab Services의 템플릿 가상 머신에서 중첩 된 가상화 사용

현재 Azure Lab Services에서는 랩에 하나의 템플릿 가상 머신을 설정 하 고 각 사용자가 단일 복사본을 사용할 수 있도록 합니다. 교수 교육 네트워킹, 보안 또는 IT 클래스를 사용 하는 경우 여러 가상 컴퓨터가 네트워크를 통해 서로 통신할 수 있는 환경에 각 학생을 제공 해야 할 수 있습니다.

중첩 된 가상화를 사용 하면 랩의 템플릿 가상 머신 내에서 다중 VM 환경을 만들 수 있습니다. 템플릿을 게시 하면 랩에 있는 각 사용자에 게 가상 머신 내에서 여러 Vm을 설정 하는 가상 머신이 제공 됩니다.  이 문서에서는 Azure Lab Services의 템플릿 컴퓨터에서 중첩 된 가상화를 설정 하는 방법을 설명 합니다.

## <a name="what-is-nested-virtualization"></a>중첩 가상화 란?

중첩 된 가상화를 사용 하면 가상 머신 내에서 가상 머신을 만들 수 있습니다. 중첩 된 가상화는 Hyper-v를 통해 수행 되며 Windows Vm 에서만 사용할 수 있습니다.

중첩 된 가상화에 대 한 자세한 내용은 다음 문서를 참조 하세요.

- [Azure에서 중첩 된 가상화](https://azure.microsoft.com/blog/nested-virtualization-in-azure/)
- [Azure VM에서 중첩된 가상화를 사용하는 방법](../../virtual-machines/windows/nested-virtualization.md)

## <a name="considerations"></a>고려 사항

중첩 된 가상화를 사용 하 여 랩을 설정 하기 전에 다음 사항을 고려해 야 합니다.

- 새 랩을 만들 때 가상 머신 크기에 대 한 **중간 (중첩 된 가상화)** 또는 **큰 (중첩 된 가상화)** 크기를 선택 합니다. 이러한 가상 머신 크기는 중첩 된 가상화를 지원 합니다.
- 호스트와 클라이언트 가상 컴퓨터 모두에 대해 양호한 성능을 제공 하는 크기를 선택 합니다.  가상화를 사용 하는 경우 선택 하는 크기는 한 대의 컴퓨터 뿐만 아니라 호스트와 동시에 실행 되는 모든 Hyper-v 컴퓨터에 적합 해야 합니다.
- 클라이언트 가상 머신은 Azure 가상 네트워크의 Azure 리소스 (예: DNS 서버)에 액세스할 수 없습니다.
- 호스트 가상 컴퓨터를 사용 하려면 클라이언트 컴퓨터에서 인터넷 연결을 허용 하도록 설정 해야 합니다.
- 클라이언트 가상 머신은 독립 컴퓨터로 사용이 허가 됩니다. Microsoft 운영 체제 및 제품의 라이선스에 대 한 자세한 내용은 [Microsoft 라이선스](https://www.microsoft.com/licensing/default) 를 참조 하십시오. 템플릿 컴퓨터를 설정 하기 전에 사용 중인 다른 소프트웨어에 대 한 사용권 계약을 확인 합니다.

## <a name="enable-nested-virtualization-on-a-template-vm"></a>템플릿 VM에서 중첩된 가상화 사용

이 문서에서는 랩 계정 및 랩을 만들었다고 가정 합니다.  새 랩 계정을 만드는 방법에 대 한 자세한 내용은 자습서를 참조 [하 여 랩 계정 설정](tutorial-setup-lab-account.md)을 참조 하세요. 랩을 만드는 방법에 대 한 자세한 내용은 [교실 랩 설정 자습서](tutorial-setup-classroom-lab.md)를 참조 하세요.

>[!IMPORTANT]
>랩을 만들 때 가상 머신 크기에 대 한 **큰 (중첩 된 가상화)** 또는 **중간 (중첩 된 가상화)** 을 선택 합니다.  그렇지 않으면 중첩 된 가상화가 작동 하지 않습니다.  

템플릿 컴퓨터에 연결 하려면 [교실 템플릿 만들기 및 관리](how-to-create-manage-template.md)를 참조 하세요.

중첩 된 가상화를 사용 하도록 설정 하려면 몇 가지 작업을 수행 해야 합니다.  

- **Hyper-v 역할을 사용 하도록 설정**합니다. Hyper-v 역할을 사용 하도록 설정 하 여 랩 서비스 가상 컴퓨터에서 Hyper-v 가상 컴퓨터를 만들고 실행 해야 합니다.
- **DHCP를 사용 하도록 설정**합니다.  Lab Services 가상 컴퓨터에서 DHCP 역할을 사용 하도록 설정한 경우 Hyper-v 가상 컴퓨터에 IP 주소를 자동으로 할당할 수 있습니다.
- **Hyper-v vm에 대 한 NAT 네트워크를 만듭니다**.  NAT 네트워크는 Hyper-v 가상 컴퓨터에서 인터넷에 액세스할 수 있도록 설정 됩니다.  Hyper-v 가상 머신은 서로 통신할 수 있습니다.

>[!NOTE]
>Lab Services VM에서 만든 NAT 네트워크를 사용 하면 Hyper-v VM이 동일한 Lab Services VM의 인터넷 및 기타 Hyper-v vm에 액세스할 수 있습니다.  Hyper-v VM은 Azure virtual network의 Azure 리소스 (예: DNS 서버)에 액세스할 수 없습니다.

스크립트를 사용 하거나 Windows 도구를 사용 하 여 위에 나열 된 작업을 수행할 수 있습니다.  자세한 내용은 아래 섹션을 참조 하세요.

### <a name="using-script-to-enable-nested-virtualization"></a>스크립트를 사용 하 여 중첩 된 가상화 사용

Windows Server 2016 또는 Windows Server 2019에서 중첩 된 가상화에 대 한 자동화 된 설치를 사용 하려면 [스크립트를 사용 하 여 Azure Lab Services의 템플릿 가상 머신에서 중첩 된 가상화 사용](how-to-enable-nested-virtualization-template-vm-using-script.md)을 참조 하세요. [Lab Services hyper-v 스크립트](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Scripts/HyperV) 의 스크립트를 사용 하 여 hyper-v 역할을 설치 합니다.  또한 스크립트는 Hyper-v 가상 컴퓨터에서 인터넷에 액세스할 수 있도록 네트워킹을 설정 합니다.

### <a name="using-windows-tools-to-enable-nested-virtualization"></a>Windows 도구를 사용 하 여 중첩 된 가상화 사용

Windows 역할 및 관리 도구를 사용 하 여 windows Server 2016 또는 Windows Server 2019 용 중첩 된 가상화를 사용 하려면 [Azure Lab Services에서 템플릿 가상 머신에서 중첩 된 가상화를 수동으로 사용](how-to-enable-nested-virtualization-template-vm-ui.md)을 참조 하세요.  또한 Hyper-v 가상 컴퓨터에서 인터넷에 액세스할 수 있도록 네트워킹을 설정 하는 방법에 대해서도 설명 합니다.
