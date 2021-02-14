---
title: Azure NetApp Files에 대 한 SMB 또는 이중 프로토콜 볼륨 문제 해결 | Microsoft Docs
description: Azure NetApp Files에 대 한 SMB 또는 이중 프로토콜 문제를 해결 하는 데 도움이 될 수 있는 오류 메시지 및 해결 방법에 대해 설명 합니다.
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
ms.date: 02/02/2021
ms.author: b-juche
ms.openlocfilehash: 237fb514229f370fcba79133232e80a6a655048f
ms.sourcegitcommit: 126ee1e8e8f2cb5dc35465b23d23a4e3f747949c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/10/2021
ms.locfileid: "100104419"
---
# <a name="troubleshoot-smb-or-dual-protocol-volumes"></a>SMB 또는 이중 프로토콜 볼륨 문제 해결

이 문서에서는 이중 프로토콜 볼륨을 만들거나 관리할 때 발생할 수 있는 오류 조건에 대 한 해결 방법을 설명 합니다.

## <a name="errors-for-dual-protocol-volumes"></a>이중 프로토콜 볼륨에 대 한 오류

|     오류 조건    |     해결 방법    |
|-|-|
| TLS를 통한 LDAP를 사용 하도록 설정 하 고 오류가 발생 하 여 이중 프로토콜 볼륨 만들기가 실패 `This Active Directory has no Server root CA Certificate` 합니다.    |     이중 프로토콜 볼륨을 만들 때이 오류가 발생 하는 경우에는 NetApp 계정에 루트 CA 인증서가 업로드 되었는지 확인 합니다.    |
| 이중 프로토콜 볼륨 만들기가 오류로 인해 실패 `Failed to validate LDAP configuration, try again after correcting LDAP configuration` 합니다.    |  DNS 서버에 AD 호스트 컴퓨터의 포인터 (PTR) 레코드가 없을 수 있습니다. DNS 서버에 역방향 조회 영역을 만든 다음 해당 역방향 조회 영역에서 AD 호스트 컴퓨터의 PTR 레코드를 추가 해야 합니다. <br> 예를 들어 AD 컴퓨터의 IP 주소가이 고 `10.X.X.X` , 명령을 사용 하 여 발견 된 ad 컴퓨터의 호스트 이름이이 `hostname` `AD1` 고, 도메인 이름이 인 것으로 가정 합니다 `contoso.com` .  역방향 조회 영역에 추가 된 PTR 레코드는 이어야 합니다 `10.X.X.X`  ->  `contoso.com` .   |
| 이중 프로토콜 볼륨 만들기가 오류로 인해 실패 `Failed to create the Active Directory machine account \\\"TESTAD-C8DD\\\". Reason: Kerberos Error: Pre-authentication information was invalid Details: Error: Machine account creation procedure failed\\n [ 434] Loaded the preliminary configuration.\\n [ 537] Successfully connected to ip 10.X.X.X, port 88 using TCP\\n**[ 950] FAILURE` 합니다. |     이 오류는 Active Directory NetApp 계정에 가입할 때 AD 암호가 잘못 되었음을 나타냅니다. 올바른 암호를 사용 하 여 AD 연결을 업데이트 하 고 다시 시도 하십시오. |
| 이중 프로토콜 볼륨 만들기가 오류로 인해 실패 `Could not query DNS server. Verify that the network configuration is correct and that DNS servers are available` 합니다. |   이 오류는 DNS에 연결할 수 없음을 나타냅니다. DNS IP가 잘못 되었거나 네트워킹 문제가 있기 때문일 수 있습니다. AD 연결에 입력 된 DNS IP를 확인 하 고 IP가 올바른지 확인 하십시오. <br> 또한 AD와 볼륨이 동일한 지역에 있고 동일한 VNet에 있어야 합니다. 서로 다른 Vnet에 있는 경우 두 Vnet 간에 VNet 피어 링이 설정 되어 있는지 확인 합니다.|
| 이중 프로토콜 볼륨을 탑재할 때 사용 권한 거부 오류가 발생 했습니다. | 이중 프로토콜 볼륨은 NFS 및 SMB 프로토콜을 모두 지원 합니다.  UNIX 시스템의 탑재 된 볼륨에 액세스 하려고 하면 시스템에서 사용 하는 UNIX 사용자를 Windows 사용자에 매핑하려고 시도 합니다. 매핑이 발견 되지 않으면 "사용 권한이 거부 되었습니다." 오류가 발생 합니다. <br> 이 상황은 액세스에 ' root ' 사용자를 사용 하는 경우에도 적용 됩니다. <br> "사용 권한이 거부 되었습니다." 문제를 방지 하려면 `pcuser` 탑재 지점에 액세스 하기 전에 Windows Active Directory에가 포함 되어 있는지 확인 합니다. `pcuser`"사용 권한이 거부 되었습니다." 문제가 발생 한 후를 추가 하는 경우 액세스를 다시 시도 하기 전에 캐시 항목을 제거 하기 위해 24 시간 동안 기다립니다. |

## <a name="common-errors-for-smb-and-dual-protocol-volumes"></a>SMB 및 이중 프로토콜 볼륨의 일반적인 오류

