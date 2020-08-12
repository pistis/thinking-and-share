
  
# What’s new in DevTools
> 이 문서는 '[2020 web.dev LIVE Day 2 Session - What's new in DevTools](https://www.youtube.com/watch?v=6yrJZHqJe2k&list=PLNYkxOF6rcIBhuGsbO6t8-OBE5-fVPe7K&index=3&t=0s)' 영상의 내용을 정리한 문서입니다.

## 개요
최근 Chrome DevTools에 도입된 몇 가지 기능에 대한 소개
> 발표자 : PAUL LEWIS (Chrome DevTools, lighthouse), SURMA (Google Advocate, Chrome)

## 내용

### Performance Tab
**측정 해야할 중요한 metrics**
- FCP (First Contentful Paint), LCP (Largest Contentful Paint), FID (First Input Delay), TTI (Time To Interactive), TBT (Total Blocking Time), CLS (Cumulative Layout Shift), Main Thread Task.
- 웹사이트/웹앱에서 사용자 경험을 개선하는데 도움이 되도록 설계된 측정 항목들이다.

> [https://web.dev/metrics/](https://web.dev/metrics/)을 직접 측정해보자/

#### 1. Timings Track - FCP, LCP, FID, TTI, TBT, CLS
LCP metric에 마우스 오버를 하면 화면상의 실제 가장 큰 컨텐츠가 무엇인지 알 수 있다
![image](https://user-images.githubusercontent.com/4979560/89927792-d30c3900-dc41-11ea-98eb-e8fd8ea83939.png)

> 그 외에도 위에서 말한 metric들을 볼 수 있다. (metric에 대해서는 이 지문에서는 자세히 다루지 않는다.)
> 다만, 여기까지는 이미 우리가 알고 있는 것들이다.

#### 2. Main Thread Track - task (long running task)
*tasks들이 빠르게 실행되므로 cpu throttling -> 6x slow down 으로 설정하여 측정해보자.*

Main thread의 task들을 관찰할 수 있다.  
50ms 이상의 시간이 소요된 task는 우상단에 빨간 세모 표시로 경고한다.  
이러한 task들로 인해 Main thread가 차단 되어 사용자 상호 작용에 대해 즉각 응답하지 못한다.  

왜 50ms가 기준인가?   
100ms 이내에 사용자 작업에 응답하면 사용자는 결과가 즉각적인 것처럼 느낀다.  
 ![image](https://webdev.imgix.net/rail/rail-response-details.png)
> 자세한 것은 [RAIL 모델로 성능 측정](https://web.dev/rail/#goals-and-guidelines) 참고


touch, mouse event handler등의 사용자가 실행하는 대부분의 task들은 모두 main thread에서 실행된다.  
버튼 터치, 폼 컨트롤 전환, 애니메이션 시작 등 사용자 입력에 대해서는 50ms를 적용할 수 있다.  

모든 task에 대해서 50ms는 매우 관대한 수치이다.  
초당 60프레임을 달성하려고 한다면 훨씬 더 적은 수치가 기준이 될 것이다.  

다만 최초 로딩시에는 사용자가 웹사이트와 상호작용할 것으로는 기대하지 않기 때문에 좀 더 관대할 수는 있다.  

>  Main thread가 차단 된것을 나타내는 metric이 [TBT(Total Blocking Time)](https://web.dev/tbt/?utm_source=devtools)이다.
> 다만 이시간이 반드시 Main thread가 차단된 시간은 아니다.
> 작업당 50ms정도까지는 메인쓰레드가 차단 되는 정도는 아니다. 
> 초과된 모든 것은 총 차단 시간에 포함된다. (50ms를 초과한 시간)
> ![TBT](https://user-images.githubusercontent.com/4979560/90044064-0fa26800-dd08-11ea-9c81-7780c1ccb751.jpg)


#### 3. Experience Track - layout shifting
Experience라는 새로운 track을 제공하고 Layout Shift metric을 표현했다. (Rendering Tab -> Layout Shift Regions 으로 live update 추적도 제공한다.)
![Layout Shifting](https://user-images.githubusercontent.com/4979560/90045518-1d58ed00-dd0a-11ea-95f4-f9a4954e014d.jpg)

web.dev의 [RAIL 모델로 성능 측정](https://web.dev/rail/#goals-and-guidelines) 사이트로 테스트를 해보았다.  
Experience Track의 Layout Shift를 클릭하면 하단에 Summary에 정보가 노출된다.  
최근 사용자 입력에 대한 응답인지 여부나 (Had recent input), 엘리먼트가 레이아웃 변경된 from과 to를 확인할 수 있다.  

대부분의 사람들은 웹을 탐색하다가 버튼을 탭하려고 하는데 화면의 다른곳으로 이동하게 되는 경우를 경험 했을것이다.   
layout shifting metric을 제공하게된 이유는 그것 때문이다.  

> 레이아웃 변경은 UX 문제를 유발하며 이를 제거해야 한다.
> 일반적인 방법으로는 스타일에서 컨텐츠를 위한 공간을 미리 예약하여 다른 컨텐츠를 이동하기 위해 컨텐츠를 떠나지 않도록 하는 것이다.(때로는 불가능하다.)



### WebAssembly
WebAssembly는 C/C++ 언어등으로 작성된 코드를 컴파일(소스 언어 -> VM byte code로 변환)하여 브라우저의 VM에서 실행해주는 기술이다.  

디버깅 할 때 컴파일 전의 사람이 이해할 수 있는 코드를 보며 디버깅 하기를 원한다.  
그것을 위해 오랫동안 source maps을 사용하여 컴파일된 WebAssembly 바이너리 코드에서 원본 소스코드로의 매핑을 정의했다.  

다만, source maps는 javascript, minifiers, transfiler를 위해 만들어졌다.  
WebAssembly를 위한 기능으로는 부족한 점이 많다.  

이번에 DevTools에서 DWARF를 지원하게 되면서 원본 소스코드 파일에 대한 매핑을 제공한다.  
- 원본 C 코드에서 중단점을 설정하고 디버깅이 가능하다.
- 디버깅 중 변수위에 마우스 오버를 통해 변수의 값을 확인 할 수 있어 효율적인 디버깅이 가능하다. (source maps로는 미지원)
- 디버깅 중에 기본 컴파일러를 사용하므로 sources map와 비교시 더욱 빠른 실행 경험을 제공한다.


### Issue Tab - 여기서부터
- 예제 
	- same site
		- https://samesite-sandbox.glitch.me
	- mixed contents
		- googlesamples.github.io/web-fundamentals/fundamentals/security/prevent-mixed-content/passive-mixed-content.html
- 페이지를 실행하는 동안 문제가 발견되었을 때 제공
	- 문제를 해결할 수 있는 방법에 대한 설명 제공
	- mixed contents, same site cookie 등
- console tab에 지저분한 것들을 제거


### Rendering Tab - Emulate vision deficiencies(시력 결함 모방)
- 예제
	- [https://www.youtube.com/watch?v=VNr1Kb07aME](https://www.youtube.com/watch?v=VNr1Kb07aME)
- 상호작용, 애니메이션/비디오를 비활성화하지 않고 페이지에 적용
- 시각 장애를 가지고 있는 사용자에게  어떤 경험을 주는지 실제로 확인할 수 있다.
- 생리적으로 정확하지만 특정 시력 결함의 가장 심각한 형태를 모방한다.
- 종류
	- Blurred vision(흐린 시야)
	- Protanopia (제 1색맹, 적색맹)
	- Deuteranopia (제 2색맹, 녹색맹)
	- tritanopia (제3 색맹, 청황(靑黃) 색맹)
	- achromatopsia (색도증)



## 참고
- [RAIL 모델로 성능 측정](https://web.dev/rail/#goals-and-guidelines)
- [웹어셈블리의 컨셉](https://developer.mozilla.org/ko/docs/WebAssembly/Concepts)
