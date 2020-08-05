---
title: Azure Static Web Apps에서 사용자 지정 도메인 설정
description: Azure Static Web Apps에 사용자 지정 도메인을 매핑하는 방법을 알아봅니다.
services: static-web-apps
author: burkeholland
ms.service: static-web-apps
ms.topic: conceptual
ms.date: 05/08/2020
ms.author: buhollan
ms.openlocfilehash: 61ec96a35fac6a033fe6c8b65cff156ba63e5e58
ms.sourcegitcommit: 97a0d868b9d36072ec5e872b3c77fa33b9ce7194
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/04/2020
ms.locfileid: "87563350"
---
# <a name="setup-a-custom-domain-in-azure-static-web-apps-preview"></a>Azure Static Web Apps에서 사용자 지정 도메인 설정 미리 보기

기본적으로 Azure Static Web Apps는 자동 생성된 도메인 이름을 제공합니다. 이 문서에서는 Azure Static Web Apps 애플리케이션에 사용자 지정 도메인 이름을 매핑하는 방법을 보여 줍니다.

## <a name="prerequisites"></a>사전 요구 사항

- 구매한 도메인 이름
- 도메인의 DNS 구성 속성에 대한 액세스

도메인 이름을 구성하는 경우 "A" 레코드를 사용하여 루트 도메인(예: `example.com`)을 IP 주소에 매핑합니다. DNS 지정에서 도메인 간 매핑을 허용하지 않으므로 루트 도메인을 IP 주소에 직접 매핑해야 합니다.

## <a name="dns-configuration-options"></a>DNS 구성 옵션

애플리케이션에 사용할 수 있는 몇 가지 유형의 DNS 구성이 있습니다.

