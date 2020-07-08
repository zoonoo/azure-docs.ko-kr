---
title: 네트워크 규칙의 Azure 방화벽 FQDN 필터링 (미리 보기)
description: 네트워크 규칙에서 FQDN 필터링을 사용 하는 방법
services: firewall-manager
author: vhorne
ms.service: firewall-manager
ms.topic: article
ms.date: 06/30/2020
ms.author: victorh
ms.openlocfilehash: 46d1f2eae1342901a660f99891d1ac1529e8ad77
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85568336"
---
# <a name="fqdn-filtering-in-network-rules-preview"></a>네트워크 규칙의 FQDN 필터링 (미리 보기)

> [!IMPORTANT]
> 네트워크 규칙의 FQDN 필터링은 현재 공개 미리 보기 상태입니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

FQDN (정규화 된 도메인 이름)은 호스트의 도메인 이름을 나타냅니다. 도메인 이름은 단일 또는 여러 IP 주소와 연결 됩니다. 응용 프로그램 규칙에서 Fqdn 및 FQDN 태그를 허용 하거나 차단할 수 있습니다. 사용자 지정 DNS 및 DNS 프록시 설정을 사용 하 여 네트워크 규칙에서 FQDN 필터링을 사용할 수도 있습니다.

## <a name="how-it-works"></a>작동 방법

Azure 방화벽은 DNS 설정을 사용 하 여 FQDN을 IP 주소로 변환 하 고 Azure DNS 또는 사용자 지정 DNS 구성에 따라 규칙을 처리 합니다.

네트워크 규칙에서 Fqdn을 사용 하려면 DNS 프록시를 사용 하도록 설정 해야 합니다. DNS 프록시를 사용 하지 않는 경우 신뢰할 수 있는 규칙 처리가 위험에 노출 됩니다. 사용 하도록 설정 하면 사용자 지정 DNS 서버를 구성할 수 있는 Azure 방화벽으로 DNS 트래픽이 전송 됩니다. 그런 다음 방화벽과 클라이언트는 동일한 구성 된 DNS 서버를 사용 합니다. DNS 프록시를 사용 하지 않는 경우 클라이언트와 방화벽에서 이름 확인에 다른 서버를 사용할 수 있기 때문에 Azure 방화벽은 다른 응답을 생성할 수 있습니다. 클라이언트와 방화벽이 다른 DNS 응답을 수신 하는 경우 네트워크 규칙의 FQDN 필터링에 오류가 발생 하거나 일관성이 없을 수 있습니다.

## <a name="next-steps"></a>다음 단계

[Azure 방화벽 DNS 설정](dns-settings.md)
