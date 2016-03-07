<properties
	pageTitle="사용자 지정 분석기(Azure 검색 REST API 버전 2015-02-28-Preview) | Microsoft Azure"
	description="사용자 지정 분석기(Azure 검색 REST API 버전 2015-02-28-Preview)"
	services="search"
	documentationCenter=""
	authors="Yahnoosh"
	manager="pablocas"
	editor=""/>

<tags
	ms.service="search"
	ms.devlang="rest-api"
	ms.workload="search"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.author="jlembicz"
	ms.date="02/18/2016" />

# 분석기(Azure 검색 REST API 버전 2015-02-28-Preview)

> [AZURE.NOTE] 사용자 지정 분석기에 대한 지원은 현재 미리 보기 상태입니다. 이 기능을 활용하려면 Azure 검색 서비스 REST API의 2015-02-28-미리 보기 버전을 사용해야 합니다. 미리 보기 기능이 현재 .NET SDK에 추가되지 않았으므로 이번에 유일한 프로그래밍 옵션은 미리 보기 REST API를 사용하는 것입니다.

## 개요

간단히 말해서 전체 텍스트 검색 엔진의 역할은 효율적으로 쿼리하고 검색할 수 있는 방식으로 문서를 처리하고 저장하는 것입니다. 높은 수준에서 결국 문서에서 중요한 단어를 추출하고 인덱스에 넣은 다음 해당 인덱스를 사용하여 지정된 쿼리의 단어와 일치하는 문서를 찾을 수 있습니다. 문서와 검색 쿼리에서 단어를 추출하는 프로세스는 어휘 분석이라고 합니다. 어휘 분석을 수행하는 구성 요소는 분석기라고 합니다. Azure 검색에서는 [미리 정의된 언어 중립적 분석기](#Analyzers) 및 [언어 특정 분석기](https://msdn.microsoft.com/library/azure/dn879793.aspx) 집합 중에서 선택할 수 있습니다. 또한 고유한 사용자 지정 분석기를 정의하는 옵션이 있습니다. 사용자 지정 분석기를 사용하면 텍스트를 인덱싱 가능/검색 가능 토큰으로 변환하는 프로세스를 제어할 수 있습니다 미리 정의된 단일 토크나이저 및 하나 이상의 토큰 필터로 구성된 사용자 정의된 구성입니다. 토크나이저는 토큰에 텍스트를 분리하는 일을 담당하고 토큰 필터는 토크나이저에서 내보낸 토큰을 수정하는 일을 담당합니다.

사용자 지정 분석기에서 사용된 인기 있는 시나리오는 다음과 같습니다.

- 음성 검색 - 음성 필터를 추가하여 단어의 철자가 아닌 소리를 기반으로 검색을 사용합니다.
- 어휘 분석 사용 안 함 - 키워드 분석기를 사용하여 분석되지 않은 검색 가능한 필드를 만듭니다
- 빠른 접두사/접미사 검색 - 단어의 인덱스 접두사에 N-gram 토큰 필터를 추가하여 빠른 접두사 일치를 사용합니다. 역방향 토큰 필터와 결합하여 접미사 일치를 수행합니다.
- 사용자 지정 토큰화 - 예를 들어 공백을 구분 기호로 사용하여 토큰에 문장을 분리하도록 공백 토크나이저를 사용합니다.
- ASCII 접기 - ASCII 접기 필터를 추가하여 검색 조건에 ö 또는 ê 등 분음 부호를 정규화합니다.

필터의 조합을 다양하게 만드는 여러 사용자 지정 분석기를 정의할 수 있지만 각 필드는 분석을 인덱싱하는 분석기 하나와 검색 분석에 대한 분석기 하나만을 사용할 수 있습니다.
 
이 페이지는 지원되는 분석기, 토크나이저 및 토큰 필터의 목록을 제공합니다. 사용 예제를 사용하여 인덱스 정의에 대한 변경을 설명합니다. 시나리오의 소개 및 탐색은 [Azure 검색의 사용자 지정 분석기](link)를 참조하세요. Azure 검색 구현에 활용되는 기본 기술에 대한 자세한 배경 정보는 [분석 패키지 요약(Lucene)](http://lucene.apache.org/core/4_10_3/core/org/apache/lucene/analysis/package-summary.html)을 참조하세요.


## 기본 분석기

기본 분석기는 [Apache Lucene 표준](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/standard/StandardAnalyzer.html) 분석기입니다.

## 유효성 검사 규칙

사용자 지정 분석기, 토크나이저 및 토큰 필터의 이름은 고유해야 하며 미리 정의된 분석기, 토크나이저 또는 토큰 필터와 같을 수 없습니다.
 

## 사용자 지정 분석기로 인덱스 정의 

인덱스를 만들 때 사용자 지정 분석기를 정의합니다. 인덱스 정의에서 사용자 지정 분석 구성 요소의 구문은 다음과 같습니다. 전체 예제는 [여기](#Example)에서 제공됩니다.

	{
	  	. . .
	 	(standard create index request body)
	 	. . .  
		"analyzers": (optional) [ 
		    {  
		       "name": "name of analyzer",  
		       "@odata.type": "#Microsoft.Azure.Search.CustomAnalyzer", 
		       "tokenizer": "tokenizer_name", (tokenizer_name is the name of a tokenizer from the [Tokenizers](#Tokenizers) table)
		       "tokenFilters": [
				  "token_filter_name", (token_filter_name is the name of a token filter from the [Token Filters](#TokenFilters) table)
				  "token_filter_name"
				] (token filters will be applied from left to right)
		    },
			{  
		       "name": "name of analyzer",  
		       "@odata.type": "#analyzer_type", 
		       "option1": value1,
		       "option2": value2,
		       ...  
		    }
		],  
		"tokenizers": (optional) [ 
		    {  
		       "name": "tokenizer_name",  
		       "@odata.type": "#tokenizer_type", 
		       "option1": value1,  
		       "option2": value2, 
		       ...  
		    }
		],
		"tokenFilters": (optional) [ 
		    {  
		       "name": "token_filter_name",  
		       "@odata.type": "#token_filter_type", 
		       "option1": value1,  
		       "option2": value2, 
		       ...  
		    }
		]	
	}

> [AZURE.NOTE] 만든 사용자 지정 분석기는 Azure 포털에서 노출되지 않습니다. 사용자 지정 분석기를 추가하는 유일한 방법은 인덱스를 정의할 때 REST API를 호출하는 코드를 통해서입니다.

##사용자 지정 분석기, 토크나이저 및 토큰 필터에 대한 인덱스 특성 

이 섹션에서는 인덱스 정의의 분석기, 토크나이저 및 토큰 필터 섹션에 대한 구성 속성을 지정합니다.

###분석기

<table>
  <tbody>
    <tr>
      <td>
        <b>Name</b>
      </td>
      <td>
		문자, 숫자, 공백, 대시 또는 밑줄만을 포함할 수 있고 영숫자로만 시작하고 끝날 수 있으며 128자로 제한됩니다.
      </td>
    </tr>
    <tr>
      <td>
        <b>형식</b>
      </td>
      <td>
		유효한 값은 "#Microsoft.Azure.Search.CustomAnalyzer" 또는 지원되는 분석기 목록의 분석기 이름입니다. 아래 [Analyzers](#Analyzers) 테이블의 <b>analyzer_type</b> 열을 참조하세요.
      </td>
	</tr>
    <tr>
      <td>
        <b>토크나이저</b>(#Microsoft.Azure.Search.CustomAnalyzer에만 유효)
      </td>
      <td>
		필수입니다. 아래 [토크나이저](#Tokenizers) 테이블에 나열된 미리 정의된 토크나이저 또는 인덱스 정의에 정의된 사용자 지정 토크나이저 중 하나임이 분명합니다.
      </td>
	</tr>
    <tr>
      <td>
        <b>TokenFilters</b>(#Microsoft.Azure.Search.CustomAnalyzer에만 유효)
      </td>
      <td>
		토큰 필터는 모두 [토큰 필터](#TokenFilters) 테이블에 나열된 미리 정의된 토큰 필터 중 하나이거나 인덱스 정의에 정의된 사용자 지정 토큰 필터 중 하나입니다.
      </td>
	</tr>
    <tr>
      <td>
        <b>옵션</b>
      </td>
      <td>
		미리 정의된(비사용자 지정) 분석기의 [유효한 옵션](#Analyzers)이어야 합니다.
      </td>
	</tr>
  </tbody>
</table>

###토크나이저

문장을 단어로 분리하는 것처럼 토크나이저는 연속된 텍스트를 토큰의 시퀀스로 분리합니다. 사용자 지정 분석기당 하나의 토크나이저를 지정할 수 있습니다. 둘 이상의 토크나이저가 필요한 경우 여러 사용자 지정 분석기를 만들고 인덱스 스키마에서 필드 별로 할당할 수 있습니다. 사용자 지정 분석기는 기본 또는 사용자 지정된 옵션 중 하나로 미리 정의된 토크나이저를 사용할 수 있습니다.

<table>
  <tbody>
    <tr>
      <td>
        <b>Name</b>
      </td>
      <td>
		문자, 숫자, 공백, 대시 또는 밑줄만을 포함할 수 있고 영숫자로만 시작하고 끝날 수 있으며 128자로 제한됩니다.
      </td>
    </tr>
    <tr>
      <td>
        <b>형식</b>
      </td>
      <td>
		지원되는 토크나이저의 목록에서 토크나이저 이름입니다. 아래 [토크나이저](#Tokenizers) 테이블에서 <b>tokenizer_type</b> 열을 참조하세요.
      </td>
	</tr>
     <tr>
      <td>
        <b>옵션</b>
      </td>
      <td>
		지정된 토크나이저 형식의 [유효한 옵션](#Tokenizers)이어야 합니다.
      </td>
	</tr>
  </tbody>
</table>

###토큰 필터

토큰 필터는 토크나이저에서 생성된 토큰을 필터링하거나 수정하는 데 사용됩니다. 예를 들어, 모든 문자를 소문자로 변환하는 소문자 필터를 지정할 수 있습니다. 사용자 지정 분석기에서 여러 토큰 필터를 사용할 수 있습니다. 토큰 필터는 나열된 순서로 실행합니다.

<table>
  <tbody>
    <tr>
      <td>
        <b>Name</b>
      </td>
      <td>
		문자, 숫자, 공백, 대시 또는 밑줄만을 포함할 수 있고 영숫자로만 시작하고 끝날 수 있으며 128자로 제한됩니다.
      </td>
    </tr>
    <tr>
      <td>
        <b>형식</b>
      </td>
      <td>
		지원되는 토큰 필터의 목록에서 토큰 필터 이름입니다. 아래 [토큰 필터](#TokenFilters) 테이블에서 <b>token_filter_type</b> 열을 참조하세요.
      </td>
	</tr>
     <tr>
      <td>
        <b>옵션</b>
      </td>
      <td>
		지정된 토큰 필터 형식의 [유효한 옵션](#TokenFilters)이어야 합니다.
      </td>
	</tr>
  </tbody>
</table>

<a name="Example"></a>
##인덱스 정의 예제

이 인덱스 정의 예제는 사용자 지정된 표준 토크나이저인 "my\_standard\_tokenizer"와 소문자 및 사용자 지정된 asciifolding 필터 "my\_asciifolding" 등 두 개의 토큰 필터를 사용하는 사용자 지정 분석기 "my\_analyzer"를 사용하는 하나의 필드를 포함합니다.

	{
	   "name":"myindex",
	   "fields":[
	      {
	         "name":"id",
	         "type":"Edm.String",
	         "key":true,
	         "searchable":false
	      },
	      {
	         "name":"text",
	         "type":"Edm.String",
	         "searchable":true,
	         "analyzer":"my_analyzer"
	      }
	   ],
	   "analyzers":[
	      {
	         "name":"my_analyzer",
	         "@odata.type":"#Microsoft.Azure.Search.CustomAnalyzer",
	         "tokenizer":"my_standard_tokenizer",
	         "tokenFilters":[
	            "my_asciifolding",
	            "lowercase"
	         ]
	      }
	   ],
	   "tokenizers":[
	      {
	         "name":"my_standard_tokenizer",
	         "@odata.type":"#Microsoft.Azure.Search.StandardTokenizer",
	         "maxTokenLength":20
	      }
	   ],
	   "tokenFilters":[
	      {
	         "name":"my_asciifolding",
	         "@odata.type":"#Microsoft.Azure.Search.AsciiFoldingTokenFilter",
	         "preserveOriginal":true
	      }
	   ]
	}

<a name="Analyzers"></a>
##분석기

<table>
  <tbody>
	<thead>
	<tr>
		<td>analyzer_name</td>
		<td>analyzer_type</td>
		<td>설명</td>
		<td>옵션</td>
	</tr>
	</thead>
    <tr>
      <td>[키워드] (http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/core/KeywordAnalyzer.html)</td>
      <td>#Microsoft.Azure.Search.KeywordAnalyzer</td>
	  <td>필드의 전체 콘텐츠를 단일 토큰으로 처리합니다. 우편 번호, ID 및 일부 제품 이름과 같은 데이터에 유용합니다.</td>
	  <td></td>
    </tr>
    <tr>
      <td>[패턴] (http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/miscellaneous/PatternAnalyzer.html)</td>
      <td>#Microsoft.Azure.Search.PatternAnalyzer</td>
	  <td>정규식 패턴을 통해 조건에 텍스트를 유연하게 구분합니다.</td>
	  <td>
		- 소문자 - 형식: 부울 - 소문자인 용어여야 함, true로 기본값 - [패턴](http://docs.oracle.com/javase/7/docs/api/java/util/regex/Pattern.html?is-external=true) - 형식: 문자열 - 토큰 분리기와 일치하는 정규식 패턴, 기본값: \w+ - [플래그](http://docs.oracle.com/javase/6/docs/api/java/util/regex/Pattern.html#field_summary) - 형식: 문자열 - 정규식 플래그, 기본값: 빈 문자열 - 중지 단어 - 형식: 문자열 배열 - 중지 단어의 목록, 기본값: 빈 목록
	  </td>
    </tr>
    <tr>
      <td>[단순] (http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/core/SimpleAnalyzer.html)</td>
      <td>#Microsoft.Azure.Search.SimpleAnalyzer</td>	 
	  <td>텍스트를 문자가 아닌 글자로 나누고 소문자로 변환합니다</td>
	  <td></td>
    </tr>
    <tr>
      <td>[스노우볼] (http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/snowball/SnowballAnalyzer.html)</td>
      <td>#Microsoft.Azure.Search.SnowballAnalyzer</td>
	  <td>[스노우볼 형태소 분석 필터]가 있는 표준 분석기 (http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/snowball/SnowballFilter.html)</td>
	  <td>
		- 언어 - 형식: 문자열 - 허용되는 값: 덴마크어, 네덜란드어, 영어, 핀란드어, 프랑스어, 독일어, 헝가리어, 이탈리아어, 노르웨이어, 포르투갈어, 러시아어, 스페인어, 스웨덴어 - 중지 단어 - 형식: 문자열 배열 - 중지 단어의 목록, 기본값: 빈 목록
	  </td>
    </tr>
    <tr>
      <td>[표준] (http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/standard/StandardAnalyzer.html)</td>
      <td>#Microsoft.Azure.Search.StandardAnalyzer</td>
	  <td>표준 Lucene 분석기 - 표준 토크 나이저, 소문자 필터 및 중지 필터로 구성됨</td>
	  <td>
		- maxTokenLength - 형식: int - 최대 토큰 길이, 기본값: 255. 최대 길이보다 긴 토큰은 분할됩니다. - 중지 단어 - 형식: 문자열 배열 - 중지 단어의 목록, 기본값: 빈 목록
	  </td>
    </tr>
    <tr>
      <td>standardasciifolding.lucene</td>
      <td>#Microsoft.Azure.Search.StandardAsciiFoldingAnalyzer</td>	  
	  <td>Ascii 접기 필터가 있는 표준 분석기</td>
	  <td></td>
    </tr>
    <tr>
      <td>[중지] (http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/core/StopAnalyzer.html)</td>
      <td>#Microsoft.Azure.Search.StopAnalyzer</td>
	  <td>텍스트를 문자가 아닌 글자로 나누고 소문자 및 중지 단어 토큰 필터를 적용합니다</td>
	  <td>
		- 중지 단어 - 형식: 문자열 배열 - 중지 단어의 목록, 기본값: 빈 목록
	  </td>
    </tr>
    <tr>
      <td>[공백] (http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/core/WhitespaceAnalyzer.html)</td>
      <td>#Microsoft.Azure.Search.WhitespaceAnalyzer</td>
	  <td>공백 토크나이저를 사용하는 분석기.</td>
	  <td></td>
    </tr>
  </tbody>
</table>

<a name="Tokenizers"></a>
##토크나이저

<table>
  <tbody>
	<thead>
	<tr>
		<td>tokenizer_name</td>
		<td>tokenizer_type</td>
		<td>설명</td>
		<td>옵션</td>
	</tr>
	</thead>
    <tr>
      <td>[클래식] (http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/standard/ClassicTokenizer.html)</td>
      <td>#Microsoft.Azure.Search.ClassicTokenizer</td>
	  <td>대부분의 유럽 언어 문서를 처리하는 데 적합한 문법 기반 토크나이저</td>
	  <td>
		- maxTokenLength - 형식: int - 최대 토큰 길이, 기본값: 255. 최대 길이보다 긴 토큰은 분할됩니다. 
	  </td>	  
    </tr>
    <tr>
      <td>[edgeNGram] (https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/ngram/EdgeNGramTokenizer.html)</td>
      <td>#Microsoft.Azure.Search.EdgeNGramTokenizer</td>
	  <td>에지에서 지정된 크기의 N-gram에 입력을 토큰화합니다.</td>
	  <td>
		- minGram - 형식: int - 기본값: 1 - maxGram - 형식: int - 기본값: 2 - tokenChars - 형식: 문자열 배열 - 토큰에 유지할 문자 클래스, 허용된 값: 문자, 숫자, 공백, 문장 부호, 기호
	  </td>	  
    </tr>
    <tr>
      <td>[키워드] (http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/core/KeywordTokenizer.html)</td>
      <td>#Microsoft.Azure.Search.KeywordTokenizer</td>
	  <td>전체 입력을 단일 토큰으로 내보냅니다.</td>
	  <td>
		- bufferSize - 형식: int - 읽기 버퍼 크기, 기본값: 256
	  </td>
    </tr>
    <tr>
      <td>[문자] (http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/core/LetterTokenizer.html)</td>
      <td>#Microsoft.Azure.Search.LetterTokenizer</td>
	  <td>텍스트를 문자가 아닌 글자로 나눕니다.</td>
	  <td></td>
    </tr>
    <tr>
      <td>[소문자] (http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/core/LowerCaseTokenizer.html)</td>
      <td>#Microsoft.Azure.Search.LowercaseTokenizer</td>
	  <td>텍스트를 문자가 아닌 글자로 나누고 소문자로 변환합니다</td>
	  <td></td>
    </tr>
    <tr>
      <td>[nGram] (http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/ngram/NGramTokenizer.html)</td>
      <td>#Microsoft.Azure.Search.NGramTokenizer</td>
	  <td>지정된 크기의 N-gram에 입력을 토큰화합니다.</td>
	  <td>
		- minGram - 형식: int - 기본값: 1 - maxGram - 형식: int - 기본값: 2 - tokenChars - 형식: 문자열 배열 - 토큰에 유지할 문자 클래스, 허용된 값: 문자, 숫자, 공백, 문장 부호, 기호
	  </td>
    </tr>
    <tr>
      <td>[path_hierarchy] (http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/path/PathHierarchyTokenizer.html)</td>
      <td>#Microsoft.Azure.Search.PathHierarchyTokenizer</td>
	  <td>경로와 유사한 계층 구조에 대한 토크나이저</td>
	  <td>
		- 구분 기호 - 형식: 문자열 - 기본값: '/' - 교체 - 형식: 문자열 - 설정되면, 구분 기호 문자 대체, 기본값: 구분 기호 - bufferSize - 형식: int - 기본값: 1024 - 역방향 - 형식: 부울 - true이면 반대 순서로 토큰 생성, 기본값: true - 건너뛰기 - 형식: 부울 - 건너뛸 초기 토큰, 기본값: 0
	  </td>
    </tr>
    <tr>
      <td>[패턴] (http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/pattern/PatternTokenizer.html)</td>
      <td>#Microsoft.Azure.Search.PatternTokenizer</td>
	  <td>토크나이저는 Regex 패턴을 사용하여 고유한 토큰을 생성합니다</td>
	  <td>
		 - 패턴 - 형식: 문자열 - 정규식 패턴, 기본값: \w+-[플래그] (http://docs.oracle.com/javase/6/docs/api/java/util/regex/Pattern.html#field_summary) - 형식: 문자열 - 정규식 플래그, 기본값: 빈 문자열 - 그룹 - 형식: int - 토큰으로 추출할 그룹, 기본값: -1(분할)
	  </td>
    </tr>
    <tr>
      <td>[표준] (http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/standard/StandardTokenizer.html)</td>
      <td>#Microsoft.Azure.Search.StandardTokenizer</td>
	  <td>[유니코드 텍스트 구분 규칙]을 따라 텍스트를 분리합니다(http://unicode.org/reports/tr29/).</td>
	  <td>
		 - maxTokenLength - 형식: int - 최대 토큰 길이, 기본값을 255로. 최대 길이보다 긴 토큰은 분할됩니다. 
	  </td>
    </tr>
    <tr>
      <td>[uax_url_email] (http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/standard/UAX29URLEmailTokenizer.html)</td>
      <td>#Microsoft.Azure.Search.UaxEmailUrlTokenizer</td>
	  <td>URL 및 전자 메일을 하나의 토큰으로 토큰화합니다.</td>
	  <td>
		- maxTokenLength - 형식: int - 최대 토큰 길이, 기본값을 255로. 최대 길이보다 긴 토큰은 분할됩니다.
	  </td>
    </tr>
    <tr>
      <td>[공백] (http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/core/WhitespaceTokenizer.html)</td>
      <td>#Microsoft.Azure.Search.WhitespaceTokenizer</td>
	  <td>텍스트를 공백으로 나눕니다.</td>
	  <td></td>
    </tr>
  </tbody>
</table>

<a name="TokenFilters"></a>
##토큰 필터

<table>
  <tbody>
	<thead>
	<tr>
		<td>token_filter_name</td>
		<td>token_filter_type</td>
		<td>설명</td>
		<td>옵션</td>
	</tr>
	</thead>
    <tr>
      <td>[arabic_normalization] (http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/ar/ArabicNormalizationFilter.html)</td>
      <td>#Microsoft.Azure.Search.ArabicNormalizationTokenFilter</td>
	  <td>아포스트로피와 그 뒤의 모든 문자를 제거합니다.</td>
    </tr>
    <tr>
      <td>[아포스트로피] (http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/tr/ApostropheFilter.html)</td>
      <td>#Microsoft.Azure.Search.ApostropheTokenFilter</td>
	  <td>아포스트로피 뒤의 모든 문자를 지웁니다.</td>
	  <td></td>	  
    </tr>
    <tr>
      <td>[asciifolding] (http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/miscellaneous/ASCIIFoldingFilter.html)</td>
      <td>#Microsoft.Azure.Search.AsciiFoldingTokenFilter</td>
	  <td>있는 경우, 처음 127개의 ASCII 문자("기본 라틴" 유니코드 블록)에 없는 영문자, 숫자 및 기호 유니코드 문자를 해당하는 ASCII 문자로 변환합니다</td>
	  <td>
		- preserveOriginal - 형식: 부울 - true이면, 원래 토큰 유지, 기본값: false
	  </td>	  
    </tr>
    <tr>
      <td>[cjk_bigram] (http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/cjk/CJKBigramFilter.html)</td>
      <td>#Microsoft.Azure.Search.CjkBigramTokenFilter</td>
	  <td>StandardTokenizer에서 생성되는 CJK 용어의 바이그램을 형성합니다</td>
	  <td>
		- ignoreScripts - 형식: 부울 - 무시할 스크립트, 허용된 값: 중국어, 히라가나, 가타카나, 한글. 기본값: 빈 목록 - outputUnigrams 형식: 부울 - 유니그램 및 바이그램을 항상 모두 출력하려면 true로 설정, 기본값: false
	  </td>	  
    </tr>
    <tr>
      <td>[cjk_width] (http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/cjk/CJKWidthFilter.html)</td>
      <td>#Microsoft.Azure.Search.CjkWidthTokenFilter</td>
	  <td>CJK 너비 차이를 정규화합니다. 전각 문자 ASCII 변형을 해당하는 기본 라틴 문자 및 반각 문자 가타카나 변형을 해당하는 일본어로 접습니다</td>
	  <td></td>	  
    </tr>
    <tr>
      <td>[클래식] (http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/standard/ClassicFilter.html)</td>
      <td>#Microsoft.Azure.Search.ClassicTokenFilter</td>
	  <td>영어 소유격을 제거하고 머리글자어에서 점을 찍습니다</td>
	  <td></td>	  
    </tr>
    <tr>
      <td>[common_grams] (http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/commongrams/CommonGramsFilter.html)</td>
      <td>#Microsoft.Azure.Search.CommonGramTokenFilter</td>
	  <td>인덱스 작업 중 자주 발생하는 용어에 대한 바이그램을 생성합니다. 단일 용어는 여전히 중첩된 바이로 인덱싱됩니다.</td>
	  <td>
		- commonWords - 형식: 문자열 배열 - 일반적인 단어의 집합, 기본값: 빈 목록 - ignoreCase - 형식: 부울 - true이면, 일반적인 단어 일치는 대소문자 구분, 기본값: false - queryMode - 형식: 부울 - 바이그램을 생성한 다음 일반적인 단어 및 일반적인 단어 뒤의 단일 용어 제거, 기본값: false
	  </td>	  
    </tr>
    <tr>
      <td>[delimited_payload_filter] (https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/payloads/DelimitedPayloadTokenFilter.html)</td>
      <td>#Microsoft.Azure.Search.DelimitedPayloadTokenFilter</td>
	  <td>구분 기호 앞에 있는 문자는 "토큰"이며 뒤에 있는 문자는 페이로드입니다. 예를 들어, 구분 기호가 '|'인 경우 문자열 "hello|world"에서 "hello"는 토큰이고 "world"는 페이로드입니다. 또한 인코더를 포함하여 페이로드를 적절한 방법으로 변환할 수 있습니다(문자부터 바이트까지).</td>
	  <td>
		- 구분 기호 - 형식: char - 기본값: '|' - 인코딩 - 형식: 문자열 - 허용된 값: int, float, ID. 기본값: float
	  </td>	  
    </tr>
    <tr>
      <td>[dictionary_decompounder] (http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/compound/DictionaryCompoundWordTokenFilter.html)</td>
      <td>#Microsoft.Azure.Search.DictionaryDecompounderTokenFilter</td>
	  <td>많은 독일 언어에서 찾은 복합 단어를 분해합니다.</td>
	  <td>
		- wordList - 형식: 문자열 배열 - 일치하는 단어의 목록, 기본값: 빈 목록 - minWordSize - 형식: int - 이보다 긴 단어만 처리됨, 기본값: 5 - minSubwordSize - 형식: int - 이보다 긴 하위단어만 출력됨, 기본값: 2 - maxSubwordSize - 형식: int - 이보다 짧은 하위단어만 출력됨, 기본값: 15 - onlyLongestMatch - 형식: 부울 - 출력할 가장 긴 일치 하위단어만 추가, 기본값: false 
	  </td>	  
    </tr>
    <tr>
      <td>[edgeNGram] (http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/ngram/EdgeNGramTokenFilter.html)</td>
      <td>#Microsoft.Azure.Search.EdgeNGramTokenFilter</td>
	  <td>입력된 토큰의 전면 또는 후면에서 처음부터 지정된 크기의 N-gram을 생성합니다</td>
	  <td>
		- minGram - 형식: int - 기본값: 1 - maxGram - 형식: int - 기본: 2 - 측면 - 형식: 문자열 - N-gram에서 생성해야 하는 입력의 측면 지정 허용된 값: 전면, 후면
	  </td>	  
    </tr>   
    <tr>
      <td>[elision] (http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/util/ElisionFilter.html)</td>
      <td>#Microsoft.Azure.Search.ElisionTokenFilter</td>
	  <td>발음 생략을 제거합니다. 예를 들어 "l'avion"(평면)은 "avion"(평면)으로 변환됩니다.</td>
	  <td>
		- 문서 - 형식: 문자열 배열 - 제거할 일련의 문서, 기본값: 빈 목록
	  </td>	  
    </tr>
    <tr>
      <td>[german_normalization] (http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/de/GermanNormalizationFilter.html)</td>
      <td>#Microsoft.Azure.Search.GermanNormalizationTokenFilter</td>
	  <td>[German2 스노우 볼 알고리즘](http://snowball.tartarus.org/algorithms/german2/stemmer.html)의 추론에 따라 독일어 문자를 정규화합니다. </td>
	  <td></td>	  
    </tr>
    <tr>
      <td>[hindi_normalization] (http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/hi/HindiNormalizationFilter.html)</td>
      <td>#Microsoft.Azure.Search.HindiNormalizationTokenFilter</td>
	  <td>힌디어에 텍스트를 정규화하여 철자 변형에 차이를 제거합니다.</td>
	  <td></td>	  
    </tr>
    <tr>
      <td>[indic_normalization] (http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/in/IndicNormalizationFilter.html)</td>
      <td>#Microsoft.Azure.Search.IndicNormalizationTokenFilter</td>
	  <td>인도어 텍스트의 유니코드 표현을 정규화합니다.</td>
	  <td></td>	  
    </tr>
    <tr>
      <td>[유지] (http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/miscellaneous/KeepWordFilter.html)</td>
      <td>#Microsoft.Azure.Search.KeepTokenFilter</td>
	  <td>지정된 단어 목록에 포함된 텍스트로 토큰을 유지하는 토큰 필터입니다</td>
	  <td>
		- keepWords - 형식: 문자열 배열 - 유지할 단어 목록, 기본값: 빈 목록 - keepWordsCase - 형식: 부울 - true이면, 모든 단어에서 소문자 우선, 기본값: false
	  </td>	  
    </tr>
    <tr>
      <td>[keep_types] (http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/core/TypeTokenFilter.html)</td>
      <td>#Microsoft.Azure.Search.KeepTypesTokenFilter</td>
	  <td>허용된 형식의 지정된 목록에서 형식이 표시되는 토큰을 유지합니다</td>
	  <td>
		- 형식 - 형식: 문자열 배열 - 유지할 형식 목록
	  </td>	  
    </tr>
    <tr>
      <td>[keyword_marker] (http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/miscellaneous/KeywordMarkerFilter.html)</td>
      <td>#Microsoft.Azure.Search.KeywordMarkerTokenFilter</td>
	  <td>키워드로 용어를 표시합니다</td>
	  <td>
		- 키워드 - 형식: 문자열 배열 - 키워드로 표시할 단어의 목록, 기본값: 빈 목록 - ignoreCase - 형식: 부울 - true이면, 모든 단어에서 소문자 우선, 기본값: false
	  </td>	  
    </tr>
    <tr>
      <td>[keyword_repeat] (http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/miscellaneous/KeywordRepeatFilter.html)</td>
      <td>#Microsoft.Azure.Search.KeywordRepeatTokenFilter</td>
	  <td>들어오는 각 토큰을 두 번, 한 번은 키워드로 다른 한 번은 키워드 아닌 것으로 내보냅니다.</td>
	  <td></td>	  
    </tr>
    <tr>
      <td>[kstem] (http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/en/KStemFilter.html)</td>
      <td>#Microsoft.Azure.Search.KStemTokenFilter</td>
	  <td>영어에 대한 고성능 kstem 필터입니다</td>
	  <td></td>	  
    </tr>
    <tr>
      <td>[길이] (http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/miscellaneous/LengthFilter.html)</td>
      <td>#Microsoft.Azure.Search.LengthTokenFilter</td>
	  <td>너무 길거나 너무 짧은 단어를 제거합니다.</td>
	  <td>
		- 분 - 형식: int - 최소수, 기본값: 0 - 최대 - 형식: int - 최대수, 기본값: 최대 정수 값
	  </td>	  
    </tr>
    <tr>
      <td>[제한] (http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/miscellaneous/LimitTokenCountFilter.html)</td>
      <td>#Microsoft.Azure.Search.LimitTokenFilter</td>
	  <td>인덱스 작업 중 토큰 수를 제한합니다</td>
	  <td>
		- maxTokenCount - 형식: int - 생성할 토큰의 최대수, 기본값: 1 - consumeAllTokens - 형식: 부울 - maxTokenCount가 도달하더라도 입력에서 모든 토큰을 사용해야 하는지 여부, 기본값: false
	  </td>	  
    </tr>
    <tr>
      <td>[소문자] (http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/core/LowerCaseFilter.html)</td>
      <td>#Microsoft.Azure.Search.LowercaseTokenFilter</td>
	  <td>토큰 텍스트를 소문자로 정규화합니다.</td>
	  <td></td>	  
    </tr>
    <tr>
      <td>[nGram] (http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/ngram/NGramTokenFilter.html)</td>
      <td>#Microsoft.Azure.Search.NGramTokenFilter</td>
	  <td>지정된 크기의 N-그램을 생성합니다</td>
	  <td>
		- minGram - 형식: int - 기본값: 1 - maxGram - 형식: int - 기본값: 2
	  </td>	  
    </tr>
    <tr>
      <td>[pattern_capture] (http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/pattern/PatternCaptureGroupTokenFilter.html)</td>
      <td>#Microsoft.Azure.Search.PatternCaptureTokenFilter</td>
	  <td>Java regex를 사용하여 하나 이상의 패턴에 있는 각 캡처 그룹에 하나씩 여러 토큰을 내보냅니다</td>
	  <td>
		- 패턴 - 형식: 문자열 배열 - 각 토큰에 일치하는 패턴의 목록 - preserveOriginal - 형식: 부울 - 패턴 중 하나와 일치하더라도 원래 토큰을 반환하려면 true로 설정
	  </td>	  
    </tr>
    <tr>
      <td>[persian_normalization] (http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/fa/PersianNormalizationFilter.html)</td>
      <td>#Microsoft.Azure.Search.PersianNormalizationTokenFilter</td>
	  <td>페르시아어에 정규화를 적용합니다.</td>
	  <td></td>	  
    </tr>
    <tr>
      <td>[음성] https://lucene.apache.org/core/4_10_3/analyzers-phonetic/org/apache/lucene/analysis/phonetic/package-tree.html)</td>
      <td>#Microsoft.Azure.Search.PhoneticTokenFilter</td>
	  <td>음성 일치에 대한 토큰을 만듭니다.</td>
	  <td>
		- 인코더 - 형식: 문자열 - 사용할 음성 인코더, 허용된 값: metaphone, doublemetaphone, soundex, refinedsoundex, caverphone1, caverphone2, cologne, nysiis, koelnerphonetik, haasephonetik, beidermorse. 기본값: metaphone - 바꾸기 - 형식: 부울 - 인코딩된 토큰이 원래 토큰을 바꾸는 경우 true, 기본적으로 동의어로 추가되어야 하는 경우 false, 기본값: true
	  </td>	  
    </tr>
    <tr>
      <td>[porter_stem] (http://lucene.apache.org/core/4_10_3/analyzers-common/org/tartarus/snowball/ext/PorterStemmer.html)</td>
      <td>#Microsoft.Azure.Search.PorterStemTokenFilter</td>
	  <td>[Porter 형태소 분석 알고리즘](http://tartarus.org/~martin/PorterStemmer/)을 기준으로 토큰 스트림을 변환합니다</td>
	  <td></td>	  
    </tr>
    <tr>
      <td>[역방향] (http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/reverse/ReverseStringFilter.html)</td>
      <td>#Microsoft.Azure.Search.ReverseTokenFilter</td>
	  <td>토큰 문자열을 역방향으로 바꿉니다.</td>
	  <td></td>	  
    </tr>
    <tr>
      <td>[scandinavian_normalization] (http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/miscellaneous/ScandinavianNormalizationFilter.html)</td>
      <td>#Microsoft.Azure.Search.ScandinavianNormalizationTokenFilter</td>
	  <td>동일하게 사용할 수 있는 스칸디나비아어 문자의 사용을 정규화합니다.</td>
	  <td></td>	  
    </tr>
    <tr>
      <td>[scandinavian_folding] (http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/miscellaneous/ScandinavianFoldingFilter.html)</td>
      <td>#Microsoft.Azure.Search.ScandinavianFoldingNormalizationTokenFilter</td>
	  <td>스칸디나비아어 문자 åÅäæÄÆ->a 및 öÖøØ->o로 접습니다. 또한 이중 모음 aa, ae, ao, oe 및 oo에서 첫 번째 것만 제외하고 용도를 구별합니다.</td>
	  <td></td>	  
    </tr>
    <tr>
      <td>[shingle] (http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/shingle/ShingleFilter.html)</td>
      <td>#Microsoft.Azure.Search.ShingleTokenFilter</td>
	  <td>토큰의 조합을 단일 토큰으로 만듭니다.</td>
	  <td>
		- maxShingleSize - 형식: int - 기본값: 2 - minShingleSize - 형식: int - 기본값: 2 - outputUnigrams - 형식: 부울 - true이면, 출력 스트림은 shingles 뿐만 아니라 입력 토큰(유 니그램)을 포함, 기본값: true - outputUnigramsIfNoShingles - 형식: 부울 - true이면, outputUnigrams의 동작을 재정의==shingles을 사용할 수 없는 경우 false, 기본값: false - tokenSeparator - 형식: 문자열 - shingle을 구성하는 인접한 토큰을 조인할 때 사용할 문자열, 기본값: " " - filterToken - 형식: 문자열 - 토큰이 없는 위치 각각에 삽입할 문자열, 기본값: "_"
	  </td>	  
    </tr>
    <tr>
      <td>[스노우볼] (http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/snowball/SnowballFilter.html)</td>
      <td>#Microsoft.Azure.Search.SnowballTokenFilter</td>
	  <td>스노우볼 형태소 분석 토큰 필터</td>
	  <td>
		- 언어 - 형식: 문자열 - 허용된 값: 아르메니아어, 바스크어, 카탈로니아어, 덴마크어, 네덜란드어, 영어, 핀란드어, 프랑스어, 독일어, 독일어2, 헝가리어, 이탈리아어, Kp, Lovins, 노르웨이어, Porter, 포르투갈어, 루마니아어, 러시아어, 스페인어, 스웨덴어, 터키어
	  </td>	  
    </tr>
	<tr>
      <td>[sorani_normalization] (http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/ckb/SoraniNormalizationFilter.html)</td>
      <td>#Microsoft.Azure.Search.SoraniNormalizationTokenFilter</td>
	  <td>Sorani 텍스트의 유니코드 표현을 정규화합니다.</td>
	  <td></td>	  
    </tr>
	<tr>
      <td>형태소 분석기</td>
      <td>#Microsoft.Azure.Search.StemmerTokenFilter</td>
	  <td>언어 특정 형태소 분석 필터</td>
	  <td>
		- 언어 - 형식: 문자열 - 허용된 값: - [아랍어](http://lucene.apache.org/core/4_9_0/analyzers-common/org/apache/lucene/analysis/ar/ArabicStemmer.html) - [아르메니아어](http://snowball.tartarus.org/algorithms/armenian/stemmer.html) - [바스크어](http://snowball.tartarus.org/algorithms/basque/stemmer.html) - [브라질어](http://lucene.apache.org/core/4_9_0/analyzers-common/org/apache/lucene/analysis/br/BrazilianStemmer.html) - [불가리아어](http://members.unine.ch/jacques.savoy/Papers/BUIR.pdf) - [카탈로니아어](http://snowball.tartarus.org/algorithms/catalan/stemmer.html) - [체코어](http://portal.acm.org/citation.cfm?id=1598600) - [덴마크어](http://snowball.tartarus.org/algorithms/danish/stemmer.html) - [네덜란드어](http://snowball.tartarus.org/algorithms/dutch/stemmer.html) - [dutch_kp](http://snowball.tartarus.org/algorithms/kraaij_pohlmann/stemmer.html) - [영어](http://snowball.tartarus.org/algorithms/porter/stemmer.html) - [light_english](http://ciir.cs.umass.edu/pubfiles/ir-35.pdf) - [minimal_english](http://www.researchgate.net/publication/220433848_How_effective_is_suffixing) - [possessive_english](http://lucene.apache.org/core/4_9_0/analyzers-common/org/apache/lucene/analysis/en/EnglishPossessiveFilter.html) - [porter2](http://snowball.tartarus.org/algorithms/english/stemmer.html) - [lovins](http://snowball.tartarus.org/algorithms/lovins/stemmer.html) - [핀란드어](http://snowball.tartarus.org/algorithms/finnish/stemmer.html) - [light_finnish](http://clef.isti.cnr.it/2003/WN_web/22.pdf) - [프랑스어](http://snowball.tartarus.org/algorithms/french/stemmer.html) - [light_french](http://dl.acm.org/citation.cfm?id=1141523) - [minimal_french](http://dl.acm.org/citation.cfm?id=318984) - [갈리시아어](http://bvg.udc.es/recursos_lingua/stemming.jsp) - [minimal_galician](http://bvg.udc.es/recursos_lingua/stemming.jsp) - [독일어](http://snowball.tartarus.org/algorithms/german/stemmer.html) - [german2](http://snowball.tartarus.org/algorithms/german2/stemmer.html) - [light_german](http://dl.acm.org/citation.cfm?id=1141523) - [minimal_german](http://members.unine.ch/jacques.savoy/clef/morpho.pdf) - [그리스어](http://sais.se/mthprize/2007/ntais2007.pdf) - [힌디어](http://computing.open.ac.uk/Sites/EACLSouthAsia/Papers/p6-Ramanathan.pdf) - [헝가리어](http://snowball.tartarus.org/algorithms/hungarian/stemmer.html) - [light_hungarian](http://dl.acm.org/citation.cfm?id=1141523&amp;dl=ACM&amp;coll=DL&amp;CFID=179095584&amp;CFTOKEN=80067181) - [인도네시아어](http://www.illc.uva.nl/Publications/ResearchReports/MoL-2003-02.text.pdf) - [아일랜드어](http://snowball.tartarus.org/otherapps/oregan/intro.html) - [이탈리아어](http://snowball.tartarus.org/algorithms/italian/stemmer.html) - [light_italian](http://www.ercim.eu/publication/ws-proceedings/CLEF2/savoy.pdf) - [sorani](http://lucene.apache.org/core/4_9_0/analyzers-common/org/apache/lucene/analysis/ckb/SoraniStemmer.html) - [latvian](http://lucene.apache.org/core/4_9_0/analyzers-common/org/apache/lucene/analysis/lv/LatvianStemmer.html) - [노르웨이어](http://snowball.tartarus.org/algorithms/norwegian/stemmer.html) - [light_norwegian](http://lucene.apache.org/core/4_9_0/analyzers-common/org/apache/lucene/analysis/no/NorwegianLightStemmer.html) - [minimal_norwegian](http://lucene.apache.org/core/4_9_0/analyzers-common/org/apache/lucene/analysis/no/NorwegianMinimalStemmer.html) - [light_nynorsk](http://lucene.apache.org/core/4_9_0/analyzers-common/org/apache/lucene/analysis/no/NorwegianLightStemmer.html) - [minimal_nynorsk](http://lucene.apache.org/core/4_9_0/analyzers-common/org/apache/lucene/analysis/no/NorwegianMinimalStemmer.html) - [포르투갈어](http://snowball.tartarus.org/algorithms/portuguese/stemmer.html) - [light_portuguese](http://dl.acm.org/citation.cfm?id=1141523&amp;dl=ACM&amp;coll=DL&amp;CFID=179095584&amp;CFTOKEN=80067181) - [minimal_portuguese](http://www.inf.ufrgs.br/~buriol/papers/Orengo_CLEF07.pdf) - [portuguese_rslp](http://www.inf.ufrgs.br//~viviane/rslp/index.htm) - [루마니아어](http://snowball.tartarus.org/algorithms/romanian/stemmer.html) - [러시아어](http://snowball.tartarus.org/algorithms/russian/stemmer.html) - [light_russian](http://doc.rero.ch/lm.php?url=1000%2C43%2C4%2C20091209094227-CA%2FDolamic_Ljiljana_-_Indexing_and_Searching_Strategies_for_the_Russian_20091209.pdf) - [스페인어](http://snowball.tartarus.org/algorithms/spanish/stemmer.html) - [light_spanish](http://www.ercim.eu/publication/ws-proceedings/CLEF2/savoy.pdf) - [스웨덴어](http://snowball.tartarus.org/algorithms/swedish/stemmer.html) - [light_swedish](http://clef.isti.cnr.it/2003/WN_web/22.pdf) - [터키어](http://snowball.tartarus.org/algorithms/turkish/stemmer.html)
	  </td>	  
    </tr>
	<tr>
      <td>[stemmer_override] (http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/miscellaneous/StemmerOverrideFilter.html)</td>
      <td>#Microsoft.Azure.Search.StemmerOverrideTokenFilter</td>
	  <td>사전 형태소 분석된 용어는 키워드로 표시되므로 일련의 형태소 분석기로 형태소를 분석하지 않습니다. 형태소 분석하는 필터 앞에 배치해야 합니다.</td>
	  <td>
		- 규칙 - 형식: 문자열 배열 - 다음과 같은 형식의 형태소 분석 규칙 "단어 => 형태소 분석" 예: "실행했다 => 실행하다", 기본값: 빈 목록
	  </td>	  
    </tr>
	<tr>
      <td>[중지] (http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/core/StopFilter.html)</td>
      <td>#Microsoft.Azure.Search.StopTokenFilter</td>
	  <td>토큰 스트림에서 중지 단어를 제거합니다</td>
	  <td>
		- 중지 단어 - 형식: 문자열 배열 - 중지 단어의 목록, 기본값: 빈 목록. -stopwords_list - 형식: 문자열 - 중지 단어의 미리 정의된 목록, 허용된 값: _arabic_, _armenian_, _basque_, _brazilian_, _bulgarian_, _catalan_, _czech_, _danish_, _dutch_, _english_, _finnish_, _french_, _galician_, _german_, _greek_, _hindi_, _hungarian_, _indonesian_, _irish_, _italian_, _latvian_, _norwegian_, _persian_, _portuguese_, _romanian_, _russian_, _sorani_, _spanish_, _swedish_, _thai_, _turkish_, 기본값: _english_ - ignoreCase - 형식: 부울 - true이면, 모든 단어는 우선 소문자, 기본값: false - removeTrailing - 형식: 부울 - true이면, 중지 단어이면 마지막 검색 용어 무시, 기본값: true
	  </td>	  
    </tr>
	<tr>
      <td>[동의어] (http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/synonym/SynonymFilter.html)</td>
      <td>#Microsoft.Azure.Search.SynonymTokenFilter</td>
	  <td>토큰 스트림에서 단일 또는  다중 단어 동의어를 일치시킵니다</td>
	  <td>
		- 동의어 - 형식: 문자열 배열 - 두 가지 형식 중 다음 하나에 있는 동의어의 목록: - incredible, unbelievable, fabulous => amazing - => 기호의 왼쪽에 있는 모든 용어는 해당하는 오른쪽에 있는 용어로 바뀜 - incredible, unbelievable, fabulous, amazing - 쉼표로 구분된 해당하는 단어의 목록. 확장 옵션을 설정하여 목록을 해석한 방법 변경 - ignoreCase - 형식: 부울 - 일치하려는 문자 접기 입력, 기본값: false - 확장 - 형식: 부울 - - true이면, 동의어 목록의 모든 단어(=> 표기법이 사용되지 않은 경우)는 서로 매핑됨. 다음 목록인 incredible, unbelievable, fabulous, amazing은  incredible, unbelievable, fabulous, amazing => incredible, unbelievable, fabulous, amazing에 해당합니다 - false이면, 다음 목록인 incredible, unbelievable, fabulous, amazing은 incredible, unbelievable, fabulous, amazing => incredible에 해당합니다   
	  </td>	  
    </tr>
	<tr>
      <td>[자르기] (http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/miscellaneous/TrimFilter.html)</td>
      <td>#Microsoft.Azure.Search.TrimTokenFilter</td>
	  <td>선행 및 후행 공백을 토큰에서 삭제합니다.</td>
	  <td></td>	  
    </tr>
	<tr>
      <td>[truncate] (http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/miscellaneous/TruncateTokenFilter.html)</td>
      <td>#Microsoft.Azure.Search.TruncateTokenFilter</td>
	  <td>용어를 특정 길이로 자릅니다.</td>
	  <td>
		- 길이 - 형식: int - 필수 옵션
	  </td>	  
    </tr>
	<tr>
      <td>[고유한] (http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/miscellaneous/RemoveDuplicatesTokenFilter.html)</td>
      <td>#Microsoft.Azure.Search.UniqueTokenFilter</td>
	  <td>이전 토큰과 동일한 텍스트를 사용하여 필터링합니다.</td>
	  <td>
		- onlyOnSamePosition - 형식: 부울 - 설정한 경우, 동일한 위치에서만 중복 항목 제거, 기본값: true
	  </td>	  
    </tr>
    <tr>
      <td>[대문자] (http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/core/UpperCaseFilter.html)</td>
      <td>#Microsoft.Azure.Search.UppercaseTokenFilter</td>
	  <td>토큰 텍스트를 대문자로 정규화합니다.</td>
	  <td></td>	  
    </tr>
	<tr>
      <td>[word_delimiter] (http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/miscellaneous/WordDelimiterFilter.html)</td>
      <td>#Microsoft.Azure.Search.WordDelimiterTokenFilter</td>
	  <td>하위 단어로 단어를 분할하고 하위 단어 그룹에서 선택적 변환을 수행합니다</td>
	  <td>
		- generateWordParts - 형식: 부울 - 예: 생성되는 단어의 부분 발생 "AzureSearch" -> "Azure" "검색", 기본값: true - generateNumberParts - 형식: 부울 - 생성된 숫자 하위 단어 발생, 기본값: true - catenateWords - 형식: 부울 - 예: 이어진 단어 일부의 최대 실행 발생 "Azure 검색" -> "AzureSearch", 기본값: false - catenateNumbers - 형식: 부울 - 예: 이어진 번호 일부의 최대 실행 발생 "1-2" -> "12", 기본값: false - catenateAll - 형식: 부울 - 예: 이어진 모든 하위 단어 일부 발생 "Azure-Search-1" -> "AzureSearch1", 기본값: false - splitOnCaseChange - 형식: 부울 - true이면, 예: caseChange에서 단어 분리 "AzureSearch" -> "Azure" "Search", 기본값: true - preserveOriginal - 원래 단어가 하위 단어 목록에 유지되고 추가됨, 기본값: false - splitOnNumerics - 형식: 부울 - true이면, 예: 번호에 분리, "Azure1Search" -> "Azure" "8" "Search", 기본값: true - stemEnglishPossessive - 형식: 부울 - 각 하위 단어에 후행 "'s"를 제거, 기본값: true - protectedWords - 형식: 문자열 배열 - 구분되도록 보호할 토큰, 기본값: 빈 목록
	  </td>	  
    </tr>
  </tbody>
</table>

**참고 항목** MSDN의 [Azure 검색 서비스 REST API](http://msdn.microsoft.com/library/azure/dn798935.aspx) MSDN의 <br/> [인덱스 만들기(Azure 검색 API)](http://msdn.microsoft.com/library/azure/dn798941.aspx)<br/>

<!---HONumber=AcomDC_0224_2016-->