بناءً على المتطلبات والتصميم الذي تم وضعه، سنبدأ بكتابة الكود للمشروع. سنتناول الأجزاء الرئيسية من التطبيق، بما في ذلك الواجهة الرسومية، المنطق، وقاعدة البيانات.

### 1. إعداد المشروع

#### 1.1. إنشاء مشروع جديد في Android Studio
1. افتح Android Studio وأنشئ مشروعًا جديدًا باسم "TileMeasurement".
2. اختر "Empty Activity" كنوع النشاط الأول.
3. اختر Kotlin كلغة البرمجة.

#### 1.2. إضافة التبعيات في `build.gradle`
أضف التبعيات اللازمة للمشروع في ملف `build.gradle` الخاص بالوحدة النمطية:

```gradle
dependencies {
    implementation "org.jetbrains.kotlin:kotlin-stdlib:$kotlin_version"
    implementation 'androidx.core:core-ktx:1.7.0'
    implementation 'androidx.appcompat:appcompat:1.4.0'
    implementation 'com.google.android.material:material:1.4.0'
    implementation 'androidx.constraintlayout:constraintlayout:2.1.2'

    // ARCore
    implementation 'com.google.ar:core:1.29.0'
    implementation 'com.google.ar.sceneform.ux:sceneform-ux:1.17.1'

    // OpenCV
    implementation 'org.opencv:opencv-android:4.5.3'

    // Room
    implementation "androidx.room:room-runtime:2.4.0"
    kapt "androidx.room:room-compiler:2.4.0"

    // ViewModel and LiveData
    implementation "androidx.lifecycle:lifecycle-viewmodel-ktx:2.4.0"
    implementation "androidx.lifecycle:lifecycle-livedata-ktx:2.4.0"

    // Coroutines
    implementation 'org.jetbrains.kotlinx:kotlinx-coroutines-android:1.5.2'

    // Retrofit
    implementation 'com.squareup.retrofit2:retrofit:2.9.0'
    implementation 'com.squareup.retrofit2:converter-gson:2.9.0'

    // OkHttp
    implementation 'com.squareup.okhttp3:okhttp:4.9.2'
    implementation 'com.squareup.okhttp3:logging-interceptor:4.9.2'
}
```

### 2. إنشاء الواجهة الرسومية

#### 2.1. `activity_main.xml`
إنشاء واجهة البداية:

```xml
<?xml version="1.0" encoding="utf-8"?>
<androidx.constraintlayout.widget.ConstraintLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context=".MainActivity">

    <TextView
        android:id="@+id/welcomeTextView"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Welcome to Tile Measurement"
        android:textSize="24sp"
        app:layout_constraintTop_toTopOf="parent"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintEnd_toEndOf="parent"
        android:layout_marginTop="16dp"/>

    <Button
        android:id="@+id/startMeasurementButton"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Start Measurement"
        app:layout_constraintTop_toBottomOf="@id/welcomeTextView"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintEnd_toEndOf="parent"
        android:layout_marginTop="16dp"/>

</androidx.constraintlayout.widget.ConstraintLayout>
```

#### 2.2. `activity_camera.xml`
إنشاء واجهة الكاميرا:

```xml
<?xml version="1.0" encoding="utf-8"?>
<androidx.constraintlayout.widget.ConstraintLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context=".CameraActivity">

    <com.google.ar.sceneform.ArSceneView
        android:id="@+id/sceneView"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        app:layout_constraintTop_toTopOf="parent"
        app:layout_constraintBottom_toBottomOf="parent"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintEnd_toEndOf="parent"/>

    <Button
        android:id="@+id/saveMeasurementButton"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Save Measurement"
        app:layout_constraintBottom_toBottomOf="parent"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintEnd_toEndOf="parent"
        android:layout_marginBottom="16dp"/>

</androidx.constraintlayout.widget.ConstraintLayout>
```

### 3. إنشاء المنطق

#### 3.1. `MainActivity.kt`
إنشاء نشاط البداية:

```kotlin
package com.example.tilemeasurement

import android.content.Intent
import android.os.Bundle
import androidx.appcompat.app.AppCompatActivity
import kotlinx.android.synthetic.main.activity_main.*

class MainActivity : AppCompatActivity() {

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)

        startMeasurementButton.setOnClickListener {
            val intent = Intent(this, CameraActivity::class.java)
            startActivity(intent)
        }
    }
}
```

#### 3.2. `CameraActivity.kt`
إنشاء نشاط الكاميرا:

