---
title: Azure에서 아웃바운드 SMTP 연결 문제 해결 | Microsoft Docs
description: 전자 메일을 보내는 권장 방법 및 Azure의 아웃 바운드 SMTP 연결 문제를 해결 하는 방법에 대해 알아봅니다.
services: virtual-network
author: genlin
manager: dcscontentpm
editor: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/04/2021
ms.author: genli
ms.openlocfilehash: c28790b2ef423a3d0f996d7c6030b04198756eb1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102607614"
---
# <a name="troubleshoot-outbound-smtp-connectivity-problems-in-azure"></a>Azure의 아웃 바운드 SMTP 연결 문제 해결

2017 년 11 월 15 일부 터 VM (가상 컴퓨터)에서 외부 도메인 (예: outlook.com 및 gmail.com)으로 직접 전송 되는 아웃 바운드 전자 메일 메시지는 Azure의 특정 구독 형식에만 사용할 수 있습니다. TCP 포트 25를 사용하는 아웃바운드 SMTP 연결이 차단되었습니다. 포트 25는 주로 인증 되지 않은 전자 메일 배달에 사용 됩니다.

이러한 동작 변경은 2017 년 11 월 15 일 이후에 만들어진 구독 및 배포에만 적용 됩니다.

## <a name="recommended-method-of-sending-email"></a>이메일을 전송하는 권장 방법

