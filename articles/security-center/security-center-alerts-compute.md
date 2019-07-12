---
title: Azure Security Center에서 클라우드 네이티브 계산에 대 한 검색 위협 | Microsoft Docs
description: 이 항목에서는 Azure Security Center에서 사용할 수 있는 네이티브 계산 경고 클라우드를 제공 합니다. Azure 보안 센터.
services: security-center
documentationcenter: na
author: monhaber
manager: rkarlin
editor: ''
ms.assetid: 5aa5efcf-9f6f-4aa1-9f72-d651c6a7c9cd
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 7/02/2019
ms.author: monhaber
ms.openlocfilehash: bc3cb66d43e71777e06c6bd63dcff35e2ff19df8
ms.sourcegitcommit: 1e347ed89854dca2a6180106228bfafadc07c6e5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/05/2019
ms.locfileid: "67571687"
---
# <a name="threat-detection-for-cloud-native-compute-in-azure-security-center"></a>위협 검색에 대 한 고유 계산을 사용할 수 있는 Azure Security Center를 클라우드

Security Center를 클라우드 공급자로 내부 로그를 분석 하 고 여러 대상에서 공격 방법을 식별에 고유한 표시 여부를 활용 합니다. 이 항목에서는 다음과 같은 Azure 서비스에 대 한 사용 가능한 경고를 제공합니다.

