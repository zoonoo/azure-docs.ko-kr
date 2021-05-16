---
title: OPC 자격 증명 모음 인증서 서비스를 관리하는 방법- Azure | Microsoft Docs
description: OPC 자격 증명 모음 루트 CA 인증서 및 사용자 권한을 관리합니다.
author: mregen
ms.author: mregen
ms.date: 8/16/2019
ms.topic: conceptual
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 0829d4b3fca068ddb0db2df53dd635ab7ad80bed
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105646366"
---
# <a name="manage-the-opc-vault-certificate-service"></a>OPC 자격 증명 모음 인증서 서비스 관리

> [!IMPORTANT]
> 이 문서를 업데이트하는 동안 최신 콘텐츠는 [Azure Industrial IoT](https://azure.github.io/Industrial-IoT/)를 참조하세요.

이 문서에서는 Azure의 OPC 자격 증명 모음 인증서 관리 서비스에 대한 관리 작업을 설명합니다. 발급자 CA 인증서를 갱신하는 방법, CRL(인증서 해지 목록)을 갱신하는 방법, 사용자 액세스 권한을 부여 및 취소하는 방법에 대한 정보가 포함되어 있습니다.

## <a name="create-or-renew-the-root-ca-certificate"></a>루트 CA 인증서 만들기 또는 갱신

OPC 자격 증명 모음을 배포한 후 루트 CA 인증서를 만들어야 합니다. 유효한 발급자 CA 인증서가 없으면 애플리케이션 인증서를 서명하거나 발급할 수 없습니다. 적절하고 안전한 수명으로 인증서를 관리하려면 [인증서](howto-opc-vault-secure-ca.md#certificates)를 참조하세요. 수명의 절반이 지난 후 발급자 CA 인증서를 갱신합니다. 갱신 시 새로 서명된 애플리케이션 인증서의 구성된 수명이 발급자 CA 인증서의 수명을 초과해서는 안 됩니다.
> [!IMPORTANT]
> 관리자 역할은 발급자 CA 인증서를 만들거나 갱신하는 데 필요합니다.

1. `https://myResourceGroup-app.azurewebsites.net`에서 인증서 서비스를 열고, 로그인합니다.
2. **인증서 그룹** 으로 이동합니다.
3. 하나의 기본 인증서 그룹이 나열되어 있습니다. **편집** 을 선택합니다.
4. **인증서 그룹 세부 정보 편집** 에서 CA 및 애플리케이션 인증서의 주체 이름 및 수명을 수정할 수 있습니다. 주체와 수명은 첫 번째 CA 인증서가 발급되기 전에 한 번만 설정해야 합니다. 작업 중의 수명 변경으로 인해 발급된 인증서와 CRL의 수명이 일치하지 않을 수 있습니다.
5. 올바른 주체를 입력합니다(예: `CN=My CA Root, O=MyCompany, OU=MyDepartment`).<br>
   > [!IMPORTANT]
   > 주체를 변경하는 경우 발급자 인증서를 갱신해야 합니다. 그렇지 않으면 서비스에서 애플리케이션 인증서에 서명하지 못합니다. 구성의 주체는 활성 발급자 인증서의 주체에 대해 확인됩니다. 주체가 일치하지 않으면 인증서 서명이 거부됩니다.
6. **저장** 을 선택합니다.
7. 이 시점에서 "사용할 수 없음" 오류가 발생하면 사용자 자격 증명에 새 루트 인증서를 수정하거나 만들 수 있는 관리자 권한이 없습니다. 기본적으로 서비스를 배포한 사용자는 서비스에 대한 관리자 및 서명 역할이 있습니다. 다른 사용자는 Azure AD(Azure Active Directory) 애플리케이션 등록에 적절한 승인자, 기록기 또는 관리자 역할에 추가되어야 합니다.
8. **세부 정보** 를 선택합니다. 여기에 업데이트된 정보가 표시되어야 합니다.
9. **CA 인증서 갱신** 을 선택하여 첫 번째 발급자 CA 인증서를 발급하거나 발급자 인증서를 갱신합니다. 그런 다음, **확인** 을 선택합니다.
10. 몇 초 후에 **인증서 세부 정보** 를 볼 수 있습니다. OPC UA 애플리케이션에 배포할 최신 CA 인증서 및 CRL을 다운로드하려면 **발급자** 또는 **Crl** 을 선택합니다.

이제 OPC UA 인증서 관리 서비스가 OPC UA 애플리케이션에 대한 인증서를 발급할 준비가 되었습니다.

## <a name="renew-the-crl"></a>CRL 갱신

CRL 갱신은 업데이트이며, 정기적으로 애플리케이션에 배포되어야 합니다. CRL 배포 지점 X509 확장을 지원하는 OPC UA 디바이스는 마이크로 서비스 엔드포인트에서 CRL을 직접 업데이트할 수 있습니다. 다른 OPC UA 디바이스는 수동 업데이트가 필요할 수 있고 또는 GDS 서버 푸시 확장(*)을 사용하여 업데이트되어 인증서 및 CRL로 신뢰 목록을 업데이트할 수 있습니다.

다음 워크플로에서 삭제된 상태의 모든 인증서 요청은 해당 인증서가 발급된 발급자 CA 인증서에 해당하는 CRL에서 해지됩니다. CRL의 버전 번호는 1씩 증가합니다. <br>
> [!NOTE]
> 발급된 모든 CRL은 발급자 CA 인증서가 만료될 때까지 유효합니다. OPC UA 사양에는 CRL에 대한 필수, 결정적 배포 모델이 필요하지 않기 때문입니다.

> [!IMPORTANT]
> 관리자 역할은 발급자 CRL을 갱신하는 데 필요합니다.

1. `https://myResourceGroup.azurewebsites.net`에서 인증서 서비스를 열고, 로그인합니다.
2. **인증서 그룹** 페이지로 이동합니다.
3. **세부 정보** 를 선택합니다. 현재 인증서 및 CRL 정보가 표시되어야 합니다.
4. OPC 자격 증명 모음 스토리지의 모든 활성 발급자 인증서에 대해 업데이트된 CRL을 발급하려면 **CRL 해지 목록(CRL) 업데이트** 를 선택합니다.
5. 몇 초 후에 **인증서 세부 정보** 를 볼 수 있습니다. OPC UA 애플리케이션에 배포할 최신 CA 인증서 및 CRL을 다운로드하려면 **발급자** 또는 **Crl** 을 선택합니다.

## <a name="manage-user-roles"></a>사용자 역할 관리

Azure AD 엔터프라이즈 애플리케이션에서 OPC 자격 증명 모음 마이크로 서비스에 대한 사용자 역할을 관리합니다. 역할 정의에 대한 자세한 설명은 [역할](howto-opc-vault-secure-ca.md#roles)을 참조하세요.

기본적으로 테넌트의 인증된 사용자는 서비스에 읽기 권한자로 로그인할 수 있습니다. 권한이 높은 역할은 Azure Portal 또는 PowerShell을 사용하여 수동으로 관리해야 합니다.

### <a name="add-user"></a>사용자 추가

1. Azure Portal을 엽니다.
2. **Azure Active Directory** > **엔터프라이즈 애플리케이션** 으로 이동합니다.
3. OPC 자격 증명 모음 마이크로 서비스 등록을 선택합니다(기본적으로 `resourceGroupName-service`).
4. **사용자 및 그룹** 으로 이동합니다.
5. **사용자 추가** 를 선택합니다.
6. 특정 역할에 대한 할당을 위해 사용자를 선택하거나 초대합니다.
7. 사용자의 역할을 선택합니다.
8. **할당** 을 선택합니다.
9. 관리자 또는 승인자 역할의 사용자의 경우 Azure Key Vault 액세스 정책 추가를 계속 진행합니다.

### <a name="remove-user"></a>사용자 제거

1. Azure Portal을 엽니다.
2. **Azure Active Directory** > **엔터프라이즈 애플리케이션** 으로 이동합니다.
3. OPC 자격 증명 모음 마이크로 서비스 등록을 선택합니다(기본적으로 `resourceGroupName-service`).
4. **사용자 및 그룹** 으로 이동합니다.
5. 제거할 역할이 있는 사용자를 선택하고 **제거** 를 선택합니다.
6. 관리자 또는 승인자 역할에서 제거된 사용자의 경우 Azure Key Vault 정책에서도 제거합니다.

### <a name="add-user-access-policy-to-azure-key-vault"></a>Azure Key Vault에서 사용자 액세스 정책 추가

추가 액세스 정책은 승인자 및 관리자에게 필요합니다.

기본적으로 서비스 ID는 Key Vault에 액세스하고 사용자 가장 없이 권한이 상승된 작업 또는 변경이 일어나는 것을 방지하는 제한된 권한만 있습니다. 기본 서비스 권한은 비밀 및 인증서에 대한 가져오기 및 나열입니다. 비밀의 경우 한 가지 예외는 서비스가 승인된 후에 비밀 저장소에서 프라이빗 키를 삭제할 수 있다는 점입니다. 다른 모든 작업에는 사용자 가장 권한이 필요합니다.

#### <a name="for-an-approver-role-the-following-permissions-must-be-added-to-key-vault"></a>승인자 역할의 경우 다음 사용 권한을 Key Vault에 추가해야 합니다.

1. Azure Portal을 엽니다.
2. 배포 중에 사용되는 OPC 자격 증명 모음 `resourceGroupName`으로 이동합니다.
3. Key Vault `resourceGroupName-xxxxx`로 이동합니다.
4. **액세스 정책** 으로 이동합니다.
5. **새로 추가** 를 선택합니다.
6. 템플릿을 건너뜁니다. 요구 사항과 일치하는 템플릿이 없습니다.
7. **보안 주체 선택** 을 선택하고 추가할 사용자를 선택하거나 새 사용자를 테넌트에 초대합니다.
8. **가져오기**, **나열** 및 **서명** **키 권한** 을 선택합니다.
9. **가져오기**, **나열**, **설정** 및 **삭제** **비밀 권한** 을 선택합니다.
10. **가져오기** 및 **나열** **인증서 권한** 을 선택합니다.
11. **확인**, **저장** 을 차례로 선택합니다.

#### <a name="for-an-administrator-role-the-following-permissions-must-be-added-to-key-vault"></a>관리자 역할의 경우 다음 사용 권한을 Key Vault에 추가해야 합니다.

1. Azure Portal을 엽니다.
2. 배포 중에 사용되는 OPC 자격 증명 모음 `resourceGroupName`으로 이동합니다.
3. Key Vault `resourceGroupName-xxxxx`로 이동합니다.
4. **액세스 정책** 으로 이동합니다.
5. **새로 추가** 를 선택합니다.
6. 템플릿을 건너뜁니다. 요구 사항과 일치하는 템플릿이 없습니다.
7. **보안 주체 선택** 을 선택하고 추가할 사용자를 선택하거나 새 사용자를 테넌트에 초대합니다.
8. **가져오기**, **나열** 및 **서명** **키 권한** 을 선택합니다.
9. **가져오기**, **나열**, **설정** 및 **삭제** **비밀 권한** 을 선택합니다.
10. **가져오기**, **나열**, **업데이트**, **만들기** 및 **가져오기** **인증서 권한** 을 선택합니다.
11. **확인**, **저장** 을 차례로 선택합니다.

### <a name="remove-user-access-policy-from-azure-key-vault"></a>Azure Key Vault에서 사용자 액세스 정책 제거

1. Azure Portal을 엽니다.
2. 배포 중에 사용되는 OPC 자격 증명 모음 `resourceGroupName`으로 이동합니다.
3. Key Vault `resourceGroupName-xxxxx`로 이동합니다.
4. **액세스 정책** 으로 이동합니다.
5. 제거할 사용자를 찾고 **삭제** 를 선택합니다.

## <a name="next-steps"></a>다음 단계

이제 OPC 자격 증명 모음 인증서와 사용자를 관리하는 방법을 배웠으므로 다음을 수행할 수 있습니다.

> [!div class="nextstepaction"]
> [OPC 디바이스의 보안 통신](howto-opc-vault-secure.md)
