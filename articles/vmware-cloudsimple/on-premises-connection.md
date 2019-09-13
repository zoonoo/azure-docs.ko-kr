---
title: Express 경로를 사용 하 여 CloudSimple 온-프레미스 연결을 통한 Azure VMware 솔루션
description: CloudSimple 영역 네트워크에서 Express 경로를 사용 하 여 온-프레미스 연결을 요청 하는 방법을 설명 합니다.
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 0dd5ede110255b6e53bbc397e683e66b3beffc65
ms.sourcegitcommit: d70c74e11fa95f70077620b4613bb35d9bf78484
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/11/2019
ms.locfileid: "70910542"
---
# <a name="connect-from-on-premises-to-cloudsimple-using-expressroute"></a>Express 경로를 사용 하 여 온-프레미스에서 CloudSimple로 연결

외부 위치 (예: 온-프레미스)에서 Azure로의 Azure Express 경로 연결이 이미 있는 경우 CloudSimple 환경에 연결할 수 있습니다. 두 개의 Express 경로 회로를 서로 연결할 수 있도록 하는 Azure 기능을 통해이 작업을 수행할 수 있습니다. 이 방법은 두 환경 간에 안전 하 고, 대역폭이 높고, 대기 시간이 짧은 연결을 설정 합니다.

[![온-프레미스 Express 경로 연결-Global Reach](media/cloudsimple-global-reach-connection.png)](media/cloudsimple-global-reach-connection.png)

## <a name="before-you-begin"></a>시작하기 전 주의 사항

온-프레미스에서 Global Reach 연결을 설정 하려면 **/29** 네트워크 주소 블록이 필요 합니다.  /29 주소 공간은 Express 경로 회로 간의 전송 네트워크에 사용 됩니다.  전송 네트워크는 Azure 가상 네트워크, 온-프레미스 네트워크 또는 CloudSimple 사설 클라우드 네트워크와 겹치면 안 됩니다.

## <a name="prerequisites"></a>필수 구성 요소

* 회로와 CloudSimple 사설 클라우드 네트워크 간의 연결을 설정 하려면 Azure Express 경로 회로가 필요 합니다.
* 사용자는 Express 경로 회로에서 인증 키를 만들 수 있는 권한이 필요 합니다.

## <a name="scenarios"></a>시나리오

사설 클라우드 네트워크에 온-프레미스 네트워크를 연결 하면 다음과 같은 시나리오를 비롯 하 여 다양 한 방법으로 사설 클라우드를 사용할 수 있습니다.

* 사이트 간 VPN 연결을 만들지 않고 사설 클라우드 네트워크에 액세스 합니다.
* 사설 클라우드의 id 원본으로 온-프레미스 Active Directory을 사용 합니다.
* 온-프레미스로 실행 되는 가상 컴퓨터를 사설 클라우드로 마이그레이션합니다.
* 재해 복구 솔루션의 일부로 사설 클라우드를 사용 합니다.
* 사설 클라우드 워크 로드 Vm에서 온-프레미스 리소스를 사용 합니다.

## <a name="connecting-expressroute-circuits"></a>Express 경로 회로 연결

Express 경로 연결을 설정 하려면 Express 경로 회로에 대 한 권한 부여를 만들고 CloudSimple에 권한 부여 정보를 제공 해야 합니다.


### <a name="create-expressroute-authorization"></a>Express 경로 권한 부여 만들기

1. Azure 포털에 로그인합니다.

2. 위쪽 검색 창에서 **express 경로 회로** 를 검색 하 고 **서비스**아래에서 **express 경로 회로** 를 클릭 합니다.
    [![Express 경로 회로](media/azure-expressroute-transit-search.png)](media/azure-expressroute-transit-search.png)

3. CloudSimple 네트워크에 연결 하려는 Express 경로 회로를 선택 합니다.

4. Express 경로 페이지 **에서 권한 부여를 클릭 하**고 권한 부여에 대 한 이름을 입력 한 다음 **저장**을 클릭 합니다.
    [![Express 경로 회로 권한 부여](media/azure-expressroute-transit-authorizations.png)](media/azure-expressroute-transit-authorizations.png)

5. 복사 아이콘을 클릭 하 여 리소스 ID와 권한 부여 키를 복사 합니다. ID와 키를 텍스트 파일에 붙여 넣습니다.
    [![Express 경로 회로 권한 부여 복사](media/azure-expressroute-transit-authorization-copy.png)](media/azure-expressroute-transit-authorization-copy.png)

    > [!IMPORTANT]
    > **리소스 ID** 는 UI에서 복사 해야 하며 지원 하도록 제공할 때 형식 ```/subscriptions/<subscription-ID>/resourceGroups/<resource-group-name>/providers/Microsoft.Network/expressRouteCircuits/<express-route-circuit-name>``` 이어야 합니다.

6. 만들 연결을 <a href="https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest" target="_blank">지 원하는</a> 티켓을 파일에 포함 합니다.
    * 문제 유형: **기술**
    * 구독: **CloudSimple 서비스가 배포 된 구독**
    * 서비스: **CloudSimple 별 VMware 솔루션**
    * 문제 유형: **서비스 요청**
    * 문제 하위 유형: **온-프레미스에 대 한 Express 경로 연결 만들기**
    * 세부 정보 창에서 복사 하 여 저장 한 리소스 ID와 권한 부여 키를 제공 합니다.
    * 전송 네트워크에 대 한/29 네트워크 주소 공간을 제공 합니다.
    * Express 경로를 통해 기본 경로를 전송 하 고 있나요?
    * 사설 클라우드 트래픽이 Express 경로를 통해 전송 되는 기본 경로를 사용 해야 하나요?

    > [!IMPORTANT]
    > 기본 경로를 전송 하면 온-프레미스 인터넷 연결을 사용 하 여 사설 클라우드의 모든 인터넷 트래픽을 보낼 수 있습니다.  사설 클라우드에 구성 된 기본 경로를 사용 하지 않도록 설정 하 고 온-프레미스 연결 기본 경로를 사용 하려면 지원 티켓에 세부 정보를 제공 합니다.

## <a name="next-steps"></a>다음 단계

* [Azure 네트워크 연결에 대 한 자세한 정보](cloudsimple-azure-network-connection.md)  
