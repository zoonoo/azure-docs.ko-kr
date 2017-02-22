---
title: "Azure 역할 속성"
description: "Eclipse용 Azure 도구 키트를 사용하여 Azure 역할 설정을 구성하는 방법을 알아봅니다."
services: 
documentationcenter: java
author: rmcmurray
manager: erikre
editor: 
ms.assetid: 5c0ec412-5702-465a-8f47-87a8ce99a267
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: multiple
ms.devlang: Java
ms.topic: article
ms.date: 12/22/2016
ms.author: robmcm
translationtype: Human Translation
ms.sourcegitcommit: ff60ebaddd3a7888cee612f387bd0c50799496ac
ms.openlocfilehash: eb1f4c815618e866e683b3fe2e3adf93a151ff5a


---
# <a name="azure-role-properties"></a>Azure 역할 속성
Azure 역할에 대한 다양한 구성 설정은 Eclipse용 Azure 도구 키트 내에서 설정할 수 있습니다.

## <a name="configuring-azure-role-properties"></a>Azure 역할 속성 구성
Azure 역할 속성을 구성하려면 작업자 역할에 대한 속성 대화 상자를 통해 수행합니다. Eclipse의 프로젝트 탐색기에서 역할에 대한 상황에 맞는 메뉴를 열고 **Azure** 하위 메뉴를 선택합니다 (Eclipse 프로젝트 탐색기에서 역할이 보이지 않으면 프로젝트 탐색기에서 Azure 프로젝트를 확장합니다.)

![][ic789599]

**속성** 대화 상자에서 설정할 수 있는 다양한 속성은 이 항목에서 설명합니다. 새 Azure 배포 프로젝트를 만들 때 많은 속성이 자동으로 채워집니다.

다음 속성 페이지는 Azure 역할에 사용할 수 있습니다.

