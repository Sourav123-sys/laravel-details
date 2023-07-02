# Task 1 Create a new migration file to add a new table named "categories" to the database. The table should have the following columns: id (primary key, auto-increment) name (string) created_at (timestamp) updated_at (timestamp)

<?php

use Illuminate\Database\Migrations\Migration;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Support\Facades\Schema;

class CreateCategoriesTable extends Migration
{
   
    public function up()
    {
        Schema::create('categories', function (Blueprint $table) {
            $table->id();
            $table->string('name');
            $table->timestamps();
        });
    }

   
    public function down()
    {
        Schema::dropIfExists('categories');
    }
}
php artisan make:migration create_categories_table --create=categories
php artisan migrate

# Task 2  Create a new model named "Category" associated with the "categories" table. Define the necessary properties and relationships.

<?php

namespace App;

use Illuminate\Database\Eloquent\Model;

class Category extends Model
{
    /**
     * The table associated with the model.
     *
     * @var string
     */
    protected $table = 'categories';

    /**
     * The attributes that are mass assignable.
     *
     * @var array
     */
    protected $fillable = ['name'];

    /**
     * The attributes that should be mutated to dates.
     *
     * @var array
     */
    protected $dates = ['created_at', 'updated_at'];
}

# Task 3 Write a migration file to add a foreign key constraint to the "posts" table. The foreign key should reference the "categories" table on the "category_id" column.

<?php

use Illuminate\Database\Migrations\Migration;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Support\Facades\Schema;

class AddForeignKeyToPostsTable extends Migration
{
    /**
     * Run the migrations.
     *
     * @return void
     */
    public function up()
    {
        Schema::table('posts', function (Blueprint $table) {
            $table->unsignedBigInteger('category_id');

            $table->foreign('category_id')
                ->references('id')
                ->on('categories')
                ->onDelete('cascade');
        });
    }

    /**
     * Reverse the migrations.
     *
     * @return void
     */
    public function down()
    {
        Schema::table('posts', function (Blueprint $table) {
            $table->dropForeign(['category_id']);
            $table->dropColumn('category_id');
        });
    }
}
php artisan make:migration add_foreign_key_to_posts_table --table=posts
php artisan migrate


# Task 4 Create a relationship between the "Post" and "Category" models. A post belongs to a category, and a category can have multiple posts.
<?php

namespace App;

use Illuminate\Database\Eloquent\Model;

class Post extends Model
{
  

 
    public function category()
    {
        return $this->belongsTo(Category::class);
    }
}

<?php

namespace App;

use Illuminate\Database\Eloquent\Model;

class Category extends Model
{
  

   
    public function posts()
    {
        return $this->hasMany(Post::class);
    }
}


$post = Post::find(1);
$category = $post->category;


$category = Category::find(1);
$posts = $category->posts;

# Task 5 Write a query using Eloquent ORM to retrieve all posts along with their associated categories. Make sure to eager load the categories to optimize the query.

$posts = Post::with('category')->get();
foreach ($posts as $post) {
    echo "Post Title: " . $post->title . "\n";
    echo "Category Name: " . $post->category->name . "\n";
   
    echo "\n";
}

# Task 6 Implement a method in the "Post" model to get the total number of posts belonging to a specific category. The method should accept the category ID as a parameter and return the count.

<?php

namespace App;

use Illuminate\Database\Eloquent\Model;

class Post extends Model
{
    

    /**
   
     * @param  int  $categoryId
     * @return int
     */
    public static function getPostsCountByCategory($categoryId)
    {
        return self::where('category_id', $categoryId)->count();
    }
}
$categoryId = 1;
$postsCount = Post::getPostsCountByCategory($categoryId);

echo "Total posts for category with ID $categoryId: " . $postsCount;


# Task 7 Create a new route in the web.php file to handle the following URL pattern: "/posts/{id}/delete". Implement the corresponding controller method to delete a post by its ID. Soft delete should be used.
use App\Http\Controllers\PostController;

Route::delete('/posts/{id}/delete', [PostController::class, 'destroy']);
<?php

