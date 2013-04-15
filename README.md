카카오링크 API (iOS) 
==============

카카오링크 API는 외부 앱이나 모바일 웹페이지에서 카카오톡으로 링크를 보낼 수 있고, 커스텀스킴방식으로 구현되어 어느 앱에서나 간단하게 적용할 수 있습니다.


카카오톡으로 URL 링크 전달
-------------
외부 앱에서 카카오톡 친구들에게 URL 링크 혹은 메세지(TEXT)를 전송할 수 있습니다.

> * 지원 OS: iOS, Android, 모바일웹(Blackberry 추후 지원 예정)

#### Custom URL Scheme

    kakaolink://sendurl?msg=[message]&url=[url]
    &appid=[appid]&appver=[appver]&type=[type]&appname=[appname]&apiver=[apiver]

#### 파라미터 설명

파라미터 이름   | type	| 필수 여부 | 설명 				| 비고 
---			| ---		| ---		| ---				| --- 
msg			| String	| O 		| 유저에게 전달될 메세지 내용 (UTF-8) | 
url 			| String	| O		| 유저에게 전달될 메세지에 포함되는 링크 url(모바일웹) |  
appid		| String 	| O		| App의 bundle id 또는 Package id<br/>정확히 입력하지 않을 경우 이용이 제한될 수 있습니다. | 
appname		| String	| O		| 3rd party app의 정확한 이름 | 
appver		| String 	| O 		| 3rd party app의 버전	| 
type			| String	| X 		| 카카오링크 타입 		| link (고정값)  
apiver		| String 	| X		| 카카오링크 API 버전		| 2.0 (고정값)  



카카오톡으로 APP 링크 전달
-------------
외부 앱에서 카카오톡 친구들에게 해당 앱으로 바로 연결 할수 있는 링크를 전송할 수 있습니다. 링크를 받는 사람이 해당 앱을 설치하지 않은 경우 설치마켓으로 연결 할 수 있으며, 호환되지 않는 OS의 경우 URL 링크로 대체하여 전달할 수 있습니다.

> * 지원 OS: iOS, Android, 모바일웹 (Blackberry 추후 지원 예정)
> * 지원 설치마켓: Google play(Android market), App store


#### Custom URL Scheme

    kakaolink://sendurl?msg=[message]&url=[url]&appid=[appid]&appver=[appver]
	&type=[type]&appname=[appname]&apiver=[apiver]&metainfo=[metainfo]


#### 파라미터 설명

파라미터 이름 	| type	| 필수 여부 | 설명 				| 비고 
---			| ---		| ---		| ---				| --- 
msg			| String	| O 		| 유저에게 전달된 메세지 내용 (UTF-8) | 
url 			| String	| O		| 유저에게 전달될 메세지에 포함되는 링크 url(모바일웹) |  
appid		| String 	| O		| App의 bundle id 또는 package id<br/>정확히 입력하지 않을 경우 이용이 제한될 수 있습니다. | 
appname		| String	| O		| 3rd party app의 정확한 이름 | 
appver		| String 	| O 		| 3rd party app의 버전	| 
type			| String	| X 		| 카카오링크 타입 		| link (고정값)  
apiver		| String 	| X		| 카카오링크 API 버전		| 2.0 (고정값)  
metainfo		| JSON Object |  O 	| 3rd party app을 구동시키기 위한 meta 정보 <br/>(JSONObject의 String Array 형식으로 지원) | 아래 'metainfo' 참조 


#### metainfo 설명

파라미터 이름 	| 필수 여부 | 설명 				| 비고 
---			| ---		| ---					| --- 
os			| O 		| 3rd party app이 지원하는 OS Platform | ios 또는 android 
devicetype 	| X		| 3rd party app이 지원하는 단말의 종류 | phone 또는 pad  
installurl		| X		| 3rd party app이 설치되지 않은 경우 이동할 Google Play나 iTunes의 설치 url | 
executeurl	| O 		| 3rd party app이 설치된 경우 앱을 구동시키기 위한 url<br/>(custom scheme의 형식만 지원)| 


