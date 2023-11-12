INTERNET_TV.mdのステップ1~3をドキュメントとしてまとめます。

## ステップ1：テーブル設計

テーブル設計について、以下に示します。

テーブル：channels(チャンネル)

|カラム名|データ型|NULL|キー|初期値|AUTO INCREMENT|
| ---- | ---- | ---- | ---- | ---- | ---- |
|channel_id|bigint(20)||PRIMARY||YES|
|channel_name|varchar(100)|||||

テーブル：programs（番組）

|カラム名|データ型|NULL|キー|初期値|AUTO INCREMENT|
| ---- | ---- | ---- | ---- | ---- | ---- |
|program_id|bigint(20)||PRIMARY||YES|
|program_title|varchar(255)|||||
|program_description|text|YES||||

テーブル：genres(ジャンル)

|カラム名|データ型|NULL|キー|初期値|AUTO INCREMENT|
| ---- | ---- | ---- | ---- | ---- | ---- |
|genre_id|bigint(20)||PRIMARY||YES|
|genre_name|varchar(255)|||||

テーブル：program_genres（番組とジャンルの関連付け）

|カラム名|データ型|NULL|キー|初期値|AUTO INCREMENT|
| ---- | ---- | ---- | ---- | ---- | ---- |
|program_genre_id|bigint(20)||PRIMARY||YES|
|program_id|bigint(20)||INDEX|||
|genre_id|bigint(20)||INDEX|||

- 外部キー制約：program_id に対して、programs テーブルの program_id カラムから設定
- 外部キー制約：genre_id に対して、genres テーブルの genre_id カラムから設定

テーブル：time_slots（番組枠）

|カラム名|データ型|NULL|キー|初期値|AUTO INCREMENT|
| ---- | ---- | ---- | ---- | ---- | ---- |
|time_slot_id|bigint(20)||PRIMARY||YES|
|channel_id|bigint(20)||INDEX|||
|program_id|bigint(20)||INDEX|||
|start_time|time|||||
|end_time|time|||||

- 外部キー制約：channel_id に対して、channels テーブルの channel_id カラムから設定
- 外部キー制約：program_id に対して、programs テーブルの program_id カラムから設定

テーブル：seasons（シーズン）

|カラム名|データ型|NULL|キー|初期値|AUTO INCREMENT|
| ---- | ---- | ---- | ---- | ---- | ---- |
|season_id|bigint(20)||PRIMARY||YES|
|program_id|bigint(20)||INDEX|||
|season_number|int|||||

- 外部キー制約：program_id に対して、programs テーブルの program_id カラムから設定

テーブル：episodes（エピソード）

|カラム名|データ型|NULL|キー|初期値|AUTO INCREMENT|
| ---- | ---- | ---- | ---- | ---- | ---- |
|episode_id|bigint(20)||PRIMARY||YES|
|season_id|bigint(20)||INDEX|||
|episode_number|int|||||
|episode_title|varchar(255)|||||
|episode_description|text|YES||||
|video_duration|time|||||
|release_date|date|||||

- 外部キー制約：season_id に対して、seasons テーブルの season_id カラムから設定

テーブル：view_history（視聴履歴）

|カラム名|データ型|NULL|キー|初期値|AUTO INCREMENT|
| ---- | ---- | ---- | ---- | ---- | ---- |
|view_id|bigint(20)||PRIMARY||YES|
|episode_id|bigint(20)||INDEX|||
|time_slot_id|bigint(20)||INDEX|||
|views|bigint(20)|||0||

- 外部キー制約：episode_id に対して、episodes テーブルの episode_id カラムから設定
- 外部キー制約：time_slot_id に対して、time_slots テーブルの time_slot_id カラムから設定


## ステップ2：データベース構築

1. データベースの構築

以下のSQL文を使用して、データベースを作成します。

```sql
CREATE DATABASE IF NOT EXISTS internet_tv;
```

次に、作成したデータベースを使用するように選択します。

```sql
USE internet_tv;
```

2. テーブルの構築

以下のSQL文を使用して、ステップ1で設計した各テーブルを作成します。

Channels テーブル
```sql
CREATE TABLE IF NOT EXISTS channels (
    channel_id bigint(20) PRIMARY KEY AUTO_INCREMENT,
    channel_name varchar(100) NOT NULL
);
```

Programs テーブル
```sql
CREATE TABLE IF NOT EXISTS programs (
    program_id bigint(20) PRIMARY KEY AUTO_INCREMENT,
    program_title varchar(255) NOT NULL,
    program_description text,
    genres varchar(255)
);
```

