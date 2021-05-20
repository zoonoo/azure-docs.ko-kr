---
title: 음성 발음 기호 - Speech Service
titleSuffix: Azure Cognitive Services
description: Speech Service 음성 기호가 IPA(국제 음성 기호)에 매핑되는 방법 및 설정 사용 시기에 대해 알아봅니다.
services: cognitive-services
author: zhaoyunED
manager: junwg
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/04/2020
ms.author: jiajzhan
ms.openlocfilehash: 8e5480b7412291c427c5eaea94a63343f12c62cf
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106077519"
---
# <a name="speech-service-phonetic-sets"></a>Speech Service 음성 기호

Speech Service는 일곱 개의 언어로 구성된 음성 기호("phone sets")을 정의합니다(`en-US`, `fr-FR`, `de-DE`, `es-ES`, `ja-JP`, `zh-CN` 및 `zh-TW`). Speech Service 음성 기호는 일반적으로 <a href="https://en.wikipedia.org/wiki/International_Phonetic_Alphabet" target="_blank">IPA(국제 음성 기호)</a>에 매핑됩니다. Speech Service 음성 기호는 텍스트 음성 변환 서비스 제공의 일부로 [SSML(음성 합성 생성 언어)](speech-synthesis-markup.md)과 함께 사용됩니다. 이 문서에서는 이러한 음성 기호가 매핑되는 방법 및 음성 기호를 사용하는 시기에 대해 알아봅니다.

# <a name="en-us"></a>[en-US](#tab/en-US)

### <a name="english-suprasegmentals"></a>영어 초분절

| 예제 1(자음은 시작, 모음은 단어 첫글자) | 예제 2(자음은 모음 사이, 모음은 단어 내측핵) | 예제 3(자음은 종결부, 모음은 단어 마지막) | 주석 |
|--|--|--|--|
| burger  /b er **1** r - g ax r/ | falafel  /f ax - l aa **1** - f ax  l/ | guitar  /g ih - t aa **1** r/ | Speech Service 음성 기호는 강조 음절의 모음 뒤에 강세를 둠 |
| inopportune /ih **2** - n aa - p ax r - t uw 1 n/ | dissimilarity  /d ih - s ih **2**- m ax -  l eh 1 - r ax - t iy/ | workforce /w er 1 r k - f ao **2** r s/ | Speech Service 음성 기호는 하위 강조 음절의 모음 뒤에 강세를 둠 |

### <a name="english-vowels"></a>영어 모음

| `sapi` | `ipa` | 예제 1     | 예 2 | 예제 3                   |
|--------|-------|---------------|-----------|-----------------------------|
| iy     | `i`   | **ea** t       | f **ee** l  | vall **ey**                  |
| ih     | `ɪ`   | **i** f        | f **i** ll  |                             |
| ey     | `eɪ`  | **a** te       | g **a** te  | d **ay**                     |
| eh     | `ɛ`   | **e** very     | p **e** t   | m **eh**(희귀한 단어) |
| ae     | `æ`   | **a** ctive    | c **a** t   | n **ah**(희귀한 단어) |
| aa     | `ɑ`   | **o** bstinate | p **o** ppy | r **ah**(희귀한 단어) |
| ao     | `ɔ`   | **o** range    | c **au** se | Ut **ah**                    |
| uh     | `ʊ`   | b **oo** k      |           |                             |
| ow     | `oʊ`  | **o** ld       | cl **o** ne | g **o**                      |
| uw     | `u`   | **U** ber      | b **oo** st | t **oo**                     |
| ah     | `ʌ`   | **u** ncle     | c **u** t   |                             |
| ay     | `aɪ`  | **i** ce       | b **i** te  | fl **y**                     |
| aw     | `aʊ`  | **ou** t       | s **ou** th | c **ow**                     |
| oy     | `ɔɪ`  | **oi** l       | j **oi** n  | t **oy**                     |
| y uw   | `ju`  | **Yu** ma      | h **u** man | f **ew**                     |
| ax     | `ə`   | **a** go       | wom **a** n | are **a**                    |

### <a name="english-r-colored-vowels"></a>영어 R 색 모음