#### 사용 예

> * APP 링크 전달을 사용할 때에는 meta 정보를 Android, iOS 모두 만들어야 합니다.

```objc
NSMutableArray *metaInfoArray = [NSMutableArray array];
    
NSDictionary *metaInfoAndroid = [NSDictionary dictionaryWithObjectsAndKeys:
                                 @"android", @"os",
                                 @"phone", @"devicetype",
                                 @"market://details?id=com.kakao.talk", @"installurl",
                                 @"example://example", @"executeurl",
                                 nil];
    
NSDictionary *metaInfoIOS = [NSDictionary dictionaryWithObjectsAndKeys:
                             @"ios", @"os",
                             @"phone", @"devicetype",
                             @"http://itunes.apple.com/app/id362057947?mt=8", @"installurl", 
                             @"example://example", @"executeurl",
                             nil];
    
[metaInfoArray addObject:metaInfoAndroid];
[metaInfoArray addObject:metaInfoIOS];
    
[KakaoLinkCenter openKakaoAppLinkWithMessage:@"First KakaoLink Message"
                                         URL:@"http://link.kakao.com/?test-ios-app"
                                 appBundleID:[[NSBundle mainBundle] bundleIdentifier]
                                  appVersion:[[NSBundle mainBundle] objectForInfoDictionaryKey:@"CFBundleShortVersionString"]
                                     appName:[[NSBundle mainBundle] objectForInfoDictionaryKey:@"CFBundleName"]
                               metaInfoArray:metaInfoArray];
```


#### Reqired Framework 

