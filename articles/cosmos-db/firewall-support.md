---
title: "Azure Cosmos DB 방화벽 지원 및 IP 액세스 제어 | Microsoft Docs"
description: "Azure Cosmos DB 데이터베이스 계정에서 방화벽을 지원하도록 IP 액세스 제어 정책을 사용하는 방법을 알아봅니다."
keywords: "IP 액세스 제어, 방화벽 지원"
services: cosmos-db
author: shahankur11
manager: jhubbard
editor: 
tags: azure-resource-manager
documentationcenter: 
ms.assetid: c1b9ede0-ed93-411a-ac9a-62c113a8e887
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/22/2017
ms.author: ankshah
ms.translationtype: Human Translation
ms.sourcegitcommit: a643f139be40b9b11f865d528622bafbe7dec939
ms.openlocfilehash: 9a17d9fe0c252e2327ffbd75fde38d1f97ce3cc9
ms.contentlocale: ko-kr
ms.lasthandoff: 05/31/2017


---
# <a name="azure-cosmos-db-firewall-support"></a>Azure Cosmos DB 방화벽 지원
Azure Cosmos DB 데이터베이스 계정에 저장된 데이터를 보호하기 위해 Azure Cosmos DB는 강력한 HMAC(해시 기반 메시지 인증 코드)를 활용하는 암호 기반 [권한 부여 모델](https://msdn.microsoft.com/library/azure/dn783368.aspx)을 지원했습니다. 이제 Azure Cosmos DB는 암호 기반 권한 부여 모델 외에도 인바운드 방화벽 지원에 대한 정책 중심 IP 기반 액세스 제어를 지원합니다. 이 모델은 기존 데이터베이스 시스템의 방화벽 규칙과 매우 유사하며 Azure Cosmos DB 데이터베이스 계정에 추가 보안 수준을 제공합니다. 이제 이 모델에서는 승인된 컴퓨터 및/또는 클라우드 서비스에서만 액세스할 수 있도록 Azure Cosmos DB 데이터베이스 계정을 구성할 수 있습니다. 이러한 승인된 컴퓨터 및 서비스에서 Azure Cosmos DB 리소스에 액세스하려면 여전히 호출자가 유효한 권한 부여 토큰을 제공해야 합니다.

## <a name="ip-access-control-overview"></a>IP 액세스 제어 개요
기본적으로 요청에 유효한 권한 부여 토큰이 포함되어 있으면 공용 인터넷에서 Azure Cosmos DB 데이터베이스 계정에 액세스할 수 있습니다. IP 정책 기반 액세스 제어를 구성하려면 사용자가 지정된 데이터베이스 계정에 대해 허용된 클라이언트 IP 목록으로 포함할 IP 주소 또는 IP 주소 범위를 CIDR 형식으로 제공해야 합니다. 이 구성이 적용되면 이 주소 이외의 컴퓨터에서 보내는 모든 요청을 서버에서 차단합니다.  IP 기반 액세스 제어의 연결 처리 흐름은 다음 다이어그램에 설명되어 있습니다.

![IP 기반 액세스 제어의 연결 프로세스를 보여주는 다이어그램](./media/firewall-support/firewall-support-flow.png)

## <a name="connections-from-cloud-services"></a>클라우드 서비스에서 연결
Azure에서 클라우드 서비스는 Azure Cosmos DB를 사용하여 중간 계층 서비스 논리를 호스팅하는 매우 일반적인 방법입니다. 클라우드 서비스에서 Azure Cosmos DB 데이터베이스 계정에 액세스할 수 있게 하려면 [IP 액세스 제어 정책을 구성](#configure-ip-policy)하여 클라우드 서비스의 공용 IP 주소를 Azure Cosmos DB 데이터베이스 계정에 연결된 허용된 IP 주소 목록에 추가해야 합니다.  이렇게 하면 클라우드 서비스의 모든 역할 인스턴스가 Azure Cosmos DB 데이터베이스 계정에 액세스할 수 있습니다. 다음 스크린샷처럼 Azure Portal에서 클라우드 서비스의 IP 주소를 검색할 수 있습니다.

![Azure Portal에 표시된 클라우드 서비스의 공용 IP 주소를 보여주는 스크린샷](./media/firewall-support/public-ip-addresses.png)

추가 역할 인스턴스를 추가하여 클라우드 서비스를 규모 확장할 때 이러한 새 인스턴스는 자동으로 Azure Cosmos DB 데이터베이스 계정에 대한 액세스 권한을 갖습니다. 동일한 클라우드 서비스에 포함되기 때문입니다.

## <a name="connections-from-virtual-machines"></a>가상 컴퓨터에서 연결
[가상 컴퓨터](https://azure.microsoft.com/services/virtual-machines/) 또는 [가상 컴퓨터 확장 집합](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md) 역시 Azure Cosmos DB를 사용하여 중간 계층 서비스를 호스트하는 데 사용할 수 있습니다.  가상 컴퓨터에서 액세스할 수 있도록 Azure Cosmos DB 데이터베이스 계정을 구성하려면 [IP 액세스 제어 정책을 구성](#configure-ip-policy)하여 가상 컴퓨터 및/또는 가상 컴퓨터 확장 집합의 공용 IP 주소를 Azure Cosmos DB 데이터베이스 계정에 허용되는 IP 주소 중 하나로 구성해야 합니다. 다음 스크린샷처럼 Azure Portal에서 가상 컴퓨터의 IP 주소를 검색할 수 있습니다.

![Azure Portal에 표시된 가상 컴퓨터의 공용 IP 주소를 보여주는 스크린샷](./media/firewall-support/public-ip-addresses-dns.png)

그룹에 추가 가상 컴퓨터 인스턴스를 추가하면 Azure Cosmos DB 데이터베이스 계정에 대한 액세스 권한이 자동으로 부여됩니다.

## <a name="connections-from-the-internet"></a>인터넷에서 연결
인터넷에 있는 컴퓨터에서 Azure Cosmos DB 데이터베이스 계정에 액세스하려면 컴퓨터의 클라이언트 IP 주소 또는 IP 주소 범위를 Azure Cosmos DB 데이터베이스 계정에 허용되는 IP 주소 목록에 추가해야 합니다. 

## <a id="configure-ip-policy"></a> IP 액세스 제어 정책 구성
Azure Portal에서나, [Azure CLI](cli-samples.md), [Azure Powershell](powershell-samples.md)을 통해 프로그래밍 방식으로 또는 `ipRangeFilter` 속성을 업데이트하여 [REST API](https://msdn.microsoft.com/library/azure/dn781481.aspx)를 통해 IP 액세스 제어 정책을 설정할 수 있습니다. IP 주소/범위는 쉼표로 구분하며 공백을 포함해서는 안 됩니다. 예: "13.91.6.132,13.91.6.1/24". 이러한 메서드를 통해 데이터베이스 계정을 업데이트할 때에는 기본 설정으로 다시 설정되지 않도록 모든 속성을 채워야 합니다.

> [!NOTE]
> Azure Cosmos DB 데이터베이스 계정에 대해 IP 액세스 제어 정책을 사용하도록 설정하면 허용되는 IP 주소 범위 목록 이외의 컴퓨터에서 시도하는 모든 Azure Cosmos DB 데이터베이스 계정 액세스가 차단됩니다. 이 모델 때문에 액세스 제어의 무결성을 보장하기 위해 포털에서 데이터 평면 작업을 검색하는 기능도 차단됩니다.

개발을 간소화하기 위해 Azure Portal에서는 클라이언트 컴퓨터의 IP를 식별하여 허용된 목록에 추가하여 컴퓨터에서 실행되는 앱이 Azure Cosmos DB 계정에 액세스할 수 있도록 합니다. 포털에서 볼 수 있듯이 여기서 클라이언트 IP 주소가 검색됩니다. 이것은 컴퓨터의 클라이언트 IP 주소일 수 있지만 네트워크 게이트웨이의 IP 주소일 수도 있습니다. 프로덕션으로 이동하기 전에 반드시 제거하세요.

Azure Portal에서 IP 액세스 제어 정책을 설정하려면 Azure Cosmos DB 계정 블레이드로 이동하고 탐색 메뉴에서 **방화벽**을 클릭한 후 **켜기**를 클릭합니다. 

![Azure Portal에서 방화벽 블레이드를 여는 방법을 보여 주는 스크린샷](./media/firewall-support/azure-portal-firewall.png)

새 창에서 Azure Portal에서 계정에 액세스할 수 있는지 여부를 지정하고 다른 주소 및 범위를 적절히 추가한 후 **저장**을 클릭합니다.  

> [!NOTE]
> IP 액세스 제어 정책을 사용하도록 설정할 경우 액세스를 유지하기 위해 Azure Portal에 대한 IP 주소를 추가해야 합니다. 포털 IP 주소는 다음과 같습니다.
> |지역|IP 주소|
> |------|----------|
> |아래 지정된 지역을 제외한 모든 지역| 104.42.195.92|
> |독일|51.4.229.218|
> |중국|139.217.8.252|
> |미국 정부 애리조나|52.244.48.71|
>

![Azure Portal에서 방화벽 설정을 구성하는 방법을 보여 주는 스크린샷](./media/firewall-support/azure-portal-firewall-configure.png)

## <a name="troubleshooting-the-ip-access-control-policy"></a>IP 액세스 제어 정책 문제 해결
### <a name="portal-operations"></a>포털 작업
Azure Cosmos DB 데이터베이스 계정에 대해 IP 액세스 제어 정책을 사용하도록 설정하면 허용되는 IP 주소 범위 목록 이외의 컴퓨터에서 시도하는 모든 Azure Cosmos DB 데이터베이스 계정 액세스가 차단됩니다. 따라서 컬렉션 탐색 및 문서 쿼리와 같은 포털 데이터 평면 작업을 사용하려면 포털의 **방화벽** 블레이드를 사용하여 Azure Portal 액세스를 명시적으로 허용해야 합니다. 

![Azure Portal에 대한 액세스를 사용하도록 설정하는 방법을 보여 주는 스크린샷](./media/firewall-support/azure-portal-access-firewall.png)

### <a name="sdk--rest-api"></a>SDK 및 Rest API
보안상의 이유로 허용 목록에 없는 컴퓨터에서 SDK 또는 REST API를 통해 액세스를 시도하면 추가 정보 없이 일반적인 404 찾을 수 없음 응답이 반환됩니다. Azure Cosmos DB 데이터베이스 계정에 대해 구성된 허용 IP 목록을 확인하여 Azure Cosmos DB 데이터베이스 계정에 올바른 정책 구성이 적용되고 있는지 확인하세요.

## <a name="next-steps"></a>다음 단계
네트워크 관련 성능 팁에 대한 정보는 [성능 팁](performance-tips.md)을 참조하세요.


