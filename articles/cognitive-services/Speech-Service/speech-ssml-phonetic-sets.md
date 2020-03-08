---
title: 음성 발음 설정-음성 서비스
titleSuffix: Azure Cognitive Services
description: 음성 서비스 윗주 (IPA)에 대 한 음성 서비스와 설정 사용 시기에 대해 알아봅니다.
services: cognitive-services
author: zhaoyunED
manager: junwg
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/04/2020
ms.author: jiajzhan
ms.openlocfilehash: 770e97ad126f66efb43bf8cf7eb12f7510858192
ms.sourcegitcommit: bc792d0525d83f00d2329bea054ac45b2495315d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78675338"
---
# <a name="speech-service-phonetic-sets"></a>음성 서비스 음성 서비스 음성 설정

음성 서비스는 일곱 개의 언어로 구성 된 발음 영문자 ("전화 집합")를 정의 합니다. `en-US`, `fr-FR`, `de-DE`, `es-ES`, `ja-JP`, `zh-CN`및 `zh-TW`. Speech service phone 집합은 일반적으로 <a href="https://en.wikipedia.org/wiki/International_Phonetic_Alphabet" target="_blank">국제 발음 (IPA) <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>에 매핑됩니다. Speech service phone 집합은 텍스트 음성 변환 서비스 제공의 일부로 [SSML (음성 합성 마크업) 언어](speech-synthesis-markup.md)와 함께 사용 됩니다. 이 문서에서는 이러한 휴대폰 집합이 매핑되는 방법 및 전화 번호를 사용 하는 시기에 대해 알아봅니다.

# <a name="en-us"></a>[en-us](#tab/en-US)

### <a name="english-suprasegmentals"></a>영어 suprasegmentals

| 예제 1 (자음의 경우 하기 시작 하면, 모음에 대 한 단어 초기) | 예 2 (자음의 경우 Intervocalic, word 중성 핵심 이기 for 모음) | 예 3 (자음 용 Coda, 모음에 대 한 단어 최종) | 주석 |
|--|--|--|--|
| 먹으며 즐길/b er **1** r-g ax r/ | 비 afel/f ax-l aa **1** -f ax l/ | 기타/g 항목-t aa **1** r/ | 음성 서비스 전화 번호 설정 스트레스 음절의 모음 뒤에 스트레스 배치 |
| 리디렉션하거나/ih **2** -n aa-p ax r-t uw 1 n/ | dissimilarity/d 항목-s 항목 **2**-m ax-l eh 1-r ax-t 항목/ | 직원/w er 1 r k-f ao **2** r s/ | 음성 서비스 전화 번호 설정 스트레스가 낮은 음절의 모음 뒤에 스트레스 배치 |

### <a name="english-vowels"></a>영어 모음

| `sapi` | `ipa` | 예 1     | 예제 2 | 예제 3                   |
|--------|-------|---------------|-----------|-----------------------------|
| iy     | `i`   | **ea**t       | f**ee**l  | vall**e**                  |
| 항목     | `ɪ`   | **i**f        | f**i**ll  |                             |
| e     | `eɪ`  | **te**       | g**a**te  | **day**                     |
| eh     | `ɛ`   | **e**매우     | p**e**t   | m**eh** (finally 단어) |
| ae     | `æ`   | **c)**    | c**a**t   | n**ah** (마지막 단어를 드물게) |
| aa     | `ɑ`   | **o**bstinate | p**o**ppy | r**ah** (가끔 word finally) |
| ao     | `ɔ`   | **o**범위    | c**au**se | 전 세계**ah**                    |
| 네     | `ʊ`   | b**oo**k      |           |                             |
| ow     | `oʊ`  | **o**ld       | cl**o**ne | g**o**                      |
| uw     | `u`   | **U**ber      | b**oo**st | t**oo**                     |
| 아기     | `ʌ`   | **u**ncle     | c**u**t   |                             |
| 결과     | `aɪ`  | **i**ce       | b**i**te  | fl**y**                     |
| aw     | `aʊ`  | **ou**t       | s**ou**th | c**ow**                     |
| oy     | `ɔɪ`  | **oi**l       | j**oi**n  | t**oy**                     |
| y uw   | `ju`  | **Yu**ma      | h**u**man | f                     |
| ax     | `ə`   | **go**       | wom**a**n | 는**입니다.**                    |

