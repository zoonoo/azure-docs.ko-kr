---
title: Azure Active Directory에 응용 프로그램을 추가 하거나 제거 하는 일반적인 문제 해결
description: Azure Active Directory에 앱을 추가 하거나 제거할 때 직면 하는 일반적인 문제를 해결 합니다.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: troubleshooting
ms.date: 09/11/2018
ms.author: kenwith
ms.openlocfilehash: e9e97aec66d99d149320938540c48b9ad68eaf0e
ms.sourcegitcommit: 1fe5127fb5c3f43761f479078251242ae5688386
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/14/2020
ms.locfileid: "90068033"
---
# <a name="troubleshoot-common-problem-adding-or-removing-an-application-to-azure-active-directory"></a>Azure Active Directory에 응용 프로그램을 추가 하거나 제거 하는 일반적인 문제 해결
이 문서는 Azure Active Directory에 앱을 추가 하거나 제거할 때 직면 하는 일반적인 문제를 이해 하는 데 도움이 됩니다.

## <a name="i-clicked-the-add-button-and-my-application-took-a-long-time-to-appear"></a>"추가" 단추를 클릭했고 애플리케이션이 나타나는 데 시간이 오래 걸렸음
경우에 따라 디렉터리에 추가한 후 애플리케이션이 나타나는 데 1-2분 정도(경우에 따라 더 길게) 걸릴 수 있습니다. 이는 예상한 정상 성능이 아니지만, [Azure Portal](https://portal.azure.com/)의 오른쪽 위에 있는 **알림** 아이콘(벨)을 클릭하고 **진행 중** 또는 **완료** 알림 레이블이 지정된 **애플리케이션 추가**를 찾아 애플리케이션 추가가 진행 중임을 볼 수 있습니다.

애플리케이션이 추가되지 않거나 **추가** 단추를 클릭할 때 오류가 발생하는 경우 **오류** 상태에 **알림**이 표시됩니다. 더 자세히 알아보거나 지원 엔지니어와 공유하기 위해 오류에 대한 자세한 정보를 원하는 경우 [포털 알림의 세부 정보를 확인하는 방법](#how-to-see-the-details-of-a-portal-notification) 섹션의 단계를 따라 오류에 대한 자세한 정보를 볼 수 있습니다.

## <a name="i-clicked-the-add-button-and-my-application-didnt-appear"></a>"추가" 단추를 클릭했고 애플리케이션이 나타나지 않았음
경우에 따라 일시적인 문제, 네트워킹 문제 또는 버그로 인해 애플리케이션 추가에 실패합니다. Azure Portal의 오른쪽 위에 있는 **알림** 아이콘(벨)을 클릭하고 **애플리케이션 추가** 알림 옆의 빨간색(!) 아이콘이 표시되는 경우 이러한 상황이 발생한다고 말할 수 있습니다. 이는 애플리케이션을 만들 때 오류가 있었음을 나타냅니다.

**추가** 단추를 클릭할 때 오류가 발생하는 경우 **오류** 상태에 **알림**이 표시됩니다. 더 자세히 알아보거나 지원 엔지니어와 공유하기 위해 오류에 대한 자세한 정보를 원하는 경우 [포털 알림의 세부 정보를 확인하는 방법](#how-to-see-the-details-of-a-portal-notification) 섹션의 단계를 따라 오류에 대한 자세한 정보를 볼 수 있습니다.

## <a name="i-dont-know-how-to-set-up-my-application-once-ive-added-it"></a>추가한 후 내 애플리케이션을 설정하는 방법을 모름
응용 프로그램에 대해 학습 하는 데 도움이 필요한 경우 [Azure Active Directory 문서와 SaaS 앱을 통합 하는 방법에 대 한 자습서 목록](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list) 에서 시작 하는 것이 좋습니다.

또한 [Azure AD 애플리케이션 문서 라이브러리](https://docs.microsoft.com/azure/active-directory/active-directory-apps-index)를 통해 Azure AD로 Single Sign-On 및 작동 방법에 대해 자세히 알아볼 수 있습니다.

## <a name="i-want-to-delete-an-application-but-the-delete-button-is-disabled"></a>응용 프로그램을 삭제 하려고 하지만 삭제 단추를 사용할 수 없습니다.

다음 시나리오에서는 삭제 단추를 사용할 수 없습니다.

- 엔터프라이즈 응용 프로그램의 응용 프로그램의 경우 전역 관리자, 클라우드 응용 프로그램 관리자, 응용 프로그램 관리자 또는 서비스 주체의 소유자 역할 중 하나가 없는 경우입니다.

- Microsoft 응용 프로그램의 경우 사용자의 역할에 관계 없이 UI에서 해당 응용 프로그램을 삭제할 수 없습니다.

- 관리 id에 해당 하는 servicePrincipals의 경우입니다. 관리 id 서비스 주체는 Enterprise apps 블레이드에서 삭제할 수 없습니다. Azure 리소스로 이동 하 여 관리 해야 합니다. [관리 id](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) 에 대 한 lear 추가 정보

## <a name="how-to-see-the-details-of-a-portal-notification"></a>포털 알림의 세부 정보를 확인하는 방법
다음 단계를 수행하여 포털 알림의 세부 정보를 확인할 수 있습니다.
1.  Azure Portal의 오른쪽 위에 있는 **알림** 아이콘 (벨)을 선택 합니다.
2.  **오류** 상태(옆에 빨간색(!)이 있는)에서 알림을 선택합니다.
    >[!NOTE]
    >**성공** 또는 **진행 중** 상태에서 알림을 클릭할 수 없습니다.
4.  **알림 세부 정보**에서 정보를 사용하여 문제에 대한 자세한 내용을 이해합니다.
5.  여전히 도움이 필요한 경우 문제에 대한 도움을 얻도록 이 정보를 지원 엔지니어 또는 제품 그룹과 공유할 수도 있습니다.
6.  **오류 복사** 텍스트 상자 오른쪽의 **복사 아이콘** 을 선택 하 여 지원 또는 제품 그룹 엔지니어와 공유 하도록 모든 알림 세부 정보를 복사 합니다.

## <a name="how-to-get-help-by-sending-notification-details-to-a-support-engineer"></a>지원 엔지니어에게 알림 세부 정보를 전송하여 도움을 얻는 방법
도움이 필요한 경우 **아래 나열 된 모든 세부 정보** 를 지원 엔지니어와 공유 하 여 신속 하 게 도움을 받을 수 있도록 하는 것이 중요 합니다. **스크린 샷을 이동** **하거나 복사 오류 텍스트 상자** 오른쪽에 있는 **복사 오류 아이콘**을 선택 합니다.

## <a name="notification-details-explained"></a>알림 세부 정보 설명
알림에 대한 자세한 내용은 다음 설명을 참조하세요.

### <a name="essential-notification-items"></a>중요 알림 항목
- **제목** - 알림의 설명이 포함된 제목
  * 예제- **응용 프로그램 프록시 설정**
- **설명** – 작업의 결과로 발생한 문제에 대한 설명
  -   예제 - **입력한 내부 url은 이미 다른 애플리케이션에서 사용 중입니다.**
- **알림 ID** - 알림의 고유 ID
  -   예제 – **clientNotification-2adbfc06-2073-4678-a69f-7eb78d96b068**
- **클라이언트 요청 ID** -브라우저에서 만든 특정 요청 ID
  -   예제 – **302fd775-3329-4670-a9f3-bea37004f0bc**
- **타임스탬프 UTC** – 알림이 발생한 동안의 타임스탬프(UTC)
  -   예제 – **2017-03-23T19:50:43.7583681Z**
- **내부 트랜잭션 id** – 시스템에서 오류를 조회 하는 데 사용할 수 있는 내부 id입니다.
  -   예제 – **71a2f329-ca29-402f-aa72-bc00a7aca603**
- **UPN** – 작업을 수행한 사용자
  -   예 – **tperkins \@ f128.info**
- **테넌트 ID** – 작업을 수행한 사용자가 구성원인 테넌트의 고유 ID
  -   예제 – **7918d4b5-0442-4a97-be2d-36f9f9962ece**
- **사용자 개체 ID** – 작업을 수행한 사용자의 고유 ID
  -   예제 – **17f84be4-51f8-483a-b533-383791227a99**

### <a name="detailed-notification-items"></a>자세한 알림 항목
-   **표시 이름** – **(비어 있을 수 있음)** 오류에 대한 보다 자세한 표시 이름
    -   예제- **응용 프로그램 프록시 설정**
-   **상태** - 알림의 특정 상태
    -   예 – **실패**
-   **개체 ID** – **(비어 있을 수 있음)** 작업이 수행된 개체 ID
    -   예제 – **8e08161d-f2fd-40ad-a34a-a9632d6bb599**
-   **세부 정보** – 작업의 결과로 발생한 문제에 대한 자세한 설명
    -   예제 – **내부 url `https://bing.com/` 은 이미 사용 중 이므로 유효 하지 않습니다** .
-   **오류 복사** – 복사 **오류** 텍스트 상자 오른쪽의 **복사 아이콘** 을 선택 하 여 지원 또는 제품 그룹과 공유할 모든 알림 세부 정보를 복사 합니다. 
-   엔지니어
    -   예제 ```{"errorCode":"InternalUrl\_Duplicate","localizedErrorDetails":{"errorDetail":"Internal url 'https://google.com/' is invalid since it is already in use"},"operationResults":\[{"objectId":null,"displayName":null,"status":0,"details":"Internal url 'https://bing.com/' is invalid since it is already in use"}\],"timeStampUtc":"2017-03-23T19:50:26.465743Z","clientRequestId":"302fd775-3329-4670-a9f3-bea37004f0bb","internalTransactionId":"ea5b5475-03b9-4f08-8e95-bbb11289ab65","upn":"tperkins@f128.info","tenantId":"7918d4b5-0442-4a97-be2d-36f9f9962ece","userObjectId":"17f84be4-51f8-483a-b533-383791227a99"}```