| `sapi` | `ipa` | 예제 1    | 예 2      | 예제 3  |
|--------|-------|--------------|----------------|------------|
| ih r   | `ɪɹ`  | **ear** s     | t **ir** amisu   | n **ear**   |
| eh r   | `ɛɹ`  | **air** plane | app **ar** ently | sc **ar** e  |
| uh r   | `ʊɹ`  |              |                | c **ur** e   |
| ay r   | `aɪɹ` | **Ire** land  | f **ir** eplace  | ch **oir**  |
| aw r   | `aʊɹ` | **hour** s    | p **ower** ful   | s **our**   |
| ao r   | `ɔɹ`  | **or** ange   | m **or** al      | s **oar**   |
| aa r   | `ɑɹ`  | **ar** tist   | st **ar** t      | c **ar**    |
| er r   | `ɝ`   | **ear** th    | b **ir** d       | f **ur**    |
| ax r   | `ɚ`   |              | all **er** gy    | supp **er** |

### <a name="english-semivowels"></a>영어 반모음

| `sapi` | `ipa` | 예제 1           | 예 2  | 예제 3 |
|--------|-------|---------------------|------------|-----------|
| w      | `w`   | **w** ith, s **ue** de | al **w** ays |           |
| y      | `j`   | **y** ard, f **e** w   | on **i** on  |           |

### <a name="english-aspirated-oral-stops"></a>영어 기식음의 구두 폐쇄음

| `sapi` | `ipa` | 예제 1 | 예 2   | 예제 3  |
|--------|-------|-----------|-------------|------------|
| p      | `p`   | **p** ut   | ha **pp** en  | fla **p**   |
| b      | `b`   | **b** ig   | num **b** er  | cra **b**   |
| t      | `t`   | **t** alk  | capi **t** al | sough **t** |
| 일      | `d`   | **d** ig   | ran **d** om  | ro **d**    |
| k      | `k`   | **c** ut   | sla **ck** er | Ira **q**   |
| g      | `g`   | **g** o    | a **g** o     | dra **g**   |

### <a name="english-nasal-stops"></a>영어 비음 폐쇄음

| `sapi` | `ipa` | 예제 1        | 예 2  | 예제 3   |
|--------|-------|------------------|------------|-------------|
| 분      | `m`   | **m** at, smash   | ca **m** era | roo **m**    |
| n      | `n`   | **n** o, s **n** ow | te **n** t   | chicke **n** |
| ng     | `ŋ`   |                  | li **n** k   | s **ing**    |

### <a name="english-fricatives"></a>영어 마찰음

| `sapi` | `ipa` | 예제 1   | 예 2        | 예제 3  |
|--------|-------|-------------|------------------|------------|
| f      | `f`   | **f** ork    | le **f** t         | hal **f**   |
| v      | `v`   | **v** alue   | e **v** ent        | lo **v** e   |
| th     | `θ`   | **th** in    | empa **th** y      | mon **th**  |
| dh     | `ð`   | **th** en    | mo **th** er       | smoo **th** |
| 초      | `s`   | **s** it     | ri **s** k         | fact **s**  |
| z      | `z`   | **z** ap     | bu **s** y         | kid **s**   |
| sh     | `ʃ`   | **sh** e    | abbrevia **ti** on | ru **sh**   |
| zh     | `ʒ`   | **J** acques | plea **s** ure     | gara **g** e |
| h      | `h`   | **h** elp    | en **h** ance      | a-**h** a!  |

### <a name="english-affricates"></a>영어 파찰음

| `sapi` | `ipa` | 예제 1 | 예 2    | 예제 3  |
|--------|-------|-----------|--------------|------------|
| ch     | `tʃ`  | **ch** in  | fu **t** ure   | atta **ch** |
| jh     | `dʒ`  | **j** oy   | ori **g** inal | oran **g** e |

### <a name="english-approximants"></a>영어 근접음

| `sapi` | `ipa` | 예제 1          | 예 2  | 예제 3 |
|--------|-------|--------------------|------------|-----------|
| l      | `l`   | **l** id, g **l** ad  | pa **l** ace | chi **ll** |
| r      | `ɹ`   | **r** ed, b **r** ing | bo **rr** ow | ta **r**   |

