<properties linkid="dev-net-how-to-autoscaling" urlDisplayName="Autoscaling" pageTitle="Use the autoscaling application block (.NET) - Azure" metaKeywords="Azure autoscaling, Azure autoscaling C#, Azure autoscaling .NET" description="Learn how to use the Autoscaling Application for Azure. Code samples are written in C# and use the .NET API." metaCanonical="" services="cloud-services" documentationCenter=".NET" title="How to Use the Autoscaling Application Block" authors="" solutions="" manager="" editor="" />

자동 크기 조정 응용 프로그램 블록을 사용하는 방법
=================================================

이 가이드에서는 [Azure용 Microsoft Enterprise Library 5.0 통합 팩][]에서 자동 크기 조정 응용 프로그램 블록을 사용하여 일반 시나리오를 수행하는 방법을 보여 줍니다. 샘플은 C\#으로 작성되었으며 .NET API를 사용합니다. **블록 호스팅**, **제약 조건 규칙 사용**, **반응 규칙 사용** 등의 시나리오를 다룹니다. 자동 크기 조정 응용 프로그램 블록에 대한 자세한 내용은 [다음 단계][] 섹션을 참조하십시오.

목차
----

[자동 크기 조정 응용 프로그램 블록이란?][]
 [개념][]
 [대상 Azure 응용 프로그램에서 성능 카운터 데이터 수집][]
 [자동 크기 조정 응용 프로그램 블록의 호스트 응용 프로그램 설정][]
 [방법: 자동 크기 조정기 인스턴스화 및 실행][] [방법: 서비스 모델 정의][]
 [방법: 자동 크기 조정 규칙 정의][]
 [방법: 자동 크기 조정 응용 프로그램 블록 구성][]
 [다음 단계][]

<a id="WhatIs"></a>자동 크기 조정 응용 프로그램 블록이란?
--------------------------------------

자동 크기 조정 응용 프로그램 블록은 응용 프로그램에 대해 특별히 정의한 규칙에 따라 Azure 응용 프로그램 크기를 자동으로 조정할 수 있습니다. 이러한 규칙을 사용하여 Azure 응용 프로그램에서 작업의 변경 내용에 따른 처리량을 관리하고, 동시에 Azure의 응용 프로그램 호스팅과 관련한 비용을 제어할 수 있습니다. 블록을 사용하면 응용 프로그램에서 역할 인스턴스의 수를 증가시키거나 감소시켜서 크기를 조정하는 것 외에도 응용 프로그램 내의 특정 기능 제한 또는 사용자 지정 작업 사용과 같은 다른 크기 조정 작업을 사용할 수 있습니다.

Azure 역할이나 온-프레미스 응용 프로그램에서 블록을 호스트하도록 선택할 수 있습니다.

자동 크기 조정 응용 프로그램 블록은 [Azure용 Microsoft Enterprise Library 5.0 통합 팩][]의 일부입니다.

<a id="Concepts"></a>개념
----

다음 다이어그램에서 녹색 선은 이틀 동안 실행 중인 Azure 역할 인스턴스의 수를 도표로 보여 줍니다. 인스턴스 수는 자동 크기 조정 규칙 집합에 따라 점차 자동으로 변경됩니다.

![샘플 자동 크기 조정 다이어그램](./media/cloud-services-dotnet-autoscaling-application-block/autoscaling01.png)

블록은 다음 두 가지 유형의 규칙을 사용하여 응용 프로그램의 자동 규칙 조정 동작을 정의합니다.

-   **제약 조건 규칙:** 인스턴스 수의 상한 및 하한을 설정하려면, 예를 들어 매일 아침 8:00\~10:00에 인스턴스를 최소 4개와 최대 6개를 설정하려면 **제약 조건 규칙**을 사용합니다. 다이어그램에서 빨간 선과 파란 선은 제약 조건 규칙을 나타냅니다. 예를 들어 다이어그램에서 **A** 지점의 역할 인스턴스 최소 수는 현재 응용 프로그램의 작업에서 예상 증가량을 수용하기 위해 2개에서 4개로 늘어납니다. 다이어그램에서 **B** 지점의 역할 인스턴스 수는 응용 프로그램의 실행 비용을 제어하기 위해 5개 위로 올라가지 못합니다.

