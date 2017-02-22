
<!--
includes/sql-database-include-connection-string-30-compare.md

Latest Freshness check:  2015-09-03 , GeneMi.

## Connection string
-->


### <a name="compare-the-connection-string"></a>연결 문자열 비교
다음 표에서는 온-프레미스 SQL Server와 클라우드의 Azure SQL 데이터베이스에 연결해야 하는 C# 프로그램의 연결 문자열을 비교하여 보여줍니다. 차이점은 굵게 표시됩니다.

| 연결 문자열<br/>Azure SQL 데이터베이스에 대한 연결 문자열 | 연결 문자열<br/>Microsoft SQL Server에 대한 연결 문자열 |
|:--- |:--- |
| Server=**tcp:**{your_serverName_here}**.database.windows.net,1433**;<br/>User ID={your_loginName_here}**@{your_serverName_here}**;<br/>암호={your_password_here};<br/>**Database={your_databaseName_here};**<br/>**Connection Timeout=30**;<br/>**Encrypt=True**;<br/>**TrustServerCertificate=False**; |Server={your_serverName_here};<br/>User ID={your_loginName_here};<br/>암호={your_password_here}; |

**Database=** 는 SQL Server에 대한 선택 사항이지만 SQL 데이터베이스에 필요합니다.

[.NET ADO SqlConnectionStringBuilder 속성](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnectionstringbuilder_properties.aspx) -모든 매개 변수를 자세히 설명합니다.

<!--
These three includes/ files are a sequenced set, but you can pick and choose:

includes/sql-database-include-connection-string-20-portalshots.md
includes/sql-database-include-connection-string-30-compare.md
includes/sql-database-include-connection-string-40-config.md
-->


<!--HONumber=Jan17_HO3-->


