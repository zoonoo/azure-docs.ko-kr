---
title: Azure Automation의 인증 소개
description: 이 문서는 Automation 보안의 개요와 Azure Automation의 Automation 계정에 사용 가능한 다양한 인증 방법에 대해 설명합니다.
keywords: 자동화 보안, 안전한 자동화, 자동화 인증
services: automation
ms.subservice: process-automation
ms.date: 04/23/2020
ms.topic: conceptual
ms.openlocfilehash: 109bb6dd29ea9c4239e0abcfc668f1185f7e9783
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "82114533"
---
# <a name="introduction-to-authentication-in-azure-automation"></a>Azure Automation의 인증 소개

Azure Automation을 사용하여 Azure, 온-프레미스 및 AWS(Amazon 웹 서비스)와 같은 기타 클라우드 공급자의 리소스에 대해 작업을 자동화할 수 있습니다. Runbook이 필요한 작업을 수행하려면 구독 내에서 최소의 권한으로 리소스에 안전하게 액세스할 수 있는 권한이 있어야 합니다.

이 문서에서는 Azure Automation에서 지 원하는 다양 한 인증 시나리오와 관리 해야 하는 환경 또는 환경을 기반으로 시작 하는 방법을 설명 합니다.  

## <a name="automation-account-overview"></a>Automation 계정 개요

Azure Automation을 처음 시작하려면 Automation 계정을 하나 이상 만들어야 합니다. Automation 계정을 사용하면 다른 Automation 계정에 포함된 리소스에서 사용자의 Automation 리소스(Runbook, 자산, 구성)를 격리할 수 있습니다. Automation 계정을 사용하여 리소스를 별도의 논리적 환경으로 구분할 수 있습니다. 예를 들어 개발, 프로덕션, 온-프레미스 환경에 서로 다른 계정을 사용할 수 있습니다. Azure Automation 계정은 Microsoft 계정 또는 Azure 구독에서 만든 계정과 다릅니다.

각 Automation 계정의 Automation 리소스는 단일 Azure 지역과 연결되지만 Automation 계정은 구독 내 모든 리소스를 관리할 수 있습니다. 여러 지역에서 Automation 계정을 만드는 주된 이유는 데이터 및 리소스를 특정 지역으로 격리해야 하는 정책이 있을 수 있기 때문입니다.

Azure Automation에서 Azure Resource Manager 및 Azure cmdlet을 사용하여 리소스에 대해 수행하는 모든 작업은 Azure Active Directory 조직 ID 자격 증명 기반 인증을 사용하여 Azure에 인증해야 합니다. Azure cmdlet을 사용 하 여 Azure에서 리소스를 관리 하는 인증을 제공 하 Azure Automation의 실행 계정. 실행 계정을 만들면 AD (Azure Active Directory)에 새 서비스 주체 사용자가 만들어지고 구독 수준에서이 사용자에 게 참가자 역할이 할당 됩니다. Azure 가상 머신에서 Hybrid Runbook Worker를 사용하는 Runbook의 경우 실행 계정 대신 [Azure 리소스에 대한 관리 ID](automation-hrw-run-runbooks.md#managed-identities-for-azure-resources)를 사용하여 Azure 리소스를 인증할 수 있습니다.

실행 계정에 대 한 서비스 주체에는 기본적으로 Azure AD를 읽을 수 있는 권한이 없습니다. Azure AD를 읽거나 관리 하는 권한을 추가 하려면 **API 권한**에서 서비스 주체에 대 한 사용 권한을 부여 해야 합니다. 자세한 내용은 [웹 api에 액세스 하기 위한 권한 추가](../active-directory/develop/quickstart-configure-app-access-web-apis.md#add-permissions-to-access-web-apis)를 참조 하세요.

Azure Resource Manager에서 역할 기반 액세스 제어를 사용하여 Azure AD 사용자 계정 및 실행 계정에 허용된 작업을 수락하고 서비스 주체를 인증할 수 있습니다. Automation 사용 권한 관리 모델을 개발하는 방법에 대한 자세한 내용은 [Azure Automation에서 역할 기반 액세스 제어 문서](automation-role-based-access-control.md)를 참조하세요.  

데이터 센터의 Hybrid Runbook Worker에서 실행 되는 runbook 또는 AWS 같은 다른 클라우드 환경에서의 컴퓨팅 서비스에 대해 실행 되는 runbook은 일반적으로 Azure 리소스를 인증 하는 runbook에 사용 되는 것과 동일한 방법을 사용할 수 없습니다. 이러한 리소스는 Azure 외부에서 실행되므로 로컬로 액세스하는 리소스에 인증하려면 Automation에서 정의한 자체 보안 자격 증명이 필요하기 때문입니다. Runbook worker를 사용 하는 runbook 인증에 대 한 자세한 내용은 [Hybrid Runbook worker에 대 한 Runbook 인증](automation-hrw-run-runbooks.md)을 참조 하세요. 

## <a name="next-steps"></a>다음 단계

* [Azure Portal에서 Automation 계정을 만듭니다](automation-create-standalone-account.md).

* [Azure Resource Manager 템플릿을 사용 하 여 Automation 계정을 만듭니다](automation-create-account-template.md).

* [Amazon Web Services를 사용 하 여 인증 합니다 (AWS)](automation-config-aws-account.md).