# <a name="fr-fr"></a>[fr-FR](#tab/fr-FR)

### <a name="french-suprasegmentals"></a>프랑스어 초분절

Speech Service 음성 기호는 강조 음절의 모음 뒤에 강세를 두지만 `fr-FR` Speech Service 음성 기호는 IPA 하위 강조 'ˌ'를 지원하지 않습니다. IPA 하위 강조가 필요한 경우 IPA를 직접 사용해야 합니다.

### <a name="french-vowels"></a>프랑스어 모음

| `sapi` | `ipa` | 예제 1     | 예 2       | 예제 3 |
|--------|-------|---------------|-----------------|-----------|
| a      | `a`   | **a** rbre     | p **a** tte       | ir **a**   |
| aa     | `ɑ`   |               | p **â** te        | p **a** s   |
| aa ~   | `ɑ̃`  | **en** fant    | enf **en** t      | t **em** ps |
| ax     | `ə`   |               | p **e** tite      | l **e**    |
| eh     | `ɛ`   | **e** lle      | p **e** rdu       | ét **ai** t |
| eu     | `ø`   | **œu** fs      | cr **eu** ser     | qu **eu**  |
| ey     | `e`   | ému           | crétin          | ôté       |
| eh ~   | `ɛ̃`  | **im** portant | p **ein** ture    | mat **in** |
| iy     | `i`   | **i** dée      | pet **i** te      | am **i**   |
| oe     | `œ`   | **œu** f       | p **eu** r        |           |
| oh     | `ɔ`   | **o** bstacle  | c **o** rps       |           |
| oh ~   | `ɔ̃`  | **on** ze      | r **on** deur     | b **on**   |
| ow     | `o`   | **au** diteur  | b **eau** coup    | p **ô**    |
| oe ~   | `œ̃ ` | **un**        | l **un** di       | br **un**  |
| uw     | `u`   | **ou** trage   | intr **ou** vable | **ou**    |
| uy     | `y`   | **u** ne       | p **u** nir       | él **u**   |

### <a name="french-consonants"></a>프랑스어 자음

