---
title: Azure SQL Database 및 SQL Managed Instance의 인증서 순환
description: Azure SQL Database 및 Azure SQL Managed Instance에 영향을 주게 될 루트 인증서에 대해 예정된 변경 내용에 대해 알아봅니다.
author: srdan-bozovic-msft
ms.author: srbozovi
ms.service: sql-db-mi
ms.subservice: security
ms.topic: conceptual
ms.date: 09/13/2020
ms.openlocfilehash: d569065a022180f2b2f94131099e6eedc4c468fb
ms.sourcegitcommit: 20acb9ad4700559ca0d98c7c622770a0499dd7ba
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/29/2021
ms.locfileid: "110702074"
---
# <a name="understanding-the-changes-in-the-root-ca-change-for-azure-sql-database--sql-managed-instance"></a>Azure SQL Database 및 SQL Managed Instance에 대한 루트 CA 변경 내용 이해

Azure SQL Database 및 SQL Managed Instance는 보안 TDS 연결을 설정하는 데 사용되는 SSL로 설정된 클라이언트 애플리케이션/드라이버에 대한 루트 인증서를 변경할 예정입니다. [현재 루트 인증서](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem)는 표준 유지 관리 및 보안 모범 사례의 일부로 2020년 10월 26일에 만료되도록 설정됩니다. 이 문서에서는 예정된 변경 내용, 영향을 받게 될 리소스 및 애플리케이션에서 데이터베이스 서버에 대한 연결을 유지 관리하는 데 필요한 단계에 대한 자세한 정보를 제공합니다.

## <a name="what-update-is-going-to-happen"></a>어떤 업데이트가 제공될 예정인가요?

