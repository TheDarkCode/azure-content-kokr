<properties
   pageTitle="Azure 가상 컴퓨터의 SQL Server에 대한 AlwaysOn 가용성 그룹용 수신기 만들기"
   description="Azure 가상 컴퓨터의 SQL Server에 대한 AlwaysOn 가용성 그룹용 수신기를 만드는 단계별 지침"
   services="virtual-machines"
   documentationCenter="na"
   authors="MikeRayMSFT"
   manager="jhubbard"
   editor="monicar"/>

<tags
   ms.service="virtual-machines"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows-sql-server"
   ms.workload="infrastructure-services"
   ms.date="07/12/2016"
   ms.author="MikeRayMSFT"/>

# Azure에서 AlwaysOn 가용성 그룹에 대한 내부 부하 분산 장치 구성

이 항목에서는 리소스 관리자 모델에서 실행 중인 Azure 가상 컴퓨터에서 SQL Server AlwaysOn 가용성 그룹에 대한 내부 부하 분산 장치를 만드는 방법을 설명합니다. SQL Server 인스턴스가 Azure 가상 컴퓨터에 있는 경우 AlwaysOn 가용성 그룹을 사용하려면 부하 분산 장치가 필요합니다. 부하 분산 장치는 가용성 그룹 수신기의 IP 주소를 저장합니다. 가용성 그룹이 여러 지역에 분산된 경우 각 지역에 부하 분산 장치가 있어야 합니다.

이 작업을 완료하려면 리소스 관리자 모델의 Azure 가상 컴퓨터에 SQL Server AlwaysOn 가용성 그룹이 배포되어야 합니다. 두 SQL Server 가상 컴퓨터는 동일한 가용성 집합에 속해야 합니다. [Microsoft 템플릿](virtual-machines-windows-portal-sql-alwayson-availability-groups.md)을 사용하여 Azure Resource Manager에서 AlwaysOn 가용성 그룹을 자동으로 만들 수 있습니다. 이 템플릿은 내부 부하 분산 장치를 자동으로 만듭니다.

원하는 경우 [수동으로 AlwaysOn 가용성 그룹을 구성](virtual-machines-windows-portal-sql-alwayson-availability-groups-manual.md)할 수 있습니다.

이 항목에서는 가용성 그룹이 이미 구성되어 있어야 합니다.

관련 항목은 다음과 같습니다.

 - [Azure VM의 AlwaysOn 가용성 그룹 구성(GUI)](virtual-machines-windows-portal-sql-alwayson-availability-groups-manual.md)
 
 - [Azure 리소스 관리자 및 PowerShell을 사용하여 VNet-VNet 연결 구성](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md)

## 단계

이 문서를 통해 Azure 포털에서 부하 분산 장치를 만들고 구성합니다. 완료 후에는 AlwaysOn 가용성 그룹 수신기에 대한 부하 분산 장치에서 IP 주소를 사용하도록 클러스터를 구성합니다.

## Azure 포털에서 부하 분산 장치 만들기 및 구성

작업의 이 부분에서는 Azure 포털에서 다음 단계를 수행합니다.

1. 부하 분산 장치 만들기 및 IP 주소 구성

1. 백 엔드 풀 구성

1. 프로브 만들기

1. 부하 분산 규칙 설정

>[AZURE.NOTE] SQL Server가 다른 리소스 그룹 및 지역에 있는 경우 이 모든 단계를 각 리소스 그룹마다 한 번씩 두 번 수행합니다.

## 1\. 부하 분산 장치 만들기 및 IP 주소 구성

첫 번째 단계는 부하 분산 장치를 만드는 것입니다. Azure 포털에서 SQL Server 가상 컴퓨터를 포함하는 리소스 그룹을 엽니다. 리소스 그룹에서 **추가**를 클릭합니다.

- **부하 분산 장치**를 검색합니다. 검색 결과에서 **Microsoft**에서 게시하는 **부하 분산 장치**를 선택합니다.

