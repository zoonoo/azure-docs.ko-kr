---
title: SQL Managed Instance & Azure SQL Database에 대 한 인증서 회전
description: Azure SQL Database 및 Azure SQL에 영향을 주는 루트 인증서 변경의 예정 된 변경 내용에 대해 알아봅니다 Managed Instance
author: srdan-bozovic-msft
ms.author: srbozovi
ms.service: sql-db-mi
ms.subservice: service
ms.topic: conceptual
ms.date: 09/13/2020
ms.openlocfilehash: b0a10744d2b48fa620b48b731144222199f711c7
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/28/2020
ms.locfileid: "92792534"
---
# <a name="understanding-the-changes-in-the-root-ca-change-for-azure-sql-database--sql-managed-instance"></a>Azure SQL Database & SQL Managed Instance에 대 한 루트 CA 변경 내용 이해

Azure SQL Database & SQL Managed Instance는 보안 TDS 연결을 설정 하는 데 사용 되는 SSL로 설정 된 클라이언트 응용 프로그램/드라이버에 대 한 루트 인증서를 변경 합니다. [현재 루트 인증서](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem) 는 표준 유지 관리 및 보안 모범 사례에 따라 2020 년 10 월 26 일 만료로 설정 됩니다. 이 문서에서는 예정 된 변경 내용, 영향을 받는 리소스 및 응용 프로그램이 데이터베이스 서버에 대 한 연결을 유지 관리 하는 데 필요한 단계에 대 한 자세한 정보를 제공 합니다.

## <a name="what-update-is-going-to-happen"></a>어떤 업데이트를 수행 해야 하나요?

