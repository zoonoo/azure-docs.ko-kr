<properties 
   pageTitle="시스템 업데이트로 서버 업데이트"
   description="서버 인프라에 누락된 업데이트를 적용할 수 있도록 Microsoft Azure Operational Insights의 시스템 업데이트 인텔리전스 팩을 사용하는 방법을 알아봅니다."
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
   ms.date="03/20/2015"
   ms.author="banders" />

# 시스템 업데이트로 서버 업데이트

서버 인프라에 누락된 업데이트를 적용할 수 있도록 Microsoft Azure Operational Insights의 시스템 업데이트 인텔리전스 팩을 사용할 수 있습니다. 인텔리전스 팩을 설치하여 Operational Insights용 기본 구성 모듈 및 Operations Manager 에이전트를 업데이트합니다. 업데이트 정보는 모니터링 되는 서버에서 읽은 다음 처리를 위해 클라우드의 Operational Insights 서비스로 보내집니다. 논리는 업데이트 데이터에 적용되며 클라우드 서비스는 데이터를 기록합니다. 누락된 업데이트가 발견되면 **업데이트** 대시보드에 표시됩니다. **업데이트** 대시보드를 사용하여 누락된 업데이트를 작업하고 필요로 하는 서버에 적용하도록 계획할 수 있습니다.

## 시스템 업데이트를 사용하여 서버를 업데이트합니다.

Microsoft Azure Operational Insights에서 시스템 업데이트를 사용하려면 인텔리전스 팩이 설치되어 있어야 합니다. 인텔리전스 팩 설치에 대한 자세한 내용은 [갤러리를 사용하여 인텔리전스 팩 추가 또는 제거](operational-insights-add-intelligence-packs.md)를 참조하세요. Operational Insights의 **개요** 페이지의 **시스템 업데이트 평가**를 사용하여 모니터링된 서버에서 누락된 업데이트를 보고 설치할 수 있습니다. 

![시스템 업데이트 평가 타일의 이미지](./media/operational-insights-updates/overview-update.png)

타일은 누락된 업데이트의 전체 요약을 볼 수 있는 **업데이트** 대시보드를 엽니다. 페이지는 다음 범주를 자세히 설명합니다.

- 보안 업데이트가 누락된 서버

- 최근에 업데이트하지 않은 서버

- 특정 서버에 적용되어야 하는 업데이트

- 누락된 업데이트의 유형

타일이나 항목을 클릭하여 **검색** 페이지에서 세부 정보를 보고 누락된 업데이트에 대한 자세한 정보를 얻습니다. 

![업데이트 대시보드의 이미지](./media/operational-insights-updates/gallery-sysupdate-01.png)

![업데이트 대시보드의 이미지](./media/operational-insights-updates/gallery-sysupdate-02.png)

## 검색 결과 ##
업데이트 검색 결과는 다음을 포함합니다.

- 서버

- 업데이트 제목

- 기술 자료 ID

- 제품 업데이트

- 업데이트 중요도

- 게시 날짜

*서버* 검색 결과는 다음을 포함합니다.

- 서버 이름

- 운영 체제 버전 이름

- 메서드를 사용하도록 설정하는 자동 업데이트

- 마지막 업데이트 이후 기간(일)

- Windows Update 에이전트 버전

## 업데이트를 작성하려면

1. **개요** 페이지에서 **시스템 업데이트 평가** 타일을 클릭합니다.

2. **맬웨어 방지** 대시보드에서 업데이트 범주를 보고 작업할 범주를 선택합니다.

3. 타일 또는 모든 항목에 대한 자세한 정보를 보려면 해당 항목을 **검색** 페이지에서 클릭합니다.

4. 발견한 정보를 사용하여 누락된 업데이트를 적용하는 계획을 만들 수 있습니다.


<!--HONumber=52-->