### <a name="english-r-colored-vowels"></a>영어 R 색 모음

| `sapi` | `ipa` | 예 1    | 예제 2      | 예제 3  |
|--------|-------|--------------|----------------|------------|
| 항목 r   | `ɪɹ`  | **귀**s     | t**ir**amisu   | n**귀**   |
| eh r   | `ɛɹ`  | **공기**평면 | 앱**ar**ently | sc**ar**e  |
| 인   | `ʊɹ`  |              |                | c **(** e)   |
| ay r   | `aɪɹ` | **Ire**지역  | f**ir**eplace  | ch**oir**  |
| aw r   | `aʊɹ` | **시간**s    | p**ower**   | s   |
| ao r   | `ɔɹ`  | **또는**a)   | m**또는**al      | s**oar**   |
| aa r   | `ɑɹ`  | **ar**tist   | st**ar**t      | c**ar**    |
| er r   | `ɝ`   | **귀**번째    | b**ir**d       | f    |
| ax r   | `ɚ`   |              | 모든**er**gy    | supp**er** |

### <a name="english-semivowels"></a>영어 Semivowels

| `sapi` | `ipa` | 예 1           | 예제 2  | 예제 3 |
|--------|-------|---------------------|------------|-----------|
| w      | `w`   | **w**i, s**ue** | al**w**ays |           |
| y      | `j`   | **y**인, f**e**w   | 온 **-시**  |           |

### <a name="english-aspirated-oral-stops"></a>English aspirated 구두 중지

| `sapi` | `ipa` | 예 1 | 예제 2   | 예제 3  |
|--------|-------|-----------|-------------|------------|
| p      | `p`   | **p**전 세계   | ha**페이지**en  | fla**p**   |
| b      | `b`   | **big**   | 번호**b**er  | cra**b**   |
| t      | `t`   | **t**alk  | capi**t**al | sough**t** |
| d      | `d`   | **d**   | 실행 한**d**om  | ro**d**    |
| k      | `k`   | **c**세계   | sla**헤드**er | Ira**q**   |
| g      | `g`   | **g**o    | a**g**o     | dra**g**   |

### <a name="english-nasal-stops"></a>영어 Nasal 중지

| `sapi` | `ipa` | 예 1        | 예제 2  | 예제 3   |
|--------|-------|------------------|------------|-------------|
| m      | `m`   | **m**, smash   | ca**m**연대 | roo**m**    |
| n      | `n`   | **n**o, s**n**ow | te**n**t   | chicke**n** |
| ng     | `ŋ`   |                  | li**n**k   | s**연산**    |

### <a name="english-fricatives"></a>영어 fricatives

| `sapi` | `ipa` | 예 1   | 예제 2        | 예제 3  |
|--------|-------|-------------|------------------|------------|
| f      | `f`   | **f**o)    | le**f**t         | hal**f**   |
| v      | `v`   | **v**alue   | e**v**ent        | 로**낮음**   |
| th     | `θ`   | **th**    | empa**번째**      | mon**번째**  |
| 교환이     | `ð`   | **번째**en    | mo       | smoo**번째** |
| s      | `s`   | **s**it     | ri**s**k         | 팩트**s**  |
| z      | `z`   | **z**ap     | bu**s**y         | 어린이**s**   |
| sh     | `ʃ`   | **sh** e    | abbrevia**ti**설정 | 및   |
| zh     | `ʒ`   | **J**acques | plea     | gara**g**e |
| h      | `h`   | **h**elp    | en**h**ance      | -**h**a!  |

### <a name="english-affricates"></a>영어 affricates

| `sapi` | `ipa` | 예 1 | 예제 2    | 예제 3  |
|--------|-------|-----------|--------------|------------|
| ch     | `tʃ`  | **ch**in  | fu**t**   | atta**ch** |
| jh     | `dʒ`  | **j**oy   | ori**g**inal | oran**g**e |

### <a name="english-approximants"></a>영어 approximants

| `sapi` | `ipa` | 예 1          | 예제 2  | 예제 3 |
|--------|-------|--------------------|------------|-----------|
| l      | `l`   | **l**id, g**l**ad  | pa**l**ace | 카이**ll** |
| r      | `ɹ`   | **r**ed, b**r**ing | bo**rr**ow | ta**r**   |

