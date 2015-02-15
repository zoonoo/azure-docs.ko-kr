#Java용 Azure SDK 다운로드

##Java용 Azure 클라이언트 라이브러리 - 수동 다운로드

Java용 Azure 라이브러리는 [Apache 라이선스 버전 2.0][라이선스](영문)으로 분산됩니다. 라이브러리 및 모든 종속성의 ZIP 파일을 다운로드하려면 [여기][zip 다운로드]를 클릭합니다.  이 라이브러리는 Microsoft Open Technologies, Inc.에서 제공합니다. 라이선스 및 기타 정보는 ZIP에 포함된 license.txt 및 ThirdPartyNotices.txt 파일을 참조하세요.

##Java용 Azure 라이브러리 - Maven

프로젝트가 빌드에 Maven을 사용하도록 이미 설정된 경우 pom.xml 파일에 다음 종속성을 추가합니다.

	<dependency>
	    <groupId>com.microsoft.azure</groupId>
	    <artifactId>azure-management</artifactId>
	    <version>n.n.n</version>
	</dependency>
	<dependency>
	    <groupId>com.microsoft.azure</groupId>
	    <artifactId>azure-management-compute</artifactId>
	    <version>n.n.n</version>
	</dependency>
	<dependency>
	    <groupId>com.microsoft.azure</groupId>
	    <artifactId>azure-management-network</artifactId>
	    <version>n.n.n</version>
	</dependency>
	<dependency>
	    <groupId>com.microsoft.azure</groupId>
	    <artifactId>azure-management-sql</artifactId>
	    <version>n.n.n</version>
	</dependency>
	<dependency>
	    <groupId>com.microsoft.azure</groupId>
	    <artifactId>azure-management-storage</artifactId>
	    <version>n.n.n</version>
	</dependency>
	<dependency>
	    <groupId>com.microsoft.azure</groupId>
	    <artifactId>azure-management-websites</artifactId>
	    <version>n.n.n</version>
	</dependency>
	<dependency>
	    <groupId>com.microsoft.azure</groupId>
	    <artifactId>azure-media</artifactId>
	    <version>n.n.n</version>
	</dependency>
	<dependency>
	    <groupId>com.microsoft.azure</groupId>
	    <artifactId>azure-servicebus</artifactId>
	    <version>n.n.n</version>
	</dependency>
	<dependency>
	    <groupId>com.microsoft.azure</groupId>
	    <artifactId>azure-serviceruntime</artifactId>
	    <version>n.n.n</version>
	</dependency>



`<version>` 요소 내에서  *n.n.n*을 유효한 버전 번호로 바꿉니다. 버전 번호는 [Maven의 Azure 라이브러리 리포지토리](http://go.microsoft.com/fwlink/?LinkID=286274)에서 얻을 수 있습니다.

[라이선스]: http://www.apache.org/licenses/LICENSE-2.0.html
[zip 다운로드]:  http://go.microsoft.com/fwlink/?LinkId=253887

<!--HONumber=42-->