-   **반응 규칙:** 예기치 않은 수요 변화에 따라 역할 인스턴스 수를 변경하려면 **반응 규칙**을 사용합니다. 다이어그램에서 **C** 지점의 블록은 역할 인스턴스 수를 작업의 감소에 따라 4개에서 3개로 자동으로 줄입니다. **D** 지점에서 블록은 작업 증가를 감지하여 실행 중인 역할 인스턴스 수를 3개에서 4개로 자동으로 늘립니다.

블록은 구성 설정을 다음 두 저장소에 저장합니다.

-   **규칙 저장소:** 규칙 저장소는 Azure 응용 프로그램에 대해 정의한 모든 자동 크기 조정 규칙 목록인 비즈니스 구성을 보유합니다. 이 저장소는 일반적으로 자동 크기 조정 응용 프로그램 블록이 읽을 수 있는 위치(예: Azure Blob 저장소 또는 로컬 파일 내)에 있는 XML 파일입니다.

-   **서비스 정보 저장소:** 서비스 정보 저장소는 Azure 응용 프로그램의 서비스 모델인 작동 구성을 저장합니다. 이 서비스 모델은 블록이 대상 Azure 응용 프로그램에서 데이터 요소를 수집하고 크기 조정 작업을 수행할 수 있어야 하는 Azure 응용 프로그램에 관한 모든 정보(예: 역할 이름 및 저장소 계정 세부 정보)를 포함합니다.

<a id="PerfCounter"></a>대상 Azure 응용 프로그램에서 성능 카운터 데이터 수집
----------------------------------------------------

반응 규칙은 규칙 정의의 일부로 역할에서 성능 카운터 데이터를 사용할 수 있습니다. 예를 들어 반응 규칙은 블록이 크기 조정 작업을 시작해야 하는지 여부를 결정하기 위해 Azure 역할의 CPU 사용률을 모니터링할 수도 있습니다. 블록은 Azure 저장소의 **WADPerformanceCountersTable**이라는 Azure 진단 테이블에서 성능 카운터 데이터를 읽습니다.

기본적으로 Azure는 성능 카운터 데이터를 Azure 저장소의 Azure 진단 테이블에 쓰지 않습니다. 따라서 이 데이터를 저장하기 위해 성능 카운터 데이터를 수집해야 하는 역할을 수정해야 합니다. 응용 프로그램에서 성능 카운터를 사용하도록 설정하는 방법에 대한 내용은 [Azure에서 성능 카운터 사용][]을 참조하십시오.

<a id="CreateHost"></a>자동 크기 조정 응용 프로그램 블록의 호스트 응용 프로그램 설정
-------------------------------------------------------------

Azure 역할이나 온-프레미스 응용 프로그램에서 자동 크기 조정 응용 프로그램 블록을 호스트할 수 있습니다. 자동 크기 조정 응용 프로그램 블록은 일반적으로 자동으로 크기를 조정할 대상 응용 프로그램과는 별도의 응용 프로그램에 호스트됩니다. 이 섹션에서는 호스트 응용 프로그램을 구성하는 방법에 관한 지침을 제공합니다.

### 자동 크기 조정 응용 프로그램 블록 NuGet 패키지 가져오기

자동 크기 조정 응용 프로그램 블록 바이너리를 가져와 프로젝트에 해당 바이너리에 대한 참조를 추가해야 Visual Studio 프로젝트에서 자동 크기 조정 응용 프로그램 블록을 사용할 수 있습니다. NuGet Visual Studio 확장을 사용하면 Visual Studio 및 Visual Web Developer에서 라이브러리와 도구를 쉽게 설치 및 업데이트할 수 있습니다. 자동 크기 조정 응용 프로그램 블록 NuGet 패키지는 자동 크기 조정 응용 프로그램 블록 API를 가져오는 가장 쉬운 방법입니다. **NuGet**에 대한 자세한 내용 및 **NuGet** Visual Studio 확장을 설치하고 사용하는 방법은 [NuGet][] 웹 사이트를 참조하십시오.

