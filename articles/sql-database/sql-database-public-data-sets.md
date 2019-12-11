---
title: Azure analytics에 대 한 공용 데이터 집합
description: Azure 분석 서비스 및 솔루션을 프로토타입 하 고 테스트 하는 데 사용할 수 있는 공용 데이터 집합에 대해 알아봅니다.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: reference
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 10/01/2018
ms.openlocfilehash: 4b1c1a963b065411f1a0ab84141bdf1835930ebb
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/10/2019
ms.locfileid: "74973546"
---
# <a name="public-data-sets-for-testing-and-prototyping"></a>테스트 및 프로토타입 생성을 위한 공용 데이터 집합

저장소 및 분석 서비스와 솔루션을 프로토타입 하 고 테스트 하는 데 사용할 수 있는 데이터에 대 한 공용 데이터 집합 목록을 찾아봅니다.

## <a name="us-government-and-agency-data"></a>미국 정부 및 에이전시 데이터

| 데이터 원본 | Tudnivalók az adatokról | 파일 정보 |
|---|---|---|
| [미국 정부 데이터](https://catalog.data.gov/dataset) | 농업, 기후, 소비자, 에코 시스템, 교육, 에너지, 재무, 건강, 지방 정부, 제조, maritime, 해양, 공공 안전, 미국에서 과학 및 연구를 다루는 데이터 집합 25만 | HTML, XML, CSV, JSON, Excel 등 다양 한 형식으로 된 다양 한 크기의 파일입니다. 파일 형식을 사용 하 여 사용 가능한 데이터 집합을 필터링 할 수 있습니다. |
| [US 인구 조사 데이터](https://www.census.gov/data.html) | 미국의 인구에 대 한 통계 데이터 | 데이터 집합은 다양 한 형식으로 되어 있습니다. |
| [NASA의 지구 과학 데이터](https://earthdata.nasa.gov/) | 농업, 분위기, 생물권, 기후, cryosphere, 인간 차원, 수권, 육지 표면, 대양, sun과의 상호 작용 등을 다루는 32000 데이터 수집 | 데이터 집합은 다양 한 형식으로 되어 있습니다. |
| [항공 비행 지연 및 기타 교통 데이터](https://www.transtats.bts.gov/OT_Delay/OT_DelayCause1.asp) | "BTS (교통)의 미국 차량 (교통) 기관 (BTS)은 대기업에서 운영 하는 국내 항공편의 실시간 성능을 추적 합니다. 시간, 지연, 취소 및 전환 된 항공편 수에 대 한 요약 정보가 표시 됩니다. 이 웹 사이트에 게시 된 요약 테이블 | 파일은 CSV 형식입니다. |
| [Traffic fatalities-FARS (US 사망률 Analysis Reporting System)](https://www.nhtsa.gov/FARS) | "FARS는 NHTSA, 의회가 취한를 제공 하는 전국 인구 조사,의 심각한 부상에 대 한 미국 공공 연간 데이터를 제공 합니다." | "FARS 쿼리 시스템을 사용 하 여 온라인으로 사용자 고유의 사망률 데이터 실행을 만듭니다. 또는 FTP 사이트에서 제공 되는 1975의 모든 FARS 데이터를 다운로드 합니다. " |
| [유해 화학 데이터-Toxicity ForeCaster (ToxCast™) 데이터](https://www.epa.gov/chemical-research/toxicity-forecaster-toxcasttm-data) | "EPA는 거의 업데이트 되어 수천 개의 화학 물질에서 공개적으로 사용할 수 있는 높은 처리량 toxicity 데이터를 제공 합니다. 이 데이터는 EPA의 ToxCast 연구 활동을 통해 생성 됩니다. " | 데이터 집합은 스프레드시트, R 패키지 및 MySQL 데이터베이스 파일을 비롯 한 다양 한 형식으로 제공 됩니다. |
| [유해 화학 데이터-NIH Tox21 데이터 챌린지 2014](https://tripod.nih.gov/tox21/challenge/) | "2014 Tox21 데이터 챌린지는 Toxicology 효과를 일으킬 수 있는 방식으로 생물학적 경로를 중단 하는 21 세기 이니셔티브의 유해를 통해 테스트 되는 화학 물질 및 복합어의 잠재력을 과학자 이해 하는 데 도움을 주기 위해 설계 되었습니다." | 데이터 집합은 웃는 및 .SDF 형식으로 사용할 수 있습니다. 데이터는 ~ 1만 복합어 (Tox21 10K)의 Tox21 컬렉션에서 "assay 활동 데이터 및 화학 구조"를 제공 합니다. |
| [NCBI의 Biotechnology 및 게놈 데이터](https://www.ncbi.nlm.nih.gov/guide/data-software/) | Genes, genomes 및 단백질를 포함 하는 여러 데이터 집합. | 데이터 집합은 텍스트, XML, 폭발 및 기타 형식으로 되어 있습니다. 앱을 사용할 수 있습니다. |

## <a name="other-statistical-and-scientific-data"></a>기타 통계 및 과학적 데이터

| 데이터 원본 | Tudnivalók az adatokról | 파일 정보 |
|---|---|---|
| [뉴욕 시 taxi 데이터](http://www.nyc.gov/html/tlc/html/about/trip_record_data.shtml) | "Taxi 여행 레코드에는 선택 및 차 날짜/시간, 선택 및 차 위치, 여행 거리, 항목별 fares, 요율 유형, 지불 유형 및 드라이버에서 보고 한 승객 개수를 캡처하는 필드가 포함 됩니다." | 데이터 집합은 월별 CSV 파일에 있습니다. |
| [Microsoft Research 데이터 집합-"연구를 위한 데이터 과학"](https://www.microsoft.com/research/academic-program/data-science-microsoft-research/) | 사용자 컴퓨터 상호 작용, 오디오/비디오, 데이터 마이닝/정보 검색, 지리 공간/위치, 자연어 처리, 로봇 공학/컴퓨터 비전을 포함 하는 여러 데이터 집합이 있습니다. | 데이터 집합은 다운로드를 위해 압축 된 다양 한 형식으로 되어 있습니다. |
| [공용 게놈 데이터](https://www.completegenomics.com/public-data/) | "모든 인간 게놈의 다양 한 데이터 집합을 공개 하 여 게놈 연구를 향상할 수 있습니다." Complete Genomics 공급자는-수익 회사 전용입니다. | 추출 후 데이터 집합은 UNIX 텍스트 형식으로 되어 있습니다. 분석 도구를 사용할 수도 있습니다. |
| [과학 데이터 클라우드 데이터 열기](https://www.opensciencedatacloud.org/projects/) | "개방형 과학 데이터 클라우드는 테라바이트 및 페타바이트 규모의 과학 데이터 집합을 저장, 공유 및 분석 하는 데 필요한 리소스를 과학 커뮤니티에 제공 합니다."| 데이터 집합은 다양 한 형식으로 되어 있습니다. |
| [글로벌 기후 데이터-WorldClim](https://worldclim.org/) | "WorldClim은 약 1 km2의 공간을 확인 하는 전 세계 기후 계층 (gridded 기후 데이터) 집합입니다. 이러한 데이터는 매핑 및 공간 모델링에 사용할 수 있습니다. " | 이러한 파일은 지리 공간적 데이터를 포함 합니다. 자세한 내용은 [데이터 형식](https://worldclim.org/formats1)을 참조 하세요. |
| [인간 사회에 대 한 데이터-GDELT 프로젝트](https://www.gdeltproject.org/data.html) | "GDELT 프로젝트는 가장 포괄적이 고 가장 포괄적 이며 가장 높은 해상도의 오픈 사용자 사회를 만든 데이터베이스입니다." | 원시 데이터 파일은 CSV 형식입니다. |
| [Criteo에서 machine learning을 위한 광고 클릭 예측 데이터](https://labs.criteo.com/2013/12/download-terabyte-click-logs/) | "공개적으로 릴리스된 ML 데이터 집합의 가장 큰 값입니다." 자세한 내용은 [Criteo의 1Tb 클릭 예측 데이터 집합](https://blogs.technet.microsoft.com/machinelearning/20../../now-available-on-azure-ml-criteos-1tb-click-prediction-dataset/)을 참조 하세요. | |
| [Lemur 프로젝트에서 ClueWeb09 텍스트 마이닝 데이터 집합](https://www.lemurproject.org/clueweb09.php/) | "ClueWeb09 데이터 집합은 정보 검색 및 관련 된 인간 언어 기술에 대 한 연구를 지원 하기 위해 만들어졌습니다. 10억 년 1 월과 2 월 2009에 수집 된 10 개의 언어로 된 웹 페이지로 구성 됩니다. " | [데이터 집합 정보](https://www.lemurproject.org/clueweb09/datasetInformation.php)를 참조 하세요.|

## <a name="online-service-data"></a>온라인 서비스 데이터

| 데이터 원본 | Tudnivalók az adatokról | 파일 정보 |
|---|---|---|
| [GitHub 보관](https://www.githubarchive.org/) | "GitHub 보관은 이벤트의 공개 GitHub 타임 라인을 기록 하 고, 보관 하 고, 추가 분석을 위해 쉽게 액세스할 수 있도록 하는 프로젝트입니다." | 웹 클라이언트에서 release.tar.gz (Gzip) 형식의 JSON 인코딩 이벤트 보관 파일을 다운로드 합니다. |
| [GHTorrent 프로젝트의 GitHub 활동 데이터](http://ghtorrent.org/) | "GHTorrent 프로젝트 [is] GitHub REST API를 통해 제공 되는 데이터의 확장 가능 하 고, 쿼리 가능한 오프 라인 미러를 만들기 위한 활동입니다. GHTorrent GitHub 공용 이벤트 시간 줄을 모니터링 합니다. 각 이벤트에 대해 해당 내용 및 종속성을 철저히 검색 합니다. " | MySQL 데이터베이스 덤프는 CSV 형식입니다. |
| [Stack Overflow 데이터 덤프](https://archive.org/details/stackexchange) | "이는 Stack Exchange 네트워크 [Stack Overflow]를 포함 하 여 모든 사용자가 제공 하는 콘텐츠의 익명화 덤프입니다." | "각 사이트 (예: Stack Overflow)는 bzip2 압축을 사용 하 여 7-zip을 통해 압축 된 XML 파일로 구성 된 별도의 아카이브로 형식이 지정 됩니다. 각 사이트 보관 파일에는 게시물, 사용자, 투표, 설명, PostHistory 및 PostLinks가 포함 됩니다. " |
