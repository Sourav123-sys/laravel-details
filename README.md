# Task 1: Request Validation

mplement request validation for a registration form that contains the following fields: name, email, and password. Validate the following rules:

name: required, string, minimum length 2.
email: required, valid email format.
password: required, string, minimum length 8


// app/Http/Requests/RegisterRequest.php

namespace App\Http\Requests;

use Illuminate\Foundation\Http\FormRequest;

class RegisterRequest extends FormRequest
{
    public function rules()
    {
        return [
            'name' => 'required|string|min:2',
            'email' => 'required|email',
            'password' => 'required|string|min:8',
        ];
    }
}
// app/Http/Controllers/RegisterController.php

namespace App\Http\Controllers;

use App\Http\Requests\RegisterRequest;

class RegisterController extends Controller
{
    public function register(RegisterRequest $request)
    {
        // Validation passed, continue with registration logic
    }
}


## Task 2: Request Redirect


Create a route /home that redirects to /dashboard using a 302 redirect.


// routes/web.php

use Illuminate\Support\Facades\Redirect;

Route::get('/home', function () {
    return Redirect::to('/dashboard', 302);
});


# Task 3: Global Middleware

// app/Http/Middleware/LogRequestMiddleware.php

namespace App\Http\Middleware;

use Closure;
use Illuminate\Support\Facades\Log;

class LogRequestMiddleware
{
    public function handle($request, Closure $next)
    {
        Log::info('Request: ' . $request->method() . ' ' . $request->fullUrl());

        return $next($request);
    }
}

// app/Http/Kernel.php

protected $middleware = [
    // Other middleware...
    \App\Http\Middleware\LogRequestMiddleware::class,
];

# Task 4: Route Middleware
// routes/web.php

use App\Http\Middleware\AuthMiddleware;

Route::middleware([AuthMiddleware::class])->group(function () {
    Route::get('/profile', function () {
        // Profile route logic
    });

    Route::get('/settings', function () {
        // Settings route logic
    });
});


# Task 5: Controller

Task 5: Controller


Create a controller called ProductController that handles CRUD operations for a resource called Product. Implement the following methods:



index(): Display a list of all products.


create(): Display the form to create a new product.


store(): Store a newly created product.


edit($id): Display the form to edit an existing product.


update($id): Update the specified product.


destroy($id): Delete the specified product.

namespace App\Http\Controllers;

use App\Models\Product;
use Illuminate\Http\Request;

class ProductController extends Controller
{
    public function index()
    {
        $products = Product::all();
        return view('products.index', ['products' => $products]);
    }

    public function create()
    {
        return view('products.create');
    }

    public function store(Request $request)
    {
        $request->validate([
            'name' => 'required|string|min:2',
            'price' => 'required|numeric',
            // Add validation rules for other fields
        ]);

        Product::create($request->all());
        return redirect()->route('products.index')->with('success', 'Product created successfully.');
    }

    public function edit($id)
    {
        $product = Product::findOrFail($id);
        return view('products.edit', ['product' => $product]);
    }

    public function update(Request $request, $id)
    {
        $request->validate([
            'name' => 'required|string|min:2',
            'price' => 'required|numeric',
            // Add validation rules for other fields
        ]);

        $product = Product::findOrFail($id);
        $product->update($request->all());
        return redirect()->route('products.index')->with('success', 'Product updated successfully.');
    }

    public function destroy($id)
    {
        $product = Product::findOrFail($id);
        $product->delete();
        return redirect()->route('products.index')->with('success', 'Product deleted successfully.');
    }
}

use App\Http\Controllers\ProductController;

Route::resource('products', ProductController::class);

# Task 6: Single Action Controller
Create a single action controller called ContactController that handles a contact form submission. Implement the __invoke() method to process the form submission and send an email to a predefined address with the submitted data.

namespace App\Http\Controllers;

use Illuminate\Http\Request;
use Illuminate\Support\Facades\Mail;

class ContactController extends Controller
{
    public function __invoke(Request $request)
    {
        $request->validate([
            'name' => 'required|string',
            'email' => 'required|email',
            'message' => 'required|string',
        ]);

        // Get the form data
        $name = $request->input('name');
        $email = $request->input('email');
        $message = $request->input('message');

        // Send email
        $recipient = 'contact@example.com'; // Replace with your predefined email address
        $subject = 'New Contact Form Submission';
        $data = [
            'name' => $name,
            'email' => $email,
            'message' => $message,
        ];

        Mail::to($recipient)->send(new \App\Mail\ContactFormMail($data));

        return redirect()->back()->with('success', 'Thank you for your message! We will get back to you soon.');
    }
}

namespace App\Mail;

use Illuminate\Bus\Queueable;
use Illuminate\Mail\Mailable;
use Illuminate\Queue\SerializesModels;

class ContactFormMail extends Mailable
{
    use Queueable, SerializesModels;

    public $data;

    public function __construct($data)
    {
        $this->data = $data;
    }

    public function build()
    {
        return $this->subject('New Contact Form Submission')
            ->view('emails.contact-form') // Replace with your email blade template
            ->with('data', $this->data);
    }
}
use App\Http\Controllers\ContactController;

Route::post('/contact', ContactController::class);

# task 7 Resource Controller

Create a resource controller called PostController that handles CRUD operations for a resource called Post. Ensure that the controller provides the necessary methods for the resourceful routing conventions in Laravel.

namespace App\Http\Controllers;

use App\Models\Post;
use Illuminate\Http\Request;

class PostController extends Controller
{
    public function index()
    {
        $posts = Post::all();
        return view('posts.index', ['posts' => $posts]);
    }

    public function create()
    {
        return view('posts.create');
    }

    public function store(Request $request)
    {
        $request->validate([
            'title' => 'required|string',
            'content' => 'required|string',
        ]);

        Post::create($request->all());
        return redirect()->route('posts.index')->with('success', 'Post created successfully.');
    }

    public function show($id)
    {
        $post = Post::findOrFail($id);
        return view('posts.show', ['post' => $post]);
    }

    public function edit($id)
    {
        $post = Post::findOrFail($id);
        return view('posts.edit', ['post' => $post]);
    }

    public function update(Request $request, $id)
    {
        $request->validate([
            'title' => 'required|string',
            'content' => 'required|string',
        ]);

        $post = Post::findOrFail($id);
        $post->update($request->all());
        return redirect()->route('posts.index')->with('success', 'Post updated successfully.');
    }

    public function destroy($id)
    {
        $post = Post::findOrFail($id);
        $post->delete();
        return redirect()->route('posts.index')->with('success', 'Post deleted successfully.');
    }
}
use App\Http\Controllers\PostController;

Route::resource('posts', PostController::class);

# Task 8: Blade Template Engine

<!-- resources/views/welcome.blade.php -->

<!DOCTYPE html>
<html>
<head>
    <title>Welcome to Laravel!</title>
</head>
<body>
    <nav>
        <!-- Navigation bar code here -->
    </nav>

    <section>
        <h1>Welcome to Laravel!</h1>
    </section>
</body>
</html>


