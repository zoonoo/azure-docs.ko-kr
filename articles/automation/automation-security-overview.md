---
title: Azure Automation 계정 인증 개요
description: 이 문서에서는 Azure Automation 계정 인증의 개요를 제공합니다.
keywords: 자동화 보안, 안전한 자동화, 자동화 인증
services: automation
ms.subservice: process-automation
ms.date: 04/23/2020
ms.topic: conceptual
ms.openlocfilehash: d2b9f705c73e667f34e46fdeed3c80af1e65fb12
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.contentlocale: ko-KR
ms.lasthandoff: 05/25/2020
ms.locfileid: "83830109"
---
# <a name="automation-account-authentication-overview"></a>Automation 계정 인증 개요

Azure Automation을 사용하여 Azure, 온-프레미스 및 AWS(Amazon 웹 서비스)와 같은 기타 클라우드 공급자의 리소스에 대해 작업을 자동화할 수 있습니다. Runbook을 사용하여 작업을 자동화하거나, 관리할 비Azure 작업이 있는 경우 Hybrid Runbook Worker를 사용할 수 있습니다. 어느 환경이든 Azure 구독에 필요한 최소 권한으로 리소스에 안전하게 액세스할 수 있는 권한이 필요합니다.

이 문서에서는 Azure Automation에서 지원하는 인증 시나리오를 살펴보고 관리해야 하는 환경에 따라 시작하는 방법을 설명합니다.

## <a name="automation-account"></a>Automation 계정 

Azure Automation을 처음 시작하려면 Automation 계정을 하나 이상 만들어야 합니다. Automation 계정을 사용하면 다른 Automation 계정의 리소스에서 사용자의 Automation 리소스, Runbook, 자산, 구성을 격리할 수 있습니다. Automation 계정을 사용하여 리소스를 별도의 논리적 환경으로 구분할 수 있습니다. 예를 들어 개발, 프로덕션, 온-프레미스 환경에 서로 다른 계정을 사용할 수 있습니다. Azure Automation 계정은 Microsoft 계정 또는 Azure 구독에서 만든 계정과 다릅니다. Automation 계정 만들기에 대한 소개는 [Automation 계정 만들기](automation-quickstart-create-account.md)를 참조하세요.

## <a name="automation-resources"></a>자동화 리소스

각 Automation 계정의 Automation 리소스는 단일 Azure 지역과 연결되지만 계정은 Azure 구독 내 모든 리소스를 관리할 수 있습니다. 여러 지역에서 Automation 계정을 만드는 주된 이유는 데이터 및 리소스를 특정 지역으로 격리해야 하는 정책이 있을 수 있기 때문입니다.

Azure Automation에서 Azure Resource Manager 및 PowerShell cmdlet을 사용하여 리소스에 대해 만드는 모든 작업은 Azure AD(Azure Active Directory) 조직 ID 자격 증명 기반 인증을 사용하여 Azure에 인증해야 합니다. 

## <a name="run-as-account"></a>실행 계정

Azure Automation의 실행 계정은 PowerShell cmdlet을 사용하여 Azure 리소스를 관리하는 데 필요한 인증을 제공합니다. 실행 계정을 만들면 Azure AD에 새로운 서비스 주체가 생성되며 구독 수준에서 이 사용자에게 기여자 역할을 할당합니다. Azure VM에서 Hybrid Runbook Worker를 사용하는 Runbook의 경우 실행 계정 대신 [관리형 ID로 Runbook 인증](automation-hrw-run-runbooks.md#runbook-auth-managed-identities)을 사용하여 Azure 리소스를 인증할 수 있습니다.

## <a name="service-principal-for-run-as-account"></a>실행 계정에 대한 서비스 주체

실행 계정의 서비스 주체에는 기본적으로 Azure AD를 읽을 권한이 없습니다. Azure AD를 읽거나 관리하는 권한을 추가하려면 **API 권한**에서 서비스 주체에 대한 권한을 부여해야 합니다. 자세히 알아보려면 [웹 API 액세스 권한 추가](../active-directory/develop/quickstart-configure-app-access-web-apis.md#add-permissions-to-access-web-apis)를 참조하세요.

## <a name="role-based-access-control"></a>역할 기반 액세스 제어

Azure Resource Manager에서 역할 기반 액세스 제어를 사용하여 Azure AD 사용자 계정 및 실행 계정에 허용된 작업을 수락하고 서비스 주체를 인증할 수 있습니다. Automation 사용 권한 관리 모델을 개발하는 방법에 대한 자세한 내용은 [Azure Automation에서 역할 기반 액세스 제어 문서](automation-role-based-access-control.md)를 참조하세요.  

## <a name="runbook-authentication-with-hybrid-runbook-worker"></a>Hybrid Runbook Worker를 사용하여 Runbook 인증 

데이터 센터의 Hybrid Runbook Worker에서 또는 AWS와 같은 기타 클라우드 환경의 컴퓨팅 서비스에서 실행되는 Runbook은 일반적으로 Azure 리소스를 인증하는 Runbook에 사용되는 것과 동일한 방법을 사용할 수 없습니다. 이러한 리소스는 Azure 외부에서 실행되므로 로컬로 액세스하는 리소스에 인증하려면 Automation에서 정의한 자체 보안 자격 증명이 필요하기 때문입니다. Runbook Workers를 사용하는 Runbook 인증에 대한 자세한 내용은 [Hybrid Runbook Worker에서 Runbook 실행](automation-hrw-run-runbooks.md)을 참조하세요. 

## <a name="next-steps"></a>다음 단계

* Azure Portal에서 Automation 계정을 만들려면 [독립 실행형 Azure Automation 계정 만들기](automation-create-standalone-account.md)를 참조하세요.
* 템플릿을 사용하여 계정을 만들려면 [Azure Resource Manager 템플릿을 사용하여 Automation 계정 만들기](automation-create-account-template.md)를 참조하세요.
* Amazon Web Services를 사용하는 인증의 경우 [Amazon Web Services를 사용하여 Runbook 인증](automation-config-aws-account.md)을 참조하세요.