# <a name="fr-fr"></a>[fr-fr](#tab/fr-FR)

### <a name="french-suprasegmentals"></a>프랑스어 suprasegmentals

Speech service phone 집합은 스트레스 음절의 모음 뒤에 스트레스를 배치 하지만 `fr-FR` Speech service phone 집합은 IPA substress ' ˌ '를 지원 하지 않습니다. IPA substress가 필요한 경우 IPA를 직접 사용 해야 합니다.

### <a name="french-vowels"></a>프랑스어 모음

| `sapi` | `ipa` | 예 1     | 예제 2       | 예제 3 |
|--------|-------|---------------|-----------------|-----------|
| a      | `a`   | **rbre**     | p**a**tte       | ir**a**   |
| aa     | `ɑ`   |               | p        | p**a**s   |
| aa ~   | `ɑ̃`  | **en**fant    | enf**en**t      | t**em**ps |
| ax     | `ə`   |               | p**e**tite      | l**e**    |
| eh     | `ɛ`   | **e**lle      | p**e**rdu       | ét**ai**t |
| eu     | `ø`   | **œu**fs      | cr**eu**ser     | qu**eu**  |
| e     | `e`   | ému           | crétin          | ôté       |
| eh ~   | `ɛ̃`  | **im**portant | p**ein**t    | **인** 경우 |
| iy     | `i`   | **dée**      | 애완**동물**      | am**i**   |
| oe     | `œ`   | **œu**f       | p**eu**r        |           |
| 돼     | `ɔ`   | **o**bstacle  | c**o**rps       |           |
| 오 ~   | `ɔ̃`  | ze **에서**      | deur**의**r     | b**설정**   |
| ow     | `o`   | **오스트레일리아**diteur  | b**eau**    | p**ô**    |
| oe ~   | `œ̃ ` | **되지**        | l**제거**(di)       | br**취소**  |
| uw     | `u`   | **ou**trage   | intr**ou**된 | **조직**    |
| uy     | `y`   | **u**ne       | p**u**n ir       | él**u**   |

### <a name="french-consonants"></a>프랑스어 자음

