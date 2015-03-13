<properties 
	pageTitle="자동 크기 조정 응용 프로그램 블록 사용(.NET) - Azure" 
	description="Azure용 자동 크기 조정 응용 프로그램 사용 방법에 대해 알아봅니다. 코드 샘플은 C#으로 작성되었으며 .NET API를 사용합니다." 
	services="cloud-services" 
	documentationCenter=".net" 
	authors="squillace" 
	manager="timlt" 
	editor=""/>

<tags 
	ms.service="cloud-services" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="11/14/2014" 
	ms.author="rasquill"/>







# 자동 크기 조정 응용 프로그램 블록을 사용하는 방법

이 가이드에서는
[Azure용 Microsoft Enterprise Library 5.0
통합 팩][]의 자동 크기 조정 응용 프로그램 블록을 사용하여 일반 시나리오를 수행하는 방법을 설명합니다. 샘플은 C\에서#
및 .NET API를 사용하여 작성되었습니다. 시나리오는 
**블록 호스팅**, **제약 조건 규칙 사용**, 및 **반응 규칙 사용**을 포함하여 다룹니다. Blob에 대한
자동 크기 조정 응용 프로그램 블록에 대한 자세한 내용은 [다음 단계][] 섹션을 참조하세요.

## 목차

[자동 크기 조정 응용 프로그램 블록이란?][]   
 [개념][]   
 [대상 Azure 응용 프로그램에서 성능 카운터 데이터 수집][]   
 [자동 크기 조정 응용 프로그램 블록에 대해 호스트 응용 프로그램 설정][]   
 [방법: 자동 크기 조정기 인스턴스화 및 실행][] [방법: 서비스 모델 정의][]   
 [방법: 자동 크기 조정 규칙 정의][]   
 [방법: 자동 크기 조정 응용 프로그램 블록 구성][]   
 [다음 단계][]

## <a id="WhatIs"> </a>자동 크기 조정 응용 프로그램 블록이란?

자동 크기 조정 응용 프로그램 블록은 자동으로 Windows를 확장할 수 있습니다.
Azure 응용 프로그램은 사용자의 응용 프로그램을 특별히 정의한 규칙에
따릅니다. 이 규칙을 사용하면 Azure에서 응용 프로그램
호스팅과 연관된 비용을 동시에 제어하는 동안 워크로드 변화에 대응하여
Azure 응용 프로그램이 처리량을 유지하도록
할 수 있습니다. 응용 프로그램의 역할 인스턴스 개수의
증감에 따라 블록도 사용자가 같은 응용 프로그램 내에서
또는 사용자 지정 작업을 사용하여 특정 기능을 제한하는 것과 같은
다른 크기 조정 작업을 사용할 수 있도록
활성화합니다.

Azure 역할 또는
온-프레미스 응용 프로그램에서 블록을 호스트하도록 선택할 수 있습니다.

자동 크기 조정 응용 프로그램 블록은 [Azure용 Microsoft Enterprise Library 5.0 통합 팩][]의 일부입니다.

## <a id="Concepts"> </a>개념

다음 다이어그램에서 녹색 줄은
이틀 동안 Azure 역할의 인스턴스를 실행한 개수를 도표로 보여줍니다. 인스턴스의
변경 개수는 시간이 지남에 따른 자동 크기 조정 규칙에
대응됩니다.

![샘플 자동 크기 조정 다이어그램](./media/cloud-services-dotnet-autoscaling-application-block/autoscaling01.png)

블록은 다음 두 가지 유형의 규칙을 사용하여 응용 프로그램의 자동 규칙 조정 동작을
정의합니다.

-   **제약 조건 규칙:** 인스턴스의 수에 상한과 하한을 설정하려면,
    예를 들어, 매일 아침 8시에서 10시 사이에
    최소 4개, 최대 6개의 인스턴스를 원하는 경우,
    **제약 조건 규칙**을 사용합니다. 다이어그램에서 빨강 및 파랑
    줄은 제약 조건 규칙을 나타냅니다. 예를 들어 다이어그램의 **A** 지점에서
    현재 응용 프로그램의 작업에서 예상된 증가량을
    수용하기 위해 역할 인스턴스의 최소값이
    2에서 4로 늘어납니다. 다이어그램의 **B** 지점에서
    응용 프로그램의 실행 비용을 제어하기 위해
    역할 인스턴스의 수는 5개 위로 올라가지 못합니다.