Genres テーブル
```sql
CREATE TABLE IF NOT EXISTS genres (
    genre_id bigint(20) PRIMARY KEY AUTO_INCREMENT,
    genre_name varchar(255) NOT NULL
);
```

Program_Genres テーブル
```sql
CREATE TABLE IF NOT EXISTS program_genres (
    program_genre_id bigint(20) PRIMARY KEY AUTO_INCREMENT,
    program_id bigint(20),
    genre_id bigint(20),
    FOREIGN KEY (program_id) REFERENCES programs(program_id),
    FOREIGN KEY (genre_id) REFERENCES genres(genre_id)
);
```

Time_Slots テーブル
```sql
CREATE TABLE IF NOT EXISTS time_slots (
    time_slot_id bigint(20) PRIMARY KEY AUTO_INCREMENT,
    channel_id bigint(20),
    program_id bigint(20),
    start_time datetime,
    end_time datetime,
    FOREIGN KEY (channel_id) REFERENCES channels(channel_id),
    FOREIGN KEY (program_id) REFERENCES programs(program_id)
);
```

Seasons テーブル
```sql
CREATE TABLE IF NOT EXISTS seasons (
    season_id bigint(20) PRIMARY KEY AUTO_INCREMENT,
    program_id bigint(20),
    season_number int,
    FOREIGN KEY (program_id) REFERENCES programs(program_id)
);
```

Episodes テーブル
```sql
CREATE TABLE IF NOT EXISTS episodes (
    episode_id bigint(20) PRIMARY KEY AUTO_INCREMENT,
    season_id bigint(20),
    episode_number int,
    episode_title varchar(255) NOT NULL,
    episode_description text,
    video_duration time,
    release_date date,
    FOREIGN KEY (season_id) REFERENCES seasons(season_id)
);
```

View_History テーブル
```sql
CREATE TABLE IF NOT EXISTS view_history (
    view_id bigint(20) PRIMARY KEY AUTO_INCREMENT,
    episode_id bigint(20),
    time_slot_id bigint(20),
    views bigint(20) DEFAULT 0,
    FOREIGN KEY (episode_id) REFERENCES episodes(episode_id),
    FOREIGN KEY (time_slot_id) REFERENCES time_slots(time_slot_id)
);
```

3. サンプルデータの挿入

以下のSQL文を使用して、各テーブルにサンプルデータを挿入します。

Channels テーブル
```sql
INSERT INTO channels (channel_name) VALUES
    ('ニュースチャンネル'),
    ('映画チャンネル'),
    ('バラエティチャンネル'),
    ('音楽チャンネル'),
    ('アニメチャンネル'),
    ('クッキングチャンネル'),
    ('ドラマチャンネル'),
    ('ドキュメンタリーチャンネル');
```

Programs テーブル
```sql
INSERT INTO programs (program_title, program_description) VALUES
    ('ニュースの時間', '国内外のニュースをお届け'),
    ('映画祭り', '人気映画の特集'),
    ('笑いの時間', 'バラエティ番組'),
    ('ミュージックライブ', '最新の音楽ライブ'),
    ('アニメタイム', '人気アニメの放送'),
    ('クッキングマスター', '料理のプロが教える'),
    ('ドラマタイム', '感動ドラマの放送'),
    ('ドキュメンタリーストーリー', '人々の実話に基づくドキュメンタリー');
```

Genres テーブル
```sql
INSERT INTO genres (genre_name) VALUES
    ('ニュース'),
    ('映画'),
    ('バラエティ'),
    ('音楽'),
    ('アニメ'),
    ('クッキング'),
    ('ドラマ'),
    ('ドキュメンタリー');
```

Program_Genres テーブル
```sql
INSERT INTO program_genres (program_id, genre_id) VALUES
    (1, 1), (2, 2), (3, 3), (4, 4),
    (5, 5), (6, 6), (7, 7), (8, 8);
```

