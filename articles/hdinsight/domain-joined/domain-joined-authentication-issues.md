---
title: Azure HDInsight의 인증 문제
description: Azure HDInsight의 인증 문제
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 08/24/2020
ms.openlocfilehash: 9c421626f62a4759ee7840b8c6d46f10d96b3d7c
ms.sourcegitcommit: 91fdedcb190c0753180be8dc7db4b1d6da9854a1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/17/2021
ms.locfileid: "112291442"
---
# <a name="authentication-issues-in-azure-hdinsight"></a>Azure HDInsight의 인증 문제

이 문서에서는 Azure HDInsight 클러스터와 상호 작용할 때 문제에 대한 문제 해결 단계 및 가능한 해결 방법을 설명합니다.

Azure Data Lake(Gen1 또는 Gen2)에 의해 지원되는 보안 클러스터에서 도메인 사용자가 HDI 게이트웨이를 통해 클러스터 서비스에 로그인하는 경우(예: Apache Ambari 포털에 로그인) HDI 게이트웨이에서 먼저 Azure AD(Azure Active Directory)로부터 OAuth 토큰을 가져오려고 시도한 다음, Azure AD DS에서 Kerberos 티켓을 가져옵니다. 이러한 단계 중 하나에서 인증이 실패할 수 있습니다. 이 문서에서는 이러한 문제 중 일부를 디버깅하는 방법을 설명합니다.

인증에 실패하면 자격 증명을 입력하라는 메시지가 표시됩니다. 이 대화 상자를 취소하면 오류 메시지가 출력됩니다. 다음은 몇 가지 일반적인 오류 메시지입니다.

## <a name="invalid_grant-or-unauthorized_client-50126"></a>invalid_grant 또는 unauthorized_client, 50126

### <a name="issue"></a>문제

페더레이션 사용자에 대한 로그인이 실패합니다(오류 코드 50126)(클라우드 사용자의 경우에는 로그인 성공). 오류 메시지는 다음과 유사합니다.

```
Reason: Bad Request, Detailed Response: {"error":"invalid_grant","error_description":"AADSTS70002: Error validating credentials. AADSTS50126: Invalid username or password\r\nTrace ID: 09cc9b95-4354-46b7-91f1-efd92665ae00\r\n Correlation ID: 4209bedf-f195-4486-b486-95a15b70fbe4\r\nTimestamp: 2019-01-28 17:49:58Z","error_codes":[70002,50126], "timestamp":"2019-01-28 17:49:58Z","trace_id":"09cc9b95-4354-46b7-91f1-efd92665ae00","correlation_id":"4209bedf-f195-4486-b486-95a15b70fbe4"}
```

### <a name="cause"></a>원인

Azure AD 오류 코드 50126은 `AllowCloudPasswordValidation` 정책이 테넌트에 의해 설정되지 않았음을 의미합니다.

### <a name="resolution"></a>해결 방법

Azure AD 테넌트의 전역 관리자가 Azure AD에서 ADFS 지원 사용자에 대해 암호 해시를 사용하도록 설정해야 합니다.  [HDInsight에서 Enterprise Security Package 사용](../domain-joined/apache-domain-joined-architecture.md) 문서에 표시된 대로 `AllowCloudPasswordValidationPolicy`를 적용합니다.

---

## <a name="invalid_grant-or-unauthorized_client-50034"></a>invalid_grant 또는 unauthorized_client, 50034

### <a name="issue"></a>문제

로그인이 실패합니다(오류 코드 50034). 오류 메시지는 다음과 유사합니다.

```
{"error":"invalid_grant","error_description":"AADSTS50034: The user account Microsoft.AzureAD.Telemetry.Diagnostics.PII does not exist in the 0c349e3f-1ac3-4610-8599-9db831cbaf62 directory. To sign into this application, the account must be added to the directory.\r\nTrace ID: bbb819b2-4c6f-4745-854d-0b72006d6800\r\nCorrelation ID: b009c737-ee52-43b2-83fd-706061a72b41\r\nTimestamp: 2019-04-29 15:52:16Z", "error_codes":[50034],"timestamp":"2019-04-29 15:52:16Z","trace_id":"bbb819b2-4c6f-4745-854d-0b72006d6800", "correlation_id":"b009c737-ee52-43b2-83fd-706061a72b41"}
```

### <a name="cause"></a>원인

사용자 이름이 잘못되었습니다(존재하지 않음). 사용자가 Azure Portal에 사용되는 것과 동일한 사용자 이름을 사용하고 있지 않습니다.

### <a name="resolution"></a>해결 방법

해당 포털에서 작동하는 것과 동일한 사용자 이름을 사용합니다.