-   **반응 규칙:** 역할 인스턴스의 개수가
    예기치 않은 수요 변화에 따라 변경되도록 활성화하려면 **반응 
    규칙**을 사용합니다. 다이아그램의 **C** 지점에서 작업의 감소에 따라 블록이
    역할 인스턴스의 개수를 4에서 3으로
    자동으로 감소합니다. **D** 지점에서 블록이
    작업의 증가를 감지하고 자동으로
    3에서 4로 역할 인스턴스의 수를 증가합니다.

블록은 구성 설정을 다음 두 저장소에 저장합니다.

-   **규칙 저장소:** 규칙 저장소는 비즈니스 구성을 유지합니다.
    또한 Azure 응용 프로그램에 대해 정의한 모든 자동 크기 조정 규칙의
    목록입니다. 이 저장소는 일반적으로 XML 파일이며
    자동 크기 조정 응용 프로그램 블록이 읽을 수 있는 위치에 존재합니다.
    예를 들어 Azure Blob 저장소 또는 로컬 파일에 있습니다.

-   **서비스 정보 저장소:** 서비스 정보 저장소에
    작동 구성이 저장되며 이는 Azure 응용 프로그램의
    서비스 모델입니다. 이 서비스 모델은
    Azure 응용 프로그램에 대한 모든 정보(예: 역할 이름
    및 저장소 계정 세부 정보)를 포함하고 있으며 이 정보는 블록이
    대상 Azure 응용 프로그램에서 데이터 요소를 수집하고
    크기 조정 작업을 수행하는 데 필요합니다.

## <a id="PerfCounter"> </a>대상 Azure 프로그램에서 성능 카운터 데이터 수집

반응 규칙은 규칙 정의의 일부로 역할에서 성능 카운터 데이터를
사용할 수 있습니다. 예를 들어 반응 규칙은 Azure 역할의 CPU
사용률을 모니터링하여 블록이
크기 조정 작업을 시작해야 하는지 여부를 결정합니다. 블록은 Azure
저장소의
**WADPerformanceCountersTable**이라는 Azure 진단 테이블에 있는 성능 카운터 데이터를 읽습니다.

기본적으로 Azure는 성능 카운터 데이터를
Azure 저장소의 Azure 진단 테이블에 작성하지 않습니다. 따라서
성능 카운터 데이터를 수집하여 이 데이터에 저장하는 규칙을
수정해야 합니다. 응용 프로그램에서 성능 카운터를 활성화하는 방법에 대한 자세한 내용은
[Azure에서 성능 카운터 사용하기][]를 참조하세요.

## <a id="CreateHost"> </a>자동 크기 조정 응용 프로그램 블록에 대한 호스트 응용 프로그램 설정

Azure 역할 또는 온-프레미스 응용 프로그램에서
각 자동 크기 조정 응용 프로그램 블록 중 하나를 호스팅할 수 있습니다. 자동 크기 조정 응용 프로그램 블록은
일반적으로 자동으로 크기 조정하려는 대상 응용 프로그램에서
별도의 응용 프로그램으로 호스팅됩니다. 이 섹션에서는
호스트 응용 프로그램을 구성하는 방법에 대한 지침입니다.

### 자동 크기 조정 응용 프로그램 블록 NuGet 패키지 가져오기

Visual Studio 프로젝트에서 자동 크기 조정 응용 프로그램 블록을 사용하기 전에
자동 크기 조정 응용 프로그램 블록 바이너리를 가져오고
프로젝트에 참조를 추가해야 합니다. NuGet
Visual Studio 확장 프로그램을 사용하면 라이브러리 및
Visual Studio 및 Visual Web Developer의 도구를 쉽게 설치하고 업데이트할 수 있습니다. 자동 크기 조정
응용 프로그램 블록 NuGet 패키지는 자동 크기 조정
응용 프로그램 블록 API를 얻는 가장 쉬운 방법입니다. **NuGet** 및
**NuGet** Visual Studio 확장 프로그램 설치 및 사용 방법에 대한 자세한 내용은 [NuGet][] 웹 사이트를
참조하세요.

NuGet 패키지 관리자를 한 번 설치한 경우 응용 프로그램에서 자동 크기 조정 NuGet 패키지를 설치하려면
다음을 수행 합니다.

