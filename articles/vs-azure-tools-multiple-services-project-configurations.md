---
title: "여러 서비스 구성을 사용하여 Azure 프로젝트 구성 | Microsoft Docs"
description: "ServiceDefinition.csdef 및 ServiceConfiguration.cscfg 파일을 변경하여 Azure 클라우드 서비스 프로젝트를 변경하는 방법에 대해 알아봅니다."
services: visual-studio-online
documentationcenter: na
author: kraigb
manager: ghogen
editor: 
ms.assetid: a4fb79ed-384f-4183-9f74-5cac257206b9
ms.service: multiple
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/11/2016
ms.author: kraigb
ms.translationtype: HT
ms.sourcegitcommit: 83f19cfdff37ce4bb03eae4d8d69ba3cbcdc42f3
ms.openlocfilehash: 24b2530b23211c654072a6edc8a31e53989bf0a8
ms.contentlocale: ko-kr
ms.lasthandoff: 08/21/2017

---
# <a name="configuring-your-azure-project-using-multiple-service-configurations"></a>여러 서비스 구성을 사용하여 Azure 프로젝트 구성
Azure 클라우드 서비스 프로젝트에는 ServiceDefinition.csdef 및 ServiceConfiguration.cscfg의 두 가지 구성 파일이 포함됩니다. 이러한 파일은 Azure 클라우드 서비스 응용 프로그램과 함께 패키지로 구성되고 Azure에 배포됩니다.

* **ServiceDefinition.csdef** 파일에는 포함하는 역할 등 클라우드 서비스 응용 프로그램의 요구 사항에 대한 Azure 환경에 필요한 메타데이터가 포함됩니다. 이 파일에는 또한 모든 인스턴스에 적용되는 구성 설정이 포함됩니다. 이러한 구성 설정은 Azure 서비스 호스팅 런타임 API를 사용 하여 런타임에서 읽을 수 있습니다. 서비스가 Azure에서 실행되는 동안 이 파일을 업데이트할 수 없습니다.
* **ServiceConfiguration.cscfg** 파일은 서비스 정의 파일에 정의된 구성 설정에 대한 값을 설정하고 각 역할에 대해 실행할 인스턴스 수를 지정합니다. 클라우드 서비스가 Azure에서 실행되는 동안 이 파일을 업데이트할 수 있습니다.

Azure Tools for Microsoft Visual Studio는 이러한 파일에 저장된 구성 설정을 설정하는 데 사용할 수 있는 속성 페이지를 제공합니다. 속성 페이지에 액세스하려면 솔루션 탐색기에서 Azure 클라우드 서비스 프로젝트 아래에 있는 역할 참조를 두 번 클릭하거나 역할 참조를 마우스 오른쪽 단추로 클릭하고 다음 그림에 나와 있는 것처럼 **속성**을 선택합니다.

![VS_Solution_Explorer_Roles_Properties](./media/vs-azure-tools-multiple-services-project-configurations/IC784076.png)

