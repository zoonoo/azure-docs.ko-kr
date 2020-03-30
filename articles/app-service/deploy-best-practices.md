---
title: 배포 모범 사례
description: Azure 앱 서비스에 배포하는 주요 메커니즘에 대해 알아봅니다. 언어별 권장 사항 및 기타 주의 사항을 찾아보십시오.
keywords: azure 앱 서비스, 웹 앱, 배포, 배포, 파이프라인, 빌드
author: jasonfreeberg
ms.assetid: bb51e565-e462-4c60-929a-2ff90121f41d
ms.topic: article
ms.date: 07/31/2019
ms.author: jafreebe
ms.openlocfilehash: 14946a05f021a9b155fd9a9621f73bde980970fa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75750473"
---
# <a name="deployment-best-practices"></a>배포 모범 사례

모든 개발 팀에는 모든 클라우드 서비스에서 효율적인 배포 파이프라인을 구현하기가 어려워질 수 있는 고유한 요구 사항이 있습니다. 이 문서에서는 앱 서비스에 배포하는 세 가지 주요 구성 요소인 배포 원본, 빌드 파이프라인 및 배포 메커니즘을 소개합니다. 이 문서에서는 특정 언어 스택에 대한 몇 가지 모범 사례 및 팁도 다룹니다.

## <a name="deployment-components"></a>배포 구성 요소

### <a name="deployment-source"></a>배포 소스

배포 소스는 응용 프로그램 코드의 위치입니다. 프로덕션 앱의 경우 배포 소스는 일반적으로 [GitHub, BitBucket 또는 Azure 리포지토리와](deploy-continuous-deployment.md)같은 버전 제어 소프트웨어에서 호스팅되는 리포지토리입니다. 개발 및 테스트 시나리오의 경우 배포 소스가 [로컬 컴퓨터의 프로젝트일](deploy-local-git.md)수 있습니다. 앱 서비스는 또한 [OneDrive 및 Dropbox 폴더를](deploy-content-sync.md) 배포 소스로 지원합니다. 클라우드 폴더를 사용하면 App Service를 쉽게 시작할 수 있지만 일반적으로 엔터프라이즈 급 프로덕션 응용 프로그램에 는 이 소스를 사용하지 않는 것이 좋습니다. 

### <a name="build-pipeline"></a>빌드 파이프라인

배포 소스를 결정한 후 다음 단계는 빌드 파이프라인을 선택하는 것입니다. 빌드 파이프라인은 배포 소스에서 소스 코드를 읽고 일련의 단계(예: 코드 컴파일, HTML 및 JavaScript 축소, 테스트 실행 및 구성 요소 패키징)를 실행 가능한 상태로 실행합니다. 빌드 파이프라인에서 실행되는 특정 명령은 언어 스택에 따라 다릅니다. 이러한 작업은 Azure 파이프라인과 같은 빌드 서버에서 실행하거나 로컬로 실행할 수 있습니다.

### <a name="deployment-mechanism"></a>배포 메커니즘

배포 메커니즘은 웹 앱의 */home/site/wwwroot* 디렉터리에 내장된 응용 프로그램을 넣는 데 사용되는 작업입니다. */wwwroot* 디렉터리웹 앱의 모든 인스턴스에서 공유하는 탑재된 저장소 위치입니다. 배포 메커니즘이 응용 프로그램을 이 디렉터리에 넣으면 인스턴스에서 새 파일을 동기화하라는 알림을 받게 됩니다. 앱 서비스는 다음 배포 메커니즘을 지원합니다.