1.  **NuGet 패키지 관리자 콘솔** 창을 엽니다. **도구** 메뉴에서
    **라이브러리 패키지 관리자**를 선택하고 **패키지
    관리자 콘솔**을 선택하십시오.

2.  NuGet 패키지 관리자 콘솔 창에 다음 명령을
    입력합니다.

        PM> Install-Package EnterpriseLibrary.WindowsAzure.Autoscaling

NuGet 패키지를 설치하면
자동 크기 조정 응용 프로그램 블록을 사용하는 데 필요한
모든 어셈블리 및 참조와 함께 프로젝트가 업데이트됩니다. 프로젝트는 이제
자동 크기 조정 규칙 정의 및 자동 크기 조정 서비스 정보에 대한 XML 스키마 파일을 포함합니다.
프로젝트에 이제 자동 조정 응용 프로그램 블록에 대한 중요한 정보가 포함된 추가 정보 파일이
포함됩니다.

![files configured by autoscaling NuGet package](./media/cloud-services-dotnet-autoscaling-application-block/auotscaling02.png)


### .NET Framework 4로 대상 프레임워크 설정

프로젝트는 .NET Framework 4를 대상으로 설정해야 합니다. 대상 프레임 워크를
변경하거나 확인하려면:

1.  솔루션 탐색기에서 해당 프로젝트 이름을 마우스 오른쪽 단추로 클릭하고
    **속성**을 선택합니다.

2.  속성 창의 **응용 프로그램** 탭에서 대상 프레임워크가 **.NET Framework 4**로 설정되어 있는지 확인합니다.

	![image](./media/cloud-services-dotnet-autoscaling-application-block/autoscaling03.png)


### 네임스페이스 참조 추가

Azure 저장소에 프로그래밍 방식으로 액세스하려는 C\# 파일의 맨 위에
다음 코드 네임스페이스 선언을
추가합니다.

    using Microsoft.Practices.EnterpriseLibrary.Common.Configuration;
    using Microsoft.Practices.EnterpriseLibrary.WindowsAzure.Autoscaling;

## <a id="Instantiate"> </a>방법: 자동 크기 조정기 인스턴스화 및 실행

**IServiceLocator.GetInstance** 메서드를 사용하여
Autoscaler를 인스턴스화하고 **Autoscaler.Start** 메서드를 호출하여
**Autoscaler**를 실행합니다.

    Autoscaler scaler =
        EnterpriseLibraryContainer.Current.GetInstance<Autoscaler>();
    scaler.Start();

## <a id="DefineServiceModel"> </a>방법: 서비스 모델 정의

일반적으로 XML 파일에 서비스 모델(구독 정보,
호스트 서비스, 규칙 및 저장소 계정이 포함된 Windows Azure 환경 설명)을
저장합니다. 이 XML 파일에 대한
스키마의 복사본은
프로젝트의 **AutoscalingServiceModel.xsd** 파일에서 찾을 수 있습니다. Visual Studio에서
서비스 모델 XML 파일을 편집할 때
이 스키마는 Intellisense 및 유효성 검사를 제공합니다.

프로젝트에서 **services.xml**이라는 새 XML 파일을 만드세요.

Visual Studio에서 서비스 모델 파일이 출력 폴더에 복사되었는지 확인해야
합니다. 다음을 수행합니다.

1.  해당 파일을 마우스 오른쪽 단추로 클릭하고 **속성**을 선택합니다.

2.  속성 창에서 **출력 디렉터리로 복사**
    값을 **항상 복사**로 설정합니다.

    ![Set Copy to Output Directory value](./media/cloud-services-dotnet-autoscaling-application-block/autoscaling04.png)


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

대괄호 안의 값을 환경 및 대상 응용 프로그램 고유 값으로 바꿔야
합니다. 대부분 이러한 값을 찾으려면
[Azure 관리 포털][]에 로그인해야 합니다.

관리 포털에 로그인합니다.

-   **[subscriptionname]:** Azure 구독을 참조하는 원하는 이름을 선택합니다.
    자동 크기 조정을 사용하려는 응용 프로그램이
    포함됩니다.