NuGet 패키지 관리자를 설치한 후 응용 프로그램에서 자동 크기 조정 NuGet 패키지를 설치하려면 다음을 수행합니다.

1.  **NuGet 패키지 관리자 콘솔** 창을 엽니다. **도구** 메뉴에서 **라이브러리 패키지 관리자**, **패키지 관리자 콘솔**을 차례로 선택합니다.

2.  NuGet 패키지 관리자 콘솔 창에서 다음 명령을 입력합니다.

        PM> Install-Package EnterpriseLibrary.WindowsAzure.Autoscaling

NuGet 패키지를 설치하면 자동 크기 조정 응용 프로그램 블록을 사용해야 하는 필요한 모든 어셈블리 및 참조와 함께 프로젝트가 업데이트됩니다. 이제 프로젝트에 자동 크기 조정 규칙 정의 및 자동 크기 조정 서비스 정보를 위한 XML 스키마 파일이 포함됩니다. 또한 자동 크기 조정 응용 프로그램 블록에 대한 중요한 정보가 포함된 추가 정보 파일도 프로젝트에 포함됩니다.

![자동 크기 조정 NuGet 패키지에서 구성한 파일](./media/cloud-services-dotnet-autoscaling-application-block/auotscaling02.png)

### .NET Framework 4로 대상 프레임워크 설정

프로젝트는 .NET Framework 4를 대상으로 해야 합니다. 대상 프레임워크를 변경하거나 확인하려면

1.  솔루션 탐색기에서 해당 프로젝트 이름을 마우스 오른쪽 단추로 클릭하고 **속성**을 선택합니다.

2.  속성 창의 **응용 프로그램** 탭에서 대상 프레임워크가 **.NET Framework 4**로 설정되어 있는지 확인합니다.

    ![이미지](./media/cloud-services-dotnet-autoscaling-application-block/autoscaling03.png)

### 네임스페이스 참조 추가

프로그래밍 방식으로 자동 크기 조정 응용 프로그램 블록에 액세스하려는 C\# 파일의 맨 위에 다음과 같은 코드 네임스페이스 선언을 추가합니다.

    using Microsoft.Practices.EnterpriseLibrary.Common.Configuration;
    using Microsoft.Practices.EnterpriseLibrary.WindowsAzure.Autoscaling;

<a id="Instantiate"></a>방법: 자동 크기 조정기 인스턴스화 및 실행
-----------------------------------------

**IServiceLocator.GetInstance** 메서드를 사용하여 자동 크기 조정기를 인스턴스화한 다음 **Autoscaler.Start** 메서드를 호출하여 **Autoscaler**를 실행합니다.

    Autoscaler scaler =
        EnterpriseLibraryContainer.Current.GetInstance<Autoscaler>();
    scaler.Start();

<a id="DefineServiceModel"> </a>방법: 서비스 모델 정의
----------------------

일반적으로 서비스 모델(구독, 호스티드 서비스, 역할, 저장소 계정에 관한 정보를 포함하는 Azure 환경에 대한 설명)은 XML 파일에 저장합니다. 프로젝트의 **AutoscalingServiceModel.xsd** 파일에서 이 XML 파일에 대한 스키마 복사본을 찾을 수 있습니다. Visual Studio에서 이 스키마는 서비스 모델 XML 파일을 편집할 때 Intellisense 및 유효성 검사를 제공합니다.

프로젝트에서 **services.xml**이라는 새 XML 파일을 만드십시오.

Visual Studio에서 서비스 모델 파일이 출력 폴더에 복사되었는지 확인해야 합니다. 다음을 수행합니다.

1.  해당 파일을 마우스 오른쪽 단추로 클릭하고 **속성**을 선택합니다.

