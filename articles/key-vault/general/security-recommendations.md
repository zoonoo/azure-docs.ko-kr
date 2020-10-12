---
title: Azure Key Vault에 대한 보안 추천 사항
description: Azure Key Vault에 대한 보안 권장 사항 이 지침을 구현하면 공유 책임 모델에 설명된 대로 보안 의무를 충족하는 데 도움이 됩니다.
services: key-vault
author: msmbaldwin
manager: rkarlin
ms.service: key-vault
ms.subservice: general
ms.topic: article
ms.date: 09/30/2019
ms.author: mbaldwin
ms.custom: security-recommendations
ms.openlocfilehash: 0c541c848290988bb1f358f045abb2d0cd221de7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "87386761"
---
# <a name="security-recommendations-for-azure-key-vault"></a>Azure Key Vault에 대한 보안 추천 사항

이 문서에는 Azure Key Vault에 대한 보안 권장 사항이 포함되어 있습니다. 이러한 권장 사항을 구현하면 공유 책임 모델에 설명된 대로 보안 의무를 충족하는 데 도움이 됩니다. Microsoft에서 서비스 공급자 책임을 충족하기 위해 수행하는 작업에 대한 자세한 내용은 [클라우드 컴퓨팅에 대한 공유 책임](https://gallery.technet.microsoft.com/Shared-Responsibilities-81d0ff91)을 참조하세요.

이 문서에 포함된 일부 권장 사항은 Azure Security Center에서 자동으로 모니터링할 수 있습니다. Azure Security Center는 Azure에서 리소스를 보호하는 첫 번째 방어선입니다. 이는 Azure 리소스의 보안 상태를 정기적으로 분석하여 잠재적인 보안 취약성을 식별합니다. 그런 다음 이를 해결하는 방법에 대한 권장 사항을 제공합니다.

- Azure Security Center 권장 사항에 대한 자세한 내용은 [Azure Security Center의 보안 권장 사항](../../security-center/security-center-recommendations.md)을 참조하세요.
- Azure Security Center에 대한 자세한 내용은 [Azure Security Center란?](../../security-center/security-center-intro.md)을 참조하세요.

## <a name="data-protection"></a>데이터 보호

| 권장 | 주석 | Security Center |
|-|----|--|
|일시 삭제 사용 | [일시 삭제](soft-delete-overview.md)를 사용하면 삭제된 자격 증명 모음 및 자격 증명 모음 개체를 복구할 수 있습니다. |  - |
| 자격 증명 모음 데이터에 대한 액세스 제한  | 최소 권한의 원칙에 따라 자격 증명 모음 데이터에 대한 액세스 권한이 있는 조직 멤버를 제한합니다. |  - |

## <a name="identity-and-access-management"></a>ID 및 액세스 관리

| 권장 | 주석 | Security Center |
|-|----|--|
| 기여자 액세스 권한이 있는 사용자 수 제한 | 사용자에게 Key Vault 관리 평면에 대한 기여자 사용 권한이 있는 경우 이 사용자는 Key Vault 액세스 정책을 설정하여 스스로 데이터 평면에 대한 액세스 권한을 부여할 수 있습니다. Key Vault에 대한 기여자 역할 액세스 권한이 있는 사용자를 긴밀하게 제어해야 합니다. 액세스 권한이 있는 사용자만 자격 증명 모음에 액세스하고 관리할 수 있어야 합니다. [Key Vault에 대한 보안 액세스](secure-your-key-vault.md))를 읽을 수 있습니다. | - |

## <a name="monitoring"></a>모니터링

| 권장 | 주석 | Security Center |
|-|----|--|
 Key Vault의 진단 로그를 사용하도록 설정해야 함 | 로그를 사용하도록 설정하고 최대 1년 간 보존합니다. 이렇게 하면 보안 인시던트가 발생하거나 네트워크가 손상된 경우 조사 목적으로 활동 내역을 다시 만들 수 있습니다. | [예](../../security-center/security-center-identity-access.md) |
| Azure Key Vault 로그에 액세스할 수 있는 사용자 제한 | [Key Vault 로그](logging.md))는 자격 증명 모음, 키, 비밀의 생성 또는 삭제와 같이 자격 증명 모음에서 수행되는 활동에 대한 정보를 저장하고 조사하는 동안 사용할 수 있습니다. |  - |

## <a name="networking"></a>네트워킹

| 권장 | 주석 | Security Center |
|-|----|--|
|네트워크 노출 제한 | 네트워크 액세스는 자격 증명 모음 액세스가 필요한 솔루션에서 사용하는 가상 네트워크로 제한되어야 합니다. [Azure Key Vault의 가상 네트워크 서비스 엔드포인트](overview-vnet-service-endpoints.md))에 대한 정보를 검토하세요. | - |

## <a name="next-steps"></a>다음 단계

애플리케이션 공급자에게 추가 보안 요구 사항이 있는지 확인합니다. 보안 애플리케이션 개발에 대한 자세한 내용은 [보안 개발 설명서](../../security/fundamentals/abstract-develop-secure-apps.md)를 참조하세요.
