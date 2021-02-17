---
title: Azure 방화벽 프리미엄 미리 보기 기능
description: Azure 방화벽 프리미엄은 Azure Virtual Network 리소스를 보호 하는 관리 되는 클라우드 기반 네트워크 보안 서비스입니다.
author: vhorne
ms.service: firewall
services: firewall
ms.topic: conceptual
ms.date: 02/16/2021
ms.author: victorh
ms.openlocfilehash: e823e1efc66592e9f48b7ff5e53a176a4e8cb514
ms.sourcegitcommit: 5a999764e98bd71653ad12918c09def7ecd92cf6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/16/2021
ms.locfileid: "100549727"
---
# <a name="azure-firewall-premium-preview-features"></a>Azure 방화벽 프리미엄 미리 보기 기능

:::image type="content" source="media/premium-features/icsa-cert-firewall-small.png" alt-text="ICSA 인증 로고" border="false"::::::image type="content" source="media/premium-features/pci-logo.png" alt-text="PCI 인증 로고" border="false":::


> [!IMPORTANT]
> Azure 방화벽 프리미엄은 현재 공개 미리 보기로 제공 됩니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

 Azure 방화벽 프리미엄 미리 보기는 매우 중요 하 고 규정을 준수 하는 환경에 필요한 기능을 제공 하는 차세대 방화벽입니다.

:::image type="content" source="media/premium-features/premium-overview.png" alt-text="Azure 방화벽 프리미엄 개요 다이어그램":::

Azure 방화벽 프리미엄 미리 보기는 Azure 방화벽 관리자를 사용 하 여 방화벽을 중앙에서 관리 하는 데 사용할 수 있는 전역 리소스인 방화벽 정책을 사용 합니다. 이 릴리스를 시작 하는 경우 모든 새 기능은 방화벽 정책을 통해서만 구성할 수 있습니다. 방화벽 규칙 (클래식)은 계속 지원 되며 기존 표준 방화벽 기능을 구성 하는 데 사용할 수 있습니다.  방화벽 정책은 독립적으로 또는 Azure 방화벽 관리자를 사용 하 여 관리할 수 있습니다. 단일 방화벽과 연결 된 방화벽 정책에는 요금이 부과 되지 않습니다.

> [!IMPORTANT]
> 현재 방화벽 프리미엄 SKU는 안전한 허브 배포 및 강제 터널 구성에서 지원 되지 않습니다. 

Azure 방화벽 프리미엄 미리 보기에는 다음과 같은 기능이 포함 되어 있습니다.

- **TLS 검사** -아웃 바운드 트래픽을 해독 하 고, 데이터를 처리 한 다음 데이터를 암호화 하 고 대상에 보냅니다.
- **Idps** -네트워크 침입 감지 및 방지 시스템 (idps)을 사용 하면 악의적인 활동의 네트워크 활동을 모니터링 하 고,이 활동에 대 한 정보를 기록 하 고, 보고 하 고, 선택적으로 차단할 수 있습니다.
- **Url 필터링** -Azure 방화벽의 FQDN 필터링 기능을 확장 하 여 전체 URL을 고려 합니다. 예를 들어 `www.contoso.com/a/c` 대신 `www.contoso.com` 입니다.
- **웹 범주** -관리자는 도박 websites, 소셜 미디어 웹 사이트 등의 웹 사이트 범주에 대 한 사용자 액세스를 허용 하거나 거부할 수 있습니다.

## <a name="features"></a>기능

### <a name="tls-inspection"></a>TLS 조사

Azure 방화벽 프리미엄은 아웃 바운드 및 동-서 TLS 연결을 종료 합니다. 인바운드 TLS 검사는 종단 간 암호화를 허용 하는 [Azure 애플리케이션 게이트웨이에서](../web-application-firewall/ag/ag-overview.md) 지원 됩니다. Azure 방화벽은 필수 값으로 추가 된 보안 기능을 수행 하 고 원래 대상으로 전송 되는 트래픽을 다시 암호화 합니다.

