---
title: "Azure AD Domain Services에서 보안 LDAP(LDAPS) 구성 | Microsoft Docs"
description: "Azure AD 도메인 서비스 관리되는 도메인에 대해 보안 LDAP(LDAPS) 구성"
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: stevenpo
editor: curtand
ms.assetid: 9d563c45-9578-410d-96c8-355af64feae8
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/14/2017
ms.author: maheshu
ms.translationtype: HT
ms.sourcegitcommit: 1e6fb68d239ee3a66899f520a91702419461c02b
ms.openlocfilehash: 3aafe209aad7383cd0610d147b5fdba673023c93
ms.contentlocale: ko-kr
ms.lasthandoff: 08/16/2017

---
# <a name="configure-secure-ldap-ldaps-for-an-azure-ad-domain-services-managed-domain"></a>Azure AD Domain Services 관리되는 도메인에 대해 보안 LDAP(LDAPS) 구성

## <a name="before-you-begin"></a>시작하기 전에
[작업 2 - 보안 LDAP 인증서를 .PFX 파일로 내보내기](active-directory-ds-admin-guide-configure-secure-ldap-export-pfx.md)를 완료했는지 확인합니다.

이 작업을 완료하는 데 미리 보기 Azure Portal 환경을 사용할 것인지 Azure 클래식 포털을 사용할 것인지 선택합니다.
> [!div class="op_single_selector"]
> * **Azure Portal(미리 보기)**: [Azure Portal을 사용하여 보안 LDAP를 사용하도록 설정](active-directory-ds-admin-guide-configure-secure-ldap-enable-ldaps.md)
> * **Azure 클래식 포털**: [클래식 Azure Portal을 사용하여 보안 LDAP를 사용하도록 설정](active-directory-ds-admin-guide-configure-secure-ldap-enable-ldaps-classic.md)
>
>


## <a name="task-3---enable-secure-ldap-for-the-managed-domain-using-the-classic-azure-portal"></a>작업 3 - 클래식 Azure Portal을 사용하여 관리되는 도메인에 대해 보안 LDAP를 사용하도록 설정
보안 LDAP를 사용하도록 설정하려면 다음 구성 단계를 수행합니다.

