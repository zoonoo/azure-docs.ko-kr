---
title: SAPI 전화 집합 사용-음성 서비스
titleSuffix: Azure Cognitive Services
description: SAPI의 자세한 전화 사용 문서입니다.
services: cognitive-services
author: zhaoyunED
manager: junwg
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/19/2020
ms.author: jiajzhan
ms.openlocfilehash: c14636e46a09bf4e7528dd732548c1a09dc10f92
ms.sourcegitcommit: 390cfe85629171241e9e81869c926fc6768940a4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/02/2020
ms.locfileid: "78228655"
---
# <a name="sapi-phone-set-usage"></a>SAPI 전화 집합 사용

Microsoft는 7 개 언어 (en-us, fr-fr, de-de, es, ja-jp, zh-cn, zh-cn)에 대 한 SAPI 전화 집합을 정의 합니다. SAPI 전화 집합은 비교적 쉽게 사용할 수 있으며 네이티브 스피커의 경우 특히 주의 해야 합니다. 예를 들어 Ja-jp를 phone으로 사용 하 고 zh-cn를 phone 세트로 사용 합니다. 그러나 강력 하지 않은 경우도 있습니다. 예를 들어 fr-fr phone 집합은 프랑스어를 표현할 수 없습니다. 

## <a name="en-us"></a>ko-KR