> [!TIP]
> TLS 1.0 및 1.1은 더 이상 사용 되지 않으며 지원 되지 않습니다. Tls/SSL(Secure Sockets Layer) (SSL)의 TLS 1.0 및 1.1 버전은 취약 한 것으로 확인 되었지만 현재는 이전 버전과의 호환성을 허용 하기 위해 작동 하는 동안에는 권장 되지 않습니다. 가능한 한 빨리 TLS 1.2로 마이그레이션합니다.

Azure 방화벽 프리미엄 미리 보기 중간 CA 인증서 요구 사항에 대해 자세히 알아보려면 [Azure 방화벽 프리미엄 미리 보기 인증서](premium-certificates.md)를 참조 하세요.

### <a name="idps"></a>IDP

네트워크 침입 감지 및 방지 시스템 (IDPS)을 사용 하면 네트워크에서 악의적인 작업을 모니터링 하 고,이 작업에 대 한 정보를 기록 하 고, 보고 하 고, 선택적으로 차단할 수 있습니다. 

Azure 방화벽 프리미엄 미리 보기는 네트워크 트래픽의 바이트 시퀀스 또는 맬웨어에 사용 되는 알려진 악성 명령 시퀀스와 같은 특정 패턴을 검색 하 여 공격에 대 한 신속한 검색을 가능 하 게 하는 서명 기반 IDPS를 제공 합니다. IDPS 서명은 완전히 관리 되 고 지속적으로 업데이트 됩니다.

IDPS를 사용 하면 암호화 되지 않은 트래픽에 대 한 모든 포트 및 프로토콜의 공격을 검색할 수 있습니다. 그러나 HTTPS 트래픽을 검사 해야 하는 경우 Azure 방화벽은 TLS 검사 기능을 사용 하 여 트래픽을 해독 하 고 악의적인 활동을 더 효과적으로 검색할 수 있습니다.  

IDPS 바이패스 목록을 사용 하면 무시 목록에 지정 된 IP 주소, 범위 및 서브넷에 대 한 트래픽을 필터링 할 수 없습니다.  

### <a name="url-filtering"></a>URL 필터링

URL 필터링은 Azure 방화벽의 FQDN 필터링 기능을 확장 하 여 전체 URL을 고려 합니다. 예를 들어 `www.contoso.com/a/c` 대신 `www.contoso.com` 입니다.  

URL 필터링은 HTTP 및 HTTPS 트래픽 모두에 적용 될 수 있습니다. HTTPS 트래픽이 검사 되 면 Azure 방화벽 프리미엄 미리 보기에서 TLS 검사 기능을 사용 하 여 트래픽을 해독 하 고 대상 URL을 추출 하 여 액세스가 허용 되는지 여부를 확인할 수 있습니다. TLS 검사를 수행 하려면 응용 프로그램 규칙 수준에서 옵트인 해야 합니다. 사용 하도록 설정 되 면 HTTPS를 사용 하 여 필터링 하는 데 Url을 사용할 수 있습니다. 

### <a name="web-categories"></a>웹 범주

관리자는 웹 범주를 사용 하 여 도박 websites, 소셜 미디어 웹 사이트 등의 웹 사이트 범주에 대 한 사용자 액세스를 허용 하거나 거부할 수 있습니다. 웹 범주는 또한 Azure 방화벽 표준에 포함 되지만, Azure 방화벽 프리미엄 미리 보기에서 보다 자세히 조정 됩니다. FQDN을 기반으로 하는 범주와 일치 하는 표준 SKU의 웹 범주 기능과 달리 Premium SKU는 HTTP 및 HTTPS 트래픽의 전체 URL에 따라 범주와 일치 합니다. 

예를 들어 Azure 방화벽이에 대 한 HTTPS 요청을 가로채는 경우 `www.google.com/news` 다음 분류가 필요 합니다. 

- 방화벽 표준 – FQDN 부분만 검사 하므로 `www.google.com` *검색 엔진* 으로 분류 됩니다. 

- 방화벽 프리미엄 – 전체 URL이 검사 되므로 `www.google.com/news` *뉴스* 로 분류 됩니다.

범주는 **책임**, **고대역폭**, **비즈니스 사용**, **생산성 손실**, **일반 서핑** 및 **범주화** 되지 않음의 심각도를 기준으로 구성 됩니다.

#### <a name="category-exceptions"></a>범주 예외

