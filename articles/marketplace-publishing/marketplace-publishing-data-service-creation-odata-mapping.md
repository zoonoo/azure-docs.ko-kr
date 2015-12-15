<properties
   pageTitle="마켓플레이스용 데이터 서비스 만들기 가이드 | Microsoft Azure"
   description="Azure 마켓플레이스에서 구매하기 위한 데이터 서비스를 만들고 인증하고 배포하는 방법에 대한 자세한 지침입니다."
   services="marketplace-publishing"
   documentationCenter=""
   authors="HannibalSII"
   manager=""
   editor=""/>

   <tags
      ms.service="marketplace-publishing"
      ms.devlang="na"
      ms.topic="article"
      ms.tgt_pltfrm="na"
      ms.workload="na"
      ms.date="11/30/2015"
      ms.author="hascipio; avikova" />

# CSDL을 통해 기존 웹 서비스를 Odata에 매핑

이 문서에서는 CSDL을 사용하여 기존 서비스를 OData 호환 서비스에 매핑하는 방법에 대한 개요를 설명합니다. 서비스 호출을 통해 클라이언트의 입력 요청을 변환하고 OData 호환 피드를 통해 출력(데이터)을 다시 클라이언트로 변환하는 매핑 문서(CSDL) 작성 방법을 설명합니다. Microsoft의 Azure 마켓플레이스는 OData 프로토콜을 사용하여 최종 사용자에게 서비스를 노출합니다. 콘텐츠 공급자(데이터 소유자)가 노출하는 서비스는 REST, SOAP 등의 다양한 형태로 노출됩니다.

## CSDL이란 무엇이며 어떤 구조로 되어 있나요?
CSDL(개념 스키마 정의 언어)은 Azure 마켓플레이스에서 통용되는 XML 용어로 웹 서비스 또는 데이터베이스 서비스를 설명하는 방법을 정의하는 사양입니다.

**요청 흐름**의 간단한 개요:

  `Client -> Azure Marketplace -> Content Provider’s Web Service (Get, Post, Delete, Put)`

**데이터 흐름**은 반대 방향:

  `Client <- Azure Marketplace <- Content Provider’s WebService`

**그림 1**은 클라이언트가 Azure 마켓플레이스를 통해 콘텐츠 공급자(서비스)로부터 데이터를 가져오는 방식을 보여 줍니다. CSDL은 매핑/변환 구성 요소가 콘텐츠 공급자의 서비스와 요청하는 클라이언트 간에 요청을 처리하고 데이터를 전달하는 데 사용됩니다.

*그림 1: Azure 마켓플레이스를 통해 요청하는 클라이언트에서 콘텐츠 공급자로의 구체적 흐름*

  ![그리기](media/marketplace-publishing-data-service-creation-odata-mapping/figure-1.png)

