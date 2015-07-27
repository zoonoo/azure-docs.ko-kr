<properties 
	pageTitle="Azure Multi-Factor Authentication 정의" 
	description="Azure Multi-Factor Authentication은 사용자 이름 및 암호 이외의 다른 내용을 사용해야하는 사람인지를 확인하는 메서드입니다. 사용자 로그인 및 트랜잭션에 대한 보안의 추가 계층을 제공합니다." 
	services="multi-factor-authentication" 
	documentationCenter="" 
	authors="billmath" 
	manager="swadwha" 
	editor="curtand"/>

<tags 
	ms.service="multi-factor-authentication" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/02/2015" 
	ms.author="billmath"/>

# Azure Multi-Factor Authentication 정의
Multi-Factor Authentication(MFA)은 두 개 이상의 검증 방법을 사용해야 하며 사용자 로그인 및 트랜잭션에 중요한 제2의 보안 계층을 추가하는 인증 방법입니다. 이러한 인증에서는 다음 중 두 가지 이상의 검증 방법을 요구합니다.

- 사용자가 알고 있는 정보(일반적으로 암호)
- 사용자가 보유한 장치(예: 휴대폰과 같이 쉽게 복제되지 않는 신뢰할 수 있는 장치)
- 사용자의 신원 정보(생체 인식)

<center>![Username and Password](./media/multi-factor-authentication/pword.png) &#160;&#160;&#160;&#160;&#160;![Certificates](./media/multi-factor-authentication/phone.png) &#160;&#160;&#160;&#160;&#160;![Smart Phone](./media/multi-factor-authentication/hware.png) &#160;&#160;&#160;&#160;&#160;![Smart Card](./media/multi-factor-authentication/smart.png) &#160;&#160;&#160;&#160;&#160;![Virtual Smart Card](./media/multi-factor-authentication/vsmart.png) &#160;&#160;&#160;&#160;&#160;![Username and Password](./media/multi-factor-authentication/cert.png)</center>



Azure Multi-Factor Authentication은 사용자 이름 및 암호 이외의 다른 내용을 사용해야하는 사람인지를 확인하는 메서드입니다. 사용자 로그인 및 트랜잭션에 대한 보안의 두번째 계층을 제공합니다.

간단한 로그인 프로세스에 대한 사용자 요구를 충족하는 동안 Azure Multi-Factor Authentication을 사용하면 데이터와 응용 프로그램에 대한 액세스를 보호합니다. 전화 통화, 문자 메시지 또는 모바일 앱 알림 또는 확인 코드 및 타사 OATH 토큰과 같은 다양한 손쉬운 확인 옵션을 통해 강력한 인증을 전달합니다.

Azure Multi-Factor Authentication의 작동 원리 개요는 다음 비디오를 참조하세요.


<center>[AZURE.VIDEO multi-factor-authentication-overview]</center>

##Azure Multi-Factor Authentication을 사용하는 이유

오늘날, 어느 때 보다 연결된 사람들이 늘어나고 있습니다. 스마트폰, 태블릿, 랩톱 및 PC를 사용하여, 사람들은 연결하고 언제든지 연결 상태를 유지하는 방법에 대한 여러 다른 옵션을 사용합니다. 사람들이 어디에서나 계정 및 응용 프로그램에 액세스할 수 있으며, 이는 더 많은 작업을 수행할 수 있고 고객에게 더 나은 서비스를 제공할 수 있음을 의미합니다.

Azure Multi-Factor Authentication은 인증의 두번째 메서드를 제공하여 사용자가 항상 보호되는 사용하기 쉽고 확장 가능한 신뢰할 수 있는 솔루션입니다.

![용이성](./media/multi-factor-authentication/simple.png)| ![확장성](./media/multi-factor-authentication/scalable.png)| ![항상 보호](./media/multi-factor-authentication/protected.png)|![안정성](./media/multi-factor-authentication/reliable.png)
:-------------: | :-------------: | :-------------: | :-------------: |
**용이성**|**확장성**|**항상 보호**|**안정성**

- **용이성** -Azure Multi-Factor Authentication은 간단하게 설정하고 사용할 수 있습니다. Azure Multi-Factor Authentication과 함께 제공되는 추가 보호를 사용하면 자신의 장치를 관리하고 대부분의 경우 간단한 몇 번 클릭으로 설치할 수 있습니다.
- **확장성** -Azure Multi-Factor Authentication은 클라우드의 강력한 클라우드의 기능을 이용하며 온-프레미스 AD와 사용자 지정 앱을 통합합니다. 이러한 보호는 고용량 업무상 중요 한 시나리오에도 확장됩니다.
- **항상 보호** -Azure Multi-Factor Authentication은 가장 높은 업계 표준을 사용하여 강력한 인증을 제공합니다.
- **안정성** -Azure Multi-Factor Authentication의 99.9%의 가용성을 보장합니다. Multi-Factor Authentication에 대한 인증 요청을 수신하거나 처리할 수 없는 경우 서비스는 사용할 수 없는 것으로 간주됩니다.  

Azure Multi-Factor Authentication을 사용하는 이유에 대한 추가 정보는 다음 비디오를 참조하세요.

<center>[AZURE.VIDEO windows-azure-multi-factor-authentication]</center>

**추가 리소스**

* [Multi-Factor Authentication이 나에게 어떤 의미가 있습니까?](multi-factor-authentication-end-user.md)
* [Office365용 Multi-Factor Authentication 설치](https://support.office.com/article/Set-up-multi-factor-authentication-for-Office-365-8f0454b2-f51a-4d9c-bcde-2c48e41621c6)
* [MSDN에서 Azure Multi-Factor Authentication](https://msdn.microsoft.com/library/azure/dn249471.aspx) 

<!---HONumber=July15_HO3-->