# Android Function 🗺

<a href="https://opensource.org/licenses/Apache-2.0"><img src="https://img.shields.io/badge/License-Apache%202.0-blue.svg"/></a>
<a href='https://developer.android.com'><img src='http://img.shields.io/badge/platform-android-green.svg'/></a>
<a href='https://developer.android.com/studio/releases/platforms#5.0'><img src="https://img.shields.io/badge/API%20Level-21+-green.svg"/></a>


1. Function to Retrieve Greeting Message Based on Time of Day
```kotlin
    private fun getGreetingMessage():Int{
        val c = Calendar.getInstance()

        return when (c.get(Calendar.HOUR_OF_DAY)) {
            in 12..15 ->  R.string.txt_good_afternoon
            in 16..20 -> R.string.txt_good_evening
            in 21..23 -> R.string.txt_good_night
            else -> R.string.txt_good_morning
        }
    }
```

2. takeScreenShot View -> Bitmap

```kotlin
fun View.takeScreenShot(): Bitmap {
    val bitmap = Bitmap.createBitmap(
        this.width,
        this.height, Bitmap.Config.ARGB_8888
    )
    val canvas = Canvas(bitmap)
    this.draw(canvas)
    return bitmap
}
```
3.View Extention 
```kotlin
fun View.show() {
    isVisible = true
}

fun View.hide() {
    isVisible = false
}

fun View.hidden() {
    isInvisible = true
}

fun ImageView.startRotateAnimation(){
    val rotate =
        RotateAnimation(0f, 360f, Animation.RELATIVE_TO_SELF, 0.5f, Animation.RELATIVE_TO_SELF, 0.5f)
    rotate.duration = 7000
    rotate.repeatCount = Animation.INFINITE
    rotate.interpolator = LinearInterpolator()
    this.startAnimation(rotate)
}
```

