# Material Theming with Jetpack Compose

## 1. Before you begin
#### Material Design이란?
- 안드로이드를 위한 디자인 시스템으로, 앱 UI를 만들기 위한 가이드 라인을 제공한다.
 
#### 배우는 것
- Material Theming 적용
- custom color palette 적용
- custom font 적용
- custom shapes 적용
- top app bar 적용

## 2. App overview
#### 앱
  - 강아지 리스트 보여주기
  - Material Design 사용

#### Color palette
- 라이트 모드와 다크 모드를 고려하여 컬러 팔레트 설정

#### Typography
- Type Role
- Font
- Font weight
- Size

#### Theme file
- 앱 테마에 대한 정보가 담긴 파일
- color, typography, shape를 통해 정의된다.
- Theme.kt 파일은 다음과 같이 작성한다.
```kotlin
fun WoofTheme(
    darkTheme: Boolean = isSystemInDarkTheme(),
    dynamicColor: Boolean = false,
    content: @Composable () -> Unit
) {
    val colorScheme = when {
        dynamicColor && Build.VERSION.SDK_INT >= Build.VERSION_CODES.S -> {
            val context = LocalContext.current
            if (darkTheme) dynamicDarkColorScheme(context) else dynamicLightColorScheme(context)
        }

        darkTheme -> DarkColors
        else -> LightColors
    }
    val view = LocalView.current
    if (!view.isInEditMode) {
        SideEffect {
            val window = (view.context as Activity).window
            window.statusBarColor = colorScheme.primary.toArgb()
            WindowCompat.getInsetsController(window, view).isAppearanceLightStatusBars = darkTheme
            
        }
    }

    MaterialTheme(
        colorScheme = colorScheme,
        shapes = Shapes,
        typography = Typography,
        content = content
    )
}
```
- MainActivity.kt 파일에서 다음과 같이 선언하면 전체 앱에 Material 테마를 적용할 수 있다.
```kotlin
class MainActivity : ComponentActivity() {
   override fun onCreate(savedInstanceState: Bundle?) {
       super.onCreate(savedInstanceState)
       setContent {
           WoofTheme {
               Surface(
                   modifier = Modifier.fillMaxSize()
               ) {
                   WoofApp()
               }
           }
       }
   }
}
```
## 4. Add color
#### color 리소스란?
  - 앱에서 처음 설정해주어야 하는 요소이다.
  - `Color`는 hexadecimal 값으로 표현된다.
    - #{ARGB}

#### Use Material Theme Builder to create a color scheme
- [Material Theme Builder](https://m3.material.io/theme-builder#/custom)를 통해 custom color scheme을 만들 수 있다.
- color roles
  - primary colors: used for key components
  - secondary colors: used for less prominent components
  - tertiary colors: used for contrasting accents that can be used to balance primary and secondary colors or bring heightened attention to an element, such as an input field
  - on colors: 팔레트의 색상 위에 나타내며, 주로 텍스트, 아이콘, 획에 적용된다.
- color scheme의 활용
  1. Color.kt 파일 설정
  2. Theme.kt 파일 설정
  - 자세한 설정 예제는 [여기](https://developer.android.com/codelabs/basic-android-kotlin-compose-material-theming?continue=https%3A%2F%2Fdeveloper.android.com%2Fcourses%2Fpathways%2Fandroid-basics-compose-unit-3-pathway-3%3Fhl%3Den%23codelab-https%3A%2F%2Fdeveloper.android.com%2Fcodelabs%2Fbasic-android-kotlin-compose-material-theming&hl=en#3)를 참고한다.

#### Color mapping
- Material component는 컬러 슬롯과 자동적으로 매칭된다.
  - [ex] status bar는 Primary color의 색을 가진다.
 
#### Dimens file
- dimension value를 저장하기 위해 dimens.xml 파일을 사용한다.
  - 하드 코드 방지
- 예제 코드
```xml
<resources>
   <dimen name="padding_small">8dp</dimen>
   <dimen name="padding_medium">16dp</dimen>
   <dimen name="image_size">64dp</dimen>
</resources>
```
- `modifier = Modifier.padding(dimensionResource(id = R.dimen.padding_small))`
