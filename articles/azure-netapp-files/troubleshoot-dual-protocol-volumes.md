---
title: Azure NetApp Files에 대한 SMB 또는 이중 프로토콜 볼륨 문제 해결 | Microsoft Docs
description: Azure NetApp Files에 대한 SMB 또는 이중 프로토콜 문제를 해결하는 데 도움이 될 수 있는 오류 메시지 및 해결 방법에 대해 설명합니다.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 02/19/2021
ms.author: b-juche
ms.openlocfilehash: 29a1251ed390ec3aefbb45a02a3c4284ca1848b8
ms.sourcegitcommit: 4a54c268400b4158b78bb1d37235b79409cb5816
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2021
ms.locfileid: "108142462"
---
# <a name="troubleshoot-smb-or-dual-protocol-volumes"></a>SMB 또는 이중 프로토콜 볼륨 문제 해결

이 문서에서는 이중 프로토콜 볼륨을 만들거나 관리할 때 발생할 수 있는 오류 조건에 대한 해결 방법을 설명합니다.

## <a name="errors-for-dual-protocol-volumes"></a>이중 프로토콜 볼륨에 대한 오류

|     오류 조건    |     해결 방법    |
|-|-|
| TLS를 통한 LDAP를 사용하도록 설정했고, 오류 `This Active Directory has no Server root CA Certificate`이 발생하여 이중 프로토콜 볼륨 만들기가 실패합니다.    |     이중 프로토콜 볼륨을 만들 때 이 오류가 발생하는 경우에는 NetApp 계정에 루트 CA 인증서가 업로드되었는지 확인합니다.    |
| 이중 프로토콜 볼륨 만들기가 오류 `Failed to validate LDAP configuration, try again after correcting LDAP configuration`로 인해 실패합니다.    |  DNS 서버에 AD 호스트 컴퓨터의 포인터(PTR) 레코드가 없을 수 있습니다. DNS 서버에 역방향 조회 영역을 만든 다음 해당 역방향 조회 영역에 AD 호스트 컴퓨터의 PTR 레코드를 추가해야 합니다. <br> 예를 들어 AD 컴퓨터의 IP 주소가 `10.x.x.x`이고, `hostname` 명령을 사용하여 발견된 AD 컴퓨터의 호스트 이름이 `AD1`이며, 도메인 이름이 `contoso.com`라고 가정합니다.  역방향 조회 영역에 추가된 PTR 레코드는 `10.x.x.x` -> `contoso.com`이어야 합니다.   |
| 이중 프로토콜 볼륨 만들기가 오류 `Failed to create the Active Directory machine account \\\"TESTAD-C8DD\\\". Reason: Kerberos Error: Pre-authentication information was invalid Details: Error: Machine account creation procedure failed\\n [ 434] Loaded the preliminary configuration.\\n [ 537] Successfully connected to ip 10.x.x.x, port 88 using TCP\\n**[ 950] FAILURE`로 인해 실패합니다. |     이 오류는 Active Directory가 NetApp 계정에 가입할 때 AD 암호가 잘못되었음을 나타냅니다. 올바른 암호를 사용하여 AD 연결을 업데이트하고 다시 시도하십시오. |
| 이중 프로토콜 볼륨 만들기가 오류 `Could not query DNS server. Verify that the network configuration is correct and that DNS servers are available`로 인해 실패합니다. |   이 오류는 DNS에 연결할 수 없음을 나타냅니다. 잘못된 DNS IP 또는 네트워킹 문제가 원인일 수 있습니다. AD 연결에 입력된 DNS IP를 확인하고 IP가 정확한지 확인하십시오. <br> 또한 AD와 볼륨이 동일한 지역 및 동일한 VNet에 위치해야 합니다. AD와 볼륨이 서로 다른 VNET에 위치한 경우 두 VNet 간에 VNet 피어링이 설정되어 있는지 확인합니다.|
| 이중 프로토콜 볼륨을 탑재할 때 사용 권한 거부 오류가 발생했습니다. | 이중 프로토콜 볼륨은 NFS 프로토콜과 SMB 프로토콜을 모두 지원합니다.  UNIX 시스템의 탑재된 볼륨에 액세스하려고 할 때 시스템은 사용자가 사용하는 UNIX 사용자와 Windows 사용자의 매핑을 시도합니다. 매핑이 발견되지 않으면 "사용 권한 거부됨" 오류가 발생합니다. <br> 이 상황은 액세스 시 'root' 사용자를 사용하는 경우에도 적용됩니다. <br> "사용 권한 거부됨" 문제를 방지하려면 탑재 지점에 액세스하기 전에 Windows Active Directory에 `pcuser`가 포함되어 있는지 확인하십시오. "사용 권한 거부됨" 문제 발생 후 `pcuser`를 추가하는 경우, 액세스를 다시 시도하기 전에 삭제할 캐시 항목을 24 시간 동안 기다립니다. |