2.  속성 창에서 **출력 디렉터리로 복사** 값을 **항상 복사**로 설정합니다.

    ![출력 디렉터리로 복사 값 설정](./media/cloud-services-dotnet-autoscaling-application-block/autoscaling04.png)

    다음 코드 샘플은 **services.xml** 파일의 예제 서비스 모델을 보여 줍니다.

	<?xml version="1.0" encoding="utf-8" ?>
    <serviceModel xmlns="http://schemas.microsoft.com/practices/2011/entlib/autoscaling/serviceModel">
      <subscriptions>
        <subscription name="[subscriptionname]"
                      certificateThumbprint="[managementcertificatethumbprint]"
                      subscriptionId="[subscriptionid]"
                      certificateStoreLocation="CurrentUser"
                      certificateStoreName="My">
          <services>
            <service dnsPrefix="[hostedservicednsprefix]" slot="Staging">
              <roles>
                <role alias="AutoscalingApplicationRole"
                      roleName="[targetrolename]"
                      wadStorageAccountName="targetstorage"/>
              </roles>
            </service>
          </services>
          <storageAccounts>
            <storageAccount alias="targetstorage"
              connectionString="DefaultEndpointsProtocol=https;AccountName=[storageaccountname];AccountKey=[storageaccountkey]">
            </storageAccount>
          </storageAccounts>
        </subscription>
      </subscriptions>
    </serviceModel>

대괄호 안의 값을 환경 및 대상 응용 프로그램 고유 값으로 바꿔야 합니다. 대부분의 이러한 값을 찾으려면 [Azure 관리 포털][]에 로그인해야 합니다.

관리 포털에 로그인합니다.

-   **[subscriptionname]:** 자동 크기 조정을 사용할 응용 프로그램이 포함된 Azure 구독을 참조하는 이름을 선택합니다.

-   **[subscriptionid]:** 자동 크기 조정을 사용할 응용 프로그램이 포함된 Azure 구독의 고유 ID입니다.

    1.  Azure 관리 포털에서 **클라우드 서비스**를 클릭합니다.

    2.  클라우드 서비스 목록에서 자동 크기 조정을 사용할 응용 프로그램을 호스트하는 서비스를 클릭합니다. 오른쪽의 간략 상태 창에 **구독 ID**가 표시됩니다.

        ![이미지](./media/cloud-services-dotnet-autoscaling-application-block/autoscaling05.png)

    -   **[hostedservicednsprefix]:** 자동 크기 조정을 사용할 호스티드 서비스의 DNS 접두사입니다.

    1.  Azure 관리 포털에서 **클라우드 서비스**를 클릭합니다.

    2.  클라우드 서비스 목록에서 자동 크기 조정을 사용할 응용 프로그램을 호스트하는 서비스를 찾습니다. 클라우드 서비스 이름은 **DNS 접두사**입니다.

        ![이미지](./media/cloud-services-dotnet-autoscaling-application-block/autoscaling06.png)

    -   **[targetrolename]:** 자동 크기 조정 규칙의 대상인 역할의 이름입니다.

    1.  Azure 관리 포털에서 **클라우드 서비스**를 클릭합니다.

    2.  클라우드 서비스 목록에서 자동 크기 조정을 사용할 응용 프로그램을 호스트하는 서비스를 클릭한 다음 **인스턴스**를 클릭합니다. *\*역할* 열에 대상 역할의 이름이 표시됩니다.

        ![이미지](./media/cloud-services-dotnet-autoscaling-application-block/autoscaling07.png)

    -   **[storageaccountname]** 및 **[storageaccountkey]:** 대상 Azure 응용 프로그램에 사용 중인 Azure 저장소 계정의 이름입니다.

    1.  Azure 관리 포털에서 **저장소**를 클릭합니다.

    2.  저장소 계정 목록에서 사용 중인 저장소 계정을 선택합니다. **이름** 열에 **이름**이 표시됩니다.

    3.  화면 아래쪽에 있는 **키 관리** 단추를 클릭하여 기본 액세스 키를 가져옵니다.

        ![이미지](./media/cloud-services-dotnet-autoscaling-application-block/autoscaling08.png)

    -   **[managementcertificatethumbprint]:** 블록이 대상 응용 프로그램에 대한 크기 조정 요청을 보호하는 데 사용하는 관리 인증서의 **손도장**입니다.

    1.  Azure 관리 포털에서 **설정**을 클릭합니다.

    2.  **손도장** 열에 **손도장**이 표시됩니다.

        ![이미지](./media/cloud-services-dotnet-autoscaling-application-block/autoscaling09.png)

