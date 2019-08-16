---
title: Express 경로를 사용 하 여 Azure 가상 네트워크를 CloudSimple에 연결
description: Azure 가상 네트워크와 CloudSimple 환경 간의 연결에 대 한 피어 링 정보를 얻는 방법에 대해 설명 합니다.
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 8ba2d2fd9c943fe55e82956d022f6ba9840a550f
ms.sourcegitcommit: 040abc24f031ac9d4d44dbdd832e5d99b34a8c61
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/16/2019
ms.locfileid: "69536600"
---
# <a name="connect-azure-virtual-network-to-cloudsimple-using-expressroute"></a>Express 경로를 사용 하 여 Azure 가상 네트워크를 CloudSimple에 연결

사설 클라우드 네트워크를 Azure 가상 네트워크 및 Azure 리소스로 확장할 수 있습니다. Express 경로 연결을 사용 하면 사설 클라우드에서 Azure 구독에서 실행 중인 리소스에 액세스할 수 있습니다.

## <a name="request-authorization-key"></a>요청 인증 키

사설 클라우드와 Azure virtual network 간의 Express 경로 연결에는 인증 키가 필요 합니다. 키를 얻으려면 <a href="https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest" target="_blank">지원</a>되는 티켓을 파일에 입력 합니다.  요청에서 다음 정보를 사용 합니다.

* 문제 유형: **기술**
* 구독: CloudSimple 서비스가 배포 된 구독을 선택 합니다. * *
* 서비스: **CloudSimple 별 VMware 솔루션**
* 문제 유형: **서비스 요청**
* 문제 하위 유형: **Azure VNET 연결에 대 한 인증 키**
* 제목: **Azure VNET 연결에 대 한 인증 키 요청**

## <a name="obtain-peering-information-for-azure-virtual-network-to-cloudsimple-connection"></a>Azure virtual network에 대 한 피어 링 정보를 CloudSimple 연결로 가져오기

연결을 설정 하려면 Azure virtual network와 CloudSimple 환경 간의 링크를 설정 해야 합니다.  절차의 일부로 피어 회로 URI 및 권한 부여 키를 제공 해야 합니다. [Cloudsimple 포털](access-cloudsimple-portal.md)에서 URI 및 권한 부여 키를 가져옵니다.  측면 메뉴에서 **네트워크** 를 선택한 다음 **Azure 네트워크 연결**을 선택 합니다. 또는 측면 메뉴에서 **계정** 을 선택 하 고 **Azure 네트워크 연결**을 선택 합니다.

피어 회로 URI와 각 지역의 인증 키에 대 한 복사 아이콘을 확인 합니다. 연결 하려는 각 사설 클라우드에 대해 다음을 수행 합니다.

* **복사** 를 클릭 하 여 URI를 복사 합니다. Azure Portal에 추가할 수 있는 파일에 붙여넣습니다.  
* **복사** 를 클릭 하 여 권한 부여 키를 복사 하 고 파일에도 붙여넣습니다.

![Virtual Network 연결 페이지](media/network-virt-conn-page.png)

Azure virtual network를 CloudSimple 링크로 설정 하는 방법에 대 한 자세한 내용은 Express 경로를 [사용 하 여 Cloudsimple 사설 클라우드 환경을 Azure virtual network에 연결](azure-expressroute-connection.md)을 참조 하세요.
