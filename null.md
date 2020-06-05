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
Relasi di Laravel memungkinkan kita untuk mendefinisikan default nilai jika hasil yang diberikan adalah `null`.

❌
```php
public function user() {
    return $this->belongsTo('App\User');
}

// Dijalankan ketika birthDate bernilai null 
$post = Post::findOrFail($id);
echo $post->user->birthDate->format('d-m-Y'); 

// Akan terjadi error karena null tidak bisa diformat tanggal
```

✅
```php
public function user() {
    return $this->belongsTo('App\User')->withDefault(function ($user, $post) {
         $user->birthDate = '1995-03-09';
    });
}

// Dijalankan ketika birthDate bernilai null 
$post = Post::findOrFail($id);
echo $post->user->birthDate->format('d-m-Y');

// 09-03-1995
```

## Null Coalesce (??)
Null Coalesce menggunakan operator `??` digunakan untuk mereturn value sebelah kiri jika bernilai `null` atau variabel belum dideklarasikan. 


❌
```php
$harga = $hargaTotal - 20;
echo $harga;

// Undefined variable: hargaTotal on line number 1
// Akan terjadi error karena variabel $hargaTotal belum didefinisikan.
```

✅
```php
$harga = ($hargaTotal ?? 50) - 20;
echo $harga;
 
// 30
```

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
