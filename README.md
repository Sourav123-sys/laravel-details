## Question 1:

You have a Laravel application with a form that submits user information using a POST request. Write the code to retrieve the 'name' input field value from the request and store it in a variable called $name.

use Illuminate\Http\Request;

public function store(Request $request) { $name = $request->input('name');

}

## Question 2:
 In your Laravel application, you want to retrieve the value of the 'User-Agent'header from the current request. Write the code to accomplish this and store the value in a variable called$userAgent.``

use Illuminate\Http\Request;

public function retrieveUserAgent(Request $request) { $userAgent = $request->header('User-Agent');

}

## Question 3

You are building an API endpoint in Laravel that accepts a GET request with a 'page'query parameter. Write the code to retrieve the value of the 'page' parameter from the current request and store it in a variable called$page. If the parameter is not present, set $pagetonull.

use Illuminate\Http\Request;

public function handleGetRequest(Request $request) { $page = $request->input('page', null);

}

Question 4

Create a JSON response in Laravel with the following data:

{ "message": "Success", "data": { "name": "John Doe", "age": 25 }
}

use Illuminate\Http\Response;

public function jsonResponse() { $data = [ 'message' => 'Success', 'data' => [ 'name' => 'John Doe', 'age' => 25 ] ];

return response()->json($data);
}

## Question 5: 
You are implementing a file upload feature in your Laravel application. Write the code to handle a file upload named 'avatar'in the currentrequestand store the uploaded file in the'public/uploads'directory. Use theoriginal filename for the uploaded file.

use Illuminate\Http\Request;

public function handleFileUpload(Request $request) { if ($request->hasFile('avatar')) { $uploadedFile = $request->file('avatar'); $path = $uploadedFile->store('uploads', 'public'); $filename = $uploadedFile->getClientOriginalName(); $fullPath = 'public/' . $path;

}
}

## Question 6:

 Retrieve the value of the 'remember_token'cookie from the current request in Laravel and store it in a variable called$rememberToken. If the cookie is not present, set $rememberToken to null.

use Illuminate\Http\Request;

public function retrieveRememberToken(Request $request) { $rememberToken = $request->cookie('remember_token', null);

}

## Question 7: 
Create a route in Laravel that handles a POSTrequest to the'/submit'URL. Inside the route closure, retrieve the'email'input parameter from the request and store it in a variable called$email. Return a JSON response with the following data:

{

"success": true,

"message": "Form submitted successfully."

} use Illuminate\Http\Request; use Illuminate\Support\Facades\Route; use Illuminate\Http\JsonResponse;

Route::post('/submit', function (Request $request) { $email = $request->input('email');

$response = [
    'success' => true,
    'message' => 'Form submitted successfully.'
];

return new JsonResponse($response);
});
