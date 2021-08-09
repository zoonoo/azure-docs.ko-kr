---
title: Azure Data Box를 위한 Lockbox 구성
description: Azure Data Box에서 고객 Lockbox를 사용하는 방법을 알아봅니다.
services: databox
author: alkohli
ms.service: databox
ms.topic: how-to
ms.date: 07/10/2020
ms.author: alkohli
ms.subservice: pod
ms.openlocfilehash: 178ad169c1b576458f38b440ca79f4bb9eb012f5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92124914"
---
# <a name="use-customer-lockbox-for-azure-data-box-preview"></a>Azure Data Box를 위해 고객 Lockbox 사용(미리 보기)

Azure Data Box는 고객 데이터를 Azure와 주고받는 데 사용됩니다. Microsoft 지원에서 지원 요청 중에 고객 데이터에 액세스해야 하는 경우가 있습니다. 고객 Lockbox를 인터페이스로 사용하여 이러한 데이터 액세스 요청을 검토하고 승인하거나 거부할 수 있습니다. 

이 문서에서는 Data Box 가져오기 및 내보내기 주문에 대해 고객 Lockbox 요청을 시작하고 추적하는 방법을 설명합니다. 이 문서는 Azure Data Box 디바이스와 Azure Data Box Heavy 디바이스 모두에 적용됩니다. 

## <a name="devops-workflow-for-data-access"></a>데이터 액세스를 위한 DevOps 워크플로

Microsoft의 지원 및 Data Box 운영 팀은 일반적으로 고객 데이터에 액세스하지 않고, 표준 도구와 원격 측정을 사용하여 문제를 해결하려고 합니다. <!--The only scenarios where there is a need to access customer data is when there is an issue with the data that needs to be fixed. For example, if the data is copied to a wrong folder or is in an incorrect format and is likely to result in an upload or download failure, then Microsoft will try to access your data in the Azure datacenter.--> 

문제를 해결할 수 없고 Microsoft 지원에서 데이터를 조사하거나 복구해야 하는 경우 JIT(Just in Time) 포털을 통해 상승된 액세스 권한을 요청합니다. JIT 포털은 권한 수준을 확인하고 다단계 인증을 제공하며 내부 Microsoft 승인자의 승인도 포함합니다. 예를 들어 승인자는 DevOps 관리자일 수 있습니다. 

JIT 포털을 통해 상승된 액세스 요청이 승인된 후 Lockbox를 사용하도록 설정한 경우 Microsoft는 데이터 액세스에 대한 명시적 동의도 요구합니다. 포털의 고객 Lockbox 서비스를 통해 액세스가 요청되고 추적됩니다. 

Lockbox를 사용하도록 설정하지 않은 경우 데이터에 액세스하는 데 동의가 필요하지 않습니다.


## <a name="prerequisites-for-access-request"></a>액세스 요청을 위한 필수 조건

시작하기 전에 다음 사항을 확인합니다.

1. 다음 지침에 따라 Azure Data Box 주문을 만들었습니다.
    1. [자습서: 가져오기 주문에 대한 Azure Data Box 주문](data-box-deploy-ordered.md)
    1. [자습서: 내보내기 주문에 대한 Azure Data Box 주문](data-box-deploy-export-ordered.md).

2. Data Box에 대해 고객 Lockbox를 구성했습니다. 옵트인 서비스입니다. 

    1. 고객 Lockbox는 현재 Data Box 서비스의 미리 보기 상태입니다. 조직의 Data Box에 대한 고객 Lockbox를 사용하도록 설정하려면 [Azure 공개 미리 보기에 대한 고객 Lockbox](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR_Kwz02N6XVCoKNpxIpqE_hUNzlTUUNYVkozOVlFNVRSWDVHRkkwTFQyViQlQCN0PWcu)에 등록합니다.
    2. 고객 Lockbox는 최소 수준의 개발자와 함께 Azure 지원 계획이 있는 모든 고객에게 자동으로 제공됩니다. <!--How do you enable Lockbox? change this for Azure Data Box, perhaps you need a different support plan When you have an eligible support plan, no action is required by you to enable Customer Lockbox. Customer Lockbox requests are initiated by a Microsoft engineer if this action is needed to progress a support ticket that is filed from somebody in your organization.-->

3. 이 문제에 대한 서비스 요청 또는 지원 티켓이 이미 열려 있습니다. 지원 티켓에 대한 자세한 내용은 [Data Box에 대한 서비스 요청 제출](data-box-disk-contact-microsoft-support.md)을 참조하세요.


## <a name="track-approve-request-via-lockbox"></a>Lockbox를 통한 요청 추적, 승인

고객 데이터 액세스 요청을 추적하고 승인하려면 다음 단계를 따르세요.

1. Microsoft는 Azure 데이터 센터에서 데이터를 업로드 또는 다운로드하는 동안 문제가 있음을 감지합니다. 예를 들어 Data Box 주문은 **데이터 복사** 단계에서 중단됩니다. 

    지원 엔지니어는 지원 세션을 통해 Data Box에 연결하고 표준 도구 및 원격 분석을 사용하여 문제를 해결하려고 합니다. Data Box 디스크가 잠겨 있고 공유에 액세스할 수 없는 경우 지원 엔지니어는 Lockbox 요청을 생성합니다. 
 
2. 요청이 생성되면 일반적으로 알림이 구독 관리자에게 전달되지만 알림 그룹을 구성할 수도 있습니다. 

3. 승인을 위해 Azure Portal에서 Lockbox 요청을 볼 수 있습니다. 

    ![Azure Portal에서 요청](./media/data-box-customer-lockbox/3-lockbox-request-azure-portal.png)

    포털에서 Lockbox 요청을 승인하려면 **승인** 을 선택할 수 있습니다.

    ![요청 승인](./media/data-box-customer-lockbox/4-lockbox-request-details-azure-portal.png)


    요청이 승인되면 디바이스 디스크의 잠금이 해제되고 지원 세션에서 공유에 액세스할 수 있습니다.

4. 지원 엔지니어가 업로드 문제를 해결한 다음 지원 세션을 사용하지 않도록 설정합니다.

문제가 해결되면 데이터 복사 작업이 완료될 때까지 진행됩니다.


## <a name="next-steps"></a>다음 단계

- [Microsoft Azure에 대한 고객 Lockbox](../security/fundamentals/customer-lockbox-overview.md)

<!--- [Approve, audit support access requests to VMs using Customer Lockbox for Azure](https://azure.microsoft.com/blog/approve-audit-support-access-requests-to-vms-using-customer-lockbox-for-azure/)-->