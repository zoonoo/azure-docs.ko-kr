---
title: Azure에서 아웃바운드 SMTP 연결 문제 해결 | Microsoft Docs
description: Azure에서 아웃바운드 SMTP 연결 문제를 해결하는 방법을 알아봅니다.
services: virtual-network
author: genlin
manager: dcscontentpm
editor: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/20/2018
ms.author: genli
ms.openlocfilehash: e5a878c8108706c4a3a175c920708beeeaa4aa12
ms.sourcegitcommit: c8a0fbfa74ef7d1fd4d5b2f88521c5b619eb25f8
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/05/2020
ms.locfileid: "82801753"
---
# <a name="troubleshoot-outbound-smtp-connectivity-issues-in-azure"></a>Azure에서 아웃바운드 SMTP 연결 문제 해결
<p class="alert is-flex is-primary"><span class="has-padding-left-medium has-padding-top-extra-small"><a class="button is-primary" href="https://azurevirtualsupportagent.services.microsoft.com?content=027fa865-2329-23de-3740-cfbe35359302" target='_blank'>Start</a></span><span class="has-padding-small">가상 에이전트를 사용 하 여 <b>자동 진단을</b> 실행 하 여 문제를 빠르게 해결 합니다.</span> <span class="has-padding-small"> <sub>Privacy Statement</sub> 개인정보 <a href="https://privacy.microsoft.com/privacystatement" target='_blank'> <div align="right"></div></a></span></p>
2017년 11월 15일부터 VM(가상 머신)에서 외부 도메인(예: outlook.com 및 gmail.com)에 직접 전송되는 아웃바운드 이메일 메시지는 Microsoft Azure에서 특정 구독 형식에만 사용할 수 있습니다. TCP 포트 25를 사용하는 아웃바운드 SMTP 연결이 차단되었습니다. (포트 25는 주로 인증되지 않은 이메일 배달을 위해 사용됩니다.)

이 동작의 변경 내용은 2017년 11월 15일 이후 새 구독 및 새 배포에만 적용됩니다.

