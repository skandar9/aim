Project overview:

An Iraqi voluntary organization accessible through a website aiming to protect the rights of minorities.
It includes a collection of projects, news and reports.
You can subscribe to receive the latest news from the organization
visitors to the organization’s website can also join them to contribute with individuals or groups or send volunteer requests. It also allows those who suffer from a violation of their freedom and rights to apply for protection.

It includes features such as user authentication, subscription management, news management, team member management, contact message handling and more.

I have developed an admin control panel using the Material Dashboard template and created APIs to provide data to the frontend side.


> :warning: **Warning:** This contents below ↓ contains just parts of my code.
>                        You can access my full project files by clone it from my GitLab repository
>                        (requires asking for my permissions to grant you access to it):
>                        https://gitlab.com/skandar.s1998/aim 

## Contents
(contains descriptive parts of my code)

[Project actions and progress(graph)](#project-graph)

[Login to the dashboard functionality](#dashboard-login)

[Store publication](#store-publication)

[Newsletter job](#newsletter-job)

[Change settings](#change-settings)

[Create migration that add date to posts table](#add-date-to-posts-table)

[Store service](#store-subscriber)

[Export to excel](#exportToExcel)

[Forms management API](#forms-api)

[latest_news API](#latest-news)

[Contact management](#contact-management)

[Ckeditor (text editor) functionality (dashboard)](#Ckeditor)

[Subscriber modal (dashboard)](#add-subscriber-modal)

["loadImage" JavaScript function (dashboard)](#loadImage)

### **project-graph**

This graph diagram represents the actions and progress for the project.

![App Logo](/images/graph(1).png)
![App Logo](/images/graph(2).png)

[🔝 Back to contents](#contents)

### **dashboard-login**

![App Logo](/images/login.png)

I used Laravel UI package in this project that provides a convenient way to generate the frontend boilerplate code for authentication views, such as login, registration, and password reset forms.

I Run the following command to install the package:

```shell
composer require laravel/ui
```

This package I used to generate the authentication views.Specifically, I utilized the following file to facilitate the login process for the admin, granting access to their dashboard.

`resources/views/auth/login.blade.php`: This file contains the HTML template for the login form. It includes form fields for the username and password, along with any necessary validation error messages.

I depended on `Auth` Facade that is a core component of Laravel that provides convenient methods for user authentication. It is used to authenticate users and manage their sessions.

### login functionality workflow:

## Routes in `web.php`

`routes\web.php`
### Index Route

```php
Route::get('/', [PagesController::class, 'index'])->name('index');
```
This route is responsible for handling the homepage of the application. When a user visits the root URL, the `index` method of the `PagesController` will be executed.

### Dashboard Route

```php
Route::get('/dashboard', [PagesController::class, 'dashboard'])->middleware(['auth'])->name('dashboard');
```
This route represents the dashboard page of the application. It is protected by the `'auth'` middleware, which means only authenticated users can access it. When a user visits the `/dashboard` URL, the `dashboard` method of the `PagesController` will be executed.


## PagesController

`app\Http\Controllers\PagesController.php`

```php
class PagesController extends Controller
{
    public function index(){
        return redirect('/login');
    }

    public function dashboard(){
        return view('dashboard');
    }
}
```
### `index` Method

This method is responsible for handling the request to the homepage of the application. It performs a redirect to the `/login` URL. When a user visits the homepage, they will be redirected to the login page.

## Authentication Route

`routes\auth.php`

```php
Route::get('/login', [AuthenticatedSessionController::class, 'create'])
                ->middleware('guest')
                ->name('login');

Route::post('/login', [AuthenticatedSessionController::class, 'store'])
                ->middleware('guest');
```
### Login Route
This route is responsible for rendering the login form. When a GET request is made to the `/login` URL, the `create` method of the `AuthenticatedSessionController` will be executed. This route is protected by the `'guest'` middleware, which ensures that only non-authenticated users can access the login page.

### Login POST Route
This route is responsible for handling the submission of the login form. When a POST request is made to the `/login` URL, the `store` method of the `AuthenticatedSessionController` will be executed. This route is also protected by the `'guest'` middleware.

## AuthenticatedSessionController

`app\Http\Controllers\Auth\AuthenticatedSessionController.php`

```php
class AuthenticatedSessionController extends Controller
{
    public function create()
    {
        return view('auth.login');
    }

    public function store(LoginRequest $request)
    {
        $request->authenticate();

        $request->session()->regenerate();

        return redirect()->intended(RouteServiceProvider::HOME);
    }
}
```
### `create` Method
This method is responsible for displaying the login view. It returns the view named `'auth.login'`, which is the login form view. When this method is called, the login form will be rendered and displayed to the user.

### `store` Method
This method handles the incoming authentication request when the login form is submitted. It expects an instance of `LoginRequest` as a parameter, which contains the validation rules for the login request. The method calls the `authenticate` method on the request instance to perform the authentication. If the authentication is successful, the user's session is regenerated, and the user is redirected to the intended page (defined by `RouteServiceProvider::HOME`).

## Login form view

`resources\views\auth\login.blade.php`

```html
<x-layouts.guest>
    <x-auth-card>
        <x-slot name="logo">
            <a href="/">
                <img src="/img/aim.png" width="220px">
            </a>
        </x-slot>

        <!-- Session Status -->
        <x-auth-session-status class="mb-4" :status="session('status')" />

        <!-- Validation Errors -->
        <x-auth-validation-errors class="mb-4" :errors="$errors" />

        <form method="POST" action="{{ route('login') }}">
            @csrf

            <!-- Username -->
            <div>
                <x-label for="username" :value="__('Username')" />
                <x-input id="username" class="block mt-1 w-full" type="text" name="username" :value="old('username')" required autofocus />
            </div>

            <!-- Password -->
            <div class="mt-4">
                <x-label for="password" :value="__('Password')" />
                <x-input id="password" class="block mt-1 w-full"
                                type="password"
                                name="password"
                                required autocomplete="current-password" />
            </div>

            <!-- Remember Me -->
            <div class="block mt-4">
                <label for="remember_me" class="inline-flex items-center">
                    <input id="remember_me" type="checkbox" class="rounded border-gray-300 text-indigo-600 shadow-sm focus:border-indigo-300 focus:ring focus:ring-indigo-200 focus:ring-opacity-50" name="remember">
                    <span class="ml-2 text-sm text-gray-600">{{ __('Remember me') }}</span>
                </label>
            </div>

            <div class="flex items-center justify-end mt-4">
                @if (Route::has('password.request'))
                    <a class="underline text-sm text-gray-600 hover:text-gray-900" href="{{ route('password.request') }}">
                        {{ __('Forgot your password?') }}
                    </a>
                @endif

                <x-button class="ml-3">
                    {{ __('Log in') }}
                </x-button>
            </div>
        </form>
    </x-auth-card>
</x-layouts.guest>
```

The `login.blade.php` file contains the HTML markup for the login form view.
This file is installed with the package `laravel/ui`.

[🔝 Back to contents](#contents)

## LoginRequest

`app\Http\Requests\Auth\LoginRequest.php`

The `LoginRequest` class extends the `FormRequest` class in Laravel and is responsible for handling validation and authentication for the login form submission.
### `authorize` Method
This method determines if the user is authorized to make the login request. In this case, it always returns `true`, allowing any user to make the request.

### `rules` Method
This method defines the validation rules for the login request. It specifies that the `username` and `password` fields are required and must be of type `'string'`.

### `authenticate` Method
This method attempts to authenticate the user using the provided credentials. It first ensures that the request is not rate limited by calling the `ensureIsNotRateLimited` method. Then, it uses the `Auth::attempt` method to attempt authentication using the `username` and `password` fields from the request. It also includes the `remember` value as a boolean parameter to determine if the user wants to be remembered for future sessions. If the authentication attempt fails, the method hits the rate limiter by calling `RateLimiter::hit` and throws a `ValidationException` with the error message `'auth.failed'`. If the authentication attempt succeeds, the rate limiter is cleared by calling `RateLimiter::clear`.

The `LoginRequest` class handles the validation of the login form inputs and performs the authentication logic, including rate limiting to protect against brute-force attacks.


When the login credentials are successfully matched, the user is redirected to the admin dashboard page:

![App Logo](/images/dashboard.png)


[🔝 Back to contents](#contents)

## store-publication

`app\Http\Controllers\Dashboard\PublicationController.php`

```php
public function store(Request $request)
{
    $request->validate([
        'project_id'     => ['nullable', 'exists:projects,id'],
        'title'          => ['required', 'array'],
        'title.ar'       => ['required_with:title'],
        'title.en'       => ['required_with:title'],
        'body'           => ['required', 'array'],
        'body.ar'        => ['required_with:body'],
        'body.en'        => ['required_with:body'],
        'image'          => ['image', 'nullable'],
    ]);

    $image = upload_file($request->image, 'image_post', 'posts_images');

    $post = Post::create([
        'title'       => $request->title,
        'body'        => $request->body,
        'image'       => $image,
        'project_id'  => $request->project_id,
        'date'        => $request->date,
    ]);

    if ($request->subscribe) {
        Newsletter::dispatch($post->id);
    }

    return back()->withStatus(__('Post is created successfully'));
}
```

The `store` function validates the request data, uploads the image and files if present, creates a new post, and redirects back with a success message.
It called from the `/latest_news` route, which is defined in the `routes\web.php` file as follows:

```php
Route::Resource('/dashboard/publications', PublicationController::class)->except(['show']);
```

I used various validation rules, such as ensuring that the `title` field is an array, the `title.ar` and `title.en` fields are required when 'title' is present, the `image` field is required and should be an image file, the `project_id` field specifies that the 'project_id' value must exist in the projects table's id column.
 
The function then proceeds to upload the image file using the [upload_file function](#upload-file), which takes the `image` from the request, specifies the file destination directory as `posts_images`, and returns the path of the uploaded image.

If the *$request* object has a truthy value for the `subscribe` field, it executes the `dispatch()` method on the `Newsletter` class, passing *$post->id* as an argument.
- Newsletter::dispatch($post->id): This line call a static method named `dispatch()`
   included with ["Newsletter" job](#newsletter-job) class.

### **upload-file**

`app\helpers.php`

```php
function upload_file($request_file, $prefix, $folder_name)
{
    $extension = $request_file->getClientOriginalExtension();
    $file_to_store = $prefix . '_' . time() . rand(10000, 99999) . '.' . $extension;
    $request_file->storeAs('public/' . $folder_name, $file_to_store);
    return $folder_name . '/' . $file_to_store;
}
```

The `upload_file` function is a utility function that handles file uploads within the application. It performs the following tasks:

1. Takes a file as input and generates a unique filename for storage.
2. Stores the file in the specified folder.
3. Returns the relative path of the uploaded file.

It takes three parameters:

1. `$request_file`: The file obtained from the request.
2. `$prefix`: A prefix string used to create a unique filename.
3. `$folder_name`: The name of the folder where the file will be stored.

The function returns the relative path of the uploaded file. This path is obtained by concatenating the `$folder_name` and the unique filename generated for the file.

The `upload_file` function is placed within the app helper class, which provides common functions for various tasks. These helper functions are globally accessible throughout the application without needing to import or instantiate any specific class.

[🔝 Back to contents](#contents)

### **newsletter-job**

`app\Jobs\Newsletter.php`

Using this command: 
```cmd
php artisan make:job Newsletter
``` 
I created This job

```php
class Newsletter implements ShouldQueue
{
    use Dispatchable, InteractsWithQueue, Queueable, SerializesModels;


    public $post_id;

    /**
     * Create a new job instance.
     *
     * @return void
     */
    public function __construct($post_id)
    {
        $this->post_id = $post_id;
    }

    /**
     * Execute the job.
     *
     * @return void
     */
    public function handle()
    {
        $post = Post::find($this->post_id);
        if(!$post)
            return;

        $news_details = [
            'title'       => $post->title,
            'body'        => $post->body,
            'image'       => $post->image,
        ];

        foreach(Subscription::all() as $subscriber){
            $email = new NewsMail($news_details,$subscriber->token);
            $email->subject(env('APP_NAME').' Newsletter');
            Mail::to($subscriber->email)->send($email);
        }
    }
}
```

The `Newsletter` class represents a queued job in Laravel that is responsible for sending newsletters to all subscribers. It interacts with other classes, such as the `NewsMail` class, to prepare and send the newsletter content.

It has the following functionality:

1. Retrieving Post Details:
   - It retrieves the details of a specific post based on the provided `$post_id`.
   
2. Newsletter Preparation and Sending:
   - It prepares the newsletter content.
   - It sends the newsletter to each subscriber's email address using the `NewsMail` class.

The `handle()` method is the main method of the `Newsletter` job class, and it is executed when the job is processed. Here's how it works:

1. Checking Post Existence:
   - If the post with the given `$post_id` exists, the following steps are performed:
   
2. Iterating over Subscriptions:
   - A `foreach` loop iterates over all `Subscription` models.
   - It assumes that there is a model named `Subscription` and fetches all instances using `Subscription::all()`.

3. Creating and Sending NewsMail:
   - Inside the loop, a new `NewsMail` instance is created, passing the `$news_details` and the subscriber's token.
   - The email subject is set to `'APP_NAME Newsletter'`, assuming that `APP_NAME` is an environment variable.
   - Finally, the email is sent using Laravel's `Mail::to()` method, specifying the subscriber's email and the `NewsMail` instance.

[🔝 Back to contents](#contents)

### **change-settings**

`app\Http\Controllers\FormController.php`

```php
public function change_settings(Request $request)
{
    $request->validate([
        'volunteer_email'   => ['nullable','email'],
        'risk_email'        => ['nullable','email'],
        'join_email'        => ['nullable','email'],
        'enabled_volunteer' => ['nullable','in:0,1'],
        'enabled_risk'      => ['nullable','in:0,1'],
        'enabled_join'      => ['nullable','in:0,1'],
    ]);

    $this->setEnv('APP_VOLUNTEER_ENABLE',$request->enabled_volunteer);
    $this->setEnv('APP_AT_RISK_ENABLE',$request->enabled_risk);
    $this->setEnv('APP_JOIN_ENABLE',$request->enabled_join);

    $this->setEnv('APP_VOLUNTEER_EMAIL',$request->volunteer_email);
    $this->setEnv('APP_AT_RISK_EMAIL',$request->risk_email);
    $this->setEnv('APP_JOIN_EMAIL',$request->join_email);

    return back()->withStatus(__('Settings Successfully Changed'));
}
```

The `change_settings` function is responsible for validating the request data, updating the corresponding environment variables based on the request, and redirecting back with a success message. This functionality allows for dynamic modification of application settings.

The `change_settings` function performs the following tasks:

1. It validates the request data to ensure it meets the required criteria.

2. Updating Environment Variables:
   - It uses the [setEnv method](#setEnv-method) method to update the values of the specified environment variables based on the request data.

3. Redirecting with Success Message:
   - After successfully updating the environment variables, it redirects back to the previous page with a success message.

The `change_settings` function is called from the following route, defined in the `routes\web.php` file:

```php
Route::post('/forms/settings', [FormController::class, 'change_settings'])->name('forms.change_settings');
```

### **setEnv-method**

```php
function setEnv($name, $value)
{
    $path = base_path('.env');
    if (file_exists($path)) {
        file_put_contents($path, str_replace(
            $name . '=' . env($name) , $name . '=' . $value, str_replace(
            $name . '="' . env($name) .'"', $name . '="' . $value.'"', file_get_contents($path)
        )));
    }
}
```

The `setEnv` function is a custom function used to update the value of an environment variable in the `.env` file of the application. It enables dynamic modification of environment variables at runtime.

The `setEnv` function performs the following tasks:

1. Obtaining the `.env` File Path:
   - It obtains the path to the `.env` file using the `base_path()` function.

2. Reading the File Contents:
   - It reads the contents of the `.env` file using the `file_get_contents()` function. This retrieves the current contents of the file as a string.

3. Replacing the Environment Variable Value:
   - Using the `str_replace()` function, the function replaces the existing value of the specified environment variable with the new value.

4. Writing the Modified Contents:
   - The modified contents of the `.env` file are written back to the file using the `file_put_contents()` function. This function overwrites the existing file contents with the updated contents.

[🔝 Back to contents](#contents)

### **add-date-to-posts-table**

`database\migrations\2023_06_04_115214_add_date_to_posts_table.php`

The migration file is responsible for adding a new column called 'date' to the 'posts' table in the database.

```php
.
.
.
Schema::table('posts', function (Blueprint $table) {
    $table->date('date')->nullable()->after('image');
});
DB::statement("UPDATE `posts` SET `date` = `created_at` WHERE 1;");
.
.
.
```

- It modifies the 'posts' table using the `Schema::table()` method.
  
- Inside the callback function, the `$table->date('date')` line adds a new column called 'date' to the 'posts' table. The `date` method specifies the column type as date.
   - The `nullable` method indicates that the column can be left empty.
   - The `after('image')` method specifies that the 'date' column should be added after the 'image' column in the table's column order.

- The `DB::statement()` line executes a direct SQL statement using Laravel's DB facade.
- The SQL statement updates the 'date' column of all rows in the 'posts' table with the value from the 'created_at' column.
- The `WHERE 1` condition matches all rows in the table, indicating that the update should be applied to all rows.

To manage database schema changes and meet the schema requirements, I used this package `laravel-doctrine/dbal`. This package provides functionality for handling database schema modifications in Laravel.

[🔝 Back to contents](#contents)

### **store-subscriber**

`app\Http\Controllers\SubscribeController.php`

```php
public function store(Request $request)
{
    $request->validate([
        'email' => 'required|email',
    ]);

    $token = Str::random(32);

    $subscribe = Subscription::firstOrCreate(['email' => $request->email],[
        'email' => $request->email,
        'token' => $token,
    ]);

    if ($subscribe->wasRecentlyCreated)
        return back()->withStatus(__('User have successfully subscribed!'));

    return back()->withInput()->with('error', 'User are already subscribed!');
}
```

The function uses the `firstOrCreate()` method on the `Subscription` model. This method attempts to find a record in the Subscription table with the given email address. If a record is found, it returns that record; otherwise, it creates a new record with the provided email and token.

[🔝 Back to contents](#contents)

### **exportToExcel**

`app\Http\Controllers\Dashboard\FormController.php`

```php
public function exportToExcel($type)
{
    $fileName = 'applications' . time() . Auth::user()->id . '.xlsx';
    $forms = Form::where('type', $type)->get();

    if($type == 'join_ind')
        return Excel::download(new JoinIndExport($forms), $fileName);
    elseif($type == 'join_org')
        return Excel::download(new JoinOrgExport($forms), $fileName);
    elseif($type == 'risk')
        return Excel::download(new AtRiskExport($forms), $fileName);
    elseif($type == 'volunteer')
        return Excel::download(new VolunteerExport($forms), $fileName);

}
```

The `exportToExcel` method is responsible for generating an Excel file based on the current timestamp and the user's ID. It retrieves data based on the specified type and exports it to an Excel file using the appropriate export class. The method then returns the exported file for download.

The `exportToExcel` method performs the following tasks:

- Generating File Name:
   - It generates a file name for the Excel file based on the current timestamp and the user's ID. The exact implementation of this generation is not provided in the code snippet.

- Exporting Data:
   - It uses the Laravel Excel package (`maatwebsite/excel`) to export the data to an Excel file.
   - The appropriate export class is instantiated, which defines the structure and formatting of the exported data.
   - The export class is passed as the first parameter to the `Excel::download()` method.
   - The generated file name is used as the second parameter to the `Excel::download()` method.

- Returning the Exported File:
   - The `Excel::download()` method returns the exported file for download.

To export data to Excel files, I used `maatwebsite/excel` package. This package provides convenient methods and classes for working with Excel files in Laravel.

[🔝 Back to contents](#contents)

#### forms-api

`app\Http\Controllers\Api\FormController.php`

```php
public function forms(Request $request)
{
    $request->validate([
        'name'           => ['required', 'string'],
        'type'           => ['required', 'in:volunteer,risk,join_ind,join_org'],
        'data'           => ['required', 'array'],
        'data.*'         => ['array'],
        'data.*.name'    => ['required', 'string'],
        'data.*.value'   => ['required'],
    ]);

    if (($request->type == 'join_ind' || $request->type == 'join_org') && env('APP_JOIN_ENABLE', 1) == 0)
        throw new BadRequestException();

    if ($request->type == 'volunteer' && env('APP_VOLUNTEER_ENABLE', 1) == 0)
        throw new BadRequestException();

    if ($request->type == 'risk' && env('APP_AT_RISK_ENABLE', 1) == 0)
        throw new BadRequestException();


    $data = [];
    foreach ($request->data as $ind => $arr) {
        if ($request->hasFile("data.$ind.value"))
            $value = upload_file($arr['value'], 'file', 'applications_files');
        else
            $value = $arr['value'];

        $data[] = [
            'name' => $arr['name'],
            'value' => $value,
        ];
    }

    $data = FormParamResource::collection($data)->toArray($request);
    $data = json_encode($data);

    $form = Form::create([
        'name' => $request->name,
        'data' => $data,
        'type' => $request->type,
    ]);

    $details = [
        'name'   => $request->name,
        'type'   => $request->type,
        'date'   => Carbon::createFromFormat('Y-m-d H:i:s', $form->created_at)->format('Y-m-d H:i:s')
    ];

    $email_address = null;
    if ($request->type == 'join' && env('APP_JOIN_EMAIL') != "") {
        $email_address = env('APP_JOIN_EMAIL');
        $details['subject'] = "Joining request";
    }
    if ($request->type == 'volunteer' && env('APP_VOLUNTEER_EMAIL') != "") {
        $email_address = env('APP_VOLUNTEER_EMAIL');
        $details['subject'] = "Volunteering request";
    }
    if ($request->type == 'risk' && env('APP_AT_RISK_EMAIL') != "") {
        $email_address = env('APP_VOLUNTEER_EMAIL');
        $details['subject'] = "At risk request";
    }

    if ($email_address) {
        $email = new ApplicationMail($details);
        $email->subject($details['subject']);
        Mail::to($email_address)->send($email);
    }

    return response()->json(new FormResource($form), 201);
}
```

The `forms` method in the `FormController` class is responsible for handling a POST request to the `/forms` route in the API:

```php
Route::post('forms', [FormController::class, 'forms'])->name('forms');
```

The `forms` method performs the following tasks:

1. Request Data Validation:
   - It validates the incoming request data using the `validate` method. The validation rules are defined for various fields such as 'name', 'type', 'data', 'data.*.name', and 'data.*.value'. These rules ensure that the required fields are present and in the expected format.

2. Environment Checks:
   - It checks the environment variables and the request type to determine if certain functionality is enabled.
   - If the 'join_ind' or 'join_org' type is requested and the 'APP_JOIN_ENABLE' environment variable is set to 0, it throws a `BadRequestException`.
   - If the 'volunteer' type is requested and the 'APP_VOLUNTEER_ENABLE' environment variable is set to 0, it throws a `BadRequestException`.
   - If the 'risk' type is requested and the 'APP_AT_RISK_ENABLE' environment variable is set to 0, it throws a `BadRequestException`.

3. Processing Request Data:
   - It iterates over each element in the 'data' array of the request.
   - For each element, it checks if a file is present in the request using the `hasFile` method.
   - If a file is present, it uploads the file using the `upload_file()` function and stores the file path in the `$value` variable.
   - If a file is not present, it assigns the value to the `$value` variable from the request's data.
   - with this part:   $data[] = [
                                'name' => $arr['name'],
                                'value' => $value,
                              ];
     It appends an array to the `$data` array, containing the 'name' and 'value' extracted from the request.

1. JSON Transformation and Form Creation:
   - The `$data` array is transformed into a collection using the `FormParamResource` resource class and converted to an array using the `toArray()` method.
   - The transformed data is then encoded into JSON format using `json_encode()`.
   - A new `Form` model instance is created in the database with the provided 'name', 'data', and 'type' values.

2. Email Notification:
   - If an email address is found based on the request type and the respective environment variable is not empty, an `ApplicationMail` instance is created with the details array.
   - The email subject is set based on the request type.
   - The email is sent using Laravel's `Mail::to()` method, with the email address and the `ApplicationMail` instance.

3. Returning Response:
   - The method returns a JSON response with the newly created `Form` resource using the `FormResource` class. The response has a status code of 201 (Created).

#### Example Usage

To create a new form entry, you would typically send a POST request to the `/forms` route in the API, providing the required data in the request payload. The method performs validation, processes the data, creates a new form entry, and sends email notifications if applicable. Finally, it returns a JSON response with the created form data.

Make sure to define the necessary routes and configure the environment variables (`APP_JOIN_ENABLE`, `APP_VOLUNTEER_ENABLE`, `APP_AT_RISK_ENABLE`, etc.) accordingly to enable or disable specific functionality based on your requirements.

[🔝 Back to contents](#contents)

#### latest-news

`app\Http\Controllers\Api\PostController.php`

```php
public function latest_news(Request $request)
{
    $request->validate([
        'post_id' => ['exists:posts,id'],
    ]);

    $q = Post::orderBy('date','desc')->take(3);

    if ($request->post_id)
    {
        $post = Post::find($request->post_id);
        $q->where('project_id', $post->project_id);
        $q->where('id', '!=', $post->id);
    }

    $posts = $q->get();

    return PostResource::collection($posts);
}
```
The `latest_news` method in the `PostController` class is responsible for retrieving the latest news posts from the database and returning them as a collection of `PostResource` objects.

It called from this route that I defined into `routes\api.php` file:

```php
Route::get('/latest_news', [PostController::class, 'latest_news'])->name('posts.latest_news');
```

The `latest_news` method performs the following tasks:

1. Request Data Validation:
   - It validates the incoming request data using the `validate` method.
   - The validation rules specify that the `post_id` field must exist in the `posts` table's `id` column.

2. Querying Latest News:
   - It constructs a query to retrieve the latest news posts from the `Post` model.
   - The query is ordered by the `date` column in descending order, ensuring that the newest posts appear first.
   - It limits the number of results to three using the `take` method.

3. Filtering Results (Optional):
   - If a `post_id` is provided in the request, it further filters the results to exclude the post with that ID.
   - It retrieves the post object corresponding to the provided `post_id` using the `find` method on the `Post` model.
   - It adds additional conditions to the query to select posts with the same project ID (`$post->project_id`) and exclude the post with the provided ID (`$post->id`).

4. Retrieving Results:
   - The query results are retrieved using the `get` method, which returns a collection of `Post` objects.

5. Returning Response:
   - The method transforms the collection of `Post` objects into a collection of `PostResource` objects using the `PostResource::collection` method.
   - It returns the transformed collection as the response.


[🔝 Back to contents](#contents)

### **contact-management**

Contact management, handles contact form submissions, validates user input, sends customized email notifications. 

```php
Route::post('contact', [ContactController::class, 'contact']);
```
First, I defined 'contact' API route to handle the submission of a contact form. It listens for a *'/contact'* URL and maps it to the 'contact' method of the *'ContactController'* class.

```php
class ContactController extends Controller
{
    public function contact(Request $request)
    {
        // Validation rules for the incoming request data
        $request->validate([
            'name'           => ['required', 'string'],
            'email'          => ['required', 'string','email'],
            'phone_number'   => ['required', 'numeric'],
            'message'        => ['required', 'string'],
        ]);

        // Extracting the user details from the request
        $user_details = [
            'name'         => $request->name,
            'email'        => $request->email,
            'phone_number' => $request->phone_number,
            'message'      => $request->message,
        ];

        // Creating a new instance of the ContactMail class and passing user details
        $email = new ContactMail($user_details);
        $email->subject('Contact Message');

        // Sending the email using the Mail facade and specifying the recipient
        Mail::to(env('MAIL_TO'))->send($email);

        // Returning a JSON response with the user details
        return response()->json($user_details, 200);
    }
}
```

To ensure successful email sending, I took the following steps:

1. Configured Mail Driver: In the `.env` file, I set the `MAIL_MAILER` parameter to the desired mail driver, such as 'smtp'.

2. Set Recipient Email: In the `.env` file, I replaced the placeholder value for `MAIL_TO` with the actual email address where I wanted to receive the contact messages.

3. Configured Email Template: I created an email template or used an existing one to format the content of the email. This template includes the necessary information such as the *subject*, *sender* *details*, and the *message content*.

4. Used the 'Mail' facade: To send the email, I utilized the 'Mail' facade provided by Laravel. I invoked the `send` method on the `Mail` facade and passed it an instance of the [ContactMail](#ContactMail) class (which represents the email to be sent). The recipient email address is specified through the `MAIL_TO` environment variable.

By following these steps, I ensured that the email sending process was properly configured and ready to send emails to the desired recipient using the specified mail driver.

### **ContactMail**

`app\Mail\ContactMail.php`

```php
class ContactMail extends Mailable
{
    use Queueable, SerializesModels;

    public $user_details = [];

    public function __construct($user_details)
    {
        $this->user_details = $user_details;
    }

    public function build()
    {
        return $this->view('mails.contact');
    }
}
```
The *$user_details* property is a public variable that holds the user details passed from the controller.

The `build` method is called internally when the email needs to be prepared for sending. It returns the view `mails.contact` that will be used as the email's body.

`resources\views\mails\contact.blade.php`

```html
<div style="margin: 0; font-family: 'Montserrat', sans-serif;">
    <div class="container">
        <div class="email-head" style="align-items: center; background: #F2F2F2; width: 100%;height:130px;">
            <div style="float:left; max-width: 100%;">
                <img class="logo-image" src="{{ env('APP_URL')}}/img/aim.png" style="width: 100px;margin-top:12px;margin-left:12px">
            </div>
            <div class="title" style="font-weight: 700; font-size: 24px; line-height: 29px; color: #BD171C; float:right; width: 400px; max-width: 100%;">
                <h3 style="width:fit-content; margin-left: auto;margin-right: auto;">Alliance of Iraqi Minorities</h3>
            </div>
        </div>
        <div class="email-body" style="padding: 0 50px;font-size: 20px; line-height: 24px; color: black;">
            <p style="font-size:22px;font-weight: 600; text-align: center;">Contact Message</p>
            <div>
                <p><b>Name:</b> {{ $user_details['name'] }}</p>
                <p><b>Email:</b> {{ $user_details['email'] }}</p>
                <p><b>Phone Number:</b> {{ $user_details['phone_number'] }}</p>
                <p>
                    <b>Contact Message:</b><br>
                    {{ $user_details['message'] }}
                </p>
            </div>
        </div>
        <div class="email-footer" style="padding: 15px 30px; background: #BD171C; text-align: center; font-size: 20px; line-height: 24px;">
            <p class="txt" style="font-weight: 600; color: white; text-align: center;">
                Don’t want to see similar emails again?<br>
                <a class="link-txt" href="{{ env('WEBSITE_URL')}}" target="_blank" style="font-weight: 400; color: white;">Click here to unsubscribe from all emails of our organization</a>
            </p>
        </div>
    </div>
</div>
```
The template includes a header section with the organization's logo and name.
The body section displays the contact message details (name, email, phone number, and the message itself). The dynamic data is fetched from the *$user_details* array using Blade syntax.
The footer section includes an unsubscribe link.

- Header Section (`email-head`):
   - Contains the organization's logo (`aim.png`) on the left side.
   - Displays the organization's name ("Alliance of Iraqi Minorities") on the right side.

- Body Section (`email-body`):
   - Contains the contact message details:
     - Name: Retrieved from the `$user_details['name']` variable.
     - Email: Retrieved from the `$user_details['email']` variable.
     - Phone Number: Retrieved from the `$user_details['phone_number']` variable.
     - Contact Message: The actual message content retrieved from `$user_details['message']`.

- Footer Section (`email-footer`):
   - Provides an option to unsubscribe from similar emails.
   - Includes a link that points to the `WEBSITE_URL` value stored in the `.env` file. This link opens in a new tab (`target="_blank"`).

[🔝 Back to contents](#contents)

### **Ckeditor**

The `CKEditor` library is used to enhance the text editing capabilities of the contact form's description field. `CKEditor` is a popular WYSIWYG (What You See Is What You Get) text editor that allows users to format and style their text easily.

![App Logo](/images/Ckeditor.png)

`resources\views\dashboard\publication_create.blade.php`

```html
<x-layouts.dashboard>
.
.
.
    <x-slot name="script">
        <script src="https://cdn.ckeditor.com/4.20.2/standard/ckeditor.js"></script>
    </x-slot>
.
.
.

    <form method="post" action="{{ route('publications.store') }}" autocomplete="off" class="form-horizontal"
        enctype="multipart/form-data">
        @csrf
.
.
.
        <div class="row">
            <label class="col-sm-3 col-form-label" for="input-description-en">{{ __('Description [en]') }}</label>
            <div class="col-sm-8">
                <div class="form-group{{ $errors->has('description[en]') ? ' has-danger' : '' }}">
                    <textarea class="ckeditor form-control{{ $errors->has('description[en]') ? ' is-invalid' : '' }}"
                        name="description[en]" id="input-description-en" placeholder="{{ __('Enter Description En') }}"
                        aria-required="true">{!! old('description.en') !!}</textarea>
                    @if ($errors->has('description[en]'))
                        <span id="description-en-error" class="error text-danger"
                            for="input-description-en">{{ $errors->first('description[en]') }}</span>
                    @endif
                </div>
            </div>
        </div>
.
.
.
        <button type="submit" class="btn btn-success">{{ __('Save') }}</button>
    </form>
</x-layouts.dashboard>
<script>
    var uploadUrl = "{{ route('upload') }}";
    var csrfToken = "{{ csrf_token() }}";

    CKEDITOR.replace('input-description-en', {
        filebrowserUploadUrl: uploadUrl + "?_token=" + csrfToken,
        filebrowserUploadMethod: 'form'

    });
</script>
<script>
    var uploadUrl = "{{ route('upload') }}";
    var csrfToken = "{{ csrf_token() }}";

    CKEDITOR.replace('input-description-ar', {
        filebrowserUploadUrl: uploadUrl + "?_token=" + csrfToken,
        filebrowserUploadMethod: 'form'

    });
</script>
```

I included CKEditor library by adding a script tag that references the CKEditor JavaScript file from the CDN (`https://cdn.ckeditor.com/4.20.2/standard/ckeditor.js`).

Inside the form, a textarea field is defined for the English description. It has the name attribute `description[en]` and the ID `input-description-en`. The CKEditor class is applied to the textarea, making it a CKEditor instance.

The CKEditor instance is initialized using the `CKEDITOR.replace` function. It takes the ID of the textarea (`input-description-en`) as the first argument and an options object as the second argument. The options object includes the following properties:
   - `filebrowserUploadUrl`: Specifies the URL where the uploaded file will be sent for processing. In this case, it uses the `uploadUrl` variable concatenated with the CSRF token (`_token`) for security.
   - `filebrowserUploadMethod`: Specifies the method to use when uploading the file. The value `'form'` indicates that the file will be uploaded via form submission.

In the route file (`routes/web.php`), there is a route defined for handling the file upload. It specifies that when a POST request is made to the `ckeditor/image_upload` endpoint, it should be handled by the [upload method](#upload-image(CKEditor)) of the `CkeditorController` class. The route is named `upload` using the `name` method.
```php
Route::post('ckeditor/image_upload', [CkeditorController::class, 'upload'])->name('upload');
```
### **upload-image(CKEditor)**

`app\Http\Controllers\Dashboard\CkeditorController.php`

```php
public function upload(Request $request)
{
    if($request->hasFile('upload')) {

        $filenamewithextension = $request->file('upload')->getClientOriginalName();
        $filename = pathinfo($filenamewithextension, PATHINFO_FILENAME);
        $extension = $request->file('upload')->getClientOriginalExtension();
        $filenametostore = $filename.'_'.time().'.'.$extension;

        //Upload File
        $request->file('upload')->storeAs('public/uploads', $filenametostore);

        $CKEditorFuncNum = $request->input('CKEditorFuncNum');
        $url = asset('storage/uploads/'.$filenametostore);
        $msg = 'Image successfully uploaded';
        $re = "<script>window.parent.CKEDITOR.tools.callFunction($CKEditorFuncNum, '$url', '$msg')</script>";

        // Render HTML output
        @header('Content-type: text/html; charset=utf-8');
        echo $re;

    }
}
```
The *'upload'* method handles the file upload functionality. When a file is uploaded, it is stored in the specified directory. The CKEditor function number and the URL of the uploaded file are then returned as a JavaScript snippet, which is echoed back to the CKEditor instance in blade page. This allows CKEditor to display the uploaded image in the editor.

[🔝 Back to contents](#contents)

### **add-subscriber-modal**

![App Logo](/images/subscriber-modal.png)

resources\views\dashboard\subscribers.blade.php:

```html
<button type="button" class="btn btn-success" style="margin-left: auto;display:block;" data-toggle="modal"
    data-target="#addModal"> {{ __('Add New Subscriber') }} </button>
<!-- Add Modal -->
<div class="modal fade" id="addModal" tabindex="-1" role="dialog" aria-labelledby="addModalTitle"
    aria-hidden="true">
    <div class="modal-dialog modal-dialog-centered" role="document">
        <div class="modal-content">
            <div class="modal-header">
                <h5 class="modal-title" id="addModalLabel">Add Subscriber</h5>
                <button type="button" class="close" data-dismiss="modal" aria-label="Close">
                    <span aria-hidden="true">&times;</span>
                </button>
            </div>
            <div class="modal-body">
                <div id="add-error" class="alert alert-danger" style="color: red;display:none;">The entered values
                    ​​are not valid</div>
                <form method="post" action="{{ route('subscribers.store') }}" autocomplete="off"
                    class="form-horizontal" enctype="multipart/form-data">
                    @csrf
                    <div class="row">
                        <label class="col-sm-3 col-form-label" for="input-email">{{ __('Email') }}</label>
                        <div class="col-sm-8">
                            <div class="form-group{{ $errors->has('email') ? ' has-danger' : '' }}">
                                <input class="form-control{{ $errors->has('email') ? ' is-invalid' : '' }}"
                                    name="email" id="input-email" type="text"
                                    placeholder="{{ __(' Enter Email') }}" value="{{ old('email') }}"
                                    aria-required="true" />
                                @if ($errors->has('email'))
                                    <span id="email-error" class="error text-danger"
                                        for="input-email">{{ $errors->first('email') }}</span>
                                @endif
                            </div>
                        </div>
                    </div>
                    <div class="modal-footer">
                        <button type="submit" class="btn btn-success">Add</button>
                    </div>
                </form>
            </div>
        </div>
    </div>
</div>
```

[🔝 Back to contents](#contents)

### **loadImage**

![App Logo](/images/loadImage.png)

`resources\views\dashboard\media_create.blade.php`

```html
<x-layouts.dashboard>
    .
    .
    .
    <x-slot name="head">
        <script>
            var loadImage = function(event) {
              var output = document.getElementById('image');
              output.src = URL.createObjectURL(event.target.files[0]);
              output.style.display = 'inline-block';
                    output.src = URL.createObjectURL(event.target.files[0]);
                    output.onload = function() {
                        URL.revokeObjectURL(output.src)
                    }
            };
        </script>
    </x-slot>
    <form method="post" action="{{ route('media.store') }}" autocomplete="off" class="form-horizontal" enctype="multipart/form-data">
        @csrf
        .
        .
        .
        <div class="row">
            <label class="col-sm-3 col-form-label" for="input_image">{{ __('Image') }}</label>
            <div class="col-sm-8">
            <div class="form-group{{ $errors->has('file') ? ' has-danger' : '' }}">
                <input type="file" onchange="loadImage(event)" class="form-control{{ $errors->has('image') ? ' is-invalid' : '' }}" name="file" id="input_image"/>
                <label for="input_image" class="btn btn-primary mt-5 ml-3">Select Image</label>
                @if ($errors->has('image'))
                <span id="image_error" class="error text-danger" for="input_image">{{ $errors->first('image') }}</span>
                @endif
                &nbsp;&nbsp;
                <img id="image" class="rounded" style="height:100px;width:100px;object-fit: cover; display:none">
            </div>
            </div>
        </div>
        <button type="submit"  class="btn btn-success">{{ __('Save') }}</button>
    </form>
    .
    .
    .
</x-layouts.dashboard>
```
The `loadImage` function is responsible for loading and displaying an image selected by the user in an HTML `<input type="file">` element.

1. When the user selects an image file using the file input, the `onchange` event triggers the `loadImage` function.
2. The function receives the event object as a parameter.
3. It starts by retrieving the file input element's value and assigning it to the `output` variable, which represents an `<img>` element that will display the selected image.
4. The `URL.createObjectURL()` method is used to generate a temporary URL for the selected image file. This URL is set as the `src` attribute of the `output` element, which displays the image in the browser.
5. The `output.style.display` property is set to `'inline-block'`, making the image visible.
6. The `URL.createObjectURL()` method is called again to set the `src` attribute of the `output` element. This is done to ensure that the `output.onload` event fires reliably in all browsers.
7. The `output.onload` event is triggered when the image finishes loading. Inside this event handler, the `URL.revokeObjectURL()` method is called to release the temporary URL created by `URL.createObjectURL()`. This helps free up memory resources used by the browser.

[🔝 Back to contents](#contents)