* [가상 컴퓨터 속성](#virtual_machine_properties)
* [캐싱 속성](#caching_properties)
* [인증서 속성](#certificates_properties)
* [구성 요소 속성](#components_properties)
* [디버깅 속성](#debugging_properties)
* [끝점 속성](#endpoints_properties)
* [환경 변수 속성](#environment_variables_properties)
* [부하 분산/세션 선호도(또는 "고정 세션") 속성](#session_affinity_properties)
* [로컬 저장소 속성](#local_storage_properties)
* [서버 구성 속성](#server_configuration_properties)
* [SSL 오프로딩 속성](#ssl_offloading_properties)

<a name="virtual_machine_properties"></a>

### <a name="virtual-machine-properties"></a>가상 컴퓨터 속성
Eclipse의 프로젝트 탐색기 창에서 역할에 대해 상황에 맞는 메뉴를 열고 **Azure**를 클릭한 다음 **속성**을 클릭합니다. 다음 그림에 나와 있는 것처럼 가상 컴퓨터 크기를 변경하고 인스턴스 개수를 변경할 수 있습니다.

![][ic719499]

> [!NOTE]
> Windows에만 해당: 인스턴스 수를 1보다 큰 값으로 설정하고 또한 응용 프로그램 서버를 구성하는 경우 도구 키트는 설정에 관계 없이 1개의 역할 인스턴스만을 허용하여 에뮬레이터에서 실행합니다.  이 동일한 컴퓨터에서 실행될 때 다른 서버 인스턴스 간의 포트 바인딩 충돌을 방지하려는 것입니다.(예를 들어 모두 포트 8080에 바인딩을 시도) 원하는 인스턴스 개수 설정은 유지되지만 클라우드로 배포하는 경우에만 적용됩니다.
> 
> 

<a name="caching_properties"></a> 

### <a name="caching-properties"></a>캐싱 속성
Eclipse의 프로젝트 탐색기에서 역할에 대한 상황에 맞는 메뉴를 열고 **Azure**를 클릭한 다음 **캐싱**을 클릭합니다. 이 대화 상자에서 명명된 memcache와 호환 가능한 캐시를 사용할 수 있으며 이는 웹 응용 프로그램 속도를 높일 수 있습니다.

![][ic719483]

**캐싱** 속성 페이지 내에서 다음에 대한 전역 설정을 지정할 수 있습니다.

* 배치된 캐싱의 사용 여부.
* 메모리의 백분율로 나타낸 캐시 크기.
* 응용 프로그램이 클라우드 서비스로 실행될 때 캐시 상태를 저장하기 위한 저장소 계정 이름 또는 캐시 상태를 저장하지 않으려면 해당 없음. (계산 에뮬레이터에서 응용 프로그램을 실행하는 경우 저장소 계정 이름은 사용되지 않습니다.) 저장소 계정 이름을 **(자동)**(기본값)으로 설정하는 경우 캐싱 구성은 **Azure에 게시** 대화 상자에서 선택한 것과 동일한 저장소 계정을 자동으로 사용합니다.

> [!NOTE]
> **(자동)** 설정은 Eclipse 도구 키트의 게시 마법사를 사용하여 배포를 게시하는 경우에만 원하는 효과를 가집니다. 대신 수동으로 [Azure Management Portal][Azure Management Portal]과 같은 외부 메커니즘을 사용하는 .cspkg 파일을 게시하는 경우 배포가 제대로 작동하지 않습니다.
> 
> 

다음 대화 상자는 캐시에 대한 속성을 보여줍니다.

![][ic719501]

* **이름:** 배치된 캐시의 이름입니다.
* **포트 번호:** 캐시에 사용할 포트 번호입니다.
* **만료 정책:** 캐시의 키가 만료되는 시기를 지정하는 다음 값 중 하나입니다.
  * **절대:** **Minutes to live**에서 지정된 시간에 도달하면 키가 만료됩니다.
  * **NeverExpires:** 키에 만료 시간이 없습니다.
  * **SlidingWindow:** **Minutes to live**에서 지정한 시간 동안 액세스 하지 않은 경우 키가 만료됩니다. 액세스할 때 마다 만료 시계가 다시 설정됩니다.
* **Minutes to live:** memcached 키가 유효한 최대 시간(분)은 만료 정책에 따라 결정됩니다.
* **다른 역할 인스턴스에서 복제된 백업을 통한 고가용성:** 사용하도록 설정하면 다른 역할 인스턴스에서 복제된 백업을 사용하여 고가용성 제공하도록 합니다. 이 기능이 작동하기 위해 두 개 이상의 역할 인스턴스가 배포에 적용되어야 합니다.

새 캐시를 추가하려면 **캐싱** 속성 페이지에서 **추가** 단추를 클릭하고 **명명된 캐시 구성** 대화 상자가 열립니다. 위에서 설명하는 속성에 대한 값을 제공합니다.

명명된 캐시를 수정하려면 캐시를 선택하고 **캐싱** 속성 페이지에서 **편집** 단추를 클릭합니다. 캐시 속성을 수정할 수 있게 대화 상자가 열립니다. **확인** 을 눌러서 캐시 값을 저장합니다.

캐시를 삭제하려면 캐시를 선택하고 **캐싱** 속성 페이지에서 **제거** 단추를 클릭한 다음 **예**를 클릭하여 삭제를 확인합니다.

캐싱을 사용하는 방법에 대한 자세한 내용은 [공동 배치된 캐싱을 사용하는 방법][How to Use Co-located Caching]을 참조하세요.

<a name="certificates_properties"></a> 

### <a name="certificates-properties"></a>인증서 속성
Eclipse의 프로젝트 탐색기에서 역할에 대한 상황에 맞는 메뉴를 열고 **Azure**를 클릭한 다음 **인증서**를 클릭합니다.

![][ic710964]

이 대화 상자에서는 Eclipse 프로젝트에서 참조하는 인증서를 추가하거나 제거할 수 있습니다. 여기에 나열된 인증서는 Java keystore 안에 자동으로 저장되지 않으며 따라서 Java 응용 프로그램 내에서 자동으로 사용할 수 없습니다. Azure로 등록되므로 배포를 실행하고 이어서 다른 Windows 소프트웨어에 의해 사용되는 가상 컴퓨터에서 Windows 인증서 저장소에 미리 로드할 수 있습니다. 현재 **인증서** 대화 상자에서 이런 방식으로 참조되는 인증서를 사용하는 도구 키트의 유일한 기능은 [SSL 오프 로딩][SSL Offloading]입니다. 이는 인터넷 정보 서비스(IIS) 및 응용 프로그램 요청 라우팅(ARR)에 의존하기 때문에 이 방식으로 사용할 수 있도록 적절한 인증서가 필요합니다.

게시 마법사를 사용하여 Azure에 프로젝트를 배포할 때 이전에 업로드 되지 않은 경우 Azure 서비스에 자동으로 업로드하기 위해 암호와 함께 이러한 인증서에 해당하는 개인 정보 교환(PFX) 파일을 가리키도록 묻는 메시지가 나타납니다.

<a name="components_properties"></a> 

### <a name="components-properties"></a>구성 요소 속성
Eclipse의 프로젝트 탐색기에서 역할에 대한 상황에 맞는 메뉴를 열고 **Azure**를 클릭한 다음 **구성 요소**를 클릭합니다. 이 대화 상자에서 역할의 구성 요소를 추가, 수정 또는 제거할 뿐만 아니라 처리되는 순서를 변경하는 기능이 있습니다.

![][ic719502]

구성 요소 기능을 사용하면 Java 응용 프로그램 프로젝트, 특수한 파일 및 배포에 필요한 실행 가능한 명령줄 문 등 Azure 배포 프로젝트에 종속성을 추가할 수 있습니다.

각 구성 요소에 다음을 지정할 수 있습니다.

* 작성 시 Azure 배포 프로젝트에 구성 요소를 가져오려면 수행해야 하는 단계.
* Azure 클라우드에서 구성 요소를 배포할 때 수행해야 하는 단계.

> [!NOTE]
> 구성 요소 파일 또는 명령줄을 지정할 때 배포가 Windows 가상 컴퓨터에 게시되므로 사용자 지정 단계는 Windows 기반 운영 체제에 적합해야 한다는 점에 유의합니다. 
> 
> 

구성 요소에는 다음과 같은 속성이 있습니다.

* **가져오기:** 프로젝트를 빌드할 때 구성 요소를 프로젝트로 가져오는 방법을 나타내는 메서드입니다. 다음 값 중 하나일 수 있습니다.
  * **copy:** 구성 요소는 **From** 속성으로 지정된 로컬 경로에서 역할의 **approot** 디렉터리에 복사됩니다.
  * **EAR:** 구성 요소는 **From** 속성이 지정한 로컬 경로에 엔터프라이즈 응용 프로그램 프로젝트에서 가져오는 Java 엔터프라이즈 아카이브(EAR)입니다. (도구 키트에서 해당 위치에 있는 프로젝트의 특성을 기준으로 자동으로 검색합니다.)
  * **JAR:** 구성 요소는 Java 웹 아카이브(JAR)이고 Java 프로젝트에 **From** 속성이 지정한 로컬 경로에 가져옵니다. (도구 키트에서 해당 위치에 있는 프로젝트의 특성을 기준으로 자동으로 검색합니다.)
  * **없음:** 구성 요소를 가져오기 위해 어떤 작업도 수행하지 않습니다. 구성 요소가 역할의 **approot** 디렉터리에 이미 있다고 가정할 때 또는 **Deploy** 메서드가 **exec**일 경우 구성 요소가 단순히 **As** 속성에서 지정된 대로 실행 가능한 명령줄 문일 때 적용할 수 있습니다.
  * **WAR:** 구성 요소는 Java 웹 응용 프로그램 보관 파일(WAR)이고 **From** 속성이 지정한 로컬 경로에 동적 웹 프로젝트에서 가져옵니다. (도구 키트에서 해당 위치에 있는 프로젝트의 특성을 기준으로 자동으로 검색합니다.)
  * **zip:** 구성 요소는 zip 파일이고 **From** 속성에 지정된 디렉터리 또는 파일을 압축하여 가져옵니다.
* **From:** 배포에 가져올 항목을 나타내는 폴더 또는 파일에 대한 로컬 컴퓨터의 원본 경로입니다. 이 속성에 Windows 환경 변수를 사용할 수 있습니다. 가져올 수 있는 모든 구성 요소는 프로젝트를 빌드할 때 역할의 **approot** 디렉터리로 가져옵니다.
  
    (계산 에뮬레이터가 아닌) 클라우드로 배포할 때 다운로드에서 구성 요소를 배포하는 기능이 있습니다. 구성 요소를 추가에 대해 아래의 관련된 정보를 참조하세요.    
* **As:** 역할의 **approot** 디렉터리로 가져오고 궁극적으로 Azure 클라우드에서 배포할 구성 요소의 파일 이름입니다. 로컬 컴퓨터에서와 동일한 이름을 유지하려면 이 속성을 비워 둡니다. (즉, **Deploy** 메서드가 **exec**로 설정된 실행 가능한 구성 요소의 경우 임의의 Windows 명령줄 문일 수 있습니다.)
  
  > [!IMPORTANT]
  > 이 값에 공백 문자를 사용하는 경우 배포 방법에 따라 다르게 처리됩니다. 배포 방법이 **exec**인 경우 공백은 파일 이름의 일부가 아니라 명령줄 인수 구분 기호로 해석됩니다. 다른 모든 배포 방법의 경우 공백은 파일 이름의 일부로 해석됩니다.
  > 
  > 
* **배포:** 배포가 시작될 때 구성 요소에 적용되는 동작을 나타내는 방법입니다. 다음 값 중 하나일 수 있습니다.
  
  * **copy:** 구성 요소가 **To** 속성으로 지정된 대상 경로에 복사됩니다.
  * **exec:** 구성 요소가 배포가 시작 될 때 **To** 속성에서 지정한 경로의 컨텍스트에서 실행된 실행 가능한 Windows 명령줄 문입니다.
  * **없음:** 배포가 시작될 때 어떤 작업도 구성 요소에 적용되지 않습니다.
  * **zip:** 구성 요소가 **To** 속성으로 지정된 대상 경로에 압축이 풀립니다. 이 메서드는 **Import** 속성이 **zip**인 경우에만 사용할 수 있습니다.
* **To:** 구성 요소를 배포할 가상 컴퓨터에서 대상 경로입니다. Windows 환경 변수는 이 속성에 사용할 수 있고 파일 경로는 **approot**에 상대적입니다.

새 구성 요소를 추가하려면 **구성 요소** 속성 페이지에서 **추가** 단추를 클릭하고 **Azure 역할 구성 요소** 대화 상자가 열립니다. 위에서 설명하는 속성에 대한 값을 제공합니다. 

다음 새로운 WAR 구성 요소를 추가하는 예를 보여줍니다.

![][ic719503]

(계산 에뮬레이터가 아닌) 클라우드로 배포할 때 다운로드에서 구성 요소를 배포하려는 경우 **패키지에 포함하는 대신 클라우드에 위치할 때 다음에서 배포** 를 선택해야 합니다. Azure Storage 계정에서 다운로드하려는 경우 **저장소 계정** 드롭다운 목록에서 저장소 계정을 선택(**계정** 링크를 클릭하여 목록의 내용을 수정할 수 있습니다.)하며 이는 부분적으로 **URL** 필드를 입력하고 URL의 나머지 부분을 입력합니다. Azure Storage를 사용하려면 **저장소 계정** 드롭다운 목록에서 **(없음)**을 선택하고 **URL** 필드에서 구성 요소에 URL을 입력합니다. 다음 방법 중 하나를 지정합니다.

* **copy:** 다운로드 구성 요소가 **To Directory** 경로로 지정된 대상 경로에 복사됩니다.
* **same:** 동일한 방법이 **다운로드 패키지에서 배포**처럼 **다운로드에서 배포**에 사용되었습니다.
* **zip:** 다운로드 구성 요소가 **To Directory** 경로로 지정된 대상 경로에 압축을 풉니다.

구성 요소를 수정하려면 구성 요소를 선택하고 **구성 요소** 속성 페이지에서 **편집** 단추를 클릭합니다. 구성 요소 속성을 수정할 수 있게 대화 상자가 열립니다. **확인** 을 눌러서 구성 요소 값을 저장합니다.

구성 요소를 삭제하려면 구성 요소를 선택하고 **구성 요소** 속성 페이지에서 **제거** 단추를 클릭한 다음 **예**를 클릭하여 삭제를 확인합니다.

구성 요소는 나열된 순서에 따라 처리됩니다. **위로 이동** 및 **아래로 이동** 단추를 사용하여 순서를 정렬합니다.

> [!NOTE]
> 또한 서버 구성 기능은 구성 요소에 의존합니다. 이러한 구성 요소는 해당하는 서버 구성을 제거하지 않고 제거되거나 편집할 수 없습니다. 이러한 구성 요소를 변경하려고 할 때 해당 사항에 대한 메시지가 표시됩니다.
> 
> 

<a name="debugging_properties"></a> 

### <a name="debugging-properties"></a>디버깅 속성
Eclipse의 프로젝트 탐색기에서 역할에 대한 상황에 맞는 메뉴를 열고 **Azure**를 클릭한 다음 **디버깅**을 클릭합니다. 이 대화 상자에서 원격 디버깅을 사용하거나 사용하지 않도록 설정할 뿐만 아니라 다음 그림에서 보여준 것처럼 디버그 구성을 만드는 기능이 있습니다.

![][ic719504]

디버깅에 관련된 내용은 [Eclipse에서 Azure 응용 프로그램 디버깅][Debugging Azure Applications in Eclipse]을 참조하세요.

<a name="endpoints_properties"></a> 

### <a name="endpoints-properties"></a>끝점 속성
Eclipse의 프로젝트 탐색기에서 역할에 대한 상황에 맞는 메뉴를 열고 **Azure**를 클릭한 다음 **끝점**을 클릭합니다. 이 대화 상자 내에서 다음 그림에서 보이는 대로 끝점을 만들 뿐만 아니라 끝점을 편집하거나 제거할 수 있습니다.

![][ic719505]

끝점을 추가하려면 **끝점** 속성 페이지에서 **추가** 단추를 클릭하고 **끝점 추가** 대화 상자가 열립니다.

![][ic710897]

끝점의 이름을 입력하고 형식(**Input**, **Internal** 또는 **InstanceInput** 중 하나)을 선택하며 공용 및 개인 포트를 지정합니다. **확인** 을 눌러 새 끝점 값을 저장합니다.

끝점의 형식에 따라 다음과 같이 포트 범위를 사용할 수 있습니다.

* 입력 인스턴스 끝점의 경우 공용 포트는 포트의 범위일 수 있고(예를 들어 **2000-2010**) 개인 포트는 고정된 값입니다.
* 내부 끝점의 경우 공용 포트를 사용하지 않고 개인 포트는 범위이거나 비어 있거나 별표로 설정되어 Azure에서 자동으로 설정된다는 것을 나타냅니다.
* 입력 끝점의 경우 공용 포트는 고정된 값일 수 있고 개인 포트는 고정된 값일 수 있고 비어 있거나 별표로 설정되어 Azure에서 자동으로 설정된다는 것을 나타냅니다.

범위 대신 단일 포트 번호를 사용하려는 경우 범위 빈 값의 끝에 텍스트 상자를 둡니다.

자동으로 설정된 포트의 경우 런타임 중에 실제로 사용되는 포트를 결정해야 하면 응용 프로그램이 Azure 서비스 런타임 API를 사용할 수 있으며 이는 [com.microsoft.windowsazure.serviceruntime 패키지 요약][com.microsoft.windowsazure.serviceruntime package summary]에서 설명합니다.

다중 인스턴스 배포를 디버깅하는 데 도움이 되도록 인스턴스 입력 끝점을 사용할 수 있는 방법을 보려면 [다중 인스턴스 배포의 특정 역할 인스턴스 디버깅][Debugging a specific role instance in a multi-instance deployment]을 참조하세요.

끝점을 수정하려면 끝점을 선택하고 **끝점** 속성 페이지에서 **편집** 단추를 클릭합니다. 끝점 이름, 형식 및 공용과 개인 포트를 수정할 수 있는 대화 상자가 열립니다. **확인** 을 눌러 수정된 끝점 값을 저장합니다.

끝점을 삭제하려면 끝점을 선택하고 **끝점** 속성 페이지에서 **제거** 단추를 클릭한 다음 **예**를 클릭하여 삭제를 확인합니다.

역할에서 사용자가 활성화한 일부 기능(예: 캐싱, 원격 디버깅, 세션 선호도 또는 SSL 오프로딩)을 제대로 구성하기 위해 도구 키트는 사용자 정의 끝점과 함께 나열될 특수한 끝점을 자동으로 구성할 수 있습니다. 도구 키트는 관련된 기능이 사용되는 한 사용자가 이렇게 자동으로 생성된 끝점을 편집하거나 삭제하지 못하도록 방지합니다.

<a name="environment_variables_properties"></a> 

### <a name="environment-variables-properties"></a>환경 변수 속성
Eclipse의 프로젝트 탐색기에서 역할에 대한 상황에 맞는 메뉴를 열고 **Azure**를 클릭한 다음 **환경 변수**를 클릭합니다. 이 대화 상자 내에서 다음 그림에서 보이는 대로 환경 변수를 만들 뿐만 아니라 환경 변수를 수정하거나 제거할 수 있습니다.

![][ic719506]

환경 변수는 역할이 시작할 때 시작 스크립트에 사용할 수 있습니다.

> [!NOTE]
> 환경 변수를 지정할 때 배포가 Windows 가상 컴퓨터에 게시되므로 환경 변수는 Windows 기반 운영 체제에 적합해야 한다는 점에 유의합니다.
> 
> 

역할 시작 시 사용 가능한 환경 변수의 예로 **추가** 단추를 클릭하여 새 환경 변수를 만듭니다. 다음은 **1.0** 값을 만들고 할당하는 **MyRoleVersion**이라는 환경 변수를 보여줍니다.

![][ic659268]

jsp 코드 내에서 `System.getenv` 메서드를 사용하여 값을 표시할 수 있습니다.

    <body>
      <b> Hello World!</b>
      <p>Running role version: <%= System.getenv("MyRoleVersion") %></p>
    </body>

응용 프로그램을 실행할 때 이 출력의 결과는 다음과 같습니다.

![][ic552233]

환경 변수를 수정하려면 환경 변수를 선택하고 **환경 변수** 속성 페이지에서 **편집** 단추를 클릭합니다. 환경 변수 속성을 수정할 수 있게 대화 상자가 열립니다. **확인** 을 눌러 환경 변수 값을 저장합니다.

환경 변수를 삭제하려면 환경 변수를 선택하고 **환경 변수** 속성 페이지에서 **제거** 단추를 클릭한 다음 **예**를 클릭하여 삭제를 확인합니다.

역할에서 사용자가 활성화한 일부 기능(예: 서버 구성, 원격 디버깅 또는 로컬 저장소)을 제대로 구성하기 위해 도구 키트는 사용자 정의 환경 변수와 함께 나열될 특수 환경 변수를 자동으로 구성할 수 있습니다. 도구 키트는 관련된 기능이 사용되는 한 사용자가 이렇게 자동으로 생성된 환경 변수를 편집하거나 삭제하지 못하도록 방지합니다.

<a name="session_affinity_properties"></a> 

### <a name="load-balancing--session-affinity-aka-sticky-sessions-properties"></a>부하 분산/세션 선호도(또는 "고정 세션") 속성
Eclipse의 프로젝트 탐색기에서 역할에 대한 상황에 맞는 메뉴를 열고 **Azure**를 클릭한 다음 **부하 분산**을 클릭합니다. 이 대화 상자에서 다음 그림에서 보여주듯 세션 선호도를 사용하거나 사용하지 않는 기능이 있습니다.

![][ic719492]

관련된 내용은 [세션 선호도][Session Affinity]를 참조하세요. 또한 [SSL 오프로딩][SSL Offloading]에서 설명한 대로 SSL 오프로딩의 컨텍스트에서이 기능의 동작에 유의합니다.

<a name="local_storage_properties"></a> 

### <a name="local-storage-properties"></a>로컬 저장소 속성
Eclipse의 프로젝트 탐색기에서 역할에 대한 상황에 맞는 메뉴를 열고 **Azure**를 클릭한 다음 **로컬 저장소**를 클릭합니다. 이 대화 상자 내에서 응용 프로그램을 실행하는 가상 컴퓨터에 대한 임시 로컬 저장소를 만들기, 수정 또는 제거하는 기능이 있습니다. 특정 값은 로컬 저장소의 크기를 설정할 수 있을 뿐만 아니라 다음 그림에서 보이는 것처럼 역할이 재활용될 때 내용이 보존되는지 여부를 설정할 수 있습니다.

![][ic719508]

또한 필요에 따라 로컬 저장소에 해당하는 환경 변수를 지정할 수 있습니다.

기본적으로 Azure에 배포하는 모든 항목은 역할 인스턴스의 **approot** 폴더에 배치(및 압축을 풀게)됩니다. 압축을 푼 후에도 대부분 간단한 배포의 크기가 맞는 반면 **approot** 디렉터리에 할당된 공간은 제한되고 잘 정의되지 않습니다. 1GB 보다 작은 점은 합리적인 좋은 방법입니다. 따라서 Azure에서 **approot** 폴더에 맞지 않는 대형 배포에 충분한 디스크 공간을 할당하려면 **로컬 저장소** 대화 상자를 사용하여 로컬 저장소 리소스를 설정해야 합니다. 이 작업을 수행하는 간단한 방법은 [대규모 배포 배포][Deploying Large Deployments]를 참조하세요.

**로컬 저장소** 대화 상자에 표시된 대로 리소스를 통해 Eclipse 도구 키트에서 자동으로 연결된 환경 변수를 사용하여 시작 스크립트에서 저장소 리소스를 쉽게 참조할 수 있습니다.(예: **startup.cmd**) 이 환경 변수는 시작 스크립트를 실행할 때 구성한 로컬 리소스의 전체 경로를 포함합니다. 

로컬 저장소 리소스를 수정하려면 로컬 저장소 리소스를 선택하고 **로컬 저장소** 속성 페이지에서 **편집** 단추를 클릭합니다. 로컬 저장소 리소스 속성을 수정할 수 있게 대화 상자가 열립니다. **확인** 을 눌러 로컬 저장소 리소스 값을 저장합니다.

로컬 저장소 리소스를 삭제하려면 로컬 저장소 리소스를 선택하고 **로컬 저장소** 속성 페이지에서 **제거** 단추를 클릭한 다음 **예**를 클릭하여 삭제를 확인합니다.

<a name="server_configuration_properties"></a> 

### <a name="server-configuration-properties"></a>서버 구성 속성
Eclipse의 프로젝트 탐색기에서 역할에 대한 상황에 맞는 메뉴를 열고 **Azure**를 클릭한 다음 **서버 구성**을 클릭합니다. 이 대화 상자에서 배포에 사용되는 JDK 및 Java 응용 프로그램 서버를 추가, 제거 및 수정하는 기능 뿐만 아니라 배포에서 사용하는 응용 프로그램(예: WAR, JAR 또는 EAR 파일)을 추가 또는 제거하는 기능이 있습니다.

### <a name="jdk-configuration"></a>JDK 구성
이 대화 상자를 사용하면 배포에 사용할 JDK 패키지를 지정할 수 있습니다. Windows에서 Eclipse를 사용하는 경우 Azure 에뮬레이터에서 실행할 때 로컬로 사용할 JDK 패키지를 지정할 수 있고 로컬 설치 프로그램을 Azure에 배포하는 옵션이 있습니다. 비 Windows 운영 체제에서 에뮬레이터 JDK 설정이 적용되지 않고 Windows와 호환되지 않으므로 로컬에 설치된 JDK를 배포할 수 없습니다. 그러나 사용 중인 운영 체제에 관계 없이 항상 Azure에 배포할 타사 JDK 패키지를 선택하거나 대체 다운로드 위치에서 사용자 고유의 Windows 호환 가능 JDK 패키지를 가리킬 수 있습니다.

다음은 Windows에서 JDK를 지정하는 방법의 예입니다.

![][ic780647]

Windows에서 Eclipse를 사용하는 경우 계산 에뮬레이터와 함께 사용할 JDK를 지정할 수 있습니다. 이렇게 하려면 **에뮬레이터 배포** 섹션에서 **로컬로 테스트하기 위해 이 파일 경로에서 JDK 사용**을 선택합니다. 그런 다음 JDK의 로컬 경로를 지정합니다. 사용하려는 JDK가 자동으로 선택되지 않은 경우 다른 JDK를 찾아볼 수 있습니다. 또한 Azure 클라우드 서비스에 JDK를 배포 하는 옵션도 있습니다. 이렇게 하려면 **클라우드 배포** 섹션에서 **내 로컬 JDK 배포(클라우드 저장소에 자동 업로드)** 옵션을 선택합니다.

참고: 비 Windows 운영 체제에서 **에뮬레이터 배포** 설정 및 **내 로컬 JDK 배포** 옵션은 사용할 수 없습니다. 다음 예제에서는 Mac 또는 다른 지원되는 비 Windows 운영 체제에서 JDK를 지정하는 것을 보여줍니다.

![][ic789643]

사용하는 운영 체제에 관계 없이 JDK 패키지의 원본 및 형식에 다음과 같은 두 가지 **클라우드 배포** 옵션이 있습니다.

* **Azure에서 사용할 수 있는 타사 JDK 패키지 배포** 
* **사용자 지정 다운로드에서 배포** 

**Azure에서 사용할 수 있는 타사 JDK 패키지 배포** 옵션을 사용하는 경우

1. **Azure에서 사용할 수 있는 타사 JDK 패키지 배포**라는 확인란을 선택합니다.
2. 드롭다운 목록에서 Azure에서 사용할 수 있는 타사 JDK 패키지를 선택합니다.
3. **JDK** 탭은 Windows에서 다음과 유사합니다.  ![][ic780648]
    그리고 Mac OS 또는 다른 지원되는 비 Windows 운영 체제에서 다음과 유사합니다.  ![][ic789643]
4. **확인** 을 클릭하여 변경 내용을 저장합니다.
5. 타사 JDK 패키지 공급자에서 라이선스 규약에 동의하는지 묻는 메시지가 나타나면 라이선스 약관을 검토합니다. 약관에 동의한 것으로 가정하면 **예**를 클릭하여 **라이선스 규약에 동의** 대화 상자를 닫습니다.
    항목이 **Azure에서 사용할 수 있는 타사 JDK 패키지 배포** 옵션에 대한 드롭다운 목록에 표시되는 기본적인 논리는 사용자 지정할 수 있습니다. 항목을 사용자 지정하려면 **JDK** 대화 상자에서 **사용자 지정** 링크를 클릭합니다. **JDK** 속성 페이지를 닫고 Eclipse에서 **componentsets.xml** 파일을 열어 필요에 따라 수정할 수 있습니다. **componentsets.xml**에 대한 설명서는 **componentsets.xml** 파일 자체에 포함됩니다.

**사용자 지정 다운로드에서 JDK 배포** 옵션을 사용하는 경우

1. JDK 설치 디렉터리의 ZIP을 만들어서 디렉터리 노드 자체가 콘텐츠가 아닌 ZIP 구조의 하위가 되도록 합니다. 나중에 필요하기 때문에 디렉터리의 이름을 기록하고 이 JDK 설치가 Windows 가상 컴퓨터에 배포된다는 점에 유의합니다.
2. ZIP을 Azure 저장소 계정에 Blob로 업로드합니다. Azure 저장소에 Blob를 업로드하는 경우 외부에서 사용 가능한 도구를 사용하여 이 작업을 수행할 수 있습니다. 개인 Blob을 사용하는 것이 좋습니다. ZIP 내용의 Blob URL을 기록해 둡니다.
3. **사용자 지정 다운로드에서 JDK 배포**라는 확인란을 선택합니다.
    Azure Storage 계정에서 다운로드하려는 경우 **저장소 계정** 드롭다운 목록에서 저장소 계정을 선택(**계정** 링크를 클릭하여 목록의 내용을 수정할 수 있습니다.)하며 이는 부분적으로 **URL** 필드를 입력하고 URL의 나머지 부분을 입력합니다. Azure Storage를 사용하려면 **저장소 계정** 드롭 다운 목록에서 **(없음)**을 선택하고 **URL** 필드에서 JDK 다운로드에 URL을 입력합니다. Azure 저장소를 사용하는 경우 URL의 Blob 이름은 소문자여야 합니다.
4. **JAVA_HOME** 텍스트 상자가 올바른 디렉터리 이름을 참조하도록 합니다. 기본적으로 동일한 JDK 디렉터리 이름을 로컬 사용을 위해 선택한 값으로 참조합니다. 그러나 ZIP에 포함된 디렉터리에 다른 이름이 있는 경우(예를 들어 다른 버전 사용으로 인해) 이 설정이 (계산 에뮬레이터가 아닌) 클라우드에서 사용되기 때문에 그에 따라 **JAVA_HOME** 텍스트 상자의 디렉터리 이름을 업데이트합니다.
5. **확인** 을 클릭하여 변경 내용을 저장합니다.

이것으로 끝입니다. 이제 클라우드를 작성하면 패키지 크기가 훨씬 더 작지만 빌드 프로세스는 일반적으로 시간이 적게 걸려야 하고 클라우드에 게시할 때 배포 자체도 시간이 적게 소요되어야 합니다. **내 로컬 JDK 배포(클라우드 저장소에 자동 업로드)** 또는 **사용자 지정 다운로드에서 JDK 배포** 옵션은 응용 프로그램을 클라우드에 배포하는 경우에만 적용됩니다. 계산 에뮬레이터 환경에 영향을 주지 않습니다. 계산 에뮬레이터에 배포하는 경우 여전히 로컬 버전의 구성이 사용됩니다. 

### <a name="server-configuration"></a>서버 구성
다음은 응용 프로그램 서버를 지정할 수 있는 방법의 예입니다.

![][ic796926]

**이 형식의 서버 배포** 확인란을 선택한 다음 사용하려는 응용 프로그램 서버의 형식을 선택하는지 확인합니다.

클라우드 배포에 사용할 서버를 지정하는 경우 다음과 같은 옵션의 장점을 활용할 수 있습니다.

1. **Azure에서 사용할 수 있는 타사 서버 배포** - 배포 효율성 및 단순성이 중요하고 서버가 사용자 지정 구성을 요구하지 않는 개발/테스트 시나리오에서 특히 적합합니다. 또는 시작 지점으로 이러한 서버 중 하나를 사용할 때 배포의 시작 논리에 적절한 서버 사용자 지정 단계를 포함합니다.
2. **사용자 지정 다운로드에서 배포** - 클라우드에서 사용하려는 특별히 준비되고 구성된 서버가 있는 경우 프로덕션 시나리오에서 특히 적합합니다.
3. **내 로컬 서버 설치 배포** - 이 로컬 서버 설치가 이미 사용할 사용자 지정으로 구성된 경우 특히 적합합니다. 또한 이 옵션을 선택하는 경우 아래에서 **로컬 서버 경로** 텍스트 상자에 로컬 서버 경로를 지정해야 합니다.

**Azure에서 사용할 수 있는 타사 서버 배포** 옵션을 사용하는 경우

1. **Azure에서 사용할 수 있는 타사 서버 배포**라는 확인란을 선택합니다.
2. 드롭다운 메뉴에서 클라우드의 배포를 통해 사용하려는 서버 소프트웨어를 선택합니다. 이전에 사용한 서버 형식을 지정하는 경우 해당 서버 형식과 같은 제품군에 있는 클라우드 서버만 선택할 수 있도록 제한됩니다. 하지만 서버 형식을 선택하지 않은 경우 Azure에서 현재 사용할 수 있는 서버 중에서 선택할 수 있고 서버 형식을 자동으로 선택합니다.
3. **확인** 을 클릭하여 변경 내용을 저장합니다.

**사용자 지정 다운로드에서 배포** 옵션을 사용하는 경우

1. 앞의 단계에 따라 서버 형식을 이미 선택했는지 확인합니다. 선택한 서버 형식과 같은 제품군에서처럼 플러그인에 사용자 지정 다운로드에서 서버를 배포하는 방법을 설명합니다.
2. **사용자 지정 다운로드에서 배포**을 클릭합니다.
    Azure Storage 계정에서 다운로드하려는 경우 **저장소 계정** 드롭다운 목록에서 저장소 계정을 선택(**계정** 링크를 클릭하여 목록의 내용을 수정할 수 있습니다.)하며 이는 부분적으로 **URL** 필드를 입력하고 URL의 나머지 부분을 서버 다운로드 ZIP에 입력합니다.(Azure Storage를 사용할 때 URL의 Blob 이름은 소문자여야 함) Azure Storage를 사용하려면 **저장소 계정** 드롭다운 목록에서 **(없음)**을 선택하고 **URL** 필드에서 서버 다운로드 ZIP에 URL을 입력합니다. ZIP은 응용 프로그램 서버 설치 디렉터리를 나타내는 하위 폴더를 포함합니다. 예를 들어 Apache Tomcat 7.0.35에 zip를 사용하는 경우 zip 내에서 하위 폴더는 **apache-tomcat-7.0.35**와 같은 설치 디렉터리를 나타냅니다. 
3. 홈 디렉터리 환경 변수의 값을 지정합니다. 값이 있는 경우 기본적으로 로컬 응용 프로그램 서버에 사용되는 값이지만 클라우드 응용 프로그램 서버가 로컬 응용 프로그램 서버와 다른 경우 다른 값을 지정할 수 있습니다. 하지만 클라우드 응용 프로그램 서버가 이전에 선택한 형식의 서버와 동일한 제품군인지 확인해야 합니다.
    나중에 클라우드 응용 프로그램 서버 zip 파일을 업데이트하는 경우 홈 디렉터리 설정을 수동으로 변경하거나 로컬 설정과 다시 일치시킬 수 있습니다.(로컬 응용 프로그램 서버도 변경한 경우)
4. **확인** 을 클릭하여 변경 내용을 저장합니다.

항목이 **서버 구성** 속성 페이지의 **서버** 탭에 표시되는 기본 논리는 사용자 지정될 수 있습니다. 이 기본값 이상으로 확장해야 하는 경우 또는 다른 서버를 추가하려는 경우 필요할 수 있는 고급 기능입니다. 논리를 사용자 지정하려면 **서버** 대화 상자에서 **사용자 지정** 링크를 클릭합니다. **서버 구성** 속성 페이지를 닫고 Eclipse에서 **componentsets.xml** 파일을 엽니다. 이렇게 하면 서버 구성 템플릿을 확장할 필요에 따라 수정할 수 있습니다. **componentsets.xml**에 대한 설명서는 **componentsets.xml** 파일 자체에 포함됩니다.

**내 로컬 서버 배포(클라우드 저장소에 자동 업로드)** 옵션을 사용하는 경우

1. **내 로컬 서버 배포(클라우드 저장소에 자동 업로드)**라는 확인란을 선택합니다.
2. **저장소 계정** 드롭다운 목록을 사용하여 **(자동)**을 선택합니다. 여기에서 **(자동)**을 지정하는 경우 Eclipse 도구 키트는 **Azure에 게시** 대화 상자의 배포에 대해 선택한 것과 같은 동일한 저장소 계정을 서버에 사용합니다.
3. **확인** 을 클릭하여 변경 내용을 저장합니다.

다음 조건 중 하나가 충족되는 경우 **로컬 서버 경로** 텍스트 상자의 컴퓨터에서 서버 설치 경로를 선택합니다.

* 에뮬레이터에서 배포를 테스트하려고 합니다.(Windows에만 적용)
* 로컬에 설치된 서버를 클라우드에 배포하려고 합니다.
* 또한 클라우드에서 고유한 사용자 지정 서버 다운로드를 사용하려는 경우 **내 로컬 서버 배포(클라우드 저장소에 자동 업로드)** 옵션을 위에서 선택하도록 합니다.

위의 옵션이 상황에 적용되지 않는 경우 로컬 서버 설정은 선택 사항입니다.

### <a name="applications-configuration"></a>응용 프로그램 구성
다음은 응용 프로그램을 지정할 수 있는 방법의 예입니다.

![][ic719512]

**추가**를 클릭하여 다른 응용 프로그램을 추가하거나 **제거**를 클릭하여 응용 프로그램을 제거합니다. 효율성을 위해 클라우드로 배포할 때 응용 프로그램의 원본에 다운로드를 사용하려는 경우 [구성 요소 속성](#components_properties) 을 사용하여 URL, 저장소 계정 등을 지정합니다. 

2014년 4월 릴리스부터, 응용 프로그램은 자동으로 배포에 대해 선택한 것과 동일한 저장소 계정에 업로드됩니다.(**eclipsedeploy** 컨테이너에서) 배포의 시작 논리는 먼저 해당 저장소 계정에서 해당 응용 프로그램을 다운로드하는 단계를 포함합니다. 즉, 전체 패키지를 다시 빌드하고 배포하지 않고도 해당 저장소 계정에 직접 응용 프로그램의 최신 버전을 수동으로 업로드하거나(예: Azure 포털 사용) 도구 키트에서 원래 업로드된 WAR 파일을 대체하여 배포에서 응용 프로그램을 업그레이드할 수 있습니다. 그런 다음 Azure 관리 포털을 사용하거나 명령줄 유틸리티를 통해 이러한 모든 역할 인스턴스를 재활용하기 시작합니다. (Eclipse 도구 키트 내에서 직접 재활용하는 역할 트리거 기능은 현재 지원되지 않습니다.)

### <a name="notes-about-server-configuration"></a>서버 구성에 대한 참고 사항
**서버 구성** 속성 페이지를 통해 만든 변경 사항은 package.xml 파일의 `<component>` 요소를 반영합니다.

JDK 또는 응용 프로그램 서버에 대해 **자동으로 업로드...** 또는 **다운로드에서 배포...** 옵션을 사용하거나 (계산 에뮬레이터가 아닌) 클라우드에 빌드 또는 네트워크에 연결하는 경우 Ant 빌더에서 다운로드의 가용성을 확인하는 대로 빌드 콘솔 출력에 다음과 같은 메시지가 나타날 수 있습니다.

`[windowsazurepackage] Verifying blob availability (https://example.blob.core.windows.net/temp/tomcat6.zip)...` 

**다운로드에서 배포 …** 옵션을 선택한 경우 다음과 같은 경고가 표시될 수 있지만 빌드는 계속됩니다.

`[windowsazurepackage] warning: Failed to confirm blob availability! Make sure the URL and/or the access key is correct (https://example.blob.core.windows.net/temp/tomcat6.zip).` 

이 경고는 다운로드의 가용성이 확인되지 않았다는 점을 나타냅니다. 따라서 배포가 어떤 이유로 클라우드에서 실패하는 경우 이 경고를 수신하는지 확인합니다.

다운로드 확인을 사용하지 않도록 설정하려는 경우(예: 빌드를 불필요하게 느리게 만든다고 생각하는 경우) package.xml의 `<windowsazurepackage>` 요소에서 `verifydownloads` 특성을 `false`로 설정합니다. 

`<windowsazurepackage verifydownloads="false" ...>` 

**자동으로 업로드 …** 옵션을 선택한 경우 콘솔 창에서 업로드가 필요하면 5초 마다 업로드의 진행률을 보고하는 빌드 메시지를 나타냅니다.

<a name="ssl_offloading_properties"></a> 

### <a name="ssl-offloading-properties"></a>SSL 오프로딩 속성
Eclipse의 프로젝트 탐색기에서 역할에 대한 상황에 맞는 메뉴를 열고 **Azure**를 클릭한 다음 **SSL 오프로딩**을 클릭합니다. 

![][ic719481]

이 대화 상자 내에서 SSL 오프로딩을 사용할 수 있으며 이를 통해 Java 응용 프로그램 서버에서 SSL을 구성하지 않고 Azure의 Java 배포에서 HTTPS(Hypertext Transfer Protocol Secure) 지원을 손쉽게 사용할 수 있습니다. 자세한 내용은 [SSL 오프로딩][SSL Offloading] 및 [SSL 오프로딩을 사용하는 방법][How to Use SSL Offloading]을 참조하세요.

## <a name="see-also"></a>참고 항목
[Eclipse용 Azure 도구 키트][Azure Toolkit for Eclipse]

[Eclipse용 Azure 도구 키트 설치][Installing the Azure Toolkit for Eclipse]

[Eclipse에서 Azure용 Hello World 응용 프로그램 만들기][Creating a Hello World Application for Azure in Eclipse]

[Azure 프로젝트 속성][Azure Project Properties]

[Azure Storage 계정 목록][Azure Storage Account List]

Java와 함께 Azure를 사용하는 방법에 대한 자세한 내용은 [Azure Java 개발자 센터][Azure Java Developer Center]를 참조하세요.

<!-- URL List -->

[Azure Java Developer Center]: http://go.microsoft.com/fwlink/?LinkID=699547
[Azure Management Portal]: http://go.microsoft.com/fwlink/?LinkID=512959
[Azure Toolkit for Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699529
[Azure Project Properties]: http://go.microsoft.com/fwlink/?LinkID=699524
[Azure Storage Account List]: http://go.microsoft.com/fwlink/?LinkID=699528
[com.microsoft.windowsazure.serviceruntime package summary]: http://azure.github.io/azure-sdk-for-java/com/microsoft/windowsazure/serviceruntime/package-summary.html
[Creating a Hello World Application for Azure in Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699533
[Debugging a specific role instance in a multi-instance deployment]: http://go.microsoft.com/fwlink/?LinkID=699535#debugging_specific_role_instance
[Debugging Azure Applications in Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699535
[Deploying Large Deployments]: http://go.microsoft.com/fwlink/?LinkID=699536
[How to Use Co-located Caching]: http://go.microsoft.com/fwlink/?LinkID=699542
[How to Use SSL Offloading]: http://go.microsoft.com/fwlink/?LinkID=699545
[Installing the Azure Toolkit for Eclipse]: http://go.microsoft.com/fwlink/?LinkId=699546
[Session Affinity]: http://go.microsoft.com/fwlink/?LinkID=699548
[SSL Offloading]: http://go.microsoft.com/fwlink/?LinkID=699549

<!-- IMG List -->

[ic789599]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic789599.png
[ic719499]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic719499.png
[ic719483]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic719483.png
[ic719501]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic719501.png
[ic710964]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic710964.png
[ic719502]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic719502.png
[ic719503]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic719503.png
[ic719504]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic719504.png
[ic719505]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic719505.png
[ic710897]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic710897.png
[ic719506]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic719506.png
[ic659268]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic659268.png
[ic552233]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic552233.png
[ic719492]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic719492.png
[ic719508]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic719508.png
[ic780647]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic780647.png
[ic789643]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic789643.png
[ic780648]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic780648.png
[ic789643]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic789643.png
[ic796926]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic796926.png
[ic719512]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic719512.png
[ic719481]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic719481.png

<!-- Legacy MSDN URL = https://msdn.microsoft.com/library/azure/hh690945.aspx -->



<!--HONumber=Jan17_HO1-->


