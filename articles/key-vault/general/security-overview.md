---
title: Azure Key Vault 보안 개요
description: Azure Key Vault에 대 한 보안 기능 및 모범 사례에 대 한 개요입니다.
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 01/05/2021
ms.author: mbaldwin
ms.openlocfilehash: c7635fdc2012ab404709733d8f5849465c2ee82f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "99071571"
---
# <a name="azure-key-vault-security"></a>Azure Key Vault 보안

Azure Key Vault를 사용 하 여 클라우드에서 인증서, 연결 문자열, 암호 등의 암호화 키와 암호를 보호 합니다. 중요 한 중요 한 데이터를 저장 하는 경우 자격 증명 모음 및 저장소에 저장 된 데이터의 보안을 최대화 하는 단계를 수행 해야 합니다.

이 문서에서는 보안 기능 및 Azure Key Vault에 대 한 모범 사례에 대 한 개요를 제공 합니다. 

> [!NOTE]
> Azure Key Vault 보안 권장 사항에 대 한 포괄적인 목록은 [Azure Key Vault의 보안 기준](security-baseline.md)을 참조 하세요.

## <a name="network-security"></a>네트워크 보안

액세스 권한이 있는 IP 주소를 지정하여 자격 증명 모음의 노출을 줄일 수 있습니다. Azure Key Vault의 가상 네트워크 서비스 엔드포인트를 사용하면 지정된 가상 네트워크에 대한 액세스를 제한할 수 있습니다. 엔드포인트를 사용하면 IPv4(인터넷 프로토콜 버전 4) 주소 범위 목록에 대한 액세스를 제한할 수도 있습니다. 해당 소스 외부에서 키 자격 증명 모음에 연결하는 모든 사용자는 액세스가 거부됩니다.  자세한 내용은 [Azure Key Vault의 가상 네트워크 서비스 끝점](overview-vnet-service-endpoints.md) 을 참조 하세요.

방화벽 규칙이 적용되면 사용자의 요청이 허용되는 가상 네트워크 또는 IPv4 주소 범위에서 시작된 경우에만 사용자가 Key Vault에서 데이터를 읽을 수 있습니다. Azure Portal에서 Key Vault에 액세스하는 경우도 마찬가지입니다. 사용자가 Azure Portal에서 키 자격 증명 모음으로 이동할 수 있다고 해도 해당 클라이언트 머신이 허용 목록에 없는 경우 키, 비밀 또는 인증서를 나열하지 못할 수 있습니다. 다른 Azure 서비스의 Key Vault 선택기도 마찬가지입니다. 방화벽 규칙이 사용자의 클라이언트 머신을 금지하는 경우 해당 사용자는 키 자격 증명 모음 목록을 확인할 수 있지만 키를 나열하지는 못합니다.  구현 단계는 [Azure Key Vault 방화벽 및 가상 네트워크 구성](network-security.md) 을 참조 하세요.

Azure Private Link Service를 사용하면 가상 네트워크의 프라이빗 엔드포인트를 통해 Azure Key Vault 및 Azure 호스팅 고객/파트너 서비스에 액세스할 수 있습니다. Azure 프라이빗 엔드포인트는 Azure Private Link에서 제공하는 서비스에 비공개로 안전하게 연결하는 네트워크 인터페이스입니다. 프라이빗 엔드포인트는 VNet의 개인 IP 주소를 사용하여 서비스를 VNet으로 효과적으로 가져옵니다. 서비스에 대한 모든 트래픽은 프라이빗 엔드포인트를 통해 라우팅할 수 있으므로 게이트웨이, NAT 디바이스, ExpressRoute 또는 VPN 연결 또는 공용 IP 주소가 필요하지 않습니다. 가상 네트워크와 서비스 간의 트래픽은 Microsoft 백본 네트워크를 통해 이동하여 공용 인터넷에서 노출을 제거합니다. Azure 리소스의 인스턴스에 연결하여 액세스 제어에서 가장 높은 수준의 세분성을 제공할 수 있습니다.  구현 단계는 [Azure 개인 링크와 Key Vault 통합](private-link-service.md) 을 참조 하세요.

## <a name="tls-and-https"></a>TLS 및 HTTPS