- **부하 분산 장치** 블레이드에서 **만들기**를 클릭합니다.

- **부하 분산 장치 만들기**에서 다음과 같이 부하 분산 장치를 구성합니다.

| 설정 | 값 |
| ----- | ----- |
| **Name** | 부하 분산 장치를 나타내는 텍스트 이름입니다. 예를 들어 **sqlLB**입니다. |
| **스키마** | **내부** |
| **가상 네트워크** | SQL Server가 있는 가상 네트워크를 선택합니다. |
| **서브넷** | SQL Server가 있는 서브넷을 선택합니다. |
| **구독** | 구독이 여러 개인 경우 이 필드가 나타날 수 있습니다. 이 리소스와 연결할 구독을 선택합니다. 일반적으로 가용성 그룹에 대한 모든 리소스와 동일한 구독입니다. |
| **리소스 그룹** | SQL Server가 있는 리소스 그룹을 선택합니다. | 
| **위치** | SQL Server가 있는 Azure 위치를 선택합니다. |

- **만들기**를 클릭합니다.

위에서 구성한 부하 분산 장치가 만들어집니다. 부하 분산 장치는 특정 네트워크, 서브넷, 리소스 그룹 및 위치에 속합니다. 완료되면 Azure에서 부하 분산 장치 설정을 확인합니다.

이제 부하 분산 장치 IP 주소를 구성합니다.

- 부하 분산 장치 **설정** 블레이드에서 **IP 주소**를 클릭합니다. **IP 주소** 블레이드에는 이 장치가 SQL Server와 동일한 가상 네트워크의 개인 부하 분산 장치임이 표시됩니다.

- 다음 설정을 지정합니다.

| 설정 | 값 |
| ----- | ----- |
| **서브넷** | SQL Server가 있는 서브넷을 선택합니다. |
| **할당** | **정적** |
| **IP 주소** | 서브넷에서 사용되지 않는 가상 IP 주소를 입력합니다. |

- 설정을 저장합니다.

이제 부하 분산 장치에 IP 주소가 지정됩니다. 이 IP 주소를 기록합니다. 클러스터에서 수신기를 만들 때 이 IP 주소를 사용합니다. 이 주소는 이 문서 뒷부분의 PowerShell 스크립트에서 `$ILBIP` 변수에 대해 사용됩니다.



## 2\. 백 엔드 풀 구성

다음 단계는 백 엔드 주소 풀을 만드는 것입니다. 백 엔드 주소 풀 *백 엔드 풀*이 호출됩니다. 이 경우 백 엔드 풀은 가용성 그룹에 있는 두 SQL Server의 주소입니다.

- 리소스 그룹에서, 만든 부하 분산 장치를 클릭합니다.

- **설정**에서 **백 엔드 풀**을 클릭합니다.

- **백 엔드 주소 풀**에서 **추가**를 클릭하여 백 엔드 주소 풀을 만듭니다.

- **백 엔드 풀 추가**의 **이름**에 백 엔드 풀의 이름을 입력합니다.

- **가상 컴퓨터**에서 **+ 가상 컴퓨터 추가**를 클릭합니다.

- **가상 컴퓨터 선택**에서 **가용성 집합 선택**을 클릭하고 SQL Server 가상 컴퓨터가 속한 가용성 집합을 지정합니다.

- 가용성 집합을 선택한 후 **가상 컴퓨터 선택**을 클릭합니다. 가용성 그룹에서 SQL Server 인스턴스를 호스트하는 두 가상 컴퓨터를 클릭합니다. **선택**을 클릭합니다.

- **확인**을 클릭하여 **가상 컴퓨터 선택** 및 **백 엔드 풀 추가**에 대한 블레이드를 닫습니다.

백 엔드 주소 풀에 대한 설정이 업데이트됩니다. 이제 가용성 집합에는 두 개의 SQL Server 풀이 있습니다.