## <a name="recommended-method-of-sending-email"></a>이메일을 전송하는 권장 방법
인증된 SMTP 릴레이 서비스(일반적으로 TCP 포트 587 또는 443을 통해 연결되지만 다른 포트도 지원함)를 사용하여 Azure VM 또는 Azure App Services에서 이메일을 보내는 것이 좋습니다. 이러한 서비스는 타사 이메일 공급자가 메시지를 거부할 가능성을 최소화하도록 IP 또는 도메인 평판을 유지하기 위해 사용됩니다. 이러한 SMTP 릴레이 서비스는 [SendGrid](https://sendgrid.com/partners/azure/)를 비롯한 다양한 기능을 포함합니다. 실행 중인 온-프레미스에서 사용할 수 있는 보안 SMTP 릴레이 서비스가 설치되었을 수 있습니다.

이러한 이메일 배달 서비스를 사용하는 작업은 구독 형식에 상관없이 Azure에서 제한되지 않습니다.

## <a name="enterprise-agreement"></a>기업 계약
기업계약 Azure 사용자의 경우 인증된 릴레이를 사용하지 않고 이메일을 보내는 기술 기능이 변경되지 않았습니다. 신규 및 기존 기업계약 사용자는 모두 Azure 플랫폼에서 아무런 제한 없이 Azure VM에서 외부 이메일 공급자에게 직접 아웃바운드 이메일 배달을 시도할 수 있습니다. 이메일 공급자가 지정된 사용자에게서 들어오는 이메일을 허용하도록 보장하지 않지만 기업계약 구독 내의 Azure 플랫폼을 통해 배달 시도가 차단되지 않습니다. 특정 공급자를 포함하는 메시지 배달 또는 스팸 필터링 문제를 해결하기 위해 이메일 공급자와 직접 작업해야 합니다.

## <a name="pay-as-you-go"></a>Pay-As-You-Go
종량제 또는 Microsoft 파트너 네트워크 구독 제품에 2017년 11월 15일 이전에 등록한 경우 아웃바운드 이메일 배달을 시도하는 기술 기능이 변경되지 않았습니다. 모두 Azure 플랫폼에서 아무런 제한 없이 이러한 구독 내의 Azure VM에서 외부 이메일 공급자에게 직접 아웃바운드 이메일 배달을 계속 시도할 수 있습니다. 마찬가지로 이메일 공급자가 지정된 사용자에게서 들어오는 이메일을 수락하고, 사용자가 특정 공급자를 포함하는 메시지 배달 또는 스팸 필터링 문제를 해결하기 위해 이메일 공급자와 직접 작업하도록 보장되지 않습니다.

2017 년 11 월 15 일 이후에 생성 된 종 량 제 또는 Microsoft 파트너 네트워크 구독의 경우 이러한 구독 내에서 Vm 으로부터 직접 전송 되는 전자 메일을 차단 하는 기술 제한이 적용 됩니다. 이메일을 Azure VM에서 외부 이메일 공급자로 직접 보내려는 경우(인증된 SMTP 릴레이를 사용하지 않음) 제한을 제거하도록 요청할 수 있습니다. 요청은 Microsoft의 재량에 의해 검토 및 승인 되며 추가 사기 행위 확인을 수행한 후에만 승인 됩니다. 요청을 하려면 **기술** > **Virtual Network** > **연결** > 에서**전자 메일을 보낼 수 없음 (SMTP/포트 25)** 문제 유형을 사용 하 여 지원 사례를 엽니다. 배포에서 인증된 릴레이 사용하는 대신 메일 공급자에게 직접 메일을 보내야 하는 이유에 대한 세부 정보를 추가했는지 확인합니다.

종 량 제 또는 Microsoft 파트너 네트워크 구독이 제외 되 고 Vm이 Azure Portal에서 ' 중지 ' & ' 시작 됨 ' 상태로 전환 된 후에는 해당 구독 내의 모든 Vm이 제외 됩니다. 예외는 요청 된 구독에만 적용 되며 인터넷으로 직접 라우팅되는 가상 컴퓨터 트래픽에만 적용 됩니다. Azure [방화벽](https://azure.microsoft.com/services/azure-firewall/) 등의 azure PaaS 서비스를 통해 포트 25 트래픽 라우팅은 지원 되지 않습니다.

> [!NOTE]
> Microsoft에서 서비스 약관이 위반되었음을 판단하게 되면 이 예외를 취소할 수 있는 권한을 가집니다.

## <a name="msdn-azure-pass-azure-in-open-education-bizspark-and-free-trial"></a>MSDN, Azure Pass, Azure in Open, Education, BizSpark 및 평가판
2017 년 11 월 15 일 이후에 MSDN, Azure Pass, Azure in Open, 교육, BizSpark, Azure 스폰서쉽, Azure 학생, 평가판 또는 Visual Studio 구독을 만든 경우 이러한 구독 내 Vm에서 전자 메일 공급자로 직접 전송 되는 전자 메일을 차단 하는 기술 제한 사항이 있습니다. 제한 사항은 남용을 방지하기 위해 적용됩니다. 이 제한 사항을 제거하는 요청은 허가되지 않습니다.

이러한 구독 유형을 사용 하는 경우이 문서의 앞부분에 설명 된 대로 SMTP 릴레이 서비스를 사용 하거나 구독 유형을 변경 하는 것이 좋습니다.

## <a name="cloud-service-provider-csp"></a>CSP(클라우드 서비스 공급자)

CSP를 통해 Azure 리소스를 사용 하는 경우 보안 SMTP 릴레이를 사용할 수 없는 경우 CSP에 Microsoft를 대신 하 여 예외 차단 해제 요청을 만들도록 요청할 수 있습니다.

## <a name="need-help-contact-support"></a>도움 필요 시 지원에 문의

여전히 도움이 필요한 경우 지원에 [문의](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) 하 여 다음과 같은 문제 유형: **구독 관리** 문제 유형: **포트 25 메일 흐름을 사용 하도록 설정 하는 요청을**사용 하 여 문제를 신속 하 게 해결 하세요.
