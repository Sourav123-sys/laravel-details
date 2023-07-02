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





