---
title: Azure Data Box에 대 한 Lockbox 구성
description: Azure Data Box에서 고객 Lockbox를 사용 하는 방법을 알아봅니다.
services: databox
author: alkohli
ms.service: databox
ms.topic: how-to
ms.date: 07/10/2020
ms.author: alkohli
ms.subservice: pod
ms.openlocfilehash: 9c95760d03db976b59537adcecbe39a942b72126
ms.sourcegitcommit: 3541c9cae8a12bdf457f1383e3557eb85a9b3187
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/09/2020
ms.locfileid: "86209566"
---
# <a name="use-customer-lockbox-for-azure-data-box-preview"></a>Azure Data Box에 대 한 고객 Lockbox 사용 (미리 보기)

Azure Data Box는 Azure에서 고객 데이터를 전송 하는 데 사용 됩니다. 지원 요청 중에 Microsoft 지원 고객 데이터에 액세스 해야 하는 경우가 있습니다. 고객 Lockbox를 인터페이스로 사용 하 여 이러한 데이터 액세스 요청을 검토 하 고 승인 하거나 거부할 수 있습니다. 

이 문서에서는 Data Box 가져오기 및 내보내기 주문에 대해 고객 Lockbox 요청을 시작 및 추적 하는 방법을 설명 합니다. 이 문서는 Azure Data Box 장치와 Azure Data Box Heavy 장치 모두에 적용 됩니다. 

## <a name="devops-workflow-for-data-access"></a>데이터 액세스를 위한 DevOps 워크플로

Microsoft의 지원 및 Data Box 운영 팀은 일반적으로 고객 데이터에 액세스 하지 않습니다. 표준 도구 및 원격 분석을 사용 하 여 문제를 해결 하려고 합니다. <!--The only scenarios where there is a need to access customer data is when there is an issue with the data that needs to be fixed. For example, if the data is copied to a wrong folder or is in an incorrect format and is likely to result in an upload or download failure, then Microsoft will try to access your data in the Azure datacenter.--> 

문제를 해결할 수 없고 데이터를 조사 또는 복구 하기 위해 Microsoft 지원 해야 하는 경우 JIT (Just-in-time) 포털을 통해 상승 된 액세스를 요청 합니다. JIP 포털은 사용 권한 수준을 확인 하 고, multi-factor authentication을 제공 하며, 내부 Microsoft 승인자의 승인을 포함 합니다. 예를 들어 승인자는 DevOps 관리자가 될 수 있습니다. 

승격 된 액세스 요청이 JIT 포털을 통해 승인 된 후에는 Lockbox를 사용 하도록 설정한 경우 데이터 액세스에 대 한 명시적인 동의가 필요 합니다. 포털의 고객 Lockbox 서비스를 통해 액세스를 요청 하 고 추적 합니다. 

Lockbox를 사용 하도록 설정 하지 않은 경우 데이터 액세스에 대 한 동의가 필요 하지 않습니다.


## <a name="prerequisites-for-access-request"></a>액세스 요청에 대 한 필수 조건

시작하기 전에 다음 사항을 확인합니다.

1. 의 지침에 따라 Azure Data Box 순서를 만들었습니다.
    1. [자습서:](data-box-deploy-ordered.md) 주문 가져오기에 대 한 Azure Data Box 주문
    1. [자습서:](data-box-deploy-export-ordered.md) 내보내기 주문에 대 한 Azure Data Box 주문

2. Data Box에 대 한 고객 Lockbox를 구성 했습니다. 이 서비스는 옵트인 (opt in) 서비스입니다. 

    1. 고객 Lockbox은 현재 Data Box 서비스에 대해 미리 보기로 제공 됩니다. 조직의 Data Box에 대 한 고객 Lockbox를 사용 하도록 설정 하려면 [Azure 공개 미리 보기 고객 Lockbox](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR_Kwz02N6XVCoKNpxIpqE_hUNzlTUUNYVkozOVlFNVRSWDVHRkkwTFQyViQlQCN0PWcu)에 등록 합니다.
    2. 고객 Lockbox는 최소한의 개발자로 Azure 지원 계획을 보유 하 고 있는 모든 고객에 게 자동으로 제공 됩니다. <!--How do you enable Lockbox? change this for Azure Data Box, perhaps you need a different support plan When you have an eligible support plan, no action is required by you to enable Customer Lockbox. Customer Lockbox requests are initiated by a Microsoft engineer if this action is needed to progress a support ticket that is filed from somebody in your organization.-->

3. 이 문제에 대 한 서비스 요청 또는 지원 티켓이 이미 열려 있습니다. 지원 티켓에 대 한 자세한 내용은 [파일 a 서비스 요청 Data Box](data-box-disk-contact-microsoft-support.md)를 참조 하세요.


## <a name="track-approve-request-via-lockbox"></a>추적, Lockbox를 통한 요청 승인

고객 데이터에 액세스 하는 요청을 추적 하 고 승인 하려면 다음 단계를 수행 합니다.

1. Microsoft는 Azure 데이터 센터에서 데이터를 업로드 하거나 다운로드 하는 동안 문제가 발생 하는 것을 감지 합니다. 예를 들어 Data Box 순서는 **데이터 복사** 단계에서 중단 됩니다. 

    지원 엔지니어가 지원 세션을 통해 Data Box에 연결 하 고 표준 도구 및 원격 분석을 사용 하 여 문제를 해결 하려고 합니다. Data Box 디스크가 잠겨 있고 공유에 액세스할 수 없는 경우 지원 엔지니어가 Lockbox 요청을 만듭니다. 
 
2. 요청이 생성 되 면 일반적으로 알림이 구독 관리자로 이동 하지만 알림에 대 한 그룹을 구성할 수도 있습니다. 

3. 승인 Azure Portal에서 lockbox 요청을 확인할 수 있습니다. 

    ![Azure Portal 요청](./media/data-box-customer-lockbox/3-lockbox-request-azure-portal.png)

    포털에서 lockbox 요청을 승인 하려면 **승인**을 선택할 수 있습니다.

    ![요청 승인](./media/data-box-customer-lockbox/4-lockbox-request-details-azure-portal.png)


    요청이 승인 되 면 장치 디스크의 잠금이 해제 되 고 지원 세션에서 공유에 액세스할 수 있습니다.

4. 지원 엔지니어가 업로드 문제를 해결 한 다음 지원 세션을 사용 하지 않도록 설정 합니다.

문제가 해결 된 후에는 데이터 복사 작업이 완료 될 때까지 진행 됩니다.


## <a name="next-steps"></a>다음 단계

- [Microsoft Azure에 대 한 고객 Lockbox](https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview)

<!--- [Approve, audit support access requests to VMs using Customer Lockbox for Azure](https://azure.microsoft.com/blog/approve-audit-support-access-requests-to-vms-using-customer-lockbox-for-azure/)-->

