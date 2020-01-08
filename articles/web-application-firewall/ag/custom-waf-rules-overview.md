---
title: Application Gateway에 대 한 Azure WAF (웹 응용 프로그램 방화벽) v2 사용자 지정 규칙
description: 이 문서에서는 Azure 애플리케이션 Gateway의 WAF (웹 응용 프로그램 방화벽) v2 사용자 지정 규칙에 대 한 개요를 제공 합니다.
services: web-application-firewall
ms.topic: article
author: vhorne
ms.service: web-application-firewall
ms.date: 10/04/2019
ms.author: victorh
ms.openlocfilehash: 323f01e08007260d4fb6d651b20937c5d5d5e357
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/03/2020
ms.locfileid: "75645092"
---
# <a name="custom-rules-for-web-application-firewall-v2-on-azure-application-gateway"></a>Azure 애플리케이션 Gateway의 웹 응용 프로그램 방화벽 v2에 대 한 사용자 지정 규칙

Azure 애플리케이션 게이트웨이 WAF (웹 응용 프로그램 방화벽) v2는 다양 한 유형의 공격 으로부터 보호 하는 미리 구성 된 플랫폼 관리 규칙 집합을 제공 합니다. 이러한 공격에는 사이트 간 스크립팅, SQL 삽입 및 기타 기능이 포함 됩니다. WAF 관리자 인 경우 CRS (핵심 규칙 집합) 규칙을 보강 하는 고유한 규칙을 작성 하는 것이 좋습니다. 규칙은 일치 조건에 따라 요청 된 트래픽을 차단 하거나 허용할 수 있습니다.

사용자 지정 규칙을 사용 하면 WAF를 통과 하는 각 요청에 대해 평가 되는 사용자 고유의 규칙을 만들 수 있습니다. 이러한 규칙은 관리형 규칙 세트의 나머지 규칙보다 높은 우선 순위를 갖습니다. 사용자 지정 규칙에는 규칙 이름, 규칙 우선 순위 및 일치 조건의 배열이 포함 되어 있습니다. 이러한 조건이 충족 되 면 허용 또는 차단에 대 한 작업이 수행 됩니다.

예를 들어 192.168.5.4/24 범위의 IP 주소에서 모든 요청을 차단할 수 있습니다. 이 규칙에서 연산자는 *Ipmatch*이 고 MATCHVALUES는 IP 주소 범위 (192.168.5.4/24) 이며 작업은 트래픽을 차단 하는 것입니다. 또한 규칙의 이름 및 우선 순위를 설정 합니다.

사용자 지정 규칙은 그래야만 논리를 사용 하 여 보안 요구 사항을 해결 하는 고급 규칙을 지원 합니다. 예를 들면 (Condition 1 **and** condition 2) **또는** condition 3)입니다.  이 예에서는 조건 1 **과** 조건 2를 만족 **하거나** 조건 3이 충족 되 면 waf가 사용자 지정 규칙에 지정 된 작업을 수행 해야 함을 의미 합니다.

동일한 규칙 내의 서로 다른 일치 조건은 항상 **및**을 사용 하 여 복합 됩니다. 예를 들어 특정 IP 주소에서 트래픽을 차단 하 고 특정 브라우저를 사용 하는 경우에만 트래픽을 차단 합니다.

**또는** 두 개의 다른 조건을 원할 경우 두 조건이 서로 다른 규칙에 있어야 합니다. 예를 들어 특정 브라우저를 사용 하는 경우 특정 IP 주소에서 트래픽을 차단 하거나 트래픽을 차단 합니다.

