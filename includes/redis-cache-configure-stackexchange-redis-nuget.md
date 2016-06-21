.NET 응용 프로그램은 **StackExchange.Redis** 캐시 클라이언트를 사용할 수 있습니다. 이는 캐시 클라이언트 응용 프로그램의 구성을 단순화하는 NuGet 패키지를 사용하여 Visual Studio에서 구성할 수 있습니다.

>[AZURE.NOTE] 자세한 내용은 [StackExchange.Redis](http://github.com/StackExchange/StackExchange.Redis)(영문) github 페이지 및 [StackExchange.Redis 캐시 클라이언트 설명서](http://github.com/StackExchange/StackExchange.Redis#documentation)(영문)를 참조하세요.

StackExchange.Redis NuGet 패키지를 사용하여 Visual Studio에서 클라이언트 응용 프로그램을 구성하려면 **솔루션 탐색기**에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 **NuGet 패키지 관리**를 선택합니다.

![NuGet 패키지 관리](media/redis-cache-configure-stackexchange-redis-nuget/redis-cache-manage-nuget-menu.png)

검색 텍스트 상자에 **StackExchange.Redis** 또는 **StackExchange.Redis.StrongName**을 입력하고 결과에서 원하는 버전을 선택한 후 **설치**를 클릭합니다.

>[AZURE.NOTE] 강력한 이름의 **StackExchange.Redis** 클라이언트 라이브러리 버전을 사용하려면 **StackExchange.Redis.StrongName**을 선택하고 그렇지 않으면 **StackExchange.Redis**를 선택합니다.

![StackExchange.Redis NuGet 패키지](media/redis-cache-configure-stackexchange-redis-nuget/redis-cache-stackexchange-redis.png)

NuGet 패키지는 클라이언트 응용 프로그램이 StackExchange.Redis 캐시 클라이언트를 사용하여 Azure Redis Cache에 액세스하는 데 필요한 어셈블리 참조를 다운로드하고 추가합니다.

<!---HONumber=AcomDC_0615_2016-->