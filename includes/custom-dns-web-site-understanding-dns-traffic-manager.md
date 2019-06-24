---
author: cephalin
ms.service: app-service-web
ms.topic: include
ms.date: 11/09/2018
ms.author: cephalin
ms.openlocfilehash: f42a97cdd74d360bc047ef561cbe626d526f9e4a
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67182782"
---
DNS(Domain Name System)는 인터넷에서 대상을 찾는 데 사용됩니다. 예를 들어 브라우저에서 주소를 입력하거나 웹 페이지의 링크를 클릭하면 브라우저에서 DNS를 사용하여 도메인을 IP 주소로 변환합니다. IP 주소는 일종의 거리 주소와 비슷하지만 사람에게 그다지 친화적이지는 않습니다. 예를 들어 192.168.1.88 또는 2001:0:4137:1f67:24a2:3888:9cce:fea3과 같은 IP 주소보다는 **contoso.com** 과 같은 DNS 이름을 기억하기는 것이 훨씬 쉽습니다.

DNS 시스템은 *레코드*를 기반으로 합니다. 레코드는 *contoso.com*과 같은 구체적인 **이름**을 IP 주소 또는 DNS 이름과 연결합니다. 웹 브라우저와 같은 애플리케이션에서 DNS의 이름을 조회할 경우 레코드를 찾아 해당 레코드가 가리키는 항목을 주소로 사용합니다. 가리키는 값이 IP 주소인 경우 브라우저는 해당 값을 사용합니다. 다른 DNS 이름을 가리키면 애플리케이션은 다시 확인을 수행해야 합니다. 최종적으로 모든 이름 확인은 IP 주소 확인으로 완료됩니다.

Azure 웹 사이트를 만들 때 DNS 이름이 사이트에 자동으로 할당됩니다. 이 이름의 형식은 **&lt;yoursitename&gt;.azurewebsites.net**과 같습니다. 웹 사이트를 Azure Traffic Manager 엔드포인트로 추가하면 **&lt;yourtrafficmanagerprofile&gt;.trafficmanager.net** 도메인을 통해 웹 사이트에 액세스할 수 있습니다.

> [!NOTE]
> 웹 사이트를 Traffic Manager 엔드포인트로 구성한 경우 DNS 레코드를 만들 때 **.trafficmanager.net** 주소를 사용합니다.
> 
> Traffic Manager에는 CNAME 레코드만 사용할 수 있습니다.
> 
> 

또한 여러 레코드 유형이 있고 각 유형에 고유한 기능 및 제한이 있지만 Traffic Manager 엔드포인트로 구성된 웹 사이트의 경우 *CNAME* 레코드 하나만 주의하면 됩니다.

### <a name="cname-or-alias-record"></a>CNAME 또는 별칭 레코드
CNAME 레코드를 매핑하는 *특정* 와 같은 DNS 이름을 **mail.contoso.com** 또는 **www\.contoso.com**, 다른 (canonical) 도메인 이름으로. Traffic Manager를 사용하는 Azure Websites의 경우 Canonical 도메인 이름은 Traffic Manager 프로필의 **&lt;myapp>.trafficmanager.net** 도메인 이름입니다. CNAME을 만들면 **&lt;myapp>.trafficmanager.net** 도메인 이름에 대한 별칭이 생성됩니다. CNAME 항목은 **&lt;myapp>.trafficmanager.net** 도메인 이름의 IP 주소로 자동으로 확인되므로 웹 사이트의 IP 주소가 변경될 경우에도 어떤 조치도 취할 필요가 없습니다.

Traffic Manager에 트래픽이 도착하면 Traffic Manager는 구성된 부하 분산 방법을 사용하여 트래픽을 웹 사이트로 라우팅합니다. 이러한 과정이 웹 사이트의 방문자에게는 전혀 노출되지 않습니다. 방문자의 브라우저에는 사용자 지정 도메인 이름만 표시됩니다.

> [!NOTE]
> 일부 도메인 등록 기관 으로만 같은 CNAME 레코드를 사용 하는 경우 하위 도메인을 매핑할 수 있도록 **www\.contoso.com**, 같은 이름이 고 **contoso.com**합니다. CNAME 레코드에 대한 자세한 내용은 등록 기관에서 제공하는 설명서인 <a href="https://en.wikipedia.org/wiki/CNAME_record">CNAME 레코드에 대한 Wikipedia 항목</a> 또는 <a href="https://tools.ietf.org/html/rfc1035">IETF 도메인 이름 - 구현 및 사양</a> 문서를 참조하세요.

