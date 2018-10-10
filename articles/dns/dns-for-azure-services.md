---
title: 다른 Azure 서비스와 함께 Azure DNS 사용 | Microsoft 문서
description: Azure DNS를 사용하여 다른 Azure 서비스에 대한 이름을 확인하는 방법 이해
services: dns
documentationcenter: na
author: vhorne
manager: jeconnoc
editor: ''
tags: azure dns
ms.assetid: e9b5eb94-7984-4640-9930-564bb9e82b78
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.custom: H1Hack27Feb2017
ms.workload: infrastructure-services
ms.date: 09/21/2016
ms.author: victorh
ms.openlocfilehash: 5d9886e16aa1921963f3d91d0fbd4da9287f7e54
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46989145"
---
# <a name="how-azure-dns-works-with-other-azure-services"></a>다른 Azure 서비스와 함께 Azure DNS가 작동하는 방법

Azure DNS는 호스팅된 DNS 관리 및 이름 확인 서비스입니다. 이를 통해 Azure에서 배포한 다른 응용 프로그램 및 서비스에 대한 공용 DNS 이름을 만들 수 있습니다. 사용자 지정 도메인에서 Azure 서비스에 대한 이름 만들기는 서비스에 대해 올바른 형식의 레코드를 추가하는 것 만큼 간단합니다.

* 동적으로 할당된 IP 주소의 경우 Azure가 서비스에 대해 만든 DNS 이름에 매핑하는 DNS CNAME 레코드를 만들 수 있습니다. DNS 표준은 영역 루트에 대해 CNAME 레코드를 사용하지 못하도록 하지만, 별칭 레코드를 대신 사용할 수 있습니다. 자세한 내용은 [자습서: Azure 공용 IP 주소를 가리키도록 별칭 레코드 구성](tutorial-alias-pip.md)을 참조하세요.
* 정적으로 할당된 IP 주소의 경우 영역 루트의 *naked 도메인* 이름을 포함한 모든 이름을 사용하여 DNS A 레코드를 만들 수 있습니다.

다음 테이블은 다양한 Azure 서비스에 사용할 수 있는 지원되는 레코드 형식을 설명합니다. 이 테이블에서 볼 수 있듯이 Azure DNS는 인터넷 연결 네트워크 리소스에 대한 DNS 레코드만을 지원합니다. Azure DNS는 내부, 개인 주소의 이름 확인에 사용할 수 없습니다.

| Azure 서비스 | 네트워크 인터페이스 | 설명 |
| --- | --- | --- |
| Application Gateway |[프런트 엔드 공용 IP](dns-custom-domain.md#public-ip-address) |DNS A 또는 CNAME 레코드를 만들 수 있습니다. |
| Load Balancer |[프런트 엔드 공용 IP](dns-custom-domain.md#public-ip-address)  |DNS A 또는 CNAME 레코드를 만들 수 있습니다. 부하 분산 장치는 동적으로 할당된 IPv6 공용 IP 주소를 가질 수 있습니다. 따라서 IPv6 주소에 대한 CNAME 레코드를 만들어야 합니다. |
| Traffic Manager |공용 이름 |Traffic Manager 프로필에 할당된 trafficmanager.net 이름에 매핑되는 별칭 레코드를 만들 수 있습니다. 자세한 내용은 [자습서: Traffic Manager를 사용하여 apex 도메인 이름을 지원하도록 별칭 레코드 구성](tutorial-alias-tm.md)을 참조하세요. |
| 클라우드 서비스 |[공용 IP](dns-custom-domain.md#public-ip-address) |정적으로 할당된 IP 주소의 경우 DNS A 레코드를 만들 수 있습니다. 동적으로 할당된 IP 주소의 경우 *cloudapp.net* 이름에 매핑하는 CNAME 레코드를 만들어야 합니다.|
| App Service | [외부 IP](dns-custom-domain.md#app-service-web-apps) |외부 IP 주소의 경우 DNS A 레코드를 만들 수 있습니다. 그렇지 않으면 azurewebsites.net 이름에 매핑하는 CNAME 레코드를 만들어야 합니다. 자세한 내용은 [Azure 앱에 사용자 지정 도메인 이름 매핑](../app-service/app-service-web-tutorial-custom-domain.md) |
| 리소스 관리자 VM |[공용 IP](dns-custom-domain.md#public-ip-address) |리소스 관리자 VM은 공용 IP 주소를 가질 수 있습니다. 공용 IP 주소가 있는 VM은 부하 분산 장치 뒤에 올 수도 있습니다. 공용 주소에 대한 DNS A, CNAME 또는 별칭 레코드를 만들 수 있습니다. 이 사용자 지정 이름은 부하 분산 장치의 VIP를 우회하는 데 사용할 수 있습니다. |
| 클래식 VM |[공용 IP](dns-custom-domain.md#public-ip-address) |PowerShell 또는 CLI를 사용하여 만든 클래식 VM은 동적 또는 정적(예약된) 가상 주소로 구성될 수 있습니다. DNS CNAME 또는 A 레코드를 각각 만들 수 있습니다. |
