---
title: Express 경로를 사용 하 여 Azure 가상 네트워크를 AVS에 연결
description: Azure virtual network와 AVS 환경 간의 연결에 대 한 피어 링 정보를 얻는 방법에 대해 설명 합니다.
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 1ff11bbafe6f9057ce70c799e0437691d89ccb40
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/05/2020
ms.locfileid: "77014405"
---
# <a name="connect-azure-virtual-network-to-avs-using-expressroute"></a>Express 경로를 사용 하 여 Azure 가상 네트워크를 AVS에 연결

Azure virtual network 및 Azure 리소스에 대해 AVS 사설 클라우드 네트워크를 확장할 수 있습니다. Express 경로 연결을 사용 하면 Azure 구독에서 실행 중인 리소스를 AVS 사설 클라우드에서 액세스할 수 있습니다.

## <a name="request-authorization-key"></a>요청 인증 키

Azure 가상 네트워크와 Azure 가상 네트워크 간의 Express 경로 연결에는 인증 키가 필요 합니다. 키를 얻으려면 <a href="https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest" target="_blank">지원</a>되는 티켓을 파일에 입력 합니다. 요청에서 다음 정보를 사용 합니다.

* 문제 유형: **기술**
* 구독: **AVS 서비스가 배포 된 구독을 선택 합니다** .
* 서비스: **VMware 솔루션 (AVS)**
* 문제 유형: **서비스 요청**
* 문제 하위 유형: **AZURE VNET 연결에 대 한 인증 키**
* 주제: **AZURE VNET 연결에 대 한 인증 키 요청**

## <a name="get-peering-information-from-avs-portal"></a>AVS 포털에서 피어 링 정보 가져오기

연결을 설정 하려면 Azure virtual network와 AVS 환경 간의 연결을 설정 해야 합니다. 절차의 일부로 피어 회로 URI 및 권한 부여 키를 제공 해야 합니다. [AVS 포털](access-cloudsimple-portal.md)에서 URI 및 권한 부여 키를 가져옵니다. 측면 메뉴에서 **네트워크** 를 선택한 다음 **Azure 네트워크 연결**을 선택 합니다. 또는 측면 메뉴에서 **계정** 을 선택 하 고 **Azure 네트워크 연결**을 선택 합니다.

*복사* 아이콘을 사용 하 여 각 지역에 대 한 인증 키와 피어 회로 URI를 복사 합니다. 연결 하려는 각 AVS 영역에 대해 다음을 수행 합니다.

1. **복사** 를 클릭 하 여 URI를 복사 합니다. Azure Portal에 추가할 수 있는 파일에 붙여넣습니다. 
2. **복사** 를 클릭 하 여 권한 부여 키를 복사 하 고 파일에도 붙여넣습니다.

**사용 가능한** 상태의 인증 키 및 피어 회로 URI를 복사 합니다. **사용** 된 상태는 키가 이미 가상 네트워크 연결을 만드는 데 사용 되었음을 나타냅니다.

![Virtual Network 연결 페이지](media/virtual-network-connection.png)

Azure virtual network를 AVS로 설정 하는 방법에 대 한 자세한 내용은 Express 경로를 [사용 하 여 azure 가상 네트워크에 Avs 사설 클라우드 환경 연결](azure-expressroute-connection.md)을 참조 하세요.

## <a name="next-steps"></a>다음 단계

* [Azure 가상 네트워크에서 AVS 사설 클라우드에 연결](azure-expressroute-connection.md)
* [Azure Express 경로를 사용 하 여 온-프레미스 네트워크에 연결](on-premises-connection.md)
