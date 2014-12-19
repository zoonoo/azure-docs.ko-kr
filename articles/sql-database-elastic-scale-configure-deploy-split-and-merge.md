<properties title="Split and Merge Service Tutorial" pageTitle="Azure SQL 분할 및 병합 서비스 자습서" description="Splitting and Merging with Elastic Scale" metaKeywords="sharding scaling, Azure SQL Database sharding, elastic scale, splitting and merging elastic scale" services="sql-database" documentationCenter=""  manager="jhubbard" authors="sidneyh@microsoft.com"/>

<tags ms.service="sql-database" ms.workload="sql-database" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/02/2014" ms.author="sidneyh" />

#탄력적인 확장 분할 및 병합 서비스 자습서

## 분할-병합 패키지 다운로드 
1. [NuGet](http://docs.nuget.org/docs/start-here/installing-nuget)에서 최신 NuGet 버전을 다운로드합니다. 
2. 명령 프롬프트를 열고 nuget.exe 다운로드한 디렉터리로 이동합니다. 
3. 아래 명령을 사용하여 최신 분할-병합 패키지를 현재 디렉터리로 다운로드합니다. 
`nuget install Microsoft.Azure.SqlDatabase.ElasticScale.Service.SplitMerge`  

위의 단계에서는 분할-병합 파일을 현재 디렉터리로 다운로드합니다. 파일은 **Microsoft.Azure.SqlDatabase.ElasticScale.Service.SplitMerge.x.x.xxx.x**라는 디렉터리에 저장됩니다. 여기서 *x.x.xxx.x*는 버전 번호를 나타냅니다. 분할-병합 서비스 파일은 **content\splitmerge\service** 하위 디렉터리에 있고 분할-병합 PowerShell 스크립트(및 필요한 클라이언트 .dll)는 **content\splitmerge\powershell** 하위 디렉터리에 있습니다.

##필수 조건 

1. 분할-병합 상태 데이터베이스로 사용할 Azure SQL DB 데이터베이스를 만듭니다. [Azure 관리 포털](https://manage.windowsazure.com)로 이동합니다. 왼쪽 아래에서 **새로 만들기**를 클릭한 다음 **데이터 서비스** -> **SQL 데이터베이스** -> **사용자 지정 만들기**를 클릭합니다. 데이터베이스 이름을 입력하고 새 사용자와 암호를 만듭니다. 나중에 사용하기 위해 이름과 암호를 기록해 두세요.

2. Azure SQL DB 서버가 Microsoft Azure 서비스의 연결을 허용하는지 확인합니다. [Azure 관리 포털](https://manage.windowsazure.com)로 이동하고 왼쪽 창에서 **SQL 데이터베이스**를 클릭합니다. 그런 다음 화면 위쪽의 리본 메뉴에서 **서버**를 클릭하고 서버를 선택합니다. 그리고 맨 위에서 **구성**을 클릭하고 **Microsoft Azure 서비스** 설정이 **예**로 설정되어 있는지 확인합니다.

    ![Allowed services][1]

3. 진단 출력에 사용할 Azure 저장소 계정을 만듭니다. [Azure 관리 포털](https://manage.windowsazure.com)로 이동합니다. 왼쪽 아래에서 **새로 만들기**, **데이터 서비스**, **저장소**, **빠른 생성**을 차례로 클릭합니다. 

4. 분할-병합 서비스를 포함할 Azure 클라우드 서비스를 만듭니다.  [Azure 관리 포털](https://manage.windowsazure.com)로 이동합니다. 왼쪽 아래에서 **새로 만들기**, **계산**, **클라우드 서비스**, **빠른 생성**을 차례로 클릭합니다. 


## 분할-병합 서비스 구성 

### 분할-병합 서비스 구성 

1. 분할/병합 비트를 다운로드한 폴더에서 **SplitMergeService.cspkg**와 함께 제공된 **ServiceConfiguration.Template.cscfg** 파일의 복사본을 만들고 이름을 **ServiceConfiguration.cscfg**라고 지정합니다.

2. 즐겨 사용하는 텍스트 편집기에서 ServiceConfiguration.cscfg를 엽니다. 인증서 지문의 형식과 같은 입력의 유효성을 검사하는 Visual Studio를 사용하는 것이 좋습니다. 

3. 분할/병합 작업의 상태 데이터베이스로 사용할 데이터베이스를 새로 만들거나 기존 데이터베이스 중에서 선택하고 해당 데이터베이스의 연결 문자열을 검색합니다. Azure SQL DB에서 연결 문자열의 형식을 일반적으로 다음과 같습니다.

        "Server=myservername.database.windows.net; Database=mydatabasename;User ID=myuserID; Password=mypassword; Encrypt=True; Connection Timeout=30" .
4.    이 연결 문자열을 cscfg 파일에서 ElasticScaleMetadata 설정의 **SplitMergeWeb** 및 **SplitMergeWorker** 역할 섹션 모두에 입력합니다.

5.    진단 로그에 대한 대상을 구성하려면 Azure의 저장소 계정이 필요합니다. 분할/병합에 대한 구성에는 Azure 저장소 계정에 대한 연결 문자열이 필요합니다. 연결 문자열의 형식은 다음과 같아야 합니다.

        "DefaultEndpointsProtocol=https;AccountName=myAccountName;AccountKey=myAccessKey" 
    
액세스 키를 확인하려면 [Azure 관리 포털](https://manage.windowsazure.com)로 이동하고 왼쪽에서 **저장소** 탭을 클릭하고 저장소 계정에 해당하는 이름을 선택한 다음 아래쪽에서 **액세스 키 관리**를 클릭합니다. **기본 액세스 키**의 **복사** 단추를 클릭합니다.

![manage access keys][2]

6.    저장소 계정 이름과 제공된 액세스 키 중 하나를 저장소 연결 문자열의 자리 표시자에 입력합니다. 이 연결 문자열은 **Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString** 설정의 **SplitMergeWeb** 및 **SplitMergeWorker** 역할 섹션 모두에서 사용됩니다. 역할에 따라 다른 저장소 계정을 사용할 수도 있습니다. 

### 보안 구성 
서비스 보안을 구성하는 방법에 대한 자세한 지침은 [탄력적인 확장 보안 구성](./sql-database-elastic-scale-configure-security.md)(영문)을 참조하세요.

단순한 테스트 배포로 이 자습서를 적절히 완료하기 위해 서비스가 가동되도록 하는 최소한의 구성 단계를 수행합니다. 이러한 단계를 실행할 때 하나의 컴퓨터/계정만 사용하여 서비스와 통신하도록 설정합니다.

### 자체 서명된 인증서 만들기 

새 디렉터리를 만들고 [Visual Studio용 개발자 명령 프롬프트](http://msdn.microsoft.com/ko-kr/library/ms229859.aspx) 창을 사용하여 이 디렉터리에서 다음 명령을 실행합니다.

    makecert ^
    -n "CN=*.cloudapp.net" ^
    -r -cy end -sky exchange -eku "1.3.6.1.5.5.7.3.1,1.3.6.1.5.5.7.3.2" ^
    -a sha1 -len 2048 ^
    -sr currentuser -ss root ^
    -sv MyCert.pvk MyCert.cer

개인 키를 보호하는 암호를 입력하라는 메시지가 표시됩니다. 강력한 암호를 입력하고 확인합니다. 그러면 이후 암호를 한 번 더 사용할지 묻는 메시지가 표시됩니다. 끝 부분에서 **예**를 클릭하여 신뢰할 수 있는 인증 기관 루트 저장소로 암호를 가져옵니다.

###    PFX 파일 만들기 

makecert를 실행한 창에서 다음 명령을 실행합니다. 이때 인증서를 만드는 데 사용한 암호를 똑같이 사용합니다.

    pvk2pfx -pvk MyCert.pvk -spc MyCert.cer -pfx MyCert.pfx -pi <password>

### 개인 저장소로 클라이언트 인증서 가져오기
1. Windows 탐색기에서 **MyCert.pfx**를 두 번 클릭합니다.
2. **인증서 가져오기 마법사**에서 **현재 사용자**를 선택하고 **다음**을 클릭합니다.
3. 파일 경로를 확인하고 **다음**을 클릭합니다.
4. 암호를 입력하고 **확장 속성 모두 포함**을 선택된 상태로 두고 **다음**을 클릭합니다.
5. **인증서 저장소를 자동으로 선택[...]**을 선택된 상태로 두고 **다음**을 클릭합니다.
6. **마침**과 **확인**을 클릭합니다.

### 클라우드 서비스에 PFX 파일 업로드

[Azure 관리 포털](https://manage.windowsazure.com)로 이동합니다.

1. **클라우드 서비스**를 선택합니다.
2. 위에서 분할/병합 서비스를 위해 만든 클라우드 서비스를 선택합니다.
3. 위쪽 메뉴에서 **인증서**를 클릭합니다.
4. 아래쪽 막대에서 **업로드**를 클릭합니다.
5. PFX 파일을 선택하고 위와 동일한 암호를 입력합니다.
6. 마쳤으면 목록의 새 항목에서 인증서 지문을 복사합니다.

### 서비스 구성 파일 업데이트

위에서 복사한 인증서 지문을 다음 설정의 thumbprint/value 특성에 붙여 넣습니다.

    <Setting name="AdditionalTrustedRootCertificationAuthorities" value="" />
    <Setting name="AllowedClientCertificateThumbprints" value="" />
    <Certificate name="SSL" thumbprint="" thumbprintAlgorithm="sha1" />
    <Certificate name="CA" thumbprint="" thumbprintAlgorithm="sha1" />

프로덕션 배포의 경우 CA, 서버 인증서 및 클라이언트 인증서에 별도의 인증서를 사용해야 합니다. 이에 대한 자세한 내용은 [보안 구성](./sql-database-elastic-scale-configure-security.md)(영문)을 참조하세요.

### 분할-병합 서비스 배포
1. [Azure 관리 포털](https://manage.windowsazure.com)로 이동합니다.
2. 왼쪽에서 **클라우드 서비스** 탭을 클릭하고 앞서 만든 클라우드 서비스를 선택합니다. 
3. **대시보드**를 클릭합니다. 
4. 스테이징 환경을 선택한 다음 **새 스테이징 배포를 업로드하세요.**를 클릭합니다.

    ![Staging][3]

5. 대화 상자에서 배포 레이블을 입력합니다. '패키지'와 '구성' 모두에 대해 '로컬에서'를 클릭하고 **SplitMergeService.cspkg** 파일과 앞서 구성한 .cscfg 파일을 선택합니다.
6. **단일 인스턴스가 포함된 역할이 하나 이상 있는 경우에도 배포합니다.**라는 확인란이 선택되어 있는지 확인합니다.
7. 오른쪽 아래에 있는 눈금 단추를 눌러 배포를 시작합니다. 완료하는 데 몇 분 정도 걸립니다.

![Upload][4]


## 배포 문제 해결
웹 역할이 온라인 상태로 전환되지 않으면 보안 구성에 문제가 있을 수 있습니다. 위에서 설명한 대로 SSL이 구성되어 있는지 확인합니다.

작업자 역할은 온라인 상태로 전환되지 않지만 웹 역할은 전환되는 경우에는 앞서 만든 상태 데이터베이스 관련 문제일 가능성이 가장 높습니다. 

* .cscfg의 연결 문자열이 정확한지 확인합니다. 
* 서버와 데이터베이스가 있고 사용자 ID 및 암호가 올바른지 확인합니다.
* Azure SQL DB의 경우 연결 문자열 형식이 다음과 같아야 합니다. 

        "Server=myservername.database.windows.net; Database=mydatabasename;User ID=myuserID; Password=mypassword; Encrypt=True; Connection Timeout=30" .

* 서버 이름이 **https://**로 시작하지 않는지 확인합니다.
* Azure SQL DB 서버가 Microsoft Azure 서비스의 연결을 허용하는지 확인합니다. 이렇게 하려면 https://manage.windowsazure.com을 열고 왼쪽에서 "SQL 데이터베이스"를 클릭하고 위쪽에서 "서버"를 클릭하고 서버를 선택합니다. 위쪽에서 **구성**을 클릭하고 **Microsoft Azure 서비스** 설정이 "예"로 설정되어 있는지 확인합니다. 이 문서의 맨 위에 있는 필수 조건 섹션을 참조하세요.

* 분할/병합 서비스 인스턴스에 대한 진단 로그를 검토합니다. Visual Studio 인스턴스를 열고 메뉴 모음에서 **보기** 및 **서버 탐색기**를 클릭합니다. **Microsoft Azure** 아이콘을 클릭하여 Azure 구독에 연결합니다. Microsoft Azure -> 저장소-> < 저장소 계정 >-> 테이블 -> WADLogsTable로 이동합니다. 자세한 내용은 [서버 탐색기로 저장소 리소스 검색](http://msdn.microsoft.com/ko-kr/library/azure/ff683677.aspx)을 참조하세요.

    ![][5]

    ![][6]

## 분할-병합 서비스 배포 테스트
### 웹 브라우저와 연결

분할-병합 서비스의 웹 끝점을 결정합니다. 이 끝점을 찾으려면 Azure 관리 포털에서 클라우드 서비스의 **대시보드**로 이동하고 오른쪽의 **사이트 URL**에서 찾습니다. 기본 보안 설정에서는 HTTP 끝점을 사용하지 않도록 설정하므로 **http://**를 **https://**로 바꿉니다. 이 URL의 페이지를 브라우저에 로드합니다.

### PowerShell 스크립트를 사용하여 테스트

포함된 샘플 PowerShell 스크립트를 실행하여 배포 및 환경을 테스트할 수 있습니다.

포함된 스크립트 파일은 다음과 같습니다.

1. **SetupSampleSplitMergeEnvironment.ps1** - 분할/병합에 대한 테스트 데이터 계층 설정(자세한 설명은 아래 표 참조)
2. **ExecuteSampleSplitMerge.ps1** - 테스트 데이터 계층에 대한 테스트 작업 실행(자세한 설명은 아래 표 참조)
3. **GetMappings.ps1** - 공유 매핑의 현재 상태를 출력하는 최상위 샘플 스크립트
4. **ShardManagement.psm1**  - ShardManagement API를 매핑하는 도우미 스크립트
5. **SqlDatabaseHelpers.psm1** - SQL 데이터베이스를 만들고 관리하는 도우미 스크립트

<table style="width:100%">
  <tr>
    <th>PowerShell 파일</th>
    <th>단계</th>
  </tr>
  <tr>
    <th rowspan="5">SetupSampleSplitMergeEnvironment.ps1</th>
    <td>1.    분할된 데이터베이스 맵 관리자 데이터베이스를 만듭니다.</td>
  </tr>
  <tr>
    <td>2.    분할된 데이터베이스를 2개 만듭니다. 
  </tr>
  <tr>
    <td>3.    해당 데이터베이스에 대한 분할된 데이터베이스 맵을 만듭니다(해당 데이터베이스에서 기존 분할된 데이터베이스 맵은 모두 삭제). </td>
  </tr>
  <tr>
    <td>4.    두 분할된 데이터베이스 모두에서 작은 샘플 테이블을 만들고 분할 데이터베이스 중 하나에 테이블을 채웁니다.</td>
  </tr>
  <tr>
    <td>5.    분할된 테이블에 대한 SchemaInfo를 선언합니다.</td>
  </tr>

</table>

<table style="width:100%">
  <tr>
    <th>PowerShell 파일</th>
    <th>단계</th>
  </tr>
<tr>
    <th rowspan="4">ExecuteSampleSplitMerge.ps1 </th>
    <td>1.    분할 요청을 분할-병합 서비스 웹 프런트 엔드에 보내, 첫 번째 분할된 데이터베이스에서 두 번째 분할된 데이터베이스 사이의 데이터를 절반으로 분할합니다.</td>
  </tr>
  <tr>
    <td>2.    웹 프런트 엔드에 분할 요청 상태를 폴링하고 요청이 완료될 때까지 기다립니다.</td>
  </tr>
  <tr>
    <td>3.    병합 요청을 분할-병합 서비스 웹 프런트 엔드에 보내, 데이터를 두 번째 분할된 데이터베이스에서 첫 번째 분할된 데이터베이스로 다시 이동합니다.</td>
  </tr>
  <tr>
    <td>4.    웹 프런트 엔드에 병합 요청 상태를 폴링하고 요청이 완료될 때까지 기다립니다.</td>
  </tr>
</table>

##PowerShell을 사용하여 배포 확인

1.    새 PowerShell 창을 열고 분할-병합 패키지를 다운로드한 디렉터리로 이동한 다음 "powershell" 디렉터리로 이동합니다.
2.    분할된 데이터베이스 맵 관리자와 분할된 데이터베이스를 만들 Azure SQL 데이터베이스 서버를 새로 만들거나 기존 서버 중에서 선택합니다. 

참고: SetupSampleSplitMergeEnvironment.ps1 스크립트는 기본적으로 이러한 데이터베이스를 모두 동일한 서버에 만들어 스크립트를 단순하게 유지합니다. 이는 분할-병합 서비스 자체의 제한 사항은 아닙니다.

    DB에 대한 읽기/쓰기 권한이 있는 SQL 인증 로그인을 수행해야만 분할-병합 서비스에서 데이터를 이동하고 분할된 데이터베이스 맵을 업데이트할 수 있습니다. 분할-병합 서비스는 클라우드에서 실행되므로 통합 인증은 현재 지원하지 않습니다.

    Azure SQL 서버가 이러한 스크립트를 실행 중인 컴퓨터의 IP 주소를 통한 액세스를 허용하도록 구성되어 있는지 확인합니다. 이 설정은 Azure SQL 서버/구성/허용된 IP 주소 아래에 있습니다.

3.    SetupSampleSplitMergeEnvironment.ps1 스크립트를 실행하여 샘플 환경을 만듭니다. 

    이 스크립트를 실행하면 분할된 데이터베이스 맵 관리자 데이터베이스 및 분할된 데이터베이스에서 기존 분할된 데이터베이스 맵 관리 데이터 구조는 모두 지워집니다. 분할된 데이터베이스 맵 또는 분할된 데이터베이스를 다시 초기화하려는 경우 스크립트를 다시 실행하면 유용합니다.

    샘플 명령줄:

        .\SetupSampleSplitMergeEnvironment.ps1 `
            -UserName 'mysqluser' `
            -Password 'MySqlPassw0rd' `
            -ShardMapManagerServerName 'abcdefghij.database.windows.net'
    
4.    Getmappings.ps1 스크립트를 실행하여 샘플 환경에 현재 있는 매핑을 봅니다.

        .\GetMappings.ps1 `
            -UserName 'mysqluser' `
            -Password 'MySqlPassw0rd' `
            -ShardMapManagerServerName 'abcdefghij.database.windows.net'

5.    ExecuteSampleSplitMerge.ps1 스크립트를 실행하여 분할 작업(첫 번째 분할된 데이터베이스의 데이터 절반을 두 번째 분할된 데이터베이스로 이동)을 실행한 다음 병합 작업(데이터를 다시 첫 번째 분할된 데이터베이스로 이동)을 실행합니다. SSL을 구성하구 http 끝점을 사용하지 않는 상태로 둔 경우 대신 https:// 끝점을 사용해야 합니다.

    샘플 명령줄:

        .\ExecuteSampleSplitMerge.ps1 `
            -UserName 'mysqluser' `
            -Password 'MySqlPassw0rd' `
            -ShardMapManagerServerName 'abcdefghij.database.windows.net' `
            -SplitMergeServiceEndpoint 'https://mysplitmergeservice.cloudapp.net' `
            -CertificateThumbprint '0123456789abcdef0123456789abcdef01234567'

    아래 오류가 수신되는 경우 웹 끝점의 인증서에 문제가 있을 가능성이 가장 높습니다. 즐겨 사용하는 웹 브라우저로 웹 끝점에 연결해 보고 인증서 오류가 발생하는지 확인합니다.

        Invoke-WebRequest : The underlying connection was closed: Could not establish trust relationship for the SSL/TLSsecure channel.

    성공하는 경우 출력이 아래와 같이 표시됩니다.

        > .\ExecuteSampleSplitMerge.ps1 -UserName 'mysqluser' -Password 'MySqlPassw0rd' -ShardMapManagerServerName 'abcdefghij.database.windows.net' -SplitMergeServiceEndpoint 'http://mysplitmergeservice.cloudapp.net' -CertificateThumbprint 0123456789abcdef0123456789abcdef01234567
        Sending split request
        Began split operation with id dc68dfa0-e22b-4823-886a-9bdc903c80f3
        Polling split-merge request status. Press Ctrl-C to end
        Progress: 0% | Status: Queued | Details: [Informational] Queued request
        Progress: 5% | Status: Starting | Details: [Informational] Starting split-merge state machine for request.
        Progress: 5% | Status: Starting | Details: [Informational] Performing data consistency checks on target     shards.
        Progress: 20% | Status: CopyingReferenceTables | Details: [Informational] Moving reference tables from     source to target shard.
        Progress: 20% | Status: CopyingReferenceTables | Details: [Informational] Waiting for reference tables copy     completion.
        Progress: 20% | Status: CopyingReferenceTables | Details: [Informational] Moving reference tables from     source to target shard.
        Progress: 44% | Status: CopyingShardedTables | Details: [Informational] Moving key range [100:110) of     Sharded tables
        Progress: 44% | Status: CopyingShardedTables | Details: [Informational] Successfully copied key range     [100:110) for table [dbo].[MyShardedTable]
        ...
        ...
        Progress: 90% | Status: Completing | Details: [Informational] Successfully deleted shardlets in table     [dbo].[MyShardedTable].
        Progress: 90% | Status: Completing | Details: [Informational] Deleting any temp tables that were created     while processing the request.
        Progress: 100% | Status: Succeeded | Details: [Informational] Successfully processed request. 
        Sending merge request
        Began merge operation with id 6ffc308f-d006-466b-b24e-857242ec5f66
        Polling request status. Press Ctrl-C to end
        Progress: 0% | Status: Queued | Details: [Informational] Queued request
        Progress: 5% | Status: Starting | Details: [Informational] Starting split-merge state machine for request.
        Progress: 5% | Status: Starting | Details: [Informational] Performing data consistency checks on target     shards.
        Progress: 20% | Status: CopyingReferenceTables | Details: [Informational] Moving reference tables from     source to target shard.
        Progress: 44% | Status: CopyingShardedTables | Details: [Informational] Moving key range [100:110) of     Sharded tables
        Progress: 44% | Status: CopyingShardedTables | Details: [Informational] Successfully copied key range     [100:110) for table [dbo].[MyShardedTable]
        ...
        ...
        Progress: 90% | Status: Completing | Details: [Informational] Successfully deleted shardlets in table     [dbo].[MyShardedTable].
        Progress: 90% | Status: Completing | Details: [Informational] Deleting any temp tables that were created     while processing the request.
        Progress: 100% | Status: Succeeded | Details: [Informational] Successfully processed request.

6.    다른 데이터 형식으로 실험합니다. 이러한 스크립트는 모두 키 유형을 지정할 수 있는 선택적-ShardKeyType 매개 변수를 사용합니다. 기본값은 Int32이지만 Int64, GUID 또는 이진을 지정할 수도 있습니다. 

## 고유한 요청 만들기 

웹 UI를 통해 서비스를 사용할 수도 있고, 가져온 SplitMerge.psm1 PowerShell 모듈로 서비스를 사용할 수도 있습니다.

분할-병합 서비스는 분할된 테이블과 참조 테이블 모두에서 데이터를 이동할 수 있습니다. 분할된 테이블은 분할 키 열이 있으며 각 분할된 데이터베이스에 서로 다른 행 데이터를 포함합니다. 참조 테이블은 분할되지 않으므로 모든 분할된 데이터베이스에 동일한 행 데이터를 포함합니다. 참조 테이블은 자주 변경되지 않는 데이터에 유용하며 쿼리의 분할된 테이블과 조인하는 데 사용됩니다.

분할-병합 작업을 수행하려면 이동할 분할된 테이블과 참조 테이블을 선언해야 합니다. 이 작업은 **SchemaInfo** API를 사용하여 이루어집니다. 이 API는 **Microsoft.Azure.SqlDatabase.ElasticScale.ShardManagement.Schema** 네임스페이스에 있습니다.

1.    각 분할된 테이블에 대해 테이블의 부모 스키마 이름(선택 사항, 기본값은 "dbo"), 테이블 이름 및 해당 테이블에서 분할 키를 포함하는 행의 이름을 설명하는 **ShardedTableInfo** 개체를 만듭니다.
2.    각 참조 테이블에 대해 테이블의 부모 스키마 이름(선택 사항, 기본값은 "dbo") 및 테이블 이름을 설명하는 **ReferenceTableInfo** 개체를 만듭니다.
3.    위의 TableInfo 개체를 새 **SchemaInfo** 개체에 추가합니다.
4.    **ShardMapManager** 개체에 대한 참조를 가져오고 **GetSchemaInfoCollection**을 호출합니다.
5.    분할된 데이터베이스 맵 이름을 제공하여 **SchemaInfo**를 **SchemaInfoCollection**에 추가합니다.

이에 대한 예제는 SetupSampleSplitMergeEnvironment.ps1 스크립트에서 확인할 수 있습니다.

분할-병합 서비스에서는 대상 데이터베이스(또는 데이터베이스에 있는 테이블에 대한 스키마)를 만들지 않습니다. 서비스에 요청을 보내기 전에 미리 만들어야 합니다.


## 문제 해결
샘플 powershell 스크립트를 실행할 때 아래와 같은 메시지가 표시될 수 있습니다.

    Invoke-WebRequest : The underlying connection was closed: Could not establish trust relationship for the SSL/TLS secure channel.

이 오류는 SSL 인증서가 올바로 구성되지 않았음을 의미합니다. '웹 브라우저와 연결' 섹션의 지침을 따르세요.

[AZURE.INCLUDE [elastic-scale-include](../includes/elastic-scale-include.md)]
 
<!--Image references-->
[1]: ./media/sql-database-elastic-scale-split-and-merge-tutorial/allowed-services.png
[2]: ./media/sql-database-elastic-scale-split-and-merge-tutorial/manage.png
[3]: ./media/sql-database-elastic-scale-split-and-merge-tutorial/staging.png
[4]: ./media/sql-database-elastic-scale-split-and-merge-tutorial/upload.png
[5]: ./media/sql-database-elastic-scale-split-and-merge-tutorial/storage.png
[6]: ./media/sql-database-elastic-scale-split-and-merge-tutorial/logs.png
