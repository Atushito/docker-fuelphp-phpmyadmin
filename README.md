# PHP+FuelPHP+phpMyadmin+Dockerテンプレート

## 1.コンテナ作成と起動

```bash
docker-compose up --build -d
```

### MySQL + phpMyAdmin の確認

http://localhost:4040/

## 2.Dockerコンテナのfuel_appに入る

```bash
docker exec -it fuel_app bash
cd /var/www/
```

## 3.FuelPHPをインストール

```bash
curl https://get.fuelphp.com/oil | sh
oil create fuel_server
```

### アクセスしてfuelphpの画面を確認

http://localhost:8080/

### ORMパッケージ有効化

/fuel_server/fuel/app/config/config.php

下記のようにコメントアウトを外す

```php
'always_load'  => array(

    /**
     * These packages are loaded on Fuel's startup.
     * You can specify them in the following manner:
     *
     * array('auth'); // This will assume the packages are in PKGPATH
     *
     * // Use this format to specify the path to the package explicitly
     * array(
     *     array('auth'	=> PKGPATH.'auth/')
     * );
     */
    'packages'  => array(
        'orm',
    ),

    /**
     * These modules are always loaded on Fuel's startup. You can specify them
     * in the following manner:
     *
     * array('module_name');
     *
     * A path must be set in module_paths for this to work.
     */
    // 'modules'  => array(),

    /**
     * Classes to autoload & initialize even when not used
     */
    // 'classes'  => array(),

    /**
     * Configs to autoload
     *
     * Examples: if you want to load 'session' config into a group 'session' you only have to
     * add 'session'. If you want to add it to another group (example: 'auth') you have to
     * add it like 'session' => 'auth'.
     * If you don't want the config in a group use null as groupname.
     */
    // 'config'  => array(),

    /**
     * Language files to autoload
     *
     * Examples: if you want to load 'validation' lang into a group 'validation' you only have to
     * add 'validation'. If you want to add it to another group (example: 'forms') you have to
     * add it like 'validation' => 'forms'.
     * If you don't want the lang in a group use null as groupname.
     */
    // 'language'  => array(),
),
```

## データベースの準備

\fuel_server\fuel\app\config\development\db.php

dsnの値を変更

```php
return array(
    'default' => array(
        'connection' => array(
            'dsn' => 'mysql:host=mysql;dbname=fuel_dev;unix_socket=/tmp/mysql.sock',
            'username' => 'root',
            'password' => 'root',
        ),
    ),
);
```

### MySQLにデータベースを作成

http://localhost:4040/ のSQLで以下のコマンドを実行。

```sql
CREATE DATABASE `fuel_dev` DEFAULT CHARACTER SET utf8 COLLATE utf8_general_ci;
```

1. モデル作成
必要なモデルの作成とマイグレーションの実行。

```bash
cd fuel_server
```

docker内のディレクトリ下記で実行
/var/www/fuel_server#

```bash
oil generate model hoge name:varchar[255] sex:int del_flg:bool
oil refine migrate
```

#### hogesテーブルができていることを確認

http://localhost:4040/

2. コントローラー作成

docker内のディレクトリ下記で実行
/var/www/fuel_server#

```bash
php oil generate controller example index add
```

3. TOPページの設定

TOPページになるコントローラーを指定

/fuel_server/fuel/app/config/routes.php

```php
<?php
return array(
    '_root_' => 'example/index',
    ...
);
```

4. 反映確認

http://localhost:8080/

