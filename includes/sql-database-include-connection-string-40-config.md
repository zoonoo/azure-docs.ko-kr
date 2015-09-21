
<!--
includes/sql-database-include-connection-string-40-config.md

Latest Freshness check:  2015-09-04 , GeneMi.

## Connection string
-->


### 연결 문자열 보안에 대한 예제 구성 파일


C# 코드에 연결 문자열을 리터럴로 저장하는 것은 바람직하지 않습니다. 구성 파일에 연결 문자열을 저장하는 것이 좋습니다. 이렇게 하면 다시 컴파일할 필요 없이 언제든지 문자열을 편집할 수 있습니다.

컴파일된 C# 프로그램의 이름이 **ConsoleApplication1.exe**이고 이 .exe가 **bin\\debug** 디렉터리에 있다고 가정해 보겠습니다.

이 예제에서는 연결 문자열의 대부분이 **ConsoleApplication1.exe.config**라는 구성 파일에 저장됩니다. 이 구성 파일도 **bin\\debug**에 있어야 합니다.

다음 구성 파일의 XML에서 **ConnectionString4NoUserIDNoPassword**라는 연결 문자열을 볼 수 있습니다. C# 코드는 이 문자열을 찾습니다.

다음 자리 표시자 대신 실제 이름을 편집해야 합니다.

- {your\_serverName\_here}
- {your\_databaseName\_here}



		<?xml version="1.0" encoding="utf-8" ?>
		<configuration>
		    <startup> 
		        <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.5" />
		    </startup>
		
		    <connectionStrings>
		        <clear />
		        <add name="ConnectionString4NoUserIDNoPassword"
		        providerName="System.Data.ProviderName"
		
		        connectionString=
				"Server=tcp:{your_serverName_here}.database.windows.net,1433;
				Database={your_databaseName_here};
				Connection Timeout=30;
				Encrypt=True;
				TrustServerCertificate=False;" />
		    </connectionStrings>
		</configuration>



이 그림에서는 다음 두 매개 변수를 생략했습니다.

- 사용자 ID={your\_userName\_here};
- 암호={your\_password\_here};


포함할 수도 있지만, 때로는 프로그램이 사용자의 키보드 입력에서 이러한 값을 가져오도록 하는 것이 더 좋습니다. 경우에 따라 다릅니다.



<!--
These three includes/ files are a sequenced set, but you can pick and choose:

includes/sql-database-include-connection-string-20-portalshots.md
includes/sql-database-include-connection-string-30-compare.md
includes/sql-database-include-connection-string-40-config.md
-->

<!---HONumber=Sept15_HO2-->