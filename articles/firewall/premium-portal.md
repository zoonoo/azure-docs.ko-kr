---
title: Azure Portal에서 Azure 방화벽 프리미엄 미리 보기
description: Azure Portal에서 Azure 방화벽 프리미엄 미리 보기에 대해 알아봅니다.
author: vhorne
ms.service: firewall
services: firewall
ms.topic: conceptual
ms.date: 02/16/2021
ms.author: victorh
ms.openlocfilehash: 3d56fc73faeb0d48ba7e5b21449c61d6213afa40
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100549696"
---
# <a name="azure-firewall-premium-preview-in-the-azure-portal"></a>Azure Portal에서 Azure 방화벽 프리미엄 미리 보기

> [!IMPORTANT]
> Azure 방화벽 프리미엄은 현재 공개 미리 보기로 제공 됩니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

 Azure Firewall 프리미엄 미리 보기는 매우 중요하고 규정을 준수하는 환경에 필요한 기능을 갖춘 차세대 방화벽입니다. 이 관리 팩에는 다음과 같은 기능이 포함되어 있습니다.

- **TLS 검사** -아웃 바운드 트래픽을 해독 하 고, 데이터를 처리 한 다음 데이터를 암호화 하 고 대상에 보냅니다.
- **Idps** -네트워크 침입 감지 및 방지 시스템 (idps)을 사용 하면 악의적인 활동의 네트워크 활동을 모니터링 하 고,이 활동에 대 한 정보를 기록 하 고, 보고 하 고, 선택적으로 차단할 수 있습니다.
- **Url 필터링** -Azure 방화벽의 FQDN 필터링 기능을 확장 하 여 전체 URL을 고려 합니다. 예를 들어 `www.contoso.com/a/c` 대신 `www.contoso.com` 입니다.
- **웹 범주** -관리자는 도박 websites, 소셜 미디어 웹 사이트 등의 웹 사이트 범주에 대 한 사용자 액세스를 허용 하거나 거부할 수 있습니다.

자세한 내용은 [Azure 방화벽 프리미엄 기능](premium-features.md)을 참조 하세요.

## <a name="deploy-the-firewall"></a>방화벽 배포

Azure 방화벽 프리미엄 미리 보기 배포는 표준 Azure 방화벽을 배포 하는 것과 비슷합니다.

:::image type="content" source="media/premium-portal/premium-portal.png" alt-text="포털 배포":::

**방화벽 계층** 의 경우 **프리미엄 (미리 보기)** 을 선택 하 고 **방화벽 정책** 에 기존 프리미엄 정책을 선택 하거나 새로 만듭니다.

## <a name="configure-the-premium-policy"></a>프리미엄 정책 구성

프리미엄 방화벽 정책을 구성 하는 것은 표준 방화벽 정책을 구성 하는 것과 비슷합니다. 프리미엄 정책을 사용 하면 프리미엄 기능을 구성할 수 있습니다.

:::image type="content" source="media/premium-portal/premium-policy.png" alt-text="프리미엄 정책 배포":::

### <a name="rule-configuration"></a>규칙 구성

프리미엄 정책에서 응용 프로그램 규칙을 구성 하는 경우 추가 프리미엄 기능을 구성할 수 있습니다.

:::image type="content" source="media/premium-portal/premium-application-rule.png" alt-text="프리미엄 규칙":::

## <a name="next-steps"></a>다음 단계

작동 중인 Azure 방화벽 프리미엄 미리 보기 기능을 확인 하려면 [Azure 방화벽 프리미엄 미리 보기 배포 및 구성](premium-deploy.md)을 참조 하세요.