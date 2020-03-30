---
title: OPC 볼트 인증서 서비스를 관리하는 방법 - Azure | 마이크로 소프트 문서
description: OPC Vault 루트 CA 인증서 및 사용자 권한을 관리합니다.
author: mregen
ms.author: mregen
ms.date: 8/16/2019
ms.topic: conceptual
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 890a25ed2cf11d657cad930815d78dbf968cc9f9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "71203660"
---
# <a name="manage-the-opc-vault-certificate-service"></a>OPC 볼트 인증서 서비스 관리

이 문서에서는 Azure의 OPC Vault 인증서 관리 서비스에 대한 관리 작업에 대해 설명합니다. 발급자 CA 인증서를 갱신하는 방법, CRL(인증서 해지 목록)을 갱신하는 방법 및 사용자 액세스를 부여및 해지하는 방법에 대한 정보가 포함되어 있습니다.

## <a name="create-or-renew-the-root-ca-certificate"></a>루트 CA 인증서 만들기 또는 갱신

OPC Vault를 배포한 후 루트 CA 인증서를 만들어야 합니다. 유효한 발급자 CA 인증서가 없으면 응용 프로그램 인증서에 서명하거나 발급할 수 없습니다. [인증서를](howto-opc-vault-secure-ca.md#certificates) 참조하여 합리적고 안전한 수명으로 인증서를 관리하십시오. 발행자 CA 인증서의 수명이 반 후에 갱신합니다. 갱신 할 때 새로 서명 된 응용 프로그램 인증서의 구성된 수명이 발급자 CA 인증서의 수명을 초과해서는 안됩니다.
> [!IMPORTANT]
> 발급자 CA 인증서를 만들거나 갱신하려면 관리자 역할이 필요합니다.

1. 에서 `https://myResourceGroup-app.azurewebsites.net`인증서 서비스를 열고 로그인합니다.
2. 인증서 **그룹으로 이동합니다.**
3. 하나의 기본 인증서 그룹이 나열되어 있습니다. **편집**을 선택합니다.
4. **인증서 그룹 세부 정보 편집에서**CA 및 응용 프로그램 인증서의 제목 이름과 수명을 수정할 수 있습니다. 주체와 수명은 첫 번째 CA 인증서가 발급되기 전에 한 번만 설정해야 합니다. 작업 중 수명이 변경되면 발급된 인증서 및 CRL의 수명이 일치하지 않을 수 있습니다.
5. 유효한 주제(예: )를 `CN=My CA Root, O=MyCompany, OU=MyDepartment`입력합니다.<br>
   > [!IMPORTANT]
   > 주체를 변경하는 경우 발급자 인증서를 갱신해야 하거나 서비스가 응용 프로그램 인증서에 서명하지 못합니다. 구성의 주체는 활성 발급자 인증서의 주체에 대해 확인됩니다. 주체가 일치하지 않으면 인증서 서명이 거부됩니다.
6. **저장**을 선택합니다.
7. 이 시점에서 "금지된" 오류가 발생하면 사용자 자격 증명에 새 루트 인증서를 수정하거나 만들 수 있는 관리자 권한이 없습니다. 기본적으로 서비스를 배포한 사용자에게는 관리자가 있고 서비스에 대한 역할에 서명합니다. Azure Active Directory(Azure AD) 응용 프로그램 등록에서 적절히 승인자, 작성기 또는 관리자 역할에 다른 사용자를 추가해야 합니다.
8. **세부 정보를 선택합니다.** 업데이트된 정보가 표시됩니다.
9. 첫 번째 발급자 CA 인증서를 발급하거나 발급자 인증서를 갱신하려면 **CA 인증서 갱신을** 선택합니다. 그런 다음 **확인을**선택합니다.
10. 몇 초 후 **인증서 세부 정보가**표시됩니다. OPC UA 응용 프로그램에 배포할 최신 CA 인증서 및 CRL을 다운로드하려면 **발급자** 또는 **Crl을**선택합니다.

이제 OPC UA 인증서 관리 서비스가 OPC UA 응용 프로그램에 대한 인증서를 발급할 준비가 되었습니다.

## <a name="renew-the-crl"></a>CRL 갱신

CRL 의 갱신은 정기적으로 응용 프로그램에 배포되어야 하는 업데이트입니다. CRL 배포 지점 X509 확장을 지원하는 OPC UA 장치는 마이크로 서비스 끝점에서 CRL을 직접 업데이트할 수 있습니다. 다른 OPC UA 장치는 수동 업데이트가 필요하거나 GDS 서버 푸시 확장(*)을 사용하여 인증서 및 CRL로 트러스트 목록을 업데이트하여 업데이트할 수 있습니다.

다음 워크플로에서는 삭제된 상태의 모든 인증서 요청이 발급된 발급자 CA 인증서에 해당하는 CRL에서 해지됩니다. CRL의 버전 번호는 1씩 증가합니다. <br>
> [!NOTE]
> 발급된 모든 CRL은 발급자 CA 인증서가 만료될 때까지 유효합니다. 이는 OPC UA 사양에 CRL에 대한 필수 결정적 배포 모델이 필요하지 않기 때문입니다.

> [!IMPORTANT]
> 발급자 CRL을 갱신하려면 관리자 역할이 필요합니다.

1. 에서 `https://myResourceGroup.azurewebsites.net`인증서 서비스를 열고 로그인합니다.
2. 인증서 그룹 페이지로 **이동합니다.**
3. **세부 정보를 선택합니다.** 현재 인증서 및 CRL 정보가 표시되어야 합니다.
4. OPC Vault 저장소의 모든 활성 발급자 인증서에 대해 업데이트된 CRL을 발급하려면 **CRL 해지 목록(CRL) 업데이트를** 선택합니다.
5. 몇 초 후 **인증서 세부 정보가**표시됩니다. OPC UA 응용 프로그램에 배포할 최신 CA 인증서 및 CRL을 다운로드하려면 **발급자** 또는 **Crl을**선택합니다.

## <a name="manage-user-roles"></a>사용자 역할 관리

Azure AD 엔터프라이즈 응용 프로그램에서 OPC Vault 마이크로 서비스에 대한 사용자 역할을 관리합니다. 역할 정의에 대한 자세한 설명은 [역할](howto-opc-vault-secure-ca.md#roles)을 참조하십시오.

기본적으로 테넌트의 인증된 사용자는 서비스에 Reader로 로그인할 수 있습니다. 권한이 높은 역할에는 Azure 포털에서 또는 PowerShell을 사용하여 수동 관리가 필요합니다.

### <a name="add-user"></a>사용자 추가

1. Azure Portal을 엽니다.
2. Azure **Active Directory** > **엔터프라이즈 응용 프로그램으로**이동합니다.
3. OPC Vault 마이크로 서비스의 등록을 선택합니다(기본적으로). `resourceGroupName-service`
4. 사용자 **및 그룹으로**이동합니다.
5. **사용자 추가**를 선택합니다.
6. 특정 역할에 할당할 사용자를 선택하거나 초대합니다.
7. 사용자의 역할을 선택합니다.
8. **을 선택합니다.**
9. 관리자 또는 승인자 역할의 사용자의 경우 Azure Key Vault 액세스 정책을 계속 추가합니다.

### <a name="remove-user"></a>사용자 제거

1. Azure Portal을 엽니다.
2. Azure **Active Directory** > **엔터프라이즈 응용 프로그램으로**이동합니다.
3. OPC Vault 마이크로 서비스의 등록을 선택합니다(기본적으로). `resourceGroupName-service`
4. 사용자 **및 그룹으로**이동합니다.
5. 제거할 역할이 있는 사용자를 선택한 다음 **제거를**선택합니다.
6. 관리자 또는 승인자 역할에서 제거된 사용자의 경우 Azure Key Vault 정책에서 제거합니다.

### <a name="add-user-access-policy-to-azure-key-vault"></a>Azure 키 자격 증명 모음에 사용자 액세스 정책 추가

승인자 및 관리자에게는 추가 액세스 정책이 필요합니다.

기본적으로 서비스 ID에는 키 볼트에 액세스할 수 있는 권한이 제한되어 사용자 가장 없이 높은 작업이나 변경이 발생하지 않도록 합니다. 기본 서비스 권한은 암호와 인증서 모두에 대한 Get 및 List입니다. 비밀의 경우 사용자가 수락한 후 서비스가 비밀 저장소에서 개인 키를 삭제할 수 있다는 단 하나의 예외가 있습니다. 다른 모든 작업에는 사용자 가장 권한이 필요합니다.

#### <a name="for-an-approver-role-the-following-permissions-must-be-added-to-key-vault"></a>승인자 역할의 경우 키 자격 증명 모음에 다음 권한을 추가해야 합니다.

1. Azure Portal을 엽니다.
2. 배포 하는 동안 `resourceGroupName`사용 하는 OPC 볼트로 이동 합니다.
3. 키 볼트로 `resourceGroupName-xxxxx`이동합니다.
4. 액세스 **정책으로**이동합니다.
5. **새 추가를 선택합니다.**
6. 템플릿을 건너뜁니다. 요구 사항과 일치하는 템플릿이 없습니다.
7. **보안 주체 선택을**선택하고 추가할 사용자를 선택하거나 새 사용자를 테넌트에 초대합니다.
8. 다음 **키 사용 권한:** **받기**, **목록**및 **서명**을 선택합니다.
9. 다음 **비밀 사용 권한:** **받기**, **목록,** **설정**및 삭제 를 **선택합니다.**
10. 다음 **인증서 사용 권한:** **받기** 및 **목록**선택 .
11. **확인을**선택하고 **저장을**선택합니다.

#### <a name="for-an-administrator-role-the-following-permissions-must-be-added-to-key-vault"></a>관리자 역할의 경우 키 자격 증명 모음에 다음 권한을 추가해야 합니다.

1. Azure Portal을 엽니다.
2. 배포 하는 동안 `resourceGroupName`사용 하는 OPC 볼트로 이동 합니다.
3. 키 볼트로 `resourceGroupName-xxxxx`이동합니다.
4. 액세스 **정책으로**이동합니다.
5. **새 추가를 선택합니다.**
6. 템플릿을 건너뜁니다. 요구 사항과 일치하는 템플릿이 없습니다.
7. **보안 주체 선택을**선택하고 추가할 사용자를 선택하거나 새 사용자를 테넌트에 초대합니다.
8. 다음 **키 사용 권한:** **받기**, **목록**및 **서명**을 선택합니다.
9. 다음 **비밀 사용 권한:** **받기**, **목록,** **설정**및 삭제 를 **선택합니다.**
10. 다음 인증서 사용 권한을 **Get** **선택합니다.** **List** **Update** **Create** **Import**
11. **확인을**선택하고 **저장을**선택합니다.

### <a name="remove-user-access-policy-from-azure-key-vault"></a>Azure 키 자격 증명 모음에서 사용자 액세스 정책 제거

1. Azure Portal을 엽니다.
2. 배포 하는 동안 `resourceGroupName`사용 하는 OPC 볼트로 이동 합니다.
3. 키 볼트로 `resourceGroupName-xxxxx`이동합니다.
4. 액세스 **정책으로**이동합니다.
5. 제거할 사용자를 찾고 에서 **삭제를**선택합니다.

## <a name="next-steps"></a>다음 단계

이제 OPC Vault 인증서 및 사용자를 관리하는 방법을 배웠으므로 다음을 수행할 수 있습니다.

> [!div class="nextstepaction"]
> [OPC 장치의 안전한 통신](howto-opc-vault-secure.md)
