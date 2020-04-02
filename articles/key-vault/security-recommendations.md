---
title: Azure 키 자격 증명 모음에 대한 보안 권장 사항
description: Azure 키 자격 증명 모음에 대한 보안 권장 사항입니다. 이 지침을 구현하면 공동 책임 모델에 설명된 대로 보안 의무를 이행하는 데 도움이 됩니다.
services: key-vault
author: msmbaldwin
manager: rkarlin
ms.service: key-vault
ms.subservice: general
ms.topic: article
ms.date: 09/30/2019
ms.author: mbaldwin
ms.custom: security-recommendations
ms.openlocfilehash: d18dca2d7c44ac9db5ebabc5cc10f2f0b3f2c069
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/02/2020
ms.locfileid: "80546369"
---
# <a name="security-recommendations-for-azure-key-vault"></a>Azure 키 자격 증명 모음에 대한 보안 권장 사항

이 문서에는 Azure 키 자격 증명 모음에 대한 보안 권장 사항이 포함되어 있습니다. 이러한 권장 사항을 구현하면 공동 책임 모델에 설명된 대로 보안 의무를 이행하는 데 도움이 됩니다. Microsoft가 서비스 공급자의 책임을 수행하기 위해 수행하는 일에 대한 자세한 내용은 [클라우드 컴퓨팅에 대한 공유 책임을](https://gallery.technet.microsoft.com/Shared-Responsibilities-81d0ff91)참조하십시오.

이 문서에 포함된 권장 사항 중 일부는 Azure 보안 센터에서 자동으로 모니터링할 수 있습니다. Azure 보안 센터는 Azure에서 리소스를 보호하는 첫 번째 방어선입니다. Azure 리소스의 보안 상태를 주기적으로 분석하여 잠재적인 보안 취약점을 식별합니다. 그런 다음 이를 해결하는 방법에 대한 권장 사항을 제공합니다.

- Azure 보안 센터 권장 사항에 대한 자세한 내용은 [Azure 보안 센터의 보안 권장 사항을](../security-center/security-center-recommendations.md)참조하십시오.
- Azure 보안 센터에 대한 자세한 [내용은 Azure 보안 센터란 무엇입니까?](../security-center/security-center-intro.md)

## <a name="data-protection"></a>데이터 보호

| 권장 | 주석 | Security Center |
|-|----|--|
|소프트 삭제 사용 | [소프트 삭제를](key-vault-ovw-soft-delete.md) 사용하면 삭제 된 볼트 및 볼트 객체를 복구 할 수 있습니다. |  - |
| 볼트 데이터에 대한 액세스 제한  | 최소 권한 원칙을 따르고 조직의 구성원이 자격 증명 모음 데이터에 액세스할 수 있는 권한을 제한합니다. |  - |

## <a name="identity-and-access-management"></a>ID 및 액세스 관리

| 권장 | 주석 | Security Center |
|-|----|--|
| 기여자 액세스 권한이 있는 사용자 수 제한 | 사용자가 키 볼트 관리 평면에 대한 기여자 권한을 가지고 있는 경우 사용자는 키 볼트 액세스 정책을 설정하여 데이터 평면에 대한 액세스 권한을 부여할 수 있습니다. 키 자격 증명 모음에 대한 기여자 역할 액세스 권한이 있는 사람을 엄격하게 제어해야 합니다. 권한이 있는 사용자만 볼트에 액세스하고 관리할 수 있는지 확인합니다. [키 자격 증명 모음에 대한 보안 액세스를](key-vault-secure-your-key-vault.md) 읽을 수 있습니다. | - |

## <a name="monitoring"></a>모니터링

| 권장 | 주석 | Security Center |
|-|----|--|
 Key Vault의 진단 로그를 사용하도록 설정해야 합니다. | 로그를 사용하도록 설정하고 최대 1년 간 보존합니다. 이렇게 하면 보안 인시던트가 발생하거나 네트워크가 손상된 경우 조사 목적으로 활동 내역을 다시 만들 수 있습니다. | [예](../security-center/security-center-identity-access.md) |
| Azure 키 자격 증명 모음 로그에 액세스할 수 있는 사용자 제한 | [키 볼트 로그는](key-vault-logging.md) 볼트, 키, 비밀의 생성 또는 삭제와 같은 볼트에서 수행된 활동에 대한 정보를 저장하며 조사 중에 사용될 수 있습니다. |  - |

## <a name="networking"></a>네트워킹

| 권장 | 주석 | Security Center |
|-|----|--|
|네트워크 노출 제한 | 네트워크 액세스는 볼트 액세스가 필요한 솔루션에서 사용하는 가상 네트워크로 제한되어야 합니다. [Azure 키 자격 증명 모음에 대한 가상 네트워크 서비스 끝점에 대한](key-vault-overview-vnet-service-endpoints.md) 정보 검토 | - |

## <a name="next-steps"></a>다음 단계

추가 보안 요구 사항이 있는지 확인하려면 응용 프로그램 공급자에게 문의하십시오. 보안 응용 프로그램 개발에 대한 자세한 내용은 [보안 개발 문서를](../security/fundamentals/abstract-develop-secure-apps.md)참조하십시오.
