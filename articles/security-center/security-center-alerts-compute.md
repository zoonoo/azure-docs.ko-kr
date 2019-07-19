---
title: Azure Security Center에서 클라우드 기본 계산에 대 한 위협 감지 | Microsoft Docs
description: 이 항목에서는 Azure Security Center에서 사용할 수 있는 클라우드 네이티브 계산 경고를 제공 합니다. Azure Security Center.
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
ms.author: v-mohabe
ms.openlocfilehash: 14433806a28e31cef1a278e16cb69e7c9b1a2458
ms.sourcegitcommit: a8b638322d494739f7463db4f0ea465496c689c6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/17/2019
ms.locfileid: "68295835"
---
# <a name="threat-detection-for-cloud-native-compute-in-azure-security-center"></a>Azure Security Center에서 클라우드 기본 계산에 대 한 위협 감지

클라우드 공급자는 내부 로그를 분석 하 고 여러 대상에 대 한 공격 방법론을 식별 하는 데 있어 고유한 가시성을 활용 Security Center. 이 항목에서는 다음 Azure 서비스에 대해 사용할 수 있는 경고를 제공 합니다.

* [Azure App Service](#app-services)
* [컨테이너](#azure-containers) 

## Azure App Service<a name="app-services"></a>

Security Center는 클라우드의 규모를 활용 하 여 Azure App Service를 실행 하는 고객 응용 프로그램을 대상으로 하는 공격을 식별 합니다. 웹 응용 프로그램은 실제로 모든 오늘날의 네트워크에 있는 경우 공격자가 이러한 취약성을 파악 하는 데 사용 됩니다. 특정 환경으로 라우팅되기 전에는 Azure에서 실행 되는 응용 프로그램에 대 한 요청을 검사 하 고 기록 하는 여러 게이트웨이를 통해 이동 합니다. 그런 다음이 데이터를 사용 하 여 악용 및 공격자를 식별 하 고 나중에 사용 될 새 패턴을 알아봅니다.

Azure가 클라우드 공급자로 표시 되는 가시성을 활용 하 여 Security Center App Service 내부 로그를 분석 하 여 여러 대상에 대 한 공격 방법론을 식별 합니다. 예를 들어 광범위 한 검색 및 분산 공격입니다. 이 유형의 공격은 일반적으로 Ip의 작은 하위 집합에서 제공 되며 취약 한 페이지 또는 플러그 인을 검색 하 여 여러 호스트의 비슷한 끝점에 대 한 탐색 패턴을 보여줍니다. 이는 클라우드를 사용 하 여 검색할 수 있지만 단일 호스트의 관점에서 식별할 수 없습니다.

또한 Security Center에는 기본 샌드박스 및 Vm에 대 한 액세스 권한이 있습니다. 이 인프라는 메모리를 분석 하는 데 사용할 수 있는 새로운 공격 circulating에서 고객 시스템의 손상에 이르기까지 이야기를 알려줄 수 있습니다. 따라서 Security Center는 악용 된 후 웹 응용 프로그램에 대 한 공격을 감지할 수 있습니다.

> [!div class="mx-tableFixed"]

|경고|Description|
|---|---|
|**의심 스러운 WordPress 테마 호출이 검색 됨**|Azure App Service 활동 로그는 App Service 리소스에서 가능한 코드 삽입 활동을 나타냅니다.<br/> 이 의심 스러운 활동은 코드의 서버 쪽 실행을 지 원하는 WordPress 테마를 조작 하 고 조작 된 테마 파일을 호출 하는 직접 웹 요청을 처리 하는 작업과 비슷합니다. 이 유형의 활동은 과거에 WordPress에 대 한 공격 캠페인의 일부로 표시 되었습니다.|
|**비정상 IP 주소에서 웹 페이지에 대 한 연결이 검색 되었습니다.**|Azure App Service 활동 로그는 이전에 연결 되지 않은 원본 주소에서 중요 한 웹 페이지에 대 한 연결을 나타냅니다. 이는 누군가가 웹 앱 관리 페이지에 무차별 암호 대입 공격을 시도 하 고 있음을 나타낼 수 있습니다. 합법적인 사용자가 새 IP 주소를 사용 하는 결과일 수도 있습니다.|
|**Azure App Service FTP 인터페이스에 연결 된 IP가 위협 인텔리전스에 있습니다.**|Azure App Service FTP 로그 분석이 위협 인텔리전스 피드에서 찾은 원본 주소에서 연결을 검색 했습니다. 이 연결 중에 사용자는 아래에 나열 된 페이지에 액세스 합니다.|
|**웹 지문 인식을 검색 됨**|Azure App Service 활동 로그는 App Service 리소스에서 가능한 웹 지문 인식을 활동을 나타냅니다. <br/>이 의심 스러운 활동은 블라인드 코끼리 라는 도구와 연결 되어 있습니다. 도구는 지문이 웹 서버 이며 설치 된 응용 프로그램 및 해당 버전을 검색 하려고 합니다. 공격자는 일반적으로이 도구를 사용 하 여 웹 응용 프로그램을 검색 하 여 취약성을 찾습니다.|
|**취약할 수 있는 웹 페이지에 대 한 의심 스러운 액세스**|Azure App Service 활동 로그는 중요 한 것으로 보이는 웹 페이지가 액세스 되었음을 나타냅니다. <br/>이 의심 스러운 활동은 액세스 패턴이 웹 스캐너와 유사한 원본 주소에서 시작 되었습니다. 이러한 종류의 작업은 공격자가 네트워크를 검색 하 여 중요 하거나 취약 한 웹 페이지에 액세스 하는 등의 작업을 수행 하는 경우가 많습니다.|
|**폴더 업로드의 PHP 파일**|Azure App Service 활동 로그는 upload 폴더에 있는 의심 스러운 PHP 페이지에 액세스 한 항목을 나타냅니다. <br/>이러한 유형의 폴더에는 일반적으로 PHP 파일이 포함 되지 않습니다. 이러한 형식의 파일이 있으면 악용 시 임의의 파일 업로드 취약점을 활용 하는 것을 나타낼 수 있습니다.|
|**Windows App Service에서 Linux 명령을 실행 하려고 했습니다.**|App Service 프로세스를 분석 하 여 Windows App Service에서 Linux 명령을 실행 하려고 했습니다. 웹 응용 프로그램에서이 작업을 실행 하 고 있습니다. 이 동작은 일반적인 웹 응용 프로그램의 취약성을 악용 하는 캠페인 중에 종종 표시 됩니다.|
|**의심 스러운 PHP 실행 검색 됨**|컴퓨터 로그는 의심 스러운 PHP 프로세스가 실행 중임을 표시 합니다. 이 작업에는 PHP 프로세스를 사용 하 여 명령줄에서 OS 명령 또는 PHP 코드를 실행 하는 시도가 포함 되었습니다. 이 동작은 합법적 일 수 있지만 웹 응용 프로그램에서는 웹 셸과 함께 웹 사이트를 감염 하려고 시도 하는 것과 같은 악의적인 작업 에서도이 동작이 관찰 됩니다.|
|**임시 폴더에서 프로세스 실행**|App Service 프로세스 분석에서 앱 임시 폴더의 프로세스 실행을 검색 했습니다. 이 동작은 합법적 일 수 있지만, 웹 응용 프로그램에서는 악성 작업 에서도이 동작이 관찰 됩니다.|
|**높은 권한 실행 명령을 검색 했습니다.**|App Service 프로세스를 분석 하 여 높은 권한이 필요한 명령을 실행 하려고 했습니다. 웹 응용 프로그램 컨텍스트에서 실행 되는 명령입니다. 이 동작은 합법적 일 수 있지만, 웹 응용 프로그램에서는 악성 작업 에서도이 동작이 관찰 됩니다.|

> [!NOTE]
> App Service에 대 한 위협 검색 Security Center 현재 Azure 정부 및 소 버린 클라우드 지역에서 사용할 수 없습니다.

App Service 위협 검색 경고에 대 한 자세한 내용은 Azure Security Center를 사용 하 여 App Service 보호를 참조 하 고 App Service 워크 로드의 모니터링 및 보호를 사용 하도록 설정 하는 방법을 검토 하세요.

## 컨테이너가<a name="azure-containers"></a>

Security Center는 auditd 프레임 워크를 기반으로 Linux 컴퓨터에서 컨테이너에 대 한 실시간 위협 감지 기능을 제공 합니다. 이 경고는 호스트에 권한 있는 컨테이너 만들기, Docker 컨테이너 내에서 실행 되는 SSH (Secure Shell) 서버 또는 crypto 마이너 사용과 같은 의심 스러운 Docker 활동을 식별 합니다. 이 정보를 사용하여 보안 문제를 신속하게 수정하고 컨테이너의 보안을 강화할 수 있습니다. Linux 검색 외에도 Security Center는 컨테이너 배포에 보다 구체적인 분석도 제공 합니다.