[CA(인증 기관) 브라우저 포럼](https://cabforum.org/)에서는 최근에 CA 공급업체에서 비준수로 발급한 여러 인증서에 대한 보고서를 게시했습니다.

업계의 규정 준수 요구 사항에 따라 CA 공급업체는 비준수 CA에 대한 CA 인증서를 해지하여 준수 CA에서 발급한 인증서를 서버에서 사용하도록 요구하기 시작했고, 해당 준수 CA의 CA 인증서로 서명했습니다. Azure SQL Database 및 SQL Managed Instance는 현재, 클라이언트 애플리케이션에서 SSL 연결의 유효성을 검사하는 데 사용하는 이러한 비호환 인증서 중 하나를 사용하므로 하므로 Azure SQL Server에 대한 잠재적인 영향을 최소화하기 위한 적절한 조치를 취해야 합니다(아래 설명 참조).

새 인증서는 2020년 10월 26일부터 사용됩니다. SQL 클라이언트에서 연결할 때 서버 인증서의 전체 유효성 검사를 사용하는 경우(TrustServerCertificate = true) SQL 클라이언트에서 2020년 10월 26일 이전에 새 루트 인증서의 유효성을 검사할 수 있는지 확인해야 합니다.

## <a name="how-do-i-know-if-my-application-might-be-affected"></a>내 애플리케이션이 영향을 받을지 어떻게 알 수 있나요?

SSL/TLS를 사용하고 루트 인증서를 확인하는 모든 애플리케이션은 Azure SQL Database 및 SQL Managed Instance에 연결하기 위해 루트 인증서를 업데이트해야 합니다. 

현재 SSL/TLS를 사용하지 않는 경우 애플리케이션 가용성에 영향을 주지 않습니다. 클라이언트 애플리케이션에서 연결 문자열을 확인하여 루트 인증서를 확인하려고 하는지 확인할 수 있습니다. TrustServerCertificate가 true로 명시적으로 설정된 경우에는 영향을 받지 않습니다.

대다수의 드라이버처럼 클라이언트 드라이버가 OS 인증서 저장소를 사용하며, OS가 정기적으로 유지 관리되는 경우 전환하는 루트 인증서를 신뢰할 수 있는 루트 인증서 저장소에서 이미 사용할 수 있기 때문에 이러한 변경 내용은 사용자에게 영향을 주지 않습니다. Baltimore CyberDigiCert GlobalRoot G2가 있는지 확인합니다.

클라이언트 드라이버에서 로컬 파일 인증서 저장소를 사용할 때 인증서가 예기치 않게 해지되거나 해지된 인증서가 업데이트되는 경우로 인해 애플리케이션의 가용성이 중단되지 않도록 하려면 [**연결을 유지하기 위해 수행해야 하는 작업**](./ssl-root-certificate-expiring.md#what-do-i-need-to-do-to-maintain-connectivity) 섹션을 참조하세요.

## <a name="what-do-i-need-to-do-to-maintain-connectivity"></a>연결을 유지하기 위해 수행해야 하는 작업

인증서가 예기치 않게 해지되거나 해지된 인증서가 업데이트되는 경우로 인해 애플리케이션의 가용성이 중단되지 않도록 하려면 아래 단계를 따르세요.

*   아래 링크에서 Baltimore CyberTrust Root 및 DigiCert GlobalRoot G2 Root CA를 다운로드합니다.
    *   https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem
    *   https://cacerts.digicert.com/DigiCertGlobalRootG2.crt.pem

*   **BaltimoreCyberTrustRoot** 및 **DigiCertGlobalRootG2** 인증서를 모두 포함하는 결합된 CA 인증서 저장소를 생성합니다.

## <a name="what-can-be-the-impact"></a>어떤 항목이 영향을 받을 수 있나요?
여기에 설명된 대로 서버 인증서의 유효성을 검사하는 경우 데이터베이스에 연결할 수 없으므로 애플리케이션의 가용성이 중단될 수 있습니다. 애플리케이션에 따라 다음을 비롯한 다양한 오류 메시지가 표시될 수 있습니다.
*   잘못된 인증서/해지된 인증서
*   연결 시간이 초과됨
*   해당하는 경우 오류 발생

## <a name="frequently-asked-questions"></a>질문과 대답

### <a name="if-i-am-not-using-ssltls-do-i-still-need-to-update-the-root-ca"></a>SSL/TLS를 사용하지 않는 경우에도 루트 CA를 업데이트해야 하나요?
SSL/TLS를 사용하지 않는 경우에는 이러한 변경에 대한 작업이 필요하지 않습니다. 그러나 가까운 미래에 TLS를 적용할 계획인 경우 언제 최신 TLS 버전을 사용할 것인지 계획하는 것이 좋습니다.

### <a name="what-will-happen-if-i-do-not-update-the-root-certificate-before-october-26-2020"></a>2020년 10월 26일 이전에 루트 인증서를 업데이트하지 않으면 어떻게 되나요?
2020년 11월 30일 이전에 루트 인증서를 업데이트하지 않으면 SSL/TLS를 통해 연결되고 루트 인증서를 확인하는 애플리케이션은 Azure SQL Database 및 SQL Managed Instance와 통신할 수 없으며, 애플리케이션에서 Azure SQL Database 및 SQL Managed Instance에 대한 연결 이슈가 발생하게 됩니다.

### <a name="do-i-need-to-plan-a-maintenance-downtime-for-this-changebr"></a>이러한 변경을 위해 유지 관리 가동 중지 시간을 계획해야 하나요?<BR>
아니요. 여기에서 변경한 내용은 서버에 연결하기 위해 클라이언트 쪽에서만 적용되므로 이 변경을 위한 유지 관리 가동 중지 시간은 없습니다.

### <a name="what-if-i-cannot-get-a-scheduled-downtime-for-this-change-before-october-26-2020"></a>2020년 10월 26일 이전에 이 변경을 위한 가동 중지 시간을 예약할 수 없으면 어떻게 되나요?
서버에 연결하는 데 사용되는 클라이언트는 [여기](./ssl-root-certificate-expiring.md#what-do-i-need-to-do-to-maintain-connectivity)의 수정 섹션에 설명된 대로 인증서 정보를 업데이트해야 하므로 이 경우에는 서버를 위한 가동 중지 시간이 필요하지 않습니다.

### <a name="if-i-create-a-new-server-after-november-30-2020-will-i-be-impacted"></a>2020년 11월 30일 이후에 새 서버를 만드는 경우 영향을 받게 되나요?
2020년 10월 26일 이후에 생성된 서버의 경우 애플리케이션에 대해 새로 발급된 인증서를 사용하여 SSL을 사용하여 연결할 수 있습니다.

### <a name="how-often-does-microsoft-update-their-certificates-or-what-is-the-expiry-policy"></a>Microsoft에서 인증서를 업데이트하는 빈도는 어떻게 되나요? 또는 만료 정책은 무엇인가요?
Azure SQL Database 및 SQL Managed Instance에서 사용하는 이러한 인증서는 신뢰할 수 있는 CA(인증 기관)에서 제공합니다. 따라서 Azure SQL Database 및 SQL Managed Instance에서 이러한 인증서가 지원되는지 여부는 CA에서 이러한 인증서가 지원되는지와 관련이 있습니다. 그러나 이 경우에는 미리 정의된 이러한 인증서에 예측할 수 없는 버그가 있을 수 있으므로 초기에 수정해야 합니다.

### <a name="if-i-am-using-read-replicas-do-i-need-to-perform-this-update-only-on-primary-server-or-all-the-read-replicas"></a>읽기 복제본을 사용하는 경우 이 업데이트를 기본 서버 또는 모든 읽기 복제본에서만 수행해야 하나요?
이 업데이트는 클라이언트 쪽 변경이므로 클라이언트가 복제본 서버에서 데이터를 읽는 데 사용된 경우 해당 클라이언트에 대한 변경 내용도 적용해야 합니다. 

### <a name="do-we-have-server-side-query-to-verify-if-ssl-is-being-used"></a>SSL이 사용되고 있는지 확인하는 서버 쪽 쿼리가 있나요?
이 구성은 클라이언트 쪽이므로 서버 쪽에서는 정보를 사용할 수 없습니다.

### <a name="what-if-i-have-further-questions"></a>추가 질문이 있으면 어떻게 하나요?
지원 플랜이 있고 Azure 지원 요청을 만들기 위한 기술 도움말이 필요한 경우 [Azure 지원 요청을 만드는 방법](../../azure-portal/supportability/how-to-create-azure-support-request.md)을 참조하세요.