서비스 모델 파일의 내용에 대한 자세한 정보를 보려면 [서비스 정보 데이터 저장][]을 참조하십시오.

<a id="DefineAutoscalingRules"></a>방법: 자동 크기 조정 규칙 정의
------------------------------

일반적으로 대상 응용 프로그램의 역할 인스턴스 수를 제어하는 자동 크기 조정 규칙은 XML 파일에 저장합니다. 프로젝트의 **AutoscalingRules.xsd** 파일에서 이 XML 파일에 대한 스키마 복사본을 찾을 수 있습니다. Visual Studio에서 이 스키마는 XML 파일을 편집할 때 Intellisense 및 유효성 검사를 제공합니다.

프로젝트에서 **rules.xml**이라는 새 XML 파일을 만드십시오.

Visual Studio에서 규칙 파일이 출력 폴더에 복사되었는지 확인해야 합니다. 다음을 수행합니다.

1.  해당 파일을 마우스 오른쪽 단추로 클릭하고 **속성**을 선택합니다.

2.  속성 창에서 **출력 디렉터리로 복사** 값을 **항상 복사**로 설정합니다.

다음 코드 샘플은 **rules.xml** 파일의 예제 규칙 집합을 보여 줍니다.

    <?xml version="1.0" encoding="utf-8" ?>
    <rules xmlns="http://schemas.microsoft.com/practices/2011/entlib/autoscaling/rules">
      <constraintRules>
        <rule name="default" enabled="true" rank="1" description="The default constraint rule">
          <actions>
            <range min="2" max="6" target="AutoscalingApplicationRole"/>
          </actions>
        </rule>
      </constraintRules>
      <reactiveRules>
        <rule name="ScaleUpOnHighUtilization" rank="10" description="Scale up the web role" enabled="true" >
          <when>
            <any>
               <greaterOrEqual operand="WebRoleA_CPU_Avg_5m" than="60"/>
            </any>
          </when>
          <actions>
            <scale target="AutoscalingApplicationRole" by="1"/>
          </actions>
        </rule>
        <rule name="ScaleDownOnLowUtilization" rank="10" description="Scale up the web role" enabled="true" >
          <when>
            <all>
              <less operand="WebRoleA_CPU_Avg_5m" than="60"/>
            </all>
          </when>
          <actions>
            <scale target="AutoscalingApplicationRole" by="-1"/>
          </actions>
        </rule>
      </reactiveRules>
      <operands>
        <performanceCounter alias="WebRoleA_CPU_Avg_5m"
          performanceCounterName="\Processor(_Total)\% Processor Time"
          source ="AutoscalingApplicationRole"
          timespan="00:05:00" aggregate="Average"/>
      </operands>
    </rules>

이 예제에는 **서비스 모델**에 정의된 역할의 별칭인 **AutoscalingApplicationRole**이라는 대상에서 작동하는 세 개의 자동 크기 조정 규칙(한 개는 **제약 조건 규칙**이고 두 개는 **반응 규칙**)이 있습니다.

-   제약 조건 규칙은 항상 활성화되어 있으며 최소 역할 인스턴스 수는 2개로, 최대 역할 인스턴스 수는 6개로 설정합니다.

-   두 개의 반응 규칙은 모두 **AutoscalingApplicationRole**이라는 Azure 역할의 지난 5분 동안의 평균 CPU 사용량을 계산하는 **WebRoleA\_CPU\_Avg\_5m**이라는 **피연산자**를 사용합니다. 이 역할은 **서비스 모델**에 정의되어 있습니다.

-   **ScaleUpOnHighUtilization**이라는 반응 규칙은 지난 5분 동안의 평균 CPU 사용량이 60% 이상인 경우 대상 역할의 인스턴스 수를 하나씩 늘립니다.

-   **ScaleDownOnLowUtilization**이라는 반응 규칙은 지난 5분 동안의 평균 CPU 사용량이 60% 미만인 경우 대상 역할의 인스턴스 수를 하나씩 줄입니다.

<a id="Configure"></a>방법: 자동 크기 조정 응용 프로그램 블록 구성
--------------------------------------------

