---
title: Dynamics 365 for Customer Engagement 애플리케이션 제안 시험 사용 탭 - Azure Marketplace | Microsoft Docs
description: AppSource Marketplace에서 Dynamics 365 for Customer Engagement 애플리케이션 제안에 대한 시험 사용을 구성하는 방법입니다.
services: Azure, Marketplace, AppSource, Cloud Partner Portal, Dynamics 365 for Customer Engagement
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 12/25/2018
ms.author: pbutlerm
ms.openlocfilehash: 373312b4c7f05fe41c9ca8165b8ff6f1b0e56f1b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60322857"
---
# <a name="dynamics-365-for-customer-engagement-application-test-drive-tab"></a>Dynamics 365 for Customer Engagement 애플리케이션 시험 사용 탭

**시험 사용** 탭을 사용하여 고객을 위한 체험 환경을 만듭니다.  실제 구현 시나리오에서 입증된 제안의 주요 기능 및 이점에 대한 실습 중심의 셀프 가이드 평가판을 고객에게 제공합니다.  사용 가능한 평가판 옵션 중에서 시험 사용은 우수한 잠재 고객 생성 및 이러한 잠재 고객의 전환율을 높이는 데 가장 효과적입니다.  자세한 내용은 [시험 사용이란?](../test-drive/what-is-test-drive.md)을 참조하세요.

Dynamic 365 애플리케이션에 대한 시험 사용 환경은 자동으로 Microsoft 호스팅 솔루션으로 실행됩니다.  자세한 내용은 [호스팅 시험 사용](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/hosted-test-drive)을 참조하세요.

시험 사용 탭에는 **시험 사용**, **세부 정보** 및 **기술 구성**의 세 가지 잠재적인 섹션이 있습니다.  마지막 두 섹션은 시험 사용 기능을 사용하도록 설정한 후에만 표시됩니다.  필드 이름에 추가된 별표(*)는 필수를 나타냅니다. 


## <a name="test-drive-section"></a>시험 사용 섹션

이 기능을 사용하도록 설정하려면 **시험 사용 설정**을 **예**로 선택합니다.


## <a name="details-section"></a>세부 정보 섹션

**세부 정보** 섹션에서는 기본적인 시험 사용 정보를 제공합니다.   

![시험 사용의 세부 정보 섹션](./media/test-drive-tab-details.png)

다음 표에서는 Dynamics 365 애플리케이션의 시험 사용을 설정하는 데 필요한 필드에 대해 설명합니다.

|      필드                    |    설명                  |
|    ---------                  |  ---------------                |
|      설명              |   시험 사용에서 수행할 수 있는 작업을 설명합니다. 기본 HTML 태그를 사용하여 이 설명의 서식을 지정할 수 있습니다. 예: &lt;p&gt;, &lt;em&gt;, &lt;ul&gt;, &lt;li&gt;, &lt;ol&gt; 및 제목  |
|  사용자 설명서                  |   고객이 시험 사용 환경을 진행하는 데 사용할 수 있는 사용자 설명서를 업로드합니다. 이 문서는 .pdf 파일이어야 합니다.              |
|  시험 사용 데모 비디오(선택 사항) |  시험 사용에 대한 연습 비디오를 제공할 수 있습니다. 고객은 시험 사용을 진행하기 전에 이 비디오를 시청할 수 있습니다. YouTube 또는 Vimeo에 비디오 URL을 제공합니다. **+비디오 추가**를 선택하면 다음 정보를 제공하라는 메시지가 표시됩니다.<ul><li>Name</li><li>URL</li><li>썸네일(PNG 형식, 533x324픽셀)</li></ul>  |
|   |   |


## <a name="technical-configuration-section"></a>기술 구성 섹션

이 섹션에서는 시험 사용에 대한 기술적인 세부 정보를 제공합니다.

![시험 사용의 세부 정보 섹션](./media/test-drive-tab-tech-config.png)

필드의 용도는 다음과 같습니다.

|      필드                    |    설명                  |
|    ---------                  |  ---------------                |
| 시험 사용 유형            | **Microsoft 호스팅(Dynamics 365 for Customer Engagement)** 을 선택합니다.  |
| 최대 동시 시험 사용 수    | 지정된 시점에 활성 시험 사용의 동시 인스턴스 수입니다. 각 사용자는 시험 사용을 활성화할 때 Dynamics 라이선스를 사용하므로 여기서 설정하는 시험 사용 사용자 수 이상의 Dynamics 라이선스가 있는지 확인해야 합니다. 권장 값은 3~5입니다.  |
| 시험 사용 기간(시)   | 사용자의 시험 사용 인스턴스가 활성화되는 최대 시간입니다. 이 기간을 초과하면 해당 인스턴스가 테넌트에서 프로비전 해제됩니다. 추천 값은 앱의 복잡성에 따라 2~24시간입니다. 시간이 부족하여 다시 평가하려는 경우 사용자는 언제든지 다른 시험 사용를 요청할 수 있습니다.  |
| 인스턴스 URL                  | 시험 사용이 처음으로 이동하게 되는 URL입니다. 일반적으로는 앱과 샘플 데이터가 설치된 Dynamics 365 인스턴스의 URL입니다.  |
| Azure AD 테넌트 ID            | Dynamics 365 인스턴스에 대한 Azure 테넌트의 GUID입니다. 이 값을 검색하려면 Azure Portal에 로그인하고, **Azure Active Directory** > **속성 선택** > **디렉터리 ID 복사**로 차례로 이동합니다.  |
| Azure AD 앱 ID               | Azure AD 애플리케이션의 GUID입니다.  |
| Azure AD 앱 키              | Azure AD 애플리케이션의 비밀입니다(예: `IJUgaIOfq9b9LbUjeQmzNBW4VGn6grr1l/n3aMrnfdk=`). |
| Azure AD 테넌트 이름          | Dynamics 365 인스턴스에 대한 Azure 테넌트의 이름입니다. <tenantname.>onmicrosoft.com 형식을 사용합니다(예: `testdrive.onmicrosoft.com`).  |
| 인스턴스 웹 API URL          | Dynamics 365 인스턴스에 대한 웹 API URL입니다. 이 값은 Microsoft Dynamics 365 인스턴스에 로그인하고, **설정** > **사용자 지정** > **개발자 리소스** > **인스턴스 웹 API(이 URL 복사)** 로 차례로 이동하여 검색할 수 있습니다. 예제 값: `https://testdrive.crm.dynamics.com/api/data/v9.0`  |
| 역할 이름                     | 시험 사용에 대해 만든 사용자 지정 Dynamics 365 보안 역할의 이름이며, 사용자가 이 애플리케이션을 실행할 때 할당됩니다(예: `testdriveuser`). |
|  |  |

필요한 정보가 모두 제공되면 **저장**을 선택합니다.


## <a name="next-steps"></a>다음 단계

다음으로, [상점 세부 정보 탭](./cpp-storefront-details-tab.md)에서 마케팅 및 영업 정보를 제공합니다.

