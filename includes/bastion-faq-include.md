---
title: 포함 파일
description: 포함 파일
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: include
ms.date: 10/15/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 4f49220da5d996615c9f8ef7cad2b6c6793866b7
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73466260"
---
### <a name="regions"></a>이용 가능한 지역은 어디인가요?

[!INCLUDE [region](bastion-regions-include.md)]

### <a name="publicip"></a>내 가상 머신에서 공용 IP가 필요한가요?

Azure Bastion 서비스를 사용하여 연결하는 Azure Virtual Machine에는 공용 IP가 필요 없습니다. Bastion 서비스는 가상 네트워크 내부에서 가상 머신의 개인 IP를 통해 가상 머신에 대한 RDP/SSH 세션/연결을 설정합니다.

### <a name="rdpssh"></a>RDP 또는 SSH 클라이언트가 필요하나요?

Azure Portal에서 Azure 가상 머신에 RDP/SSH로 액세스할 때 RDP 또는 SSH 클라이언트가 필요하지 않습니다. [Azure Portal](https://portal.azure.com)을 사용하여 브라우저에서 직접 가상 머신에 대한RDP/SSH 액세스를 가져올 수 있습니다.

### <a name="agent"></a>Azure 가상 머신에서 실행되는 에이전트가 필요하나요?

브라우저 또는 Azure 가상 머신에 에이전트나 소프트웨어를 설치할 필요가 없습니다. Bastion 서비스는 에이전트 없이 사용할 수 있으며 RDP/SSH용 추가 소프트웨어가 필요하지 않습니다.

### <a name="browsers"></a>어떤 브라우저가 지원되나요?

Windows에서 Microsoft Edge 브라우저 또는 Chrome을 사용합니다. Apple Mac의 경우 Google Chrome 브라우저를 사용하세요. Microsoft Edge Chromium도 Windows와 Mac에서 모두 지원됩니다.

### <a name="roles"></a>가상 머신에 액세스할 때 꼭 필요한 역할이 있나요?

연결하려면 다음 역할이 필요합니다.

* 가상 머신에 대한 읽기 권한자 역할
* 가상 머신의 개인 IP를 사용하는 NIC에 대한 읽기 권한자 역할
* Azure Bastion 리소스에 대한 읽기 권한자 역할

### <a name="pricingpage"></a>가격 책정이란?

자세한 내용은 [가격 책정 페이지](https://aka.ms/BastionHostPricing)를 참조하세요.

### <a name="session"></a>왜 Bastion 세션이 시작하기 전에 "세션이 만료되었습니다." 오류 메시지를 받나요?

세션은 Azure Portal에서만 시작되어야 합니다. Azure Portal에 로그인하고 세션을 다시 시작합니다. 다른 브라우저 세션이나 탭에서 URL로 직접 이동하는 경우 이 오류가 예상됩니다. 세션이 더 안전한지 Azure Portal을 통해서만 세션에 액세스할 수 있는지 확인하도록 돕습니다.
