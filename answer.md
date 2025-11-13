---
sort: 3
---

# 課題の解答

以下は、課題の解答例です。
課題に合格されなかった方は、こちらのコードを参考にしてください。

**routes/web.php**

```php
<?php

use Illuminate\Support\Facades\Route;
use App\Http\Controllers\ItemController; 

Route::get('/', function () {
    return view('index');
})->name('index');

// 以前までのはコメントアウト
//Route::post('item', [ItemController::class, 'index'])->name('item.index');

// --- 以下を追加 ---
Route::match(['get', 'post'], 'item/{genre?}', [ItemController::class, 'index'])->name('item.index');
// --- 以下を追加 ---
Route::get('item/show/{item}', [ItemController::class, 'show'])->name('item.show');
```

**resources/views/item/index.blade.php**

```php
<!DOCTYPE html>
<html lang="ja">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<link rel="stylesheet" href="{{ asset('css/minishop.css')}}">
<title>ショッピングサイト</title>
</head>
<body>
<h3>ジャンル別商品一覧</h3>
    <table>
        <tr>
            <th>&nbsp;</th>
            <th>商品名</th>
            <th>メーカー・著者<br>アーティスト</th>
            <th>価格</th>
            <th>詳細</th>
        </tr>
    @foreach( $items  as  $item )
        <tr>
        <td class="td_mini_img"><img class="mini_img" src="{{ asset('images/'.$item->image )}}"></td>
        <td class="td_item_name"> {{  $item->name }} </td>
        <td class="td_item_maker"> {{  $item->maker }}  </td>
        <td class="td_right">&yen; {{  number_format( $item->price) }} </td>
        <!-- 以下を追加 -->
        <td><a href="{{ route('item.show',  ['item' => $item->ident]) }}">詳細</a></td>
        <!-- ここまで -->
        </tr>
    @endforeach
    </table>
    <br>
    <a href="{{ route('index') }}">ジャンル選択に戻る</a>
</body>
</html>
```

**app/Http/Controllers/ItemController.php**

```php
<?php

namespace App\Http\Controllers;

use Illuminate\Http\Request;
use App\Models\Item;

class ItemController extends Controller
{
    public function index(Request $request)
    {

        $items = Item::where('genre', $request->genre)->get();
        return view('item.index', ['items' => $items]);
    }

    // --- ここから追加 ---
    public function show(Item $item)
    {
        return view('item.show', ['item' => $item]);
    }
    // --- ここまで追加 ---
}
```

**resources/views/item/show.blade.php**

```php
<!DOCTYPE html>
<html lang="ja">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<link rel="stylesheet" href="{{ asset('css/minishop.css')}}">
<title>ショッピングサイト</title>
</head>
<body>
<h3>商品詳細</h3>
<!-- action属性は空にしています -->
<form method="POST" action="">
    @csrf
    <input type="hidden" name="ident" value="{{ $item->ident }}">
    <table>
        <tr><th>商品名</th>
        <td>{{ $item->name }}</td></tr>
        <tr><td colspan="2"><div class="td_center">
        <img class="detail_img" src="{{ asset('images/'.$item->image )}}"></div></td></tr>
        <tr><th>メーカー・著者<br>アーティスト</th>
        <td>{{ $item->maker }}</td></tr>
        <tr><th>価 格</th>
        <td>&yen;{{  number_format( $item->price) }}</td></tr>
        <tr><th>注文数</th>
        <td><select name="quantity">
            @for ( $i=1;  $i<=10;  $i++ )
                <option value="{{ $i }}"> {{ $i }} </option>
            @endfor
        </select></td></tr>
        <tr><th colspan="2"><input type="submit" value="カートに入れる"></th></tr>
    </table>
</form>
<br>
<a href="{{ route('item.index',['genre' => $item->genre])}}">ジャンル別商品一覧に戻る</a>
</body>
</html>
```

