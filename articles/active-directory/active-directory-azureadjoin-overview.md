---
title: Azure Active Directory 조인을 통해 클라우드 기능을 Windows 10 장치로 확장 | Microsoft Docs
description: Windows 10 장치가 Azure AD 조인을 활용하여 Azure Active Directory에 등록할 수 있는 방법의 자세한 개요를 제공합니다.
services: active-directory
documentationcenter: ''
author: femila
manager: swadhwa
editor: ''
tags: azure-classic-portal

ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/27/2016
ms.author: femila

---
# <a name="extending-cloud-capabilities-to-windows-10-devices-through-azure-active-directory-join"></a>Azure Active Directory 조인을 통해 클라우드 기능을 Windows 10 장치로 확장
## <a name="what-is-azure-active-directory-join?"></a>Azure Active Directory 조인이란?
Azure AD 조인(Azure Active Directory 조인)은 Azure Active Directory에 회사 소유의 장치를 등록하여 장치의 중앙 관리를 가능하게 하는 기능입니다. 이 기능으로 직원 및 학생과 같은 사용자가 Azure Active Directory를 통해 엔터프라이즈 클라우드에 연결할 수 있습니다. 이를 통해 회사 소유 또는 개인 소유(BYOD)를 막론하고 어떤 Windows 장치에서도 Windows 배포가 간편해졌으며 조직 앱 및 리소스에 쉽게 액세스할 수 있게 되었습니다.

Azure AD 조인은 클라우드 우선/클라우드 전용인 기업(일반적으로 온-프레미스 Windows Server Active Directory 인프라가 없는 중소 규모 기업)을 위한 것입니다. 즉, Azure AD 조인은 기존의 도메인 가입을 수행할 수 없는 장치(예: 모바일 장치)를 사용하는 대기업 또는 Office 365나 다른 Azure AD SaaS 앱에 주로 액세스해야 하는 사용자에 의해 사용될 수 있거나 사용됩니다.

기존의 도메인 가입이 도메인 가입이 가능한 장치에서 최고의 온-프레미스 환경을 제공하기는 하지만 Azure AD 조인은 도메인 가입이 불가능한 장치에 적합합니다. Azure AD 조인은 클라우드에서 사용자를 관리하는 데도 적합합니다. 그룹 정책 및 SCCM(System Center Configuration Manager)과 같은 기존의 도메인 관리 도구를 사용하는 대신 모바일 장치 관리 기능을 사용하여 수행합니다.

![온-프레미스 Active Directory 및 Azure AD와 개인 장치 및 회사 장치 개요](./media/active-directory-azureadjoin/active-directory-azureadjoin-overview.png)

## <a name="why-should-enterprises-adopt-azure-ad-join?"></a>기업에서 Azure AD 조인을 채택해야 하는 이유는 무엇인가요?
* **클라우드에서 주로 업무를 처리하는 기업**: 온-프레미스 사용 공간을 줄이고 클라우드에서 더 많은 작업을 수행하는 모델을 사용하고 있거나 이러한 모델로 전환하려는 경우 Azure AD 조인이 유용할 수 있습니다. Azure AD 계정을 수동으로 또는 온-프레미스 Active Directory 동기화를 통해 만들었을 수 있습니다. 어떤 경우든 Azure AD에 계정이 있으며 이 계정을 사용하여 Windows 10에 로그인할 수 있습니다. 사용자는 OOBE(첫 실행 경험)나 설정 메뉴를 통해 컴퓨터를 Azure AD에 연결할 수 있습니다. 연결한 후에 사용자는 브라우저나 Office 응용 프로그램에서 SSO(Single-Sign-On)를 사용하여 Office 365와 같은 클라우드 리소스에 편리하게 액세스할 수 있습니다.
* **교육 기관**: 일반적인 시나리오 중 하나는 교육 기관에 두 가지 사용자 유형인 교직원과 학생이 있는 경우입니다. 교직원은 조직에서 장기 멤버로 간주되므로 온-프레미스 계정을 만드는 것이 적절합니다. 하지만 학생은 조직의 단기 멤버이며 Azure AD에서 관리할 수 있습니다. 즉, 디렉터리 규모를 온-프레미스로 저장하는 대신 클라우드에 푸시할 수 있습니다. 또한 학생들은 자신의 Azure AD 계정으로 Windows에 로그인하고 브라우저 또는 Office 응용 프로그램에서 Office 365 리소스에 액세스할 수 있습니다.
* **소매 기업**: 고객에게서 들은 다른 시나리오는 비정규직 근로자를 보다 쉽게 관리하고 싶다는 내용입니다.  역시, 장기적인 정규직 직원을 위한 계정은 보통 도메인에 연결된 컴퓨터에 온-프레미스 계정으로 만들어집니다. 하지만 비정규직 근로자는 조직의 단기 멤버이므로 사용자 라이선스를 보다 쉽게 이동할 수 있도록 관리하는 것이 좋습니다. Office 365 라이선스를 사용하여 클라우드에 이러한 사용자 계정을 만들면 이러한 사용자는 Azure AD 계정을 사용하여 Windows 및 Office 응용 프로그램에 로그인할 수 있습니다. 한편, 사용자가 조직을 떠난 후에 해당 라이선스에 더 많은 유연성을 유지할 수 있습니다.
* **기타 기업**: 온-프레미스 Active Directory에서 사용자를 유지 관리하더라도 사용자가 Azure-AD에 가입되는 장점을 계속 활용할 수 있습니다. Azure AD가 간소화된 가입 환경, 효율적인 장치 관리, 자동 모바일 장치 관리 등록 및 Azure AD/온-프레미스 리소스에 대한 Single Sign-On 기능을 제공하기 때문입니다.  

