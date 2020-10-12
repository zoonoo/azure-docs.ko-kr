---
title: SSIS integration runtime에서 연결 진단 기능 사용
description: 연결 진단 기능을 사용 하 여 SSIS 통합 런타임에서 연결 문제를 해결 합니다.
services: data-factory
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.author: meiyl
author: meiyl
ms.reviewer: sawinark
manager: yidetu
ms.date: 06/07/2020
ms.openlocfilehash: cf41da685036770144ebf7eb2befd0c3d126362d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "87446016"
---
# <a name="use-the-diagnose-connectivity-feature-in-the-ssis-integration-runtime"></a>SSIS integration runtime에서 연결 진단 기능 사용

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Ssis 통합 런타임에 SQL Server Integration Services (SSIS) 패키지를 실행 하는 동안 연결 문제가 발생할 수 있습니다. 이러한 문제는 SSIS 통합 런타임이 Azure virtual network에 가입 하는 경우에 특히 발생 합니다.

연결 *진단* 기능을 사용 하 여 연결을 테스트 하면 연결 문제를 해결할 수 있습니다. 이 기능은 Azure Data Factory 포털의 SSIS 통합 런타임 모니터링 페이지에 있습니다.

 ![모니터 페이지-연결 진단](media/ssis-integration-runtime-diagnose-connectivity-faq/ssis-monitor-diagnose-connectivity.png)

 ![모니터 페이지-연결 테스트](media/ssis-integration-runtime-diagnose-connectivity-faq/ssis-monitor-test-connection.png)

다음 섹션에서는 연결을 테스트할 때 발생 하는 가장 일반적인 오류에 대해 알아봅니다. 각 섹션에서는 다음에 대해 설명 합니다.

- 오류 코드
- 오류 메시지
- 오류의 잠재적 원인
- 권장 솔루션

## <a name="error-code-invalidinput"></a>오류 코드: InvalidInput

- **오류 메시지**: "입력이 올바른지 확인 하십시오."
- **잠재적 원인**: 입력이 잘못 되었습니다.
- **권장 사항**: 입력을 확인 합니다.

## <a name="error-code-firewallornetworkissue"></a>오류 코드: FirewallOrNetworkIssue

- **오류 메시지**: "이 포트가 방화벽/서버/n a g g에서 열려 있으며 네트워크가 안정적 인지 확인 하십시오."
- **가능한 원인:**
  - 서버에서 포트를 열지 않습니다.
  - 네트워크 보안 그룹의 아웃 바운드 트래픽이 포트에서 거부 되었습니다.
  - NVA/Azure 방화벽/온-프레미스 방화벽에서 포트를 열지 않습니다.
- **관련**
  - 서버에서 포트를 엽니다.
  - 네트워크 보안 그룹을 업데이트 하 여 포트에서 아웃 바운드 트래픽을 허용 합니다.
  - NVA/Azure 방화벽/온-프레미스 방화벽에서 포트를 엽니다.

## <a name="error-code-misconfigureddnssettings"></a>오류 코드: MisconfiguredDnsSettings

- **오류 메시지**: "Azure-SSIS IR에 의해 조인 된 VNet에서 자체 DNS 서버를 사용 하는 경우 호스트 이름을 확인할 수 있는지 확인 합니다."
- **가능한 원인:**
  -  사용자 지정 DNS에 문제가 있습니다.
  -  개인 호스트 이름에 FQDN (정규화 된 도메인 이름)을 사용 하지 않습니다.
- **관련**
  -  사용자 지정 DNS 문제를 해결 하 여 호스트 이름을 확인할 수 있는지 확인 합니다.
  -  FQDN을 사용해야 합니다. Azure-SSIS IR 자체 DNS 접미사를 자동으로 추가 하지 않습니다. 예를 들어 **<your_private_server>** 대신 **<your_private_server> contoso.com** 를 사용 합니다.

## <a name="error-code-servernotallowremoteconnection"></a>오류 코드: ServerNotAllowRemoteConnection

- **오류 메시지**: "서버에서이 포트를 통해 원격 TCP 연결을 허용 하는지 확인 하십시오."
- **가능한 원인:**
  -  서버 방화벽은 원격 TCP 연결을 허용 하지 않습니다.
  -  서버가 온라인 상태가 아닙니다.
- **관련**
  -  서버 방화벽에서 원격 TCP 연결을 허용 합니다.
  -  서버를 시작합니다.
   
## <a name="error-code-misconfigurednsgsettings"></a>오류 코드: MisconfiguredNsgSettings

- **오류 메시지**: "VNET의 nsg가이 포트를 통해 아웃 바운드 트래픽을 허용 하는지 확인 하세요. Azure Express 경로 및 또는 UDR을 사용 하는 경우이 포트가 방화벽/서버에서 열려 있는지 확인 하세요. "
- **가능한 원인:**
  -  네트워크 보안 그룹의 아웃 바운드 트래픽이 포트에서 거부 되었습니다.
  -  NVA/Azure 방화벽/온-프레미스 방화벽에서 포트를 열지 않습니다.
- **권장 사항:**
  -  네트워크 보안 그룹을 업데이트 하 여 포트에서 아웃 바운드 트래픽을 허용 합니다.
  -  NVA/Azure 방화벽/온-프레미스 방화벽에서 포트를 엽니다.

## <a name="error-code-genericissues"></a>오류 코드: GenericIssues

- **오류 메시지**: "일반적인 문제로 인해 연결 테스트에 실패 했습니다."
- **잠재적 원인**: 테스트 연결에 일반적인 일시적인 문제가 발생 했습니다.
- **권장 사항**: 나중에 테스트 연결을 다시 시도 합니다. 다시 시도 해도 도움이 되지 않으면 Azure Data Factory 지원 팀에 문의 하세요.

## <a name="error-code-pspingexecutiontimeout"></a>오류 코드: PSPingExecutionTimeout

- **오류 메시지**: "연결 테스트 시간 초과, 나중에 다시 시도 하세요."
- **잠재적 원인**: 테스트 연결 시간이 초과 되었습니다.
- **권장 사항**: 나중에 테스트 연결을 다시 시도 합니다. 다시 시도 해도 도움이 되지 않으면 Azure Data Factory 지원 팀에 문의 하세요.

## <a name="error-code-networkinstable"></a>오류 코드: Networkable 가능

- **오류 메시지**: "네트워크가 불안정 하 여 연결을 불규칙 하 게 테스트 했습니다."
- **잠재적 원인**: 일시적인 네트워크 문제입니다.
- **권장 사항**: 서버 또는 방화벽 네트워크가 안정적인 지 확인 합니다.

## <a name="next-steps"></a>다음 단계

- [SSMS를 사용 하 여 Azure에 SSIS 프로젝트 배포](https://docs.microsoft.com/sql/integration-services/ssis-quickstart-deploy-ssms)
- [SSMS를 사용 하 여 Azure에서 SSIS 패키지 실행](https://docs.microsoft.com/sql/integration-services/ssis-quickstart-run-ssms)
- [Azure에서 SSIS 패키지 예약](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms?view=sql-server-ver15)
