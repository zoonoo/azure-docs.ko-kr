---
title: Azure EA Portal 관리
description: 이 문서에서는 관리자가 Azure EA Portal에서 수행하는 일반적인 작업에 대해 설명합니다.
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 10/07/2019
ms.topic: conceptual
ms.service: billing
manager: boalcsva
ms.openlocfilehash: 21e9d4af783ed5d9eb3ace1c8b5189163b89f8b0
ms.sourcegitcommit: f9e81b39693206b824e40d7657d0466246aadd6e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/08/2019
ms.locfileid: "72035213"
---
# <a name="azure-ea-portal-administration"></a>Azure EA Portal 관리

이 문서에서는 관리자가 Azure EA Portal(https://ea.azure.com) 에서 수행하는 일반적인 작업에 대해 설명합니다. Azure EA Portal은 고객이 Azure EA 서비스의 비용을 관리할 수 있는 온라인 관리 포털입니다. Azure EA Portal을 소개하는 내용은 [Azure EA Portal 시작](billing-ea-portal-get-started.md) 문서를 참조하세요.

## <a name="add-a-new-enterprise-administrator"></a>새 엔터프라이즈 관리자 추가

엔터프라이즈 관리자는 Azure EA 등록을 관리할 때 가장 많은 권한을 가집니다. EA 계약이 설정될 때 초기 Azure EA 관리자가 생성되었습니다. 하지만 언제든지 관리자를 새로 추가하거나 제거할 수 있습니다. 기존 관리자만이 새 관리자를 추가할 수 있습니다. 엔터프라이즈 관리자 추가에 대한 자세한 내용은 [다른 엔터프라이즈 관리자 만들기](billing-ea-portal-get-started.md#create-another-enterprise-admin)를 참조하세요. 청구 프로필 역할 및 작업에 대한 자세한 내용은 [청구 프로필 역할 및 작업](billing-understand-mca-roles.md#billing-profile-roles-and-tasks)을 참조하세요.

## <a name="update-user-state-from-pending-to-active"></a>사용자 상태를 보류 중에서 활성으로 업데이트

새 AO(계정 소유자)를 Azure EA 등록에 처음으로 추가하면 상태가 _보류 중_으로 표시됩니다. 새 계정 소유자는 활성화 시작 이메일을 받으면 로그인하여 계정을 활성화할 수 있습니다. 계정 소유자가 계정을 활성화하면 계정 상태가 _보류 중_에서 _활성_으로 업데이트됩니다. 신규 사용자가 상거래 계정을 만들 때 이름과 성을 입력하라는 메시지가 표시될 수 있습니다. 이 경우 필요한 정보를 추가하여 계속 진행하면 계정이 활성화됩니다.

## <a name="add-a-department-admin"></a>부서 관리자 추가

Azure EA 관리자가 부서를 만들면 Azure 엔터프라이즈 관리자는 부서 관리자를 추가하고 각 부서 관리자를 부서에 연결할 수 있습니다. 부서 관리자는 새 계정을 만들 수 있습니다. Azure EA 구독을 만들려면 새 계정이 필요합니다.

부서 관리자 추가에 대한 자세한 내용은 [Azure EA 부서 관리자 만들기](billing-ea-portal-get-started.md#add-a-department-admin)를 참조하세요.

## <a name="enterprise-user-roles"></a>엔터프라이즈 사용자 역할

Azure EA Portal에서 Azure EA 비용 및 사용량을 관리할 수 있습니다. Azure EA Portal에는 다음과 같은 세 가지 주요 역할이 있습니다.

- EA 관리자
- 부서 관리자
- 계정 소유자

각 역할의 액세스 및 권한 수준은 서로 다릅니다.

사용자 역할에 대한 자세한 내용은 엔터프라이즈 사용자 역할을 참조하세요.

## <a name="add-an-azure-ea-account"></a>Azure EA 계정 추가

Azure EA 계정은 구독 관리에 사용되는 Azure EA Portal의 조직 구성 단위이며 보고에서 사용됩니다. Azure 서비스에 액세스하여 사용하려면 계정을 만들거나 다른 사람이 대신 계정을 만들어주어야 합니다.

Azure 계정에 대한 자세한 내용은 계정 추가를 참조하세요.

## <a name="transfer-an-enterprise-account-to-a-new-enrollment"></a>엔터프라이즈 계정을 새 등록에 양도

엔터프라이즈 계정을 새 등록에 양도할 때 다음 사항을 염두에 두어야 합니다.

- 요청에 지정된 계정만 양도됩니다. 모든 계정을 선택하면 모든 계정이 양도됩니다.
- 원본 등록의 상태는 활성 또는 확장됨으로 유지됩니다. 등록이 만료될 때까지 계속 사용할 수 있습니다.

### <a name="effective-transfer-date"></a>발효 양도 날짜

발효 양도 날짜는 계정을 양도하려는 등록의 시작 날짜 또는 이후 날짜입니다. 계정을 양도받을 등록이 _대상 등록_입니다. 계정 양도 후에는 발효 양도 날짜 이전의 모든 계정 사용량 정보가 원본 등록에 유지됩니다. 양도하려는 계정이 포함된 등록이 _원본 등록_입니다.  원본 등록 사용량에 대한 요금은 현금 약정 금액 또는 초과분으로 청구됩니다. 발효 양도 날짜 이후에 발생하는 사용량은 새 등록으로 전송되고 그에 따라 요금이 청구됩니다.

계정 양도 날짜를 대상 등록의 시작 날짜로 소급할 수 있습니다. 또는 원본 등록 발효 시작 날짜로 소급할 수도 있습니다.

### <a name="monetary-commitment"></a>금액 약정

현금 약정 금액은 등록 간에 양도할 수 없습니다. 현금 약정 잔액은 주문된 등록과 계약으로 연결됩니다. 현금 약정 금액은 계정 또는 등록 양도 프로세스의 일부로 양도되지 않습니다.

### <a name="services-affected"></a>영향을 받는 서비스

계정을 양도하는 동안 가동 중지 시간이 없습니다. 모든 필수 정보를 입력하면 요청 당일에 양도를 완료할 수 있습니다.

### <a name="prerequisites"></a>필수 조건

계정 양도를 요청할 때 다음 정보를 입력하세요.


- 양도할 계정의 이름 및 계정 소유자 ID
- 원본 등록의 경우 양도할 등록 번호 및 계정
- 대상 등록의 경우 대상 등록 번호
- 계정 양도 발효 날짜는 대상 등록의 시작 날짜 또는 이후 날짜입니다.

그 외에도 계정 양도 전에 다음과 같은 사항을 염두에 두어야 합니다.

- 대상 및 원본 등록에는 EA 관리자의 승인이 필요합니다.
  - 경우에 따라 Microsoft에서 원본 등록 EA 관리자의 추가 승인을 요청할 수 있습니다.
- 계정을 양도해도 요구 사항이 충족되지 않는 경우 등록 양도를 고려해 보세요.
- 계정 전송은 특정 계정과 관련된 모든 서비스 및 구독을 전송합니다.
- 전송이 완료된 후 전송된 계정은 원본 등록에서 비활성 상태로 표시됩니다.
- 계정 전송은 대상 등록 시작 날짜 내의 임의의 날짜로 시작될 수 있습니다.
- 이 계정에는 원본 등록의 유효 전송 날짜와 대상 등록의 시작 날짜에 해당하는 종료 날짜가 표시됩니다.
- 유효 전송 날짜 이전 계정에 발생하는 모든 사용량은 원본 등록에 남아 있습니다.


## <a name="transfer-enterprise-enrollment-to-a-new-one"></a>엔터프라이즈 등록을 새 등록에 양도

엔터프라이즈 등록 전체를 다른 등록에 양도해 달라고 요청하면 다음 작업이 수행됩니다.

- 모든 EA 부서 관리자를 포함하여 모든 Azure 서비스, 구독, 계정, 부서 및 등록 구조 전체가 양도됩니다.
- 등록 상태가 _전송됨_으로 설정됩니다. 양도된 등록은 이전 사용량 보고 용도로만 사용할 수 있습니다.
- 양도된 등록에 역할 또는 구독을 추가할 수 없습니다. 양도됨 상태에서는 등록에 사용량을 추가할 수 없습니다.
- 이후 조건을 포함하여 계약의 남은 현금 약정 잔액은 모두 소멸됩니다.

### <a name="effective-transfer-date"></a>발효 양도 날짜

발효 양도 날짜는 대상 등록에 양도하려는 등록의 시작 날짜 또는 이후 날짜입니다.

원본 등록 사용량에 대한 요금은 현금 약정 금액 또는 초과분으로 청구됩니다. 발효 양도 날짜 이후에 발생하는 사용량은 새 등록으로 전송되고 그에 따라 요금이 청구됩니다.

### <a name="effective-transfer-date-in-the-past"></a>과거의 발효 양도 날짜

계정 양도 날짜를 대상 등록의 시작 날짜로 소급할 수 있습니다. 또는 원본 등록의 발효 시작 날짜로 소급할 수도 있습니다.

### <a name="monetary-commitment"></a>금액 약정

현금 약정 금액은 등록 간에 양도할 수 없습니다. 현금 약정 잔액은 주문된 등록과 계약으로 연결됩니다. 현금 약정 금액은 계정 또는 등록 양도 프로세스의 일부로 양도되지 않습니다.

### <a name="services-affected"></a>영향을 받는 서비스

계정을 양도하는 동안 가동 중지 시간이 없습니다. 모든 필수 정보를 입력하면 요청 당일에 양도를 완료할 수 있습니다.

### <a name="prerequisites"></a>필수 조건

등록 양도를 요청할 때 다음 정보를 입력하세요.

- 원본 등록의 경우 이전할 등록 번호 및 계정입니다.
- 대상 등록의 경우 이전할 등록 번호입니다.
- 등록 양도 발효 날짜는 대상 등록의 시작 날짜 또는 이후 날짜입니다. 선택한 날짜는 이미 발급된 초과분 청구서의 사용량에 영향을 주지 않습니다.

그 외에도 등록 양도 전에 다음과 같은 사항을 염두에 두어야 합니다.

- 대상 및 원본 등록에는 EA 관리자의 승인이 필요합니다.
  - 경우에 따라 Microsoft는 원본 등록 EA 관리자에게 추가 승인을 요청할 수 있습니다.
- 등록을 양도해도 요구 사항이 충족되지 않는 경우 계정 양도를 고려해 보세요.
- 지정한 계정만 양도됩니다. 모든 계정을 양도해 달라고 요청할 수 있습니다.
- 원본 등록의 상태는 활성/확장됨으로 유지됩니다. 등록이 만료될 때까지 계속 사용할 수 있습니다.

## <a name="change-account-owner"></a>계정 소유자 변경

Azure EA Portal에서는 한 계정 소유자에서 다른 계정 소유자로 구독을 양도할 수 있습니다. 자세한 내용은 [계정 소유자 변경](billing-ea-portal-get-started.md#change-account-owner)을 참조하세요.

## <a name="subscription-transfer-effects"></a>구독 양도 효과

Azure 구독이 동일한 Azure Active Directory 테넌트의 계정으로 양도되면 리소스 관리를 위한 [RBAC(역할 기반 액세스 제어)](../role-based-access-control/overview.md)가 있는 모든 사용자, 그룹 및 서비스 사용자의 액세스 권한이 유지됩니다.

구독에 대한 RBAC 액세스 권한이 있는 사용자를 보려면 다음을 수행합니다.

1. Azure Portal에서 **구독**을 엽니다.
2. 보고 싶은 구독을 선택한 다음, **액세스 제어(IAM)** 를 선택합니다.
3. **역할 할당**을 선택합니다. 역할 할당 페이지에는 구독에 대한 RBAC 액세스 권한이 있는 모든 사용자가 나열됩니다.

구독이 다른 Azure AD 테넌트의 계정으로 양도되면 리소스 관리를 위한 [RBAC](../role-based-access-control/overview.md)가 있는 모든 사용자, 그룹 및 서비스 사용자의 액세스 권한이 _사라집니다_. RBAC 액세스는 불가능하지만, 다음을 비롯한 보안 메커니즘을 통해 구독에 액세스할 수도 있습니다.

- 구독 리소스에 대한 관리자 권한을 사용자에게 부여하는 관리 인증서. 자세한 내용은 [Azure용 관리 인증서 만들기 및 업로드](../cloud-services/cloud-services-certs-create.md)를 참조하세요.
- 스토리지와 같은 서비스에 대한 액세스 키. 자세한 내용은 [Azure Storage 계정 개요](../storage/common/storage-account-overview.md)를 참조하세요.
- Azure Virtual Machines 같은 서비스에 대한 원격 액세스 자격 증명.

수신자는 Azure 리소스에 대한 액세스를 제한해야 하는 경우 서비스와 연결된 비밀을 업데이트하는 것을 고려해야 합니다. 다음 단계를 사용하여 대부분의 리소스를 업데이트할 수 있습니다.

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.
2. 허브 메뉴에서 **모든 리소스**를 선택합니다.
3. 리소스를 선택합니다.
4. 리소스 페이지에서 **설정**을 클릭하여 기존 비밀을 확인하고 업데이트합니다.



## <a name="close-an-azure-enterprise-enrollment"></a>Azure 엔터프라이즈 등록 종료

다음과 같은 방법으로 Azure EA 등록을 종료할 수 있습니다.

- Azure Portal에서 Azure EA와 연결된 모든 구독을 취소합니다.
- 고객 소프트웨어 관리자 또는 파트너에게 연락하여 Azure 기업계약을 종료해 달라고 요청합니다.

## <a name="update-notification-settings"></a>알림 설정 업데이트

엔터프라이즈 관리자는 등록과 연결된 사용 알림을 받도록 자동으로 등록됩니다. 각 엔터프라이즈 관리자는 개별 알림의 간격을 변경하거나 알림을 완전히 해제할 수 있습니다.

알림 연락처는 Azure EA Portal의 **알림 연락처** 섹션에 표시됩니다. 조직 내 적절한 사용자가 Azure EA 알림을 받도록 알림 연락처를 관리할 수 있습니다.

현재 알림 설정을 보려면 다음을 수행합니다.

1. Azure EA Portal에서 **관리** > **알림 연락처**로 이동합니다.
2. 이메일 주소 – 알림을 받는 엔터프라이즈 관리자의 Microsoft 계정, 회사 또는 학교 계정과 연결된 이메일 주소입니다.
3. 미청구 잔액 알림 빈도 – 각 개별 엔터프라이즈 관리자에게 알림으로 보내도록 설정되는 빈도입니다.

연락처를 추가하려면 다음을 수행합니다.

1. **+연락처 추가**를 클릭합니다.
2. 이메일 주소를 입력하고 확인합니다.
3. **저장**을 클릭합니다.

새 알림 연락처가 **알림 연락처** 섹션에 표시됩니다. 알림 빈도를 변경하려면 알림 연락처를 선택하고, 선택한 행의 오른쪽에 있는 연필 기호를 클릭합니다. 빈도를 **매일**, **매주**, **매월** 또는 **없음**으로 설정합니다.

_적용 기간 종료 날짜 임박_ 및 _비활성화 및 프로비전 해제 날짜 임박_ 수명 주기 알림을 사용하지 않을 수 있습니다. 수명 주기 알림을 사용하지 않도록 설정하면 적용 기간 및 계약 종료 날짜에 대한 알림이 표시되지 않습니다.

## <a name="manage-partner-notifications"></a>파트너 알림 관리

파트너 관리자는 등록에 대한 사용 알림을 받는 빈도를 관리할 수 있습니다. 파트너 관리자에게는 미청구 잔액 알림이 매주 자동으로 전달됩니다. 파트너 관리자는 개별 알림의 빈도를 매월, 매주, 매일로 변경하거나 완전히 해제할 수 있습니다.

사용자가 알림을 받지 못하는 경우 다음 단계에 따라 사용자의 알림 설정이 올바른지 확인합니다.

1. Azure EA Portal에 파트너 관리자로 로그인합니다.
2. **관리**를 클릭하고 **파트너** 탭을 클릭합니다.
3. **관리자** 섹션에서 관리자 목록을 확인합니다.
4. 알림 기본 설정을 편집하려면 적절한 관리자를 마우스로 가리키고 연필 기호를 클릭합니다.
5. 알림 빈도 및 수명 주기를 필요한 대로 설정
6. 연락처를 추가하고, 필요한 경우 **추가**를 클릭합니다.
7. **저장**을 클릭합니다.

![연락처 추가 상자를 보여주는 예제 ](./media/billing-ea-portal-administration/create-ea-manage-partner-notification.png)

## <a name="azure-sponsorship-offer"></a>Azure 스폰서쉽 제안
Azure 스폰서쉽 제안은 제한적인 후원을 받는 Microsoft Azure 계정입니다. 이 제안은 Microsoft에서 선별한 제한된 고객에게만 이메일 초대를 통해 제공됩니다. Microsoft Azure 스폰서쉽 제안 자격이 되는 고객의 계정 ID로 이메일 초대장이 전송됩니다.
자세한 내용은 다음을 참조하세요.

- 스폰서쉽 제안 개요 - https://azure.microsoft.com/en-us/offers/ms-azr-0143p/
- 스폰서쉽 잔액 포털 - https://www.microsoftazuresponsorships.com/balance  
- 스폰서쉽 외부 FAQ - https://azuresponsorships-staging.azurewebsites.net/faq
- 스폰서쉽 활성화에 대한 지원 요청 - http://aka.ms/azrsponsorship

## <a name="next-steps"></a>다음 단계
- [가상 머신 예약](billing-ea-portal-vm-reservations.md)을 통해 비용을 절감하는 방법에 대해 알아보세요.
- Azure EA Portal 이슈를 해결하는 데 도움이 필요한 경우 [Azure EA Portal 액세스 문제 해결](billing-ea-portal-troubleshoot.md)을 참조하세요.
