<div align="center">
  <img src="ui-preview-v2.png" alt="Magic Eraser SDK UI Mockup" width="100%"/>
  <br><br>
  
  <img src="https://img.shields.io/badge/Platform-Android-green?style=for-the-badge&logo=android" alt="Android" />
  <img src="https://img.shields.io/badge/Language-Kotlin%20%7C%20Java-purple?style=for-the-badge" alt="Language" />
  <img src="https://img.shields.io/badge/AI-On--Device-blue?style=for-the-badge&logo=tensorflow" alt="AI" />
  <img src="https://img.shields.io/badge/Privacy-100%25-success?style=for-the-badge" alt="Privacy" />
  
  <h1>🪄 Magic Eraser Android SDK</h1>
  <p><b>The ultimate fully offline, on-device AI object and text removal tool for Android applications.</b></p>
</div>

---

## 🚀 Stop Paying High Cloud API Fees
Are you currently using cloud APIs like Remove.bg, Photoroom, or ClipDrop to power object removal in your app? **Stop paying per image.**

The **Magic Eraser SDK** is a premium, 100% on-device Android library that brings desktop-grade AI erasing to your mobile app for a flat, affordable subscription fee.

<div align="center">
  <img src="before-after-v2.png" alt="Magic Eraser Before and After" width="100%"/>
  <p><i>Flawless object removal powered by localized AI, processing in milliseconds.</i></p>
</div>

### 🌟 Why Top Apps Choose Our SDK:
1. **Zero Cloud Costs:** Because the AI runs entirely on the user's phone, you pay $0 in server processing fees, regardless of if your users edit 10 images or 10,000 images.
2. **Absolute Privacy:** User photos never leave the device. This is a massive selling point for privacy-conscious users and enterprise apps.
3. **Lightning Fast:** No upload or download wait times. Erasing happens instantly.
4. **State-of-the-Art AI:** Powered by NS Pro & NS Fast AI Inpainting, NS Smart Select for flawless tap-to-select, and ML Kit for automatic text detection.

---

## 💡 Perfect For Your Use Case
*   **Real Estate Apps:** Remove clutter, people, or cars from property photos instantly.
*   **E-Commerce:** Clean up product photography and remove background distractions.
*   **Social Media & Dating:** Let users erase photobombers and exes from their profile pictures.
*   **Photo Editors:** Add premium "Magic Eraser" functionality to your existing editing suite.

---

## 📦 How to Integrate (In 5 Minutes)
Integration is incredibly simple. We use JitPack to securely distribute the compiled SDK.

### Step 1: Add the Repository
Add the JitPack repository to your root project build file.

**Gradle (Kotlin DSL - `settings.gradle.kts`)**
```kotlin
dependencyResolutionManagement {
    repositoriesMode.set(RepositoriesMode.FAIL_ON_PROJECT_REPOS)
    repositories {
        mavenCentral()
        maven("https://jitpack.io")
    }
}
```

**Gradle (Groovy - `settings.gradle`)**
```groovy
dependencyResolutionManagement {
    repositoriesMode.set(RepositoriesMode.FAIL_ON_PROJECT_REPOS)
    repositories {
        mavenCentral()
        maven { url 'https://jitpack.io' }
    }
}
```

**Maven (`pom.xml`)**
```xml
<repositories>
    <repository>
        <id>jitpack.io</id>
        <url>https://jitpack.io</url>
    </repository>
</repositories>
```

### Step 2: Add the Dependency
Add the SDK to your app-level dependencies and sync your project.

**Gradle (Kotlin DSL - `build.gradle.kts`)**
```kotlin
dependencies {
    // 1. The Magic Eraser SDK
    implementation("com.github.nsenterprise9865-stack:magic-eraser-android-sdk:1.0.9")
    
    // 2. Required SDK Dependencies (AI Models & Background Sync)
    implementation("androidx.work:work-runtime-ktx:2.9.0")
    implementation("com.google.android.gms:play-services-mlkit-subject-segmentation:16.0.0-beta1")
    implementation("com.google.mlkit:text-recognition:16.0.1")
    implementation("com.microsoft.onnxruntime:onnxruntime-android:1.21.1")
    implementation(platform("com.google.firebase:firebase-bom:33.7.0"))
    implementation("com.google.firebase:firebase-firestore")
}
```

**Gradle (Groovy - `build.gradle`)**
```groovy
dependencies {
    // 1. The Magic Eraser SDK
    implementation 'com.github.nsenterprise9865-stack:magic-eraser-android-sdk:1.0.9'
    
    // 2. Required SDK Dependencies (AI Models & Background Sync)
    implementation 'androidx.work:work-runtime-ktx:2.9.0'
    implementation 'com.google.android.gms:play-services-mlkit-subject-segmentation:16.0.0-beta1'
    implementation 'com.google.mlkit:text-recognition:16.0.1'
    implementation 'com.microsoft.onnxruntime:onnxruntime-android:1.21.1'
    implementation platform('com.google.firebase:firebase-bom:33.7.0')
    implementation 'com.google.firebase:firebase-firestore'
}
```

**Maven (`pom.xml`)**
```xml
<dependency>
    <groupId>com.github.nsenterprise9865-stack</groupId>
    <artifactId>magic-eraser-android-sdk</artifactId>
    <version>1.0.9</version>
</dependency>
<!-- Ensure you also include WorkManager, ML Kit, ONNX, and Firebase dependencies -->
```

### Step 3: Initialize the SDK & Download Models
The SDK requires network initialization to verify your license and fetch the AI model URLs securely. Since `initialize()` is a suspend function, you must call it from a coroutine.

Once initialized successfully, you should start the **Background Model Downloader** so the AI model is ready before the user opens the editor.

