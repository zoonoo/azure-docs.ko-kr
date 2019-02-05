---
title: '예제: 시작 - Knowledge Exploration Service API'
titlesuffix: Azure Cognitive Services
description: KES(Knowledge Exploration Service) API를 사용하여 학술 게시에 대한 대화형 검색 환경 엔진을 만듭니다.
services: cognitive-services
author: bojunehsu
manager: cgronlun
ms.service: cognitive-services
ms.subservice: knowledge-exploration
ms.topic: sample
ms.date: 03/26/2016
ms.author: paulhsu
ms.openlocfilehash: 14dc1ca90ecd342330425db840776fa67caa80b0
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55208145"
---
# <a name="get-started-with-the-knowledge-exploration-service"></a>Knowledge Exploration Service 시작

이 연습에서는 KES(Knowledge Exploration Service)를 사용하여 학술 게시에 대한 대화형 검색 환경 엔진을 만듭니다. 명령줄 도구인 [`kes.exe`](CommandLine.md) 및 [Knowledge Exploration Service SDK](https://www.microsoft.com/en-us/download/details.aspx?id=51488)의 모든 예제 파일을 설치할 수 있습니다.

학술 게시 예제에는 Microsoft 연구원들이 게시한 1000개의 학술 논문 샘플이 포함되어 있습니다.  각 논문에는 제목, 게시 연도, 작성자 및 키워드가 연결되어 있습니다. 각 작성자에 대해 ID, 이름 및 게시 시점의 소속이 표시됩니다. 각 키워드에 일련의 동의어를 연결할 수 있습니다(예: “support vector machine” 키워드에 동의어 “svm”을 연결할 수 있음).

## <a name="define-the-schema"></a>스키마 정의

스키마는 도메인에 있는 개체의 특성 구조를 설명합니다. JSON 파일 형식의 각 특성에 대한 이름 및 데이터 형식을 지정합니다. 다음 예제는 *Academic.schema* 파일의 내용입니다.

```json
{
  "attributes":[
    {"name":"Title", "type":"String"},
    {"name":"Year", "type":"Int32"},
    {"name":"Author", "type":"Composite"},
    {"name":"Author.Id", "type":"Int64", "operations":["equals"]},
    {"name":"Author.Name", "type":"String"},
    {"name":"Author.Affiliation", "type":"String"},
    {"name":"Keyword", "type":"String", "synonyms":"Keyword.syn"}
  ]
}
```

여기서는 *Title*, *Year* 및 *Keyword*를 각각 문자열, 정수 및 문자열 특성으로 정의합니다. 작성자는 ID, 이름 및 소속으로 표시되므로 다음 세 가지 하위 특성이 있는 복합 특성으로 *Author*를 정의합니다. *Author.Id*, *Author.Name* 및 *Author.Affiliation*.

기본적으로 특성은 *equals*, *starts_with*, *is_between*을 포함하여 데이터 형식에 사용 가능한 모든 작업을 지원합니다. 작성자 ID는 내부적으로만 식별자로 사용되므로 기본값을 재정의하고 *equals*를 유일한 인덱싱된 작업으로 지정합니다.

*Keyword* 특성의 경우 특성 정의에서 동의어 파일 *Keyword.syn*을 지정하여 동의어가 표준 키워드 값과 일치할 수 있도록 합니다. 이 파일에는 표준 및 동의어 값 쌍의 목록이 포함되어 있습니다.

```json
...
["support vector machine","support vector machines"]
["support vector machine","support vector networks"]
["support vector machine","support vector regression"]
["support vector machine","support vector"]
["support vector machine","svm machine learning"]
["support vector machine","svm"]
["support vector machine","svms"]
["support vector machine","vector machine"]
...
```

스키마 정의에 대한 자세한 내용은 [스키마 형식](SchemaFormat.md)을 참조하세요.

## <a name="generate-data"></a>데이터 생성

데이터 파일은 각 줄에서 논문의 특성 값을 [JSON 형식](http://json.org/)으로 지정하여 인덱싱할 게시 목록을 설명합니다.  다음 예제는 데이터 파일 *Academic.data*의 한 줄이며, 읽기 쉽도록 서식이 지정되었습니다.

```
...
{
    "logprob": -16.714,
    "Title": "the world wide telescope",
    "Year": 2001,
    "Author": [
        {
            "Id": 717694024,
            "Name": "alexander s szalay",
            "Affiliation": "microsoft"
        },
        {
            "Id": 2131537204,
            "Name": "jim gray",
            "Affiliation": "microsoft"
        }
    ]
}
...
```

이 코드 조각에서는 논문의 *Title* 및 *Year* 특성을 각각 JSON 문자열과 숫자로 지정합니다. 다중 값은 JSON 배열을 사용하여 나타냅니다. *Author*는 복합 특성이므로 해당 하위 특성으로 구성된 JSON 개체를 사용하여 각 값을 나타냅니다. 누락된 값이 있는 특성(이 예제에서는 *Keyword*)은 JSON 표현에서 제외될 수 있습니다.

각 논문의 발생 가능성을 구분하려면 기본 제공 *logprob* 특성을 사용하여 상대 로그 확률을 지정합니다. 확률 *p*를 0에서 1 사이의 값이라고 가정하여, 로그 확률을 log(*p*)로 계산합니다. 여기서 log()는 자연 로그 함수입니다.

자세한 내용은 [데이터 형식](DataFormat.md)을 참조하세요.

## <a name="build-a-compressed-binary-index"></a>압축된 이진 인덱스 빌드

스키마 파일과 데이터 파일이 준비되면 [`kes.exe build_index`](CommandLine.md#build_index-command)를 사용하여 데이터 개체의 압축된 이진 인덱스를 작성할 수 있습니다. 이 예제에서는 입력 스키마 파일 *Academic.schema* 및 데이터 파일 *Academic.data*에서 인덱스 파일 *Academic.index*를 작성합니다. 다음 명령을 사용합니다.

`kes.exe build_index Academic.schema Academic.data Academic.index`

Azure 외부에서 프로토타입을 신속하게 생성하기 위해 [`kes.exe build_index`](CommandLine.md#build_index-command)는 최대 10,000개의 개체가 포함된 데이터 파일에서 작은 인덱스를 로컬로 작성할 수 있습니다. 큰 데이터 파일의 경우 [Azure의 Windows VM](../../../articles/virtual-machines/windows/quick-create-portal.md) 내에서 명령을 실행하거나 Azure에서 원격 빌드를 수행할 수 있습니다. 자세한 내용은 [확장](#scaling-up)을 참조하세요.

## <a name="use-an-xml-grammar-specification"></a>XML 문법 사양 사용

문법은 서비스에서 해석할 수 있는 자연어 쿼리 집합 및 이러한 자연어 쿼리가 의미 체계 쿼리 식으로 변환하는 방법을 지정합니다. 이 예제에서는 *academic.xml*에 지정된 문법을 사용합니다.

```xml
<grammar root="GetPapers">

  <!-- Import academic data schema-->
  <import schema="Academic.schema" name="academic"/>

  <!-- Define root rule-->
  <rule id="GetPapers">
    <example>papers about machine learning by michael jordan</example>

    papers
    <tag>
      yearOnce = false;
      isBeyondEndOfQuery = false;
      query = All();
    </tag>

    <item repeat="1-" repeat-logprob="-10">
      <!-- Do not complete additional attributes beyond end of query -->
      <tag>AssertEquals(isBeyondEndOfQuery, false);</tag>

      <one-of>
        <!-- about <keyword> -->
        <item logprob="-0.5">
          about <attrref uri="academic#Keyword" name="keyword"/>
          <tag>query = And(query, keyword);</tag>
        </item>

        <!-- by <authorName> [while at <authorAffiliation>] -->
        <item logprob="-1">
          by <attrref uri="academic#Author.Name" name="authorName"/>
          <tag>authorQuery = authorName;</tag>
          <item repeat="0-1" repeat-logprob="-1.5">
            while at <attrref uri="academic#Author.Affiliation" name="authorAffiliation"/>
            <tag>authorQuery = And(authorQuery, authorAffiliation);</tag>
          </item>
          <tag>
            authorQuery = Composite(authorQuery);
            query = And(query, authorQuery);
          </tag>
        </item>

        <!-- written (in|before|after) <year> -->
        <item logprob="-1.5">
          <!-- Allow this grammar path to be traversed only once -->
          <tag>
            AssertEquals(yearOnce, false);
            yearOnce = true;
          </tag>
          <ruleref uri="#GetPaperYear" name="year"/>
          <tag>query = And(query, year);</tag>
        </item>
      </one-of>

      <!-- Determine if current parse position is beyond end of query -->
      <tag>isBeyondEndOfQuery = GetVariable("IsBeyondEndOfQuery", "system");</tag>
    </item>
    <tag>out = query;</tag>
  </rule>

  <rule id="GetPaperYear">
    <tag>year = All();</tag>
    written
    <one-of>
      <item>
        in <attrref uri="academic#Year" name="year"/>
      </item>
      <item>
        before
        <one-of>
          <item>[year]</item>
          <item>
            <attrref uri="academic#Year" op="lt" name="year"/>
          </item>
        </one-of>
      </item>
      <item>
        after
        <one-of>
          <item>[year]</item>
          <item>
            <attrref uri="academic#Year" op="gt" name="year"/>
          </item>
        </one-of>
      </item>
    </one-of>
    <tag>out = year;</tag>
  </rule>
</grammar>
```

문법 사양 구문에 대한 자세한 내용은 [문법 형식](GrammarFormat.md)을 참조하세요.

## <a name="compile-the-grammar"></a>문법 컴파일

XML 문법 사양이 준비되면 [`kes.exe build_grammar`](CommandLine.md#build_grammar-command)를 사용하여 이진 문법으로 컴파일할 수 있습니다. 문법이 스키마를 가져오는 경우 스키마 파일이 문법 XML과 동일한 경로에 있어야 합니다. 이 예제에서는 입력 XML 문법 파일 *Academic.xml*에서 이진 문법 파일 *Academic.grammar*를 작성합니다. 다음 명령을 사용합니다.

`kes.exe build_grammar Academic.xml Academic.grammar`

## <a name="host-the-grammar-and-index-in-a-web-service"></a>웹 서비스에서 문법 및 인덱스 호스트

프로토타입을 신속하게 생성하기 위해 [`kes.exe host_service`](CommandLine.md#host_service-command)를 사용하여 로컬 컴퓨터의 웹 서비스에서 문법 및 인덱스를 호스트할 수 있습니다. 그런 다음, [Web API](WebAPI.md)를 통해 서비스에 액세스하여 데이터 정확성과 문법 디자인을 확인할 수 있습니다. 이 예제에서는 http://localhost:8000/에서 문법 파일 *Academic.grammar*와 인덱스 파일 *Academic.index*를 호스트합니다. 다음 명령을 사용합니다.

`kes.exe host_service Academic.grammar Academic.index --port 8000`

그러면 웹 서비스의 로컬 인스턴스가 시작됩니다. 브라우저에서 `http::localhost:<port>`를 방문하여 서비스를 대화형으로 테스트할 수 있습니다. 자세한 내용은 [서비스 테스트](#testing-service)를 참조하세요.

다양한 [Web API](WebAPI.md)를 직접 호출하여 자연어 해석, 쿼리 완성, 구조화된 쿼리 평가 및 히스토그램 계산을 테스트할 수도 있습니다. 서비스를 중지하려면 `kes.exe host_service` 명령 프롬프트에 “quit”를 입력하거나 Ctrl+C를 누릅니다. 예를 들어 다음과 같은 노래를 선택할 수 있다.

* [http://localhost:8000/interpret?query=papers by susan t dumais](http://localhost:8000/interpret?query=papers%20by%20susan%20t%20dumais)
* [http://localhost:8000/interpret?query=papers by susan t d&complete=1](http://localhost:8000/interpret?query=papers%20by%20susan%20t%20d&complete=1)
* [http://localhost:8000/evaluate?expr=Composite(Author.Name=='susan t dumais')&attributes=Title,Year,Author.Name,Author.Id&count=2](http://localhost:8000/evaluate?expr=Composite%28Author.Name==%27susan%20t%20dumais%27%29&attributes=Title,Year,Author.Name,Author.Id&count=2)
* [http://localhost:8000/calchistogram?expr=And(Composite(Author.Name=='susan t dumais'),Year>=2013)&attributes=Year,Keyword&count=4](http://localhost:8000/calchistogram?expr=And%28Composite%28Author.Name=='susan%20t%20dumais'%29,Year>=2013%29&attributes=Year,Keyword&count=4)

Azure 외부에서는 [`kes.exe host_service`](CommandLine.md#host_service-command)가 최대 10,000개의 개체 인덱스로 제한됩니다. 기타 한도에는 초당 10개 요청의 API 속도, 프로세스가 자동으로 종료되기까지 총 1000개의 요청 등이 있습니다. 이러한 제한을 무시하려면 [Azure의 Windows VM](../../../articles/virtual-machines/windows/quick-create-portal.md) 내에서 명령을 실행하거나, [`kes.exe deploy_service`](CommandLine.md#deploy_service-command) 명령을 사용하여 Azure 클라우드 서비스에 배포합니다. 자세한 내용은 [서비스 배포](#deploying-service)를 참조하세요.

## <a name="scale-up-to-host-larger-indices"></a>더 큰 인덱스 호스트를 위해 확장

Azure 외부에서 `kes.exe`를 실행하는 경우 인덱스가 10,000개의 개체로 제한됩니다. Azure를 사용하여 더 큰 인덱스를 작성하고 호스트할 수 있습니다. [평가판](https://azure.microsoft.com/pricing/free-trial/)에 등록합니다. 또는 Visual Studio 또는 MSDN을 구독하는 경우 [구독자 혜택을 활성화](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)할 수 있습니다. 이러한 구독은 매달 일정한 Azure 크레딧을 제공합니다.

`kes.exe`가 Azure 계정에 액세스할 수 있게 하려면 Azure Portal에서 [Azure 게시 설정 파일을 다운로드](https://portal.azure.com/#blade/Microsoft_Azure_ClassicResources/PublishingProfileBlade)합니다. 메시지가 표시되면 원하는 Azure 계정에 로그인합니다. `kes.exe`가 실행되는 작업 디렉터리에 파일을 *AzurePublishSettings.xml*로 저장합니다.

큰 인덱스를 작성하고 호스트하는 방법에는 두 가지가 있습니다. 첫 번째 방법은 Azure의 Windows VM에서 스키마 및 데이터 파일을 준비하는 것입니다. 그런 다음, [`kes.exe build_index`](#building-index)를 실행하여 크기 제한 없이 VM에서 로컬로 인덱스를 작성합니다. 제한 없이 프로토타입을 신속하게 생성하기 위해 [`kes.exe host_service`](#hosting-service)를 사용하여 결과로 생성된 인덱스를 VM에서 로컬로 호스트할 수 있습니다. 자세한 단계는 [Azure VM 자습서](../../../articles/virtual-machines/windows/quick-create-portal.md)를 참조하세요.

두 번째 방법은 [`kes.exe build_index`](CommandLine.md#build_index-command)에 `--remote` 매개 변수를 사용하여 원격 Azure 빌드를 수행하는 것입니다. 그러면 Azure VM 크기가 지정됩니다. `--remote` 매개 변수를 지정하면 명령은 해당 크기의 임시 Azure VM을 만듭니다. VM에서 인덱스를 작성하고, 인덱스를 대상 Blob Storage에 업로드한 다음, 완료 시 VM을 삭제합니다. 인덱스를 작성하는 동안 Azure 구독에 VM 비용이 청구됩니다.

이 원격 Azure 빌드 기능을 사용하면 모든 환경에서 [`kes.exe build_index`](CommandLine.md#build_index-command)를 실행할 수 있습니다. 원격 빌드를 수행하는 경우 입력 스키마 및 데이터 인수는 로컬 파일 경로 또는 [Azure Blob Storage](../../storage/blobs/storage-dotnet-how-to-use-blobs.md) URL일 수 있습니다. 출력 인덱스 인수는 Blob Storage URL이어야 합니다. Azure Storage 계정을 만들려면 [Azure Storage 계정 정보](../../storage/common/storage-create-storage-account.md)를 참조하세요. Blob Storage에서 파일을 효율적으로 복사하려면 [AzCopy](../../storage/common/storage-use-azcopy.md) 유틸리티를 사용합니다.

이 예제에서는 다음 Blob Storage 컨테이너가 이미 생성되었다고 가정할 수 있습니다. http://&lt;*account*&gt;.blob.core.windows.net/&lt;*container*&gt;/. 이 컨테이너에는 *Academic.schema* 스키마, 참조된 동의어 파일 *Keywords.syn*, 대규모 데이터 파일 *Academic.full.data*이 들어 있습니다. 다음 명령을 사용하여 원격으로 전체 인덱스를 작성할 수 있습니다.

`kes.exe build_index http://<account>.blob.core.windows.net/<container>/Academic.schema http://<account>.blob.core.windows.net/<container>/Academic.full.data http://<account>.blob.core.windows.net/<container>/Academic.full.index --remote <vm_size>`

인덱스를 작성하기 위해 임시 VM을 프로비전하는 데 5-10분 정도 걸릴 수 있습니다. 프로토타입을 신속하게 생성하기 위해 다음을 수행할 수 있습니다.
- 임의 컴퓨터에서 로컬로 작은 데이터 집합을 사용하여 개발합니다.
- 수동으로 [Azure VM을 만들고](../../../articles/virtual-machines/windows/quick-create-portal.md), 원격 데스크톱을 통해 [Azure VM에 연결](../../../articles/virtual-machines/windows/quick-create-portal.md#connect-to-virtual-machine)한 다음, [Knowledge Exploration Service SDK](https://www.microsoft.com/en-us/download/details.aspx?id=51488)를 설치하고, VM 내에서 [`kes.exe`](CommandLine.md)를 실행합니다.

페이징으로 인해 빌드 프로세스의 속도가 느려집니다. 페이징을 방지하려면 RAM 크기가 입력 데이터 파일 크기의 세 배인 VM을 인덱스 작성에 사용합니다. RAM이 인덱스 크기보다 1GB 더 큰 VM을 호스팅에 사용합니다. 사용 가능한 VM 크기 목록은 [가상 머신의 크기](../../../articles/virtual-machines/virtual-machines-windows-sizes.md)를 참조하세요.

## <a name="deploy-the-service"></a>서비스 배포

문법과 인덱스가 있으면 Azure 클라우드 서비스에 서비스를 배포할 준비가 된 것입니다. 새 Azure 클라우드 서비스를 만들려면 [클라우드 서비스를 만들고 배포하는 방법](../../../articles/cloud-services/cloud-services-how-to-create-deploy-portal.md)을 참조하세요. 이 시점에는 배포 패키지를 지정하지 마세요.  

클라우드 서비스를 만든 후 [`kes.exe deploy_service`](CommandLine.md#deploy_service-command)를 사용하여 서비스를 배포할 수 있습니다. Azure 클라우드 서비스에는 두 가지 배포 슬롯(프로덕션 및 스테이징)이 있습니다. 라이브 사용자 트래픽을 수신하는 서비스는 처음에 스테이징 슬롯에 배포해야 합니다. 서비스가 시작되고 초기화될 때까지 기다리세요. 그런 다음, 몇 개의 요청을 전송하여 배포의 유효성을 검사하고 기본 테스트를 통과하는지 확인할 수 있습니다.

라이브 트래픽이 이제 새로 배포된 서비스로 전송되도록 스테이징 슬롯의 콘텐츠를 프로덕션 슬롯과 [교환](../../../articles/cloud-services/cloud-services-nodejs-stage-application.md)합니다. 새 데이터로 업데이트된 서비스 버전을 배포하는 경우 이 프로세스를 반복할 수 있습니다. 다른 모든 Azure 클라우드 서비스와 마찬가지로 Azure Portal을 선택적으로 사용하여 [자동 크기 조정](../../../articles/cloud-services/cloud-services-how-to-scale-portal.md)을 구성할 수 있습니다.

이 예제에서는 *<vm_size>* VM을 사용하여 기존 클라우드 서비스의 스테이징 슬롯에 *Academic* 인덱스를 배포합니다. 다음 명령을 사용합니다.

`kes.exe deploy_service http://<account>.blob.core.windows.net/<container>/Academic.grammar http://<account>.blob.core.windows.net/<container>/Academic.index <serviceName> <vm_size> --slot Staging`

사용 가능한 VM 크기 목록은 [가상 머신 크기](../../../articles/virtual-machines/virtual-machines-windows-sizes.md)를 참조하세요.

서비스를 배포한 후 다양한 [Web API](WebAPI.md)를 호출하여 자연어 해석, 쿼리 완성, 구조화된 쿼리 평가 및 히스토그램 계산을 테스트할 수 있습니다.  

## <a name="test-the-service"></a>서비스 테스트

라이브 서비스를 디버그하려면 웹 브라우저에서 호스트 컴퓨터를 찾습니다. [host_service](#hosting-service)를 통해 배포된 로컬 서비스의 경우 `http://localhost:<port>/`를 방문하세요.  [deploy_service](#deploying-service)를 통해 배포된 Azure 클라우드 서비스의 경우 `http://<serviceName>.cloudapp.net/`을 방문하세요.

이 페이지에는 이 서비스에 호스트된 문법 및 인덱스뿐 아니라 기본 API 호출 통계에 대한 정보 링크가 포함되어 있습니다. Web API 사용을 보여 주는 대화형 검색 인터페이스도 있습니다. 검색 상자에 쿼리를 입력하여 [interpret](interpretMethod.md), [evaluate](evaluateMethod.md) 및 [calchistogram](calchistogramMethod.md) API 호출의 결과를 확인합니다. 이 페이지의 기본 HTML 소스는 Web API를 앱에 통합하여 풍부한 대화형 검색 환경을 만드는 방법의 예로 사용할 수도 있습니다.


