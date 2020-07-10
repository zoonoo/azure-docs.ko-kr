---
title: SSIS 통합 런타임에서 연결 진단 문제 해결
description: 이 문서에서는 SSIS 통합 런타임의 연결을 진단 하는 방법에 대 한 문제 해결 지침을 제공 합니다.
services: data-factory
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.author: meiyl
author: meiyl
ms.reviewer: sawinark
manager: yidetu
ms.date: 06/07/2020
ms.openlocfilehash: 8e520048a6067f134e847953f4f4aa0598d9926e
ms.sourcegitcommit: 1e6c13dc1917f85983772812a3c62c265150d1e7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/09/2020
ms.locfileid: "86172717"
---
# <a name="troubleshoot-diagnose-connectivity-in-the-ssis-integration-runtime"></a>SSIS 통합 런타임에서 연결 진단 문제 해결

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Ssis integration runtime에서 ssis 패키지를 실행 하는 동안 연결 문제가 발생 하는 경우, 특히 SSIS 통합 런타임이 Azure 가상 네트워크에 연결 된 경우에 해당 합니다. Azure Data Factory 포털의 SSIS 통합 런타임 모니터링 페이지에서이 연결 진단 기능을 사용 하 여 문제를 자체 진단 해 볼 수 있습니다. 

 ![모니터 페이지-연결 진단 ](media/ssis-integration-runtime-diagnose-connectivity-faq/ssis-monitor-diagnose-connectivity.png) ![ 모니터 페이지-연결 테스트](media/ssis-integration-runtime-diagnose-connectivity-faq/ssis-monitor-test-connection.png)
 
이 문서에는 SSIS integration runtime에서 연결을 테스트할 때 발견할 수 있는 가장 일반적인 오류가 포함 되어 있습니다. 오류를 해결 하는 잠재적인 원인과 작업을 설명 합니다. 

## <a name="common-errors-potential-causes-and-recommendation-solutions"></a>일반적인 오류, 잠재적 원인 및 권장 해결 방법

### <a name="error-code-invalidinput"></a>오류 코드: InvalidInput.
* **오류 메시지**: 입력이 올바른지 확인 하세요.
* **가능한 원인:** 입력이 잘못 되었습니다.
* **권장 사항:** 입력을 확인 하세요.

### <a name="error-code-firewallornetworkissue"></a>오류 코드: FirewallOrNetworkIssue.
* **오류 메시지**:이 포트가 방화벽/서버/n g g에서 열려 있고 네트워크가 안정적인 지 확인 하세요.
* **가능한 원인:** 
  * 서버에서이 포트를 열지 않습니다.
  * 네트워크 보안 그룹이이 포트에서 아웃 바운드 트래픽을 거부 했습니다.
  * NVA/Azure 방화벽/온-프레미스 방화벽은이 포트를 열지 않습니다.
* **권장 사항:** 
  * 서버에서이 포트를 엽니다.
  * 이 포트에서 아웃 바운드 트래픽을 허용 하도록 네트워크 보안 그룹을 업데이트 합니다.
  * NVA/Azure 방화벽/온-프레미스 방화벽에서이 포트를 엽니다.

### <a name="error-code-misconfigureddnssettings"></a>오류 코드: MisconfiguredDnsSettings.
* **오류 메시지**: Azure-SSIS IR에 의해 조인 된 VNet에서 자체 DNS 서버를 사용 하는 경우 호스트 이름을 확인할 수 있는지 확인 하세요.
* **가능한 원인:** 
  *  사용자 지정 DNS의 문제
  *  개인 호스트 이름에 대 한 FQDN (정규화 된 도메인 이름)을 사용 하 고 있지 않습니다.
* **권장 사항:** 
  *  사용자 지정 DNS 문제를 해결 하 여 호스트 이름을 확인할 수 있는지 확인 합니다.
  *  FQDN (정규화 된 도메인 이름)을 사용 합니다. 예를 들어 your_private_server>를 사용 하는 <대신 <your_private_server contoso.com>를 사용 합니다. Azure-SSIS IR는 자체 DNS 접미사를 자동으로 추가 하지 않습니다.

### <a name="error-code-servernotallowremoteconenction"></a>오류 코드: ServerNotAllowRemoteConenction.
* **오류 메시지**: 서버에서이 포트를 통해 원격 TCP 연결을 허용 하는지 확인 하세요.
* **가능한 원인:** 
  *  서버 방화벽은 원격 TCP 연결을 허용 하지 않습니다.
  *  서버가 온라인 상태가 아닙니다.
* **권장 사항:** 
  *  서버 방화벽에서 원격 TCP 연결을 허용 합니다.
  *  서버를 시작합니다.
   
### <a name="error-code-misconfigurednsgsettings"></a>오류 코드: MisconfiguredNsgSettings.
* **오류 메시지**: VNET의 nsg가이 포트를 통해 아웃 바운드 트래픽을 허용 하는지 확인 하세요. Azure Express 경로 및 또는 UDR을 사용 하는 경우이 포트가 방화벽/서버에서 열려 있는지 확인 하세요.
* **가능한 원인:** 
  *  네트워크 보안 그룹이이 포트에서 아웃 바운드 트래픽을 거부 했습니다.
  *  NVA/Azure 방화벽/온-프레미스 방화벽은이 포트를 열지 않습니다.
* **권장 사항:** 
  *  이 포트에서 아웃 바운드 트래픽을 허용 하도록 네트워크 보안 그룹을 업데이트 합니다.
  *  NVA/Azure 방화벽/온-프레미스 방화벽에서이 포트를 엽니다.

### <a name="error-code-genericissues"></a>오류 코드: GenericIssues
* **오류 메시지**: 일반적인 문제로 인해 연결 테스트에 실패 했습니다.
* **가능한 원인:** 테스트 연결에 일반적인 일시적인 문제가 발생 했습니다.
* **권장 사항:** 나중에 테스트 연결을 다시 시도 하세요. 다시 시도 해도 도움이 되지 않으면 Azure Data Factory 지원 팀에 문의 하세요.


### <a name="error-code-pspingexecutiontimeout"></a>오류 코드: PSPingExecutionTimeout.
* **오류 메시지**: 연결 테스트 제한 시간을 초과 했습니다. 나중에 다시 시도 하세요.
* **가능한 원인:** 테스트 연결 시간이 초과 되었습니다.
* **권장 사항:** 나중에 테스트 연결을 다시 시도 하세요. 다시 시도 해도 도움이 되지 않으면 Azure Data Factory 지원 팀에 문의 하세요.

### <a name="error-code-networkinstable"></a>오류 코드: Networkable.
* **오류 메시지**: 네트워크 불안정성으로 인해 불규칙 하 게 연결 테스트에 성공 했습니다.
* **가능한 원인:** 일시적인 네트워크 문제
* **권장 사항:** 서버 또는 방화벽 네트워크가 안정적인 지 확인 하십시오.

## <a name="next-steps"></a>다음 단계

- 패키지를 배포합니다. 자세한 내용은 [SSMS를 사용하여 Azure에 SSIS 프로젝트 배포](https://docs.microsoft.com/sql/integration-services/ssis-quickstart-deploy-ssms)를 참조합니다.
- 패키지를 실행합니다. 자세한 내용은 [SSMS를 사용하여 Azure에서 SSIS 패키지 실행](https://docs.microsoft.com/sql/integration-services/ssis-quickstart-run-ssms)을 참조합니다.
- 패키지를 예약합니다. 자세한 내용은 [Azure에서 SSIS 패키지 예약](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms?view=sql-server-ver15)을 참조 하세요.

