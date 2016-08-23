1. [Azure 포털]에서 로그인합니다.

2. **+ 새로 만들기** > **웹 + 모바일** > **모바일 앱**을 클릭한 다음 모바일 앱 백엔드에 대한 이름을 제공합니다.

3. **리소스 그룹**에 대해 기존 리소스 그룹을 선택하거나 새 리소스 그룹을 만듭니다(앱과 같은 이름을 사용하여).
 
	다른 앱 서비스 계획을 선택하거나 새 앱 서비스 계획을 만들 수 있습니다. 앱 서비스 계획에 대한 자세한 내용과 다른 가격 책정 계층 및 원하는 위치에서 새 계획을 만드는 방법은 [Azure 앱 서비스 계획의 포괄 개요](../articles/app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md)를 참조하세요.

4. **앱 서비스 계획**에 대해서는 기본 계획([표준 계층](https://azure.microsoft.com/pricing/details/app-service/)에)이 선택되어 있습니다. 또한 다른 계획을 선택하거나 [새 계획을 만들 수 있습니다](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md#create-an-app-service-plan). 앱 서비스 계획의 설정에 따라 앱과 연결된 [위치, 기능, 비용 및 계산 리소스](https://azure.microsoft.com/pricing/details/app-service/)가 결정됩니다.

	계획을 결정한 다음 **만들기**를 클릭합니다. 이렇게 모바일 앱 백 엔드를 만듭니다.
	
6. 새로운 모바일 앱 백 엔드에 대한 **설정** 블레이드에서 **빠른 시작** > 클라이언트 앱 플랫폼 > **데이터베이스 연결**을 클릭합니다.

	![](./media/app-service-mobile-dotnet-backend-create-new-service/dotnet-backend-create-data-connection.png)

7. **데이터 연결 추가** 블레이드에서 **SQL 데이터베이스** > **새 데이터베이스 만들기**를 클릭하고 데이터베이스 **이름**을 입력하며 가격 책정 계층을 선택한 다음 **서버**를 클릭합니다. 이 새 데이터베이스를 다시 사용할 수 있습니다. 같은 위치에 이미 데이터베이스가 있는 경우 **기존 데이터베이스 사용**을 대신 선택할 수 있습니다. 다른 위치에 있는 데이터베이스는 대역폭 비용 및 더 많은 대기 시간 때문에 사용을 권장하지 않습니다.
 
    ![](./media/app-service-mobile-dotnet-backend-create-new-service/dotnet-backend-create-db.png)

8. **새 서버** 블레이드에서 **서버 이름** 필드에 고유한 서버 이름을 입력하고 로그인 및 암호를 입력한 다음 **Azure 서비스가 서버에 액세스하도록 허용**을 선택하고 **확인**을 클릭합니다. 이렇게 하면 새 데이터베이스가 만들어집니다.

9. **데이터 연결 추가** 블레이드로 돌아와 **연결 문자열**을 클릭하고 사용자 데이터베이스의 로그인 및 암호 값을 입력한 다음 **확인**을 클릭합니다. 계속 진행하기 전에 데이터베이스가 성공적으로 배포되도록 몇 분 동안 기다립니다.

<!-- URLs. -->
[Azure 포털]: https://portal.azure.com/

<!---HONumber=AcomDC_0810_2016-->