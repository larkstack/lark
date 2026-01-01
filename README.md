# Lark — A Dart Web Framework

_Light. Elegant. Calm._

Lark is a Dart web framework that combines the best ideas from modern web development into a cohesive, type-safe package.

---

## Quick Start

```dart
import 'package:lark/lark.dart';

class Counter extends Component {
  final count = ref(0);

  @override
  Component build() => Column([
    Text('Count: ${count()}'),
    Button(
      label: '+',
      onPressed: () => count.value++,
    ),
  ]);
}
```

📖 **[Installation →](docs/1.getting-started/2.installation.md)**

---

## Documentation

### Getting Started

| Guide                                                             | Description                         |
| ----------------------------------------------------------------- | ----------------------------------- |
| [Introduction](docs/1.getting-started/1.introduction.md)          | What is Lark and why use it         |
| [Installation](docs/1.getting-started/2.installation.md)          | Set up a new Lark project           |
| [Configuration](docs/1.getting-started/3.configuration.md)        | Configure your app                  |
| [Assets](docs/1.getting-started/4.assets.md)                      | Managing static assets              |
| [Styling](docs/1.getting-started/5.styling.md)                    | Styling, Tailwind, theming          |
| [Routing](docs/1.getting-started/6.routing.md)                    | File-based and programmatic routing |
| [SEO & Meta](docs/1.getting-started/7.seo-and-meta.md)            | Head management, meta tags          |
| [Transitions](docs/1.getting-started/8.transitions.md)            | Page and component transitions      |
| [Data Fetching](docs/1.getting-started/9.data-fetching.md)        | useFetch, usePost, caching          |
| [State Management](docs/1.getting-started/10.state-management.md) | Stores, global state                |
| [Error Handling](docs/1.getting-started/11.error-handling.md)     | Error boundaries, logging           |
| [Prerendering](docs/1.getting-started/12.prerendering.md)         | Static Site Generation (SSG)        |
| [Deployment](docs/1.getting-started/13.deployment.md)             | Build and deploy                    |
| [Environment Variables](docs/1.getting-started/14.environment.md) | Configuration and secrets           |
| [Testing](docs/1.getting-started/15.testing.md)                   | Unit and integration tests          |

### Directory Structure

| Directory                                                   | Purpose                |
| ----------------------------------------------------------- | ---------------------- |
| [Overview](docs/2.directory-structure/1.overview.md)        | Project organization   |
| [lib/](docs/2.directory-structure/2.lib.md)                 | Source code            |
| [pages/](docs/2.directory-structure/3.pages.md)             | Route pages            |
| [components/](docs/2.directory-structure/4.components.md)   | Reusable components    |
| [layouts/](docs/2.directory-structure/5.layouts.md)         | Page layouts           |
| [stores/](docs/2.directory-structure/6.stores.md)           | Global state           |
| [composables/](docs/2.directory-structure/7.composables.md) | Reusable logic         |
| [services/](docs/2.directory-structure/8.services.md)       | API clients, utilities |
| [web/](docs/2.directory-structure/9.web.md)                 | Static web assets      |

### Guide

| Topic                                              | Description                   |
| -------------------------------------------------- | ----------------------------- |
| [Key Concepts](docs/3.guide/1.key-concepts.md)     | Deep dive into core concepts  |
| [Best Practices](docs/3.guide/2.best-practices.md) | Patterns and conventions      |
| [Recipes](docs/3.guide/3.recipes.md)               | Common patterns and solutions |
| [Going Further](docs/3.guide/4.going-further.md)   | Advanced topics               |

---

## Core Concepts

### 1. Reactivity

Lark's reactivity is built on three primitives:

```dart
// State
final count = ref(0);
count();        // Read: 0
count.value++;  // Write: 1

// Derived values (cached, auto-tracked)
final doubled = computed(() => count() * 2);

// Side effects (auto-tracked)
effect(() => print('Count is: ${count()}'));

// Batch updates
batch(() {
  firstName.value = 'Jane';
  lastName.value = 'Smith';
});
```

📖 **[Key Concepts → Reactivity](docs/3.guide/1.key-concepts.md)**

---

### 2. Components

All UI components extend `Component`:

```dart
class Greeting extends Component {
  final String name;
  Greeting({required this.name});

  @override
  Component build() => Text('Hello, $name!');
}
```

With reactive state:

```dart
class Counter extends Component {
  final count = ref(0);

  @override
  Component build() => Button(
    label: 'Count: ${count()}',
    onPressed: () => count.value++,
  );
}
```

📖 **[Components](docs/2.directory-structure/4.components.md)**

### 3. Stores

Global state with Pinia-like stores:

```dart
final useCart = defineStore('cart', () {
  final items = ref<List<CartItem>>([]);
  final total = computed(() => items().fold(0, (s, i) => s + i.price));

  void addItem(Product p) => items.value = [...items(), CartItem(p)];

  return (items: items, total: total, addItem: addItem);
});

// In any component
class CartIcon extends Component {
  final cart = useCart();

  @override
  Component build() {
    return Badge(count: cart.total());
  }
}
```

📖 **[State Management](docs/1.getting-started/10.state-management.md)** | **[Stores](docs/2.directory-structure/6.stores.md)**

---

### 4. Data Fetching

```dart
final posts = useFetch<List<Post>>('/api/posts',
  transform: (json) => (json as List).map(Post.fromJson).toList(),
);

// In build
if (posts.loading()) return Spinner();
if (posts.error() != null) return Text('Error: ${posts.error()}');
return Column(posts.data()!.map((p) => Text(p.title)).toList());

// Refresh
posts.refresh();
```

| Composable      | Purpose                 |
| --------------- | ----------------------- |
| `useFetch`      | GET with reactive state |
| `useLazyFetch`  | Manual-trigger fetch    |
| `usePost`       | POST/mutations          |
| `usePagination` | Pagination helpers      |

📖 **[Data Fetching](docs/1.getting-started/9.data-fetching.md)** | **[Composables](docs/2.directory-structure/7.composables.md)**

---

### 5. Routing

```dart
// Navigate
final router = useRouter();
router.push('/about');
router.replace('/login');
router.back();

// Access route
final route = useRoute();
route.path      // '/users/123'
route.params    // {'id': '123'}
route.query     // {'page': '1'}
```

Route definitions:

```dart
final routes = RouteCollection([
  route('/', (_) => HomePage()),
  route('/about', (_) => AboutPage()),
  route('/users/:id', (p) => UserDetail(id: p['id']!)),
]);
```

📖 **[Routing](docs/1.getting-started/6.routing.md)** | **[Pages](docs/2.directory-structure/3.pages.md)**

---

### 6. Primitives

| Component  | Usage                                             |
| ---------- | ------------------------------------------------- |
| `Text`     | `Text('Hello', style: Style(...))`                |
| `Button`   | `Button(label: 'Click', onPressed: () => {})`     |
| `Input`    | `Input(value: email, onInput: (v) => ...)`        |
| `Image`    | `Image(src: 'path/to/image.png')`                 |
| `Link`     | `Link(to: '/path', label: 'Go')`                  |
| `Column`   | `Column([...], gap: '16px')`                      |
| `Row`      | `Row([...], gap: '8px')`                          |
| `Stack`    | `Stack([...])`                                    |
| `Spinner`  | `Spinner()`                                       |
| `Checkbox` | `Checkbox(checked: value, onChange: ...)`         |
| `Select`   | `Select(value: v, options: [...], onChange: ...)` |

---

## Project Structure

```
my-app/
├── lib/
│   ├── app.dart              # App entry
│   ├── pages/                # Route pages
│   │   ├── home.dart
│   │   └── about.dart
│   ├── components/           # Reusable components
│   │   └── card.dart
│   ├── stores/               # Global state
│   │   └── cart.dart
│   └── composables/          # Custom composables
│       └── use_auth.dart
├── web/
│   ├── index.html
│   ├── main.dart
│   └── _assets/              # Static assets (images, fonts, css)
└── pubspec.yaml
```

📖 **[Directory Structure Overview](docs/2.directory-structure/1.overview.md)**

---

## VS Code Extension

Enhanced developer experience with snippets, syntax highlighting, and Tailwind IntelliSense.

📖 **[lark-vscode/](lark-vscode/README.md)**

---

## License

MIT License - see [LICENSE](LICENSE) for details.