웹 범주 규칙에 대 한 예외를 만들 수 있습니다. 규칙 컬렉션 그룹 내에서 우선 순위가 높은 별도의 허용 또는 거부 규칙 컬렉션을 만듭니다. 예를 들어 우선 순위 100를 사용 하는 규칙 컬렉션을 구성할 수 있습니다 `www.linkedin.com` . 규칙 컬렉션은 우선 순위 200를 사용 하 여 **소셜 네트워킹** 을 거부 합니다. 이렇게 하면 미리 정의 된 **소셜 네트워킹** 웹 범주에 대 한 예외가 생성 됩니다. 

## <a name="known-issues"></a>알려진 문제

Azure 방화벽 프리미엄 미리 보기에는 다음과 같은 알려진 문제가 있습니다.

|문제  |Description  |완화 방법  |
|---------|---------|---------|
|TLS 검사는 HTTPS 표준 포트 에서만 지원 됩니다.|TLS 검사는 HTTPS/443만 지원 합니다. |없음 다른 포트는 GA에서 지원 됩니다.|
|ESNI은 HTTPS에서 FQDN 확인을 지원 합니다.|암호화 된 SNI는 HTTPS 핸드셰이크에서 지원 되지 않습니다.|오늘 Firefox 에서만 사용자 지정 구성을 통해 ESNI을 지원 합니다. 제안 된 해결 방법은이 기능을 사용 하지 않도록 설정 하는 것입니다.|
|클라이언트 인증서 (TLS)|클라이언트 인증서는 클라이언트와 서버 간에 상호 id 트러스트를 구축 하는 데 사용 됩니다. 클라이언트 인증서는 TLS 협상 중에 사용 됩니다. Azure 방화벽은 서버와의 연결을 재협상 클라이언트 인증서의 개인 키에 액세스할 수 없습니다.|None|
|HTTP3|새 주요 버전은 HTTP입니다. 80 (계획) 및 443 (SSL)을 초과 하는 UDP 기반 프로토콜입니다. FQDN/URL/TLS 검사는 지원 되지 않습니다.|UDP 80/443을 네트워크 규칙으로 전달 하도록 구성 합니다.|
|보안 허브 및 강제 터널링이 프리미엄에서 지원 되지 않음|현재 방화벽 프리미엄 SKU는 안전한 허브 배포 및 강제 터널 구성에서 지원 되지 않습니다.|GA 예약 수정|
신뢰할 수 없는 고객 서명 인증서|사용자가 서명한 인증서는 인트라넷 기반 웹 서버에서 받은 방화벽에서 신뢰할 수 없습니다.|GA 예약 수정
|TLS 검사를 사용 하는 IDPS에 대 한 경고의 원본 및 대상 IP 주소가 잘못 되었습니다.|TLS 검사를 사용 하도록 설정 하 고 IDPS에서 새 경고를 발행 하면 표시 된 원본/대상 IP 주소가 잘못 됩니다 (원래 IP 주소 대신 내부 IP 주소가 표시 됨).|GA 예약 수정|
|HTTP에 대해 IDPS를 사용 하는 경고의 원본 IP 주소 (TLS 검사 제외)가 잘못 되었습니다.|일반 텍스트 HTTP 트래픽이 사용 중이 고 IDPS가 새 경고를 발행 하며 대상이 공용 ip 주소 이면 표시 된 원본 IP 주소가 잘못 된 것입니다 (원본 IP 주소 대신 내부 IP 주소가 표시 됨).|GA 예약 수정|
|인증서 전파|CA 인증서를 방화벽에 적용 한 후에는 인증서를 적용 하는 데 5-10 분 정도 걸릴 수 있습니다.|GA 예약 수정|
|IDPS 바이패스|IDPS 바이패스는 TLS 종료 트래픽에는 적용 되지 않으며 원본 IP 주소 및 원본 IP 그룹은 지원 되지 않습니다.|GA 예약 수정|




## <a name="next-steps"></a>다음 단계

- [Azure 방화벽 프리미엄 인증서에 대해 알아보기](premium-certificates.md)
- [Azure 방화벽 프리미엄 미리 보기 배포 및 구성](premium-deploy.md)
- [Azure 방화벽 프리미엄 미리 보기로 마이그레이션](premium-migrate.md)