metainfo는 JSON 스트링 형태로 전달됩니다. 이에 따라 JSON 라이브러리가 요구됩니다.
예제에서는 JSONKit Framework([https://github.com/johnezang/JSONKit](https://github.com/johnezang/JSONKit))을 사용하여 작성되었습니다.


카카오 스토리로 텍스트/URL 포스팅 전송하기 
-------------
외부 앱에서 텍스트(url 포함 가능)를 카카오스토리로 포스팅할 수 있습니다. 본문에 url이 포함되어 있을 경우 해당 페이지의 썸네일 이미지, 제목, 설명이 자동으로 스크랩되어 포스팅 됩니다. 원하는 정보를 전달하려면 파라미터 중 urlinfo를 활용할 수 있습니다.
> * 지원 OS: iOS, Android, 모바일웹 
> * 카카오스토리에서의 '스크랩 타입'에 대한 정의는 [http://www.kakao.com/link/ko/api_story?tab=mobile](http://www.kakao.com/link/ko/api_story?tab=mobile) 의 내용을 참조하십시오. 

#### Custom URL Scheme

    storylink://posting?post=[post]&appid=[appid]&appver=[appver]&apiver=[apiver]&appname=[appname]&urlinfo=[urlinfo]


#### 파라미터 설명

파라미터 이름 	| type	| 필수 여부 | 설명 				| 비고 
---			| ---		| ---		| ---				| --- 
post			| String	| O 		| 사용자 메시지 내용(UTF-8) 또는 URL<br/> * 본문에 url이 포함되어 있을 경우 해당 페이지의 썸네일 이미지, 제목, 설명이 자동으로 스크랩되어 포스팅 됩니다. 원하는 정보를 전달하려면 파라미터 중 `urlinfo`를 활용할 수 있습니다.<br/> * 본문에 url이 없는 경우엔 urlinfo를 보내도 동작하지 않습니다. | 
appid		| String 	| O		| 3rd party app의 bundle id 또는 package id<br/>정확히 입력하지 않을 경우 이용이 제한될 수 있습니다. | 
appname		| String	| O		| 3rd party app의 정확한 이름 | host(출처)로 이용되니 정확히 기재
appver		| String 	| O 		| 3rd party app Version	| 
apiver		| String 	| O		| 카카오링크 API 버전		| 1.0 (고정값)  
urlinfo		| JSON Object | X 	| 스크랩 기능이 동작될 경우 원하는 정보를 보여주기 위한 설정 정보 | 아래 'urlinfo 설명' 참조 


#### urlinfo 설명

파라미터 이름 	| 필수 여부 | 설명 				| 비고 
---			| ---		| ---					| --- 
title			| O 		| 스크랩 형태에 표시되는 제목 | 
desc		 	| X		| 스크랩 형태에 표시되는 설명  | 
imageurl		| X		| 스크랩 형태에 표시되는 대표이미지 url<br/>(JSON Object의 String Array 형식으로 지원) | 
executeurl	| X 		| 스크랩 형태에 사용되는 type | video, music, book, article, profile, website<br/>기본값은 website



#### 사용 예

```objc 
if (![KakaoLinkCenter canOpenStoryLink]) {
	return;
}

NSArray *imageArray = [NSArray arrayWithObject:@"http://i4.ytimg.com/vi/gU2-ZMWm0xc/default.jpg"];
NSDictionary *urlInfoDict = [NSDictionary dictionaryWithObjectsAndKeys:
							 @"CloudAthlas", @"title",
							 @"blahblahblah", @"desc",
							 imageArray, @"imageurl",
							 nil];

[KakaoLinkCenter openStoryLinkWithPost:@"text from Storylink http://www.youtube.com/watch?v=gU2-ZMWm0xc&feature=g-vrec"
					   appBundleID:[[NSBundle mainBundle] bundleIdentifier]
							appVersion:[[NSBundle mainBundle] objectForInfoDictionaryKey:@"CFBundleShortVersionString"]
							   appName:[[NSBundle mainBundle] objectForInfoDictionaryKey:@"CFBundleName"]
							   urlInfo:urlInfoDict];
```


아이폰 OS 공유기능을 활용하여 카카오 스토리로 포스팅 전송하기 
-------------
iOS에서 제공하는 공유 기능을 활용하여 텍스트/url, 이미지를 포스팅할 수 있습니다.
구체적인 정의는 [http://developer.apple.com/library/ios/#documentation/UIKit/Reference/UIDocumentInteractionController_class/Reference/Reference.html](http://developer.apple.com/library/ios/#documentation/UIKit/Reference/UIDocumentInteractionController_class/Reference/Reference.html)를 참조하세요.

#### 사용 예

```objc
// Image를 전송할 때

NSURL *fileURL = [NSURL fileURLWithPath:[[NSBundle mainBundle] pathForResource:@"yourImage" ofType:@"jpg"]];

UIDocumentInteractionController *dic = [[UIDocumentInteractionController interactionControllerWithURL:fileURL] retain];

[dic presentOpenInMenuFromRect:self.view.frame inView:self.view animated:YES];
```

다운로드
--------------------------

### [개발자용 카카오톡 B.I 다운로드](http://www.kakao.com/link/images/v2/link/kakaotalk_icon.zip)

*B.I는 카카오링크를 이용하여 카카오톡으로 메시지를 전송하는 기능을 안내하는 버튼/페이지에 한하여 사용하셔야 하며, 다른 페이지나 메뉴 등에서 위 이미지를 그대로 또는 일부 변형하여 사용자가 카카오 제작 앱으로 혼동하게 하시면 안됩니다. 

### [개발자용 카카오스토리 B.I 다운로드](http://www.kakao.com/images/v2/link/kakaostory_icon_guide.zip)

*B.I는 스토리링크를 이용하여 카카오스토리로 포스팅하는 기능을 안내하는 버튼/페이지에 한하여 사용하셔야 하며, 다른 페이지나 메뉴 등에서 위 이미지를 그대로 또는 일부 변형하여 사용자가 카카오 제작 앱으로 혼동하게 하시면 안됩니다. 

