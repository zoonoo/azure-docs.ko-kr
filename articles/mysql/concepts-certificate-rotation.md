---
title: Azure Database for MySQL에 대 한 인증서 회전
description: 영향을 받는 루트 인증서 변경의 예정 된 변경 내용에 대해 알아봅니다 Azure Database for MySQL
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: conceptual
ms.date: 01/18/2021
ms.openlocfilehash: 23fa3e93565066ce4b897bffe63164486efc179e
ms.sourcegitcommit: f6193c2c6ce3b4db379c3f474fdbb40c6585553b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/08/2021
ms.locfileid: "102449886"
---
# <a name="understanding-the-changes-in-the-root-ca-change-for-azure-database-for-mysql-single-server"></a>Azure Database for MySQL 단일 서버에 대 한 루트 CA 변경 내용 이해

단일 서버 Azure Database for MySQL 표준 유지 관리 및 보안 모범 사례에서 **2021 (02/15/2021)** 의 루트 인증서 변경을 완료 했습니다. 이 문서에서는 변경 내용, 영향을 받는 리소스 및 응용 프로그램이 데이터베이스 서버에 대 한 연결을 유지 관리 하는 데 필요한 단계에 대 한 자세한 정보를 제공 합니다.

> [!NOTE]
> 이 문서는 [Azure Database for MySQL 단일 서버](single-server-overview.md) 에만 적용 됩니다. [Azure Database for MySQL 유연한 서버의](flexible-server/overview.md)경우 SSL을 통해 통신 하는 데 필요한 인증서는 [DIGICERT Global Root CA](https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem) 입니다.
> 
> 이 문서에는 Microsoft에서 더 이상 사용 하지 않는 용어 _종속_ 용어에 대 한 참조가 포함 되어 있습니다. 소프트웨어에서 용어가 제거되면 이 문서에서 해당 용어가 제거됩니다.
>

## <a name="why-root-certificate-update-is-required"></a>루트 인증서 업데이트가 필요한 이유는 무엇 인가요?