-   **[subscriptionid]:** Azure 구독의 고유 ID에는
    자동 크기 조정을 사용하려는 응용 프로그램이
    포함됩니다.

    1.  Azure 관리 포털에서
        **클라우드 서비스**를 클릭합니다.

    2.  클라우드 서비스 목록에서 자동 크기 조정을 사용하려는 응용 프로그램을 호스트하는
        서비스를 클릭하여 선택합니다. 오른쪽의
        간략 상태 창에
        **구독 ID**가 표시됩니다.

        ![image](./media/cloud-services-dotnet-autoscaling-application-block/autoscaling05.png)

  
	-   **[hostedservicednsprefix]:** 자동 크기 조정을 사용할 호스팅 서비스의 DNS 접두사입니다.

    1.  Azure 관리 포털에서
        **클라우드 서비스**를 클릭합니다.

    2.  클라우드 서비스 목록에서 자동 크기 조정을 사용하려는 응용 프로그램을 호스트하는
        서비스를 찾습니다. 클라우드
        서비스 이름은 **DNS 접두사**입니다.

        ![image](./media/cloud-services-dotnet-autoscaling-application-block/autoscaling06.png)
 
	-   **[targetrolename]:** 자동 크기 조정 규칙의 대상인 역할의 이름입니다.

    1.  Azure 관리 포털에서
        **클라우드 서비스**를 클릭합니다.

    2.  클라우드 서비스 목록에서 자동 크기 조정을 사용하려는 응용 프로그램을 호스트하는
        서비스를 클릭한 다음
        **인스턴스**를 클릭합니다. **역할** 열에서 대상 역할의 이름을
        나타냅니다.

        ![image](./media/cloud-services-dotnet-autoscaling-application-block/autoscaling07.png)


	-   **[storageaccountname]** 및 **[storageaccountkey]:** 대상 Azure 응용 프로그램에 사용 중인 Azure 저장소 계정의 이름입니다.

    1.  Azure 관리 포털에서
        **저장소**를 클릭합니다.

    2.  저장소 계정 목록에서  사용하는 저장소 계정입니다. 이
        **이름** 열에 **이름**이 표시됩니다.

    3.  화면 아래쪽에 있는 **관리 키** 단추를 클릭하여
        기본 액세스 키를 가져옵니다.

        ![image](./media/cloud-services-dotnet-autoscaling-application-block/autoscaling08.png)
  
 
	-   **[managementcertificatethumbprint]:** 블록이 대상 응용 프로그램에 대한 크기 조정 요청을 보호하는 데 사용하는 관리 인증서의 **손도장**입니다.

    1.  Azure 관리 포털에서
        **설정**을 클릭하십시오.

    2.  **손도장** 열에 **손도장**이 표시됩니다.

        ![image](./media/cloud-services-dotnet-autoscaling-application-block/autoscaling09.png)
 

서비스 모델 파일의 내용에 대한 자세한 정보를 보려면
[서비스 정보 데이터 저장][]을 참조하세요.

## <a id="DefineAutoscalingRules"> </a>방법: 자동 크기 조정 규칙 정의

일반적으로 대상 응용 프로그램의 역할 인스턴스의 개수를 제어하는 자동 크기 조정 규칙을
XML 파일에 저장합니다. 이 XML 파일에 대한
스키마의 복사본은 프로젝트의 **AutoscalingRules.xsd**
파일에서 찾을 수 있습니다. Visual Studio에서 이 스키마는
XML 파일을 편집할 때 Intellisense 및 유효성 검사를 제공합니다.

프로젝트에서 **rules.xml**이라는 새 XML 파일을 만드세요.

Visual Studio에서 규칙 파일이 출력 폴더에 복사되었는지
확인해야 합니다. 다음을 수행합니다.

1.  해당 파일을 마우스 오른쪽 단추로 클릭하고 **속성**을 선택합니다.

2.  속성 창에서 **출력 디렉터리로 복사**
    값을 **항상 복사**로 설정합니다.

다음 코드 샘플은 **rules.xml**
파일의 예제 규칙 집합을 보여 줍니다.

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

이 예제에는 3개의 자동 크기 조정 규칙(1개의 **제약 조건
규칙** 및 2개의 **반응 규칙**)이 있습니다.
**AutoscalingApplicationRole**이라는 대상에서 작동하며 이는
**서비스 모델**에서 정의된 규칙의 별명입니다.

