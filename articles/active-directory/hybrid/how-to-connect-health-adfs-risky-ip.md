---
title: AD FS는 위험한 IP 보고서와 함께 azure AD Connect Health | Microsoft Docs
description: Azure AD Connect Health AD FS 위험한 IP 보고서를 설명합니다.
services: active-directory
documentationcenter: ''
ms.reviewer: zhiweiwangmsft
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/26/2019
ms.author: billmath
ms.custom: H1Hack27Feb2017
ms.collection: M365-identity-device-management
ms.openlocfilehash: 49b93cb7852692e4dad65fcbd72cd749db1b16fb
ms.sourcegitcommit: cf971fe82e9ee70db9209bb196ddf36614d39d10
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2019
ms.locfileid: "58540910"
---
# <a name="risky-ip-report-public-preview"></a>위험한 IP 보고서 (공개 미리 보기)
AD FS 고객은 암호 인증 엔드포인트를 인터넷에 공개하여 최종 사용자가 Office 365와 같은 SaaS 애플리케이션에 액세스할 수 있게 하는 인증 서비스를 제공할 수 있습니다. 이 경우 악의적 행위자가 AD FS 시스템에 대한 로그인을 시도하여 사용자의 암호를 추측하고 종료하고 애플리케이션 리소스에 액세스할 수 있습니다. Windows Server 2012 R2의 AD FS 이후부터 AD FS에서는 이러한 유형의 공격을 방지하기 위해 엑스트라넷 계정 잠금 기능을 제공합니다. 더 낮은 버전을 사용하는 경우 AD FS 시스템을 Windows Server 2016으로 업그레이드하는 것이 좋습니다. <br />

또한 단일 IP 주소에서 여러 사용자에 대해 여러 로그인을 시도할 수도 있습니다. 이러한 경우 사용자당 시도 횟수가 AD FS의 계정 잠금 보호 임계값보다 낮을 수 있습니다. Azure AD Connect Health는 이제 이 조건을 감지하고 해당 문제가 발생하면 관리자에게 알리는 "위험한 IP 보고서"를 제공합니다. 이 보고서의 주요 이점은 다음과 같습니다. 
- 실패한 암호 기반 로그인의 임계값을 초과하는 IP 주소 검색
- 암호가 잘못되었거나 엑스트라넷 잠금 상태로 인해 실패한 로그인 지원
- 사용자 지정 가능한 이메일 설정을 사용하여 문제가 발생하는 즉시 관리자에게 경고하는 이메일 알림
- 조직의 보안 정책과 일치하는 사용자 지정 가능한 임계값 설정
- 오프라인 분석을 위한 다운로드 가능한 보고서 및 자동화를 통한 다른 시스템과의 통합