Azure database for MySQL 사용자는 미리 정의 된 인증서를 사용 하 여 해당 MySQL 서버에 연결할 수 있습니다 .이는 [여기](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem)에 있습니다. 그러나 ca [(인증 기관) 브라우저 포럼](https://cabforum.org/)은   ca 공급 업체에서 발급 한 여러 인증서의 보고서를 준수 하지 않는 것으로 게시 했습니다.

업계의 규정 준수 요구 사항에 따라, CA 공급 업체는 비규격 ca에 대 한 CA 인증서를 해지 하기 시작 하 고, 호환 Ca에서 발급 한 인증서를 서버에 사용 하 고, 해당 규격 Ca의 CA 인증서로 서명 합니다. 이러한 비호환 인증서 중 하나를 사용 Azure Database for MySQL 있으므로 MySQL 서버에 대 한 잠재적인 위협을 최소화 하기 위해 인증서를 규격 버전으로 회전 해야 합니다.

새 인증서가 롤아웃 되 고 2 월 15 일에 2021 (02/15/2021)부터 적용 됩니다. 

## <a name="what-change-was-performed-on-february-15-2021-02152021"></a>02/15/2021 2021 년 2 월 15 일에 수행 된 변경 내용

2021 년 2 월 15 일, 기존 고객이 아무것도 변경할 필요가 없고 서버에 대 한 연결에 영향을 주지 않도록 [baltimorecybertrustroot.crt.pem 루트 인증서](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem) 가 동일한 [baltimorecybertrustroot.crt.pem 루트 인증서](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem) 의 **호환 버전** 으로 대체 되었습니다. 이러한 변경을 수행 하는 동안 [baltimorecybertrustroot.crt.pem 루트 인증서](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem) 가 [DigiCertGlobalRootG2](https://cacerts.digicert.com/DigiCertGlobalRootG2.crt.pem) 로 **대체 되지** 않으며 고객이 변경을 수행 하는 데 더 많은 시간을 허용 하기 위해 변경 내용이 지연 됩니다.

## <a name="do-i-need-to-make-any-changes-on-my-client-to-maintain-connectivity"></a>클라이언트에서 연결을 유지 하는 데 필요한 변경 작업을 수행 해야 하나요?

클라이언트 쪽에는 변경이 필요 하지 않습니다. 아래의 이전 권장 사항을 따르는 경우 결합 된 CA 인증서에서 **baltimorecybertrustroot.crt.pem 인증서가 제거 되지 않는** 한 계속 연결할 수 있습니다. **연결을 유지 하기 위해 추가 공지가 있을 때까지 결합 된 CA 인증서에서 Baltimorecybertrustroot.crt.pem을 제거 하지 않는 것이 좋습니다.**

### <a name="previous-recommendation"></a>이전 권장 사항

인증서가 예기치 않게 해지 되거나 해지 된 인증서를 업데이트 하기 때문에 응용 프로그램의 가용성이 중단 되지 않도록 하려면 다음 단계를 사용 합니다. 현재 인증서와 새 pem 파일을 결합 하는 새 *pem* 파일을 만들고 SSL 인증서 유효성 검사 중에 허용 되는 값 중 하나를 사용 하는 것이 좋습니다. 다음 단계를 참조 하세요.

* 다음 링크에서 Baltimorecybertrustroot.crt.pem & DigiCertGlobalRootG2 Root CA를 다운로드 합니다.

  * [https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem)
  * [https://cacerts.digicert.com/DigiCertGlobalRootG2.crt.pem](https://cacerts.digicert.com/DigiCertGlobalRootG2.crt.pem)

* **Baltimorecybertrustroot.crt.pem** 및 **DigiCertGlobalRootG2** 인증서를 모두 포함 하는 결합 된 CA 인증서 저장소를 생성 합니다.

  * Java (MySQL 커넥터/J) 사용자의 경우 다음을 실행 합니다.

    ```console
    keytool -importcert -alias MySQLServerCACert -file D:\BaltimoreCyberTrustRoot.crt.pem -keystore truststore -storepass password -noprompt
    ```

    ```console
    keytool -importcert -alias MySQLServerCACert2 -file D:\DigiCertGlobalRootG2.crt.pem -keystore truststore -storepass password -noprompt
    ```

    그런 다음 원래 키 저장소 파일을 새로 생성 된 파일로 바꿉니다.

    * System.object ("javax. trustStore", "path_to_truststore_file");
    * System.object ("javax (Storepassword", "password");

  * .NET (MySQL 커넥터/NET, MySQLConnector) 사용자의 경우 **baltimorecybertrustroot.crt.pem** 및 **DigiCertGlobalRootG2** 이 모두 Windows 인증서 저장소, 신뢰할 수 있는 루트 인증 기관에 존재 하는지 확인 합니다. 인증서가 없는 경우 누락 된 인증서를 가져옵니다.

    :::image type="content" source="media/overview/netconnecter-cert.png" alt-text="Azure Database for MySQL .net cert 다이어그램":::

  * SSL_CERT_DIR를 사용 하는 Linux의 .NET 사용자의 경우, SSL_CERT_DIR에 표시 된 디렉터리에 **baltimorecybertrustroot.crt.pem** 및 **DigiCertGlobalRootG2** 가 모두 있는지 확인 합니다. 인증서가 없는 경우 누락 된 인증서 파일을 만듭니다.

  * 다른 (MySQL 클라이언트/MySQL 워크 벤치/C/c + +/Go/Python/Ruby/PHP/NodeJS/Perl/Swift) 사용자의 경우 두 가지 CA 인증서 파일을 다음과 같은 형식으로 병합할 수 있습니다.

      ```
      -----BEGIN CERTIFICATE-----
      (Root CA1: BaltimoreCyberTrustRoot.crt.pem)
      -----END CERTIFICATE-----
      -----BEGIN CERTIFICATE-----
      (Root CA2: DigiCertGlobalRootG2.crt.pem)
      -----END CERTIFICATE-----
      ```

* 원래 루트 CA pem 파일을 결합 된 루트 CA 파일로 바꾸고 응용 프로그램/클라이언트를 다시 시작 합니다.
* 나중에 서버 쪽에 새 인증서를 배포한 후에는 CA pem 파일을 DigiCertGlobalRootG2로 변경할 수 있습니다.

> [!NOTE]
> 인증서가 변경 될 때까지 **Baltimore certificate** 를 삭제 하거나 변경 하지 마세요. 변경이 완료 되 면 통신이 전송 되며, 그 후에는 Baltimore 인증서를 삭제 하는 것이 안전 합니다. 

## <a name="why-was-baltimorecybertrustroot-certificate-not-replaced-to-digicertglobalrootg2-during-this-change-on-february-15-2021"></a>Baltimorecybertrustroot.crt.pem 인증서가 2021 년 2 월 15 일에 변경 되는 동안 DigiCertGlobalRootG2로 대체 되지 않는 이유는 무엇 인가요?

이러한 변경에 대 한 고객의 준비를 평가 하 고 많은 고객이이 변경을 관리 하기 위한 추가 리드 시간을 찾고 있습니다. 고객의 준비를 위해 고객에 게 더 많은 리드 시간을 제공 하는 것은 고객 및 최종 사용자에 게 충분 한 리드 시간을 제공 하는 최소 1 년 동안 DigiCertGlobalRootG2로 인증서 변경을 지연 하기로 결정 했습니다. 

사용자에 대 한 권장 사항은 앞에서 설명한 단계를 사용 하 여 결합 된 인증서를 만들고 서버에 연결 하지만,이를 제거 하는 통신을 보낼 때까지 Baltimorecybertrustroot.crt.pem 인증서를 제거 하지 않습니다. 

## <a name="what-if-we-removed-the-baltimorecybertrustroot-certificate"></a>Baltimorecybertrustroot.crt.pem 인증서를 제거 하면 어떻게 되나요?

Azure Database for MySQL 서버에 연결 하는 동안 연결 오류가 시작 됩니다. 연결을 유지 하려면 [baltimorecybertrustroot.crt.pem](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem) certificate를 사용 하 여 SSL을 다시 [구성](howto-configure-ssl.md) 해야 합니다.


## <a name="frequently-asked-questions"></a>질문과 대답

### <a name="1-if-im-not-using-ssltls-do-i-still-need-to-update-the-root-ca"></a>1. SSL/TLS를 사용 하지 않는 경우 여전히 루트 CA를 업데이트 해야 하나요?

  SSL/TLS를 사용 하지 않는 경우에는 작업이 필요 하지 않습니다.

### <a name="2-if-im-using-ssltls-do-i-need-to-restart-my-database-server-to-update-the-root-ca"></a>2. SSL/TLS를 사용 하는 경우에는 데이터베이스 서버를 다시 시작 하 여 루트 CA를 업데이트 해야 하나요?

아니요, 새 인증서 사용을 시작 하기 위해 데이터베이스 서버를 다시 시작 하지 않아도 됩니다. 이 루트 인증서는 클라이언트 쪽 변경 이며 들어오는 클라이언트 연결은 새 인증서를 사용 하 여 데이터베이스 서버에 연결할 수 있는지 확인 해야 합니다.

### <a name="3-how-do-i-know-if-im-using-ssltls-with-root-certificate-verification"></a>3. 루트 인증서 인증을 사용 하는 SSL/TLS를 사용 하 고 있는지 확인 어떻게 할까요??

연결 문자열을 검토 하 여 연결에서 루트 인증서를 확인 하는지 여부를 식별할 수 있습니다.

- 연결 문자열에 `sslmode=verify-ca` 또는가 포함 `sslmode=verify-identity` 된 경우 인증서를 업데이트 해야 합니다.
- 연결 문자열에,, 또는가 포함 되어 있으면 `sslmode=disable` `sslmode=allow` `sslmode=prefer` `sslmode=require` 인증서를 업데이트할 필요가 없습니다.
- 연결 문자열이 sslmode를 지정 하지 않는 경우 인증서를 업데이트할 필요가 없습니다.

연결 문자열을 추상화 하는 클라이언트를 사용 하는 경우 클라이언트의 설명서를 검토 하 여 인증서를 확인 하는지 여부를 파악 합니다.

### <a name="4-what-is-the-impact-if-using-app-service-with-azure-database-for-mysql"></a>4. Azure Database for MySQL에서 App Service를 사용 하는 경우 미치는 영향은 무엇입니까?

Azure Database for MySQL에 연결 하는 Azure app services의 경우 두 가지 시나리오와 응용 프로그램에서 SSL을 사용 하는 방법에 따라 두 가지 시나리오를 사용할 수 있습니다.

* 이 새 인증서는 플랫폼 수준에서 App Service에 추가 되었습니다. 응용 프로그램의 App Service 플랫폼에 포함 된 SSL 인증서를 사용 하는 경우 아무 작업도 필요 하지 않습니다. 가장 일반적인 시나리오입니다. 
* 코드에 SSL 인증서 파일의 경로를 명시적으로 포함 하는 경우에는 새 인증서를 다운로드 하 여 위에서 설명한 대로 결합 된 인증서를 생성 하 고 인증서 파일을 사용 해야 합니다. 이 시나리오의 좋은 예는 [App Service 설명서](../app-service/tutorial-multi-container-app.md#configure-database-variables-in-wordpress)에서 공유 된 App Service의 사용자 지정 컨테이너를 사용 하는 경우입니다. 이는 드문 경우 지만이를 사용 하는 일부 사용자에 대해 살펴보았습니다.

### <a name="5-what-is-the-impact-if-using-azure-kubernetes-services-aks-with-azure-database-for-mysql"></a>5. Azure Database for MySQL에서 AKS (Azure Kubernetes Services)를 사용 하는 경우 미치는 영향은 무엇 인가요?

AKS (Azure Kubernetes Services)를 사용 하 여 Azure Database for MySQL에 연결 하려는 경우 전용 고객 호스트 환경에서 액세스 하는 것과 유사 합니다. [여기](../aks/ingress-own-tls.md)에서 단계를 참조 하세요.

### <a name="6-what-is-the-impact-if-using-azure-data-factory-to-connect-to-azure-database-for-mysql"></a>6. Azure Data Factory를 사용 하 여 Azure Database for MySQL에 연결 하는 경우 미치는 영향은 무엇입니까?

Azure Integration Runtime를 사용 하는 커넥터의 경우 커넥터는 Azure에서 호스팅되는 환경의 Windows 인증서 저장소에서 인증서를 활용 합니다. 이러한 인증서는 새로 적용 된 인증서와 이미 호환 되므로 아무런 조치도 필요 하지 않습니다.

연결 문자열에 SSL 인증서 파일의 경로를 명시적으로 포함 하는 자체 호스팅 Integration Runtime를 사용 하는 커넥터의 경우 [새 인증서](https://cacerts.digicert.com/DigiCertGlobalRootG2.crt.pem) 를 다운로드 하 고이를 사용 하려면 연결 문자열을 업데이트 해야 합니다.

### <a name="7-do-i-need-to-plan-a-database-server-maintenance-downtime-for-this-change"></a>7 .이 변경에 대 한 데이터베이스 서버 유지 관리 가동 중지 시간을 계획 해야 하나요?

아니요. 여기서 변경 내용은 데이터베이스 서버에 연결 하기 위해 클라이언트 쪽에만 해당 되므로 데이터베이스 서버에서이 변경 작업을 수행 하는 데 필요한 유지 관리 중지 시간은 없습니다.

### <a name="8-if-i-create-a-new-server-after-february-15-2021-02152021-will-i-be-impacted"></a>8.2021 (02/15/2021)의 2 월 15 일 이후에 새 서버를 만드는 경우 영향을 받게 되나요?

02/15/2021 2021 년 2 월 15 일 이후에 생성 된 서버의 경우 응용 프로그램에서 SSL을 사용 하 여 연결 하는 데 [baltimorecybertrustroot.crt.pem](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem) 계속 사용 됩니다.

### <a name="9-how-often-does-microsoft-update-their-certificates-or-what-is-the-expiry-policy"></a>9. Microsoft에서 인증서를 업데이트 하는 빈도 또는 만료 정책 이란?

Azure Database for MySQL에서 사용 하는 이러한 인증서는 신뢰할 수 있는 CA (인증 기관)에서 제공 합니다. 따라서 이러한 인증서를 지 원하는 것은 CA에서 이러한 인증서를 지 원하는 것과 관련이 있습니다. [Baltimorecybertrustroot.crt.pem](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem) 인증서는 2025에 만료 될 예정 이므로 Microsoft에서 만료 되기 전에 인증서 변경을 수행 해야 합니다. 또한 이러한 미리 정의 된 인증서에 예측할 수 없는 버그가 있는 경우 Microsoft는 2021 년 2 월 15 일에 수행 된 변경 내용과 비슷한 방식으로 인증서를 회전 하 여 서비스가 안전 하 고 규정을 준수 하는지 확인 해야 합니다.

### <a name="10-if-im-using-read-replicas-do-i-need-to-perform-this-update-only-on-source-server-or-the-read-replicas"></a>10. 읽기 복제본을 사용 하는 경우 원본 서버 또는 읽기 복제본 에서만이 업데이트를 수행 해야 하나요?

이 업데이트는 클라이언트 쪽 변경 이기 때문에 클라이언트가 복제본 서버에서 데이터를 읽는 데 사용 되는 경우에도 해당 클라이언트에 대 한 변경 내용을 적용 해야 합니다.

### <a name="11-if-im-using-data-in-replication-do-i-need-to-perform-any-action"></a>11. 데이터 복제를 사용 하는 경우 모든 작업을 수행 해야 하나요?

[데이터에서 복제](concepts-data-in-replication.md) 를 사용 하 여 Azure Database for MySQL에 연결 하는 경우 다음 두 가지 사항을 고려해 야 합니다.

* 가상 컴퓨터 (온-프레미스 또는 Azure 가상 컴퓨터)에서 Azure Database for MySQL로 데이터를 복제 하는 경우에는 SSL을 사용 하 여 복제본을 만들 수 있는지 확인 해야 합니다. **슬레이브 상태 표시** 를 실행 하 고 다음 설정을 선택 합니다.  

    ```azurecli-interactive
    Master_SSL_Allowed            : Yes
    Master_SSL_CA_File            : ~\azure_mysqlservice.pem
    Master_SSL_CA_Path            :
    Master_SSL_Cert               : ~\azure_mysqlclient_cert.pem
    Master_SSL_Cipher             :
    Master_SSL_Key                : ~\azure_mysqlclient_key.pem
    ```

    CA_file, SSL_Cert 및 SSL_Key에 대 한 인증서가 제공 되는 것으로 확인 되 면 [새 인증서](https://cacerts.digicert.com/DigiCertGlobalRootG2.crt.pem) 를 추가 하 고 결합 된 인증서 파일을 만들어 파일을 업데이트 해야 합니다.

* 데이터 복제가 두 Azure Database for MySQL 사이에 있는 경우 **호출 mysql.az_replication_change_master** 를 실행 하 여 복제본을 다시 설정 하 고 새 이중 루트 인증서를 마지막 매개 변수로 제공 해야 [master_ssl_ca](howto-data-in-replication.md#link-source-and-replica-servers-to-start-data-in-replication)

### <a name="12-do-we-have-server-side-query-to-verify-if-ssl-is-being-used"></a>12. SSL이 사용 중인지 확인 하는 서버 쪽 쿼리가 있나요?

SSL 연결을 사용 하 여 서버에 연결 하 고 있는지 확인 하려면 [ssl 확인](howto-configure-ssl.md#step-4-verify-the-ssl-connection)을 참조 하세요.

### <a name="13-is-there-an-action-needed-if-i-already-have-the-digicertglobalrootg2-in-my-certificate-file"></a>13. 인증서 파일에 DigiCertGlobalRootG2이 이미 있는 경우 필요한 작업이 있나요?

아니요. 인증서 파일에 **DigiCertGlobalRootG2** 이 이미 있는 경우에는 필요한 작업이 없습니다.

### <a name="14-what-if-i-have-further-questions"></a>14. 추가 질문이 있으면 어떻게 하나요?

질문이 있는 경우 [Microsoft Q&](mailto:AzureDatabaseforMySQL@service.microsoft.com)의 커뮤니티 전문가 로부터 답변을 받으세요. 지원 계획이 있고 기술 도움말이 필요한 경우 [microsoft에 문의 하세요](mailto:AzureDatabaseforMySQL@service.microsoft.com).