- Key Vault 프런트 엔드 (데이터 평면)는 다중 테 넌 트 서버입니다. 즉, 서로 다른 고객의 키 자격 증명 모음이 동일한 공용 IP 주소를 공유할 수 있습니다. 격리를 위해 각 HTTP 요청은 다른 요청과 독립적으로 인증 되 고 권한이 부여 됩니다.
- 이전 버전의 TLS는 취약성을 보고할 수 있지만 공용 IP 주소는 공유 되므로 key vault 서비스 팀은 전송 수준에서 개별 키 자격 증명 모음에 대해 이전 버전의 TLS를 사용 하지 않도록 설정할 수 없습니다.
- HTTPS 프로토콜을 사용 하면 클라이언트가 TLS 협상에 참여할 수 있습니다. **클라이언트는 최신 버전의 TLS를 적용할 수** 있으며, 클라이언트에서이를 수행할 때마다 전체 연결에서 해당 하는 수준 보호를 사용 합니다. 이전 TLS 버전을 지 원하는 Key Vault 사실은 최신 TLS 버전을 사용 하는 연결 보안을 약화 하지 않습니다.
- TLS 프로토콜의 알려진 취약성에도 불구 하 고 공격자가 취약성이 있는 TLS 버전과의 연결을 시작할 때 악의적인 에이전트가 키 자격 증명 모음에서 모든 정보를 추출할 수 있도록 하는 알려진 공격은 없습니다. 공격자는 자신을 인증 하 고 권한을 부여 해야 하며 합법적인 클라이언트가 항상 최신 TLS 버전에 연결 하는 한 이전 TLS 버전의 취약성으로 인해 자격 증명이 누출 되지 않을 수 있습니다.

## <a name="identity-management"></a>ID 관리

Azure 구독에 Key Vault을 만들 때 해당 구독의 Azure AD 테넌트에 자동으로 연결됩니다. 자격 증명 모음에서 콘텐츠를 관리하거나 검색하려는 사용자는 Azure AD의 인증을 받은 사용자여야 합니다. 두 경우 모두 응용 프로그램은 다음 세 가지 방법으로 Key Vault에 액세스할 수 있습니다.

- **응용 프로그램 전용**: 응용 프로그램은 서비스 주체 또는 관리 id를 나타냅니다. 이 id는 정기적으로 키 자격 증명 모음에서 인증서, 키 또는 암호에 액세스 해야 하는 응용 프로그램에 가장 일반적으로 사용 되는 시나리오입니다. 이 시나리오가 작동 하려면 `objectId` 응용 프로그램의를 액세스 정책에 지정 하 고을 `applicationId` 지정 _하지_ 않아야 하거나를 지정 해야 합니다 `null` .
- **사용자 전용**: 사용자는 테 넌 트에 등록 된 응용 프로그램에서 키 자격 증명 모음에 액세스 합니다. Azure PowerShell과 Azure Portal이 이러한 액세스 유형의 예제입니다. 이 시나리오가 작동 하려면 `objectId` 사용자의을 액세스 정책에 지정 하 고을 `applicationId` 지정 _하지_ 않아야 하거나를 지정 해야 합니다 `null` .
- **응용 프로그램-사용자** ( _복합 id_ 라고도 함): 사용자는 특정 응용 프로그램에서 키 자격 증명 모음에 액세스 해야 _하며,_ 응용 프로그램은 사용자를 가장 하기 위해 obo (인증 시) 흐름을 사용 해야 합니다. 이 시나리오가 작동 하려면 `applicationId` 및 모두 `objectId` 액세스 정책에서 지정 해야 합니다. 는 `applicationId` 필수 응용 프로그램을 식별 하 고는 `objectId` 사용자를 식별 합니다. 현재이 옵션은 데이터 평면 Azure RBAC (미리 보기)에서 사용할 수 없습니다.

모든 유형의 액세스에서 응용 프로그램은 Azure AD를 사용 하 여 인증 합니다. 애플리케이션은 애플리케이션 유형에 따라 [지원되는 인증 방법](../../active-directory/develop/authentication-vs-authorization.md)을 사용합니다. 애플리케이션은 액세스 권한을 부여하기 위해 평면의 리소스에 대해 토큰을 획득합니다. 리소스는 Azure 환경에 따라 관리 또는 데이터 평면의 엔드포인트입니다. 애플리케이션은 해당 토큰을 사용하고 REST API 요청을 Key Vault에 보냅니다. 자세한 내용은 [전체 인증 흐름](../../active-directory/develop/v2-oauth2-auth-code-flow.md)을 참조하세요.

자세한 내용은 [Key Vault 인증 기본 사항](authentication-fundamentals.md) 을 참조 하세요.

## <a name="privileged-access"></a>권한 있는 액세스

