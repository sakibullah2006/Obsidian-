## **BLoC Pattern in Flutter**

The **BLoC (Business Logic Component)** pattern is an architectural pattern used in Flutter applications to separate business logic from the UI. It ensures a clean and reactive code structure, enhancing testability and scalability.

---

### **Core Concepts of BLoC**

1. **Events**
    
    - Actions or triggers that are sent to the BLoC, such as user interactions or API calls.
    - Examples: Button clicks, data fetch requests.
2. **States**
    
    - Represent the current UI state of the application.
    - Examples: Loading, success, error states.
3. **BLoC**
    
    - The heart of the pattern, which handles the flow of events and emits states in response.
    - Transforms incoming events into outgoing states.
4. **Stream**
    
    - Used for asynchronous communication between BLoC and UI.

---

### **Classes Used in BLoC**

#### **1. Event Class**

- Abstract class for all events.
- Use cases: Defining user actions, API requests.

#### **2. State Class**

- Abstract class for all states.
- Use cases: Defining application or UI states.

#### **3. Bloc Class**

- Extends `Bloc<Event, State>` for advanced cases.
- Use cases: Complex state management.

#### **4. Cubit Class**

- Simplified version of BLoC, extends `Cubit<State>`.
- Use cases: Simple state management.

---

### **Steps to Use BLoC**

#### **Step 1: Define Your States**

Create a base state class and its subclasses for each UI state.

```dart
abstract class CounterState {}  
class CounterInitial extends CounterState {}  
class CounterValueState extends CounterState {   
	final int value;   
	CounterValueState(this.value); 
}
```
---

#### **Step 2: Define Your Events**

Create a base event class and its subclasses for user actions or triggers.

```dart
abstract class CounterEvent {}  
class IncrementEvent extends CounterEvent {}  
class DecrementEvent extends CounterEvent {}
```
---

#### **Step 3: Implement the BLoC**

Create a BLoC class to handle events and emit states.

##### Using `Bloc`:

```dart
class CounterBloc extends Bloc<CounterEvent, CounterState> {   
	CounterBloc() : super(CounterInitial());    
	@override   
	Stream<CounterState> mapEventToState(CounterEvent event) async* {     
		if (event is IncrementEvent) {       
			int currentValue = (state is CounterValueState) ? (state as CounterValueState).value : 0;       
			yield CounterValueState(currentValue + 1);     
		} else if (event is DecrementEvent) {       
			int currentValue = (state is CounterValueState) ? (state as CounterValueState).value : 0;       
			yield CounterValueState(currentValue - 1);     
		}   
	} 
}
```

##### Using `Cubit`:

```dart
class CounterCubit extends Cubit<int> {   
	CounterCubit() : super(0);    
	void increment() => emit(state + 1);   
	void decrement() => emit(state - 1); 
}
```

---

#### **Step 4: Inject the BLoC into the Widget Tree**

Use `BlocProvider` to make the BLoC accessible to widgets.

```dart
BlocProvider(   
	create: (context) => CounterBloc(),   
	child: CounterScreen(), 
);
```

---

#### **Step 5: Build the UI with BlocBuilder**

Use `BlocBuilder` to update the UI based on state changes.

```dart
BlocBuilder<CounterBloc, CounterState>(   
	builder: (context, state) {     
		if (state is CounterValueState) {       
			return Text('Counter: ${state.value}');     
		}     
		return Text('Counter: 0');   
	}, 
);
```

---

#### **Step 6: Handle Side Effects with BlocListener**

For navigation, snack bars, or dialogs.

```dart
BlocListener<CounterBloc, CounterState>(   
	listener: (context, state) {     
		if (state is CounterValueState && state.value == 5) {
			ScaffoldMessenger.of(context).showSnackBar(SnackBar( 
				content: Text('Counter reached 5!'),   
			));     
		}   
	},   
	child: CounterScreen(), 
);
```

---

#### **Step 7: Combine Builder and Listener with BlocConsumer**

For scenarios needing both UI updates and side effects.

```dart
BlocConsumer<CounterBloc, CounterState>(   
	listener: (context, state) {     
		if (state is CounterValueState && state.value == 5) {
			ScaffoldMessenger.of(context).showSnackBar(SnackBar(         
			   content: Text('Counter reached 5!'),       
			));     
		}   
	},   
	builder: (context, state) {     
		if (state is CounterValueState) {       
			return Text('Counter: ${state.value}');     
		}     
		return CircularProgressIndicator();   
	}, 
);
```

---

### **Best Practices for Using BLoC**

1. **Keep BLoC Logic Simple**
    
    - Offload heavy logic to services or repositories.
2. **Avoid Business Logic in UI**
    
    - Keep the UI reactive by depending only on states.
3. **Use Immutable States**
    
    - Prevent accidental mutations for predictable behavior.
4. **Use Abstract Base Classes for Events and States**
    
    - Makes code easier to extend and maintain.
5. **Test Your BLoCs**
    
    - Use `bloc_test` to validate state transitions.
6. **Use `BlocObserver` for Debugging**
    
    - Track state transitions during development.
7. **Optimize Performance with `buildWhen`**
    
    - Rebuild widgets only when necessary.

---

### **Project Structure for BLoC**

Organize files for better maintainability:

```lua
lib/
|-- blocs/
|   |-- counter_bloc.dart
|-- models/
|   |-- user_model.dart
|-- repositories/
|   |-- user_repository.dart
|-- screens/
|   |-- counter_screen.dart
|-- widgets/
|   |-- custom_button.dart


```

---

### **Testing Your BLoCs**

#### Install `bloc_test`:

```
flutter pub add bloc_test
```

#### Write Test:

```dart
void main() {   
	blocTest<CounterBloc, CounterState>(     
		'emits [CounterValueState(1)] when IncrementEvent is added',     
		build: () => CounterBloc(),     
		act: (bloc) => bloc.add(IncrementEvent()),     
		expect: () => [CounterValueState(1)],   
	); 
}
```

---

### **Advanced Features**

- **Dependency Injection**: Use `get_it` or `RepositoryProvider`.
- **Build Optimizations**: Use `buildWhen` to prevent unnecessary rebuilds.

#### Example:

```dart
BlocBuilder<CounterBloc, CounterState>(   
	buildWhen: (previous, current) => current != previous,   
	builder: (context, state) {     
		// Build UI   
	}, 
);
```