| `sapi` | `ipa` | 예 1   | 예제 2     | 예제 3                        |
|--------|-------|-------------|---------------|----------------------------------|
| b      | `b`   | **b**ête    | ha**b**ille   | ro**b**e                         |
| d      | `d`   | **d**ire    | ron**d**eur   | chau**d**e                       |
| f      | `f`   | **f**emme   | su**ff**ixe   | bo**f**                          |
| g      | `g`   | **g**auche  | é**g**ale     | ba**gu**e                        |
| ng     | `ŋ`   |             |               | [<sup>1 개</sup>](#fr-1)대기 |
| hy     | `ɥ`   | h**u**개   | n**u**ire     |                                  |
| k      | `k`   | **c**arte   | é**c**   | **c**                          |
| l      | `l`   | **l**긴    | é**l**ire     | ba**l**                          |
| m      | `m`   | **m**adame  | ai**m**er     | po**mm**e                        |
| n      | `n`   | **n**ou    | te**n**ir     | bo**nn**e                        |
| nj     | `ɲ`   |             |               | pei**gn**e                       |
| p      | `p`   | **p**   | 다시**p**     | ca**p**                          |
| r      | `ʁ`   | **r**at     | cha**r**iot   | senti**r**                       |
| s      | `s`   | **s**ourir  | a**ss**ez     | pa**ss**e                        |
| sh     | `ʃ`   | **ch**anter | ma**ch**   | po**ch**e                        |
| t      | `t`   | **t**ête    | ô**t**er      | ne**t**                          |
| v      | `v`   | **v**ent    | **v**입력  | rê**v**e                         |
| w      | `w`   | **ou**i     | f**ou**    |                                  |
| y      | `j`   | **y**od     | p**i**étiner  | **Ille** se                    |
| z      | `z`   | \* * z * * éro   | rai**s**onner | ro**s**e                         |
| zh     | `ʒ`   | **j**ardin  | 남자**g**er    | piè**g**e                        |
|        | `n‿`  |             |               | u**n** arbre                     |
|        | `t‿`  |             |               | **차원**                        |
|        | `z‿`  |             |               | di**x**                          |

<a id="fr-1"></a> *일부 외래 단어에 대해서만 1 
합니다.*

> [!TIP]
> `fr-FR` Speech service phone 집합은 다음 프랑스어 liasions, `n‿`, `t‿`및 `z‿`를 지원 하지 않습니다. 필요한 경우 IPA를 직접 사용 하는 것을 고려해 야 합니다.

# <a name="de-de"></a>[de-DE](#tab/de-DE)

### <a name="german-suprasegmentals"></a>독일어 suprasegmentals

| 예제 1 (자음의 경우 하기 시작 하면, 모음에 대 한 단어 초기) | 예 2 (자음의 경우 Intervocalic, word 중성 핵심 이기 for 모음) | 예 3 (자음 용 Coda, 모음에 대 한 단어 최종) | 주석 |
|--|--|--|--|
| anders/a **1** n-d ax r s/ | Multiplikationszeichen/m \_l-t iy-p l iy-k a-ts y ow **1** n s-ts ay-c n/ | Biologie/b iy-ow-l ow-g iy **1**/ | 음성 서비스 전화 번호 설정 스트레스 음절의 모음 뒤에 스트레스 배치 |
| Allgemeinwissen/a **2** l-g ax-m ay 1 n-v 항목-s n/ | Abfallentsorgungsfirma/a 1 p-f a l-^ eh n t-z **n2** ax r-g. s-f 항목 ax r-m a/ | Computertomographie/k n m-p y uw 1-t ax r-t ow-m ow-g r a-f iy **2**/ | 음성 서비스 전화 번호 설정 스트레스가 낮은 음절의 모음 뒤에 스트레스 배치 |

### <a name="german-vowels"></a>독일어 모음

| `sapi` | `ipa`     | 예 1                             | 예제 2     | 예제 3                          |
|--------|-----------|---------------------------------------|---------------|------------------------------------|
| a:     | `aː`      | **Ber**                              | Maßst**a**b   | 스키마                         |
| a      | `a`       | **Bfall**.                            | B**a**ch      | Agath                         |
| 돼     | `ɔ`       | **O**sten                             | Pf**o**   |                                    |
| eh    | `ɛː`      | **Ä**hnlichkeit                       | B**ä**r       | [<sup>1</sup>](#de-v-1) Fasci**ae** |
| eh     | `ɛ`       | **ä**ndern                            | Proz**e**nt   | Amygdal**ae**                      |
| ax     | `ə`       | [<sup>2</sup>](#de-v-2)' v**e**r | Aach**e**n    | 조각**e**                          |
| iy     | `iː`      | **실행 됨**                              | abb**ie**gt   | Relativitätstheor**ie**            |
| 항목     | `ɪ`       | **Nnung**                            | s**i**ngen    | 목조**y**                          |
| eu     | `øː`      | **Ö**send                              | abl**ö**sten  | Malm**ö**                          |
| ow     | `o`, `oː` | **o**hne                              | 신데렐라**o**n    | Trept**ow**                        |
| oe     | `œ`       | **Ö**ffnung                           | bef**ö**rdern |                                    |
| e     | `e`, `eː` | **E-learning**하드                          | .abf**e**gt    | b                                  |
| uw     | `uː`      | **U**do                               | H**u**t       | Akk**u**                           |
| 네     | `ʊ`       | **U**nterschiedes                     | b**u**nt      |                                    |
| agent     | `yː`      | **Ü**bermut                           | default.pfl**ü**gt    | 남자**ü**                           |
| uy     | `ʏ`       | **ü**ppig                             | S**y**스템    |                                    |

<a id="de-v-1"></a>
예 *: Fasci**ae**와 같은 외국어 원본 단어로만* **1**<br>
<a id="de-v-2"></a>
**2** *단어 (intially)와 같은 외래 원본 단어에만해당 합니다. 음절-처음에는 ' v**e**rstauen '입니다.*

### <a name="german-diphthong"></a>독일어 diphthong

| `sapi` | `ipa`       | 예 1    | 예제 2          | 예제 3 |
|--------|-------------|--------------|--------------------|-----------|
| 결과     | `ai`        | **ei**nsam   | Unabhängigk**ei**t | Abt**ei** |
| aw     | `au`        | **오스트레일리아**ßen    | abb**au**st        | St**au**  |
| oy     | `ɔy`, `ɔʏ̯` | **Eu**phorie | tr**äu**mt         | sch-m**eu** |

### <a name="german-semivowels"></a>독일어 semivowels

| `sapi` | `ipa` | 예 1 | 예제 2    | 예제 3  |
|--------|-------|-----------|--------------|------------|
| ax r   | `ɐ`   |           | abänd**er**n | 잠금 |

### <a name="german-consonants"></a>독일어 자음

| `sapi` | `ipa` | 예 1 | 예제 2 | 예제 3 |
|--|--|--|--|--|
| b | `b` | **B**ank |  | [<sup>1</sup>](#de-c-1) Pu**b** |  |
| c | `ç` | **Ch**emie | mögli**ch**st | [<sup>2</sup>](#de-c-2)i**ch** |
| d | `d` | **d**anken | [<sup>3</sup>](#de-c-3) Len**d**l | [<sup>4</sup>](#de-c-4) Clau**d**e |  |
| jh | `ʤ` | **J**eff | gemana**g**t | [<sup>5</sup>](#de-c-5) 변경 (**g**e |
| f | `f` | **F**ahrtdauer | angri**ff**slustig | abbruchrei**f** |  |
| g | `g` | **g**전 세계 |  | [<sup>6</sup>](#de-c-6) Gre**g** |  |
| h | `h` | **H**-ausanbau |  |  |  |
| y | `j` | **J**od | Reakt**i** | hu-hu**i** |  |
| k | `k` | **K**oma-uri | E**k**t | Flec**k** |  |
| l | `l` | **l**au | ähne**l**n | zuvie**l** |  |
| m | `m` | **M**전 세계 | **M**t | Leh**m** |  |
| n | `n` | **n**취소 | u**n**d | 나 에 게 |  |
| ng | `ŋ` | [<sup>7</sup>](#de-c-7) **-uyen** | Schwa**빈** | R**ing** |  |
| p | `p` | **P**artner | abru**p**t | Ti**p** |  |
| pf | `pf` | **Pf**erd | dam**pf**t | **Pf** 로 |  |
| r | `ʀ`, `r`, `ʁ` | **R**eise | knu**rr**t | Haa**r** |  |
| s | `s` | [<sup>8</sup>](#de-c-8)**S**taccato | bi**s**t | mie**s** |  |
| sh | `ʃ` | **Sch-m**u) | mi**sch-m**t | lappi**sch-m** |  |
| t | `t` | **T**raum | S**t**raße | Mu**t** |  |
| ts | `ts` | **Z**ug | Ar**z**t | Wit**z** |  |
| ch | `tʃ` | **Tsch**ecto en | aufgepu**tsch**t | bundesdeu**tsch** |  |
| v | `v` | **w**inken | Q**u**alle | [<sup>9</sup>](#de-c-9) Gr**지향**ve |  |
| x | [<sup>10</sup>](#de-c-10)`x`,[<sup>11</sup>](#de-c-11)`ç` | [<sup>12</sup>](#de-c-12) Ba**ch**erach | Ma**ch**t mögli**ch**st | Schma**ch** ' i**ch** |
| z | `z` | **s**uper |  |  |  |
| zh | `ʒ` | **G**enre | B**re**ezinski | Edvi**g**e |

<a id="de-c-1"></a>
**1** 은 *Pu**b**와 같은 외부 원본 단어로만 1입니다.*<br>
<a id="de-c-2"></a> *"e" 및 "i" 뒤에 
2 소프트 "ch"*<br>
<a id="de-c-3"></a>
**3** 은 ( *는) Len**d**l과 같은 외부 원점 단어로만 수행 합니다.*<br>
<a id="de-c-4"></a>
**4** *는 clau**d**e와 같은 외국어의 단어만* 을 기준으로 합니다.<br>
<a id="de-c-5"></a>
 *는 변경 (**g**e와 같이 외국어의 단어 에서만 5입니다.*<br>
<a id="de-c-6"></a>
**6** *단어 (예: Gre**g**와 같은 외국어 terminally)로만 시작 합니다.*<br>
<a id="de-c-7"></a>
**7** 은 *외부 원점 (예: 고가와같은)의 단어 에서만 7입니다.*<br>
<a id="de-c-8"></a>
 *: **S**taccato와 같은 외부 원본에만 8이 있습니다.*<br>
<a id="de-c-9"></a>
는 *Gr**oo**ve와 같이 외국어의 단어 에서만 9입니다.*<br>
<a id="de-c-10"></a>
**10** *IPA `x`는 모든 비 front 모음 (a, aa, 오, ow, diphthong aw) 후에 하드 "ch"입니다.*<br>
<a id="de-c-11"></a>
**11** *IPA `ç`는 프런트 모음 (ih, ih, eh, ae, uy, ue, oe, eu도 diphthongs ay, oy) 및 자음 후에 소프트 ' ch '입니다* .<br>
<a id="de-c-12"></a>
**12** *단어-처음에는 **J**uan과 같은 외부 원본의 단어만 있습니다. 음절-처음에는 Ba**ch**erach와 같은 단어에도 있습니다.*<br>

### <a name="german-oral-consonants"></a>독일어 구두 자음

| `sapi` | `ipa` | 예 1                                  |
|--------|-------|--------------------------------------------|
| ^      | `ʔ`   | beachtlich/b ax-^ a 1 x t-l 항목 c/ |

> [!NOTE]
> 두 개의 모음이 진짜 diphthong는 점을 제외 하 고 두 개의 고유한 모음 사이에 [gs\] phone을 추가 해야 합니다. 이 구두 자음은 glottal stop입니다. 자세한 내용은 </a>glottal stop <span class="docon docon-navigate-external x-hidden-focus"></a>를 <a href="http://en.wikipedia.org/wiki/Glottal_stop" target="_blank">참조 하세요.

# <a name="es-es"></a>[es](#tab/es-ES)

### <a name="spanish-vowels"></a>스페인어 모음

| `sapi` | `ipa` | 예 1    | 예제 2     | 예제 3    |
|--------|-------|--------------|---------------|--------------|
| a      | `a`   | **lto**     | c**a**ntar    | cas**a**     |
| i      | `i`   | **bérica**  | av**i**spa    | 세금     |
| e      | `e`   | **e**lefante | at**e**    | elefant**e** |
| o      | `o`   | **o**caso    | enc**o**ntrar | ocasenc**o** |
| u      | `u`   | **u**sted    | p**u**nta     | Juanl**u**   |

### <a name="spanish-consonants"></a>스페인어 자음

| `sapi` | `ipa`      | 예 1  | 예제 2      | 예제 3      |
|--------|------------|------------|----------------|----------------|
| b      | `b`        | **b**aobab |                | am**b**        |
|        | `β`        |            | bao**b**ab     | baoba**b**     |
| ch     | `tʃ`       | **ch**.e | co**ch**e      | Marraque**ch** |
| d      | `d`        | **d**에 도어   |                | portlan**d**   |
|        | `ð`        |            | de**d**o       | verda**d**     |
| f      | `f`        | **f**ácil  | ele**f**am (ante   | pu**f**        |
| g      | `g`        | **g**앙  |                | dópin**g**     |
|        | `ɣ`        |            | **g**ua       | 는**g** 입니다.     |
| j      | `j`        | **내가**   | cal**i**ente   | 다시**y**        |
| jj     | `j.j` `jj` |            | vi      |                |
| k      | `k`        | **c**oche  | bo**c**a       | titáni**c**    |
| l      | `l`        | **l**ápiz  | a**l**a        | corde**l**     |
| ll     | `ʎ`        | **ll**평균  | desarro**ll**o |                |
| m      | `m`        | **m**순서 | **m**ar       | álbu**m**      |
| n      | `n`        | **n**ada   | ce**n**a       | rató**n**      |
| nj     | `ɲ`        | **ñ**aña   | ara**ñ**azo    |                |
| p      | `p`        | **p**oca   | to**p**o       | o**p**       |
| r      | `ɾ`        |            | ca**r**a       | abri**r**      |
| rr     | `r`        | **r**adio  | co**rr**e      | pu**rr**       |
| s      | `s`        | **s**aco   | va**s**o       | pelo**s**      |
| t      | `t`        | **t**oldo  | a**t**ar       | disque**t**    |
| th     | `θ`        | **z**ebra  | **z**ul       | lápi**z**      |
| w      | `w`        | h**u**eso  | ag**u**a       | gua**u**       |
| x      | `x`        | **j**ota   | **j**o        | j o**j**      |

> [!TIP]
> `es-ES` Speech service phone 집합은 다음과 같은 스페인어 IPA, `β`, `ð`및 `ɣ`를 지원 하지 않습니다. 필요한 경우 IPA를 직접 사용 하는 것을 고려해 야 합니다.

# <a name="zh-cn"></a>[zh-CN](#tab/zh-CN)

`zh-CN`에 대해 설정 된 음성 서비스 전화는 네이티브 폰 <a href="https://en.wikipedia.org/wiki/Pinyin" target="_blank">핀 <span class="docon docon-navigate-external x-hidden-focus"></span> </a> 집합을 기반으로 합니다.

### <a name="tone"></a>음

| 병음 톤 | `sapi` | 문자 예제 |
|-------------|--------|-------------------|
| mā          | ma 1  | 妈                 |
| má          | ma 2  | 麻                 |
| mǎ          | ma 3  | 马                 |
| mà          | ma 4  | 骂                 |
| m          | ma 5  | 嘛                 |

#### <a name="example"></a>예제

| 문자 | Speech Service                |
|-----------|-------------------------------|
| 组织关系      | zu-za 3-zhi 1-안 면 1-크 크 5 |
| 累进        | lei 3-jin 4                 |
| 西宅巷       | 크 크 1-zhai 2-xiang 4      |

# <a name="zh-tw"></a>[zh-cn-HY 얕은](#tab/zh-TW)

`zh-TW`에 대해 설정 된 음성 서비스 전화는 기본 전화 <a href="https://en.wikipedia.org/wiki/Bopomofo" target="_blank">보포모포 <span class="docon docon-navigate-external x-hidden-focus"></span> </a> 집합을 기반으로 합니다.

### <a name="tone"></a>음

| 음성 서비스 톤 | 보포모포 톤 | 예 (word) | 음성 서비스 폰 | 발음 | 병음 (拼音) |
|---------------------|---------------|----------------|-----------------------|----------|-------------|
| ˉ                   | empty         | 偵              | ㄓㄣ ˉ                   | ㄓㄣ       | zhēn        |
| ˊ                   | ˊ             | 察              | ㄔㄚˊ                   | ㄔㄚˊ      | chá         |
| ˇ                   | ˇ             | 打              | ㄉㄚˇ                   | ㄉㄚˇ      | dǎ          |
| ˋ                   | ˋ             | 望              | ㄨㄤˋ                   | ㄨㄤˋ      | wàng        |
| ˙                   | ˙             | 影子             | 一ㄥˇ ㄗ˙               | 一ㄥˇ ㄗ˙  | yǐng zi    |

#### <a name="example"></a>예제

| 문자 | `sapi`   |
|-----------|----------|
| 狗         | ㄍㄡˇ      |
| 然后        | ㄖㄢˊㄏㄡˋ   |
| 剪掉        | ㄐㄧㄢˇㄉㄧㄠˋ |

# <a name="ja-jp"></a>[ja-JP](#tab/ja-JP)

`ja-JP`에 대해 설정 된 음성 서비스 전화는 기본 phone가 <a href="https://en.wikipedia.org/wiki/Kana" target="_blank">나 <span class="docon docon-navigate-external x-hidden-focus"></span> </a> 집합을 기반으로 합니다.

### <a name="stress"></a>상태

| `sapi` | `ipa`          |
|--------|----------------|
| `ˈ`    | `ˈ` mainstress |
| `+`    | `ˌ` substress  |

#### <a name="example"></a>예제

| 문자 | `sapi`  | `ipa`       |
|-----------|---------|-------------|
| 合成        | ゴ'ウセ    | go ˈ wɯseji   |
| 所有者       | ショュ'ウ?ャ | ɕjojɯˈwɯɕja |
| 最適化       | サィテキカ +  | sajitecikaˌ |

***