권한은 호출자가 수행할 수 있는 작업을 결정합니다. Key Vault의 권한 부여에서는 azure [RBAC (역할 기반 액세스 제어)](../../role-based-access-control/overview.md) 및 Azure Key Vault 액세스 정책 조합을 사용 합니다.

자격 증명 모음에 대한 액세스는 두 가지 인터페이스 또는 평면에서 이루어집니다. 평면에는 관리 평면과 데이터 평면이 있습니다.

- ‘관리 평면’은 Key Vault 자체를 관리하는 곳으로, 자격 증명 모음을 만들고 삭제하는 데 사용되는 인터페이스입니다. 키 자격 증명 모음 속성을 읽고 액세스 정책을 관리할 수도 있습니다.
- ‘데이터 평면’에서는 키 자격 증명 모음에 저장된 데이터로 작업할 수 있습니다. 키, 비밀 및 인증서를 추가, 삭제 및 수정할 수 있습니다.

애플리케이션은 엔드포인트를 통해 평면에 액세스합니다. 두 평면에 대한 액세스 제어는 독립적으로 작동합니다. 키 자격 증명 모음에서 키를 사용 하는 응용 프로그램 액세스 권한을 부여 하려면 Key Vault 액세스 정책 또는 Azure RBAC (미리 보기)를 사용 하 여 데이터 평면 액세스를 부여 합니다. 사용자에 게 속성 및 태그 Key Vault에 대 한 읽기 액세스 권한을 부여 하 고 데이터 (키, 암호 또는 인증서)에 액세스할 수 없는 경우 Azure RBAC를 사용 하 여 관리 평면 액세스를 부여 합니다.

다음 표에서는 관리 및 데이터 평면에 대한 엔드포인트를 보여 줍니다.

| 액세스&nbsp;평면 | 액세스 엔드포인트 | 작업 | 액세스&nbsp;제어 메커니즘 |
| --- | --- | --- | --- |
| 관리 평면 | **전역:**<br> management.azure.com:443<br><br> **Azure China 21Vianet:**<br> management.chinacloudapi.cn:443<br><br> **Azure 미국 정부:**<br> management.usgovcloudapi.net:443<br><br> **Azure 독일:**<br> management.microsoftazure.de:443 | Key Vault 만들기, 읽기, 업데이트 및 삭제<br><br>Key Vault 액세스 정책 설정<br><br>Key Vault 태그 설정 | Azure RBAC |
| 데이터 평면 | **전역:**<br> &lt;vault-name&gt;.vault.azure.net:443<br><br> **Azure China 21Vianet:**<br> &lt;vault-name&gt;.vault.azure.cn:443<br><br> **Azure 미국 정부:**<br> &lt;vault-name&gt;.vault.usgovcloudapi.net:443<br><br> **Azure 독일:**<br> &lt;vault-name&gt;.vault.microsoftazure.de:443 | 키: 암호화, 암호 해독, wrapKey, unwrapKey, 서명, 확인, 가져오기, 목록, 만들기, 업데이트, 가져오기, 삭제, 복구, 백업, 복원, 제거<br><br> 인증서: managecontacts, getissuers, listissuers, setissuers, deleteissuers, manageissuers, 가져오기, 목록, 만들기, 가져오기, 업데이트, 삭제, 복구, 백업, 복원, 제거<br><br>  비밀: get, list, set, delete, 복구, 백업, 복원, 제거 | Key Vault 액세스 정책 또는 Azure RBAC (미리 보기)|

### <a name="managing-administrative-access-to-key-vault"></a>Key Vault에 대한 관리 액세스 관리

리소스 그룹에서 키 자격 증명 모음을 만든 경우 Azure AD를 사용하여 액세스를 관리합니다. 사용자 또는 그룹에 리소스 그룹에서 key vault를 관리하는 기능을 부여합니다. 적절 한 Azure 역할을 할당 하 여 특정 범위 수준에서 액세스 권한을 부여할 수 있습니다. key vault를 관리하기 위해 사용자에게 액세스 권한을 부여하려면 특정 범위에 속한 사용자에게 미리 정의된 `key vault Contributor` 역할을 할당합니다. Azure 역할에 할당 될 수 있는 범위 수준은 다음과 같습니다.

- **구독**: 구독 수준에서 할당 된 Azure 역할은 해당 구독 내의 모든 리소스 그룹 및 리소스에 적용 됩니다.
- **리소스 그룹**: 리소스 그룹 수준에서 할당 된 Azure 역할은 해당 리소스 그룹의 모든 리소스에 적용 됩니다.
- **특정 리소스**: 특정 리소스에 할당 된 Azure 역할이 해당 리소스에 적용 됩니다. 이 경우 리소스는 특정 Key Vault입니다.