```kotlin
package com.example.tilemeasurement

import android.os.Bundle
import androidx.appcompat.app.AppCompatActivity
import com.google.ar.core.ArCoreApk
import com.google.ar.core.Config
import com.google.ar.core.Session
import com.google.ar.sceneform.ArSceneView
import com.google.ar.sceneform.Scene
import kotlinx.android.synthetic.main.activity_camera.*

class CameraActivity : AppCompatActivity() {

    private lateinit var arSceneView: ArSceneView
    private var arCoreSession: Session? = null

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_camera)

        arSceneView = sceneView

        // Initialize ARCore session
        if (ArCoreApk.getInstance().requestInstall(this, true) == ArCoreApk.InstallStatus.INSTALLED) {
            arCoreSession = Session(this)
            val config = Config(arCoreSession!!)
            config.updateMode = Config.UpdateMode.LATEST_CAMERA_IMAGE
            arCoreSession!!.configure(config)
            arSceneView.setupSession(arCoreSession!!)
        }

        saveMeasurementButton.setOnClickListener {
            // Save measurement logic here
        }
    }

    override fun onResume() {
        super.onResume()
        if (arCoreSession != null) {
            arSceneView.resume()
        }
    }

    override fun onPause() {
        super.onPause()
        arSceneView.pause()
    }

    override fun onDestroy() {
        super.onDestroy()
        arSceneView.destroy()
    }
}
```

### 4. إنشاء قاعدة البيانات

#### 4.1. `AppDatabase.kt`
إنشاء قاعدة البيانات باستخدام Room:

```kotlin
package com.example.tilemeasurement.data

import androidx.room.Database
import androidx.room.Room
import androidx.room.RoomDatabase
import android.content.Context
import com.example.tilemeasurement.model.Measurement

@Database(entities = [Measurement::class], version = 1, exportSchema = false)
abstract class AppDatabase : RoomDatabase() {

    abstract fun measurementDao(): MeasurementDao

    companion object {
        @Volatile
        private var INSTANCE: AppDatabase? = null

        fun getDatabase(context: Context): AppDatabase {
            return INSTANCE ?: synchronized(this) {
                val instance = Room.databaseBuilder(
                    context.applicationContext,
                    AppDatabase::class.java,
                    "tile_measurement_database"
                ).build()
                INSTANCE = instance
                instance
            }
        }
    }
}
```

#### 4.2. `Measurement.kt`
إنشاء نموذج القياس:

```kotlin
package com.example.tilemeasurement.model

import androidx.room.Entity
import androidx.room.PrimaryKey

@Entity(tableName = "measurements")
data class Measurement(
    @PrimaryKey(autoGenerate = true) val measurement_id: Int = 0,
    val width: Float,
    val height: Float,
    val date_created: Long
)
```

#### 4.3. `MeasurementDao.kt`
إنشاء DAO للتعامل مع قاعدة البيانات:

```kotlin
package com.example.tilemeasurement.data

import androidx.room.Dao
import androidx.room.Insert
import androidx.room.Query
import com.example.tilemeasurement.model.Measurement

@Dao
interface MeasurementDao {

    @Insert
    suspend fun insertMeasurement(measurement: Measurement)

    @Query("SELECT * FROM measurements")
    suspend fun getAllMeasurements(): List<Measurement>
}
```

### 5. إنشاء الأدوات المساعدة

#### 5.1. `ImageUtils.kt`
إنشاء أدوات لمعالجة الصور:

```kotlin
package com.example.tilemeasurement.utils

import org.opencv.core.CvType
import org.opencv.core.Mat
import org.opencv.core.MatOfPoint
import org.opencv.core.Scalar
import org.opencv.imgproc.Imgproc

object ImageUtils {

    fun detectEdges(mat: Mat): List<MatOfPoint> {
        val edges = Mat()
        Imgproc.Canny(mat, edges, 50.0, 150.0)
        val contours = ArrayList<MatOfPoint>()
        Imgproc.findContours(edges, contours, Mat(), Imgproc.RETR_LIST, Imgproc.CHAIN_APPROX_SIMPLE)
        return contours
    }
}
```

### 6. إنشاء ViewModel

#### 6.1. `MeasurementViewModel.kt`
إنشاء ViewModel لإدارة منطق القياس:

```kotlin
package com.example.tilemeasurement.viewmodel

import androidx.lifecycle.ViewModel
import androidx.lifecycle.viewModelScope
import com.example.tilemeasurement.data.MeasurementRepository
import com.example.tilemeasurement.model.Measurement
import kotlinx.coroutines.launch

class MeasurementViewModel(private val repository: MeasurementRepository) : ViewModel() {

    fun saveMeasurement(width: Float, height: Float) {
        viewModelScope.launch {
            val measurement = Measurement(width = width, height = height, date_created = System.currentTimeMillis())
            repository.insertMeasurement(measurement)
        }
    }
}
```

### 7. إنشاء Repository