| 아마도  | IPA  | example1 (자음의 경우 하기 시작 하면, word 초기에는 모음) | example2 (자음의 intervocalic, word 중성 핵심 이기 for 모음) | example3 (자음 용 coda, 모음에 대 한 단어 최종) | comments                                                     |
| ---- | ---- | ---------------------------------------------------------- | ------------------------------------------------------------ | ------------------------------------------------------- | ------------------------------------------------------------ |
| SUPRASEGMENTALS|      |                                                            |                                                              |                                                         |                                                              |
| 1                    | ˈ    | 먹으며 즐길/b er **1** r-g ax r/                                | 비 afel/f ax-l aa **1** -f ax l/                           | 기타/g 항목-t aa **1** r/                               | SAPI 휴대폰 설정 스트레스 음절의 모음 뒤에 스트레스 배치 |
| 2                    | ˌ    | 리디렉션하거나/ih **2** -n aa-p ax r-t uw 1 n/              | dissimilarity/d 항목-s 항목 **2**-m ax-l eh 1-r ax-t 항목/ | 직원/w er 1 r k-f ao **2** r s/                     | SAPI 전화 집합 substressed 음절의 모음 뒤에 스트레스를 둡니다. |
| -                    | .    |                                                            |                                                              |                                                         |                                                              |
| 모음은               |      |                                                            |                                                              |                                                         |                                                              |
| iy                   | i    | **ea**t                                                        | f**ee**l                                                         | vall**e**                                                  |                                                              |
| 항목                   | ɪ    | **i**f                                                         | f**i**ll                                                         |                                                         |                                                              |
| e                   | eɪ   | **te**                                                        | g**a**te                                                         | **day**                                                     |                                                              |
| eh                   | ɛ    | **e**매우                                                      | p**e**t                                                          | m**eh**(finally 단어)                               |                                                              |
| ae                   | →    | **c)**                                                     | c**a**t                                                          | n**ah**(마지막 단어를 드물게)                               |                                                              |
| aa                   | ɑ    | **o**bstinate                                                  | p**o**ppy                                                        | r**ah**(가끔 word finally)                               |                                                              |
| ao                   | ɔ    | **o**범위                                                     | c**au**se                                                        | 전 세계**ah**                                                    |                                                              |
| 네                   | ʊ    |                                                            | b**oo**k                                                         |                                                         |                                                              |
| ow                   | oʊ   | **o**ld                                                        | cl**o**ne                                                        | g**o**                                                      |                                                              |
| uw                   | u    | **U**ber                                                       | b**oo**st                                                        | t**oo**                                                     |                                                              |
| 아기                   | ʌ    | **u**ncle                                                      | c**u**t                                                          |                                                         |                                                              |
| 결과                   | aɪ   | **i**ce                                                        | b**i**te                                                         | fl**y**                                                     |                                                              |
| aw                   | aʊ   | **ou**t                                                        | s**ou**th                                                        | c**ow**                                                     |                                                              |
| oy                   | ɔɪ   | **oi**l                                                        | j**oi**n                                                         | t**oy**                                                     |                                                              |
| y uw                 | ju   | **Yu**ma                                                       | h**u**man                                                        | f                                                     |                                                              |
| ax                   | ə    | **go**                                                        | wom**a**n                                                        | 는**입니다.**                                                    |                                                              |
| R 색 모음|      |                                                            |                                                              |                                                         |                                                              |
| 항목 r                 | ɪɹ   | **귀**s                                                       | t**ir**amisu                                                     | n**귀**                                                    |                                                              |
| eh r                 | ɛɹ   | **공기**평면                                                   | 앱**ar**ently                                                   | sc**ar**e                                                   |                                                              |
| 인                 | ʊɹ   |                                                            |                                                              | c **(** e)                                                    |                                                              |
| ay r                 | aɪɹ  | **Ire**지역                                                    | f**ir**eplace                                                    | ch**oir**                                                   |                                                              |
| aw r                 | aʊɹ  | **시간**s                                                      | p**ower**                                                     | s                                                    |                                                              |
| ao r                 | ɔɹ   | **또는**a)                                                     | m**또는**al                                                        | s**oar**                                                    |                                                              |
| aa r                 | ɑɹ   | **ar**tist                                                     | st**ar**t                                                        | c**ar**                                                     |                                                              |
| er r                 | ɝ    | **귀**번째                                                      | b**ir**d                                                         | f                                                     |                                                              |
| ax r                 | ɚ    |                                                            | 모든**er**gy                                                      | supp**er**                                                  |                                                              |
| SEMIVOWELS|      |                                                            |                                                              |                                                         |                                                              |
| w                    | w    | **w**i, s**ue**                                                | al**w**ays                                                       |                                                         |                                                              |
| y                    | j    | **y**인, f**e**w                                                  | 온 **-시**                                                        |                                                         |                                                              |
| ASPIRATED 구두 중지 |      |                                                            |                                                              |                                                         |                                                              |
| p                    | p    | **p**전 세계                                                        | ha**페이지**en                                                       | fla**p**                                                    |                                                              |
| b                    | b    | **big**                                                        | 번호**b**er                                                       | cra**b**                                                    |                                                              |
| t                    | t    | **t**alk                                                       | capi**t**al                                                      | sough**t**                                                  |                                                              |
| d                    | d    | **d**                                                        | 실행 한**d**om                                                       | ro**d**                                                     |                                                              |
| k                    | k    | **c**세계                                                        | sla**헤드**er                                                      | Ira**q**                                                    |                                                              |
| g                    | g    | **g**o                                                         | a**g**o                                                          | dra**g**                                                    |                                                              |
| NASAL 중지          |      |                                                            |                                                              |                                                         |                                                              |
| m                    | m    | **m**, smash                                                 | ca**m**연대                                                       | roo**m**                                                    |                                                              |
| n                    | n    | **n**o, s**n**ow                                                   | te**n**t                                                         | chicke**n**                                                 |                                                              |
| ng                   | ŋ    |                                                            | li**n**k                                                         | s**연산**                                                    |                                                              |
| FRICATIVES|      |                                                            |                                                              |                                                         |                                                              |
| f                    | f    | **f**o)                                                       | le**f**t                                                         | hal**f**                                                    |                                                              |
| v                    | v    | **v**alue                                                      | e**v**ent                                                        | 로**낮음**                                                    |                                                              |
| th                   | θ    | **th**                                                       | empa**번째**                                                      | mon**번째**                                                   |                                                              |
| 교환이                   | led    | **번째**en                                                       | mo                                                       | smoo**번째**   |                                                              |
| s                    | s    | **s**it                                                        | ri**s**k                                                         | 팩트**s**                                                   |                                                              |
| z                    | z    | **z**ap                                                        | bu**s**y                                                         | 어린이**s**                                                    |                                                              |
| sh                   | ʃ    | **sh** e                                                        | abbrevia**ti**설정                                                 | 및                                                    |                                                              |
| zh                   | ʒ    | **J**acques                                                    | plea                                                     | gara**g**e                                                  |                                                              |
| h                    | h    | **h**elp                                                       | en**h**ance                                                      | -**h**a!                                                   |                                                              |
| AFFRICATES           |      |                                                            |                                                              |                                                         |                                                              |
| ch                   | tʃ   | **ch**in                                                       | fu**t**                                                       | atta**ch**                                                  |                                                              |
| jh                   | dʒ   | **j**oy                                                        | ori**g**inal                                                     | oran**g**e                                                  |                                                              |
| APPROXIMANTS|      |                                                            |                                                              |                                                         |                                                              |
| l                    | l    | **l**id, g**l**ad                                                  | pa**l**ace                                                       | 카이**ll**                                                   |                                                              |
| r                    | ʁ    | **r**ed, b**r**ing                                                 | bo**rr**ow                                                       | ta**r**                                                     |                                                              |