미리 정의된 몇 가지 역할이 있습니다. 미리 정의된 역할이 요구에 맞지 않는 경우 고유한 역할을 정의할 수 있습니다. 자세한 내용은 [AZURE RBAC: 기본 제공 역할](../../role-based-access-control/built-in-roles.md)을 참조 하세요.

> [!IMPORTANT]
> 사용자에게 Key Vault 관리 평면에 대한 `Contributor` 사용 권한이 있는 경우 이 사용자는 Key Vault 액세스 정책을 설정하여 스스로 데이터 평면에 대한 액세스 권한을 부여할 수 있습니다. Key Vault에 대한 `Contributor` 역할 액세스 권한이 있는 사용자를 긴밀하게 제어해야 합니다. 권한이 있는 사람만 키 자격 증명 모음, 키, 비밀 및 인증서에 액세스하고 관리할 수 있는지 확인합니다.

### <a name="controlling-access-to-key-vault-data"></a>Key Vault 데이터에 대한 액세스 제어

Key Vault 액세스 정책은 키, 비밀 또는 인증서에 대해 개별적으로 권한을 부여합니다. 비밀이 아닌 키에 대해서만 사용자 액세스 권한을 부여할 수 있습니다. 키, 비밀 및 인증서에 대한 액세스 권한은 자격 증명 모음 수준에서 관리됩니다.

> [!IMPORTANT]
> Key Vault 액세스 정책은 특정 키, 비밀 또는 인증서와 같은 세분화된 개체 수준 권한을 지원하지 않습니다. 사용자에게 키를 만들고 삭제하는 권한이 부여되면 해당 사용자는 Key Vault의 모든 키에 대해 이러한 작업을 수행할 수 있습니다.

키 자격 증명 모음에 대 한 액세스 정책을 설정 하려면 [Azure Portal](assign-access-policy-portal.md), [Azure CLI](assign-access-policy-cli.md), [Azure PowerShell](assign-access-policy-powershell.md)또는 [Key Vault 관리 REST api](/rest/api/keyvault/)를 사용 합니다.

[Azure Key Vault에 대한 가상 네트워크 서비스 엔드포인트](overview-vnet-service-endpoints.md)를 사용하여 데이터 평면 액세스를 제한할 수 있습니다. 추가 보안 계층에 대한 [방화벽 및 가상 네트워크 규칙](network-security.md)을 구성할 수 있습니다.

## <a name="logging-and-monitoring"></a>로깅 및 모니터링

Key Vault 로깅은 자격 증명 모음에서 수행된 작업에 대한 정보를 저장합니다. 자세한 내용은 [Key Vault 로깅](logging.md)을 참조 하세요.

키 자격 증명 모음에 저장 된 키, 인증서 또는 암호의 상태가 변경 되 면 알리도록 Event Grid와 Key Vault를 통합할 수 있습니다. 자세한 내용은 [Azure Event Grid를 사용 하 여 Key Vault 모니터링](event-grid-overview.md) 을 참조 하세요.

키 자격 증명 모음의 상태를 모니터링 하 여 서비스가 의도 한 대로 작동 하는지 확인 하는 것도 중요 합니다. 이 작업을 수행 하는 방법에 [대 한 자세한 내용은 Azure Key Vault의 모니터링 및 경고](alert.md)를 참조 하세요.

## <a name="backup-and-recovery"></a>Backup 및 복구

Azure Key Vault 일시 삭제 및 보호 제거를 사용 하면 삭제 된 자격 증명 모음 및 자격 증명 모음 개체를 복구할 수 있습니다. 자세한 내용은 [Azure Key Vault 일시 삭제 개요](soft-delete-overview.md)를 참조 하세요.

또한 자격 증명 모음 내에서 개체의 업데이트/삭제/만들기에 대 한 자격 증명 모음을 정기적으로 백업 해야 합니다.  

## <a name="next-steps"></a>다음 단계

- [Azure Key Vault 보안 기준](security-baseline.md)
- [Azure Key Vault 모범 사례](security-baseline.md)
- [Azure Key Vault의 가상 네트워크 서비스 엔드포인트](overview-vnet-service-endpoints.md)
- [Azure RBAC: 기본 제공 역할](../../role-based-access-control/built-in-roles.md)