-   제약 조건 규칙은 항상 활성화되어 있으며 역할 인스턴스의 최소 개수를 2로,
    최대 개수를 6으로
    설정합니다.

-   2개의 반응 규칙은 **피연산자**인
    **WebRoleA\_CPU\_Avg\_5m**을
    사용하며
    **AutoscalingApplicationRole**이라는 Azure 역할의 지난 5분 간의 평균 CPU 사용량을 계산합니다.
    이 역할은 **서비스 모델**에서 정의됩니다.

-   **ScaleUpOnHighUtilization**이라는 반응 규칙은
    지난 5분 간의 평균 CPU 사용률이
    60% 이상인 경우
    대상 역할의 인스턴스 수를 1에서 증가합니다.

-   **ScaleDownOnLowUtilization**이라는 반응 규칙은
    지난 5분 간의 평균 CPU 사용률이
    60% 미만인 경우 대상 역할의 인스턴스 수를 1에서 감소합니다.

## <a id="Configure"> </a>방법: 자동 크기 조정 응용 프로그램 블록 구성

서비스 모델 및 자동 크기 조정 규칙을 정의한 후
자동 크기 조정 응용 프로그램 블록을 구성하여 사용해야 합니다. 이
이 작동 구성 정보는 응용 프로그램 구성 파일에
저장됩니다.

기본적으로 자동 크기 조정 응용 프로그램 블록에서는 자동 크기 조정
규칙 및 서비스 모델이 Azure blob에 저장되기를 예상합니다. 이
예제에서는 자동 크기 조정 규칙 및 서비스 모델을 로컬 파일 시스템에서 로드하도록 블록을 구성했다고
가정합니다.

### 호스트 응용 프로그램에서 자동 크기 조정 응용 프로그램 블록 구성

1.  솔루션 탐색기에서 **App.config** 파일을 마우스 오른쪽 단추로 클릭한 다음
    **구성 파일 편집**을 클릭합니다.

2.  **블록** 메뉴에서 **자동 크기 조정 설정 추가**를 클릭합니다.  
	![image](./media/cloud-services-dotnet-autoscaling-application-block/autoscaling10.png)
  
3.  **자동 크기 조정 설정**을 확장한 다음
    **데이터 포인트 저장 저장소 계정** 옆에 있는 줄임표(...)를 클릭하고, Azure 저장소 계정의 **계정
    이름** 및 **계정 키**를 추가합니다.
    블록이 여기에 수집한 데이터 포인트를 저장합니다(이 값의 위치를 잘 모르는 경우
    [방법: 서비스 모델 정의][] 참조).
    그런 다음 **확인**을 클릭합니다.  

	![image](./media/cloud-services-dotnet-autoscaling-application-block/autoscaling11.png)

4.  **자동 크기 조정 설정** 섹션을 확장하고 **규칙
    저장소** 및 **서비스 정보 저장소** 섹션을 표시합니다. 기본적으로 이러한
    설정은 Azure Blob 저장소를 사용하도록 구성되어 있습니다.  
	![image](./media/cloud-services-dotnet-autoscaling-application-block/autoscaling12.png)


5.  **규칙 저장소** 옆에 있는 더하기 기호(+)를 클릭하여 **규칙
    저장소 설정**을 가리킨 다음 **로컬 파일 규칙 저장소 사용**을 클릭하고
    **예**를 클릭합니다.

6.  **파일 이름** 상자에 **rules.xml**을 입력합니다. 다음은
    자동 크기 조정 규칙을 포함하는 파일의 이름입니다.  
	![image](./media/cloud-services-dotnet-autoscaling-application-block/autoscaling13.png)


7.  **서비스 정보 저장소** 옆에 있는 더하기 기호(+)를 클릭하여
    **서비스 정보 저장소 설정**을 가리킨 다음 **로컬 파일
    서비스 정보 저장소 사용**을 클릭하고 **예**를 클릭합니다.

8.  **파일 이름** 상자에 **services.xml**을 입력합니다. 다음은
    자동 크기 조정 규칙을 포함하는 파일의 이름입니다.  
	![image](./media/cloud-services-dotnet-autoscaling-application-block/autoscaling14.png)


9.  엔터프라이즈 라이브러리 구성 창의 **파일**
    메뉴에서 **저장**을 클릭하여 구성 변경 내용을 저장합니다. 그 다음에
    엔터프라이즈 라이브러리 구성 창의 **파일** 메뉴에서
    **끝내기**를 클릭합니다.

