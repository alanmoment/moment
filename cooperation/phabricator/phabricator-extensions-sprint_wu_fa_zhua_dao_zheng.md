# phabricator-extensions-Sprint 無法抓到正確的 Story Points

在版號為 `741e2ef4b1150f9a9e4b121218b3ea536289968d` 之後的版本，因為 story points 變更欄位，所以 burndown chart 會沒辦法抓到正確的 point。

可以在[官方](https://secure.phabricator.com/T10350)這邊找到答案，但是並不是環境都一樣，所以必須要調整一下。

## 複製 copy_points.php

將[檔案](https://secure.phabricator.com/P1940)複製到 phabricator 目錄底下，如果你的資料庫名稱不是 `phabricator_maniphest`，那就要修改，第 `106` 行，如我就修改為 `default_maniphest`

```php
#!/usr/bin/env php
<?php // See <https://secure.phabricator.com/T10350> for discussion.

require_once 'scripts/__init_script__.php';

$args = new PhutilArgumentParser($argv);
$args->parseStandardArguments();
$args->parse(
  array(
    array(
      'name'  => 'field',
      'param' => 'key',
      'help'  => pht('Field to migrate.'),
    )
  ));

$task = new ManiphestTask();
$fields = PhabricatorCustomField::getObjectFields(
  $task,
  PhabricatorCustomField::ROLE_EDIT);

$field_map = $fields->getFields();
$field_list = implode(', ', array_keys($field_map));

if (!$field_map) {
  throw new PhutilArgumentUsageException(
    pht(
      'You do not have any custom fields defined in Maniphest, so there is '.
      'nowhere that points can be copied from.'));
}

$field_key = $args->getArg('field');
if (!strlen($field_key)) {
  throw new PhutilArgumentUsageException(
    pht(
      'Use --field to specify which field to copy points from. Available '.
      'fields are: %s.',
      $field_list));
}

$field = idx($field_map, $field_key);
if (!$field) {
  throw new PhutilArgumentUsageException(
    pht(
      'Field "%s" is not a valid field. Available fields are: %s.',
      $field_key,
      $field_list));
}

$proxy = $field->getProxy();
if (!$proxy) {
  throw new PhutilArgumentUsageException(
    pht(
      'Field "%s" is not a standard custom field, and can not be migrated.',
      $field_key,
      $field_list));
}

if (!($proxy instanceof PhabricatorStandardCustomFieldInt)) {
  throw new PhutilArgumentUsageException(
    pht(
      'Field "%s" is not an "int" field, and can not be migrated.',
      $field_key,
      $field_list));
}

$storage = $field->newStorageObject();
$conn_r = $storage->establishConnection('r');

$value_rows = queryfx_all(
  $conn_r,
  'SELECT objectPHID, fieldValue FROM %T WHERE fieldIndex = %s
    AND fieldValue IS NOT NULL',
  $storage->getTableName(),
  $field->getFieldIndex());
$value_map = ipull($value_rows, 'fieldValue', 'objectPHID');

$id_rows = queryfx_all(
  $conn_r,
  'SELECT phid, id, points FROM %T',
  $task->getTableName());
$id_map = ipull($id_rows, null, 'phid');

foreach ($value_map as $phid => $value) {
  $dict = idx($id_map, $phid, array());
  $id = idx($dict, 'id');
  $current_points = idx($dict, 'points');

  if (!$id) {
    continue;
  }

  if ($current_points !== null) {
    continue;
  }

  if ($value === null) {
    continue;
  }

  $sql = qsprintf(
    $conn_r,
    'UPDATE %T.%T SET points = %f WHERE id = %d;',
    'phabricator_maniphest',
    $task->getTableName(),
    $value,
    $id);

  echo $sql."\n";
}
```

## 更改權限

```bash
chmod +x copy_points.php
```

## 執行

必須要去套件原始檔去看原始的 [story point](https://github.com/wikimedia/phabricator-extensions-Sprint/blob/master/src/customfield/SprintTaskStoryPointsField.php) 欄位是什麼，我查到的結果是 `isdc:sprint:storypoints`，所以修改如下。

另外要注意自己之前輸入 story points 時是否有用到非數字的字元，有的話會出錯，得先去修改。可以在 `default_maniphest.maniphest_customfieldstorage` 這張資料表去查看。

```bash
./copy_points.php --field isdc:sprint:storypoints > points.sql
cat points.sql # Look at the results and sanity check them.
cat points.sql | mysql -u root
```
