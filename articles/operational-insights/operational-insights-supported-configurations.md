<properties
   pageTitle="Operational Insights에 대해 지원되는 구성"
   description="Operational Insights에 필요한 구성을 알아봅니다."
   services="operational-insights"
   documentationCenter=""
   authors="bandersmsft"
   manager="jwhit"
   editor="tysonn" />
<tags
   ms.service="operational-insights"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="07/02/2015"
   ms.author="banders" />

# Operational Insights에 대해 지원되는 구성

[AZURE.INCLUDE [operational-insights-note-moms](../../includes/operational-insights-note-moms.md)]

Operational Insights를 사용하는 데 필요한 사항 Operational Insights를 준비하려면 다음 정보를 확인합니다.


## System Center 2012 Operations Manager에 대한 구성

System Center 2012 R2 또는 System Center 2012 SP1 R2의 Operations Manager에서 Operational Insights를 연결된 서비스로 사용할 수 있습니다. 이 경우 Operations Manager의 운영 콘솔을 사용하여 Operational Insights 경고 및 구성 정보를 볼 수 있습니다. Operations Manager에서 Operational Insights를 연결된 서비스로 사용하는 경우 에이전트가 관리 서버와 직접 통신하며, 관리 서버는 다시 Operational Insights 서비스와 통신합니다.

Operational Insights를 연결된 서비스로 사용하려면 다음과 같은 필수 조건이 필요합니다.


- System Center 2012 SP1 Operations Manager와 Operational Insights 간의 통합을 위해 [Operational Insights Connector for Operations Manager](https://www.microsoft.com/ko-kr/download/details.aspx?id=38199)에 포함된 업데이트된 관리 팩이 필요합니다. [Operational Insights Connector for Operations Manager](https://www.microsoft.com/ko-kr/download/details.aspx?id=38199)에서 관리 팩을 다운로드하고 설치할 수 있습니다.

- System Center 2012 SP1: Operations Manager 업데이트 롤업 6(업데이트 롤업 7 선호). 이 업데이트는 연결된 서비스 시나리오로 Operational Insights의 관리 서버, 에이전트 및 운영 콘솔에 적용되어야 합니다.

- System Center 2012 R2: Operations Manager 업데이트 롤업 2(업데이트 롤업 3 선호). 이 업데이트는 연결된 서비스 시나리오로 Operational Insights의 관리 서버, 에이전트 및 운영 콘솔에 적용되어야 합니다.

- 용량 관리 데이터를 보려면 Virtual Machine Manager(VMM)과의 Operations Manager 연결을 사용하도록 설정해야 합니다. 시스템 연결에 대한 자세한 내용은 [Operations Manager와 VMM을 연결하는 방법](https://technet.microsoft.com/ko-kr/library/hh882396.aspx)을 참조하세요.

설치 및 구성 지침은 [Operational Insights 경고 보기](http://go.microsoft.com/fwlink/?LinkID=293793)를 참조하세요.

SharePoint Server 2010, Lync Server 2013, Lync Server 2010 또는 System Center 2012 SP1 - Virtual Machine Manager에 대한 Operational Insights 경고를 보려면 해당 작업에 대해 실행 계정을 구성해야 합니다. 실행을 계정으로 설정하는 방법에 대한 정보는 [Operational Insights와 작업 관리자 고려 사항](operational-insights-operations-manager.md)을 참조하십시오.


### Operations Manager 운영 체제

Operations Manager 에이전트는 다양한 컴퓨터에서 지원됩니다. 에이전트 지원에 대한 자세한 내용은 [시스템 요구 사항: System Center 2012 R2 Operations Manager](https://technet.microsoft.com/library/dn249696.aspx)를 참조하세요.

### Operations Manager에 대한 필수 소프트웨어

용량 관리 데이터를 보려면 VMM과의 Operations Manager 연결을 사용하도록 설정해야 합니다. 시스템 연결에 대한 자세한 내용은 [Operations Manager와 VMM을 연결하는 방법](https://technet.microsoft.com/ko-kr/library/hh882396.aspx)을 참조하세요.

## Operational Insights에 직접 연결하는 에이전트

서비스에 직접 연결하는 데 사용되는 에이전트는 Microsoft Monitoring Agent입니다. 해당 시스템 요구 사항은 [Microsoft 다운로드 센터](https://www.microsoft.com/ko-kr/download/details.aspx?id=40316&e6b34bbe-475b-1abd-2c51-b5034bcdd6d2=True) 페이지에 나와 있습니다.

## 브라우저

Operational Insights와 함께 다음 브라우저 중 하나를 사용할 수 있습니다.

- Windows Internet Explorer 11, Internet Explorer 10, Internet Explorer 9, Internet Explorer 8 또는 Internet Explorer 7

- Mozilla Firefox 3.5 이상

사용하는 브라우저에 관계없이 Microsoft Silverlight 4도 설치해야 합니다.

## 분석할 수 있는 기술

Operational Insights 구성 평가는 다음과 같은 작업을 분석합니다.

- Windows Server 2012 및 Microsoft Hyper-v Server 2012

- 다음을 포함하는 Windows Server 2008 및 Windows Server 2008 R2
    - Active Directory
	- Hyper-V 호스트
	- 일반 운영 체제

- SQL Server 2012, SQL Server 2008 R2, SQL Server 2008
    - SQL Server 데이터베이스 엔진

- Microsoft SharePoint 2010

- Microsoft Exchange Server 2010 및 Microsoft Exchange Server 2013

- Microsoft Lync Server 2013 및 Lync Server 2010

- System Center 2012 SP1 - Virtual Machine Manager

SQL Server의 경우 분석에 대해 다음과 같은 32비트 및 64비트 버전이 지원됩니다.

- SQL Server 2008 및 2008 R2 Enterprise

- SQL Server 2008 및 2008 R2 Standard

- SQL Server 2008 및 2008 R2 Workgroup

- SQL Server 2008 및 2008 R2 Web

- SQL Server 2008 및 2008 R2 Express

또한 WOW64 구현에서 실행하는 경우 SQL Server 32비트 버전이 지원됩니다.

<!---HONumber=August15_HO6-->