자동 크기 조정
응용 프로그램 블록의 수행 동작에 관한 정보를 보려면 작성되는 로그 메시지를 캡처해야
합니다. 예: 콘솔 응용 프로그램에서 블록을 호스팅하는 경우
Visual Studio의 출력 창에서 로그 메시지를 볼 수
있습니다. 다음 섹션에서는 이 동작을 구성하는 방법을 보여
줍니다.

### 자동 크기 조정 응용 프로그램 블록 호스트 응용 프로그램 로그인 구성

1.  Visual Studio에서 솔루션 탐색기의 **App.config** 파일을 두 번 클릭하면
    편집기가 열립니다. 그 후에
    다음 샘플처럼 **system.diagnostics** 섹션을 추가합니다.

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

이제 자동 크기 조정 응용 프로그램 블록 호스트 콘솔
응용 프로그램을 실행하고 대상 Azure 응용 프로그램과 작동하는 자동 크기 조정 규칙을
확인합니다. 호스트 콘솔 응용 프로그램을 실행할 때
Visual Studio의 출력 창에 다음과 비슷한 메시지가
나타납니다. 이러한 로그 메시지를 통해 블록 동작을 이해할 수
있습니다. 예를 들어 블록에 일치하는 규칙 및
블록 동작을 나타냅니다.

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

## <a id="NextSteps"> </a>다음 단계

이제 자동 크기 조정 응용 프로그램 블록 사용에 대한 기본 사항을 익혔으므로
다음 링크를 따라 좀 더 복잡한 자동 크기 조정 시나리오를 구현하는 방법을
알아보세요.

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
  [자동 크기 조정 응용 프로그램 블록에 대해 호스트 응용 프로그램 설정]: #CreateHost
  [방법: 자동 크기 조정기 인스턴스화 및 실행]: #Instantiate
  [방법: 서비스 모델 정의]: #DefineServiceModel
  [방법: 자동 크기 조정 규칙 정의]: #DefineAutoscalingRules
  [방법: 자동 크기 조정 응용 프로그램 블록 구성]: #Configure
  [Azure에서 성능 카운터 사용하기]: http://azure.microsoft.com/develop/net/common-tasks/performance-profiling/
  [NuGet]: http://nuget.org/
  [Azure 관리 포털]: http://manage.windowsazure.com
  [서비스 정보 데이터 저장]: http://msdn.microsoft.com/library/hh680878(PandP.50).aspx		
  [작업자 역할에서 자동 크기 조정 응용 프로그램 블록 호스팅]: http://msdn.microsoft.com/library/hh680914(PandP.50).aspx
  [제한 동작 구현]: http://msdn.microsoft.com/library/hh680896(PandP.50).aspx
  [규칙 순위 및 조정 이해]: http://msdn.microsoft.com/library/hh680923(PandP.50).aspx
  [자동 크기 조정 응용 프로그램 블록 확장 및 수정]: http://msdn.microsoft.com/library/hh680889(PandP.50).aspx
  [최적화 안정기를 사용하여 고주파 진동 방지 및 비용 최적화]: http://msdn.microsoft.com/library/hh680951(PandP.50).aspx
  [알림 및 수동 크기 조정 사용]: http://msdn.microsoft.com/library/hh680885(PandP.50).aspx
  [확장 그룹 정의]: http://msdn.microsoft.com/library/hh680902(PandP.50).aspx
  [WASABiCmdlet을 사용하여 Windows PowerShell을 통해 블록 조작]: http://msdn.microsoft.com/library/hh680938(PandP.50).aspx
  [Azure용 Enterprise Library 5.0 통합 팩에 대한 개발자 가이드]: http://msdn.microsoft.com/library/hh680949(PandP.50).aspx
  [Sage가 자동 크기 조정을 사용하여 Azure 호스팅 비용을 절감하는 방법]: http://msdn.microsoft.com/library/jj838716(PandP.50).aspx
  [TechNet 및 MSDN 호스팅 비용과 Azure에서 자동 크기 조정으로 인한 환경적 영향 줄이기]: http://msdn.microsoft.com/library/jj838718(PandP.50).aspx

<!--HONumber=45--> 