- Kudu 끝점: [Kudu는](https://github.com/projectkudu/kudu/wiki) Windows 앱 서비스에서 별도의 프로세스로 실행되며 Linux 앱 서비스의 두 번째 컨테이너로 실행되는 오픈 소스 개발자 생산성 도구입니다. Kudu는 지속적인 배포를 처리하고 zipdeploy와 같은 배포를 위한 HTTP 끝점을 제공합니다.
- FTP 및 웹 배포: [사이트 또는 사용자 자격 증명을](deploy-configure-credentials.md)사용하여 [FTP](deploy-ftp.md) 또는 WebDeploy를 통해 파일을 업로드할 수 있습니다. 이러한 메커니즘은 Kudu를 통과하지 않습니다.  

Azure 파이프라인, Jenkins 및 편집기 플러그인과 같은 배포 도구는 이러한 배포 메커니즘 중 하나를 사용합니다.

## <a name="language-specific-considerations"></a>언어별 고려 사항

### <a name="java"></a>Java

JAR 응용 프로그램을 배포하기 위해 Kudu [zipdeploy/](deploy-zip.md) API를 사용하고 WAR 앱에 [대한 배포/배포를](deploy-zip.md#deploy-war-file) 사용합니다. Jenkins를 사용하는 경우 배포 단계에서 해당 API를 직접 사용할 수 있습니다. 자세한 내용은 [이 문서를](../jenkins/execute-cli-jenkins-pipeline.md)참조하십시오.

### <a name="node"></a>노드

기본적으로 Kudu는 노드 응용 프로그램()에`npm install`대한 빌드 단계를 실행합니다. Azure DevOps와 같은 빌드 서비스를 사용하는 경우 Kudu 빌드가 필요하지 않습니다. Kudu 빌드를 사용하지 않도록 설정하려면 `SCM_DO_BUILD_DURING_DEPLOYMENT`값으로 앱 `false`설정을 만듭니다.

### <a name="net"></a>.NET 

기본적으로 Kudu는 .Net 응용 프로그램()에`dotnet build`대한 빌드 단계를 실행합니다. Azure DevOps와 같은 빌드 서비스를 사용하는 경우 Kudu 빌드가 필요하지 않습니다. Kudu 빌드를 사용하지 않도록 설정하려면 `SCM_DO_BUILD_DURING_DEPLOYMENT`값으로 앱 `false`설정을 만듭니다.

## <a name="other-deployment-considerations"></a>기타 배포 고려 사항

### <a name="use-deployment-slots"></a>배포 슬롯 사용

가능하면 새 프로덕션 빌드를 배포할 때 [배포 슬롯을](deploy-staging-slots.md) 사용합니다. 표준 앱 서비스 계획 계층 또는 그 이상을 사용하는 경우 스테이징 환경에 앱을 배포하고 변경 내용을 확인하며 연기 테스트를 수행할 수 있습니다. 준비가 되면 스테이징 및 프로덕션 슬롯을 교체할 수 있습니다. 스왑 작업은 생산 규모에 맞게 필요한 작업자 인스턴스를 예열하여 가동 중지 시간을 제거합니다. 

### <a name="local-cache"></a>로컬 캐시

Azure App Service의 콘텐츠는 Azure Storage에 저장되며 영구적 방식으로 콘텐츠 공유로 표시됩니다. 그러나 일부 앱은 고가용성으로 실행할 수 있는 고성능 읽기 전용 콘텐츠 저장소만 있으면 됩니다. 이러한 앱은 [로컬 캐시를](overview-local-cache.md)사용하여 이점을 얻을 수 있습니다. 로컬 캐시는 워드 프레스와 같은 콘텐츠 관리 사이트에 대 한 권장 하지 않습니다.

항상 가동 중지 시간을 방지하기 위해 [배포 슬롯과](deploy-staging-slots.md) 함께 로컬 캐시를 사용합니다. 이러한 기능을 함께 사용하는 방법에 대한 자세한 내용은 [이 섹션을](overview-local-cache.md#best-practices-for-using-app-service-local-cache) 참조하십시오.

### <a name="high-cpu-or-memory"></a>높은 CPU 또는 메모리

앱 서비스 계획에서 사용 가능한 CPU 또는 메모리의 90% 이상을 사용하는 경우 기본 가상 시스템이 배포를 처리하는 데 문제가 있을 수 있습니다. 이 경우 배포를 수행하기 위해 인스턴스 수를 일시적으로 늘입니다. 배포가 완료되면 인스턴스 수를 이전 값으로 되돌릴 수 있습니다.

모범 사례에 대한 자세한 내용은 [앱 서비스 진단을](https://docs.microsoft.com/azure/app-service/overview-diagnostics) 방문하여 리소스와 관련된 실행 가능한 모범 사례를 알아보십시오.

- [Azure 포털에서](https://portal.azure.com)웹 앱으로 이동합니다.
- 앱 서비스 진단을 여는 왼쪽 탐색에서 **진단 및 해결 문제를** 클릭합니다.
- 모범 사례 홈페이지 타일을 **선택합니다.**
- **가용성 & 성능 모범 사례** 또는 최적의 **구성에 대한 모범 사례를** 클릭하여 이러한 모범 사례와 관련하여 앱의 현재 상태를 확인합니다.

이 링크를 사용하여 리소스에 대한 앱 서비스 진단을 `https://ms.portal.azure.com/?websitesextension_ext=asd.featurePath%3Ddetectors%2FParentAvailabilityAndPerformance#@microsoft.onmicrosoft.com/resource/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Web/sites/{siteName}/troubleshoot`직접 열 수도 있습니다.
