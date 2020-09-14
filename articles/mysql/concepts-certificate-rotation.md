---
title: Azure Database for MySQL에 대 한 인증서 회전
description: 영향을 받는 루트 인증서 변경의 예정 된 변경 내용에 대해 알아봅니다 Azure Database for MySQL
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: conceptual
ms.date: 09/02/2020
ms.openlocfilehash: 7e78c0d2538dbb6326df30d63ec1c38de48b62e6
ms.sourcegitcommit: 814778c54b59169c5899199aeaa59158ab67cf44
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2020
ms.locfileid: "90057226"
---
# <a name="understanding-the-changes-in-the-root-ca-change-for-azure-database-for-mysql"></a>Azure Database for MySQL에 대 한 루트 CA 변경의 변경 내용 이해

[데이터베이스 서버에 연결](concepts-connectivity-architecture.md)하는 데 사용 되는 SSL로 사용 하도록 설정 된 클라이언트 응용 프로그램/드라이버에 대 한 루트 인증서가 변경 될 Azure Database for MySQL. 현재 사용할 수 있는 루트 인증서는 표준 유지 관리 및 보안 모범 사례에 따라 2020 (10/26/2020)의 만료 날짜 ()로 설정 됩니다. 이 문서에서는 예정 된 변경 내용, 영향을 받는 리소스 및 응용 프로그램이 데이터베이스 서버에 대 한 연결을 유지 관리 하는 데 필요한 단계에 대 한 자세한 정보를 제공 합니다.

## <a name="what-update-is-going-to-happen"></a>어떤 업데이트를 수행 해야 하나요?

