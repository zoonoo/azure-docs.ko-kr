<!--
includes/sql-database-include-connection-string-20-portalshots.md

Latest Freshness check:  2015-09-02 , GeneMi.

## Connection string
-->


### Azure 포털에서 연결 문자열 가져오기


[Azure 포털](https://portal.azure.com/)에서 클라이언트 프로그램이 Azure SQL 데이터베이스와 상호 작용하는 데 필요한 연결 문자열을 가져올 수 있습니다.


1. **찾아보기** > **SQL 데이터베이스**를 클릭합니다.

    ![SQL 선택][1-select-sql]

2. **SQL 데이터베이스** 블레이드 왼쪽 위에 있는 필터 텍스트 상자에 데이터베이스 이름을 입력합니다.

    ![데이터베이스 선택][2-select-database]]

3. 해당 데이터베이스에 대한 행을 클릭합니다.

4. 데이터베이스 블레이드가 표시된 후 시각적 편의를 위해 표준 최소화 컨트롤을 클릭하여 검색 및 데이터베이스 필터링에 사용한 블레이드를 축소할 수 있습니다.

5. 데이터베이스 블레이드에서 **데이터베이스 연결 문자열 표시**를 클릭합니다.

6. PHP 연결 라이브러리를 사용하려는 경우 **PHP**라는 레이블이 지정된 문자열을 복사합니다.

	![데이터베이스에 대한 PHP 연결 문자열 복사][3-get-connection-string]

7. 연결 문자열 정보를 클라이언트 프로그램 코드에 붙여 넣습니다. {Your\_password\_here}를 실제 암호로 바꾸어야 합니다.


<!--
Could not find a good link for PHP

For more information, see:<br/>[Connection Strings and Configuration Files](https://msdn.microsoft.com/library/ms378428.aspx).
-->


<!-- Image references. -->

[1-select-sql]: ./media/sql-database-include-connection-string-20-portalshots/connection-string-select-sql.png

[2-select-database]: ./media/sql-database-include-connection-string-20-portalshots/connection-string-select-database.PNG

[3-get-connection-string]: ./media/sql-database-include-connection-string-20-portalshots/connection-string-php.PNG


<!--
These three includes/ files are a sequenced set, but you can pick and choose:

includes/sql-database-include-connection-string-20-portalshots.md
includes/sql-database-include-connection-string-30-compare.md
includes/sql-database-include-connection-string-40-config.md
-->

<!---HONumber=AcomDC_0406_2016-->