| 원하는 경우                                 | 작업                                                |
| -----------------------------------------------| --------------------------------------------------- |
| 지원 `www.example.com` 또는`blog.example.net`| [CNAME 레코드 매핑](#map-a-cname-record)           |
| `example.com` 지원                          | [루트 도메인 구성](#configure-a-root-domain) |
| `www.example.com`으로 모든 하위 도메인 가리키기      | [와일드카드 매핑](#map-a-wildcard-domain)            |

## <a name="map-a-cname-record"></a>CNAME 레코드 매핑

CNAME 레코드는 하나의 도메인을 다른 도메인에 매핑합니다. CNAME 레코드를 사용 하 여 `www.example.com` , `blog.example.com` 또는 다른 하위 도메인을 Azure Static Web Apps에서 제공 하는 자동 생성 도메인에 매핑할 수 있습니다.

1. [Azure 포털](https://portal.azure.com)에서 Azure 계정으로 로그인합니다.

1. **Static Web Apps**를 검색하고 선택합니다.

1. _Static Web Apps_ 페이지에서 앱의 이름을 선택합니다.

1. 메뉴에서 **사용자 지정 도메인**을 클릭합니다.

1. _사용자 지정 도메인_ 창에서 **값** 필드에 URL을 복사합니다.

### <a name="configure-dns-provider"></a>DNS 공급자 구성

1. 도메인 공급자의 웹 사이트에 로그인합니다.

2. DNS 레코드를 관리하기 위한 페이지를 찾습니다. 각 도메인 공급자마다 고유한 DNS 레코드 인터페이스가 있으므로 공급자의 설명서를 참조하세요. **도메인 이름**, **DNS** 또는 **이름 서버 관리**라는 레이블이 지정된 사이트 영역을 찾습니다.

3. 종종 계정 정보를 확인한 다음 **내 도메인**과 같은 링크를 검색하여 DNS 레코드 페이지를 찾을 수 있습니다. 해당 페이지로 이동한 다음, **영역 파일**, **DNS 레코드** 또는 **고급 구성**과 유사한 링크를 찾습니다.

    다음 스크린샷은 DNS 레코드 페이지의 예입니다.

    :::image type="content" source="media/custom-domain/example-record-ui.png" alt-text="샘플 DNS 공급자 구성":::

4. 다음 값을 사용하여 새 **CNAME** 레코드를 만듭니다.

    | 설정             | 값                     |
    | ------------------- | ------------------------- |
    | Type                | CNAME                     |
    | 호스트                | www                       |
    | 값               | 클립보드에서 붙여넣기 |
    | TTL(적용 가능한 경우) | 기본값대로 유지    |

5. DNS 공급자를 사용하여 변경 내용을 저장합니다.

### <a name="validate-cname"></a>CNAME의 유효성 검사

1. Azure Portal에서 _사용자 지정 도메인_ 창으로 돌아갑니다.

1. _사용자 지정 도메인 유효성 검사_ 섹션의 `www` 부분을 포함하여 도메인을 입력합니다.

1. **유효성 검사** 단추를 클릭합니다.

이제 사용자 지정 도메인이 구성 되었으므로 DNS 공급자가 전 세계에 변경 내용을 전파 하는 데 몇 시간 정도 걸릴 수 있습니다. [dnspropagation.net](https://dnspropagation.net)으로 이동하여 전파 상태를 확인할 수 있습니다. `www`를 포함하는 사용자 지정 도메인을 입력하고, 드롭다운에서 CNAME을 선택하고, **시작**을 선택합니다.

DNS 변경 내용이 채워지면 웹 사이트는 Static Web App의 자동 생성된 URL(예: _random-name-123456789c.azurestaticapps.net_)을 반환합니다.

## <a name="configure-a-root-domain"></a>루트 도메인 구성

루트 도메인은 `www`를 포함하여 도메인에서 하위 도메인을 뺀 값입니다. 예를 들어 `www.example.com`의 루트 도메인은 `example.com`입니다. 이를 "APEX" 도메인이라고도 합니다.

미리 보기 중에는 루트 도메인 지원을 사용할 수 없지만 Static Web App을 사용하여 루트 도메인 지원을 구성하는 방법에 대한 자세한 내용은 [Azure Static Web Apps에서 루트 도메인 구성](https://burkeholland.github.io/posts/static-app-root-domain) 블로그 게시물을 참조하세요.

## <a name="map-a-wildcard-domain"></a>와일드카드 도메인 매핑

경우에 따라 하위 도메인으로 전송되는 모든 트래픽을 다른 도메인으로 라우팅하도록 합니다. 일반적인 예제로 `www.example.com`에 모든 하위 도메인 트래픽을 매핑합니다. 이러한 방식으로 사용자가 `www.example.com` 대신 `w.example.com`을 입력하는 경우에도 요청은 `www.example.com`으로 전송됩니다.

### <a name="configure-dns-provider"></a>DNS 공급자 구성

1. 도메인 공급자의 웹 사이트에 로그인합니다.

2. DNS 레코드를 관리하기 위한 페이지를 찾습니다. 각 도메인 공급자마다 고유한 DNS 레코드 인터페이스가 있으므로 공급자의 설명서를 참조하세요. **도메인 이름**, **DNS** 또는 **이름 서버 관리**라는 레이블이 지정된 사이트 영역을 찾습니다.

3. 종종 계정 정보를 확인한 다음 **내 도메인**과 같은 링크를 검색하여 DNS 레코드 페이지를 찾을 수 있습니다. 해당 페이지로 이동한 다음, **영역 파일**, **DNS 레코드** 또는 **고급 구성**과 유사한 링크를 찾습니다.

    다음 스크린샷은 DNS 레코드 페이지의 예입니다.

    :::image type="content" source="media/custom-domain/example-record-ui.png" alt-text="샘플 DNS 공급자 구성":::

4. 다음 값을 사용하여 새 **CNAME** 레코드를 만들고 `www.example.com`을 사용자 지정 도메인 이름으로 바꿉니다.

    | 설정 | 값                  |
    | ------- | ---------------------- |
    | Type    | CNAME                  |
    | 호스트    | \*                     |
    | 값   | www.example.com        |
    | TTL     | 기본값대로 유지 |

5. DNS 공급자를 사용하여 변경 내용을 저장합니다.

이제 와일드카드 도메인이 구성되었으므로 변경 내용이 전 세계에 전파되는 데 몇 시간이 걸릴 수 있습니다. [dnspropagation.net](https://dnspropagation.net)으로 이동하여 전파 상태를 확인할 수 있습니다. 모든 하위 도메인(`www` 제외)이 있는 도메인 사용자 지정 도메인을 입력하고 드롭다운에서 CNAME을 선택하고, **시작**을 선택합니다.

DNS 변경 내용이 채워지면 웹 사이트는 Static Web App에 대해 구성된 사용자 지정 도메인(예: `www.example.com`)을 반환합니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [앱 설정 구성](application-settings.md)
