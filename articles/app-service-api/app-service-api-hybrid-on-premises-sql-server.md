<properties 
	pageTitle="하이브리드 연결을 사용하여 Azure 앱 서비스의 API 앱에서 온-프레미스 SQL Server에 연결" 
	description="Microsoft Azure에서 API 앱을 만들고 온-프레미스 SQL Server 데이터베이스에 연결"
	services="app-service\api" 
	documentationCenter="" 
	authors="TomArcher" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="app-service-api" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/03/2015" 
	ms.author="tdykstra"/>

# 하이브리드 연결을 사용하여 Azure 앱 서비스의 API 앱에서 온-프레미스 SQL Server에 연결

[AZURE.INCLUDE [app-service-api-v2-note](../../includes/app-service-api-v2-note.md)]

하이브리드 연결은 정적 TCP 포트를 사용하는 온-프레미스 리소스에 [Azure 앱 서비스](http://go.microsoft.com/fwlink/?LinkId=529714) API 앱을 연결할 수 있습니다. 지원되는 리소스로는 Microsoft SQL Server, MySQL, HTTP 웹 API, 모바일 서비스, 대부분의 사용자 지정 웹 서비스가 있습니다.

이 자습서에서는 새 하이브리드 연결 기능을 사용하여 로컬 온-프레미스 SQL Server 데이터베이스에 연결하는 [Azure 미리 보기](http://go.microsoft.com/fwlink/?LinkId=529715)에서 앱 서비스 API 앱을 만드는 방법에 대해 배웁니다. 이 자습서는 이전에 Azure 또는 SQL 서버를 사용해 본 경험이 없다고 가정합니다.

>[AZURE.NOTE]Azure 계정을 등록하기 전에 Azure 앱 서비스를 시작하려면 [앱 서비스 평가](http://go.microsoft.com/fwlink/?LinkId=523751)로 이동합니다. 앱 서비스에서 단기 스타터 웹 앱을 즉시 만들 수 있습니다. 신용 카드는 필요하지 않으며 약정도 필요하지 않습니다.

## 필수 조건

이 자습서를 완료하려면 다음 제품이 필요합니다. 모두 무료 버전으로 사용 가능할 수 있으므로, 완전히 무료로 Azure용 개발을 시작할 수 있습니다.

- **Azure 구독** - 무료 구독에 대해서는 [Azure 무료 평가](/pricing/free-trial/)를 참조하세요. 

- **Visual Studio** - Visual Studio 2013 또는 Visual Studio 2015의 무료 평가판 버전을 다운로드하려면 [Visual Studio 다운로드](http://www.visualstudio.com/downloads/download-visual-studio-vs)를 참조하세요. 계속하기 전에 다음 중 하나를 설치합니다. (이 자습서의 스크린샷은 Visual Studio 2013을 사용하여 수행되었음)

- **SQL Server 2014 Express with Tools** - [Microsoft 웹 플랫폼 데이터베이스 페이지](https://www.microsoft.com/ko-KR/download/details.aspx?id=42299)에서 Microsoft SQL Server Express를 무료로 다운로드하세요. 이 자습서의 뒷부분에서 [SQL Server 설치](#InstallSQLDB) 방법을 참조하여 제대로 구성되었는지 확인합니다.

- **SQL Server Management Studio Express** - 이 제품은 위에서 언급한 SQL Server 2014 Express with Tools 다운로드와 함께 포함되지만 별도로 설치해야 하는 경우 [SQL Server Express 다운로드 페이지](https://www.microsoft.com/ko-KR/download/details.aspx?id=42299)에서 이 제품을 다운로드하여 설치할 수 있습니다.

이 자습서에서는 사용자가 Azure 구독을 사용하며, Visual Studio 2013을 설치했고, .NET Framework 3.5을 설치했거나 사용하도록 설정했다고 가정합니다. 이 자습서에서는 Azure 하이브리드 연결 기능(정적 TCP 포트를 사용하는 기본 인스턴스)에서 적절히 작동하는 구성에 SQL Server 2014 Express를 설치하는 방법을 보여 줍니다. 자습서를 시작하기 전에 SQL 서버가 설치되어 있지 않은 경우 위에서 언급한 위치의 도구를 사용하여 SQL Server 2014 Express를 다운로드합니다(설치는 하지 않음).

### 참고 사항
하이브리드 연결을 사용하여 온-프레미스 SQL Server 또는 SQL Server Express 데이터베이스를 사용하려면 TCP/IP를 고정 포트에서 사용할 수 있어야 합니다. SQL Server의 기본 인스턴스에서는 고정 포트 1433을 사용하는 반면 명명된 인스턴스에서는 이 포트를 사용하지 않습니다.

온-프레미스 하이브리드 연결 관리자 에이전트를 설치하는 컴퓨터는 다음 조건을 충족해야 합니다.

- 다음 포트를 통해 Azure와의 아웃바운드 연결이 설정되어야 합니다.

> <table border="1">
    <tr>
       <th><strong>포트</strong></th>
        <th>이유</th>
    </tr>
    <tr>
        <td>80</td>
        <td>인증서 유효성 검사용 HTTP 포트에 <strong>필요하며</strong> 선택적으로 데이터 연결에 필요합니다.</td>
    </tr>
    <tr>
        <td>443</td>
        <td>데이터 연결에 대해 <strong>선택사항</strong>입니다. 443에 대한 아웃바운드 연결을 사용할 수 없으면 TCP 포트 80이 사용됩니다.</td>
    </tr>
	<tr>
        <td>5671 및 9352</td>
        <td>데이터 연결에 대해 <strong>권장</strong>하지만 선택사항입니다. 이 모드를 사용하면 일반적으로 더 높은 처리량을 얻을 수 있습니다. 이러한 포트에 대한 아웃바운드 연결을 사용할 수 없으면 TCP 포트 443이 사용됩니다.</td>
	</tr>
</table>

- 온-프레미스 리소스의 *호스트이름*:*포트번호*에 연결할 수 있어야 합니다. 

이 자습서의 단계에서는 온-프레미스 하이브리드 연결 에이전트를 호스트하는 컴퓨터에서 브라우저를 사용하고 있다고 가정합니다.

위에서 설명한 조건을 충족하는 구성 및 환경으로 SQL Server를 이미 설치한 경우 건너뛰어서 [SQL Server 데이터베이스 온-프레미스](#CreateSQLDB)를 시작할 수 있습니다.

<a name="InstallSQL"></a>
## SQL Server Express 설치, TCP/IP 사용 및 SQL Server 데이터베이스 온-프레미스 만들기

이 섹션에서는 API 앱이 [Azure Preview 포털](https://portal.azure.com)에서 작동하도록 SQL Server Express를 설치하고, TCP/IP를 사용하도록 설정하고, 데이터베이스를 만드는 방법을 보여 줍니다.

<a name="InstallSQLDB"></a>
### SQL Server Express 설치

1. SQL Server Express를 설치하려면 **SQLEXPRWT\_x64\_ENU.exe**(64비트 버전) 또는 **SQLEXPR\_x86\_ENU.exe**(32비트 버전) 파일을 원하는 폴더에 압축을 풀어야 합니다. 

2. SQL Server Express 설치 파일의 압축을 푼 후 **setup.exe**를 실행합니다.

3. **SQL Server 설치 센터**가 표시되면, **새 SQL Server 독립형 설치 또는 기존 설치에 기능 추가**를 선택합니다.

	![SQL Server 설치 센터](./media/app-service-api-hybrid-on-premises-sql-server/sql-server-installation-center.png)

4. **인스턴스 구성** 페이지가 나타날 때까지 지침을 따라, 기본 선택 사항 및 설정을 그대로 사용합니다.
	
5. **인스턴스 구성** 페이지에서 **기본 인스턴스**를 선택하고 **다음**을 클릭합니다.
	
	![인스턴스 구성](./media/app-service-api-hybrid-on-premises-sql-server/instance-configuration.png)
	
	SQL Server의 기본 인스턴스는 하이브리드 연결 기능이 요구하는 고정 포트 1433을 통해 SQL Server 클라이언트의 요청을 수신 대기합니다. 명명된 인스턴스는 하이브리드 연결에서 지원하지 않는 동적 포트 및 UDP를 사용합니다.
	
6. **서버 구성** 페이지에서 기본값을 사용하고 **다음**을 클릭합니다.
	
7. **데이터베이스 엔진 구성** 페이지의 **인증 모드**에서 **혼합 모드(SQL Server 인증 및 Windows 인증)**를 선택하고 암호를 입력합니다.
	
	![데이터베이스 엔진 구성](./media/app-service-api-hybrid-on-premises-sql-server/database-engine-configuration.png)
	
	이 자습서에서는 SQL Server 인증을 사용합니다. 나중에 필요하므로, 입력하는 암호는 기억해 두어야 합니다.
	
8. 마법사의 나머지 단계를 진행하여 기본값을 사용하여 설치를 완료합니다.

9. **SQL Server 설치 센터** 대화 상자가 닫힙니다.

### TCP/IP 사용
TCP/IP를 사용하도록 설정하려면 SQL Server Express를 설치할 때 설치된 SQL Server 구성 관리자를 사용합니다. 계속하기 전에 [SQL Server에 대한 TCP/IP 네트워크 프로토콜 사용](http://technet.microsoft.com/library/hh231672%28v=sql.110%29.aspx)의 단계를 따르세요.

<a name="CreateSQLDB"></a>
### SQL Server 데이터베이스 온-프레미스 만들기

1. **SQL Server Management Studio**에서 방금 설치한 SQL Server에 연결합니다. **서버 유형**으로 **데이터베이스 엔진**을 선택합니다. **서버 이름**으로 **localhost** 또는 사용 중인 컴퓨터의 이름을 사용할 수 있습니다. **SQL Server 인증**을 선택한 다음 앞서 만든 `sa` 사용자 이름 및 암호로 로그인합니다.

	![서버에 연결](./media/app-service-api-hybrid-on-premises-sql-server/connect-to-server.png)
	
	**서버에 연결** 대화 상자가 자동으로 나타나지 않으면 왼쪽 창의 **개체 탐색기**로 이동하고 **연결**을 클릭한 후 **데이터베이스 엔진**을 클릭합니다.
	
2. SQL Server Management Studio를 사용하여 새 데이터베이스를 만들려면 개체 탐색기에서 **데이터베이스**를 마우스 오른쪽 단추로 클릭한 다음 **새 데이터베이스**를 클릭합니다.
	
	![새 데이터베이스 메뉴 만들기](./media/app-service-api-hybrid-on-premises-sql-server/new-database-menu.png)
	
3. **새 데이터베이스** 대화 상자에 데이터베이스 이름으로 `LocalDatabase`를 입력한 다음 **확인**을 클릭합니다.
	
	![새 데이터베이스 만들기](./media/app-service-api-hybrid-on-premises-sql-server/new-database.png)
	
### SQL Server 테이블 만들기 및 채우기

1. **SQL Server Management Studio** **개체 탐색기**에서 `LocalDatabase` 항목을 확장합니다.

	![확장된 데이터베이스](./media/app-service-api-hybrid-on-premises-sql-server/local-database-expanded.png)

2. **테이블**을 마우스 오른쪽 단추로 클릭하고 상황에 맞는 메뉴에서 **테이블...** 옵션을 선택합니다.

	![새 테이블 메뉴](./media/app-service-api-hybrid-on-premises-sql-server/new-table-menu.png)

3. 테이블 디자이너 눈금이 표시되면 다음 그림에서와 같이 열 정보를 입력합니다.

	![새 테이블 열](./media/app-service-api-hybrid-on-premises-sql-server/table-def.png)

4. **&lt;Ctrl>S** 키를 눌러 새 테이블의 정의를 저장합니다. 테이블 이름을 입력하라는 메시지가 표시됩니다. `Speakers`를 입력하고 **확인**을 누릅니다.

	![새 테이블 저장](./media/app-service-api-hybrid-on-premises-sql-server/save-new-table.png)

5. **개체 탐색기**에서 새로 만든 `Speakers` 테이블을 마우스 오른쪽 단추로 클릭하고 상황에 맞는 메뉴에서 **상위 200개의 행 편집**을 선택합니다.

	![상위 200개 행 편집](./media/app-service-api-hybrid-on-premises-sql-server/edit-top-200-rows.png)

6. 테이블 데이터를 입력/수정하는 눈금이 표시되면, 다음 그림과 같이 일부 테스트 데이터를 입력합니다.

	![테스트 데이터](./media/app-service-api-hybrid-on-premises-sql-server/speakers-data.png)

## 데모 API 앱 만들기 및 Azure에 배포 

이 섹션에서는 데모 API 앱을 만드는 과정을 안내합니다.

1. Visual Studio 2013을 열고 **파일 > 새로 만들기 > 프로젝트**를 선택합니다. 

2. **Visual C# > 웹 > ASP.NET 웹 응용 프로그램** 템플릿을 선택하고 **프로젝트에 Application Insights 추가** 옵션을 선택 취소한 후, 프로젝트 이름을 *SpeakersList*로 지정하고 **확인**을 클릭합니다.

	![](./media/app-service-api-hybrid-on-premises-sql-server/new-project.png)

3. **새 ASP.NET 프로젝트** 대화 상자에서 **Azure API 앱** 프로젝트 템플릿을 선택한 다음 **확인**을 클릭합니다.

	![](./media/app-service-api-hybrid-on-premises-sql-server/new-project-api-app.png)

4. **솔루션 탐색기**에서 **Models** 폴더를 마우스 오른쪽 단추로 클릭한 후 **추가 > 클래스...** 상황에 맞는 메뉴 옵션을 선택합니다.

	![](./media/app-service-api-hybrid-on-premises-sql-server/new-model-menu.png)

5. 새 파일의 이름을 *Speaker.cs*로 지정한 후 **추가**를 클릭합니다.

	![](./media/app-service-api-hybrid-on-premises-sql-server/new-model-class.png)

6. `Speaker.cs` 파일의 전체 내용을 다음 코드로 바꿉니다.

		namespace SpeakersList.Models
		{
			public class Speaker
			{
				public int Id { get; set; }
				public string Name { get; set; }
				public string EmailAddress { get; set; }
			}
		}

7. **솔루션 탐색기**에서 **Controllers** 폴더를 마우스 오른쪽 단추로 클릭한 후 **추가 > 컨트롤러...** 상황에 맞는 메뉴 옵션을 선택합니다.

	![](./media/app-service-api-hybrid-on-premises-sql-server/new-controller.png)

8. **스캐폴드 추가** 대화 상자에서 **Web API 2 컨트롤러 - 비어 있음** 옵션을 선택하고 **추가**를 클릭합니다.

	![](./media/app-service-api-hybrid-on-premises-sql-server/add-scaffold.png)

9. 컨트롤러 이름을 **SpeakersController**로 지정하고 **추가**를 클릭합니다.

	![](./media/app-service-api-hybrid-on-premises-sql-server/add-controller-name.png)

10. `SpeakersController.cs` 파일의 코드를 아래 코드로 바꿉니다. `connectionString`에서 &lt;serverName> 및 &lt;password> 자리 표시자의 고유 값을 지정해야 합니다. &lt;serverName> 값은 SQL Server가 있는 컴퓨터 이름이며 &lt;password> 값은 SQL Server를 설치하고 구성할 때 설정한 값입니다.

	> [AZURE.NOTE]다음 코드 조각에는 암호 정보가 포함됩니다. 이렇게 데모를 간단하게 유지합니다. 실제 프로덕션 환경에서는 코드에 자격 증명을 저장하지 마십시오. 대신에 [ASP.NET 및 Azure에 암호(및 기타 중요한 데이터)를 배포하기 위한 모범 사례](http://www.asp.net/identity/overview/features-api/best-practices-for-deploying-passwords-and-other-sensitive-data-to-aspnet-and-azure)를 참조하세요.

		using System;
		using System.Collections.Generic;
		using System.Linq;
		using System.Net;
		using System.Net.Http;
		using System.Web.Http;
		using SpeakersList.Models;
		using System.Data.Sql;
		using System.Data.SqlClient;
		
		namespace SpeakersList.Controllers
		{
		    public class SpeakersController : ApiController
		    {
		        [HttpGet]
		        public IEnumerable<Speaker> Get()
		        {
					// Instantiate List object that will be populated and returned
					// from this method.
		            List<Speaker> speakers = new List<Speaker>();
	
					// Build database connection string.
		            string connectionString = "Server=<serverName>;" +
		                                      "Initial Catalog=LocalDatabase;" +
		                                      "User Id=sa;Password=<password>;" +
		                                      "Asynchronous Processing=true;";
		
					// Build query string to select all three columns from the Speakers table.
		            string queryString = "SELECT Id, EmailAddress, Name FROM dbo.Speakers;";

					// Instantiate the SqlConnection object using the connection string.
		            using (SqlConnection connection = new SqlConnection(connectionString))
		            {
						// Instantiate the SqlCommand object using the connection and query strings.
		                SqlCommand command = new SqlCommand(queryString, connection);

						// Open the connection to the database.
		                connection.Open();

						// Execute the command and return a SqlDataReader object
						// that can be iterated.
		                SqlDataReader reader = command.ExecuteReader();
		                try
		                {
							// Read a row from the SqlDataReader object
		                    while (reader.Read())
		                    {
								// For each row, use the returned data to instantiate
								// and add to the List a new Speaker object.
		                        speakers.Add(new Speaker 
		                            { 
		                                Id = Convert.ToInt32(reader[0]), 
		                                EmailAddress = reader[1].ToString(), 
		                                Name = reader[2].ToString() 
		                            }
		                        );
		                    }
		                }
		                finally
		                {
							// Close the SqlDataReader object.
		                    reader.Close();
		                }
		            }
					// Return the List of Speaker objects.
		            return speakers;
		        }
		    }
		}

### Swagger UI 사용

Swagger UI를 사용하면 이 메서드를 호출 하도록 클라이언트 코드를 작성하지 않고도 API 앱을 쉽게 테스트할 수 있습니다.

1. *App\_Start/SwaggerConfig.cs* 파일을 열고 **EnableSwaggerUI**를 검색합니다.

	![](./media/app-service-api-hybrid-on-premises-sql-server/swagger-ui-disabled.png)

2. 다음 코드 줄의 주석 처리를 제거합니다.

	        })
	    .EnableSwaggerUi(c =>
	        {

3. 완료되면 파일이 다음 이미지와 같이 표시되는지 확인합니다.

	![](./media/app-service-api-hybrid-on-premises-sql-server/swagger-ui-enabled.png)

### API 앱 테스트

1. API 테스트 페이지를 보려면 **&lt;Ctrl>F5**를 통해 로컬로 앱을 실행합니다. 다음과 유사한 오류가 표시되어야 합니다.

	![](./media/app-service-api-hybrid-on-premises-sql-server/error-forbidden.png)

2. 브라우저의 주소 표시줄에서 URL의 끝에 `/swagger`를 추가한 다음 **&lt;Enter>**를 클릭합니다. 이전 섹션에서 사용하도록 설정한 Swagger UI가 표시됩니다.

	![](./media/app-service-api-hybrid-on-premises-sql-server/swagger-ui.png)

3. **스피커** 섹션을 클릭하여 확장합니다.

4. **GET /api/speakers**를 클릭하여 해당 섹션을 확장합니다.

5. **사용해보기**를 클릭하여 이전에 데이터베이스에 입력된 데이터를 볼 수 있습니다.

	![](./media/app-service-api-hybrid-on-premises-sql-server/try-it-out.png)

### API 앱 배포

이제 앱을 로컬로 테스트했으며, Azure에 앱을 배포하는 시간입니다.

1. **솔루션 탐색기**에서 솔루션이 아니라 프로젝트를 마우스 오른쪽 단추로 클릭하고 **게시...**를 클릭합니다. 

	![프로젝트 게시 메뉴 옵션](./media/app-service-api-hybrid-on-premises-sql-server/publish-menu.png)

2. **프로필** 탭을 클릭하고 **Microsoft Azure API 앱(미리 보기)**을 클릭합니다.

	![프로젝트 게시 메뉴 옵션](./media/app-service-api-hybrid-on-premises-sql-server/publish-web.png)

3. **새로 만들기**를 클릭하여 Azure 구독에서 새 API 앱을 프로비전합니다.

	![기존 API 서비스 선택 대화 상자](./media/app-service-api-hybrid-on-premises-sql-server/publish-to-existing-api-app.png)

4. **API 앱 만들기** 대화 상자에서 다음을 입력합니다.

	- **API 앱 이름** 아래에 앱의 이름을 입력합니다. 
	- Azure 구독이 여러 개 있는 경우 사용할 구독을 선택합니다.
	- **앱 서비스 계획**에서, 기존 앱 서비스 계획에서 선택하거나 **새 앱 서비스 계획 만들기**를 선택하고 새 계획의 이름을 입력합니다. 
	- **리소스 그룹**에 대해 기존 리소스 그룹 중에서 선택하거나 **새 리소스 그룹 만들기**를 선택하고 이름을 입력합니다. 이름은 고유해야 합니다. 앱 이름을 접두사로 사용하고 Microsoft ID(@ 기호 없이)와 같은 일부 개인 정보를 추가하는 것이 좋습니다.  
	- **액세스 수준**에서 **Available to anyone(누구나 사용 가능)**을 선택합니다. 이 옵션은 API를 완전히 공용으로 만들며, 이 자습서에서는 이렇게 해도 괜찮습니다. 나중에 [Azure Preview 포털](https://portal.azure.com)을 통해 액세스를 제한할 수 있습니다.
	- 지역을 선택합니다.

	**확인**을 클릭하여 구독에 API 앱을 만듭니다.

	![Microsoft Azure 웹앱 구성 대화 상자](./media/app-service-api-hybrid-on-premises-sql-server/publish-new-api-app.png)

5. 프로세스에 몇 분이 걸릴 수 있어 Visual Studio에서는 프로세스가 시작되었음을 알리는 대화 상자를 표시합니다. 확인 대화 상자에서 **확인**을 클릭합니다.

	![API 서비스 만들기 시작 확인 메시지](./media/app-service-api-hybrid-on-premises-sql-server/create-api-app-confirmation.png)

6. 프로비전 프로세스가 Azure 구독에서 리소스 그룹 및 API 앱을 만드는 동안, Visual Studio는 **Azure 앱 서비스 활동** 창에 진행률을 표시합니다.

	![Azure 앱 서비스 활동 창을 통한 상태 알림](./media/app-service-api-hybrid-on-premises-sql-server/app-service-activity.png)

7. API 앱이 프로비전되면 **솔루션 탐색기**에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 **게시**를 선택하여 게시 대화 상자를 다시 엽니다. 이전 단계에서 만든 게시 프로필이 미리 선택되어야 합니다. **게시**를 클릭하여 배포 프로세스를 시작합니다.

	![API 앱 배포](./media/app-service-api-hybrid-on-premises-sql-server/publish2.png)

**Azure 앱 서비스 활동** 창에 배포 진행률이 표시되고 게시 프로세스가 완료되면 표시됩니다.

## 하이브리드 연결 및 BizTalk 서비스 만들기 ##

1. 브라우저에서 [Azure Preview 포털](https://portal.azure.com)로 이동합니다. 

2. 왼쪽의 **모두 찾아보기** 옵션을 클릭합니다.

3. **찾아보기** 블레이드에서 **API 앱**을 선택합니다.

4. **API 앱** 블레이드에서 API 앱을 찾아 클릭합니다.

5. API 앱의 블레이드에서 **API 앱 호스트** 아래의 값을 클릭합니다.
 
	![API 앱 블레이드](./media/app-service-api-hybrid-on-premises-sql-server/api-app-blade-api-app-host.png)

6. **API 앱 호스트** 블레이드가 나타나면, 아래 **네트워킹** 섹션으로 스크롤하고 **하이브리드 연결**을 클릭합니다.
	
	![하이브리드 연결](./media/app-service-api-hybrid-on-premises-sql-server/api-app-host-blade-hybrid-connections.png)
	
7. **하이브리드 연결** 블레이드에서 **추가** > **새 하이브리드 연결**을 클릭합니다.
	
8. **하이브리드 연결 블레이드 만들기**에서 다음을 수행합니다.
	- **이름**에서 연결 이름을 입력합니다.
	- **호스트 이름**에 SQL Server 호스트 컴퓨터의 이름을 입력합니다.
	- **포트**로 `1433`(SQL Server의 기본 포트)을 입력합니다.
	- **Biz Talk 서비스**를 클릭하고 BizTalk 서비스의 이름을 입력합니다.
	
	![하이브리드 연결 만들기](./media/app-service-api-hybrid-on-premises-sql-server/create-biztalk-service.png)
		
9. **확인**을 두 번 클릭합니다.

	프로세스가 완료되면, **알림** 영역은 녹색 **성공**으로 깜박이며 **하이브리드 연결** 블레이드는 **연결되지 않음** 상태와 함께 새 하이브리드 연결을 표시합니다.
	
	![만든 하이브리드 연결](./media/app-service-api-hybrid-on-premises-sql-server/hybrid-not-connected-yet.png)
	
여기서 클라우드 하이브리드 연결 인프라의 중요한 부분을 완료했습니다. 이제 해당하는 온-프레미스 부분을 만듭니다.

<a name="InstallHCM"></a>
## 온-프레미스 하이브리드 연결 관리자를 설치하여 연결 완료

[AZURE.INCLUDE [app-service-hybrid-connections-manager-install](../../includes/app-service-hybrid-connections-manager-install.md)]

하이브리드 연결 인프라를 완성했으므로 응용 프로그램을 테스트할 시간입니다.

<a name="CreateASPNET"></a>
## Azure에서 완성된 API 앱 테스트

1. Azure Preview 포털에서 API 앱 호스트 블레이드로 이동하여 **URL** 아래 값을 클릭합니다.
	
2. API 앱의 호스트 페이지가 브라우저에 표시되면 `/swagger`를 브라우저의 주소 표시줄에 추가하고 **&lt;Enter>**를 클릭합니다.
	
3. **스피커** 섹션을 클릭하여 확장합니다.

4. **GET /api/speakers**를 클릭하여 해당 섹션을 확장합니다.

5. **사용해보기**를 클릭하여 이전에 데이터베이스에 입력된 데이터를 볼 수 있습니다.

	![](./media/app-service-api-hybrid-on-premises-sql-server/try-it-out-azure.png)
	
**축하합니다.** Azure에서 실행되는 API 앱을 만들고 하이브리드 연결을 사용하여 로컬 온-프레미스 SQL Server 데이터베이스에 연결했습니다.

## 참고 항목
[하이브리드 연결 개요](http://go.microsoft.com/fwlink/p/?LinkID=397274)

[Josh Twist가 소개하는 하이브리드 연결(채널 9 비디오)(영문)](http://channel9.msdn.com/Shows/Azure-Friday/Josh-Twist-introduces-hybrid-connections)

[하이브리드 연결 개요](/services/biztalk-services/)

[BizTalk 서비스: 대시보드, 모니터, 확장, 구성 및 하이브리드 연결 탭](../biztalk-dashboard-monitor-scale-tabs/)

[원활한 응용 프로그램 이식성으로 실시간 하이브리드 연결 클라우드 구축(채널 9 비디오)(영문)](http://channel9.msdn.com/events/TechEd/NorthAmerica/2014/DCIM-B323#fbid=)

[하이브리드 연결을 사용하여 Azure 모바일 서비스에서 온-프레미스 SQL Server에 연결](../mobile-services-dotnet-backend-hybrid-connections-get-started.md)

[하이브리드 연결을 사용하여 Azure 모바일 서비스에서 온-프레미스 SQL Server에 연결(채널 9 비디오)(영문)](http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Connect-to-an-on-premises-SQL-Server-from-Azure-Mobile-Services-using-Hybrid-Connections)

[ASP.NET ID 개요(영문)](http://www.asp.net/identity)

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

<!---HONumber=AcomDC_1203_2015-->