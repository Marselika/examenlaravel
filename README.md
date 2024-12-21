# Examen
## Arhitectura aplicatie
### Modele
- Category.php
- MenuItem.php

### Controllers
- CategoryController.php
- MenuItemController.php

### Views
- /categories/create.blade.php
- /categories/index.blade.php

### Schema bazei de date
![image](https://github.com/user-attachments/assets/61129600-57f5-43ca-8075-c7373a484308)

### Tipuri de stocare
- Baza de date, in baza de date noi stocam informatia despre categoriile meniului si insusi meniul cu poze, descriere, denumire.

### Date pentru cache
- Lista categoriilor (refresh la 24h)
- Meniurile populare (refresh la 1h)

### Stack-ul tehnologic utilizat
- Laravel 10.x
- PHP 8.1+
- MySQL
- Bootstrap pentru frontend
- XAMPP ca server local

### Comenzi Utilizate
- composer create-project laravel/laravel menu-app
- php artisan make:model Category -m
- php artisan make:model MenuItem -m
- php artisan make:controller CategoryController --resource
- php artisan make:controller MenuItemController --resource
- php artisan migrate
- php artisan serv
- php artisan make:seeder CategorySeeder

### Metodele
```php
    public function create()
    {
        return view('categories.create');
    }

    public function store(Request $request)
    {
        $validated = $request->validate([
            'name' => 'required|max:255',
            'description' => 'nullable|string'
        ]);

        DB::table('categories')->insert([
            'name' => $validated['name'],
            'description' => $validated['description'],
            'created_at' => now(),
            'updated_at' => now()
        ]);

        return redirect()->route('categories.index')->with('success', 'Categorie adăugată cu succes!');
    }

    public function destroy($id)
    {
        DB::table('categories')->where('id', $id)->delete();
        return redirect()->route('categories.index')->with('success', 'Categorie ștearsă cu succes!');
    }

    public function show($id)
    {
        $category = DB::table('categories')->where('id', $id)->first();
        return view('categories.show', compact('category'));
    }
```

### Validarea datelor
```php
        $validated = $request->validate([
            'name' => 'required|max:255',
            'description' => 'nullable|string'
        ]);
```

### Vizualizarea
```html
<!DOCTYPE html>
<html>
<head>
    <title>Adaugă Categorie</title>
</head>
<body>
    <div class="container mt-5">
        <h1>Adaugă Categorie Nouă</h1>

        @if ($errors->any())
            <div class="alert alert-danger">
                <ul>
                    @foreach ($errors->all() as $error)
                        <li>{{ $error }}</li>
                    @endforeach
                </ul>
            </div>
        @endif

        <form action="{{ route('categories.store') }}" method="POST">
            @csrf
            <div class="mb-3">
                <label for="name" class="form-label">Nume</label>
                <input type="text" class="form-control" id="name" name="name" required>
            </div>
            <div class="mb-3">
                <label for="description" class="form-label">Descriere</label>
                <textarea class="form-control" id="description" name="description"></textarea>
            </div>
            <button type="submit" class="btn btn-primary">Salvează</button>
            <a href="{{ route('categories.index') }}" class="btn btn-secondary">Înapoi</a>
        </form>
    </div>
</body>
</html>
```

### Modele Cod
```php
<?php

namespace App\Models;

use Illuminate\Database\Eloquent\Model;

class Category extends Model
{
    protected $fillable = ['name', 'description'];

    public function menuItems()
    {
        return $this->hasMany(MenuItem::class);
    }
}

```
```php
<?php

namespace App\Models;

use Illuminate\Database\Eloquent\Model;

class MenuItem extends Model
{
    protected $fillable = ['category_id', 'name', 'description', 'price', 'image_path'];

    public function category()
    {
        return $this->belongsTo(Category::class);
    }
}
```


### Migratie Cod
```php
<?php

use Illuminate\Database\Migrations\Migration;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Support\Facades\Schema;

return new class extends Migration
{
    /**
     * Run the migrations.
     */
    public function up()
    {
        Schema::create('menu_items', function (Blueprint $table) {
            $table->id();
            $table->foreignId('category_id')->constrained()->onDelete('cascade');
            $table->string('name');
            $table->text('description')->nullable();
            $table->decimal('price', 8, 2);
            $table->string('image_path')->nullable();
            $table->timestamps();
        });
    }

    /**
     * Reverse the migrations.
     */
    public function down(): void
    {
        Schema::dropIfExists('menu_items');
    }
};
```

### Rute Cod
```php
<?php

use Illuminate\Support\Facades\Route;
use App\Http\Controllers\CategoryController;
use App\Http\Controllers\MenuItemController;

Route::get('/', function () {
    return redirect('/categories');
});

Route::resource('categories', CategoryController::class);
Route::resource('menu-items', MenuItemController::class);
```
### Screen aplicatie
![image](https://github.com/user-attachments/assets/2efc5459-fda1-4525-8693-bbf61e4c4c52)
![image](https://github.com/user-attachments/assets/6a5e028b-c2a0-44e7-9455-93dadf0ac461)


