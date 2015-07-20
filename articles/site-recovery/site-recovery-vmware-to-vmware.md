<properties 
	pageTitle="온-프레미스 VMWare 사이트 간 배포" 
	description="Azure Site Recovery의 InMage Scout는 온-프레미스 VMWare 사이트 간의 복제, 장애 조치(Failover) 및 복구를 처리합니다." 
	services="site-recovery" 
	documentationCenter="" 
	authors="rayne-wiselman" 
	manager="jwhit" 
	editor=""/>

<tags 
	ms.service="site-recovery" 
	ms.workload="backup-recovery" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/29/2015" 
	ms.author="raynew"/>


# 온-프레미스 VMWare 사이트 간 배포


##개요

Azure Site Recovery의 InMage Scout는 온-프레미스 VMWare 사이트 간의 실시간 복제 기능을 제공합니다. InMage Scout는 Azure Site Recovery 서비스에 대한 구독에 포함되어 있습니다.


## 필수 조건

- **Azure 계정**—[Microsoft Azure](http://azure.microsoft.com/) 계정이 있어야 합니다. [무료 평가판](pricing/free-trial/)으로 시작할 수 있습니다.


##1단계: 자격 증명 모음 만들기 및 InMage Scout 다운로드

1. [관리 포털](https://portal.azure.com)에 로그인합니다.
2. **데이터 서비스** > **복구 서비스** > **사이트 복구 자격 증명 모음**을 클릭합니다.
3. **새로 만들기** > **빠른 생성**을 클릭합니다.
4. **이름**에 자격 증명 모음을 식별하기 위한 이름을 입력합니다.
5. **하위 지역**에서 자격 증명 모음에 대한 지리적 하위 지역을 선택합니다. 지원되는 하위 지역을 확인하려면 [Azure Site Recovery 가격 정보](pricing/details/site-recovery/)에서 지리적 가용성을 참조하세요.

<P>상태 표시줄을 점검하여 자격 증명 모음이 성공적으로 만들어졌는지 확인합니다. 자격 증명 모음은 기본 복구 서비스 페이지에서 <b>활성</b>으로 나열됩니다.</P>

##2단계: 자격 증명 모음 구성

1. **자격 증명 모음 만들기**를 클릭합니다.
2. **복구 서비스** 페이지에서 자격 증명 모음을 클릭하여 빠른 시작 페이지를 엽니다.
3. 드롭다운 목록에서 **두 개의 온-프레미스 VMWare 사이트 간**을 선택합니다.
4. InMage Scout를 다운로드합니다.
5. 제품과 함께 다운로드한 InMage Scout 설명서를 이용하여 두 VMWare 사이트 간 복제를 설정합니다.


##다음 단계

문의 사항은 [Azure 복구 서비스 포럼](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)에 게시하세요.<

<!---HONumber=July15_HO2-->