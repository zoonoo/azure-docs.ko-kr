---
title: "Operations Management Suite 보안 및 감사 솔루션의 보안 경고 모니터링 및 응답 | Microsoft Docs"
description: "이 문서는 OMS 보안 및 감사에서 사용 가능한 위협 인텔리전스 옵션을 사용하여 보안 경고를 모니터링하고 대응하는 방법을 설명합니다."
services: operations-management-suite
documentationcenter: na
author: YuriDio
manager: swadhwa
editor: 
ms.assetid: 7d45a32b-1341-4bb5-a436-1f42a8a2590a
ms.service: operations-management-suite
ms.custom: oms-security
ms.topic: article
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/01/2016
ms.author: yurid
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 7161cacfd371aa73974e635a343793bbec76d858


---
# <a name="monitoring-and-responding-to-security-alerts-in-operations-management-suite-security-and-audit-solution"></a>Operations Management Suite 보안 및 감사 솔루션의 보안 경고 모니터링 및 응답
이 문서는 OMS 보안 및 감사에서 사용 가능한 위협 인텔리전스 옵션을 사용하여 보안 경고를 모니터링하고 대응하는 방법을 설명합니다.

## <a name="what-is-oms"></a>OMS란?
Microsoft Operations Management Suite(OMS)란 온-프레미스 및 클라우드 인프라를 관리 및 보호하도록 도와주는 Microsoft의 클라우드 기반 IT 관리 솔루션입니다. OMS에 대한 자세한 내용은 [Operations Management Suite](https://technet.microsoft.com/library/mt484091.aspx)문서를 참조하세요.

## <a name="threat-intelligence"></a>위협 인텔리전스
사용자가 네트워크에 광범위하게 액세스할 수 있고 다양한 장치를 사용하여 기업 데이터에 연결하는 엔터프라이즈 환경에서는 리소스를 능동적으로 모니터링하고 보안 문제에 빠르게 대응하는 것이 중요합니다. 이러한 대응은 일부 사이버 보안 위협은 기존 보안 기술 통제 수단으로 식별할 수 있는 경고나 의심스러운 활동으로 나타나지 않기 때문에 보안 수명 주기 측면에서 특히 중요합니다. 

IT 관리자는 OMS의 **위협 인텔리전스** 옵션을 사용하여 환경에 대한 보안 위협을 식별할 수 있습니다. 예를 들어 특정 컴퓨터가 [봇넷](https://www.microsoft.com/security/sir/story/default.aspx#!botnetsection)의 일부인 경우를 식별할 수 있습니다. 공격자가 컴퓨터를 명령 및 컨트롤에 몰래 연결하는 맬웨어를 불법으로 설치할 경우 해당 컴퓨터가 봇넷의 노드가 되었다고 합니다. 또한 [darknet](https://www.microsoft.com/security/sir/story/default.aspx#!botnetsection_honeypots_darkents)과 같은 지하 통신 채널에서 오는 잠재적 위협도 식별할 수 있습니다. 

OMS 보안 및 감사는 이러한 위협 인텔리전스를 구축하기 위해 Microsoft 내의 여러 소스에서 가져온 데이터를 사용합니다. OMS 보안 및 감사는 이 데이터를 사용하여 사용자 환경에 대한 잠재적 위협을 식별합니다.

위협 인텔리전스 창은 세 가지 기본 옵션으로 구성되어 있습니다.

* 아웃바운드 악성 트래픽이 있는 서버
* 검색된 위협 유형
* 위협 인텔리전스 맵

> [!NOTE]
> 모든 옵션에 대한 개요는 [Operations Management Suite 보안 및 감사 솔루션 시작](oms-security-getting-started.md)을 참조하세요.
> 
> 

### <a name="responding-to-security-alerts"></a>보안 경고에 대응
[보안 사고 대응](https://technet.microsoft.com/library/cc512623.aspx) 프로세스 단계 중 하나는 손상된 시스템의 심각도를 식별하는 것입니다. 이 단계에서는 다음 작업을 수행해야 합니다.

* 공격 특성 확인
* 공격 원점 확인
* 공격 의도 확인 특정 정보를 얻기 위해 사용자 조직만 노린 공격입니까, 아니면 무작위 공격입니까?
* 손상된 시스템 식별
* 액세스된 파일 식별 및 해당 파일의 중요도 확인

OMS 보안 및 감사 솔루션의 **위협 인텔리전스** 정보를 활용하여 이러한 작업을 수행할 수 있습니다. 이 **위협 인텔리전스** 옵션에 액세스하려면 다음 단계를 따릅니다.

1. **Microsoft Operations Management Suite** 기본 대시보드에서 **보안 및 감사** 타일을 클릭합니다.
   
    ![보안 및 감사](./media/oms-security-responding-alerts/oms-security-responding-alerts-fig1.png)
2. 아래와 같이 **보안 및 감사** 대시보드의 오른쪽에 **위협 인텔리전스** 옵션이 표시됩니다.
   
    ![위협 인텔리전스](./media/oms-security-responding-alerts/oms-security-responding-alerts-fig2-ga.png)

이러한 세 타일은 현재 위협에 대한 개요를 제공합니다. **아웃바운드 악성 트래픽이 있는 서버** 에서는 모니터링 중인 컴퓨터(네트워크 내부 또는 외부)에서 인터넷에 악성 트래픽을 전송하고 있는지 여부를 식별할 수 있습니다. 

**검색된 위협 유형** 타일에는 현재 "널리 퍼져 있는" 위협의 요약이 표시되며 이 타일을 클릭하면 아래와 같이 이러한 위협에 대한 자세한 내용이 표시됩니다.

![검색된 위협 유형](./media/oms-security-responding-alerts/oms-security-responding-alerts-fig3.png)

각 위협을 클릭하여 자세한 내용을 확인할 수 있습니다. 아래 예는 봇넷에 대한 자세한 정보를 보여줍니다.

![위협에 대한 자세한 내용](./media/oms-security-responding-alerts/oms-security-responding-alerts-fig4.png)

이 섹션의 시작 부분에서 설명한 대로 이 정보는 사고 대응 사례에서 매우 유용할 수 있습니다. 또한 공격 원점, 손상된 시스템, 시기를 찾아야 하는 범죄 수사에서도 중요할 수 있습니다. 이 보고서에서는 공격 원점, 손상된 로컬 IP, 연결의 현재 세션 상태와 같이 공격에 대한 주요 정보를 쉽게 식별할 수 있습니다. 

**위협 인텔리전스 맵**에서는 전 세계에서 악성 트래픽이 있는 현재 위치를 식별할 수 있습니다. 이 맵에는 트래픽의 방향을 식별하는 주황색(들어오는) 및 빨간색(나가는) 화살표가 있습니다. 각 화살표를 클릭하면 아래와 같이 위협의 유형과 트래픽의 방향이 표시됩니다.

![위협 인텔리전스 맵](./media/oms-security-responding-alerts/oms-security-responding-alerts-fig5.png)

> [!NOTE]
> 인시던트 응답 프로세스 중에 이 기능을 사용하는 방법에 대한 데모는 Microsoft Ignite에서 제공하는 [Operations Management Suite를 사용하여 안내식 조사를 통해 데이터 센터 보안 위협 완화](https://myignite.microsoft.com/videos/5000) 프레젠테이션을 참조하세요.
> 
> 

## <a name="see-also"></a>참고 항목
이 문서에서는 OMS 보안 및 감사 솔루션의 **위협 인텔리전스** 옵션을 사용하여 보안 경고에 대응하는 방법에 대해 알아보았습니다. OMS 보안에 대해 자세히 알아보려면 다음 문서를 참조하세요.

* [OMS(Operations Management Suite) 개요](operations-management-suite-overview.md)
* [Operations Management Suite 보안 및 감사 솔루션 시작](oms-security-getting-started.md)
* [Operations Management Suite 보안 및 감사 솔루션의 리소스 모니터링](oms-security-monitoring-resources.md)




<!--HONumber=Nov16_HO3-->