서비스 모델 및 자동 크기 조정 규칙을 정의했으면 이들을 사용할 자동 크기 조정 응용 프로그램 블록을 구성해야 합니다. 이 작동 구성 정보는 응용 프로그램 구성 파일에 저장됩니다.

기본적으로 자동 크기 조정 응용 프로그램 블록의 자동 크기 조정 규칙 및 서비스 모델은 Azure Blob에 저장되어야 합니다. 이 예제에서는 자동 크기 조정 규칙 및 서비스 모델을 로컬 파일 시스템에서 로드하도록 블록을 구성합니다.

### 호스트 응용 프로그램에서 자동 크기 조정 응용 프로그램 블록 구성

1.  솔루션 탐색기에서 **App.config** 파일을 마우스 오른쪽 단추로 클릭한 다음 **구성 파일 편집**을 클릭합니다.

2.  **블록** 메뉴에서 **자동 크기 조정 설정 추가**를 클릭합니다.
     ![이미지](./media/cloud-services-dotnet-autoscaling-application-block/autoscaling10.png)

3.  **자동 크기 조정 설정**을 확장한 다음 **데이터 요소 저장 저장소 계정** 옆의 줄임표(...)를 클릭하고, 블록이 수집하는 데이터 요소를 저장할 Azure 저장소 계정의 **계정 이름** 및 **계정 키**를 추가한 다음(이러한 값의 위치가 확실하지 않은 경우 [방법: 서비스 모델 정의][] 참조) **확인**을 클릭하십시오.

    ![이미지](./media/cloud-services-dotnet-autoscaling-application-block/autoscaling11.png)

4.  **자동 크기 조정 설정** 섹션을 확장하여 **규칙 저장소** 및 **서비스 정보 저장소** 섹션을 표시합니다. 기본적으로 이러한 설정은 Azure Blob 저장소를 사용하도록 구성되어 있습니다.
     ![이미지](./media/cloud-services-dotnet-autoscaling-application-block/autoscaling12.png)

5.  **규칙 저장소** 옆의 더하기 기호(+)를 클릭하고 **규칙 저장소 설정**을 가리킨 다음 **로컬 파일 규칙 저장소 사용**을 클릭하고 **예**를 클릭합니다.

6.  **파일 이름** 상자에 **rules.xml**을 입력합니다. 자동 크기 조정 규칙이 포함된 파일의 이름입니다.
     ![이미지](./media/cloud-services-dotnet-autoscaling-application-block/autoscaling13.png)

7.  **서비스 정보 저장소** 옆의 더하기 기호(+)를 클릭하고 **서비스 정보 저장소 설정**을 가리킨 다음 **로컬 파일 서비스 정보 저장소 사용**을 클릭하고 **예**를 클릭합니다.

8.  **파일 이름** 상자에 **services.xml**을 입력합니다. 자동 크기 조정 규칙이 포함된 파일의 이름입니다.
     ![이미지](./media/cloud-services-dotnet-autoscaling-application-block/autoscaling14.png)

9.  엔터프라이즈 라이브러리 구성 창의 **파일** 메뉴에서 **저장**을 클릭하여 구성 변경 내용을 저장합니다. 그다음에 엔터프라이즈 라이브러리 구성 창의 **파일** 메뉴에서 **끝내기**를 클릭합니다.

자동 크기 조정 응용 프로그램 블록이 수행하는 동작에 대한 자세한 정보를 가져오려면 작성되는 로그 메시지를 캡처해야 합니다. 예를 들어 콘솔 응용 프로그램의 블록을 호스트하는 중이면 Visual Studio의 출력 창에서 로그 메시지를 볼 수 있습니다. 다음 섹션에서는 이 동작을 구성하는 방법을 보여 줍니다.

### 자동 크기 조정 응용 프로그램 블록 호스트 응용 프로그램 로그인 구성

