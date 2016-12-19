---
title: "Marketplace용 데이터 서비스 만들기 가이드 | Microsoft Docs"
description: "Azure 마켓플레이스에서 구매하기 위한 데이터 서비스를 만들고 인증하고 배포하는 방법에 대한 자세한 지침입니다."
services: marketplace-publishing
documentationcenter: 
author: HannibalSII
manager: hascipio
editor: 
ms.assetid: 107baab2-5828-4158-abdf-59a580c80d37
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/26/2016
ms.author: hascipio; avikova
translationtype: Human Translation
ms.sourcegitcommit: f8b0917b6eb0295641360c4e0a80e81100809f6e
ms.openlocfilehash: e3ce01d20f6b47c6fe68fdbfe31679cc2c92f2e7


---
# <a name="understanding-the-nodes-schema-for-mapping-an-existing-web-service-to-odata-through-csdl"></a>CSDL을 통해 기존 웹 서비스를 OData에 매핑하는 노드 스키마 이해
> [!IMPORTANT]
> **현재는 새 데이터 서비스 게시자 등록을 더 이상 받지 않고 있습니다. 따라서 새 데이터 서비스 등재 승인을 받을 수 없습니다.** SaaS 비즈니스 응용 프로그램을 AppSource에 게시하려는 경우 [여기](https://appsource.microsoft.com/partners)에서 자세한 내용을 확인할 수 있습니다. IaaS 응용 프로그램 또는 개발자 서비스를 Azure Marketplace에 게시하려는 경우에는 [여기](https://azure.microsoft.com/marketplace/programs/certified/)에서 자세한 내용을 확인할 수 있습니다.
>
>

이 문서는 OData 프로토콜을 CSDL에 매핑하는 노드 구조를 명확하게 이해하는 데 도움이 됩니다. 노드 구조는 올바르게 구성된 XML입니다. 따라서 OData 매핑을 설계할 때 루트, 부모 및 자식 스키마를 적용할 수 있습니다.

## <a name="ignored-elements"></a>무시되는 요소
다음은 웹 서비스의 메타데이터를 가져오는 동안 Azure 마켓플레이스 백 엔드에서 사용하지 않는 상위 수준 CSDL 요소(XML 노드)입니다. 이러한 요소가 있더라도 무시됩니다.

| 요소 | 범위 |
| --- | --- |
| Using 요소 |노드, 하위 노드 및 모든 특성 |
| Documentation 요소 |노드, 하위 노드 및 모든 특성 |
| ComplexType |노드, 하위 노드 및 모든 특성 |
| Association 요소 |노드, 하위 노드 및 모든 특성 |
| 확장 속성 |노드, 하위 노드 및 모든 특성 |
| EntityContainer |*extends* 및 *AssociationSet* 특성만 무시됩니다. |
| 스키마 | *네임스페이스* |
| FunctionImport |*Mode* (ln의 기본값으로 가정) 특성만 무시됩니다. |
| EntityType |*Key* 및 *PropertyRef* 하위 노드만 무시됩니다. |

다음은 다양한 CSDL XML 노드의 변경 내용(추가된 요소 및 무시된 요소)을 자세히 설명합니다.

## <a name="functionimport-node"></a>FunctionImport 노드
FunctionImport 노드는 최종 사용자에게 서비스를 노출하는 하나의 URL(진입점)을 나타냅니다. 노드를 사용하여 URL이 처리되는 방식, 최종 사용자에게 제공되는 매개 변수, 매개 변수가 제공되는 방식을 설명할 수 있습니다.

이 노드에 대한 세부 정보는 [여기][MSDNFunctionImportLink](https://msdn.microsoft.com/library/cc716710.aspx)에서 확인할 수 있습니다.

다음은 FunctionImport 노드에 의해 노출되는 추가 특성(또는 특성에 추가되는 항목)입니다.

**d:BaseUri** -
마켓플레이스에 노출되는 REST 리소스에 대한 URI 템플릿입니다. 마켓플레이스에서는 이 템플릿을 사용하여 REST 웹 서비스에 대한 쿼리를 생성합니다. URI 템플릿은 매개 변수의 자리 표시자를 {parameterName} 형태로 포함하고 있으며, 여기서 parameterName은 해당 매개 변수의 이름입니다. 예: apiVersion={apiVersion}.
매개 변수는 URI 매개 변수 또는 URI 경로의 일부로 표시될 수 있습니다. 경로에 표시되는 경우 매개 변수가 항상 필수입니다(nullable로 표시할 수 없음). *예제:* `d:BaseUri="http://api.MyWeb.com/Site/{url}/v1/visits?start={start}&amp;end={end}&amp;ApiKey=3fadcaa&amp;Format=XML"`

**Name** - 가져온 함수의 이름입니다.  CSDL에서 정의된 다른 이름과 같으면 안 됩니다.  예: Name="GetModelUsageFile"

**EntitySet** *(선택 사항)* - 함수가 엔터티 유형 컬렉션을 반환하는 경우 **EntitySet**의 값은 컬렉션이 소속된 엔터티 집합이어야 합니다. 그렇지 않으면 **EntitySet** 특성을 사용할 수 없습니다. *예제:* `EntitySet="GetUsageStatisticsEntitySet"`

**ReturnType** *(선택 사항)* - URI에서 반환하는 요소 유형을 지정합니다.  함수에서 값을 반환하지 않는 경우에는 이 특성을 사용하지 마십시오. 지원되는 유형은 다음과 같습니다.

* **Collection(<Entity type name>)**: 정의된 엔터티 유형 컬렉션을 지정합니다. 이름은 EntityType 노드의 Name 특성에 있습니다. 예: Collection(WXC.HourlyResult)
* **Raw(<mime type>)**: 사용자에게 반환되는 원시 문서/blob를 지정합니다. 예: Raw(image/jpeg) 기타 예:

  * ReturnType="Raw(text/plain)"
  * ReturnType="Collection(sage.DeleteAllUsageFilesEntity)"*

**d:Paging** - REST 리소스에서 페이징을 처리하는 방식을 지정합니다. 매개 변수 값은 중괄호 안에 사용됩니다. 예: page={$page}&itemsperpage={$size} 제공되는 옵션:

* **None:** 페이징을 사용할 수 없습니다.
* **Skip:** 논리적 “skip” 및 “take”(위)를 통해 페이징이 표현됩니다. skip이 M 요소를 건너뛰면 take가 그 다음 N 요소를 반환합니다. 매개 변수 값: $skip
* **Take:** take는 그 다음 N 요소를 반환합니다. 매개 변수 값: $take
* **PageSize:** 논리적 페이지 및 크기(페이지당 항목 수)를 통해 페이징이 표현됩니다. 페이지는 반환되는 현재 페이지를 나타냅니다. 매개 변수 값: $page
* **Size:** size는 각 페이지에 반환되는 항목 수를 나타냅니다. 매개 변수 값: $size

**d:AllowedHttpMethods** *(선택 사항)* - REST 리소스에서 처리하는 동사를 지정합니다. 또한 지정된 값에 허용되는 동사를 제한합니다.  기본값은 POST입니다.  *예제:* `d:AllowedHttpMethods="GET"` 제공되는 옵션:

* **GET:** 데이터 반환에 주로 사용
* **POST:** 새 데이터 삽입에 주로 사용
* **PUT:** 데이터 업데이트에 주로 사용
* **DELETE:** 데이터 삭제에 사용

FunctionImport 노드의 추가 자식 노드(CSDL 문서에서는 다루지 않음)는 다음과 같습니다.

**d:RequestBody** *(선택 사항)* - 요청 본문을 사용하여 본문을 보내야 한다는 것을 나타냅니다. 요청 본문에 매개 변수를 제공할 수 있습니다. 매개 변수는 중괄호 안에 표현됩니다(예: {parameterName}). 이러한 매개 변수는 사용자 입력에서 콘텐츠 공급자의 서비스로 전송되는 본문으로 매핑됩니다. requestBody 요소는 httpMethod라는 특성을 갖고 있습니다. 이 특성은 두 가지 값을 허용합니다.

* **POST:** 요청이 HTTP POST인 경우에 사용
* **GET:** 요청이 HTTP GET인 경우에 사용

    예제:

        `<d:RequestBody d:httpMethod="POST">
        <![CDATA[
        <req1:Request xmlns:r1="http://schemas.mysite.com//generic/requests/1" Version="1.0">
        <req1:Query>{Query}</req1:Query><req1:AppId>D453474</req1:AppId>
        <req:DestinationSchemas><req1:Schema>Generic.RequestResponse[1.0]</req1:Schema>
        </req1:DestinationSchemas>
        </req1: Request>
        ]]>
        </d:RequestBody>`

**d:Namespaces** 및 **d:Namespace** - 이 노드는 함수 가져오기(URI 끝점)에 의해 반환되는 XML에 정의된 네임스페이스를 설명합니다. 백 엔드 서비스에서 반환하는 XML은 반환되는 콘텐츠를 구분하기 위해 필요한 만큼 네임스페이스를 포함할 수 있습니다. **이러한 네임스페이스가 d:Map 또는 d:Match XPath 쿼리에 사용될 경우 모든 네임스페이스를 나열해야 합니다.**  d:Namespaces 노드는 d:Namespace 노드 집합/목록을 포함하고 있습니다. 각각은 백 엔드 서비스 응답에 사용되는 하나의 네임스페이스를 나열합니다. 다음은 d:Namespace 노드의 특성입니다.

* **d:Prefix:** 서비스에서 반환하는 XML 결과에 표시된 것처럼 네임스페이스의 접두사입니다(예: f:FirstName, f:LastName, f는 접두사).
* **d:Uri:** 결과 문서에 사용된 네임스페이스의 전체 URI입니다. 런타임에 접두사를 확인하는 값을 나타냅니다.

**d:ErrorHandling** *(선택 사항)* - 이 노드는 오류 처리 조건을 포함합니다. 각 조건은 콘텐츠 공급자의 서비스에서 반환하는 결과에 대해 유효성이 검사됩니다. 조건이 제안된 HTTP 오류 코드와 일치하면 최종 사용자에게 오류 메시지가 반환됩니다.

**d:ErrorHandling** *(선택 사항)* 및 **d:Condition** *(선택 사항)* - 한 조건 노드는 콘텐츠 공급자의 서비스가 반환한 결과에서 검사되는 조건 하나를 포함합니다. 다음은 **필수** 특성입니다.

* **d:Match:** 지정된 노드/값이 콘텐츠 공급자의 출력 XML에 있는지 확인하는 XPath 식입니다. XPath는 출력에 대해 실행되며 조건이 일치하면 true, 일치하지 않으면 false를 반환해야 합니다.
* **d:HttpStatusCode:** 조건이 일치할 경우 마켓플레이스에서 반환해야 하는 HTTP 상태 코드입니다. 마켓플레이스는 HTTP 상태 코드를 통해 사용자에게 오류 신호를 보냅니다. HTTP 상태 코드의 목록은 http://en.wikipedia.org/wiki/HTTP_status_code에서 제공됩니다.
* **d:ErrorMessage:** HTTP 상태 코드와 함께 최종 사용자에게 반환되는 오류 메시지입니다. 기밀 정보가 포함되지 않은 친숙한 오류 메시지여야 합니다.

**d:Title** *(선택 사항)* - 함수 제목을 설명할 수 있습니다. 제목의 값은 다음에서 가져옵니다.

* 서비스 요청에서 반환하는 응답에서 제목을 검색할 위치를 지정하는 선택적 맵 특성(xpath)
* 또는 노드의 값으로 지정된 제목

**d:Rights** *(선택 사항)* - 함수와 연결된 권한(예: 저작권)입니다. 권한의 값은 다음에서 가져옵니다.

* 서비스 요청에서 반환하는 응답에서 권한을 검색할 위치를 지정하는 선택적 맵 특성(xpath)
* 또는 노드의 값으로 지정된 권한

**d:Description** *(선택 사항)* - 함수에 대한 간단한 설명입니다. 설명의 값은 다음에서 가져옵니다.

* 서비스 요청에서 반환하는 응답에서 설명을 검색할 위치를 지정하는 선택적 맵 특성(xpath)
* 또는 노드의 값으로 지정된 설명

**d:EmitSelfLink** - *위의 "반환된 데이터를 통해 '페이징'에 대한 FunctionImport" 예를 참조하세요.*

**d:EncodeParameterValue** - OData로 선택적 확장

**d:QueryResourceCost** - OData로 선택적 확장

**d:Map** - OData로 선택적 확장

**d:Headers** - OData로 선택적 확장

**d:Headers** - OData로 선택적 확장

**d:Value** - OData로 선택적 확장

**d:HttpStatusCode** - OData로 선택적 확장

**d:ErrorMessage** - OData로 선택적 확장

## <a name="parameter-node"></a>매개 변수 노드
이 노드는 FunctionImport 노드에서 지정된 URI 템플릿/요청 본문의 일부로 노출되는 하나의 매개 변수를 나타냅니다.

[여기](http://msdn.microsoft.com/library/ee473431.aspx)에서 "매개 변수 요소" 노드에 대해 자세하게 설명하는 매우 유용한 문서 페이지를 확인할 수 있습니다. 문서를 보기 위해 필요한 경우 **다른 버전** 드롭다운을 사용하여 다른 버전을 선택할 수 있습니다. *예제:* `<Parameter Name="Query" Nullable="false" Mode="In" Type="String" d:Description="Query" d:SampleValues="Rudy Duck" d:EncodeParameterValue="true" MaxLength="255" FixedLength="false" Unicode="false" annotation:StoreGeneratedPattern="Identity"/>`

| 매개 변수 특성 | 필수 여부 | 값 |
| --- | --- | --- |
| 이름 |예 |매개 변수의 이름입니다. 대/소문자를 구분합니다.  BaseUri 대/소문자가 일치해야 합니다. **예제:** `<Property Name="IsDormant" Type="Byte" />` |
| 형식 |예 |매개 변수 유형입니다. 값이 **EDMSimpleType** 또는 모델 범위에 포함되는 복합 형식이어야 합니다. 자세한 내용은 “지원되는 6가지 매개 변수/속성 유형”을 참조하세요.  (대/소문자를 구분합니다. 첫 번째 문자는 대문자, 나머지는 소문자입니다.)  또한 [개념적 모델 형식(CSDL)][MSDNParameterLink](http://msdn.microsoft.com/library/bb399548.aspx)도 참조하세요. **예제:** `<Property Name="LimitedPartnershipID " Type="Int32" />` |
| Mode |아니요 |매개 변수가 입력인지, 출력인지 아니면 입력/출력 매개 변수인지에 따라 **In**, Out 또는 InOut입니다. (Azure 마켓플레이스에서는 "IN"만 사용할 수 있습니다.) **예제:** `<Parameter Name="StudentID" Mode="In" Type="Int32" />` |
| MaxLength |아니요 |매개 변수에 허용되는 최대 길이입니다. **예제:** `<Property Name="URI" Type="String" MaxLength="100" FixedLength="false" Unicode="false" />` |
| 자릿수 |아니요 |매개 변수의 자릿수입니다. **예제:** `<Property Name="PreviousDate" Type="DateTime" Precision="0" />` |
| 확장 |아니요 |매개 변수의 크기입니다. **예제:** `<Property Name="SICCode" Type="Decimal" Precision="10" Scale="0" />` |

다음은 CSDL 사양에 추가된 특성입니다.

| 매개 변수 특성 | 설명 |
| --- | --- |
| **d:Regex** *(선택 사항)* |매개 변수의 입력 값을 검사하는 데 사용되는 regex 문입니다. 입력 값이 문과 일치하지 않으면 해당 값이 거부됩니다. 이를 이용하여 가능한 값 집합을 지정할 수 있습니다. 예를 들어 ^[0-9]+?$는 숫자만 허용합니다. **예:** `<Parameter Name="name" Mode="In" Type="String" d:Nullable="false" d:Regex="^[a-zA-Z]*$" d:Description="A name that cannot contain any spaces or non-alpha non-English characters" d:SampleValues="George |
| **d:Enum** *(선택 사항)* |파이프로 구분된 매개 변수에 유효한 값 목록입니다. 값의 유형이 정의된 매개 변수 유형과 일치해야 합니다. 예: `english |
| **d: Nullable** *(선택 사항)* |매개 변수가 null일 수 있는지 여부를 정의할 수 있습니다. 기본값은 true입니다. 그러나 URI 템플릿에서 경로의 일부로 노출되는 매개 변수는 null일 수 없습니다. 이러한 매개 변수에 대한 특성이 false로 설정되면 사용자 입력이 무시됩니다. **예제:** `<Parameter Name="BikeType" Type="String" Mode="In" Nullable="false"/>` |
| **d:SampleValue** *(선택 사항)* |UI에 클라이언트에 대한 메모로 표시되는 샘플 값입니다.  즉, 파이프로 구분된 목록을 사용하여 여러 값을 추가할 수 있습니다. `a |

## <a name="entitytype-node"></a>EntityType 노드
이 노드는 마켓플레이스에서 최종 사용자에게 반환되는 유형 중 하나를 나타냅니다. 또한 콘텐츠 공급자의 서비스가 반환하는 출력에서 최종 사용자에게 반환되는 값으로의 매핑을 포함합니다.

이 노드에 대한 자세한 내용은 [여기](http://msdn.microsoft.com/library/bb399206.aspx)에서 확인할 수 있습니다. 문서를 보기 위해 필요한 경우 **다른 버전** 드롭다운을 사용하여 다른 버전을 선택할 수 있습니다.

| 특성 이름 | 필수 여부 | 값 |
| --- | --- | --- |
| 이름 |예 |엔터티 유형의 이름입니다. **예제:** `<EntityType Name="ListOfAllEntities" d:Map="//EntityModel">` |
| BaseType |아니요 |정의되는 엔터티 유형의 기본 유형인 또 다른 엔터티 유형의 이름입니다. **예제:** `<EntityType Name="PhoneRecord" BaseType="dqs:RequestRecord">` |

다음은 CSDL 사양에 추가된 특성입니다.

**d:Map** - 서비스 출력에 대해 실행되는 XPath 식입니다. 여기서 반복되는 항목 노드 집합이 있는 ATOM 피드처럼 반복되는 요소 집합이 서비스 출력에 포함되는 것으로 가정합니다. 이러한 반복 노드는 하나의 레코드를 포함합니다. 그런 다음 개별 레코드의 값을 포함하고 있는 콘텐츠 공급자 서비스 결과의 개별 반복 노드를 가리키도록 XPath가 지정됩니다. 서비스의 출력 예:

        `<foo>
          <bar> … content … </bar>
          <bar> … content … </bar>
          <bar> … content … </bar>
        </foo>`

XPath 식은 /foo/bar가 됩니다. 각 bar 노드는 출력의 반복 노드이고 최종 사용자에 반환되는 실제 콘텐츠를 포함하기 때문입니다.

**Key** - 이 특성은 마켓플레이스에서 무시됩니다. 일반적으로 REST 기반 웹 서비스는 기본 키를 노출하지 않습니다.

## <a name="property-node"></a>속성 노드
이 노드는 레코드의 한 속성을 포함합니다.

이 노드에 대한 자세한 내용은 [http://msdn.microsoft.com/library/bb399546.aspx](http://msdn.microsoft.com/library/bb399546.aspx)에서 찾을 수 있습니다. 문서를 보기 위해 필요한 경우 **다른 버전** 드롭다운을 사용하여 다른 버전을 선택할 수 있습니다. *예:*
        `<EntityType Name="MetaDataEntityType" d:Map="/MyXMLPath">
        <Property Name="Name"     Type="String" Nullable="true" d:Map="./Service/Name" d:IsPrimaryKey="true" DefaultValue=”Joe Doh” MaxLength="25" FixedLength="true" />
        ...
        </EntityType>`

| 특성 이름 | 필수 | 값 |
| --- | --- | --- |
| 이름 |예 |속성의 이름입니다. |
| 형식 |예 |속성 값의 유형입니다. 속성 값 유형은 **EDMSimpleType** 또는 모델 범위에 포함되는 복합 유형(정규화된 이름으로 표시)이어야 합니다. 자세한 내용은 개념적 모델 유형(CSDL)을 참조하세요. |
| Nullable |아니요 |속성이 null 값을 가질 수 있는지 여부에 따라 **True**(기본값) 또는 **False**입니다. 참고: [http://schemas.microsoft.com/ado/2006/04/edm](http://schemas.microsoft.com/ado/2006/04/edm) 네임스페이스를 통해 표시되는 CSDL 버전에서는 복합 유형 속성의 값이 Nullable="False"여야 합니다. |
| DefaultValue |아니요 |속성의 기본값입니다. |
| MaxLength |아니요 |속성 값의 최대 길이입니다. |
| FixedLength |아니요 |속성 값이 고정 길이 문자열로 저장되는지 여부에 따라 **True** 또는 **False**입니다. |
| 자릿수 |아니요 |숫자 값에서 유지할 최대 자릿수를 나타냅니다. |
| 확장 |아니요 |숫자 값에서 유지할 최대 소수 자릿수입니다. |
| Unicode |아니요 |속성 값이 유니코드 문자열로 저장되는지 여부에 따라 **True** 또는 **False**입니다. |
| Collation |아니요 |데이터 원본에 사용할 정렬 순서를 지정하는 문자열입니다. |
| ConcurrencyMode |아니요 |**없음**(기본값) 또는 **고정**입니다. 이 값을 **고정**으로 설정하면 속성 값이 낙관적 동시성 검사에 사용됩니다. |

다음은 CSDL 사양에 추가된 추가 특성입니다.

**d:Map** - 서비스에 대해 실행되는 XPath 식으로 출력의 한 속성을 추출합니다. 지정된 XPath는 EntityType 노드의 XPath에서 선택한 반복 노드에 상대적입니다. 절대 XPath를 지정하여 각 출력 노드에 정적 리소스를 포함하는 것도 가능합니다. 예를 들어 저작권 정보는 원래 서비스 출력에서 한 번만 표시되지만 OData 출력의 각 행마다 있어야 합니다. 서비스의 예:

        `<foo>
          <bar>
           <baz0>… value …</baz0>
           <baz1>… value …</baz1>
           <baz2>… value …</baz2>
          </bar>
        </foo>`

여기서 XPath 식은 콘텐츠 공급자의 서비스에서 baz0 노드를 가져오는 ./bar/baz0입니다.

**d:CharMaxLength** - 문자열 유형에 대한 최대 길이를 지정할 수 있습니다. DataService CSDL 예를 참조하세요.

**d:IsPrimaryKey** - 열이 테이블/보기의 기본 키인지 여부를 나타냅니다. DataService CSDL 예를 참조하세요.

**d:isExposed** - 테이블 스키마의 노출 여부(일반적으로 true)를 결정합니다. DataService CSDL 예를 참조하세요.

**d:IsView** *(선택 사항)* - 테이블이 아닌 보기를 기반으로 할 경우 true입니다.  DataService CSDL 예를 참조하세요.

**d:Tableschema** - DataService CSDL 예를 참조하세요.

**d:ColumnName** - 테이블/보기의 열 이름입니다.  DataService CSDL 예를 참조하세요.

**d:IsReturned** - 서비스에서 이 값을 클라이언트에 노출할지 여부를 결정하는 부울입니다.  DataService CSDL 예를 참조하세요.

**d:IsQueryable** - 데이터베이스 쿼리에 열을 사용할 수 있는지 여부를 결정하는 부울입니다.   DataService CSDL 예를 참조하세요.

**d:OrdinalPosition** - 테이블 또는 보기에서 열이 표시되는 숫자 위치 x입니다. 여기서 x는 1부터 테이블의 열 수까지입니다.  DataService CSDL 예를 참조하세요.

**d:DatabaseDataType** - 데이터베이스에 있는 열의 데이터 유형, 다시 말해서 SQL 데이터 유형입니다. DataService CSDL 예를 참조하세요.

## <a name="supported-parametersproperty-types"></a>지원되는 매개 변수/속성 유형
다음은 매개 변수 및 속성에 대해 지원되는 유형입니다. (대/소문자 구분)

| 기본 유형 | 설명 |
| --- | --- |
| Null |값이 없음을 나타냅니다. |
| Boolean |이진 값 논리의 수학적 개념을 나타냅니다. |
| Byte |부호 없는 8비트 정수 값 |
| DateTime |1753년 1월 1일 자정 12:00:00부터 9999년 12월 오후 11:59:59 사이의 값을 사용하여  날짜 및 시간을 나타냅니다. |
| DECIMAL |고정 전체 자릿수와 소수 자릿수를 사용하여 숫자 값을 나타냅니다. 이 유형은 음수 10^255+1부터 양수 10^255-1 사이의 숫자 값을 설명할 수 있습니다. |
| Double |약 ±2.23e-308부터 ±1.79e+308 사이의 값을 표시할 수 있는 15자리의 부동 소수점 숫자를 나타냅니다. **Exel 내보내기 문제가 있으니 10진수를 사용하세요.** |
| Single |약 ±1.18e-38부터 ±3.40e+38 사이의 값을 표시할 수 있는 7자리의 부동 소수점 숫자를 나타냅니다. |
| Guid |16바이트(128비트) 고유 식별자 값을 나타냅니다. |
| Int16 |부호 있는 16비트 정수 값을 나타냅니다. |
| Int32 |부호 있는 32비트 정수 값을 나타냅니다. |
| Int64 |부호 있는 64비트 정수 값을 나타냅니다. |
| String |고정 또는 가변 길이 문자 데이터를 나타냅니다. |

## <a name="see-also"></a>참고 항목
* 전체 OData 매핑 프로세스와 목적을 이해하려는 경우 문서 [데이터 서비스 OData 매핑](marketplace-publishing-data-service-creation-odata-mapping.md) 을 읽고 정의, 구조 및 지침을 검토하세요.
* 예제를 검토하고 싶으면 [데이터 서비스 OData 매핑 예제](marketplace-publishing-data-service-creation-odata-mapping-examples.md) 문서를 통해 샘플 코드를 살펴보고 코드 구문 및 컨텍스트를 이해하세요.
* Azure 마켓플레이스에 데이터 서비스를 게시하기 위한 규정된 경로로 반환하려면 문서 [데이터 서비스 게시 가이드](marketplace-publishing-data-service-creation.md)를 읽어 보세요.



<!--HONumber=Nov16_HO3-->