Azure 마켓플레이스 확장 기능이 빌드되는 Atom, Atom Pub 및 OData 프로토콜에 대한 배경 정보는 [http://msdn.microsoft.com/library/ff478141.aspx](http://msdn.microsoft.com/library/ff478141.aspx)를 참조하세요.

위 링크에서 발췌한 내용: *"개방형 데이터 프로토콜(이하 OData)의 목적은 데이터 서비스로 노출되는 리소스에 대해 CRUD 스타일 작업(만들기, 읽기, 업데이트 및 삭제)을 위한 REST 기반 프로토콜을 제공하는 것입니다. "데이터 서비스"는 하나 이상의 "컬렉션"에서 데이터가 노출되고 각 컬렉션의 "항목"이 0개 이상인 끝점이며, 항목은 형식화된 데이터-값 쌍으로 구성됩니다. 누구든지 로열티나 제한 없이 서버, 클라이언트 또는 도구를 구축할 수 있도록 Microsoft에서 OASIS(Organization for the Advancement of Structured Information Standards) 표준에 따라 OData를 게시합니다."*

### CSDL에서 정의해야 하는 세 가지 중요한 것

- 서비스 공급자의 **끝점** 서비스의 웹 주소(URI)
- 서비스 공급자에 입력으로 전달되는 **데이터 매개 변수** 콘텐츠 공급자의 서비스에서 데이터 유형까지 전달되는 매개 변수 정의
- 요청하는 서비스로 반환되는 **스키마** 컨테이너, 컬렉션/테이블, 변수/열, 데이터 유형을 포함하여 콘텐츠 공급자의 서비스를 통해 전달되는 데이터의 스키마.

다음 다이어그램은 클라이언트가 OData 문(콘텐츠 공급자의 웹 서비스 호출)을 입력하고 결과/데이터를 받는 흐름을 개략적으로 보여 줍니다.

  ![그리기](media/marketplace-publishing-data-service-creation-odata-mapping/figure-2.png)

### 단계:

1. 입력 매개 변수가 XML로 정의되어 있는 서비스 호출을 통해 클라이언트가 Azure 마켓플레이스로 요청을 보냅니다.
2. CSDL을 사용하여 서비스 호출의 유효성을 검사합니다.
	- 그런 다음 형식이 지정된 서비스 호출이 Azure 마켓플레이스를 통해 콘텐츠 공급자 서비스로 전송됩니다.
3. 웹 서비스에서 Http 동사(즉, GET)를 실행 및 수행합니다. 데이터가 Azure 마켓플레이스로 반환되고 요청된 데이터(있는 경우)는 CSDL에 정의된 매핑을 사용하여 클라이언트에 XML 형식으로 노출됩니다.
4. 클라이언트에서 데이터(있는 경우)를 XML 또는 JSON 형식으로 전송합니다.

## 정의

### OData ATOM pub

ATOM pub의 확장 기능으로, 각 항목이 결과 집합의 한 행을 나타냅니다. 항목의 콘텐츠 부분은 키 값 쌍으로 행 값을 포함하도록 향상되었습니다. 자세한 내용은 [https://www.odata.org/documentation/odata-version-3-0/atom-format/](https://www.odata.org/documentation/odata-version-3-0/atom-format/)에서 확인할 수 있습니다.

### CSDL - 개념 스키마 정의 언어

데이터베이스를 통해 노출되는 함수(SPROC) 및 엔터티를 정의할 수 있습니다. 자세한 내용은 [http://msdn.microsoft.com/library/bb399292.aspx](http://msdn.microsoft.com/library/bb399292.aspx)에서 확인할 수 있습니다.

> [AZURE.TIP]문서가 보이지 않으면 **다른 버전** 드롭다운을 클릭하고 다른 버전을 선택하세요.

### EDM - 항목 데이터 모델
- 개요: [http://msdn.microsoft.com/library/vstudio/ee382825(v=vs.100).aspx](OverviewLink)
[OverviewLink]:http://msdn.microsoft.com/library/vstudio/ee382825(v=vs.100).aspx
- 미리 보기: [http://msdn.microsoft.com/library/aa697428(v=vs.80).aspx](PreviewLink)
[PreviewLink]:http://msdn.microsoft.com/library/aa697428(v=vs.80).aspx
- 데이터 유형: [http://msdn.microsoft.com/library/bb399548(v=VS.100).aspx](DataTypesLink)
[DataTypesLink]:http://msdn.microsoft.com/library/bb399548(v=VS.100).aspx

다음은 클라이언트가 OData 문(콘텐츠 공급자의 웹 서비스 호출)을 입력하고 결과/데이터를 받는 순차적 흐름을 자세히 보여 줍니다.

  ![그리기](media/marketplace-publishing-data-service-creation-odata-mapping/figure-3.png)


## CSDL 기본 사항

CSDL(개념 스키마 정의 언어)은 Azure 마켓플레이스에서 통용되는 XML 용어로 웹 서비스 또는 데이터베이스 서비스를 설명하는 방법을 정의하는 사양입니다. CSDL은 **데이터 원본에서 Azure 마켓플레이스로의 데이터 전송을 가능하게 하는** 중요한 부분을 설명합니다. 중요한 부분은 여기에 설명되어 있습니다.

- 공개적으로 제공되는 함수(FunctionImport 노드)를 설명하는 인터페이스 정보
- 모든 메시지 요청(입력) 및 메시지 응답(출력)에 대한 데이터 유형 정보(EntityContainer/EntitySet/EntityType 노드)
- 사용할 전송 프로토콜에 대한 바인딩 정보(헤더 노드)
- 지정된 서비스를 찾기 위한 주소 정보(BaseURI 특성)

간단히 말해서 CSDL은 서비스 요청자와 서비스 공급자 간에 이루어지는 플랫폼 및 언어에 독립적인 계약을 나타냅니다. 클라이언트는 CSDL을 사용하여 웹 서비스/데이터베이스 서비스를 찾아서 공개적으로 제공되는 모든 함수를 호출할 수 있습니다.

### CSDL과 데이터베이스 또는 컬렉션 사이에 관계 설정
**CSDL 사양**

CSDL은 웹 서비스를 설명하는 XML 문법입니다. 사양 자체는 EntitySet, FunctionImport, 네임스페이스 및 EntityType의 4개 주요 요소로 나뉩니다.

이 추상화를 보다 쉽게 이해하기 위해 CSDL과 테이블 사이에 관계를 설정하겠습니다.

기억하세요!

  CSDL은 **서비스 요청자**와 **서비스 공급자** 간에 이루어지는 플랫폼 및 언어에 독립적인 계약을 나타냅니다. **클라이언트**는 CSDL을 사용하여 **웹 서비스/데이터베이스 서비스**를 찾아서 공개적으로 제공되는 모든 **함수**를 호출할 수 있습니다.

데이터 서비스의 경우 데이터베이스, 테이블, 열 및 저장 프로시저의 관점에서 CSDL의 네 부분을 생각해 볼 수 있습니다.

데이터 서비스에 대해 다음과 같이 관련 지을 수 있습니다.

- EntityContainer ~= 데이터베이스
- EntitySet ~= 테이블
- EntityType ~= 열
- FunctionImport ~= 저장된 프로시저

**허용되는 HTTP 동사** - GET – 데이터베이스의 값을 반환(컬렉션 반환) - POST – 데이터베이스로 데이터를 전송하고 선택적으로 데이터베이스의 값을 반환하는 데 사용(컬렉션에 새 항목 생성, id/URI 반환) - DELETE – 데이터베이스에서 데이터 삭제(컬렉션 삭제) - PUT – 데이터베이스에 데이터 업데이트(컬렉션을 대체하거나 새로 생성)

## 메타데이터/매핑 문서

메타데이터/매핑 문서는 Azure 마켓플레이스 시스템에서 웹 서비스를 OData 웹 서비스로 노출할 수 있도록 콘텐츠 공급자의 기존 웹 서비스를 매핑하는 데 사용됩니다. CSDL을 기반으로 하며 Azure 마켓플레이스를 통해 노출되는 웹 서비스 기반의 REST를 수용할 수 있도록 CSDL에 몇 가지 확장 기능을 구현합니다. 확장 기능은 [http://schemas.microsoft.com/dallas/2010/04](http://schemas.microsoft.com/dallas/2010/04) 네임스페이스에서 찾을 수 있습니다.

CSDL의 예는 다음과 같습니다. (아래의 예제 CSDL을 복사하여 XML 편집기에 붙여넣고 해당 서비스와 일치하도록 변경하세요. 그런 다음 [Azure 마켓플레이스 게시 포털](https://publish.windowsazure.com)에서 서비스를 만들 때 DataService 탭 아래에 CSDL 매핑을 붙여넣으세요).

**용어:** CSDL 용어와 [게시 포털](https://publish.windowsazure.com) UI(PPUI) 용어 사이의 관계. - PPUI의 제품 “제목”은 MyWebOffer에 해당 - PPUI의 MyCompany는 [판매자 대시보드](https://sellerdashboard.microsoft.com) UI의 회사 이름에 해당 - API는 웹 서비스 또는 데이터 서비스(PPUI의 플랜)에 해당

**계층:** 회사(콘텐츠 공급자)는 API와 일직선상에 있는 플랜, 즉 서비스가 포함된 제품을 소유합니다.

### 웹 서비스 CSDL 예

웹 응용 프로그램 끝점(예: C# 응용 프로그램)을 노출하는 서비스에 연결

        <?xml version="1.0" encoding="utf-8"?>
        <!-- The namespace attribute below is used by our system to generate C#. You can change “MyCompany.MyOffer” to something that makes sense for you, but change “MyOffer” consistently throughout the document. -->
        <Schema Namespace="MyCompany.MyWebOffer" Alias="MyOffer" xmlns="http://schemas.microsoft.com/ado/2009/08/edm" xmlns:d="http://schemas.microsoft.com/dallas/2010/04" >
        <!-- EntityContainer groups all the web service calls together into a single offering. Every web service call has a FunctionImport definition. -->
          <EntityContainer Name="MyOffer">
        <!-- EntitySet is defined for CSDL compatibility reasons, not required for ReturnType=”Raw”
        @Name is used as reference by FunctionImport @EntitySet. And is used in the customer facing UI as name of the Service.
        @EntityType is used to point at the type definition near the bottom of this file. -->
            <EntitySet Name="MyEntities" EntityType="MyOffer.MyEntityType" />
        <!-- Add a FunctionImport for every service method. Multiple FunctionImports can share a single return type (EntityType). -->
        <!-- ReturnType is either Raw() for a stream or Collection() for an Atom feed. Ex. of Raw: ReturnType=”Raw(text/plain)” -->
        <!—EntitySet is the entityset defined above, and is needed if ReturnType is not Raw -->
        <!-- BaseURI attribute defines the service call, replace & with the encode value (&amp;).
        In the input name value pairs {param} represents passed in value.
        Or the value can be hard coded as with AccountKey. -->
        <!-- AllowedHttpMethods optional (default = “GET”), allows the CSDL to specifically specify the verb of the service, “Get”, “Post”, “Put”, or “Delete”. -->
        <!-- EncodeParameterValues, True encodes the parameter values, false does not. -->
        <!-- BaseURI is translated into an URITemplate which defines how the web service call is exposed to marketplace customers.
        Ex. https://api.datamarket.azure.com/mycompany/MyOfferPlan?name={name}
        BaseURI is XML encoded, the {...} point to the parameters defined below.
        Marketplace will read the parameters from this URITemplate and fill the values into the corresponding parameters of the BaseUri or RequestBody (below) during calls to your service.  
        It is okay for @d:BaseUri to include information only for Marketplace consumption, it will not be exposed to end users. i.e. the hardcoded AccountKey in the above BaseURI does not show up in the client facing URITemplate. -->
            <FunctionImport Name="MyWebServiceMethod"
                            EntitySet="MyEntities"
                            ReturnType="Collection(MyOffer.MyEntityType)"
        d:AllowedHttpMethods="GET"
        d:EncodeParameterValues="true"
        d:BaseUri="http://services.organization.net/MyServicePath?name={name}&amp;AccountKey=22AC643">
        <!-- Definition of the RequestBody is only required for HTTP POST requests and is optional for HTTP GET requests. -->
        <d:RequestBody d:httpMethod="POST">
                <!-- Use {} for placeholders to insert parameters. -->
                <!-- This example uses SOAP formatting, but any POST body can be used. -->
        	<!-- This example shows how to pass userid and password via the header -->
                <![CDATA[<soapenv:Envelope xmlns:soapenv="http://schemas.xmlsoap.org/soap/envelope/" xmlns:MyOffer="http://services.organization.net/MyServicePath">
                  <soapenv:Header/>
                  <soapenv:Body>
                    <MyOffer:ws_MyWebServiceMethod>
                      <myWebServiceMethodRequest>
                        <!--This information is not exposed to end users. -->
                        <UserId>userid</UserId>
                        <Password>password</Password>
                        <!-- {name} is replaced with the value read from @d:UriTemplate above -->
                        <Name>{name}</Name>
                        <!-- Parameters can be used more than once and are not limited to appearing as the value of an element -->
                        <CustomField Name="{name}" />
                        <MyField>Static content</MyField>
                      </myWebServiceMethodRequest>
                    </MyOffer:ws_MyWebServiceMethod>
                  </soapenv:Body>
                </soapenv:Envelope>      
              ]]>
        </d:RequestBody>
        <!-- Title, Rights and Description are optional and used to specify values to insert into the ATOM feed returned to the end user.  You can specify the element to contain a fixed message by providing a value for the element (this is the default value).  @d:Map is an XPath expression that points into the response returned by your service and is optional.  -->
        <d:Title d:Map="/MyResponse/Title">Default title.</d:Title>
        <d:Rights>© My copyright. This is a fixed response. It is okay to also add a d:Map attribute to override this text.</d:Rights>
        <d:Description d:Map="/MyResponse/Description"></d:Description>
        <d:Namespaces>
        <d:Namespace d:Prefix="p"  d:Uri="http://schemas.organization.net/2010/04/myNamespace" />
        <d:Namespace d:Prefix="p2" d:Uri="http://schemas.organization.net/2010/04/MyNamespace2" />
        </d:Namespaces>
        <!-- Parameters of the web service call:
        @Name should match exactly (case sensitive) the {…} placeholders in the @d:BaseUri, @d:UriTemplate, and d:RequestBody, i.e. “name” parameter in above BaseURI.
        @Mode is always "In", compatibility with CSDL
        @Type is the EDM.SimpleType of the parameter, see http://msdn.microsoft.com/library/bb399548(v=VS.100).aspx
        @d:Nullable indicates whether the parameter is required.
        @d:Regex - optional, attribute to describe the string, limiting unwanted input at the entry of the system
        @d:Description - optional, is used by Service Explorer as help information
        @d:SampleValues - optional, is used by Service Explorer as help information. Multiple Sample values are separated by '|', e.g. "804735132|234534224|23409823234"
        @d:Enum - optional for string type. Contains an enumeration of possible values separated by a '|', e.g. d:enum="english|metric|raw". Will be converted in a dropdown list in the Service Explorer.
        -->
        <Parameter name="name" Mode="In" Type="String" d:Nullable="false" d:Regex="^[a-zA-Z]*$" d:Description="A name that cannot contain any spaces or non-alpha non-English characters"
        d:Enum="George|John|Thomas|James"
        d:SampleValues="George"/>
        <Parameter Name=" AccountKey" Mode="In" Type="String" d:Nullable="false" />

        <!-- d:ErrorHandling is an optional element. Use it define standardized errors by evaluating the service response. -->
        <d:ErrorHandling>
        <!-- Any number of d:Condition elements are allowed, they are evaluated in the order listed.
        @d:Match is an Xpath query on the service response, it should return true or false where true indicates an error.
        @d:httpStatusCode is the error code to return if an response matches the error.
        @d:errorMessage is the user friendly message to return when an error occurs.
        -->
        <d:Condition d:Match="/Result/ErrorMessage[text()='Invalid token']" d:HttpStatusCode="403" d:ErrorMessage="User cannot connect to the service." />
        </d:ErrorHandling>
           </FunctionImport>

        	<!-- The EntityContainer defines the output data schema -->
        </EntityContainer>
        <!-- The EntityType @d:Map defines the repeating node (an XPath query) in the response (output data schema). -->
        <!-- If these nodes are outside a namespace, add the prefix in the xpath. -->
        <!--
        @Name - define your user readable name, will become an XML element in the ATOM feed, so comply with the XML element naming restrictions (no spaces or other illegal characters).
        @Type is the EDM.SimpleType of the parameter, see http://msdn.microsoft.com/library/bb399548(v=VS.100).aspx.
        @d:Map uses an Xpath query to point at the location to extract the content from your services response.
        The "." is relative to the repeating node in the EntityType @d:Map Xpath expression.
        -->
            <EntityType Name="MyEntityType" d:Map="/MyResponse/MyEntities">
        <Property Name="ID"	d:IsPrimaryKey="True" Type="Int32"	Nullable="false" d:Map="./Remaining[@Amount]"/>
        <Property Name="Amount"	Type="Double"	Nullable="false" d:Map="./Remaining[@Amount]"/>
        <Property Name="City"	Type="String"	Nullable="false" d:Map="./City"/>
        <Property Name="State"	Type="String"	Nullable="false" d:Map="./State"/>
        <Property Name="Zip"	Type="Int32"	Nullable="false" d:Map="./Zip"/>
        <Property Name="Updated"	Type="DateTime"	Nullable="false" d:Map="./Updated"/>
        <Property Name="AdditionalInfo" Type="String" Nullable="true"
        d:Map="./Info/More[1]"/>
            </EntityType>
        </Schema>

> [AZURE.TIP] [CSDL을 통해 기존 웹 서비스를 Odata에 매핑하는 예](marketplace-publishing-data-service-creation-odata-mapping-examples.md) 문서에서 더 많은 CSDL 웹 서비스 예를 살펴보세요.

###DataService CSDL 예

데이터베이스 테이블 또는 보기를 끝점으로 노출하는 서비스에 연결 아래 예는 데이터베이스 기반 API CSDL의 두 API를 보여 줍니다(테이블 대신 보기 사용 가능).

        <?xml version="1.0"?>
        <!-- The namespace attribute below is used by our system to generate C#. You can change “MyCompany.MyOffer” to something that makes sense for you, but change “MyOffer” consistently throughout the document. -->
        <Schema Namespace="MyCompany.MyDataOffer" Alias="MyOffer" xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/ado/2009/08/edm">
        <!-- EntityContainer groups all the data service calls together into a single offering. Every web service call has a FunctionImport definition. -->
        <EntityContainer Name="MyOfferContainer">
        <!-- EntitySet is defined for CSDL compatibility reasons, not required for ReturnType=”Raw”
        	Think of the EntitySet as a Service
        @Name is used in the customer facing UI as name of the Service.
        @EntityType is used to point at the type definition (returned set of table columns). -->
        <EntitySet Name="CompanyInfoEntitySet" EntityType="MyOffer.CompanyInfo" />
        <EntitySet Name="ProductInfoEntitySet" EntityType="MyOffer.ProductInfo" />
        </EntityContainer>
        <!-- EntityType defines result (output); the table (or view) and columns to be returned by the data service.)
        	Map is the schema.tabel or schema.view
        	dals.TableName is the table Name
        	Name is the name identifier for the EntityType and the Name of the service exposed to the client via the UI.
        	dals:IsExposed determines if the table schema is exposed (generally true).
        	dals:IsView (optional) true if this is based on a view rather than a table
        	dals:TableSchema is the schema name of the table/view
        -->
        <EntityType
        Map="[dbo].[CompanyInfo]"
        dals:TableName="CompanyInfo"
        Name="CompanyInfo"
        dals:IsExposed="true"
        dals:IsView="false"
        dals:TableSchema="dbo"
        xmlns:dals="http://schemas.microsoft.com/dallas/2010/04">
        <!-- Property defines the column properties and the output of the service.
        	dals:ColumnName is the name of the column in the table /view.
        	Type is the emd.SimpleType
        	Nullable determines if NULL is a valid output value
        	dals.CharMaxLenght is the maximum length of the output value
        	Name is the name of the Property and is exposed to the client facing UI
        	dals:IsReturned is the Boolean that determines if the Service exposes this value to the client.
        	IsQueryable is the Boolean that determines if the column can be used in a database query
        	(For data Services: To improve Performance make sure that columns marked ISQueryable=”true” are in an index.)
        	dals:OrdinalPosition is the numerical position x in the table or the View, where x is from 1 to the number of columns in the table.
        	dals:DatabaseDataType is the data type of the column in the database, i.e. SQL data type dals:IsPrimaryKey indicates if the column is the Primary key in the table/view.  (The columns marked ISPrimaryKey are used in the Order by clause when returning data.)
        -->
        <Property dals:ColumnName="data" Type="String" Nullable="true" dals:CharMaxLength="-1" Name="data" dals:IsReturned="true" dals:IsQueryable="false" dals:IsPrimaryKey="false" dals:OrdinalPosition="3" dals:DatabaseDataType="nvarchar" />
        <Property dals:ColumnName="id" Type="Int32" Nullable="false" Name="id" dals:IsReturned="true" dals:IsQueryable="true" dals:IsPrimaryKey="true" dals:OrdinalPosition="1" dals:NumericPrecision="10" dals:DatabaseDataType="int" />
        <Property dals:ColumnName="ticker" Type="String" Nullable="true" dals:CharMaxLength="10" Name="ticker" dals:IsReturned="true" dals:IsQueryable="true" dals:IsPrimaryKey="false" dals:OrdinalPosition="2" dals:DatabaseDataType="nvarchar" />
        </EntityType>
        <EntityType Map="[dbo].[ProductInfo]" dals:TableName="ProductInfo" Name="ProductInfo" dals:IsExposed="true" dals:IsView="false" dals:TableSchema="dbo" xmlns:dals="http://schemas.microsoft.com/dallas/2010/04">
        <Property dals:ColumnName="companyid" Type="Int32" Nullable="true" Name="companyid" dals:IsReturned="true" dals:IsQueryable="true" dals:IsPrimaryKey="false" dals:OrdinalPosition="2" dals:NumericPrecision="10" dals:DatabaseDataType="int" />
        <Property dals:ColumnName="id" Type="Int32" Nullable="false" Name="id" dals:IsReturned="true" dals:IsQueryable="true" dals:IsPrimaryKey="true" dals:OrdinalPosition="1" dals:NumericPrecision="10" dals:DatabaseDataType="int" />
        <Property dals:ColumnName="product" Type="String" Nullable="true" dals:CharMaxLength="50" Name="product" dals:IsReturned="true" dals:IsQueryable="true" dals:IsPrimaryKey="false" dals:OrdinalPosition="3" dals:DatabaseDataType="nvarchar" />
        </EntityType>
        </Schema>

## 참고 항목
- 특정 노드 및 해당 매개 변수를 학습하고 이해하려면 문서 [데이터 서비스 OData 매핑 노드](marketplace-publishing-data-service-creation-odata-mapping-nodes.md)에서 정의 및 설명, 예제, 사용 사례 컨텍스트를 살펴보세요.
- 예제를 검토하고 싶으면 [데이터 서비스 OData 매핑 예제](marketplace-publishing-data-service-creation-odata-mapping-examples.md) 문서를 통해 샘플 코드를 살펴보고 코드 구문 및 컨텍스트를 이해하세요.
- Azure 마켓플레이스에 데이터 서비스를 게시하기 위한 규정된 경로로 반환하려면 문서 [데이터 서비스 게시 가이드](marketplace-publishing-data-service-creation.md)를 읽어 보세요.

<!----HONumber=AcomDC_1203_2015--->