1.  Visual Studio의 솔루션 탐색기에서 **App.config** 파일을 두 번 클릭하여 편집기에서 엽니다. 그후에 다음 샘플처럼 **system.diagnostics** 섹션을 추가합니다.

        <?xml version="1.0" encoding="utf-8" ?>
        <configuration>
          ...
          <system.diagnostics>
            <sources>
              <sourcename="Autoscaling General" switchName="SourceSwitch" switchType="System.Diagnostics.SourceSwitch" />
              <sourcename="Autoscaling Updates" switchName="SourceSwitch" switchType="System.Diagnostics.SourceSwitch" />
            </sources>
            <switches>
              <addname="SourceSwitch" value="Verbose, Information, Warning, Error, Critical" />
            </switches>
          </system.diagnostics>
        </configuration>

2.  변경 내용을 저장합니다.

이제 자동 크기 조정 응용 프로그램 블록 콘솔 응용 프로그램을 실행하여 자동 크기 조정 규칙이 대상 Azure 응용 프로그램에서 어떻게 작동되는지 관찰할 수 있습니다. 호스트 콘솔 응용 프로그램을 실행할 때 Visual Studio의 출력 창에 다음과 유사한 메시지가 표시되어야 합니다. 이러한 로그 메시지를 통해 블록 동작을 이해할 수 있습니다. 예를 들어 로그 메시지는 블록과 일치하는 규칙 및 블록이 수행하는 동작을 표시합니다.

    Autoscaling General Verbose: 1002 : Rule match.
    [BEGIN DATA]{"EvaluationId":"6b27dfa0-b671-44a3-adf1-bb1e0b7c3726",
    "MatchingRules":[{"RuleName":"default","RuleDescription":"The default constraint rule",
    "Targets":["AutoscalingApplicationRole"]},{"RuleName":"ScaleUp","RuleDescription":"Scale up the web role",
    "Targets":[]},{"RuleName":"ScaleDown","RuleDescription":"Scale up the web role","Targets":[]}]}

    Autoscaling Updates Verbose: 3001 : The current deployment configuration for a hosted service is about to be checked
    to determine if a change is required (for role scaling or changes to settings).
    [BEGIN DATA]{"EvaluationId":"6b27dfa0-b671-44a3-adf1-bb1e0b7c3726",
    "HostedServiceDetails":{"Subscription":"AutoscalingHowTo","HostedService":"autoscalingtarget",
    "DeploymentSlot":"Staging"},"ScaleRequests":{"AutoscalingApplicationRole":{"Min":2,"Max":6,"AbsoluteDelta":0,
    "RelativeDelta":0,"MatchingRules":"default"}},"SettingChangeRequests":{}}

    Autoscaling Updates Verbose: 3003 : Role scaling requests for hosted service about to be submitted.
    [BEGIN DATA]{"EvaluationId":"6b27dfa0-b671-44a3-adf1-bb1e0b7c3726",
    "HostedServiceDetails":{"Subscription":"AutoscalingHowTo","HostedService":"autoscalingtarget",
    "DeploymentSlot":"Staging"},
    "ScaleRequests":{"AutoscalingApplicationRole":
    {"Min":2,"Max":6,"AbsoluteDelta":0,"RelativeDelta":0,"MatchingRules":"default"}},
    "SettingChangeRequests":{},"InstanceChanges":{"AutoscalingApplicationRole":{"CurrentValue":1,"DesiredValue":2}},
    "SettingChanges":{}}

    Autoscaling Updates Information: 3002 : Role configuration changes for deployment were submitted.
    [BEGIN DATA]{"EvaluationId":"6b27dfa0-b671-44a3-adf1-bb1e0b7c3726",
    "HostedServiceDetails":{"Subscription":"AutoscalingHowTo","HostedService":"autoscalingtarget",
    "DeploymentSlot":"Staging"},"ScaleRequests":{"AutoscalingApplicationRole":{"Min":2,"Max":6,"AbsoluteDelta":0,
    "RelativeDelta":0,"MatchingRules":"default"}},"SettingChangeRequests":{},
    "InstanceChanges":{"AutoscalingApplicationRole":{"CurrentValue":1,"DesiredValue":2}},
    "SettingChanges":{},"RequestID":"f8ca3ada07c24559b1cb075534f02d44"}

<a id="NextSteps"></a>다음 단계
---------

