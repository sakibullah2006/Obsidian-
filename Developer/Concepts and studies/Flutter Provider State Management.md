## **Overview**

Provider is a Flutter state management solution built on top of **InheritedWidget**. It enables sharing and managing the state across widgets in a reactive and efficient manner. Provider is officially recommended by the Flutter team due to its simplicity, flexibility, and efficiency.

---

## **Why Use Provider?**

1. **Declarative State Management**: Simplifies managing app state without manually passing data through widget constructors.
2. **Efficient Rebuilds**: Only rebuilds widgets that rely on the specific state, reducing unnecessary computations.
3. **Clean Architecture**: Encourages separation of concerns by keeping UI and business logic apart.
4. **Easy to Scale**: Suitable for apps of any size, from small demos to complex, multi-feature applications.

---

## **How Provider Works**

1. **Provider Tree**: You wrap your widget tree with a `Provider` to expose state to child widgets.
2. **Consumer**: Widgets subscribe to changes using `Consumer`, which rebuilds on state updates.
3. **ChangeNotifierProvider**: A specific provider for `ChangeNotifier`-based state objects.

---
## **Key Features of This Implementation**

1. **Separation of Concerns**:
    
    - Models handle raw data.
    - Providers manage state and notify listeners.
    - Screens handle UI logic.
    - Widgets encapsulate reusable UI components.
2. **Scalability**:
    
    - The modular structure makes it easy to add new features without disturbing existing code.
3. **Best Practices in `ChangeNotifier`**:
    
    - The state (`_counter`) is private, and only the getter (`counter`) is exposed.
    - Each state update calls `notifyListeners()` for reactive rebuilds.
4. **Error Handling**:
    
    - The custom `AppException` class can be expanded to handle API errors, validation, etc.
5. **Provider Usage**:
    
    - `context.read` is used for actions (non-rebuilding methods like `increment` and `decrement`).
    - `context.watch` or `Consumer` rebuilds widgets that depend on state.

---


## **Core Concepts**

### 1. **State Management**

State is the data that defines the behavior or appearance of your app. Provider helps manage:

- **Local State**: Limited to specific widgets (e.g., text input values).
- **App-Wide State**: Shared across multiple widgets (e.g., user authentication, theme).

### 2. **Provider**

- A wrapper around `InheritedWidget` that exposes state to the widget tree.
- Automatically rebuilds widgets when the state changes.

### 3. **ChangeNotifier**

- A class that allows listening for changes. Widgets can subscribe to its updates.
- Use `notifyListeners()` to signal state changes to listeners.

### 4. **Consumers**

- Widgets that rebuild when the provided state changes.

---

## **Provider Variants**

1. **Provider**
    
    - Simplest form of state provider.
    - Used for providing static or rarely updated values.

```dart
	Provider<String>(  
		create: (_) => "Hello, Provider!",   
		child: MyApp(), 
	);
```
    
2. **ChangeNotifierProvider**
    
    - Works with `ChangeNotifier` objects to manage mutable state.
```dart
	ChangeNotifierProvider(   
		create: (_) => Counter(),
		child: MyApp(), 
	);
```
    
    
3. **FutureProvider**
    
    - Handles asynchronous state with `Future`.

```dart
FutureProvider<String>(   
	create: (_) async => fetchData(),
	initialData: "Loading...",   
	child: MyApp(), 
);
```
    
    
4. **StreamProvider**
    
    - Provides state from a `Stream`.
    
```dart
	StreamProvider<int>(   
		create: (_) => streamCounter(),   
		initialData: 0,   
		child: MyApp(), 
	);
```
    
    
5. **MultiProvider**
    
    - Manages multiple providers at once for scalability.

```dart
MultiProvider(   
	providers: [     
		ChangeNotifierProvider(create: (_) => Counter()),     
		Provider(create: (_) => SomeStaticValue()),   
	],   
	child: MyApp(), 
);
```
    
6. **ProxyProvider**
    
    - Allows creating providers that depend on other providers.

```dart
ProxyProvider<Counter, DependentState>(   
	update: (_, counter, dependentState) => DependentState(counter.value), 
);
```
    
    

