---
title: Azure HDInsight의 인증 문제
description: Azure HDInsight의 인증 문제
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 11/08/2019
ms.openlocfilehash: 26eec9cdd327ceb51e72deb1d6f40d585ce368fb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75896131"
---
# <a name="authentication-issues-in-azure-hdinsight"></a>Azure HDInsight의 인증 문제

이 문서에서는 Azure HDInsight 클러스터와 상호 작용할 때 문제에 대한 문제 해결 단계 및 가능한 해결 에 대해 설명합니다.

도메인 사용자가 HDI 게이트웨이를 통해 클러스터 서비스에 로그인할 때 Azure Data Lake(Gen1 또는 Gen2)가 지원하는 보안 클러스터에서 HDI 게이트웨이는 Azure Active Directory(Azure AD)에서 OAuth 토큰을 먼저 얻으려고 시도합니다. 을 따르면 Azure AD DS에서 Kerberos 티켓을 받습니다. 이러한 단계 중 하나에서 인증이 실패할 수 있습니다. 이 문서는 이러한 문제 중 일부를 디버깅하기 위한 것입니다.

인증에 실패하면 자격 증명에 대한 메시지가 표시됩니다. 이 대화 상자를 취소하면 오류 메시지가 인쇄됩니다. 다음은 몇 가지 일반적인 오류 메시지입니다.

## <a name="invalid_grant-or-unauthorized_client-50126"></a>invalid_grant 또는 unauthorized_client, 50126

### <a name="issue"></a>문제

오류 코드 50126(클라우드 사용자의 로그인 성공)을 통해 페더레이션된 사용자의 로그인에 실패합니다. 오류 메시지는 다음과 유사합니다.

```
Reason: Bad Request, Detailed Response: {"error":"invalid_grant","error_description":"AADSTS70002: Error validating credentials. AADSTS50126: Invalid username or password\r\nTrace ID: 09cc9b95-4354-46b7-91f1-efd92665ae00\r\n Correlation ID: 4209bedf-f195-4486-b486-95a15b70fbe4\r\nTimestamp: 2019-01-28 17:49:58Z","error_codes":[70002,50126], "timestamp":"2019-01-28 17:49:58Z","trace_id":"09cc9b95-4354-46b7-91f1-efd92665ae00","correlation_id":"4209bedf-f195-4486-b486-95a15b70fbe4"}
```

### <a name="cause"></a>원인

Azure AD 오류 코드 50126은 테넌트에서 `AllowCloudPasswordValidation` 정책을 설정하지 않음을 의미합니다.

### <a name="resolution"></a>해결 방법

Azure AD 테넌트의 회사 관리자는 Azure AD가 ADFS 백업 사용자에 대한 암호 해시를 사용하도록 설정해야 합니다.  `AllowCloudPasswordValidationPolicy` [HDInsight에서 엔터프라이즈 보안 패키지 사용](../domain-joined/apache-domain-joined-architecture.md)문서에 표시된 대로 적용합니다.

---

## <a name="invalid_grant-or-unauthorized_client-50034"></a>invalid_grant 또는 unauthorized_client, 50034

### <a name="issue"></a>문제

오류 코드 50034로 로그인에 실패합니다. 오류 메시지는 다음과 유사합니다.

```
{"error":"invalid_grant","error_description":"AADSTS50034: The user account Microsoft.AzureAD.Telemetry.Diagnostics.PII does not exist in the 0c349e3f-1ac3-4610-8599-9db831cbaf62 directory. To sign into this application, the account must be added to the directory.\r\nTrace ID: bbb819b2-4c6f-4745-854d-0b72006d6800\r\nCorrelation ID: b009c737-ee52-43b2-83fd-706061a72b41\r\nTimestamp: 2019-04-29 15:52:16Z", "error_codes":[50034],"timestamp":"2019-04-29 15:52:16Z","trace_id":"bbb819b2-4c6f-4745-854d-0b72006d6800", "correlation_id":"b009c737-ee52-43b2-83fd-706061a72b41"}
```

### <a name="cause"></a>원인

사용자 이름이 올바르지 않습니다(존재하지 않음). 사용자가 Azure Portal에서 사용되는 것과 동일한 사용자 이름을 사용하고 있지 않습니다.

### <a name="resolution"></a>해결 방법

해당 포털에서 작동하는 것과 동일한 사용자 이름을 사용합니다.

---

## <a name="invalid_grant-or-unauthorized_client-50053"></a>invalid_grant 또는 unauthorized_client, 50053

### <a name="issue"></a>문제

사용자 계정이 잠겨 있으며 오류 코드 50053입니다. 오류 메시지는 다음과 유사합니다.