## <a name="what-capabilities-does-azure-ad-join-offer?"></a>Azure AD 조인에서 제공하는 기능은 무엇인가요?
Azure AD 조인을 사용하면 다음과 같은 이점을 누릴 수 있습니다.

* **회사 소유의 장치의 자체 프로비전**: Windows 10을 사용하여 사용자는 IT 담당자의 도움 없이도 뛰어난 환경에서 완전히 새로운 장치를 구성할 수 있습니다.
* **최신 폼 팩터 지원**: Azure AD 조인은 기존의 도메인 연결 기능이 없는 장치에서도 작동합니다.  
* **기존 조직 계정 지원**: 사용자는 더 이상 Windows 8에서와 같이 회사에서 발급한 장치를 최대한 활용하기 위해 개인 Microsoft 계정을 만들고 유지 관리할 필요가 없습니다. 대신 Azure AD에서 기존 회사 계정을 그대로 사용할 수 있습니다. 이를 통해 대부분의 조직에서는 기본적으로 사용자가 Office 365에 액세스하는 데 사용되는 것과 동일한 자격 증명으로 Windows를 설정하고 로그인할 수 있습니다.
* **자동 모바일 장치 관리 등록**: Azure AD에 연결된 장치는 경우 모바일 장치 관리에 자동으로 등록될 수 있습니다. 이 프로세스는 Microsoft Intune 및 파트너 모바일 장치 관리 솔루션으로 작동합니다. Intune을 사용하여 장치 관리를 하는 경우 IT 관리자는 SCCM 관리 콘솔에서 도메인에 가입된 장치와 Azure AD에 조인된 장치를 모니터링하고 관리할 수 있습니다.
* **회사 리소스에 Single Sign-on**: 사용자는 Single Sign-on을 통해 Windows 데스크톱에서 [Azure AD Connect](active-directory-azureadjoin-deployment-aadjoindirect.md)를 통해 인증을 받기 위해 Azure AD에 의존하는 수많은 비즈니스 응용 프로그램과 Office 365 앱과 같은 클라우드의 앱 및 리소스에 로그온할 수 있습니다. Azure AD에 조인된 회사 소유의 장치는 회사 네트워크에 연결되어 있을 때 SSO를 통해 온-프레미스 리소스에 로그온할 수 있으며, 이러한 리소스가 노출된 경우에는 [Azure AD 응용 프로그램 프록시](https://msdn.microsoft.com/library/azure/Dn768219.aspx)를 통해 어디에서든지 연결할 수 있습니다.
* **OS 상태 로밍**: 내게 필요한 옵션 설정, 웹 사이트, Wi-Fi 암호 및 기타 설정은 개인 Microsoft 계정 없이도 회사 소유의 장치 간에 동기화됩니다.
* **엔터프라이즈급 Windows 스토어**: Windows 스토어는 Azure AD 계정을 사용한 앱 획득 및 라이선스를 지원합니다. 조직에서는 볼륨 라이선스 방식으로 앱 사용을 허가할 수 있으며 조직의 사용자가 앱을 사용하도록 할 수 있습니다.

## <a name="how-do-different-devices-work-with-azure-ad-join?"></a>다양한 장치가 Azure AD 조인에서 어떻게 작동하나요?
| 회사 장치(온-프레미스 도메인에 가입) | 회사 장치(클라우드에 가입) | 개인 장치 |
| --- | --- | --- |
| 사용자는 회사 자격 증명을 사용하여 Windows에 로그인할 수 있습니다(현재 방식). |사용자는 Azure AD에서 관리되는 회사 자격 증명을 사용하여 Windows에 로그인할 수 있습니다. 이러한 방식은 다음과 같은 세 가지 경우의 회사 장치에 적합합니다. 1)조직에 Active Directory 온-프레미스가 없습니다(예: 소규모 기업). 2)조직이 Active Directory에 모든 사용자 계정을 다 만드는 것은 아닙니다(예: 학생, 컨설턴트, 비정규직 근로자를 위한 계정이 Active Directory에 만들어지지 않음). 3)조직에 모바일 SKU(예: 공장/소매 매장의 보조 장치)가 실행되는 휴대폰이나 태블릿과 같이 (온-프레미스) 도메인에 가입할 수 없는 회사 장치가 있습니다. Azure AD 조인은 관리 및 페더레이션 조직 모두를 위한 회사 장치의 가입을 지원합니다. |사용자는 개인 Microsoft 계정 자격 증명을 사용하여 Windows에 로그인합니다(변경 없음). |
| 사용자는 로밍 설정 및 엔터프라이즈 Windows 스토어에 액세스할 수 있습니다. 이러한 서비스는 회사 계정으로 작업하고 개인 Microsoft 계정이 필요하지 않습니다. 이 경우 조직이 자신의 온-프레미스 Active Directory를 Azure AD에 연결해야 합니다. |사용자는 셀프 서비스 설정을 수행할 수 있습니다. 사용자는 IT 부서의 장치 프로비전 대신 회사 계정을 통해 FRX(첫 실행 경험)를 거칠 수 있으며 두 방법 모두 지원됩니다. |사용자가 Active Directory 또는 Azure AD에서 관리되는 회사 계정을 쉽게 추가할 수 있습니다. |
| 사용자는 데스크톱에서, 회사 앱, 웹 사이트 및 리소스(인증을 위해 Azure AD를 사용하는 클라우드 앱 및 온-프레미스 리소스를 포함)까지 SSO 기능을 보유합니다. |장치는 엔터프라이즈 디렉터리(Azure AD)에 자동으로 등록되며 모바일 장치 관리에 자동으로 등록됩니다. (Azure AD Premium 기능) |사용자는 이 회사 계정을 사용하여 앱 간 및 웹 사이트/리소스에 대한 SSO 기능을 보유합니다. |
| 사용자는 엔터프라이즈 데이터에 영향을 주지 않고 자신의 개인 사진 및 파일에 액세스하는 개인 Microsoft 계정을 추가할 수 있습니다. (로밍 설정이 해당 회사 계정으로 계속 작동합니다.) Microsoft 계정을 통해 SSO가 가능하며 더 이상 설정을 로밍하도록 유도하지 않습니다. |사용자는 winlogon에서 셀프 서비스 암호 재설정(SSPR)을 수행할 수 있으므로 잊어버린 암호를 재설정할 수 있습니다. (Azure AD Premium 기능) |사용자는 자신의 개인 장치에서 기간 업무 앱을 획득 및 사용할 수 있도록 엔터프라이즈 Windows 스토어에 액세스할 수 있습니다. |

## <a name="additional-information"></a>추가 정보
* [엔터프라이즈를 위한 Windows 10: 작업에 장치를 사용하는 방법](active-directory-azureadjoin-windows10-devices-overview.md)
* [Azure Active Directory 조인을 통해 클라우드 기능을 Windows 10 장치로 확장](active-directory-azureadjoin-user-upgrade.md)
* [Microsoft Passport를 통해 암호 없이 ID 인증](active-directory-azureadjoin-passport.md)
* [Azure AD 조인에 대한 사용 시나리오에 대해 알아보기](active-directory-azureadjoin-deployment-aadjoindirect.md)
* [Windows 10 환경용 Azure AD에 도메인 가입된 장치 연결](active-directory-azureadjoin-devices-group-policy.md)
* [Azure AD 조인 설정](active-directory-azureadjoin-setup.md)

<!--HONumber=Oct16_HO2-->


