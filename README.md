# Master JavaScript for ASP.NET Core: From A to Z

## Table of Contents

1. [Introduction to JavaScript in ASP.NET Core](#chapter-1-introduction-to-javascript-in-aspnet-core)
2. [Including JavaScript in ASP.NET Core Projects](#chapter-2-including-javascript-in-aspnet-core-projects)
3. [JavaScript Fundamentals for C# Developers](#chapter-3-javascript-fundamentals-for-c-developers)
4. [DOM Manipulation and Events](#chapter-4-dom-manipulation-and-events)
5. [AJAX and Fetch API](#chapter-5-ajax-and-fetch-api)
6. [jQuery in ASP.NET Core](#chapter-6-jquery-in-aspnet-core)
7. [Client-Side Validation](#chapter-7-client-side-validation)
8. [JavaScript Modules and Bundling](#chapter-8-javascript-modules-and-bundling)
9. [TypeScript Integration](#chapter-9-typescript-integration)
10. [SignalR JavaScript Client](#chapter-10-signalr-javascript-client)
11. [JavaScript Interop in Blazor](#chapter-11-javascript-interop-in-blazor)
12. [SPA Frameworks Integration](#chapter-12-spa-frameworks-integration)
13. [Managing JavaScript Libraries](#chapter-13-managing-javascript-libraries)
14. [Debugging JavaScript](#chapter-14-debugging-javascript)
15. [Security Considerations](#chapter-15-security-considerations)
16. [Performance Optimization](#chapter-16-performance-optimization)
17. [Testing JavaScript Code](#chapter-17-testing-javascript-code)
18. [Best Practices](#chapter-18-best-practices)
19. [Quick Reference Cheatsheet](#chapter-19-quick-reference-cheatsheet)
20. [Troubleshooting Guide](#chapter-20-troubleshooting-guide)

---

## Chapter 1: Introduction to JavaScript in ASP.NET Core

### The Role of JavaScript in Modern Web Development

JavaScript has evolved from a simple scripting language for adding interactivity to web pages into a powerful, full-featured programming language that powers everything from dynamic user interfaces to server-side applications. In the ASP.NET Core ecosystem, JavaScript plays a crucial role in creating rich, interactive user experiences that complement server-side C# code. Understanding how to effectively integrate JavaScript with ASP.NET Core is essential for building modern web applications that meet user expectations for responsiveness and interactivity.

The relationship between ASP.NET Core and JavaScript is multifaceted. ASP.NET Core serves as the server-side framework handling HTTP requests, business logic, and data access, while JavaScript operates on the client side, managing user interactions, DOM manipulation, and asynchronous communication with the server. This separation of concerns allows each technology to excel in its domain while working together to deliver cohesive applications. With the rise of Single Page Applications (SPAs) and technologies like Blazor, the integration patterns have become more sophisticated, offering developers multiple approaches to building web applications.

### Server-Side vs Client-Side Rendering

Understanding the distinction between server-side rendering (SSR) and client-side rendering (CSR) is fundamental to making informed architectural decisions in ASP.NET Core applications. Traditional ASP.NET Core MVC and Razor Pages use server-side rendering, where the server generates complete HTML pages and sends them to the client. JavaScript in this context enhances the user experience by adding interactivity, handling form validation, and making AJAX calls for partial page updates.

Client-side rendering shifts the rendering responsibility to the browser. The server provides a minimal HTML shell and JavaScript handles the rest—fetching data, rendering templates, and managing navigation. SPA frameworks like React, Angular, and Vue embrace this model. ASP.NET Core supports both approaches through its flexible architecture, and you can even combine them using techniques like progressive enhancement or hybrid rendering with Blazor.

### When to Use JavaScript in ASP.NET Core

JavaScript becomes essential in several scenarios within ASP.NET Core applications. Real-time updates using SignalR require JavaScript on the client side to receive and process server push notifications. Complex form interactions, such as dependent dropdowns, dynamic field addition, and real-time validation, benefit significantly from JavaScript enhancement. Rich UI components like date pickers, modal dialogs, and data grids often rely on JavaScript libraries.

Client-side data visualization with charts and graphs requires JavaScript libraries like Chart.js or D3. Single Page Applications demand JavaScript (or WebAssembly with Blazor) for client-side routing and state management. File uploads with progress indicators need JavaScript for XMLHttpRequest or Fetch API. Third-party integrations such as payment gateways, maps, and social media widgets typically provide JavaScript SDKs.

### ASP.NET Core's JavaScript Integration Features

ASP.NET Core provides several built-in features to support JavaScript integration. The framework includes tag helpers that simplify referencing JavaScript files with cache-busting capabilities. The `@section` directive in Razor allows you to place scripts at specific locations in the layout. The environment tag helper enables different scripts for development and production environments. Built-in support for libraries like jQuery Validation integrates seamlessly with ASP.NET Core's validation attributes.

Modern ASP.NET Core applications can leverage Blazor, which reduces JavaScript requirements but still provides JavaScript interop for scenarios requiring direct browser API access or integration with existing JavaScript libraries. For SPA development, ASP.NET Core offers templates and integration points for React, Angular, and Vue, complete with client-side routing, hot module replacement, and production-ready build configurations.

---

## Chapter 2: Including JavaScript in ASP.NET Core Projects

### Script Tag Basics

The fundamental way to include JavaScript in ASP.NET Core views is through the `<script>` tag. You can place scripts directly in views or use Razor sections to organize them properly within your layout.

```html
<!-- Direct script inclusion in a view -->
<script src="~/js/site.js"></script>

<!-- Inline script -->
<script>
    console.log('Page loaded');
    document.addEventListener('DOMContentLoaded', function() {
        // Initialize page-specific JavaScript
    });
</script>
```

### Using Razor Sections for Scripts

Razor sections provide a clean way to inject page-specific scripts into designated areas of the layout, ensuring scripts load in the correct order and location.

```html
<!-- _Layout.cshtml -->
<!DOCTYPE html>
<html>
<head>
    <!-- Head scripts section -->
    @RenderSection("HeadScripts", required: false)
</head>
<body>
    @RenderBody()

    <!-- Scripts section at end of body -->
    @RenderSection("Scripts", required: false)
    
    <!-- Default scripts -->
    <script src="~/lib/jquery/dist/jquery.min.js"></script>
    <script src="~/js/site.js"></script>
</body>
</html>

<!-- Index.cshtml -->
@{
    ViewData["Title"] = "Home Page";
}

<h1>Welcome</h1>

@section Scripts {
    <script src="~/js/pages/index.js"></script>
    <script>
        // Page-specific initialization
        $(document).ready(function() {
            console.log('Index page ready');
        });
    </script>
}

@section HeadScripts {
    <script>
        // Scripts that must load in head
        var appConfig = @Html.Raw(Json.Serialize(ViewBag.Config));
    </script>
}
```

### Environment-Based Script Loading

ASP.NET Core's environment tag helper allows you to load different scripts based on the hosting environment, enabling development conveniences like unminified files while serving optimized production builds.

```html
<!-- Environment-based script loading -->
<environment include="Development">
    <script src="~/lib/jquery/dist/jquery.js"></script>
    <script src="~/lib/bootstrap/dist/js/bootstrap.bundle.js"></script>
    <script src="~/js/site.js"></script>
</environment>

<environment exclude="Development">
    <script src="~/lib/jquery/dist/jquery.min.js"></script>
    <script src="~/lib/bootstrap/dist/js/bootstrap.bundle.min.js"></script>
    <script src="~/js/site.min.js" asp-append-version="true"></script>
</environment>

<!-- CDN with local fallback -->
<script src="https://cdn.jsdelivr.net/npm/jquery@3.6.0/dist/jquery.min.js"
        integrity="sha256-/xUj+3OJU5yExlq6GSYGSHk7tPXikynS7ogEvDej/m4="
        crossorigin="anonymous">
</script>
<script>
    // Fallback to local if CDN fails
    window.jQuery || document.write('<script src="~/lib/jquery/dist/jquery.min.js"><\/script>');
</script>
```

### Cache Busting with asp-append-version

The `asp-append-version` tag helper adds a unique version hash to script URLs, ensuring browsers fetch updated files after deployment.

```html
<!-- Without version -->
<script src="~/js/app.js"></script>
<!-- Renders: <script src="/js/app.js"></script> -->

<!-- With version -->
<script src="~/js/app.js" asp-append-version="true"></script>
<!-- Renders: <script src="/js/app.js?v=abc123def456"></script> -->
```

### Bundling and Minification

ASP.NET Core supports bundling and minification through BuildBundlerMinifier or newer tools like LibMan and npm-based solutions.

```xml
<!-- bundleconfig.json -->
[
  {
    "outputFileName": "wwwroot/js/bundle.min.js",
    "inputFiles": [
      "wwwroot/js/site.js",
      "wwwroot/js/utils.js",
      "wwwroot/js/validation.js"
    ],
    "minify": {
      "enabled": true,
      "renameLocals": true
    },
    "sourceMap": false
  },
  {
    "outputFileName": "wwwroot/css/bundle.min.css",
    "inputFiles": [
      "wwwroot/css/site.css",
      "wwwroot/css/forms.css"
    ]
  }
]
```

### JavaScript in Razor Pages

Razor Pages provide specific patterns for JavaScript integration with partial views and page handlers.

```csharp
// Pages/Products/Index.cshtml.cs
public class IndexModel : PageModel
{
    public List<Product> Products { get; set; }
    
    public async Task OnGetAsync()
    {
        Products = await _productService.GetAllAsync();
    }
    
    public async Task<JsonResult> OnGetProductAsync(int id)
    {
        var product = await _productService.GetByIdAsync(id);
        return new JsonResult(product);
    }
}

<!-- Pages/Products/Index.cshtml -->
@page
@model IndexModel

<table id="productsTable" class="table">
    <thead>
        <tr>
            <th>Name</th>
            <th>Price</th>
            <th>Actions</th>
        </tr>
    </thead>
    <tbody>
        @foreach (var product in Model.Products)
        {
            <tr data-product-id="@product.Id">
                <td>@product.Name</td>
                <td>@product.Price.ToString("C")</td>
                <td>
                    <button class="btn btn-sm btn-primary view-product">View</button>
                </td>
            </tr>
        }
    </tbody>
</table>

@section Scripts {
    <script>
        // Access page handler URLs
        const getProductUrl = '@Url.Page("./Index", "Product")';
        
        document.querySelectorAll('.view-product').forEach(btn => {
            btn.addEventListener('click', async function() {
                const row = this.closest('tr');
                const productId = row.dataset.productId;
                
                const response = await fetch(`${getProductUrl}?id=${productId}`);
                const product = await response.json();
                
                // Display product details
                console.log(product);
            });
        });
    </script>
}
```

### Passing Server Data to JavaScript

Several patterns exist for passing server-side data to client-side JavaScript.

```html
@{
    var appConfig = new
    {
        apiUrl = Url.Content("~/api"),
        userId = User.FindFirst(ClaimTypes.NameIdentifier)?.Value,
        isAuthenticated = User.Identity?.IsAuthenticated ?? false,
        culture = CultureInfo.CurrentUICulture.Name
    };
}

<!-- Option 1: Global variable -->
<script>
    window.appConfig = @Html.Raw(Json.Serialize(appConfig));
    
    // Usage
    console.log(appConfig.apiUrl);
    console.log(appConfig.isAuthenticated);
</script>

<!-- Option 2: Data attributes -->
<body data-api-url="@appConfig.apiUrl"
      data-user-id="@appConfig.userId"
      data-culture="@appConfig.culture">
    
    <script>
        const body = document.body;
        const apiUrl = body.dataset.apiUrl;
        const userId = body.dataset.userId;
        const culture = body.dataset.culture;
    </script>
</body>

<!-- Option 3: Hidden input fields -->
<input type="hidden" id="apiUrl" value="@appConfig.apiUrl" />
<input type="hidden" id="antiForgeryToken" value="@Html.AntiForgeryToken()" />

<script>
    const apiUrl = document.getElementById('apiUrl').value;
</script>

<!-- Option 4: Config script module -->
<script id="app-config" type="application/json">
    @Html.Raw(Json.Serialize(appConfig))
</script>

<script>
    const configScript = document.getElementById('app-config');
    const config = JSON.parse(configScript.textContent);
</script>
```

---

## Chapter 3: JavaScript Fundamentals for C# Developers

### Variables and Scope

JavaScript variables differ significantly from C# in their scoping rules and behavior. Understanding these differences is crucial for C# developers transitioning to JavaScript.

```javascript
// var - function-scoped (avoid in modern code)
function oldWay() {
    if (true) {
        var x = 10;  // Function-scoped, hoisted
    }
    console.log(x);  // 10 - accessible outside block
}

// let - block-scoped (preferred for mutable values)
function modernWay() {
    if (true) {
        let y = 20;  // Block-scoped
    }
    // console.log(y);  // ReferenceError
}

// const - block-scoped, immutable reference (preferred for constants)
const API_URL = 'https://api.example.com';
const config = { timeout: 5000 };
config.timeout = 10000;  // OK - object properties can change
// config = {};  // Error - cannot reassign const

// Hoisting behavior
console.log(hoisted);  // undefined (not error)
var hoisted = 'value';

// Temporal Dead Zone (let/const)
// console.log(notHoisted);  // ReferenceError
let notHoisted = 'value';
```

### Types and Type Conversion

JavaScript has dynamic typing with primitive and reference types. Understanding type coercion is essential for avoiding common pitfalls.

```javascript
// Primitive types
let string = 'Hello';
let number = 42;
let boolean = true;
let nullValue = null;
let undefinedValue = undefined;
let symbol = Symbol('unique');
let bigInt = 9007199254740991n;

// Reference types
let object = { name: 'John', age: 30 };
let array = [1, 2, 3];
let func = function() { return 'Hello'; };

// Type checking (preferred methods)
typeof 'string' === 'string';       // true
typeof 42 === 'number';             // true
typeof true === 'boolean';          // true
typeof undefined === 'undefined';   // true
typeof null === 'object';           // true (historical bug)
typeof {} === 'object';             // true
typeof [] === 'object';             // true (array is object)

// Better type checking
Array.isArray([1, 2, 3]);           // true
Array.isArray({});                  // false

// Checking for null
value === null;

// Checking for objects (not null)
typeof value === 'object' && value !== null;

// Type conversion
String(123);          // '123'
Number('42');         // 42
Number('abc');        // NaN
parseInt('42px');     // 42
parseFloat('3.14');   // 3.14
Boolean(1);           // true
Boolean('');          // false

// Falsy values
Boolean(false);       // false
Boolean(0);           // false
Boolean(-0);          // false
Boolean(0n);          // false
Boolean('');          // false
Boolean(null);        // false
Boolean(undefined);   // false
Boolean(NaN);         // false

// Truthy values (everything else)
Boolean({});          // true
Boolean([]);          // true
Boolean('false');     // true
Boolean(42);          // true
```

### Functions and Arrow Functions

JavaScript functions are first-class objects, enabling functional programming patterns familiar to C# developers but with important differences in syntax and behavior.

```javascript
// Function declaration (hoisted)
function greet(name) {
    return `Hello, ${name}!`;
}

// Function expression
const greet = function(name) {
    return `Hello, ${name}!`;
};

// Arrow function (ES6+)
const greet = (name) => `Hello, ${name}!`;

// Arrow functions and 'this' binding
const obj = {
    name: 'John',
    
    // Regular function - 'this' refers to obj
    regularGreet: function() {
        console.log(`Hello, ${this.name}`);
    },
    
    // Arrow function - 'this' is lexically bound (outer scope)
    arrowGreet: () => {
        console.log(`Hello, ${this.name}`);  // undefined
    },
    
    // Correct pattern for methods using callbacks
    delayedGreet: function() {
        setTimeout(() => {
            console.log(`Hello, ${this.name}`);  // 'John' - arrow preserves 'this'
        }, 1000);
    }
};

// Default parameters
function greet(name = 'World') {
    return `Hello, ${name}!`;
}

// Rest parameters
function sum(...numbers) {
    return numbers.reduce((total, n) => total + n, 0);
}
sum(1, 2, 3, 4, 5);  // 15

// Destructuring in parameters
function displayUser({ name, age, city = 'Unknown' }) {
    console.log(`${name}, ${age}, ${city}`);
}
displayUser({ name: 'John', age: 30 });

// Higher-order functions
const numbers = [1, 2, 3, 4, 5];

// map, filter, reduce (similar to LINQ)
const doubled = numbers.map(n => n * 2);        // [2, 4, 6, 8, 10]
const evens = numbers.filter(n => n % 2 === 0); // [2, 4]
const total = numbers.reduce((sum, n) => sum + n, 0); // 15

// find (similar to FirstOrDefault)
const first = numbers.find(n => n > 3);  // 4

// some (similar to Any)
const hasEven = numbers.some(n => n % 2 === 0);  // true

// every (similar to All)
const allPositive = numbers.every(n => n > 0);  // true
```

### Objects and Classes

JavaScript's object model differs from C#'s class-based inheritance, though ES6+ classes provide familiar syntax while maintaining prototype-based inheritance.

```javascript
// Object literal
const person = {
    name: 'John',
    age: 30,
    
    greet() {
        console.log(`Hello, I'm ${this.name}`);
    },
    
    // Computed property
    ['full' + 'Name']: 'John Doe'
};

// Object methods
Object.keys(person);      // ['name', 'age', 'greet', 'fullName']
Object.values(person);    // ['John', 30, function, 'John Doe']
Object.entries(person);   // [['name', 'John'], ...]

// Spread operator (similar to C# collection expressions)
const extended = { ...person, city: 'New York' };

// Object.assign
const merged = Object.assign({}, person, { country: 'USA' });

// Classes (ES6+)
class Person {
    // Private fields (ES2022)
    #ssn;
    
    // Static property
    static species = 'Homo sapiens';
    
    constructor(name, age) {
        this.name = name;
        this.age = age;
        this.#ssn = '000-00-0000';
    }
    
    // Getter
    get birthYear() {
        return new Date().getFullYear() - this.age;
    }
    
    // Method
    greet() {
        console.log(`Hello, I'm ${this.name}`);
    }
    
    // Private method
    #validate() {
        return this.name && this.age > 0;
    }
    
    // Static method
    static create(name, age) {
        return new Person(name, age);
    }
}

// Inheritance
class Employee extends Person {
    constructor(name, age, position, salary) {
        super(name, age);
        this.position = position;
        this.salary = salary;
    }
    
    greet() {
        super.greet();
        console.log(`I work as a ${this.position}`);
    }
}

const employee = new Employee('John', 30, 'Developer', 75000);
employee.greet();
```

### Asynchronous Programming

JavaScript's asynchronous model differs significantly from C#'s async/await, though the syntax is similar.

```javascript
// Callbacks (traditional approach)
function fetchData(callback) {
    setTimeout(() => {
        callback(null, { data: 'result' });
    }, 1000);
}

fetchData((error, data) => {
    if (error) {
        console.error(error);
        return;
    }
    console.log(data);
});

// Promises
function fetchData() {
    return new Promise((resolve, reject) => {
        setTimeout(() => {
            const success = true;
            if (success) {
                resolve({ data: 'result' });
            } else {
                reject(new Error('Failed to fetch'));
            }
        }, 1000);
    });
}

// Promise consumption
fetchData()
    .then(data => {
        console.log(data);
        return processData(data);  // Chaining
    })
    .then(processed => {
        console.log(processed);
    })
    .catch(error => {
        console.error(error);
    })
    .finally(() => {
        console.log('Cleanup');
    });

// Async/await (similar to C#)
async function loadData() {
    try {
        const response = await fetch('/api/data');
        if (!response.ok) {
            throw new Error(`HTTP error! status: ${response.status}`);
        }
        const data = await response.json();
        return data;
    } catch (error) {
        console.error('Failed to load data:', error);
        throw error;
    }
}

// Parallel execution
async function loadAllData() {
    // Promise.all (similar to Task.WhenAll)
    const [users, products, orders] = await Promise.all([
        fetch('/api/users').then(r => r.json()),
        fetch('/api/products').then(r => r.json()),
        fetch('/api/orders').then(r => r.json())
    ]);
    
    return { users, products, orders };
}

// Promise.race (similar to Task.WhenAny)
const result = await Promise.race([
    fetch('/api/fast'),
    fetch('/api/slow')
]);

// Promise.allSettled (wait for all, regardless of outcome)
const results = await Promise.allSettled([
    fetch('/api/endpoint1'),
    fetch('/api/endpoint2'),
    fetch('/api/endpoint3')
]);

results.forEach(result => {
    if (result.status === 'fulfilled') {
        console.log('Success:', result.value);
    } else {
        console.error('Failed:', result.reason);
    }
});
```

### Modules

Modern JavaScript uses ES modules for code organization, similar to C# namespaces and using statements.

```javascript
// math.js - Export module
export const PI = 3.14159;

export function add(a, b) {
    return a + b;
}

export function subtract(a, b) {
    return a - b;
}

export default class Calculator {
    add(a, b) { return a + b; }
    subtract(a, b) { return a - b; }
}

// main.js - Import module
import Calculator, { PI, add, subtract } from './math.js';
import * as MathModule from './math.js';
import { add as sum } from './math.js';  // Rename import

// Dynamic import (loaded on demand)
async function loadModule() {
    const module = await import('./heavy-module.js');
    module.doSomething();
}

// In ASP.NET Core views
<script type="module">
    import { initPage } from '/js/pages/index.js';
    initPage();
</script>
```

---

## Chapter 4: DOM Manipulation and Events

### Selecting Elements

The Document Object Model (DOM) provides the interface between JavaScript and HTML. Modern JavaScript offers powerful selection methods that replace jQuery-style selectors for most use cases.

```javascript
// Selecting single elements
const element = document.getElementById('myId');
const element = document.querySelector('#myId');
const element = document.querySelector('.myClass');
const element = document.querySelector('[data-id="123"]');
const element = document.querySelector('input[type="text"]');

// Selecting multiple elements
const elements = document.getElementsByClassName('myClass');  // HTMLCollection
const elements = document.getElementsByTagName('div');        // HTMLCollection
const elements = document.querySelectorAll('.myClass');       // NodeList

// Iterating over NodeList
document.querySelectorAll('.item').forEach(item => {
    console.log(item.textContent);
});

// Converting to array for array methods
const items = Array.from(document.querySelectorAll('.item'));
const texts = items.map(item => item.textContent);

// Traversing the DOM
const parent = element.parentElement;
const children = element.children;
const nextSibling = element.nextElementSibling;
const prevSibling = element.previousElementSibling;
const closest = element.closest('.container');
const matches = element.matches('.active');  // Boolean
```

### Modifying Elements

```javascript
// Content manipulation
element.textContent = 'Plain text';  // Safe, escapes HTML
element.innerHTML = '<strong>Bold</strong>';  // Parses HTML
element.innerText = 'Visible text';  // Respects CSS visibility

// Attributes
element.getAttribute('href');
element.setAttribute('href', 'https://example.com');
element.removeAttribute('disabled');
element.hasAttribute('disabled');
element.dataset.productId;  // data-product-id attribute

// Classes
element.classList.add('active');
element.classList.remove('active');
element.classList.toggle('active');
element.classList.toggle('active', condition);  // Toggle based on condition
element.classList.contains('active');
element.classList.replace('old-class', 'new-class');

// Styles
element.style.color = 'red';
element.style.backgroundColor = 'blue';  // camelCase for CSS properties
element.style.cssText = 'color: red; background: blue;';
element.style.removeProperty('color');

// Creating elements
const div = document.createElement('div');
div.className = 'card';
div.innerHTML = '<h2>Title</h2><p>Content</p>';
document.body.appendChild(div);

// Inserting elements
parent.appendChild(child);
parent.insertBefore(newElement, referenceElement);
element.insertAdjacentHTML('beforebegin', '<div>Before</div>');
element.insertAdjacentHTML('afterbegin', '<div>First child</div>');
element.insertAdjacentHTML('beforeend', '<div>Last child</div>');
element.insertAdjacentHTML('afterend', '<div>After</div>');

// Modern insertion methods
parent.append(child1, child2);  // At end
parent.prepend(child1, child2);  // At beginning
element.before(sibling);
element.after(sibling);
element.replaceWith(newElement);

// Removing elements
element.remove();
parent.removeChild(child);
element.replaceWith(newElement);

// Cloning
const clone = element.cloneNode(true);  // Deep clone
```

### Event Handling

```javascript
// Adding event listeners
element.addEventListener('click', function(event) {
    console.log('Clicked!', event.target);
});

// Arrow function
element.addEventListener('click', (event) => {
    console.log('Clicked!', event.target);
});

// Named function (allows removal)
function handleClick(event) {
    console.log('Clicked!');
}
element.addEventListener('click', handleClick);
element.removeEventListener('click', handleClick);

// One-time event
element.addEventListener('click', handler, { once: true });

// Event options
element.addEventListener('click', handler, {
    capture: false,  // Use capturing phase
    once: true,      // Remove after first invocation
    passive: true    // Won't call preventDefault()
});

// Common events
// Mouse events
element.addEventListener('click', handler);
element.addEventListener('dblclick', handler);
element.addEventListener('mouseenter', handler);
element.addEventListener('mouseleave', handler);
element.addEventListener('mousemove', handler);

// Keyboard events
document.addEventListener('keydown', (event) => {
    console.log(event.key);      // 'Enter', 'a', 'Escape'
    console.log(event.code);     // 'Enter', 'KeyA', 'Escape'
    console.log(event.ctrlKey);  // Boolean
    console.log(event.shiftKey); // Boolean
});

// Form events
form.addEventListener('submit', (event) => {
    event.preventDefault();  // Prevent form submission
    const formData = new FormData(form);
    console.log(formData.get('fieldName'));
});

input.addEventListener('input', (event) => {
    console.log(event.target.value);
});

input.addEventListener('change', (event) => {
    console.log(event.target.value);
});

// Focus events
input.addEventListener('focus', handler);
input.addEventListener('blur', handler);

// Event delegation (efficient for dynamic content)
document.querySelector('.list').addEventListener('click', (event) => {
    if (event.target.matches('.delete-btn')) {
        const item = event.target.closest('.list-item');
        item.remove();
    }
});

// Custom events
const customEvent = new CustomEvent('userLoggedIn', {
    detail: { userId: 123, name: 'John' },
    bubbles: true,
    cancelable: true
});
element.dispatchEvent(customEvent);

element.addEventListener('userLoggedIn', (event) => {
    console.log('User logged in:', event.detail);
});
```

### Form Handling

```javascript
// Getting form values
const form = document.querySelector('#myForm');
const formData = new FormData(form);

// Access values
formData.get('username');           // Single value
formData.getAll('interests');       // Multiple values (checkboxes)
formData.has('email');              // Check existence

// Iterate entries
for (const [name, value] of formData) {
    console.log(name, value);
}

// Convert to object
const data = Object.fromEntries(formData);

// Form validation
form.addEventListener('submit', async (event) => {
    event.preventDefault();
    
    // Client-side validation
    if (!form.checkValidity()) {
        form.reportValidity();
        return;
    }
    
    // Submit to server
    const response = await fetch('/api/submit', {
        method: 'POST',
        body: formData
    });
    
    if (response.ok) {
        alert('Form submitted successfully!');
    }
});

// Real-time validation
const emailInput = document.querySelector('#email');
emailInput.addEventListener('input', (event) => {
    const input = event.target;
    const isValid = input.validity.valid;
    
    if (!isValid) {
        input.classList.add('is-invalid');
        input.nextElementSibling.textContent = input.validationMessage;
    } else {
        input.classList.remove('is-invalid');
    }
});

// File upload handling
const fileInput = document.querySelector('#fileInput');
fileInput.addEventListener('change', (event) => {
    const files = Array.from(event.target.files);
    
    files.forEach(file => {
        console.log(`File: ${file.name}, Size: ${file.size}, Type: ${file.type}`);
        
        // Preview images
        if (file.type.startsWith('image/')) {
            const reader = new FileReader();
            reader.onload = (e) => {
                const img = document.createElement('img');
                img.src = e.target.result;
                document.body.appendChild(img);
            };
            reader.readAsDataURL(file);
        }
    });
});
```

---

## Chapter 5: AJAX and Fetch API

### The Fetch API

Modern JavaScript uses the Fetch API for HTTP requests, replacing the older XMLHttpRequest. The Fetch API provides a cleaner, Promise-based interface for making HTTP requests.

```javascript
// Basic GET request
async function getData() {
    try {
        const response = await fetch('/api/products');
        
        if (!response.ok) {
            throw new Error(`HTTP error! status: ${response.status}`);
        }
        
        const data = await response.json();
        return data;
    } catch (error) {
        console.error('Fetch error:', error);
        throw error;
    }
}

// GET with query parameters
async function searchProducts(query, page = 1, pageSize = 10) {
    const params = new URLSearchParams({
        q: query,
        page: page.toString(),
        pageSize: pageSize.toString()
    });
    
    const response = await fetch(`/api/products/search?${params}`);
    return response.json();
}

// POST with JSON
async function createProduct(product) {
    const response = await fetch('/api/products', {
        method: 'POST',
        headers: {
            'Content-Type': 'application/json',
            'RequestVerificationToken': getAntiForgeryToken()
        },
        body: JSON.stringify(product)
    });
    
    if (!response.ok) {
        const error = await response.json();
        throw new Error(error.message || 'Failed to create product');
    }
    
    return response.json();
}

// PUT request
async function updateProduct(id, product) {
    const response = await fetch(`/api/products/${id}`, {
        method: 'PUT',
        headers: {
            'Content-Type': 'application/json'
        },
        body: JSON.stringify(product)
    });
    
    if (!response.ok) {
        throw new Error(`Failed to update product: ${response.status}`);
    }
    
    // Handle 204 No Content
    if (response.status === 204) {
        return null;
    }
    
    return response.json();
}

// DELETE request
async function deleteProduct(id) {
    const response = await fetch(`/api/products/${id}`, {
        method: 'DELETE'
    });
    
    return response.ok;
}
```

### Anti-Forgery Token Integration

ASP.NET Core requires anti-forgery tokens for POST requests. Here's how to integrate them with JavaScript.

```html
<!-- In _Layout.cshtml or view -->
@inject Microsoft.AspNetCore.Antiforgery.IAntiforgery Antiforgery
@{
    var token = Antiforgery.GetAndStoreTokens(Context);
}
<meta name="csrf-token" content="@token.RequestToken" />

<!-- Or using a hidden field -->
@Html.AntiForgeryToken()
```

```javascript
// Get anti-forgery token from meta tag
function getAntiForgeryToken() {
    const token = document.querySelector('meta[name="csrf-token"]')?.content;
    return token || document.querySelector('input[name="__RequestVerificationToken"]')?.value;
}

// Or from hidden input
function getAntiForgeryToken() {
    return document.querySelector('input[name="__RequestVerificationToken"]')?.value;
}

// Create headers with anti-forgery token
function createHeaders(includeContentType = true) {
    const headers = {
        'RequestVerificationToken': getAntiForgeryToken()
    };
    
    if (includeContentType) {
        headers['Content-Type'] = 'application/json';
    }
    
    return headers;
}

// Usage in fetch
async function postData(url, data) {
    const response = await fetch(url, {
        method: 'POST',
        headers: createHeaders(),
        body: JSON.stringify(data)
    });
    
    return response.json();
}
```

### Working with FormData

FormData provides a convenient way to send multipart/form-data, including file uploads.

```javascript
// Submit form with FormData
async function submitForm(form) {
    const formData = new FormData(form);
    
    const response = await fetch('/api/upload', {
        method: 'POST',
        body: formData  // Don't set Content-Type header; browser sets it with boundary
    });
    
    return response.json();
}

// File upload with progress
function uploadFileWithProgress(file, onProgress) {
    return new Promise((resolve, reject) => {
        const xhr = new XMLHttpRequest();
        
        xhr.upload.addEventListener('progress', (event) => {
            if (event.lengthComputable) {
                const percent = (event.loaded / event.total) * 100;
                onProgress(percent);
            }
        });
        
        xhr.addEventListener('load', () => {
            if (xhr.status >= 200 && xhr.status < 300) {
                resolve(JSON.parse(xhr.response));
            } else {
                reject(new Error(`Upload failed: ${xhr.status}`));
            }
        });
        
        xhr.addEventListener('error', () => reject(new Error('Upload error')));
        
        const formData = new FormData();
        formData.append('file', file);
        formData.append('__RequestVerificationToken', getAntiForgeryToken());
        
        xhr.open('POST', '/api/upload');
        xhr.send(formData);
    });
}

// Usage
const fileInput = document.querySelector('#fileInput');
const result = await uploadFileWithProgress(fileInput.files[0], (percent) => {
    console.log(`Upload progress: ${percent.toFixed(1)}%`);
    document.querySelector('.progress-bar').style.width = `${percent}%`;
});
```

### Error Handling

```javascript
// Comprehensive error handling
class ApiError extends Error {
    constructor(status, message, errors = null) {
        super(message);
        this.status = status;
        this.errors = errors;
        this.name = 'ApiError';
    }
}

async function apiRequest(url, options = {}) {
    const defaults = {
        headers: {
            'Content-Type': 'application/json',
            'RequestVerificationToken': getAntiForgeryToken()
        }
    };
    
    // Merge options
    const config = {
        ...defaults,
        ...options,
        headers: {
            ...defaults.headers,
            ...options.headers
        }
    };
    
    try {
        const response = await fetch(url, config);
        
        // Handle different response types
        const contentType = response.headers.get('content-type');
        
        if (!response.ok) {
            if (contentType?.includes('application/json')) {
                const error = await response.json();
                throw new ApiError(
                    response.status,
                    error.message || error.title || 'An error occurred',
                    error.errors
                );
            }
            
            throw new ApiError(
                response.status,
                response.statusText || 'An error occurred'
            );
        }
        
        // Handle empty response
        if (response.status === 204) {
            return null;
        }
        
        // Parse JSON response
        if (contentType?.includes('application/json')) {
            return response.json();
        }
        
        return response.text();
        
    } catch (error) {
        if (error instanceof ApiError) {
            throw error;
        }
        
        // Network errors
        if (error.name === 'TypeError') {
            throw new ApiError(0, 'Network error - please check your connection');
        }
        
        throw new ApiError(0, error.message);
    }
}

// Usage with error handling
try {
    const product = await apiRequest('/api/products/1');
    console.log(product);
} catch (error) {
    if (error instanceof ApiError) {
        if (error.status === 404) {
            showError('Product not found');
        } else if (error.status === 400 && error.errors) {
            // Validation errors
            displayValidationErrors(error.errors);
        } else {
            showError(error.message);
        }
    }
}
```

### Caching and Request Cancellation

```javascript
// Request with timeout
async function fetchWithTimeout(url, options = {}, timeout = 10000) {
    const controller = new AbortController();
    const timeoutId = setTimeout(() => controller.abort(), timeout);
    
    try {
        const response = await fetch(url, {
            ...options,
            signal: controller.signal
        });
        return response;
    } finally {
        clearTimeout(timeoutId);
    }
}

// Cancelable request
let currentController = null;

async function searchWithCancellation(query) {
    // Cancel previous request
    if (currentController) {
        currentController.abort();
    }
    
    currentController = new AbortController();
    
    try {
        const response = await fetch(`/api/search?q=${encodeURIComponent(query)}`, {
            signal: currentController.signal
        });
        return response.json();
    } catch (error) {
        if (error.name === 'AbortError') {
            console.log('Request cancelled');
            return null;
        }
        throw error;
    } finally {
        currentController = null;
    }
}

// Request deduplication
const pendingRequests = new Map();

async function fetchUnique(url) {
    if (pendingRequests.has(url)) {
        return pendingRequests.get(url);
    }
    
    const promise = fetch(url).then(response => {
        pendingRequests.delete(url);
        return response.json();
    });
    
    pendingRequests.set(url, promise);
    return promise;
}
```

---

## Chapter 6: jQuery in ASP.NET Core

### Setting Up jQuery

jQuery is included by default in ASP.NET Core templates and provides cross-browser compatibility and convenient DOM manipulation methods.

```html
<!-- Include jQuery via LibMan or CDN -->
<environment include="Development">
    <script src="~/lib/jquery/dist/jquery.js"></script>
</environment>
<environment exclude="Development">
    <script src="~/lib/jquery/dist/jquery.min.js"></script>
</environment>

<!-- Or CDN -->
<script src="https://cdn.jsdelivr.net/npm/jquery@3.7.1/dist/jquery.min.js"
        integrity="sha256-/JqT3SQfawRcv/BIHPThkBvs0OEvtFFmqPF/lYI/Cxo="
        crossorigin="anonymous"></script>
```

### jQuery AJAX with ASP.NET Core

```javascript
// Basic AJAX GET
$.get('/api/products', function(data) {
    console.log(data);
});

// AJAX with configuration
$.ajax({
    url: '/api/products',
    type: 'GET',
    dataType: 'json',
    success: function(data) {
        console.log(data);
    },
    error: function(xhr, status, error) {
        console.error('Error:', error);
    }
});

// POST with anti-forgery token
$.ajax({
    url: '/api/products',
    type: 'POST',
    contentType: 'application/json',
    data: JSON.stringify({ name: 'Product', price: 99.99 }),
    headers: {
        'RequestVerificationToken': $('input[name="__RequestVerificationToken"]').val()
    },
    success: function(response) {
        console.log('Created:', response);
    },
    error: function(xhr) {
        if (xhr.status === 400) {
            const errors = xhr.responseJSON?.errors;
            displayErrors(errors);
        }
    }
});

// Convenience methods
$.getJSON('/api/products', function(data) {
    // Automatically parses JSON response
});

$.post('/api/products', { name: 'Product', price: 99.99 })
    .done(function(response) {
        console.log('Success:', response);
    })
    .fail(function(xhr) {
        console.error('Error:', xhr.responseText);
    });

// Using Promise syntax
try {
    const response = await $.ajax({
        url: '/api/products/1',
        type: 'GET'
    });
    console.log(response);
} catch (error) {
    console.error('Error:', error);
}
```

### jQuery with ASP.NET Core MVC Actions

```javascript
// Call controller action
function loadProduct(id) {
    $.get(`/Products/Details/${id}`, function(html) {
        $('#productDetails').html(html);
    });
}

// Post form data to action
$('#productForm').on('submit', function(e) {
    e.preventDefault();
    
    const formData = $(this).serialize();
    
    $.post('/Products/Create', formData)
        .done(function(response) {
            if (response.success) {
                window.location.href = '/Products';
            } else {
                showErrors(response.errors);
            }
        });
});

// Partial view loading
function loadPartial(url, target) {
    $(target).load(url, function(response, status, xhr) {
        if (status === 'error') {
            $(target).html('<div class="alert alert-danger">Failed to load content</div>');
        }
    });
}

// Usage
$('#detailsPanel').load('/Products/DetailsPartial/1');
```

### jQuery Validation Integration

```javascript
// Enable unobtrusive validation
$.validator.unobtrusive.parse('#myForm');

// Manual validation setup
$('#myForm').validate({
    rules: {
        email: {
            required: true,
            email: true,
            remote: {
                url: '/Account/CheckEmail',
                type: 'post',
                data: {
                    email: function() {
                        return $('#email').val();
                    }
                }
            }
        },
        password: {
            required: true,
            minlength: 8
        },
        confirmPassword: {
            required: true,
            equalTo: '#password'
        }
    },
    messages: {
        email: {
            required: 'Email is required',
            email: 'Please enter a valid email',
            remote: 'Email already registered'
        }
    },
    errorElement: 'span',
    errorClass: 'text-danger',
    highlight: function(element) {
        $(element).addClass('is-invalid').removeClass('is-valid');
    },
    unhighlight: function(element) {
        $(element).addClass('is-valid').removeClass('is-invalid');
    },
    submitHandler: function(form) {
        // AJAX submission
        $.ajax({
            url: form.action,
            type: form.method,
            data: $(form).serialize(),
            success: function(response) {
                // Handle success
            }
        });
    }
});

// Custom validation method
$.validator.addMethod('strongPassword', function(value, element) {
    return this.optional(element) ||
           /^(?=.*[a-z])(?=.*[A-Z])(?=.*\d)(?=.*[@$!%*?&])[A-Za-z\d@$!%*?&]{8,}$/.test(value);
}, 'Password must contain uppercase, lowercase, number, and special character');
```

---

## Chapter 7: Client-Side Validation

### Unobtrusive Validation in ASP.NET Core

ASP.NET Core includes jQuery Validation and jQuery Unobtrusive Validation for client-side validation based on server-side attributes.

```csharp
// Model with validation attributes
public class RegisterViewModel
{
    [Required(ErrorMessage = "Username is required")]
    [StringLength(50, MinimumLength = 3, ErrorMessage = "Username must be between 3 and 50 characters")]
    [Remote(action: "CheckUsername", controller: "Account", ErrorMessage = "Username is already taken")]
    public string Username { get; set; } = string.Empty;

    [Required(ErrorMessage = "Email is required")]
    [EmailAddress(ErrorMessage = "Invalid email format")]
    public string Email { get; set; } = string.Empty;

    [Required(ErrorMessage = "Password is required")]
    [StringLength(100, MinimumLength = 8, ErrorMessage = "Password must be at least 8 characters")]
    [DataType(DataType.Password)]
    public string Password { get; set; } = string.Empty;

    [DataType(DataType.Password)]
    [Compare("Password", ErrorMessage = "Passwords do not match")]
    public string ConfirmPassword { get; set; } = string.Empty;
}
```

```html
<!-- View with unobtrusive validation -->
@model RegisterViewModel

<form asp-action="Register" method="post" id="registerForm">
    <div class="form-group">
        <label asp-for="Username"></label>
        <input asp-for="Username" class="form-control" />
        <span asp-validation-for="Username" class="text-danger"></span>
    </div>
    
    <div class="form-group">
        <label asp-for="Email"></label>
        <input asp-for="Email" class="form-control" />
        <span asp-validation-for="Email" class="text-danger"></span>
    </div>
    
    <div class="form-group">
        <label asp-for="Password"></label>
        <input asp-for="Password" class="form-control" />
        <span asp-validation-for="Password" class="text-danger"></span>
    </div>
    
    <div class="form-group">
        <label asp-for="ConfirmPassword"></label>
        <input asp-for="ConfirmPassword" class="form-control" />
        <span asp-validation-for="ConfirmPassword" class="text-danger"></span>
    </div>
    
    <button type="submit" class="btn btn-primary">Register</button>
</form>

<!-- Include validation scripts -->
@section Scripts {
    <partial name="_ValidationScriptsPartial" />
}
```

### Native HTML5 Validation

```html
<form id="nativeValidationForm" novalidate>
    <div class="form-group">
        <label for="email">Email</label>
        <input type="email" 
               id="email" 
               name="email" 
               required
               pattern="[a-z0-9._%+-]+@[a-z0-9.-]+\.[a-z]{2,}$"
               class="form-control" />
        <div class="invalid-feedback">Please enter a valid email address</div>
    </div>
    
    <div class="form-group">
        <label for="age">Age</label>
        <input type="number" 
               id="age" 
               name="age" 
               min="18" 
               max="120"
               required
               class="form-control" />
        <div class="invalid-feedback">Age must be between 18 and 120</div>
    </div>
    
    <div class="form-group">
        <label for="phone">Phone</label>
        <input type="tel" 
               id="phone" 
               name="phone"
               pattern="[0-9]{3}-[0-9]{3}-[0-9]{4}"
               placeholder="123-456-7890"
               class="form-control" />
        <div class="invalid-feedback">Format: 123-456-7890</div>
    </div>
    
    <button type="submit" class="btn btn-primary">Submit</button>
</form>

<script>
    // Bootstrap-style validation styling
    (function() {
        'use strict';
        
        const forms = document.querySelectorAll('.needs-validation');
        
        forms.forEach(form => {
            form.addEventListener('submit', event => {
                if (!form.checkValidity()) {
                    event.preventDefault();
                    event.stopPropagation();
                }
                
                form.classList.add('was-validated');
            }, false);
        });
    })();
    
    // Custom validation
    const emailInput = document.getElementById('email');
    
    emailInput.addEventListener('input', function() {
        if (this.validity.typeMismatch) {
            this.setCustomValidity('Please enter a valid email address');
        } else {
            this.setCustomValidity('');
        }
    });
</script>
```

### Custom Client-Side Validators

```javascript
// Custom validation adapter for jQuery Unobtrusive
$.validator.addMethod('mustbetrue', function(value, element, params) {
    return element.checked;
}, 'This field must be checked');

$.validator.unobtrusive.adapters.addBool('mustbetrue');

// Usage in C# model
public class TermsViewModel
{
    [MustBeTrue(ErrorMessage = "You must accept the terms")]
    public bool AcceptTerms { get; set; }
}

// Custom attribute
public class MustBeTrueAttribute : ValidationAttribute, IClientModelValidator
{
    public void AddValidation(ClientModelValidationContext context)
    {
        MergeAttribute(context.Attributes, "data-val", "true");
        MergeAttribute(context.Attributes, "data-val-mustbetrue", ErrorMessage);
    }
    
    private bool MergeAttribute(IDictionary<string, string> attributes, string key, string value)
    {
        if (attributes.ContainsKey(key))
        {
            return false;
        }
        
        attributes.Add(key, value);
        return true;
    }
    
    public override bool IsValid(object? value)
    {
        return value is bool b && b;
    }
}

// Custom validation with parameters
$.validator.addMethod('rangeif', function(value, element, params) {
    const otherValue = $(params.dependentproperty).val();
    if (params.dependentvalue === otherValue) {
        return value >= params.min && value <= params.max;
    }
    return true;
});

$.validator.unobtrusive.adapters.add('rangeif', 
    ['dependentproperty', 'dependentvalue', 'min', 'max'],
    function(options) {
        options.rules['rangeif'] = {
            dependentproperty: '#' + options.params.dependentproperty,
            dependentvalue: options.params.dependentvalue,
            min: parseFloat(options.params.min),
            max: parseFloat(options.params.max)
        };
        options.messages['rangeif'] = options.message;
    }
);
```

---

## Chapter 8: JavaScript Modules and Bundling

### ES Modules in ASP.NET Core

```html
<!-- Using ES modules in views -->
<script type="module">
    import { initPage } from '/js/pages/products/index.js';
    initPage();
</script>

<!-- Import maps for cleaner imports -->
<script type="importmap">
{
    "imports": {
        "app/": "/js/",
        "lib/": "/lib/"
    }
}
</script>

<script type="module">
    import { api } from 'app/services/api.js';
    import { utils } from 'app/utils/index.js';
</script>
```

### Module Organization

```javascript
// wwwroot/js/services/api.js
const BASE_URL = '/api';

export async function get(endpoint) {
    const response = await fetch(`${BASE_URL}${endpoint}`);
    if (!response.ok) {
        throw new Error(`API error: ${response.status}`);
    }
    return response.json();
}

export async function post(endpoint, data) {
    const response = await fetch(`${BASE_URL}${endpoint}`, {
        method: 'POST',
        headers: {
            'Content-Type': 'application/json',
            'RequestVerificationToken': getAntiForgeryToken()
        },
        body: JSON.stringify(data)
    });
    return response.json();
}

export async function del(endpoint) {
    const response = await fetch(`${BASE_URL}${endpoint}`, {
        method: 'DELETE',
        headers: {
            'RequestVerificationToken': getAntiForgeryToken()
        }
    });
    return response.ok;
}

// wwwroot/js/components/modal.js
export class Modal {
    constructor(element) {
        this.element = element;
        this.setupEventListeners();
    }
    
    setupEventListeners() {
        this.element.addEventListener('click', (e) => {
            if (e.target.matches('.modal-close')) {
                this.close();
            }
        });
    }
    
    open() {
        this.element.classList.add('show');
        document.body.style.overflow = 'hidden';
    }
    
    close() {
        this.element.classList.remove('show');
        document.body.style.overflow = '';
    }
}

// wwwroot/js/pages/products/index.js
import { get } from '/js/services/api.js';
import { Modal } from '/js/components/modal.js';

export function initPage() {
    loadProducts();
    setupEventHandlers();
}

async function loadProducts() {
    const products = await get('/products');
    renderProducts(products);
}

function renderProducts(products) {
    const container = document.getElementById('productsList');
    container.innerHTML = products.map(p => `
        <div class="product-card" data-id="${p.id}">
            <h3>${p.name}</h3>
            <p>${p.price}</p>
            <button class="btn btn-view">View Details</button>
        </div>
    `).join('');
}

function setupEventHandlers() {
    document.getElementById('productsList').addEventListener('click', (e) => {
        if (e.target.matches('.btn-view')) {
            const card = e.target.closest('.product-card');
            showProductDetails(card.dataset.id);
        }
    });
}

async function showProductDetails(id) {
    const product = await get(`/products/${id}`);
    const modal = new Modal(document.getElementById('productModal'));
    // Populate modal with product details
    modal.open();
}
```

### Build-Time Bundling with ES Build

```javascript
// esbuild.config.js
const esbuild = require('esbuild');

async function build() {
    await esbuild.build({
        entryPoints: {
            'bundle': './wwwroot/js/app.js',
            'pages/products': './wwwroot/js/pages/products/index.js',
            'pages/orders': './wwwroot/js/pages/orders/index.js'
        },
        bundle: true,
        outdir: './wwwroot/dist',
        minify: true,
        sourcemap: true,
        splitting: true,
        format: 'esm',
        external: ['jquery'],
        loader: {
            '.html': 'text',
            '.css': 'css'
        }
    });
}

build();
```

---

## Chapter 9: TypeScript Integration

### Setting Up TypeScript in ASP.NET Core

```bash
# Install TypeScript
npm install --save-dev typescript @types/jquery

# Create tsconfig.json
npx tsc --init
```

```json
// tsconfig.json
{
  "compilerOptions": {
    "target": "ES2020",
    "module": "ES2020",
    "lib": ["ES2020", "DOM", "DOM.Iterable"],
    "outDir": "./wwwroot/js/dist",
    "rootDir": "./wwwroot/js/src",
    "strict": true,
    "esModuleInterop": true,
    "skipLibCheck": true,
    "forceConsistentCasingInFileNames": true,
    "moduleResolution": "node",
    "declaration": true,
    "declarationMap": true,
    "sourceMap": true,
    "noImplicitAny": true,
    "noImplicitReturns": true,
    "noUnusedLocals": true,
    "noUnusedParameters": true
  },
  "include": ["wwwroot/js/src/**/*"],
  "exclude": ["node_modules", "wwwroot/js/dist"]
}
```

### TypeScript for ASP.NET Core

```typescript
// wwwroot/js/src/services/api.ts
interface ApiResponse<T> {
    success: boolean;
    data?: T;
    errors?: Record<string, string[]>;
}

interface Product {
    id: number;
    name: string;
    price: number;
    category: string;
}

interface ProductCreateRequest {
    name: string;
    price: number;
    categoryId: number;
}

class ApiService {
    private baseUrl: string;
    private antiforgeryToken: string | null;

    constructor(baseUrl: string = '/api') {
        this.baseUrl = baseUrl;
        this.antiforgeryToken = this.getAntiForgeryToken();
    }

    private getAntiForgeryToken(): string | null {
        const meta = document.querySelector('meta[name="csrf-token"]');
        return meta?.getAttribute('content');
    }

    private async request<T>(
        endpoint: string,
        options: RequestInit = {}
    ): Promise<T> {
        const headers: HeadersInit = {
            'Content-Type': 'application/json',
            ...options.headers,
        };

        if (this.antiforgeryToken) {
            headers['RequestVerificationToken'] = this.antiforgeryToken;
        }

        const response = await fetch(`${this.baseUrl}${endpoint}`, {
            ...options,
            headers,
        });

        if (!response.ok) {
            const error = await response.json();
            throw new ApiError(response.status, error.message, error.errors);
        }

        if (response.status === 204) {
            return null as T;
        }

        return response.json();
    }

    async get<T>(endpoint: string): Promise<T> {
        return this.request<T>(endpoint);
    }

    async post<T, R>(endpoint: string, data: T): Promise<R> {
        return this.request<R>(endpoint, {
            method: 'POST',
            body: JSON.stringify(data),
        });
    }

    async put<T, R>(endpoint: string, data: T): Promise<R> {
        return this.request<R>(endpoint, {
            method: 'PUT',
            body: JSON.stringify(data),
        });
    }

    async delete(endpoint: string): Promise<void> {
        return this.request<void>(endpoint, { method: 'DELETE' });
    }
}

class ApiError extends Error {
    constructor(
        public status: number,
        message: string,
        public errors?: Record<string, string[]>
    ) {
        super(message);
        this.name = 'ApiError';
    }
}

export { ApiService, ApiError, Product, ProductCreateRequest, ApiResponse };
```

### TypeScript with ASP.NET Core Views

```typescript
// wwwroot/js/src/pages/products/index.ts
import { ApiService, Product, ApiError } from '../../services/api';

class ProductsPage {
    private api: ApiService;
    private products: Product[] = [];

    constructor() {
        this.api = new ApiService();
        this.initialize();
    }

    private async initialize(): Promise<void> {
        await this.loadProducts();
        this.setupEventListeners();
    }

    private async loadProducts(): Promise<void> {
        try {
            this.products = await this.api.get<Product[]>('/products');
            this.renderProducts();
        } catch (error) {
            this.showError('Failed to load products');
            console.error(error);
        }
    }

    private renderProducts(): void {
        const container = document.getElementById('products-list');
        if (!container) return;

        container.innerHTML = this.products
            .map(
                (product) => `
            <div class="product-card" data-id="${product.id}">
                <h3>${this.escapeHtml(product.name)}</h3>
                <p class="price">$${product.price.toFixed(2)}</p>
                <button class="btn btn-primary btn-view">View Details</button>
                <button class="btn btn-danger btn-delete">Delete</button>
            </div>
        `
            )
            .join('');
    }

    private setupEventListeners(): void {
        const container = document.getElementById('products-list');
        if (!container) return;

        container.addEventListener('click', async (e) => {
            const target = e.target as HTMLElement;
            const card = target.closest('.product-card') as HTMLElement;
            const productId = card?.dataset.id;

            if (!productId) return;

            if (target.matches('.btn-view')) {
                await this.showProductDetails(parseInt(productId));
            } else if (target.matches('.btn-delete')) {
                await this.deleteProduct(parseInt(productId));
            }
        });
    }

    private async showProductDetails(id: number): Promise<void> {
        try {
            const product = await this.api.get<Product>(`/products/${id}`);
            // Show modal with product details
            console.log('Product details:', product);
        } catch (error) {
            this.showError('Failed to load product details');
        }
    }

    private async deleteProduct(id: number): Promise<void> {
        if (!confirm('Are you sure you want to delete this product?')) {
            return;
        }

        try {
            await this.api.delete(`/products/${id}`);
            this.products = this.products.filter((p) => p.id !== id);
            this.renderProducts();
        } catch (error) {
            this.showError('Failed to delete product');
        }
    }

    private showError(message: string): void {
        const alert = document.createElement('div');
        alert.className = 'alert alert-danger alert-dismissible fade show';
        alert.innerHTML = `
            ${message}
            <button type="button" class="btn-close" data-bs-dismiss="alert"></button>
        `;
        document.querySelector('.container')?.prepend(alert);
    }

    private escapeHtml(text: string): string {
        const div = document.createElement('div');
        div.textContent = text;
        return div.innerHTML;
    }
}

// Initialize when DOM is ready
document.addEventListener('DOMContentLoaded', () => {
    new ProductsPage();
});
```

---

## Chapter 10: SignalR JavaScript Client

### Setting Up SignalR Client

```html
<!-- Include SignalR client library -->
<script src="~/lib/microsoft/signalr/dist/browser/signalr.min.js"></script>
<!-- Or from CDN -->
<script src="https://cdn.jsdelivr.net/npm/@microsoft/signalr@latest/dist/browser/signalr.min.js"></script>
```

### Connecting to SignalR Hub

```javascript
// Basic connection
const connection = new signalR.HubConnectionBuilder()
    .withUrl('/notificationHub')
    .withAutomaticReconnect()
    .configureLogging(signalR.LogLevel.Information)
    .build();

// Start connection
async function startConnection() {
    try {
        await connection.start();
        console.log('SignalR Connected');
    } catch (err) {
        console.error('SignalR Connection Error:', err);
        setTimeout(startConnection, 5000);
    }
}

connection.onclose(async () => {
    await startConnection();
});

connection.onreconnecting(error => {
    console.log('SignalR Reconnecting:', error);
    // Update UI to show reconnecting state
});

connection.onreconnected(connectionId => {
    console.log('SignalR Reconnected:', connectionId);
    // Update UI to show connected state
});

startConnection();
```

### Receiving Messages

```javascript
// Receive messages from server
connection.on('ReceiveMessage', (user, message) => {
    console.log(`${user}: ${message}`);
    displayMessage(user, message);
});

connection.on('ReceiveNotification', (notification) => {
    showNotification(notification.title, notification.body);
});

connection.on('UserConnected', (connectionId) => {
    console.log(`User connected: ${connectionId}`);
});

connection.on('UserDisconnected', (connectionId) => {
    console.log(`User disconnected: ${connectionId}`);
});

// Handle data updates
connection.on('ProductUpdated', (product) => {
    updateProductInList(product);
});

connection.on('ProductDeleted', (productId) => {
    removeProductFromList(productId);
});

connection.on('OrderStatusChanged', (orderId, newStatus) => {
    updateOrderStatus(orderId, newStatus);
});
```

### Sending Messages

```javascript
// Send message to all clients
async function sendMessage(user, message) {
    try {
        await connection.invoke('SendMessage', user, message);
    } catch (err) {
        console.error('Error sending message:', err);
    }
}

// Send to specific group
async function sendToGroup(groupName, message) {
    await connection.invoke('SendToGroup', groupName, message);
}

// Join a group
async function joinGroup(groupName) {
    await connection.invoke('JoinGroup', groupName);
}

// Leave a group
async function leaveGroup(groupName) {
    await connection.invoke('LeaveGroup', groupName);
}

// Streaming (server-to-client)
connection.stream('StreamData', parameters)
    .subscribe({
        next: (item) => {
            console.log('Received:', item);
        },
        complete: () => {
            console.log('Stream completed');
        },
        error: (err) => {
            console.error('Stream error:', err);
        }
    });
```

### Complete SignalR Service

```javascript
// wwwroot/js/services/signalr-service.js
class SignalRService {
    constructor(hubUrl, options = {}) {
        this.hubUrl = hubUrl;
        this.options = options;
        this.connection = null;
        this.eventHandlers = new Map();
        this.reconnectAttempts = 0;
        this.maxReconnectAttempts = 5;
    }

    async connect() {
        if (this.connection && this.connection.state === signalR.HubConnectionState.Connected) {
            return;
        }

        this.connection = new signalR.HubConnectionBuilder()
            .withUrl(this.hubUrl, {
                accessTokenFactory: this.options.accessTokenFactory,
                skipNegotiation: this.options.skipNegotiation || false,
                transport: this.options.transport || signalR.HttpTransportType.WebSockets
            })
            .withAutomaticReconnect({
                nextRetryDelayInMilliseconds: retryContext => {
                    if (retryContext.previousRetryCount >= this.maxReconnectAttempts) {
                        return null; // Stop reconnecting
                    }
                    // Exponential backoff: 0, 2, 4, 6, 8 seconds
                    return Math.min(retryContext.previousRetryCount * 2000, 10000);
                }
            })
            .configureLogging(this.options.logLevel || signalR.LogLevel.Warning)
            .build();

        this.setupConnectionEvents();
        await this.startConnection();
    }

    setupConnectionEvents() {
        this.connection.onclose(async (error) => {
            console.log('Connection closed', error);
            this.emit('disconnected', error);
        });

        this.connection.onreconnecting((error) => {
            console.log('Reconnecting...', error);
            this.emit('reconnecting', error);
        });

        this.connection.onreconnected((connectionId) => {
            console.log('Reconnected', connectionId);
            this.reconnectAttempts = 0;
            this.emit('reconnected', connectionId);
        });
    }

    async startConnection() {
        try {
            await this.connection.start();
            console.log('Connected to SignalR hub');
            this.emit('connected');
        } catch (error) {
            console.error('Connection failed:', error);
            this.reconnectAttempts++;
            
            if (this.reconnectAttempts < this.maxReconnectAttempts) {
                setTimeout(() => this.startConnection(), 5000);
            }
            
            throw error;
        }
    }

    on(eventName, callback) {
        if (!this.eventHandlers.has(eventName)) {
            this.eventHandlers.set(eventName, []);
            
            // Register with SignalR
            this.connection?.on(eventName, (...args) => {
                this.emit(eventName, ...args);
            });
        }
        
        this.eventHandlers.get(eventName).push(callback);
    }

    off(eventName, callback) {
        const handlers = this.eventHandlers.get(eventName);
        if (handlers) {
            const index = handlers.indexOf(callback);
            if (index > -1) {
                handlers.splice(index, 1);
            }
        }
    }

    emit(eventName, ...args) {
        const handlers = this.eventHandlers.get(eventName);
        if (handlers) {
            handlers.forEach(handler => handler(...args));
        }
    }

    async invoke(methodName, ...args) {
        if (!this.connection || this.connection.state !== signalR.HubConnectionState.Connected) {
            throw new Error('Not connected to SignalR hub');
        }
        
        return await this.connection.invoke(methodName, ...args);
    }

    async disconnect() {
        if (this.connection) {
            await this.connection.stop();
            this.connection = null;
        }
    }

    get isConnected() {
        return this.connection?.state === signalR.HubConnectionState.Connected;
    }
}

// Usage
const notificationHub = new SignalRService('/notificationHub', {
    logLevel: signalR.LogLevel.Information
});

notificationHub.on('connected', () => {
    document.body.setAttribute('data-signalr', 'connected');
});

notificationHub.on('disconnected', () => {
    document.body.setAttribute('data-signalr', 'disconnected');
});

notificationHub.on('ReceiveNotification', (notification) => {
    showToast(notification.message, notification.type);
});

await notificationHub.connect();
```

---

## Chapter 11: JavaScript Interop in Blazor

### Calling JavaScript from Blazor

```csharp
// Services/JsInteropService.cs
public class JsInteropService : IAsyncDisposable
{
    private readonly IJSRuntime _jsRuntime;
    private IJSObjectReference? _module;

    public JsInteropService(IJSRuntime jsRuntime)
    {
        _jsRuntime = jsRuntime;
    }

    public async ValueTask InitializeAsync()
    {
        _module = await _jsRuntime.InvokeAsync<IJSObjectReference>(
            "import", "/js/blazor-interop.js");
    }

    // Simple function call
    public async ValueTask ShowAlertAsync(string message)
    {
        await _jsRuntime.InvokeVoidAsync("alert", message);
    }

    // Function with return value
    public async ValueTask<string> GetLocalStorageAsync(string key)
    {
        return await _jsRuntime.InvokeAsync<string>(
            "localStorage.getItem", key);
    }

    public async ValueTask SetLocalStorageAsync(string key, string value)
    {
        await _jsRuntime.InvokeVoidAsync(
            "localStorage.setItem", key, value);
    }

    // Complex object
    public async ValueTask<BrowserInfo> GetBrowserInfoAsync()
    {
        return await _jsRuntime.InvokeAsync<BrowserInfo>("getBrowserInfo");
    }

    // Using module
    public async ValueTask DownloadFileAsync(string fileName, byte[] content)
    {
        if (_module is not null)
        {
            await _module.InvokeVoidAsync(
                "downloadFile", fileName, content);
        }
    }

    public async ValueTask DisposeAsync()
    {
        if (_module is not null)
        {
            await _module.DisposeAsync();
        }
    }
}

public record BrowserInfo
{
    public string UserAgent { get; init; } = string.Empty;
    public string Platform { get; init; } = string.Empty;
    public int ScreenWidth { get; init; }
    public int ScreenHeight { get; init; }
}
```

```javascript
// wwwroot/js/blazor-interop.js
export function downloadFile(fileName, content) {
    const blob = new Blob([content], { type: 'application/octet-stream' });
    const url = URL.createObjectURL(blob);
    const a = document.createElement('a');
    a.href = url;
    a.download = fileName;
    document.body.appendChild(a);
    a.click();
    document.body.removeChild(a);
    URL.revokeObjectURL(url);
}

export function initializeChart(elementId, config) {
    const ctx = document.getElementById(elementId);
    return new Chart(ctx, config);
}

export function updateChart(chart, data) {
    chart.data = data;
    chart.update();
}

window.getBrowserInfo = function() {
    return {
        userAgent: navigator.userAgent,
        platform: navigator.platform,
        screenWidth: screen.width,
        screenHeight: screen.height
    };
};
```

### Calling Blazor from JavaScript

```csharp
// Components/Counter.razor
@page "/counter"
@implements IAsyncDisposable

<h1>Counter</h1>
<p>Current count: @currentCount</p>

@code {
    private int currentCount;
    private DotNetObjectReference<Counter>? dotNetRef;

    protected override void OnInitialized()
    {
        dotNetRef = DotNetObjectReference.Create(this);
    }

    [JSInvokable]
    public void IncrementCount(int amount = 1)
    {
        currentCount += amount;
        StateHasChanged();
    }

    [JSInvokable]
    public async Task<int> GetCurrentCountAsync()
    {
        return currentCount;
    }

    public async ValueTask DisposeAsync()
    {
        if (dotNetRef != null)
        {
            // Clean up any JavaScript references
            await JS.InvokeVoidAsync("counterInterop.dispose");
            dotNetRef.Dispose();
        }
    }
}
```

```javascript
// JavaScript code to call Blazor
// Instance methods (requires DotNetObjectReference)
let dotNetRef = null;

async function initCounter(componentRef) {
    dotNetRef = componentRef;
}

function incrementFromJs() {
    if (dotNetRef) {
        dotNetRef.invokeMethodAsync('IncrementCount', 5);
    }
}

async function getCount() {
    if (dotNetRef) {
        return await dotNetRef.invokeMethodAsync('GetCurrentCountAsync');
    }
    return null;
}

function dispose() {
    dotNetRef = null;
}

// Static methods
async function callStaticMethod() {
    const result = await DotNet.invokeMethodAsync(
        'MyAssembly', 
        'MyStaticMethod', 
        param1, 
        param2
    );
    return result;
}

// Register functions for Blazor to call
window.counterInterop = {
    init: initCounter,
    increment: incrementFromJs,
    getCount: getCount,
    dispose: dispose
};
```

---

## Chapter 12: SPA Frameworks Integration

### React Integration

```bash
# Create ASP.NET Core with React
dotnet new react -o MyReactApp

# Or add React to existing project
npm install react react-dom
npm install --save-dev @types/react @types/react-dom
```

```csharp
// Program.cs for React SPA
var builder = WebApplication.CreateBuilder(args);

builder.Services.AddControllers();
builder.Services.AddSpaStaticFiles(configuration =>
{
    configuration.RootPath = "ClientApp/build";
});

var app = builder.Build();

app.UseStaticFiles();
app.UseSpaStaticFiles();

app.UseRouting();
app.MapControllers();

app.UseSpa(spa =>
{
    spa.Options.SourcePath = "ClientApp";

    if (app.Environment.IsDevelopment())
    {
        spa.UseReactDevelopmentServer(npmScript: "start");
    }
});

app.Run();
```

```jsx
// ClientApp/src/components/ProductList.jsx
import React, { useState, useEffect } from 'react';

function ProductList() {
    const [products, setProducts] = useState([]);
    const [loading, setLoading] = useState(true);

    useEffect(() => {
        fetch('/api/products')
            .then(response => response.json())
            .then(data => {
                setProducts(data);
                setLoading(false);
            })
            .catch(error => {
                console.error('Error fetching products:', error);
                setLoading(false);
            });
    }, []);

    if (loading) {
        return <div>Loading...</div>;
    }

    return (
        <div className="product-list">
            {products.map(product => (
                <div key={product.id} className="product-card">
                    <h3>{product.name}</h3>
                    <p>${product.price}</p>
                </div>
            ))}
        </div>
    );
}

export default ProductList;
```

### Angular Integration

```bash
# Create ASP.NET Core with Angular
dotnet new angular -o MyAngularApp
```

```typescript
// ClientApp/src/app/services/product.service.ts
import { Injectable } from '@angular/core';
import { HttpClient } from '@angular/common/http';
import { Observable } from 'rxjs';
import { Product } from '../models/product';

@Injectable({
  providedIn: 'root'
})
export class ProductService {
  private apiUrl = '/api/products';

  constructor(private http: HttpClient) { }

  getProducts(): Observable<Product[]> {
    return this.http.get<Product[]>(this.apiUrl);
  }

  getProduct(id: number): Observable<Product> {
    return this.http.get<Product>(`${this.apiUrl}/${id}`);
  }

  createProduct(product: Product): Observable<Product> {
    return this.http.post<Product>(this.apiUrl, product);
  }

  updateProduct(product: Product): Observable<void> {
    return this.http.put<void>(`${this.apiUrl}/${product.id}`, product);
  }

  deleteProduct(id: number): Observable<void> {
    return this.http.delete<void>(`${this.apiUrl}/${id}`);
  }
}
```

### Vue Integration

```bash
# Add Vue to ASP.NET Core project
npm create vue@latest client
cd client
npm install
```

```javascript
// client/src/main.js
import { createApp } from 'vue';
import App from './App.vue';
import router from './router';

const app = createApp(App);
app.use(router);
app.mount('#app');

// client/src/composables/useProducts.js
import { ref } from 'vue';

export function useProducts() {
    const products = ref([]);
    const loading = ref(false);
    const error = ref(null);

    async function fetchProducts() {
        loading.value = true;
        error.value = null;

        try {
            const response = await fetch('/api/products');
            if (!response.ok) throw new Error('Failed to fetch products');
            products.value = await response.json();
        } catch (e) {
            error.value = e.message;
        } finally {
            loading.value = false;
        }
    }

    return { products, loading, error, fetchProducts };
}
```

---

## Chapter 13: Managing JavaScript Libraries

### LibMan (Library Manager)

```xml
<!-- libman.json -->
{
  "version": "1.0",
  "defaultProvider": "cdnjs",
  "libraries": [
    {
      "provider": "cdnjs",
      "library": "jquery@3.7.1",
      "destination": "wwwroot/lib/jquery/dist",
      "files": ["jquery.min.js", "jquery.js"]
    },
    {
      "provider": "cdnjs",
      "library": "bootstrap@5.3.2",
      "destination": "wwwroot/lib/bootstrap/dist",
      "files": ["js/bootstrap.bundle.min.js", "js/bootstrap.bundle.js", "css/bootstrap.min.css", "css/bootstrap.css"]
    },
    {
      "provider": "cdnjs",
      "library": "microsoft-signalr@8.0.0",
      "destination": "wwwroot/lib/microsoft/signalr",
      "files": ["signalr.min.js", "signalr.js"]
    },
    {
      "provider": "jsdelivr",
      "library": "chart.js@4.4.1",
      "destination": "wwwroot/lib/chart.js"
    }
  ]
}
```

```bash
# LibMan CLI commands
dotnet tool install -g Microsoft.Web.LibraryManager.Cli

# Restore libraries
libman restore

# Install library
libman install jquery@3.7.1 -d wwwroot/lib/jquery

# Update library
libman update jquery
```

### NPM Integration

```json
// package.json
{
  "name": "my-aspnet-app",
  "version": "1.0.0",
  "scripts": {
    "build": "esbuild wwwroot/js/src/**/*.js --bundle --outdir=wwwroot/js/dist --minify",
    "watch": "esbuild wwwroot/js/src/**/*.js --bundle --outdir=wwwroot/js/dist --watch",
    "lint": "eslint wwwroot/js/src/**/*.js",
    "test": "jest"
  },
  "devDependencies": {
    "esbuild": "^0.19.0",
    "eslint": "^8.56.0",
    "jest": "^29.7.0",
    "typescript": "^5.3.0"
  },
  "dependencies": {
    "@microsoft/signalr": "^8.0.0",
    "chart.js": "^4.4.1"
  }
}
```

---

## Chapter 14: Debugging JavaScript

### Browser Developer Tools

```javascript
// Console debugging
console.log('Simple log');
console.info('Info message');
console.warn('Warning message');
console.error('Error message');

// Formatted output
console.log('User: %s, Age: %d', name, age);
console.log('Object:', { name, age });

// Table output
console.table([
    { name: 'John', age: 30 },
    { name: 'Jane', age: 25 }
]);

// Grouping
console.group('API Response');
console.log('Status:', response.status);
console.log('Data:', response.data);
console.groupEnd();

// Timing
console.time('fetchData');
await fetchData();
console.timeEnd('fetchData');

// Stack trace
console.trace('Function called from');

// Debugger statement
function problematicFunction() {
    debugger; // Execution pauses here in DevTools
    return complexCalculation();
}

// Assertions
console.assert(value !== null, 'Value should not be null');
```

### Error Handling

```javascript
// Global error handler
window.addEventListener('error', (event) => {
    console.error('Global error:', event.error);
    
    // Send to error reporting service
    reportError({
        message: event.message,
        filename: event.filename,
        lineno: event.lineno,
        colno: event.colno,
        stack: event.error?.stack
    });
});

// Unhandled promise rejection
window.addEventListener('unhandledrejection', (event) => {
    console.error('Unhandled rejection:', event.reason);
    
    // Prevent default browser behavior
    event.preventDefault();
    
    // Report the error
    reportError({
        type: 'unhandledrejection',
        reason: event.reason
    });
});

// Try-catch with async
async function safeAsync(fn) {
    try {
        return await fn();
    } catch (error) {
        console.error('Async error:', error);
        handleError(error);
        return null;
    }
}

// Error boundary pattern
class ErrorBoundary {
    constructor(fn, fallback) {
        this.fn = fn;
        this.fallback = fallback;
    }
    
    execute(...args) {
        try {
            const result = this.fn(...args);
            if (result instanceof Promise) {
                return result.catch(error => {
                    console.error('Promise error:', error);
                    return this.fallback(error);
                });
            }
            return result;
        } catch (error) {
            console.error('Sync error:', error);
            return this.fallback(error);
        }
    }
}
```

---

## Chapter 15: Security Considerations

### XSS Prevention

```javascript
// NEVER do this - vulnerable to XSS
element.innerHTML = userInput;

// Safe alternatives
element.textContent = userInput;  // Escapes HTML

// Sanitize HTML when needed
function sanitizeHtml(html) {
    const div = document.createElement('div');
    div.textContent = html;
    return div.innerHTML;
}

// Using DOMPurify library
import DOMPurify from 'dompurify';
element.innerHTML = DOMPurify.sanitize(userInput, {
    ALLOWED_TAGS: ['b', 'i', 'em', 'strong', 'a'],
    ALLOWED_ATTR: ['href']
});

// Safe URL handling
function isSafeUrl(url) {
    try {
        const parsed = new URL(url, window.location.origin);
        // Only allow same-origin URLs
        return parsed.origin === window.location.origin;
    } catch {
        return false;
    }
}

// Safe redirect
function safeRedirect(url) {
    if (isSafeUrl(url)) {
        window.location.href = url;
    } else {
        console.warn('Blocked potentially unsafe redirect:', url);
    }
}
```

### CSRF Protection

```javascript
// Always include anti-forgery token in POST requests
async function securePost(url, data) {
    const token = getAntiForgeryToken();
    
    return fetch(url, {
        method: 'POST',
        headers: {
            'Content-Type': 'application/json',
            'RequestVerificationToken': token,
            'X-Requested-With': 'XMLHttpRequest'
        },
        body: JSON.stringify(data)
    });
}

// Double-submit cookie pattern
async function fetchWithCsrf(url, options = {}) {
    // Get CSRF token from cookie
    const csrfToken = getCookie('XSRF-TOKEN');
    
    return fetch(url, {
        ...options,
        headers: {
            ...options.headers,
            'X-XSRF-TOKEN': csrfToken
        }
    });
}

function getCookie(name) {
    const value = `; ${document.cookie}`;
    const parts = value.split(`; ${name}=`);
    if (parts.length === 2) {
        return parts.pop().split(';').shift();
    }
}
```

### Content Security Policy

```csharp
// Program.cs - Configure CSP
app.Use(async (context, next) =>
{
    context.Response.Headers.Add("Content-Security-Policy", 
        "default-src 'self'; " +
        "script-src 'self' 'unsafe-inline' 'unsafe-eval' https://cdn.jsdelivr.net; " +
        "style-src 'self' 'unsafe-inline' https://cdn.jsdelivr.net; " +
        "img-src 'self' data: https:; " +
        "font-src 'self' https://cdn.jsdelivr.net; " +
        "connect-src 'self' https://api.example.com; " +
        "frame-ancestors 'none';");
    
    await next();
});
```

---

## Chapter 16: Performance Optimization

### Lazy Loading Scripts

```html
<!-- Defer script loading -->
<script src="~/js/analytics.js" defer></script>

<!-- Async loading -->
<script src="~/js/non-critical.js" async></script>

<!-- Dynamic import -->
<script>
    document.getElementById('loadChart').addEventListener('click', async () => {
        const module = await import('/js/chart.js');
        module.initializeChart();
    });
</script>
```

### Code Splitting

```javascript
// Dynamic imports for code splitting
const routes = {
    home: () => import('./pages/home.js'),
    products: () => import('./pages/products.js'),
    orders: () => import('./pages/orders.js')
};

async function loadPage(page) {
    const module = await routes[page]();
    module.init();
}

// Intersection Observer for lazy loading
const lazyScripts = document.querySelectorAll('script[data-src]');
const observer = new IntersectionObserver((entries) => {
    entries.forEach(entry => {
        if (entry.isIntersecting) {
            const script = document.createElement('script');
            script.src = entry.target.dataset.src;
            document.body.appendChild(script);
            observer.unobserve(entry.target);
        }
    });
});

lazyScripts.forEach(script => observer.observe(script));
```

### Performance Monitoring

```javascript
// Performance API
// Measure page load
window.addEventListener('load', () => {
    const timing = performance.timing;
    const metrics = {
        dns: timing.domainLookupEnd - timing.domainLookupStart,
        tcp: timing.connectEnd - timing.connectStart,
        request: timing.responseStart - timing.requestStart,
        response: timing.responseEnd - timing.responseStart,
        domProcessing: timing.domComplete - timing.domLoading,
        totalLoad: timing.loadEventEnd - timing.navigationStart
    };
    
    console.table(metrics);
});

// Custom performance marks
performance.mark('startOperation');
// ... operation
performance.mark('endOperation');
performance.measure('operation', 'startOperation', 'endOperation');

const measures = performance.getEntriesByName('operation');
console.log('Duration:', measures[0].duration);

// Performance Observer
const observer = new PerformanceObserver((list) => {
    list.getEntries().forEach(entry => {
        console.log(entry.name, entry.duration);
    });
});
observer.observe({ entryTypes: ['measure', 'navigation', 'resource'] });
```

---

## Chapter 17: Testing JavaScript Code

### Unit Testing with Jest

```javascript
// jest.config.js
module.exports = {
    testEnvironment: 'jsdom',
    roots: ['<rootDir>/wwwroot/js/tests'],
    moduleNameMapper: {
        '^@/(.*)$': '<rootDir>/wwwroot/js/src/$1'
    }
};

// wwwroot/js/tests/services/api.test.js
import { ApiService } from '../../src/services/api';

// Mock fetch
global.fetch = jest.fn();

describe('ApiService', () => {
    let api;
    
    beforeEach(() => {
        api = new ApiService();
        fetch.mockClear();
    });
    
    test('get should fetch data from endpoint', async () => {
        const mockData = { id: 1, name: 'Test' };
        fetch.mockResolvedValueOnce({
            ok: true,
            json: async () => mockData
        });
        
        const result = await api.get('/test');
        
        expect(fetch).toHaveBeenCalledWith('/api/test');
        expect(result).toEqual(mockData);
    });
    
    test('should throw on error response', async () => {
        fetch.mockResolvedValueOnce({
            ok: false,
            status: 404
        });
        
        await expect(api.get('/test')).rejects.toThrow();
    });
});
```

### Integration Testing

```javascript
// Integration test with DOM
describe('ProductList', () => {
    beforeEach(() => {
        document.body.innerHTML = `
            <div id="products-list"></div>
            <button id="load-products">Load</button>
        `;
    });
    
    test('should render products', async () => {
        const mockProducts = [
            { id: 1, name: 'Product 1', price: 10 },
            { id: 2, name: 'Product 2', price: 20 }
        ];
        
        fetch.mockResolvedValueOnce({
            ok: true,
            json: async () => mockProducts
        });
        
        await loadProducts();
        
        const container = document.getElementById('products-list');
        expect(container.children.length).toBe(2);
    });
});
```

---

## Chapter 18: Best Practices

### Code Organization

```javascript
// Use a consistent module structure
// wwwroot/js/
// ├── src/
// │   ├── components/
// │   ├── services/
// │   ├── utils/
// │   └── pages/
// ├── dist/           // Compiled/bundled output
// └── tests/

// Namespace pattern (for legacy code)
window.MyApp = window.MyApp || {};
MyApp.Services = MyApp.Services || {};
MyApp.Components = MyApp.Components || {};

// Use strict mode
'use strict';

// IIFE for encapsulation
(function(window, document) {
    'use strict';
    
    // Private variables
    const privateVar = 'private';
    
    // Expose public API
    window.MyApp = {
        publicMethod: function() {
            return privateVar;
        }
    };
})(window, document);
```

### Error Handling Best Practices

```javascript
// Always handle promise rejections
async function safeOperation() {
    try {
        const result = await riskyOperation();
        return result;
    } catch (error) {
        console.error('Operation failed:', error);
        // Return safe default or re-throw
        return null;
    }
}

// Validate inputs
function processUser(user) {
    if (!user || typeof user !== 'object') {
        throw new TypeError('Invalid user object');
    }
    
    if (!user.id || !user.name) {
        throw new Error('User missing required fields');
    }
    
    // Process valid user
}

// Graceful degradation
function loadFeature() {
    if ('IntersectionObserver' in window) {
        // Use modern API
        new IntersectionObserver(handleIntersect);
    } else {
        // Fallback
        window.addEventListener('scroll', handleScroll);
    }
}
```

---

## Chapter 19: Quick Reference Cheatsheet

### Fetch API Quick Reference

```javascript
// GET
const data = await fetch('/api/products').then(r => r.json());

// POST
await fetch('/api/products', {
    method: 'POST',
    headers: { 'Content-Type': 'application/json' },
    body: JSON.stringify({ name: 'Product' })
});

// FormData
const formData = new FormData(form);
await fetch('/api/upload', { method: 'POST', body: formData });
```

### DOM Manipulation Quick Reference

```javascript
// Selectors
document.querySelector('.class');
document.querySelectorAll('.class');
document.getElementById('id');

// Manipulation
element.textContent = 'text';
element.innerHTML = '<b>html</b>';
element.classList.add('class');
element.classList.remove('class');
element.classList.toggle('class');
element.style.color = 'red';
element.setAttribute('data-id', '123');
element.dataset.id;  // '123'

// Events
element.addEventListener('click', (e) => { });
element.removeEventListener('click', handler);

// Creation
const div = document.createElement('div');
parent.appendChild(div);
```

### SignalR Quick Reference

```javascript
// Connection
const connection = new signalR.HubConnectionBuilder()
    .withUrl('/hub')
    .withAutomaticReconnect()
    .build();

await connection.start();

// Receive
connection.on('method', (data) => { });

// Send
await connection.invoke('method', data);

// Groups
await connection.invoke('JoinGroup', 'groupName');
await connection.invoke('SendToGroup', 'groupName', data);
```

---

## Chapter 20: Troubleshooting Guide

### Common Issues and Solutions

| Issue | Cause | Solution |
|-------|-------|----------|
| Script not loading | Wrong path or order | Check path, ensure loaded before use |
| $ is undefined | jQuery not loaded | Include jQuery before dependent scripts |
| CORS error | Cross-origin request blocked | Configure CORS on server |
| 400 Bad Request | Anti-forgery token missing | Include RequestVerificationToken header |
| 401 Unauthorized | Authentication required | Include authentication token |
| SignalR connection fails | WebSocket unavailable | Check proxy/firewall, enable fallback |

### Debugging Tips

```javascript
// Enable verbose logging
localStorage.setItem('debug', '*');

// Check script loading order
document.querySelectorAll('script').forEach(s => console.log(s.src));

// Monitor network requests
performance.getEntriesByType('resource').filter(r => r.initiatorType === 'script');

// Check for memory leaks
setInterval(() => {
    console.log('DOM nodes:', document.getElementsByTagName('*').length);
}, 10000);
```

---

## Summary

This comprehensive guide has covered everything you need to know about JavaScript integration in ASP.NET Core applications. From basic script inclusion and DOM manipulation to advanced topics like SignalR integration, Blazor interop, and SPA frameworks, you now have the knowledge to build rich, interactive web applications. Remember to always prioritize security, performance, and maintainability when implementing JavaScript in your projects.

---

## Resources

- [MDN JavaScript Documentation](https://developer.mozilla.org/en-US/docs/Web/JavaScript)
- [ASP.NET Core JavaScript Services](https://docs.microsoft.com/aspnet/core/client-side/)
- [SignalR JavaScript Client](https://docs.microsoft.com/aspnet/core/signalr/javascript-client)
- [TypeScript Documentation](https://www.typescriptlang.org/docs/)
- [Jest Testing Framework](https://jestjs.io/docs/getting-started)