## <a name="fr-fr"></a>fr-FR

| 아마도            | IPA                   | 예제 1 (자음의 경우 하기 시작 하면, 모음에 대 한 단어 초기) | 예 2 (자음의 경우 intervocalic, word 중성 핵심 이기 for 모음) | 예 3 (자음 용 coda, 모음에 대 한 단어 최종) | 주석                                                      |
| --------------- | --------------------- | -------------------------------------------------------- | ------------------------------------------------------------ | ----------------------------------------------------- | ------------------------------------------------------------ |
| SUPRASEGMENTALS |                       |                                                          |                                                              |                                                       |                                                              |
| 1               | ˈ (기본 스트레스)    |                                                          |                                                              |                                                       | SAPI 휴대폰 설정 스트레스 음절의 모음 뒤에 스트레스 배치 |
|                 | ˌ (substress)         |                                                          |                                                              |                                                       | fr-fr SAPI phone 집합은 IPA substress ' ˌ '를 나타내는 것을 지원 하지 않습니다. 필요한 경우 IPA을 직접 사용 해야 합니다. |
| -               | . (음절 경계) |                                                          |                                                              |                                                       |                                                              |
| 모음은          |                       |                                                          |                                                              |                                                       |                                                              |
| a               | a                     | **rbre**                                                    | p**a**tte                                                        | ir**a**                                                   |                                                              |
| aa              | ɑ                     |                                                          | p                                                         | p**a**s                                                   |                                                              |
| aa ~            | ɑ̃                     | **en**fant                                                   | enf**en**t                                                       | t**em**ps                                                 |                                                              |
| ax              | ə                     |                                                          | p**e**tite                                                       | l**e**                                                    |                                                              |
| eh              | ɛ                     | **e**lle                                                     | p**e**rdu                                                        | ét**ai**t                                                 |                                                              |
| eu              | ø                     | **œu**fs                                                     | cr**eu**ser                                                      | qu**eu**                                                  |                                                              |
| e              | e                     | ému                                                      | crétin                                                       | ôté                                                   |                                                              |
| eh ~            | ɛ̃                     | **im**portant                                                | p**ein**t                                                     | **인** 경우                                                 |                                                              |
| iy              | i                     | **dée**                                                     | 애완**동물**                                                       | am**i**                                                   |                                                              |
| oe              | 속성만                     | **œu**f                                                      | p**eu**r                                                         |                                                       |                                                              |
| 돼              | ɔ                     | **o**bstacle                                                 | c**o**rps                                                        |                                                       |                                                              |
| 오 ~            | ɔ̃                     | ze **에서**                                                     | deur**의**r                                                      | b**설정**                                                   |                                                              |
| ow              | o                     | **오스트레일리아**diteur                                                 | b**eau**                                                     | p**ô**                                                    |                                                              |
| oe ~            | œ̃                     | **되지**                                                       | l**제거**(di)                                                        | br**취소**                                                  |                                                              |
| uw              | u                     | **ou**trage                                                  | intr**ou**된                                                  | **조직**                                                    |                                                              |
| uy              | y                     | **u**ne                                                      | p**u**n ir                                                        | él**u**                                                   |                                                              |
| 자음      |                       |                                                          |                                                              |                                                       |                                                              |
| b               | b                     | **b**ête                                                     | ha**b**ille                                                      | ro**b**e                                                  |                                                              |
| d               | d                     | **d**ire                                                     | ron**d**eur                                                      | chau**d**e                                                |                                                              |
| f               | f                     | **f**emme                                                    | su**ff**ixe                                                      | bo**f**                                                   |                                                              |
| g               | g                     | **g**auche                                                   | é**g**ale                                                        | ba**gu**e                                                 |                                                              |
| ng              | ŋ                     |                                                          |                                                              | 파킹                                              | 일부 외래 단어만                                 |
| hy              | ɥ                     | h**u**개                                                    | n**u**ire                                                        |                                                       |                                                              |
| k               | k                     | **c**arte                                                    | é**c**                                                      | **c**                                                   |                                                              |
| l               | l                     | **l**긴                                                     | é**l**ire                                                        | ba**l**                                                   |                                                              |
| m               | m                     | **m**adame                                                   | ai**m**er                                                        | po**mm**e                                                 |                                                              |
| n               | n                     | **n**ou                                                     | te**n**ir                                                        | bo**nn**e                                                 |                                                              |
| nj              | ɲ                     |                                                          |                                                              | pei**gn**e                                                |                                                              |
| p               | p                     | **p**                                                    | 다시**p**                                                        | ca**p**                                                   |                                                              |
| r               | ʁ                     | **r**at                                                      | cha**r**iot                                                      | senti**r**                                                |                                                              |
| s               | s                     | **s**ourir                                                   | a**ss**ez                                                        | pa**ss**e                                                 |                                                              |
| sh              | ʃ                     | **ch**anter                                                  | ma**ch**                                                      | po**ch**e                                                 |                                                              |
| t               | t                     | **t**ête                                                     | ô**t**er                                                         | ne**t**                                                   |                                                              |
| v               | v                     | **v**ent                                                     | **v**입력                                                     | rê**v**e                                                  |                                                              |
| w               | w                     | **ou**i                                                      | f**ou**                                                       |                                                       |                                                              |
| y               | j                     | **y**od                                                      | p**i**étiner                                                     | \* * Ille * *                                            |                                                              |
| z               | z                     | \* * z * * éro                                                     | rai**s**onner                                                    | ro**s**e                                                  |                                                              |
| zh              | ʒ                     | **j**ardin                                                   | 남자**g**er                                                       | piè**g**e                                                 |                                                              |
|                 | 개의                    |                                                          |                                                              | u**n** arbre                                              | fr-fr SAPI phone 집합은 프랑스어 liasion "n ‿"를 나타내는 기능을 지원 하지 않습니다. 필요한 경우 IPA를 직접 사용 해야 합니다. |
|                 | 트                    |                                                          |                                                              | **차원**                                                 | fr-fr SAPI phone 집합은 프랑스어 liasion "t ‿"를 나타내는 기능을 지원 하지 않습니다. 필요한 경우 IPA를 직접 사용 해야 합니다. |
|                 | -                    |                                                          |                                                              | di**x**                                                   | fr-fr SAPI phone 집합은 프랑스어 liasion "z ‿"를 나타내는 기능을 지원 하지 않습니다. 필요한 경우 IPA를 직접 사용 해야 합니다. |