서비스 정의 및 서비스 구성 파일의 기본 스키마에 대한 정보는 [스키마 참조](https://msdn.microsoft.com/library/azure/dd179398.aspx)를 참조하세요. 서비스 구성에 대한 자세한 내용은 [클라우드 서비스 구성 방법](cloud-services/cloud-services-how-to-configure.md)을 참조하세요.

## <a name="configuring-role-properties"></a>역할 속성 구성
다음 섹션에서 지적된 대로 몇 가지 차이점이 있지만 웹 역할 및 작업자 역할에 대한 속성 페이지는 비슷합니다.

**캐싱** 페이지에서 Azure 캐싱 서비스를 구성할 수 있습니다.

### <a name="configuration-page"></a>구성 페이지
**구성** 페이지에서 다음 속성을 설정할 수 있습니다.

**인스턴스**

**인스턴스** 횟수 속성을 이 역할에 대해 서비스를 실행해야 하는 인스턴스 수로 설정합니다.

**VM 크기** 속성을 **매우 작음**, **작음**, **보통**, **큼** 또는 **매우 큼**으로 설정합니다.  자세한 내용은 [클라우드 서비스에 적합한 크기](cloud-services/cloud-services-sizes-specs.md)를 참조하세요.

**시작 작업** (웹 역할만)

디버깅을 시작할 때 Visual Studio가 HTTP 끝점 또는 HTTPS 끝점 또는 모두에 대한 웹 브라우저를 시작하도록 이 속성을 설정합니다.

HTTPS 끝점 옵션은 사용자의 역할에 대한 HTTPS 끝점을 이미 정의한 경우에만 사용할 수 있습니다. **끝점** 속성 페이지에서 HTTPS 끝점을 정의할 수 있습니다.

HTTPS 끝점을 이미 추가한 경우 HTTPS 끝점 옵션은 기본적으로 사용되고 HTTP 끝점에 대한 브라우저 외에도 디버깅을 시작하면 Visual Studio에서 이 끝점에 대한 브라우저를 시작합니다. 이는 두 시작 옵션이 설정되었다고 가정합니다.

**진단**

기본적으로 웹 역할에 대한 진단이 사용됩니다. Azure 클라우드 서비스 프로젝트 및 저장소 계정은 로컬 저장소 에뮬레이터를 사용하도록 설정됩니다. Azure에 배포할 준비가 되면 작성기 단추(**...**)를 선택하여 클라우드에서 Azure 저장소를 사용하도록 저장소 계정을 업데이트할 수 있습니다. 요청 시 또는 자동으로 예약된 간격으로 저장소 계정에 진단 데이터를 전송할 수 있습니다. Azure 진단에 대한 자세한 내용은 [Azure Cloud Services 및 Virtual Machines에서 진단 사용](cloud-services/cloud-services-dotnet-diagnostics.md)을 참조하세요.

## <a name="settings-page"></a>설정 페이지
**설정** 페이지에서 서비스에 대한 구성 설정을 추가할 수 있습니다. 구성 설정은 이름-값 쌍입니다. 역할에서 실행되는 코드는 [Azure 관리 라이브러리](http://go.microsoft.com/fwlink?LinkID=171026)에서 제공하는 클래스를 사용하여 런타임에서 구성 설정값을 읽을 수 있습니다. 특히 [GetConfigurationSettingValue](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleenvironment.getconfigurationsettingvalue.aspx) 메서드는 런타임에 명명된 구성 설정값을 반환합니다.

### <a name="configuring-a-connection-string-to-a-storage-account"></a>저장소 계정에 연결 문자열 구성
연결 문자열은 저장소 에뮬레이터 또는 Azure 저장소 계정에 대한 연결 및 인증 정보를 제공 하는 구성 설정입니다. 코드가 역할에서 실행 중인 코드에서 Azure 저장소 서비스 데이터 – 즉, blob, 큐 또는 테이블 데이터 –에 액세스해야 할 때마다 해당 저장소 계정에 대한 연결 문자열을 정의해야 합니다.

Azure 저장소 계정을 가리키는 연결 문자열은 정의된 형식을 사용해야 합니다. 연결 문자열을 만드는 방법에 대한 자세한 내용은 [Azure Storage 연결 문자열 구성](storage/common/storage-configure-connection-string.md)을 참조하세요.

Azure 저장소 서비스에 대해 서비스를 테스트할 준비가 되었거나 Azure에 클라우드 서비스를 배포할 준비가 되었으면 Azure 저장소 계정을 가리키도록 연결 문자열의 값을 변경할 수 있습니다. (**...**)를 선택하고 **저장소 계정 자격 증명 입력**을 선택합니다. 계정 이름 및 계정 키를 포함한 계정 정보를 입력합니다. **저장소 계정 연결 문자열** 대화 상자에서 기본 HTTPS 끝점(기본 옵션), 기본 HTTP 끝점 또는 사용자 지정 끝점 사용 여부를 나타낼 수도 있습니다. [Azure 저장소 계정에서 Blob 데이터에 대한 사용자 지정 도메인 이름 구성](storage/blobs/storage-custom-domain-name.md)에 설명된 대로 서비스에 사용자 지정 도메인 이름을 등록한 경우 사용자 지정 끝점을 사용하도록 결정할 수 있습니다.

> [!IMPORTANT]
> 서비스를 배포하기 전에 Azure 저장소 계정을 가리키도록 연결 문자열을 수정해야 합니다. 이 작업을 수행하지 못하면 사용자의 역할이 시작되지 않거나 초기화 중, 사용 중 및 상태 중지 중 간에 전환될 수 있습니다.
> 
> 

## <a name="endpoints-page"></a>끝점 페이지
작업자 역할에는 임의 개수의 HTTP, HTTPS 또는 TCP 끝점이 있을 수 있습니다. 끝점은 외부 클라이언트에 사용할 수 있는 입력된 끝점 또는 서비스에서 실행 되는 다른 역할에 사용할 수 있는 내부 끝점일 수 있습니다.

* 외부 클라이언트와 웹 브라우저에서 HTTP 끝점을 사용할 수 있도록 하려면 끝점 유형을 입력으로 변경하고 이름 및 공용 포트 번호를 지정합니다.
* 외부 클라이언트와 웹 브라우저에서 HTTP 끝점을 사용할 수 있도록 하려면 끝점 유형을 **입력**으로 변경하고 이름, 공용 포트 번호 및 관리 인증서 이름을 지정합니다.
  
    관리 인증서를 지정하려면 먼저 **인증서** 속성 페이지에 인증서를 정의해야 합니다.
* 클라우드 서비스의 다른 역할에서 내부 액세스에 대해 끝점을 사용할 수 있도록 하려면 끝점 유형을 내부로 변경하고 이 끝점에 대한 이름 및 가능한 개인 포트를 지정합니다.

## <a name="local-storage-page"></a>로컬 저장소 페이지
**로컬 저장소** 속성 페이지를 사용하여 역할에 대한 하나 이상의 로컬 저장소 리소스를 예약할 수 있습니다. 로컬 저장소 리소스는 역할의 인스턴스가 실행 중인 Azure 가상 컴퓨터의 파일 시스템에서 예약된 디렉터리입니다.

## <a name="certificates-page"></a>인증서 페이지
**인증서** 페이지에서 인증서와 역할을 연결할 수 있습니다. 추가하는 인증서는 **끝점** 속성 페이지에서 HTTPS 끝점을 구성하는 데 사용할 수 있습니다.

**인증서** 속성 페이지는 서비스 구성 인증서에 대한 정보를 추가합니다. 인증서는 서비스와 함께 패키징되지 않습니다. [Azure 클래식 포털](http://go.microsoft.com/fwlink/?LinkID=213885)을 통해 Azure에 별도로 인증서를 업로드해야 합니다.

인증서를 역할에 연결하려면 인증서에 대한 이름을 입력합니다. 이 이름을 사용하여 **끝점** 속성 페이지에서 HTTPS 끝점을 구성할 때 인증서를 참고합니다. 그런 다음 인증서 저장소가 **로컬 컴퓨터** 또는 **현재 사용자** 및 저장소의 이름인지를 지정합니다. 마지막으로, 인증서의 손도장을 입력합니다. 인증서가 현재 사용자\개인(나의) 저장소에 있는 경우 채워진 목록에서 인증서를 선택하여 인증서의 손도장을 입력할 수 있습니다. 다른 위치에 상주하는 경우 손도장 값을 직접 입력합니다.

인증서 저장소에서 인증서를 추가하면 모든 중간 인증서가 구성 설정에 자동으로 추가됩니다. 또한 이러한 중간 인증서는 SSL에 대한 서비스를 올바르게 구성하기 위해 Azure에 업로드해야 합니다.

서비스와 연결하는 모든 관리 인증서는 클라우드에서 실행되는 경우에만 서비스에 적용됩니다. 서비스는 로컬 개발 환경에서 실행 중인 경우에 계산 에뮬레이터에서 관리되는 표준 인증서를 사용합니다.

## <a name="configuring-the-azure-cloud-service-project"></a>Azure 클라우드 서비스 프로젝트 구성
전체 Azure 클라우드 서비스 프로젝트에 적용되는 설정을 구성하려면 먼저 해당 프로젝트 노드에 대한 바로 가기 메뉴를 열고 속성을 선택하여 해당 속성 페이지를 엽니다. 다음 표에서 이러한 속성 페이지를 보여줍니다.

| 속성 페이지 | 설명 |
| --- | --- |
| 응용 프로그램 |이 페이지에서 이 클라우드 서비스 프로젝트를 사용하는 Azure 도구의 버전에 대한 정보를 표시할 수 있으며 도구의 현재 버전으로 업그레이드할 수 있습니다. |
| 빌드 이벤트 |이 페이지에서 빌드 전 및 빌드 후 이벤트를 설정할 수 있습니다. |
| 개발 |이 페이지에서 빌드 구성 지침과 빌드 후 이벤트를 실행할 조건을 지정할 수 있습니다. |
| 웹 |이 페이지에서 웹 서버와 관련된 설정을 구성할 수 있습니다. |