---

## **How to Use Provider**

### 1. **Setup and Installation**

Add the `provider` package to your `pubspec.yaml` file:

```yaml
dependencies:   provider: ^6.0.5
```



Run:
```bash
flutter pub get`
```

---

### 2. **Basic Example: Counter App**

#### `main.dart`

```dart
import 'package:flutter/material.dart'; import 'package:provider/provider.dart'; import 'counter_model.dart';  

void main() {   
	runApp(     
		ChangeNotifierProvider(       
			create: (_) => Counter(),       
			child: MyApp(),     
		),   
	); 
}  

class MyApp extends StatelessWidget {   
	@override   
	Widget build(BuildContext context) {     
		return MaterialApp(       
			home: CounterScreen(),     
		);   
	} 
}

```

#### `counter_model.dart`

```dart 
import 'package:flutter/material.dart';  
class Counter extends ChangeNotifier {   
	int _count = 0; 

	Counter(this._count);
	
	int get count => _count;
	    
	void increment() {     
		_count++;     
		notifyListeners();   
	} 
}
```

#### `counter_screen.dart`

```dart
import 'package:flutter/material.dart'; 
import 'package:provider/provider.dart'; 
import 'counter_model.dart';  
class CounterScreen extends StatelessWidget {   
	@override   
	Widget build(BuildContext context) {     
		return Scaffold(       
			appBar: AppBar(title: Text('Counter App')),       
			body: Center(         
				child: Consumer<Counter>(           
					builder: (_, counter, __) => Text( 
						'Count:${counter.count}',             
						style: TextStyle(fontSize: 36),
						),         
				),       
			),       
			floatingActionButton: FloatingActionButton(         
				onPressed: () => context.read<Counter>().increment(), 
				child: Icon(Icons.add),       
			),     
		);   
	} 
}

```


---

### 3. **Advanced Use Cases**

#### **Optimizing Rebuilds with Selector**

Use `Selector` to filter state changes and avoid unnecessary rebuilds:

```dart
Selector<Counter, int>(   
	selector: (_, counter) => counter.count,   
	builder: (_, count, __) => Text('Count: $count'), 
);
```


#### **Using FutureProvider**

```dart
FutureProvider<String>(   
	create: (_) async => fetchUserData(),   
	initialData: "Fetching user data...",   
	child: UserScreen(), 
);
```

#### **Combining Providers with ProxyProvider**

```dart
ProxyProvider<User, AuthService>(   
	update: (_, user, authService) => AuthService(user),   
	child: MyApp(), 
);
```


---

## **Common Issues and Troubleshooting**

1. **ProviderNotFoundException**
    
    - Occurs when trying to access a provider outside its context.
    - Solution: Ensure the `Provider` wraps the widget tree where it's being accessed.
2. **Unnecessary Rebuilds**
    
    - Avoid rebuilding widgets unnecessarily by using `Selector` or setting `listen: false` in `Provider.of`.
3. **Complex Dependencies**
    
    - Use `ProxyProvider` to manage dependent providers.

---

## **Best Practices**

1. **Break Down State**: Divide state into smaller providers instead of a monolithic one.
2. **Use Read/Watch Wisely**:
    - `context.read`: Access state without listening to changes.
    - `context.watch`: Listen to state changes and rebuild widgets.
3. **Optimize with Selector**: Use `Selector` for better performance by rebuilding only on specific changes.
4. **Document Dependencies**: Clearly define which providers depend on others.

---

## **File Structure for Large Projects**

```plaintxt
lib/ 
├── main.dart 
├── models/ 
│ ├── counter.dart 
│ └── user.dart 
├── providers/ 
│ ├── counter_provider.dart 
│ └── auth_provider.dart 
├── screens/ 
│ ├── home_screen.dart 
│ └── login_screen.dart 
├── widgets/ 
│ ├── counter_display.dart 
│ └── login_form.dart

```


---

## **Additional Resources**

1. **Official Docs**: Flutter Provider Package
2. **Flutter State Management Guide**: Official Flutter Docs
3. **Video Tutorials**: [Reso Coder’s Provider Guide](https://resocoder.com)