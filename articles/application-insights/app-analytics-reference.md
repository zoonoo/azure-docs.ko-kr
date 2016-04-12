<properties 
	pageTitle="Application Insights의 분석에 대한 참조 자료" 
	description="Application Insights의 강력한 검색 도구인 분석의 정규식" 
	services="application-insights" 
    documentationCenter=""
	authors="alancameronwills" 
	manager="douge"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/21/2016" 
	ms.author="awills"/>

# Application Insights: 분석 참고 자료

[분석](app-analytics.md)을 사용하면 [Application Insights](app-insights-overview.md)에서 수집된 앱의 원격 분석을 통해 유용한 쿼리를 실행할 수 있습니다. 다음 페이지에서는 해당 쿼리 언어를 설명합니다.


[AZURE.INCLUDE [app-analytics-top-index](../../includes/app-analytics-top-index.md)]

## 정규식


[> 정규식에 대한 일반적인 설명](https://github.com/google/re2/wiki/Syntax).

이 페이지는 RE2에서 허용하는 정규식 구문을 나열합니다. 또한 PCRE, PERL 및 VIM에서 허용하는 구문을 나열합니다.

|
|---|---
|단일 문자: | 
|. |모든 문자, 줄 바꿈 문자를 포함할 수 있음(s=true) 
|[xyz] |문자 클래스 
|[^xyz] |부정 문자 클래스 
|\\d |Perl 문자 클래스 
|\\D |부정 Perl 문자 클래스 
|[[:alpha:]] |ASCII 문자 클래스 
|[[:^alpha:]] |부정 ASCII 문자 클래스 
|\\pN |유니코드 문자 클래스(한 글자 이름) 
|\\p{Greek} |유니코드 문자 클래스 
|\\PN |부정 유니코드 문자 클래스(한 글자 이름) 
|\\P{Greek} |부정 유니코드 문자 클래스 
|합성: | 
|xy |x, y 순서로 구성 
|x|y |x 또는 y(x 선호) 
|
|반복: |
| |0개 이상의 x, 많을수록 더 좋음 
|x+ |한 개 이상의 x, 많을수록 더 좋음 
|x? |0개 또는 한 개의 x, 한 개 선호 
|x{n,m} |n개 또는 n+1개 또는 ... 또는 m개의 x, 많을수록 더 좋음 
|x{n,} |n개 이상의 x, 많을수록 더 좋음
|x{n} |정확히 n개의 x 
|x*? |0개 이상의 x, 적을수록 더 좋음 
|x+? |한 개 이상의 x, 적을수록 더 좋음 
|x?? |0개 또는 한 개의 x, 0개 선호 
|x{n,m}? |n개 또는 n+1개 또는 ... 또는 m개의 x, 적을수록 더 좋음 
|x{n,}? |n개 이상의 x, 적을수록 더 좋음 
|x{n}? |정확히 n개의 x 
|x{} |(== x*) (지원되지 않음) VIM 
|x{-} |(== x*?) (지원되지 않음) VIM 
|x{-n} |(== x{n}?) (지원되지 않음) VIM 
|x= |(== x?) (지원되지 않음) VIM 
|구현 제한 : 계산 양식 x{n,m}, x{n,} 및 x{n} | 
|1000보다 큰 최소 또는 최대 반복 횟수를 만드는 양식을 거부한다. | 
|무제한 반복 횟수에는 이 제한이 적용되지 않는다. | 
|소유 반복: | 
|x*+ |0개 이상의 x, 소유(지원되지 않음) 
|x++ |한 개 이상의 x, 소유(지원되지 않음) 
|x?+ |0개 이상의 x, 소유(지원되지 않음) 
|x{n,m}+ |n개 또는 ... 또는 m개의 x, 소유(지원되지 않음) 
|x{n,}+ |n개 이상의 x, 소유(지원되지 않음) 
|x{n}+ |정확히 n개의 x, 소유(지원되지 않음)
|그룹화: |
|(re) |번호가 매겨진 캡처 그룹(submatch) 
|(?P<name>re) |명명된 및 번호가 매겨진 캡처 그룹(submatch) 
|(?<name>re) |명명된 및 번호가 매겨진 캡처 그룹(submatch) (지원되지 않음) 
|(?'name're) |명명된 및 번호가 매겨진 캡처 그룹(submatch) (지원되지 않음) 
|(?:re) |비캡처 그룹 
|(?flags) |현재 그룹 내에 플래그 설정; 비캡처 
|(?flags:re) |re 중에 플래그 설정; 비캡처 
|(?#text) |주석(지원되지 않음) 
|(?&#124;x&#124;y&#124;z) |분기 번호 매기기 재설정(지원되지 않음) 
|(?>re) |re의 소유 일치(지원되지 않음) 
|re@> |re의 소유 일치(지원되지 않음) VIM 
|%(re) |비캡처 그룹(지원되지 않음) VIM 
|Flags: | 
|i |대/소문자 구분 안 함(기본값 false) 
|m |다중 행 모드: ^ 및 $ 는 텍스트 시작/종료 외에 행 시작/종료 일치(기본값 false) 
|s |let . 일치 \\n(기본값 false) 
|U |ungreedy: x*와 x*?, x+와 x+? 등의 의미 스왑(기본값 false) 
|플래그 구문은 xyz (set) 또는 -xyz (clear) 또는 xy-z (set xy, clear z)입니다.  
|빈 문자열: | 
|^ |텍스트 또는 행의 시작 부분(m=true) 
|$ |텍스트(\\Z가 아닌 \\z 등) 또는 행(m=true)의 끝 
|\\A |텍스트 시작 부분 
|\\b |ASCII 워드 경계(한 쪽에 \\w 및 반대쪽에 \\W, \\A 또는 \\z) 
|\\B |ASCII 단어 경계가 아닌 부분 
|\\G |검색 중인 하위 텍스트의 시작 부분(지원되지 않음) PCRE 
|\\G |마지막 일치의 끝(지원되지 않음) PERL 
|\\Z |텍스트 끝 또는 텍스트 끝의 줄 바꿈 앞(지원되지 않음) 
|\\z |텍스트의 끝 
|(?=re) |re와 일치하는 텍스트 앞(지원되지 않음) 
|(?!re) |re와 일치하지 않는 텍스트 앞(지원되지 않음) 
|(?<=re) |re와 일치하는 텍스트 뒤(지원되지 않음) 
|(?<!re) |re와 일치하지 않는 텍스트 뒤(지원되지 않음) 
|re& |re와 일치하는 텍스트 앞(지원되지 않음) VIM 
|re@= |re와 일치하는 텍스트 앞(지원되지 않음) VIM 
|re@! |re와 일치하지 않는 텍스트 앞(지원되지 않음) VIM 
|re@<= |re와 일치하는 텍스트 뒤(지원되지 않음) VIM 
|re@<! |re와 일치하지 않는 텍스트 뒤(지원되지 않음) VIM 
|\\zs |일치의 시작 설정(= \\K) (지원되지 않음) VIM 
|\\ze |일치의 끝 설정(지원되지 않음) VIM
|\\%^ |파일 시작(지원되지 않음) VIM 
|\\%$ |파일 끝(지원되지 않음) VIM
|\\%V |화면상(지원되지 않음) VIM 
|\\%# |커서 위치(지원되지 않음) VIM 
|\\%'m |m 위치 표시(지원되지 않음) VIM
|\\%23l |행 23(지원되지 않음) VIM 
|\\%23c |열 23(지원되지 않음) VIM 
|\\%23v |가상 열 23(지원되지 않음) VIM 
|이스케이프 시퀀스: | 
|\\a |bell(== \\007) 
|\\f |폼 피드(== \\014) 
|\\t |가로 탭(== \\011) 
|\\n |줄 바꿈(== \\012) 
|\\r |캐리지 리턴(== \\015) 
|\\v |세로 탭 문자(== \\013) 
|* |리터럴 *, 문장 부호 문자의 경우 * 
|\\123 |8진 문자 코드(최대 3자리) 
|\\x7F |16진 문자 코드(정확히 두 자리) 
|\\x{10FFFF} |16진 문자 코드 
|\\C |UTF-8 모드에서도 단일 바이트 일치 
|\\Q...\\E |리터럴 텍스트 ... 문장 부호가 ... 있더라도
|\\1 |역 참조(지원되지 않음) 
|\\b |백스페이스(지원되지 않음) (\\010 사용) 
|\\cK |제어 문자 ^K(지원되지 않음) (\\001 등 사용)
|\\e |이스케이프(지원되지 않음) (\\033 사용) 
|\\g1 |역 참조(지원되지 않음) 
|\\g{1} |역 참조(지원되지 않음) 
|\\g{+1} |역 참조(지원되지 않음) 
|\\g{-1} |역 참조(지원되지 않음) 
|\\g{name} |명명된 역 참조(지원되지 않음) 
|\\g<name> |서부루틴 호출(지원되지 않음) 
|\\g'name' |서브루틴 호출(지원되지 않음) 
|\\k<name> |명명된 역 참조(지원되지 않음) 
|\\k'name' |명명된 역 참조(지원되지 않음) 
|\\lX |소문자 X(지원되지 않음) 
|\\ux |대문자 x(지원되지 않음) 
|\\L...\\E |소문자 텍스트 ... (지원되지 않음) 
|\\K |$0의 시작 재설정(지원되지 않음) 
|\\N{name} |명명된 유니코드 문자(지원되지 않음)
|\\R |줄 나누기(지원되지 않음) 
|\\U...\\E |대문자 텍스트 ... (지원되지 않음) 
|\\X |확장 유니코드 시퀀스(지원되지 않음) 
|\\%d123 |십진 문자 123(지원되지 않음) VIM 
|\\%xFF |16진 문자 FF(지원되지 않음) VIM 
|\\%o123 |8진 문자 123(지원되지 않음) VIM
|\\%u1234 |유니코드 문자 0x1234(지원되지 않음) VIM 
|\\%U12345678 |유니코드 문자 0x12345678(지원되지 않음) VIM 
|문자 클래스 요소: | 
|x |단일 문자 
|A-Z |문자 범위(포함) 
|\\d |Perl 문자 클래스 
|[:foo:] |ASCII 문자 클래스 foo 
|\\p{Foo} |유니코드 문자 클래스 Foo 
|\\pF |유니코드 문자 클래스 F(한 문자로 된 이름) 
|문자 클래스 요소로서의 명명된 문자 클래스: | 
|[\\d] |숫자(== \\d) 
|[^\\d] |숫자가 아님(== \\D) 
|[\\D] |숫자가 아님(== \\D) 
|[^\\D] |숫자가 아닌 것이 아님(== \\d) 
|[[:name:]] |문자 클래스 내의 명명된 ASCII 클래스(== [:name:]) 
|[^[:name:]] |부정 문자 클래스 내의 명명된 ASCII(== [:^name:]) 
|[\\p{Name}] |문자 클래스 내의 명명된 유니코드 속성(== \\p{Name}) 
|[^\\p{Name}] |부정 문자 클래스 내의 명명된 유니코드 속성(== \\P{Name}) 
|Perl 문자 클래스(모두 ASCII만): | 
|\\d |숫자(== [0-9]) 
|\\D |숫자가 아님(== [^0-9]) 
|\\s |공백(== [\\t\\n\\f\\r ]) 
|\\S |공백 아님(== [^\\t\\n\\f\\r ]) 
|\\w |워드 문자(== [0-9A-Za-z\_]) 
|\\W |워드 문자 아님(== [^0-9A-Za-z\_]) 
|\\h |가로 간격(지원되지 않음) 
|\\H |가로 간격 아님(지원되지 않음) 
|\\v |세로 간격(지원되지 않음)
|\\V |세로 간격 아님(지원되지 않음) 
|ASCII 문자 클래스: | 
|[[:alnum:]] |영숫자(== [0-9A-Za-z]) 
|[[:alpha:]] |알파벳(== [A-Za-z]) 
|[[:ascii:]] |ASCII (== [\\x00-\\x7F]) 
|[[:blank:]] |공백(== [\\t ]) 
|[[:cntrl:]] |제어(== [\\x00-\\x1F\\x7F]) 
|[[:digit:]] |숫자(== [0-9])
|[[:graph:]] |그래픽(== [!-~] == [A-Za-z0-9!"#$%&'()*+,-./:;<=>?@[\\]^\_`{&#124;}~]) 
|[[:lower:]] |lower case (== [a-z]) 
|[[:print:]] |printable (== [ -~] == [ [:graph:]]) 
|[[:punct:]] |punctuation (== [!-/:-@[-`{-~]) 
|[[:space:]] |공백(== [\\t\\n\\v\\f\\r ]) 
|[[:upper:]] |대문자(== [A-Z]) 
|[[:word:]] |워드 문자(== [0-9A-Za-z\_]) 
|[[:xdigit:]] |16진 숫자(== [0-9A-Fa-f]) 
|유니코드 문자 클래스 이름--일반 범주: | 
|C |other 
|Cc |control 
|Cf |형식 
|Cn |할당되지 않은 코드 지점(지원되지 않음) 
|Co |개인 사용 
|Cs |대리 
|L |문자 
|LC |대/소문자 구분 문자(지원되지 않음) 
|L& |대/소문자 구분 문자(지원되지 않음) 
|Ll |소문자 
|Lm |한정자 문자
|Lo |기타 문자 
|Lt |첫 자 
|Lu |대문자 
|M |표시 
|Mc |간격 표시 
|Me |묶는 표시 
|Mn |간격 표시 아님 
|N |숫자 
|Nd |십진 숫자 
|Nl |문자 번호 
|No |다른 번호 
|P |문장 부호 
|Pc |연결자 문장 부호 
|Pd |대시 문장 부호 
|Pe |닫기 문장 부호 
|Pf |최종 문장 부호 
|Pi |초기 문장 부호 
|Po |기타 문장 부호 
|Ps |열기 문장 부호 
|S |기호 
|Sc |통화 기호 
|Sk |한정자 기호 
|Sm |수학 기호 
|So |기타 기호 
|Z |구분 기호 
|Zl |행 구분 기호 
|Zp |단락 구분 기호 
|Zs |공간 분리 기호 
|유니코드 문자 클래스 이름--스크립트: | 
|Arabic |아랍어 
|Armenian |아르메니아어 
|Balinese |발리어 
|Bamum |바뭄어 
|Batak |바탁어
|Bengali |벵갈어 
|Bopomofo |주음부호 
|Brahmi |브라미어 
|Braille |브라유 점자
|Buginese |부기어 
|Buhid |부히드어 
|Canadian\_Aboriginal |캐나다 아보리진어 
|Carian |카리아어 
|Chakma |차크마어 
|Cham |참어 
|Cherokee |체로키어 
|Common |한 문자에 특정되지 않은 문자
|Coptic |콥트어 
|Cuneiform |설형문자 
|Cypriot |키프로스어
|Cyrillic |키릴 문자 
|Deseret |데저레트 
|Devanagari |데바나가리어
|Egyptian\_Hieroglyphs |이집트 상형 문자 
|Ethiopic |에티오피아어 
|Georgian |그루지야어 
|Glagolitic |글라골어 
|Gothic |고트어
|Greek |그리스어 
|Gujarati |구자라트어 
|Gurmukhi |구르무키(펀잡어) 
|Han |한어 
|Hangul |한글 
|Hanunoo |하누누어 
|Hebrew |히브리어 
|Hiragana |히라가나 
|Imperial\_Aramaic |제국 아람어 
|Inherited |이전 문자에서 상속한 언어 
|Inscriptional\_Pahlavi |비문 팔레비어 
|Inscriptional\_Parthian |비문 파르티아어
|Javanese |자바어 
|Kaithi |카이티 문자 
|Kannada |칸나다어 
|Katakana |카타카나 
|Kayah\_Li |카야 리 문자 
|Kharoshthi |카로슈티 문자 
|Khmer |크메르어 
|Lao |라오어 
|Latin |라틴어 
|Lepcha |렙차어 
|Limbu |림부어 
|Linear\_B |선형 문자
|Lycian |리키아어 
|Lydian |리디아어 
|Malayalam |말라얄람어
|Mandaic |만다야어 
|Meetei\_Mayek |메이테이 문자 
|Meroitic\_Cursive |메로에 필기체 
|Meroitic\_Hieroglyphs |메로에 상형문자 
|Miao |먀오어 
|Mongolian |몽고어 
|Myanmar |미얀마어 
|New\_Tai\_Lue |신 타이 루에 문자(일명 간체 따이루어) 
|Nko |Nko 
|Ogham |오검 문자 
|Ol\_Chiki |올치키 문자 
|Old\_Italic |고대 이탈리아 문자 
|Old\_Persian |고대 페르시아어 
|Old\_South\_Arabian |고대 남부 아라비아어
|Old\_Turkic |고대 튀르크어
|Oriya |오리야어 
|Osmanya |오스만어 
|Phags\_Pa |'팍파 문자 
|Phoenician |페키니아어 
|Rejang |레장어 
|Runic |룬 문자 
|Saurashtra |사우라쉬트라어 
|Sharada |샤라다 문자 
|Shavian |샤비안어 
|Sinhala |싱할라어 
|Sora\_Sompeng |소라 솜펭 문자 
|Sundanese |순다어 
|Syloti\_Nagri |실헤티 나가리 문자 
|Syriac |고대 시리아어 
|Tagalog |카갈로그어 
|Tagbanwa |타그반와 문자 
|Tai\_Le |따이레이 
|Tai\_Tham |타이 땀 
|Tai\_Viet |타이 비에트 
|Takri |타크리어 
|Tamil |타밀어 
|Telugu |텔루구어 
|Thaana |타나어 
|Thai |태국어 
|Tibetan |티베트어 
|Tifinagh |티피나그어 
|Ugaritic |우가리트어 
|Vai |바이어 
|Yi |Yi 
|Vim 문자 클래스: | 
|\\i |식별자 문자(지원되지 않음) VIM 
|\\I |\\i 숫자 제외(지원되지 않음) VIM 
|\\k |키워드 문자(지원되지 않음) VIM 
|\\K |\\k 숫자 제외(지원되지 않음) VIM 
|\\f |파일 이름 문자(지원되지 않음) VIM 
|\\F |\\f 문자 제외(지원되지 않음) VIM 
|\\p |인쇄 가능 문자(지원되지 않음) VIM 
|\\P |\\p 숫자 제외(지원되지 않음) VIM 
|\\s |공백 문자(== [ \\t]) (지원되지 않음) VIM 
|\\S |비공백 문자(== [^ \\t]) (지원되지 않음) VIM 
|\\d |숫자(== [0-9]) VIM 
|\\D |\\d VIM 아님 
|\\x |16진 숫자(== [0-9A-Fa-f]) (지원되지 않음) VIM 
|\\X |\\x 아님(지원되지 않음) VIM 
|\\o |8진 숫자(== [0-7]) (지원되지 않음) VIM 
|\\O |\\o 아님(지원되지 않음) VIM 
|\\w |워드 문자 VIM 
|\\W |\\w VIM 아님 
|\\h |워드 문자의 머리(지원되지 않음) VIM 
|\\H |\\h 아님(지원되지 않음) VIM 
|\\a |알파벳(지원되지 않음) VIM 
|\\A |\\a 아님(지원되지 않음) VIM 
|\\l |소문자(지원되지 않음) VIM 
|\\L |소문자 아님(지원되지 않음) VIM 
|\\u |대문자(지원되지 않음) VIM 
|\\U |대문자 아님(지원되지 않음) VIM 
|\_x |\\x 및 줄 바꿈, 임의의 x에 대해(지원되지 않음) VIM 
|Vim 플래그: | 
|\\c |대/소문자 무시(지원되지 않음) VIM 
|\\C |대/소문자 일치(지원되지 않음) VIM 
|\\m |매직(지원되지 않음) VIM 
|\\M |매직 아님(지원되지 않음) VIM 
|\\v |매우 매직(지원되지 않음) VIM 
|\\V |매우 매직 아님(지원되지 않음) VIM 
|\\Z |문자를 조합하는 유니코드의 차이점 무시(지원되지 않음) VIM 
|Magic: | 
|(?{code}) |임의의 Perl 코드(지원되지 않음) PERL 
|(??{code}) |연기된 임의의 Perl 코드(지원되지 않음) PERL 
|(?n) |그룹 n을 캡처하는 정규식에 대한 재귀 호출(지원되지 않음) 
|(?+n) |상대 그룹 +n에 대한 재귀 호출(지원되지 않음)
|(?-n) |상대 그룹 -n에 대한 재귀 호출(지원되지 않음) 
|(?C) |PCRE 설명선(지원되지 않음) PCRE
|(?R) |전체 정규식에 대한 재귀 호출(== (?0)) (지원되지 않음) 
|(?&name) |명명된 그룹에 대한 재귀 호출(지원되지 않음) 
|(?P=name) |명명된 역 참조(지원되지 않음)
|(?P>name) |명명된 그룹에 대한 재귀 호출(지원되지 않음)
|(?(cond)true&#124;false) |조건 분기(지원되지 않음)
|(?(cond)true) |조건 분기(지원되지 않음) 
|(*ACCEPT) |정규식을 더 프롤로그처럼 만들기(지원되지 않음) 
|(*COMMIT) |(지원되지 않음) 
|(*F) |(지원되지 않음) 
|(*FAIL) |(지원되지 않음) 
|(*MARK) |(지원되지 않음) 
|(*PRUNE) |(지원되지 않음) 
|(*SKIP) |(지원되지 않음) 
|(*THEN) |(지원되지 않음) 
|(*ANY) |줄 바꿈 규칙 설정(지원되지 않음)
|(*ANYCRLF) |(지원되지 않음)
|(*CR) |(지원되지 않음)
|(*CRLF) |(지원되지 않음) 
|(*LF) |(지원되지 않음) 
|(*BSR\_ANYCRLF) |\\R 규칙 설정(지원되지 않음) PCRE
|(*BSR\_UNICODE) |(지원되지 않음) PCRE




[AZURE.INCLUDE [app-analytics-footer](../../includes/app-analytics-footer.md)]

<!---HONumber=AcomDC_0330_2016-->