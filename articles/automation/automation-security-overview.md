---
title: Azure Automation 계정 인증 개요
description: 이 문서에서는 Azure Automation 계정 인증의 개요를 제공합니다.
keywords: 자동화 보안, 안전한 자동화, 자동화 인증
services: automation
ms.subservice: process-automation
ms.date: 09/28/2020
ms.topic: conceptual
ms.openlocfilehash: bcb5f61c93bd4c3ff7c0f81ae808807f7deb71df
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91766094"
---
# <a name="automation-account-authentication-overview"></a>Automation 계정 인증 개요

Azure Automation을 사용하여 Azure, 온-프레미스 및 AWS(Amazon 웹 서비스)와 같은 기타 클라우드 공급자의 리소스에 대해 작업을 자동화할 수 있습니다. Runbook을 사용 하 여 작업을 자동화 하거나, 비즈니스 또는 운영 프로세스가 Azure 외부에서 관리 되는 경우 Hybrid Runbook Worker 수 있습니다. 이러한 환경 중 하나에서 작업 하려면 필요한 최소한의 권한으로 리소스에 안전 하 게 액세스할 수 있는 권한이 필요 합니다.

이 문서에서는 Azure Automation에서 지원하는 인증 시나리오를 살펴보고 관리해야 하는 환경에 따라 시작하는 방법을 설명합니다.

## <a name="automation-account"></a>Automation 계정

Azure Automation을 처음 시작하려면 Automation 계정을 하나 이상 만들어야 합니다. Automation 계정을 사용하면 다른 Automation 계정의 리소스에서 사용자의 Automation 리소스, Runbook, 자산, 구성을 격리할 수 있습니다. Automation 계정을 사용하여 리소스를 별도의 논리적 환경으로 구분할 수 있습니다. 예를 들어 개발, 프로덕션, 온-프레미스 환경에 서로 다른 계정을 사용할 수 있습니다. Azure Automation 계정은 Microsoft 계정 또는 Azure 구독에서 만든 계정과 다릅니다. Automation 계정 만들기에 대한 소개는 [Automation 계정 만들기](automation-quickstart-create-account.md)를 참조하세요.

## <a name="automation-resources"></a>자동화 리소스

각 Automation 계정의 Automation 리소스는 단일 Azure 지역과 연결되지만 계정은 Azure 구독 내 모든 리소스를 관리할 수 있습니다. 여러 지역에서 Automation 계정을 만드는 주된 이유는 데이터 및 리소스를 특정 지역으로 격리해야 하는 정책이 있을 수 있기 때문입니다.

Azure Automation에서 Azure Resource Manager 및 PowerShell cmdlet을 사용하여 리소스에 대해 만드는 모든 작업은 Azure AD(Azure Active Directory) 조직 ID 자격 증명 기반 인증을 사용하여 Azure에 인증해야 합니다.

## <a name="run-as-accounts"></a>실행 계정

Azure Automation의 실행 계정은 클래식 배포 모델에 배포 된 Azure Resource Manager 리소스 또는 리소스를 관리 하기 위한 인증을 제공 합니다. Azure Automation에는 두 가지 유형의 실행 계정이 있습니다.

* Azure 실행 계정
* Azure 클래식 실행 계정

이러한 두 배포 모델에 대해 자세히 알아보려면 [리소스 관리자 및 클래식 배포](../azure-resource-manager/management/deployment-models.md)를 참조 하세요.

>[!NOTE]
>Azure CSP(클라우드 솔루션 공급자) 구독은 Azure Resource Manager 모델만 지원합니다. Azure Resource Manager 기반이 아닌 서비스는 프로그램에서 사용할 수 없습니다. CSP 구독을 사용하는 경우 Azure 클래식 실행 계정이 생성되지 않지만 Azure 실행 계정은 생성됩니다. CSP 구독에 대해 자세히 알아보려면 [CSP 구독에서 사용 가능한 서비스](/azure/cloud-solution-provider/overview/azure-csp-available-services)를 참조하세요.

### <a name="run-as-account"></a>실행 계정

Azure 실행 계정은 azure에 대 한 Azure Resource Manager 배포 및 관리 서비스를 기반으로 Azure 리소스를 관리 합니다.

실행 계정을 만들 때 다음 작업을 수행 합니다.

