---
title: "DocumentDB용 NoSQL C++ 자습서 | Microsoft Docs"
description: "C++용 DocumentDB 인증 SDK를 사용하여 C++ 데이터베이스 및 콘솔 응용 프로그램을 만드는 NoSQL C++ 자습서입니다. DocumentDB는 전 세계적인 규모의 NoSQL 데이터베이스 서비스입니다."
services: documentdb
documentationcenter: cpp
author: asthana86
manager: jhubbard
editor: 
ms.assetid: b8756b60-8d41-4231-ba4f-6cfcfe3b4bab
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: cpp
ms.topic: hero-article
ms.date: 12/25/2016
ms.author: aasthan
translationtype: Human Translation
ms.sourcegitcommit: 72b2d9142479f9ba0380c5bd2dd82734e370dee7
ms.openlocfilehash: 78c3da6fd83a6fca0351a90846d10acd82924be3
ms.lasthandoff: 03/08/2017


---
# <a name="nosql-c-tutorial-documentdb-c-console-application"></a>NoSQL C++ 자습서: DocumentDB C++ 콘솔 응용 프로그램
> [!div class="op_single_selector"]
> * [.NET](documentdb-get-started.md)
> * [.NET Core](documentdb-dotnetcore-get-started.md)
> * [MongoDB용 Node.js](documentdb-mongodb-samples.md)
> * [Node.JS](documentdb-nodejs-get-started.md)
> * [Java](documentdb-java-get-started.md)
> * [C++](documentdb-cpp-get-started.md)
>  
> 
 

C++용 Azure DocumentDB 인증 SDK에 대한 C++ 자습서를 시작합니다. 이 자습서를 따라 하면 C++ 데이터베이스를 포함하여 DocumentDB 리소스를 만들고 쿼리하는 콘솔 응용 프로그램이 만들어집니다.

다음에 대해 설명합니다.

* DocumentDB 계정 만들기 및 연결
* 응용 프로그램 설정
* C++ DocumentDB 데이터베이스 만들기
* 컬렉션 만들기
* JSON 문서 만들기
* 컬렉션 쿼리
* 문서 바꾸기
* 문서 삭제
* C++ DocumentDB 데이터베이스 삭제