**Example (Inside your MainActivity or Application class):**
```kotlin
import androidx.lifecycle.lifecycleScope
import com.nsenterprise.magiceraser.sdk.MagicEraserSDK
import com.nsenterprise.magiceraser.sdk.utils.ModelDownloadWorker
import kotlinx.coroutines.Dispatchers
import kotlinx.coroutines.launch

class MainActivity : AppCompatActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        
        lifecycleScope.launch(Dispatchers.IO) {
            // 1. Initialize the SDK with your unique license key
            MagicEraserSDK.initialize(
                context = applicationContext, 
                licenseKey = "YOUR_LICENSE_KEY_HERE"
            )
            
            // 2. If the license is valid, start downloading the AI models in the background
            if (MagicEraserSDK.isReady()) {
                ModelDownloadWorker.enqueue(applicationContext)
            }
        }
    }
}
```

*Note: `ModelDownloadWorker` uses Android's native `WorkManager` to download the models efficiently in the background, even if the user minimizes your app.*

### Step 4: Launch the Editor
The SDK provides a built-in, polished UI that takes over when your user selects a photo. You can integrate it using either modern Kotlin or traditional Java.

#### 🔹 Option A: Kotlin (Jetpack Compose)
If your app uses Compose, use the Android Photo Picker and pass the `Uri` directly into the SDK's `MagicEraserScreen`:

```kotlin
import androidx.activity.compose.rememberLauncherForActivityResult
import androidx.activity.result.contract.ActivityResultContracts
import com.nsenterprise.magiceraser.sdk.ui.MagicEraserScreen

@Composable
fun EditFeatureScreen() {
    var selectedImageUri by remember { mutableStateOf<android.net.Uri?>(null) }
    
    // 1. Setup the Android Photo Picker
    val photoLauncher = rememberLauncherForActivityResult(ActivityResultContracts.PickVisualMedia()) { uri ->
        selectedImageUri = uri
    }

    if (selectedImageUri != null) {
        // 2. Launch the Magic Eraser Editor
        MagicEraserScreen(
            onBack = { selectedImageUri = null }, // Close the editor
            initialImageUri = selectedImageUri!!,
            onSaveSuccess = { resultBitmap -> 
                // The user successfully erased objects! 
                // Do something with resultBitmap here.
                selectedImageUri = null
            }
        )
    } else {
        // 3. Your App's Button to open the gallery
        Button(onClick = { 
            photoLauncher.launch(PickVisualMediaRequest(ActivityResultContracts.PickVisualMedia.ImageOnly)) 
        }) {
            Text("Select Photo to Edit")
        }
    }
}
```

#### 🔹 Option B: Java (Traditional XML)
If you are using Java and traditional XML layouts, you can launch the SDK by starting its dedicated Activity:

```java
import com.nsenterprise.magiceraser.sdk.MagicEraserActivity;

public class MainActivity extends AppCompatActivity {
    
    // 1. Register a launcher to get the edited image back from the SDK
    private final ActivityResultLauncher<Intent> eraserLauncher = registerForActivityResult(
            new ActivityResultContracts.StartActivityForResult(),
            result -> {
                if (result.getResultCode() == RESULT_OK && result.getData() != null) {
                    Uri editedImageUri = result.getData().getData();
                    // The user successfully erased objects! Use the editedImageUri.
                }
            }
    );

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        
        Button editButton = findViewById(R.id.editButton);
        editButton.setOnClickListener(v -> {
            // 2. Get a photo from your gallery picker, then pass it to the SDK
            Uri imageToEdit = /* ... get URI from your own gallery ... */;
            
            Intent intent = new Intent(this, MagicEraserActivity.class);
            intent.putExtra("EXTRA_IMAGE_URI", imageToEdit.toString());
            eraserLauncher.launch(intent);
        });
    }
}
```

#### 🔹 Option C: Fully Custom UI (Headless API)
If you require 100% control over the user interface (e.g. custom layout measurements, your own custom drawing canvas, and branded UI elements), the SDK provides a Headless API via `MagicEraserCore`.

This tier exposes suspending functions for generating selection masks and executing the AI models directly:

```kotlin
// 1. Ensure Model is ready
MagicEraserCore.ensureModelReady(context) { progress ->
    println("Downloading Model: $progress")
}

// 2. Pre-warm EdgeSAM when user enters Detect mode (NEW in v1.0.9)
// This generates embeddings once so every tap is instant AI segmentation
MagicEraserCore.prepareForDetect(context, imageBmp)

// 3. On user tap — full 3-tier AI detect pipeline runs automatically:
//    EdgeSAM → MLKit Subject Segmentation → Color Flood-Fill fallback
val maskPair = MagicEraserCore.buildDetectMask(
    context = context,
    source  = imageBmp,
    tapX    = 500,
    tapY    = 500
)

// 4. Apply the mask to erase
// accurateMode = true  → NS Pro (LaMa) — highest quality
// accurateMode = false → NS Fast AI (MIGAN v2) — instant
val finalImage = MagicEraserCore.applyMask(
    context      = context,
    source       = imageBmp,
    mask         = maskPair!!.first,
    accurateMode = true
)
```
*(Please refer to our Sample App's `CustomEditorActivity` for a complete reference on implementing an XML-based custom drawing canvas, Fast/Pro toggle, and tools).*

---

## 💰 Get Your License Key
Our flat-rate plans start at just **$19/month** (which saves developers thousands of dollars compared to cloud APIs). 

To purchase a license key, or to inquire about Enterprise white-label packages, contact us directly:
✉️ **contact.nsenterprise@gmail.com**

---
*Brought to you by NS Enterprise.*