## <a name="common-errors-for-smb-and-dual-protocol-volumes"></a>SMB 및 이중 프로토콜 볼륨의 일반적인 오류

|     오류 조건    |     해결 방법    |
|-|-|
| SMB 또는 이중 프로토콜 볼륨 만들기는 다음 오류로 인해 실패합니다. <br> `{"code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details. Please see https://aka.ms/DeployOperations for usage details.","details":[{"code":"InternalServerError", "message":"Error when creating - Could not query DNS server. Verify that the network configuration is correct and that DNS servers are available."}]}` | 이 오류는 DNS에 연결할 수 없음을 나타냅니다. <br> 다음과 같은 해결 방법을 고려해 보십시오. <ul><li>ADDS와 볼륨이 동일한 지역에 배포되고 있는지 확인합니다.</li> <li>ADDS와 볼륨이 동일한 VNet에 배포되고 있는지 확인합니다. 서로 다른 VNET를 사용하는 경우 두 VNet이 서로 피어링하는지 확인하십시오. [Azure NetApp Files 네트워크 계획 지침](azure-netapp-files-network-topologies.md)을 참조하십시오. </li> <li>DNS 서버에 NSG(네트워크 보안 그룹)가 적용되어 있을 수 있습니다.  따라서 트래픽 흐름이 허용되지 않습니다. 이 경우 NSG를 DNS 또는 AD에 개방해 여러 포트에 연결합니다. 포트 요구 사항은 [Active Directory 연결에 대한 요구 사항](create-active-directory-connections.md#requirements-for-active-directory-connections)을 참조하십시오. </li></ul> <br>Azure ADDS에 동일한 솔루션이 적용됩니다. Azure ADDS는 동일한 지역에 배포되어야 합니다. VNet은 동일한 지역에 위치하거나 볼륨에서 사용하는 VNet과 피어링되어야 합니다. | 
| SMB 또는 이중 프로토콜 볼륨 만들기는 다음 오류로 인해 실패합니다. <br> `{"code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details. Please see https://aka.ms/DeployOperations for usage details.","details":[{"code":"InternalServerError", "message":"Error when creating - Failed to create the Active Directory machine account \"SMBTESTAD-C1C8\". Reason: Kerberos Error: Invalid credentials were given Details: Error: Machine account creation procedure failed\n [ 563] Loaded the preliminary configuration.\n**[ 670] FAILURE: Could not authenticate as 'test@contoso.com':\n** Unknown user (KRB5KDC_ERR_C_PRINCIPAL_UNKNOWN)\n. "}]}`  |  <ul><li>사용자 이름을 정확하게 입력해야 합니다. </li> <li>사용자가 컴퓨터 계정을 만들 수 있는 권한이 있는 관리자 그룹의 일부인지 확인합니다. </li> <li> Azure ADDS를 사용하는 경우 사용자가 Azure AD 그룹 `Azure AD DC Administrators`의 일부인지 확인합니다. </li></ul> | 
| SMB 또는 이중 프로토콜 볼륨 만들기가 다음 오류로 인해 실패합니다. <br> `{"code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details. Please see https://aka.ms/DeployOperations for usage details.","details":[{"code":"InternalServerError", "message":"Error when creating - Failed to create the Active Directory machine account \"SMBTESTAD-A452\". Reason: Kerberos Error: Pre-authentication information was invalid Details: Error: Machine account creation procedure failed\n [ 567] Loaded the preliminary configuration.\n [ 671] Successfully connected to ip 10.x.x.x, port 88 using TCP\n**[ 1099] FAILURE: Could not authenticate as\n** 'user@contoso.com': CIFS server account password does\n** not match password stored in Active Directory\n** (KRB5KDC_ERR_PREAUTH_FAILED)\n. "}]}` | AD 연결에 참가하기 위해 입력한 암호가 정확한지 확인합니다. |
| SMB 또는 이중 프로토콜 볼륨 만들기가 다음 오류로 인해 실패합니다. <br> `{"code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details. Please see https://aka.ms/DeployOperations for usage details.","details":[{"code":"InternalServerError","message":"Error when creating - Failed to create the Active Directory machine account \"SMBTESTAD-D9A2\". Reason: SecD Error: ou not found Details: Error: Machine account creation procedure failed\n [ 561] Loaded the preliminary configuration.\n [ 665] Successfully connected to ip 10.x.x.x, port 88 using TCP\n [ 1039] Successfully connected to ip 10.x.x.x, port 389 using TCP\n**[ 1147] FAILURE: Specifed OU 'OU=AADDC Com' does not exist in\n** contoso.com\n. "}]}` | AD 연결에 참가하기 위해 지정한 OU 경로가 올바른지 확인합니다. Azure ADDS를 사용하는 경우 조직 구성 단위 경로가 `OU=AADDC Computers`인지 확인합니다. |
| SMB 또는 이중 프로토콜 볼륨 만들기가 다음 오류로 인해 실패합니다. <br> `Failed to create the Active Directory machine account \"SMB-ANF-VOL. Reason: LDAP Error: Local error occurred Details: Error: Machine account creation procedure failed. [nnn] Loaded the preliminary configuration. [nnn] Successfully connected to ip 10.x.x.x, port 88 using TCP [nnn] Successfully connected to ip 10.x.x.x, port 389 using [nnn] Entry for host-address: 10.x.x.x not found in the current source: FILES. Ignoring and trying next available source [nnn] Source: DNS unavailable. Entry for host-address:10.x.x.x found in any of the available sources\n*[nnn] FAILURE: Unable to SASL bind to LDAP server using GSSAPI: local error [nnn] Additional info: SASL(-1): generic failure: GSSAPI Error: Unspecified GSS failure. Minor code may provide more information (Cannot determine realm for numeric host address) [nnn] Unable to connect to LDAP (Active Directory) service on contoso.com (Error: Local error) [nnn] Unable to make a connection (LDAP (Active Directory):contosa.com, result: 7643. `  | DNS 서버에 AD 호스트 컴퓨터의 포인터(PTR) 레코드가 존재하지 않을 가능성이 있습니다. DNS 서버에 역방향 조회 영역을 만든 다음 해당 역방향 조회 영역에 AD 호스트 컴퓨터의 PTR 레코드를 추가해야 합니다. <br> 예를 들어 AD 컴퓨터의 IP 주소가 `10.x.x.x`이고, `hostname` 명령을 사용하여 발견된 AD 컴퓨터의 호스트 이름이 `AD1`이며, 도메인 이름이 `contoso.com`라고 가정합니다.  역방향 조회 영역에 추가된 PTR 레코드는 `10.x.x.x` -> `contoso.com`이어야 합니다.   | 
| SMB 또는 이중 프로토콜 볼륨 만들기가 다음 오류로 인해 실패합니다. <br> `Failed to create the Active Directory machine account \"SMB-ANF-VOL\". Reason: Kerberos Error: KDC has no support for encryption type Details: Error: Machine account creation procedure failed [nnn]Loaded the preliminary configuration. [nnn]Successfully connected to ip 10.x.x.x, port 88 using TCP [nnn]FAILURE: Could not authenticate as 'contosa.com': KDC has no support for encryption type  (KRB5KDC_ERR_ETYPE_NOSUPP) ` | Active Directory 연결 및 서비스 계정 모두에서 [AES 암호화](./create-active-directory-connections.md#create-an-active-directory-connection)를 사용하도록 설정했는지 확인합니다. |
| SMB 또는 이중 프로토콜 볼륨 만들기가 다음 오류로 인해 실패합니다. <br> `Failed to create the Active Directory machine account \"SMB-NTAP-VOL\". Reason: LDAP Error: Strong authentication is required Details: Error: Machine account creation procedure failed\n [ 338] Loaded the preliminary configuration.\n [ nnn] Successfully connected to ip 10.x.x.x, port 88 using TCP\n [ nnn ] Successfully connected to ip 10.x.x.x, port 389 using TCP\n [ 765] Unable to connect to LDAP (Active Directory) service on\n dc51.area51.com (Error: Strong(er) authentication\n required)\n*[ nnn] FAILURE: Unable to make a connection (LDAP (Active\n* Directory):contoso.com), result: 7609\n. "` | LDAP 서명 옵션은 선택되지 않지만 AD 클라이언트에는 LDAP 서명이 있습니다.  [LDAP 서명 사용](create-active-directory-connections.md#create-an-active-directory-connection)으로 설정하고 다시 시도하십시오. | 

## <a name="next-steps"></a>다음 단계  

* [SMB 볼륨 만들기](azure-netapp-files-create-volumes-smb.md)
* [이중 프로토콜 볼륨 만들기](create-volumes-dual-protocol.md)
* [Azure NetApp Files에 대한 NFS 클라이언트 구성](configure-nfs-clients.md)