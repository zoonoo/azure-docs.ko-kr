---
title: '요새를 사용 하 여 원격으로 작업: Azure 방호'
description: 이 페이지에서는 Azure 방호를 활용 하 여 COVID-19 전염병로 원격 작업을 사용 하도록 설정 하는 방법을 설명 합니다.
services: bastion
author: mialdrid
ms.service: bastion
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: mialdrid
ms.openlocfilehash: 515a6f7175aee23c6f720258b7fdaefe76b3b5d6
ms.sourcegitcommit: a92fbc09b859941ed64128db6ff72b7a7bcec6ab
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2020
ms.locfileid: "92077952"
---
# <a name="working-remotely-using-azure-bastion"></a>Azure 방호를 사용 하 여 원격으로 작업

Azure 방호는 인터넷 연결을 통해 사용자가 Azure virtual machines에 액세스할 수 있도록 함으로써 원격 작업 시나리오를 지원 하기 위해 pivotal 역할을 합니다. 특히 IT 관리자는 Azure에서 실행 되는 응용 프로그램을 언제 어디서 나 어디에서 나 관리할 수 있습니다.

>[!NOTE]
>이 문서에서는 Azure 방호, Azure, Microsoft 네트워크 및 Azure 파트너 에코 시스템을 활용 하 여 원격으로 작업 하 고 COVID-19 위기 때문에 직면 하 고 있는 네트워크 문제를 완화 하는 방법을 설명 합니다.
>

## <a name="securely-access-virtual-machines"></a>가상 컴퓨터에 안전 하 게 액세스

특히, Azure 방호는 공용 IP 주소를 사용 하지 않고 Azure Portal에 직접 Azure virtual network 내에서 가상 머신에 대 한 안전 하 고 원활한 RDP/SSH 연결을 제공 합니다. Azure 방호 아키텍처 및 주요 기능에 대 한 자세한 내용은 [Azure 방호 이란?](bastion-overview.md)을 참조 하세요.

Azure 방호는 가상 네트워크 별로 배포 되며, 회사는 azure 가상 네트워크 내에서 가상 머신에 대 한 원격 사용자 액세스를 빠르게 지원할 수 있도록 Azure 방호를 구성 하 고 관리할 수 있습니다. Azure 방호를 만들고 관리 하는 방법에 대 한 지침은 [요새 호스트 만들기](./tutorial-create-host-portal.md)를 참조 하세요.

## <a name="next-steps"></a>다음 단계

* [Azure Portal](./tutorial-create-host-portal.md), [PowerShell](bastion-create-host-powershell.md)또는 Azure CLI를 사용 하 여 Azure 방호를 구성 합니다.

* 추가 정보는 [요새 FAQ](bastion-faq.md) 를 참조 하세요.