이제 자동 크기 조정 응용 프로그램 블록 사용의 기본 사항을 배웠으므로 다음 링크를 따라 좀 더 복잡한 자동 크기 조정 시나리오를 구현하는 방법을 알아보십시오.

-   [작업자 역할에서 자동 크기 조정 응용 프로그램 블록 호스팅][]
-   [제한 동작 구현][]
-   [규칙 순위 및 조정 이해][]
-   [자동 크기 조정 응용 프로그램 블록 확장 및 수정][]
-   [최적화 안정기를 사용하여 고주파 진동 방지 및 비용 최적화][]
-   [알림 및 수동 크기 조정 사용][]
-   [확장 그룹 정의][]
-   [WASABiCmdlet을 사용하여 Windows PowerShell을 통해 블록 조작][]
-   [Azure용 Enterprise Library 5.0 통합 팩에 대한 개발자 가이드][]
-   [Sage가 자동 크기 조정을 사용하여 Azure 호스팅 비용을 절감하는 방법][]
-   [TechNet 및 MSDN 호스팅 비용과 Azure에서 자동 크기 조정으로 인한 환경적 영향 줄이기][]

[Azure용 Microsoft Enterprise Library 5.0 통합 팩]: http://go.microsoft.com/fwlink/?LinkID=235134 
[다음 단계]: #NextSteps 
[자동 크기 조정 응용 프로그램 블록이란?]: #WhatIs 
[개념]: #Concepts 
[대상 Azure 응용 프로그램에서 성능 카운터 데이터 수집]: #PerfCounter 
[자동 크기 조정 응용 프로그램 블록의 호스트 응용 프로그램 설정]: #CreateHost 
[방법: 자동 크기 조정기 인스턴스화 및 실행]: #Instantiate 
[방법: 서비스 모델 정의]: #DefineServiceModel 
[방법: 자동 크기 조정 규칙 정의]: #DefineAutoscalingRules 
[방법: 자동 크기 조정 응용 프로그램 블록 구성]: #Configure 

[Azure에서 성능 카운터 사용]: http://www.windowsazure.com/ko-kr/develop/net/common-tasks/performance-profiling/ 
[NuGet]: http://nuget.org/ 
[Azure 관리 포털]: http://manage.windowsazure.com 
[서비스 정보 데이터 저장]: http://msdn.microsoft.com/ko-kr/library/hh680878(PandP.50).aspx 
[작업자 역할에서 자동 크기 조정 응용 프로그램 블록 호스팅]: http://msdn.microsoft.com/ko-kr/library/hh680914(PandP.50).aspx 
[제한 동작 구현]: http://msdn.microsoft.com/ko-kr/library/hh680896(PandP.50).aspx 
[규칙 순위 및 조정 이해]: http://msdn.microsoft.com/ko-kr/library/hh680923(PandP.50).aspx 
[자동 크기 조정 응용 프로그램 블록 확장 및 수정]: http://msdn.microsoft.com/ko-kr/library/hh680889(PandP.50).aspx 
[최적화 안정기를 사용하여 고주파 진동 방지 및 비용 최적화]: http://msdn.microsoft.com/ko-kr/library/hh680951(PandP.50).aspx 
[알림 및 수동 크기 조정 사용]: http://msdn.microsoft.com/ko-kr/library/hh680885(PandP.50).aspx 
[확장 그룹 정의]: http://msdn.microsoft.com/ko-kr/library/hh680902(PandP.50).aspx 
[WASABiCmdlet을 사용하여 Windows PowerShell을 통해 블록 조작]: http://msdn.microsoft.com/ko-kr/library/hh680938(PandP.50).aspx 
[Azure용 Enterprise Library 5.0 통합 팩에 대한 개발자 가이드]: http://msdn.microsoft.com/ko-kr/library/hh680949(PandP.50).aspx 
[Sage가 자동 크기 조정을 사용하여 Azure 호스팅 비용을 절감하는 방법]: http://msdn.microsoft.com/ko-kr/library/jj838716(PandP.50).aspx 
[TechNet 및 MSDN 호스팅 비용과 Azure에서 자동 크기 조정으로 인한 환경적 영향 줄이기]: http://msdn.microsoft.com/ko-kr/library/jj838718(PandP.50).aspx

