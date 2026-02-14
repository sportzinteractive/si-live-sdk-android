# Brightcove Live Streaming SDK for Android

A native Android SDK built with Jetpack Compose that provides a single, embeddable live streaming screen powered by Brightcove.

## Features

- ✅ **Native Jetpack Compose UI** - Fully native Compose implementation
- ✅ **Single Public Screen** - One composable (`LiveStreamScreen`) for easy integration
- ✅ **Multiple States** - Loading, Pre-live, and Live stream states with automatic transitions
- ✅ **Brightcove Integration** - Fully encapsulated Brightcove player logic with event handling
- ✅ **Analytics** - Built-in pageview tracking
- ✅ **Fullscreen Support** - Native fullscreen with system bar management and orientation handling
- ✅ **Error Handling** - Robust error recovery with retry mechanisms
- ✅ **AAR Distribution** - Packaged as Android Library (AAR) for easy integration
- ✅ **Demo App** - Included demo module for testing and validation

## Requirements

- Android API 24+ (Android 7.0+)
- Jetpack Compose
- Brightcove account (credentials are mapped internally based on event type and environment)

## Installation

### Using the AAR File

To use the Brightcove Live Streaming SDK, you need to add the AAR file to your project and configure the required dependencies.

#### Step 1: Add the AAR File

1. Copy the `SILiveSdk.aar` file to your project's `libs` folder (create the folder if it doesn't exist):
   ```
   your-project/
   └── app/
       └── libs/
           └── SILiveSdk.aar
   ```

#### Step 2: Configure Project-Level Gradle

Add the Brightcove Maven repository and flatDir configuration to your **project-level** `build.gradle.kts` (or `build.gradle`):

```kotlin
allprojects {
    repositories {
        google()
        mavenCentral()
        
        // Brightcove Maven repository
        maven {
            url = uri("https://repo.brightcove.com/releases")
        }
        
        // Local AAR files
        flatDir {
            dirs("libs")
        }
    }
}
```

Or if using `settings.gradle.kts`:

```kotlin
dependencyResolutionManagement {
    repositoriesMode.set(RepositoriesMode.FAIL_ON_PROJECT_REPOS)
    repositories {
        google()
        mavenCentral()
        
        // Brightcove Maven repository
        maven {
            url = uri("https://repo.brightcove.com/releases")
        }
        
        // Local AAR files
        flatDir {
            dirs("libs")
        }
    }
}
```

#### Step 3: Configure App-Level Gradle

Add the following dependencies to your **app-level** `build.gradle.kts` (or `build.gradle`):

```kotlin
dependencies {
    // AAR file
    implementation(files("libs/SILiveSdk.aar"))
    
    // Required dependency - CRITICAL!
    implementation("androidx.lifecycle:lifecycle-viewmodel-compose:2.7.0")
    
    // Brightcove SDK
    implementation("com.brightcove.player:exoplayer2:8.3.0")
    
    // Your other dependencies...
}
```

**Important Notes:**
- The `lifecycle-viewmodel-compose` dependency is **CRITICAL** and must be included
- The Brightcove Maven repository is required for the Brightcove SDK dependency
- The `flatDir` configuration allows Gradle to find the AAR file in the `libs` folder

## Quick Start

### 1. Add Required Imports

```kotlin
import androidx.compose.foundation.layout.fillMaxSize
import androidx.compose.ui.Modifier
import com.silivesdk.app.ui.LiveStreamScreen
import com.silivesdk.app.model.EventType
import com.silivesdk.app.model.Environment
```

### 2. Use LiveStreamScreen in Your Activity/Fragment

The SDK automatically initializes when you use `LiveStreamScreen`. Simply add it to your Compose UI:

#### Basic Usage

```kotlin
import androidx.activity.ComponentActivity
import androidx.activity.compose.setContent
import androidx.compose.foundation.layout.fillMaxSize
import androidx.compose.material3.MaterialTheme
import androidx.compose.material3.Surface
import androidx.compose.ui.Modifier
import com.silivesdk.app.ui.LiveStreamScreen
import com.silivesdk.app.model.EventType
import com.silivesdk.app.model.Environment

class MainActivity : ComponentActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        
        setContent {
            MaterialTheme {
                Surface(
                    modifier = Modifier.fillMaxSize(),
                    color = MaterialTheme.colorScheme.background
                ) {
                    LiveStreamScreen(
                        eventType = EventType.camera,  // or EventType.mobile
                        environment = Environment.nonProd,  // or Environment.prod
                        locales = "en",  // Locale identifier (e.g., "en", "hi", "it")
                        modifier = Modifier.fillMaxSize(),
                        onBackButtonClick = { clicked ->
                            // Handle back button click
                            if (clicked) {
                                finish()  // or navigate back
                            }
                        }
                    )
                }
            }
        }
    }
}
```

**Note:** The SDK automatically initializes when `LiveStreamScreen` is used. Credentials are mapped internally based on `eventType`, `environment`, and `locales`.


### LiveStreamScreen

The main public composable for displaying live streams.

**Parameters:**
- `eventType: EventType` - `EventType.camera` or `EventType.mobile` (required, determines credentials)
- `environment: Environment` - `Environment.prod` or `Environment.nonProd` (required, determines credentials)
- `locales: String` - Locale identifier (required, e.g., `"en"`, `"hi"`, `"it"`)
- `modifier: Modifier` - Modifier for the composable (required)
- `onBackButtonClick: ((Boolean) -> Unit)?` - Optional callback when user taps back button. Receives a boolean indicating if back was clicked.
- `debug: Boolean` - Enable debug logging (optional, default: `BuildConfig.DEBUG`)

**Example:**
```kotlin
LiveStreamScreen(
    eventType = EventType.camera,
    environment = Environment.prod,
    locales = "en",
    modifier = Modifier.fillMaxSize(),
    onBackButtonClick = { clicked ->
        if (clicked) {
            // Handle back button click
        }
    },
    debug = true
)
```

## Features

### Error Handling & State Management
- ✅ **Loading State**: Shows loading indicator while checking stream availability
- ✅ **Error State**: Displays errors with retry capability
- ✅ **Automatic Retry**: Configurable auto-retry with exponential backoff
- ✅ **Manual Retry**: Exposed retry function for manual error recovery
- ✅ **Network Monitoring**: Automatic detection of network connectivity

### Player Controls & Customization
- ✅ **Control Visibility**: Show/hide native Brightcove player controls
- ✅ **Fullscreen Support**: Native fullscreen with system bar management and orientation handling
- ✅ **Event Tracking**: Track all player events for analytics

### Configuration & Customization
- ✅ **Polling Interval**: Configurable interval for checking stream availability
- ✅ **Retry Configuration**: Customizable retry attempts and backoff
- ✅ **UI Customization**: Custom loading text, error retry text

### Network & Performance
- ✅ **Network State Handling**: Automatic pause/resume based on connectivity
- ✅ **Smart Polling**: Only polls when network is available
- ✅ **Error Recovery**: Automatic retry on network restoration

### Developer Experience
- ✅ **Enhanced Analytics**: Track all player events
- ✅ **Better Error Messages**: Descriptive error messages with context

## Architecture

The SDK is designed with the following principles:

- **Encapsulation**: All Brightcove logic is internal to the SDK
- **No Dependencies**: Parent apps don't need to manage Brightcove or streaming logic
- **Lifecycle Management**: SDK handles player lifecycle automatically
- **Error Handling**: Graceful handling of network issues and playback failures with automatic retry
- **Network Awareness**: Automatic detection and handling of network state changes

## License

Sportz Interactive

## Support

Android & IT Team