```
{"error":"unauthorized_client","error_description":"AADSTS50053: You've tried to sign in too many times with an incorrect user ID or password.\r\nTrace ID: 844ac5d8-8160-4dee-90ce-6d8c9443d400\r\nCorrelation ID: 23fe8867-0e8f-4e56-8764-0cdc7c61c325\r\nTimestamp: 2019-06-06 09:47:23Z","error_codes":[50053],"timestamp":"2019-06-06 09:47:23Z","trace_id":"844ac5d8-8160-4dee-90ce-6d8c9443d400","correlation_id":"23fe8867-0e8f-4e56-8764-0cdc7c61c325"}
```

### <a name="cause"></a>원인

잘못된 암호를 사용하면 로그인시도가 너무 많습니다.

### <a name="resolution"></a>해결 방법

30분 정도 기다린 후 인증하려는 응용 프로그램을 중지합니다.

---

## <a name="invalid_grant-or-unauthorized_client-50053"></a>invalid_grant 또는 unauthorized_client, 50053

### <a name="issue"></a>문제

암호가 만료됨 오류 코드 50053. 오류 메시지는 다음과 유사합니다.

```
{"error":"user_password_expired","error_description":"AADSTS50055: Password is expired.\r\nTrace ID: 241a7a47-e59f-42d8-9263-fbb7c1d51e00\r\nCorrelation ID: c7fe4a42-67e4-4acd-9fb6-f4fb6db76d6a\r\nTimestamp: 2019-06-06 17:29:37Z","error_codes":[50055],"timestamp":"2019-06-06 17:29:37Z","trace_id":"241a7a47-e59f-42d8-9263-fbb7c1d51e00","correlation_id":"c7fe4a42-67e4-4acd-9fb6-f4fb6db76d6a","suberror":"user_password_expired","password_change_url":"https://portal.microsoftonline.com/ChangePassword.aspx"}
```

### <a name="cause"></a>원인

암호가 만료되었습니다.

### <a name="resolution"></a>해결 방법

온-프레미스 시스템에서 Azure 포털의 암호를 변경한 다음 동기화가 따라잡을 때까지 30분 동안 기다립니다.

---

## <a name="interaction_required"></a>interaction_required

### <a name="issue"></a>문제

오류 메시지를 `interaction_required`수신합니다.

### <a name="cause"></a>원인

조건부 액세스 정책 또는 MFA가 사용자에게 적용되고 있습니다. 대화형 인증은 아직 지원되지 않으므로 MFA/조건부 액세스에서 사용자 또는 클러스터를 제외해야 합니다. 클러스터(IP 주소 기반 면제 정책)를 제외하도록 선택한 경우 `ServiceEndpoints` 해당 vnet에 대해 AD가 활성화되어 있는지 확인합니다.

### <a name="resolution"></a>해결 방법

조건부 액세스 정책을 사용하고 [Azure Active Directory 도메인 서비스를 사용하여 엔터프라이즈 보안 패키지로 HDInsight 클러스터 구성에](./apache-domain-joined-configure-using-azure-adds.md)표시된 대로 MFA에서 HDInisght 클러스터를 제외합니다.

---

## <a name="sign-in-denied"></a>거부됨

### <a name="issue"></a>문제

로그인이 거부되었습니다.

### <a name="cause"></a>원인

이 단계에 들어가려면 OAuth 인증은 문제가 되지 않지만 Kerberos 인증은 문제가 되지 않습니다. 이 클러스터가 ADLS에 의해 지원되는 경우 Kerberos 인증이 시도되기 전에 OAuth 로그인이 성공했습니다. WASB 클러스터에서 OAuth 로그인을 시도하지 않습니다. Kerberos 실패에 대 한 많은 이유가 있을 수 있습니다-암호 해시 동기화 되지 않은 처럼, Azure AD DS에서 잠긴 사용자 계정, 등등. 암호 해시는 사용자가 암호를 변경하는 경우에만 동기화됩니다. Azure AD DS 인스턴스를 만들면 생성 후 변경된 암호 동기화가 시작됩니다. 시작 전에 설정된 암호는 소급하여 동기화되지 않습니다.

### <a name="resolution"></a>해결 방법

암호가 동기화되지 않을 수 있다고 생각되면 암호를 변경하고 동기화될 때까지 몇 분 동안 기다립니다.

SSH를 SSH로 시도하여 도메인에 조인된 컴퓨터에서 동일한 사용자 자격 증명을 사용하여 (kinit)을 인증해야 합니다. 로컬 사용자와 헤드 / 에지 노드에 SSH를 입력 한 다음 kinit을 실행합니다.

---

## <a name="kinit-fails"></a>키이트 실패

### <a name="issue"></a>문제

키릿이 실패합니다.

### <a name="cause"></a>원인

다양함

### <a name="resolution"></a>해결 방법

