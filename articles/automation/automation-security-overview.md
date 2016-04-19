<properties
   pageTitle="Azure 자동화 보안"
   description="이 문서에서는 보안 개요 및 Azure 자동화의 자동화 계정에 사용 가능한 다양한 인증 방법에 대해 설명합니다."
   services="automation"
   documentationCenter=""
   authors="MGoedtel"
   manager="jwhit"
   editor="tysonn" />
<tags
   ms.service="automation"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="04/08/2016"
   ms.author="magoedte" />

# Azure 자동화 보안
Azure 자동화를 사용하여 Azure, 온-프레미스 및 AWS(Amazon 웹 서비스)와 같은 기타 클라우드 공급자의 리소스에 대해 작업을 자동화할 수 있습니다. Runbook이 필요한 작업을 수행하려면 안전하게 작동하고 구독 내에서 최소의 권한만 있는 리소스에 액세스할 수 있는 권한이 Runbook에 있어야 합니다. 이 문서에서는 Azure 자동화에서 지원하는 다양한 보안 시나리오를 살펴보고 관리해야 하는 환경에 따라 시작하는 방법을 보여 드리겠습니다.

## 자동화 계정 개요
Azure 자동화를 처음 시작하려면 자동화 계정을 하나 이상 만들어야 합니다. 자동화 계정을 사용하면 다른 자동화 계정에 포함된 리소스에서 사용자의 자동화 리소스(Runbook, 자산, 구성)를 격리할 수 있습니다. 자동화 계정을 사용하여 리소스를 별도의 논리적 환경으로 구분할 수 있습니다. 예를 들어 개발, 프로덕션, 온-프레미스 환경에 서로 다른 계정을 사용할 수 있습니다. Azure 자동화 계정은 Microsoft 계정 또는 Azure 구독에서 만든 계정과 다릅니다.

각 자동화 계정의 자동화 리소스는 단일 Azure 지역과 연결되지만 자동화 계정에서 모든 지역의 리소스를 관리할 수 있습니다. 여러 지역에서 자동화 계정을 만드는 주된 이유는 데이터 및 리소스를 특정 지역으로 격리해야 하는 정책이 있을 수 있기 때문입니다.

>[AZURE.NOTE]Azure 포털에서 작성된 자동화 계정 및 자동화 계정이 포함하는 리소스는 Azure 클래식 포털에서 액세스할 수 없습니다. 이러한 계정 또는 해당 리소스를 Windows PowerShell을 사용하여 관리하려는 경우 Azure 리소스 관리자 모듈을 사용해야 합니다.

Azure 자동화에서 ARM(Azure Resource Manager) 및 Azure cmdlet을 사용하여 리소스에 대해 수행하는 모든 작업은 Azure Active Directory 조직 ID 자격 증명 기반 인증을 사용하여 Azure에 인증해야 합니다. 원래는 ASM(Azure 서비스 관리) 모드에서 인증서 기반 인증을 사용했지만 설치 방법이 복잡합니다. Azure AD 사용자로 Azure에 인증하는 방법은 2014년에 인증 계정 구성을 간소화하는 한편 ASM 및 ARM 모드와 연동하는 단일 사용자 계정으로 Azure에 비 대화식으로 인증하는 기능을 지원하기 위해 도입되었습니다.

최근에 또 다른 업데이트가 발표되었습니다. 이제는 자동화 계정을 만들면 Azure AD 서비스 사용자 개체가 자동으로 생성됩니다. 이를 Azure 실행 계정이라고 하며 Azure Resource Manager의 Runbook 자동화에 사용되는 기본 인증 방법입니다.

ARM 모드에서 역할 기반 액세스 제어를 사용하여 Azure AD 사용자 계정 및 서비스 주체에게 허용된 작업을 수락하고 서비스 주체를 인증할 수 있습니다. 자동화 권한 관리 모델을 개발하는 방법에 대한 자세한 내용은 [Azure 자동화의 역할 기반 액세스 제어](../automation/automation-role-based-access-control.md) 문서를 참조하세요.

데이터 센터의 Hybrid Runbook Worker에서 실행되거나 AWS의 계산 서비스에 대해 실행되는 Runbook은 일반적으로 Azure 리소스를 인증하는 Runbook에 사용되는 것과 동일한 방법을 사용할 수 없습니다. 이러한 리소스가 Azure 외부에서 실행되며, 따라서 로컬로 액세스하는 리소스에 인증하려면 자동화에서 정의한 고유의 보안 자격 증명이 필요하기 때문입니다.

## 인증 방법

다음 테이블에는 Azure 자동화에서 지원하는 각 환경에 대한 다양한 인증 방법과 Runbook에 대한 인증 설정 방법을 설명하는 문서가 요약되어 있습니다.

메서드 | Environment | 문서
----------|----------|----------
Azure AD 사용자 계정 만들기 | Azure 리소스 관리자 및 Azure 서비스 관리 | [Azure AD 사용자 계정으로 Runbook 인증](../automation/automation-sec-configure-aduser-account.md)
Azure AD 서비스 사용자 개체 | Azure 리소스 관리자 | [Azure 실행 계정으로 Runbook 인증](../automation/automation-sec-configure-azure-runas-account.md)
Windows 인증 | 온-프레미스 데이터 센터 | [Hybrid Runbook Worker용 Runbook 인증](../automation/automation-hybrid-runbook-worker.md)
AWS 자격 증명 | Amazon 웹 서비스 | [AWS(Amazon 웹 서비스)로 Runbook 인증](../automation/automation-sec-configure-aws-account.md)

<!---HONumber=AcomDC_0413_2016-->