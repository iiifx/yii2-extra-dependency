# Extra dependencies for Yii2

## Дополнительные зависимости для кэширования

1. DbTimestampDependency - расширяет базовый DbDependency

Создает запрос получения MAX данных с полей TIMESTAMP, на основе которых определяет акуальность закэшированных данных

Использование:
```php
use iiifx\caching\extra\DbTimestampDependency;

# Кэширование данных
$cachedData = Yii::$app->db->cache( function () {

    # Извлекаем свежие данные с БД
    return Item::find()
        ->asArray()
        ->all();
        
}, 0, new DbTimestampDependency( [ # Для слежения за актуальностью используем DbTimestampDependency

    'table'   => [
        # Указываем список таблиц
        Item::tableName()
    ],
    
    # Указываем, что нужно повторно использовать
    'reusable' => TRUE
    
] ) ) );
```

Можно использовать много таблиц в зависимости:
```php
new DbTimestampDependency( [
    'table'   => [
        User::tableName(),
        UserProfile::tableName(),
        UserImage::tableName(),
    ]
] );
```

И переопределить имена полей, которые установлены по умолчанию как 'date_created' и 'date_edited':
```php
new DbTimestampDependency( [
    'table'   => User::tableName(),
    'timestamp' => [ 'created', 'edited', 'deleted' ]
] );
```

Объект формирует такой запрос:
```sql
SELECT
    MAX( tableName.date_created ),
    MAX( tableName.date_edited )
FROM tableName
```