Ca [(인증 기관) 브라우저 포럼](https://cabforum.org/) 은 ca 공급 업체가 발급 하지 않는 여러 인증서의 보고서를 최근에 게시 했습니다.

업계의 규정 준수 요구 사항에 따라, CA 공급 업체는 비규격 ca에 대 한 CA 인증서를 해지 하기 시작 하 고, 호환 Ca에서 발급 한 인증서를 서버에 사용 하 고, 해당 규격 Ca의 CA 인증서로 서명 합니다. Azure SQL Database & SQL Managed Instance는 현재 이러한 비호환 인증서 중 하나를 사용 하므로 클라이언트 응용 프로그램에서 SSL 연결의 유효성을 검사 하는 데 사용 하므로 적절 한 조치를 취해야 합니다 (아래 설명 참조) .이를 통해 Azure SQL server에 대 한 잠재적인 영향을 최소화 해야 합니다.

새 인증서는 2020 년 10 월 26 일부터 사용 됩니다. SQL 클라이언트에서 연결할 때 서버 인증서의 전체 유효성 검사를 사용 하는 경우 (TrustServerCertificate = true) SQL 클라이언트에서 2020 10 월 26 일 이전에 새 루트 인증서의 유효성을 검사할 수 있는지 확인 해야 합니다.

## <a name="how-do-i-know-if-my-application-might-be-affected"></a>응용 프로그램에 영향을 줄 수 있는지 여부를 어떻게 할까요? 확인

Azure SQL Database & SQL Managed Instance에 연결 하기 위해 SSL/TLS를 사용 하 고 루트 인증서를 확인 하는 모든 응용 프로그램은 루트 인증서를 업데이트 해야 합니다. 

현재 SSL/TLS를 사용 하지 않는 경우 응용 프로그램 가용성에 영향을 주지 않습니다. 클라이언트 응용 프로그램에서 연결 문자열을 확인 하 여 루트 인증서를 확인 하려고 하는지 확인할 수 있습니다. TrustServerCertificate가 true로 명시적으로 설정 된 경우에는 영향을 받지 않습니다.

클라이언트 드라이버가 OS 인증서 저장소를 사용 하는 경우 대다수의 드라이버가 수행 하 고 OS가 정기적으로 유지 관리 되는 경우이 변경 사항은 사용자에 게 영향을 주지 않습니다. 전환 하는 루트 인증서를 신뢰할 수 있는 루트 인증서 저장소에서 이미 사용할 수 있기 때문입니다. Baltimore CyberDigiCert GlobalRoot G2를 확인 하 고 있는지 확인 합니다.

클라이언트 드라이버에서 로컬 파일 인증서 저장소를 사용 하는 경우 인증서가 예기치 않게 취소 되거나 해지 된 인증서를 업데이트 하기 위해 응용 프로그램의 가용성이 중단 되지 않도록 하려면 [**연결을 유지 하기 위해 수행 해야**](./ssl-root-certificate-expiring.md#what-do-i-need-to-do-to-maintain-connectivity) 하는 작업 섹션을 참조 하세요.

## <a name="what-do-i-need-to-do-to-maintain-connectivity"></a>연결을 유지 하기 위해 수행 해야 하는 작업

인증서가 예기치 않게 취소 되거나 해지 된 인증서를 업데이트 하기 때문에 응용 프로그램의 가용성이 중단 되지 않도록 하려면 다음 단계를 수행 합니다.

*   아래 링크에서 Baltimore CyberTrust Root & DigiCert GlobalRoot G2 Root CA를 다운로드 합니다.
    *   https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem
    *   https://cacerts.digicert.com/DigiCertGlobalRootG2.crt.pem

*   **Baltimorecybertrustroot.crt.pem** 및 **DigiCertGlobalRootG2** 인증서를 모두 포함 하는 결합 된 CA 인증서 저장소를 생성 합니다.

## <a name="what-can-be-the-impact"></a>영향을 받을 수 있는 항목은 무엇 인가요?
여기에 설명 된 대로 서버 인증서의 유효성을 검사 하는 경우 데이터베이스에 연결할 수 없으므로 응용 프로그램의 가용성이 중단 될 수 있습니다. 응용 프로그램에 따라 다음을 비롯 한 다양 한 오류 메시지를 받을 수 있습니다.
*   잘못 된 인증서/해지 된 인증서
*   연결 시간이 초과됨
*   해당 하는 경우 오류

## <a name="frequently-asked-questions"></a>질문과 대답

### <a name="if-i-am-not-using-ssltls-do-i-still-need-to-update-the-root-ca"></a>SSL/TLS를 사용 하지 않는 경우 여전히 루트 CA를 업데이트 해야 하나요?
SSL/TLS를 사용 하지 않는 경우에는 이러한 변경에 대 한 작업이 필요 하지 않습니다. 그러나 향후에 TLS를 적용할 계획인 경우 최신 TLS 버전을 사용 하 여 시작에 대 한 계획을 설정 해야 합니다.

### <a name="what-will-happen-if-i-do-not-update-the-root-certificate-before-october-26-2020"></a>2020 년 10 월 26 일 이전에 루트 인증서를 업데이트 하지 않으면 어떻게 되나요?
2020 년 11 월 30 일 이전에 루트 인증서를 업데이트 하지 않는 경우 SSL/TLS를 통해 연결 하 고 루트 인증서를 확인 하는 응용 프로그램은 SQL Managed Instance & Azure SQL Database 통신할 수 없으며, 응용 프로그램은 Azure SQL Database & SQL Managed Instance에 대 한 연결 문제를 경험 합니다.

### <a name="do-i-need-to-plan-a-maintenance-downtime-for-this-changebr"></a>이러한 변경에 대 한 유지 관리 가동 중지 시간을 계획 해야 하나요?<BR>
아니요. 여기에서 변경한 내용은 서버에 연결 하기 위한 클라이언트 쪽에만 해당 되므로이 변경에 대 한 유지 관리 가동 중지 시간은 없습니다.

### <a name="what-if-i-cannot-get-a-scheduled-downtime-for-this-change-before-october-26-2020"></a>2020 년 10 월 26 일 이전에이 변경에 대 한 예약 된 가동 중지 시간을 얻을 수 없는 경우 어떻게 되나요?
서버에 연결 하는 데 사용 되는 클라이언트는 [여기](./ssl-root-certificate-expiring.md#what-do-i-need-to-do-to-maintain-connectivity)의 수정 섹션에 설명 된 대로 인증서 정보를 업데이트 해야 하므로이 경우 서버에 대 한 가동 중지 시간이 필요 하지 않습니다.

### <a name="if-i-create-a-new-server-after-november-30-2020-will-i-be-impacted"></a>2020 년 11 월 30 일 이후에 새 서버를 만드는 경우 영향을 받게 되나요?
2020 년 10 월 26 일 이후에 생성 된 서버의 경우 새로 발급 된 인증서를 사용 하 여 응용 프로그램에서 SSL을 사용 하 여 연결할 수 있습니다.

### <a name="how-often-does-microsoft-update-their-certificates-or-what-is-the-expiry-policy"></a>Microsoft에서 인증서를 업데이트 하는 빈도 또는 만료 정책 이란?
Azure SQL Database & SQL Managed Instance에서 사용 하는 이러한 인증서는 신뢰할 수 있는 CA (인증 기관)에서 제공 합니다. 따라서 Azure SQL Database & SQL Managed Instance에서 이러한 인증서를 지 원하는 것은 CA에서 이러한 인증서를 지 원하는 것과 관련이 있습니다. 그러나이 경우에는 미리 정의 된 이러한 인증서에 예측할 수 없는 버그가 있을 수 있으며,이는 초기에 수정 해야 합니다.

### <a name="if-i-am-using-read-replicas-do-i-need-to-perform-this-update-only-on-master-server-or-all-the-read-replicas"></a>읽기 복제본을 사용 하는 경우 마스터 서버 또는 모든 읽기 복제본 에서만이 업데이트를 수행 해야 하나요?
이 업데이트는 클라이언트 쪽 변경 이기 때문에 클라이언트가 복제본 서버에서 데이터를 읽는 데 사용 되는 경우에도 해당 클라이언트에 대 한 변경 내용을 적용 해야 합니다. 

### <a name="do-we-have-server-side-query-to-verify-if-ssl-is-being-used"></a>SSL이 사용 되 고 있는지 확인 하는 서버 쪽 쿼리가 있나요?
이 구성은 클라이언트 쪽 이므로 서버 쪽에서는 정보를 사용할 수 없습니다.

### <a name="what-if-i-have-further-questions"></a>추가 질문이 있으면 어떻게 하나요?
지원 계획이 있고 기술 도움말이 필요한 경우 azure 지원 요청 만들기를 참조 하세요. [azure 지원 요청을 만드는 방법](../../azure-portal/supportability/how-to-create-azure-support-request.md)을 참조 하세요.