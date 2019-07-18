---
title: 포함 파일
description: 포함 파일
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: include
ms.date: 06/17/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 29ab9b3c33aae6005510c34b207c7f87714149e5
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/05/2019
ms.locfileid: "67608272"
---
### <a name="preview"></a>공개 미리 보기에 참여하려면 어떻게 할까요?

공개 미리 보기에 참여하려면 등록해야 합니다. [이 문서](../articles/bastion/bastion-create-host-portal.md)의 단계에 따라 새 Azure Bastion 리소스를 만드세요. 현재는 이 서비스에 액세스하여 사용하려면 일반 Azure Portal이 아닌 [Azure Portal - 미리 보기](https://aka.ms/BastionHost)를 사용해야 합니다.

### <a name="regions"></a>미리 보기 기간에 이용 가능한 지역은 어디인가요?

[Azure Portal - 미리 보기 링크](https://aka.ms/BastionHost)를 통해 다음 미리 보기 지역에서 Bastion 리소스를 배포하고 사용할 수 있습니다.

[!INCLUDE [region](bastion-regions-include.md)]

### <a name="portal"></a>Azure Portal에서 Bastion 리소스를 찾을 수 없습니다. 어떻게 해야 하나요?

일반 Azure Portal이 아닌 [Azure Portal - 미리 보기 링크](https://aka.ms/BastionHost)를 사용 중인지 확인합니다.

### <a name="publicip"></a>내 가상 머신에서 공용 IP가 필요한가요?

Azure Bastion 서비스를 사용하여 연결하는 Azure Virtual Machine에는 공용 IP가 필요 없습니다. Bastion 서비스는 가상 네트워크 내부에서 가상 머신의 개인 IP를 통해 가상 머신에 대한 RDP/SSH 세션/연결을 설정합니다.

### <a name="rdpssh"></a>RDP 또는 SSH 클라이언트가 필요하나요?

Azure Portal에서 Azure 가상 머신에 RDP/SSH로 액세스할 때 RDP 또는 SSH 클라이언트가 필요하지 않습니다. 포털의 미리 보기 플라이트에 액세스하려면 [Azure Portal - 미리 보기 링크](https://aka.ms/BastionHost)를 사용하세요. 이 링크를 통해 브라우저에서 직접 가상 머신에 RDP/SSH로 액세스할 수 있습니다.

### <a name="agent"></a>Azure 가상 머신에서 실행되는 에이전트가 필요하나요?

브라우저 또는 Azure 가상 머신에 에이전트나 소프트웨어를 설치할 필요가 없습니다. Bastion 서비스는 에이전트 없이 사용할 수 있으며 RDP/SSH용 추가 소프트웨어가 필요하지 않습니다.

### <a name="browsers"></a>어떤 브라우저가 지원되나요?

공개 미리 보기 기간에는 Windows에서 Microsoft Edge 브라우저 또는 Google Chrome을 사용하세요. Apple Mac의 경우 Google Chrome 브라우저를 사용하세요. Microsoft Edge Chromium도 Windows와 Mac에서 모두 지원됩니다.

### <a name="roles"></a>가상 머신에 액세스할 때 꼭 필요한 역할이 있나요?

연결하려면 다음 역할이 필요합니다.

* 가상 머신에 대한 읽기 권한자 역할
* 가상 머신의 개인 IP를 사용하는 NIC에 대한 읽기 권한자 역할
* Azure Bastion 리소스에 대한 읽기 권한자 역할

### <a name="previewbill"></a>가격 책정 - 이 미리 보기에 참여하면 요금이 청구되나요?

공개 미리 보기 기간에 부분적으로만 요금이 청구됩니다. 단, 배포에 연결된 SLA는 없습니다. 자세한 내용은 [가격 책정 페이지](https://aka.ms/BastionHostPricing)를 참조하세요.

### <a name="previewbill"></a>왜 Bastion 세션이 시작하기 전에 "세션이 만료되었습니다." 오류 메시지를 받나요?

세션은 Azure Portal에서만 시작되어야 합니다. Azure Portal에 로그인하고 세션을 다시 시작합니다. 다른 브라우저 세션이나 탭에서 URL로 직접 이동하는 경우 이 오류가 예상됩니다. 세션이 더 안전한지 Azure Portal을 통해서만 세션에 액세스할 수 있는지 확인하도록 돕습니다.
