<properties 
   pageTitle="시작에 실패한 역할의 문제 해결 | Microsoft Azure"
   description="클라우드 서비스 역할이 시작에 실패한 이유에 대한 몇 가지 일반적인 원인은 다음과 같습니다. 또한 이러한 문제에 대한 솔루션이 제공됩니다."
   services="cloud-services"
   documentationCenter=""
   authors="dalechen"
   manager="msmets"
   editor=""
   tags="top-support-issue"/>
<tags 
   ms.service="cloud-services"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="tbd"
   ms.date="10/14/2015"
   ms.author="daleche" />

# 시작에 실패한 클라우드 서비스 역할의 문제를 해결하는 일반적인 단계

시작에 실패한 Azure 클라우드 서비스 역할에 관련된 일반적인 문제 및 솔루션은 다음과 같습니다.

## Azure 고객 지원 서비스에 문의

이 문서의 어디에서든 도움이 필요한 경우 [MSDN Azure 및 스택 오버플로 포럼](http://azure.microsoft.com/support/forums/)에서 Azure 전문가에게 문의할 수 있습니다.

또는 Azure 기술 지원 인시던트를 제출할 수도 있습니다. [Azure 지원 사이트](http://azure.microsoft.com/support/options/)로 이동한 다음 **지원 받기**를 클릭합니다. Azure 지원을 사용하는 방법에 대한 자세한 내용은 [Microsoft Azure 지원 FAQ](http://azure.microsoft.com/support/faq/)를 참조하세요.


## DLL 또는 종속성 누락
응답하지 않는 역할 및 **초기화 중**, **사용 중**, 및 **중지** 상태를 반복하는 역할은 누락된 DLL 또는 어셈블리 때문에 발생할 수 있습니다.

**증상:** 누락된 DLL 또는 어셈블리의 증상은 다음과 같을 수 있습니다.

- 역할 인스턴스가 **초기화 중**/**사용 중**/**중지** 상태를 반복하고 있습니다.
- 역할 인스턴스가 **준비**로 이동했지만 웹 응용 프로그램에 탐색 페이지가 나타나지 않습니다.

해결 방법: 이러한 문제를 조사하기 위해 권장되는 세 가지 방법이 있습니다.

## 웹 역할에서 누락된 DLL 문제 진단

웹 역할에 배포된 웹 사이트로 이동하고 브라우저가 아래와 유사한 서버 오류를 표시하는 경우입니다.

!['/' 응용 프로그램의 서버 오류.](./media/cloud-services-troubleshoot-roles-that-fail-start/ic503388.png)

## 사용자 지정 오류를 해제하여 문제 진단

웹 역할에 대해 web.config가 사용자 지정 오류 모드를 꺼짐으로 설정하고 서비스를 다시 배포하도록 구성하여 오류를 더 자세하게 볼 수 있습니다.

원격 데스크톱을 사용하지 않고 오류를 더 자세하게 보려면

1. Visual Studio에서 솔루션을 엽니다.

2. **솔루션 탐색기**에서 web.config 파일을 찾아 엽니다.

3. Web.config 파일에서 system.web 섹션을 찾아서 다음 줄을 추가합니다.

    ```xml
    <customErrors mode="Off" />
    ```

4. 파일을 저장합니다.

5. 서비스를 다시 패키지하고 다시 배포합니다.

서비스가 다시 배포되면 누락된 어셈블리나 DLL의 이름으로 아래의 오류가 표시됩니다.

## 원격으로 오류를 확인하여 문제 진단

원격 데스크톱을 사용하여 역할에 액세스하고 원격으로 오류를 더 자세하게 볼 수 있습니다. 원격 데스크톱을 사용하여 오류를 보려면 다음 단계를 수행합니다.

1. Azure SDK 1.3 이상이 설치되어야 합니다.

2. Visual Studio를 사용하여 솔루션을 배포하는 동안 "원격 데스크톱 연결 구성..."을 선택합니다. 원격 데스크톱 연결 구성에 대한 자세한 내용은 [Azure 역할로 원격 데스크톱 사용](https://msdn.microsoft.com/library/gg443832.aspx)을 참조하세요.

3. Microsoft Azure 관리 포털에서 인스턴스 상태가 **준비**로 표시되면 역할 인스턴스 중 하나를 클릭합니다.

4. 리본의 **원격 액세스** 영역에서 **연결** 아이콘을 클릭합니다.

5. 원격 데스크톱을 구성하는 동안 지정한 자격 증명을 사용하여 가상 컴퓨터에 로그인합니다.

6. 명령 프롬프트를 엽니다.

7. `IPconfig`를 입력합니다.

8. IPV4 주소 값을 적습니다.

9. Internet Explorer를 엽니다.

10. 웹 응용 프로그램의 주소 및 이름을 입력합니다. 예: `http://<IPV4 Address>/default.aspx`.

웹 사이트를 탐색하면 더 구체적인 오류 메시지가 반환됩니다.

* '/' 응용 프로그램의 서버 오류

* 설명: 현재 웹 요청을 실행하는 동안 처리되지 않은 예외가 발생했습니다. 오류에 대한 자세한 내용 및 코드에서 어디에 기반하는지는 스택 추적을 검토합니다.

* 예외 세부 정보: System.IO.FIleNotFoundException: 파일이나 어셈블리를 로드할 수 없습니다 'Microsoft.WindowsAzure.StorageClient, Version=1.1.0.0, Culture=neutral, PublicKeyToken=31bf856ad364e35’ 또는 해당 종속성 중 하나입니다. 시스템은 지정된 파일을 찾을 수 없습니다.

예:

!['/' 응용 프로그램의 명시적 서버 오류](./media/cloud-services-troubleshoot-roles-that-fail-start/ic503389.png)

## 계산 에뮬레이터를 사용하여 문제 진단

Azure Microsoft Azure 계산 에뮬레이터를 사용하여 누락된 종속성 및 web.config 오류 문제를 진단하고 해결할 수 있습니다.

이 진단 방법을 사용하여 최상의 결과가 발생한 경우 Windows 새로 설치한 컴퓨터 또는 가상 컴퓨터를 사용해야 합니다. Azure 환경을 시뮬레이션하려면 Windows Server 2008 R2 x64를 사용해야 합니다.

1. [Azure SDK](https://azure.microsoft.com/downloads)의 독립 실행형 버전 설치

2. 개발 컴퓨터에서 클라우드 서비스 프로젝트를 빌드합니다.

3. Windows Explorer에서 클라우드 서비스 프로젝트의 bin\\debug 폴더로 이동합니다.

4. 문제를 디버깅하는 데 사용하는 컴퓨터에 .csx 폴더와.cscfg 파일을 복사합니다.

5. 클린 컴퓨터에서 Azure SDK 명령 프롬프트를 열고 `csrun.exe /devstore:start`을 입력합니다.

6. 명령 프롬프트에 `run csrun <path to .csx folder> <path to .cscfg file> /launchBrowser`을 입력합니다.

7. 역할이 시작될 때 Internet Explorer에서 자세한 오류 정보가 표시됩니다. 또한 표준 Windows 문제해결 도구를 사용하여 추가로 문제를 진단할 수 있습니다.

## IntelliTrace를 사용하여 문제 진단

.NET Framework 4를 사용하는 작업자 및 웹 역할의 경우 [Microsoft Visual Studio Ultimate](https://www.visualstudio.com/products/visual-studio-ultimate-with-MSDN-vs)에서 사용 가능한 [IntelliTrace](https://msdn.microsoft.com/library/dd264915.aspx)를 사용할 수 있습니다.

IntelliTrace를 사용하는 서비스를 배포하려면 다음 단계를 수행합니다.

1. Azure SDK 1.3 이상이 설치되었는지 확인합니다.

2. Visual Studio를 사용하여 솔루션을 배포합니다. 배포하는 동안 **.NET 4 역할에 IntelliTrace 사용** 확인란을 선택합니다.

3. 인스턴스가 시작되면 **서버 탐색기**를 엽니다.

4. **Azure\\클라우드 서비스** 노드를 확장하고 배포를 찾습니다.

5. 역할 인스턴스를 확인할 때까지 배포를 확장합니다. 마우스 오른쪽 단추로 인스턴스 중 하나를 클릭합니다.

6. **IntelliTrace 로그 보기**를 선택합니다. **IntelliTrace 요약**이 열립니다.

7. 요약의 예외 섹션을 찾습니다. 예외가 있는 경우 **예외 데이터**로 표시됩니다.

8. **예외 데이터**를 확장하고 다음과 비슷한 **System.IO.FileNotFoundException** 오류를 찾습니다.

![예외 데이터, 파일 또는 어셈블리 누락](./media/cloud-services-troubleshoot-roles-that-fail-start/ic503390.png)

## 누락된 DLL 및 어셈블리 주소 지정

누락된 DLL 및 어셈블리 오류에 주소를 지정하려면 다음 단계를 수행합니다.

1. Visual Studio에서 솔루션을 엽니다.

2. **솔루션 탐색기**에서 **참조** 폴더를 엽니다.

3. 오류에서 식별된 어셈블리를 클릭합니다.

4. **속성** 창에서 복사 로컬 속성을 찾아 값을 **True**로 설정합니다.

5. 호스티드 서비스를 다시 배포합니다.

모든 오류가 수정되었다고 확인되면 **.NET 4 역할에 IntelliTrace 사용** 설정을 선택하지 않고 서비스를 배포할 수 있습니다.

## 다음 단계

클라우드 서비스에 대한 [문제해결 문서](..\?tag=top-support-issue&service=cloud-services)를 더 봅니다.

<!---HONumber=Oct15_HO4-->