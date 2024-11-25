# MaleficNav Library

MaleficNav is a Kotlin-based navigation library designed for Compose Desktop applications. It provides a simple and flexible way to manage navigation routes, including dynamic and static routes, with support for YAML-based configuration. It is based off and intended for use alongside [PreCompose](https://github.com/Tlaster/PreCompose).

## Features

- **Dynamic and Static Routes**: Define routes with or without parameters.
- **YAML Configuration**: Load routes from a YAML file.
- **Composable Navigation**: Use composable functions for route content.
- **Navigator Integration**: Seamless integration with PreCompose navigation.
- **Basic Setup**: Comes with a basic implementation that's akin to plug and play.

## Installation

Until this library has been published as a package, you'll need to build this project. Please disregard the below.
To use MaleficNav in your project, add the following dependencies to your `build.gradle.kts` file:

```kotlin
dependencies {
    implementation("moe.tlaster:precompose:1.6.2")
}
```

## Usage

### 1. Create Composable Functions

Below are a couple I made using the route setup that follows.

```kotlin
@Composable
fun App1(id: String, name: String?) {
  var text by remember { mutableStateOf("Hello, World!") }

  Box(modifier = Modifier.fillMaxSize(), contentAlignment = Alignment.Center) {
    Column(
      horizontalAlignment = Alignment.CenterHorizontally,
      verticalArrangement = Arrangement.Center,
    ) {
      Button(onClick = { text = "Hello, Desktop!" }) { Text(text) }
      Spacer(modifier = Modifier.height(16.dp))
      Text("ID: $id")
      name?.let { Text("Name: $name") } ?: run { Text("Unnamed") }
    }
  }
}

@Composable
fun App2(navi: Navigator) {
  var text by remember { mutableStateOf("Hello, World 2!") }

  Box(modifier = Modifier.fillMaxSize(), contentAlignment = Alignment.Center) {
    Column(
      horizontalAlignment = Alignment.CenterHorizontally,
      verticalArrangement = Arrangement.Center,
    ) {
      Button(onClick = { text = "Hello, Desktop 2!" }) { Text(text) }
      Spacer(modifier = Modifier.height(16.dp))
      Button(onClick = { navi.navigate("home/1061844") }) { Text("Go to App1") }
      Spacer(modifier = Modifier.height(16.dp))
      Button(onClick = { navi.navigate("hidden/boo!") }) { Text("Go to Hidden Page") }
    }
  }
}
```

### 2. Define Routes in YAML

Create a `routes.yaml` file to define your routes. For example:

```yaml
routes:
  - name: home2
    composable: App2
  - name: home
    composable: App1
    hidden: true
    params:
      - id
      - name?
  - name: hidden
    composable: Text
    hidden: true
    params:
      - text?
startup: home2
```

### 3. Create Navigation Menu

Define the navigation menu using `RoutedSidebar` and `RoutedNavHost` or create your own implementations of them. Everything I used to make them is available through RouteManager and you can look at those two composables in the same location for inspiration.

```kotlin
@Composable
fun NavigationMenu() {
    Row(modifier = Modifier.fillMaxWidth().fillMaxHeight()) {
        RouteManager.RoutedSidebar()
        Divider(color = Color.Black, modifier = Modifier.fillMaxHeight().width(1.dp))
        RouteManager.RoutedNavHost()
    }
}
```

### 4. Define Composable Map

Create a map of composable functions that follows whatever you set up:

```kotlin
val composableMap: Map<String, @Composable (List<String?>) -> Unit> = mapOf(
    "App1" to { params -> App1(id = params[0]!!, name = params[1, null]) },
    "App2" to { _ -> App2(RouteManager.navigator) },
    "Text" to { params -> Text(text = params[0, "Nope."]) }
)
```

### 5. Initialize RouteManager

Initialize the `RouteManager` in your `main` function:

```kotlin
fun main() = application {
    NavWindow(onCloseRequest = ::exitApplication) {
        MaterialTheme {
            RouteManager.initialize(composableMap, this::class.java.getResourceAsStream("/routes.yaml")!!)
            NavigationMenu()
        }
    }
}
```

### Finished Example Project Structure

```
src/
├── main/
│   ├── kotlin/
│   │   ├── screens/
│   │   │   ├── App1.kt
│   │   │   ├── App2.kt
│   │   ├── Main.kt
│   ├── resources/
│   │   └── routes.yaml
```

## ComposeDesktopTemplate

If you want to start with a completely set up project, you can find the above example [here](https://github.com/OmyDaGreat/ComposeDesktopTemplate).

## Contributing

Contributions are welcome! Please open an issue or submit a pull request.

## Contact

For any questions or feedback, please feel free to contact me.