> [!NOTE]
> 이 보고서를 사용하려면 AD FS 감사를 사용하도록 설정해야 합니다. 자세한 내용은 [AD FS 감사 사용](how-to-connect-health-agent-install.md#enable-auditing-for-ad-fs)을 참조하십시오. <br />
> 미리 보기에 액세스하려면 전역 관리자 또는 [보안 읽기 권한자](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#security-reader) 권한이 필요합니다.  
> 

## <a name="what-is-in-the-report"></a>보고서에 대 한 기능
실패 한 로그인 활동 클라이언트 IP 주소는 웹 응용 프로그램 프록시 서버를 통해 집계 됩니다. 위험한 IP 보고서의 각 항목에는 지정된 임계값을 초과하는 실패한 AD FS 로그인 활동에 대한 집계 정보가 표시됩니다. 다음과 같은 정보를 제공합니다. ![Azure AD Connect Health 포털](./media/how-to-connect-health-adfs/report4a.png)

| 보고서 항목 | 설명 |
| ------- | ----------- |
| 타임스탬프 | 탐지 시간 범위가 시작되면 Azure Portal 현지 시간 기준의 타임스탬프가 표시됩니다.<br /> 모든 일별 이벤트는 자정 UTC 시간에 생성됩니다. <br />시간별 이벤트의 타임스탬프는 시간의 시작 시점으로 반올림됩니다. 첫 번째 활동 시작 시간은 내보낸 파일의 "firstAuditTimestamp"에서 찾을 수 있습니다. |
| 트리거 형식 | 탐지 시간 범위 형식이 표시됩니다. 집계 트리거 형식은 시간 또는 일 단위입니다. 이렇게 하면 하루 동안에 걸쳐 분산된 시도 횟수에서 자주 발생한 무차별 암호 대입 공격 및 느린 공격을 탐지하는 데 도움이 될 수 있습니다. |
| IP 주소 | 잘못된 암호 또는 엑스트라넷 잠금 로그인 활동이 있는 위험한 단일 IP 주소입니다. IPv4 또는 IPv6 주소일 수 있습니다. |
| 잘못된 암호 오류 수 | 탐지 시간 범위 동안 IP 주소에서 잘못된 암호 오류가 발생했습니다. 잘못된 암호 오류는 특정 사용자에게 여러 번 발생할 수 있습니다. 만료된 암호로 인해 실패한 시도는 포함되지 않습니다. |
| 엑스트라넷 잠금 오류 수 | 탐지 시간 범위 동안 IP 주소에서 엑스트라넷 잠금 오류가 발생했습니다. 엑스트라넷 잠금 오류는 특정 사용자에게 여러 번 발생할 수 있습니다. 이 정보는 엑스트라넷 잠금이 AD FS(2012R2 버전 이상)로 구성된 경우에만 표시됩니다. <b>참고</b> 암호를 사용하는 엑스트라넷 로그인을 허용하는 경우 이 기능을 설정하는 것이 좋습니다. |
| 시도한 고유 사용자 | 탐지 시간 범위 동안 IP 주소에서 시도한 고유 사용자 계정의 수입니다. 이 정보는 단일 사용자 공격 패턴 및 다중 사용자 공격 패턴을 구분하는 메커니즘을 제공합니다.  |

예를 들어 아래의 보고서 항목은 <i>104.2XX.2XX.9</i> IP 주소에서 2018년 2월 28일 오후 6 시부터 오후 7시까지 잘못된 암호 오류는 없지만 284개의 엑스트라넷 잠금 오류가 있었다고 나타냅니다. 조건 내에서 14명의 고유 사용자가 영향을 받았습니다. 활동 이벤트가 지정된 보고서 시간당 임계값을 초과했습니다. 

![Azure AD Connect Health 포털](./media/how-to-connect-health-adfs/report4b.png)

> [!NOTE]
> - 지정된 임계값을 초과하는 활동만 보고서 목록에 표시됩니다. 
> - 이 보고서는 최대 30일까지 다시 추적할 수 있습니다.
> - 이 경고 보고서에는 Exchange IP 주소 또는 개인 IP 주소가 표시되지 않습니다. 이러한 주소는 여전히 내보내기 목록에 포함되어 있습니다. 
>

![Azure AD Connect Health 포털](./media/how-to-connect-health-adfs/report4c.png)

## <a name="load-balancer-ip-addresses-in-the-list"></a>부하 분산 장치 IP 주소 목록
부하 분산 장치는 실패한 로그인 작업을 집계하여 경고 임계값에 도달합니다. 부하 분산 장치 IP 주소가 표시되는 경우 웹 애플리케이션 프록시 서버에 요청을 전달할 때 외부 부하 분산 장치에서 클라이언트 IP 주소를 보내지 않을 가능성이 큽니다. 포워드 클라이언트 IP 주소를 전달하도록 올바르게 부하 분산 장치를 구성하세요. 

## <a name="download-risky-ip-report"></a>위험한 IP 보고서 다운로드 
**다운로드** 기능을 사용하면 지난 30일 동안의 위험한 IP 주소 목록 전체를 Connect Health 포털에서 내보낼 수 있습니다. 내보내기 결과에는 각 탐지 시간 범위에서 실패한 AD FS 로그인 활동이 모두 포함되므로 내보낸 후에 필터링을 사용자 지정할 수 있습니다. 포털에서 강조 표시된 집계 외에도 내보내기 결과에는 실패한 로그인 활동에 대해 IP 주소별로 자세한 정보가 표시됩니다.

|  보고서 항목  |  설명  | 
| ------- | ----------- | 
| firstAuditTimestamp | 탐지 시간 범위 동안 실패한 활동이 시작된 첫 번째 타임스탬프를 표시합니다.  | 
| lastAuditTimestamp | 탐지 시간 범위 동안 실패한 활동이 종료된 마지막 타임스탬프를 표시합니다.  | 
| attemptCountThresholdIsExceeded | 현재 활동이 경고 임계값을 초과하는 경우의 플래그입니다.  | 
| isWhitelistedIpAddress | 경고 및 보고에서 IP 주소가 필터링된 경우의 플래그입니다. 개인 IP 주소(<i>10.x.x.x, 172.x.x.x & 192.168.x.x</i>) 및 Exchange IP 주소는 필터링되고 True로 표시됩니다. 개인 IP 주소 범위가 표시되는 경우 웹 애플리케이션 프록시 서버에 요청을 전달할 때 외부 부하 분산 장치에서 클라이언트 IP 주소를 보내지 않을 가능성이 큽니다.  | 

## <a name="configure-notification-settings"></a>알림 설정 구성
보고서의 관리자 연락처는 **알림 설정**을 통해 업데이트할 수 있습니다. 기본적으로 위험한 IP 경고 이메일 알림은 해제 상태입니다. "실패한 활동 임계값 보고서를 초과하는 IP 주소에 대한 이메일 알림 받기" 아래의 단추를 설정/해제하여 알림을 사용하도록 설정할 수 있습니다. Connect Health의 일반 경고 알림 설정과 마찬가지로 여기서는 위험한 IP 보고서에 대한 지정된 알림 받는 사람 목록을 사용자 지정할 수 있습니다. 또한 변경하는 동안 모든 전역 관리자에게 알릴 수도 있습니다. 

## <a name="configure-threshold-settings"></a>임계값 설정 구성
알림 임계값은 임계값 설정을 통해 업데이트할 수 있습니다. 먼저 임계값은 기본적으로 시스템에 설정되어 있습니다. 위험한 IP 보고서 임계값 설정에는 다음 네 가지 범주가 있습니다.

![Azure AD Connect Health 포털](./media/how-to-connect-health-adfs/report4d.png)

| 임계값 항목 | 설명 |
| --- | --- |
| (잘못된 U/P 수 + 엑스트라넷 잠금 수) / 일  | **일** 단위의 잘못된 암호 수와 엑스트라넷 잠금 수의 합계가 이 값을 초과하는 경우 활동을 보고하고 경고 알림을 트리거하는 임계값 설정 |
| (잘못된 U/P 수 + 엑스트라넷 잠금 수) / 시간 | **시간** 단위의 잘못된 암호 수와 엑스트라넷 잠금 수의 합계가 이 값을 초과하는 경우 활동을 보고하고 경고 알림을 트리거하는 임계값 설정 |
| 엑스트라넷 잠금 수 / 일 | **일** 단위의 엑스트라넷 잠금 수가 이 값을 초과하는 경우 활동을 보고하고 경고 알림을 트리거하는 임계값 설정 |
| 엑스트라넷 잠금 수 / 시간| **시간** 단위의 엑스트라넷 잠금 수가 이 값을 초과하는 경우 활동을 보고하고 경고 알림을 트리거하는 임계값 설정 |

> [!NOTE]
> - 보고서 임계값 변경은 설정 변경 1시간 후에 적용됩니다. 
> - 보고된 기존 항목은 임계값 변경의 영향을 받지 않습니다. 
> - 환경 내에서 표시되는 이벤트 수를 분석하여 임계값을 적절히 조정하는 것이 좋습니다. 
>
>

## <a name="faq"></a>FAQ
**보고서에 개인 IP 주소 범위가 왜 표시 되나요?**  <br />
개인 IP 주소(<i>10.x.x.x, 172.x.x.x & 192.168.x.x</i>) 및 Exchange IP 주소는 IP 허용 목록에서 필터링되고 True로 표시됩니다. 개인 IP 주소 범위가 표시되는 경우 웹 애플리케이션 프록시 서버에 요청을 전달할 때 외부 부하 분산 장치에서 클라이언트 IP 주소를 보내지 않을 가능성이 큽니다.

**표시 되는 이유 부하 분산 장치 IP 주소 보고서에서?**  <br />
부하 분산 장치 IP 주소가 표시되는 경우 웹 애플리케이션 프록시 서버에 요청을 전달할 때 외부 부하 분산 장치에서 클라이언트 IP 주소를 보내지 않을 가능성이 큽니다. 포워드 클라이언트 IP 주소를 전달하도록 올바르게 부하 분산 장치를 구성하세요. 

**IP 주소를 차단 하려면 어떻게 해야 하나요?**  <br />
식별된 악성 IP 주소는 방화벽에 추가되거나 Exchange에서 차단해야 합니다.   <br />

**하지이 보고서에 있는 모든 항목을 표시 여기는 이유** <br />
- 실패한 로그인 활동이 임계값 설정을 초과하지 않습니다.
- AD FS 서버 목록에서 "상태 서비스가 최신이 아닙니다" 경고가 활성화되어 있지 않은지 확인합니다.  [이 경고 문제를 해결하는 방법](how-to-connect-health-data-freshness.md)에 대해 자세히 알아보세요.
- AD FS 팜에서는 감사를 사용할 수 없습니다.

**보고서에 액세스할 수 없는 왜 표시 되나요?**  <br />
전역 관리자 또는 [보안 읽기 권한자](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#security-reader) 권한이 필요합니다. 전역 관리자에게 문의하여 액세스 권한을 얻으세요.


## <a name="next-steps"></a>다음 단계
* [Azure AD Connect Health](whatis-hybrid-identity-health.md)
* [Azure AD Connect Health Agent 설치](how-to-connect-health-agent-install.md)
