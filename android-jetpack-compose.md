### Composable Basics

#### Text
```kotlin
Text(
    text = "Hello, Compose!",
    color = Color.Black,
    fontSize = 16.sp,
    fontWeight = FontWeight.Bold
)
```

#### Button
```kotlin
Button(
    onClick = { /* Handle click */ },
    modifier = Modifier.padding(16.dp)
) {
    Text("Click Me")
}
```

#### Image
```kotlin
Image(
    painter = painterResource(id = R.drawable.ic_launcher_foreground),
    contentDescription = "Sample Image",
    modifier = Modifier.size(100.dp)
)
```

### Layouts

#### Row
```kotlin
Row(
    modifier = Modifier.padding(16.dp),
    horizontalArrangement = Arrangement.SpaceBetween,
    verticalAlignment = Alignment.CenterVertically
) {
    Text("First Item")
    Text("Second Item")
}
```

#### Column
```kotlin
Column(
    modifier = Modifier.padding(16.dp),
    verticalArrangement = Arrangement.spacedBy(8.dp),
    horizontalAlignment = Alignment.CenterHorizontally
) {
    Text("First Item")
    Text("Second Item")
}
```

#### Box
```kotlin
Box(
    modifier = Modifier
        .fillMaxSize()
        .background(Color.Gray),
    contentAlignment = Alignment.Center
) {
    Text("Centered Text")
}
```

#### LazyColumn (RecyclerView alternative)
```kotlin
val items = listOf("Item 1", "Item 2", "Item 3")
LazyColumn(
    modifier = Modifier.padding(16.dp),
    verticalArrangement = Arrangement.spacedBy(8.dp)
) {
    items(items) { item ->
        Text(item)
    }
}
```

### Modifiers

#### Size
```kotlin
Modifier.size(100.dp)
```

#### Padding
```kotlin
Modifier.padding(16.dp)
```

#### Background
```kotlin
Modifier.background(Color.Blue)
```

#### Fill
```kotlin
Modifier.fillMaxWidth()
Modifier.fillMaxHeight()
Modifier.fillMaxSize()
```

#### Align
```kotlin
Modifier.align(Alignment.CenterHorizontally)
Modifier.align(Alignment.BottomEnd)
```

### State

#### State Management
```kotlin
var count by remember { mutableStateOf(0) }

Button(onClick = { count++ }) {
    Text("Clicked $count times")
}
```

### Scaffold

#### Scaffold with TopBar and FloatingActionButton
```kotlin
Scaffold(
    topBar = {
        TopAppBar(
            title = { Text("My App") },
            navigationIcon = {
                IconButton(onClick = { /* Handle navigation icon click */ }) {
                    Icon(Icons.Filled.Menu, contentDescription = "Menu")
                }
            }
        )
    },
    floatingActionButton = {
        FloatingActionButton(onClick = { /* Handle FAB click */ }) {
            Icon(Icons.Filled.Add, contentDescription = "Add")
        }
    }
) {
    // Screen content
}
```

### Shapes

#### RoundedCornerShape
```kotlin
Box(
    modifier = Modifier
        .size(100.dp)
        .background(Color.Red, shape = RoundedCornerShape(8.dp))
)
```

#### CircleShape
```kotlin
Box(
    modifier = Modifier
        .size(100.dp)
        .background(Color.Green, shape = CircleShape)
)
```

### TextField

#### Basic TextField
```kotlin
var text by remember { mutableStateOf("") }

TextField(
    value = text,
    onValueChange = { text = it },
    label = { Text("Enter text") }
)
```

### Common Properties

#### Color
```kotlin
color = Color.Blue
backgroundColor = Color.Gray
contentColor = Color.White
```

#### Alignment
```kotlin
horizontalAlignment = Alignment.CenterHorizontally
verticalAlignment = Alignment.CenterVertically
contentAlignment = Alignment.Center
```

#### Arrangement
```kotlin
horizontalArrangement = Arrangement.SpaceBetween
verticalArrangement = Arrangement.spacedBy(16.dp)
```

### Examples of Combined Usage

#### Simple UI with Text, Button, and Image
```kotlin
@Composable
fun SimpleUI() {
    Column(
        modifier = Modifier
            .fillMaxSize()
            .padding(16.dp),
        horizontalAlignment = Alignment.CenterHorizontally,
        verticalArrangement = Arrangement.Center
    ) {
        Text(
            text = "Hello, Jetpack Compose!",
            fontSize = 24.sp,
            fontWeight = FontWeight.Bold
        )

        Spacer(modifier = Modifier.height(16.dp))

        Button(onClick = { /* Do something */ }) {
            Text("Click Me")
        }

        Spacer(modifier = Modifier.height(16.dp))

        Image(
            painter = painterResource(id = R.drawable.ic_launcher_foreground),
            contentDescription = "Sample Image",
            modifier = Modifier.size(100.dp)
        )
    }
}
```

#### LazyColumn with Items
```kotlin
@Composable
fun ItemList(items: List<String>) {
    LazyColumn(
        modifier = Modifier.fillMaxSize(),
        contentPadding = PaddingValues(16.dp),
        verticalArrangement = Arrangement.spacedBy(8.dp)
    ) {
        items(items) { item ->
            Text(
                text = item,
                modifier = Modifier
                    .fillMaxWidth()
                    .padding(8.dp)
                    .background(Color.LightGray, RoundedCornerShape(8.dp))
                    .padding(16.dp),
                fontSize = 18.sp
            )
        }
    }
}
```
