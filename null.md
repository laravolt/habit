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
Null Coalesce menggunakan operator `??` digunakan untuk mereturn value sebelah kanan jika bernilai `null` atau variabel belum didefinisikan. 

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
`optional()` merupakan salah satu helper di Laravel yang berguna untuk mereturn object menjadi `null` jika properti dan method bernilai `null` sehingga tidak menimbulkan error.

❌
```php
$user = auth()->user;
echo $user->username;

// Jika user belum login, maka akan menimbulkan error pada aplikasi.
```

✅
```php
$user = auth()->user;
echo optional($user)->username;

// null
// Jika user belum login, maka username akan mengembalikan `null`
```

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
Jika kita ingin menambahkan fitur-fitur `array` ke `object` di php, kita dapat menggunakan interface `ArrayAccess`.

❌
```php
$post = new Post;
$post['date'] = '2020-06-05';
echo $post['date'];

// Fatal error: Cannot use object of type stdClass as array …
```

✅
```php
<?php

class Post implements ArrayAccess
{
    protected $_data=array();
    public function offsetExists ($offset)
    {
        return array_key_exists($offset, $this->_data);
    }

    public function offsetGet ($offset)
    {
        return $this->_data[$offset];
    }

    public function offsetSet ($offset, $value)
    {
        $this->_data[$offset] = $value;
    }

    public function offsetUnset ($offset)
    {
        unset($this->_data[$offset]);
    }

}

$post = new Post;
$post['date'] = '2020-06-05';

echo "Tanggal artikel dibuat adalah : " . $post['date'];

// Tanggal artikel dibuat adalah : 2020-06-05
// Object post dapat menggunakan sintaks array
```