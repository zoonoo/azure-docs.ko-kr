---
title: OPC 자격 증명 모음 인증서 서비스를 관리 하는 방법-Azure | Microsoft Docs
description: OPC 자격 증명 모음 루트 CA 인증서 및 사용자 권한을 관리 합니다.
author: mregen
ms.author: mregen
ms.date: 8/16/2019
ms.topic: conceptual
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 4420e5b0d895f8ea30dbd39fc50dd7480d57d086
ms.sourcegitcommit: 4b8a69b920ade815d095236c16175124a6a34996
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/23/2019
ms.locfileid: "69996002"
---
# <a name="how-to-manage-the-opc-vault-certificate-service"></a>OPC 자격 증명 모음 인증서 서비스를 관리 하는 방법

이 문서에서는 Azure의 OPC 자격 증명 모음 인증서 관리 서비스, 발급자 CA 인증서를 갱신 하는 방법, CRL (인증서 해지 목록)을 갱신 하는 방법 및 사용자 액세스 권한을 부여 하 고 취소 하는 방법에 대 한 관리 작업을 설명 합니다.

## <a name="create-or-renew-the-root-ca-certificate"></a>루트 CA 인증서를 만들거나 갱신 합니다.

루트 CA 인증서를 만들려면 배포 후 필수 단계입니다. 유효한 발급자 CA 인증서가 없으면 서명 및 발급할 수 있는 응용 프로그램 인증서가 없습니다.<br>적절 하 고 안전한 수명으로 인증서를 관리 하려면 [인증서 수명](howto-opc-vault-secure-ca.md#certificates) 에 대 한 장을 참조 하세요. 발급자 CA 인증서의 수명이 절반 후에 갱신 되어야 하지만, 새로 서명 된 응용 프로그램 인증서의 구성 된 수명이 발급자 인증서의 수명을 초과 하기 전에는 그렇지 않습니다.<br>
> [!IMPORTANT]
> 발급자 CA 인증서를 만들거나 갱신 하려면 ' 관리자 ' 역할이 필요 합니다.

1. 에서 `https://myResourceGroup-app.azurewebsites.net` 인증서 서비스를 열고 로그인 합니다.
2. `Certificate Groups` 페이지로 이동합니다.
3. 하나의 `Default` 인증서 그룹이 나열 됩니다. `Edit`를 클릭합니다.
4. 에서 `Edit Certificate Group Details` CA 및 응용 프로그램 인증서의 주체 이름 및 수명을 수정할 수 있습니다.<br>첫 번째 CA 인증서가 발급 되기 전에는 주체와 수명을 한 번만 설정 해야 합니다. 작업 중의 수명 변경으로 인해 발급 된 인증서와 Crl의 수명이 일치 하지 않을 수 있습니다.
5. 올바른 제목을 입력 `CN=My CA Root, O=MyCompany, OU=MyDepartment`합니다 (예:).<br>
   > [!IMPORTANT]
   > 주체를 변경 하려면 발급자 인증서를 갱신 해야 합니다. 그렇지 않으면 서비스에서 응용 프로그램 인증서에 서명 하지 못합니다. 구성의 주체는 활성 발급자 인증서의 주체에 대해 온전성 검사 됩니다. 주체가 일치 하지 않으면 인증서 서명이 거부 됩니다.
6. `Save` 단추를 클릭 합니다.
7. 이 시점에서 ' 사용할 수 없음 ' 오류가 발생 하면 사용자 자격 증명에 새 루트 인증서를 수정 하거나 만들 수 있는 관리자 권한이 없습니다. 기본적으로 서비스를 배포한 사용자에 게는 서비스와의 관리자 및 서명 역할이 있으며, AzureAD 응용 프로그램 등록에 따라 다른 사용자를 ' 승인자 ', ' 작성자 ' 또는 ' 관리자 ' 역할에 추가 해야 합니다.
8. `Details` 단추를 클릭 합니다. 에 `View Certificate Group Details` 업데이트 된 정보가 표시 됩니다.
9. 첫 번째 발급자 CA 인증서를 발급 하거나 발급자 인증서를 갱신 하려면 단추를클릭합니다.`Renew CA Certificate` 계속 `Ok` 하려면 키를 누르십시오.
10. 몇 초 후에 `Certificate Details` 가 표시 됩니다. `Issuer` 또는`Crl` 를 눌러 OPC UA 응용 프로그램에 배포할 최신 CA 인증서 및 CRL을 다운로드 합니다.
11. 이제 opc UA 인증서 관리 서비스가 OPC UA 응용 프로그램에 대 한 인증서를 발급할 준비가 되었습니다.

## <a name="renew-the-crl"></a>CRL 갱신

CRL (인증서 해지 목록) 갱신은 정기적으로 응용 프로그램에 배포 해야 하는 업데이트입니다. CRL 배포 지점 X509 확장을 지 원하는 OPC UA 장치는 마이크로 서비스 끝점에서 CRL을 직접 업데이트할 수 있습니다. 다른 OPC UA 장치에는 수동 업데이트가 필요할 수 있으며, 그렇지 않으면 GDS 서버 푸시 확장 (*)을 사용 하 여 인증서 및 Crl을 사용 하 여 신뢰 목록을 업데이트할 수 있습니다.

다음 워크플로에서 삭제 된 상태의 모든 인증서 요청은 발급 된 발급자 CA 인증서에 해당 하는 Crl에서 해지 됩니다. CRL의 버전 번호는 1 씩 증가 합니다. <br>
> [!NOTE]
> 모든 발급 된 Crl은 발급자 CA 인증서가 만료 될 때까지 유효 합니다. OPC UA 사양에는 CRL에 대 한 필수 결정적 배포 모델이 필요 하지 않기 때문입니다.

> [!IMPORTANT]
> 발급자 CRL을 갱신 하려면 ' 관리자 ' 역할이 필요 합니다.

1. 에서 `https://myResourceGroup.azurewebsites.net` 인증서 서비스를 열고 로그인 합니다.
2. `Certificate Groups` 페이지로 이동합니다.
3. `Details` 단추를 클릭 합니다. 에서 `View Certificate Group Details` 현재 인증서 및 CRL 정보를 표시 해야 합니다.
4. OPC 자격 증명 모음 저장소의 모든 활성 발급자 인증서에 대해 업데이트 된 CRL을 발급 하려면 단추를클릭합니다.`Update CRL Revocation List(CRL)`
5. 몇 초 후에 `Certificate Details` 가 표시 됩니다. `Issuer` 또는`Crl` 를 눌러 OPC UA 응용 프로그램에 배포할 최신 CA 인증서 및 CRL을 다운로드 합니다.

## <a name="manage-user-roles"></a>사용자 역할 관리

OPC 자격 증명 모음 마이크로 서비스의 사용자 역할은 Azure Active Directory Enterprise 응용 프로그램에서 관리 됩니다.

역할 정의에 대 한 자세한 내용은 [역할](howto-opc-vault-secure-ca.md#roles) 섹션을 참조 하세요.

기본적으로 테 넌 트의 인증 된 사용자는 ' 판독기 '로 서비스에 로그인 할 수 있습니다. 권한이 높은 역할은 Azure Portal 또는 Powershell을 사용 하 여 수동으로 관리 해야 합니다.

### <a name="add-user"></a>사용자 추가

1. 에서 `portal.azure.com`Azure Portal를 엽니다.
2. `Azure Active Directory`/`Enterprise applications`으로 이동합니다.
3. 기본적으로 OPC 자격 증명 모음 마이크로 서비스의 등록을 선택 합니다 `resourceGroupName-service`.
4. [https://test-cors.org](`Users and Groups`) 로 이동합니다.
5. `Add User`를 클릭합니다.
6. 특정 역할에 대 한 할당을 위해 사용자를 선택 하거나 초대 합니다.
7. 사용자에 대 한 역할을 선택 합니다.
8. `Assign` 단추를 누릅니다.
9. `Administrator` 또는`Approver` 역할의 사용자에 대해 Azure Key Vault 액세스 정책 추가를 계속 합니다.

### <a name="remove-user"></a>사용자 삭제

1. 에서 `portal.azure.com`Azure Portal를 엽니다.
2. `Azure Active Directory`/`Enterprise applications`으로 이동합니다.
3. 기본적으로 OPC 자격 증명 모음 마이크로 서비스의 등록을 선택 합니다 `resourceGroupName-service`.
4. [https://test-cors.org](`Users and Groups`) 로 이동합니다.
5. 제거할 역할이 있는 사용자를 선택 합니다.
6. `Remove` 단추를 누릅니다.
7. 제거 되는 관리자 및 승인자를 Azure Key Vault 정책 에서도 제거 합니다.

### <a name="add-user-access-policy-to-azure-key-vault"></a>Azure Key Vault에 사용자 액세스 정책 추가

추가 액세스 정책은 **승인자** 및 **관리자**에 게 필요 합니다.

기본적으로 서비스 id는 사용자 가장 없이 상승 된 작업 또는 변경 작업을 방지 하기 위해 Key Vault 액세스에 대 한 제한 된 권한만 갖습니다. 기본 서비스 권한은 `Get` 암호와 인증서 `List` 둘 다에 대 한 및입니다. 비밀의 경우 한 가지 예외가 있으며, 서비스는 사용자가 `Delete` 승인한 후 비밀 저장소의 개인 키를 사용할 수 있습니다. 다른 모든 작업에는 사용자가 가장 한 권한이 필요 합니다.<br>

#### <a name="for-an-approver-role-the-following-permissions-must-be-added-to-key-vault"></a>**승인자 역할** 의 경우 Key Vault에 다음 사용 권한을 추가 해야 합니다.

1. 에서 `portal.azure.com`Azure Portal를 엽니다.
2. 배포 중에 사용 되 `resourceGroupName`는 OPC 자격 증명 모음으로 이동 합니다.
3. Key Vault `resourceGroupName-xxxxx`로 이동 합니다.
4. 로 `Access Policies`이동 합니다.
5. `Add new`를 클릭합니다.
6. 템플릿을 건너뛰고 요구 사항과 일치 하는 템플릿이 없습니다.
7. 켜기 `Select Principal` 를 클릭 하 고 추가할 사용자를 선택 하거나 새 사용자를 테 넌 트에 초대 합니다.
8. 확인 `Key permissions` :`Get`및 가장 중요`Sign`합니다. `List`
9. `Secret permissions` ,`List`및 를확인합니다`Delete`. `Get` `Set`
10. 확인 `Certificate permissions`: `Get`및 .`List`
11. `Ok`을 클릭합니다.
12. `Save`변경.

#### <a name="for-an-administrator-role-the-following-permissions-must-be-added-to-key-vault"></a>**관리자 역할** 의 경우 Key Vault에 다음 사용 권한을 추가 해야 합니다.

1. 에서 `portal.azure.com`Azure Portal를 엽니다.
2. 배포 중에 사용 되 `resourceGroupName`는 OPC 자격 증명 모음으로 이동 합니다.
3. Key Vault `resourceGroupName-xxxxx`로 이동 합니다.
4. 로 `Access Policies`이동 합니다.
5. `Add new`를 클릭합니다.
6. 템플릿을 건너뛰고 요구 사항과 일치 하는 템플릿이 없습니다.
7. 켜기 `Select Principal` 를 클릭 하 고 추가할 사용자를 선택 하거나 새 사용자를 테 넌 트에 초대 합니다.
8. 확인 `Key permissions` :`Get`및 가장 중요`Sign`합니다. `List`
9. `Secret permissions` ,`List`및 를확인합니다`Delete`. `Get` `Set`
10. `Certificate permissions` ,`Create` , 및를`Import`확인 합니다. `List` `Get` `Update`
11. `Ok`을 클릭합니다.
12. `Save`변경.

### <a name="remove-user-access-policy-from-azure-key-vault"></a>Azure Key Vault에서 사용자 액세스 정책 제거

1. 에서 `portal.azure.com`Azure Portal를 엽니다.
2. 배포 중에 사용 되 `resourceGroupName`는 OPC 자격 증명 모음으로 이동 합니다.
3. Key Vault `resourceGroupName-xxxxx`로 이동 합니다.
4. 로 `Access Policies`이동 합니다.
5. 제거할 사용자를 찾은 다음를 클릭 `... / Delete` 하 여 사용자 액세스 권한을 삭제 합니다.

## <a name="next-steps"></a>다음 단계

이제 OPC 자격 증명 모음 인증서와 사용자를 관리 하는 방법을 배웠으므로 제안 된 다음 단계는 다음과 같습니다.

> [!div class="nextstepaction"]
> [OPC 장치의 보안 통신](howto-opc-vault-secure.md)