#### 7.1. `MeasurementRepository.kt`
إنشاء Repository لإدارة البيانات:

```kotlin
package com.example.tilemeasurement.data

import com.example.tilemeasurement.model.Measurement

class MeasurementRepository(private val measurementDao: MeasurementDao) {

    suspend fun insertMeasurement(measurement: Measurement) {
        measurementDao.insertMeasurement(measurement)
    }

    suspend fun getAllMeasurements(): List<Measurement> {
        return measurementDao.getAllMeasurements()
    }
}
```

### 8. إنشاء Factory لViewModel

#### 8.1. `MeasurementViewModelFactory.kt`
إنشاء Factory لViewModel:

```kotlin
package com.example.tilemeasurement.viewmodel

import androidx.lifecycle.ViewModel
import androidx.lifecycle.ViewModelProvider
import com.example.tilemeasurement.data.MeasurementRepository

class MeasurementViewModelFactory(private val repository: MeasurementRepository) : ViewModelProvider.Factory {

    override fun <T : ViewModel?> create(modelClass: Class<T>): T {
        if (modelClass.isAssignableFrom(MeasurementViewModel::class.java)) {
            @Suppress("UNCHECKED_CAST")
            return MeasurementViewModel(repository) as T
        }
        throw IllegalArgumentException("Unknown ViewModel class")
    }
}
```

### 9. تحديث `CameraActivity.kt` لحفظ القياسات

```kotlin
package com.example.tilemeasurement

import android.os.Bundle
import androidx.appcompat.app.AppCompatActivity
import androidx.lifecycle.ViewModelProvider
import com.example.tilemeasurement.data.AppDatabase
import com.example.tilemeasurement.data.MeasurementRepository
import com.example.tilemeasurement.viewmodel.MeasurementViewModel
import com.example.tilemeasurement.viewmodel.MeasurementViewModelFactory
import com.google.ar.core.ArCoreApk
import com.google.ar.core.Config
import com.google.ar.core.Session
import com.google.ar.sceneform.ArSceneView
import kotlinx.android.synthetic.main.activity_camera.*

class CameraActivity : AppCompatActivity() {

    private lateinit var arSceneView: ArSceneView
    private var arCoreSession: Session? = null
    private lateinit var viewModel: MeasurementViewModel

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_camera)

        arSceneView = sceneView

        // Initialize ARCore session
        if (ArCoreApk.getInstance().requestInstall(this, true) == ArCoreApk.InstallStatus.INSTALLED) {
            arCoreSession = Session(this)
            val config = Config(arCoreSession!!)
            config.updateMode = Config.UpdateMode.LATEST_CAMERA_IMAGE
            arCoreSession!!.configure(config)
            arSceneView.setupSession(arCoreSession!!)
        }

        // Initialize ViewModel
        val database = AppDatabase.getDatabase(applicationContext)
        val repository = MeasurementRepository(database.measurementDao())
        viewModel = ViewModelProvider(this, MeasurementViewModelFactory(repository)).get(MeasurementViewModel::class.java)

        saveMeasurementButton.setOnClickListener {
            // Save measurement logic here
            val width = 100f // Example width
            val height = 100f // Example height
            viewModel.saveMeasurement(width, height)
        }
    }

    override fun onResume() {
        super.onResume()
        if (arCoreSession != null) {
            arSceneView.resume()
        }
    }

    override fun onPause() {
        super.onPause()
        arSceneView.pause()
    }

    override fun onDestroy() {
        super.onDestroy()
        arSceneView.destroy()
    }
}
```

### 10. تحديث `AndroidManifest.xml`

أضف الأذونات اللازمة في `AndroidManifest.xml`:

```xml
<uses-permission android:name="android.permission.CAMERA" />
<uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
<uses-permission android:name="android.permission.READ_EXTERNAL_STORAGE" />

<application
    ...>
    <meta-data
        android:name="com.google.ar.core"
        android:value="required" />
    ...
</application>
```

### 11. تنفيذ المزيد من الميزات

- **التعرف على الأبعاد**: استخدم OpenCV لاكتشاف الحواف وحساب الأبعاد.
- **عرض النتائج**: أنشئ نشاطًا جديدًا لعرض النتائج وتعديلها.
- **الإعدادات**: أنشئ نشاطًا جديدًا لإعدادات التطبيق.
- **المشاريع المحفوظة**: أنشئ نشاطًا جديدًا لعرض المشاريع المحفوظة وإعادة فتحها.

باتباع هذه الخطوات، ستكون قد بدأت في بناء تطبيق قياس أبعاد البلاط باستخدام الكاميرا. يمكنك الآن توسيع التطبيق بإضافة المزيد من الميزات وتحسين الأداء والدقة.
