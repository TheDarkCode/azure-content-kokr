<properties
	pageTitle="Windows 스토어 앱에 대한 모바일 서비스 시작(C#) | Microsoft Azure"
	description="이 자습서에 따라 Azure 모바일 서비스를 사용하여 C#로 Windows 스토어 개발을 시작할 수 있습니다."
	services="mobile-services"
	documentationCenter="windows"
	authors="ggailey777"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-windows"
	ms.devlang="dotnet"
	ms.topic="get-started-article" 
	ms.date="07/21/2016"
	ms.author="glenga"/>

# <a name="getting-started"> </a>모바일 서비스 시작

[AZURE.INCLUDE [mobile-services-selector-get-started](../../includes/mobile-services-selector-get-started.md)] &nbsp;

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]
> 이 항목에 해당하는 모바일 앱 버전은 [Windows 앱 만들기](../app-service-mobile/app-service-mobile-windows-store-dotnet-get-started.md)를 참조하세요.

이 자습서에서는 Azure 모바일 서비스를 사용하여 범용 Windows 앱에 클라우드 기반 백 엔드 서비스를 추가하는 방법을 보여 줍니다. 유니버설 Windows 앱 솔루션에는 Windows 스토어 8.1 및 Windows Phone 스토어 8.1 앱용 프로젝트와 공통 공유 프로젝트가 포함됩니다. 자세한 내용은 [Windows 및 Windows Phone을 대상으로 하는 범용 Windows 앱 작성](http://msdn.microsoft.com/library/windows/apps/xaml/dn609832.aspx)을 참조하세요.

이 자습서에서는 새 모바일 서비스와 새 모바일 서비스에 앱 데이터를 저장하는 간단한 *할 일 모음* 앱을 둘 다 만듭니다. 만드는 모바일 서비스는 서버 쪽 비즈니스 논리에 JavaScript를 사용합니다. Visual Studio를 사용하여 지원되는 .NET 언어로 서버 쪽 비즈니스 논리를 작성할 수 있게 해 주는 모바일 서비스를 만들려면 이 항목의 .NET 백 엔드 버전을 참조하세요.

[AZURE.INCLUDE [mobile-services-windows-universal-get-started](../../includes/mobile-services-windows-universal-get-started.md)]

이 자습서를 완료하려면 다음이 필요합니다.

* 활성 Azure 계정. 계정이 없는 경우 Azure 평가판을 등록하고 최대 10개의 무료 모바일 서비스를 사용할 수 있습니다. 이러한 서비스는 평가판 사용 기간이 끝난 후에도 계속 사용할 수 있습니다. 자세한 내용은 [Azure 무료 체험](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fko-KR%2Fdocumentation%2Farticles%2Fmobile-services-javascript-backend-windows-store-javascript-get-started%2F)을 참조하세요.
* [Visual Studio 2013 Express for Windows]

## 새 모바일 서비스 만들기

[AZURE.INCLUDE [mobile-services-create-new-service](../../includes/mobile-services-create-new-service.md)]

## 새 유니버설 Windows 앱 만들기

모바일 서비스를 만든 후 Azure 클래식 포털에서 쉬운 빠른 시작을 따라 새 유니버설 Windows 앱을 만들거나 모바일 서비스에 연결하도록 기존 Windows 스토어 또는 Windows Phone 앱을 수정할 수 있습니다.

이 섹션에서는 모바일 서비스에 연결된 새 유니버설 Windows 앱을 만듭니다.

1.  [Azure 클래식 포털]에서 **모바일 서비스**를 클릭한 후 방금 만든 모바일 서비스를 클릭합니다.


2. 빠른 시작 탭에서 **플랫폼 선택** 아래의 **Windows**를 클릭하고 **새 Windows 스토어 앱 만들기**를 확장합니다.

   	모바일 서비스에 연결된 Windows 스토어 앱을 만드는 간편한 세 가지 단계가 표시됩니다.

  	![모바일 서비스 퀵 스타트 단계](./media/mobile-services-javascript-backend-windows-store-dotnet-get-started/mobile-quickstart-steps.png)

3. 아직 수행하지 않은 경우 로컬 컴퓨터나 가상 컴퓨터에서 [Visual Studio 2013 Express for Windows]를 다운로드하여 설치합니다.

4. **TodoItem 테이블 만들기**를 클릭하여 앱 데이터를 저장할 테이블을 만듭니다.

5. **앱 다운로드 및 실행**에서 앱의 언어를 선택하고 **다운로드**를 클릭합니다.

  	모바일 서비스에 연결된 샘플 *할 일 모음* 응용 프로그램에 대한 프로젝트가 다운로드됩니다. 압축된 프로젝트 파일을 로컬 컴퓨터에 저장하고 저장 위치를 기록해 둡니다.

## Windows 앱 실행

[AZURE.INCLUDE [mobile-services-javascript-backend-run-app](../../includes/mobile-services-javascript-backend-run-app.md)]

>[AZURE.NOTE]모바일 서비스에 액세스하여 데이터를 쿼리 및 삽입하는 코드를 검토할 수 있습니다. 이 코드는 MainPage.xaml.cs 파일에 있습니다.

## 다음 단계
이제 퀵 스타트를 완료했으며 모바일 서비스에서 중요한 추가 작업을 수행하는 방법을 알아보겠습니다.

* [오프라인 데이터 동기화 시작] 오프라인 데이터 동기화를 활용하여 응답성과 견고성이 뛰어난 앱을 제작하는 방법을 알아봅니다.

* [모바일 서비스 앱에 인증 추가][Get started with authentication] ID 공급자를 사용하여 앱 사용자를 인증하는 방법을 알아봅니다.

* [앱에 푸시 알림 추가][Get started with push notifications] 기본적인 푸시 알림을 앱에 보내는 방법을 알아봅니다.

* [.NET 클라이언트 사용 방법](mobile-services-dotnet-how-to-use-client-library.md) 모바일 서비를 쿼리하고, 데이터로 작업하고, 사용자 지정 API에 액세스하는 방법을 알아봅니다.

[AZURE.INCLUDE [app-service-disqus-feedback-slug](../../includes/app-service-disqus-feedback-slug.md)]

<!-- Anchors. -->
[Getting started with Mobile Services]: #getting-started
[Create a new mobile service]: #create-new-service
[Define the mobile service instance]: #define-mobile-service-instance
[Next Steps]: #next-steps

<!-- Images. -->



<!-- URLs. -->
[오프라인 데이터 동기화 시작]: mobile-services-windows-store-dotnet-get-started-offline-data.md
[Get started with authentication]: mobile-services-javascript-backend-windows-universal-dotnet-get-started-users.md
[Get started with push notifications]: mobile-services-javascript-backend-windows-universal-dotnet-get-started-push.md
[Visual Studio 2013 Express for Windows]: http://go.microsoft.com/fwlink/?LinkId=257546
[Mobile Services SDK]: http://go.microsoft.com/fwlink/?LinkId=257545
[Azure 클래식 포털]: https://manage.windowsazure.com/
 

<!---HONumber=AcomDC_0727_2016-->