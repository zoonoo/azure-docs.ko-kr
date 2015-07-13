이 가이드에서는 [ClearDB]를 사용하여 [Azure 스토어]에서 MySQL 데이터베이스를 만드는 방법 및 [Azure 웹 사이트][waws]를 만들 때 MySQL 데이터베이스를 연결된 리소스로 만드는 방법을 보여 줍니다. [ClearDB]는 Azure 데이터 센터에서 MySQL 데이터베이스를 실행 및 관리하고 모든 응용 프로그램에서 연결할 수 있게 해 주는 내결함성 DasS(Database-as-a-Service) 공급자입니다.

> [AZURE.NOTE]웹 사이트를 만드는 과정에서 MySQL 데이터베이스를 만드는 경우 무료 데이터베이스만 만들 수 있습니다. Azure 스토어에서 MySQL 데이터베이스를 만드는 경우 무료 데이터베이스를 만들거나 유료 옵션 중에서 선택할 수 있습니다.

## 방법: Azure 스토어에서 MySQL 데이터베이스 만들기

[Azure 스토어]에서 MySQL 데이터베이스를 만들려면 다음을 수행합니다.

1. [Azure 관리 포털][portal]에 로그인합니다.
2. 페이지 맨 아래에 있는 **+새로 만들기**를 클릭하고 **마켓플레이스**를 선택합니다.

	![스토어에서 추가 기능 선택](./media/create-mysql-db/select-store.png)

3. **ClearDB MySQL Database**를 선택하고 프레임 맨 아래에 있는 화살표를 클릭합니다.

	![ClearDB MySQL 데이터베이스 선택](./media/create-mysql-db/select-cleardb-mysql.png)

4. 요금제를 선택하고 데이터베이스 이름을 입력한 다음 지역을 선택하고 프레임 맨 아래에 있는 화살표를 클릭합니다.

	![스토어에서 MySQL 데이터베이스 구매](./media/create-mysql-db/purchase-mysql.png)

5. 확인 표시를 클릭하여 구매를 완료합니다.

	![구매 검토 및 완료](./media/create-mysql-db/complete-mysql-purchase.png)

6. 데이터베이스가 만들어진 후 관리 포털의 **ADD-ONS** 탭에서 관리할 수 있습니다.

	![Azure 포털에서 MySQL 데이터베이스 관리](./media/create-mysql-db/manage-mysql-add-on.png)

7. 페이지 맨 아래에 있는 **CONNECTION INFO**를 클릭하여 데이터베이스 연결 정보를 가져올 수 있습니다(위에 표시됨).

	![MySql 연결 정보](./media/create-mysql-db/mysql-conn-info.png)


## 방법: Azure 웹 사이트에 대한 연결된 리소스로 MySQL 데이터베이스 만들기

[Azure 웹 사이트][waws]를 만들 때 MySQL 데이터베이스를 연결된 리소스로 만들려면 다음을 수행합니다.

1. [Azure 관리 포털][portal]에 로그인합니다.
2. 페이지 맨 아래에 있는 **+새로 만들기**를 클릭하고 **계산**, **웹 사이트**, **데이터베이스와 함께 만들기**를 차례로 선택합니다.

	![데이터베이스를 사용하여 웹 사이트 만들기](./media/create-mysql-db/custom_create.png)

3. 웹 사이트의 **URL**을 제공하고 사이트의 **지역**을 선택한 다음 **데이터베이스** 드롭다운에서 **새 MySQL 데이터베이스 만들기**를 선택합니다. 선택적으로, 연결 문자열의 기본 이름을 바꿀 수 있습니다. 페이지 맨 아래에 있는 화살표를 클릭합니다.

	![웹 사이트 세부 정보 제공](./media/create-mysql-db/provide-website-details.png)

4. 데이터베이스 **이름**을 제공하고 데이터베이스의 **지역**을 선택한 다음(웹 사이트의 지역과 같아야 함) ClearDB의 약관에 동의하고 프레임 맨 아래에 있는 확인 표시를 클릭합니다.

	![MySQL 세부 정보 제공](./media/create-mysql-db/provide-mysql-details.png)

5. 웹 사이트가 만들어진 후 사이트 이름을 클릭하여 사이트 대시보드로 이동합니다.

	![웹 사이트 대시보드로 이동](./media/create-mysql-db/go-to-website-dashboard.png)

6. **CONFIGURE**를 클릭합니다.

	![구성 탭으로 이동](./media/create-mysql-db/go-to-configure-tab.png)

7. **connection strings** 섹션까지 아래로 스크롤하고 **Show Connection Strings**를 클릭합니다.

	![연결 문자열 표시](./media/create-mysql-db/show-conn-string.png)

8. 응용 프로그램에서 사용할 연결 문자열을 복사합니다.

	![표시된 연결 문자열](./media/create-mysql-db/shown-conn-string.png)

> [AZURE.NOTE]연결 문자열은 웹 사이트 응용 프로그램에서 연결 문자열 이름으로 액세스할 수 있습니다. .NET 응용 프로그램의 경우 **connectionStrings** 개체에서 연결 문자열을 사용할 수 있습니다. 다른 프로그래밍 언어에서는 연결 문자열을 환경 변수로 액세스할 수 있습니다. 자세한 내용은 [웹 사이트를 구성하는 방법][configure]을 참조하십시오.

[ClearDB]: http://www.cleardb.com/
[waws]: /documentation/services/web-sites/
[Azure 스토어]: ../articles/store.md
[portal]: http://manage.windowsazure.com
[configure]: ../article/app-service-web/web-sites-configure.md

<!---HONumber=62-->