Time_Slots テーブル
```sql
INSERT INTO time_slots (channel_id, program_id, start_time, end_time) VALUES
    -- ニュースチャンネル
    (1, 1, '2023-11-12 08:00:00', '2023-11-12 09:00:00'),
    (1, 1, '2023-11-12 12:00:00', '2023-11-12 13:00:00'),
    (1, 1, '2023-11-12 18:00:00', '2023-11-12 19:00:00'),
    (1, 1, '2023-11-12 21:00:00', '2023-11-12 22:00:00'),

    -- 映画チャンネル
    (2, 2, '2023-11-12 14:00:00', '2023-11-12 16:00:00'),
    (2, 2, '2023-11-12 19:00:00', '2023-11-12 21:00:00'),

    -- バラエティチャンネル
    (3, 3, '2023-11-12 20:30:00', '2023-11-12 21:30:00'),

    -- 音楽チャンネル
    (4, 4, '2023-11-12 18:00:00', '2023-11-12 19:30:00'),

    -- アニメチャンネル
    (5, 5, '2023-11-12 15:00:00', '2023-11-12 16:00:00'),
    (5, 5, '2023-11-12 22:00:00', '2023-11-12 23:00:00'),

    -- クッキングチャンネル
    (6, 6, '2023-11-12 12:30:00', '2023-11-12 13:30:00'),
    (6, 6, '2023-11-12 19:30:00', '2023-11-12 20:30:00'),

    -- ドラマチャンネル
    (7, 7, '2023-11-12 16:00:00', '2023-11-12 17:00:00'),
    (7, 7, '2023-11-12 22:00:00', '2023-11-12 23:00:00'),

    -- ドキュメンタリーチャンネル
    (8, 8, '2023-11-12 14:00:00', '2023-11-12 15:30:00'),
    (8, 8, '2023-11-12 20:00:00', '2023-11-12 21:30:00');
```

Seasons テーブル
```sql
INSERT INTO seasons (program_id, season_number) VALUES
    (5, 1), -- アニメタイムのシーズン1
    (5, 2), -- アニメタイムのシーズン2

    (7, 1), -- ドラマタイムのシーズン1
    (7, 2); -- ドラマタイムのシーズン2
```

Episodes テーブル
```sql
INSERT INTO episodes (season_id, episode_number, episode_title, episode_description, video_duration, release_date) VALUES
    -- アニメタイムのエピソード
    (3, 1, '冒険の始まり', '個性豊かなキャラクターたち', '00:25:00', '2023-11-11'),
    (3, 2, '仲間たちの絆', '熱い友情ストーリー', '00:25:00', '2023-11-18'),

    -- ドラマタイムのエピソード
    (1, 1, '感動の始まり', '主人公の成長が描かれる', '00:45:00', '2023-11-12'),
    (1, 2, '新たなる試練', 'ドラマチックな展開', '00:45:00', '2023-11-19');
```

View_History テーブル
```sql
INSERT INTO view_history (episode_id, time_slot_id, views) VALUES
    (1, 1, 1200), -- アニメタイムのエピソード1の視聴履歴
    (2, 2, 1000), -- アニメタイムのエピソード2の視聴履歴

    (3, 5, 800),  -- ドラマタイムのエピソード1の視聴履歴
    (4, 6, 500);  -- ドラマタイムのエピソード2の視聴履歴
```

## ステップ3

以下に、各要件に対するSQLクエリを示します。

1. エピソード視聴数トップ3のエピソードタイトルと視聴数を取得するクエリ:
```sql
SELECT episode_title, views
FROM view_history
JOIN episodes ON view_history.episode_id = episodes.episode_id
ORDER BY views DESC
LIMIT 3;
```

2. エピソード視聴数トップ3の番組タイトル、シーズン数、エピソード数、エピソードタイトル、視聴数を取得するクエリ:
```sql
SELECT program_title, season_number, episode_number, episode_title, views
FROM view_history
JOIN episodes ON view_history.episode_id = episodes.episode_id
JOIN seasons ON episodes.season_id = seasons.season_id
JOIN programs ON seasons.program_id = programs.program_id
ORDER BY views DESC
LIMIT 3;
```

3. 本日放送される番組の情報を取得するクエリ:
```sql
SELECT channel_name, start_time, end_time, season_number, episode_number, episode_title, episode_description
FROM time_slots
JOIN channels ON time_slots.channel_id = channels.channel_id
JOIN programs ON time_slots.program_id = programs.program_id
JOIN seasons ON programs.program_id = seasons.program_id
JOIN episodes ON seasons.season_id = episodes.season_id
WHERE DATE(start_time) = CURDATE();
```

4. ドラマのチャンネルにおける本日から一週間分の番組情報を取得するクエリ:
```sql
SELECT start_time, end_time, season_number, episode_number, episode_title, episode_description
FROM time_slots
JOIN channels ON time_slots.channel_id = channels.channel_id
JOIN programs ON time_slots.program_id = programs.program_id
JOIN seasons ON programs.program_id = seasons.program_id
JOIN episodes ON seasons.season_id = episodes.season_id
WHERE
    DATE(time_slots.start_time) BETWEEN CURDATE() AND DATE_ADD(CURDATE(), INTERVAL 6 DAY)
    AND channels.channel_name = 'ドラマチャンネル';
```