## 3\. 프로브 만들기

다음 단계는 프로브를 만드는 것입니다. 프로브는 Azure에서 현재 가용성 그룹 수신기를 소유하는 SQL Server를 확인하는 방법을 정의합니다. Azure는 프로브를 만들 때 정의한 포트의 IP 주소를 기반으로 서비스를 프로브합니다.

- 부하 분산 장치 **설정** 블레이드에서 **프로브**를 클릭합니다.

- **프로브** 블레이드에서 **추가**를 클릭합니다.

- **프로브 추가** 블레이드에서 프로브를 구성합니다. 다음 값을 사용하여 프로브를 구성합니다.

| 설정 | 값 |
| ----- | ----- |
| **Name** | 프로브를 나타내는 텍스트 이름입니다. 예를 들어 **SQLAlwaysOnEndPointProbe**입니다. |
| **프로토콜** | **TCP** |
| **포트** | 사용 가능한 모든 포트를 사용할 수 있습니다. 예를 들어 *59999*입니다. |
| **간격** | *5* | 
| **비정상 임계값** | *2* | 

- **확인**을 클릭합니다.

>[AZURE.NOTE] 지정한 포트가 두 SQL Server의 방화벽에서 열려 있는지 확인합니다. 두 서버 모두 사용하는 TCP 포트에 대한 인바운드 규칙이 필요합니다. 자세한 내용은 [방화벽 규칙 추가 또는 편집](http://technet.microsoft.com/library/cc753558.aspx)을 참조하세요.

프로브가 만들어집니다. 프로브는 가용성 그룹에 대한 수신기가 있는 SQL Server를 테스트하는 데 사용됩니다.

## 4\. 부하 분산 규칙 설정

부하 분산 규칙을 설정합니다. 부하 분산 규칙은 부하 분산 장치가 트래픽을 SQL Server로 라우트하는 방법을 구성합니다. 이 부하 분산 장치의 경우 한 번에 두 SQL Server 중 하나만 가용성 그룹 수신기 리소스를 소유하므로 DSR(Direct Server Return)이 사용됩니다.

- 부하 분산 장치 **설정** 블레이드에서 **부하 분산 규칙**을 클릭합니다.

- **부하 분산 규칙** 블레이드에서 **추가**를 클릭합니다.

- **부하 분산 규칙 추가** 블레이드를 사용하여 부하 분산 규칙을 구성합니다. 다음 설정을 사용합니다.

| 설정 | 값 |
| ----- | ----- |
| **Name** | 부하 분산 규칙을 나타내는 텍스트 이름입니다. 예를 들어 **SQLAlwaysOnEndPointListener**입니다. |
| **프로토콜** | **TCP** |
| **포트** | *1433* |
| **백 엔드 포트** | *1433*. 이 규칙은 **부동 IP(Direct Server Return)**를 사용하므로 이 설정이 사용되지 않습니다. |
| **프로브** | 이 부하 분산 장치에 대해 만든 프로브의 이름을 사용합니다. |
| **세션 지속성** | **없음** | 
| **유휴 제한 시간(분)** | *4* | 
| **부동 IP(Direct Server Return)** | **사용** | 

 >[AZURE.NOTE] 블레이드에서 모든 설정을 보려면 아래로 스크롤해야 할 수도 있습니다.

- **확인**을 클릭합니다.

- 부하 분산 규칙이 구성됩니다. 이제 가용성 그룹에 대한 수신기를 호스트하는 SQL Server로 트래픽을 라우트하도록 부하 분산 장치가 구성되었습니다.

현재 리소스 그룹에는 두 SQL Server 컴퓨터에 모두 연결하는 하나의 부하 분산 장치가 있습니다. 부하 분산 장치에는 SQL Server AlwaysOn 가용성 그룹 수신기에 대한 IP 주소도 있으므로 두 컴퓨터 중 하나가 가용성 그룹에 대한 요청에 응답할 수 있습니다.

>[AZURE.NOTE] SQL Server가 두 개의 별도 지역에 있는 경우 다른 지역에서 단계를 반복합니다. 각 지역마다 하나의 부하 분산 장치가 필요합니다.

## 부하 분산 장치 IP 주소를 사용하도록 클러스터 구성 

다음 단계는 클러스터에서 수신기를 구성하고 수신기를 온라인 상태로 전환하는 것입니다. 이 작업을 완료하려면 다음을 수행합니다.

1. 장애 조치(failover) 클러스터에서 가용성 그룹 수신기 만들기

1. 수신기를 온라인 상태로 만들기

## 1\. 장애 조치(failover) 클러스터에서 가용성 그룹 수신기 만들기

이 단계에서는 수동으로 장애 조치(Failover) 클러스터 관리자 및 SSMS(SQL Server Management Studio)에서 가용성 그룹 수신기를 만듭니다.

- RDP를 사용하여 주 복제본을 호스트하는 Azure 가상 컴퓨터에 연결합니다.

- 장애 조치(failover) 클러스터 관리자를 엽니다.

- **네트워크** 노드를 선택하고 클러스터 네트워크 이름을 확인합니다. 이 이름은 PowerShell 스크립트에서 `$ClusterNetworkName` 변수에 사용됩니다.

- 클러스터 이름을 확장한 다음 **역할**을 클릭합니다.

- **역할** 창에서 가용성 그룹 이름을 마우스 오른쪽 단추로 클릭한 다음 **리소스 추가** > **클라이언트 액세스 지점**을 선택합니다.

- **이름** 상자에 이 새 수신기에 대한 이름을 입력하고 **다음**을 두 번 클릭한 다음 **마침**을 클릭합니다. 현재 온라인 상태에서 수신기 또는 리소스를 가져오지 마세요.

 >[AZURE.NOTE] 새 수신기 이름은 응용 프로그램에서 SQL Server 가용성 그룹의 데이터베이스에 연결하는 데 사용할 네트워크 이름입니다.

- **리소스** 탭을 클릭한 다음 방금 만든 클라이언트 액세스 지점을 확장합니다. IP 리소스를 마우스 오른쪽 단추로 클릭하고 속성을 클릭합니다. IP 주소의 이름을 적어둡니다. 이 이름은 PowerShell 스크립트에서 `$IPResourceName` 변수에 사용됩니다.

- **IP 주소**에서 **고정 IP 주소**를 클릭하고 Azure 포털에서 부하 분산 장치 IP 주소를 설정할 때 사용된 주소와 동일한 주소로 고정 IP 주소를 설정합니다. 이 주소에 대해 NetBIOS를 사용하도록 설정하고 **확인**을 클릭합니다. 솔루션이 여러 Azure Vnet에 걸쳐 있는 경우 각 IP 리소스에 대해 이 단계를 반복합니다.

- 현재 주 복제본을 호스트하는 클러스터 노드에서 관리자 권한으로 PowerShell ISE를 열고 새 스크립트에 다음 명령을 붙여넣습니다.

        $ClusterNetworkName = "<MyClusterNetworkName>" # the cluster network name (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name)
        $IPResourceName = "<IPResourceName>" # the IP Address resource name
        $ILBIP = “<X.X.X.X>” # the IP Address of the Internal Load Balancer (ILB). This is the static IP address for the load balancer you configured in the Azure portal.
    
        Import-Module FailoverClusters
    
        Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$ILBIP";"ProbePort"="59999";"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"EnableDhcp"=0}