> [!NOTE]
> WAF 사용자 지정 규칙의 최대 수는 100입니다. Application Gateway 제한에 대 한 자세한 내용은 [Azure 구독 및 서비스 제한, 할당량 및 제약 조건](../../azure-resource-manager/management/azure-subscription-service-limits.md#application-gateway-limits)을 참조 하세요.

정규식은 CRS 규칙 집합에서와 마찬가지로 사용자 지정 규칙 에서도 지원 됩니다. 이에 대 한 예제는 [사용자 지정 웹 응용 프로그램 방화벽 규칙 만들기 및 사용](create-custom-waf-rules.md)의 예제 3 및 5를 참조 하세요.

## <a name="allowing-vs-blocking"></a>차단 및 차단 허용

사용자 지정 규칙을 사용 하 여 트래픽을 허용 하 고 차단 하는 것은 간단 합니다. 예를 들어 IP 주소 범위에서 들어오는 모든 트래픽을 차단할 수 있습니다. 특정 브라우저에서 요청을 가져오는 경우 트래픽을 허용 하는 다른 규칙을 만들 수 있습니다.

항목을 허용 하려면 `-Action` 매개 변수가 **허용**으로 설정 되어 있는지 확인 합니다. 항목을 차단 하려면 `-Action` 매개 변수가 **block**으로 설정 되어 있는지 확인 합니다.

```azurepowershell
$AllowRule = New-AzApplicationGatewayFirewallCustomRule `
   -Name example1 `
   -Priority 2 `
   -RuleType MatchRule `
   -MatchCondition $condition `
   -Action Allow

$BlockRule = New-AzApplicationGatewayFirewallCustomRule `
   -Name example2 `
   -Priority 2 `
   -RuleType MatchRule `
   -MatchCondition $condition `
   -Action Block
```

이전 `$BlockRule` Azure Resource Manager의 다음 사용자 지정 규칙에 매핑됩니다.

```json
"customRules": [
      {
        "name": "blockEvilBot",
        "priority": 2,
        "ruleType": "MatchRule",
        "action": "Block",
        "matchConditions": [
          {
            "matchVariables": [
              {
                "variableName": "RequestHeaders",
                "selector": "User-Agent"
              }
            ],
            "operator": "Contains",
            "negationConditon": false,
            "matchValues": [
              "evilbot"
            ],
            "transforms": [
              "Lowercase"
            ]
          }
        ]
      }
    ], 
```

이 사용자 지정 규칙에는 작업을 수행 하기 위해 충족 해야 하는 이름, 우선 순위, 동작 및 일치 조건의 배열이 포함 되어 있습니다. 이러한 필드에 대 한 자세한 설명은 다음 필드 설명을 참조 하십시오. 사용자 지정 규칙 예제는 [사용자 지정 웹 응용 프로그램 방화벽 규칙 만들기 및 사용](create-custom-waf-rules.md)을 참조 하세요.

## <a name="fields-for-custom-rules"></a>사용자 지정 규칙에 대 한 필드

### <a name="name-optional"></a>이름 [선택 사항]

규칙의 이름입니다. 이 이름은 로그에 표시 됩니다.

### <a name="priority-required"></a>우선 순위 [필수]

- 규칙의 평가 순서를 결정 합니다. 값이 낮을수록 규칙의 이전 계산입니다. 허용 되는 범위는 1-100입니다. 
- 모든 사용자 지정 규칙에서 고유 해야 합니다. 우선 순위 40의 규칙은 우선 순위가 80 인 규칙 보다 먼저 평가 됩니다.

### <a name="rule-type-required"></a>규칙 유형 [필수]

현재는 **Matchrule**이어야 합니다.

### <a name="match-variable-required"></a>일치 변수 [필수]

변수 중 하나 여야 합니다.

- RemoteAddr – 원격 컴퓨터 연결의 IP 주소/호스트 이름
- RequestMethod – HTTP 요청 메서드 (GET, POST, PUT, DELETE 등)
- QueryString – URI의 변수
- PostArgs – POST 본문에 전달 된 인수입니다. 이 일치 변수를 사용 하는 사용자 지정 규칙은 ' Content-type ' 헤더가 ' application/x-www-form-urlencoded ' 및 ' multipart/'로 설정 된 경우에만 적용 됩니다.
- RequestUri – 요청의 URI
- RequestHeaders – 요청의 헤더
- RequestBody – 전체 요청 본문을 포함 합니다. 이 일치 변수를 사용 하는 사용자 지정 규칙은 ' Content-type ' 헤더가 ' application/x-www-form-urlencoded '로 설정 된 경우에만 적용 됩니다. 
- RequestCookies – 요청의 쿠키

### <a name="selector-optional"></a>선택기 [선택 사항]

MatchVariable 컬렉션의 필드에 대해 설명 합니다. 예를 들어 matchVariable이 RequestHeaders 인 경우 선택기는 *사용자 에이전트* 헤더에 있을 수 있습니다.

### <a name="operator-required"></a>연산자 [필수]

다음 연산자 중 하나 여야 합니다.

- IPMatch-일치 변수가 *Remoteaddr* 인 경우에만 사용 됩니다.
- Equals – 입력이 MatchValue와 동일 합니다.
- 포함
- LessThan
- GreaterThan
- LessThanOrEqual
- GreaterThanOrEqual
- BeginsWith
- EndsWith
- Regex
- Geomatch (미리 보기)

### <a name="negate-condition-optional"></a>부정 조건 [선택 사항]

현재 조건을 부정 합니다.

### <a name="transform-optional"></a>Transform [선택 사항]

일치를 시도 하기 전에 변환 이름이 포함 된 문자열의 목록입니다. 이러한 변환은 다음과 같을 수 있습니다.

- 소문자
- Trim
- UrlDecode
- UrlEncode 
- RemoveNulls
- HtmlEntityDecode

### <a name="match-values-required"></a>값 일치 [필수]

와 일치 하는 값 목록으로, *또는*' ed '로 간주할 수 있습니다. 예를 들어 IP 주소 또는 다른 문자열일 수 있습니다. 값 형식은 이전 연산자에 따라 달라 집니다.

### <a name="action-required"></a>작업 [필수]

- Allow – 트랜잭션을 승인 하 고 모든 후속 규칙을 건너뜁니다. 이는 지정 된 요청을 허용 목록에 추가 하 고 일치 하는 경우 요청에서 추가 평가를 중지 하 고 백 엔드 풀로 전송 됨을 의미 합니다. 허용 목록에 있는 규칙은 추가 사용자 지정 규칙 또는 관리 규칙에 대해 평가 되지 않습니다.
- Block – *SecDefaultAction* (검색/방지 모드)를 기반으로 트랜잭션을 차단 합니다. 허용 동작과 마찬가지로 요청이 평가 되 고 차단 목록에 추가 되 면 평가가 중지 되 고 요청이 차단 됩니다. 같은 조건을 충족 하는 모든 요청은 평가 되지 않고 차단 됩니다. 
- 로그 – 규칙을 로그에 기록할 수 있지만 나머지 규칙은 평가를 위해 실행할 수 있습니다. 후속 사용자 지정 규칙은 우선 순위에 따라 관리 되는 규칙에 따라 평가 됩니다.

## <a name="geomatch-custom-rules-preview"></a>Geomatch 사용자 지정 규칙 (미리 보기)

사용자 지정 규칙을 사용 하면 응용 프로그램의 정확한 요구 사항과 보안 정책에 맞게 조정 된 규칙을 만들 수 있습니다. 이제 공개 미리 보기로 제공 되는 국가/지역에 따라 웹 응용 프로그램에 대 한 액세스를 제한할 수 있습니다. 모든 사용자 지정 규칙과 마찬가지로이 논리는 응용 프로그램의 요구 사항에 맞게 다른 규칙과 함께 사용할 수 있습니다. 

   > [!NOTE]
   > Geomatch 사용자 지정 규칙은 미국 중 북부와 유럽 서 면에서 사용할 수 있습니다. 포털에서이 항목에 액세스 하려면 모든 사용자에 게 라이브 상태가 될 때까지 [이 링크](https://aka.ms/AppGWWAFGeoMatch) 를 사용 하세요. 

Geomatch 연산자를 사용 하는 경우 선택기는 다음 두 자리 국가 코드 중 하나를 사용할 수 있습니다. 

|국가 코드 | 국가 이름 |
| ----- | ----- |
| AD | 안도라 |
| AE | 아랍에미리트|
| AF | 아프가니스탄|
| AG | 앤티가 바부다|
| AL | 알바니아|
| AM | 아르메니아|
| AO | 앙골라|
| AR | 아르헨티나|
| AS | 미국령 사모아|
| AT | 오스트리아|
| AU | 오스트레일리아|
| AZ | 아제르바이잔|
| BA | 보스니아 헤르체고비나|
| BB | 바베이도스|
| BD | 방글라데시|
| BE | 벨기에|
| BF | 부르키나파소|
| BG | 불가리아|
| BH | 바레인|
| BI | 부룬디|
| BJ | 베냉|
| BL | 생바르텔레미|
| BN | 브루나이|
| BO | 볼리비아|
| BR | 브라질|
| BS | 바하마|
| BT | 부탄|
| BW | 보츠와나|
| BY | 벨로루시|
| BZ | 벨리즈|
| CA | 캐나다|
| CD | 콩고 민주 공화국|
| CF | 중앙 아프리카 공화국|
| CH | 스위스|
| CI | 코트디부아르|
| CL | 칠레|
| CM | 카메룬|
| CN | 중국|
| CO | 콜롬비아|
| CR | 코스타리카|
| CU | 쿠바|
| CV | 카보베르데|
| CY | 키프로스|
| CZ | 체코 공화국|
| DE | 독일|
| DK | 덴마크|
| DO | 도미니카 공화국|
| DZ | 알제리|
| EC | 에콰도르|
| EE | 에스토니아|
| EG | 이집트|
| ES | 스페인|
| ET | 에티오피아|
| FI | 핀란드|
| FJ | 피지|
| FM | 미크로네시아|
| FR | 프랑스|
| GB | 영국|
| GE | 조지아|
| GF | 프랑스령 기아나|
| GH | 가나|
| GN | 기니|
| GP | 과들루프|
| GR | 그리스|
| GT | 과테말라|
| GY | 가이아나|
| HK | 홍콩 특별 행정구|
| HN | 온두라스|
| HR | 크로아티아|
| HT | 아이티|
| HU | 헝가리|
| ID | 인도네시아|
| IE | 아일랜드|
| IL | 이스라엘|
| IN | 인도|
| IQ | 이라크|
| IR | 이란|
| IS | 아이슬란드|
| IT | 이탈리아|
| JM | 자메이카|
| JO | 요르단|
| JP | 일본|
| KE | 케냐|
| KG | 키르기스스탄|
| KH | 캄보디아|
| KI | 키리바시|
| KN | 세인트키츠 네비스|
| KP | 북한|
| KR | 한국|
| KW | 쿠웨이트|
| KY | 케이맨 제도|
| KZ | 카자흐스탄|
| LA | 라오스|
| LB | 레바논|
| LI | 리히텐슈타인|
| LK | 스리랑카|
| LR | 라이베리아|
| LS | 레소토|
| LT | 리투아니아|
| LU | 룩셈부르크|
| LV | 라트비아|
| LY | 리비아 |
| MA | 모로코|
| MD | 몰도바|
| MG | 마다가스카르|
| MK | 북마케도니아|
| ML | 말리|
| MM | 미얀마|
| MN | 몽골|
| MO | 마카오|
| MQ | 마르티니크|
| MR | 모리타니|
| MT | 몰타|
| MV | 몰디브|
| MW | 말라위|
| MX | 멕시코|
| MY | 말레이시아|
| MZ | 모잠비크|
| 해당 없음 | 나미비아|
| NE | 니제르|
| NG | 나이지리아|
| NI | 니카라과|
| NL | 네덜란드|
| 아니요 | 노르웨이|
| NP | 네팔|
| NR | 나우루|
| NZ | 뉴질랜드|
| OM | 오만|
| PA | 파나마|
| PE | 페루|
| PH | 필리핀|
| PK | 파키스탄|
| PL | 폴란드|
| PR | 푸에르토리코|
| PT | 포르투갈|
| PW | 팔라우|
| PY | 파라과이|
| QA | 카타르|
| RE | 리유니언|
| RO | 루마니아|
| RS | 세르비아|
| RU | 러시아|
| RW | 르완다|
| SA | 사우디아라비아|
| SD | 수단|
| SE | 스웨덴|
| SG | 싱가포르|
| SI | 슬로베니아|
| SK | 슬로바키아|
| SN | 세네갈|
| SO | 소말리아|
| SR | 수리남|
| SS | 남부 세단|
| SV | 엘살바도르|
| SY | 시리아|
| SZ | 스와질란드|
| TC | 터크스 케이커스 제도|
| TG | 토고|
| TH | 태국|
| TN | 튀니지|
| TR | 터키|
| TT | 트리니다드 토바고|
| TW | 대만|
| TZ | 탄자니아|
| UA | 우크라이나|
| UG | 우간다|
| 미국 | 미국|
| UY | 우루과이|
| UZ | 우즈베키스탄|
| VC | 세인트 빈센트 그레나딘|
| VE | 베네수엘라|
| VG | 영국령 버진 아일랜드|
| VI | 미국령 버진 아일랜드|
| VN | 베트남|
| ZA | 남아프리카 공화국|
| ZM | 잠비아|
| ZW | 짐바브웨|

## <a name="next-steps"></a>다음 단계

사용자 지정 규칙에 대 한 자세한 내용을 확인 한 후 [사용자 지정 규칙을 만듭니다](create-custom-waf-rules.md).
