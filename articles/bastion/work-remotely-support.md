---
title: 'Bastion을 사용한 원격 근무: Azure Bastion'
description: 이 페이지에서는 Azure Bastion을 활용하여 코로나19 대유행에 따른 원격 근무를 지원하는 방법을 설명합니다.
services: bastion
author: mialdrid
ms.service: bastion
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: mialdrid
ms.openlocfilehash: 515a6f7175aee23c6f720258b7fdaefe76b3b5d6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92077952"
---
# <a name="working-remotely-using-azure-bastion"></a>Azure Bastion을 사용한 원격 근무

Azure Bastion은 인터넷 연결을 통해 사용자가 Azure 가상 머신에 액세스할 수 있도록 허용하여 원격 근무 시나리오를 지원하는 데 중추적인 역할을 합니다. 특히 IT 관리자는 Azure에서 실행되는 애플리케이션을 언제 어디서나 관리할 수 있습니다.

>[!NOTE]
>이 문서에서는 Azure Bastion, Azure, Microsoft 네트워크, Azure 파트너 에코시스템을 활용하여 원격 근무하고 코로나19 위기로 인해 발생하는 네트워크 이슈를 완화하는 방법을 설명합니다.
>

## <a name="securely-access-virtual-machines"></a>가상 머신에 안전하게 액세스

구체적으로 Azure Bastion은 공용 IP 주소를 사용하지 않고 Azure Portal에서 직접 Azure 가상 네트워크 내의 가상 머신에 액세스할 수 있도록 안전하고 원활한 RDP/SSH 연결을 제공합니다. Azure Bastion 아키텍처 및 주요 기능에 대한 자세한 내용은 [Azure Bastion이란?](bastion-overview.md)을 참조하세요.

Azure Bastion은 가상 네트워크별로 배포되므로 기업이 Azure 가상 네트워크 내의 가상 머신에 대한 원격 사용자 액세스를 빠르게 지원하도록 Azure Bastion을 구성하고 관리할 수 있습니다. Azure Bastion을 만들고 관리하는 방법에 대한 지침은 [베스천 호스트 만들기](./tutorial-create-host-portal.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

* [Azure Portal](./tutorial-create-host-portal.md), [PowerShell](bastion-create-host-powershell.md) 또는 Azure CLI를 사용하여 Azure Bastion을 구성합니다.

* 추가 정보는 [Bastion FAQ](bastion-faq.md)를 참조하세요.