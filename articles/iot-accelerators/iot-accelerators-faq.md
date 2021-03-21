---
title: Azure IoT 솔루션 가속기 FAQ - Azure | Microsoft Docs
description: 이 문서에서는 IoT 솔루션 가속기에 대 한 자주 묻는 질문에 답변 합니다. GitHub 리포지토리에 대 한 링크를 포함 합니다.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 02/15/2018
ms.author: dobett
ms.openlocfilehash: 1fd2b8461bd66c826dc4890c331b740c4703f896
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96903992"
---
# <a name="frequently-asked-questions-for-iot-solution-accelerators"></a>IoT 솔루션 가속기에 대한 질문과 대답

[연결 된 팩터리 관련 FAQ](iot-accelerators-faq-cf.md)도 참조 하세요.

### <a name="where-can-i-find-the-source-code-for-the-solution-accelerators"></a>어디에서 솔루션 가속기에 대한 소스 코드를 찾을 수 있나요?

소스 코드는 다음 GitHub 리포지토리에 저장됩니다.

* [연결된 팩터리 솔루션 가속기](https://github.com/Azure/azure-iot-connected-factory)
* [장치 시뮬레이션 솔루션 가속기](https://github.com/Azure/device-simulation-dotnet)

### <a name="where-can-i-find-the-remote-monitoring-and-predictive-maintenance-solution-accelerators"></a>원격 모니터링 및 예측 유지 관리 솔루션 가속기는 어디서 찾을 수 있나요?

2020 년 12 월 10 일까 지 [Azure IoT 솔루션 가속기](https://www.azureiotsolutions.com/Accelerators) 사이트에서 원격 모니터링 및 예측 유지 관리 가속기가 제거 되었으며 새 배포에 더 이상 사용할 수 없습니다. 두 액셀러레이터에 대 한 GitHub 리포지토리가 보관 되었습니다. 모든 사용자가 코드를 계속 사용할 수 있지만 리포지토리가 새로운 기여를 취하지 않습니다.

### <a name="what-happens-to-my-existing-remote-monitoring-and-predictive-maintenance-deployments"></a>기존 원격 모니터링 및 예측 유지 관리 배포는 어떻게 되나요?

기존 배포는 원격 모니터링 및 예측 유지 관리 솔루션 가속기 제거의 영향을 받지 않으며 계속 작동 합니다. 분기 리포지토리는 영향을 받지 않습니다. GitHub의 마스터 리포지토리가 보관 되었습니다.

### <a name="how-do-i-deploy-device-simulation-solution-accelerator"></a>장치 시뮬레이션 솔루션 가속기를 배포 어떻게 할까요??

장치 시뮬레이션 솔루션 가속기를 배포 하려면 [장치 시뮬레이션](https://github.com/Azure/device-simulation-dotnet/blob/master/README.md) GitHub 리포지토리를 참조 하세요.

### <a name="where-can-i-find-information-about-the-removed-solution-accelerators"></a>제거 된 솔루션 가속기에 대 한 정보는 어디서 찾을 수 있나요?

이전 버전 사이트에서 다음 페이지를 참조 하세요.

* [원격 모니터링](/previous-versions/azure/iot-accelerators/about-iot-accelerators)
* [예측 유지 관리](/previous-versions/azure/iot-accelerators/about-iot-accelerators)

### <a name="what-sdks-can-i-use-to-develop-device-clients-for-the-solution-accelerators"></a>솔루션 가속기에 대한 디바이스 클라이언트를 개발하는 데 어떤 SDK를 사용할 수 있나요?

[Microsoft Azure IoT SDK](https://github.com/Azure/azure-iot-sdks) GitHub 리포지토리에서 다른 언어(C, .NET, Java, Node.js, Python) IoT 디바이스 SDK에 대한 링크를 확인할 수 있습니다.

DevKit 디바이스를 사용하는 경우 [IoT DevKit SDK](https://github.com/Microsoft/devkit-sdk) GitHub 리포지토리에서 리소스 및 샘플을 확인할 수 있습니다.

### <a name="im-a-service-administrator-and-id-like-to-change-the-directory-mapping-between-my-subscription-and-a-specific-azure-ad-tenant-how-do-i-complete-this-task"></a>서비스 관리자가 구독과 특정 Azure AD 테넌트 간의 디렉터리 매핑을 변경하고자 합니다. 이 태스크를 완료하려면 어떻게 해야 하나요?

[Azure AD 디렉터리에 기존 구독을 추가하는 방법](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md#to-associate-an-existing-subscription-to-your-azure-ad-directory)을 참조하세요.

### <a name="i-want-to-change-a-service-administrator-or-co-administrator-when-logged-in-with-an-organizational-account"></a>조직 계정으로 로그인 할 때 서비스 관리자 또는 공동 관리자를 변경하려고 합니다.

지원 문서 [조직 계정으로 로그인하였을 때 서비스 관리자 및 공동 관리자 변경](https://azure.microsoft.com/support/changing-service-admin-and-co-admin)을 참조하세요.

### <a name="why-am-i-seeing-this-error-your-account-does-not-have-the-proper-permissions-to-create-a-solution-please-check-with-your-account-administrator-or-try-with-a-different-account"></a>왜 이 오류가 표시되나요? "Your account does not have the proper permissions to create a solution.(사용자의 계정에 솔루션을 생성하기에 적합한 권한이 없습니다.) Please check with your account administrator or try with a different account.(계정 관리자에게 문의하거나 다른 계정으로 시도하세요.)"

지침은 다음 다이어그램을 살펴보겠습니다.

![사용 권한 순서도](media/iot-accelerators-faq/flowchart.png)

> [!NOTE]
> Azure AD 테넌트의 글로벌 관리자이고 구독의 공동 관리자임을 확인한 후에 오류가 계속 표시되는 경우 계정 관리자를 통해 사용자를 제거하고 다음과 같은 순서로 필요한 사용 권한을 다시 할당합니다. 먼저 사용자를 전역 관리자로 추가한 다음, Azure 구독의 공동 관리자로 추가합니다. 문제가 지속되면 [도움말 및 지원](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade)에 문의하세요.

### <a name="why-am-i-seeing-this-error-when-i-have-an-azure-subscription-an-azure-subscription-is-required-to-create-pre-configured-solutions-you-can-create-a-free-trial-account-in-just-a-couple-of-minutes"></a>Azure 구독이 있는데 왜 이런 오류가 표시되나요? "Azure 구독은 미리 구성된 솔루션을 만드는 데 필요합니다. 몇 분 만에 무료 평가판 계정을 만들 수 있습니다."

Azure 구독이 있는 것이 확실하다면, 구독에 대한 테넌트 매핑의 유효성을 검사하고 드롭다운에 올바른 테넌트가 선택되어 있는지 확인하세요. 테넌트가 맞는다는 것을 확인했으면, 이전의 다이어그램에 따라서 구독과 이 Azure AD 테넌트 매핑의 유효성을 검사합니다.

### <a name="whats-the-difference-between-deleting-a-resource-group-in-the-azure-portal-and-clicking-delete-on-a-solution-accelerator-in-azureiotsolutionscom"></a>Azure Portal에서 리소스 그룹을 삭제 하 고 azureiotsolutions.com의 솔루션 가속기에서 삭제를 클릭 하는 경우의 차이점은 무엇 인가요?

* [Azureiotsolutions.com](https://www.azureiotsolutions.com/)에서 솔루션 가속기를 삭제 하는 경우 솔루션 가속기를 만들 때 배포 된 모든 리소스를 삭제 합니다. 리소스 그룹에 리소스를 추가하면 이들 역시 삭제됩니다.
* [Azure Portal](https://portal.azure.com)에서 리소스 그룹을 삭제하는 경우 해당 리소스 그룹의 리소스만 삭제됩니다. 또한 솔루션 가속기와 연결된 Azure Active Directory 애플리케이션을 삭제해야 합니다.

### <a name="can-i-continue-to-leverage-my-existing-investments-in-azure-iot-solution-accelerators"></a>Azure IoT 솔루션 가속기에 대한 기존 투자를 계속 활용할 수 있나요?

예. 현재 존재하는 모든 솔루션은 Azure 구독에서 계속 작동하며 소스 코드는 GitHub에서 계속 사용할 수 있습니다.

### <a name="how-many-iot-hub-instances-can-i-provision-in-a-subscription"></a>하나의 구독에 프로비전할 수 있는 IoT Hub 인스턴스는 몇 개인가요?

기본적으로 [구독당 10개의 IoT Hub](../azure-resource-manager/management/azure-subscription-service-limits.md#iot-hub-limits)를 프로비전할 수 있습니다. [Azure 지원 티켓](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade)을 만들어 이 제한을 높일 수 있습니다. 결과적으로 모든 솔루션 가속기가 새 IoT Hub를 프로비전하기 때문에 지정된 구독에서 최대 10개의 솔루션 가속기만 프로비전할 수 있습니다.

### <a name="how-many-azure-cosmos-db-instances-can-i-provision-in-a-subscription"></a>하나의 구독에 프로비전할 수 있는 Azure Cosmos DB 인스턴스는 몇 개인가요?

50개입니다. [Azure 지원 티켓](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade)을 만들어서 이 한도를 높일 수 있지만, 기본적으로 구독당 Cosmos DB 인스턴스를 50개만 프로비전할 수 있습니다.

### <a name="can-i-create-a-solution-accelerator-if-i-have-microsoft-azure-for-dreamspark"></a>DreamSpark용 Microsoft Azure가 있는 경우 솔루션 가속기를 만들 수 있나요?

> [!NOTE]
> Microsoft Azure for DreamSpark는 현재 학생용 Microsoft Imagine이라고 합니다.

현재는 [DreamSpark용 Microsoft Azure](https://azure.microsoft.com/pricing/member-offers/imagine/) 계정으로 솔루션 가속기를 만들 수 없습니다. 하지만 몇 분 이내에 [Azure용 무료 평가판 계정](https://azure.microsoft.com/free/)을 만들면 솔루션 가속기를 만들 수 있습니다.

### <a name="how-do-i-delete-an-azure-ad-tenant"></a>Azure AD 테넌트를 삭제하려면 어떻게 하나요?

Eric Golpe의 블로그 게시물 [Azure AD 테넌트 삭제 연습(영문)](/archive/blogs/ericgolpe/walkthrough-of-deleting-an-azure-ad-tenant)을 참조하세요.

### <a name="next-steps"></a>다음 단계

IoT 솔루션 가속기의 몇 가지 다른 기능을 탐색할 수도 있습니다.

* [연결된 팩터리 솔루션 가속기 배포](quickstart-connected-factory-deploy.md)
* [처음부터 IoT 보안을 고려](../iot-fundamentals/iot-security-ground-up.md)