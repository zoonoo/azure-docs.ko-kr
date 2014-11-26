<properties pageTitle="Calling SQL stored procedures with a JavaScript back end" metaKeywords="stored procedures, SQL, mobile devices, Azure" description="explains how to use SQL stored procedures in a mobile services JavaScript backed." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Calling SQL stored procedures with a JavaScript back end" authors="ricksal" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-multiple" ms.devlang="multiple" ms.topic="article" ms.date="01/01/1900" ms.author="ricksal" />

# JavaScript 백 엔드를 사용하여 SQL 저장 프로시저 호출

SQL Server 저장 프로시저를 호출해야 하는 이유는 다음과 같습니다.

-   모바일 서비스에 사용자가 액세스해야 하는 기존 저장 프로시저가 사용됩니다.
-   응용 프로그램 아키텍처에 따라 데이터베이스 액세스를 위한 저장 프로시저 사용이 필요합니다.
-   개인적 선호도

이 항목에서는 다음을 수행하는 방법에 대해 설명합니다.

-   [간단한 저장 프로시저 호출][간단한 저장 프로시저 호출]
-   [매개 변수를 사용한 저장 프로시저 호출][매개 변수를 사용한 저장 프로시저 호출]
-   [추가 정보][추가 정보]

이 문서에서는 JavaScript 백 엔드와 함께 모바일 서비스의 저장 프로시저에 대해 설명합니다.

하지만 Entity Framework를 기준으로 하는 완전히 다른 아키텍처인 .Net 백 엔드를 사용하여 모바일 서비스를 만들 수 있습니다. 자세한 내용은 [Azure 모바일 서비스 .Net 백 엔드 작동 방법][Azure 모바일 서비스 .Net 백 엔드 작동 방법]을 참조하세요.

## 간단한 저장 프로시저 호출

**"내 데이터베이스에 저장 프로시저가 사용됩니다. 모바일 서비스에서 이를 호출하려면 어떻게 하나요?"**

이렇게 하려면 프로시저를 호출하는 *Transact-SQL*("T-SQL") 문을 실행할 수 있게 해주는 [mssql 개체][mssql 개체]를 사용합니다.

### Transact SQL 코드 작성 및 테스트

저장 프로시저 이름이 *GetAll*이고 모바일 서비스 이름이 **todolist**라고 가정합니다.

1.  Azure 포털 사이드 바에서 **Databases** 아이콘을 선택한 후 모바일 서비스 이름과 접미사 "**\_db**"가 붙은 사용자의 데이터베이스를 선택합니다.

2.  데이터베이스 빠른 시작 페이지에서 페이지 맨 아래에 있는 **관리** 링크를 누릅니다.

3.  데이터베이스에 로그인합니다.

4.  위쪽 막대에서 **새 쿼리**를 누르고 다음 코드를 추가합니다.

            EXEC todolist.GetAll

5.  **실행**을 클릭하고 결과를 확인합니다.

프로시저 이름의 접두사로 데이터베이스 스키마 이름이 어떻게 지정되는지 확인하세요. 이 이름은 기본적으로 모바일 서비스의 이름입니다.

### mssql 개체에 호출을 배치하는 위치

저장 프로시저를 직접 호출해야 할 경우 가장 유연한 방법은 [사용자 지정 API][사용자 지정 API]를 작성하고 호출하는 방법입니다.

1.  모바일 서비스 대시보드에서 **API**를 클릭한 후 **CREATE**를 클릭하고, 스크립트 이름을 ***getall***로 지정하고, 저장 프로시저 호출을 위해 이 코드를 추가합니다.

        exports.get = function(request, response) {
            var mssql = request.service.mssql;
            var sql = "EXEC todolist.GetAll";
            mssql.query(sql, {
                success: function(results) {                          
                        response.send(200, results); 
                }, error: function(err) {
                        response.send(400, { error: err });        
                }
            })
        };

2.  이 URL을 주소 표시줄에 입력하여 브라우저에서 테스트합니다.

        https://todolist.azure-mobile.net/api/getall

대신 테이블의 표준 *read*, *insert*, *update* 또는 *delete* 서버 스크립트 내에 비슷한 코드를 넣을 수도 있습니다. 그러면 해당 코드가 기본 동작을 우회하고 저장 프로시저를 호출합니다. 비슷한 코드는 *Scheduler* 스크립트 안으로 이동될 수 있습니다. 하지만 사용자 지정 API를 만드는 방법이 가장 유연합니다.

### 클라이언트에서 코드 호출

**MobileServiceClient** 개체에서 **invokeApi** 메서드를 호출하도록 클라이언트 코드를 수정합니다. 추가 코드 구문은 클라이언트 장치에 따라 달라지며, 다음 항목들에서 설명합니다.