인증 된 SMTP 릴레이 서비스를 사용 하 여 Azure Vm 또는 Azure App Service에서 전자 메일을 보내는 것이 좋습니다. 이러한 릴레이 서비스는 일반적으로 TCP 포트 587을 통해 연결 되지만 다른 포트를 지원 합니다. 이러한 서비스는 타사 전자 메일 공급자가 메시지를 거부할 가능성을 최소화 하기 위해 IP 또는 도메인 평판을 유지 하는 데 사용 됩니다. [SendGrid](https://sendgrid.com/partners/azure/) 는 이러한 SMTP 릴레이 서비스 이지만 다른는 있습니다. 또한 사용할 수 있는 온-프레미스에서 실행 되는 보안 SMTP 릴레이 서비스가 있을 수 있습니다.

이러한 이메일 배달 서비스를 사용하는 작업은 구독 형식에 상관없이 Azure에서 제한되지 않습니다.

## <a name="enterprise-agreement"></a>기업 계약

기업계약 Azure 사용자의 경우 인증된 릴레이를 사용하지 않고 이메일을 보내는 기술 기능이 변경되지 않았습니다. 신규 및 기존 기업계약 사용자는 모두 Azure 플랫폼에서 아무런 제한 없이 Azure VM에서 외부 이메일 공급자에게 직접 아웃바운드 이메일 배달을 시도할 수 있습니다. 전자 메일 공급자가 지정 된 사용자의 수신 전자 메일을 허용 한다는 보장이 없습니다. 그러나 Azure 플랫폼은 기업계약 구독 내의 Vm에 대 한 배달 시도를 차단 하지 않습니다. 특정 공급자와 관련 된 메시지 배달 또는 스팸 필터링 문제를 해결 하려면 전자 메일 공급자로 직접 작업 해야 합니다.

## <a name="pay-as-you-go"></a>종량제

종 량 제 구독의 경우 2017 년 11 월 15 일 이전에 등록 한 경우에는 아웃 바운드 전자 메일 배달을 시도 하는 기술 기능이 변경 되지 않습니다. Azure 플랫폼의 제한 없이, 이러한 구독 내의 Azure Vm에서 외부 전자 메일 공급자로 직접 아웃 바운드 전자 메일 배달을 시도할 수 있습니다. 또한 전자 메일 공급자가 지정 된 사용자 로부터 들어오는 전자 메일을 허용 한다는 보장이 없습니다. 사용자는 전자 메일 공급자로 직접 작업 하 여 특정 공급자와 관련 된 메시지 배달 또는 스팸 필터링 문제를 해결 해야 합니다.

2017 년 11 월 15 일 이후에 생성 된 종 량 제 구독의 경우 구독 내의 Vm에서 직접 전송 되는 전자 메일을 차단 하는 기술 제한이 있습니다. 인증 된 SMTP 릴레이를 사용 하지 않고 Azure Vm에서 외부 전자 메일 공급자로 직접 전자 메일을 보내고 지불 기록과 관련 된 계정이 있는 경우 제한을 제거 하도록 요청할 수 있습니다. 이렇게 하려면 Azure Portal의 Azure Virtual Network 리소스에 대 한 **진단 및 해결** 블레이드의 **연결** 섹션에서이 작업을 수행할 수 있습니다. 요청이 수락 되 면 구독이 사용 하도록 설정 되거나 다음 단계에 대 한 지침을 받게 됩니다. 

종 량 제 구독이 제외 되 고 Azure Portal에서 Vm이 중지 되었다가 다시 시작 되 면 해당 구독의 모든 Vm은 제외 됩니다. 예외는 요청 된 구독에만 적용 되 고 인터넷으로 직접 라우팅되는 VM 트래픽에만 적용 됩니다.

> [!NOTE]
> Microsoft는 서비스 약관에 대 한 위반이 발생 한 것으로 확인 되는 경우 이러한 예외를 해지할 수 있는 권리를 보유 합니다.

## <a name="msdn-azure-pass-azure-in-open-education-azure-for-students-visual-studio-and-free-trial"></a>MSDN, Azure Pass, Azure in Open, 교육, 학생용 Azure, Visual Studio 및 무료 평가판

2017 년 11 월 15 일 이후에 다음 구독 유형 중 하나를 만든 경우 구독 내 Vm에서 전자 메일 공급자로 직접 전송 되는 전자 메일을 차단 하는 기술 제한 사항이 있습니다.
- MSDN
- Azure Pass
- Azure in Open
- Education
- Azure for Students
- 무료 평가판
- 모든 Visual Studio 구독  

남용 방지를 위해 제한이 적용 됩니다. 이러한 제한을 제거 하는 요청은 허용 되지 않습니다.

이러한 구독 유형을 사용 하는 경우이 문서의 앞부분에 설명 된 대로 SMTP 릴레이 서비스를 사용 하거나 구독 유형을 변경 하는 것이 좋습니다.

## <a name="cloud-solution-provider"></a>클라우드 솔루션 공급자

클라우드 솔루션 공급자를 통해 Azure 리소스를 사용 하는 경우 Azure Portal의 가상 네트워크 리소스에 대 한 **진단 및 해결** 창의 **연결** 섹션에서 제한을 제거 하도록 요청할 수 있습니다. 요청이 수락 되 면 구독이 사용 하도록 설정 되거나 다음 단계에 대 한 지침을 받게 됩니다.

## <a name="microsoft-partner-network-bizspark-plus-or-azure-sponsorship"></a>Microsoft 파트너 네트워크, BizSpark 및 Azure 스폰서쉽

2017 년 11 월 15 일 이후에 생성 된 다음 형식의 구독의 경우 구독 내의 Vm에서 직접 전송 되는 전자 메일을 차단 하는 기술 제한이 있습니다.

- Microsoft 파트너 네트워크 (MPN)
- BizSpark Plus
- Azure 스폰서쉽

인증 된 SMTP 릴레이를 사용 하지 않고 Azure vm에서 외부 전자 메일 공급자로 직접 전자 메일을 보낼 수 있도록 하려면 **기술**  >  **Virtual Network**  >  **연결** 에서  >  **전자 메일을 보낼 수 없습니다 (SMTP/포트 25)**. 배포가 인증 된 릴레이를 사용 하는 대신 메일 공급자에 게 직접 메일을 보내야 하는 이유에 대 한 세부 정보를 추가 해야 합니다. Microsoft에서 요청을 검토 하 고 승인 합니다. 추가 antifraud 검사가 완료 된 후에만 요청이 허용 됩니다. 

구독이 제외 되 고 Vm이 중지 되 고 Azure Portal 다시 시작 되 면 해당 구독의 모든 Vm은 제외 됩니다. 예외는 요청 된 구독에만 적용 되 고 인터넷으로 직접 라우팅되는 VM 트래픽에만 적용 됩니다.

## <a name="changing-subscription-type"></a>구독 유형 변경

구독 유형 또는 클라우드 솔루션 공급자 또는 종 량 제 구독이 승인 되 면 VM을 중지 하 고 할당을 취소 한 다음 다시 시작 하 여 새 정책을 적용 해야 합니다. 마찬가지로, 기본적으로 허용 되는 구독 유형으로 변경 하 고 허용 되지 않는 구독 유형으로 변경 하는 경우 배포에 대 한 변경 내용으로 인해 포트 25가 차단 될 수 있습니다.

## <a name="need-help-contact-support"></a>도움 필요 시 지원에 문의

도움이 필요한 경우 [지원에 문의](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)하여 문제를 신속하게 해결하세요. 이 문제 유형 사용: **기술**  >  **Virtual Network**  >  **연결** 에서  >  **전자 메일을 보낼 수 없습니다 (SMTP/포트 25)**.
