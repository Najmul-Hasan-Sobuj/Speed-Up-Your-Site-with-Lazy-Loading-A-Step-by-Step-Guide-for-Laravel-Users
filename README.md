# Lazy Loading

Improve the efficiency of your website with our comprehensive guide on how to implement lazy loading in Laravel. We will walk you through every step of the process, from the basics of what lazy loading is, to detailed instructions on how to integrate it into your Laravel projects. Discover how this powerful tool can enhance your site's performance, reduce server load, and provide a smoother, more enjoyable user experience. Don't let heavy image files bog down your website — get started with lazy loading today!

`To use lazy loading for images in Laravel, you can follow the steps outlined below:`

Step 1: Install Intervention Image Package
First, you need to install the Intervention Image package, which provides an easy and efficient way to manipulate images in Laravel. Run the following command in your terminal:
```
composer require intervention/image
```

Step 2: Configure Intervention Image Package
After installing the package, you need to configure it. Open the `config/app.php` file and add the following lines under the 'aliases' section:
```php
'InterventionImage' => Intervention\Image\Facades\Image::class,
```

Step 3: Create a Model and Migration (if necessary)
If you haven't already done so, create a model and migration to store the image data. You can use the `make:model` and `make:migration` commands to generate them.

Step 4: Add Image Column to Migration
In the migration file, add a column to store the image path. For example, you can use the `string` data type:
```php
$table->string('image');
```

Step 5: Run the Migration
Run the migration to create the table in the database:
```
php artisan migrate
```

Step 6: Add Form Input for Image Upload
Create a form view with an input field for uploading the image. Ensure that you set the `enctype` attribute of the form to `"multipart/form-data"`. Here's an example:
```html
<form action="/upload" method="POST" enctype="multipart/form-data">
    @csrf
    <input type="file" name="image">
    <button type="submit">Upload</button>
</form>
```

Step 7: Handle the Image Upload
In your controller, handle the image upload by saving the uploaded image file and storing its path in the database. You can use the `store()` method provided by Laravel for this purpose. Here's an example:
```php
use Intervention\Image\Facades\Image;

public function upload(Request $request)
{
    $imagePath = $request->file('image')->store('images');
    
    // save the image path to the database using your model
    YourModel::create([
        'image' => $imagePath,
    ]);
    
    return redirect()->back();
}
```

Step 8: Display the Image with Lazy Loading
In your view, you can display the image with lazy loading using the `lazy` attribute and the `src` attribute set to a placeholder image. Then, use JavaScript to load the actual image when it becomes visible in the viewport. Here's an example using the `laravel/laravel-mix` package to compile assets:
```html
<img src="{{ asset('path-to-placeholder-image') }}" data-src="{{ asset($model->image) }}" alt="" loading="lazy">

<script src="{{ mix('js/app.js') }}"></script>
<script>
    document.addEventListener("DOMContentLoaded", function() {
        const lazyImages = document.querySelectorAll('img[loading="lazy"]');
        const imageObserver = new IntersectionObserver(function(entries, observer) {
            entries.forEach(function(entry) {
                if (entry.isIntersecting) {
                    const lazyImage = entry.target;
                    lazyImage.src = lazyImage.dataset.src;
                    lazyImage.onload = function() {
                        lazyImage.removeAttribute("data-src");
                        lazyImage.removeAttribute("loading");
                    };
                    observer.unobserve(lazyImage);
                }
            });
        });

        lazyImages.forEach(function(lazyImage) {
            imageObserver.observe(lazyImage);
        });
    });
</script>
```

Make sure to replace `'path-to-placeholder-image'` with the path to your desired placeholder image.

That's it! You now have implemented lazy loading for images in Laravel. When the image is close to being visible in the viewport, the actual image will be loaded asynchronously for a better performance.
