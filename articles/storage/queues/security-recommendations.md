---
title: 큐 저장소에 대한 보안 권장 사항
titleSuffix: Azure Storage
description: Queue 저장소에 대한 보안 권장 사항에 대해 알아봅니다. 이 지침을 구현하면 공동 책임 모델에 설명된 대로 보안 의무를 이행하는 데 도움이 됩니다.
services: storage
author: tamram
ms.service: storage
ms.subservice: queues
ms.topic: conceptual
ms.date: 12/12/2019
ms.author: tamram
ms.custom: security-recommendations
ms.openlocfilehash: 8bb56db9eed962ac8f8202c61a7446527c15dfc6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80060894"
---
# <a name="security-recommendations-for-queue-storage"></a>큐 저장소에 대한 보안 권장 사항

이 문서에는 Queue 저장소에 대한 보안 권장 사항이 포함되어 있습니다. 이러한 권장 사항을 구현하면 공동 책임 모델에 설명된 대로 보안 의무를 이행하는 데 도움이 됩니다. Microsoft가 서비스 공급자의 책임을 수행하기 위해 수행하는 일에 대한 자세한 내용은 [클라우드 컴퓨팅에 대한 공유 책임을](https://gallery.technet.microsoft.com/Shared-Responsibilities-81d0ff91/file/225366/1/Shared%20Responsibility%20for%20Cloud%20Computing-2019-10-25.pdf)참조하십시오.

이 문서에 포함된 권장 사항 중 일부는 Azure 보안 센터에서 자동으로 모니터링할 수 있습니다. Azure 보안 센터는 Azure에서 리소스를 보호하는 첫 번째 방어선입니다. Azure 보안 센터에 대한 자세한 [내용은 Azure 보안 센터란 무엇입니까?](../../security-center/security-center-intro.md)

Azure 보안 센터는 Azure 리소스의 보안 상태를 주기적으로 분석하여 잠재적인 보안 취약점을 식별합니다. 그런 다음 이를 해결하는 방법에 대한 권장 사항을 제공합니다. Azure 보안 센터 권장 사항에 대한 자세한 내용은 [Azure 보안 센터의 보안 권장 사항을](../../security-center/security-center-recommendations.md)참조하십시오.

## <a name="data-protection"></a>데이터 보호

| 권장 | 주석 | Security Center |
|-|----|--|
| Azure 리소스 관리자 배포 모델 사용 | 우수한 액세스 제어(RBAC) 및 감사, 리소스 관리자 기반 배포 및 거버넌스, 관리되는 ID에 대한 액세스, 액세스 등 중요한 보안 향상을 위해 Azure Resource Manager 배포 모델을 사용하여 새 저장소 계정을 만듭니다. Azure 키 자격 증명 모음에 대한 보안 검색및 Azure 저장소 데이터 및 리소스에 대한 액세스에 대한 Azure AD 기반 인증 및 권한 부여를 사용합니다. 가능하면 클래식 배포 모델을 사용하는 기존 저장소 계정을 마이그레이션하여 Azure 리소스 관리자를 사용합니다. Azure 리소스 관리자에 대한 자세한 내용은 [Azure 리소스 관리자 개요를](/azure/azure-resource-manager/resource-group-overview)참조하십시오. | - |
| 모든 저장소 계정에서 **보안 전송 필수** 옵션 사용 | 보안 전송 **필수** 옵션을 사용하도록 설정하면 저장소 계정에 대해 수행된 모든 요청이 보안 연결을 통해 수행되어야 합니다. HTTP를 통해 이루어진 모든 요청은 실패합니다. 자세한 내용은 [Azure 저장소에서 보안 전송 필요를](../common/storage-require-secure-transfer.md)참조하십시오. | [예](../../security-center/security-center-sql-service-recommendations.md) |
| 모든 스토리지 계정에 대한 고급 위협 보호 지원 | Azure Storage에 대한 고급 위협 보호는 저장소 계정에 액세스하거나 악용하려는 이상하고 잠재적으로 유해한 시도를 감지하는 추가 보안 인텔리전스 계층을 제공합니다. Azure Security Center에서 활동이 이상 징후가 발생할 때 보안 경고가 트리거되고 의심스러운 활동에 대한 세부 정보와 위협을 조사하고 해결하는 방법에 대한 권장 사항과 함께 구독 관리자에게 전자 메일을 통해 전송됩니다. 자세한 내용은 [Azure 저장소에 대한 고급 위협 보호를](../common/storage-advanced-threat-protection.md)참조하십시오. | [예](../../security-center/security-center-sql-service-recommendations.md) |
| 공유 액세스 서명(SAS) 토큰을 HTTPS 연결로만 제한 | 클라이언트가 SAS 토큰을 사용하여 큐 데이터에 액세스하는 경우 HTTPS를 요구하면 도청 위험을 최소화하는 데 도움이 됩니다. 자세한 내용은 [공유 액세스 시그니처(SAS)를 사용하여 Azure Storage 리소스에 대한 제한된 액세스 권한 부여를](../common/storage-sas-overview.md)참조하십시오. | - |

## <a name="identity-and-access-management"></a>ID 및 액세스 관리

| 권장 | 주석 | Security Center |
|-|----|--|
| Azure Active Directory(Azure AD)를 사용하여 큐 데이터에 대한 액세스 권한 부여 | Azure AD는 큐 저장소에 대한 요청을 승인하기 위해 공유 키를 통해 뛰어난 보안과 사용 편의성을 제공합니다. 자세한 내용은 Azure Active Directory 를 [사용하여 Azure Blob 및 큐에 대한 액세스 권한 부여를](../common/storage-auth-aad.md)참조하세요. | - |
| RBAC를 통해 Azure AD 보안 보안 주체에 권한을 할당할 때 최소 권한의 주체를 염두에 두십시오. | 사용자, 그룹 또는 응용 프로그램에 역할을 할당할 때 해당 보안 주체가 작업을 수행하는 데 필요한 권한만 부여합니다. 리소스에 대한 액세스를 제한하면 의도하지 않은 데이터와 악의적인 오용을 방지할 수 있습니다. | - |
| Azure 키 볼트로 계정 액세스 키 보호 | Azure AD를 사용하여 Azure 저장소에 대한 요청을 승인하는 것이 좋습니다. 그러나 공유 키 인증을 사용해야 하는 경우 Azure Key Vault를 사용하여 계정 키를 보호합니다. 런타임에 키 자격 증명 모음에서 키를 응용 프로그램에 저장하는 대신 검색할 수 있습니다. | - |
| 계정 키주기재생 | 계정 키를 주기적으로 회전하면 악의적인 행위자에게 데이터가 노출될 위험이 줄어듭니다. | - |
| SAS에 사용 권한을 할당할 때 최소 권한의 주체를 염두에 두십시오. | SAS를 만들 때 클라이언트가 해당 기능을 수행하는 데 필요한 권한만 지정합니다. 리소스에 대한 액세스를 제한하면 의도하지 않은 데이터와 악의적인 오용을 방지할 수 있습니다. | - |
| 고객에게 발급하는 모든 SAS에 대해 해지 계획을 수립하십시오. | SAS가 손상된 경우 가능한 한 빨리 해당 SAS를 해지해야 합니다. 사용자 위임 SAS를 취소하려면 사용자 위임 키를 해지하여 해당 키와 연결된 모든 서명을 신속하게 무효화합니다. 저장된 액세스 정책과 연결된 서비스 SAS를 해지하려면 저장된 액세스 정책을 삭제하거나 정책 이름을 바꾸거나 만료 시간을 과거의 시간으로 변경할 수 있습니다. 자세한 내용은 [공유 액세스 시그니처(SAS)를 사용하여 Azure Storage 리소스에 대한 제한된 액세스 권한 부여를](../common/storage-sas-overview.md)참조하십시오.  | - |
| 서비스 SAS가 저장된 액세스 정책과 연결되지 않은 경우 만료 시간을 1시간 이하로 설정합니다. | 저장된 액세스 정책과 연결되지 않은 서비스 SAS는 해지할 수 없습니다. 따라서 SAS가 1시간 이하로 유효할 수 있도록 만료 시간을 제한하는 것이 좋습니다. | - |

## <a name="networking"></a>네트워킹

| 권장 | 주석 | Security Center |
|-|----|--|
| 방화벽 규칙 사용 | 방화벽 규칙을 구성하여 지정된 IP 주소 또는 범위에서 발생한 요청 또는 Azure 가상 네트워크(VNet)의 서브넷 목록에서 시작된 요청으로 저장소 계정에 대한 액세스를 제한합니다. 방화벽 규칙 구성에 대한 자세한 내용은 [Azure 파일 동기화 프록시 및 방화벽 설정을](../files/storage-sync-files-firewall-and-proxy.md)참조하십시오. | - |
| 신뢰할 수 있는 Microsoft 서비스가 저장소 계정에 액세스할 수 있도록 허용 | Azure 가상 네트워크(VNet) 내에서 운영되는 서비스 또는 허용된 공용 IP 주소에서 요청이 발생하는 경우가 아니면 저장소 계정에 대한 방화벽 규칙을 설정하면 기본적으로 들어오는 데이터 요청이 차단됩니다. 차단되는 요청에는 다른 Azure 서비스, Azure Portal, 로깅 및 메트릭 서비스 등이 포함됩니다. 신뢰할 수 있는 Microsoft 서비스가 저장소 계정에 액세스할 수 있도록 예외를 추가하여 다른 Azure 서비스의 요청을 허용할 수 있습니다. 신뢰할 수 있는 Microsoft 서비스에 대한 예외 추가에 대한 자세한 내용은 [Azure 파일 동기화 프록시 및 방화벽 설정을](../files/storage-sync-files-firewall-and-proxy.md)참조하십시오.| - |
| 전용 끝점 사용 | 개인 끝점은 Azure 가상 네트워크(VNet)에서 저장소 계정에 개인 IP 주소를 할당합니다. 개인 링크를 통해 VNet과 저장소 계정 간의 모든 트래픽을 보호합니다. 개인 끝점에 대한 자세한 내용은 [Azure 개인 끝점을 사용하여 저장소 계정에 개인적으로 연결 참조](../../private-link/create-private-endpoint-storage-portal.md) | - |
| 특정 네트워크에 대한 네트워크 액세스 제한 | 액세스가 필요한 클라이언트를 호스팅하는 네트워크에 대한 네트워크 액세스를 제한하면 리소스가 네트워크 공격에 노출되는 것을 줄일 수 있습니다. | [예](../../security-center/security-center-sql-service-recommendations.md) |

## <a name="loggingmonitoring"></a>로깅/모니터링

| 권장 | 주석 | Security Center |
|-|----|--|
| 요청이 승인되는 방식 추적 | Azure 저장소 로깅을 활성화하여 Azure Storage에 대해 이루어진 각 요청이 승인된 방식을 추적합니다. 로그는 OAuth 2.0 토큰을 사용하거나 공유 키를 사용하거나 SAS(공유 액세스 서명)를 사용하여 요청이 익명으로 이루어졌는지 여부를 나타냅니다. 자세한 내용은 [Azure Storage 분석 로깅](../common/storage-analytics-logging.md)을 참조하십시오. | - |

## <a name="next-steps"></a>다음 단계

- [Azure 보안 설명서](https://docs.microsoft.com//azure/security/)
- [보안 개발 문서](https://docs.microsoft.com/azure/security/develop/).