- 변수를 업데이트하고 PowerShell 스크립트를 실행하여 새 수신기에 대한 IP 주소와 포트를 구성합니다.

 >[AZURE.NOTE] SQL Server가 별도 지역에 있는 경우 PowerShell 스크립트를 두 번 실행해야 합니다. 먼저 클러스터 네트워크 이름, 클러스터 IP 리소스 이름 및 첫 번째 리소스 그룹의 부하 분산 장치 IP 주소를 사용합니다. 그런 다음 클러스터 네트워크 이름, 클러스터 IP 리소스 이름 및 두 번째 리소스 그룹의 부하 분산 장치 IP 주소를 사용합니다.

이제 클러스터에 가용성 그룹 수신기 리소스가 있습니다.

## 2\. 수신기를 온라인 상태로 만들기

구성된 가용성 그룹 수신기 리소스를 사용하여 수신기를 온라인 상태로 만들 수 있습니다. 이렇게 하면 응용 프로그램에서 수신기를 사용하여 가용성 그룹의 데이터베이스에 연결할 수 있습니다.

- 다시 장애 조치(Failover) 클러스터 관리자로 이동합니다. **역할**을 확장한 다음 가용성 그룹을 강조 표시합니다. **리소스** 탭에서 수신기 이름을 마우스 오른쪽 단추로 클릭하고 **속성**을 클릭합니다.