* 자체 서명 된 인증서를 사용 하 여 Azure AD 응용 프로그램을 만들고, Azure AD에서 응용 프로그램에 대 한 서비스 주체 계정을 만들며, 현재 구독의 계정에 대 한 [참가자](../role-based-access-control/built-in-roles.md#contributor) 역할을 할당 합니다. 인증서 설정을 소유자 또는 다른 어떤 역할로든 변경할 수 있습니다. 자세한 내용은 [Azure Automation의 역할 기반 액세스 제어](automation-role-based-access-control.md)를 참조하세요.

* 지정된 Automation 계정에서 `AzureRunAsCertificate`라는 Automation 인증서 자산을 만듭니다. 인증서 자산은 Azure AD 애플리케이션에서 사용하는 인증서 프라이빗 키를 보유합니다.

* 지정된 Automation 계정에서 `AzureRunAsConnection`이라는 Automation 연결 자산을 만듭니다. 이 연결 자산은 애플리케이션 ID, 테넌트 ID, 구독 ID 및 인증서 지문을 보유합니다.

### <a name="azure-classic-run-as-account"></a>Azure 클래식 실행 계정

Azure 클래식 실행 계정은 클래식 배포 모델을 기반으로 Azure 클래식 리소스를 관리 합니다. 이 유형의 실행 계정을 만들거나 갱신 하려면 구독에 대 한 공동 관리자 여야 합니다.

Azure 클래식 실행 계정을 만들 때 다음 작업을 수행 합니다.

* 구독에 관리 인증서를 만듭니다.

* 지정된 Automation 계정에서 `AzureClassicRunAsCertificate`라는 Automation 인증서 자산을 만듭니다. 인증서 자산은 관리 인증서에서 사용되는 인증서 프라이빗 키를 보유합니다.

* 지정된 Automation 계정에서 `AzureClassicRunAsConnection`이라는 Automation 연결 자산을 만듭니다. 연결 자산은 구독 이름, 구독 ID 및 인증서 자산 이름을 보유합니다.

>[!NOTE]
>Azure 클래식 실행 계정은 기본적으로 Automation 계정을 만들 때 동시에 생성되지 않습니다. 이 계정은 [실행 계정 관리](manage-runas-account.md#create-a-run-as-account-in-azure-portal) 문서에 설명 된 단계에 따라 개별적으로 만들어집니다.

## <a name="service-principal-for-run-as-account"></a>실행 계정에 대한 서비스 주체

실행 계정의 서비스 주체에는 기본적으로 Azure AD를 읽을 권한이 없습니다. Azure AD를 읽거나 관리하는 권한을 추가하려면 **API 권한**에서 서비스 주체에 대한 권한을 부여해야 합니다. 자세한 내용은 [웹 API에 액세스할 수 있는 권한 추가](../active-directory/develop/quickstart-configure-app-access-web-apis.md#add-permissions-to-access-your-web-api)를 참조 하세요.

## <a name="role-based-access-control"></a>역할 기반 액세스 제어

Azure Resource Manager에서 역할 기반 액세스 제어를 사용하여 Azure AD 사용자 계정 및 실행 계정에 허용된 작업을 수락하고 서비스 주체를 인증할 수 있습니다. Automation 사용 권한 관리 모델을 개발하는 방법에 대한 자세한 내용은 [Azure Automation에서 역할 기반 액세스 제어 문서](automation-role-based-access-control.md)를 참조하세요.

## <a name="runbook-authentication-with-hybrid-runbook-worker"></a>Hybrid Runbook Worker를 사용하여 Runbook 인증

데이터 센터의 Hybrid Runbook Worker에서 또는 AWS와 같은 기타 클라우드 환경의 컴퓨팅 서비스에서 실행되는 Runbook은 일반적으로 Azure 리소스를 인증하는 Runbook에 사용되는 것과 동일한 방법을 사용할 수 없습니다. 이러한 리소스는 Azure 외부에서 실행되므로 로컬로 액세스하는 리소스에 인증하려면 Automation에서 정의한 자체 보안 자격 증명이 필요하기 때문입니다. Runbook Workers를 사용하는 Runbook 인증에 대한 자세한 내용은 [Hybrid Runbook Worker에서 Runbook 실행](automation-hrw-run-runbooks.md)을 참조하세요.

Azure VM에서 Hybrid Runbook Worker를 사용하는 Runbook의 경우 실행 계정 대신 [관리형 ID로 Runbook 인증](automation-hrw-run-runbooks.md#runbook-auth-managed-identities)을 사용하여 Azure 리소스를 인증할 수 있습니다.

## <a name="next-steps"></a>다음 단계

* Azure Portal에서 Automation 계정을 만들려면 [독립 실행형 Azure Automation 계정 만들기](automation-create-standalone-account.md)를 참조하세요.
* 템플릿을 사용하여 계정을 만들려면 [Azure Resource Manager 템플릿을 사용하여 Automation 계정 만들기](quickstart-create-automation-account-template.md)를 참조하세요.
* Amazon Web Services를 사용하는 인증의 경우 [Amazon Web Services를 사용하여 Runbook 인증](automation-config-aws-account.md)을 참조하세요.