| `sapi` | `ipa` | 예제 1   | 예 2     | 예제 3                        |
|--------|-------|-------------|---------------|----------------------------------|
| b      | `b`   | **b** ête    | ha **b** ille   | ro **b** e                         |
| 일      | `d`   | **d** ire    | ron **d** eur   | chau **d** e                       |
| f      | `f`   | **f** emme   | su **ff** ixe   | bo **f**                          |
| g      | `g`   | **g** auche  | é **g** ale     | ba **gu** e                        |
| ng     | `ŋ`   |             |               | [<sup>1</sup>](#fr-1)park **ing** |
| hy     | `ɥ`   | h **u** ile   | n **u** ire     |                                  |
| k      | `k`   | **c** arte   | é **c** aille   | be **c**                          |
| l      | `l`   | **l** ong    | é **l** ire     | ba **l**                          |
| 분      | `m`   | **m** adame  | ai **m** er     | po **mm** e                        |
| n      | `n`   | **n** ous    | te **n** ir     | bo **nn** e                        |
| nj     | `ɲ`   |             |               | pei **gn** e                       |
| p      | `p`   | **p** atte   | re **p** as     | ca **p**                          |
| r      | `ʁ`   | **r** at     | cha **r** iot   | senti **r**                       |
| 초      | `s`   | **s** ourir  | a **ss** ez     | pa **ss** e                        |
| sh     | `ʃ`   | **ch** anter | ma **ch** ine   | po **ch** e                        |
| t      | `t`   | **t** ête    | ô **t** er      | ne **t**                          |
| v      | `v`   | **v** ent    | in **v** enter  | rê **v** e                         |
| w      | `w`   | **ou** i     | f **ou** ine    |                                  |
| y      | `j`   | **y** od     | p **i** étiner  | Marse **ille**                    |
| z      | `z`   | **z **éro   | rai **s** onner | ro **s** e                         |
| zh     | `ʒ`   | **j** ardin  | man **g** er    | piè **g** e                        |
|        | `n‿`  |             |               | u **n** arbre                     |
|        | `t‿`  |             |               | quan **d**                        |
|        | `z‿`  |             |               | di **x**                          |

<a id="fr-1"></a>
**1** *일부 외래어에만 해당합니다.*

> [!TIP]
> `fr-FR` Speech Service 음성 기호는 다음 프랑스어 liasions, `n‿`, `t‿` 및 `z‿`를 지원하지 않습니다. 필요한 경우 IPA를 직접 사용하는 것을 고려해야 합니다.

# <a name="de-de"></a>[de-DE](#tab/de-DE)

### <a name="german-suprasegmentals"></a>독일어 초분절

| 예제 1(자음은 시작, 모음은 단어 첫글자) | 예제 2(자음은 모음 사이, 모음은 단어 내측핵) | 예제 3(자음은 종결부, 모음은 단어 마지막) | 주석 |
|--|--|--|--|
| anders /a **1** n - d ax r s/ | Multiplikationszeichen /m uh l - t iy - p l iy - k a - ts y ow **1** n s - ts ay - c n/ | Biologie /b iy - ow - l ow - g iy **1**/ | Speech Service 음성 기호는 강조 음절의 모음 뒤에 강세를 둠 |
| Allgemeinwissen /a **2** l - g ax - m ay 1 n - v ih - s n/ | Abfallentsorgungsfirma /a 1 p - f a l - ^ eh n t - z oh **2** ax r - g uh ng s - f ih ax r - m  a/ | Computertomographie /k oh m - p y uw 1 - t ax r - t ow - m ow - g r a - f iy **2**/ | Speech Service 음성 기호는 하위 강조 음절의 모음 뒤에 강세를 둠 |

### <a name="german-vowels"></a>독일어 모음

| `sapi` | `ipa`     | 예제 1                             | 예 2     | 예제 3                          |
|--------|-----------|---------------------------------------|---------------|------------------------------------|
| a:     | `aː`      | **A** ber                              | Maßst **a** b   | Schem **a**                         |
| a      | `a`       | **A** bfall                            | B **a** ch      | Agath **a**                         |
| oh     | `ɔ`       | **O** sten                             | Pf **o** sten   |                                    |
| eh:    | `ɛː`      | **Ä** hnlichkeit                       | B **ä** r       | [<sup>1</sup>](#de-v-1)Fasci **ae** |
| eh     | `ɛ`       | **ä** ndern                            | Proz **e** nt   | Amygdal **ae**                      |
| ax     | `ə`       | [<sup>2</sup>](#de-v-2)'v **e** rstauen | Aach **e** n    | Frag **e**                          |
| iy     | `iː`      | **I** ran                              | abb **ie** gt   | Relativitätstheor **ie**            |
| ih     | `ɪ`       | **I** nnung                            | s **i** ngen    | Wood **y**                          |
| eu     | `øː`      | **Ö** sen                              | abl **ö** sten  | Malm **ö**                          |
| ow     | `o`, `oː` | **o** hne                              | Balk **o** n    | Trept **ow**                        |
| oe     | `œ`       | **Ö** ffnung                           | bef **ö** rdern |                                    |
| ey     | `e`, `eː` | **E** berhard                          | abf **e** gt    | b                                  |
| uw     | `uː`      | **U** do                               | H **u** t       | Akk **u**                           |
| uh     | `ʊ`       | **U** nterschiedes                     | b **u** nt      |                                    |
| ue     | `yː`      | **Ü** bermut                           | pfl **ü** gt    | Men **ü**                           |
| uy     | `ʏ`       | **ü** ppig                             | S **y** stem    |                                    |

<a id="de-v-1"></a>
**1** *Fasci **ae** 와 같은 외래어의 경우에만 해당합니다.*<br>
<a id="de-v-2"></a>
**2** ***A** ppointment와 같은 외래어의 경우에만 단어 시작입니다. 'v **e** rstauen의 음절 시작입니다.*

### <a name="german-diphthong"></a>독일어 이중 모음

| `sapi` | `ipa`       | 예제 1    | 예 2          | 예제 3 |
|--------|-------------|--------------|--------------------|-----------|
| ay     | `ai`        | **ei** nsam   | Unabhängigk **ei** t | Abt **ei** |
| aw     | `au`        | **au** ßen    | abb **au** st        | St **au**  |
| oy     | `ɔy`, `ɔʏ̯` | **Eu** phorie | tr **äu** mt         | sch **eu** |

### <a name="german-semivowels"></a>독일어 반모음

| `sapi` | `ipa` | 예제 1 | 예 2    | 예제 3  |
|--------|-------|-----------|--------------|------------|
| ax r   | `ɐ`   |           | abänd **er** n | lock **er** |

### <a name="german-consonants"></a>독일어 자음

| `sapi` | `ipa` | 예제 1 | 예 2 | 예제 3 |
|--|--|--|--|--|
| b | `b` | **B** ank | | [<sup>1</sup>](#de-c-1)Pu **b** | 
| c | `ç` | **Ch** emie | mögli **ch** st | [<sup>2</sup>](#de-c-2)i **ch** |
| 일 | `d` | **d** anken | [<sup>3</sup>](#de-c-3)Len **d** l | [<sup>4</sup>](#de-c-4)Clau **d** e | 
| jh | `ʤ` | **J** eff | gemana **g** t | [<sup>5</sup>](#de-c-5)Chan **g** e |
| f | `f` | **F** ahrtdauer | angri **ff** slustig | abbruchrei **f** |  
| g | `g` | **g** ut |  [<sup>6</sup>](#de-c-6)Gre **g** |  |
| h | `h` | **H** ausanbau |  |  | 
| y | `j` | **J** od | Reakt **i** on | hu **i** | 
| k | `k` | **K** oma | Aspe **k** t | Flec **k** | 
| l | `l` | **l** au | ähne **l** n | zuvie **l** | 
| 분 | `m` | **M** ut | A **m** t | Leh **m** | 
| n | `n` | **n** un | u **n** d | Huh **n** | 
| ng | `ŋ` | [<sup>7</sup>](#de-c-7)**Ng** uyen | Schwa **nk** | R **ing** | 
| p | `p` | **P** artner | abru **p** t | Ti **p** | 
| pf | `pf` | **Pf** erd | dam **pf** t | To **pf** |
| r | `ʀ`, `r`, `ʁ` | **R** eise | knu **rr** t | Haa **r** | 
| 초 | `s` | [<sup>8</sup>](#de-c-8)**S** taccato | bi **s** t | mie **s** | 
| sh | `ʃ` | **Sch** ule | mi **sch** t | lappi **sch** | 
| t | `t` | **T** raum | S **t** raße | Mu **t** | 
| ts | `ts` | **Z** ug | Ar **z** t | Wit **z** | 
| ch | `tʃ` | **Tsch** echien | aufgepu **tsch** t | bundesdeu **tsch** | 
| v | `v` | **w** inken | Q **u** alle | [<sup>9</sup>](#de-c-9)Gr **oo** ve | 
| x | [<sup>10</sup>](#de-c-10)`x`,[<sup>11</sup>](#de-c-11)`ç` | [<sup>12</sup>](#de-c-12)Ba **ch** erach | Ma **ch** t mögli **ch** st | Schma **ch** 'i **ch** |
| z | `z` | **s** uper |  |  | 
| zh | `ʒ` | **G** enre | B **re** ezinski | Edvi **g** e |

<a id="de-c-1"></a>
**1** *Pu **b** 와 같은 외래어의 경우에만 해당합니다.*<br>
<a id="de-c-2"></a>
**2** *"e" 및 "i" 뒤의 소프트 "ch"*<br>
<a id="de-c-3"></a>
**3** *Len **d** l와 같은 외래어의 경우에만 해당합니다.*<br>
<a id="de-c-4"></a>
**4** *Clau **d** e와 같은 외래어의 경우에만 해당합니다.*<br>
<a id="de-c-5"></a>
**5** *Chan **g** e와 같은 외래어의 경우에만 해당합니다.*<br>
<a id="de-c-6"></a>
**6** *Gre **g** 와 같은 외래어의 경우에만 단어 종결입니다.*<br>
<a id="de-c-7"></a>
**7** ***Ng** uyen과 같은 외래어의 경우에만 해당합니다.*<br>
<a id="de-c-8"></a>
**8** ***S** taccato와 같은 외래어의 경우에만 해당합니다.*<br>
<a id="de-c-9"></a>
**9** *Gr **oo** ve와 같은 외래어의 경우에만 해당합니다.*<br>
<a id="de-c-10"></a>
**10** *IPA `x`는 모든 비전설 모음(a, aa, oh, ow, uh, uw 및 이중 모음 aw) 뒤의 하드 "ch"입니다.*<br>
<a id="de-c-11"></a>
**11** *IPA `ç`는 전설 모음(ih, iy, eh, ae, uy, ue, oe, eu, 이중 모음 ay, oy) 및 자음 뒤의 소프트 'ch'입니다.*<br>
<a id="de-c-12"></a>
**12** ***J** uan과 같은 외래어의 경우에만 단어 시작입니다. Ba **ch** erach와 같은 단어에도 음절 시작입니다.*<br>

### <a name="german-oral-consonants"></a>German 구두 자음

| `sapi` | `ipa` | 예 1                                  |
|--------|-------|--------------------------------------------|
| ^      | `ʔ`   | beachtlich     /b ax - ^ a 1 x t - l ih c/ |

> [!NOTE]
> 두 개의 모음이 진짜 이중 모음인 것을 제외하고 두 개의 고유한 모음 사이에 [gs\] 음성을 추가해야 합니다. 이 구두 자음은 성문 폐쇄음입니다. 자세한 내용은 <a href="http://en.wikipedia.org/wiki/Glottal_stop" target="_blank">성문 폐쇄음<span class="docon docon-navigate-external x-hidden-focus"></a></a>을 참조하세요.

# <a name="es-es"></a>[es-ES](#tab/es-ES)

### <a name="spanish-vowels"></a>스페인어 모음

| `sapi` | `ipa` | 예제 1    | 예 2     | 예제 3    |
|--------|-------|--------------|---------------|--------------|
| a      | `a`   | **a** lto     | c **a** ntar    | cas **a**     |
| i      | `i`   | **i** bérica  | av **i** spa    | tax **i**     |
| e      | `e`   | **e** lefante | at **e** nto    | elefant **e** |
| o      | `o`   | **o** caso    | enc **o** ntrar | ocasenc **o** |
| u      | `u`   | **u** sted    | p **u** nta     | Juanl **u**   |

### <a name="spanish-consonants"></a>스페인어 자음

| `sapi` | `ipa`      | 예제 1  | 예 2      | 예제 3      |
|--------|------------|------------|----------------|----------------|
| b      | `b`        | **b** aobab |                | am **b**        |
|        | `β`        |            | bao **b** ab     | baoba **b**     |
| ch     | `tʃ`       | **ch** eque | co **ch** e      | Marraque **ch** |
| 일      | `d`        | **d** edo   |                | portlan **d**   |
|        | `ð`        |            | de **d** o       | verda **d**     |
| f      | `f`        | **f** ácil  | ele **f** ante   | pu **f**        |
| g      | `g`        | **g** anga  |                | dópin **g**     |
|        | `ɣ`        |            | a **g** ua       | tuare **g**     |
| j      | `j`        | **i** odo   | cal **i** ente   | re **y**        |
| jj     | `j.j` `jj` |            | vi **ll** a      |                |
| k      | `k`        | **c** oche  | bo **c** a       | titáni **c**    |
| l      | `l`        | **l** ápiz  | a **l** a        | corde **l**     |
| ll     | `ʎ`        | **ll** ave  | desarro **ll** o |                |
| 분      | `m`        | **m** order | a **m** ar       | álbu **m**      |
| n      | `n`        | **n** ada   | ce **n** a       | rató **n**      |
| nj     | `ɲ`        | **ñ** aña   | ara **ñ** azo    |                |
| p      | `p`        | **p** oca   | to **p** o       | sto **p**       |
| r      | `ɾ`        |            | ca **r** a       | abri **r**      |
| rr     | `r`        | **r** adio  | co **rr** e      | pu **rr**       |
| 초      | `s`        | **s** aco   | va **s** o       | pelo **s**      |
| t      | `t`        | **t** oldo  | a **t** ar       | disque **t**    |
| th     | `θ`        | **z** ebra  | a **z** ul       | lápi **z**      |
| w      | `w`        | h **u** eso  | ag **u** a       | gua **u**       |
| x      | `x`        | **j** ota   | a **j** o        | relo **j**      |

> [!TIP]
> `es-ES` Speech Service 음성 기호는 다음 스페인어 IPA, `β`, `ð` 및 `ɣ`를 지원하지 않습니다. 필요한 경우 IPA를 직접 사용하는 것을 고려해야 합니다.

# <a name="zh-cn"></a>[zh-CN](#tab/zh-CN)

`zh-CN`에 대한 Speech Service 음성 기호는 네이티브 음성 <a href="https://en.wikipedia.org/wiki/Pinyin" target="_blank">Pinyin </a> 기호를 기반으로 합니다.

### <a name="tone"></a>발신음

| 병음 톤 | `sapi` | 문자 예제 |
|-------------|--------|-------------------|
| mā          | ma  1  | 妈                 |
| má          | ma  2  | 麻                 |
| mǎ          | ma  3  | 马                 |
| mà          | ma  4  | 骂                 |
| ma          | ma  5  | 嘛                 |

#### <a name="example"></a>예제

| 문자 | Speech Service                |
|-----------|-------------------------------|
| 组织关系      | zu  3 - zhi 1 - guan 1 - xi 5 |
| 累进        | lei  3 -jin 4                 |
| 西宅巷       | xi  1 - zhai 2 - xiang 4      |

# <a name="zh-tw"></a>[zh-TW](#tab/zh-TW)

`zh-TW`에 대한 Speech Service 음성 기호는 네이티브 음성 <a href="https://en.wikipedia.org/wiki/Bopomofo" target="_blank">Bopomofo </a> 기호를 기반으로 합니다.

### <a name="tone"></a>발신음

| Speech Service 톤 | Bopomofo 톤 | 예제(단어) | Speech Service 음성 | Bopomofo | 병음（拼音） |
|---------------------|---------------|----------------|-----------------------|----------|-------------|
| ˉ                   | 비어 있음         | 偵              | ㄓㄣˉ                   | ㄓㄣ       | zhēn        |
| ˊ                   | ˊ             | 察              | ㄔㄚˊ                   | ㄔㄚˊ      | chá         |
| ˇ                   | ˇ             | 打              | ㄉㄚˇ                   | ㄉㄚˇ      | dǎ          |
| ˋ                   | ˋ             | 望              | ㄨㄤˋ                   | ㄨㄤˋ      | wàng        |
| ˙                   | ˙             | 影子             | 一ㄥˇ  ㄗ˙               | 一ㄥˇ  ㄗ˙  | yǐng  zi    |

#### <a name="example"></a>예제

| 문자 | `sapi`   |
|-----------|----------|
| 狗         | ㄍㄡˇ      |
| 然后        | ㄖㄢˊㄏㄡˋ   |
| 剪掉        | ㄐㄧㄢˇㄉㄧㄠˋ |

# <a name="ja-jp"></a>[ja-JP](#tab/ja-JP)

`ja-JP`에 대한 Speech Service 음성 기호는 네이티브 음성 <a href="https://en.wikipedia.org/wiki/Kana" target="_blank">Kana </a> 기호를 기반으로 합니다.

### <a name="stress"></a>강세

| `sapi` | `ipa`          |
|--------|----------------|
| `ˈ`    | `ˈ` 주 강세 |
| `+`    | `ˌ` 하위 강세  |

#### <a name="example"></a>예제

| 문자 | `sapi`  | `ipa`       |
|-----------|---------|-------------|
| 合成        | ゴ'ウセ    | goˈwɯseji   |
| 所有者       | ショュ'ウ?ャ | ɕjojɯˈwɯɕja |
| 最適化       | サィテキカ+  | sajitecikaˌ |

***