- **종속성** 탭을 클릭합니다. 여러 리소스가 나열되어 있으면 IP 주소에 AND가 아닌 OR 종속성이 있는지 확인합니다. **확인**을 클릭합니다.

- 수신기 이름을 마우스 오른쪽 단추로 클릭하고 **온라인 상태로 전환**을 클릭합니다.


- 수신기가 온라인 상태로 전환되면 **리소스** 탭에서 가용성 그룹을 마우스 오른쪽 단추로 클릭하고 **속성**을 클릭합니다.

- 수신기 이름 리소스(IP 주소 리소스 이름이 아님)에 대한 종속성을 만듭니다. **확인**을 클릭합니다.


- SQL Server Management Studio를 시작하고 주 복제본에 연결합니다.


- **AlwaysOn 고가용성** | **가용성 그룹** | **가용성 그룹 수신기**로 이동합니다.


- 이제 장애 조치(Failover) 클러스터 관리자에서 만든 수신기 이름이 표시됩니다. 수신기 이름을 마우스 오른쪽 단추로 클릭하고 **속성**을 클릭합니다.


- **포트** 상자에서 이전에 사용한 $EndpointPort(1433이 기본값임)를 사용하여 가용성 그룹 수신기에 대한 포트 번호를 지정한 다음 **확인**을 클릭합니다.

이제 리소스 관리자 모드에서 실행 중인 Azure 가상 컴퓨터의 SQL Server AlwaysOn 가용성 그룹이 만들어졌습니다.

## 수신기에 대한 연결 테스트

연결을 테스트하려면

1. 동일한 가상 네트워크에 있지만 복제본을 소유하지 않는 SQL Server로 RDP합니다. 클러스터의 다른 SQL Server가 될 수 있습니다.

1. **sqlcmd** 유틸리티를 사용하여 연결을 테스트합니다. 예를 들어 다음 스크립트는 Windows 인증을 사용하는 수신기를 통해 주 복제본에 대한 **sqlcmd** 연결을 설정합니다.

        sqlcmd -S <listenerName> -E

SQLCMD 연결은 주 복제본을 호스트하는 SQL Server 인스턴스에 자동으로 연결합니다.

## 지침 및 제한 사항

내부 부하 분산 장치를 사용하는 Azure에서는 가용성 그룹 수신기에 다음과 같은 지침이 적용됩니다.

- 수신기는 부하 분산 장치로 구성되고 내부 부하 분산 장치 하나만 있기 때문에 클라우드 서비스당 하나의 내부 가용성 그룹 수신기만 지원됩니다. 그러나 외부 수신기는 여러 개를 만들 수 있습니다.

- 내부 부하 분산 장치를 사용할 경우 동일한 가상 네트워크 내에서만 수신기에 액세스합니다.
 

<!---HONumber=AcomDC_0720_2016-->