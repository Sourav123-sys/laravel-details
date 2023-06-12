# Task 1: Explain what Laravel's query builder is and how it provides a simple and elegant way to interact with databases.

Laravel's query builder is a feature that allows developers to build database queries using a fluent, expressive syntax in PHP. It provides a simple and elegant way to interact with databases by abstracting the underlying SQL code and providing a more intuitive and readable API.

The query builder in Laravel provides a set of methods that represent different parts of a typical SQL query, such as selecting columns, specifying conditions, joining tables, sorting results, and aggregating data. These methods can be chained together to construct complex queries in a concise and readable manner.

# Task 2:.Write the code to retrieve the "excerpt" and "description" columns from the "posts" table using Laravel's query builder. Store the result in the $posts variable. Print the $posts variable.

$posts = DB::table('posts')
            ->select('excerpt', 'description')
            ->get();

print_r($posts);

# Task 3 :Describe the purpose of the distinct() method in Laravel's query builder. How is it used in conjunction with the select() method?

The distinct() method in Laravel's query builder is used to retrieve unique records from a table by eliminating any duplicate rows. It ensures that only distinct values are returned for the specified columns.

When used in conjunction with the select() method, the distinct() method modifies the query to apply the uniqueness constraint on the specified columns. It ensures that only unique combinations of values for those columns are included in the result set.

# Task 4 :Write the code to retrieve the first record from the "posts" table where the "id" is 2 using Laravel's query builder. Store the result in the $posts variable. Print the "description" column of the $posts variable.
$posts = DB::table('posts')
            ->where('id', 2)
            ->first();

if ($posts) {
    echo $posts->description;
} else {
    echo "No posts found.";
}
# Task 5 :.Write the code to retrieve the "description" column from the "posts" table where the "id" is 2 using Laravel's query builder. Store the result in the $posts variable. Print the $posts variable.
$posts = DB::table('posts')
            ->where('id', 2)
            ->pluck('description');

print_r($posts);

# Task 6 :Explain the difference between the first() and find() methods in Laravel's query builder. How are they used to retrieve single records?

first() method is used to retrieve the first record that matches the specified conditions. It returns an instance of the stdClass class, which is an anonymous PHP object representing the retrieved record.

find() method is used to retrieve a record by its primary key. It expects the primary key value as an argument and returns a single record that matches the provided primary key.

# Task 7 :Write the code to retrieve the "title" column from the "posts" table using Laravel's query builder. Store the result in the $posts variable. Print the $posts variable.
$posts = DB::table('posts')
            ->pluck('title');

print_r($posts);

# Task 8 :Write the code to insert a new record into the "posts" table using Laravel's query builder. Set the "title" and "slug" columns to 'X', and the "excerpt" and "description" columns to 'excerpt' and 'description', respectively. Set the "is_published" column to true and the "min_to_read" column to 2. Print the result of the insert operation.
$result = DB::table('posts')->insert([
    'title' => 'X',
    'slug' => 'X',
    'excerpt' => 'excerpt',
    'description' => 'description',
    'is_published' => true,
    'min_to_read' => 2
]);

print_r($result);


# Task 9 : Write the code to update the "excerpt" and "description" columns of the record with the "id" of 2 in the "posts" table using Laravel's query builder. Set the new values to 'Laravel 10'. Print the number of affected rows.

$affectedRows = DB::table('posts')
                ->where('id', 2)
                ->update([
                    'excerpt' => 'Laravel 10',
                    'description' => 'Laravel 10'
                ]);

echo "Number of affected rows: " . $affectedRows;

# Task 10 : Write the code to delete the record with the "id" of 3 from the "posts" table using Laravel's query builder. Print the number of affected rows.
$affectedRows = DB::table('posts')
                ->where('id', 3)
                ->delete();

echo "Number of affected rows: " . $affectedRows;

# Task 11 :.Explain the purpose and usage of the aggregate methods count(), sum(), avg(), max(), and min() in Laravel's query builder. Provide an example of each.


The count() method returns the number of records that match a specific condition. It is used to count the rows in a table or the number of rows that satisfy certain criteria.
The sum() method calculates the sum of values in a specific column. It is commonly used to find the total of a numeric column.
The avg() method calculates the average value of a specific column. It is useful for finding the average of numeric data.
 The max() method retrieves the maximum value from a specific column. It is used to find the highest value in a column.
 The min() method retrieves the minimum value from a specific column. It is used to find the lowest value in a column.

 # Task 12 : Describe how the whereNot() method is used in Laravel's query builder. Provide an example of its usage.

 The whereNot() method takes two arguments: the column name and the value to compare against. It adds a condition to the query stating that the specified column should not be equal to the provided value.

 # Task 13 : Explain the difference between the exists() and doesntExist() methods in Laravel's query builder. How are they used to check the existence of records?
 The exists() method checks if there are any records in the table that match the specified condition. It returns a boolean value indicating whether any matching records exist or not.
  The doesntExist() method checks if there are no records in the table that match the specified condition. It returns a boolean value indicating whether no matching records exist or not.

  # Task 14 :Write the code to retrieve records from the "posts" table where the "min_to_read" column is between 1 and 5 using Laravel's query builder. Store the result in the $posts variable. Print the $posts variable.

  $posts = DB::table('posts')
            ->whereBetween('min_to_read', [1, 5])
            ->get();

print_r($posts);

# Task 15 : Write the code to increment the "min_to_read" column value of the record with the "id" of 3 in the "posts" table by 1 using Laravel's query builder. Print the number of affected rows.

$affectedRows = DB::table('posts')
                ->where('id', 3)
                ->increment('min_to_read', 1);

echo "Number of affected rows: " . $affectedRows;




