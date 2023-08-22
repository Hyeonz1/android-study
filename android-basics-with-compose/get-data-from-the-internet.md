## 🍒 Introduction to web service

- ViewModel의 역할
  - ViewModel은 data layer와 communicate한다.
  - ViewModel에서는 네트워크를 호출해야한다.
  - ViewModel에서는 MutableState를 통해 앱 UI를 업데이트한다.

## 🍒 Web services and Retrofit

- 통신
  - 대부분의 웹서버는 stateless web architecture인 REST를 사용하여 통신
  - URI를 통해 RESTful 웹 서비스 Request가 만들짐
  - URI는 서버에 있는 리소스를 찾도록 해줌
  - URL은 URI의 일부분
 

## 🍒 Web service request

- 웹 서비스 요청에는 URI가 포함되며, 동일한 HTTP 프로토콜을 통해 서버에 요청이 전송된다.
- HTTP 요청에는 서버가 무엇을 하면 되는지에 대한 작동이 기술되어있다.


#### 일반적인 HTTP 요청들

- GET
- POST
- PUT
- DELETE

#### Retrofit Library

- Retrofit은 REST 백엔드와 통신한다.
- Dependency 추가가 필요하다.
```kotlin
// Retrofit 
implementation("com.squareup.retrofit2:retrofit:2.9.0")
// Retrofit with Scalar Converter
implementation("com.squareup.retrofit2:converter-scalars:2.9.0")
```
- scalar converter는 JSON 결과를 String으로 바꿔줌

## 🍒 Connecting the internet

#### 레트로핏의 작동
1. Retrofit은 network API를 만듦.
2. API는 데이터를 패치하여 converter library로 보냄.
3. converter library는 데이터를 decode하여 오브젝트 형태로 반환함.
4. Retrofit은 XML과 JSON에 대한 데이터 형식에 대해 지원함.
5. 결과적으로 Retrofit은 서비스를 call하고 consume할 코드를 생성.

#### API Service 만들기

1. network 패키지 하위에 APIService.kt 파일 생성
2. Base URL 선언
```kotlin
private const val BASE_URL = 
   "https://android-kotlin-fun-mars-server.appspot.com"
```
3. retrofit 선언
```kotlin
private val retrofit = Retrofit.Builder()
   .addConverterFactory(ScalarsConverterFactory.create())
   .baseUrl(BASE_URL)
   .build()
```
- Retrofit은 웹 서비스 API를 만들기 위해 base URI와 converter factory를 필요로 한다.
- converter는 Retrofit에 응답받은 데이터로 무엇을 해야하는지 알려준다
- ScalarsConverterFactory는 응답받은 JSON 데이터를 String 등의 원시 타입으로 바꿔준다.
4. 통신 인터페이스 추가
```kotlin
import retrofit2.http.GET

interface MarsApiService {
    @GET("photos") 
    fun getPhotos(): String
}
```

#### Object declarations

- object 선언
  - 싱글톤 오브젝트를 만들 때 사용되는 키워드

- 예제
```kotlin
// Example for Object declaration, do not copy over

object SampleDataProvider {
    fun register(provider: SampleProvider) {
        // ...
    }
​
    // ...
}

// To refer to the object, use its name directly.
SampleDataProvider.register(...)
```

- 싱글톤 패턴
  - 단 하나의 객체만 만들어지는 것을 보장한다.
  - 오브젝트에 접근하는 단 하나의 global point만 존재
  - 오브젝트 초기화는 thread-safe이고 첫 접근에 이루어짐
  - 단점
    - 테스트에서 예측이 어려움
  - 주의
    - 어떤 dependency를 필요로 하는지 정의해야함. => [Dependency injection](https://developer.android.com/training/dependency-injection)의 사용

#### API 생성

```kotlin
object MarsApi {
    val retrofitService : MarsApiService by lazy { 
       retrofit.create(MarsApiService::class.java)
    }
}
```
- object 키워드
  - Retrofit API Servide는 한 가지 instance만 필요함
  - Retrofit object의 create() 함수를 호출하는 것은 메모리, 비용, 성능 면에서 비효율적.
  - object declation을 활용
    
- lazy 키워드
  - 처음 사용할 때 초기화되도록 함

## 🍒 Call the web service in MarsViewModel

#### ViewModelScope
- 특징
  - built-in coroutine scope이다.
  - 앱에 있는 뷰모델 하나마다 각각 하나씩 존재
  - 뷰모델이 사라지면 ViewModelScope 코루틴은 자동적으로 취소
  - configuration 변화가 일어나도 요청은 유지
 
- 필요성
  - coroutine을 시작하고, 백그라운드에서 통신 요청을 보내기 위함.
