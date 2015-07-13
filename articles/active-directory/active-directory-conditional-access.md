<properties 
	pageTitle="조건부 액세스를 사용한 위험 관리" 
	description="정책을 준수하는 알려진 장치에서 원격으로 특정 리소스에 액세스하도록 허용하고 분실되었거나, 도난당했거나, 호환되지 않는 장치에서는 액세스할 수 없도록 하는 방법을 설명하는 항목입니다." 
	services="active-directory, virtual-network" 
	documentationCenter="" 
	authors="Justinha" 
	manager="TerryLan" 
	editor="LisaToft"/>

<tags 
	ms.service="active-directory" 
	ms.devlang="na" 
	ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="identity" 
	ms.date="05/05/2015" 
	ms.author="Justinha"/>


# 조건부 액세스를 사용한 위험 관리

직원이 어떤 방식으로 작업하는지, 얼마나 생산적인지, 작업을 수행할 때 어떤 수단을 이용하는지에 대한 현재의 추세는 빠르게 변경되고 있습니다. 직원은 회사에 개인 장치를 가지고 오기도 합니다. 이러한 개인 장치에는 개인적인 디지털 생활 속에서 사용하는 앱이 담겨 있습니다. 또한 이러한 장치로 얻게 되는 자유로움과 기능에 익숙해져 있습니다. 직원들은 회사에서도 동일한 응용 프로그램을 사용하기 원하며 개인 디지털 생활에서 누리는 동일한 유연성을 회사 생활에서도 누리려고 합니다. 오늘날의 회사 직원들은 어디에서든지 선택한 장치에서 작업하고 비즈니스 응용 프로그램에 원활하게 연결하고 액세스할 수 있기 바랍니다.

이와 같이 사용자들이 원하는 장소에서 원할 때 원하는 방식으로 작업할 수 있게 되면서 위험도 높아지고 있습니다. 많은 양의 데이터가 들어 있는 이러한 장치를 도난당하거나, 잘못 두거나, 분실하게 됩니다. 이러한 많은 스마트폰 및 태블릿에는 고객 및 회사의 중요한 기밀 콘텐츠가 방대하게 포함되어 있습니다. IT 설계자, 보안 전문가 및 IT 관리자는 이러한 측면에서 균형을 유지하려고 노력하고 있습니다. 즉, 사용자가 선호하는 모든 장치에서 생산적인 업무를 진행하도록 하면서 해당 장치에 있는 회사 콘텐츠에 대해 적절한 수준의 보안 및 보호를 제공하는 것이 필요합니다.

Azure Active Directory, Office 365 및 Microsoft Intune을 통해 제공되는 여러 조건부 액세스 기능을 사용하여 IT 관리자는 다음 문제를 해결할 수 있습니다.

- 인터넷에 있는 모든 사람에게 개방하지는 않으면서, 직원의 원격 액세스를 허용합니다.
- 정책을 준수하는 알려진 장치에서 원격으로 특정 리소스에 액세스할 수 있도록 합니다.
- 분실되거나 도난당한 장치 또는 비규격 장치에서는 액세스할 수 없도록 합니다.

![][1]

## 다음 단계

다음 항목에서는 조직에서 조건부 액세스 정책을 설정하는 데 사용할 수 있는 메커니즘 각각에 대해 설명합니다.

- [Azure Active Directory Device Registration Overview](https://msdn.microsoft.com/library/azure/dn903763.aspx)
- [Azure Active Directory Device Registration을 사용하여 온-프레미스 조건부 액세스 설정](https://msdn.microsoft.com/library/azure/dn788908.aspx)
- [Conditional Access Device Policies for Office 365 services](https://msdn.microsoft.com/library/azure/dn903766.aspx)
- [Azure Conditional Access Preview for SaaS Apps](https://msdn.microsoft.com/library/azure/dn906877.aspx)


<!--Image references-->
[1]: ./media/active-directory-conditional-access/condaccoverviewvsdx1.png
 

<!---HONumber=62-->