-   [Windows 스토어 C#][Windows 스토어 C#]
-   [Windows 스토어 JavaScript][Windows 스토어 JavaScript]
-   [Windows Phone][Windows Phone]
-   [iOS][iOS]
-   [Android][Android]
-   [HTML][HTML]

## <a name="parameters"></a>매개 변수를 사용한 저장 프로시저 호출

저장 프로시저 이름이 *ItemsByStatus*고, *Status*라는 단일 매개 변수가 있고, 이를 사용자 지정 API에서 호출하려고 한다고 가정합니다.

1.  이전 섹션의 단계를 수행해서 Azure 데이터베이스 포털을 사용하여 T-SQL 코드를 작성 및 테스트하지만 대신 다음 T-SQL 코드를 사용합니다.

        EXEc todolist.ItemsByStatus @Status = 1

2.  **실행** 기호를 클릭하고 결과를 확인합니다.

3.  이전 단계에서와 같이 저장 프로시저를 호출하는 다음 코드를 사용하여 **completeall**이라는 새로운 사용자 지정 API를 만듭니다. 아래 코드에서는 테스트에 사용한 **@Status** 매개 변수의 실제 값을 제공된 매개 변수에서 런타임에 채워지는 **"?"**로 바꿉니다.

        exports.get = function(request, response) { 
            var mssql = request.service.mssql;
            var param1 = parseInt(request.query.status);
            var sql = "EXEC todolist.ItemsByStatus @Status = ?";
            mssql.query(sql, [param1], {
                success: function(results) { 
                         response.send(200, results[0]); 
                    }, error: function(err) {
                        response.send(400, { error: err });        
                }
            })
        };

4.  브라우저에서 다음 URL을 사용하여 API를 테스트합니다.

        https://todolist.azure-mobile.net/api/completeall?status=1

5.  이전 섹션에서 설명한 것처럼 클라이언트에서 API를 호출합니다.

### 보다 복잡한 매개 변수 서명

이전 섹션의 저장 프로시저에는 단일 매개 변수만 포함되었습니다. 다음은 더 긴 서명에 사용하기 위한 구문입니다.

        function read(query, user, request) {
            var sql = 'EXEC MySProc @Param1 = ?, @Param2 = ?, @Param3 = ?';
            var param1 = request.parameters.first;
            var param2 = request.parameters.second;
            var param3 = request.parameters.third;
            mssql.query(sql, [param1, param2, param3], {
                success: function(results) {
                    request.respond(200, results);
                }
            });
        }

**EXEC** 문에서 매개 변수는 위치가 아닌 이름으로 호출됩니다. 이 구문은 권장 구문이며, 매개 변수 순서 변경이나 선택적인 매개 변수 추가 등과 같이 저장 프로시저에서 발생하는 사소한 변경 사항으로부터 코드를 보호합니다.

## <a name="more"></a>추가 정보

이 항목에서는 간략한 내용만 다룹니다.

사용되는 코드에 대한 자세한 참조 정보는 다음 항목을 참조하세요.

-   [mssql 개체]: 모바일 서비스에서 저장 프로시저 호출에 사용되는 중앙 개체에 대한 참조 정보
-   [JavaScript 백 엔드 모바일 서비스 작업]: mssql 개체에 대한 자료 등 JavaScript 백 엔드에 대한 보다 일반적인 정보


발생 가능한 다른 시나리오는 다음과 같습니다.

-   [스크립트를 사용하여 읽기 작업을 2번 이상 수행하는 방법][스크립트를 사용하여 읽기 작업을 2번 이상 수행하는 방법]에서는 테이블을 조건부로 읽는 방법 또는 저장 프로시저를 호출하는 방법에 대해 설명합니다. 이 블로거 Carlos Figueira는 Azure 모바일 서비스 및 데이터베이스에 대한 기사를 자주 게시합니다.

-   [다른 스키마에서 저장 프로시저에 액세스][다른 스키마에서 저장 프로시저에 액세스]에서는 동일한 모바일 서비스 데이터베이스의 여러 다른 스키마에 상주하는 저장 프로시저에 액세스할 때 발생할 수 있는 문제에 대한 솔루션을 설명합니다.

또한 Azure 포털을 사용하여 새로운 저장 프로시저를 관리 및 생성할 수 있습니다.




  [간단한 저장 프로시저 호출]: #simple
  [매개 변수를 사용한 저장 프로시저 호출]: #parameters
  [추가 정보]: #more
  [Azure 모바일 서비스 .Net 백 엔드 작동 방법]: http://curah.microsoft.com/64518/how-the-azure-mobile-services-net-backend-works
  [mssql 개체]: http://msdn.microsoft.com/ko--kr/library/windowsazure/jj554212.aspx
  [사용자 지정 API]: http://msdn.microsoft.com/ko--kr/library/windowsazure/dn280974.aspx
  [Windows 스토어 C#]: http://azure.microsoft.com/ko--kr/documentation/articles/mobile-services-windows-store-dotnet-call-custom-api/
  [Windows 스토어 JavaScript]: http://azure.microsoft.com/ko--kr/documentation/articles/mobile-services-windows-store-javascript-call-custom-api/
  [Windows Phone]: http://azure.microsoft.com/ko--kr/documentation/articles/mobile-services-windows-phone-call-custom-api/
  [iOS]: http://azure.microsoft.com/ko--kr/documentation/articles/mobile-services-ios-call-custom-api/
  [Android]: http://azure.microsoft.com/ko--kr/documentation/articles/mobile-services-android-call-custom-api/
  [HTML]: http://azure.microsoft.com/ko--kr/documentation/articles/mobile-services-html-call-custom-api/
  [스크립트를 사용하여 읽기 작업을 2번 이상 수행하는 방법]: http://social.msdn.microsoft.com/Forums/windowsazure/ko-kr/fccf4ae7-f43c-4c2d-8518-32e2df84a824/how-do-i-do-more-than-1-read-operation-by-scripting?forum=azuremobile
  [다른 스키마에서 저장 프로시저에 액세스]: http://blogs.msdn.com/b/jpsanders/archive/2013/05/02/windows-azure-mobile-services-accessing-a-stored-procedure-from-a-different-schema.aspx
  [mssql 개체]: http://msdn.microsoft.com/ko--kr/library/windowsazure/jj554212.aspx