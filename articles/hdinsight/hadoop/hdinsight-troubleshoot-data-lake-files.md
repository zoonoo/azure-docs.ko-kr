---
title: Azure HDInsight에서 Data Lake 저장소 파일에 액세스할 수 없음
description: Azure HDInsight에서 Data Lake 저장소 파일에 액세스할 수 없음
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/13/2019
ms.openlocfilehash: 21269f7d5a9ec832a49a613351702dd24be156af
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "75894158"
---
# <a name="unable-to-access-data-lake-storage-files-in-azure-hdinsight"></a>Azure HDInsight에서 Data Lake 저장소 파일에 액세스할 수 없음

이 문서에서는 Azure HDInsight 클러스터와 상호 작용할 때 문제에 대 한 문제 해결 단계 및 가능한 해결 방법을 설명 합니다.

## <a name="issue-acl-verification-failed"></a>문제: ACL 확인 실패

다음과 유사한 오류 메시지가 표시 됩니다.

```
LISTSTATUS failed with error 0x83090aa2 (Forbidden. ACL verification failed. Either the resource does not exist or the user is not authorized to perform the requested operation.).
```

### <a name="cause"></a>원인

사용자가 파일/폴더에 대해 SP (서비스 사용자)의 사용 권한을 취소 했을 수 있습니다.

### <a name="resolution"></a>해결 방법

