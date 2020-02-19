---
title: Azure 방화벽 강제 터널링
description: 추가 처리를 위해 인터넷 바인딩된 트래픽을 추가 방화벽 또는 네트워크 가상 어플라이언스로 라우팅하도록 강제 터널링을 구성할 수 있습니다.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 02/18/2020
ms.author: victorh
ms.openlocfilehash: 4093f91e55272a32ce7df4a78e2ee8b3ebed5fde
ms.sourcegitcommit: 6e87ddc3cc961945c2269b4c0c6edd39ea6a5414
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/18/2020
ms.locfileid: "77444473"
---
# <a name="azure-firewall-forced-tunneling-preview"></a>Azure 방화벽 강제 터널링 (미리 보기)

모든 인터넷 바인딩된 트래픽을 인터넷으로 직접 이동 하는 대신 지정 된 다음 홉으로 라우팅하도록 Azure 방화벽을 구성할 수 있습니다. 예를 들어 온-프레미스에 지 방화벽이 나 기타 NVA (네트워크 가상 어플라이언스)를 통해 네트워크 트래픽을 인터넷에 전달 하기 전에 처리할 수 있습니다.

> [!IMPORTANT]
> Azure 방화벽 강제 터널링은 현재 공개 미리 보기로 제공 됩니다.
>
> 이 공개 미리 보기는 Service Level Agreement(서비스 수준 약정)없이 제공되므로 프로덕션 워크로드에 사용하지 말아야 합니다. 특정 기능이 지원되지 않거나, 기능이 제한되거나, 일부 Azure 위치에 제공되지 않을 수 있습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

기본적으로 강제 터널링은 Azure 방화벽에서 모든 아웃 바운드 Azure 종속성이 충족 되도록 허용 되지 않습니다. *AzureFirewallSubnet* 의 udr (사용자 정의 경로) 구성은 인터넷으로 직접 이동 하지 않는 기본 경로를 사용 합니다.

## <a name="forced-tunneling-configuration"></a>강제 터널링 구성

강제 터널링을 지원 하기 위해 서비스 관리 트래픽은 고객 트래픽과 구분 됩니다. *AzureFirewallManagementSubnet* 이라는 추가 전용 서브넷은 연결 된 공용 IP 주소와 함께 필요 합니다. 이 서브넷에서 허용 되는 유일한 경로는 인터넷에 대 한 기본 경로 이며 BGP 경로 전파를 사용 하지 않도록 설정 해야 합니다.

BGP를 통해 온-프레미스로의 트래픽을 강제로 적용 하는 기본 경로를 알린 경우 방화벽을 배포 하기 전에 *AzureFirewallSubnet* 및 *AzureFirewallManagementSubnet* 를 만들고 기본 경로를 사용 하 여 udr을 사용 하 고 가상 네트워크 게이트웨이 경로 전파를 사용 하지 않도록 설정 해야 합니다.

이 구성에서 *AzureFirewallSubnet* 는 이제 온-프레미스 방화벽 또는 nva에 대 한 경로를 포함 하 여 인터넷에 전달 되기 전에 트래픽을 처리할 수 있습니다. 이 서브넷에서 가상 네트워크 게이트웨이 경로 전파가 사용 하도록 설정 된 경우 BGP를 통해 이러한 경로를 *AzureFirewallSubnet* 에 게시할 수도 있습니다.

강제 터널링을 지원 하도록 Azure 방화벽을 구성 하면 구성을 실행 취소할 수 없습니다. 방화벽에서 다른 모든 IP 구성을 제거 하면 관리 IP 구성도 제거 되 고 방화벽의 할당이 취소 됩니다. 관리 IP 구성에 할당 된 공용 IP 주소를 제거할 수 없지만 다른 공용 IP 주소를 할당할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [자습서: Azure Portal을 사용 하 여 하이브리드 네트워크에서 Azure 방화벽 배포 및 구성](tutorial-hybrid-portal.md)