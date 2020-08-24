[//]: # (Laravel 批量更新数据库)

### 背景

Laravel 中一般都是用 ORM 操作数据库的，但是遇到批量操作很多数据的时候如果还是使用 ORM 对象进行 `save()` 则会造成数据库压力过大，增加没必要的消耗。


### 具体实现

改方法是 Laravel 5.8 版本的时候写的，其他版本或者其他框架则需要灵活调整。  
基本思路是把二维数组进行拆分，组装成原生 SQL 一次性修改数据库的数据。

```php
/**
 * 批量更新数据库
 * @param $tableName
 * @param array $multipleData
 * @param string $where
 * @return bool|int
 */
private function updateBatch($tableName , $multipleData = [], $where = '')
{
    $updateSql = '';
    try {
        if (empty($multipleData)) {
            throw new \Exception("数据不能为空");
        }
        $firstRow = current($multipleData);

        $updateColumn = array_keys($firstRow);
        // 默认以id为条件更新，如果没有ID则以第一个字段为条件
        $referenceColumn = isset($firstRow['id']) ? 'id' : current($updateColumn);
        unset($updateColumn[0]);
        // 拼接sql语句
        $updateSql = "UPDATE " . $tableName . " SET ";
        $sets = [];
        $bindings = [];
        foreach ($updateColumn as $uColumn) {
            $setSql = "`" . $uColumn . "` = CASE ";
            foreach ($multipleData as $data) {
                $setSql .= "WHEN `" . $referenceColumn . "` = ? THEN ? ";
                $bindings[] = $data[$referenceColumn];
                $bindings[] = $data[$uColumn];
            }
            $setSql .= "ELSE `" . $uColumn . "` END ";
            $sets[] = $setSql;
        }
        $updateSql .= implode(', ', $sets);
        $updateSql = rtrim($updateSql, ", ") . $where;
        // 传入预处理sql语句和对应绑定数据
        return DB::update($updateSql, $bindings);
    } catch (\Exception $e) {
        Log::error('批量保存失败,sql=' . $updateSql.',结果='. $e->getMessage());
        return false;
    }

}
```

### 调用方法

只需要依次传递表名、二维数组、where 条件即可

```php
...
//批量更新数据
$data = [];
foreach ($list as $value) {
    $data[] = [
        'user_id' => $value['user_id'],
        'name' => $value['name'],
        'author' => 'HongXunPan'
    ];
}

$chunk_datas = array_chunk($data, 200, true);

foreach ($chunk_datas as $chunk_data) {

    $where = ' WHERE user_id in (' .implode(',', array_column($chunk_data, 'user_id')).')';
    $this->updateBatch($ORM->getTable(), $chunk_data, $where);
}
```

