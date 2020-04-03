---
title: '바스티온: Azure 요새를 사용하여 원격으로 작업'
description: 이 페이지에서는 COVID-19 전염병으로 인해 Azure Bastion을 활용하여 원격으로 작업을 활성화하는 방법에 대해 설명합니다.
services: bastion
author: mialdrid
ms.service: bastion
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: mialdrid
ms.openlocfilehash: 182195190fed70b46185f98f595de6b6c32bbffe
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/02/2020
ms.locfileid: "80619391"
---
# <a name="working-remotely-using-azure-bastion"></a>Azure 요새를 사용하여 원격으로 작업

Azure Bastion은 인터넷 연결이 있는 사용자가 Azure 가상 시스템에 액세스할 수 있도록 하여 원격 작업 시나리오를 지원하는 데 중추적인 역할을 합니다. 특히 IT 관리자는 언제 어디서나 Azure에서 실행되는 응용 프로그램을 관리할 수 있습니다.

>[!NOTE]
>이 문서에서는 Azure Bastion, Azure, Microsoft 네트워크 및 Azure 파트너 에코시스템을 활용하여 원격으로 작업하고 COVID-19 위기로 인해 직면하고 있는 네트워크 문제를 완화하는 방법에 대해 설명합니다.
>

## <a name="securely-access-virtual-machines"></a>가상 머신에 안전하게 액세스

특히 Azure Bastion은 공용 IP 주소를 사용하지 않고 Azure 포털에서 직접 Azure 가상 네트워크 내의 가상 시스템에 안전하고 원활한 RDP/SSH 연결을 제공합니다. Azure 요새 아키텍처 및 주요 기능에 대한 자세한 내용은 [Azure 요새입니다.](bastion-overview.md)

Azure Bastion은 가상 네트워크당 배포되므로 기업은 Azure Bastion을 구성하고 관리하여 Azure 가상 네트워크 내의 가상 시스템에 대한 원격 사용자 액세스를 신속하게 지원할 수 있습니다. Azure 요새를 만들고 관리하는 방법에 대한 지침은 [대위 호스트 만들기를](bastion-create-host-portal.md)참조하십시오.

## <a name="next-steps"></a>다음 단계

* [Azure 포털,](bastion-create-host-portal.md) [PowerShell](bastion-create-host-powershell.md)또는 Azure CLI를 사용하여 Azure 요새를 구성합니다.

* 자세한 내용은 [배스션 FAQ를](bastion-faq.md) 참조하십시오.