---

## <a name="invalid_grant-or-unauthorized_client-50053"></a>invalid_grant 또는 unauthorized_client, 50053

### <a name="issue"></a>문제

사용자 계정이 잠겨 있습니다(오류 코드 50053). 오류 메시지는 다음과 유사합니다.

```
{"error":"unauthorized_client","error_description":"AADSTS50053: You've tried to sign in too many times with an incorrect user ID or password.\r\nTrace ID: 844ac5d8-8160-4dee-90ce-6d8c9443d400\r\nCorrelation ID: 23fe8867-0e8f-4e56-8764-0cdc7c61c325\r\nTimestamp: 2019-06-06 09:47:23Z","error_codes":[50053],"timestamp":"2019-06-06 09:47:23Z","trace_id":"844ac5d8-8160-4dee-90ce-6d8c9443d400","correlation_id":"23fe8867-0e8f-4e56-8764-0cdc7c61c325"}
```

### <a name="cause"></a>원인

잘못된 암호를 사용하여 로그인을 너무 많이 시도했습니다.

### <a name="resolution"></a>해결 방법

30분 정도 기다립니다. 인증을 시도할 수 있는 애플리케이션을 모두 중지합니다.

---

## <a name="invalid_grant-or-unauthorized_client-50053-2"></a>invalid_grant 또는 unauthorized_client, 50053 (#2)

### <a name="issue"></a>문제

암호가 만료되었습니다(오류 코드 50053). 오류 메시지는 다음과 유사합니다.

```
{"error":"user_password_expired","error_description":"AADSTS50055: Password is expired.\r\nTrace ID: 241a7a47-e59f-42d8-9263-fbb7c1d51e00\r\nCorrelation ID: c7fe4a42-67e4-4acd-9fb6-f4fb6db76d6a\r\nTimestamp: 2019-06-06 17:29:37Z","error_codes":[50055],"timestamp":"2019-06-06 17:29:37Z","trace_id":"241a7a47-e59f-42d8-9263-fbb7c1d51e00","correlation_id":"c7fe4a42-67e4-4acd-9fb6-f4fb6db76d6a","suberror":"user_password_expired","password_change_url":"https://portal.microsoftonline.com/ChangePassword.aspx"}
```

### <a name="cause"></a>원인

암호가 만료되었습니다.

### <a name="resolution"></a>해결 방법

온-프레미스 시스템에서 Azure Portal의 암호를 변경한 다음, 동기화가 완료될 때까지 30분 동안 기다립니다.

---

## <a name="interaction_required"></a>interaction_required

### <a name="issue"></a>문제

`interaction_required` 오류 메시지를 수신합니다.

### <a name="cause"></a>원인

조건부 액세스 정책 또는 MFA가 사용자에게 적용되고 있습니다. 대화형 인증은 아직 지원되지 않으므로 MFA/조건부 액세스에서 사용자 또는 클러스터를 제외해야 합니다. 클러스터(IP 주소 기반 예외 정책)를 제외하도록 선택한 경우 해당 vnet에 대해 AD `ServiceEndpoints`를 사용하도록 설정했는지 확인합니다.

### <a name="resolution"></a>해결 방법

[Azure Active Directory Domain Services를 사용하여 Enterprise Security Package로 HDInsight 클러스터 구성](./apache-domain-joined-configure-using-azure-adds.md)에 표시된 대로 조건부 액세스 정책을 사용하고 MFA에서 HDInisght 클러스터를 제외합니다.

---

## <a name="sign-in-denied"></a>로그인 거부됨

### <a name="issue"></a>문제

로그인이 거부되었습니다.

### <a name="cause"></a>원인

이 단계로 이동하는 경우 OAuth 인증 문제는 없지만 Kerberos 인증 문제가 있는 것입니다. 이 클러스터가 ADLS에서 지원되는 경우 Kerberos 인증을 시도하기 전에 OAuth 로그인이 성공했습니다. WASB 클러스터에서는 OAuth 로그인이 시도되지 않았습니다. 암호 해시가 동기화되지 않아서 사용자 계정이 Azure AD DS에서 잠기는 경우와 같이 여러 가지 Kerberos 오류가 발생할 수 있습니다. 암호 해시는 사용자가 암호를 변경하는 경우에만 동기화됩니다. Azure AD DS 인스턴스를 만들 때 인스턴스 생성 후 변경된 암호의 동기화가 시작됩니다. 동기화가 시작되기 전에 설정된 암호는 소급되지 않습니다.

### <a name="resolution"></a>해결 방법