kinit이 성공하려면(영역이 없는 `sAMAccountName` 짧은 계정 이름) 알 필요가 있습니다. `sAMAccountName`는 일반적으로 계정 접두사입니다(예: bob in). `bob@contoso.com` 일부 사용자에 대 한, 그것은 다를 수 있습니다. 을 학습하기 위해 디렉토리를 찾아보거나 `sAMAccountName`검색할 수 있는 기능이 필요합니다.

찾는 `sAMAccountName`방법 :

* 로컬 Ambari 관리자를 사용하여 Ambari에 로그인할 수 있는 경우 사용자 목록을 참조하세요.

* [도메인에 가입된 Windows 컴퓨터가](../../active-directory-domain-services/manage-domain.md)있는 경우 표준 Windows AD 도구를 사용하여 찾아볼 수 있습니다. 이렇게 하려면 도메인에서 작업 계정이 필요합니다.

* 헤드 노드에서 SAMBA 명령을 사용하여 검색할 수 있습니다. 이를 위해서는 유효한 Kerberos 세션(성공적인 kinit)이 필요합니다. 순 광고 검색 "(사용자 PrincipalName=밥*)"

    검색 /찾아보기 결과에 특성이 `sAMAccountName` 표시되어야 합니다. 또한 . `pwdLastSet` `badPasswordTime`등과 `userPrincipalName` 같은 다른 속성을 보고 해당 속성이 예상과 일치하는지 확인할 수 있습니다.

---

## <a name="kinit-fails-with-preauthentication-failure"></a>사전 인증 실패로 kinit 실패

### <a name="issue"></a>문제

Kinit이 `Preauthentication` 실패하면 실패합니다.

### <a name="cause"></a>원인

잘못된 사용자 이름 또는 암호.

### <a name="resolution"></a>해결 방법

사용자 이름과 암호를 확인합니다. 위에서 설명한 다른 속성도 확인하십시오. 자세한 디버깅을 사용하려면 `export KRB5_TRACE=/tmp/krb.log` kinit을 시도하기 전에 세션에서 실행합니다.

---

## <a name="job--hdfs-command-fails-due-to-tokennotfoundexception"></a>작업 / HDFS 명령으로 인해 실패 TokenNotException

### <a name="issue"></a>문제

작업 / HDFS 명령으로 인해 `TokenNotFoundException`실패합니다.

### <a name="cause"></a>원인

필요한 OAuth 액세스 토큰이 작업 /명령이 성공하기 위해 찾을 수 없습니다. ADLS / ABFS 드라이버는 저장소 요청을하기 전에 자격 증명 서비스에서 OAuth 액세스 토큰을 검색하려고 시도합니다. 이 토큰은 동일한 사용자를 사용하여 Ambari 포털에 로그인할 때 등록됩니다.

### <a name="resolution"></a>해결 방법

작업을 실행하는 데 ID가 사용되는 사용자 이름을 통해 Ambari 포털에 한 번 성공적으로 로그인한 적이 있는지 확인합니다.

---

## <a name="error-fetching-access-token"></a>액세스 토큰 오류 가져오기

### <a name="issue"></a>문제

사용자가 오류 `Error fetching access token`메시지를 받습니다.

### <a name="cause"></a>원인

이 오류는 사용자가 ACL을 사용하여 ADLS Gen2에 액세스하려고 시도하고 Kerberos 토큰이 만료될 때 간헐적으로 발생합니다.

### <a name="resolution"></a>해결 방법

* Azure Data Lake Storage Gen1의 경우 브라우저 캐시를 정리하고 Ambari에 다시 로그인합니다.

* Azure Data Lake 저장소 Gen2의 경우 사용자가 로그인하려는 사용자에 대해 실행합니다. `/usr/lib/hdinsight-common/scripts/RegisterKerbWithOauth.sh <upn>`

---

## <a name="next-steps"></a>다음 단계

문제가 표시되지 않거나 문제를 해결할 수 없는 경우 다음 채널 중 하나를 방문하여 추가 지원을 받으세요.

* Azure 커뮤니티 지원을 통해 Azure 전문가의 답변을 얻을 [수 있습니다.](https://azure.microsoft.com/support/community/)

* 연결 [@AzureSupport](https://twitter.com/azuresupport) - 고객 경험을 개선하기 위한 공식 Microsoft Azure 계정입니다. Azure 커뮤니티를 올바른 리소스( 답변, 지원 및 전문가)에 연결합니다.

* 추가 도움이 필요한 경우 [Azure 포털](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)에서 지원 요청을 제출할 수 있습니다. 메뉴 모음에서 **지원을** 선택하거나 도움말 + 지원 허브를 **엽니다.** 자세한 내용은 Azure [지원 요청을 만드는 방법을](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)검토합니다. 구독 관리 및 청구 지원에 대한 액세스는 Microsoft Azure 구독에 포함되며 기술 지원은 [Azure 지원 계획](https://azure.microsoft.com/support/plans/)중 하나를 통해 제공됩니다.