namespace App\Http\Controllers;

use App\Post;

class PostController extends Controller
{
   

    /**
   
     *
     * @param  int  $id
     * @return \Illuminate\Http\Response
     */
    public function destroy($id)
    {
        $post = Post::findOrFail($id);
        $post->delete();

        return redirect()->back()->with('message', 'Post deleted successfully.');
    }
}

# Task 8 Implement a method in the "Post" model to get all posts that have been soft deleted. The method should return a collection of soft deleted posts.
<?php

namespace App;

use Illuminate\Database\Eloquent\Model;
use Illuminate\Database\Eloquent\SoftDeletes;

class Post extends Model
{
    use SoftDeletes;

    

    /**
 
     *
     * @return \Illuminate\Database\Eloquent\Collection
     */
    public static function getSoftDeletedPosts()
    {
        return self::onlyTrashed()->get();
    }
    

}




# Task 9 Write a Blade template to display all posts and their associated categories. Use a loop to iterate over the posts and display their details.
<!DOCTYPE html>
<html>
<head>
    <title>All Posts</title>
</head>
<body>
    <h1>All Posts</h1>

    <table>
        <thead>
            <tr>
                <th>Title</th>
                <th>Category</th>
            </tr>
        </thead>
        <tbody>
            @foreach ($posts as $post)
                <tr>
                    <td>{{ $post->title }}</td>
                    <td>{{ $post->category->name }}</td>
                </tr>
            @endforeach
        </tbody>
    </table>
</body>
</html>
use App\Post;

public function index()
{
    $posts = Post::with('category')->get();
    return view('posts', compact('posts'));
}


# Task 10 Create a new route in the web.php file to handle the following URL pattern: "/categories/{id}/posts". Implement the corresponding controller method to retrieve all posts belonging to a specific category. The category ID should be passed as a parameter to the method.
use App\Http\Controllers\CategoryController;

Route::get('/categories/{id}/posts', [CategoryController::class, 'getPostsByCategory']);

<?php

namespace App\Http\Controllers;

use App\Category;

class CategoryController extends Controller
{
    // ...

    /**
     * Retrieve all posts belonging to a specific category.
     *
     * @param  int  $id
     * @return \Illuminate\Http\Response
     */
    public function getPostsByCategory($id)
    {
        $category = Category::findOrFail($id);
        $posts = $category->posts;

        return view('category.posts', compact('category', 'posts'));
    }
}

<!DOCTYPE html>
<html>
<head>
    <title>Posts by Category</title>
</head>
<body>
    <h1>Posts by Category: {{ $category->name }}</h1>

    <ul>
        @foreach ($posts as $post)
            <li>{{ $post->title }}</li>
        @endforeach
    </ul>
</body>
</html>

# Task 11 Implement a method in the "Category" model to get the latest post associated with the category. The method should return the post object.


<?php

namespace App;

use Illuminate\Database\Eloquent\Model;

class Category extends Model
{
    // ...

    /**
     * Get the latest post associated with the category.
     *
     * @return \Illuminate\Database\Eloquent\Relations\HasOne
     */
    public function latestPost()
    {
        return $this->hasOne(Post::class)->latest();
    }
}
$category = Category::find(1);
$latestPost = $category->latestPost;

# Task 12 Write a Blade template to display the latest post for each category. Use a loop to iterate over the categories and display the post details.

<!DOCTYPE html>
<html>
<head>
    <title>Latest Posts for Each Category</title>
</head>
<body>
    <h1>Latest Posts for Each Category</h1>

    @foreach ($categories as $category)
        <h2>{{ $category->name }}</h2>

        @if ($category->latestPost)
            <p>Title: {{ $category->latestPost->title }}</p>
            <p>Content: {{ $category->latestPost->content }}</p>
            <hr>
        @else
            <p>No posts found for this category.</p>
        @endif
    @endforeach
</body>
</html>
use App\Category;

public function index()
{
    $categories = Category::with('latestPost')->get();
    return view('latest-posts', compact('categories'));
}