1. SP에 경로를 따라 트래버스하는 ' x ' 권한이 있는지 확인 하십시오. 자세한 내용은 [사용 권한](https://hdinsight.github.io/ClusterCRUD/ADLS/adls-create-permission-setup.html)을 참조하세요. Data Lake 저장소 계정의 파일/폴더에 대 한 액세스를 확인 하는 샘플 dfs 명령:

    ```
    hdfs dfs -ls /<path to check access>
    ```

1. 수행 되는 읽기/쓰기 작업에 따라 경로에 액세스 하는 데 필요한 권한을 설정 합니다. 다양 한 파일 시스템 작업에 필요한 권한은 여기를 참조 하세요.

---

## <a name="issue-service-principal-certificate-expiry"></a>문제: 서비스 주체 인증서 만료

다음과 유사한 오류 메시지가 표시 됩니다.

```
Token Refresh failed - Received invalid http response: 500
```

### <a name="cause"></a>원인

서비스 주체 액세스를 위해 제공 된 인증서가 만료 되었을 수 있습니다.

1. 헤드 노드로 SSH 합니다. 다음 dfs 명령을 사용 하 여 저장소 계정에 대 한 액세스를 확인 합니다.

    ```
    hdfs dfs -ls /
    ```

1. 오류 메시지가 다음과 유사 하는지 확인 합니다.

    ```
    {"stderr": "-ls: Token Refresh failed - Received invalid http response: 500, text = Response{protocol=http/1.1, code=500, message=Internal Server Error, url=http://gw0-abccluster.24ajrd4341lebfgq5unsrzq0ue.fx.internal.cloudapp.net:909/api/oauthtoken}}...
    ```

1. 에서 url 중 하나를 가져옵니다 `core-site.xml property`  -  `fs.azure.datalake.token.provider.service.urls` .

1. 다음 말아 넘기기 명령을 실행 하 여 OAuth 토큰을 검색 합니다.

    ```
    curl gw0-abccluster.24ajrd4341lebfgq5unsrzq0ue.fx.internal.cloudapp.net:909/api/oauthtoken
    ```

1. 유효한 서비스 사용자에 대 한 출력은 다음과 같습니다.

    ```
    {"AccessToken":"MIIGHQYJKoZIhvcNAQcDoIIGDjCCBgoCAQA…….","ExpiresOn":1500447750098}
    ```

1. 서비스 사용자 인증서가 만료 된 경우 출력은 다음과 같이 표시 됩니다.

    ```
    Exception in OAuthTokenController.GetOAuthToken: 'System.InvalidOperationException: Error while getting the OAuth token from AAD for AppPrincipalId 23abe517-2ffd-4124-aa2d-7c224672cae2, ResourceUri https://management.core.windows.net/, AADTenantId https://login.windows.net/80abc8bf-86f1-41af-91ab-2d7cd011db47, ClientCertificateThumbprint C49C25705D60569884EDC91986CEF8A01A495783 ---> Microsoft.IdentityModel.Clients.ActiveDirectory.AdalServiceException: AADSTS70002: Error validating credentials. AADSTS50012: Client assertion contains an invalid signature. **[Reason - The key used is expired.**, Thumbprint of key used by client: 'C49C25705D60569884EDC91986CEF8A01A495783', Found key 'Start=08/03/2016, End=08/03/2017, Thumbprint=C39C25705D60569884EDC91986CEF8A01A4956D1', Configured keys: [Key0:Start=08/03/2016, End=08/03/2017, Thumbprint=C39C25705D60569884EDC91986CEF8A01A4956D1;]]
    Trace ID: e4d34f1c-a584-47f5-884e-1235026d5000
    Correlation ID: a44d870e-6f23-405a-8b23-9b44aebfa4bb
    Timestamp: 2017-10-06 20:44:56Z ---> System.Net.WebException: The remote server returned an error: (401) Unauthorized.
    at System.Net.HttpWebRequest.GetResponse()
    at Microsoft.IdentityModel.Clients.ActiveDirectory.HttpWebRequestWrapper.<GetResponseSyncOrAsync>d__2.MoveNext()
    ```

1. 다른 모든 Azure Active Directory 관련 오류/인증서 관련 오류는 OAuth 토큰을 가져오기 위해 게이트웨이 url을 ping 하 여 인식 될 수 있습니다.

1. HDI 클러스터에서 ADLS에 액세스 하려고 할 때 다음과 같은 오류가 발생 합니다. 위에서 언급 한 단계에 따라 인증서가 만료 되었는지 확인 합니다.

    ```
    Error: java.lang.IllegalArgumentException: Token Refresh failed - Received invalid http response: 500, text = Response{protocol=http/1.1, code=500, message=Internal Server Error, url=http://clustername.hmssomerandomstringc.cx.internal.cloudapp.net:909/api/oauthtoken}
    ```

### <a name="resolution"></a>해결 방법

다음 PowerShell 스크립트를 사용 하 여 새 인증서를 만들거나 기존 인증서를 할당 합니다.

```powershell
$clusterName = 'CLUSTERNAME'
$resourceGroupName = 'RGNAME'
$subscriptionId = 'SUBSCRIPTIONID'
$appId = 'APPLICATIONID'
$generateSelfSignedCert = $false
$addNewCertKeyCredential = $true
$certFilePath = 'NEW_CERT_PFX_LOCAL_PATH'
$certPassword = Read-Host "Enter Certificate Password"

if($generateSelfSignedCert)
{
    Write-Host "Generating new SelfSigned certificate"
    
    $cert = New-SelfSignedCertificate -CertStoreLocation "cert:\CurrentUser\My" -Subject "CN=hdinsightAdlsCert" -KeySpec KeyExchange
    $certBytes = $cert.Export([System.Security.Cryptography.X509Certificates.X509ContentType]::Pkcs12, $certPassword);
    $certString = [System.Convert]::ToBase64String($certBytes)
}
else
{

    Write-Host "Reading the cert file from path $certFilePath"

    $cert = new-object System.Security.Cryptography.X509Certificates.X509Certificate2($certFilePath, $certPassword)
    $certString = [System.Convert]::ToBase64String([System.IO.File]::ReadAllBytes($certFilePath))
}


Login-AzureRmAccount

if($addNewCertKeyCredential)
{
    Write-Host "Creating new KeyCredential for the app"

    $keyValue = [System.Convert]::ToBase64String($cert.GetRawCertData())
    
    New-AzureRmADAppCredential -ApplicationId $appId -CertValue $keyValue -EndDate $cert.NotAfter -StartDate $cert.NotBefore

    Write-Host "Waiting for 30 seconds for the permissions to get propagated"

    Start-Sleep -s 30
}

Select-AzureRmSubscription -SubscriptionId $subscriptionId

Write-Host "Updating the certificate on HDInsight cluster."

Invoke-AzureRmResourceAction `
    -ResourceGroupName $resourceGroupName `
    -ResourceType 'Microsoft.HDInsight/clusters' `
    -ResourceName $clusterName `
    -ApiVersion '2015-03-01-preview' `
    -Action 'updateclusteridentitycertificate' `
    -Parameters @{ ApplicationId = $appId.ToString(); Certificate = $certString; CertificatePassword = $certPassword.ToString() } `
    -Force

```

기존 인증서를 할당 하려면 인증서를 만들고 .pfx 파일 및 암호를 준비 합니다. 인증서를 클러스터가 생성 된 서비스 주체와 연결 하 고 AppId를 준비 합니다.

매개 변수를 실제 값으로 대체 한 후 PowerShell 명령을 실행 합니다.

## <a name="next-steps"></a>다음 단계

문제가 표시되지 않거나 문제를 해결할 수 없는 경우 다음 채널 중 하나를 방문하여 추가 지원을 받으세요.

* [Azure 커뮤니티 지원](https://azure.microsoft.com/support/community/)을 통해 Azure 전문가로부터 답변을 얻습니다.

* [@AzureSupport](https://twitter.com/azuresupport)(고객 환경을 개선하기 위한 공식 Microsoft Azure 계정)에 연결합니다. Azure 커뮤니티를 적절한 리소스(답변, 지원 및 전문가)에 연결합니다.

* 도움이 더 필요한 경우 [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)에서 지원 요청을 제출할 수 있습니다. 메뉴 모음에서 **지원**을 선택하거나 **도움말 + 지원** 허브를 엽니다. 자세한 내용은 [Azure 지원 요청을 만드는 방법](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)을 참조하세요. 구독 관리 및 청구 지원에 대한 액세스는 Microsoft Azure 구독에 포함되며 [Azure 지원 플랜](https://azure.microsoft.com/support/plans/) 중 하나를 통해 기술 지원이 제공됩니다.
