---
title: Azure Front Door용 도메인에 지역 필터링 | Microsoft Docs
description: 이 문서에서는 Azure Front Door에 대한 지역 필터링 정책에 대해 알아봅니다.
services: frontdoor
documentationcenter: ''
author: duongau
editor: ''
ms.service: frontdoor
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/28/2020
ms.author: duau
ms.reviewer: tyao
ms.openlocfilehash: 42697a57d39f4a34eee4866b67e2cde947db1ff5
ms.sourcegitcommit: 3792cf7efc12e357f0e3b65638ea7673651db6e1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91449255"
---
# <a name="geo-filtering-on-a-domain-for-azure-front-door"></a>Azure 전면 도어의 도메인에 대 한 지역 필터링

기본적으로 Azure Front 도어가 요청이 발생 하는 위치에 관계 없이 모든 사용자 요청에 응답 합니다. 일부 시나리오에서는 국가/지역에 따라 웹 응용 프로그램에 대 한 액세스를 제한 하는 것이 좋습니다. 전면 도어의 WAF (웹 응용 프로그램 방화벽) 서비스를 사용 하면 지정 된 국가/지역에서 액세스를 허용 하거나 차단 하는 끝점의 특정 경로에 대 한 사용자 지정 액세스 규칙을 사용 하 여 정책을 정의할 수 있습니다. 

WAF 정책은 사용자 지정 규칙 집합을 포함 합니다. 규칙은 일치 조건, 동작 및 우선 순위로 구성 됩니다. 일치 조건에서 일치 변수, 연산자 및 일치 값을 정의 합니다. 지역 필터링 규칙의 경우 일치 변수는 REMOTE_ADDR이 고 연산자는 GeoMatch 이며 값은 두 문자 국가/지역 코드입니다. GeoMatch 조건 및 REQUEST_URI 문자열 일치 조건을 결합 하 여 경로 기반 지역 필터링 규칙을 만들 수 있습니다.

[Azure PowerShell](front-door-tutorial-geo-filtering.md) 를 사용 하거나 [빠른 시작 템플릿을](https://github.com/Azure/azure-quickstart-templates/tree/master/101-front-door-geo-filtering)사용 하 여 Front 문에 대해 지역 필터링 정책을 구성할 수 있습니다.

## <a name="countryregion-code-reference"></a>국가/지역 코드 참조

|국가/지역 코드 | 국가/지역 이름 |
| ----- | ----- |
| AD | 안도라 |
| AE | 아랍에미리트|
| AF | 아프가니스탄|
| AG | 앤티가 바부다|
| AL | 알바니아|
| AM | 아르메니아|
| AO | 앙골라|
| AR | 아르헨티나|
| AS | 아메리칸 사모아|
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
| CA | Canada|
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
| CZ | 체코|
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
| GB | United Kingdom|
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
| US | 미국|
| UY | 우루과이|
| UZ | 우즈베키스탄|
| VC | 세인트 빈센트 그레나딘|
| VE | 베네수엘라|
| VG | 영국령 버진 아일랜드|
| VI | 미국령 버진 아일랜드|
| VN | 베트남|
| ZA | 남아프리카|
| ZM | 잠비아|
| ZW | 짐바브웨|

## <a name="next-steps"></a>다음 단계

- [Front Door를 만드는](quickstart-create-front-door.md) 방법을 알아봅니다.
- 핫에서 [지역 필터링 WAF 정책을 설정 하는](front-door-tutorial-geo-filtering.md)방법을 알아봅니다.