시간이 없으십니까? 염려하지 마십시오. [GitHub](https://github.com/stalker314314/DocumentDBCpp)에서 전체 솔루션을 사용할 수 있습니다. 빠른 지침은 [전체 솔루션 다운로드](#GetSolution) 를 참조하세요.

C++ 자습서를 완료한 후에 이 페이지의 아래쪽에 있는 응답 단추를 통해 의견을 보내주세요. 

직접 연락을 받고 싶은 경우 코멘트에 메일 주소를 적거나 [여기에 알려주세요](https://www.research.net/r/8BKRJ3Z). 

이제 시작하겠습니다.

## <a name="prerequisites-for-the-c-tutorial"></a>C++ 자습서의 필수 구성 요소
다음 항목이 있는지 확인합니다.

* 활성 Azure 계정. 아직 구독하지 않은 경우 [Azure 무료 평가판](https://azure.microsoft.com/pricing/free-trial/)에 등록할 수 있습니다.
* C++ 언어 구성 요소가 설치된 [Visual Studio](https://www.visualstudio.com/downloads/)입니다.

## <a name="step-1-create-a-documentdb-account"></a>1단계: DocumentDB 계정 만들기
DocumentDB 계정을 만들어 보겠습니다. 계정이 이미 있는 경우 [C++ 응용 프로그램 설치](#SetupNode)로 건너뛸 수 있습니다.

[!INCLUDE [documentdb-create-dbaccount](../../includes/documentdb-create-dbaccount.md)]

## <a id="SetupC++"></a>2단계: C++ 응용 프로그램 설정
1. Visual Studio를 열고 **파일** 메뉴에서 **새로 만들기**를 클릭한 다음 **프로젝트**를 클릭합니다. 
2. **새 프로젝트** 창의 **설치됨** 창에서 **Visual C++**를 확장하고 **Win32**, **Win32 콘솔 응용 프로그램**을 차례로 클릭합니다. 프로젝트 이름을 hellodocumentdb로 지정한 다음 **확인**을 클릭합니다. 
   
    ![새 프로젝트 마법사의 스크린샷](media/documentdb-cpp-get-started/hellodocumentdb.png)
3. Win32 응용 프로그램 마법사가 시작되면 **마침**을 클릭합니다.
4. 프로젝트를 만들면 **솔루션 탐색기**에서 **hellodocumentdb** 프로젝트를 마우스 오른쪽 단추로 클릭하고 **NuGet 패키지 관리**를 클릭하여 NuGet 패키지 관리자를 엽니다. 
   
    ![프로젝트 메뉴에서 NuGet 패키지 관리를 보여 주는 스크린샷](media/documentdb-cpp-get-started/nuget.png)
5. **NuGet: hellodocumentdb** 탭에서 **찾아보기**를 클릭한 다음 *documentdbcpp*를 검색합니다. 결과에서 다음 스크린샷에 표시된 것처럼 DocumentDbCPP를 선택합니다. 이 패키지에서는 DocumentDbCPP에 대한 종속성이 있는 C++ REST SDK에 대한 참조를 설치합니다.  
   
    ![DocumentDbCpp 패키지를 강조 표시하는 스크린샷](media/documentdb-cpp-get-started/documentdbcpp.png)
   
    패키지가 프로젝트에 추가되면 코드 작성을 시작하는 설정이 완료됩니다.   

## <a id="Config"></a>3단계: DocumentDB 데이터베이스에 대한 Azure Portal의 연결 세부 정보 복사
[Azure Portal](https://portal.azure.com)을 불러와서, 만든 NoSQL (DocumentDB) 데이터베이스 계정에 트래버스합니다. 다음 단계에서는 C++ 코드 조각에서 연결을 설정하기 위해 Azure Portal의 URI 및 기본 키가 필요합니다. 

![Azure Portal에서 DocumentDB URI 및 키](media/documentdb-cpp-get-started/nosql-tutorial-keys.png)

## <a id="Connect"></a>4단계: DocumentDB 계정에 연결
1. `#include "stdafx.h"` 뒤의 소스 코드에 다음 헤더 및 네임스페이스를 추가합니다.
   
        #include <cpprest/json.h>
        #include <documentdbcpp\DocumentClient.h>
        #include <documentdbcpp\exceptions.h>
        #include <documentdbcpp\TriggerOperation.h>
        #include <documentdbcpp\TriggerType.h>
        using namespace documentdb;
        using namespace std;
        using namespace web::json;
2. 그런 다음 main 함수에 다음 코드를 추가하고 계정 구성 및 기본 키를 바꾸어 3단계의 DocumentDB 설정과 일치시킵니다. 
   
        DocumentDBConfiguration conf (L"<account_configuration_uri>", L"<primary_key>");
        DocumentClient client (conf);
   
    documentdb 계정을 시작하는 코드가 있다면 DocumentDB 리소스와 함께 작동하는지 살펴보겠습니다.

## <a id="CreateDBColl"></a>5단계: C++ 데이터베이스 및 컬렉션 만들기
이 단계를 수행하기 전에 DocumentDB를 처음 접하는 경우 데이터베이스, 컬렉션 및 문서가 상호 작용하는 방법을 살펴보겠습니다. [데이터베이스](documentdb-resources.md#databases)는 여러 컬렉션으로 분할된 문서 저장소의 논리적 컨테이너입니다. [컬렉션](documentdb-resources.md#collections)은 JSON 문서 및 관련 JavaScript 응용 프로그램 논리의 컨테이너입니다. [DocumentDB 계층적 리소스 모델 및 개념](documentdb-resources.md)에서 DocumentDB 계층적 리소스 모델 및 개념에 대해 자세히 알아볼 수 있습니다.

데이터베이스와 해당 컬렉션을 만들려면 main 함수 끝에 다음 코드를 추가합니다. 이전 단계에서 선언한 클라이언트 구성을 사용하여 'FamilyRegistry'라는 데이터베이스 및 'FamilyCollection'라는 컬렉션을 만듭니다.

    try {
      shared_ptr<Database> db = client.CreateDatabase(L"FamilyRegistry");
      shared_ptr<Collection> coll = db->CreateCollection(L"FamilyCollection");
    } catch (DocumentDBRuntimeException ex) {
      wcout << ex.message();
    }


## <a id="CreateDoc"></a>6단계: 문서 만들기
[문서](documentdb-resources.md#documents)는 사용자 정의(임의) JSON 콘텐츠입니다. 이제 DocumentDB 문서를 삽입할 수 있습니다. main 함수의 끝에 다음 코드를 복사하여 문서를 만들 수 있습니다. 

    try {
      value document_family;
      document_family[L"id"] = value::string(L"AndersenFamily");
      document_family[L"FirstName"] = value::string(L"Thomas");
      document_family[L"LastName"] = value::string(L"Andersen");
      shared_ptr<Document> doc = coll->CreateDocumentAsync(document_family).get();

      document_family[L"id"] = value::string(L"WakefieldFamily");
      document_family[L"FirstName"] = value::string(L"Lucy");
      document_family[L"LastName"] = value::string(L"Wakefield");
      doc = coll->CreateDocumentAsync(document_family).get();
    } catch (ResourceAlreadyExistsException ex) {
      wcout << ex.message();
    }

요약하자면 이 코드는 Azure Portal의 문서 탐색기에서 쿼리할 수 있는 DocumentDB 데이터베이스, 컬렉션 및 문서를 만듭니다. 

![C++ 자습서 - 계정, 데이터베이스, 컬렉션 및 문서 간의 계층 관계를 보여 주는 다이어그램](media/documentdb-cpp-get-started/documentdbdocs.png)

## <a id="QueryDB"></a>7단계: DocumentDB 리소스 쿼리
DocumentDB는 각 컬렉션에 저장된 JSON 문서에 대해 [다양한 쿼리](documentdb-sql-query.md) 를 지원합니다. 다음 샘플 코드는 DocumentDB SQL 구문을 사용해서 만든 다양한 쿼리를 보여 줍니다. 이러한 쿼리는 이전 단계에서 만든 문서에 대해 실행할 수 있습니다.

함수는 문서 클라이언트와 함께 데이터베이스 및 컬렉션에 대한 고유한 식별자 또는 리소스 ID를 인수로 사용합니다. main 함수 앞에 이 코드를 추가합니다.

    void executesimplequery(const DocumentClient &client,
                            const wstring dbresourceid,
                            const wstring collresourceid) {
      try {
        client.GetDatabase(dbresourceid).get();
        shared_ptr<Database> db = client.GetDatabase(dbresourceid);
        shared_ptr<Collection> coll = db->GetCollection(collresourceid);
        wstring coll_name = coll->id();
        shared_ptr<DocumentIterator> iter =
            coll->QueryDocumentsAsync(wstring(L"SELECT * FROM " + coll_name)).get();
        wcout << "\n\nQuerying collection:";
        while (iter->HasMore()) {
          shared_ptr<Document> doc = iter->Next();
          wstring doc_name = doc->id();
          wcout << "\n\t" << doc_name << "\n";
          wcout << "\t"
                << "[{\"FirstName\":"
                << doc->payload().at(U("FirstName")).as_string()
                << ",\"LastName\":" << doc->payload().at(U("LastName")).as_string()
                << "}]";
        }
      } catch (DocumentDBRuntimeException ex) {
        wcout << ex.message();
      }
    }

## <a id="Replace"></a>8단계: 문서 바꾸기
DocumentDB는 다음 코드에서와 같이 JSON 문서를 바꾸도록 지원합니다. executesimplequery 함수 뒤에 이 코드를 추가합니다.

    void replacedocument(const DocumentClient &client, const wstring dbresourceid,
                         const wstring collresourceid,
                         const wstring docresourceid) {
      try {
        client.GetDatabase(dbresourceid).get();
        shared_ptr<Database> db = client.GetDatabase(dbresourceid);
        shared_ptr<Collection> coll = db->GetCollection(collresourceid);
        value newdoc;
        newdoc[L"id"] = value::string(L"WakefieldFamily");
        newdoc[L"FirstName"] = value::string(L"Lucy");
        newdoc[L"LastName"] = value::string(L"Smith Wakefield");
        coll->ReplaceDocument(docresourceid, newdoc);
      } catch (DocumentDBRuntimeException ex) {
        throw;
      }
    }

## <a id="Delete"></a>9단계: 문서 삭제
DocumentDB는 JSON 문서를 삭제하도록 지원합니다. replacedocument 함수 뒤에 다음 코드를 복사하고 붙여 넣는 방법으로 수행할 수 있습니다. 

    void deletedocument(const DocumentClient &client, const wstring dbresourceid,
                        const wstring collresourceid, const wstring docresourceid) {
      try {
        client.GetDatabase(dbresourceid).get();
        shared_ptr<Database> db = client.GetDatabase(dbresourceid);
        shared_ptr<Collection> coll = db->GetCollection(collresourceid);
        coll->DeleteDocumentAsync(docresourceid).get();
      } catch (DocumentDBRuntimeException ex) {
        wcout << ex.message();
      }
    }

## <a id="DeleteDB"></a>10단계: 데이터베이스 삭제
만든 데이터베이스를 삭제하면 데이터베이스와 모든 자식 리소스(컬렉션, 문서 등)가 제거됩니다.

deletedocument 함수 뒤에 다음 코드 조각(함수 정리)을 복사하고 붙여넣어서 데이터베이스와 모든 하위 리소스를 제거합니다.

    void deletedb(const DocumentClient &client, const wstring dbresourceid) {
      try {
        client.DeleteDatabase(dbresourceid);
      } catch (DocumentDBRuntimeException ex) {
        wcout << ex.message();
      }
    }

## <a id="Run"></a>11단계: C# 응용 프로그램 모두 함께 실행
이제 다른 DocumentDB 리소스를 만들기, 쿼리, 수정 및 삭제하는 코드를 추가했습니다.  이제 몇몇 진단 메시지와 함께 hellodocumentdb.cpp에서 main 함수의 이러한 다양한 기능에 대한 호출을 추가하여 마무리합니다.

다음 코드로 응용 프로그램의 main 함수를 대체하여 수행할 수 있습니다. 3단계에서 코드에 복사한 account_configuration_uri 및 primary_key를 덮어 쓰기 때문에 해당 줄을 저장하거나 포털에서의 값을 다시 복사합니다. 

    int main() {
        try {
            // Start by defining your account's configuration
            DocumentDBConfiguration conf (L"<account_configuration_uri>", L"<primary_key>");
            // Create your client
            DocumentClient client(conf);
            // Create a new database
            try {
                shared_ptr<Database> db = client.CreateDatabase(L"FamilyDB");
                wcout << "\nCreating database:\n" << db->id();
                // Create a collection inside database
                shared_ptr<Collection> coll = db->CreateCollection(L"FamilyColl");
                wcout << "\n\nCreating collection:\n" << coll->id();
                value document_family;
                document_family[L"id"] = value::string(L"AndersenFamily");
                document_family[L"FirstName"] = value::string(L"Thomas");
                document_family[L"LastName"] = value::string(L"Andersen");
                shared_ptr<Document> doc =
                    coll->CreateDocumentAsync(document_family).get();
                wcout << "\n\nCreating document:\n" << doc->id();
                document_family[L"id"] = value::string(L"WakefieldFamily");
                document_family[L"FirstName"] = value::string(L"Lucy");
                document_family[L"LastName"] = value::string(L"Wakefield");
                doc = coll->CreateDocumentAsync(document_family).get();
                wcout << "\n\nCreating document:\n" << doc->id();
                executesimplequery(client, db->resource_id(), coll->resource_id());
                replacedocument(client, db->resource_id(), coll->resource_id(),
                    doc->resource_id());
                wcout << "\n\nReplaced document:\n" << doc->id();
                executesimplequery(client, db->resource_id(), coll->resource_id());
                deletedocument(client, db->resource_id(), coll->resource_id(),
                    doc->resource_id());
                wcout << "\n\nDeleted document:\n" << doc->id();
                deletedb(client, db->resource_id());
                wcout << "\n\nDeleted db:\n" << db->id();
                cin.get();
            }
            catch (ResourceAlreadyExistsException ex) {
                wcout << ex.message();
            }
        }
        catch (DocumentDBRuntimeException ex) {
            wcout << ex.message();
        }
        cin.get();
    }

이제 Visual Studio에서 F5 키를 누르거나 터미널 창에서 응용 프로그램을 찾고 실행 파일을 실행하여 코드를 빌드하고 실행할 수 있게 됩니다. 

시작한 앱의 출력이 표시됩니다. 축력은 다음 스크린샷과 일치해야 합니다.

![DocumentDB C++ 응용 프로그램 출력](media/documentdb-cpp-get-started/docdbconsole.png)

축하합니다. C++ 자습서를 완료했으며 첫 번째 DocumentDB 콘솔 응용 프로그램이 있습니다.

## <a id="GetSolution"></a> 전체 C++ 자습서 솔루션 가져오기
이 문서의 모든 샘플을 포함하는 GetStarted 솔루션을 빌드하려면 다음이 필요합니다.

* [DocumentDB 계정][documentdb-create-account].
* GitHub에서 제공하는 [GetStarted](https://github.com/stalker314314/DocumentDBCpp) 솔루션

## <a name="next-steps"></a>다음 단계
* [DocumentDB 계정 모니터링](documentdb-monitor-accounts.md)방법에 대해 자세히 알아봅니다.
* [쿼리 실습](https://www.documentdb.com/sql/demo)의 샘플 데이터 집합에 대해 쿼리를 실행합니다.
* [DocumentDB 설명서](https://azure.microsoft.com/documentation/services/documentdb/)페이지의 개발 섹션에서 프로그래밍 모델에 대해 자세히 알아봅니다.

[documentdb-create-account]: documentdb-create-account.md