* [Azure App Service](#app-services)
* [컨테이너](#azure-containers) 

## Azure App Service <a name="app-services"></a>

Security Center는 Azure App Service를 통해 실행 하는 고객 응용 프로그램을 대상으로 한 공격을 식별 하는 클라우드 규모를 활용 합니다. 최신 네트워크에 실질적으로 되는 웹 응용 프로그램을 사용 하 여 공격자가 이러한 취약성을 악용을 검색 합니다. 특정 환경에 라우팅되기 전에 Azure에서 실행 중인 응용 프로그램에 대 한 요청 검사 및 기록 된 여러 게이트웨이 통해 이동 합니다. 이 데이터는 공격자가 악용을 식별 하 고 나중에 사용 되는 새 패턴에 다음 사용 됩니다.

Azure는 클라우드 공급자로 표시 여부를 활용 하 여 Security Center는 여러 대상에서 공격 방법론을 파악 하기 위해 App Service 내부 로그를 분석 합니다. 예를 들어, 광범위 한 검색 및 분산된 공격입니다. 일반적으로 이러한 유형의 공격 Ip의 작은 하위 집합에서 제공 하 고 취약 한 페이지 또는 플러그 인을 검색 하는 여러 호스트와 유사한 끝점 크롤링이 패턴을 보여 합니다. 이 클라우드를 사용 하 여 검색할 수 있지만 단일 호스트의 관점에서 식별할 수 없습니다.

또한 security Center 기본 샌드박스 및 Vm에 액세스할 수 있습니다. 메모리 법정 데이터와 함께 인프라는 새로운 공격 고객 컴퓨터에서 손상 하 고 야생에서 순환을 위한 방법에서 스토리를 알 수 있습니다. 따라서 Security Center 악용 되 면 긴 웹 응용 프로그램에 대 한 공격을 감지할 수 있습니다.

> [!div class="mx-tableFixed"]

|경고|설명|
|---|---|
|**검색 된 의심 스러운 WordPress 테마 호출**|Azure App Service 활동 로그에는 App Service 리소스에 대해 가능한 코드 삽입 작업을 나타냅니다.<br/> 이 의심 스러운 활동에 활동 뒤에 직접 웹 요청을 조작된 테마 파일을 호출 코드의 서버 쪽 실행을 지 원하는 WordPress 테마를 조작 하는 유사 합니다. 이 유형의 작업 나타났습니다 과거에서 공격 캠페인의 일환으로 WordPress를 통해.|
|**검색 된 비정상적인 IP 주소에서 웹 페이지에 연결**|Azure App Service 활동 로그는 이전에 연결한 적이 없는 원본 주소에서 중요 한 웹 페이지에 대 한 연결을 나타냅니다. 이 웹 앱 관리 페이지에 무차별 공격을 시도 하는 사람이 나타낼 수 있습니다. 합법적인 사용자가 사용 되 고 새 IP 주소를 결과일 수도 있습니다.|
|**Azure App Service FTP 인터페이스에 연결 하는 IP 위협 인텔리전스에서 찾을 수**|Azure App Service FTP 로그 분석에는 위협 인텔리전스 피드에서 발견 되는 원본 주소에서 연결이 되었습니다. 이 연결 하는 동안 사용자는 아래 나열 된 페이지가 액세스 합니다.|
|**웹 검색의 지문 인식**|Azure App Service 활동 로그를 가능한 웹 앱 서비스 리소스에서 작업의 지문 인식을 나타냅니다. <br/>이 의심 스러운 활동이 검색 됨 블라인드 코끼리 라는 도구를 사용 하 여 연결 됩니다. 도구 지문을 웹 서버 및 설치 된 응용 프로그램 및 해당 버전을 검색 하려고 합니다. 공격자는 종종 취약점을 찾기 위해 웹 응용 프로그램을 검색 하는 것에 대 한이 도구를 사용 합니다.|
|**검색 가능한 취약 한 웹 페이지에 대 한 의심 스러운 액세스**|Azure App Service 활동 로그는 중요할 수 있는 웹 페이지 액세스를 나타냅니다. <br/>이 의심 스러운 활동 인 액세스 패턴이 유사 웹 스캐너는 원본 주소에서 시작 합니다. 이러한 활동 검색 해 보면 중요 하거나 취약 한 웹 페이지에 액세스 하려면 네트워크에 공격자에 의해 연결 시도가 된 경우가 있습니다.|
|**PHP 폴더의 파일에 업로드**|Azure App Service 활동 로그 항목에 폴더 업로드에 의심 스러운 PHP 페이지에 액세스를 나타냅니다. <br/>이 유형의 폴더 들어 있지 않은 경우가 PHP 파일입니다. 이 유형의 파일의 존재 여부는 임의의 파일 업로드 취약성을 활용 하는 악용을 나타낼 수 있습니다.|
|**Windows App Service에서 Linux 명령을 실행 하려고**|App Service 프로세스 분석 Windows App Service에서 Linux 명령을 실행 하려고를 했습니다. 이 작업은 웹 응용 프로그램이 실행 있었습니다. 이 문제는 주로 캠페인을 일반적인 웹 응용 프로그램에서 취약점을 악용 하는 동안 표시 됩니다.|
|**검색 된 의심 스러운 PHP 실행**|컴퓨터 로그에 표시 하는 의심 스러운 PHP 프로세스를 실행 합니다. 작업에는 PHP 프로세스를 사용 하 여 명령줄에서 운영 체제 명령 또는 PHP 코드를 실행 하려는 시도가 포함 되어 있습니다. 이 동작은 합법적인 수 있지만, 웹 응용 프로그램에서이 동작에도 발견 됩니다 웹 셸 사용 하 여 websites를 감염 시도 같은 악성 활동에 합니다.|
|**임시 폴더에서 프로세스 실행**|App Service 프로세스 분석에서 앱의 임시 폴더에서 프로세스의 실행을 감지 합니다. 이 동작은이 또한 관찰 됩니다 악성 활동에는 웹 응용 프로그램에서 합법적이 될 수 있습니다.|
|**권한이 높은 명령 검색을 실행 하려고 합니다.**|App Service 프로세스 분석에 높은 권한이 필요한 명령을 실행 하려면 시도를 감지 했습니다. 웹 응용 프로그램 컨텍스트에서 명령을 실행 합니다. 이 동작은이 또한 관찰 됩니다 악성 활동에는 웹 응용 프로그램에서 합법적이 될 수 있습니다.|

> [!NOTE]
> App Service에 대 한 보안 센터 위협 검색은 현재 Azure government 및 소 버린 클라우드 지역에서 지원 되지.

App Service 위협 요소 탐지에 대 한 자세한 내용은 경고 App Service를 Azure Security Center를 사용 하 여 보호를 방문 하 고 모니터링 하며, 앱 서비스 워크 로드 보호를 사용 하도록 설정 하는 방법을 검토.

## 컨테이너 <a name="azure-containers"></a>

Security Center auditd framework를 기반으로 하는 Linux 컴퓨터에서 컨테이너에 대 한 실시간 위협 감지를 제공 합니다. 경고는 호스트 암호화가 사용 또는 Docker 컨테이너 내에서 실행 되는 SSH (보안 셸) 서버의 표시에서 권한 있는 컨테이너의 만들기와 같은 여러 의심 스러운 Docker 활동을 식별 합니다. 이 정보를 사용하여 보안 문제를 신속하게 수정하고 컨테이너의 보안을 강화할 수 있습니다. Linux 검색 외에도 보안 센터도 컨테이너 배포에 대 한 더 구체적인는 분석을 제공 합니다.
