---
title: "자습서: Entity Framework 및 행 수준 보안을 사용하여 다중 테넌트 데이트베이스를 포함하는 웹 앱"
description: "Entity Framework 및 행 수준 보안을 사용하여 다중 테넌트 SQL 데이터베이스 백 엔드를 포함하는 ASP.NET MVC 5 웹 앱을 개발하는 방법에 대해 알아봅니다."
metakeywords: azure asp.net mvc entity framework multi tenant row level security rls sql database
services: app-service\web
documentationcenter: .net
manager: jeffreyg
author: tmullaney
ms.assetid: 8fdc47a5-6fc3-4d29-ab6a-33e79f50699f
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 04/25/2016
ms.author: thmullan
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: ba1bb3d84b462dfebbb2564569517d7336bf54fd


---
# <a name="tutorial-web-app-with-a-multi-tenant-database-using-entity-framework-and-row-level-security"></a>자습서: Entity Framework 및 행 수준 보안을 사용하여 다중 테넌트 데이트베이스를 포함하는 웹 앱
이 자습서는 Entity Framework 및 [행 수준 보안](https://msdn.microsoft.com/library/dn765131.aspx)을 사용하여 "[공유 데이터베이스, 공유 스키마](https://msdn.microsoft.com/library/aa479086.aspx)" 테넌트 모델로 다중 테넌트 웹 앱을 빌드하는 방법을 보여줍니다. 이 모델에서 단일 데이터베이스는 많은 테넌트의 데이터를 포함하고 각 테이블의 각 행은 "Tenant ID"와 연결되어 있습니다. 행 수준 보안(RLS)이라는 Azure SQL 데이터베이스의 새로운 기능은 테넌트가 상대편의 데이터에 액세스하지 못하도록 막기 위해서 사용됩니다. 이를 위해 응용 프로그램에서 한 가지만 조금 변경하면 됩니다. RLS는 데이터베이스 자체 내의 테넌트 액세스 논리를 중앙 집중화하여, 응용 프로그램 코드를 간소화하고 테넌트 간의 실수로 인한 데이터 누출 위험을 줄였습니다.

[인증된 ASP.NET MVP 앱과 SQL DB를 만들고 Azure 앱 서비스에 배포](web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database.md)를 통해 간단한 연락처 관리자 응용 프로그램부터 시작해 보겠습니다. 현재 응용 프로그램은 모든 사용자(테넌트)가 모든 연락처를 보도록 허용합니다.

![RLS를 활성화하기 전의 연락처 관리자 응용 프로그램](./media/web-sites-dotnet-entity-framework-row-level-security/ContactManagerApp-Before.png)

몇 가지만 조금 변경하여 사용자들이 자신에게 속한 연락처만 볼 수 있도록 다중 테넌트에 대한 지원을 추가하겠습니다.

## <a name="step-1-add-an-interceptor-class-in-the-application-to-set-the-sessioncontext"></a>1 단계: SESSION_CONTEXT를 설정하기 위해 응용 프로그램에 인터셉터 클래스를 추가합니다.
응용 프로그램에서 변경해야 하는 항목은 한 가지입니다. 모든 응용 프로그램 사용자가 동일한 연결 문자열(예: 동일한 SQL 로그인)을 사용하여 데이터베이스에 연결하기 때문에 RLS 정책에서 어떤 사용자를 필터링해야 할지를 알 수 있는 방법이 현재로서는 없습니다. 이러한 접근 방법은 효율적인 연결 풀링을 가능하게 하기 때문에 웹 응용 프로그램에서는 매우 일반적이지만, 데이터베이스 내에서 현재 응용 프로그램 사용자를 식별하기 위해 다른 방법이 필요하다는 것을 의미합니다. 해결책은 연결된 직후 쿼리를 실행하기 전에 응용 프로그램에서 [SESSION_CONTEXT](https://msdn.microsoft.com/library/mt590806)의 현재 UserId에 대해 키-값 쌍을 설정하도록 하는 것입니다. SESSION_CONTEXT는 세션 범위 키-쌍 저장소이며, RLS 정책은 여기에 저장된 UserId를 사용하여 현재 사용자를 식별하게 됩니다.

EF가 연결을 시작할 때마다 T-SQL 문을 실행하여 SESSION_CONTEXT에 자동으로 현재 UserId를 설정하기 위해, Entity Framework(EF) 6의 새 기능인 [인터셉터](https://msdn.microsoft.com/data/dn469464.aspx)(특히 [DbConnectionInterceptor](https://msdn.microsoft.com/library/system.data.entity.infrastructure.interception.idbconnectioninterceptor))를 추가해 보겠습니다.

1. Visual Studio에서 ContactManager 프로젝트를 엽니다.
2. 솔루션 탐색기에서 Models 폴더를 마우스 오른쪽 단추로 클릭하고 추가 > 클래스를 선택합니다.
3. 새 클래스 이름을 "SessionContextInterceptor.cs"로 설정하고 추가를 클릭합니다.
4. SessionContextInterceptor.cs 내용을 다음 코드로 바꿉니다.

```
using System;
using System.Collections.Generic;
using System.Linq;
using System.Web;
using System.Data.Common;
using System.Data.Entity;
using System.Data.Entity.Infrastructure.Interception;
using Microsoft.AspNet.Identity;

namespace ContactManager.Models
{
    public class SessionContextInterceptor : IDbConnectionInterceptor
    {
        public void Opened(DbConnection connection, DbConnectionInterceptionContext interceptionContext)
        {
            // Set SESSION_CONTEXT to current UserId whenever EF opens a connection
            try
            {
                var userId = System.Web.HttpContext.Current.User.Identity.GetUserId();
                if (userId != null)
                {
                    DbCommand cmd = connection.CreateCommand();
                    cmd.CommandText = "EXEC sp_set_session_context @key=N'UserId', @value=@UserId";
                    DbParameter param = cmd.CreateParameter();
                    param.ParameterName = "@UserId";
                    param.Value = userId;
                    cmd.Parameters.Add(param);
                    cmd.ExecuteNonQuery();
                }
            }
            catch (System.NullReferenceException)
            {
                // If no user is logged in, leave SESSION_CONTEXT null (all rows will be filtered)
            }
        }

        public void Opening(DbConnection connection, DbConnectionInterceptionContext interceptionContext)
        {
        }

        public void BeganTransaction(DbConnection connection, BeginTransactionInterceptionContext interceptionContext)
        {
        }

        public void BeginningTransaction(DbConnection connection, BeginTransactionInterceptionContext interceptionContext)
        {
        }

        public void Closed(DbConnection connection, DbConnectionInterceptionContext interceptionContext)
        {
        }

        public void Closing(DbConnection connection, DbConnectionInterceptionContext interceptionContext)
        {
        }

        public void ConnectionStringGetting(DbConnection connection, DbConnectionInterceptionContext<string> interceptionContext)
        {
        }

        public void ConnectionStringGot(DbConnection connection, DbConnectionInterceptionContext<string> interceptionContext)
        {
        }

        public void ConnectionStringSet(DbConnection connection, DbConnectionPropertyInterceptionContext<string> interceptionContext)
        {
        }

        public void ConnectionStringSetting(DbConnection connection, DbConnectionPropertyInterceptionContext<string> interceptionContext)
        {
        }

        public void ConnectionTimeoutGetting(DbConnection connection, DbConnectionInterceptionContext<int> interceptionContext)
        {
        }

        public void ConnectionTimeoutGot(DbConnection connection, DbConnectionInterceptionContext<int> interceptionContext)
        {
        }

        public void DataSourceGetting(DbConnection connection, DbConnectionInterceptionContext<string> interceptionContext)
        {
        }

        public void DataSourceGot(DbConnection connection, DbConnectionInterceptionContext<string> interceptionContext)
        {
        }

        public void DatabaseGetting(DbConnection connection, DbConnectionInterceptionContext<string> interceptionContext)
        {
        }

        public void DatabaseGot(DbConnection connection, DbConnectionInterceptionContext<string> interceptionContext)
        {
        }

        public void Disposed(DbConnection connection, DbConnectionInterceptionContext interceptionContext)
        {
        }

        public void Disposing(DbConnection connection, DbConnectionInterceptionContext interceptionContext)
        {
        }

        public void EnlistedTransaction(DbConnection connection, EnlistTransactionInterceptionContext interceptionContext)
        {
        }

        public void EnlistingTransaction(DbConnection connection, EnlistTransactionInterceptionContext interceptionContext)
        {
        }

        public void ServerVersionGetting(DbConnection connection, DbConnectionInterceptionContext<string> interceptionContext)
        {
        }

        public void ServerVersionGot(DbConnection connection, DbConnectionInterceptionContext<string> interceptionContext)
        {
        }

        public void StateGetting(DbConnection connection, DbConnectionInterceptionContext<System.Data.ConnectionState> interceptionContext)
        {
        }

        public void StateGot(DbConnection connection, DbConnectionInterceptionContext<System.Data.ConnectionState> interceptionContext)
        {
        }
    }

    public class SessionContextConfiguration : DbConfiguration
    {
        public SessionContextConfiguration()
        {
            AddInterceptor(new SessionContextInterceptor());
        }
    }
}
```

이것이 응용 프로그램에 필요한 유일한 변경 사항입니다. 계속해서 응용 프로그램을 빌드하고 게시합니다.

## <a name="step-2-add-a-userid-column-to-the-database-schema"></a>2 단계: 데이터베이스 스키마에 UserId 열 추가
다음으로, 각 행을 사용자(테넌트)와 연결하기 위해서 Contacts 테이블에 UserId 열을 추가해야 합니다. 이 필드를 EF 데이터 모델에 포함시킬 필요가 없도록, 스키마를 데이터베이스에서 바로 변경하겠습니다.

SQL Server Management Studio 또는 Visual Studio를 사용하여 데이터베이스에 직접 연결한 후 다음 T-SQL을 실행합니다.

```
ALTER TABLE Contacts ADD UserId nvarchar(128)
    DEFAULT CAST(SESSION_CONTEXT(N'UserId') AS nvarchar(128))
```

이것은 Contacts 테이블에 UserId 열을 추가합니다. AspNetUsers 테이블에 저장된 UserId와 맞추기 위해 nvarchar(128) 데이터 형식을 사용하며, 새로 삽입된 행의 UserId가 SESSION_CONTEXT에 현재 저장되어 있는 UserId가 되도록 자동으로 설정하는 DEFAULT 제약 조건을 만듭니다.

이제 테이블은 다음과 같습니다.

![SSMS Contacts 테이블](./media/web-sites-dotnet-entity-framework-row-level-security/SSMS-Contacts.png)

새 연락처가 만들어지면, 올바른 UserId에 자동으로 할당됩니다. 하지만 데모 목적으로, 기존 연락처 중 일부를 기존 사용자에게 할당하겠습니다.

응용 프로그램에서(예: 로컬, Google 또는 Facebook 계정을 사용하여) 몇 개의 사용자를 이미 만들었다면 AspNetUsers 테이블에서 볼 수 있습니다. 아래 스크린샷에는 아직 한 명의 사용자만 있습니다.

![SSMS AspNetUsers 테이블](./media/web-sites-dotnet-entity-framework-row-level-security/SSMS-AspNetUsers.png)

user1@contoso.com,의 ID를 복사하여 아래 T-SQL 문에 붙여 넣습니다. 이 명령문을 실행하여 연락처 중 3개를 이 UserId와 연결합니다.

```
UPDATE Contacts SET UserId = '19bc9b0d-28dd-4510-bd5e-d6b6d445f511'
WHERE ContactId IN (1, 2, 5)
```

## <a name="step-3-create-a-row-level-security-policy-in-the-database"></a>3 단계: 데이터베이스에 행 수준 보안 정책 만들기
마지막 단계는 쿼리에 의해 자동으로 반환되는 결과를 자동으로 필터링하기 위해서 SESSION_CONTEXT의 UserId를 사용하는 보안 정책을 만드는 것입니다.

데이터베이스에 연결된 상태에서 다음 T-SQL을 실행합니다.

```
CREATE SCHEMA Security
go

CREATE FUNCTION Security.userAccessPredicate(@UserId nvarchar(128))
    RETURNS TABLE
    WITH SCHEMABINDING
AS
    RETURN SELECT 1 AS accessResult
    WHERE @UserId = CAST(SESSION_CONTEXT(N'UserId') AS nvarchar(128))
go

CREATE SECURITY POLICY Security.userSecurityPolicy
    ADD FILTER PREDICATE Security.userAccessPredicate(UserId) ON dbo.Contacts,
    ADD BLOCK PREDICATE Security.userAccessPredicate(UserId) ON dbo.Contacts
go

```

이 코드는 세 가지를 수행합니다. 우선, RLS 개체에 대한 액세스를 중앙 집중화하고 제한하는 모범 사례의 하나로 새로운 스키마를 만듭니다. 다음으로, 행의 UserId가 SESSION_CONTEXT의 UserId와 일치하면 '1'을 반환하는 조건자 함수를 만듭니다. 마지막으로, Contacts 테이블에 대한 필터 및 차단 조건자로 이 함수를 추가하는 보안 정책을 만듭니다. 필터 조건자는 쿼리가 현재 사용자에게 속하는 행만 반환하도록 하며, 차단 조건자는 응용 프로그램이 우발적으로 잘못된 사용자의 행을 삽입하지 못하도록 막는 안전 장치 역할을 합니다.

이제 응용 프로그램을 실행하고 user1@contoso.com. 이제 이 사용자에는 자신의 UserId에 할당된 연락처만 볼 수 있습니다.

![RLS를 활성화하기 전의 연락처 관리자 응용 프로그램](./media/web-sites-dotnet-entity-framework-row-level-security/ContactManagerApp-After.png)

더 검증하려면 새로운 사용자 등록을 시도해 봅니다. 새 사용자에게 할당된 연락처가 없기 때문에 아무 연락처도 표시되지 않습니다. 새 사용자가 새 연락처를 만들면, 새 사용자에게 할당되고, 그들만 연락처를 볼 수 있게 됩니다.

## <a name="next-steps"></a>다음 단계
이것으로 끝입니다. 간단한 연락처 관리자 웹 앱이 각 사용자가 자신의 연락처 목록을 갖는 다중 테넌트 앱으로 변환되었습니다. 행 수준 보안을 사용하여, 응용 프로그램 코드에 테넌트 액세스 논리를 적용하는 복잡한 상황을 피했습니다. 이러한 투명성을 통해 응용 프로그램이 당면한 실제 비즈니스 문제에 중점을 둘 수 있고, 응용 프로그램의 코드베이스가 커지면서 우발적으로 데이터가 누출되는 위험을 줄일 수도 있습니다.

이 자습서에는 RLS로 가능한 표면적인 기능을 급히 모았을 뿐입니다. 예를 들면 보다 정교하고 세분화된 액세스 논리를 포함할 수 있으며, SESSION_CONTEXT에는 현재 UserId뿐만 아니라 훨씬 더 많은 내용을 저장할 수 있습니다. 확장 데이터 계층에 다중 테넌트 분할을 지원하기 위해서 [RLS와 탄력적 데이터베이스 도구 클라이언트 라이브러리를 통합](../sql-database/sql-database-elastic-tools-multi-tenant-row-level-security.md) 하는 것도 가능합니다.

이러한 가능성 외에도 RLS를 훨씬 더 개선하기 위해 최선을 다하고 있습니다. 질문이나 아이디어가 있거나 보고 싶은 내용이 있으면 의견을 알려 주시기 바랍니다. 여러분의 의견에 감사드립니다.




<!--HONumber=Nov16_HO3-->