1. **[Azure 클래식 포털](https://manage.windowsazure.com)**로 이동합니다.
2. 왼쪽 창에서 **Active Directory** 노드를 선택합니다.
3. Azure AD 도메인 서비스를 사용하도록 설정한 Azure AD 디렉터리('테넌트'라고도 함)를 선택합니다.

    ![Azure AD 디렉터리 선택](./media/active-directory-domain-services-getting-started/select-aad-directory.png)
4. **구성** 탭을 클릭합니다.

    ![디렉터리의 탭 구성](./media/active-directory-domain-services-getting-started/configure-tab.png)
5. **도메인 서비스**섹션까지 아래로 스크롤합니다. 다음 스크린샷에 표시된 것처럼 **보안 LDAP(LDAPS)** 옵션이 표시됩니다.

    ![도메인 서비스 구성 섹션](./media/active-directory-domain-services-admin-guide/secure-ldap-start.png)
6. **인증서 구성...** 단추를 클릭하면 **보안 LDAP에 대한 인증서 구성** 대화 상자가 표시됩니다.

    ![보안 LDAP에 대한 인증서 구성](./media/active-directory-domain-services-admin-guide/secure-ldap-configure-cert-page.png)
7. **인증서가 있는 PFX 파일** 뒤의 폴더 아이콘을 클릭하여 관리되는 도메인에 대한 보안 LDAP 액세스에 사용할 인증서가 들어 있는 PFX 파일을 지정합니다. 또한 인증서를 PFX 파일로 내보낼 때 지정한 암호도 입력합니다. 완료되면 아래쪽에 있는 [완료] 단추를 클릭합니다.

    ![보안 LDAP PFX 파일 및 암호 지정](./media/active-directory-domain-services-admin-guide/secure-ldap-specify-pfx.png)
8. **구성** 탭의 **도메인 서비스** 섹션이 회색 표시되고 몇 분 동안 **보류 중...** 상태가 됩니다. 이 기간 중에 LDAPS 인증서의 정확도가 확인되고 관리되는 도메인에 대해 보안 LDAP가 구성됩니다.

    ![보안 LDAP - 보류 중 상태](./media/active-directory-domain-services-admin-guide/secure-ldap-pending-state.png)

   > [!NOTE]
   > 관리되는 도메인에 대해 보안 LDAP를 사용하도록 설정하는 데 약 10-15분이 소요됩니다. 제공된 보안 LDAP 인증서가 필수 조건을 만족하지 않으면 보안 LDAP는 디렉터리에 사용할 수 없으며 오류가 표시됩니다. 도메인 이름이 올바르지 않거나 인증서가 만료되었거나 곧 만료된다는 오류 등이 여기에 해당합니다.
   >
   >

9. 관리되는 도메인에 대해 보안 LDAP를 사용하도록 설정하는 데 성공하면 **보류 중...** 메시지가 사라집니다. 표시된 인증서의 지문이 나타납니다.

    ![보안 LDAP 사용](./media/active-directory-domain-services-admin-guide/secure-ldap-enabled.png)

<br>

## <a name="task-4---enable-secure-ldap-access-over-the-internet"></a>작업 4 - 인터넷을 통해 보안 LDAP 액세스 사용
**선택적 태스크** - 인터넷을 통해 LDAPS를 사용하여 관리되는 도메인에 액세스하지 않으려면 이 구성 태스크를 건너뜁니다.

이 태스크를 시작하기 전에 [태스크 3](#task-3---enable-secure-ldap-for-the-managed-domain-using-the-classic-azure-portal)에 나와 있는 단계를 완료해야 합니다.

1. **구성** 페이지의 **도메인 서비스** 섹션에서 **인터넷을 통해 보안 LDAP 액세스 사용** 옵션이 표시됩니다. 보안 LDAP를 통해 관리되는 도메인에 대한 인터넷 액세스가 기본적으로 사용되지 않으므로 이 옵션은 기본적으로 **아니요** 로 설정됩니다.

    ![보안 LDAP 사용](./media/active-directory-domain-services-admin-guide/secure-ldap-enabled2.png)
2. **인터넷을 통해 보안 LDAP 액세스 사용**을 **예**로 토글합니다. 아래쪽 패널에서 **저장** 단추를 클릭합니다.
    ![보안 LDAP 사용](./media/active-directory-domain-services-admin-guide/secure-ldap-enable-internet-access.png)
3. **구성** 탭의 **도메인 서비스** 섹션이 회색 표시되고 몇 분 동안 **보류 중...** 상태가 됩니다. 잠시 동안 보안 LDAP 통한 관리되는 도메인에 대한 인터넷 액세스가 활성화됩니다.

    ![보안 LDAP - 보류 중 상태](./media/active-directory-domain-services-admin-guide/secure-ldap-enable-internet-access-pending-state.png)

   > [!NOTE]
   > 보안 LDAP를 통해 관리되는 도메인에 대한 인터넷 액세스를 사용하도록 설정하는 데 약 10분이 소요됩니다.
   >
   >
4. 인터넷을 통한 관리되는 도메인에 대해 보안 LDAP를 사용하도록 설정하는 데 성공하면 **보류 중...** 메시지가 사라집니다. LDAPS를 통해 디렉터리에 액세스하는 데 사용할 수 있는 외부 IP 주소가 **LDAPS 액세스를 위한 외부 IP 주소**필드에 표시됩니다.

    ![보안 LDAP 사용](./media/active-directory-domain-services-admin-guide/secure-ldap-internet-access-enabled.png)

<br>

## <a name="task-5---configure-dns-to-access-the-managed-domain-from-the-internet"></a>작업 5 - 인터넷에서 관리되는 도메인에 액세스할 DNS 구성
**선택적 태스크** - 인터넷을 통해 LDAPS를 사용하여 관리되는 도메인에 액세스하지 않으려면 이 구성 태스크를 건너뜁니다.

이 태스크를 시작하기 전에 [태스크 4](#task-4---enable-secure-ldap-access-over-the-internet)에 나와 있는 단계를 완료해야 합니다.

관리되는 도메인에 대한 인터넷을 통한 보안 LDAP 액세스를 사용하도록 설정했으면 클라이언트 컴퓨터가 관리되는 도메인을 찾을 수 있도록 DNS를 업데이트 야 합니다. 태스크 4의 끝에 외부 IP 주소가 **LDAPS 액세스를 위한 외부 IP 주소**의 **구성** 탭에 표시됩니다.

관리되는 도메인의 DNS 이름(예: 'ldaps.contoso100.com')이 이 외부 IP 주소를 가리키도록 외부 DNS 공급자를 구성합니다. 이 예에서는 다음과 같은 DNS 항목을 만들어야 합니다.

    ldaps.contoso100.com  -> 52.165.38.113

이것으로 끝입니다. 이제 인터넷을 통해 보안 LDAP를 사용하여 관리되는 도메인에 연결할 준비가 되었습니다.

> [!WARNING]
> 클라이언트 컴퓨터가 LDAPS를 사용하여 관리되는 도메인에 성공적으로 연결하려면 LDAPS 인증서의 발급자를 신뢰해야 한다는 점에 유의하세요. 엔터프라이즈 인증 기관 또는 공개적으로 신뢰할 수 있는 인증 기관을 사용하는 경우 클라이언트 컴퓨터가 이러한 인증서 발급자를 신뢰하므로 문제가 되지 않습니다. 자체 서명된 인증서를 사용하는 경우 자체 서명된 인증서의 공개 부분을 클라이언트 컴퓨터의 신뢰할 수 있는 인증서 저장소에 설치해야 합니다.
>
>


## <a name="lock-down-ldaps-access-to-your-managed-domain-over-the-internet"></a>인터넷을 통해 관리되는 도메인에 대한 LDAPS 액세스 잠금
> [!NOTE]
> **선택적 작업** - 인터넷을 통해 관리되는 도메인에 대한 LDAPS 액세스를 사용하여 않는 경우 이 구성 작업을 건너뜁니다.
>
>

이 태스크를 시작하기 전에 [태스크 4](#task-4---enable-secure-ldap-access-over-the-internet)에 나와 있는 단계를 완료해야 합니다.

인터넷을 통해 LDAPS 액세스가 가능하도록 관리되는 도메인을 노출하면 보안 위협이 됩니다. 관리되는 도메인은 보안 LDAP에 사용되는 포트(즉, 포트 636)에서 인터넷을 통해 접속할 수 있습니다. 따라서 관리되는 도메인에 대한 액세스를 알려진 특정 IP 주소로 제한할 수 있습니다. 보안이 향상되도록 NSG(네트워크 보안 그룹)를 만들고 Azure AD Domain Services를 사용하도록 설정된 서브넷과 연결합니다.

다음 표에서는 구성 가능한 예제 NSG를 통해 인터넷을 통한 보안 LDAP 액세스를 잠그는 방법을 보여 줍니다. 이 NSG에는 지정된 IP 주소 집합에서만 TCP 포트 636을 통해 인바운드 LDAPS 액세스를 허용하는 규칙 집합이 포함되어 있습니다. 기본 'DenyAll' 규칙은 인터넷의 다른 모든 인바운드 트래픽에 적용됩니다. 지정된 IP 주소에서 인터넷을 통해 LDAPS 액세스를 허용하는 NSG 규칙은 DenyAll NSG 규칙보다 우선 순위가 높습니다.

![인터넷을 통해 LDAPS 액세스를 보안하는 예제 NSG](./media/active-directory-domain-services-admin-guide/secure-ldap-sample-nsg.png)

**자세한 정보** - [네트워크 보안 그룹](../virtual-network/virtual-networks-nsg.md).

<br>

## <a name="related-content"></a>관련 콘텐츠
* [Azure AD Domain Services - 시작 가이드](active-directory-ds-getting-started.md)
* [Azure AD 도메인 서비스 관리되는 도메인 관리](active-directory-ds-admin-guide-administer-domain.md)
* [Azure AD Domain Services 관리되는 도메인에서 그룹 정책 관리](active-directory-ds-admin-guide-administer-group-policy.md)
* [네트워크 보안 그룹](../virtual-network/virtual-networks-nsg.md)
* [네트워크 보안 그룹 만들기](../virtual-network/virtual-networks-create-nsg-arm-pportal.md)

