<properties
	pageTitle="Azure 가상 컴퓨터에서 .NET으로 계산 집약적인 작업을 실행하는 방법"
	description="Azure 가상 컴퓨터에서 계산 집약적인 .NET 앱을 배포 및 실행하고 Azure 서비스 버스 큐를 사용하여 진행률을 원격으로 모니터링하는 방법에 대해 알아봅니다."
	services="virtual-machines"
	documentationCenter=".net"
	authors="wadepickett"
	manager="wpickett"
	editor="mollybos"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="06/25/2015"
	ms.author="wpickett"/>

# Azure 가상 컴퓨터에서 .NET으로 계산 집약적인 작업을 실행하는 방법

Azure에서 가상 컴퓨터를 사용하여 계산 집약적인 작업을 처리할 수 있습니다. 예를 들어 가상 컴퓨터는 작업을 처리하고 그 결과를 클라이언트 컴퓨터 또는 모바일 응용 프로그램에 제공할 수 있습니다. 이 자습서를 완료하면 다른 .NET 응용 프로그램에서 모니터링할 수 있는 계산 집약적인 .NET 응용 프로그램을 실행하는 가상 컴퓨터를 만드는 방법을 이해할 수 있게 됩니다.

이 자습서는 .NET 콘솔 응용 프로그램을 만드는 방법을 알고 있는 개발자를 대상으로 합니다. Azure에 대한 지식은 없는 것으로 가정합니다.

다음 내용을 배웁니다.

* 가상 컴퓨터를 만드는 방법
* 가상 컴퓨터에 원격으로 로그인하는 방법
* Azure 서비스 버스 네임스페이스를 만드는 방법
* 계산 집약적인 작업을 수행하는 .NET 응용 프로그램을 만드는 방법
* 계산 집약적인 작업의 진행 상황을 모니터링하는 .NET 응용 프로그램을 만드는 방법
* .NET 응용 프로그램을 실행하는 방법
* .NET 응용 프로그램을 중지하는 방법

이 자습서에서는 계산 집약적인 작업으로 순회 외판원 문제를 사용합니다. 다음은 계산 집약적인 작업을 실행하는 .NET 응용 프로그램의 예제입니다.

![순회 외판원 문제 해 찾기][solver_output]

다음은 계산 집약적인 작업을 모니터링하는 .NET 응용 프로그램의 예제입니다.

![순회 외판원 문제 클라이언트][client_output]

[AZURE.INCLUDE [create-account-and-vms-note](../../includes/create-account-and-vms-note.md)]

## 가상 컴퓨터를 만드는 방법