암호가 동기화되지 않은 것 같다고 생각되면 암호를 변경하고 몇 분 후에 동기화해 보세요.

SSH를 시도합니다. 도메인에 가입된 컴퓨터에서 동일한 사용자 자격 증명을 사용하여 인증(kinit)을 시도해야 합니다. 로컬 사용자로 헤드/에지 노드에 대한 SSH를 실행한 다음, kinit를 실행합니다.

---

## <a name="kinit-fails"></a>kinit 실패

### <a name="issue"></a>문제

Kinit가 실패합니다.

### <a name="cause"></a>원인

다양함

### <a name="resolution"></a>해결 방법

Kinit가 성공하려면 `sAMAccountName`(영역이 없는 짧은 계정 이름)을 알아야 합니다. `sAMAccountName`는 일반적으로 `bob@contoso.com`의 bob과 같은 계정 접두사입니다. 일부 사용자의 경우 상황이 다를 수 있습니다. `sAMAccountName`을 알아보려면 디렉터리를 찾아보거나 검색하는 기능이 필요합니다.

`sAMAccountName` 검색 방법:

* 로컬 Ambari admin을 사용하여 Ambari에 로그인할 수 있는 경우 사용자 목록을 확인합니다.

* 도메인에 가입된 [Windows 컴퓨터](../../active-directory-domain-services/tutorial-create-management-vm.md)를 사용하는 경우 표준 Windows AD 도구를 사용하여 찾아볼 수 있습니다. 이렇게 하려면 도메인의 작업 계정이 필요합니다.

* 헤드 노드에서는 SAMBA 명령을 사용하여 검색할 수 있습니다. 이를 위해서는 유효한 Kerberos 세션(성공한 kinit)이 필요합니다. net ads search "(userPrincipalName=bob*)"

    검색/찾아보기 결과에 `sAMAccountName` 특성이 표시됩니다. 또한, `pwdLastSet`, `badPasswordTime`, `userPrincipalName` 등의 다른 특성도 확인하여 이러한 속성이 기대한 속성과 일치하는지 확인할 수 있습니다.

---

## <a name="kinit-fails-with-preauthentication-failure"></a>kinit가 사전 인증 실패로 실패함

### <a name="issue"></a>문제

`Preauthentication` 실패와 함께 Kinit가 실패합니다.

### <a name="cause"></a>원인

잘못된 사용자 이름 또는 암호입니다.

### <a name="resolution"></a>해결 방법

사용자 이름과 암호를 확인합니다. 또한 위에서 설명한 다른 속성도 확인합니다. 자세한 디버깅을 사용하려면 kinit를 시도하기 전에 세션에서 `export KRB5_TRACE=/tmp/krb.log`를 실행합니다.

---

## <a name="job--hdfs-command-fails-due-to-tokennotfoundexception"></a>TokenNotFoundException으로 인해 작업/HDFS 명령이 실패합니다.

### <a name="issue"></a>문제

`TokenNotFoundException`으로 인해 작업/HDFS 명령이 실패합니다.

### <a name="cause"></a>원인

작업/명령이 성공하기 위해 필요한 OAuth 액세스 토큰을 찾을 수 없습니다. ADLS/ABFS 드라이버는 스토리지 요청을 만들기 전에 자격 증명 서비스에서 OAuth 액세스 토큰을 검색합니다. 이 토큰은 동일한 사용자를 사용하여 Ambari 포털에 로그인할 때 등록됩니다.

### <a name="resolution"></a>해결 방법

작업을 실행하는 데 사용되는 ID의 사용자 이름을 통해 Ambari 포털에 성공적으로 로그인한 적이 있는지 확인합니다.

---

## <a name="error-fetching-access-token"></a>액세스 토큰을 가져오는 동안 오류 발생

### <a name="issue"></a>문제

사용자가 `Error fetching access token` 오류 메시지를 받습니다.

### <a name="cause"></a>원인

사용자가 ACL을 사용하여 ADLS Gen2에 액세스하려고 하는데 Kerberos 토큰이 만료된 경우 이 오류가 간헐적으로 발생합니다.

### <a name="resolution"></a>해결 방법

* Azure Data Lake Storage Gen1의 경우 브라우저 캐시를 정리하고 다시 Ambari에 로그인합니다.

* Azure Data Lake Storage Gen2의 경우 현재 로그인을 시도하고 있는 사용자에 대해 `/usr/lib/hdinsight-common/scripts/RegisterKerbTicketAndOAuth.sh <upn>`을 실행합니다.

---

## <a name="next-steps"></a>다음 단계

[!INCLUDE [troubleshooting next steps](../includes/hdinsight-troubleshooting-next-steps.md)]