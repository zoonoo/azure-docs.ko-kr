---
title: 공유 리소스를 사용 하 여 랩을 만드는 방법 | Azure Lab Services
description: 학생 간에 리소스를 공유 해야 하는 랩을 만드는 방법을 알아봅니다.
author: emaher
ms.topic: article
ms.date: 06/26/2020
ms.author: enewman
ms.openlocfilehash: 9cb5698f95aa220208fb02a35a52ff5363a173ac
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85443369"
---
# <a name="how-to-create-a-lab-with-a-shared-resource-in-azure-lab-services"></a>Azure Lab Services에서 공유 리소스를 사용 하 여 랩을 만드는 방법

경우에 따라 교실 랩을 만들 때 랩에서 모든 학생 들 사이에 공유 해야 하는 리소스가 있을 수 있습니다.  예를 들어 데이터베이스 클래스에 대 한 라이선스 서버 또는 SQL Server 있습니다.  이 문서에서는 랩에 대 한 공유 리소스를 사용 하도록 설정 하는 단계에 대해 설명 합니다.  해당 공유 리소스에 대 한 액세스를 제한 하는 방법에 대해서도 설명 합니다.

## <a name="architecture"></a>Architecture

아래 다이어그램에 표시 된 것 처럼 랩을 포함 하는 랩 계정이 있습니다.  랩 계정에는 vnet 피어 링 설정이 있으므로 랩의 가상 네트워크가 공유 리소스의 네트워크에 연결 됩니다.  아래 다이어그램에는 IP 범위가 겹치지 않는 두 개의 가상 네트워크가 있습니다.  이러한 IP 범위는 예제 범위에 불과합니다.  또한 공유 리소스 가상 네트워크는 랩 계정과 동일한 구독에 있습니다.

![공유 리소스 아키텍처를 사용 하는 랩 서비스](./media/how-to-create-a-lab-with-shared-resource/shared-resource-architecture.png)

## <a name="setup-shared-resource"></a>공유 리소스 설정

랩을 만들기 전에 공유 리소스에 대 한 가상 네트워크를 만들어야 합니다.  가상 네트워크를 만드는 방법에 대 한 자세한 내용은 [가상 네트워크 만들기](../virtual-network/quick-create-portal.md)를 참조 하세요.  가상 네트워크 범위를 계획 하 여 랩 컴퓨터의 ip 주소와 겹치지 않도록 하는 것이 중요 합니다.  네트워크를 계획 하는 방법에 대 한 자세한 내용은 [가상 네트워크 계획](../virtual-network/virtual-network-vnet-plan-design-arm.md) 문서를 참조 하세요. 이 예제에서 공유 리소스는 범위가 10.2.0.0/16 인 가상 네트워크에 있습니다.  아직 수행 하지 않은 경우 공유 리소스를 저장할 [서브넷을 만듭니다](../virtual-network/virtual-network-manage-subnet.md#add-a-subnet) .  이 예제에서는 10.2.0.0/24 범위를 사용 하지만 범위는 네트워크 요구 사항에 따라 다를 수 있습니다.

공유 리소스는 가상 머신 또는 Azure 제공 서비스에서 실행 되는 소프트웨어가 될 수 있습니다. 공유 리소스는 개인 IP 주소를 통해 사용할 수 있어야 합니다.  개인 IP를 통해서만 공유 리소스를 사용할 수 있도록 설정 하면 해당 공유 리소스에 대 한 액세스를 제한 합니다.

또한이 다이어그램은 학생 VM에서 들어오는 트래픽을 제한 하는 데 사용할 수 있는 NSG (네트워크 보안 그룹)를 보여 줍니다.  예를 들어 학생 VM의 IP 주소에서 트래픽이 하나의 공유 리소스에만 액세스할 수 있도록 하는 보안 규칙을 작성할 수 있습니다.  보안 규칙을 설정 하는 방법에 대 한 자세한 내용은 [네트워크 보안 그룹 관리](../virtual-network/manage-network-security-group.md#work-with-security-rules)를 참조 하세요. 특정 랩에 대 한 공유 리소스에 대 한 액세스를 제한 하려는 경우 랩 [계정의 랩 설정](manage-labs.md#view-labs-in-a-lab-account) 에서 랩에 대 한 ip 주소를 가져오고 해당 ip 주소에서의 액세스만 허용 하도록 인바운드 규칙을 설정 합니다.  해당 IP 주소에 대해 49152에서 65535 포트를 허용 하는 것을 잊지 마세요.  필요에 따라 [가상 컴퓨터 풀 페이지](how-to-set-virtual-machine-passwords.md)를 사용 하 여 학생의 vm에 대 한 개인 IP 주소를 찾을 수 있습니다.

공유 리소스가 필요한 소프트웨어를 실행 하는 Azure 가상 머신 인 경우 가상 머신에 대 한 기본 방화벽 규칙을 수정 해야 할 수 있습니다.

## <a name="lab-account"></a>랩 계정

공유 리소스를 사용 하려면 랩 계정이 [피어 링 가상 네트워크](how-to-connect-peer-virtual-network.md)를 사용 하도록 설정 되어 있어야 합니다.  이 경우 공유 리소스를 보유 하는 가상 네트워크에 대 한 피어 링이 제공 됩니다.

>[!WARNING]
>랩 계정이 공유 리소스 가상 네트워크에 피어 링 된 **후** 클래스의 랩을 만들어야 합니다.  
템플릿 컴퓨터

랩 계정이 가상 네트워크에 피어 링 되 면 이제 템플릿 컴퓨터가 공유 리소스에 액세스할 수 있어야 합니다.  액세스 되는 공유 리소스에 따라 방화벽 규칙을 업데이트 해야 할 수 있습니다.