경우에 따라 응용 프로그램은 신뢰할 수 있는 CA (인증 기관) 인증서 파일에서 생성 된 로컬 인증서 파일을 사용 하 여 안전 하 게 연결 합니다. 현재 고객은 [여기](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem)에 있는 Azure Database for MySQL 서버에 연결 하기 위해 미리 정의 된 인증서만 사용할 수 있습니다. 그러나 ca [(인증 기관) 브라우저 포럼](https://cabforum.org/)은   ca 공급 업체에서 발급 한 여러 인증서의 보고서를 준수 하지 않는 것으로 게시 했습니다.

업계의 규정 준수 요구 사항에 따라, CA 공급 업체는 비규격 ca에 대 한 CA 인증서를 해지 하기 시작 하 고, 호환 Ca에서 발급 한 인증서를 서버에 사용 하 고, 해당 규격 Ca의 CA 인증서로 서명 합니다. 현재 Azure Database for MySQL는 이러한 비호환 인증서 중 하나를 사용 하므로 클라이언트 응용 프로그램에서 SSL 연결의 유효성을 검사 하는 데 사용 하므로 적절 한 조치를 취해야 합니다 (아래 설명 참조).

새 인증서는 2020 년 10 월 26 일부터 사용 됩니다 (10/26/2020). MySQL 클라이언트에서 연결할 때 CA 유효성 검사 또는 서버 인증서의 전체 유효성 검사를 사용 하는 경우 (sslmode = verify-CA 또는 sslmode = verify full), 2020 (10/26/2020) 10 월 26 일 전에 응용 프로그램 구성을 업데이트 해야 합니다.

## <a name="how-do-i-know-if-my-database-is-going-to-be-affected"></a>데이터베이스에 영향을 미치는지 여부를 어떻게 할까요? 확인

Azure Database for MySQL에 연결 하기 위해 SSL/TLS를 사용 하 고 루트 인증서를 확인 하는 모든 응용 프로그램은 루트 인증서를 업데이트 해야 합니다. 현재 SSL/TLS를 사용 하지 않는 경우 응용 프로그램 가용성에 영향을 주지 않습니다. 클라이언트 응용 프로그램에서 미리 정의 된 신뢰할 수 있는 [CA (인증](concepts-ssl-connection-security.md#ssl-default-settings)기관)와 함께 SSL 모드를 사용 하려고 하는지 확인할 수 있습니다.

인증서가 예기치 않게 해지 되거나 해지 된 인증서를 업데이트 하기 때문에 응용 프로그램의 가용성이 중단 되지 않도록 하려면 [**"연결을 유지 하기 위해 수행 해야 하**](concepts-certificate-rotation.md#what-do-i-need-to-do-to-maintain-connectivity) 는 작업" 섹션을 참조 하세요.

## <a name="what-do-i-need-to-do-to-maintain-connectivity"></a>연결을 유지 하기 위해 수행 해야 하는 작업

인증서가 예기치 않게 취소 되거나 해지 된 인증서를 업데이트 하기 때문에 응용 프로그램의 가용성이 중단 되지 않도록 하려면 다음 단계를 수행 합니다.

*   아래 링크에서 Baltimorecybertrustroot.crt.pem & DigiCertGlobalRootG2 Root CA를 다운로드 합니다.
    *   https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem
    *   https://cacerts.digicert.com/DigiCertGlobalRootG2.crt.pem

*   **Baltimorecybertrustroot.crt.pem** 및 **DigiCertGlobalRootG2** 인증서를 모두 포함 하는 결합 된 CA 인증서 저장소를 생성 합니다.
    *   Java (MySQL 커넥터/J) 사용자의 경우 다음을 실행 합니다.

          ```azurecli-interactive
          keytool -importcert -alias MySQLServerCACert  -file D:\BaltimoreCyberTrustRoot.crt.pem  -keystore truststore -storepass password -noprompt
          ```

          ```azurecli-interactive
          keytool -importcert -alias MySQLServerCACert2  -file D:\DigiCertGlobalRootG2.crt.pem -keystore truststore -storepass password  -noprompt
          ```

          그런 다음 원래 키 저장소 파일을 새로 생성 된 파일로 바꿉니다.
        *   System.object ("javax. trustStore", "path_to_truststore_file"); 
        *   System.object ("javax (Storepassword", "password");

    *   .NET (MySQL 커넥터/NET, MySQLConnector) 사용자의 경우 **baltimorecybertrustroot.crt.pem** 및 **DigiCertGlobalRootG2** 이 모두 Windows 인증서 저장소, 신뢰할 수 있는 루트 인증 기관에 존재 하는지 확인 합니다. 인증서가 없는 경우 누락 된 인증서를 가져옵니다.

        ![Azure Database for MySQL .net cert](media/overview/netconnecter-cert.png)

    *   SSL_CERT_DIR를 사용 하는 Linux의 .NET 사용자의 경우, SSL_CERT_DIR에 표시 된 디렉터리에 **baltimorecybertrustroot.crt.pem** 및 **DigiCertGlobalRootG2** 가 모두 있는지 확인 합니다. 인증서가 없는 경우 누락 된 인증서 파일을 만듭니다.

    *   다른 (MySQL 클라이언트/MySQL 워크 벤치/C/c + +/Go/Python/Ruby/PHP/NodeJS/Perl/Swift) 사용자의 경우 아래와 같은 두 가지 CA 인증서 파일을 병합할 수 있습니다.</b>

        </br>인증서-----시작-----
 </br>(Root C A 1: Baltimorecybertrustroot.crt.pem)
 </br>-----최종 인증서-----
 </br>인증서-----시작-----
 </br>(Root CA2: DigiCertGlobalRootG2)
 </br>-----최종 인증서-----

*   원래 루트 CA pem 파일을 결합 된 루트 CA 파일로 바꾸고 응용 프로그램/클라이언트를 다시 시작 합니다.
*   나중에 서버 쪽에 새 인증서를 배포한 후에는 CA pem 파일을 DigiCertGlobalRootG2로 변경할 수 있습니다.

## <a name="what-can-be-the-impact"></a>영향을 받을 수 있는 항목은 무엇 인가요?
여기에 설명 된 대로 발급 된 Azure Database for MySQL 인증서를 사용 하는 경우 데이터베이스에 연결할 수 없으므로 응용 프로그램의 가용성이 중단 될 수 있습니다. 응용 프로그램에 따라 다음을 비롯 한 다양 한 오류 메시지를 받을 수 있습니다.
*   잘못 된 인증서/해지 된 인증서
*   연결 시간이 초과됨
*   해당 하는 경우 오류

## <a name="frequently-asked-questions"></a>질문과 대답

### <a name="1-if-i-am-not-using-ssltls-do-i-still-need-to-update-the-root-ca"></a>1. SSL/TLS를 사용 하지 않는 경우 여전히 루트 CA를 업데이트 해야 하나요?
SSL/TLS를 사용 하지 않는 경우 아무 작업도 필요 하지 않습니다. 

### <a name="2-if-i-am-using-ssltls-do-i-need-to-restart-my-database-server-to-update-the-root-ca"></a>2. SSL/TLS를 사용 하는 경우에는 데이터베이스 서버를 다시 시작 하 여 루트 CA를 업데이트 해야 하나요?
아니요, 새 인증서 사용을 시작 하기 위해 데이터베이스 서버를 다시 시작할 필요는 없습니다. 이 루트 인증서는 클라이언트 쪽 변경 이며 들어오는 클라이언트 연결은 새 인증서를 사용 하 여 데이터베이스 서버에 연결할 수 있는지 확인 해야 합니다.

### <a name="3-what-will-happen-if-i-do-not-update-the-root-certificate-before-october-26-2020-10262020"></a>3.2020 (10/26/2020) 10 월 26 일 이전에 루트 인증서를 업데이트 하지 않으면 어떻게 되나요?
2020 년 11 월 30 일 이전에 루트 인증서를 업데이트 하지 않은 경우 SSL/TLS를 통해 연결 되 고 루트 인증서를 확인 하는 응용 프로그램은 MySQL 데이터베이스 서버와 통신할 수 없으며, 응용 프로그램에서 MySQL 데이터베이스 서버에 대 한 연결 문제가 발생 합니다.

### <a name="4-do-i-need-to-plan-a-maintenance-downtime-for-this-changebr"></a>4. 이러한 변경에 대 한 유지 관리 가동 중지 시간을 계획 해야 하나요?<BR>
아닙니다. 여기서 변경 내용은 데이터베이스 서버에 연결 하기 위한 클라이언트 쪽에만 해당 되므로이 변경에 대 한 유지 관리 가동 중지 시간은 없습니다.

### <a name="5--what-if-i-cannot-get-a-scheduled-downtime-for-this-change-before-october-26-2020-10262020"></a>5.2020 (10/26/2020)의 10 월 26 일 이전에이 변경에 대 한 예약 된 가동 중지 시간을 얻을 수 없는 경우 어떻게 하나요?
서버에 연결 하는 데 사용 되는 클라이언트는 [여기](./concepts-certificate-rotation.md#what-do-i-need-to-do-to-maintain-connectivity)의 수정 섹션에 설명 된 대로 인증서 정보를 업데이트 해야 하므로이 경우 서버에 대 한 가동 중지 시간이 필요 하지 않습니다.

###  <a name="6-if-i-create-a-new-server-after-nov-30-2020-will-i-be-impacted"></a>6.2020 년 11 월 30 일 이후에 새 서버를 만드는 경우 영향을 받게 되나요?
2020 (10/26/2020) 10 월 26 일 이후에 만들어진 서버의 경우 새로 발급 된 인증서를 사용 하 여 응용 프로그램에서 SSL을 사용 하 여 연결할 수 있습니다.

### <a name="7-how-often-does-microsoft-update-their-certificates-or-what-is-the-expiry-policy"></a>7. Microsoft에서 인증서를 업데이트 하는 빈도 또는 만료 정책 이란?
Azure Database for MySQL에서 사용 하는 이러한 인증서는 신뢰할 수 있는 CA (인증 기관)에서 제공 합니다. 따라서 Azure Database for MySQL에서 이러한 인증서를 지 원하는 것은 CA에서 이러한 인증서를 지 원하는 것과 관련이 있습니다. 그러나이 경우에는 미리 정의 된 이러한 인증서에 예측할 수 없는 버그가 있을 수 있으며,이는 초기에 수정 해야 합니다.

### <a name="8-if-i-am-using-read-replicas-do-i-need-to-perform-this-update-only-on-master-server-or-all-the-read-replicas"></a>8. 읽기 복제본을 사용 하는 경우 마스터 서버 또는 모든 읽기 복제본 에서만이 업데이트를 수행 해야 하나요?
이 업데이트는 클라이언트 쪽 변경 이기 때문에 클라이언트가 복제본 서버에서 데이터를 읽는 데 사용 되는 경우에도 해당 클라이언트에 대 한 변경 내용을 적용 해야 합니다. 

### <a name="9-do-we-have-server-side-query-to-verify-if-ssl-is-being-used"></a>9. SSL이 사용 중인지 확인 하는 서버 쪽 쿼리가 있나요?
SSL 연결을 사용 하 여 서버에 연결 하는지 확인 하려면 [ssl 확인](howto-configure-ssl.md#step-4-verify-the-ssl-connection)을 참조 하세요.

### <a name="10-what-if-i-have-further-questions"></a>10. 추가 질문이 있으면 어떻게 하나요?
질문이 있는 경우 [Microsoft Q&](mailto:AzureDatabaseforMySQL@service.microsoft.com)의 커뮤니티 전문가 로부터 답변을 받으세요. 지원 계획이 있고 기술 도움말이 필요한 경우 [microsoft에 문의 하세요](mailto:AzureDatabaseforMySQL@service.microsoft.com) .