4. Dialog Fragment
```kotlin
fun DialogFragment.setWidthPercent(percentage: Int) {
    val percent = percentage.toFloat() / 100
    val dm = Resources.getSystem().displayMetrics
    val rect = dm.run { Rect(0, 0, widthPixels, heightPixels) }
    val percentWidth = rect.width() * percent
    dialog?.window?.setLayout(percentWidth.toInt(), ViewGroup.LayoutParams.WRAP_CONTENT)
}
fun DialogFragment.setFullScreen() {
    dialog?.window?.setLayout(ViewGroup.LayoutParams.MATCH_PARENT, ViewGroup.LayoutParams.WRAP_CONTENT)
}
class DialogUnlockTheme : DialogFragment() {
    private lateinit var binding: DialogUnlockThemeBinding

    private var listener: UnlockThemeDialogListener? = null

    companion object {
        const val TAG = "DialogPremiumNew"

        fun newInstance(): DialogUnlockTheme {
            return DialogUnlockTheme()
        }
    }
    @Deprecated("Deprecated in Java")
    override fun onActivityCreated(savedInstanceState: Bundle?) {
        super.onActivityCreated(savedInstanceState)
        setWidthPercent(90)
    }
    override fun onCreateView(
        inflater: LayoutInflater,
        container: ViewGroup?,
        savedInstanceState: Bundle?
    ): View? {
        binding = DialogUnlockThemeBinding.inflate(inflater, container, false)
        return binding.root
    }

    override fun onViewCreated(view: View, savedInstanceState: Bundle?) {
        super.onViewCreated(view, savedInstanceState)
        initView(view)
        handleEvents(view)
    }

    override fun show(manager: FragmentManager, tag: String?) {
        if (dialog?.isShowing == true || this.isAdded) return
        super.show(manager, tag)
    }

    override fun onStart() {
        super.onStart()
        dialog?.window?.setBackgroundDrawable(ColorDrawable(Color.TRANSPARENT))
//        dialog?.window?.setLayout(
//            WindowManager.LayoutParams.WRAP_CONTENT,
//            WindowManager.LayoutParams.WRAP_CONTENT
//        )
    }

    override fun onDismiss(dialog: DialogInterface) {
        super.onDismiss(dialog)
        listener!!.onDismiss()
    }

    override fun onAttach(context: Context) {
        super.onAttach(context)
        if (context is UnlockThemeDialogListener) {
            listener = context
        } else {
            throw RuntimeException("$context must implement DialogConfirmListener")
        }
    }
    override fun onDetach() {
        super.onDetach()
        listener = null
    }
    private fun initView(view: View) {

    }
interface UnlockThemeDialogListener {
    fun onWatchAds()
    fun onGetPremium()
    fun onDismiss()
}
```
3. Crash Detector for Android
[Article](https://medium.com/@sandeepkella23/crash-detector-for-android-41b55868d230)
   
Xác định lớp Ứng dụng tùy chỉnh trong tệp kê khai: Thêm lớp Ứng dụng tùy chỉnh vào tệp AndroidManifest.xml của bạn bằng cách sử dụng thuộc tính android:name trong phần tử <application> . Điều này thông báo cho hệ thống Android sử dụng lớp tùy chỉnh của bạn để quản lý trạng thái toàn cầu của ứng dụng. ```xml <ứng dụng android:name=".MyApplication" ...> ... </application> ```

`SplashActivity.kt`
```kotlin
  override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_splash)
        if (intent.hasExtra("crash")) {
            // Handle any state restoration if necessary
            textSubtitle.text = "App has recovered from a crash"
        }
    }
```

`MyApplication : Application()`
```kotlin
  override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        Thread.setDefaultUncaughtExceptionHandler(CustomExceptionHandler(this))
    }
```

`CustomExceptionHandler`
```kotlin
class CustomExceptionHandler(context: Context) : Thread.UncaughtExceptionHandler {
    private val defaultHandler: Thread.UncaughtExceptionHandler? =
        Thread.getDefaultUncaughtExceptionHandler()
    private val context: Context = context

    override fun uncaughtException(thread: Thread, throwable: Throwable) {
        // Log the exception or send it to a server
        restartApp()
        defaultHandler?.uncaughtException(thread, throwable) // Optional: Call the default handler
    }

    private fun restartApp() {
        val intent: Intent = Intent(context, SplashScreenActivity::class.java)
        intent.addFlags(Intent.FLAG_ACTIVITY_NEW_TASK or Intent.FLAG_ACTIVITY_CLEAR_TASK)
        intent.putExtra("crash","crash")
        context.startActivity(intent)
        Runtime.getRuntime().exit(0) // Close the current process
    }
}
```

4. Thư viện BlurView

### GaussianBlur
([https://github.com/jrvansuita/GaussianBlur](https://github.com/jrvansuita/GaussianBlur)


### 지도 구성하기

지도는 `MapProperties`와 `MapUiSettings` 객체를 `NaverMap` composable에 전달하여 구성할 수 있습니다.

```kotlin
var mapProperties by remember {
    mutableStateOf(
        MapProperties(maxZoom = 10.0, minZoom = 5.0)
    )
}
var mapUiSettings by remember {
    mutableStateOf(
        MapUiSettings(isLocationButtonEnabled = false)
    )
}
Box(Modifier.fillMaxSize()) {
    NaverMap(properties = mapProperties, uiSettings = mapUiSettings)
    Column {
        Button(onClick = {
            mapProperties = mapProperties.copy(
                isBuildingLayerGroupEnabled = !mapProperties.isBuildingLayerGroupEnabled
            )
        }) {
            Text(text = "Toggle isBuildingLayerGroupEnabled")
        }
        Button(onClick = {
            mapUiSettings = mapUiSettings.copy(
                isLocationButtonEnabled = !mapUiSettings.isLocationButtonEnabled
            )
        }) {
            Text(text = "Toggle isLocationButtonEnabled")
        }
    }
}
```

### 지도의 카메라 제어하기

`CameraPositionState`를 통해 카메라 변경을 관찰하고 제어할 수 있습니다.

```kotlin
val seoul = LatLng(37.532600, 127.024612)
val cameraPositionState: CameraPositionState = rememberCameraPositionState {
    // 카메라 초기 위치를 설정합니다.
    position = CameraPosition(seoul, 11.0)
}
Box(Modifier.fillMaxSize()) {
    NaverMap(cameraPositionState = cameraPositionState)
    Button(onClick = {
        // 카메라를 새로운 줌 레벨로 이동합니다.
        cameraPositionState.move(CameraUpdate.zoomIn())
    }) {
        Text(text = "Zoom In")
    }
}
```

### 지도에 그리기

지도에 `Marker`처럼 Overlay를 추가하려면, `NaverMap`의 content에 child composable을 추가하면 됩니다.

```kotlin
NaverMap {
    Marker(
        state = MarkerState(position = LatLng(37.532600, 127.024612)),
        captionText = "Marker in Seoul"
    )
    Marker(
        state = MarkerState(position = LatLng(37.390791, 127.096306)),
        captionText = "Marker in Pangyo"
    )
}
```

제공되는 Overlay는 다음과 같습니다.

- `ArrowheadPathOverlay`
- `CircleOverlay`
- `GroundOverlay`
- `LocationOverlay`
- `Marker`
- `MultipartPathOverlay`
- `PathOverlay`
- `PolygonOverlay`
- `PolylineOverlay`

#### raw NaverMap 객체 얻기 (Experimental)

특정 UseCase에서는 `NaverMap` 객체가 필요합니다.
예를 들어, 이 라이브러리에서 마커 클러스터링은 아직 지원되지 않지만([Issue #14](https://github.com/fornewid/naver-map-compose/issues/14) 참조),
[외부 라이브러리](https://github.com/ParkSangGwon/TedNaverMapClustering) 를 이용하여 클러스터링을 구현할 수 있습니다.
이렇게 구현하려면 `MapEffect` 혹은 `DisposableMapEffect` composable을 사용하여 네이버지도 SDK의 raw `NaverMap` 객체에 접근해야 합니다.

```kt
NaverMap(
    // ...
) {
    val context = LocalContext.current
    var clusterManager by remember { mutableStateOf<TedNaverClustering<MyItem>?>(null) }
    DisposableMapEffect(items) { map ->
        if (clusterManager == null) {
            clusterManager = TedNaverClustering.with<MyItem>(context, map).make()
        }
        clusterManager?.addItems(items)
        onDispose {
            clusterManager?.clearItems()
        }
    }
}
```

### 위치 추적하기

> :warning: `play-services-location` 버전이 16.0.0 보다 높으면, 컴파일 혹은 런타임에 오류가 발생할 수 있습니다. ([Warnings 섹션을 참고하세요.](https://github.com/fornewid/naver-map-compose/edit/main/README.md#warning-warnings))

구글에서 제공하는 [FusedLocationProviderClient](https://developers.google.com/android/reference/com/google/android/gms/location/FusedLocationProviderClient)을 이용하여, 위치를 추적하는 기능을 제공합니다.

`MapProperties`의 `locationTrackingMode`를 설정하여 위치 추적 모드를 지정할 수 있습니다.

```kotlin
NaverMap(
    locationSource = rememberFusedLocationSource(),
    properties = MapProperties(
        locationTrackingMode = LocationTrackingMode.Follow,
    ),
    uiSettings = MapUiSettings(
        isLocationButtonEnabled = true,
    )
)
```

`LocationTrackingMode`가 `Follow` 또는 `Face`일 때, 나침반 기능을 활성화하려면 `isCompassEnabled`을 true로 설정해야 합니다.

```kotlin
NaverMap(
    locationSource = rememberFusedLocationSource(isCompassEnabled = true),
    properties = MapProperties(
        locationTrackingMode = LocationTrackingMode.Face,
    ),
    uiSettings = MapUiSettings(
        isLocationButtonEnabled = true,
    )
)
```

## Snapshots

현재 개발 중인 버전을 확인하고 싶다면 [SNAPSHOT 버전](https://s01.oss.sonatype.org/content/repositories/snapshots/io/github/fornewid/naver-map-compose/)을 사용할 수 있습니다.

Snapshot은 `main` branch에 커밋이 추가될 때마다 업데이트됩니다.

```groovy
repositories {
    maven { url 'https://s01.oss.sonatype.org/content/repositories/snapshots' }
}

dependencies {
    // 위 링크에서 최신 SNAPSHOT 버전을 확인하세요.
    classpath 'io.github.fornewid:naver-map-compose:XXX-SNAPSHOT'
}
```

## Contributions

- 오류를 발견하거나 궁금한 점이 있다면 [이슈를 등록해주세요.](https://github.com/fornewid/naver-map-compose/issues/new)
- 새로운 기능을 추가하고 싶다면 [GitHub Issues](https://github.com/fornewid/naver-map-compose/issues)를 통해 지원 가능 여부를 확인해야 합니다.

## License

```
Copyright 2022 SOUP

Licensed to the Apache Software Foundation (ASF) under one or more contributor
license agreements. See the NOTICE file distributed with this work for
additional information regarding copyright ownership. The ASF licenses this
file to you under the Apache License, Version 2.0 (the "License"); you may not
use this file except in compliance with the License. You may obtain a copy of
the License at

   http://www.apache.org/licenses/LICENSE-2.0

Unless required by applicable law or agreed to in writing, software
distributed under the License is distributed on an "AS IS" BASIS, WITHOUT
WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied. See the
License for the specific language governing permissions and limitations under
the License.
```

[naver-map]: https://navermaps.github.io/android-map-sdk/guide-ko/
[compose]: https://developer.android.com/jetpack/compose
[api-key]: https://navermaps.github.io/android-map-sdk/guide-ko/1.html