1. [Azure 포털](https://manage.windowsazure.com)에 로그인합니다.
2. **새로 만들기**를 클릭합니다.
3. **가상 컴퓨터**를 클릭합니다.
4. **빠른 생성**을 클릭합니다.
5. **가상 컴퓨터 만들기** 화면에서 **DNS 이름** 값을 입력합니다.
6. **이미지** 드롭다운 목록에서 **Windows Server 2012 R2**와 같은 이미지를 선택합니다.
7. **사용자 이름** 필드에 관리자의 이름을 입력합니다. 입력하는 이름 및 암호는 나중에 가상 컴퓨터에 원격으로 로그인할 때 사용합니다.
8. **새 암호** 필드에 암호를 입력하고 **확인** 필드에 다시 입력합니다.
9. **위치** 드롭다운 목록에서 가상 컴퓨터의 데이터 센터 위치를 선택합니다.
10. **가상 컴퓨터 만들기**를 클릭합니다. Azure 포털의 **가상 컴퓨터** 섹션에서 상태를 모니터링할 수 있습니다. 상태가 **활성**으로 표시된 경우 가상 컴퓨터에 로그인할 수 있습니다.

## 가상 컴퓨터에 원격으로 로그인하는 방법

1. [Azure 포털](https://manage.windowsazure.com)에 로그인합니다.
2. **가상 컴퓨터**를 클릭합니다.
3. 로그인할 가상 컴퓨터의 이름을 클릭합니다.
4. **연결**을 클릭합니다.
5. 가상 컴퓨터에 연결해야 한다는 메시지에 응답합니다. 관리자 이름 및 암호를 묻는 메시지가 표시되면 가상 컴퓨터를 만들 때 제공한 값을 사용하십시오.

## 서비스 버스 네임스페이스를 만드는 방법

Azure에서 서비스 버스 큐 사용을 시작하려면 먼저 서비스 네임스페이스를 만들어야 합니다. 서비스 네임스페이스는 응용 프로그램 내에서 서비스 버스 리소스의 주소를 지정하기 위한 범위 컨테이너를 제공합니다.

서비스 네임스페이스를 만들려면

1.  [Azure 포털](https://manage.windowsazure.com)에 로그인합니다.
2.  Azure 포털의 왼쪽 탐색 창에서 **서비스 버스**를 클릭합니다.
3.  Azure 포털의 아래쪽 창에서 **만들기**를 클릭합니다.

    ![새 서비스 버스 만들기][create_service_bus]
4.  **네임스페이스 만들기** 대화 상자에서 네임스페이스 이름을 입력합니다. 이 이름은 고유해야 하므로, 입력한 이름을 사용할 수 있는지 시스템에서 즉시 확인합니다.

    ![네임스페이스 만들기 대화 상자][create_namespace_dialog]
5.  네임스페이스 이름을 사용할 수 있음을 확인한 후에는 네임스페이스를 호스트해야 하는 지역을 선택합니다(가상 컴퓨터가 호스트되는 것과 동일한 지역을 사용하는지 확인).

    > [AZURE.IMPORTANT]가상 컴퓨터에 사용하거나 사용하려는 것과 **같은 지역**을 선택합니다. 그러면 최상의 성능을 얻을 수 있습니다.

6. 로그온하는 데 사용한 계정에 Azure 구독을 두 개 이상 사용하는 경우 네임스페이스에 사용할 구독을 선택합니다. 로그온하는 데 사용한 계정의 구독이 하나뿐인 경우에는 구독이 포함된 드롭다운 목록이 표시되지 않습니다.
7. 확인 표시를 클릭합니다. 이제 시스템이 서비스 네임스페이스를 만들고 사용하도록 설정합니다. 시스템이 계정에 대한 리소스를 프로비전하는 동안 몇 분 정도 기다려야 할 수도 있습니다.

	![만들기 클릭 스크린샷][click_create]

만든 네임스페이스가 Azure 포털에 표시되며, 활성화되는 데 약간의 시간이 걸립니다. 다음 단계를 계속하기 전에 **활성** 상태가 될 때까지 기다리십시오.

## 네임스페이스에 대한 기본 관리 자격 증명 얻기

새 네임스페이스에 대해 큐 만들기 등의 관리 작업을 수행하려면 네임스페이스에 대한 관리 자격 증명을 받아야 합니다.

1.  왼쪽 탐색 창에서 **서비스 버스**를 클릭하여 사용 가능한 네임스페이스 목록을 표시합니다. ![사용 가능한 네임스페이스 스크린샷][available_namespaces]
2.  목록에서 방금 만든 네임스페이스를 선택합니다. ![네임스페이스 목록 스크린샷][namespace_list]
3. **연결 정보**를 클릭합니다. ![액세스 키 단추][access_key_button]
4.  대화 상자에서 **연결 문자열** 항목을 찾습니다. 자습서에서 나중에 이 정보를 사용하여 네임스페이스 관련 작업을 수행할 것이므로 이 값을 기록해 둡니다.

## 계산 집약적인 작업을 수행하는 .NET 응용 프로그램을 만드는 방법

1. 개발 컴퓨터(직접 생성한 가상 컴퓨터일 필요는 없음)에서 [.NET용 Azure SDK](http://azure.microsoft.com/develop/net/)(영문)를 다운로드합니다.
2. TSPSolver라는 프로젝트를 사용하여 .NET 콘솔 응용 프로그램을 만듭니다. 대상 프레임워크가 .**NET Framework 4** 이상(**.NET Framework 4 Client Profile**이 아님)용으로 설정되어 있는지 확인합니다. 다음으로 프로젝트를 만든 후 대상 프레임워크를 설정할 수 있습니다. Visual Studio의 메뉴에서 **프로젝트**를 클릭하고 **속성**을 클릭한 후 **응용 프로그램** 탭을 클릭한 다음 **대상 프레임워크** 값을 설정합니다.
3. Microsoft ServiceBus 라이브러리를 추가합니다. Visual Studio 솔루션 탐색기에서 **TSPSolver**를 마우스 오른쪽 단추로 클릭하고 **참조 추가**, **찾아보기** 탭을 차례로 클릭한 후 Azure .NET SDK(예를 들어 C:\\Program Files\\Microsoft SDKs\\Azure.NET SDK\\v2.5\\ToolsRef)로 이동하고 참조로 **Microsoft.ServiceBus.dll**을 선택합니다.
4. System Runtime Serialization 라이브러리를 추가합니다. Visual Studio 솔루션 탐색기에서 **TSPSolver**를 마우스 오른쪽 단추로 클릭하고 **참조 추가**를 클릭한 후 **.NET** 탭을 클릭하고 참조로 **System.Runtime.Serialization**을 선택합니다.
5. Program.cs 파일 내용으로 이 섹션 끝에 있는 예제 코드를 사용합니다.
6. 서비스 버스 **연결 문자열**을 사용하도록 **your\_connection\_string** 자리 표시자를 수정합니다.
7. 응용 프로그램을 컴파일합니다. 이렇게 하면 프로젝트의 bin 폴더(릴리스를 대상으로 하는지 디버그 빌드를 대상으로 하는지에 따라 bin\\release 또는 bin\\debug)에 TSPSolver.exe가 만들어집니다. 나중에 이 실행 파일과 Microsoft.ServiceBus.dll을 가상 컴퓨터에 복사합니다.

<p/>

	using System;
	using System.Collections.Generic;
	using System.Linq;
	using System.Text;
	using System.IO;

	using Microsoft.ServiceBus;
	using Microsoft.ServiceBus.Messaging;

	namespace TSPSolver
	{
	    class Program
	    {
	        // Value specifying how often to provide an update to the console.
	        private static long loopCheck = 100000000;
	        private static long nTimes = 0, nLoops = 0;

	        private static double[,] distances;
	        private static String[] cityNames;
	        private static int[] bestOrder;
	        private static double minDistance;

	        private static NamespaceManager namespaceManager;
	        private static QueueClient queueClient;
	        private static String queueName = "TSPQueue";

	        private static void BuildDistances(String fileLocation, int numCities)
	        {

	            try
	            {
	                StreamReader sr = new StreamReader(fileLocation);
	                String[] sep1 = { ", " };

	                double[,] cityLocs = new double[numCities, 2];

	                for (int i = 0; i < numCities; i++)
	                {
	                    String[] line = sr.ReadLine().Split(sep1, StringSplitOptions.None);
	                    cityNames[i] = line[0];
	                    cityLocs[i, 0] = Convert.ToDouble(line[1]);
	                    cityLocs[i, 1] = Convert.ToDouble(line[2]);
	                }
	                sr.Close();

	                for (int i = 0; i < numCities; i++)
	                {
	                    for (int j = i; j < numCities; j++)
	                    {
	                        distances[i, j] = hypot(Math.Abs(cityLocs[i, 0] - cityLocs[j, 0]), Math.Abs(cityLocs[i, 1] - cityLocs[j, 1]));
	                        distances[j, i] = distances[i, j];
	                    }
	                }
	            }
	            catch (Exception e)
	            {
	                throw e;
	            }
	        }

	        private static double hypot(double x, double y)
	        {
	            return Math.Sqrt(x * x + y * y);
	        }

	        private static void permutation(List<int> startCities, double distSoFar, List<int> restCities)
	        {
	            try
	            {

	                nTimes++;
	                if (nTimes == loopCheck)
	                {
	                    nLoops++;
	                    nTimes = 0;
	                    DateTime dateTime = DateTime.Now;
	                    Console.Write("Current time is {0}.", dateTime);
	                    Console.WriteLine(" Completed {0} iterations of size of {1}.", nLoops, loopCheck);
	                }

	                if ((restCities.Count == 1) && ((minDistance == -1) || (distSoFar + distances[restCities[0], startCities[0]] + distances[restCities[0], startCities[startCities.Count - 1]] < minDistance)))
	                {
	                    startCities.Add(restCities[0]);
	                    newBestDistance(startCities, distSoFar + distances[restCities[0], startCities[0]] + distances[restCities[0], startCities[startCities.Count - 2]]);
	                    startCities.Remove(startCities[startCities.Count - 1]);
	                }
	                else
	                {
	                    for (int i = 0; i < restCities.Count; i++)
	                    {
	                        startCities.Add(restCities[0]);
	                        restCities.Remove(restCities[0]);
	                        permutation(startCities, distSoFar + distances[startCities[startCities.Count - 1], startCities[startCities.Count - 2]], restCities);
	                        restCities.Add(startCities[startCities.Count - 1]);
	                        startCities.Remove(startCities[startCities.Count - 1]);
	                    }
	                }
	            }
	            catch (Exception e)
	            {
	                throw e;
	            }
	        }

	        private static void newBestDistance(List<int> cities, double distance)
	        {
	            try
	            {
	                minDistance = distance;
	                String cityList = "Shortest distance is " + minDistance + ", with route: ";

	                for (int i = 0; i < bestOrder.Length; i++)
	                {
	                    bestOrder[i] = cities[i];
	                    cityList += cityNames[bestOrder[i]];
	                    if (i != bestOrder.Length - 1)
	                        cityList += ", ";
	                }
	                Console.WriteLine(cityList);
	                queueClient.Send(new BrokeredMessage(cityList));
	            }
	            catch (Exception e)
	            {
	                throw e;
	            }
	        }

	        static void Main(string[] args)
	        {
	            try
	            {

                  String connectionString = @"your_connection_string";

	                int numCities = 10; // Use as the default, if no value is specified
	                // at the command line.
	                if (args.Count() != 0)
	                {

	                    if (args[0].ToLower().CompareTo("createqueue") == 0)
	                    {
	                        // No processing to occur other than creating the queue.
	                        namespaceManager = NamespaceManager.CreateFromConnectionString(connectionString);
	                        namespaceManager.CreateQueue(queueName);
	                        Console.WriteLine("Queue named {0} was created.", queueName);
	                        Environment.Exit(0);
	                    }

	                    if (args[0].ToLower().CompareTo("deletequeue") == 0)
	                    {
	                        // No processing to occur other than deleting the queue.
	                        namespaceManager = NamespaceManager.CreateFromConnectionString(connectionString);
	                        namespaceManager.DeleteQueue("TSPQueue");
	                        Console.WriteLine("Queue named {0} was deleted.", queueName);
	                        Environment.Exit(0);
	                    }

	                    // Neither creating or deleting a queue.
	                    // Assume the value passed in is the number of cities to solve.
	                    numCities = Convert.ToInt32(args[0]);
	                }

	                Console.WriteLine("Running for {0} cities.", numCities);

	                queueClient = QueueClient.CreateFromConnectionString(connectionString, "TSPQueue");

	                List<int> startCities = new List<int>();
	                List<int> restCities = new List<int>();

	                startCities.Add(0);
	                for (int i = 1; i < numCities; i++)
	                {
	                    restCities.Add(i);
	                }
	                distances = new double[numCities, numCities];
	                cityNames = new String[numCities];
	                BuildDistances(@"c:\tsp\cities.txt", numCities);
	                minDistance = -1;
	                bestOrder = new int[numCities];
	                permutation(startCities, 0, restCities);
	                Console.WriteLine("Final solution found!");
	                queueClient.Send(new BrokeredMessage("Complete"));

	                queueClient.Close();
	                Environment.Exit(0);

	            }
	            catch (ServerBusyException serverBusyException)
	            {
	                Console.WriteLine("ServerBusyException encountered");
	                Console.WriteLine(serverBusyException.Message);
	                Console.WriteLine(serverBusyException.StackTrace);
	                Environment.Exit(-1);
	            }
	            catch (ServerErrorException serverErrorException)
	            {
	                Console.WriteLine("ServerErrorException encountered");
	                Console.WriteLine(serverErrorException.Message);
	                Console.WriteLine(serverErrorException.StackTrace);
	                Environment.Exit(-1);
	            }
	            catch (Exception exception)
	            {
	                Console.WriteLine("Exception encountered");
	                Console.WriteLine(exception.Message);
	                Console.WriteLine(exception.StackTrace);
	                Environment.Exit(-1);
	            }
	        }
	    }
	}



## 계산 집약적인 작업의 진행 상황을 모니터링하는 .NET 응용 프로그램을 만드는 방법

1. 개발 컴퓨터에서 프로젝트 이름으로 TSPClient를 사용하는 .NET 콘솔 응용 프로그램을 만듭니다. 대상 프레임워크가 .**NET Framework 4** 이상(**.NET Framework 4 Client Profile**이 아님)용으로 설정되어 있는지 확인합니다. Visual Studio의 메뉴에서 **프로젝트**를 클릭하고 **속성**을 클릭한 후 **응용 프로그램** 탭을 클릭한 다음 **대상 프레임워크** 값을 설정하여 프로젝트를 만든 후 대상 프레임워크를 설정할 수 있습니다.
2. Microsoft ServiceBus 라이브러리에 추가합니다. Visual Studio 솔루션 탐색기에서 **TSPClient**를 마우스 오른쪽 단추로 클릭하고 **참조 추가**, **찾아보기** 탭을 차례로 클릭한 후 Azure .NET SDK(예를 들어 C:\\Program Files\\Microsoft SDKs\\Azure.NET SDK\\v2.5\\ToolsRef)로 이동하고 참조로 **Microsoft.ServiceBus.dll**을 선택합니다.
3. System Runtime Serialization 라이브러리를 추가합니다. Visual Studio 솔루션 탐색기에서 **TSPClient**를 마우스 오른쪽 단추로 클릭하고 **참조 추가**를 클릭한 후 **.NET** 탭을 클릭하고 참조로 **System.Runtime.Serialization**을 선택합니다.
4. Program.cs 파일 내용으로 이 섹션 끝에 있는 예제 코드를 사용합니다.
5. 서비스 버스 **연결 문자열**을 사용하도록 **your\_connection\_string** 자리 표시자를 수정합니다.
6. 응용 프로그램을 컴파일합니다. 이렇게 하면 프로젝트의 bin 폴더(릴리스를 대상으로 하는지 디버그 빌드를 대상으로 하는지에 따라 bin\\release 또는 bin\\debug)에 TSPClient.exe가 만들어집니다. 개발 컴퓨터에서 이 코드를 실행하거나 클라이언트 응용 프로그램(반드시 가상 컴퓨터에 있을 필요는 없음)을 실행할 컴퓨터에 이 실행 파일 및 Microsoft.ServiceBus.dll을 복사합니다.

<p/>

	using System;
	using System.Collections.Generic;
	using System.Linq;
	using System.Text;
	using System.IO;

	using Microsoft.ServiceBus;
	using Microsoft.ServiceBus.Messaging;
	using System.Threading; // For Thread.Sleep

	namespace TSPClient
	{
	    class Program
	    {

	        static void Main(string[] args)
	        {

	            try
	            {

	                Console.WriteLine("Starting at {0}", DateTime.Now);

									String connectionString = @"your_connection_string";

	                QueueClient queueClient = QueueClient.CreateFromConnectionString(connectionString, "TSPQueue");

	                BrokeredMessage message;

	                int waitMinutes = 3;  // Use as the default, if no value
	                // is specified at command line.

	                if (0 != args.Length)
	                {
	                    waitMinutes = Convert.ToInt16(args[0]);
	                }

	                String waitString;
	                waitString = (waitMinutes == 1) ? "minute" : waitMinutes.ToString() + " minutes";

	                while (true)
	                {
	                    message = queueClient.Receive();

	                    if (message != null)
	                    {
	                        try
	                        {
	                            string str = message.GetBody<string>();
	                            Console.WriteLine(str);

	                            // Remove message from queue.
	                            message.Complete();

	                            if ("Complete" == str)
	                            {
	                                Console.WriteLine("Finished at {0}.", DateTime.Now);
	                                break;
	                            }
	                        }
	                        catch (Exception e)
	                        {
	                            // Indicates a problem. Unlock the message in the queue.
	                            message.Abandon();
	                            throw e;
	                        }
	                    }
	                    else
	                    {
	                        // The queue is empty.
	                        Console.WriteLine("Queue is empty. Sleeping for another {0}.", waitString);
	                        System.Threading.Thread.Sleep(60000 * waitMinutes);
	                    }
	                }
	                queueClient.Close();
	                Environment.Exit(0);
	            }
	            catch (ServerBusyException serverBusyException)
	            {
	                Console.WriteLine("ServerBusyException encountered");
	                Console.WriteLine(serverBusyException.Message);
	                Console.WriteLine(serverBusyException.StackTrace);
	                Environment.Exit(-1);
	            }
	            catch (ServerErrorException serverErrorException)
	            {
	                Console.WriteLine("ServerErrorException encountered");
	                Console.WriteLine(serverErrorException.Message);
	                Console.WriteLine(serverErrorException.StackTrace);
	                Environment.Exit(-1);
	            }
	            catch (Exception exception)
	            {
	                Console.WriteLine("Exception encountered");
	                Console.WriteLine(exception.Message);
	                Console.WriteLine(exception.StackTrace);
	                Environment.Exit(-1);
	            }
	        }
	    }
	}

## .NET 응용 프로그램을 실행하는 방법

계산 집약적인 응용 프로그램을 실행하여 먼저 큐를 만든 후에 순회 외판원 문제를 해결합니다. 그러면 현 시점에서의 최상의 경로가 서비스 버스 큐에 추가됩니다. 계산 집약적인 응용 프로그램이 실행되는 동안 또는 실행된 이후에 클라이언트를 실행하여 서비스 버스 큐에서 가져온 결과를 표시합니다.

### 계산 집약적인 응용 프로그램을 실행하는 방법

1. 가상 컴퓨터에 로그인합니다.
2. c:\\TSP라는 폴더를 만듭니다. 응용 프로그램을 실행할 위치입니다.
3. TSPSolver 프로젝트의 bin 폴더에 있는 TSPSolver.exe 및 Microsoft.ServiceBus.dll을 c:\\TSP에 복사합니다.
4. 다음과 같은 콘텐츠가 포함된 c:\\TSP\\cities.txt 파일을 만듭니다.

		City_1, 1002.81, -1841.35
		City_2, -953.55, -229.6
		City_3, -1363.11, -1027.72
		City_4, -1884.47, -1616.16
		City_5, 1603.08, -1030.03
		City_6, -1555.58, 218.58
		City_7, 578.8, -12.87
		City_8, 1350.76, 77.79
		City_9, 293.36, -1820.01
		City_10, 1883.14, 1637.28
		City_11, -1271.41, -1670.5
		City_12, 1475.99, 225.35
		City_13, 1250.78, 379.98
		City_14, 1305.77, 569.75
		City_15, 230.77, 231.58
		City_16, -822.63, -544.68
		City_17, -817.54, -81.92
		City_18, 303.99, -1823.43
		City_19, 239.95, 1007.91
		City_20, -1302.92, 150.39
		City_21, -116.11, 1933.01
		City_22, 382.64, 835.09
		City_23, -580.28, 1040.04
		City_24, 205.55, -264.23
		City_25, -238.81, -576.48
		City_26, -1722.9, -909.65
		City_27, 445.22, 1427.28
		City_28, 513.17, 1828.72
		City_29, 1750.68, -1668.1
		City_30, 1705.09, -309.35
		City_31, -167.34, 1003.76
		City_32, -1162.85, -1674.33
		City_33, 1490.32, 821.04
		City_34, 1208.32, 1523.3
		City_35, 18.04, 1857.11
		City_36, 1852.46, 1647.75
		City_37, -167.44, -336.39
		City_38, 115.4, 0.2
		City_39, -66.96, 917.73
		City_40, 915.96, 474.1
		City_41, 140.03, 725.22
		City_42, -1582.68, 1608.88
		City_43, -567.51, 1253.83
		City_44, 1956.36, 830.92
		City_45, -233.38, 909.93
		City_46, -1750.45, 1940.76
		City_47, 405.81, 421.84
		City_48, 363.68, 768.21
		City_49, -120.3, -463.13
		City_50, 588.51, 679.33

5. 명령 프롬프트에서 디렉터리를 c:\\TSP로 변경합니다.
6. TSP 해 찾기 순열을 실행하기 전에 서비스 버스 큐를 먼저 만들어야 합니다. 다음 명령을 실행하여 서비스 버스 큐를 만듭니다.

        TSPSolver createqueue

7. 큐가 만들어졌으므로 이제 TSP 해 찾기 순열을 실행할 수 있습니다. 예를 들어 다음 명령을 실행하여 8개 도시에 대해 해 찾기를 실행합니다.

        TSPSolver 8

 숫자를 지정하지 않으면 10개 도시에 대해 실행됩니다. 해 찾기에서 현재의 최단 경로를 찾으면 해당 경로가 큐에 추가됩니다.

모든 경로에 대한 조사를 마칠 때까지 해 찾기가 실행됩니다.

> [AZURE.NOTE]지정한 숫자가 클수록 해 찾기 실행 시간이 길어집니다. 예를 들어 14개 도시에 대해 실행하면 몇 분이 걸릴 수 있고, 15개 도시에 대해 실행하면 몇 시간이 소요될 수 있습니다. 도시를 16개 이상으로 늘리면 며칠 동안, 더 나아가 수주, 수개월, 수년에 걸쳐 실행될 수도 있습니다. 이는 도시의 수가 증가함에 따라 해 찾기에 의해 평가되는 순열의 수가 급증하기 때문입니다.

### 모니터링하는 클라이언트 응용 프로그램을 실행하는 방법
1. 클라이언트 응용 프로그램을 실행할 컴퓨터에 로그인합니다. 이 컴퓨터가 TSPSolver 응용 프로그램을 실행하는 컴퓨터와 같을 수도 있지만 반드시 같아야 하는 것은 아닙니다.
2. 응용 프로그램을 실행할 폴더(예: c:\\TSP)를 만듭니다.
3. TSPClient 프로젝트의 bin 폴더에 있는 TSPClient.exe 및 Microsoft.ServiceBus.dll을 c:\\TSP 폴더에 복사합니다.
4. 명령 프롬프트에서 디렉터리를 c:\\TSP로 변경합니다.
5. 다음 명령을 실행합니다.

        TSPClient

    큐를 점검하는 시점 사이의 대기 시간(분)을 명령줄 인수로 전달하여 지정할 수도 있습니다. 큐 점검을 위한 기본 대기 기간은 3분이며, TSPClient로 명령줄 인수가 전달되지 않으면 이 값이 사용됩니다. 대기 간격으로 다른 값(예: 1분)을 사용하고 싶으면 다음 명령을 실행합니다.

	    TSPClient 1

    클라이언트는 "완료"라는 큐 메시지가 확인될 때까지 실행됩니다. 클라이언트를 실행하지 않은 상태로 해 찾기를 여러 번 실행하는 경우, 큐를 완전히 비우기 위해 클라이언트를 여러 번 실행해야 할 수도 있습니다. 또는 큐를 삭제한 후 큐를 다시 만들 수도 있습니다. 큐를 삭제하려면 다음 TSPSolver(TSPClient 아님) 명령을 실행합니다.

        TSPSolver deletequeue

## .NET 응용 프로그램을 중지하는 방법

해 찾기 및 클라이언트 응용 프로그램을 정상적인 완료 이전에 종료하고 싶으면 Ctrl+C를 누르면 됩니다.

## TSPSolver를 사용한 큐 만들기 및 제거의 대안
TSPSolver를 사용하여 큐를 만들거나 삭제하는 대신 [Azure 포털](https://manage.windowsazure.com)을 사용하여 큐를 만들거나 삭제할 수 있습니다. Azure 포털의 서비스 버스 섹션에서 큐를 만들거나 삭제할 뿐만 아니라 연결 문자열, 발급자 및 액세스 키를 검색하는 데 사용되는 사용자 인터페이스에 액세스할 수 있습니다. 서비스 버스 큐의 대시보드를 표시하여 여기서 들어오는 메시지와 보내는 메시지의 메트릭을 볼 수도 있습니다.

[solver_output]: ./media/virtual-machines-dotnet-run-compute-intensive-task/WA_dotNetTSPSolver.png
[client_output]: ./media/virtual-machines-dotnet-run-compute-intensive-task/WA_dotNetTSPClient.png
[create_service_bus]: ./media/virtual-machines-dotnet-run-compute-intensive-task/ServiceBusCreateNew.png
[create_namespace_dialog]: ./media/virtual-machines-dotnet-run-compute-intensive-task/CreateNameSpaceDialog.png
[available_namespaces]: ./media/virtual-machines-dotnet-run-compute-intensive-task/AvailableNamespaces.png
[click_create]: ./media/virtual-machines-dotnet-run-compute-intensive-task/ClickCreate.png
[namespace_list]: ./media/virtual-machines-dotnet-run-compute-intensive-task/NamespaceList.png
[access_key_button]: ./media/virtual-machines-dotnet-run-compute-intensive-task/AccessKey.png

<!---HONumber=August15_HO6-->