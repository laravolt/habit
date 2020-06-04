# How To Handle Null Value

## Early Exit
Berasumsi ketika melakukan query (Eloquent ataupun Query Builder) selalu ada hasilnya. 
Tidak mempertimbangkan kasus alternatif ketika query menghasilkan null.

❌
```php
$user = User::find($id);
$user->posts->count();
```

✅
```php
$user = User::findOrFail($id);
$user->posts->count(); // kode ini tidak akan dieksekusi jika $user null
```

## withDefault()
TODO

## Null Coalesce
TODO

## optional()
TODO

## Query Filter
Ketika melakukan query, pastikan hanya mengambil data yang valid.

❌
```php
$posts = Post::get();
foreach($posts as $post) {
  $post->comments->count();
}
```

✅
```php
$posts = Post::has('comments')->get();
foreach($posts as $post) {
  $post->comments->count();
}
```

## Array Access
TODO