|     오류 조건    |     해결 방법    |
|-|-|
| SMB 또는 이중 프로토콜 볼륨 만들기가 실패 하 고 다음 오류가 발생 합니다. <br> `{"code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details. Please see https://aka.ms/DeployOperations for usage details.","details":[{"code":"InternalServerError", "message":"Error when creating - Could not query DNS server. Verify that the network configuration is correct and that DNS servers are available."}]}` | 이 오류는 DNS에 연결할 수 없음을 나타냅니다. <br> 다음과 같은 해결 방법을 고려해 보십시오. <ul><li>가를 추가 하 고 볼륨이 동일한 지역에 배포 되는지 확인 합니다.</li> <li>가를 추가 하 고 볼륨이 동일한 VNet을 사용 하는지 확인 합니다. 서로 다른 Vnet를 사용 하는 경우 Vnet가 서로 피어 링 하는지 확인 합니다. [Azure NetApp Files 네트워크 계획에 대 한 지침을](azure-netapp-files-network-topologies.md)참조 하세요. </li> <li>DNS 서버에 NSGs (네트워크 보안 그룹)가 적용 되어 있을 수 있습니다.  따라서 트래픽을 전달할 수 없습니다. 이 경우 DNS 또는 AD에 대 한 NSGs를 열어 여러 포트에 연결 합니다. 포트 요구 사항은 [Active Directory 연결에 대 한 요구 사항](azure-netapp-files-create-volumes-smb.md#requirements-for-active-directory-connections)을 참조 하세요. </li></ul> <br>Azure ADDS에 동일한 솔루션이 적용 됩니다. Azure ADDS는 동일한 지역에 배포 되어야 합니다. VNet은 볼륨에서 사용 하는 VNet과 동일한 지역 또는 피어 링에 있어야 합니다. | 
| SMB 또는 이중 프로토콜 볼륨 만들기가 실패 하 고 다음 오류가 발생 합니다. <br> `{"code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details. Please see https://aka.ms/DeployOperations for usage details.","details":[{"code":"InternalServerError", "message":"Error when creating - Failed to create the Active Directory machine account \"SMBTESTAD-C1C8\". Reason: Kerberos Error: Invalid credentials were given Details: Error: Machine account creation procedure failed\n [ 563] Loaded the preliminary configuration.\n**[ 670] FAILURE: Could not authenticate as 'test@contoso.com':\n** Unknown user (KRB5KDC_ERR_C_PRINCIPAL_UNKNOWN)\n. "}]}`  |  <ul><li>입력 한 사용자 이름이 올바른지 확인 합니다. </li> <li>사용자가 컴퓨터 계정을 만들 수 있는 권한이 있는 관리자 그룹의 일부 인지 확인 합니다. </li> <li> Azure ADDS를 사용 하는 경우 사용자가 Azure AD 그룹의 일부 인지 확인 `Azure AD DC Administrators` 합니다. </li></ul> | 
| SMB 또는 이중 프로토콜 볼륨 만들기가 실패 하 고 다음 오류가 발생 합니다. <br> `{"code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details. Please see https://aka.ms/DeployOperations for usage details.","details":[{"code":"InternalServerError", "message":"Error when creating - Failed to create the Active Directory machine account \"SMBTESTAD-A452\". Reason: Kerberos Error: Pre-authentication information was invalid Details: Error: Machine account creation procedure failed\n [ 567] Loaded the preliminary configuration.\n [ 671] Successfully connected to ip 10.X.X.X, port 88 using TCP\n**[ 1099] FAILURE: Could not authenticate as\n** 'user@contoso.com': CIFS server account password does\n** not match password stored in Active Directory\n** (KRB5KDC_ERR_PREAUTH_FAILED)\n. "}]}` | AD 연결을 조인 하기 위해 입력 한 암호가 올바른지 확인 합니다. |
| SMB 또는 이중 프로토콜 볼륨 만들기가 실패 하 고 다음 오류가 발생 합니다. `{"code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details. Please see https://aka.ms/DeployOperations for usage details.","details":[{"code":"InternalServerError","message":"Error when creating - Failed to create the Active Directory machine account \"SMBTESTAD-D9A2\". Reason: SecD Error: ou not found Details: Error: Machine account creation procedure failed\n [ 561] Loaded the preliminary configuration.\n [ 665] Successfully connected to ip 10.X.X.X, port 88 using TCP\n [ 1039] Successfully connected to ip 10.x.x.x, port 389 using TCP\n**[ 1147] FAILURE: Specifed OU 'OU=AADDC Com' does not exist in\n** contoso.com\n. "}]}` | AD 연결에 조인 하기 위해 지정 된 OU 경로가 올바른지 확인 합니다. Azure ADDS를 사용 하는 경우 조직 구성 단위 경로가 인지 확인 `OU=AADDC Computers` 합니다. |

## <a name="next-steps"></a>다음 단계  

* [SMB 볼륨 만들기](azure-netapp-files-create-volumes-smb.md)
* [이중 프로토콜 볼륨 만들기](create-volumes-dual-protocol.md)
* [Azure NetApp Files에 대한 NFS 클라이언트 구성](configure-nfs-clients.md)