## <a name="de-de"></a>de-DE

| 아마도   | IPA     | 예제 1 (자음의 경우 하기 시작 하면, 모음에 대 한 단어 초기)  | 예 2 (자음의 경우 intervocalic, word 중성 핵심 이기 for 모음) | 예 3 (자음 용 coda, 모음에 대 한 단어 최종)     | 주석                                                      |
| --------------- | --------------------- | -------------------------------------------------------- | ------------------------------------------------------------ | ----------------------------------------------------- | ------------------------------------------------------------ |
| SUPRASEGMENTALS |         |                                                              |                                                              |                                                              |                                                              |
| 1               | ˈ       | anders/a **1** n-d ax r s/                                | Multiplikationszeichen/m \_l-t iy-p l iy-k a-ts y ow **1** n s-ts ay-c n/ | Biologie/b iy-ow-l ow-g iy **1**/                     | SAPI 휴대폰 설정 스트레스 음절의 모음 뒤에 스트레스 배치 |
| 2               | ˌ       | Allgemeinwissen/a **2** l-g ax-m ay 1 n-v 항목-s n/   | Abfallentsorgungsfirma/a 1 p-f a l-^ eh n t-z **n2** ax r-g. s-f 항목 ax r-m a/ | Computertomographie/k n m-p y uw 1-t ax r-t ow-m ow-g r a-f iy **2**/ | SAPI 전화 집합 substressed 음절의 모음 뒤에 스트레스를 둡니다. |
| -               | .       |                                                              |                                                              |                                                              |                                                              |
| 모음은          |         |                                                              |                                                              |                                                              |                                                              |
| 은             | ː      | **Ber**                                        | Maßst**a**b                             | 스키마                                      |                                                              |
| a               | a       | **Bfall**.                                        | B**a**ch                                               | Agath                                    |                                                              |
| 돼              | ɔ       | **O**sten                                          | Pf**o**                                     |                                                              |                                                              |
| eh            | ɛː      | **Ä**hnlichkeit                      | B**ä**r                                           | 예: Fasci**ae** 와 같은 외부 원본의 단어만      |                                                              |
| eh              | ɛ       | **ä**ndern                                    | Proz**e**nt                                | Amygdal**ae**                         |                                                              |
| ax              | ə       | Intially-처음에는 ' v e r s i o n t i m**e**'과 같은 외래 원본 단어에만 해당 **합니다**.      | Aach**e**n                                       | 조각**e**                                      |                                                              |
| iy              | ː      | **실행 됨**                                         | abb**ie**gt                                    | Relativitätstheor**ie**     |                                                              |
| 항목              | ɪ       | **Nnung**                                       | s**i**ngen                                     | 목조**y**                                         |                                                              |
| eu              | øː      | **Ö**send                                             | abl**ö**sten                               | Malm**ö**                                        |                                                              |
| ow              | o, o ː   | **o**hne                                            | 신데렐라**o**n                                     | Trept**ow**                                   |                                                              |
| oe              | 속성만       | **Ö**ffnung                                    | bef**ö**rdern                     |                                                              |                                                              |
| e              | e, e ː   | **E-learning**하드                            | .abf**e**gt                                     | b                                                    |                                                              |
| uw              | u ː      | **U**do                                             | H**u**t                                                | Akk**u**                                             |                                                              |
| 네              | ʊ       | **U**nterschiedes              | b**u**nt                                             |                                                              |                                                              |
| agent              | y ː      | **Ü**bermut                              | default.pfl**ü**gt                                        | 남자**ü**                                          |                                                              |
| uy              | ʏ       | **ü**ppig                                         | S**y**스템                                    |                                                              |                                                              |
| DIPHTHONG       |         |                                                              |                                                              |                                                              |                                                              |
| 결과              | ai      | **ei**nsam                                     | Unabhängigk**ei**t      | Abt**ei**                                          |                                                              |
| aw              | au      | **오스트레일리아**ßen                                            | abb**au**st                                  | St**au**                                              |                                                              |
| oy              | ɔy, ɔʏ̯  | **Eu**phorie                                 | tr**äu**mt                                         | sch-m**eu**                                               |                                                              |
| SEMIVOWEL       |         |                                                              |                                                              |                                                              |                                                              |
| ax r            | ɐ       |                                                              | abänd**er**n                          | 잠금                                     |                                                              |
| 자음       |         |                                                              |                                                              |                                                              |                                                              |
| b               | b       | **B**ank                                             |                                                              | ' 예: Pu**b** 와 같은 외부 원본의 단어만     |                                                              |
| c               | ç       | **Ch**emie                                        | mögli**ch**st                             | i**ch**                                                  | ' e ' 및 ' i ' 뒤의 ' ch ' 소프트                                 |
| d               | d       | **d**anken                                       | 외부 원점 (예: Len**d**l)의 단어만      | 외래 원본 (예: Clau**d**e)의 단어만      |                                                              |
| jh              | ʤ       | **J**eff                                                | gemana**g**t                        | 변경 (**g**e와 같이 외래 원본에만 해당 합니다.      | 외부 원본의 단어만                             |
| f               | f       | **F**ahrtdauer                          | angri**ff**slustig         | abbruchrei**f**                      |                                                              |
| g               | g       | **g**전 세계                                                |                                                              | Terminally (예: Gre**g** )와 같은 외부 원본에만 해당 하는 단어     |                                                              |
| h               | h       | **H**-ausanbau                           |                                                              |                                                              |                                                              |
| y               | j       | **J**od                                                | Reakt**i**                           | hu-hu**i**                                                |                                                              |
| k               | k       | **K**oma-uri                                          | E**k**t                                     | Flec**k**                                            |                                                              |
| l               | l       | **l**au                                                  | ähne**l**n                                       | zuvie**l**                                     |                                                              |
| m               | m       | **M**전 세계                                                | **M**t                                                 | Leh**m**                                              |                                                              |
| n               | n       | **n**취소                                                | u**n**d                                                | 나 에 게                                               |                                                              |
| ng              | ŋ       | 외부 원점 (예: 서 **-uyen**)의 단어만      | Schwa**빈**                                       | R**ing**                                              |                                                              |
| p               | p       | **P**artner                                | abru**p**t                                     | Ti**p**                                                |                                                              |
| pf              | pf      | **Pf**erd                                       | dam**pf**t                                         | **Pf** 로                                              |                                                              |
| r               | ʀ, r, ʁ | **R**eise                                         | knu**rr**t                                      | Haa**r**                                              |                                                              |
| s               | s       |  외부 원본 (예: **S Taccato)** 의 단어만      | bi**s**t                                             | mie**s**                                               |                                                              |
| sh              | ʃ       | **Sch-m**u)                                      | mi**sch-m**t                                          | lappi**sch-m**                                    |                                                              |
| t               | t       | **T**raum                                            | S**t**raße                                  | Mu**t**                                                |                                                              |
| ts              | ts      | **Z**ug                                               | Ar**z**t                                           | Wit**z**                                              |                                                              |
| ch              | tʃ      | **Tsch**ecto en                               | aufgepu**tsch**t                     | bundesdeu**tsch**                   |                                                              |
| v               | v       | **w**inken                                      | Q**u**alle                                       | 외부 원점 (예: 그루브)의 단어만      |                                                              |
| x               | x, ç     | Word-처음에는 다음과 같은 외부 원본에 대 한 단어만 **있습니다. 예: Ba** **ch**erach  | Ma**ch**t mögli**ch**st  | Schma**ch** ' i**ch**             | IPA [x]: 모든 비 front 모음 (a, aa, 오, ow, 고 uw 및 diphthong aw) 후에는 하드 ' ch '입니다.  IPA [ç]: front 모음 (ih, ih, eh, ae, uy, ue, oe, eu도 diphthongs ay, oy) 및 자음 다음에 소프트 ' ch '가 있습니다. |
| z               | z       | **s**uper                                       |                                                              |                                                              |                                                              |
| zh              | ʒ       | **G**enre                                      | B**re**ezinski                      | Edvi**g**e                                     | 외부 원본의 단어만                             |
| 구두 자음  |         |                                                              |                                                              |                                                              |                                                              |
| ^               | ʔ       | beachtlich/b ax-^ a 1 x t-l 항목 c/      |                                                              |                                                              | Glottal 중지, 추가 정보를 의미 합니다. [여기](http://en.wikipedia.org/wiki/Glottal_stop)를 클릭 하세요. 두 개의 모음이 진짜 diphthong을 제외 하 고 두 개의 고유한 모음 사이에 [gs\] phone을 추가 해야 합니다. |


## <a name="es-es"></a>es-ES

| 아마도            | IPA            | 예제 1 (자음의 경우 하기 시작 하면, 모음에 대 한 단어 초기) | 예 2 (자음의 경우 intervocalic, word 중성 핵심 이기 for 모음) | 예 3 (자음 용 coda, 모음에 대 한 단어 최종) | comments                                                     |
| --------------- | -------------- | ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| SUPRASEGMENTALS |                |                                                              |                                                              |                                                              |                                                              |
| -               | . 음절   |                                                              |                                                              |                                                              |                                                              |
| 1               | ˈ (mainstress) |                                                              |                                                              |                                                              | SAPI 휴대폰 설정 스트레스 음절의 모음 뒤에 스트레스 배치 |
| 2               | ˌ (substress)  |                                                              |                                                              |                                                              | SAPI 전화 집합 substressed 음절의 모음 뒤에 스트레스를 둡니다. |
| 모음은          |                |                                                              |                                                              |                                                              |                                                              |
| a               | a              | **lto**                          | c**a**ntar                           | cas**a**                                                        |                                                              |
| i               | i   | **bérica**                                                      | av**i**spa                                                       | 세금                                                         |                                                              |
| e               | e              | **e**lefante                                                     | at**e**                                                       | elefant**e**                                                     |                                                              |
| o               | o              | **o**caso                                                        | enc**o**ntrar                                                    | ocasenc**o**                                                        |                                                              |
| u               | u              | **u**sted                                                        | p**u**nta                                                        | Juanl**u**                                                       |                                                              |
| 자음      |                |                                                              |                                                              |                                                              |                                                              |
| b               | b              | **b**aobab                                                       |                                                              | am**b**                                                          |                                                              |
|                 | β              |                                                              | bao**b**ab                                                       | baoba**b**                                                       | es SAPI phone 집합은 IPA ' β '을 나타내는 것을 지원 하지 않습니다. 필요한 경우 IPA를 직접 사용 해야 합니다. |
| ch              | tʃ             | **ch**.e                                                       | co**ch**e                                                        | Marraque**ch**                                                   |                                                              |
| d               | d              | **d**에 도어                                                         |                                                              | portlan**d**                                                     |                                                              |
|                 | led              |                                                              | de**d**o                                                         | verda**d**                                                       | es SAPI 휴대폰 집합은 IPA ' u s '를 나타내는 것을 지원 하지 않습니다. 필요한 경우 IPA을 직접 사용 해야 합니다. |
| f               | f              | **f**ácil                                                        | ele**f**am (ante                                                     | pu**f**                                                          |                                                              |
| g               | g              | **g**앙                                                        |                                                              | dópin**g**                                                       |                                                              |
|                 | ɣ              |                                                              | **g**ua                                                         | 는**g** 입니다.                                                       | es SAPI phone 집합은 IPA ' ɣ '을 나타내는 것을 지원 하지 않습니다. 필요한 경우 IPA를 직접 사용 해야 합니다. |
| j               | j              | **내가**                                                         | cal**i**ente                                                     | 다시**y**                                                          |                                                              |
| jj              | j. j/jj         |                                                              | vi                                                        |                                                              |                                                              |
| k               | k              | **c**oche                                                        | bo**c**a                                                         | titáni**c**                                                      |                                                              |
| l               | l              | **l**ápiz                                                        | a**l**a                                                          | corde**l**                                                       |                                                              |
| ll              | ʎ              | **ll**평균                                                        | desarro**ll**o                                                   |                                                              |                                                              |
| m               | m              | **m**순서                                                       | **m**ar                                                         | álbu**m**                                                        |                                                              |
| n               | n              | **n**ada                                                         | ce**n**a                                                         | rató**n**                                                        |                                                              |
| nj              | ɲ              | **ñ**aña                                                         | ara**ñ**azo                                                      |                                                              |                                                              |
| p               | p              | **p**oca                                                         | to**p**o                                                         | o**p**                                                         |                                                              |
| r               | ɾ              |                                                              | ca**r**a                                                         | abri**r**                                                        |                                                              |
| rr              | r              | **r**adio                                                        | co**rr**e                                                        | pu**rr**                                                         |                                                              |
| s               | s              | **s**aco                                                         | va**s**o                                                         | pelo**s**                                                        |                                                              |
| t               | t              | **t**oldo                                                        | a**t**ar                                                         | disque**t**                                                      |                                                              |
| th              | θ              | **z**ebra                                                        | **z**ul                                                         | lápi**z**                                                        |                                                              |
| w               | w              | h**u**eso                                                        | ag**u**a                                                         | gua**u**                                                         |                                         |
| x               | x              | **j**ota                                                         | **j**o                                                          | j o**j**                                                        |                                                              |


## <a name="zh-cn"></a>zh-CN

Zh-cn에 대 한 TTS SAPI phone 집합은 기본 전화 세트 병음를 기반으로 합니다. 네이티브 스피커의 경우 IPA 대신 사용 하기 쉬운 SAPI (병음)를 사용 하는 것이 좋습니다. 

**발신음**

MS TTS zh-cn-CN SAPI number: 1 2 3 4 5를 사용 하 여 톤에 주석을 추가 합니다.

| 톤을 사용한 병음 예제 | 아마도  | 문자 예제 |
| ------------------------- | ----- | ------------------ |
| mā                        | ma 1 | 妈                 |
| má                        | ma 2 | 麻                 |
| mǎ                        | ma 3 | 马                 |
| mà                        | ma 4 | 骂                 |
| m                        | ma 5 | 嘛                 |

**예제**

| SAPI 예 |                               |
| ------------- | ----------------------------- |
| 문자     | 아마도                          |
| 组织关系      | zu-za 3-zhi 1-안 면 1-크 크 5 |
| 累进          | lei 3-jin 4                 |
| 西宅巷        | 크 크 1-zhai 2-xiang 4      |

## <a name="zh-tw"></a>zh-TW

Zh-cn에 대 한 SAPI 전화 집합은 기본 전화 설정 보포모포를 기반으로 합니다. 네이티브 스피커의 경우 IPA 대신 사용 하기 쉬운 SAPI (보포모포)를 사용 하는 것이 좋습니다. 

**발신음**

| SAPI 톤 | 보포모포 톤 | 예 (word) | SAPI 휴대폰 | 발음   | 병음 (拼音) |
| ---------- | ------------- | ------------------ | ----------- | ---------- | -------------------- |
| ˉ          | empty         | 偵                 | ㄓㄣ ˉ       | ㄓㄣ       | zhēn                 |
| ˊ          | ˊ             | 察                 | ㄔㄚˊ       | ㄔㄚˊ      | chá                  |
| ˇ          | ˇ             | 打                 | ㄉㄚˇ       | ㄉㄚˇ      | dǎ                   |
| ˋ          | ˋ             | 望                 | ㄨㄤˋ       | ㄨㄤˋ      | wàng                 |
| ˙          | ˙             | 影子               | 一ㄥˇ ㄗ˙  | 一ㄥˇ ㄗ˙ | yǐng zi             |

**예제**

| SAPI 예 |                |
| ------------- | -------------- |
| 문자     | 아마도           |
| 狗            | ㄍㄡˇ          |
| 然后          | ㄖㄢˊㄏㄡˋ     |
| 剪掉          | ㄐㄧㄢˇㄉㄧㄠˋ |

## <a name="ja-jp"></a>ja-JP

Ja-jp에 대해 설정 된 SAPI 전화는 일본어 기본 전화 집합 (Kana)을 기반으로 합니다. 네이티브 스피커의 경우 IPA 대신 사용 하기 쉬운 SAPI (Kana)를 사용 하는 것이 좋습니다. 

**상태**

| 상태 |                 |
| ------ | --------------- |
| 아마도   | IPA             |
| ˈ      | ˈ (mainstress) |
| +      | ˌ (substress)  |

**예제**

| SAPI 예 |              |             |
| ------------- | ------------ | ----------- |
| 문자     | 아마도         | IPA         |
| 合成          | ゴ'ウセ      | go ˈ wɯseji   |
| 所有者        | ショュ'ウ?ャ | ɕjojɯˈwɯɕja |
| 最適化        | サィテキカ +  | sajitecikaˌ |